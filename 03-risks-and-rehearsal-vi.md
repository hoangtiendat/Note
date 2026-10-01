# Rủi ro khi demo live và kế hoạch tập dượt

## 0. Cách dùng tài liệu này

- Đọc **một lần đầy đủ** trước buổi tập đầu tiên. Không cần thuộc, chỉ cần biết "sự cố này có cách
  xử lý, nằm ở mục nào".
- Mục 2 (Top 10) và mục 7 (câu nói chuẩn bị sẵn) là phần cần nhớ nhất. In ra hoặc mở ở màn hình phụ.
- Mục 3 đi theo đúng thứ tự kịch bản `spring-boot-live-demo-script-vi.md`. Khi tập, đọc mục của
  bước sắp gõ trước khi gõ.
- Tài liệu này **không thay** kịch bản. Giờ giấc, số request, tên tag, run configuration và
  [Kế hoạch dự phòng] đều lấy theo kịch bản. Ở đây chỉ bổ sung: triệu chứng trông thế nào, sửa ra sao
  trong 1 phút, khi nào thì thôi sửa và nhảy tag.
- Log/lỗi ghi "đại ý" là nội dung gần đúng, chữ trên máy có thể khác một chút. Tìm theo từ khóa.

Quy tắc vàng (giống kịch bản): **đọc to lỗi → sửa trong 1 phút → quá 2 phút thì nhảy tag**, nói
"mình lấy bản đã chuẩn bị để tiết kiệm thời gian".

Nhảy tag (Terminal PowerShell trong IntelliJ, **dừng app trước** bằng `Ctrl+F2`):

```powershell
git stash -u
git checkout step-07-get-by-id
```

Sau đó: chạy lại app, gửi lại request #1 hoặc `seed.http` nếu cần dữ liệu. Tag của một bước là
trạng thái **đã xong** bước đó.

## 1. Ba điều cần hiểu để chẩn đoán nhanh

1. **Không có devtools.** Mỗi lần sửa code Java hoặc file `.yml`, phải restart app (`Ctrl+F5`). Quên
   restart là nguyên nhân số một của "code đúng mà kết quả sai".
2. **H2 nằm trong bộ nhớ.** Mỗi lần restart là database trống, id bắt đầu lại từ 1. Sau restart
   luôn gửi request #1 (hoặc `seed.http`) trước khi `GET`.
3. **Nhìn body để biết lỗi từ đâu.** Body có `code` (`VALIDATION_ERROR`, `PRODUCT_NOT_FOUND`,
   `DUPLICATE_SKU`) là handler của mình đã chạy. Body dạng `timestamp/status/error/path` là body mặc
   định của Spring Boot — nghĩa là handler chưa có, chưa restart, hoặc sai URL.

## 2. Top 10 rủi ro

