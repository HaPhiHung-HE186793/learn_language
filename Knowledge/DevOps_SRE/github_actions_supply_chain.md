# GitHub Actions Supply-Chain Controls

## Immutable execution

- Major/minor tags như `@v4` là mutable references. Một workflow không đổi vẫn có thể chạy code khác nếu tag bị di chuyển hoặc upstream bị chiếm quyền.
- External action phải pin vào full 40-character commit SHA. Ghi release version bên cạnh SHA để reviewer hiểu provenance mà không đổi tính immutable.
- Pinning không thay thế cập nhật. Dependabot tạo PR cho ecosystem `github-actions`; quality gate và reviewer đánh giá release notes rồi mới merge SHA mới.

## Enforcement pattern

- Repository có một verifier quét mọi `.github/workflows/*.yml`, từ chối reference không phải SHA đầy đủ, action ngoài allowlist đã review và reference thiếu comment version.
- Verifier phải chạy sớm trong required quality workflow. Chính `checkout` và `setup-node` dùng để chạy verifier cũng phải được pin.
- Workflow chỉ dùng quyền tối thiểu (`contents: read` mặc định), không truyền production secret cho pull request code và không dùng `pull_request_target` để checkout code không tin cậy.
- Docker service images trong CI phải giữ human-readable version tag và pin `sha256` digest; verifier chặn tag-only image. Hosted runner image vẫn do GitHub quản lý, còn package dependency dùng lockfile + audit.

## Review checklist

1. SHA có thuộc tag/release chính thức của đúng owner/action không?
2. Release có thay runtime requirement, permissions, cache hoặc artifact semantics không?
3. Workflow vẫn có permissions tối thiểu và không đưa secret vào untrusted job không?
4. Quality gate, YAML parse và artifact invariant tests có pass sau update không?

## Release evidence bundle

- Mỗi Android release artifact phải đi cùng CycloneDX SBOM của production npm graph, Gradle `releaseRuntimeClasspath`, signing-certificate report, R8 mapping và checksums.
- Provenance manifest bind evidence hashes với full source commit, workflow run ID/attempt và toolchain chính. Manifest tự khai báo không thay thế cryptographic platform attestation, nhưng loại nhầm artifact và tạo audit trail có thể kiểm tra offline.
- SBOM gate phải kiểm format/version, root component, component inventory không rỗng, package URL/version và cấm dev-scope hoặc secret-like content.
- Khi có CVE, tra SBOM + native dependency inventory của đúng checksum artifact trước khi quyết định ảnh hưởng; không suy luận từ dependency state trên nhánh hiện tại.
