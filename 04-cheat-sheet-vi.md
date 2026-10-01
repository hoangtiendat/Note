# Cheat sheet — mở cạnh IDE lúc dạy

Nguồn: `spring-boot-live-demo-script-vi.md`. Có gì lệch nhau thì **kịch bản đúng**.

## 1. Lịch trình và checkpoint

| Giờ | Phần | Slide | Tag khi xong |
|---|---|---|---|
| 09:30 | Mở đầu, khảo sát 0/1/2 | 1–2 | — |
| 09:35 | Mục tiêu, request flow | 3–4 | — |
| 09:38 | Fundamentals, start.spring.io (Explore, không Generate; Package `com.example.product`), chạy starter; #8 = `404` chỉ khi còn giờ | 5 | — |
| 09:43 | Dependency Injection | 6 | — |
| 09:47 | Package structure, demo project | 7–8 | — |
| 09:50 | Entity `Product` → `Ctrl+F9` | 9 | `step-01-entity` |
| 09:56 | `CreateProductRequest`, `ProductResponse` | 9 | `step-02-dtos` |
| 10:01 | `ProductRepository` | — | `step-03-repository` |
| 10:04 | `ProductService.create` | (6) | `step-04-service` |
| 10:11 | Controller `POST` | — | — |
| 10:16 | Chạy: #1 `201`, #2 `400` mặc định, (#10 `500`) | — | `step-05-post-endpoint` ✅ **A 10:23** |
| 10:23 | Nghỉ 5 phút / buffer #1 | — | — |
| 10:28 | `GlobalExceptionHandler` (400) | 10 | `step-06-error-handling` |
| 10:37 | `GET /{id}` + 404 | 10 | `step-07-get-by-id` ✅ **B 10:44** |
| 10:44 | Pagination, sorting | 11 | `step-08-pagination` |
| 10:54 | *Tùy chọn:* SKU trùng → 409 (cắt đầu tiên) | — | `step-09-duplicate-sku` |
| 10:57 | Config + Profiles | 12 | `step-10-profiles` ✅ **C 11:05** |
| 11:05 | Giới thiệu testing | 13 | — |
| 11:06 | Unit test (Mockito) | 13 | `step-11-unit-test` |
| 11:11 | Integration test (MockMvc) | 13 | `step-12-integration-test` |
| **11:15** | **Tổng kết — bắt đầu đúng giờ, dù demo ở đâu** | 4, 14, 15 | — |
| 11:19 | Q&A (rút còn 5 phút nếu trễ) | 16 | — |
| 11:27 | Kết thúc: link repo, bài tập PUT, feedback | 16 | — |

Quy tắc: lỗi compile quá 2 phút → nhảy tag. 11:13 integration test chưa xanh → mở file ở cửa sổ dự phòng.

## 2. Nhảy tag (Terminal `Alt+F12`, **dừng app trước** `Ctrl+F2`)

```powershell
git stash -u
git checkout step-07-get-by-id
```

- *detached HEAD* là bình thường. Sau đó: chạy lại app, gửi lại #1 hoặc `seed.http`.
- `step-10-profiles` đã gồm bước 409. Sau buổi: `git stash list` / `git stash pop`.
- Tag: `step-01-entity` · `step-02-dtos` · `step-03-repository` · `step-04-service` · `step-05-post-endpoint` ·
  `step-06-error-handling` · `step-07-get-by-id` · `step-08-pagination` · `step-09-duplicate-sku` ·
  `step-10-profiles` · `step-11-unit-test` · `step-12-integration-test`.
- Câu nói: "Đây là bản giống hệt những gì mình định gõ, mình dùng nó để giữ đúng giờ."

## 3. `demo-requests.http` (`@host = http://localhost:8080`)

