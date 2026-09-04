# Kiến trúc Hệ thống & Design Patterns Cấp Enterprise: Từ Bài học Cogover đến Chuẩn hóa Dự án

> **Mục đích**: Phân tích, đánh giá cây thư mục & kiến trúc code của dự án `app-tai-chinh` so với các hệ thống Enterprise sản xuất lớn (Cogover Multi-Service Platform), tổng hợp tri thức cấp Senior/Architect để nâng cấp dự án hiện tại và làm bộ khung chuẩn (Master Blueprint) cho mọi dự án tương lai.
> **Ngày cập nhật**: 2026-08-05
> **Nguồn nghiên cứu**: Enterprise Codebase `E:\Stringee\Cogover` (`omni-server`, `object-server`, `manufacture-server`)

---

## 1. SO SÁNH TỔNG QUAN: ENTERPRISE COGOVER VS. CURRENT APP TÀI CHÍNH

### 1.1 Bảng So sánh Kiến trúc

| Tiêu chí | Enterprise Cogover Systems | App Tài Chính (Hiện tại) | Đánh giá & Hướng nâng cấp |
| :--- | :--- | :--- | :--- |
| **Phân tầng (Layering)** | Strict Layered / Hexagonal Architecture (Controller → Service → Repository → DB) | Route-centric (Logic business + DB Prisma nằm trực tiếp trong Route Handlers) | ⚠️ **Cần nâng cấp**: Tách Route ra khỏi Business Logic & DB Queries. |
| **Xử lý Luồng Phức tạp** | **Pipeline Pattern (`PipelineStage<I, O>`)** (Mỗi bước là 1 class độc lập, dễ test & reuse) | Inline procedural code trong route handler (Viết 1 hàm dài hàng trăm dòng) | ⚠️ **Cần áp dụng**: Dùng Pipeline cho Auto SMS Capture, Import/Export, FIFO Debt Sync. |
| **Tác vụ Bất đồng bộ** | Pub/Sub Event Bus (Kafka) + Async Distributed Workers | Synchronous inline execution | 💡 **Có thể tối ưu**: Dùng Event Bus / Queue cho notification, logging, analytics. |
| **Quản lý Dữ liệu Cache** | Repository bọc `CacheUtil.getFromCacheOrDb()` + Pub/Sub invalidation | SWR trên FE (RAM + LocalStorage) + Invalidation qua Custom Event Bus | ✅ FE tốt, ⚠️ BE thiếu cache layer minh bạch cấp Repository. |
| **Cấu trúc Component FE** | Atomic Design / Feature-based module | Shared UI (`AppModal`, `FormField`) + Pages (Simple/Advanced) | ✅ FE đã tuân thủ SOLID (Phase 0 Review), 💡 Cần chuyển sang Feature-driven folder structure. |

---

## 2. 5 DESIGN PATTERNS CỐT LÕI HỌC ĐƯỢC TỪ ENTERPRISE BASE

### 2.1 Pattern 1: Pipeline Pattern (`PipelineStage<I, O>`)

Trong enterprise backend của Cogover, các nghiệp vụ phức tạp (như routing cuộc gọi, tạo conversation, xử lý visitor) **KHÔNG BAO GIỜ** viết trong 1 hàm monolith. Chúng được chia thành các **Stage** chạy qua một **Pipeline**.

#### Cấu trúc Generic Pipeline (TypeScript Version):

```typescript
// src/patterns/pipeline/Pipeline.ts
export interface PipelineStage<I, O> {
  process(input: I): Promise<O>
}

export class Pipeline<I, O> {
  private stages: PipelineStage<any, any>[] = []

  constructor(initialStage?: PipelineStage<I, O>) {
    if (initialStage) this.stages.push(initialStage)
  }

  static of<I, O>(stage: PipelineStage<I, O>): Pipeline<I, O> {
    return new Pipeline<I, O>(stage)
  }

  addStage<N>(stage: PipelineStage<O, N>): Pipeline<I, N> {
    this.stages.push(stage)
    return this as unknown as Pipeline<I, N>
  }

  async execute(input: I): Promise<O> {
    let currentOutput: any = input
    for (const stage of this.stages) {
      currentOutput = await stage.process(currentOutput)
    }
    return currentOutput as O
  }
}
```