| # | Rủi ro | Khả năng | Hậu quả | Phòng ngừa trước buổi | Xử lý tại chỗ |
|---|---|---|---|---|---|
| 1 | Maven import lần đầu phải tải thư viện Spring Boot 4.1.1 ngay trong buổi | Cao nếu chưa import trước | Không chạy được gì trong 5–20 phút | Import, build, chạy test **trên đúng máy, đúng mạng công ty** từ D-3; tối D-1 làm lại (kịch bản, mục "Tối hôm trước") | Nói phần slide lâu hơn trong khi đợi; mạng chết thì bắt hotspot điện thoại; hỏng hẳn thì mở cửa sổ dự phòng, giải thích bằng code có sẵn |
| 2 | Quên restart sau khi sửa code | Cao | Kết quả cũ, 404/405 lạ, lớp tưởng code sai | Tập thói quen: gõ xong → `Ctrl+F5` → đợi `Started ProductApplication` → mới gửi request | Nhìn body (mục 1.3); `Ctrl+F5`; gửi lại |
| 3 | Restart làm mất dữ liệu H2 | Chắc chắn xảy ra | `GET` ra 404, trang phân trang rỗng | Kịch bản đã biến việc này thành điểm dạy ở bước 07; thuộc thứ tự "restart → #1/seed → GET" | "Đúng rồi, H2 trong bộ nhớ, restart là mất" → gửi #1 hoặc `seed.http` |
| 4 | IntelliJ gợi ý import nhầm class trùng tên | Trung bình (thấp nếu đã exclude) | Lỗi compile, hoặc lỗi khởi động ở chỗ rất xa nơi gõ sai | Thêm danh sách exclude (kịch bản, mục "Tối hôm trước" #6); khi popup hiện, **đọc to package** | So với Phụ lục E của kịch bản; xóa dòng import, `Alt+Enter` chọn lại |
| 5 | Gõ sai/typo, lỗi compile không tìm ra | Trung bình | Mất 2–5 phút, mất bình tĩnh | Tập gõ đủ 2 lần; dùng autocomplete thay vì gõ hết | Đọc lỗi **đầu tiên** trong cửa sổ Build; quá 2 phút → nhảy tag |
| 6 | `Port 8080 was already in use` | Trung bình | App không khởi động | Dừng app sau mỗi lần chạy thử; cửa sổ dự phòng không chạy ở 8080 | Stop ở mọi tab Run; còn lỗi thì xem mục 3 "Port 8080" |
| 7 | YAML sai ở bước profile hoặc `via-dto` (tab, `spring:` lặp lại) | Trung bình | App không khởi động (bước 08 nếu là `via-dto`, ngay trước CHECKPOINT C nếu là profile) | Tập bước 10 hai lần; nhớ "chỉ có **một** dòng `spring:` ở gốc" | `Ctrl+Z`, lồng lại dưới `spring:` có sẵn; `via-dto` thì xóa hẳn khối đó; profile quá 2 phút → `step-10-profiles` |
| 8 | Bộ gõ tiếng Việt (Telex) biến chữ trong code | Trung bình | `isActive` thành `íActive`, lỗi compile khó thấy qua Zoom | Loại trừ `idea64.exe` trong Unikey/EVKey hoặc chuyển sang **E** trước khi gõ | `Ctrl+Z`, chuyển bộ gõ sang E, gõ lại |
| 9 | Zoom: chữ quá nhỏ, share nhầm màn hình, thông báo bật lên | Trung bình | Lớp không đọc được code; lộ kịch bản/tin nhắn riêng | Font 20/16, share **cả màn hình** chứa IntelliJ, bật Do not disturb, nhờ người xác nhận đọc được | Co-host báo ngay; `Ctrl+con lăn` tăng font; dừng share, chọn lại màn hình |
| 10 | Trễ giờ | Cao với người trình bày lần đầu | Mất phần test và tổng kết | 2 lần tập bấm giờ đầy đủ; biết trước danh sách cắt (Phụ lục A) | Cắt theo Phụ lục A từ trên xuống; **11:15 luôn tổng kết** |

## 3. Rủi ro theo từng bước của kịch bản

Mỗi bước: sự cố → triệu chứng (đại ý) → sửa trong 1 phút. Dòng "Nhảy tag khi" nói lúc nào dừng sửa.

### 09:38 — Chạy thử starter (slide 5)

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Maven chưa import xong | Không có nút ▶ cạnh `main`, hoặc import đỏ, class `SpringApplication` đỏ | Đợi thanh tiến trình; không kịp thì nói tiếp slide 6–7, quay lại chạy sau |
| JDK sai | Lỗi compile đại ý `release version 21 not supported` | *File → Project Structure → SDK* = JDK 21 (Temurin) |
| Windows Firewall hỏi quyền | Hộp thoại hiện trên màn hình đang share | *Cancel* cũng được, localhost vẫn chạy. Phòng bằng cách chạy trước ở D-1 |
| Quên dừng app | Lần chạy sau (10:16) IntelliJ hỏi "Stop and Rerun?" (chọn đồng ý); nếu app cũ chạy từ run config/cửa sổ khác thì báo port 8080 đã dùng | Nhớ `Ctrl+F2` như kịch bản ghi |

Request #8 lúc này ra `404` body mặc định — **đúng như kịch bản**, không phải lỗi.

### step-01 — Entity `Product`

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Chọn `org.springframework.data.annotation.Id` | Compile vẫn xanh. **10:16** app không khởi động, Hibernate báo đại ý entity "has no identifier" | Sửa import thành `jakarta.persistence.Id`, `Ctrl+F5` |
| Chọn `org.hibernate.annotations.Table` | Lỗi compile ở `name = "products"` | Đổi sang `jakarta.persistence.Table` |
| Generate thiếu setter (hoặc thiếu `setId`) | Service báo đỏ ở `setName`/`setSku`…; test bước 11 đỏ ở `setId` | `Alt+Insert` → *Getter and Setter* → chọn **tất cả** field |
| `Alt+Insert` không phản hồi | Laptop cần phím `Fn` | Chuột phải → *Generate…* |

Luôn `Ctrl+F9` (Build) sau bước này như kịch bản ghi. Cuối bước, liếc qua khối import: tất cả là
`jakarta.persistence.*`.

### step-02 — DTO

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Tạo nhầm *Class* thay vì *Record* | Phải sửa khai báo tay | *New → Java Class* → chọn **Record** |
| Đặt `@NotBlank` lên `price` (BigDecimal) | Compile xanh, khởi động xanh. Mọi `POST` (cả #1 hợp lệ) ra **500**, console đại ý `HV000030: No validator could be found for constraint ...NotBlank... BigDecimal` | Đổi thành `@NotNull @Positive` |
| Import constraint từ package khác | Annotation đỏ | Tất cả từ `jakarta.validation.constraints` |

### step-03 — Repository

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Gõ sai `existsBySku` (vd. `existsBySkuu`) | Compile xanh. **10:16** app không khởi động, đại ý `No property 'skuu' found for type 'Product'` | Sửa đúng tên, `Ctrl+F5`. Gõ `exists` rồi để autocomplete gợi ý |

Kịch bản nói đúng điều này như một điểm dạy ("gõ sai thì app không khởi động") — nếu xảy ra thật,
nói: "Đấy, đúng như mình vừa nói."

### step-04 — Service

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Chọn `jakarta.transaction.Transactional` | Bây giờ chưa thấy gì. **10:37** `readOnly = true` báo lỗi compile đại ý `cannot find symbol` | Đổi import thành `org.springframework.transaction.annotation.Transactional` |
| Đảo thứ tự tham số trong `toResponse` (vd. `name` và `sku` cùng là `String`) | Compile xanh; response #1 có `"name": "KB-100"` | Thứ tự phải khớp record: `id, name, sku, price, active` |
| Telex biến `isActive` thành `íActive` | Lỗi compile, chữ khó nhìn qua Zoom | `Ctrl+Z`, chuyển bộ gõ sang E |

### step-05 — Controller `POST` và chạy lần đầu (CHECKPOINT A 10:23)

Đây là lần chạy đầu tiên sau khi gõ 6 file — mọi lỗi "ngủ" ở bước 01–04 hiện ra cùng lúc. Khi app
không khởi động: cuộn console lên tìm khối `APPLICATION FAILED TO START` hoặc dòng `Caused by:` cuối
cùng; đó mới là nguyên nhân.

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Lỗi compile ở file nào đó | Cửa sổ Build liệt kê lỗi, app không chạy | Bấm vào lỗi đầu tiên, sửa |
| Thiếu `@RequestBody` | Request #1 **hợp lệ** mà vẫn ra 400 | Thêm `@RequestBody` trước kiểu tham số |
| Quên `@Valid` | Request #2 ra 201 | Đây là câu hỏi dự đoán trong kịch bản — thêm `@Valid`, restart |
| Sai đường dẫn (`/api/product`) | 404 body mặc định | Sửa `@RequestMapping("/api/products")` |
| Gửi #1 hai lần | Lần hai ra 500 (SKU trùng, H2 `23505`) | Bình thường — dùng làm "preview" bước 409 như kịch bản |
| Port 8080 bận | Đại ý `Port 8080 was already in use` | Xem mục "Port 8080" cuối mục này |

**Nhảy tag khi:** 10:23 mà #1 chưa ra 201 → `step-05-post-endpoint` (kịch bản, CHECKPOINT A).

### step-06 — Global exception handling

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Quên restart | #2 vẫn ra body mặc định `timestamp/status/error/path` | `Ctrl+F5`, gửi lại #2 |
| Quên `@RestControllerAdvice` | Như trên dù đã restart | Thêm annotation trên class |
| Quên `.sorted()` | Thứ tự `name/price/sku` khác kịch bản, mỗi lần chạy có thể khác | Thêm `.sorted()` — và dùng nó làm ví dụ vì sao cần sort |
| Import `Collectors`/`Instant` thiếu | Đỏ | `java.util.stream.Collectors`, `java.time.Instant` |

`timestamp` là giờ UTC (đuôi `Z`), ngày giờ trên máy sẽ khác ví dụ trong kịch bản — bình thường.

### step-07 — `GET /{id}` và 404 (CHECKPOINT B 10:44)

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Quên restart | #3 ra 404 **body mặc định** (không có `code`) | `Ctrl+F5` |
| Restart rồi, DB trống | #3 ra 404 **`PRODUCT_NOT_FOUND`** | Đúng kịch bản: gửi #1 rồi #3 |
| IntelliJ vừa mở lại nên chưa có `productId` | Báo biến chưa có giá trị | Gửi thẳng `GET {{host}}/api/products/1` (kịch bản đã ghi) |
| `readOnly` báo lỗi compile | Xem step-04 | Đổi import `@Transactional` |
| Hiếm: thiếu cờ `-parameters` | Đại ý `Name for argument of type [java.lang.Long] not specified` | Viết `@PathVariable("id") Long id`; sau buổi reload Maven |

**Nhảy tag khi:** 10:44 mà `GET` chưa chạy → `step-07-get-by-id`.

### step-08 — Pagination và sorting

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| `Pageable` từ `java.awt.print`, `Page` từ `org.hibernate.query` | Lỗi compile ngay | Cả hai từ `org.springframework.data.domain` |
| Quên restart | #5 ra **405** Method Not Allowed (đường dẫn `/api/products` mới chỉ có `POST`) | `Ctrl+F5` |
| Chưa chạy `seed.http` | `content: []`, `totalElements: 0` | *Run All* `seed.http`, rồi #1 → 9 sản phẩm |
| Chạy `seed.http` hai lần không restart | Các request seed ra 500 (SKU trùng) | Restart, seed **một** lần, rồi #1 |
| `via-dto` dán nguyên khối có `spring:` ở gốc | App không khởi động, đại ý duplicate key | Lồng `data:` vào dưới `spring:` có sẵn. Demo xong **xóa lại** (tag `step-08` không có dòng này) |
| Dùng terminal thay vì `.http` | `&` lỗi cú pháp; `sort=price,desc` không nháy → sort tăng dần mà **không báo lỗi** | Quay về `demo-requests.http`; nếu phải dùng terminal: `curl.exe` + URL trong nháy đơn |

WARN `Serializing PageImpl instances as-is is not supported…` và 500 của request #9 là **dự kiến**.

### step-09 — (Tùy chọn) SKU trùng → 409

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Quên restart | #10 vẫn 500 | `Ctrl+F5` → #1 → #10 |
| Kiểm tra đặt sau `save` | Vẫn 500 | Đặt `if (existsBySku…)` ở **đầu** `create` |

Trễ quá 2 phút thì bỏ qua (Phụ lục A #1). Tag `step-10-profiles` đã có sẵn bước này.

### step-10 — Configuration và Profiles (CHECKPOINT C 11:05)

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Gõ thêm một khối `spring:` mới ở đầu file, khối cũ vẫn còn | App không khởi động, đại ý duplicate key `spring` | Chỉ thêm `profiles:` + `active:` **dưới** dòng `spring:` có sẵn |
| Dùng tab trong YAML | Lỗi đọc YAML lúc khởi động | Xóa tab, dùng 2 dấu cách |
| Chép `spring.profiles.active` vào `application-dev.yml` | App không khởi động, `InvalidConfigDataPropertyException` | Xóa dòng đó khỏi file profile |
| Sai tên/vị trí file (`application_dev.yml`, nằm ngoài `src/main/resources`) | Log có dòng `profile is active: "dev"` nhưng không có SQL, không có DEBUG — **không báo lỗi** | Đổi tên đúng `application-dev.yml`, đặt cạnh `application.yml` |
| Gõ sai tên profile (`prdo`) | Không báo lỗi; app chạy chỉ với cấu hình gốc | Sửa tên trong run config |
| `show-sql` không nằm dưới `spring.jpa` | Không có dòng `Hibernate: ...` | Lồng đúng như snippet |
| `Logger` import từ `java.util.logging` | `log.debug(...)` báo đỏ | Dùng `org.slf4j.Logger` / `org.slf4j.LoggerFactory` |
| Quên restart | Vẫn thấy `No active profile set, falling back to 1 default profile: "default"` | `Ctrl+F5` |
| Chưa tạo run config `ProductApplication (prod)` | Mất thời gian tạo live | Tạo sẵn từ D-3; không có thì chỉ chiếu 2 file yml (Phụ lục A #8) |

Tìm nhanh trong console: `Ctrl+F` → `profile is active`. Không chạy `mvn` hay `java -jar` thật:
Maven không có trên PATH (`mvn` sẽ báo không nhận ra lệnh), và kịch bản chỉ **chiếu** các lệnh đó.

**Nhảy tag khi:** 11:05 mà profile chưa đúng → `step-10-profiles`.

### step-11 — Unit test (Mockito)

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| Hộp thoại *Create Test* hiện "JUnit5 library not found" + nút **Fix** | — | **Không bấm Fix**, bấm OK (kịch bản đã ghi) |
| Sai *Destination package* | Test nằm chỗ khác tag | Chọn `com.example.product.service` |
| `assertEquals` từ `AssertionErrors` hoặc AssertJ | Lỗi compile hoặc sai thứ tự tham số | `org.junit.jupiter.api.Assertions` |
| Stub `findById(1L)` nhưng gọi `getById(2L)` | Test fail ngay với `PotentialStubbingProblem` (đại ý "Strict stubbing argument mismatch") | Sửa tham số cho khớp. (`UnnecessaryStubbingException` là khi stub hoàn toàn không được gọi tới) |
| Chữ đỏ Mockito/Java agent | Lớp tưởng test lỗi | Chỉ vào thanh xanh: cảnh báo, không phải lỗi |
| Lần chạy test đầu cần tải `junit-platform-launcher` | Chậm hoặc báo lỗi tải khi mạng kém | Đã chạy test một lần ở D-1 trên mạng công ty thì không gặp |

Một file test bị lỗi compile sẽ chặn **mọi** test — sửa hết đỏ trước khi bấm ▶.

### step-12 — Integration test (MockMvc)

| Sự cố | Triệu chứng | Sửa nhanh |
|---|---|---|
| `post` từ `RequestEntity`, `jsonPath` từ `MockRestRequestMatchers`, `MediaType` từ Tomcat | Lỗi compile/kiểu | Bảng import trong kịch bản (mục Integration test) |
| `@AutoConfigureMockMvc` theo package Spring Boot 3 (nhớ từ tài liệu cũ) | Import đỏ | `org.springframework.boot.webmvc.test.autoconfigure` |
| Test chạy mấy giây mới xong | Lớp tưởng treo | Bình thường: đang khởi động Spring context |

**Nhảy tag khi:** 11:13 mà chưa xanh → dừng, mở file ở cửa sổ dự phòng, chuyển tổng kết đúng 11:15.

### Port 8080 và nhảy tag (dùng cho mọi bước)

- **Port bận:** bấm Stop ở mọi tab Run (kể cả cửa sổ dự phòng). Còn lỗi: trong PowerShell
  `netstat -ano | findstr :8080` để lấy PID rồi `taskkill /PID <pid> /F`. Cách cuối: run config với
  `SERVER_PORT=8081` và sửa `@host` trong **cả hai** file `.http`.
- **IntelliJ hỏi "Stop and Rerun?"** khi app đang chạy: chọn đồng ý.
- **`git checkout solution` trong repo chính sẽ lỗi**: `solution` đang được worktree dự phòng dùng,
  và file mới chưa commit cũng chặn checkout. Chỉ nhảy tới tag `step-XX`, không checkout `solution`.
- **Quên `git stash -u`**, checkout báo đại ý `untracked working tree files would be overwritten`:
  chạy `git stash -u` rồi checkout lại.
- Sau checkout, Git báo *detached HEAD* — bình thường. Đợi IntelliJ tải lại file rồi mới chạy.

## 4. Rủi ro môi trường

| Chủ đề | Rủi ro | Chuẩn bị |
|---|---|---|
| Maven import lần đầu | Máy chưa có artifact Spring Boot 4.1.1/Hibernate 7 trong `~/.m2`; lần mở đầu tải rất nhiều | Làm từ D-3, lặp lại tối D-1. Sau khi mọi thứ đã tải và test xanh, có thể bật *Toggle Offline Mode* trong tab Maven để IntelliJ không đi mạng |
| Mạng công ty/proxy | Tải Maven chậm hoặc bị chặn; có thể cần proxy | Import trên đúng mạng sẽ dùng hôm dạy. Nếu bị chặn, hỏi IT về proxy trước D-2, đừng tự đoán cấu hình. Dự phòng: hotspot điện thoại |
| Maven không có trên PATH | `mvn` trong terminal báo không nhận ra lệnh | Chỉ dùng tab Maven của IntelliJ (*Lifecycle → test/package*) |
| Firewall | Lần đầu chạy app, Windows Firewall hỏi quyền trên màn hình share | Chạy app một lần trước buổi; *Cancel* cũng không sao |
| Zoom share | Share một cửa sổ thì popup/hộp thoại/Postman không hiện | Share **cả màn hình** chứa IntelliJ; tắt *Share sound* nếu không cần |
| Màn hình phụ | Share nhầm màn hình phụ → lộ kịch bản, đáp án câu dự đoán | Trước giờ học, nhờ người xác nhận đang thấy IntelliJ. Không có màn hình phụ: in cheat sheet `04-cheat-sheet-vi.md` hoặc mở trên tablet |
| Cỡ chữ | Font 12 không đọc được qua Zoom | Editor 20, Console 16, bật *Change font size with Ctrl+Mouse Wheel* (kịch bản, mục "Tối hôm trước" #5) |
| Presentation Mode | Ẩn cửa sổ Run/Build, không thấy log | Dùng chế độ thường. Nếu lỡ dùng: `Alt+4` mở cửa sổ Run. Response HTTP chật chỗ: `Ctrl+Shift+F12` ẩn/hiện tool window |
| Thông báo | Teams/Outlook/Windows bật popup lên màn hình share | Bật *Do not disturb* của Windows, thoát Teams/Outlook hoặc tắt thông báo |
| Gợi ý code AI | Inline completion hiện code lạ, lớp hỏi "cái đó ở đâu ra" | Tắt AI Assistant/inline completion (gõ "completion" vào ô tìm của Settings) |
| Hộp thoại IntelliJ | *Trust project*, "add file to Git?" khi tạo file mới, thông báo Windows Defender về hiệu năng | Gặp hết ở lần mở D-3; chọn một lựa chọn cố định (vd. *Don't ask again*) để hôm dạy không hiện lại |
| Phím F | `Ctrl+F5`, `Shift+F10`, `Ctrl+F2`, `Ctrl+F9` cần `Fn` trên nhiều laptop | Tập với đúng bàn phím; không chắc thì bấm nút trên toolbar |
| PowerShell | `curl` có thể là alias của `Invoke-WebRequest` (Windows PowerShell 5.1); `&` không nháy là lỗi cú pháp; `,desc` bị tách im lặng; `-D...` bị tách ở dấu chấm | Gửi request bằng `demo-requests.http`. Nếu phải dùng terminal: `curl.exe`, URL trong nháy đơn, tham số `-D` trong nháy kép. Biến môi trường: `$env:X="..."`, không dùng `setx` |
| Pin/điện | Laptop tụt hiệu năng khi chạy pin | Cắm sạc, chế độ nguồn hiệu năng cao |

## 5. Câu hỏi khó và kiểm soát thời gian

### Chủ đề hay kéo ra câu hỏi khó

| Lúc nào | Chủ đề dễ bị hỏi | Cách xử lý |
|---|---|---|
| DI (09:43) | Hai bean cùng kiểu, circular dependency, field injection | Trả lời ngắn theo Phụ lục D; chi tiết ở `02-qa-bank-vi.md` |
| Service (10:04) | `@Transactional` propagation/isolation, rollback, method private | "Hôm nay chỉ cần biết nó bao một nghiệp vụ" → parking lot |
| Validation (10:28) | 400 hay 422, message tiếng Việt, catch-all `Exception` | Trả lời 1 câu; catch-all để tổng kết (kịch bản đã có ghi chú slide 14) |
| Pagination (10:44) | N+1, `EAGER`, `Page` JSON, `sort` không hợp lệ | N+1: một câu + "không dùng EAGER để sửa", còn lại parking lot |
| Profiles (10:57) | Thứ tự ưu tiên cấu hình, secret, Vault, Kubernetes ConfigMap | Thứ tự: CLI > `-D` > env > file profile > `application.yml`. Hạ tầng → sau buổi |
| Bất kỳ lúc nào | Microservices, WebFlux, Security/JWT, Lombok, MapStruct, Kotlin, virtual threads, nâng cấp Boot 3 → 4, H2 console | "Ngoài phạm vi hôm nay, mình ghi lại" → parking lot |

Cách "đỗ" câu hỏi (park):

- Câu **liên quan bước đang làm** và trả lời được trong 30 giây: trả lời ngay.
- Câu **hay nhưng dài**: "Câu này hay, mình ghi vào parking lot, trả lời ở phần Q&A." Nhờ co-host ghi.
- Câu **không biết**: không đoán. Ghi tên người hỏi, hứa gửi trong nhóm chat trong ngày (kịch bản,
  phần Q&A).
- Không để một người kéo cả lớp vào tranh luận quá 2 phút.

### Quy tắc giữ giờ

- Đồng hồ trong tầm mắt; co-host (nếu có) báo khi trễ quá 3 phút so với bảng lịch trình.
- **10:23 CHECKPOINT A:** chưa xong → nhảy `step-05`. Trễ thì rút giờ nghỉ còn 2 phút (kịch bản).
- **10:44 CHECKPOINT B:** chưa xong → nhảy `step-07`. Trễ quá 2 phút → bỏ bước 409 (Phụ lục A #1).
- **11:05 CHECKPOINT C:** chưa xong → nhảy `step-10`. Vẫn trễ → cắt dần Phụ lục A #5–#7 (bỏ test đỏ,
  rồi thay gõ test bằng mở file từ tag `step-12`).
- **11:13:** integration test chưa xanh → mở cửa sổ dự phòng, giải thích.
- **11:15: bắt đầu tổng kết, dù demo đang ở đâu.** Đây là giờ cứng.
- **11:27: chuyển sang kết thúc**, Q&A có thể rút còn 5 phút.
- Danh sách cắt dùng đúng **Phụ lục A** của kịch bản, cắt từ trên xuống. Các phần "không cắt" ghi
  ở cuối Phụ lục A.

## 6. Kế hoạch tập dượt

Ghi ngày buổi học: `____/____/2026` (hôm nay là 26/09/2026). D-3 = ba ngày trước buổi học. Ngày giờ
trong các `timestamp` ví dụ của kịch bản chỉ là mẫu.

### D-3 — Dựng máy và tập lần 1

- [ ] Làm toàn bộ mục "Tối hôm trước" #1–#8 của kịch bản **ngay hôm nay** (import, test xanh trên
      `solution`, về `main`, 12 tag, worktree dự phòng, 2 run config, font, exclude import, bộ gõ,
      chạy app/test trên mạng công ty). Tối D-1 chỉ còn kiểm tra lại.
- [ ] Đọc `01-study-guide-vi.md` phần nền (DI, request flow, transaction).
- [ ] **Tập lần 1, đầy đủ, bấm giờ**, một mình, nói thành tiếng như có lớp. Ghi giờ thực tế vào bảng
      bên dưới. Không dừng lại sửa kịch bản — ghi chú rồi đi tiếp.
- [ ] Tự ghi hình lần tập (Zoom record một cuộc họp chỉ có mình, share màn hình). Xem lại ở tốc độ
      1.5x: chỗ nào nói "ờ" nhiều, chỗ nào chữ nhỏ, chỗ nào im lặng quá lâu khi gõ.
- [ ] Reset sau khi tập: dừng app, `git stash -u`, `git checkout main`, `git status` sạch.

### D-2 — Tập hỗn loạn (chaos rehearsal) và tập lần 2

**Chaos rehearsal** — cố tình gây từng lỗi, bấm giờ phần xử lý. Mục tiêu: sửa < 1 phút, hoặc quyết
định nhảy tag < 2 phút.

| # | Cố tình làm | Phải thấy | Tập xử lý | Mục tiêu |
|---|---|---|---|---|
| 1 | Import `org.springframework.data.annotation.Id` (bỏ exclude tạm thời) | App không khởi động, "no identifier" | Tìm `Caused by:`, sửa import, `Ctrl+F5` | 1 phút |
| 2 | Import `jakarta.transaction.Transactional`, rồi viết `readOnly = true` | Lỗi compile | Sửa import | 30 giây |
| 3 | Sửa handler xong **không** restart, gửi #2 | Body mặc định | Nhận ra qua body, restart | 30 giây |
| 4 | Thêm `getProducts` không restart, gửi #5 | 405 | Như trên | 30 giây |
| 5 | Restart rồi gửi #3 ngay | 404 `PRODUCT_NOT_FOUND` | Nói câu H2, gửi #1 rồi #3 | 30 giây |
| 6 | Chạy cửa sổ dự phòng ở 8080 (hoặc chạy `ProductApplication` rồi chạy thêm `ProductApplication (prod)`), rồi chạy app chính | `Port 8080 was already in use` | Stop hết; thử cả `netstat`/`taskkill` | 1 phút |
| 7 | Thêm khối `spring:` thứ hai vào `application.yml` | App không khởi động (duplicate key) | `Ctrl+Z`, lồng đúng | 1 phút |
| 8 | Để bộ gõ ở Telex, gõ `isActive` | Chữ có dấu | Chuyển E, gõ lại | 30 giây |
| 9 | Chạy `seed.http` hai lần | Seed ra 500 | Restart, seed một lần | 1 phút |
| 10 | Để code gõ dở rồi `git checkout step-07-get-by-id` **không** stash | Git từ chối checkout | `git stash -u`, checkout lại, restart, #1 | 2 phút |
| 11 | Giả vờ IntelliJ hỏng | — | Chuyển cửa sổ dự phòng, chạy, giải thích bằng code có sẵn | 2 phút |

Sau chaos rehearsal: xóa code thử, bật lại exclude import, `git stash -u`, `git checkout main`.

- [ ] **Tập lần 2, đầy đủ, bấm giờ**, lần này trên Zoom thật với một đồng nghiệp: kiểm tra đọc được
      code, nghe rõ, thấy được popup. Nhờ họ hỏi 3 câu khó lấy từ `02-qa-bank-vi.md` vào lúc bất
      ngờ, và nhắn "mạng mình rớt rồi" một lần để tập câu nói ở mục 7.
- [ ] So sánh bảng giờ hai lần tập. Phần nào trễ > 2 phút cả hai lần → quyết định trước sẽ cắt gì
      (theo Phụ lục A), đừng để lúc dạy mới quyết.

### D-1 — Tối hôm trước

- [ ] Làm lại checklist "Tối hôm trước" của kịch bản trên đúng máy, đúng mạng (xác nhận, không làm
      mới).
- [ ] Tập nhanh 3 đoạn khó nhất (~25 phút): chạy lần đầu → CHECKPOINT A; `GET /{id}` sau restart;
      profile `dev`/`prod`.
- [ ] Tập một lần nhảy tag bằng tay: `git stash -u`, `git checkout step-07-get-by-id`, chạy, #1, #3.
- [ ] Chuẩn bị sẵn để dán vào chat: link repo (hiện **chưa có remote** — quyết định đẩy lên
      GitLab/GitHub nội bộ hay gửi zip; nếu đẩy, chỉ đẩy `main`, `solution` và các tag `step-*`,
      đừng `git push --tags` vì repo còn tag `backup/*`), link form feedback, nội dung Phụ lục B.
- [ ] Reset: `git status` sạch, đang ở `main`. Sạc laptop, điện thoại (hotspot), tai nghe.
- [ ] Đọc lại mục 2 và mục 7 của tài liệu này. Rồi đi ngủ — không tập thêm.

### Ngày dạy

- [ ] Trước 09:10: bật máy, mở IntelliJ để index xong, thoát app không cần thiết, bật Do not disturb.
- [ ] 09:10 và 09:25: làm đúng checklist của kịch bản (Zoom, đọc được code, port trống, bộ gõ E,
      chạy thử rồi dừng, `git status`, cửa sổ dự phòng, file mở sẵn, Terminal mở sẵn).
- [ ] Thêm: Zoom đang share **đúng** màn hình; điện thoại có Zoom để vào lại nếu laptop rớt mạng;
      bảng lịch trình và mục 7 ở tầm mắt.

### Bảng bấm giờ (điền khi tập)

| Giờ (kế hoạch) | Phần | Dự kiến | Lần 1 | Lần 2 | Ghi chú / quyết định cắt |
|---|---|---|---|---|---|
| 09:30 | Mở đầu, slide 1–2 | 5' | | | |
| 09:35 | Slide 3–4 | 3' | | | |
| 09:38 | Slide 5 + chạy thử starter | 5' | | | |
| 09:43 | DI (slide 6) | 4' | | | |
| 09:47 | Package + demo project (slide 7–8) | 3' | | | |
| 09:50 | step-01 Entity + slide 9 | 6' | | | |
| 09:56 | step-02 DTO | 5' | | | |
| 10:01 | step-03 Repository | 3' | | | |
| 10:04 | step-04 Service | 7' | | | |
| 10:11 | Controller `POST` | 5' | | | |
| 10:16 | Chạy lần đầu → CHECKPOINT A | 7' | | | |
| 10:23 | Nghỉ / buffer #1 | 5' | | | |
| 10:28 | step-06 Exception handling | 9' | | | |
| 10:37 | step-07 `GET /{id}` → CHECKPOINT B | 7' | | | |
| 10:44 | step-08 Pagination | 10' | | | |
| 10:54 | step-09 409 (tùy chọn) / buffer #2 | 3' | | | |
| 10:57 | step-10 Profiles → CHECKPOINT C | 8' | | | |
| 11:05 | Giới thiệu testing | 1' | | | |
| 11:06 | step-11 Unit test | 5' | | | |
| 11:11 | step-12 Integration test | 4' | | | |
| 11:15 | Tổng kết (slide 4, 14, 15) | 4' | | | |
| 11:19 | Q&A | 8' | | | |
| 11:27 | Kết thúc | 3' | | | |

## 7. Câu nói chuẩn bị sẵn

**Lỗi compile:**

> "À, có lỗi compile — mình đọc to cho mọi người: … Đây là loại lỗi rất hay gặp, sửa thế này."
>
> (Quá 2 phút) "Mình không để lỗi này ăn giờ của mọi người. Mình lấy bản đã chuẩn bị ở tag
> `step-…` — giống hệt những gì mình định gõ."

**App không khởi động:**

> "App không lên. Khi gặp chuyện này, mình không đọc từ trên xuống — mình cuộn tìm dòng
> `Caused by:` cuối cùng hoặc khối `APPLICATION FAILED TO START`. Đây: …"
>
> (Nếu là lỗi đã dự đoán) "Đúng cái mình vừa nói — Spring kiểm tra lúc khởi động, nên lỗi hiện sớm."

**Kết quả khác dự đoán (quên restart, H2 trống):**

> "Kết quả này là do mình chưa restart — không có devtools thì phải restart sau mỗi lần sửa code."
>
> "404 là đúng: H2 nằm trong bộ nhớ, restart là mất dữ liệu. Mình tạo lại sản phẩm."

**Không biết câu trả lời:**

> "Câu này mình không muốn trả lời sai. Mình ghi lại tên bạn và sẽ gửi câu trả lời trong nhóm chat
> ngay trong hôm nay."
>
> "Câu này đi ra ngoài phạm vi hôm nay — mình để vào parking lot, cuối buổi còn giờ thì mình quay lại."

**Đang trễ giờ:**

> "Để giữ đúng giờ cho phần test, phần này mình để làm bài tập — code có sẵn ở tag `step-…`."
>
> (11:15) "Mình dừng demo ở đây để tổng kết đúng giờ. Phần còn lại có đầy đủ trong repo."

**Mạng/Zoom rớt:**

> (Khi vào lại) "Xin lỗi mọi người, mạng mình vừa rớt. Mình quay lại checkpoint gần nhất — tag
> `step-…` — và đi tiếp theo lịch."
>
> (Nhờ co-host nói trước khi mình vào lại) "Người trình bày đang kết nối lại, mọi người đợi 1–2 phút
> nhé; ai có câu hỏi cứ gõ vào chat."