| # | Request | Kết quả mong đợi |
|---|---|---|
| 1 | `POST` KB-100, 79.99 | `201`, `id` được lưu vào `{{productId}}` |
| 2 | `POST` `name:""`, `sku:""`, `price:-1` | `400` — trước step-06: body mặc định; sau: `VALIDATION_ERROR` |
| 3 | `GET /api/products/{{productId}}` | `200`; ngay sau restart → `404` (H2 in-memory mất dữ liệu) |
| 4 | `GET /api/products/99999` | `404`, `"Product not found: 99999"` |
| 5 | `?page=0&size=5&sort=name,asc` | 5 item, `totalElements` 9, `totalPages` 2; đầu tiên Desk Lamp |
| 6 | `?page=1&size=5&sort=name,asc` | 4 item: Noise-Cancelling Headset, USB-C Hub, Webcam HD, Wireless Mouse; `"last": true` |
| 7 | `?page=0&size=5&sort=price,desc` | Ergonomic Chair 249.00, Monitor 219.00, Headset 129.00, Keyboard 79.99, Webcam 59.99 |
| 8 | `GET /api/products` (không tham số) | 09:38: `404` (chưa có controller). Sau step-08: 9 item, sort `name` |
| 9 | `?sort=unknownField` | `500`, console `PropertyReferenceException` |
| 10 | `POST` KB-100 lần nữa (tên khác) | Trước step-09: `500` (`SQL Error: 23505`). Sau: `409 DUPLICATE_SKU` |

`seed.http` → **Run All** (▶▶) → 8 request `201`. Gửi thêm #1 → tổng **9** sản phẩm. Restart là phải seed lại.
Client gửi `sort` thì **thay thế** sort mặc định. `size` tối đa 2000.

## 4. Output quan trọng

| Tình huống | Thấy gì |
|---|---|
| Starter, chưa có profile | `No active profile set, falling back to 1 default profile: "default"` |
| Sau step-10 | `The following 1 profile is active: "dev"` / `"prod"` |
| `dev` + #1 (đại ý) | `DEBUG ... ProductService : Creating product sku=KB-100` + dòng `Hibernate: insert into products ...` |
| `prod` + #1 | `201`, không có SQL, không có DEBUG |
| 400 mặc định (trước step-06) | `{timestamp, status, error, path}` + dòng WARN `DefaultHandlerExceptionResolver` trong console |
| 400 sau step-06 | `{"code":"VALIDATION_ERROR","message":"name: must not be blank, price: must be greater than 0, sku: must not be blank","timestamp":"...Z"}` |
| 404 | `{"code":"PRODUCT_NOT_FOUND","message":"Product not found: 1",...}` |
| 409 | `{"code":"DUPLICATE_SKU","message":"SKU already exists: KB-100",...}` |
| `Page` JSON | `content` + `pageable`, `totalElements`, `totalPages`, `number`, `size`, `first`, `last`… Thứ tự field có thể khác |
| WARN một lần sau request phân trang đầu | Đại ý: `Serializing PageImpl instances as-is is not supported...` (pro-tip slide 11) |
| `via-dto` (tùy chọn, xóa lại sau) | `{"content":[...], "page":{"size","number","totalElements","totalPages"}}`, hết WARN |
| #9 | `500`, `No property 'unknownField' found for type 'Product'` |
| Unit test | Xanh, kèm cảnh báo đỏ Mockito self-attaching / Java agent — **không phải lỗi** |
| `timestamp` | Giờ UTC (đuôi `Z`), chậm hơn giờ VN 7 tiếng; 3/6/9 chữ số lẻ đều bình thường |

## 5. Phím tắt IntelliJ (Windows keymap)

| Phím | Làm gì |
|---|---|
| `Alt+Insert` | Generate: getter/setter, constructor |
| `Alt+Enter` | Quick fix, thêm import, *Add static import* |
| `Ctrl+Shift+T` | Create New Test… (trên tên class) |
| `Ctrl+F9` | Build Project |
| `Shift+F10` / `Ctrl+F5` | Run / Rerun app |
| `Ctrl+F2` | Stop app |
| `Alt+4` | Mở cửa sổ Run (xem log) |
| `Alt+F12` | Mở Terminal |
| `Ctrl+F12` `Ctrl+F12` | File structure: bấm **2 lần** để hiện cả method kế thừa từ `JpaRepository` (bấm 1 lần chỉ thấy `existsBySku`) |
| `Ctrl+Alt+O` | Optimize imports (xóa import thừa/sai) |
| Slide: số + `Enter` | Nhảy slide trong PowerPoint (vd. `4` Enter) |

Trước khi gõ code: bộ gõ tiếng Việt ở chế độ **E**.

## 6. Import đúng