#### Ví dụ Áp dụng: Xử lý SMS Auto-Capture

```
Raw SMS Text 
  └─► Stage 1: ParseSmsStage (Extract amount, bank, description)
  └─► Stage 2: MatchWalletAndCategoryStage (Map bank code → Wallet, note → Category)
  └─► Stage 3: CreateTransactionStage (Execute DB transaction & update balance)
  └─► Stage 4: InvalidateCacheStage (Broadcast cache clear event)
```

> **Lợi ích**: Mỗi Stage chỉ làm 1 việc (Single Responsibility), có thể Unit Test độc lập 100% không phụ thuộc các bước khác!

---

### 2.2 Pattern 2: Repository Pattern Kết hợp Cache Transparent Layer

Trong Cogover (`AccountRepositoryMySQL`, `WorkspaceRepositoryMySQL`), tầng Repository che giấu hoàn toàn chi tiết truy vấn DB và xử lý Cache tự động:

```typescript
// src/repositories/AccountRepository.ts
export class AccountRepository {
  async getById(id: string): Promise<Account | null> {
    // 1. Kiểm tra Cache trước
    const cached = await cacheService.get(`account:${id}`)
    if (cached) return cached

    // 2. Cache miss -> Fetch DB
    const account = await prisma.account.findUnique({ where: { id } })
    if (account) {
      await cacheService.set(`account:${id}`, account, 3600) // TTL 1 hour
    }
    return account
  }

  async updateStatus(id: string, status: number): Promise<void> {
    await prisma.account.update({ where: { id }, data: { status } })
    // Invalidate local & broadcast invalidation
    await cacheService.delete(`account:${id}`)
    await eventBus.publish('db:change', { table: 'account', id, type: 'UPDATE' })
  }
}
```

> **Lợi ích**: Business code chỉ cần gọi `accountRepo.getById(id)` mà không cần quan tâm data lấy từ Redis, Memory hay DB.

---

### 2.3 Pattern 3: DTO & Context Object Pattern

Tránh việc truyền hàng loạt parameters lẻ tẻ `(userId, personName, amount, billId, memberId, note, date, ...)` vào hàm. Enterprise Cogover gom toàn bộ vào một **Context Object** đi suốt luồng xử lý:

```typescript
export interface DebtPaymentContext {
  userId: string
  debtId: string
  payAmount: number
  debtRecord?: Debt
  splitTransactions?: DebtTransaction[]
  processedMemberIds: Set<string>
  remainingPay: number
  resultStatus?: 'SUCCESS' | 'PARTIAL' | 'FAILED'
}
```

---

### 2.4 Pattern 4: Chain of Responsibility Pattern

Sử dụng cho các luồng kiểm tra điều kiện tuần tự (Permission Checks, Validation Rules, Rate Limiting):

```typescript
export abstract class ValidationHandler<T> {
  private nextHandler?: ValidationHandler<T>

  setNext(handler: ValidationHandler<T>): ValidationHandler<T> {
    this.nextHandler = handler
    return handler
  }

  async handle(request: T): Promise<boolean> {
    const isValid = await this.validate(request)
    if (!isValid) return false
    if (this.nextHandler) return this.nextHandler.handle(request)
    return true
  }

  protected abstract validate(request: T): Promise<boolean>
}
```

---

### 2.5 Pattern 5: Event-Driven Pub/Sub & Decoupled Side-Effects

Khi một hành động chính xảy ra (ví dụ: Tất toán nợ / Tạo hóa đơn nhóm), các tác vụ phụ (gửi notification, ghi log audit, recalculate insights) **KHÔNG ĐƯỢC** làm chậm luồng chính.

