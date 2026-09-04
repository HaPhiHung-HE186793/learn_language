# Web Vitals, Instant Latency & Network Performance Optimization

> **Tài liệu Senior Performance**: Kỹ thuật tối ưu Instant 0ms Latency, Dynamic Prefetching, và Core Web Vitals (LCP, INP, CLS).

Chuẩn hóa các kỹ thuật tối ưu hóa hiệu suất mạng và Core Web Vitals của Google.INP Optimization (Interaction to Next Paint): Giảm độ trễ hiển thị bằng cách chia nhỏ các tác vụ dài (yield often) để không chặn luồng chính.  Asset Loading Strategy: Chỉ tải tài nguyên khi người dùng thực sự tương tác (Import On Interaction) hoặc khi component hiển thị trên màn hình (Import On Visibility).  Font Optimization: Khai báo font-display: swap trong CSS hoặc sử dụng thẻ link preload để hiển thị văn bản ngay lập tức mà không phải chờ font tải xong.  

## 1. STRATEGY CHO INSTANT 0MS LATENCY

1. **Synchronous Memory Read (SWR L1)**: Khi người dùng chuyển tab, SWR đọc RAM Map ngay lập tức (0ms delay) và hiển thị dữ liệu lập tức.
2. **Warmup & Route Hover Prefetching (`prefetchService`)**:
   - Khởi động app / Đăng nhập → Gọi `prefetchService.warmupAppCache()` tải ngầm toàn bộ dữ liệu cần thiết.
   - User rê chuột / chạm vào Tab → Gọi `prefetchService.prefetchTab(path)` để tiền tải dữ liệu trước khi click.
3. **Safety Timeout Guard**: Đặt timeout 12 giây trong SWR hook để ngắt loading state nếu network bị đứt.

---

## 2. DYNAMIC BUNDLE & IMAGE OPTIMIZATION
- Lazy load các Modal nặng bằng `React.lazy()` và `Suspense`.
- Sử dụng WebP/AVIF format cho hình ảnh minh hoạ.
# Network-aware Finance UX Addendum `[Updated 2026-08-25]`

- `navigator.onLine` chỉ là tín hiệu trạng thái, không phải bằng chứng internet thực sự hoạt động; request success/error vẫn là nguồn xác nhận cuối.
- Dữ liệu tài chính lấy từ cache phải có disclosure “dữ liệu cũ” và thời điểm sync gần nhất.
- Tôn trọng Network Information API `effectiveType` và `saveData`: tắt speculative prefetch trên 2G/Save-Data, giảm warmup trên 3G.
- Không queue write tài chính nếu chưa có idempotency key bền vững và trạng thái pending hiển thị cho người dùng.
- Khi đổi tài khoản, xóa cả cached payload lẫn last-sync timestamp để tránh thông tin phiên trước gây hiểu nhầm.
## HttpOnly session và offline cache partition `[Updated 2026-08-25]`

- Không dùng access token làm cache key trên Web vì token phải ở HttpOnly cookie. Sau login, server cấp nonce `sessionPartition` không có quyền truy cập; client gửi `X-Session-Partition` để Service Worker hash thành cache namespace.
- Cookie fetch bắt buộc `credentials: include`. Mutation thêm CSRF header trước khi gửi. GET/HEAD và mutation có durable `Idempotency-Key` được retry tối đa một lần với cùng key; mutation không có key không được tự replay.
- Dùng một deadline end-to-end thay vì tạo timeout mới sau mỗi retry. Caller cancellation phải được giữ nguyên, không biến thành banner lỗi backend.
- `navigator.onLine=true` không đủ chứng minh API reachable. Transport timeout/unreachable phát event degraded; bất kỳ HTTP response nào chứng minh backend reachable và dọn trạng thái này.
- Cached user giúp first paint khi mạng yếu nhưng không chứng minh session còn hiệu lực. Chạy `/auth/me` nền; 401 purge session/cache, timeout/offline giữ UI với network disclosure.
- Production nên đặt app và API trên cùng registrable domain để SameSite strict hoạt động ổn định, tránh phụ thuộc third-party cookies bị Safari/browser privacy controls chặn.

## Hosting and financial-cache boundary `[Updated 2026-08-25]`