| Dùng | Chọn | Không chọn |
|---|---|---|
| `@Entity`, `@Id`, `@Table`, `@Column`… | `jakarta.persistence.*` | `org.springframework.data.annotation.Id`, `org.hibernate.annotations.Table` |
| `@NotBlank`, `@Size`, `@NotNull`, `@Positive` / `@Valid` | `jakarta.validation.constraints.*` / `jakarta.validation.Valid` | — |
| `@Transactional` | `org.springframework.transaction.annotation` | `jakarta.transaction.Transactional` |
| `Page`, `Pageable` | `org.springframework.data.domain` | `org.hibernate.query.Page`, `java.awt.print.Pageable` |
| `@PageableDefault` | `org.springframework.data.web` | — |
| `Logger`, `LoggerFactory` | `org.slf4j` | `java.util.logging.Logger` |
| `HttpStatus`, `ResponseEntity`, `MediaType` | `org.springframework.http` | `org.apache.tomcat.util.http.parser.MediaType` |
| `@Test`, `assertEquals` | `org.junit.jupiter.api` (`Assertions`, static) | `AssertionErrors.assertEquals`, AssertJ |
| `@Mock`, `@InjectMocks` / `MockitoExtension` / `when`, `verify` | `org.mockito` / `org.mockito.junit.jupiter` / `org.mockito.Mockito` (static) | — |
| `@AutoConfigureMockMvc` | `org.springframework.boot.webmvc.test.autoconfigure` | package Boot 3 |
| `post`, `get` / `status`, `jsonPath` | `MockMvcRequestBuilders` / `MockMvcResultMatchers` (static) | `RequestEntity.post` / `MockRestRequestMatchers.jsonPath` |

Hộp thoại tạo test: chọn **JUnit5**, **đừng bấm Fix** nếu báo thiếu thư viện.

## 7. Câu hỏi khó — một câu trả lời

| Câu hỏi | Trả lời |
|---|---|
| Hai bean cùng kiểu? | App không khởi động (*"required a single bean, but 2 were found"*); dùng `@Primary`, `@Qualifier` hoặc inject `List<...>`. |
| `@MockBean` đâu rồi? | Bị bỏ ở Boot 4 → dùng `@MockitoBean` (`org.springframework.test.context.bean.override.mockito`). |
| `existsBySku` rồi insert có an toàn? | Không tuyệt đối; unique constraint trong DB mới là chốt chặn, bắt `DataIntegrityViolationException` để trả 409. |
| Biến môi trường ưu tiên cao nhất? | Cao nhất trong ba nguồn trên slide; `--server.port=...` và `-D...` còn cao hơn. |
| Test chạy được khi app đang ở 8080? | `@SpringBootTest` mặc định là MOCK: không mở port, JVM và H2 riêng. |

Không chắc: "Câu này mình ghi lại, trả lời sau buổi." Thêm câu: Phụ lục D của kịch bản, `02-qa-bank-vi.md`.

## 8. Sự cố → làm gì

| Triệu chứng | Làm gì |
|---|---|
| Lỗi compile > 2 phút | Nhảy tag của bước đó |
| Tới checkpoint mà chưa chạy | A `step-05-post-endpoint` · B `step-07-get-by-id` · C `step-10-profiles` |
| `Port 8080 was already in use` | Stop mọi tab Run; vẫn lỗi → `SERVER_PORT=8081`, sửa `@host` trong `.http` |
| 404 mà không có log | URL có `/` thừa, sai chính tả, quên restart sau khi thêm endpoint |
| `415 Unsupported Media Type` | Thiếu `Content-Type: application/json` |
| `GET /1` ra 404 sau restart | Bình thường (H2 in-memory) → gửi lại #1 hoặc `seed.http` |
| `productId` chưa có giá trị | Gửi thẳng `GET {{host}}/api/products/1` |
| App không khởi động, "no identifier" | Sai import `@Id` → `jakarta.persistence.Id` |
| IntelliJ/Maven hỏng hẳn | Chuyển sang cửa sổ dự phòng (`solution`), chạy/giải thích ở đó |
| Zoom/mạng rớt | Vào lại, "quay lại checkpoint gần nhất", nhảy tag, theo lịch trình |
| start.spring.io không vào được | Bỏ qua, mở `pom.xml` của starter |
| Trễ giờ | Cắt theo thứ tự: (start.spring.io nếu trễ từ 09:38) → 409 → #8/sort thêm → `via-dto` → `/abc` → test đỏ (Phụ lục A) |