```
Main Flow (HTTP Sync)                 Async Event Side-Effects (Background)
┌─────────────────────────┐           ┌──────────────────────────────────┐
│ 1. Create Split Bill    │ ──Event──►│ • Invalidate SWR Cache           │
│ 2. Save DB Transaction  │           │ • Send Zalo Push Notification    │
│ 3. Return HTTP 201      │           │ • Update Net Worth Insights      │
└─────────────────────────┘           └──────────────────────────────────┘
```

---

## 3. ĐÁNH GIÁ CÂY THƯ MỤC HIỆN TẠI VS. CÂY THƯ MỤC CHUẨN ENTERPRISE

### 3.1 Đánh giá Cây Thư mục Backend Hiện tại (`backend/src/`)

#### Hiện tại:
```
backend/src/
├── middleware/         # authMiddleware, rateLimiter
├── routes/             # auth, wallet, category, transaction, debt, split... (Hỗn hợp Logic + DB + Route)
├── services/           # ai.service, smsParser.service (Chưa nhất quán)
├── utils/              # formatters, validators
└── server.ts           # Entry point
```

#### Khuyết điểm:
1. `routes/*.routes.ts` chứa quá nhiều logic nghiệp vụ (ví dụ: `debt.routes.ts` tính toán FIFO, update `groupMember`, `debtTransaction` trực tiếp trong handler).
2. Khi mở rộng feature mới, route file sẽ phình to >500-1000 dòng, rất khó bảo trì và viết Unit Test.

---

### 3.2 Đánh giá Cây Thư mục Frontend Hiện tại (`frontend/src/`)

#### Hiện tại:
```
frontend/src/
├── components/         # Modals, Form fields, Widgets, UI components (Đã có ui/AppModal, ui/FormField)
├── context/            # BalanceContext, AuthContext, ThemeContext
├── hooks/              # useSWR, useAutoCapture, useUIMode...
├── pages/              # Advanced & Simple pages
├── services/           # api.ts, prefetchService.ts
└── utils/              # eventBus, formatters
```

#### Ưu điểm:
1. Tuân thủ tốt SOLID & DRY rules (Phase 0 Review): Tách `src/components/ui/` cho base UI components.
2. Có `prefetchService` và `useSWR` tối ưu performance.
3. Đồng bộ 100% giữa Simple Mode và Advanced Mode qua shared services.

#### Hướng nâng cấp (Feature-Driven Structure):
Khi dự án phình to (>30+ màn hình), các components trong `components/` sẽ rất hỗn loạn. Nên nhóm theo **Domain / Feature**.

---

## 4. MASTER BLUEPRINT: CÂY THƯ MỤC CHUẨN ENTERPRISE (MASTER TEMPLATE)

Bộ khung cây thư mục chuẩn cấp Senior/Architect dành cho mọi dự án TypeScript Fullstack (Next.js / Node.js + React):

### 4.1 Master Blueprint — BACKEND (`backend/src/`)