- HTTP/CDN layer của ứng dụng tài chính phải trả `Cache-Control: no-store`; offline speed thuộc về service worker cache có session partition, TTL và explicit invalidation. Không giao cùng trách nhiệm cho browser cache và SW.
- CSP production không dùng inline script. Đưa bootstrap/service-worker registration vào hashed module bundle; verifier phải khóa `script-src 'self'`, anti-framing và không cho hai hosting config drift.
- Deployment smoke test phải chạy trên origin thật vì local build không chứng minh CDN headers, exact CORS hoặc readiness của database/Redis.

## Production bundle budgets `[Updated 2026-08-25]`

- Lazy loading chỉ có giá trị khi được bảo vệ bằng CI budget; build pass không ngăn entry/chunk phình âm thầm sau mỗi dependency.
- Đo cả raw và gzip: raw phản ánh parse/compile/memory trên thiết bị yếu, gzip gần chi phí truyền tải. Kiểm soát entry, từng chunk, CSS và tổng JS thay vì chỉ một file.
- Baseline launch: entry JS ≤ 500 KB raw/150 KB gzip; mỗi JS chunk ≤ 500 KB raw/135 KB gzip; tổng JS ≤ 350 KB gzip; entry CSS ≤ 90 KB raw.
- Budget là regression gate, không thay real-device LCP/INP và throttled 3G. Khi vượt ngưỡng phải split/remove dependency hoặc có quyết định performance review được ghi nhận, không tùy tiện tăng số.

## Bounded client response consumption `[Updated 2026-08-25]`

- `response.text()`, `json()` và `blob()` buffer toàn body trước khi application kiểm soát size. Client cần streaming byte cap dù backend đã có limit; CDN/proxy/config drift vẫn là trust boundary.
- Dùng cap riêng: JSON business tối đa 10 MB, error envelope 64 KB, user-initiated export 50 MB. Kiểm tra `Content-Length` để fail fast nhưng vẫn đếm từng chunk khi header thiếu/sai.
- Cancel response body trước retry gateway và ngay khi oversize để giải phóng connection/bandwidth. Một deadline phải bao phủ download; object URL phải revoke sau khi click.
- API exception phải giữ `status` và bounded machine `code`; chỉ giữ message làm nội dung UX. Nếu bỏ code, UI buộc parse text và không thể xử lý consent/rate-limit/conflict ổn định.
- Stale cache fallback chỉ hợp lệ cho lỗi connectivity, 5xx tạm thời, 408 hoặc 429 và phải disclosure rõ dữ liệu đã đồng bộ trước đó. Authoritative 4xx (validation, conflict, consent, money range) phải xóa representation của key hiện tại; không dùng cache cũ để che một business rejection.
- Không gắn mọi `error && data` là “offline”. UI phải phân biệt network/server/business theo HTTP status/code; `MONEY_RANGE_EXCEEDED` phải ẩn tổng cũ và giải thích rằng hệ thống từ chối đưa ra con số không chính xác.

## Atomic PWA release update `[Updated 2026-08-25]`

- Worker mới phải tải thành công exact `index.html` và toàn bộ hashed JS/CSS mà HTML tham chiếu trước khi được coi là sẵn sàng. Install lỗi phải xóa cache generation tải dở và giữ worker hiện tại phục vụ người dùng.
- Không gọi `skipWaiting()` tự động trong install. Hiển thị update banner, chỉ activate sau thao tác rõ ràng của người dùng; khi `controllerchange`, mọi tab đang do worker cũ kiểm soát reload đúng một lần để không chạy lẫn HTML/chunk giữa hai release.
- `/sw.js` phải `no-store`; verifier CI khóa cache version, explicit activation, failed-install cleanup và sự tồn tại của hashed assets trong production build.
- Source gate không thay thế device drill: deploy hai version liên tiếp trên Safari iOS PWA/Android, thử offline/Fast 3G/Slow 4G, hai tab đồng thời và một lần cố tình làm hỏng asset download.

## Race-safe financial API cache `[Updated 2026-08-25]`

