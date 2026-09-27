# Java Spring Boot — Kịch bản Live Demo
## Buổi sharing nội bộ 2 giờ · 09:30–11:30 · Zoom

Kịch bản này dành cho người trình bày. Lời nói bằng tiếng Việt; code, annotation, log và thuật ngữ kỹ
thuật giữ nguyên tiếng Anh để khớp slide và source code.

**Giọng nói của buổi này:** đây là buổi chia sẻ giữa đồng nghiệp trong công ty. Người nghe là anh chị em đang làm dự án, nhiều người có kinh nghiệm thực tế. Nói như đang ngồi
review code cùng team: "mình", "mọi người", kể chuyện hay gặp trong dự án, hỏi lại người nghe. Các
đoạn **[NÓI]** là gợi ý ý chính, không phải bài để đọc thuộc. Mỗi slide có dòng **Trên slide** liệt
kê đúng những gì slide ghi, theo thứ tự trên slide, để lời nói bám sát slide.

Tài liệu đi kèm (slide/DOC là nguồn chuẩn; tài liệu `trainer-prep` cần đối chiếu riêng):

| Tài liệu | Dùng để |
|---|---|
| `Java Spring Boot.pptx` (16 slide) | Các mốc `[Slide N]` trong kịch bản cho biết lúc nào chuyển slide |
| `product-api-starter` — branch `main` | Project khung để live coding (đã có `pom.xml`, `application.yml`, `seed.http`, `demo-requests.http`) |
| `product-api-starter` — tag `step-01-entity` … `step-12-integration-test` | Mỗi bước live coding là một tag. Bị kẹt thì nhảy tới tag (xem [Kế hoạch dự phòng](#ke-hoach-du-phong)) |
| `product-api-starter` — branch `solution` | Bản hoàn chỉnh, kèm thêm test cho các nhánh lỗi |
| `trainer-prep/01-study-guide-vi.md` | Ôn kiến thức nền: giải thích thế nào, bên dưới chạy thế nào, câu hỏi follow-up |
| `trainer-prep/02-qa-bank-vi.md` | Kho câu hỏi người nghe hay hỏi, kèm câu trả lời |
| `trainer-prep/03-risks-and-rehearsal-vi.md` | Rủi ro khi demo live, kế hoạch tập dượt |
| `trainer-prep/04-cheat-sheet-vi.md` | Bản ngắn để mở cạnh màn hình lúc chia sẻ |

Phạm vi bám theo nội dung đã duyệt (slide và tài liệu `Java Spring Boot Training.doc`): kiến trúc Spring Boot và Dependency Injection; REST API với
DTO và validation; xử lý lỗi tập trung; Spring Data JPA; pagination, sorting và repository pattern; cấu
hình và Spring Profiles; unit test và integration test. Cả buổi sử dụng **một** ví dụ: Product API.

DOC định hướng **30% theory / 70% live coding and demonstrations**. Giữ phần slide ngắn,
ưu tiên code và demo; đây là tỉ lệ định hướng, cần bấm giờ khi rehearsal để kiểm tra thực tế.
Prerequisite trong DOC là Basic Java và familiarity with OOP concepts; không thêm nội dung nâng cao vào lời nói chính.

## Ký hiệu trong kịch bản

| Ký hiệu | Nghĩa |
|---|---|
| **[NÓI]** | Lời nói. Không cần đọc nguyên văn — nói theo ý của mình |
| **[GÕ]** | Gõ tay live. Đây là phần người xem cần nhìn thấy từng dòng |
| **[DÁN]** | Dán từ khối code trong kịch bản (mở kịch bản ở màn hình phụ). Dùng cho phần dài, nhàm |
| **[SINH]** | Dùng IntelliJ generate (`Alt+Insert`) — ví dụ getter/setter, constructor |
| **[MỞ]** | Mở file có sẵn hoặc chiếu slide |
| **[CHẠY]** | Chạy app, gửi request, hoặc chạy test |
| **[DỰ ĐOÁN]** | Hỏi mọi người đoán kết quả **trước khi** chạy. Mọi người trả lời trong chat Zoom |
| **Trên slide** | Những gì slide ghi, theo đúng thứ tự. Lời nói đi lần lượt qua từng ý này |
| **[CHECKPOINT]** | Mốc thời gian cứng. Nếu code chưa chạy được thì nhảy tới tag, không sửa tiếp |

## Ghi chú cho người trình bày — cách nói và nhịp trình bày

- **Nói trọn ý:** mỗi câu cần cho người nghe biết mình đang nói về điều gì và vì sao điều đó liên quan đến demo. Tránh đọc cụm từ rời như “cùng code, nhiều môi trường”.
- **Dùng tiếng Anh làm mốc:** chỉ vào cụm từ trên slide rồi giải thích bằng một câu tiếng Việt tự nhiên. Không cần đọc cả dòng tiếng Anh rồi dịch lại từng chữ. Vẫn đi đủ các ý theo thứ tự.
- **Chuyển phần có dẫn dắt:** nhắc ngắn kết quả vừa có, rồi giới thiệu việc tiếp theo. Ví dụ: “Mình đã tạo được sản phẩm. Tiếp theo, mình sẽ xử lý trường hợp dữ liệu gửi lên chưa hợp lệ.”
- **Tương tác nhẹ nhàng:** mỗi lần hỏi một câu rõ ràng, mời trả lời thay vì kiểm tra kiến thức. Ghi nhận ý kiến bằng nội dung cụ thể: “Cảm ơn anh/chị, ý về response size đúng với tình huống này.” Nếu ý kiến chưa khớp, cùng đối chiếu với request hoặc code; tránh phán xét người trả lời.
- **Giữ nhịp trên Zoom:** dừng một nhịp sau khi chuyển slide hoặc mở file; chỉ đúng dòng đang nói. Khi gõ phần dài, nói trước mình sẽ thêm gì, gõ xong rồi giải thích, tránh vừa gõ vừa nói liên tục.
- **Xử lý sự cố bình tĩnh:** mô tả điều vừa quan sát, nói bước kiểm tra tiếp theo. Nếu cần dùng checkpoint, giải thích ngắn lý do và nhắc mọi người đang ở bước nào.
- **Giữ thời lượng:** lời nói là câu hoàn chỉnh để dễ tập trình bày; khi rehearsal, bỏ các câu chuyển ý bị lặp trước khi cắt nội dung slide. Không kéo dài bằng cách đọc cả dòng “Trên slide” lẫn toàn bộ phần giải thích.

Nguyên tắc cho cả buổi:

- **Một ý → một demo → một câu dự đoán.** Đừng giải thích quá 2 phút mà không cho mọi người thấy code
  chạy.
- **Để mọi người chọn cách theo dõi.** Có thể xem hoặc gõ cùng; giới thiệu tag như một cách đồng bộ code, tránh gọi người nghe là “bị chậm”.
- **Nói như đang review code cùng đồng nghiệp.** Giải thích lựa chọn và lý do, mời mọi người bổ sung. Chỉ dùng “team mình từng…” khi đó là trải nghiệm thật của người trình bày.
- **Lỗi khi gõ là bình thường.** Đọc to lỗi compile, sửa trong 1 phút. Quá 2 phút → nhảy tag, nói
  "Mình chuyển sang bản đã chuẩn bị để mọi người tiếp tục theo dõi luồng xử lý nhé".
- **Cho mọi người thời gian suy nghĩ.** Sau câu hỏi, dừng khoảng 5–10 giây. Nếu chat chưa có phản hồi, nói “Mình cùng chạy thử để đối chiếu nhé” rồi tiếp tục; không nhận xét về việc mọi người im lặng.
- **Không biết thì nói không biết.** "Câu này mình ghi lại, trả lời sau buổi" là câu trả lời tốt.

---

# Lịch trình

Giờ là giờ thật của buổi sharing (09:30 bắt đầu). Cột "+" là thời gian đã trôi qua.

| Giờ | + | Phần | Slide | Tag khi xong | Ưu tiên |
|---|---|---|---|---|---|
| 09:10–09:30 | — | Chuẩn bị trên Zoom (không tính giờ sharing) | — | — | bắt buộc |
| 09:30–09:35 | 0:00 | Mở đầu, nội quy, khảo sát nhanh | 1–2 | — | bắt buộc |
| 09:35–09:38 | 0:05 | Mục tiêu, bản đồ request flow | 3–4 | — | bắt buộc |
| 09:38–09:43 | 0:08 | Spring Boot fundamentals + start.spring.io (Explore) + chạy thử starter | 5 | — | bắt buộc |
| 09:43–09:47 | 0:13 | Dependency Injection | 6 | — | bắt buộc |
| 09:47–09:50 | 0:17 | Package structure + demo project | 7–8 | — | bắt buộc |
| 09:50–09:56 | 0:20 | Entity `Product` + chuyển ý slide 9 | 9 | `step-01-entity` | bắt buộc |
| 09:56–10:01 | 0:26 | Request DTO + Response DTO | 9 | `step-02-dtos` | bắt buộc |
| 10:01–10:04 | 0:31 | Repository | — | `step-03-repository` | bắt buộc |
| 10:04–10:11 | 0:34 | Service `create` | (6) | `step-04-service` | bắt buộc |
| 10:11–10:16 | 0:41 | Controller `POST` | — | — | bắt buộc |
| 10:16–10:23 | 0:46 | Chạy lần đầu: 201, rồi 400 mặc định | — | `step-05-post-endpoint` | **CHECKPOINT A** |
| 10:23–10:28 | 0:53 | Nghỉ 5 phút / buffer #1 | — | — | nên có |
| 10:28–10:37 | 0:58 | Global exception handling (400) | 10 | `step-06-error-handling` | bắt buộc |
| 10:37–10:44 | 1:07 | `GET /{id}` + 404 | 10 | `step-07-get-by-id` | **CHECKPOINT B** |
| 10:44–10:54 | 1:14 | Pagination và sorting | 11 | `step-08-pagination` | bắt buộc |
| 10:54–10:57 | 1:24 | *Tùy chọn:* SKU trùng → 409 / buffer #2 | — | `step-09-duplicate-sku` | cắt đầu tiên |
| 10:57–11:05 | 1:27 | Configuration + Spring Profiles | 12 | `step-10-profiles` | **CHECKPOINT C** |
| 11:05–11:06 | 1:35 | Giới thiệu testing | 13 | — | nên có |
| 11:06–11:11 | 1:36 | Unit test (Mockito) | 13 | `step-11-unit-test` | bắt buộc |
| 11:11–11:15 | 1:41 | Integration test (MockMvc) | 13 | `step-12-integration-test` | bắt buộc |
| 11:15–11:19 | 1:45 | Tổng kết | 4, 14, 15 | — | **bắt đầu đúng 11:15** |
| 11:19–11:27 | 1:49 | Q&A | 16 | — | có thể rút còn 5 phút |
| 11:27–11:30 | 1:57 | Kết thúc: tài liệu, bài tập, feedback | 16 | — | bắt buộc |

Buffer có sẵn: giờ nghỉ 10:23 (5 phút), khối tùy chọn 10:54 (3 phút), các phần "tùy chọn" trong từng
mục (~8 phút), Q&A co lại từ 8 xuống 5 phút. Dù demo đang ở đâu, **11:15 luôn chuyển sang tổng kết**.

---

# Trước buổi sharing

Ghi chú review tĩnh: các lệnh build/run/test dưới đây là checklist cho người trình bày, chưa được chạy trong lần review này. Dependency/API Boot 4.1.1, log, SQL, JSON mặc định, locale của validation và phím tắt/keymap IntelliJ **cần verify khi build** và rehearsal trên máy demo. Không coi các output minh họa là log đã đo.

## Tối hôm trước (bắt buộc — làm trên đúng máy sẽ trình bày)

1. **Import và build project một lần.** Nếu máy chưa có các artifact của Spring Boot 4.1.1 trong `~/.m2`,
   lần import đầu tiên sẽ cần tải dependency. Đừng để việc này xảy ra lúc đang chia sẻ.
   - IntelliJ: *File → Open* → thư mục `product-api-starter` → đợi Maven import xong (thanh tiến trình
     góc dưới biến mất).
   - Checkout `solution` (`git checkout solution`), mở tab Maven → *Lifecycle → test*. Tất cả test phải
     xanh. Cảnh báo về Mockito/Java agent là bình thường (xem mục Unit test).
   - Chạy `ProductApplication`, gửi hết `seed.http` và `demo-requests.http` một lượt, đối chiếu với
     phần "Kết quả mong đợi" trong kịch bản.
   - Quay về `main`: `git checkout main`. Kiểm tra `git status` sạch.
2. **Kiểm tra tag:** `git tag -l "step-*"` phải ra đủ 12 tag.
3. **Tạo cửa sổ dự phòng** (bản hoàn chỉnh, mở ở cửa sổ IntelliJ thứ hai):

   ```powershell
   git worktree add ..\product-api-solution solution
   ```

   Mở `..\product-api-solution` bằng *File → Open → New Window*. Cửa sổ này chỉ để **xem** code. Nếu
   cần chạy cả hai cùng lúc, chạy bản dự phòng ở port khác (environment variable `SERVER_PORT=8081`).
4. **Tạo sẵn 2 run configuration** trong IntelliJ (*Run → Edit Configurations…*):
   - `ProductApplication` — mặc định, không set gì (sẽ chạy profile `dev` sau bước 10).
   - `ProductApplication (prod)` — copy cái trên, ô *Active profiles* điền `prod`. (Nếu không thấy ô
     này: *Modify options → Add VM options* → `-Dspring.profiles.active=prod`.)
5. **Màn hình cho Zoom:** nếu font editor đang là 12 thì tăng lên để dễ nhìn. *Settings → Editor → Font* = 20,
   *Console Font* = 16, bật *Change font size with Ctrl+Mouse Wheel*. Nên dùng chế độ thường thay vì
   Presentation Mode (Presentation Mode ẩn cửa sổ Run/Build — phải bấm `Alt+4` mới thấy log). Share
   **cả màn hình** chứ không share một cửa sổ, để popup và Postman (nếu dùng) cũng hiện. Tắt thông báo
   Windows/Teams/Outlook. Tắt gợi ý code AI/inline completion để không hiện code lạ khi gõ.
6. **Chặn import sai ngay từ đầu.** *Settings → Editor → General → Auto Import → Exclude from auto-import
   and completion*, thêm:
   `java.awt.print.Pageable`, `org.hibernate.query.Page`, `jakarta.transaction.Transactional`,
   `org.springframework.data.annotation.Id`, `org.springframework.test.util.AssertionErrors`,
   `org.springframework.test.web.client.match.MockRestRequestMatchers`,
   `org.apache.tomcat.util.http.parser.MediaType`.
   Đây là các tên dễ chọn nhầm; tên nào còn có trên classpath cần verify khi build. Chọn sai có thể gây lỗi compile hoặc lỗi lúc start app. Bảng import đúng ở [Phụ lục E](#phu-luc-e).
7. **Bộ gõ tiếng Việt:** Unikey/EVKey ở chế độ Telex sẽ biến `isActive` thành `íActive`, `desc` thành
   `dếc`… Thêm `idea64.exe` vào danh sách loại trừ của bộ gõ, hoặc tập thói quen chuyển sang **E**
   trước khi gõ code (vẫn gõ tiếng Việt được trong chat Zoom).
8. **Chạy một lần mọi thứ sẽ chạy trong buổi**, trên mạng công ty: chạy app (Windows Firewall có thể hỏi
   quyền — chọn *Cancel* cũng được, localhost vẫn chạy), chạy test một lần (IntelliJ có thể tải thêm
   `junit-platform-launcher` ở lần chạy test đầu tiên). Mở `https://start.spring.io` trên mạng công ty
   (kiểm tra proxy), điền đúng các field ở slide 5 rồi bấm *EXPLORE* một lần. Đối chiếu `pom.xml` và các
   điểm khác với repo trong [ghi chú start.spring.io](#ghi-chu-initializr); ghi lại bản 4.1.x đang có.
9. **Link repo cho mọi người:** kiểm tra remote và cách truy cập repo trước buổi. Quyết định trước cách chia sẻ (đẩy lên
   GitLab/GitHub nội bộ của công ty, hoặc gửi file zip sau buổi) và chuẩn bị sẵn link để dán vào chat.
10. **Đọc lại** `trainer-prep/03-risks-and-rehearsal-vi.md` và tập một lượt có bấm giờ.

## 09:10 — 20 phút trước giờ sharing

- Vào Zoom sớm. Nếu có người hỗ trợ (co-host): nhờ họ canh giờ theo bảng lịch trình, gom câu hỏi
  trong chat vào "parking lot", và báo khi màn hình bị mờ/chữ nhỏ.
- Share màn hình, nhờ một người xác nhận **đọc được code**.
- Port 8080 đang trống (không có app nào khác đang chạy).
- Bộ gõ tiếng Việt: IntelliJ đang ở chế độ **E**.
- Chạy thử `ProductApplication` trên `main` một lần cho JVM/IDE "ấm", rồi **dừng lại** (`Ctrl+F2`).
- `git status` sạch, đang ở `main`.
- Cửa sổ dự phòng (`solution`) đã mở, thu nhỏ.
- Mở sẵn: slide (chế độ trình chiếu), `demo-requests.http`, `seed.http`, kịch bản này ở màn hình phụ.
- Mở sẵn tab Terminal trong IntelliJ (PowerShell) ở thư mục project, để nhảy tag khi cần.
- Mở sẵn tab browser `https://start.spring.io`, zoom browser 125–150% để chữ trên form đọc được qua Zoom.

## 09:25 — 5 phút trước giờ sharing

- Tắt âm thanh thông báo. Nước uống. Đồng hồ trong tầm mắt.
- Slide 1 đang chiếu.

<a id="ke-hoach-du-phong"></a>

## Kế hoạch dự phòng

| Tình huống | Làm gì |
|---|---|
| Lỗi compile, không thấy nguyên nhân trong 2 phút | Nhảy tag của bước đó (lệnh bên dưới) |
| Tới CHECKPOINT mà API chưa chạy đúng | Nhảy tag của checkpoint: A = `step-05-post-endpoint`, B = `step-07-get-by-id`, C = `step-10-profiles` |
| `Port 8080 was already in use` | Có app cũ đang chạy: bấm Stop (ô vuông đỏ) ở mọi tab Run, hoặc chạy lại. Vẫn lỗi thì dùng run config với `SERVER_PORT=8081` và sửa `@host` trong file `.http` |
| Request trả 404 mà không có log gì | Kiểm tra URL: dấu `/` thừa ở cuối, sai chính tả, quên restart app sau khi thêm endpoint |
| Request trả `415 Unsupported Media Type` | Thiếu header `Content-Type: application/json` |
| `GET /api/products/1` trả 404 sau khi restart | Bình thường: H2 chạy in-memory, restart là mất dữ liệu. Chạy lại request #1 hoặc `seed.http` |
| IntelliJ/Maven hỏng hẳn | Chuyển sang cửa sổ dự phòng (`solution`), chạy ở đó, giải thích bằng code có sẵn |
| Zoom/mạng rớt | Vào lại, nói "mình quay lại checkpoint gần nhất", nhảy tag, tiếp tục theo lịch trình |
| start.spring.io không vào được hoặc quá chậm | Bỏ qua. Mở `pom.xml` trong starter và nói "Project kiểu này tạo từ start.spring.io; các starter ở đây là các dependency mình chọn" |
| Initializr không còn bản 4.1.x hoặc mặc định là bản khác | Chọn bản 4.1 mới nhất. Nếu chỉ khác số patch thì nói "repo dùng 4.1.1, patch trên Initializr có thể mới hơn" |

Nhảy tới một tag (Terminal trong IntelliJ, **dừng app trước**):

```powershell
git stash -u
git checkout step-07-get-by-id
```

- `git stash -u` cất toàn bộ thay đổi đang gõ dở (kể cả file mới). Không mất gì; sau buổi có thể lấy
  lại bằng `git stash pop`.
- Git in ra thông báo *detached HEAD* — bình thường, cứ tiếp tục gõ trên đó.
- Sau khi nhảy tag: đợi IntelliJ tải lại file, chạy lại app, gửi lại `seed.http` nếu cần dữ liệu.
- Nói với mọi người: "Mình chuyển sang code đã chuẩn bị ở bước này để giữ thời gian cho các phần tiếp theo. Mình sẽ chỉ lại phần vừa hoàn thành để mọi người tiện theo dõi."

---

# 09:30–09:35 — Mở đầu

## [Slide 1] Java Spring Boot — From Controller to Database

**Trên slide:** *Java Spring Boot* · *From Controller to Database — building a production-ready REST API, one project at a time.* · logo TMA · `Spring Boot 4.x` · `Java 21` · `REST · JPA · Testing`.

**[NÓI]** — trong lúc chờ mọi người vào đủ (khoảng 1 phút sau 09:30 thì bắt đầu):

> Chào mọi người, mình là Đạt, đến từ DC 42. Cảm ơn mọi người đã dành thời gian tham gia buổi Java Spring Boot hôm nay.
>
> Chủ đề của buổi này là *From Controller to Database*. Mình sẽ cùng mọi người xây dựng một Product API và theo dõi một request đi qua các bước, từ lúc API tiếp nhận cho đến khi dữ liệu được lưu vào database. Trong quá trình đó, mình sẽ chia sẻ một số cách tổ chức code để API rõ ràng và dễ bảo trì hơn.
>
> Buổi này mình sẽ dành phần lớn thời gian để code và chạy demo. Mọi người cứ thoải mái đặt câu hỏi bất cứ lúc nào nhé. Mình cũng rất muốn nghe thêm kinh nghiệm và cách làm của mọi người.

Ghi chú cho người trình bày: chỉ kể trải nghiệm của team nếu đó là chuyện mình thực sự đã gặp.
Nếu có ghi hình, thông báo và gửi link theo cách đã thống nhất với mọi người.

**[DÁN]** link repo vào chat (nếu đã có).

**[DỰ ĐOÁN]** — khảo sát nhanh trong chat:

> Trước khi bắt đầu, mình xin hỏi nhanh để điều chỉnh phần trình bày cho phù hợp với mọi người. Mọi
> người có thể gửi một con số vào chat: **0** nếu chưa dùng Spring Boot, **1** nếu đã thử qua
> tutorial, hoặc **2** nếu đang dùng trong dự án. Không cần chuẩn bị gì thêm; thông tin này chỉ giúp
> mình chọn nhịp trình bày phù hợp.

Nhìn kết quả để điều chỉnh: nhiều số 0 → nói chậm ở DI và annotation; nhiều số 2 → đi nhanh phần
nền, dành thời gian cho các "góc khuất" (thứ tự lỗi validation, `Page` JSON, profile). Đọc to vài
kết quả: "Cảm ơn mọi người. Nhiều anh chị em đã dùng Spring Boot trong dự án, nên mình sẽ đi gọn phần nền và dành thêm thời gian cho demo."

## [Slide 2] Today's Session — One project, built step by step

**Trên slide:** *Today's Session* · *One project, built step by step.* · (1) *Spring Boot Fundamentals & Dependency Injection* · (2) *Building REST APIs (DTOs, validation, error handling)* · (3) *Spring Data JPA, Service Layer, Pagination & Sorting* · (4) *Configuration Management & Spring Profiles* · (5) *Unit & Integration Testing, Wrap-up*.

**[NÓI]**

> Về nội dung hôm nay, chúng ta sẽ
> có **5 phần**, cùng xoay quanh Product API này:
>
> 1. Với **Spring Boot Fundamentals & Dependency Injection**, mình sẽ cùng xem Spring Boot hỗ trợ
>    những gì khi xây dựng ứng dụng, và cách Spring cung cấp dependency cho các thành phần qua Dependency
>    Injection.
> 2. Sang **Building REST APIs (DTOs, validation, error handling)**, mình sẽ xây dựng endpoint, dùng
>    DTO để nhận và trả dữ liệu, kiểm tra input và xử lý lỗi.
> 3. Ở phần **Spring Data JPA, Service Layer, Pagination & Sorting**, mình sẽ đưa business logic vào
>    service, kết nối với database và lấy danh sách có pagination, sorting.
> 4. Tiếp theo là **Configuration Management & Spring Profiles**. Mình sẽ xem cách dùng cấu hình phù
>    hợp khi cùng một ứng dụng chạy ở các môi trường khác nhau.
> 5. Cuối cùng, với **Unit & Integration Testing, Wrap-up**, mình sẽ viết hai loại test để kiểm tra
>    API hoạt động đúng như mong đợi. Sau đó, mình sẽ tổng kết những ý chính.
>
> Mình dùng một project từ đầu đến cuối để mọi người dễ theo dõi và thấy các phần liên kết với nhau như thế nào.
>

---

# 09:35–09:38 — Mục tiêu và bản đồ

## [Slide 3] Learning Objectives

**Trên slide:** *Learning Objectives* · *By the end of this session, you'll be able to:* · *Explain architecture* — Understand Spring Boot’s request flow and how dependency injection wires it together. · *Build REST APIs* — Define boundaries using DTOs and input validation. · *Handle errors* — Implement consistent API error contracts with `@RestControllerAdvice`. · *Access data* — Map entities and manage persistence using Spring Data JPA repositories. · *Apply pagination* — Safely query collections with Spring Data sorting and pageables. · *Manage config* — Handle environment-specific configurations via external config and Spring Profiles.

**[NÓI]** — khoảng 1 phút, mỗi ô một câu, chỉ lần lượt từ trái sang phải, trên xuống dưới:

> Với **Learning Objectives**, mình mong sau buổi này mọi người có thể áp dụng được sáu nội
> dung sau:
>
> - **Explain architecture**: giải thích được request đi qua những thành phần nào và Dependency
>   Injection kết nối các thành phần đó ra sao.
> - **Build REST APIs**: dùng DTO để xác định API contract và validate dữ liệu đầu vào.
> - **Handle errors**: tổ chức xử lý lỗi bằng `@RestControllerAdvice`, để client nhận được error
>   contract nhất quán.
> - **Access data**: map entity và thao tác với database qua Spring Data JPA repository.
> - **Apply pagination**: lấy danh sách có giới hạn, kết hợp sorting và `Pageable`.
> - **Manage config**: sử dụng external config và Spring Profiles cho từng môi trường.
>
> Trong phần demo, mình sẽ lần lượt gắn từng nội dung này với code cụ thể để mọi người dễ đối chiếu.

## [Slide 4] Architecture — The request flow

**Trên slide:** *Architecture* · *The request flow*: `[ Client ]` → `DTO + Validation` → `[ Controller ]` → `[ Service ]` → `[ Repository ]` → `[ Database ]`. Nhánh lỗi từ Controller / Service: `[ Exception ]` → `[ @RestControllerAdvice ]` → `[ Standard Error ]`.

**[NÓI]**

> Mình bắt đầu với **Architecture — The request flow**. Sơ đồ này giúp mình hình dung toàn bộ đường đi
> của một request trước khi vào code.
>
> Từ trái sang, **Client** gửi dữ liệu vào **DTO + Validation**. Sau khi input hợp lệ, **Controller**
> tiếp nhận request và gọi **Service**. Service xử lý nghiệp vụ, gọi **Repository** để làm việc với
> **Database**.
>
> Ở nhánh bên dưới, khi gặp trường hợp như dữ liệu không hợp lệ hoặc sản phẩm không tồn tại,
> **Exception** tương ứng sẽ được xử lý qua **@RestControllerAdvice** nếu đã có handler. Client nhận
> lại **Standard Error** theo format quy định. Demo sẽ bắt đầu với validation.
>
> Mình sẽ quay lại sơ đồ này sau khi có API chạy được, để cùng đối chiếu từng thành phần với code.

Ghi chú cho người trình bày — mỗi ô trên slide ứng với class nào trong code (nói nếu có người hỏi):

| Ô trên slide | Class sẽ viết |
|---|---|
| DTO + Validation | `CreateProductRequest`, `ProductResponse` |
| Controller | `ProductController` |
| Service | `ProductService` |
| Repository | `ProductRepository` |
| Database | bảng `products` trong H2 (entity `Product`) |
| Exception | `ProductNotFoundException`, `DuplicateSkuException` |
| @RestControllerAdvice | `GlobalExceptionHandler` |
| Standard Error | `ApiError` |

---

# 09:38–09:43 — Spring Boot fundamentals

## [Slide 5] Spring Boot Fundamentals

**Trên slide:** *Spring Boot Fundamentals* · cột trái *What Spring Boot gives us* — *Auto-configuration* · *Embedded application
server* · *Dependency management* · *Convention over configuration* · *Integration with the Spring
ecosystem* · *Production-oriented configuration support*. Cột phải *Under the hood* —
`@SpringBootApplication public class ProductApplication { ... }`; `@SpringBootApplication` ≈ `@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan`.

**[NÓI]** — cột trái, mỗi ý một câu, đi đúng thứ tự trên slide:

> Trước hết, mình làm rõ vai trò của Spring Boot: Spring Boot được xây dựng trên Spring và hỗ trợ sẵn
> nhiều phần cấu hình thường gặp. Cột **What Spring Boot gives us** liệt kê sáu điểm:
>
> - **Auto-configuration**: Spring Boot dựa vào dependency và cấu hình của project để thiết lập các
>   thành phần phù hợp, chẳng hạn web server hoặc kết nối database.
> - **Embedded application server**: ứng dụng có thể chạy cùng Tomcat được tích hợp sẵn, nên với demo
>   này mình không cần cài một Tomcat riêng.
> - **Dependency management**: starter gom các dependency thường dùng, còn cơ chế quản lý version của
>   Boot giúp mình sử dụng các phiên bản tương thích.
> - **Convention over configuration**: khi theo các quy ước có sẵn, mình giảm được phần cấu hình phải
>   viết thủ công.
> - **Integration with the Spring ecosystem**: mình có thể kết hợp ứng dụng với các thành phần khác
>   trong hệ sinh thái Spring.
> - **Production-oriented configuration support**: Spring Boot hỗ trợ cấu hình phù hợp cho môi trường
>   vận hành. Phần sau mình sẽ minh họa qua config và profiles.

**[MỞ]** tab browser `https://start.spring.io` (đã mở sẵn từ 09:10).

**[NÓI]**

> Trước khi mở code, mình xem nhanh một project Spring Boot được tạo ra thế nào. Cách thường dùng là
> Spring Initializr ở start.spring.io, chỉ mất khoảng một phút.

Điền trên form, vừa chọn vừa nói ngắn tên từng field:

| Field | Giá trị |
|---|---|
| Project / Language | Maven / Java |
| Spring Boot | 4.1.x (bản ổn định; không chọn SNAPSHOT, M hay RC) |
| Group / Artifact / Name | `com.example` / `product-api` / `product-api` |
| Package name | **`com.example.product`** — sửa tay, vì giá trị tự sinh từ Artifact sẽ khác package của repo |
| Packaging / Configuration / Java | Jar / YAML / 21 |
| Dependencies (*ADD DEPENDENCIES*) | Spring Web, Spring Data JPA, Validation, H2 Database |

**[CHẠY]** bấm **EXPLORE** (không bấm GENERATE), mở `pom.xml` trong cửa sổ Explore.

**[NÓI]**

> Đây chính là ý **Dependency management** trên slide. `spring-boot-starter-parent` quản lý version,
> nên các starter bên dưới không cần ghi version. Bốn dependency mình vừa chọn trở thành
> `spring-boot-starter-webmvc`, `spring-boot-starter-data-jpa`, `spring-boot-starter-validation` và
> `h2`. Initializr cũng thêm sẵn test starter.
>
> Mình không bấm Generate, vì project mới cần tải dependency ở lần mở đầu tiên. Repo starter hôm nay có
> cùng cấu trúc với project này; mình chỉ chuẩn bị thêm `application.yml`, hai file `.http` và các tag
> để mọi người dễ theo dõi. Giờ mình mở class main trong starter để xem phần **Under the hood**.

<a id="ghi-chu-initializr"></a>

> **Ghi chú cho người trình bày — start.spring.io**
>
> - start.spring.io không có trên slide. Khối này chỉ minh họa ý *Dependency management*, không cần sửa
>   slide. Trễ giờ thì cắt theo [Phụ lục A](#phu-luc-a).
> - Có người hỏi "sao không Generate luôn?": project mới phải tải dependency và không có tag/`.http`
>   của buổi này. Ai muốn tự tạo thì làm sau buổi, khi có mạng để tải dependency.
> - Những chỗ bản Explore khác repo (**cần verify ở rehearsal**, chỉ nói khi có người hỏi): class main
>   Initializr đặt theo Name nên là `ProductApiApplication` (repo là `ProductApplication`); Initializr
>   có thêm `mvnw`, `HELP.md` và `ProductApiApplicationTests`; Boot 4 có thể sinh test starter riêng cho
>   từng starter, còn repo chỉ giữ `spring-boot-starter-webmvc-test`.

**[MỞ]** `ProductApplication.java` trong starter:

```java
@SpringBootApplication
public class ProductApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProductApplication.class, args);
    }
}
```

**[NÓI]** — cột phải, *Under the hood*:

> Ở phần **Under the hood**, mọi người để ý annotation `@SpringBootApplication` trên
> `ProductApplication`. Ở đay dùng dấu xấp xỉ để minh họa ba vai trò được kết hợp ở đây:
>
> - `@Configuration` cho phép class khai báo bean.
> - `@EnableAutoConfiguration` bật cơ chế auto-configuration mình vừa trao đổi.
> - `@ComponentScan` tìm các component trong package hiện tại và các package con, chẳng hạn `@Service`
>   hoặc `@RestController`.
>
> Để thấy những phần này hoạt động trong project, mình sẽ start app và cùng xem console.

**[CHẠY]** bấm ▶ cạnh `main` (lần đầu: *Run 'ProductApplication'*).

Kết quả mong đợi trong console (đại ý, bỏ bớt phần đầu dòng):

```text
Starting ProductApplication using Java 21...
No active profile set, falling back to 1 default profile: "default"
Tomcat initialized with port 8080 (http)
HikariPool-1 - Start completed.
Tomcat started on port 8080 (http) with context path '/'
Started ProductApplication in 3.2 seconds
```

**[NÓI]** — chỉ vào từng dòng:

> Trong starter đã có cấu hình datasource và port, nhưng mình chưa tự tạo Tomcat hay connection pool.
> Mọi người nhìn hai phần tương ứng trong console nhé: `spring-boot-starter-webmvc`, tức là Spring Web
> mình chọn lúc nãy, giúp ứng dụng chạy với Tomcat ở port 8080; H2 và JPA cùng cấu hình datasource giúp
> Boot thiết lập phần truy cập database. Dòng Hikari ở đây cho biết connection pool đã được khởi tạo.

**[DỰ ĐOÁN]** (tùy chọn: **mặc định bỏ khi đã demo start.spring.io**; chỉ làm nếu còn giờ):

> App đang chạy rồi. Giờ mình gọi thử `GET /api/products` — mọi người đoán xem ra status code bao
> nhiêu?

**[CHẠY]** mở `demo-requests.http`, gửi request #8 (`GET {{host}}/api/products`).

Kết quả: `404`, body mặc định của Spring Boot:

```json
{
  "timestamp": "2026-09-25T02:40:12.345Z",
  "status": 404,
  "error": "Not Found",
  "path": "/api/products"
}
```

> Kết quả ở đây là `404`, vì project hiện chưa có controller cho endpoint này. Web server đã tiếp nhận
> request, nhưng chưa có endpoint tương ứng để xử lý. Tiếp theo, mình sẽ bổ sung từng phần của Product
> API.
>
> Một chi tiết nhỏ khi đọc response: `timestamp` có chữ `Z` ở cuối là giờ UTC, nên sẽ chênh 7 tiếng so
> với giờ Việt Nam.

**[CHẠY]** dừng app (`Ctrl+F2`).

---

# 09:43–09:47 — Dependency Injection

## [Slide 6] Dependency Injection

**Trên slide:** *Dependency Injection* · bên trái: `@Service public class ProductService { private final ProductRepository productRepository; public ProductService(ProductRepository productRepository) { this.productRepository = productRepository; } }`. Bên phải *Why constructor injection?* — ✓ *Dependencies are explicit* · ✓ *Easier to test* · ✓ *Fields can be immutable (final)* · ✓ *Easier to reason about* · ✗ *Avoid field injection as the default*.

**[NÓI]** — chỉ vào code bên trái:

> Mọi người cùng nhìn constructor bên trái. `ProductService` cần `ProductRepository` để làm việc với dữ liệu,
> nên mình khai báo dependency này qua tham số constructor. Khi tạo service, Spring cung cấp
> repository tương ứng. Đó là cách mình sử dụng **Dependency Injection** trong project này.

**[DỰ ĐOÁN]**

> Trước khi đi tiếp, mình mời mọi người cùng nghĩ một chút: với `ProductRepository` là interface, liệu
> mình có thể viết trực tiếp `new ProductRepository()` không?

Đáp án:

> Mình không thể khởi tạo trực tiếp như vậy vì `ProductRepository` là **interface**. Trong ứng dụng
> này, Spring Data tạo proxy cho repository, rồi Spring truyền nó vào constructor của service. Khi
> viết unit test, mình có thể truyền một mock thay cho repository đó.

**[NÓI]** — cột phải, đi lần lượt 5 dòng từ trên xuống (khoảng 1,5 phút):

> Vậy tại sao mình chọn **constructor injection**? Có năm ý:
>
> **Thứ nhất, dependency được thể hiện rõ.** Chỉ cần nhìn vào constructor, mình biết `ProductService` cần `ProductRepository` để hoạt động.
>
> **Thứ hai, dễ viết unit test.** Mình có thể tạo service bằng `new ProductService(mockRepository)` và kiểm tra logic mà không cần khởi động Spring. Có thể skip, Field injection cũng test được với Mockito, nhưng khi đó mình cần Mockito gắn mock vào field.
>
> **Thứ ba, có thể dùng `final`.** Repository được gán một lần trong constructor, nên mình khai báo field là `final`. Sau khi service được tạo, field đó không thể được gán sang một repository khác.
>
> **Thứ tư, dễ hiểu object đã được tạo như thế nào.** Khi thấy `new ProductService(repository)`, mình biết dependency được đưa vào ngay lúc tạo object. Nếu dùng field injection rồi tự gọi `new ProductService()`, object vẫn được tạo, nhưng field `repository` sẽ là `null` vì Spring không tham gia inject.
>
> **Thứ năm, mình không chọn field injection làm cách mặc định.** Field injection vẫn hoạt động khi Spring quản lý object, và vẫn có thể viết unit test. Nhưng constructor injection giúp mình nhìn rõ dependency, tự truyền mock khi test và sử dụng được `final`. Vì vậy, đây là cách mình sẽ dùng khi viết service trong phần demo.
>
> Lát nữa, khi viết service, mình sẽ áp dụng đúng cấu trúc này.

*Ghi chú cho bạn, không cần nói trên slide:* Ý 4 không có nghĩa constructor tự chặn `null`—người viết vẫn có thể gọi `new ProductService(null)`. Ý 5 là **quyết định chọn cách viết** dựa trên bốn ý trước, nên bạn chỉ cần nói ngắn như trên.

---

# 09:47–09:50 — Package structure và demo project

## [Slide 7] Suggested Package Structure

**Trên slide:** *Suggested Package Structure* · bên trái cây package `com.example.product` — `controller` · `dto` · `entity` ·
`exception` · `repository` · `service` · `ProductApplication.java`. Bên phải *Layer
Responsibilities* — *Controller: HTTP requests and responses* · *Service: Business logic and
orchestration* · *Repository: Database access and persistence*.

**[NÓI]** — cây package bên trái trước, rồi ba ô bên phải:

> Về **Suggested Package Structure**, project được tổ chức dưới package gốc `com.example.product`.
> Mình đi lần lượt từ trên xuống để mọi người biết mỗi phần code sẽ được đặt ở đâu.
>
> `controller` chứa phần xử lý HTTP; `dto` chứa dữ liệu nhận vào và trả về; `entity` chứa các class
> map với database. Tiếp theo, `exception` chứa exception và handler, `repository` chứa phần truy cập
> dữ liệu, còn `service` chứa business logic. Cuối cùng, `ProductApplication.java` nằm ở package gốc
> để scan các package con.
>
> Ba ô **Layer Responsibilities** bên phải nhấn mạnh trách nhiệm chính:
>
> - **Controller — (HTTP requests and responses)**: controller tiếp nhận request và trả response.
> - **Service — (Business logic and orchestration)**: service xử lý nghiệp vụ và phối hợp các bước cần
>   thiết.
> - **Repository — (Database access and persistence)**: repository phụ trách truy cập và lưu dữ liệu.
>
> Khi vào code, mình sẽ giữ cách phân chia này để mỗi phần có trách nhiệm rõ ràng.

Ghi chú cho người trình bày: chia theo feature cũng là một lựa chọn; không mở rộng trong phần nói chính.

**[MỞ]** IDE, mở cây thư mục: các package rỗng `controller`, `dto`, `entity`, `exception`,
`repository`, `service` (có file `.gitkeep` để git giữ thư mục rỗng — không cần quan tâm).

> Trong project đã có sẵn đúng cây này. Để ý `ProductApplication` nằm ở package gốc
> `com.example.product`, các package kia nằm bên dưới — nhờ vậy `@ComponentScan` mới tìm thấy hết.
> Nếu đặt class ngoài package gốc, cấu hình scan mặc định của project này sẽ không tìm thấy nó.

## [Slide 8] Demo Project — The Product API

**Trên slide:** *Demo Project* · *The Product API — One project for the whole session, so you can see how every piece
connects.* · *Product Entity*: `id | name | sku | price | active` · *Core APIs*: `POST /api/products`,
`GET /api/products/{id}`, `GET /api/products?page=0&size=10&sort=name,asc` · *Tech Stack*: Java 21,
Spring Boot 4.x, Spring Web, Spring Data JPA, Validation, H2, JUnit 5 · Mockito.

**[NÓI]**

> Đây là **Demo Project — The Product API** mình sẽ dùng xuyên suốt buổi hôm nay. Nghiệp vụ được giữ
> đơn giản để mọi người dễ theo dõi cách các thành phần kết nối với nhau.
>
> **Product Entity** có năm field: `id`, `name`, `sku` là mã sản phẩm, `price` và `active`.
>
> Phần **Core APIs** gồm ba endpoint:
>
> - `POST /api/products` để tạo sản phẩm.
> - `GET /api/products/{id}` để lấy sản phẩm theo id.
> - `GET /api/products` với `page`, `size`, `sort` để lấy danh sách có pagination và sorting.
>
> Về **Tech Stack**, đây là những gì mình đã chọn trên start.spring.io lúc nãy: Java 21, Spring Boot
> 4.x, Spring Web, Spring Data JPA, Validation và H2. Thêm JUnit và Mockito để viết test.
>
> *(Nếu đã cắt phần start.spring.io, dùng câu đầy đủ:)* Về **Tech Stack**, mình dùng Java 21, Spring
> Boot 4.x và Spring Web để xây dựng API; Spring Data JPA để truy cập database; Validation để kiểm tra
> input; H2 làm database in-memory; JUnit và Mockito để viết test.
>
> Slide ghi **JUnit 5**, còn project này dùng **JUnit Jupiter 6** theo stack đã chọn. Những annotation
> mình sử dụng trong demo, như `@Test` và `@ExtendWith`, vẫn có cách viết quen thuộc.

---

# 09:50–09:56 — Entity `Product`

**[NÓI]**

> Mình bắt đầu phần code với **entity**, tương ứng với phần dữ liệu ở cuối sơ đồ. Trước tiên, mình sẽ
> mô tả một sản phẩm có những field nào và cách chúng được lưu trong database.

**[GÕ]** tạo class `Product` trong package `entity`:

```java
@Entity
@Table(name = "products")
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false, unique = true)
    private String sku;

    @Column(nullable = false)
    private BigDecimal price;

    @Column(nullable = false)
    private boolean active = true;
}
```

**[SINH]** `Alt+Insert` → *Getter and Setter* → chọn tất cả field → OK.

> ⚠ Khi IntelliJ hỏi import cho `@Id`, chọn **`jakarta.persistence.Id`**, không chọn
> `org.springframework.data.annotation.Id`. Tất cả annotation của entity đều từ `jakarta.persistence`.

**[NÓI]** — vừa gõ vừa nói, mỗi annotation một câu:

> `@Entity` — JPA sẽ map class này sang một bảng. `@Table` đặt tên bảng là `products`.
>
> `@Id` là primary key. `@GeneratedValue(IDENTITY)` — để database tự sinh id tăng dần.
>
> `@Column(nullable = false)` — cột không được null. `unique = true` trên `sku` — hai sản phẩm không
> được trùng mã. Constraint này nằm **trong database**; lát nữa mình sẽ quay lại khi thử tạo SKU trùng.
>
> Giá tiền dùng `BigDecimal`, không dùng `double`, vì `double` không biểu diễn chính xác số thập phân
> nên mình chọn kiểu dữ liệu phù hợp để lưu giá trị thập phân.

**[CHẠY]** `Ctrl+F9` (Build Project) — phải build xanh trước khi đi tiếp.

## Chuyển ý — [Slide 9] Building REST APIs

**Trên slide:** *Building REST APIs* · *Keep API contracts separate from persistence* · *API Contract*: `[ API ]` → `[ Request DTO ]` → `[ Validation ]`, nhánh về `[ Response DTO ]`; phía *Persistence*: `[ Entity ]` → `[ Database ]`, nhận mũi tên từ Validation qua boundary nét đứt. Bốn câu theo hàng: *Entity represents persistence.* · *DTO represents API contract.* · *Validate input at the boundary.* · *Avoid exposing entities directly.*

**[NÓI]**

> Sau khi có entity, mình cần xác định dữ liệu mà API sẽ nhận và trả về. Một cách có thể nghĩ đến là
> nhận JSON trực tiếp vào `Product`, rồi trả luôn object đó cho client. Mình cùng xem vì sao nên
>  tách hai phần này.
>
> **Keep API contracts separate from persistence** nghĩa là cấu trúc dữ liệu của API cần được tách
> khỏi cấu trúc dùng để lưu dữ liệu.
>
> Trong sơ đồ, **API** nhận **Request DTO**, rồi thực hiện **Validation**. Dữ liệu hợp lệ được service
> map sang **Entity** để lưu vào **Database**. Đường nét đứt đứng chia phần **API Contract** và
> **Persistence**. Ở chiều trả về, client nhận **Response DTO**.
>
> Bốn câu bên dưới làm rõ cách phân chia này:
>
> - **Entity represents persistence**: entity mô tả dữ liệu được lưu như thế nào.
> - **DTO represents API contract**: DTO mô tả dữ liệu mà API nhận và trả cho client.
> - **Validate input at the boundary**: mình kiểm tra input ngay khi tiếp nhận request.
> - **Avoid exposing entities directly**: mình trả DTO để kiểm soát những field được công khai qua
>   API.
>
> Ví dụ, request tạo sản phẩm chỉ cần `name`, `sku` và `price`. Các field như `id` hoặc `active` do
> phía ứng dụng quyết định. Tách DTO cũng giúp mình tránh vô tình đưa một field nội bộ mới ra
> response.

Ghi chú cho người trình bày: mũi tên từ Validation sang Entity là nét liền; đường nét đứt đứng là boundary giữa API Contract và Persistence. Service map DTO đã validate sang entity.

---

# 09:56–10:01 — Request DTO và Response DTO

**[GÕ]** `dto/CreateProductRequest.java`:

```java
public record CreateProductRequest(
        @NotBlank @Size(max = 100) String name,
        @NotBlank @Size(max = 50) String sku,
        @NotNull @Positive BigDecimal price
) {}
```

Import annotation từ `jakarta.validation.constraints`; `BigDecimal` từ `java.math.BigDecimal`.

**[NÓI]**

> Mình dùng `record` — có từ Java 16 — vì DTO dùng để truyền dữ liệu. Record tự có constructor, accessor
> `name()`, `equals`, `hashCode`, `toString`, và là immutable.
>
> Validation rule đặt ngay trên DTO:
>
> - `@NotBlank` — không null, không rỗng, không chỉ toàn dấu cách.
> - `@Size(max = 100)` — kiểm tra độ dài của giá trị nhận vào; ở đây tối đa là 100 ký tự.
> - `@Positive` — giá phải lớn hơn 0.
> - `@NotNull` đi kèm `@Positive` vì `@Positive` **bỏ qua null** — thiếu `@NotNull` thì giá trị
>   `price` bằng null vẫn có thể qua bước validation.

**[GÕ]** `dto/ProductResponse.java`:

```java
public record ProductResponse(
        Long id,
        String name,
        String sku,
        BigDecimal price,
        boolean active
) {}
```

**[NÓI]**

> Response DTO là những gì client nhận về. **Hôm nay** nó nhìn giống hệt entity, nhưng hai bên đổi độc
> lập được: thêm một cột nội bộ vào bảng thì API vẫn y nguyên.

> ❓ Câu hỏi "quên `@Valid` thì sao?" để dành tới lúc viết controller — lúc đó mới có `@Valid` để chỉ
> vào.

---

# 10:01–10:04 — Repository

**[GÕ]** `repository/ProductRepository.java`:

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    boolean existsBySku(String sku);
}
```

**[NÓI]**

> Đây là một **interface**, và mình không viết class nào implement nó. Lúc app start, Spring Data JPA
> tự tạo một object thật (một proxy) cho interface này.
>
> `JpaRepository<Product, Long>` — entity là `Product`, kiểu của id là `Long`. Chỉ vậy thôi là có sẵn
> `save`, `findById`, `findAll`, `deleteById`, `count`… và cả `findAll(Pageable)` cho pagination.

**[CHẠY]** (tùy chọn) gõ `productRepository.` ở đâu đó hoặc `Ctrl+F12` **hai lần** trong `ProductRepository` (lần thứ hai bật *Show inherited members*) để mọi người
thấy danh sách method có sẵn.

**[NÓI]**

> `existsBySku` là một **derived query**: Spring Data đọc tên method — `exists`, `By`, `Sku` — và tự
> sinh câu SQL kiểm tra có dòng nào có `sku` bằng tham số không. Gõ sai tên field, ví dụ `existsBySkuu`,
> thì app **không start được** — lỗi lộ ra ngay từ đầu. Method này mình dùng ở bước tùy chọn sau.

---

# 10:04–10:11 — Service

**[GÕ]** `service/ProductService.java` — gõ phần khung, field, annotation:

```java
@Service
public class ProductService {

    private final ProductRepository productRepository;

    // [SINH] Alt+Insert → Constructor → chọn productRepository

    @Transactional
    public ProductResponse create(CreateProductRequest request) {
        Product product = new Product();
        product.setName(request.name());
        product.setSku(request.sku());
        product.setPrice(request.price());
        product.setActive(true);

        Product saved = productRepository.save(product);
        return toResponse(saved);
    }

    private ProductResponse toResponse(Product product) {
        return new ProductResponse(
                product.getId(),
                product.getName(),
                product.getSku(),
                product.getPrice(),
                product.isActive()
        );
    }
}
```

**[SINH]** constructor bằng `Alt+Insert → Constructor`. Kết quả:

```java
    public ProductService(ProductRepository productRepository) {
        this.productRepository = productRepository;
    }
```

> ⚠ Import `@Transactional` từ **`org.springframework.transaction.annotation`**, không chọn
> `jakarta.transaction.Transactional` (cái sau không có `readOnly` mà lát nữa mình cần).

**[NÓI]**

> Đoạn constructor này tương ứng với ví dụ trên slide 6: `ProductService` nhận `ProductRepository` và
> lưu vào field `final`. Vì class có một constructor, Spring có thể dùng constructor đó để inject mà
> không cần thêm `@Autowired`.
>
> Service sẽ thực hiện các bước của nghiệp vụ tạo sản phẩm: map request DTO sang entity, lưu dữ liệu,
> rồi map kết quả sang response DTO. Nhờ vậy, controller chỉ cần gọi service, không phải xử lý chi
> tiết việc lưu dữ liệu.
>
> `@Transactional` đặt transaction quanh phần lưu dữ liệu. Khi thành công thì commit; với runtime
> exception trong demo thì rollback. Phần chi tiết mình để ở ghi chú.
>
> Method `toResponse` tập trung việc map entity sang DTO vào một chỗ. Mình viết phần map này thủ công
> để mọi người dễ theo dõi từng field.

Ghi chú cho người trình bày: MapStruct là lựa chọn ngoài phạm vi buổi này.

---

# 10:11–10:16 — Controller `POST /api/products`

**[GÕ]** `controller/ProductController.java`:

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    private final ProductService productService;

    public ProductController(ProductService productService) {
        this.productService = productService;
    }

    @PostMapping
    public ResponseEntity<ProductResponse> create(@Valid @RequestBody CreateProductRequest request) {
        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body(productService.create(request));
    }
}
```

(Constructor có thể **[SINH]** như ở Service.) Import `@Valid` từ `jakarta.validation.Valid`.

**[NÓI]**

> `@RestController` — giá trị trả về được ghi thẳng ra response body dưới dạng JSON.
> `@RequestMapping` đặt base path cho cả class.
>
> `@RequestBody` — Spring dùng Jackson đọc JSON trong body thành `CreateProductRequest`.
>
> `@Valid` — chạy các validation rule trên DTO **trước khi** method được gọi. Dữ liệu sai thì method
> này không bao giờ chạy tới.
>
> Controller chỉ lo HTTP: nhận request, gọi service, chọn status code. Không có business logic ở đây
> — controller càng mỏng càng tốt.

**[DỰ ĐOÁN]**

> Trước khi chạy request, mình mời mọi người dự đoán một tình huống: nếu mình bỏ `@Valid` ở đây, các
> validation rule trên DTO có còn được thực hiện khi nhận request không?

Đáp án: annotation trên DTO vẫn còn đó nhưng **không ai chạy chúng** — dữ liệu sai đi thẳng vào
service. Request #2 (`"name": ""`, `"sku": ""`, `"price": -1`) sẽ trả **`201 Created`** và sản phẩm
có tên rỗng, giá âm nằm luôn trong database. Database chỉ chặn được `null` (nhờ `nullable = false`),
và khi đó là lỗi 500 chứ không phải 400.

> Với trường hợp tạo sản phẩm thành công, theo mọi người mình nên trả `200` hay `201`?

Đáp án: `201 Created` — nói rõ là "đã tạo resource mới". `200` vẫn là thành công, nhưng kém chính xác
hơn. Theo chuẩn HTTP, `201` nên kèm header `Location` trỏ tới resource mới — hôm nay mình bỏ qua
cho gọn.

---

# 10:16–10:23 — Chạy lần đầu

**[CHẠY]** chạy lại `ProductApplication` (`Shift+F10`). Đợi dòng `Started ProductApplication`.

**[CHẠY]** trong `demo-requests.http`, gửi **request #1**:

```http
POST {{host}}/api/products
Content-Type: application/json

{
  "name": "Mechanical Keyboard",
  "sku": "KB-100",
  "price": 79.99
}
```

Kết quả mong đợi: `201 Created`

```json
{
  "id": 1,
  "name": "Mechanical Keyboard",
  "sku": "KB-100",
  "price": 79.99,
  "active": true
}
```

**[NÓI]** — chỉ lại slide 4 bằng lời:

> Request vừa đi đúng bản đồ: JSON thành DTO, validate, Controller, Service, Repository, database;
> rồi quay ngược lại thành `ProductResponse` và thành JSON.

**[DỰ ĐOÁN]**

> Giờ mình cố tình gửi dữ liệu sai: tên rỗng, SKU rỗng, giá âm. Status code sẽ là gì? Và body sẽ nói
> cho client biết field nào sai không?

**[CHẠY]** gửi **request #2** (`"name": ""`, `"sku": ""`, `"price": -1`).

Kết quả mong đợi: `400 Bad Request`, body mặc định của Spring Boot — **không** có chi tiết field nào
sai:

```json
{
  "timestamp": "2026-09-25T03:18:40.123Z",
  "status": 400,
  "error": "Bad Request",
  "path": "/api/products"
}
```

Chi tiết lỗi chỉ nằm trong **console** (một dòng WARN dài):

```text
WARN ... DefaultHandlerExceptionResolver : Resolved [org.springframework.web.bind.MethodArgumentNotValidException:
Validation failed for argument [0] ... with 3 errors: [Field error in object 'createProductRequest' on field 'name' ...
default message [must not be blank]] ...]
```

**[NÓI]**

> Response trả `400`, tức là validation đã phát hiện input không hợp lệ. Tuy nhiên, body hiện chưa cho
> client biết field nào cần sửa; chi tiết lỗi đang nằm trong log của server. Tý nữa mình sẽ
> thêm global exception handling để trả thông tin đó theo một format rõ ràng hơn.

**[CHẠY]** (tùy chọn, nếu đúng giờ) — **[DỰ ĐOÁN]** "Gửi lại request #1 lần nữa, cùng SKU `KB-100`,
chuyện gì xảy ra?" → gửi **request #10** (SKU `KB-100`, tên khác).

Kết quả: `500 Internal Server Error`, body mặc định. Console có `SQL Error: 23505` và
`Unique index or primary key violation` — database đã chặn nhờ `unique = true`.

> Unique constraint đã ngăn việc lưu SKU trùng, nhưng API hiện trả `500`. Với tình huống này, client
> sẽ khó biết cần điều chỉnh request thế nào. Ở bước tùy chọn phía sau, mình sẽ bổ sung xử lý để trả
> lỗi cụ thể hơn.

## [CHECKPOINT A] — 10:23

Code phải khớp tag `step-05-post-endpoint`. Nếu `POST` chưa trả `201`:

```powershell
git stash -u
git checkout step-05-post-endpoint
```

Chạy lại app, gửi lại request #1, rồi đi tiếp.

---

# 10:23–10:28 — Nghỉ 5 phút / buffer #1

**[NÓI]**

> Mình tạm nghỉ 5 phút và sẽ tiếp tục lúc 10:28 nhé. Nếu mọi người đang gõ theo và muốn đồng bộ code,
> có thể mở tag `step-05-post-endpoint` trong lúc nghỉ.

Trước khi nghỉ: trả lời 1–2 câu hỏi trong chat. Nếu đang trễ, dùng thời gian này để bắt kịp
(checkpoint, chạy lại) và rút giờ nghỉ còn 2 phút.

---

# 10:28–10:37 — Global exception handling

## [Slide 10] Error Handling — One consistent API error contract

**Trên slide:** *Error Handling* · *One consistent API error contract*. Sơ đồ: *Invalid Request* → *@Valid* →
*MethodArgumentNotValidException*; và *NotFoundException* — cả hai → *@RestControllerAdvice* →
*400 / 404 ApiError*. Dòng cuối: *Different failures, one consistent API error contract.*

**[NÓI]** — 1,5 phút, đi theo sơ đồ:

> Mình tiếp tục với **Error Handling — One consistent API error contract**. Sau phần trước, mình đã
> thấy validation trả `400`; bây giờ mình sẽ làm rõ thông tin trong response lỗi.
>
> Theo sơ đồ, **Invalid Request** đi qua `@Valid` và tạo ra `MethodArgumentNotValidException`. Với
> nhánh **NotFoundException**, code của mình sẽ dùng `ProductNotFoundException` khi không tìm thấy sản
> phẩm.
>
> Cả hai exception được xử lý qua `@RestControllerAdvice`, rồi trả **400 / 404 ApiError**. Status khác
> nhau, nhưng cấu trúc response thống nhất.
>
> Đó là ý của câu **Different failures, one consistent API error contract**: client có thể dùng cùng
> một cách đọc thông tin lỗi cho hai trường hợp này.

**[MỞ]** IDE.

**[GÕ]** `exception/ApiError.java`:

```java
public record ApiError(
        String code,
        String message,
        Instant timestamp
) {}
```

**[GÕ]** `exception/GlobalExceptionHandler.java` — gõ annotation và chữ ký method, **[DÁN]** chuỗi
stream:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiError> handleValidation(MethodArgumentNotValidException ex) {
        String message = ex.getBindingResult()
                .getFieldErrors()
                .stream()
                .map(error -> error.getField() + ": " + error.getDefaultMessage())
                .sorted()
                .collect(Collectors.joining(", "));

        return ResponseEntity
                .badRequest()
                .body(new ApiError(
                        "VALIDATION_ERROR",
                        message,
                        Instant.now()
                ));
    }
}
```

Import: `org.springframework.web.bind.MethodArgumentNotValidException`, `java.time.Instant`,
`java.util.stream.Collectors`.

**[NÓI]**

> Khi client gửi dữ liệu không hợp lệ, ví dụ tên sản phẩm bị bỏ trống, Spring sẽ phát hiện lỗi validation. Thay vì xử lý lỗi này ở từng controller, mình dùng `@RestControllerAdvice` để xử lý chung tại đây.
>
> Dòng `@ExceptionHandler(MethodArgumentNotValidException.class)` cho Spring biết: khi gặp loại lỗi validation này, hãy gọi method `handleValidation`.
>
> Bên trong method, mình lấy từng field bị lỗi và ghép thành thông báo như `name: must not be blank`. Nếu có nhiều lỗi, mình nối chúng lại bằng dấu phẩy. Cuối cùng, API trả về **400 Bad Request** cùng một `ApiError` có mã `VALIDATION_ERROR`, thông báo lỗi và thời gian xảy ra lỗi.
>
> Mình thêm `.sorted()` để các thông báo luôn được sắp xếp theo cùng một thứ tự. Nhờ vậy response ổn định hơn và khi viết test cũng dễ so sánh hơn.

**Ghi nhớ nhanh:** `@RestControllerAdvice` là *chỗ xử lý lỗi chung*; `@ExceptionHandler` chọn *loại lỗi cần xử lý*; phần `stream()` tạo *nội dung thông báo trả cho client*.

**[CHẠY]** restart app (`Ctrl+F5`). Gửi lại **request #2**.

Kết quả mong đợi: `400 Bad Request`

```json
{
  "code": "VALIDATION_ERROR",
  "message": "name: must not be blank, price: must be greater than 0, sku: must not be blank",
  "timestamp": "2026-09-25T03:33:05.123456Z"
}
```

**[NÓI]**

> Với response này, client đã biết field nào không hợp lệ và lý do tương ứng. Field `code` giúp phía
> client phân biệt loại lỗi để xử lý; `message` cung cấp nội dung dễ đọc cho người dùng API.
>
> SKIP, Trong ví dụ, message của Hibernate Validator đang hiển thị bằng tiếng Anh. Nếu cần tùy chỉnh, mình
> có thể khai báo `message` trên annotation hoặc sử dụng `ValidationMessages.properties`.

> Ghi chú: `timestamp` có thể in 3, 6 hoặc 9 chữ số lẻ tùy máy — không sao.

---

# 10:37–10:44 — `GET /api/products/{id}` và 404

**[GÕ]** `exception/ProductNotFoundException.java`:

```java
public class ProductNotFoundException extends RuntimeException {

    public ProductNotFoundException(Long id) {
        super("Product not found: " + id);
    }
}
```

**[GÕ]** thêm vào `ProductService`:

```java
    @Transactional(readOnly = true)
    public ProductResponse getById(Long id) {
        Product product = productRepository.findById(id)
                .orElseThrow(() -> new ProductNotFoundException(id));

        return toResponse(product);
    }
```

**[GÕ]** thêm vào `ProductController`:

```java
    @GetMapping("/{id}")
    public ProductResponse getById(@PathVariable Long id) {
        return productService.getById(id);
    }
```

**[GÕ]** thêm vào `GlobalExceptionHandler` (bên dưới handler validation):

```java
    @ExceptionHandler(ProductNotFoundException.class)
    public ResponseEntity<ApiError> handleProductNotFound(ProductNotFoundException ex) {
        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(new ApiError(
                        "PRODUCT_NOT_FOUND",
                        ex.getMessage(),
                        Instant.now()
                ));
    }
```

**[NÓI]**

> `findById` trả về `Optional` — có thể có, có thể không. Không có thì `orElseThrow` ném exception
> của mình. Đây chính là ô "NotFoundException" trên slide 10.
>
> Để ý: **Service không biết gì về HTTP**, trong service không có con số 404 nào. Nó chỉ nói "không
> tìm thấy". Chuyển thành `404` là việc của handler.
>
> `readOnly = true` đánh dấu ý định chỉ đọc để framework có thể tối ưu. Đây không phải cơ chế
> ngăn mọi thao tác ghi; method này của mình chỉ gọi `findById`.
>
> Method trả thẳng `ProductResponse`, không bọc `ResponseEntity` — khi đó status mặc định là `200`.

**[CHẠY]** restart app.

**[DỰ ĐOÁN]**

> Mình vừa restart app. Nếu gọi lại sản phẩm có `id = 1` đã tạo trước đó, theo mọi người API sẽ trả
> kết quả gì?

**[CHẠY]** gửi **request #3** (`GET {{host}}/api/products/{{productId}}` — biến này vẫn là `1` từ lúc
chạy request #1).

Kết quả: `404`

```json
{
  "code": "PRODUCT_NOT_FOUND",
  "message": "Product not found: 1",
  "timestamp": "..."
}
```

> Lần này API trả `404`, vì dữ liệu trong H2 **in-memory** của demo không còn sau khi restart app.
> Response cũng cho thấy handler not-found đã hoạt động. Mình sẽ tạo lại sản phẩm rồi gọi GET một lần
> nữa để đối chiếu hai trường hợp.

(Nếu IntelliJ báo biến `productId` chưa có giá trị: gửi thẳng `GET {{host}}/api/products/1`.)

**[CHẠY]** gửi **request #1** (tạo lại, `201`, `id` = 1) → gửi **request #3** → `200`:

```json
{
  "id": 1,
  "name": "Mechanical Keyboard",
  "sku": "KB-100",
  "price": 79.99,
  "active": true
}
```

**[CHẠY]** gửi **request #4** (`GET /api/products/99999`) → `404`, `"message": "Product not found: 99999"`.

**[NÓI]**

> Như vậy, mình đã có hai trường hợp `400` và `404` cùng trả về `ApiError`. Đây là error contract
> thống nhất mà slide 10 mô tả. Tiếp theo, mình sẽ bổ sung API lấy danh sách sản phẩm.

(Tùy chọn, nếu người nghe nhiều kinh nghiệm) Gọi `GET {{host}}/api/products/abc` → `400` nhưng body là **format mặc
định** của Spring (`timestamp/status/error/path`), không phải `ApiError`. Nói thẳng: "handler của mình
mới cover các lỗi mình khai báo thôi; làm thật thì phải cover thêm các lỗi của framework nữa."

## [CHECKPOINT B] — 10:44

Code phải khớp tag `step-07-get-by-id`. Nếu `GET` chưa chạy:

```powershell
git stash -u
git checkout step-07-get-by-id
```

---

# 10:44–10:54 — Pagination và sorting

**[NÓI]**

> Với API lấy danh sách, ngoài việc trả đúng dữ liệu, mình còn cần quan tâm đến số lượng dữ liệu được
> lấy trong mỗi request. Trước tiên, mình cùng xem cách gọi `findAll()` không có tham số:

**[MỞ]** chiếu (không gõ vào project):

```java
return productRepository.findAll();
```

> Nếu bảng tăng lên một triệu dòng, việc lấy toàn bộ dữ liệu trong một request sẽ ảnh hưởng đến
> database, ứng dụng và client như thế nào? Mọi người có thể chia sẻ một ý trong chat nhé.

Dừng 5 giây.

## [Slide 11] Spring Data JPA — Pagination and sorting

**Trên slide:** *Spring Data JPA* · *Pagination and sorting* · `[ Database ]`: *1,000,000 rows*. *The Danger Path*: *Unbounded query (findAll())* → *Massive Result (Memory/Network Bloat)*. *The Safe Path*: *Pageable (?size=10&sort=name,asc)* → *Page of results*, *10 items*. *Key risks: database load, memory, response size, network cost, slow clients, accidental huge queries.* *Pro-tips:* *Public APIs often wrap pagination metadata in their own response DTO instead of exposing Spring Data’s Page directly.* · *Inspect generated SQL with* `spring.jpa.show-sql=true`.

**[NÓI]** — Database, danger path, safe path, key risks, rồi hai pro-tips:

> Trên slide, **Database — 1,000,000 rows** Giả sử db có 1 triệu products. Nhánh **The Danger Path**
> dùng **Unbounded query (findAll())** để lấy toàn bộ dữ liệu, dẫn đến **Massive Result
> (Memory/Network Bloat-Sự phình to bộ nhớ/mạng)**.
>
> Ở nhánh **The Safe Path**, mình dùng **Pageable (?size=10&sort=name,asc)** để nhận **Page of
> results** gồm **10 items** mỗi page. Cách này giới hạn lượng dữ liệu được lấy trong mỗi lần gọi.
>
> Phần **Key risks** giải thích vì sao mình cần giới hạn query:
>
> - **Database load**: database phải xử lý lượng dữ liệu lớn.
> - **Memory**: ứng dụng cần thêm bộ nhớ để giữ kết quả.
> - **Response size**: response lớn hơn và mất thêm thời gian xử lý.
> - **Network cost**: lượng dữ liệu truyền qua mạng tăng lên.
> - **Slow clients**: client có thể mất nhiều thời gian nhận và đọc response.
> - **Accidental huge queries**: một query chạy tốt khi dữ liệu còn ít có thể gây vấn đề khi dữ liệu
>   tăng lên.
>
> Slide có hai **Pro-tips**. **Public APIs often wrap pagination metadata in their own response DTO
> instead of exposing Spring Data’s Page directly** nhắc mình dùng page DTO riêng để kiểm soát API
> contract. **Inspect generated SQL with spring.jpa.show-sql=true** nhắc mình kiểm tra SQL thực tế;
> phần profiles sẽ minh họa việc này.

**[MỞ]** IDE.

**[GÕ]** thêm vào `ProductController`:

```java
    @GetMapping
    public Page<ProductResponse> getProducts(
            @PageableDefault(size = 10, sort = "name") Pageable pageable) {
        return productService.getProducts(pageable);
    }
```

> ⚠ Import `Pageable` từ **`org.springframework.data.domain`**. IntelliJ hay gợi ý
> `java.awt.print.Pageable` đầu tiên — chọn nhầm là lỗi compile ngay.

**[GÕ]** thêm vào `ProductService`:

```java
    @Transactional(readOnly = true)
    public Page<ProductResponse> getProducts(Pageable pageable) {
        return productRepository.findAll(pageable).map(this::toResponse);
    }
```

**[NÓI]**

> Spring MVC đọc `page`, `size`, `sort` từ query string rồi tạo sẵn object `Pageable`. Client không
> gửi thì lấy default ở `@PageableDefault`: 10 item, sort theo `name`.
>
> Repository nhận `Pageable`, trả về `Page<Product>`: dữ liệu của một page **kèm** tổng số dòng, tổng
> số page. `.map(this::toResponse)` đổi từng entity thành DTO mà vẫn giữ nguyên thông tin page.

**[CHẠY]** restart app. Mở `seed.http` → **Run All** (▶▶ ở đầu file) → 8 request, tất cả `201`.
Gửi thêm **request #1** trong `demo-requests.http` → tổng cộng **9** sản phẩm.

**[DỰ ĐOÁN]**

> Đang có 9 sản phẩm. Mình lấy page đầu, mỗi page 5 item. Mọi người đoán `totalPages` bằng mấy?

**[CHẠY]** gửi **request #5** (`?page=0&size=5&sort=name,asc`).

Kết quả mong đợi (rút gọn — `totalPages` = 2):

```json
{
  "content": [
    { "id": 7, "name": "Desk Lamp", "sku": "DL-800", "price": 18.75, "active": true },
    { "id": 8, "name": "Ergonomic Chair", "sku": "CH-900", "price": 249.00, "active": true },
    { "id": 4, "name": "Laptop Stand", "sku": "LS-500", "price": 45.00, "active": true },
    { "id": 9, "name": "Mechanical Keyboard", "sku": "KB-100", "price": 79.99, "active": true },
    { "id": 3, "name": "Monitor 27-inch", "sku": "MN-400", "price": 219.00, "active": true }
  ],
  "pageable": { "pageNumber": 0, "pageSize": 5, "...": "..." },
  "totalElements": 9,
  "totalPages": 2,
  "number": 0,
  "size": 5,
  "first": true,
  "last": false,
  "...": "..."
}
```

> Thứ tự các field của khối ngoài (`content`, `pageable`, `totalElements`…) có thể khác trên máy —
> không sao. Id có thể khác nếu thứ tự chạy seed khác.

**[NÓI]**

> `content` là dữ liệu của page. Các field còn lại là metadata: tổng 9 item, 2 page, đang ở page 0.
> Khi gọi API, mình cần lưu ý page **đếm từ 0**, để phía client chọn đúng page mong muốn.

**[CHẠY]** gửi **request #6** (`page=1`) → 4 item còn lại: Noise-Cancelling Headset, USB-C Hub,
Webcam HD, Wireless Mouse; `"last": true`.

**[CHẠY]** gửi **request #7** (`sort=price,desc`) → Ergonomic Chair 249.00, Monitor 27-inch 219.00,
Noise-Cancelling Headset 129.00, Mechanical Keyboard 79.99, Webcam HD 59.99.

**[CHẠY]** (tùy chọn) gửi **request #8** (không có tham số) → cả 9 sản phẩm, sort theo `name`: đây là
mặc định của `@PageableDefault`. Lưu ý: khi client gửi `sort`, nó **thay thế** sort mặc định chứ
không cộng thêm.

## Pro-tip trên slide 11 — cảnh báo trong console

**[MỞ]** console. Sau request pagination đầu tiên, có một dòng WARN (chỉ in một lần):

```text
WARN ... Serializing PageImpl instances as-is is not supported, meaning that there is no guarantee
about the stability of the resulting JSON structure! ...
```

**[NÓI]**

> Cảnh báo này liên quan đến pro-tip đầu tiên trên slide 11: **wrap pagination metadata in your own
> response DTO**. Khi trả trực tiếp `Page`, cấu trúc JSON không được đảm bảo ổn định giữa các version.
> Demo giữ cách trả này để tập trung vào pagination; với API public, mình nên có DTO riêng để kiểm
> soát response.

Ghi chú cho người trình bày — ngoài phần demo chính: cấu hình sau **cần verify khi build**
với metadata của Boot 4.1.1 trước khi dùng; review này chưa xác nhận property tồn tại:

```yaml
spring:
  data:
    web:
      pageable:
        serialization-mode: via-dto
```

Nếu property được hỗ trợ và đã verify, restart, seed lại, gửi #5; dạng DTO kỳ vọng: `{"content": [...], "page": {"size": 5, "number": 0,
"totalElements": 9, "totalPages": 2}}` ; không coi đây là kết quả đã kiểm chứng. **Không thêm vào tag demo** (tag
`step-08-pagination` không có nó).

## Câu hỏi thảo luận — sort theo field không tồn tại

**[DỰ ĐOÁN]** (tùy chọn)

> Nếu client yêu cầu sort theo một field không tồn tại, chẳng hạn `unknownField`, theo mọi người API
> hiện tại sẽ xử lý như thế nào?

**[CHẠY]** gửi **request #9** → `500`, body mặc định. Console có
`PropertyReferenceException: No property 'unknownField' found for type 'Product'`.

> Lỗi của client mà trả 500 là chưa đúng. API thật thì nên whitelist các field được sort, hoặc bắt
> exception này rồi trả 400. Hôm nay mình để nguyên làm ví dụ. Tương tự, `size` bị Spring giới hạn
> tối đa **2000** — client xin `size=1000000` cũng chỉ nhận 2000.

## Ghi chú cho người trình bày — JPA nâng cao (không đọc trong phần chính)

> Khi trao đổi thêm về JPA, mình có thể giới thiệu lý do cần kiểm tra SQL thực tế. Với entity có
> relationship, lazy loading có thể tạo thêm nhiều query, thường được nhắc đến qua vấn đề **N+1**. Nội
> dung này dành cho trao đổi thêm; trong phần chính, mình chỉ minh họa việc xem SQL qua profile dev.

Tag khi xong: `step-08-pagination`.

---

# 10:54–10:57 — (Tùy chọn) SKU trùng → `409 Conflict` / buffer #2

Ghi chú cho người trình bày: 409 là phần mở rộng tùy chọn, không có slide riêng; không đọc phần giải thích dưới đây trong `[NÓI]` chính.

**Bỏ qua nếu đang trễ quá 2 phút.** Khi bỏ qua, nói: "Bước SKU trùng mình để làm bài tập, code có ở
tag `step-09-duplicate-sku`."

**[GÕ]** `exception/DuplicateSkuException.java`:

```java
public class DuplicateSkuException extends RuntimeException {

    public DuplicateSkuException(String sku) {
        super("SKU already exists: " + sku);
    }
}
```

**[GÕ]** trong `ProductService.create`, thêm lên **đầu** method:

```java
        if (productRepository.existsBySku(request.sku())) {
            throw new DuplicateSkuException(request.sku());
        }
```

**[GÕ]** thêm vào `GlobalExceptionHandler`:

```java
    @ExceptionHandler(DuplicateSkuException.class)
    public ResponseEntity<ApiError> handleDuplicateSku(DuplicateSkuException ex) {
        return ResponseEntity
                .status(HttpStatus.CONFLICT)
                .body(new ApiError(
                        "DUPLICATE_SKU",
                        ex.getMessage(),
                        Instant.now()
                ));
    }
```

**[CHẠY]** restart → **request #1** (`201`) → **request #10** (cùng `KB-100`) → `409`:

```json
{
  "code": "DUPLICATE_SKU",
  "message": "SKU already exists: KB-100",
  "timestamp": "..."
}
```

Ghi chú cho người trình bày — chỉ dùng khi trao đổi thêm về bước 409:

> Sau khi thêm handler, request có SKU trùng trả `409 Conflict` cùng message cụ thể, giúp client hiểu
> nguyên nhân hơn so với response `500` trước đó.
>
> Cách kiểm tra bằng `existsBySku` vẫn có giới hạn khi hai request đến đồng thời: cả hai có thể cùng
> thấy SKU chưa tồn tại rồi cùng insert. Vì vậy, **unique constraint trong database** vẫn cần được giữ
> để bảo vệ dữ liệu. Đây là điểm mình có thể trao đổi thêm nếu mọi người hỏi về race condition.

---

# 10:57–11:05 — Configuration và Spring Profiles

## [Slide 12] Configuration — External configuration and Spring Profiles

**Trên slide:** *Configuration* · *External configuration and Spring Profiles* · ba mức từ trên xuống — *Environment Variables* (*Highest priority*) ·
`application-{profile}.yml` (*Environment specific*) · `application.yml` (*Default settings*); mũi
tên *OVERRIDE PRIORITY or RUNTIME SELECTION*. Bốn ý: *Externalize environment-specific config.* ·
*Do not hard-code secrets.* · *Profiles change environment-specific behavior.* · *Profiles are not
secret management.*

**[NÓI]** — hình bên trái từ trên xuống, mũi tên, rồi 4 ý:

> Đến đây, API đã có thể tạo và lấy dữ liệu. Mình chuyển sang phần **Configuration — External
> configuration and Spring Profiles**: làm sao để cùng một ứng dụng sử dụng cấu hình phù hợp ở môi
> trường dev, test và production?
>
> Mình cùng đọc ba nguồn trên hình từ trên xuống:
>
> - **Environment Variables (Highest priority)** có độ ưu tiên cao nhất trong ba nguồn được minh họa,
>   nên có thể override giá trị trong hai file bên dưới.
> - **application-{profile}.yml (Environment specific)** chứa cấu hình của profile được chọn và có thể
>   override cấu hình mặc định.
> - **application.yml (Default settings)** chứa các giá trị dùng chung.
>
> Mũi tên **OVERRIDE PRIORITY or RUNTIME SELECTION** thể hiện việc override cấu hình và chọn profile
> lúc runtime.
>
> Bốn ý bên phải là những điểm mình sẽ áp dụng:
>
> - **Externalize environment-specific config**: đưa các giá trị thay đổi theo môi trường ra khỏi
>   code.
> - **Do not hard-code secrets**: không ghi password hay API key trực tiếp vào code.
> - **Profiles change environment-specific behavior**: dùng profile để chọn hành vi phù hợp với môi
>   trường.
> - **Profiles are not secret management**: profile giúp chọn cấu hình, nhưng không thay thế cách quản
>   lý secret. Sau demo mình sẽ quay lại điểm này.

Ghi chú cho người trình bày: slide đơn giản hóa. Trong ba nguồn trên slide, environment variable cao nhất là
đúng. Nhưng tham số dòng lệnh (`--server.port=9090`) và Java system property (`-Dserver.port=9090`)
còn **cao hơn** environment variable. Chỉ nói nếu có người hỏi.

**[MỞ]** `application.yml` (đã có sẵn từ đầu):

```yaml
spring:
  datasource:
    url: ${DB_URL:jdbc:h2:mem:demo}
    username: ${DB_USERNAME:sa}
    password: ${DB_PASSWORD:}

  jpa:
    hibernate:
      ddl-auto: update
    open-in-view: false

server:
  port: ${SERVER_PORT:8080}
```

**[NÓI]**

> `${DB_URL:jdbc:h2:mem:demo}` nghĩa là: có environment variable `DB_URL` thì dùng nó, không có thì
> dùng H2 in-memory. Demo có sẵn H2 driver; database khác còn cần driver và cấu hình phù hợp.

**[DỰ ĐOÁN]**

> Nếu mình set environment variable `SERVER_PORT=9090` rồi chạy app, app chạy ở port nào?

Đáp án: `9090`. Không đụng vào file nào.

> Với password của database production, mình cũng áp dụng ý **Do not hard-code secrets**: lấy giá trị
> từ environment variable hoặc secret manager của môi trường chạy, thay vì ghi trực tiếp vào file rồi
> commit lên Git.

## Spring Profiles

**[GÕ]** chỉ thêm 2 dòng `profiles:` / `active:` ngay dưới dòng `spring:` **đã có** ở đầu `application.yml` (không gõ thêm `spring:` thứ hai, YAML trùng key thì app không start được):

```yaml
spring:
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}
```

(Giữ nguyên các dòng `datasource`, `jpa`, `server` bên dưới. YAML dùng **dấu cách**, không dùng tab.)

**[GÕ]** tạo `src/main/resources/application-dev.yml`:

```yaml
logging:
  level:
    com.example.product: DEBUG

spring:
  jpa:
    show-sql: true
```

**[GÕ]** tạo `src/main/resources/application-prod.yml`:

```yaml
logging:
  level:
    com.example.product: INFO
```

**[GÕ]** trong `ProductService`: thêm logger và một dòng log ở đầu `create`:

```java
    private static final Logger log = LoggerFactory.getLogger(ProductService.class);
```

```java
        log.debug("Creating product sku={}", request.sku());
```

Import `org.slf4j.Logger` và `org.slf4j.LoggerFactory`.

**[NÓI]**

> Profile `dev` bật log DEBUG cho code của mình và in SQL ra console. Profile `prod` chỉ log INFO.
> Default là `dev`; môi trường khác chỉ cần set `SPRING_PROFILES_ACTIVE=prod`.

**[CHẠY]** restart (run config `ProductApplication`). Chỉ vào dòng đầu log:

```text
The following 1 profile is active: "dev"
```

**[CHẠY]** gửi **request #1**. Console (đại ý):

```text
DEBUG ... c.e.product.service.ProductService : Creating product sku=KB-100
Hibernate: select p1_0.id from products p1_0 where p1_0.sku=? fetch first ? rows only
Hibernate: insert into products (active,name,price,sku,id) values (?,?,?,?,default)
```

(Dòng `select ... where p1_0.sku=?` chỉ có nếu đã làm bước 409 tùy chọn — đó là `existsBySku`.)

**[CHẠY]** (tùy chọn) chạy `seed.http`, gửi **request #5** → console có câu `select ... order by
p1_0.name ... fetch first ? rows only` và một câu `select count(p1_0.id) from products p1_0`:

> Mọi người nhìn câu SQL này nhé: database giới hạn kết quả ở 5 dòng, còn câu `count` cung cấp tổng số
> item để tính số page. Việc xem SQL giúp mình đối chiếu cách pagination được thực hiện, đúng với
> pro-tip **Inspect generated SQL** trên slide 11.

**[DỰ ĐOÁN]**

> Tiếp theo, mình sẽ chuyển sang profile `prod` và gửi lại request tạo sản phẩm. Trước khi chạy, mọi
> người thử dự đoán xem console sẽ còn in SQL và dòng DEBUG như ở profile `dev` không nhé.

**[CHẠY]** dừng app, chạy run config `ProductApplication (prod)`. Dòng đầu log:

```text
The following 1 profile is active: "prod"
```

Gửi **request #1** → `201`, console **không** có SQL, **không** có dòng DEBUG.

**[NÓI]**

> Qua hai lần chạy, mình có thể thấy cùng một code nhưng hành vi logging thay đổi theo profile. Đây là
> ý **Profiles change environment-specific behavior** trên slide. Ngoài cách chọn trong IntelliJ, mình
> cũng có thể chọn profile bằng các cách sau:

**[MỞ]** chiếu (không cần chạy):

```powershell
# Environment variable (PowerShell)
$env:SPRING_PROFILES_ACTIVE="prod"

# Maven — PowerShell cần dấu nháy quanh tham số -D
mvn spring-boot:run "-Dspring-boot.run.profiles=prod"

# File jar đã build (mvn package tạo ra target/product-api-0.0.1-SNAPSHOT.jar)
java -jar target/product-api-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod
```

> Mình quay lại ý **Profiles are not secret management**. Nếu password được ghi trong
> `application-prod.yml` rồi commit vào repo, người có quyền đọc repo vẫn có thể xem được giá trị đó.
> Tên profile `prod` không bảo vệ secret. Vì vậy, mình dùng profile để chọn hành vi và lấy secret từ
> environment variable hoặc secret manager.

**[CHẠY]** dừng app `prod`.

## [CHECKPOINT C] — 11:05

Code phải khớp tag `step-10-profiles` (tag này đã gồm cả bước 409). **Nếu đã bỏ qua bước 409, bắt buộc nhảy tag này**, kể cả profile tự gõ đã chạy đúng. Nếu profile chưa chạy đúng cũng dùng:

```powershell
git stash -u
git checkout step-10-profiles
```

Sau khi nhảy tag, code đã có 409; start app và kiểm tra profile trước khi sang testing. Phần gõ 409 là tùy chọn, trạng thái CHECKPOINT C vẫn đầy đủ.

---

# 11:05–11:06 — Giới thiệu testing

## [Slide 13] Testing Strategies — Unit vs. Integration tests

**Trên slide:** *Testing Strategies* · *Unit vs. Integration tests* · *Unit Test* — *Small and fast* · *Narrow scope, isolated class* · *Dependencies
mocked* · *Best for business logic* · *Tools*: `@Mock`, `@InjectMocks`, Mockito. *Integration Test*
— *Closer to real behavior* · *Loads the Spring application context* · *Exercises multiple layers
together* · *Slower to run* · *Tools*: `@SpringBootTest`, `@AutoConfigureMockMvc`, `MockMvc`.

**[NÓI]** — khoảng 1 phút; mỗi dòng một câu ngắn, cột trái rồi cột phải:

> Mình đã kiểm tra API qua các request mẫu. Khi code thay đổi, việc thực hiện lại các kiểm tra đó bằng
> test sẽ giúp mình phát hiện lỗi sớm hơn. Phần **Testing Strategies — Unit vs. Integration tests** so
> sánh hai cách kiểm tra; tiếp theo mình sẽ viết một ví dụ cho mỗi loại.
>
> Với **Unit Test**, ở cột bên trái:
>
> - **Small and fast**: test nhỏ và thường chạy nhanh.
> - **Narrow scope, isolated class**: mình tập trung kiểm tra một class riêng biệt.
> - **Dependencies mocked**: dependency được thay bằng mock.
> - **Best for business logic**: cách này phù hợp để kiểm tra business logic.
> - **Tools** mình dùng là `@Mock`, `@InjectMocks` và Mockito.
>
> Với **Integration Test**, ở cột bên phải:
>
> - **Closer to real behavior**: test kiểm tra hành vi gần với ứng dụng thực tế hơn.
> - **Loads the Spring application context**: test khởi tạo Spring context.
> - **Exercises multiple layers together**: test kiểm tra nhiều layer phối hợp với nhau.
> - **Slower to run**: vì phạm vi rộng hơn nên thường cần nhiều thời gian hơn.
> - **Tools** ở đây là `@SpringBootTest`, `@AutoConfigureMockMvc` và `MockMvc`.
>
> Hai loại bổ sung cho nhau: một bên kiểm tra logic riêng, một bên kiểm tra luồng xử lý qua nhiều
> thành phần.

---

# 11:06–11:11 — Unit test với Mockito

**[CHẠY]** mở `ProductService`, đặt con trỏ vào tên class → `Ctrl+Shift+T` → *Create New Test…*

Trong hộp thoại:

- *Testing library*: **JUnit5** (IntelliJ gọi chung JUnit Jupiter là "JUnit5"; project dùng JUnit 6,
  vẫn chọn mục này).
- *Class name*: `ProductServiceTest` · *Destination package*: `com.example.product.service`.
- Không tick method nào → **OK**.

> ⚠ Nếu hộp thoại hiện dòng "JUnit5 library not found in the module" kèm nút **Fix** thì **đừng bấm
> Fix** — thư viện đã có sẵn qua `spring-boot-starter-webmvc-test`; bấm Fix sẽ thêm một dependency
> JUnit khác phiên bản vào `pom.xml`. Cứ bấm OK.

IntelliJ tạo file trong `src/test/java/com/example/product/service/`.

**[GÕ]** annotation và hai field:

```java
@ExtendWith(MockitoExtension.class)
class ProductServiceTest {

    @Mock
    private ProductRepository productRepository;

    @InjectMocks
    private ProductService productService;
}
```

**[DÁN]** phần chuẩn bị dữ liệu (phần nhàm, không cần gõ):

```java
    @Test
    void getById_returnsProduct() {
        Product product = new Product();
        product.setId(1L);
        product.setName("Keyboard");
        product.setSku("KB-001");
        product.setPrice(new BigDecimal("49.99"));
        product.setActive(true);

    }
```

**[GÕ]** phần quan trọng, ngay dưới dòng `product.setActive(true);`:

```java
        when(productRepository.findById(1L))
                .thenReturn(Optional.of(product));

        ProductResponse result = productService.getById(1L);

        assertEquals("Keyboard", result.name());
        verify(productRepository).findById(1L);
```

Import (so với [Phụ lục E](#phu-luc-e)):

- `when`, `verify`: gõ `Mockito.when(...)`, rồi `Alt+Enter` trên `when` → *Add static import for
  'org.mockito.Mockito.when'*. Làm tương tự với `verify`.
- `assertEquals`: chọn **`org.junit.jupiter.api.Assertions`**. Không chọn
  `org.springframework.test.util.AssertionErrors` (thứ tự tham số khác) hay AssertJ `Assertions`.
- `@Test` từ `org.junit.jupiter.api`; `@Mock`, `@InjectMocks` từ `org.mockito`;
  `MockitoExtension` từ `org.mockito.junit.jupiter`.

**[NÓI]** — chỉ vào từng phần:

> Mình chia test thành ba phần **Arrange – Act – Assert**: chuẩn bị dữ liệu, gọi hành vi cần kiểm tra,
> rồi đối chiếu kết quả.
>
> `@Mock` tạo mock của `ProductRepository`, nên test này không cần truy cập database. `@InjectMocks`
> tạo `ProductService` rồi truyền mock vào constructor. Đây là ví dụ cho ý **Easier to test** ở slide
> 6: mình kiểm tra service mà không cần start Spring context.
>
> Ở phần chuẩn bị, `when(...).thenReturn(...)` quy định mock sẽ trả sản phẩm mẫu khi được gọi với
> `findById(1L)`. Sau đó, mình gọi method của service, dùng `assertEquals` để đối chiếu kết quả và
> `verify` để kiểm tra lời gọi tới repository mock.

**[DỰ ĐOÁN]**

> Nhìn vào các dependency trong test này, theo mọi người test có cần start Spring context hoặc kết nối
> database không? Mình cùng chạy để đối chiếu nhé.

Đáp án: không Spring, không database; chạy dưới một giây (lần đầu Mockito khởi tạo có thể mất vài trăm mili giây).

**[CHẠY]** bấm ▶ cạnh tên class → *Run 'ProductServiceTest'*. Kết quả: thanh xanh, *Tests passed: 1*.

Console có vài dòng cảnh báo màu đỏ, đại ý:

```text
Mockito is currently self-attaching to enable the inline-mock-maker. This will no longer work in
future releases of the JDK. Please add Mockito as an agent to your build ...
WARNING: A Java agent has been loaded dynamically ...
```

> Nếu console xuất hiện cảnh báo về Java agent, mình sẽ phân biệt cảnh báo đó với kết quả test. Đây là
> thông báo liên quan đến cách Mockito nạp agent trên JDK; mình vẫn cần nhìn kết quả chạy để biết test
> đã pass hay chưa. Cấu hình agent mình để ở phần ghi chú, ngoài nội dung demo chính.

**[CHẠY]** (tùy chọn, 30 giây) cho mọi người thấy test **đỏ**: sửa `"Keyboard"` trong `assertEquals` thành
`"Mouse"` → chạy → `expected: <Mouse> but was: <Keyboard>`. Sửa lại, chạy xanh.

> Khi thay đổi giá trị mong đợi và thấy test fail đúng chỗ, mình có thêm cơ sở để biết assertion đang
> kiểm tra đúng hành vi cần kiểm tra. Bây giờ mình trả lại giá trị đúng trước khi đi tiếp.

Ghi chú cho người trình bày: `MockitoExtension` chạy ở chế độ *strict stubs* — nếu `when(...)` một method
mà test không hề gọi tới, test sẽ **fail** với `UnnecessaryStubbingException`. Đây là tính năng: nó
bắt stub thừa. Nếu gõ nhầm tham số (ví dụ stub `findById(1L)` nhưng gọi `getById(2L)`), Mockito báo lỗi khác là
`PotentialStubbingProblem` (method đã stub nhưng bị gọi với tham số khác). Đọc lỗi và sửa tham số.

Tag khi xong: `step-11-unit-test`.

---

# 11:11–11:15 — Integration test với MockMvc

**[GÕ]** tạo class `ProductApiIntegrationTest` trong package test `com.example.product.api` (package
đã có sẵn trong `src/test/java`):

```java
@SpringBootTest
@AutoConfigureMockMvc
class ProductApiIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void createProduct_returns201() throws Exception {
        mockMvc.perform(post("/api/products")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content("""
                                {
                                  "name": "Keyboard",
                                  "sku": "KB-001",
                                  "price": 49.99
                                }
                                """))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.name").value("Keyboard"));
    }
}
```

(Khối JSON trong `"""` có thể **[DÁN]**.)

Import — đây là chỗ IntelliJ dễ gợi ý nhầm nhất:

| Gõ | Chọn | Không chọn |
|---|---|---|
| `@AutoConfigureMockMvc` | `org.springframework.boot.webmvc.test.autoconfigure` | (Spring Boot 3 nằm ở package khác — tài liệu cũ trên mạng) |
| `post(...)` | `MockMvcRequestBuilders.post` (static) | `RequestEntity.post` |
| `status()`, `jsonPath(...)` | `MockMvcResultMatchers` (static) | `MockRestRequestMatchers.jsonPath` |
| `MediaType` | `org.springframework.http.MediaType` | `org.apache.tomcat.util.http.parser.MediaType` |

**[NÓI]**

> `@SpringBootTest` khởi tạo application context để test các thành phần của ứng dụng cùng nhau.
> `@AutoConfigureMockMvc` cung cấp `MockMvc`, giúp mình gửi request qua `DispatcherServlet` trong môi
> trường test mà không mở port hay truyền qua mạng. Luồng xử lý vẫn đi từ JSON, validation,
> controller, service, repository đến H2.
>
> Mọi người có thể nhận thấy class test đang dùng `@Autowired` trên field. Điểm này vẫn phù hợp với ý
> **as the default** trên slide 6: code chính dùng constructor injection; còn class test ở đây được
> framework quản lý và inject `MockMvc` để phục vụ test.

**[DỰ ĐOÁN]**

> App của mình có thể vẫn đang chạy ở port 8080. Chạy test này có bị lỗi "port already in use"
> không? Và sản phẩm `KB-001` trong test có lẫn vào database của app đang chạy không?

Đáp án: không, cả hai. `MockMvc` không mở port nào. Test chạy trong một JVM riêng với H2 in-memory
riêng.

**[CHẠY]** ▶ cạnh class → xanh. Chỉ vào console:

- Spring Boot start lại từ đầu (banner, `The following 1 profile is active: "dev"`) — vì
  `application.yml` để default là `dev`.
- Có dòng `Hibernate: insert into products ...` — request thật sự xuống database.

**[NÓI]** — so thời gian chạy hai test (cột thời gian trong cửa sổ Run):

> Mình cùng nhìn thời gian thực tế của hai test trong cửa sổ Run. Unit test không cần khởi tạo Spring
> context, còn integration test cần bước đó nên thường chạy lâu hơn. Khi chọn test cho dự án, mình cân
> nhắc cả phạm vi cần kiểm tra và thời gian chạy, như ý cân bằng hai loại test trên slide 15.

**[CHẠY]** (tùy chọn) chuột phải thư mục `src/test/java` → *Run 'All Tests'* → 2 test xanh.

Ghi chú cho người trình bày: các integration test có cùng cấu hình có thể dùng chung một Spring context (Spring cache context giữa các test) và **không rollback** dữ liệu sau mỗi test MockMvc. Vì vậy mỗi test tạo dữ liệu nên dùng SKU
riêng — branch `solution` có thêm test cho 400, 404, 409 và test 409 dùng SKU `IT-DUP-001` vì lý do
này. Nếu ai hỏi: xem `02-qa-bank-vi.md`.

Tag khi xong: `step-12-integration-test`.

> Nếu tới 11:13 mà integration test chưa xanh: dừng, nói "code đầy đủ ở tag `step-12-integration-test`", **mở** file
> ở cửa sổ dự phòng, giải thích, và chuyển sang tổng kết đúng 11:15.

---

# 11:15–11:19 — Tổng kết

**Bắt đầu đúng 11:15, dù demo đang ở đâu.**

Nhảy slide trong chế độ trình chiếu PowerPoint: gõ **số slide rồi Enter** (ví dụ `4` Enter).

## [Slide 4] Nhìn lại request flow (1 phút)

**Trên slide:** *Architecture* · *The request flow*: Client → DTO + Validation → Controller → Service → Repository → Database; Exception → @RestControllerAdvice → Standard Error.

**[NÓI]** — chỉ vào từng hộp trên slide, nói file tương ứng:

> Mình quay lại **Architecture — The request flow** để cùng nhìn lại những phần vừa thực hiện:
>
> - **Client** gửi JSON qua **DTO + Validation** đến **Controller**. `ProductController` tiếp nhận dữ
>   liệu và gọi service.
> - **Service** là `ProductService`, nơi xử lý nghiệp vụ, quản lý transaction và map entity với DTO.
> - **Repository** là `ProductRepository`, cung cấp các thao tác truy cập dữ liệu, gồm cả query với
>   `Pageable`.
> - **Database** là H2, với bảng được tạo từ entity `Product`.
> - Các **Exception** đã khai báo handler được xử lý qua **@RestControllerAdvice** trong
>   `GlobalExceptionHandler`, trả **Standard Error** dưới dạng `ApiError`.
>
> Cuối cùng, unit test và integration test giúp mình kiểm tra logic riêng và luồng xử lý tổng thể.

## [Slide 14] Common Mistakes — quiz trong chat (2 phút)

**Trên slide:** *Common Mistakes* · *Bad vs. Better* · bảng *Mistake | Bad | Better* — *Business logic in Controller* (logic inside
`@RestController` → Delegate to Service) · *Returning Entity* (`return product;` → Return
`ProductResponse` DTO) · *Catch-all errors* (`catch (Exception e)` → Typed exception →
`@RestControllerAdvice`) · *Unbounded query* (`repo.findAll()` → `repo.findAll(pageable)`) ·
*Hard-coded config* (Production URL in code → Externalized configuration).

**[NÓI]**

> Ở slide **Common Mistakes — Bad vs. Better**, mình cùng đối chiếu năm tình huống với code vừa viết.
> Nếu tiện, mọi người có thể chọn một dòng và gửi vào chat tên file đã xử lý tình huống đó. Mình sẽ
> lần lượt đi qua cả năm dòng.

Đợi khoảng 10 giây rồi nói lần lượt cả 5 dòng (giữ phần này trong 2 phút):

**[NÓI]**

>
> - Với **Business logic in Controller**, cột Bad là **Logic inside @RestController**. Hướng Better là
>   **Delegate to Service**: mình chuyển business logic sang service và để controller xử lý HTTP.
> - Với **Returning Entity**, thay vì **return product;**, mình chọn **Return ProductResponse DTO** để
>   kiểm soát dữ liệu trả về.
> - Với **Catch-all errors**, thay vì gom vào **catch (Exception e)**, mình dùng **Typed exception →
>   @RestControllerAdvice** để xử lý từng loại lỗi rõ ràng.
> - Với **Unbounded query**, mình thay **repo.findAll()** bằng **repo.findAll(pageable)** để giới hạn
>   kết quả mỗi request.
> - Với **Hard-coded config**, mình chuyển **Production URL in code** sang **Externalized
>   configuration**, để giá trị được lấy từ cấu hình của môi trường.

Đáp án để đối chiếu:

| Mistake (slide) | Bad → Better (slide) | Hôm nay tránh ở đâu |
|---|---|---|
| Business logic in Controller | Logic inside `@RestController` → Delegate to Service | `ProductController` chỉ gọi `productService` |
| Returning Entity | `return product;` → Return `ProductResponse` DTO | Trả `ProductResponse`, nhận `CreateProductRequest` |
| Catch-all errors | `catch (Exception e)` → Typed exception → `@RestControllerAdvice` | `ProductNotFoundException`, `DuplicateSkuException` + `GlobalExceptionHandler` |
| Unbounded query | `repo.findAll()` → `repo.findAll(pageable)` | `ProductService.getProducts`: default 10 dòng, tối đa 2000 |
| Hard-coded config | Production URL in code → Externalized configuration | `${DB_URL:...}` trong `application.yml`, profile `dev` / `prod` |

Ghi chú cho người trình bày — nếu có người hỏi "vậy có nên thêm `@ExceptionHandler(Exception.class)` vào
advice không?": có thể có một handler cuối cùng cho lỗi **không lường trước** (log đầy đủ, trả 500
chung chung). Nhưng viết thẳng như vậy sẽ bắt luôn các lỗi 4xx mà Spring đang xử lý đúng (JSON sai
format → 400, sai method → 405, thiếu Content-Type → 415) và biến chúng thành 500. Cách đúng là
cho advice kế thừa `ResponseEntityExceptionHandler`. Chi tiết trong `02-qa-bank-vi.md`.

## [Slide 15] Five Things to Remember (1 phút)

**Trên slide:** *Five Things to Remember* · 1. *Keep controllers thin* — Business logic lives in the service layer. 2. *Define
strict boundaries* — DTOs keep the API contract separate from the persistence entity. 3. *Validate at
the edge* — Check input at the boundary and fail with consistent, structured errors. 4. *Design for
environments* — Externalize configuration and prepare for scale. 5. *Test behavior, not just syntax*
— Use a balanced mix of isolated unit tests and contextual integration tests.

**[NÓI]** — khoảng 1 phút, mỗi ý một câu, gắn lại với code vừa viết:

> Để khép lại phần demo, mình xin nhắc lại **Five Things to Remember**, gắn với những gì vừa thực
> hiện:
>
> 1. **Keep controllers thin**: để business logic ở service layer, còn controller tập trung xử lý
>    HTTP.
> 2. **Define strict boundaries**: dùng DTO để tách API contract khỏi persistence entity.
> 3. **Validate at the edge**: kiểm tra input tại boundary và trả lỗi có cấu trúc nhất quán.
> 4. **Design for environments**: externalize configuration để ứng dụng phù hợp với từng môi trường và
>    có thể tiếp tục mở rộng.
> 5. **Test behavior, not just syntax**: kết hợp unit test kiểm tra logic riêng với integration test
>    kiểm tra các thành phần hoạt động cùng nhau.
>
> Mọi người có thể dùng năm ý này khi xem lại Product API hoặc đối chiếu với API trong dự án của mình.

## Ghi chú cho người trình bày — API này đã "production-ready" chưa?

Không đọc danh sách này trong khung tổng kết 4 phút; chỉ dùng khi có câu hỏi hoặc trao đổi sau buổi.

> Về chữ **production-ready** trên slide đầu, demo này mới minh họa các phần nền của một REST API.
> Trước khi dùng trong production, mình cần hoàn thiện thêm theo yêu cầu của dự án, chẳng hạn:

- Header `Location` trong response `201`; thêm `PUT` / `DELETE`.
- Flyway hoặc Liquibase để quản lý schema, thay cho `ddl-auto: update`.
- Spring Security (authentication, authorization).
- DTO riêng cho kết quả pagination, thay vì trả thẳng `Page`.
- Handler cho các lỗi của framework (JSON sai, `sort` không hợp lệ đang ra 500) — giới hạn field được
  sort.
- Bắt `DataIntegrityViolationException` để trường hợp SKU trùng do hai request đồng thời cũng ra
  `409`.
- Actuator (health check, metrics), database thật + Testcontainers trong test, CI chạy test mỗi lần
  push.

---

# 11:19–11:27 — Q&A

## [Slide 16] Questions?

**Trên slide:** *Questions?* · *Thank you for joining the session.*

**[NÓI]**

> Mình chuyển sang phần **Questions?** để cùng trao đổi thêm. Nếu có câu hỏi từ phần vừa rồi, hoặc
> muốn chia sẻ cách team đang áp dụng, mọi người có thể bật mic hoặc gửi vào chat nhé.
>
> **Thank you for joining the session** — cảm ơn mọi người đã dành thời gian theo dõi và cùng tham
> gia. Mình xin bắt đầu với những câu hỏi đã có trong chat.

- Trả lời các câu trong "parking lot" (câu hỏi gom từ chat trong buổi) trước.
- Mỗi câu tối đa 2 phút. Câu dài/lạc đề: "Để giữ đủ thời gian cho các câu hỏi còn lại, mình xin ghi lại phần này và trao đổi thêm sau buổi nhé."
- Không chắc: "Phần này mình cần kiểm tra lại để trả lời chính xác. Mình ghi nhận câu hỏi và sẽ phản hồi vào group chat sau buổi nhé." Ghi tên
  người hỏi.
- Không ai hỏi sau 10 giây: dùng câu gợi ý ở [Phụ lục C](#phu-luc-c).
- Câu khó thường gặp và câu trả lời ngắn: [Phụ lục D](#phu-luc-d); bản đầy đủ
  ở `trainer-prep/02-qa-bank-vi.md`.

Đang trễ giờ: rút Q&A còn 5 phút. 11:27 luôn chuyển sang kết thúc.

---

# 11:27–11:30 — Kết thúc

**[NÓI]**

> Trước khi kết thúc, mình xin gửi lại source code, gợi ý để mọi người tự thử thêm và link feedback.
> Mình sẽ để các link trong chat để mọi người tiện xem lại sau buổi.

1. **Source code:** dán link repo vào chat (đã chuẩn bị tối hôm trước). Branch `main` là bản khung,
   tag `step-01-entity` … `step-12-integration-test` là từng bước hôm nay, branch `solution` là bản hoàn chỉnh kèm thêm test.
2. **Nếu ai muốn thử tay:** thêm `PUT /api/products/{id}` — cập nhật sản phẩm; trả `404` nếu không tồn
   tại, `409` nếu SKU mới trùng với **sản phẩm khác**; viết một unit test và một integration test.
   Gợi ý ở [Phụ lục B](#phu-luc-b) (gửi kèm trong chat).
3. **Feedback:** dán link form feedback vào chat — "mọi người góp ý giúp mình 2 phút, buổi sau mình
   làm tốt hơn."

> Cảm ơn mọi người đã tham gia và trao đổi cùng mình. Nếu còn câu hỏi chưa kịp nói trong buổi này, mọi
> người có thể nhắn lại; mình sẽ tổng hợp và phản hồi sau nhé.

**Sau khi dừng share màn hình:** `git stash list` — nếu có stash từ các lần nhảy tag, giữ lại để xem
sau. Trả lời các câu hỏi đã hứa trả lời sau trong ngày.

---

<a id="phu-luc-a"></a>

# Phụ lục A — Danh sách cắt khi trễ giờ

Cắt theo thứ tự từ trên xuống. Các mục cuối bảng chỉ dùng khi trễ nặng.

| # | Cắt gì | Ở đâu | Tiết kiệm | Khi cắt, nói gì |
|---|---|---|---|---|
| 0 | Demo start.spring.io (cắt ngay nếu tới slide 5 đã trễ, vì đây là phần đầu tiên có thể cắt) | 09:38 | 2 phút | Mở `pom.xml` của starter khoảng 20 giây: "Project kiểu này tạo từ start.spring.io; các starter ở đây là các dependency mình chọn." |
| 1 | Bước SKU trùng → 409 | 10:54 | 3 phút | "Bước này để làm bài tập, code ở tag `step-09-duplicate-sku`." (CHECKPOINT C `step-10-profiles` đã có sẵn bước này.) |
| 2 | Request #8 (không tham số) và các request sort thêm | 10:44 | 1 phút | Không cần nói gì |
| 3 | Trao đổi thêm về page DTO (chỉ sau khi verify) | 10:44 | 2 phút | "Page DTO có trong tài liệu." |
| 4 | Demo `/api/products/abc` (400 mặc định) | 10:37 | 1 phút | Không cần nói gì |
| 5 | Test đỏ trong phần unit test | 11:06 | 30 giây | Không cần nói gì |
| 6 | Giải thích chi tiết `@Transactional`, `readOnly` | 10:04, 10:37 | 1 phút | "Transaction là chủ đề riêng; hôm nay chỉ cần biết nó bao một nghiệp vụ." |
| 7 | Gõ test | 11:06–11:15 | 5 phút | Nhảy tag `step-12-integration-test`, **mở** hai file test, giải thích, rồi chạy |
| 8 | Chạy profile `prod` | 10:57 | 2 phút | Chỉ chiếu hai file `application-*.yml` và giải thích |

Không cắt: CHECKPOINT A–C, validation 400, 404, pagination cơ bản, một unit test và một integration
test (dù chỉ mở file có sẵn), tổng kết 11:15.

---

<a id="phu-luc-b"></a>

# Phụ lục B — Bài tập PUT (kèm gợi ý)

**Yêu cầu:** `PUT /api/products/{id}` với body giống `POST` (`name`, `sku`, `price`).

| Trường hợp | Kết quả mong đợi |
|---|---|
| Thành công | `200 OK` + `ProductResponse` đã cập nhật |
| Body không hợp lệ | `400` + `VALIDATION_ERROR` |
| `id` không tồn tại | `404` + `PRODUCT_NOT_FOUND` |
| SKU mới đã thuộc về **sản phẩm khác** | `409` + `DUPLICATE_SKU` |
| Giữ nguyên SKU của chính nó | `200` (không phải 409) |

**Gợi ý:**

1. Tạo DTO riêng `UpdateProductRequest` (có thể giống hệt `CreateProductRequest` lúc đầu). Tách riêng
   vì hai contract sẽ khác nhau theo thời gian (ví dụ update cho phép đổi `active`).
2. Repository: thêm derived query `boolean existsBySkuAndIdNot(String sku, Long id);` — "có sản phẩm
   **khác** dùng SKU này không?".
3. Service trong `@Transactional`: `findById(id).orElseThrow(...)` → kiểm tra SKU → gọi setter. Không
   cần gọi `save()`: entity lấy ra trong transaction được Hibernate theo dõi (*dirty checking*), thay
   đổi được `UPDATE` khi transaction commit.
4. Controller: `@PutMapping("/{id}")`, `@PathVariable Long id`, `@Valid @RequestBody`.
5. Test: unit test cho trường hợp 409 (`when(existsBySkuAndIdNot(...)).thenReturn(true)` rồi
   `assertThrows`); integration test cho `200` và `404`. Dùng SKU riêng cho mỗi test.

Nâng cao: thêm `@Version` vào entity (optimistic locking) — hai người cùng sửa một sản phẩm thì người
sau nhận lỗi thay vì âm thầm override (lost update).

---

<a id="phu-luc-c"></a>

# Phụ lục C — Câu hỏi gợi ý khi chưa ai hỏi

Hỏi mọi người, đợi 10 giây, không ai trả lời thì tự trả lời:

1. "Nếu mình quên `@Valid` trong controller, request #2 sẽ ra gì?" — `201`, dữ liệu rác vào database.
2. "Vì sao `ProductService` không cần `@Autowired`?" — chỉ có một constructor, Spring tự dùng nó.
3. "Sort theo field không tồn tại ra 500 — nên sửa thế nào?" — giới hạn field được sort (allow-list)
   hoặc thêm handler phù hợp sau khi verify exception thực tế → 400.
4. "Unit test và integration test, cái nào bắt được lỗi quên `@Valid`?" — integration test có gửi body sai
   (unit test gọi thẳng service, không đi qua controller nên không thấy `@Valid`).
5. "Mọi người đang dùng Spring Boot phiên bản nào ở dự án? Đã gặp chuyện gì khi nâng cấp?"
6. "Ở dự án của mọi người, lỗi API đang trả về format thế nào?"

---

<a id="phu-luc-d"></a>

# Phụ lục D — Câu hỏi khó, trả lời ngắn

Bản đầy đủ (có giải thích bên dưới và câu follow-up): `trainer-prep/02-qa-bank-vi.md`.

| Câu hỏi | Trả lời ngắn |
|---|---|
| Có hai bean cùng kiểu thì sao? | App không start được: *"required a single bean, but 2 were found"*. Chọn bằng `@Primary`, `@Qualifier`, hoặc inject `List<...>` nếu cần tất cả. |
| Singleton có an toàn khi nhiều request cùng lúc không? | An toàn vì service/repository của mình **không giữ state** (chỉ có field `final` trỏ tới bean khác). Nếu thêm field lưu dữ liệu theo request thì không an toàn. |
| Sao không trả entity cho nhanh? | Lộ cấu trúc DB, client gửi được field không được phép (`id`, `active`), đổi DB là đổi API. `CreateProductRequest` chỉ dùng cho tạo mới; update nên có DTO riêng. |
| PUT khác PATCH thế nào? | PUT gửi **toàn bộ** tài nguyên, gửi lặp lại cho cùng kết quả (idempotent). PATCH gửi **một phần** thay đổi; không đảm bảo idempotent. |
| `@Valid` khác `@Validated`? | `@Valid` (Jakarta) kích hoạt validation cho object/field lồng nhau. `@Validated` (Spring) hỗ trợ validation groups và validation ở mức method của bean. Trên `@RequestBody`, dùng cái nào cũng được. |
| Có nên `@ExceptionHandler(Exception.class)`? | Làm handler cuối cho lỗi không lường trước thì được, nhưng phải kế thừa `ResponseEntityExceptionHandler`, không thì các lỗi 4xx của Spring (405, 415, JSON sai) biến thành 500. |
| `existsBySku` rồi mới insert có an toàn không? | Không tuyệt đối: hai request đồng thời có thể cùng qua bước kiểm tra. Unique constraint trong DB mới là chốt chặn; bắt `DataIntegrityViolationException` để trả 409. |
| Trả thẳng `Page` có vấn đề gì? | Spring Data in WARN một lần: cấu trúc JSON không đảm bảo ổn định. Dùng DTO riêng. Property `serialization-mode: via-dto` và JSON tương ứng cần verify khi build trước khi demo. |
| `sort=unknownField` thì sao? | Hiện ra `500` (`PropertyReferenceException`). Nên giới hạn field được sort hoặc trả 400. |
| N+1 là gì, sửa bằng `EAGER` được không? | Một query lấy danh sách + N query lấy quan hệ. `EAGER` **không** sửa được (thường còn tệ hơn). Dùng `JOIN FETCH`, `@EntityGraph` hoặc batch size. |
| `ddl-auto: update` dùng cho production được không? | Không. Dùng Flyway/Liquibase, `ddl-auto: validate` hoặc `none`. |
| Environment variable có phải ưu tiên cao nhất? | Cao nhất trong ba nguồn trên slide. Tham số dòng lệnh (`--server.port`) và `-D` còn cao hơn. |
| Sao test chạy được khi app đang chạy ở 8080? | `@SpringBootTest` mặc định dùng môi trường MOCK: không mở port; JVM và H2 riêng. |
| `@MockBean` đâu rồi? | Bị bỏ ở Spring Boot 4. Dùng `@MockitoBean` (`org.springframework.test.context.bean.override.mockito`). |
| `@Transactional` trên method private có tác dụng không? | Không. Spring dùng proxy; method private và lời gọi nội bộ trong cùng class (`this.x()`) không đi qua proxy. |
| Checked exception có làm rollback không? | Mặc định **không** — chỉ `RuntimeException` và `Error`. Muốn rollback thì `@Transactional(rollbackFor = Exception.class)`. |

---

<a id="phu-luc-e"></a>

# Phụ lục E — Bảng import đúng

Cột phải là những tên dễ nhầm khi đọc tài liệu hoặc dùng IDE. Việc tên nào thực sự có trên
classpath Boot 4.1.1 **cần verify khi build**; không mặc định IDE luôn gợi ý tất cả.

| Dùng | Import đúng | Hay bị chọn nhầm |
|---|---|---|
| `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column` | `jakarta.persistence.*` | `org.springframework.data.annotation.Id` (lỗi Hibernate "no identifier" lúc khởi động), tài liệu Hibernate cũ dùng annotation `Table` riêng (không dùng cho demo) |
| `@NotBlank`, `@Size`, `@NotNull`, `@Positive` | `jakarta.validation.constraints.*` | — |
| `@Valid` | `jakarta.validation.Valid` | — |
| `@Transactional` | `org.springframework.transaction.annotation.Transactional` | `jakarta.transaction.Transactional` (không có `readOnly`) |
| `Page`, `Pageable` | `org.springframework.data.domain.*` | `org.hibernate.query.Page`, `java.awt.print.Pageable` |
| `@PageableDefault` | `org.springframework.data.web.PageableDefault` | — |
| `Logger`, `LoggerFactory` | `org.slf4j.*` | `java.util.logging.Logger` |
| `HttpStatus`, `ResponseEntity`, `MediaType` | `org.springframework.http.*` | `org.apache.tomcat.util.http.parser.MediaType` |
| `@Test` | `org.junit.jupiter.api.Test` | — |
| `assertEquals` | `org.junit.jupiter.api.Assertions.assertEquals` (static) | `org.springframework.test.util.AssertionErrors.assertEquals` (3 tham số), AssertJ `Assertions` |
| `@Mock`, `@InjectMocks` | `org.mockito.*` | — |
| `MockitoExtension` | `org.mockito.junit.jupiter.MockitoExtension` | — |
| `when`, `verify` | `org.mockito.Mockito.*` (static) | — |
| `@AutoConfigureMockMvc` | `org.springframework.boot.webmvc.test.autoconfigure.AutoConfigureMockMvc` | package của Spring Boot 3 (không còn) |
| `post`, `get` | `org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*` (static) | `org.springframework.http.RequestEntity.post` |
| `status`, `jsonPath` | `org.springframework.test.web.servlet.result.MockMvcResultMatchers.*` (static) | `org.springframework.test.web.client.match.MockRestRequestMatchers.jsonPath` |