```
backend/src/
├── config/                     # Environment variables, database configs, logger setup
├── controllers/                # Thin HTTP adapters (chỉ parse req, call service, res.json)
│   ├── auth.controller.ts
│   ├── debt.controller.ts
│   ├── transaction.controller.ts
│   └── splitBill.controller.ts
├── services/                   # Business Logic Services (Pure domain logic)
│   ├── debt.service.ts         # Logic FIFO, running balance calculation
│   ├── splitBill.service.ts    # Logic chia tiền nhóm
│   └── transaction.service.ts
├── repositories/               # Data Access Layer (Prisma / SQL queries + Caching)
│   ├── base.repository.ts      # Generic CRUD & cache logic
│   ├── debt.repository.ts
│   └── wallet.repository.ts
├── patterns/                   # Reusable Enterprise Design Patterns
│   ├── pipeline/               # Pipeline execution framework
│   │   ├── Pipeline.ts
│   │   └── PipelineStage.ts
│   └── chain/                  # Chain of responsibility handlers
├── pipelines/                  # Concrete Business Pipelines
│   ├── smsCapture/             # Auto SMS Parsing Pipeline
│   │   ├── stages/             # ParseStage, MatchWalletStage, ExecuteStage
│   │   └── SmsCapturePipeline.ts
│   └── debtSettle/             # FifoSettlePipeline
├── dtos/                       # Data Transfer Objects & Zod Validation Schemas
│   ├── debt.dto.ts
│   └── transaction.dto.ts
├── events/                     # Pub/Sub Event Bus & Event Handlers
│   ├── eventBus.ts
│   └── handlers/               # CacheInvalidationHandler, NotificationHandler
├── middleware/                 # Express/Fastify Middlewares (Auth, RateLimit, ErrorHandler)
├── utils/                      # Helper pure functions (date, currency, crypto)
└── server.ts                   # Application Entry Point
```

---

### 4.2 Master Blueprint — FRONTEND (`frontend/src/`)

```
frontend/src/
├── assets/                     # Icons, images, fonts, static styles
├── components/                 # Shared Cross-Feature UI System (Atomic Design)
│   ├── ui/                     # Base UI components (AppModal, FormField, Button, Input, Card)
│   ├── layout/                 # MainLayout, Sidebar, Header, Navigation
│   └── feedback/               # Toast, Spinner, Skeleton, ErrorBoundary
├── features/                   # Domain-Driven Feature Modules (Co-location Pattern)
│   ├── debt/                   # Feature Module: Quản lý nợ
│   │   ├── components/         # DebtManagerModal, DebtCard, DebtTxHistory
│   │   ├── hooks/              # useDebt.ts, useDebtSync.ts
│   │   ├── services/           # debtApi.ts
│   │   └── types/              # debt.types.ts
│   ├── splitBill/              # Feature Module: Chia tiền nhóm
│   │   ├── components/         # SplitBillModal, MemberPillSelector
│   │   ├── hooks/              # useSplitBill.ts
│   │   └── services/           # splitBillApi.ts
│   └── transactions/           # Feature Module: Quản lý giao dịch
├── context/                    # App-wide global contexts (AuthContext, ThemeContext)
├── hooks/                      # Global reusable custom hooks (useSWR, useUIMode)
├── pages/                      # Page containers (Routing targets)
│   ├── advanced/               # Advanced Mode Dashboard/Pages
│   └── simple/                 # Simple Mode Dashboard/Pages
├── services/                   # Core HTTP client, Axios/Fetch interceptors, Prefetcher
│   ├── api.ts
│   └── prefetchService.ts
├── utils/                      # Helper functions, EventBus, Math helpers
└── types/                      # Shared global TypeScript types
```

---

## 5. QUY TẮC THIẾT KẾ & CODE DÀNH CHO SENIOR ENGINEER (RULES TO FOLLOW)

### Rule 1: Thin Controller, Rich Service, Encapsulated Repository
- **Controller**: KHÔNG chứa `prisma.xxx` hay `if/else` nghiệp vụ. Chỉ làm nhiệm vụ HTTP Adapter.
- **Service**: KHÔNG chứa SQL raw. Chỉ chứa Business Rules & phối hợp Repositories.
- **Repository**: Nơi DUY NHẤT được phép gọi Database ORM / Client và xử lý Cache.

### Rule 2: Co-location Principle (Frontend Feature Modules)
- Code thuộc về một Feature nào (Debt, SplitBill, Analytics) thì để trong folder `features/<feature_name>/`.
- Chỉ đưa vào `components/ui/` những component thực sự dùng chung cho toàn bộ app (Modal base, Input base, Button base).

### Rule 3: Single Responsibility & Open-Closed trong Component
- Sử dụng pattern Wrapper (`AppModal`, `FormField`) đã quy định trong `AGENTS.md`.
- KHÔNG tạo lại cấu trúc modal / inline error label ở bất kỳ màn hình nào mới.