- Cache tài chính phải fail closed: request thiếu hoặc có session partition sai định dạng được đi thẳng mạng, không bao giờ dùng namespace dùng chung như `anonymous`.
- Chỉ hash partition hợp lệ, có giới hạn độ dài, trước khi tạo CacheStorage key. Không để nonce hoặc Bearer token thô xuất hiện trong URL/key/devtools.
- Xóa cache chưa đủ để chống race. Mỗi fetch phải capture generation hiện tại; logout/mutation tăng generation trước khi delete, và response chỉ được serve/put nếu generation không đổi.
- Invalidation theo URL phải canonicalize URL và xóa tất cả partition tương ứng vì physical cache key có query nội bộ. Test phải mô phỏng response cũ hoàn tất sau invalidation và chứng minh cache không bị hồi sinh.
- SW không được dùng cache-first/stale-while-revalidate cho financial API vì response cache có thể che 401/409/422 authoritative. Dùng network-first; 4xx xóa representation và đi xuyên nguyên trạng.
- Chỉ connectivity, 5xx, 408 hoặc 429 được dùng fallback có giới hạn tuổi. Response fallback phải mang machine metadata để API client/SWR hiển thị disclosure và tuyệt đối không cập nhật `last_successful_sync_at`.
- Chỉ cache HTTP 200 JSON và đọc clone theo streaming byte cap. Không buffer body không giới hạn hoặc cache HTML 200 do proxy/config drift.

## Cross-session SWR generation `[Updated 2026-08-25]`

- Xóa Map/localStorage không đủ: fetch/prefetch của phiên A có thể hoàn tất sau logout rồi ghi lại vào key dùng chung mà phiên B sẽ đọc.
- Mọi async cache producer phải capture generation lúc bắt đầu và chỉ commit/return result nếu generation chưa đổi. `clearAllCache`, 401 và auth verification fail phải tăng generation trước khi xóa.
- RAM cache khác nhau giữa các tab. Lắng nghe thay đổi `session_active`/`session_partition` qua `storage`; tab nhận sự kiện phải tăng generation và fail closed UI auth khi tab khác logout hoặc đổi tài khoản.
- Regression test cần dùng deferred Promise: bắt đầu response A, đổi generation, resolve A, rồi chứng minh persistent cache rỗng và response B là dữ liệu duy nhất được commit.
- Generation phải được capture đồng bộ khi nhận fetch/prefetch, trước cả async partition hashing. Capture sau `crypto.subtle.digest` tạo cửa sổ invalidation mà request cũ có thể tự nhận generation mới rồi hồi sinh cache.

## Bounded render-failure recovery `[Updated 2026-09-04]`

- Top-level React error boundary phải nằm ngoài router/providers để lỗi render, lifecycle hoặc lazy chunk không biến thành màn hình trắng.
- Fallback không hiển thị `error.message`, stack, route state hay payload vì chúng có thể chứa dữ liệu nhạy cảm. Chỉ đưa thông báo hữu hạn và một hành động reload rõ ràng.
- Không khẳng định mutation “chưa xảy ra” sau crash. Response có thể mất sau khi server đã commit; hướng dẫn người dùng reload và kiểm tra lịch sử trước khi nhập lại.
- Dùng `role=alert`, touch target tối thiểu 44 px và fallback CSS có giá trị mặc định vì theme provider cũng có thể là nguồn lỗi.
- Error boundary không bắt event-handler/async callback errors; các đường đó vẫn cần typed API handling, global telemetry đã lọc dữ liệu và promise rejection discipline riêng.
- Không rải `console.error(error)` ở component/hook production. Dùng một boundary có finite event codes; raw exception chỉ được phép trong nhánh development và phải được kiểm chứng đã tree-shake khỏi production bundle. Nếu bổ sung remote telemetry, chỉ gửi taxonomy/count đã allowlist và tôn trọng consent—không gửi message, stack, URL, request/response hoặc financial state.

## Bounded user-visible errors `[Updated 2026-09-04]`

- Không hiển thị trực tiếp `err.message` từ fetch, plugin native, browser API hoặc thư viện. Exception có thể chứa SQL, provider body, URL, identifier hoặc dữ liệu người dùng.
- Shared API boundary phải tạo typed error cho offline, deadline và transport failure. HTTP 5xx luôn dùng catalog generic; 4xx chỉ được phản ánh khi chuỗi không rỗng, không control character và nằm trong giới hạn 240 ký tự.
- UI chỉ tin message từ typed API/cache boundary. Mọi error khác nhận fallback cố định theo hành động như tạo ví, xuất báo cáo hoặc đồng bộ Gmail để vẫn hữu ích mà không disclosure.
- Kiểm tra source ở CI phải cấm direct exception message ngoài API boundary; regression cần chứng minh SQL/provider response 5xx và 4xx quá dài/control-character không tới người dùng.