### Rule 4: Non-Destructive Invalidation & Fallback Strategy
- Khi thực hiện mutation dữ liệu, luôn duy trì **Strategy A (Cache / Linked Records)** và **Strategy B (Direct Lookup Fallback cho Data cũ)** như bài học từ FIFO Debt Sync.

---

*Tài liệu này được biên soạn: 2026-08-05 | Đã cập nhật vào Knowledge Base của Dự án*

## Addendum 2026-08-25 — Financial mutation boundary

- Split bill dùng Transaction Script service cho một business mutation nguyên tử; route chỉ validate/serialize/map lỗi HTTP.
- Số dư nợ nội bộ luôn là signed `bigint`: receivable dương, payable âm. Chỉ đổi sang JSON number tại boundary sau khi schema đã chặn unsafe integer.
- Idempotency là durable DB state, không phải debounce UI. Fingerprint bind user + key + canonical intent; key thô không được lưu.
- Row lock bảo vệ state entity; advisory lock bảo vệ business key chưa chắc đã có row (`userId + personName`). Tất cả writer cùng dùng một lock namespace.
- Audit history append-only: thao tác xóa business object tạo reversal entry trước khi xóa object, không xóa lịch sử để “làm sạch”.
- Savings balance là một aggregate có row lock; mọi delta và resulting snapshot phải commit cùng aggregate. Không dùng `Number(existing.currentAmount) + amount` cho financial mutation.
- Prisma arithmetic operators giữ dấu: `decrement: -x` là increment. Vì vậy validation positive amount phải xảy ra trước mọi wallet mutation và được kiểm tra lại cho legacy/default values.
- Exactly-once effect của scheduler cần hai lớp: atomic schedule claim ngăn hai worker thắng cùng occurrence và unique deterministic transaction key làm hàng rào durable. Balance update + history snapshot phải nằm cùng DB transaction.
- Idempotency identity phải bind tenant trước unique constraint. Không lưu raw retry key và không dùng global unique raw key cho bảng chỉ suy ra owner qua parent relation.
- Không đưa nullable column trực tiếp vào business unique key nếu `NULL` mang nghĩa một scope thật. Chuẩn hóa thành sentinel/domain key không-null hoặc dùng `NULLS NOT DISTINCT` có chủ đích.
- Mutation sửa/xóa aggregate tiền không được truyền `oldState` đọc ngoài transaction xuống repository. Lock row, đọc current state, rồi lock mọi dependent aggregate theo một canonical order trước khi reverse/apply.
- Current balance là derived aggregate của opening balance + immutable ledger effects; client không được ghi trực tiếp. Khi sửa opening balance phải rebuild current balance và historical snapshots trong cùng wallet lock.
- Invariant theo tenant như “luôn còn ít nhất một ví” cần một canonical parent lock (user row). `count` ngoài transaction hoặc chỉ khóa từng child không serialize được hai delete khác child.
- Batch mutation phải scope từng item theo cả tenant và tập business entity đã được người dùng xác nhận. Ownership đúng nhưng đổi một record ngoài selection vẫn là corruption nghiệp vụ.
- Create endpoint có hậu quả lâu dài (challenge/schedule/goal) cần durable idempotency ở server; UI loading state hay debounce chỉ cải thiện UX, không chứng minh exactly-once effect khi mất mạng.
### Dependency direction: transport không được xâm nhập domain

- Route/controller là inbound adapter: parse HTTP, gọi use case/service và map response. Service/repository/lib/config không được import route hoặc controller.
- Nghiệp vụ dùng lại ở nhiều transport/worker phải nằm trong service thuần. Ví dụ streak calculation, timezone boundary và optimistic concurrency không thuộc Express router.
- CI nên scan import graph tối thiểu để chặn dependency ngược; unit test pure function cho calendar/state transition và integration test database cho concurrent update.
