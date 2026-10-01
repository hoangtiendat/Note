# Tài liệu ôn tập cho người trình bày — Java Spring Boot

Tài liệu này giúp người dạy ôn lại kiến thức nền **trước** buổi học. Nó đi theo đúng thứ tự của
kịch bản `spring-boot-live-demo-script-vi.md` và chỉ nói về những gì code demo thực sự dùng.

Mỗi mục có bốn phần:

| Phần | Dùng để |
|---|---|
| **Giải thích trong lớp** | Câu nói đơn giản, có thể nói gần nguyên văn trên Zoom |
| **Bên dưới** | Điều thực sự xảy ra bên trong framework. Để mình hiểu và trả lời tự tin, không cần nói hết trong lớp |
| **Hiểu lầm** | Những điều học viên (và cả người dạy) hay nghĩ sai, kèm cách sửa |
| **Câu hỏi follow-up** | Câu hỏi học viên có thể hỏi tiếp, kèm câu trả lời ngắn |

Phiên bản trong demo: Spring Boot 4.1.1, Spring Framework 7, Hibernate 7, Jackson 3, JUnit 6,
Mockito 5, Java 21. Rất nhiều bài viết trên mạng vẫn viết cho Spring Boot 2/3 — những chỗ khác nhau
được gom ở [C15](#c15-spring-boot-4--spring-framework-7--những-gì-khác-với-tài-liệu-cũ-trên-mạng).

Tài liệu liên quan:

- `02-qa-bank-vi.md` — kho câu hỏi của học viên, trả lời chi tiết hơn.
- `03-risks-and-rehearsal-vi.md` — rủi ro khi demo live và kế hoạch tập dượt.
- `04-cheat-sheet-vi.md` — một trang để mở cạnh IDE lúc dạy.
- `05-advanced-spring-architect-vi.md` — kiến thức nâng cao cho vai trò Tech Lead / Solution Architect (không cần cho buổi demo).

Cách ôn gợi ý (khoảng 3–4 giờ, chia làm 2–3 buổi):

1. Đọc lướt Phần A. Dừng ở mục nào thấy không tự giải thích được thì đọc kỹ.
2. Đọc kỹ Phần B và Phần C, mỗi mục đọc to phần "Giải thích trong lớp" một lần.
3. Mở code ở branch `solution` song song; tìm đúng dòng code mà mỗi mục nói tới.
4. Che phần trả lời của "Câu hỏi follow-up", tự trả lời, rồi so lại.

---

# Phần A — Ôn lại Java nền tảng (những gì code demo thực sự dùng)

> Mục tiêu phần này: đọc bất kỳ dòng nào trong code demo mà **giải thích được nó là gì ở mức Java
> thuần**, trước khi nói đến Spring. Rất nhiều câu hỏi "khó" của học viên thực ra là câu hỏi Java.

## A1. Annotation — "nhãn dán", tự nó không làm gì cả

**Trong demo:** `@Entity`, `@Service`, `@Valid`, `@Transactional`, `@Test`… ở khắp nơi.

**Giải thích trong lớp:**
> Annotation chỉ là metadata — một cái nhãn dán lên class, field, hay method. Bản thân nó không
> chạy code gì. Có một "người đọc nhãn" — ở đây là Spring, Hibernate, Jackson, JUnit — đọc các
> nhãn này lúc khởi động (bằng reflection) và quyết định phải làm gì.

**Bên dưới:** annotation có `@Retention(RUNTIME)` được giữ lại trong file `.class`; framework dùng
reflection (`Class.getAnnotations()`…) hoặc đọc metadata của bytecode để tìm chúng. Ví dụ Spring
khi scan package sẽ tìm các class có `@Component` (và các annotation "kế thừa" nó như `@Service`).

**Hiểu lầm:** "Thêm `@Transactional` là Java tự mở transaction." → Sai. Chỉ khi object được Spring
quản lý (bean) và được gọi **qua proxy** thì Spring mới đọc nhãn đó và mở transaction (xem C8).

## A2. `record` (Java 16+) — class mang dữ liệu, bất biến

**Trong demo:** `CreateProductRequest`, `ProductResponse`, `ApiError`.

```java
public record ProductResponse(Long id, String name, String sku, BigDecimal price, boolean active) {}
```

Compiler tự sinh ra:
- các field `private final` cho từng component;
- **canonical constructor** `ProductResponse(Long id, String name, ...)`;
- accessor tên **giống hệt component**: `response.name()` — **không phải** `getName()`;
- `equals()`, `hashCode()`, `toString()` dựa trên toàn bộ component.

**Giải thích trong lớp:**
> Record là cách Java viết một "data carrier" ngắn gọn: khai báo các field một lần, Java tự sinh
> constructor, accessor, equals/hashCode/toString. Nó bất biến — tạo xong là không đổi được — rất
> hợp với DTO, vì DTO chỉ mang dữ liệu qua ranh giới API.

**Bẫy khi gõ live:** gọi `request.getName()` → compile error. Phải là `request.name()`.

**Hiểu lầm thường gặp:**
- "Record = Lombok `@Data`." → Không. `@Data` sinh cả setter (mutable). Record gần với `@Value`
  của Lombok hơn (bất biến, không setter).
- "Record bất biến hoàn toàn." → Chỉ **shallow immutable**: field là `final`, nhưng nếu component
  là một `List` mutable thì nội dung list vẫn đổi được.

**Câu hỏi follow-up:**
- *"Sao entity `Product` không dùng record?"* → JPA/Hibernate cần: constructor không tham số, có thể
  thay đổi state (setter/dirty checking), và class không `final` để tạo proxy lazy-loading. Record
  là `final`, không có no-arg constructor, không đổi state được → không hợp làm entity. Record rất
  hợp làm DTO và projection.
- *"Annotation `@NotBlank` đặt trên component của record thì validate được không?"* → Được.
  Annotation trên record component được compiler "truyền" xuống field/parameter/accessor tương ứng
  (tùy `@Target` của annotation), và Hibernate Validator đọc được. Demo chính là ví dụ.
- *"Jackson có deserialize được record không?"* → Được, Jackson hỗ trợ record từ 2.12 (Spring Boot 4
  dùng Jackson 3 — vẫn hỗ trợ): nó gọi canonical constructor, map JSON field theo tên component.

## A3. Generics — `<Product, Long>` nghĩa là gì

**Trong demo:** `JpaRepository<Product, Long>`, `Optional<Product>`, `Page<ProductResponse>`,
`ResponseEntity<ProductResponse>`.

**Giải thích trong lớp:**
> `JpaRepository<Product, Long>` nghĩa là: repository này làm việc với entity `Product`, và kiểu của
> khóa chính là `Long`. Nhờ vậy `findById` nhận `Long` và trả về `Optional<Product>` — compiler kiểm
> tra kiểu giúp chúng ta.

**Bên dưới:** Java có *type erasure* — lúc chạy, `List<String>` và `List<Integer>` là cùng một class.
Nhưng kiểu generic **khai báo trong định nghĩa class/interface** (như `extends JpaRepository<Product,
Long>`) vẫn được lưu trong metadata của bytecode, nên Spring Data đọc được bằng reflection và biết
repository này dành cho entity nào.

**Câu hỏi bẫy:** *"Type erasure xóa kiểu rồi, sao Spring biết là `Product`?"* → Trả lời như trên:
erasure xóa kiểu của *biến/đối tượng lúc chạy*, không xóa kiểu generic trong *khai báo* của interface.

## A4. Lambda và method reference

**Trong demo:**

```java
.orElseThrow(() -> new ProductNotFoundException(id))      // lambda: Supplier<ProductNotFoundException>
.map(this::toResponse)                                     // method reference: Function<Product, ProductResponse>
.map(error -> error.getField() + ": " + error.getDefaultMessage())  // lambda: Function<FieldError, String>
```

**Giải thích trong lớp:**
> Lambda là một "hàm nhỏ viết tại chỗ" truyền vào như một tham số. `this::toResponse` là cách viết
> ngắn của `product -> this.toResponse(product)`.

**Điểm cần hiểu:** lambda chỉ dùng được ở nơi cần một *functional interface* (interface có đúng một
method abstract): `Supplier`, `Function`, `Predicate`…

**Bẫy/câu hỏi:**
- *"Sao `orElseThrow` nhận lambda mà không nhận thẳng `new ProductNotFoundException(id)`?"* →
  Vì là `Supplier` — exception chỉ được **tạo khi thật sự không tìm thấy** (lazy). Tương tự
  `orElse(x)` luôn tính `x` trước (eager), còn `orElseGet(() -> x)` chỉ tính khi cần.
- Biến dùng trong lambda phải *effectively final* (`id` ở đây là tham số, không bị gán lại → OK).

## A5. `Optional<T>`

**Trong demo:** `productRepository.findById(id)` trả `Optional<Product>`.

**Giải thích trong lớp:**
> `Optional` là cái hộp có thể rỗng. `findById` trả về hộp thay vì `null`, buộc chúng ta phải
> quyết định: nếu rỗng thì làm gì — ở đây là ném `ProductNotFoundException`.

**Hiểu lầm / best practice:**
- Đừng gọi `.get()` khi chưa kiểm tra — sẽ ném `NoSuchElementException` (thành lỗi 500).
- Optional được thiết kế cho **giá trị trả về**; không nên dùng làm field của entity hay tham số
  method (khuyến nghị phổ biến, không phải luật cứng).

## A6. Stream API

**Trong demo:** gom lỗi validation thành một chuỗi:

```java
ex.getBindingResult().getFieldErrors()   // List<FieldError>
  .stream()                              // Stream<FieldError>
  .map(error -> error.getField() + ": " + error.getDefaultMessage())   // Stream<String>
  .sorted()                              // Stream<String>, thứ tự cố định (xem C5)
  .collect(Collectors.joining(", "));   // String
```

**Giải thích:** `stream()` → các bước biến đổi (`map`, `filter`… — *intermediate*, lazy) → một bước
kết thúc (`collect`, `toList`… — *terminal*) mới thực sự chạy.

**Lưu ý dễ nhầm:** `productRepository.findAll(pageable).map(this::toResponse)` **không phải** Stream.
Đó là method `map` của `Page` (Spring Data): biến `Page<Product>` thành `Page<ProductResponse>` và
**giữ nguyên metadata phân trang** (totalElements, totalPages…).

## A7. Exception: checked vs unchecked

**Trong demo:** `ProductNotFoundException extends RuntimeException` (unchecked).

**Giải thích trong lớp:**
> Checked exception (kế thừa `Exception`) bắt buộc phải `throws` hoặc `try/catch` ở mọi tầng. Unchecked
> (kế thừa `RuntimeException`) thì không. Với lỗi nghiệp vụ như "không tìm thấy product", mình dùng
> unchecked để nó "bay" thẳng từ Service lên `@RestControllerAdvice` mà không làm bẩn chữ ký của mọi
> method ở giữa.

**Liên quan Spring (hay bị hỏi):** mặc định `@Transactional` **rollback với `RuntimeException` và
`Error`**, **không** rollback với checked exception (trừ khi khai báo `rollbackFor`). Đây là thêm một
lý do domain exception thường là unchecked.

**Câu hỏi:** *"Sao test method có `throws Exception`?"* → Vì `mockMvc.perform(...)` khai báo `throws
Exception` (checked). Trong test, khai báo `throws Exception` là cách gọn nhất; nếu có exception thì
test fail — đúng điều mình muốn.

## A8. `BigDecimal` — tiền không dùng `double`

**Trong demo:** `price` là `BigDecimal` ở cả DTO lẫn entity.

**Giải thích trong lớp:**
> `double` là số nhị phân dấu phẩy động, không biểu diễn chính xác được nhiều số thập phân: `0.1 + 0.2`
> ra `0.30000000000000004`. Với tiền, sai một chút cũng là sai. `BigDecimal` lưu số thập phân chính xác.

**Những điểm hay bị hỏi / bẫy:**
- `new BigDecimal("49.99")` ✅ chính xác. `new BigDecimal(49.99)` ❌ mang theo sai số của `double`.
  `BigDecimal.valueOf(49.99)` dùng được (đi qua `Double.toString`).
- **`equals` so sánh cả scale**: `new BigDecimal("50").equals(new BigDecimal("50.00"))` là **false**.
  Muốn so giá trị thì dùng `compareTo(...) == 0`. Quan trọng khi viết assert trong test.
- **Scale khi lưu DB**: Hibernate mặc định map `BigDecimal` thành cột `numeric(38,2)` (precision 38,
  scale 2) → POST `50` có thể trả `50` (object trong bộ nhớ), nhưng GET lại từ DB trả `50.00`.
  (Chi tiết ở phần C3.)

## A9. Text block `"""` (Java 15+)

**Trong demo:** JSON trong integration test.

```java
.content("""
        {
          "name": "Keyboard",
          "sku": "KB-001",
          "price": 49.99
        }
        """)
```

Text block cho phép viết chuỗi nhiều dòng không cần `\n` và escape `"`. Java tự bỏ phần thụt lề
chung (dựa vào dòng thụt ít nhất, kể cả dòng `"""` đóng).

## A10. `static import` — vì sao code test trông như "phép thuật"

```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.mockito.Mockito.when;
```

Nhờ static import mà viết `post("/api/products")`, `status().isCreated()`, `when(...)` thay vì
`MockMvcRequestBuilders.post(...)`. **Bẫy khi gõ live:** IntelliJ có thể gợi ý nhầm class có method
cùng tên — luôn nhìn package trước khi Enter (xem bảng import đúng ở Phụ lục E của kịch bản, bản rút gọn trong cheat sheet).

## A11. Quy ước JavaBean: getter/setter, `isActive()`

- Hibernate và Jackson đều hiểu quy ước JavaBean: `getName()`/`setName()` ↔ property `name`.
- Với `boolean` primitive, getter chuẩn là `isActive()` ↔ property `active`.
- `boolean active = true` (primitive) không bao giờ `null`; `Boolean` (wrapper) có thể `null`.
- JPA cần **constructor không tham số** (public hoặc protected). Class `Product` không khai báo
  constructor nào → Java tự có constructor mặc định không tham số → OK.

## A12. `final` field và constructor

```java
private final ProductRepository productRepository;
public ProductService(ProductRepository productRepository) { this.productRepository = productRepository; }
```

`final` = gán đúng một lần, trong constructor. Không thể quên inject (compile sẽ báo nếu thiếu gán),
không ai đổi được sau đó. Đây là nền tảng của câu "fields can be immutable" trên slide 6.

## A13. Vì sao Java 21?

- Java 21 là bản **LTS** (Long-Term Support) — doanh nghiệp thường chọn LTS.
- Spring Boot 4 yêu cầu tối thiểu Java 17; khuyến nghị dùng LTS mới.
- Tính năng liên quan: record (16), text block (15), pattern matching cho `switch` và record pattern
  (21), **virtual threads** (21) — Spring Boot bật bằng `spring.threads.virtual.enabled=true` (ngoài
  phạm vi hôm nay, chỉ cần biết tên).

---

# Phần B — Nền tảng Spring Boot (slide 4–7)

## B1. Spring vs Spring Boot

**Giải thích trong lớp:**
> Spring Framework là bộ nền: IoC container, dependency injection, Spring MVC, transaction…
> Spring Boot đứng trên Spring Framework và lo phần "lắp ráp": chọn sẵn phiên bản thư viện tương
> thích (dependency management), tự cấu hình theo những gì có trên classpath (auto-configuration),
> nhúng sẵn web server, để mình chạy app bằng một lệnh `main`.

**Hiểu lầm:** "Spring Boot là framework khác thay thế Spring." → Không, Boot *dùng* Spring Framework.
Spring Boot 4 chạy trên Spring Framework 7.

**Câu hỏi follow-up:**
- *"Không có Boot thì sao?"* → Vẫn làm được, nhưng phải tự khai báo DataSource, EntityManagerFactory,
  TransactionManager, DispatcherServlet, Jackson converter, server… và tự chọn version cho từng thư viện.

## B2. IoC container, ApplicationContext và bean

**Giải thích trong lớp:**
> Bình thường mình tự `new` object. Với Spring, mình *đảo ngược* việc đó (Inversion of Control):
> Spring có một "container" — `ApplicationContext` — tự tạo object, nối chúng với nhau, và quản lý
> vòng đời. Mỗi object do Spring quản lý gọi là một **bean**.

**Bên dưới (thứ tự khởi động, rút gọn):**
1. `SpringApplication.run(...)` tạo `ApplicationContext`.
2. Đọc cấu hình (`application.yml`, biến môi trường, tham số dòng lệnh…) vào `Environment`.
3. Component scan: tìm class có `@Component`/`@Service`/`@Repository`/`@RestController`/
   `@Configuration`… → tạo *bean definition*.
4. Auto-configuration: thêm bean definition cho hạ tầng (DataSource, JPA, Jackson, Tomcat…) — chỉ
   khi điều kiện thỏa (xem B4).
5. Tạo các bean singleton, tiêm dependency qua constructor, tạo **proxy** nếu cần
   (`@Transactional`, Spring Data repository).
6. Khởi động embedded Tomcat, app sẵn sàng nhận request.

**Scope mặc định:** **singleton** — một instance cho mỗi `ApplicationContext`, tạo lúc khởi động
(trừ khi `@Lazy`). Có các scope khác: `prototype`, `request`, `session`…

**Câu hỏi hay gặp:**
- *"Service là singleton, nhiều request cùng lúc có bị xung đột không?"* → Không, **miễn là service
  stateless** (không giữ dữ liệu của request trong field). `ProductService` chỉ có field `final
  productRepository` — không đổi — nên an toàn. Dữ liệu của mỗi request nằm trong biến local/tham số,
  mỗi thread có stack riêng. Bẫy: đừng lưu kiểu `private Product current;` trong service.
- *"Bean có phải là một object đặc biệt không?"* → Không, chỉ là object Java bình thường (POJO) mà
  Spring tạo và quản lý.
- *"Hai bean cùng implement một interface thì inject cái nào?"* → App không khởi động; Spring Boot in
  *"Parameter 0 of constructor in … required a single bean, but 2 were found"* (bên dưới là
  `NoUniqueBeanDefinitionException`). Sửa bằng `@Primary`, `@Qualifier`, hoặc inject `List<...>` nếu
  cần tất cả.

## B3. `@SpringBootApplication` và component scan (slide 5)

```text
@SpringBootApplication ≈ @Configuration + @EnableAutoConfiguration + @ComponentScan
```

**Giải thích trong lớp:**
> Ba việc: class này là một nguồn cấu hình (`@Configuration` — thực ra là `@SpringBootConfiguration`),
> bật auto-configuration, và quét component bắt đầu **từ package của chính class này trở xuống**.

**Bên dưới:** base package mặc định của scan là package chứa `ProductApplication` —
`com.example.product` — và mọi package con (`controller`, `service`…). Đó là lý do main class đặt ở
package gốc.

**Câu hỏi bẫy:** *"Nếu mình đặt một `@Service` ở package `com.example.other` thì sao?"* → Không
được scan → không thành bean → nơi nào inject nó sẽ lỗi khởi động kiểu *"required a bean of type …
that could not be found"*. Cách sửa: chuyển vào dưới `com.example.product` (hoặc cấu hình
`scanBasePackages`, nhưng không cần trong project bình thường).

**Vì sao slide dùng dấu "≈" chứ không phải "="?** → Vì chính xác thì là `@SpringBootConfiguration`
(một biến thể của `@Configuration`) + `@EnableAutoConfiguration` + `@ComponentScan` kèm vài filter
mặc định. Ở mức ý tưởng thì tương đương.

## B4. Auto-configuration và starter

**Giải thích trong lớp:**
> Spring Boot nhìn vào classpath và cấu hình của mình rồi quyết định: có Spring MVC + Tomcat → dựng web
> server và DispatcherServlet; có JPA + H2 → tạo DataSource trỏ vào H2, EntityManagerFactory,
> TransactionManager. Mình khai báo bean riêng thì Boot "lùi lại", dùng bean của mình.

**Bên dưới:**
- Mỗi module của Boot liệt kê các class auto-configuration trong file
  `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.
- Mỗi class auto-configuration có điều kiện `@Conditional…`: `@ConditionalOnClass` (có class trên
  classpath không), `@ConditionalOnMissingBean` (mình đã tự định nghĩa bean chưa),
  `@ConditionalOnProperty` (có bật property không)…
- **Starter** chỉ là một dependency "gom nhóm": ví dụ `spring-boot-starter-data-jpa` kéo theo
  Hibernate, Spring Data JPA, HikariCP (connection pool)… với version đã được Boot kiểm tra tương thích.
- Ở Spring Boot 4, starter được chia nhỏ theo module hơn: dùng `spring-boot-starter-webmvc` (tên cũ
  `spring-boot-starter-web` — xem trạng thái chính xác ở phần C15), test MVC dùng
  `spring-boot-starter-webmvc-test`.

**Mẹo khi bị hỏi "làm sao biết Boot đã cấu hình gì?":** chạy app với `--debug` (hoặc `debug: true`)
→ Boot in **Condition Evaluation Report** (positive/negative matches). Hoặc dùng Actuator endpoint
`/actuator/conditions` (ngoài phạm vi hôm nay).

**Hiểu lầm:** "Auto-configuration là phép thuật, không kiểm soát được." → Nó chỉ là các class
`@Configuration` bình thường có điều kiện; mình override được bằng property hoặc bean riêng.

## B5. Embedded server

**Giải thích trong lớp:**
> Web server (mặc định là Tomcat) chạy **bên trong** app. Build ra một file JAR, chạy `java -jar` là
> có server — không cần cài Tomcat riêng rồi deploy file WAR như ngày xưa.

**Câu hỏi:** *"Đổi port thế nào?"* → `server.port` — trong demo là `${SERVER_PORT:8080}`.
*"Đổi sang Jetty được không?"* → Được, exclude Tomcat và thêm starter Jetty. (Boot 4 không còn hỗ trợ
Undertow — chi tiết ở C15.)

## B6. Dependency Injection và constructor injection (slide 6)

**Giải thích trong lớp:**
> `ProductService` cần `ProductRepository`. Thay vì tự `new`, nó *khai báo* nhu cầu qua constructor.
> Spring thấy constructor đó, tìm bean kiểu `ProductRepository` trong container và truyền vào.

**Bên dưới:**
- Class chỉ có **một constructor** → Spring dùng nó để inject, **không cần `@Autowired`** (từ Spring 4.3).
- `ProductRepository` là interface, không ai viết class implement — Spring Data tạo một **proxy**
  implement interface này lúc khởi động (bên trong ủy quyền cho `SimpleJpaRepository`). Proxy đó là
  bean được inject.
- `ProductController` cũng được inject `ProductService` theo cùng cách → chuỗi
  Controller → Service → Repository được "nối dây" (wiring) hoàn toàn bởi container.

**Ba kiểu injection:**

| Kiểu | Ví dụ | Đánh giá |
|---|---|---|
| Constructor | `public ProductService(ProductRepository r)` | ✅ Mặc định nên dùng |
| Setter | `@Autowired void setRepo(...)` | Cho dependency *tùy chọn* |
| Field | `@Autowired private ProductRepository repo;` | ❌ Tránh làm mặc định |

**Vì sao constructor injection (4 lý do trên slide):**
1. *Explicit* — nhìn constructor là biết class cần gì.
2. *Dễ test* — unit test chỉ cần `new ProductService(mockRepo)`, không cần Spring, không cần reflection.
3. *Immutable* — field `final`.
4. *Dễ suy luận* — object luôn ở trạng thái hợp lệ ngay sau khi tạo; không có khoảnh khắc field còn `null`.

**Hiểu lầm:**
- "Field injection không chạy được." → Chạy bình thường, chỉ là không khuyến nghị làm mặc định.
- "Phải có `@Autowired` trên constructor." → Không cần khi chỉ có một constructor.

**Câu hỏi follow-up:**
- *"Nếu constructor có quá nhiều tham số thì sao?"* → Đó là tín hiệu class đang làm quá nhiều việc
  (vi phạm Single Responsibility) — constructor injection giúp *lộ ra* vấn đề đó sớm.
- *"Circular dependency (A cần B, B cần A)?"* → Với constructor injection Spring báo lỗi ngay khi khởi
  động (Spring Boot mặc định cấm circular reference từ 2.6). Đó là tín hiệu thiết kế cần tách lại.
- *"Lombok `@RequiredArgsConstructor`?"* → Sinh đúng constructor này cho các field `final`. Tiện,
  nhưng hôm nay không dùng Lombok để code tường minh, không phụ thuộc thêm.

## B7. Kiến trúc phân tầng và cấu trúc package (slide 4, 7)

**Giải thích trong lớp:**
> Controller lo HTTP (đọc request, trả status code). Service lo nghiệp vụ và điều phối (gọi repository,
> kiểm tra quy tắc, mở transaction). Repository lo truy cập database. Mỗi tầng chỉ biết tầng ngay dưới.

**Vì sao chia vậy:** dễ test từng tầng (mock tầng dưới), thay đổi một tầng không lan ra tầng khác,
người mới vào project biết tìm code ở đâu.

**Hiểu lầm:**
- "Package theo layer là cách duy nhất." → Project lớn thường chia **theo feature** (`product`,
  `order`…) rồi mới chia layer bên trong. Slide 7 ghi "Suggested" — với một domain nhỏ như hôm nay thì
  chia theo layer dễ học nhất.
- "Controller gọi thẳng Repository cho nhanh cũng được." → Chạy được, nhưng mất chỗ đặt business rule
  và transaction; đó chính là mistake #1 trên slide 14.

**Câu hỏi:** *"Service có cần interface (`ProductService` + `ProductServiceImpl`) không?"* → Không bắt
buộc. Ngày xưa hay tách để tạo proxy JDK; Spring hiện nay tạo proxy CGLIB cho class bình thường. Chỉ
tạo interface khi thực sự có nhiều implementation.

---

# Phần C — Xây dựng API: từ Controller đến Database

## C1. HTTP & REST tối thiểu cần nắm

| Method | Ý nghĩa | Idempotent? | Trong demo |
|---|---|---|---|
| `GET` | Đọc | ✅ | `GET /api/products/{id}`, `GET /api/products?page=…` |
| `POST` | Tạo mới | ❌ (gửi 2 lần = tạo 2 lần) | `POST /api/products` |
| `PUT` | Thay thế toàn bộ | ✅ | bài tập về nhà (Phụ lục B của kịch bản) |
| `PATCH` | Cập nhật một phần | không đảm bảo | — |
| `DELETE` | Xóa | ✅ | — |

*Idempotent* = gọi 1 lần hay nhiều lần thì trạng thái server như nhau.

Status code cần thuộc: `200 OK`, `201 Created`, `204 No Content`, `400 Bad Request`,
`404 Not Found`, `409 Conflict`, `500 Internal Server Error`. Nhóm `4xx` = lỗi phía client (client
cần sửa request), `5xx` = lỗi phía server (client không làm gì sai).

**Câu hỏi hay gặp:**
- *"Vì sao `201` mà không `200`?"* → `201 Created` nói rõ "đã tạo resource mới" — client/gateway/test
  hiểu đúng ngữ nghĩa mà không cần đọc body.
- *"Resource naming: `/api/products` hay `/api/product`, `/api/getProducts`?"* → Danh từ số nhiều,
  không đặt động từ trong URL; động từ là HTTP method.
- *"`400` hay `422` cho lỗi validation?"* → Cả hai đều gặp trong thực tế. Spring mặc định dùng
  `400` cho `MethodArgumentNotValidException`; điều quan trọng là **nhất quán** trong toàn hệ thống.

## C2. Một request đi qua Spring MVC như thế nào (slide 4 — "bên dưới")

```text
HTTP request
  → Tomcat (embedded)
  → DispatcherServlet                     (một servlet duy nhất nhận mọi request)
  → HandlerMapping                        (tìm method khớp: POST + /api/products → ProductController.create)
  → HandlerAdapter + argument resolvers   (chuẩn bị tham số cho method)
       @RequestBody → HttpMessageConverter (Jackson) đọc JSON → CreateProductRequest
       @Valid       → Bean Validation (Hibernate Validator) kiểm tra → lỗi thì ném MethodArgumentNotValidException
       @PathVariable/@PageableDefault Pageable → đọc từ URL / query string
  → ProductController.create(...)
  → ProductService (qua proxy transaction) → ProductRepository (proxy Spring Data) → Hibernate → JDBC → H2
  ← return value
  ← HttpMessageConverter (Jackson) ghi ProductResponse → JSON
  ← HTTP response (status 201 + body)
```

Nếu có exception ở bất kỳ đâu trong chuỗi trên:

```text
exception
  → DispatcherServlet hỏi lần lượt các HandlerExceptionResolver:
      1. ExceptionHandlerExceptionResolver   → tìm @ExceptionHandler (trong controller, rồi @RestControllerAdvice)
      2. ResponseStatusExceptionResolver     → exception có @ResponseStatus / ResponseStatusException
      3. DefaultHandlerExceptionResolver     → các exception chuẩn của Spring MVC → 4xx/5xx phù hợp
  → không ai xử lý → lỗi đi tới /error (BasicErrorController của Spring Boot) → JSON lỗi mặc định
```

**Giải thích trong lớp (bản ngắn):**
> Mọi request vào `DispatcherServlet` trước. Nó tìm method controller phù hợp, dùng Jackson chuyển JSON
> thành DTO, chạy validation, gọi controller, rồi lại dùng Jackson chuyển kết quả thành JSON. Nếu có
> exception, nó tìm `@ExceptionHandler` phù hợp — đó là chỗ `@RestControllerAdvice` của mình vào cuộc.

**Hiểu lầm:**
- "Validation chạy trong Service." → Với `@Valid @RequestBody`, validation chạy **trước khi method
  controller được gọi** — lúc resolve tham số. Controller và Service không bao giờ nhận object lỗi.
- "`@RestController` khác hẳn `@Controller`." → `@RestController = @Controller + @ResponseBody`: giá
  trị trả về được ghi thẳng vào body (JSON) thay vì coi là tên view (HTML template).

**Câu hỏi follow-up:**
- *"Ai chuyển object thành JSON?"* → Jackson, thông qua `HttpMessageConverter`, được Boot
  auto-configure. Nó dùng accessor của record (`name()`) hoặc getter (`getName()`) để lấy dữ liệu.
- *"Client gửi thiếu header `Content-Type: application/json` thì sao?"* → Spring không biết đọc body
  bằng converter nào → `415 Unsupported Media Type`.

## C3. DTO, record và Jackson (slide 9)

**Giải thích trong lớp:**
> DTO là "hợp đồng" giữa API và client. `CreateProductRequest` là những gì client **được phép gửi**,
> `ProductResponse` là những gì client **nhận về**. Entity `Product` là cách mình **lưu** dữ liệu.
> Ba thứ này có thể giống nhau hôm nay, nhưng thay đổi độc lập với nhau. Jackson là thư viện chuyển
> JSON thành DTO và DTO thành JSON.

**Bên dưới:**
- **Đọc request:** `@RequestBody` → Jackson tạo `CreateProductRequest` bằng **constructor chuẩn
  (canonical constructor)** của record. Tên component (`name`, `sku`, `price`) lấy từ metadata của
  record, nên không cần setter, không cần constructor rỗng.
- **Field thiếu trong JSON** → component đó là `null` (riêng kiểu primitive: Jackson 3 mặc định bật `FAIL_ON_NULL_FOR_PRIMITIVES`, nên thiếu field `int`/`boolean` là lỗi đọc JSON → `400`, không còn tự thành `0`/`false` như Jackson 2). Vì vậy
  không gửi `price` thì `price == null` và `@NotNull` bắt được.
- **Field thừa trong JSON bị bỏ qua im lặng:** Spring Boot tắt `FAIL_ON_UNKNOWN_PROPERTIES`. Client
  gửi thêm `"id": 999, "active": false` thì Jackson lờ đi — record không có chỗ chứa, nên không có
  cách nào lọt vào entity. Đây chính là lợi ích của DTO.
- **Ghi response:** Jackson gọi accessor của record (`id()`, `name()`, … `active()`) và ghi field
  theo **thứ tự khai báo component**. Với class thường (như entity) thì Jackson dùng getter
  `getName()`, `isActive()`.
- **`BigDecimal` và scale** (nền tảng ở A8):

| Tình huống | JSON trả về | Vì sao |
|---|---|---|
| POST `"price": 50` | `"price": 50` | `save()` trả lại **chính object** mình vừa tạo, giá vẫn là `BigDecimal` từ request (scale 0) |
| GET sản phẩm đó sau đó | `"price": 50.00` | Đọc lại từ cột `numeric(38,2)` (Hibernate mặc định precision 38, scale 2) |
| POST `"price": 79.999` | POST trả `79.999`, GET trả `80.00` | DB làm tròn theo scale 2 khi lưu |

  Muốn chặt chẽ: thêm `@Digits(integer = 10, fraction = 2)` vào DTO để từ chối số lẻ quá 2 chữ số, và
  đặt `@Column(precision = 12, scale = 2)` trên entity cho rõ ràng. Hôm nay không làm.
- **`Instant`** (trong `ApiError.timestamp`) được ghi thành chuỗi ISO-8601 theo **UTC**, có hậu tố
  `Z`, ví dụ `"2026-09-25T03:33:05.123456Z"`. Việt Nam là UTC+7, nên 10:33 sáng hiện là `03:33Z` —
  không phải lỗi. Số chữ số lẻ (3, 6 hoặc 9) tùy máy.
- **Jackson 3:** Spring Boot 4 dùng Jackson 3. Package đổi từ `com.fasterxml.jackson.databind` sang
  `tools.jackson.databind`, nhưng các annotation (`@JsonProperty`, `@JsonIgnore`…) **vẫn** ở
  `com.fasterxml.jackson.annotation`. Code demo không import gì của Jackson nên không bị ảnh hưởng. Khi
  copy code từ bài viết cũ có `import com.fasterxml.jackson.databind.ObjectMapper` thì cần sửa.

**Hiểu lầm:**
- "Trả entity cho nhanh, DTO là thừa." → Trả entity làm lộ cấu trúc DB (thêm cột nội bộ là tự lộ ra
  API), nhận entity cho phép client gửi `id`/`active`. Khi entity có quan hệ lazy, Jackson còn có thể
  gặp lỗi khi serialize hoặc vòng lặp vô hạn. Slide 14: *Returning Entity* là lỗi #2.
- "Record không dùng được với Jackson / Spring." → Jackson hỗ trợ record đầy đủ; record là lựa chọn
  tự nhiên cho DTO vì bất biến và ngắn gọn.
- "Record làm entity được luôn." → Không. JPA cần class **không final**, có **constructor không tham
  số**, và field thay đổi được. Record là final và bất biến → không làm entity được.
- "JSON số `79.99` đi qua `double` nên mất chính xác." → Khi kiểu đích là `BigDecimal`, Jackson đọc
  thẳng chuỗi số trong JSON thành `BigDecimal`, không qua `double`.

**Câu hỏi follow-up:**
- *"Mapping DTO ↔ entity bằng tay có mệt không?"* → Project nhỏ thì viết tay (`toResponse`) là rõ ràng
  nhất. Project lớn dùng MapStruct (sinh code lúc compile, không dùng reflection).
- *"Muốn client gửi field lạ thì báo lỗi?"* → Bật `spring.jackson.deserialization.fail-on-unknown-properties=true`.
  Thường không bật, để client cũ/mới tương thích với nhau dễ hơn.
- *"Một DTO dùng chung cho create và update được không?"* → Được lúc đầu, nhưng nên tách
  (`UpdateProductRequest`) vì hai contract sẽ khác nhau theo thời gian — xem Phụ lục B của kịch bản.
- *"Muốn đổi tên field trong JSON (`product_name`)?"* → `@JsonProperty("product_name")` trên component
  của record.

## C4. Bean Validation

**Giải thích trong lớp:**
> Mình khai báo quy tắc bằng annotation ngay trên DTO. `@Valid` trên tham số controller là "công tắc":
> có nó thì Spring chạy các quy tắc **trước khi** gọi method; dữ liệu sai thì method không bao giờ
> chạy, client nhận 400.

**Bên dưới:**
- **Ai kiểm tra:** Bean Validation là **đặc tả** (Jakarta Validation, package `jakarta.validation`),
  Hibernate Validator là **bản cài đặt**. `spring-boot-starter-validation` kéo Hibernate Validator vào
  và Boot tự tạo bean `Validator`.
- **Khi nào kiểm tra:** lúc resolve tham số (xem C2). Argument resolver của `@RequestBody` đọc JSON
  bằng Jackson → thấy `@Valid` → gọi validator → có lỗi thì ném `MethodArgumentNotValidException`.
  Hibernate Validator mặc định gom **tất cả** lỗi (không dừng ở lỗi đầu tiên), nên request #2 ra đủ 3
  lỗi.
- **Các constraint trong demo:**

| Annotation | `null` | Kiểm tra gì | Dùng cho |
|---|---|---|---|
| `@NotNull` | ❌ | chỉ cấm `null` | mọi kiểu |
| `@NotEmpty` | ❌ | không `null` và độ dài/kích thước > 0 | `String`, collection, map, mảng |
| `@NotBlank` | ❌ | không `null` và có ít nhất một ký tự không phải khoảng trắng | **chỉ** chuỗi (`CharSequence`) |
| `@Size(max = 100)` | ✅ bỏ qua | độ dài ≤ 100 | chuỗi, collection… |
| `@Positive` | ✅ bỏ qua | > 0 (`@PositiveOrZero` là ≥ 0) | số, kể cả `BigDecimal` |

  Ví dụ: `"   "` qua được `@NotNull` và `@NotEmpty`, nhưng bị `@NotBlank` chặn. Hầu hết constraint (trừ
  nhóm `@Not…`) **coi `null` là hợp lệ** — đó là lý do `price` cần `@NotNull @Positive`.
- **Message mặc định là tiếng Anh**: `must not be blank`, `must be greater than 0`,
  `size must be between 0 and 100`. Hibernate Validator không có bộ message tiếng Việt. Muốn đổi:
  `@NotBlank(message = "...")` hoặc file `ValidationMessages.properties`.
- **Sai kiểu constraint không bị phát hiện lúc khởi động:** `@NotBlank` đặt trên `BigDecimal price`
  vẫn compile và app vẫn start. Chỉ khi request đầu tiên được validate mới nổ
  `UnexpectedTypeException` với mã `HV000030` (không có validator cho kiểu này) → client nhận 500.
- **Constraint đặt thẳng trên tham số method** (ví dụ `@PathVariable @Positive Long id`): từ Spring 6.1,
  Spring MVC tự chạy *method validation* → lỗi là `HandlerMethodValidationException` → 400 với body
  mặc định (handler của mình không bắt exception này). Lưu ý: khi method validation được áp dụng cho
  một method, lỗi của `@Valid @RequestBody` **trong cùng method đó** cũng được báo qua
  `HandlerMethodValidationException` thay vì `MethodArgumentNotValidException`. Nếu lại thêm
  `@Validated` ở mức class controller thì cơ chế khác (`MethodValidationPostProcessor`) chạy, ném
  `ConstraintViolationException` → mặc định **500**. Hôm nay mình không đặt constraint trên tham số.

**Hiểu lầm:**
- "Có annotation trên DTO là đủ." → Không. **Quên `@Valid`** thì không ai chạy các annotation đó:
  request #2 (`"name": ""`, `"sku": ""`, `"price": -1`) trả **`201 Created`** và dữ liệu rác vào DB.
  DB chỉ chặn `null` (nhờ `nullable = false`), và khi đó là 500 chứ không phải 400.
- "Thiếu `spring-boot-starter-validation` thì sẽ báo lỗi." → Không báo gì cả. Annotation vẫn compile
  (nếu có API), nhưng không có bộ cài đặt nào chạy → validation bị bỏ qua im lặng. Hay gặp khi tự tạo
  project mà quên chọn dependency Validation.
- "Validation thay cho ràng buộc DB." → Không. Validation ở DTO là để báo lỗi sớm, rõ ràng cho client.
  Ràng buộc DB (`nullable = false`, `unique = true`) là chốt cuối cho mọi đường ghi dữ liệu.
- "`@Valid` và `@Validated` giống hệt nhau." → Trên `@RequestBody` thì dùng cái nào cũng chạy.
  Khác biệt: `@Valid` (Jakarta) còn dùng để **đi sâu vào object lồng nhau** (đặt trên field kiểu DTO
  con); `@Validated` (Spring) hỗ trợ **validation groups** và bật method validation khi đặt ở mức class.

**Câu hỏi follow-up:**
- *"DTO có field là object khác (ví dụ `AddressRequest address`) thì sao?"* → Phải đặt `@Valid` lên
  field đó thì constraint bên trong mới được kiểm tra.
- *"Muốn tự xử lý lỗi trong controller?"* → Thêm tham số `BindingResult` ngay sau tham số `@Valid`; khi
  đó Spring không ném exception mà để mình tự kiểm tra. Không khuyến khích — làm controller dày lên.
- *"Quy tắc cần gọi DB (SKU đã tồn tại) có đặt thành annotation được không?"* → Làm được bằng custom
  constraint, nhưng quy tắc cần DB nên để trong Service (như `existsBySku`) — rõ ràng và dễ test hơn.
- *"Custom validator viết thế nào?"* → Tạo annotation có `@Constraint(validatedBy = ...)` và một class
  implement `ConstraintValidator<A, T>`. Ngoài phạm vi hôm nay.
- *"Client gửi `Accept-Language` khác thì message đổi không?"* → Message được sinh theo locale của
  request, nên có thể đổi sang ngôn ngữ mà Hibernate Validator có sẵn bản dịch. Không có tiếng Việt.

## C5. Global exception handling (slide 10)

**Giải thích trong lớp:**
> Thay vì try-catch ở từng controller, mình gom việc "dịch exception thành response" về một chỗ:
> `@RestControllerAdvice`. Mỗi method `@ExceptionHandler` nhận một loại exception và trả về `ApiError`
> với status phù hợp. Lỗi khác nhau, nhưng client luôn nhận **cùng một định dạng**.

**Bên dưới:**
- `@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody`: áp dụng cho mọi controller, giá trị
  trả về được ghi ra JSON.
- Nhắc lại chuỗi resolver ở C2: khi exception bay ra, `ExceptionHandlerExceptionResolver` đứng đầu. Lúc
  khởi động nó đã quét các `@ControllerAdvice` và lập bảng "loại exception → method". Lúc chạy nó chọn
  method khớp **gần nhất** theo cây kế thừa (handler cho `ProductNotFoundException` thắng handler cho
  `RuntimeException`). `@ExceptionHandler` viết ngay trong controller được ưu tiên hơn advice.
- `ResponseEntity<ApiError>` trả về từ handler đi qua **cùng** `HttpMessageConverter` (Jackson) như
  response bình thường.
- **Hợp đồng lỗi `ApiError`:**

| `code` | Status | Exception | Ai ném |
|---|---|---|---|
| `VALIDATION_ERROR` | 400 | `MethodArgumentNotValidException` | Spring, khi `@Valid` thất bại |
| `PRODUCT_NOT_FOUND` | 404 | `ProductNotFoundException` | `ProductService.getById` |
| `DUPLICATE_SKU` | 409 | `DuplicateSkuException` | `ProductService.create` (từ step-09) |

  `code` cố định để code phía client dùng `if`/`switch`; `message` cho người đọc; `timestamp` để đối
  chiếu log. Service **không biết** 404 hay 409 — việc dịch sang HTTP là của handler.
- **Vì sao `.sorted()`:** danh sách field error không có thứ tự đảm bảo (lần này `sku` trước, lần sau
  `name` trước). Sort chuỗi `"field: message"` để output lần nào cũng giống nhau → dễ đọc, và test có
  thể so sánh nguyên chuỗi.
- **Những lỗi handler của mình KHÔNG phủ** (vẫn ra body mặc định `{timestamp, status, error, path}` của
  Spring Boot):

| Tình huống | Exception bên dưới | Kết quả |
|---|---|---|
| `GET /api/products/abc` | `MethodArgumentTypeMismatchException` | 400 |
| JSON sai cú pháp | `HttpMessageNotReadableException` | 400 |
| Thiếu `Content-Type: application/json` | `HttpMediaTypeNotSupportedException` | 415 |
| Sai method (ví dụ `DELETE`) | `HttpRequestMethodNotSupportedException` | 405 + header `Allow` |
| Sai URL, thừa dấu `/` ở cuối | không có handler khớp | 404 |
| `sort=unknownField` | `PropertyReferenceException` | 500 |
| SKU trùng trước step-09 | `DataIntegrityViolationException` | 500 |

- **Log:** khi handler của mình xử lý exception, console **không** in gì (dòng WARN của
  `DefaultHandlerExceptionResolver` thấy ở lúc chưa có handler sẽ biến mất). Production nên tự log trong
  handler: 4xx log mức `WARN`/`DEBUG` ngắn gọn, lỗi không lường trước log `ERROR` kèm stack trace.

**Hiểu lầm:**
- "Thêm `@ExceptionHandler(Exception.class)` cho chắc." → Nguy hiểm nếu advice **không** kế thừa
  `ResponseEntityExceptionHandler`: handler "bắt tất cả" này sẽ nuốt luôn các lỗi 4xx của Spring (405,
  415, JSON sai) và biến chúng thành 500. Cách đúng: kế thừa `ResponseEntityExceptionHandler` (lớp này
  đã xử lý sẵn các exception của Spring MVC với status đúng), rồi mới thêm handler cuối cho
  `Exception`.
- "Kế thừa `ResponseEntityExceptionHandler` rồi giữ nguyên `@ExceptionHandler(MethodArgumentNotValidException.class)`."
  → Lớp cha đã có handler cho exception này → app báo lỗi khởi động vì handler bị trùng (ambiguous).
  Phải **override** method `handleMethodArgumentNotValid(...)` của lớp cha thay vì khai báo lại.
- "Có `@RestControllerAdvice` thì mọi lỗi đều ra `ApiError`." → Chỉ những exception mình khai báo.
  Demo `/api/products/abc` cho thấy rõ điều này.
- "Trả `ex.getMessage()` cho client là ổn." → Ổn với exception **của mình** (message do mình viết). Với
  exception lạ (SQL, NullPointer) thì không — có thể lộ tên bảng, câu SQL. Handler cuối nên trả message
  chung chung.

**Câu hỏi follow-up:**
- *"Sao không dùng `ProblemDetail` (RFC 9457) có sẵn của Spring?"* → Spring hỗ trợ `ProblemDetail`
  (body chuẩn gồm `type`, `title`, `status`, `detail`, `instance`, content type
  `application/problem+json`) và Boot có `spring.mvc.problemdetails.enabled` (mặc định `false`). Hôm
  nay không bật vì: (1) `ApiError` ngắn, dễ hiểu cho bài học; (2) khi bật, Boot đăng ký một advice
  riêng có `@Order(0)` — nó có thể chạy trước và "che" handler validation của mình. Dự án mới nên cân
  nhắc `ProblemDetail` vì đó là chuẩn.
- *"Sao không dùng `@ResponseStatus(HttpStatus.NOT_FOUND)` trên exception cho gọn?"* → Chạy được (đi
  qua `ResponseStatusExceptionResolver`), nhưng body là mặc định của Boot, không phải `ApiError`.
- *"Có nhiều advice thì cái nào chạy?"* → Dùng `@Order`/`@Priority` để định thứ tự; có thể giới hạn
  phạm vi bằng `@RestControllerAdvice(basePackages = ...)`.
- *"SKU trùng do hai request đồng thời thì sao?"* → Cả hai qua được `existsBySku`, DB chặn request
  thứ hai bằng unique constraint → `DataIntegrityViolationException` → hiện là 500. Thêm handler cho
  exception đó để trả 409.

## C6. JPA, Hibernate và entity

**Giải thích trong lớp:**
> JPA là bộ quy tắc chuẩn của Java để ánh xạ object sang bảng. Hibernate là thư viện làm việc đó thật
> sự. Spring Data JPA đứng trên cùng, giúp mình khỏi viết code repository lặp đi lặp lại. Entity
> `Product` là một class Java bình thường có vài annotation để Hibernate biết nó ứng với bảng nào.

**Bên dưới:**

| Tầng | Là gì | Trong demo |
|---|---|---|
| Jakarta Persistence (JPA) | **Đặc tả**: annotation + interface (`EntityManager`) | `jakarta.persistence.*` |
| Hibernate ORM 7 | **Bản cài đặt** JPA: sinh SQL, quản lý entity, cache | chạy bên dưới, in SQL khi `show-sql` |
| Spring Data JPA | **Lớp tiện ích** trên JPA: repository interface, derived query, paging | `ProductRepository` |
| JDBC + HikariCP + H2 | Kết nối và database thật | `jdbc:h2:mem:demo` |

- **Yêu cầu tối thiểu của một entity:**
  - Có `@Entity` và một field `@Id` (nhớ import `jakarta.persistence.Id` — xem Phụ lục E).
  - Có **constructor không tham số** (public hoặc protected). `Product` không khai báo constructor nào
    nên Java tự sinh. Nếu sau này thêm constructor có tham số, phải thêm lại constructor rỗng.
  - Class **không final** (Hibernate cần tạo subclass làm proxy cho lazy loading).
  - `@Id` đặt trên field → Hibernate đọc/ghi thẳng field (field access). Getter/setter là cho code của
    mình.
- **Sinh id — `IDENTITY` vs `SEQUENCE`:**

| | `IDENTITY` (demo) | `SEQUENCE` |
|---|---|---|
| Ai sinh id | Cột tự tăng của DB, sinh lúc `INSERT` | Sequence của DB, Hibernate lấy trước khi insert |
| Khi nào có id | Hibernate phải `INSERT` **ngay** lúc `save()` | Có id trước, `INSERT` có thể dồn tới lúc flush |
| Batch insert | Không batch được | Batch được (mặc định lấy trước 50 id một lần) |
| Hay dùng | MySQL, demo đơn giản | PostgreSQL, Oracle |

  Hệ quả nhìn thấy trong demo: với `IDENTITY`, dòng `insert into products ...` hiện ngay khi gọi
  `save()`, và `saved.getId()` đã có giá trị trước khi transaction commit.
- **`ddl-auto`** — Hibernate tự tạo/sửa bảng theo entity:

| Giá trị | Làm gì |
|---|---|
| `none` | Không đụng vào schema |
| `validate` | So entity với schema có sẵn, lệch thì không khởi động |
| `update` | Thêm bảng/cột còn thiếu; **không** xóa cột, không đổi tên, không migrate dữ liệu |
| `create` | Xóa rồi tạo lại schema mỗi lần khởi động |
| `create-drop` | Như `create`, và xóa schema khi tắt app |

  Demo đặt `update`. Nếu không đặt gì, Boot mặc định `create-drop` với DB nhúng (H2) và `none` với DB
  khác. Production: `validate` hoặc `none`, và quản lý schema bằng Flyway/Liquibase (có version, review
  được, chạy giống nhau ở mọi môi trường).
- **Vòng đời H2 in-memory:** `jdbc:h2:mem:demo` sống trong bộ nhớ của JVM, chừng nào còn kết nối mở
  (connection pool giữ kết nối suốt thời gian app chạy). Restart app = mất hết dữ liệu — đó là lý do
  request #3 ra 404 sau khi restart. Test chạy ở JVM khác nên có H2 riêng, không đụng dữ liệu của app
  đang chạy.

**Hiểu lầm:**
- "Spring Data JPA là ORM." → Không, ORM là Hibernate. Spring Data chỉ sinh repository và gọi
  `EntityManager` bên dưới.
- "`@Column(nullable = false)` là validation." → Nó chỉ ảnh hưởng DDL (`not null` trong
  `create table`). Kiểm tra dữ liệu đầu vào là việc của Bean Validation trên DTO.
- "`ddl-auto: update` đủ dùng cho production." → Không. Nó không xóa/đổi tên cột, không có lịch sử,
  không rollback được, và có thể làm những thay đổi mình không lường trước.
- "Cứ dùng Lombok `@Data` cho entity." → `@Data` sinh `equals/hashCode/toString` trên **mọi** field;
  với entity có quan hệ lazy thì dễ kích hoạt query ngoài ý muốn hoặc vòng lặp vô hạn. Nếu cần
  `equals/hashCode`, dựa trên id (cẩn thận id còn `null` trước khi lưu) hoặc khóa nghiệp vụ như `sku`.
  `Product` hôm nay không override — trong cùng một transaction, Hibernate đảm bảo mỗi id chỉ ứng với
  một object, nên không cần.

**Câu hỏi follow-up:**
- *"So với Entity Framework Core bên .NET?"*

| EF Core | Java / Spring |
|---|---|
| `DbContext` | `EntityManager` (persistence context) |
| `DbSet<Product>` | `ProductRepository` (Spring Data) |
| Change tracking + `SaveChanges()` | Dirty checking + flush/commit transaction (xem C8) |
| LINQ | Derived query, JPQL `@Query`, Criteria API |
| EF Migrations | Flyway / Liquibase (JPA không có migration sẵn) |
| `EnsureCreated()` | gần giống việc để Hibernate tự sinh schema (`ddl-auto`), nhưng lưu ý `create` **xóa** rồi tạo lại schema mỗi lần khởi động, còn `EnsureCreated()` không xóa gì |

- *"Cột được đặt tên thế nào?"* → Mặc định Boot đổi camelCase thành snake_case (`createdAt` →
  `created_at`). Đặt tên riêng bằng `@Column(name = "...")`.
- *"Xem bảng H2 ở đâu?"* → H2 console ở Spring Boot 4 là module riêng (`spring-boot-h2console`), demo
  không thêm. Hôm nay mình xem SQL qua `show-sql`.
- *"Quan hệ `@OneToMany`, `@ManyToOne`?"* → Ngoài phạm vi. Chỉ nhớ: quan hệ `...ToMany` mặc định lazy,
  và đó là nguồn gốc của vấn đề N+1.

## C7. Spring Data repository

**Giải thích trong lớp:**
> Mình chỉ viết một interface. Lúc khởi động, Spring Data tạo một object thật implement interface đó,
> với đầy đủ `save`, `findById`, `findAll`… Với method mình tự đặt tên như `existsBySku`, Spring Data
> đọc tên method rồi tự sinh câu query.

**Bên dưới:**
- **Interface → proxy → `SimpleJpaRepository`:** Spring Data tạo một **proxy** implement
  `ProductRepository`. Lời gọi method có sẵn (`save`, `findById`, `findAll`) được chuyển cho
  `SimpleJpaRepository` — một class thật của Spring Data dùng `EntityManager`. Lời gọi query method tự
  đặt (`existsBySku`) được chuyển cho phần xử lý query đã chuẩn bị sẵn lúc khởi động.
- **Các method demo dùng:**

| Method | Từ đâu | Bên dưới |
|---|---|---|
| `save(product)` | `CrudRepository` | id `null` → entity mới → `persist` (trả lại **chính object đó**); id có giá trị → `merge` |
| `findById(id)` | `CrudRepository` | `EntityManager.find` → `Optional<Product>` |
| `findAll(pageable)` | `PagingAndSortingRepository` | query có `offset`/`fetch first` + query `count` |
| `existsBySku(sku)` | mình tự khai báo | derived query |

- **Derived query `existsBySku`:** Spring Data tách tên: `exists` (kiểu kết quả: có/không) + `By` +
  `Sku` (điều kiện `sku = ?`). SQL thật in ra console:

```sql
select p1_0.id from products p1_0 where p1_0.sku=? fetch first ? rows only
```

  Nó chỉ lấy tối đa 1 dòng — đủ để trả lời "có hay không", không tải cả entity.
- **Kiểm tra tên lúc khởi động:** query của derived method được chuẩn bị ngay khi app start. Gõ sai
  `existsBySkuu` → app **không khởi động được**, lỗi đại ý: không tạo được query cho method
  `existsBySkuu`, *No property 'skuu' found for type 'Product'*. Lỗi phát hiện sớm, không đợi tới
  lúc có request.
- **Transaction mặc định:** `SimpleJpaRepository` có `@Transactional(readOnly = true)` ở mức class;
  `save`/`delete` được override với `@Transactional`. Khi được gọi từ trong `ProductService` (đã có
  transaction), chúng **tham gia** transaction của service (xem C8).

**Hiểu lầm:**
- "Spring Data sinh code Java cho repository lúc compile." → Không. Proxy được tạo **lúc chạy** (khi
  khởi động), không có file `.java` nào được sinh ra.
- "Derived query dùng cho mọi trường hợp." → Tên method dài như
  `findByNameContainingIgnoreCaseAndPriceLessThanAndActiveTrue` là tín hiệu nên chuyển sang `@Query`.
- "`findAll()` không sao, bảng nhỏ mà." → Bảng sẽ lớn lên. Slide 11 / slide 14: dùng
  `findAll(pageable)`.
- "`existsBySku` đã chặn được SKU trùng." → Chỉ trong trường hợp bình thường. Hai request đồng thời có
  thể cùng qua bước kiểm tra; unique constraint trong DB mới là chốt chặn thật.

**Câu hỏi follow-up:**
- *"Muốn viết query phức tạp?"* → Dùng `@Query` với JPQL (query theo **tên entity và field**, không
  phải tên bảng):

```java
@Query("select p from Product p where p.price < :max and p.active = true")
List<Product> findCheapActive(@Param("max") BigDecimal max);
```

  JPQL cũng được kiểm tra lúc khởi động. Cần SQL riêng của DB thì dùng
  `@Query(value = "select * from products where ...", nativeQuery = true)` — cái này **không** được
  kiểm tra trước, sai là lỗi lúc chạy.
- *"Query `UPDATE`/`DELETE` bằng `@Query`?"* → Cần thêm `@Modifying` và chạy trong transaction.
- *"Derived query có hỗ trợ phân trang không?"* → Có, thêm tham số `Pageable`:
  `Page<Product> findByActiveTrue(Pageable pageable)`.
- *"Sao `existsBySku` không cần `@Transactional` riêng?"* → Trong demo nó được gọi từ `create()` đã có
  transaction, nên chạy chung transaction đó. Lưu ý: query method tự khai báo (`existsBySku`) mặc định
  **không** có cấu hình transaction; chỉ các method kế thừa từ `SimpleJpaRepository` (`save`, `findById`…) mới có sẵn `readOnly`. Muốn thì đặt `@Transactional(readOnly = true)` lên interface repository.

## C8. Transaction

**Giải thích trong lớp:**
> `@Transactional` trên method của service nghĩa là: mọi thay đổi database trong method này là **một
> khối** — hoặc thành công hết, hoặc hủy hết. Spring lo việc mở, commit, rollback. Mình chỉ cần đặt
> annotation ở đúng chỗ: trên method public của service.

**Bên dưới:**
- **Proxy:** vì có `@Transactional`, bean `ProductService` mà controller nhận được thực ra là một
  **proxy** (subclass tạo bằng CGLIB). Khi controller gọi `productService.create(...)`:

```text
ProductController
  → proxy của ProductService
      mở transaction (JpaTransactionManager: lấy connection, mở EntityManager, gắn vào thread hiện tại)
      → ProductService.create()  (code thật của mình)
            existsBySku(...)   ─┐ tham gia transaction đang có (propagation REQUIRED)
            save(...)          ─┘
      method kết thúc bình thường        → flush + COMMIT
      method ném RuntimeException/Error  → ROLLBACK
  ← kết quả / exception
```

- **`readOnly = true`** (trên `getById`, `getProducts`) là một **gợi ý**: Hibernate đặt flush mode
  `MANUAL` (không flush, bỏ qua bước dò thay đổi), và connection JDBC được đánh dấu read-only. Nó
  **không** đảm bảo chặn mọi lệnh ghi ở mọi database. Ý nghĩa chính: tối ưu nhỏ + nói rõ ý định.
- **Quy tắc rollback mặc định:** `RuntimeException` và `Error` → rollback. **Checked exception →
  không rollback** (vẫn commit!). Muốn khác: `@Transactional(rollbackFor = Exception.class)`. Các
  exception của mình (`ProductNotFoundException`, `DuplicateSkuException`) đều kế thừa
  `RuntimeException`.
- **Dirty checking:** entity lấy ra trong transaction được Hibernate **theo dõi** (managed). Gọi setter
  rồi để method kết thúc → Hibernate tự sinh `UPDATE` lúc flush/commit, không cần gọi `save()`. Đây là
  gợi ý cho bài tập `PUT` (Phụ lục B).
- **`open-in-view: false`:** mặc định Boot để `true` (và in WARN nhắc nhở lúc khởi động) — khi đó
  `EntityManager` được giữ mở suốt request, kể cả lúc Jackson ghi JSON. Demo đặt `false`:
  `EntityManager` đóng ngay khi transaction của service kết thúc. Hệ quả: truy cập quan hệ lazy **bên
  ngoài** service sẽ gặp `LazyInitializationException` (đại ý: *could not initialize proxy - no
  Session*). `Product` không có quan hệ nên hôm nay không gặp, và `toResponse` được gọi **bên trong**
  method có transaction — mọi dữ liệu cần thiết đã được đọc xong trước khi đóng.

**Hiểu lầm:**
- "Đặt `@Transactional` ở đâu cũng có tác dụng." → Chỉ khi lời gọi **đi qua proxy**:

| Trường hợp | Có transaction? |
|---|---|
| Controller gọi `productService.create()` (public) | ✅ |
| `@Transactional` trên method `private` | ❌ proxy không override được method private |
| Trong `ProductService`, method A gọi `this.create()` (self-invocation) | ❌ gọi thẳng object thật, bỏ qua proxy |
| Method `final` | ❌ subclass CGLIB không override được |
| Class không phải bean (tự `new ProductService(...)`) | ❌ không có proxy nào |

- "Không có `@Transactional` thì không có transaction nào." → Các method kế thừa từ `SimpleJpaRepository`
  (`save`, `findById`…) vẫn tự chạy trong transaction riêng; query method tự khai báo như `existsBySku` thì
  chạy không có transaction (auto-commit). Dù sao `existsBySku` và `save` cũng tách rời — không còn là một khối nghiệp vụ.
- "Bắt exception trong method rồi thì transaction vẫn commit bình thường." → Nếu exception bay ra từ
  một method transactional **bên trong** (ví dụ repository, cùng transaction REQUIRED), transaction đã bị
  đánh dấu *rollback-only*. Bắt nó rồi đi tiếp thì lúc commit sẽ nhận `UnexpectedRollbackException`.
- "`jakarta.transaction.Transactional` cũng như nhau." → Spring hiểu cả hai, nhưng bản Jakarta không có
  `readOnly`. Dùng `org.springframework.transaction.annotation.Transactional` (Phụ lục E).

**Câu hỏi follow-up:**
- *"Đặt `@Transactional` ở controller được không?"* → Được về kỹ thuật, nhưng không nên: transaction
  kéo dài cả phần xử lý HTTP, và ranh giới nghiệp vụ nằm ở service. Repository đã có transaction sẵn.
- *"Đặt ở mức class được không?"* → Được. Mẫu hay dùng (giống `SimpleJpaRepository`):
  `@Transactional(readOnly = true)` ở class, `@Transactional` trên các method ghi.
- *"`DuplicateSkuException` ném ra thì rollback cái gì?"* → Không có gì để rollback — nó được ném
  **trước** `save()`. Transaction vẫn được đánh dấu rollback cho đúng quy tắc.
- *"Propagation, isolation là gì?"* → Propagation: method transactional gọi method transactional khác
  thì dùng chung (`REQUIRED`, mặc định) hay mở transaction mới (`REQUIRES_NEW`). Isolation: mức cách ly
  giữa các transaction đồng thời. Hôm nay dùng mặc định; đây là chủ đề riêng.
- *"Open-in-view `true` có gì xấu?"* → Tiện vì ít gặp `LazyInitializationException`, nhưng query có thể
  chạy ngầm lúc Jackson serialize (khó thấy, dễ N+1) và tài nguyên DB bị giữ lâu hơn. Tắt đi buộc
  mình lấy đủ dữ liệu trong service.

## C9. Pagination và sorting (slide 11)

```text
GET /api/products?page=0&size=5&sort=name,asc
  → PageableHandlerMethodArgumentResolver (Spring Data web support, Boot tự cấu hình)
       đọc page / size / sort → tạo Pageable (thiếu tham số nào thì lấy từ @PageableDefault)
  → ProductService.getProducts(pageable)
  → productRepository.findAll(pageable)          → 2 câu SQL: lấy 1 trang + count
  → Page<Product>.map(this::toResponse)          → Page<ProductResponse> (giữ nguyên metadata)
  → Jackson ghi Page ra JSON
```

**Giải thích trong lớp:**
> `findAll()` không tham số là "danger path": bảng một triệu dòng thì kéo hết một triệu dòng vào bộ
> nhớ. Với `Pageable`, database chỉ trả đúng một trang. Client gửi `page`, `size`, `sort` trên query
> string; không gửi thì dùng mặc định ở `@PageableDefault(size = 10, sort = "name")`. Trang **bắt
> đầu từ 0**.

**Bên dưới:**
- Tham số mặc định là `page`, `size`, `sort`. Cú pháp sort: `sort=price,desc`. Sort nhiều cột thì lặp
  tham số: `sort=price,desc&sort=name,asc`. Không ghi chiều thì là `asc`.
- Không có `@PageableDefault` thì mặc định của Spring Data là `size=20`, không sort.
- **Giới hạn 2000**: `size=1000000` bị cắt âm thầm xuống 2000, vẫn `200 OK`. Giá trị không hợp lệ
  (ví dụ `page=-1`) bị đưa về giá trị hợp lệ, **không** trả `400`. Chỉnh được qua
  `spring.data.web.pageable.max-page-size`, `default-page-size`, `one-indexed-parameters`…
- **`sort` của client thay thế sort mặc định**, không cộng thêm. `?sort=price,desc` → chỉ sort theo
  price, `name` trong `@PageableDefault` bị bỏ.
- **SQL thật** (xem được khi bật `show-sql`, đại ý trên H2):
  `select ... from products p1_0 order by p1_0.name offset ? rows fetch first ? rows only`
  (trang đầu có thể không có phần `offset`), rồi thêm
  `select count(p1_0.id) from products p1_0` để tính `totalElements`/`totalPages`. Nếu trang đầu
  chưa đầy (ví dụ 9 sản phẩm, `size=10`) thì Spring Data đã biết tổng số nên **bỏ qua** câu count.
- **JSON của `Page`** (mặc định, chế độ "DIRECT"): `content` + một loạt field `pageable`,
  `totalElements`, `totalPages`, `size`, `number`, `sort`, `first`, `last`, `numberOfElements`,
  `empty`. Lần đầu serialize, Spring Data in **một lần** dòng WARN (đại ý: serialize `PageImpl` trực
  tiếp không được hỗ trợ, cấu trúc JSON không đảm bảo ổn định).
- `spring.data.web.pageable.serialization-mode: via-dto` → JSON gọn và ổn định:
  `{"content": [...], "page": {"size", "number", "totalElements", "totalPages"}}`, hết WARN. Đổi chế
  độ là **đổi contract**: `$.totalElements` thành `$.page.totalElements` → client và test phải sửa.
- `sort=unknownField` → `PropertyReferenceException` (*"No property 'unknownField' found for type
  'Product'"*) → không có handler → `500` với body mặc định của Boot, không phải `ApiError`. Trong
  MockMvc nó hiện ra dưới dạng `ServletException` bị ném ra (xem C14).

**Hiểu lầm:**
- "Có `Pageable` là hết vấn đề hiệu năng." → Offset sâu (`page=90000`) thì database vẫn phải đọc rồi
  bỏ qua các dòng phía trước; câu `count` trên bảng lớn/filter phức tạp cũng tốn. Pageable giải quyết
  chuyện *bộ nhớ và response size*, chưa chắc giải quyết chuyện *DB load*.
- "Sort theo `name` là đủ ổn định." → `name` không unique. SQL không quy định thứ tự giữa các dòng
  trùng giá trị sort, nên giữa trang 0 và trang 1 có thể **lặp hoặc sót** dòng. Nên thêm cột unique
  làm tie-breaker: `@PageableDefault(size = 10, sort = {"name", "id"})`. Nhưng nhớ: client gửi `sort`
  thì tie-breaker mặc định cũng mất → muốn chắc thì thêm `id` trong service
  (`pageable.getSort().and(Sort.by("id"))`).
- "Trả thẳng `Page` là sai." → Không sai, API vẫn chạy; chỉ là JSON phụ thuộc chi tiết nội bộ của thư
  viện. API public thường bọc trong DTO riêng — đúng pro-tip slide 11, cùng lý do không trả entity.

**Câu hỏi follow-up:**
- *"Client gửi `sort` theo field bất kỳ có bị SQL injection không?"* → Với `findAll(Pageable)`,
  Spring Data kiểm tra tên field với entity trước khi dựng query (nên mới ra
  `PropertyReferenceException`). Nhưng nó cho sort theo **mọi** field của entity → API thật nên có
  **allow-list** (chỉ cho `name`, `price`…) và trả `400` cho field lạ, ví dụ thêm
  `@ExceptionHandler(PropertyReferenceException.class)` → `400`. Với native query tự viết thì phải tự
  cẩn thận.
- *"`Page` với `Slice` khác gì?"* → `Page` biết tổng số (cần câu count). `Slice` chỉ biết "còn trang
  sau không", **không chạy count** — hợp với nút "Load more". Khai báo kiểu
  `Slice<Product> findByActiveTrue(Pageable pageable)`.
- *"Bảng rất lớn, infinite scroll thì sao?"* → Keyset pagination: "lấy 10 dòng sau cặp
  `(name, id)` cuối cùng", tận dụng được index. Spring Data hỗ trợ qua `Window<T>` và
  `ScrollPosition.keyset()` — xem mục *Scrolling* trong docs Spring Data. Đánh đổi: không nhảy thẳng
  tới trang 500 được.
- *"PowerShell gửi `?page=0&size=5&sort=price,desc` bị lỗi?"* → `&` và `,desc` không có dấu nháy bị
  PowerShell hiểu sai. Dùng `curl.exe` (trong Windows PowerShell 5.1, `curl` là alias của `Invoke-WebRequest`) và **bọc URL
  trong dấu nháy kép**.

## C10. SKU trùng, `409` và race condition

**Giải thích trong lớp:**
> Cột `sku` có `@Column(unique = true)`, nên database có unique constraint. Trước bước 09, POST trùng
> SKU thì database chặn, exception không ai xử lý → `500`. Đó là lỗi của client mà báo như lỗi
> server. Bước 09 thêm `existsBySku` ở đầu `create`: trùng thì ném `DuplicateSkuException`, handler
> trả `409 Conflict` với code `DUPLICATE_SKU`.

**Bên dưới:**
- Trước step-09: H2 báo lỗi mã **23505** (unique violation) → Hibernate bọc lại → proxy repository của
  Spring dịch sang **`DataIntegrityViolationException`** → `GlobalExceptionHandler` không có handler
  cho nó (và không có catch-all) → lỗi đi tới `/error` → `500` body mặc định
  `{timestamp, status, error, path}`.
- Vì `Product` dùng `GenerationType.IDENTITY`, câu `INSERT` chạy **ngay trong `save()`** để lấy id →
  exception bay ra từ `save()`, transaction của `create` bị rollback.
- Sau step-09: `existsBySku` sinh SQL
  `select p1_0.id from products p1_0 where p1_0.sku=? fetch first ? rows only` → có dòng thì
  `DuplicateSkuException` (một `RuntimeException`) → `409`.
- **Check-then-act không an toàn khi chạy đồng thời:**

```text
Request A: existsBySku("KB-100") → false
Request B: existsBySku("KB-100") → false      (A chưa commit)
Request A: INSERT → OK, commit
Request B: INSERT → unique constraint chặn → DataIntegrityViolationException → 500
```

- `@Transactional` **không khóa** gì ở đây. Người bảo vệ thật là **unique constraint trong DB**.
  `existsBySku` chỉ để trả lỗi thân thiện cho trường hợp thường gặp.
- Muốn trường hợp race cũng ra `409`: thêm
  `@ExceptionHandler(DataIntegrityViolationException.class)` trả `409`. Lưu ý exception này cũng bao
  gồm lỗi `NOT NULL`, khóa ngoại… → đừng mặc định gắn tất cả là `DUPLICATE_SKU`; hoặc dùng code chung
  kiểu `CONFLICT`, hoặc bắt trong service quanh lệnh lưu rồi ném lại `DuplicateSkuException`.
- Nếu entity dùng `SEQUENCE` thay vì `IDENTITY`, `INSERT` có thể bị hoãn tới lúc flush/commit → lỗi
  nổ **sau** khi method service đã chạy xong, `try/catch` quanh `save()` không bắt được → dùng
  `saveAndFlush()` nếu muốn bắt ngay trong service.

**Hiểu lầm:**
- "Có `existsBySku` rồi thì bỏ unique constraint cũng được." → Không. Bỏ constraint là race condition
  tạo ra **dữ liệu trùng thật** trong DB, không chỉ là một response `500`.
- "Thêm `synchronized` vào `create` là xong." → Chỉ đúng trong một JVM. Chạy 2 instance sau load
  balancer là hỏng. Isolation `SERIALIZABLE` thì tùy DB, không nên hứa.
- "`unique = true` luôn tạo constraint." → Chỉ khi Hibernate sinh schema (`ddl-auto`). Dùng
  Flyway/Liquibase thì phải tự viết `UNIQUE` trong script migration.

**Câu hỏi follow-up:**
- *"`409` hay `400` cho SKU trùng?"* → Request đúng định dạng, chỉ xung đột với trạng thái hiện tại
  của server → `409 Conflict` hợp ngữ nghĩa hơn. Quan trọng nhất vẫn là nhất quán.
- *"`kb-100` và `KB-100` có bị coi là trùng không?"* → Tùy database và collation. H2 mặc định so
  sánh chuỗi phân biệt hoa thường; MySQL với collation mặc định thì thường không phân biệt. Muốn chắc
  thì chuẩn hóa SKU (ví dụ `toUpperCase()`) trước khi lưu.
- *"Hai người cùng **sửa** một product thì sao?"* → Mặc định người ghi sau thắng. Cách phổ biến là
  **optimistic locking**: thêm field `@Version Long version`. Hibernate chạy
  `UPDATE ... WHERE id = ? AND version = ?`; không dòng nào khớp → exception, Spring dịch thành
  `OptimisticLockingFailureException` → map sang `409`. Hôm nay chưa có API update nên chưa cần.

## C11. Externalized configuration (slide 12)

**Giải thích trong lớp:**
> Cùng một file jar phải chạy ở máy dev, môi trường test, production — mỗi nơi database khác, port
> khác, log khác. Nên cấu hình nằm **ngoài** code: trong `application.yml`, và giá trị theo môi
> trường thì truyền vào bằng biến môi trường hoặc tham số lúc chạy. Không build lại jar.

**Bên dưới:**
- `application.yml` trong `src/main/resources` → nằm trong classpath → Boot tự đọc lúc khởi động
  vào `Environment`. Boot cũng đọc file trong **thư mục hiện tại lúc chạy lệnh** (`./application.yml`, `./config/`), không nhất thiết là thư mục chứa jar; file ngoài
  jar ghi đè file trong jar.
- `${DB_URL:jdbc:h2:mem:demo}` = lấy property/biến môi trường `DB_URL`; không có thì dùng phần sau
  **dấu `:` đầu tiên** làm default. Vì tách ở dấu `:` đầu tiên nên default `jdbc:h2:mem:demo` (có
  nhiều dấu `:`) vẫn nguyên vẹn. `${DB_PASSWORD:}` = default là chuỗi rỗng.
- Viết `${DB_URL}` **không có** default mà thiếu biến → Boot giữ nguyên chuỗi `${DB_URL}` làm URL, nên app
  không khởi động được với lỗi kết nối/driver (đại ý: driver không chấp nhận jdbcUrl `${DB_URL}`, kèm lỗi Hibernate không xác định được dialect). Chỉ `@Value("${DB_URL}")` mới báo *Could not resolve placeholder 'DB_URL'*.
- **Relaxed binding**: biến môi trường `SERVER_PORT` tự map vào `server.port` (quy tắc: `.` → `_`,
  bỏ `-`, viết hoa). Nghĩa là `SPRING_DATASOURCE_URL` cũng ghi đè được `spring.datasource.url` mà
  không cần placeholder. Placeholder `${DB_URL:...}` chỉ để có tên ngắn và default cho dev.
- **Thứ tự ưu tiên** (cao → thấp, phần liên quan):

| Nguồn | Ví dụ |
|---|---|
| Tham số dòng lệnh | `java -jar app.jar --server.port=7070` |
| Java system property | `java -Dserver.port=7070 -jar app.jar` |
| Biến môi trường OS | `SERVER_PORT=9090` |
| `application-{profile}.yml` | `application-dev.yml` |
| `application.yml` | mặc định |

  (Danh sách đầy đủ còn vài nguồn khác, ví dụ `SPRING_APPLICATION_JSON`, và trong test thì test
  properties cao nhất.) Slide 12 chỉ vẽ **ba tầng dưới** và biến môi trường là cao nhất trong ba tầng
  đó — **đúng**, chỉ là rút gọn. Không nói "slide sai".
- YAML: thụt lề bằng **dấu cách**, không dùng tab. Hai key gốc trùng nhau (ví dụ hai dòng `spring:` ở
  gốc) → Boot báo lỗi khởi động.

**`@Value` vs `@ConfigurationProperties`:**

| | `@Value("${app.x}")` | `@ConfigurationProperties(prefix = "app")` |
|---|---|---|
| Hợp cho | 1–2 giá trị lẻ | một nhóm config liên quan |
| Type-safe, gom vào một record | ❌ | ✅ |
| Relaxed binding | hạn chế | ✅ đầy đủ |
| Validate bằng `@Validated` | ❌ | ✅ |
| SpEL | ✅ | ❌ |

Docs Spring Boot khuyên gom config của mình vào class/record `@ConfigurationProperties` (đăng ký bằng
`@ConfigurationPropertiesScan` hoặc `@EnableConfigurationProperties`).

**Hiểu lầm:**
- "Để secret trong `application-prod.yml` là được vì file đó chỉ dùng cho prod." → File vẫn nằm trong
  Git và trong jar. Secret đến từ biến môi trường, Kubernetes Secret, hoặc secret manager (Vault…).
  Lỡ commit password thì phải **đổi password**; xóa khỏi lịch sử Git thôi là chưa đủ.
- "Biến môi trường là nguồn cao nhất." → Chỉ cao nhất trong ba tầng trên slide; `-D` và `--x=y` còn
  cao hơn.

**Câu hỏi follow-up:**
- *"Set `SERVER_PORT=9090` rồi chạy `--server.port=7070` thì port nào?"* → `7070` — tham số dòng lệnh
  thắng.
- *"`application.yml` hay `application.properties`?"* → Chức năng như nhau, khác cú pháp (cây vs
  `key=value` phẳng). Cùng thư mục có cả hai thì `.properties` thắng với key trùng → team nên chọn một
  loại. `@PropertySource` không load được YAML.
- *"Trên PowerShell set biến môi trường thế nào?"* → `$env:SERVER_PORT="9090"` — chỉ có hiệu lực
  trong cửa sổ PowerShell đó, rồi chạy app từ chính cửa sổ đó.

## C12. Spring Profiles (slide 12)

**Giải thích trong lớp:**
> Profile là một cái tên môi trường — `dev`, `prod` — dùng để bật thêm một bộ cấu hình. Profile
> `dev` đang active thì Boot đọc `application.yml` **rồi** đọc thêm `application-dev.yml`, key nào
> trùng thì bản của profile thắng. Cùng code, khác hành vi theo môi trường.

**Bên dưới:**
- Cách chọn profile:

```powershell
# 1. Trong application.yml của demo (default là dev, env var ghi đè được)
#    spring.profiles.active: ${SPRING_PROFILES_ACTIVE:dev}

# 2. Biến môi trường (PowerShell)
$env:SPRING_PROFILES_ACTIVE="prod"

# 3. Tham số khi chạy jar
java -jar target/product-api-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod

# 4. Java system property — PowerShell phải bọc cả tham số -D trong dấu nháy
java "-Dspring.profiles.active=prod" -jar target/product-api-0.0.1-SNAPSHOT.jar

# 5. Maven plugin
mvn spring-boot:run "-Dspring-boot.run.profiles=prod"
```

  IntelliJ: ô *Active profiles* trong run configuration. Trong test: `@ActiveProfiles("test")`.
- **Merge, không thay thế**: `application-dev.yml` chỉ có `logging` và `show-sql`, nhưng datasource,
  `server.port`… vẫn lấy từ `application.yml`.
- Nhiều profile cùng lúc: `prod,eu` — nếu hai file cùng khai báo một key thì profile đứng **sau**
  thắng.
- Dòng log để kiểm tra (luôn nhìn dòng này đầu tiên):
  - `The following 1 profile is active: "dev"`
  - không có profile nào: `No active profile set, falling back to 1 default profile: "default"`
- Khác biệt nhìn thấy được giữa `dev` và `prod` trong demo: chỉ có các dòng SQL của `show-sql` và
  dòng `log.debug("Creating product sku={}", ...)` của mình. Prod không in hai thứ đó.
- Ngoài file cấu hình, `@Profile("dev")` trên `@Component`/`@Bean` làm bean đó chỉ được tạo khi profile
  đó active.

**Hiểu lầm:**
- "Gõ sai tên profile thì Spring báo lỗi." → **Không.** `prdo` được nhận âm thầm: không có file
  `application-prdo.yml` thì app chạy với cấu hình gốc. Log vẫn in `"prdo"` → luôn đọc dòng profile.
- "Đặt `spring.profiles.active` trong `application-dev.yml` để kích hoạt thêm profile." → Lỗi khởi động
  (`InvalidConfigDataPropertyException`). Chỉ đặt nó ở `application.yml` hoặc truyền từ ngoài vào.
  Muốn gom nhiều profile thì có *profile groups* (`spring.profiles.group.*`).
- "Profile là cách quản lý secret." → Dòng cuối slide 12: **Profiles are not secret management.**
  Profile đổi *hành vi*; secret vẫn đến từ môi trường chạy.

**Câu hỏi follow-up:**
- *"Default `dev` có nguy hiểm không?"* → Có một rủi ro: deploy prod mà **quên** set
  `SPRING_PROFILES_ACTIVE` thì app chạy âm thầm ở `dev` (bật `show-sql`, log DEBUG). Hôm nay để `dev`
  cho tiện demo; dự án thật nên để default an toàn hoặc bắt buộc truyền profile khi deploy.
- *"Docker/Kubernetes chọn profile thế nào?"* → Qua biến môi trường, ví dụ
  `docker run -e SPRING_PROFILES_ACTIVE=prod ...` hoặc khai báo `env` trong Deployment. Password thì
  lấy từ Kubernetes Secret / secret manager, tùy hạ tầng dự án.
- *"PowerShell chạy `mvn spring-boot:run -Dspring-boot.run.profiles=prod` mà profile không đổi?"* →
  PowerShell tách tham số `-D...` có dấu `.` sai cách. Bọc cả tham số trong dấu nháy kép như ví dụ trên,
  rồi kiểm tra dòng `The following 1 profile is active: "prod"`.

## C13. Logging và xem SQL

**Giải thích trong lớp:**
> Code gọi logger qua **SLF4J** — một lớp API chung. Bên dưới, Spring Boot dùng **Logback** để thực sự
> ghi log. Mỗi dòng log có một level; mình cấu hình level theo package trong file YAML. Profile `dev`
> để `com.example.product: DEBUG` nên thấy dòng `log.debug`; `prod` để `INFO` nên dòng đó bị bỏ.

**Bên dưới:**
- Level từ chi tiết đến nghiêm trọng: `TRACE` < `DEBUG` < `INFO` < `WARN` < `ERROR`. Đặt level `INFO`
  nghĩa là in `INFO` trở lên. Root mặc định là `INFO`.
- `logging.level.<package-hoặc-class>: LEVEL` — áp cho package đó và mọi package con.
- Khai báo logger: `private static final Logger log = LoggerFactory.getLogger(ProductService.class);`
  (import `org.slf4j.Logger`, `org.slf4j.LoggerFactory`). Logback có sẵn qua starter, không cần thêm
  dependency.
- Dòng log mặc định gồm thời gian, level, PID, thread, tên logger viết tắt
  (`c.e.product.service.ProductService`) rồi message. Ghi ra file: `logging.file.name`.
- **Message có tham số**: `log.debug("Creating product sku={}", request.sku())`. Không nối chuỗi
  (`"sku=" + sku`): khi level DEBUG đang tắt, SLF4J không cần dựng chuỗi. Log exception: truyền exception
  làm **tham số cuối** (không cần `{}`) để có stack trace: `log.error("Save failed sku={}", sku, ex)`.
- **Xem SQL — hai cách:**

| Cách | Cấu hình | Đặc điểm |
|---|---|---|
| `show-sql` | `spring.jpa.show-sql: true` | In thẳng ra stdout với tiền tố `Hibernate: `, không qua logging, **không có giá trị tham số** (chỉ thấy `?`) |
| Logger của Hibernate | `logging.level.org.hibernate.SQL: DEBUG` | Đi qua Logback (có thời gian, level, bật/tắt theo profile như log khác) |
| + giá trị tham số | `logging.level.org.hibernate.orm.jdbc.bind: TRACE` | In giá trị bind cho từng `?` |

  Bật cả `show-sql` lẫn `org.hibernate.SQL=DEBUG` thì mỗi câu SQL in **hai lần**. Muốn SQL xuống dòng dễ
  đọc: `spring.jpa.properties.hibernate.format_sql: true`.
- Demo dùng `show-sql` vì đơn giản và slide 11 ghi đúng property này.

**Hiểu lầm:**
- "Bật SQL log ở production cho dễ debug." → Log rất nhiều, tốn hiệu năng, và với bind TRACE có thể
  **lộ dữ liệu nhạy cảm**. Vì vậy demo chỉ bật trong profile `dev`.
- "Tên category bind trong tài liệu là `org.hibernate.type.descriptor.sql`." → Đó là tên của Hibernate
  5 (tài liệu cũ). Hibernate 6+ dùng `org.hibernate.orm.jdbc.bind`.
- "`System.out.println` cũng được." → Không có level, không tắt được theo môi trường, không có
  thời gian/thread. Luôn dùng logger.

**Câu hỏi follow-up:**
- *"N+1 là gì?"* → Một query lấy N dòng cha (ví dụ N order), rồi với mỗi dòng lại bắn thêm một query
  lấy dữ liệu con (customer) → 1 + N query. `Product` hôm nay không có quan hệ nên không gặp. Phát hiện
  nhanh nhất: bật SQL log và **đếm số câu SELECT**. Cách xử lý: `JOIN FETCH` trong `@Query`,
  `@EntityGraph`, hoặc batch fetching của Hibernate. Đổi sang `EAGER` không phải là cách sửa.
- *"Log có nên in cả request body?"* → Cẩn thận: body có thể chứa dữ liệu cá nhân, password. Log vừa đủ
  để truy vết (id, sku), không log secret.

## C14. Testing (slide 13)

**Giải thích trong lớp:**
> Hai loại test, mỗi loại bắt một kiểu lỗi khác nhau. **Unit test** test một class, thay dependency
> bằng mock — không Spring, không database, chạy vài chục mili giây. **Integration test** khởi động
> Spring context thật, request đi qua JSON → validation → controller → service → repository → H2 —
> chậm hơn (vài giây) nhưng gần thực tế. Nhiều unit test cho logic, ít integration test cho các luồng
> chính.

**Bên dưới — unit test (`ProductServiceTest`):**
- `@ExtendWith(MockitoExtension.class)` bật Mockito cho JUnit. `@Mock ProductRepository` = repository
  giả. `@InjectMocks ProductService` = service **thật**, Mockito truyền mock vào **constructor** — lợi
  ích trực tiếp của constructor injection (slide 6).
- Arrange – Act – Assert: `when(repo.findById(1L)).thenReturn(Optional.of(product))` → gọi
  `productService.getById(1L)` → `assertEquals(...)` → `verify(repo).findById(1L)`.
- Test nghiệp vụ SKU trùng (branch `solution`): stub `existsBySku` trả `true`,
  `assertThrows(DuplicateSkuException.class, ...)`, rồi `verify(productRepository, never()).save(any())`.
- `MockitoExtension` chạy chế độ **STRICT_STUBS**: stub một method mà test không gọi tới → test
  **fail** với `UnnecessaryStubbingException`. Đây là tính năng, không phải lỗi — nó bắt stub thừa hoặc
  gõ nhầm tham số.
- Trên JDK 21 console có chữ đỏ, đại ý: *"Mockito is currently self-attaching to enable the
  inline-mock-maker…"* và *"A Java agent has been loaded dynamically…"* → **cảnh báo, test vẫn xanh.**
- `assertEquals` chọn từ `org.junit.jupiter.api.Assertions` (tham số: expected trước, actual sau).
  IntelliJ gọi JUnit Jupiter là "JUnit5"; project dùng JUnit 6, cùng annotation `org.junit.jupiter.api`.

**Bên dưới — integration test (`ProductApiIntegrationTest`):**
- `@SpringBootTest` mặc định `webEnvironment = MOCK`: load đủ context nhưng **không** start Tomcat,
  không mở port → không đụng app đang chạy ở 8080. Test chạy JVM riêng → H2 in-memory riêng.
- `@AutoConfigureMockMvc` (package `org.springframework.boot.webmvc.test.autoconfigure`) cho một
  `MockMvc`: gửi request thẳng vào `DispatcherServlet` trong bộ nhớ.
- **Context caching**: các test class có cùng cấu hình dùng chung một context → dùng chung DB H2.
  Khác cấu hình (khác `@MockitoBean`, `@ActiveProfiles`, properties…) → tạo context mới → chậm hơn.
- **Không rollback**: test MockMvc không có `@Transactional` nên dữ liệu còn lại cho test sau → mỗi test
  dùng **SKU riêng** (test 409 dùng `IT-DUP-001`). Thêm `@Transactional` lên test thì với môi trường
  `MOCK` sẽ rollback sau mỗi test, nhưng khi đó test không đi qua commit thật; với `RANDOM_PORT` thì
  server chạy thread khác nên rollback không có tác dụng.
- Exception **không có handler** (ví dụ `sort=unknownField`): trên Tomcat thật thành JSON `500` qua
  `/error`; trong MockMvc thì **ném ra `ServletException`** vì MockMvc không mô phỏng bước forward
  sang `/error` của container.
- Test chạy với profile `dev` (default trong `application.yml`) → console test cũng có SQL. Muốn tách
  riêng thì `@ActiveProfiles("test")` + `application-test.yml`.

**Test slice và công cụ khác:**

| Công cụ | Load gì | Dùng khi |
|---|---|---|
| Mockito thuần (`@Mock`) | không Spring | logic trong service |
| `@WebMvcTest(ProductController.class)` | chỉ tầng web: controller, advice, Jackson… | test mapping, validation, status code; service thay bằng `@MockitoBean` |
| `@DataJpaTest` | chỉ JPA + repository, **tự rollback** mỗi test | test query của repository; cần thêm `spring-boot-starter-data-jpa-test` |
| `@SpringBootTest` + MockMvc | toàn bộ context | luồng chính end-to-end trong app |
| Testcontainers | DB thật (PostgreSQL…) trong Docker | bug chỉ lộ trên DB thật; máy dev và CI cần Docker |

**Mỗi loại test bắt được gì:**

| Lỗi | Unit test service | Integration test |
|---|---|---|
| Quên `@Valid` trên `@RequestBody` | ❌ (service không biết) | ✅ test body sai mong `400` sẽ đỏ |
| Quên `@ExceptionHandler` / sai status code | ❌ | ✅ |
| Rule SKU trùng không được kiểm tra | ✅ | ✅ |
| Quên `@Service` / sai wiring | ❌ (tự `new` bằng Mockito) | ✅ context không khởi động |
| Sai tên field JSON | ❌ | ✅ `jsonPath` đỏ |
| Nhánh logic hiếm (nhiều trường hợp) | ✅ nhanh, rẻ | tốn thời gian |

**Hiểu lầm:**
- "Có integration test thì không cần unit test." → Integration test chậm; dùng nó để phủ mọi nhánh logic
  thì bộ test chạy rất lâu. Kim tự tháp: đáy nhiều unit test, lên trên ít integration test.
- "`@Mock` và `@MockitoBean` như nhau." → `@Mock` là Mockito thuần, không có Spring. `@MockitoBean` thay
  một **bean trong ApplicationContext** bằng mock. Boot 4 đã **xóa** `@MockBean` — tài liệu cũ dùng nó.
- "Test xanh là code đúng." → Test chưa bao giờ đỏ thì chưa chắc nó kiểm tra gì. Thử sửa expected cho
  đỏ một lần.

**Câu hỏi follow-up:**
- *"Test chạy trong khi app đang chạy ở 8080 có đụng nhau không?"* → Không: `MOCK` không mở port, H2 của
  test nằm trong JVM test. Chỉ `DEFINED_PORT` mới dùng port thật từ `server.port`.
- *"Test lúc pass lúc fail tùy thứ tự chạy?"* → Gần như luôn do dữ liệu dùng chung (context cache + không
  rollback). Mỗi test tự tạo dữ liệu với giá trị unique, không dựa vào thứ tự chạy; assert cả `$.code`,
  không chỉ status.
- *"Coverage bao nhiêu là đủ?"* → Không có con số chuẩn; 70–80% chỉ là thói quen phổ biến. Coverage cho
  biết dòng nào chạy qua, không cho biết assert có đúng không. IntelliJ có *Run with Coverage*; JaCoCo
  không được Boot BOM quản lý version, phải tự khai báo.
- *"`TestRestTemplate` đâu?"* → Boot 4: cần `@AutoConfigureTestRestTemplate` + module
  `spring-boot-resttestclient`; lựa chọn mới là `RestTestClient`.

## C15. Spring Boot 4 / Spring Framework 7 — những gì khác với tài liệu cũ trên mạng

**Giải thích trong lớp:**
> Phần lớn tutorial trên mạng viết cho Spring Boot 2 hoặc 3. **Khái niệm** không đổi: DI, controller,
> DTO, validation, JPA, profile, test — hôm nay học gì dùng được cho Boot 2/3. Cái đổi là **tên
> package, tên dependency, vài annotation**. Copy code cũ mà không compile thì nghĩ ngay tới bảng dưới.

**Bên dưới — bảng đối chiếu:**

| Tài liệu cũ | Boot 4 / Spring Framework 7 (hôm nay) |
|---|---|
| `javax.persistence.*`, `javax.validation.*` (Boot 2) | `jakarta.persistence.*`, `jakarta.validation.*` (từ Boot 3) |
| `spring-boot-starter-web` | `spring-boot-starter-webmvc` (tên cũ bị deprecated) |
| Test chỉ cần `spring-boot-starter-test` | `spring-boot-starter-webmvc-test` (và starter test theo từng module) |
| `@MockBean` / `@SpyBean` | **đã xóa** → `@MockitoBean` / `@MockitoSpyBean` (`org.springframework.test.context.bean.override.mockito`) |
| `@AutoConfigureMockMvc` ở `org.springframework.boot.test.autoconfigure.web.servlet` (Boot 3) | `org.springframework.boot.webmvc.test.autoconfigure` |
| `@WebMvcTest` cùng package cũ | `org.springframework.boot.webmvc.test.autoconfigure` |
| `@DataJpaTest` có sẵn | cần thêm `spring-boot-starter-data-jpa-test` |
| `TestRestTemplate` có sẵn với `@SpringBootTest` | cần `@AutoConfigureTestRestTemplate` + `spring-boot-resttestclient`; mới: `RestTestClient` |
| Chỉ thêm `flyway-core` | cần `spring-boot-starter-flyway` |
| H2 console có sẵn trong Boot, chỉ cần bật property | là module riêng, phải thêm dependency `spring-boot-h2console` |
| Jackson 2: `com.fasterxml.jackson.databind.*` | Jackson 3: `tools.jackson.*`; annotation vẫn `com.fasterxml.jackson.annotation` |
| `HttpStatus.UNPROCESSABLE_ENTITY` | `HttpStatus.UNPROCESSABLE_CONTENT` (tên cũ deprecated) |
| Tự làm API versioning (URL/header) | có sẵn: `@GetMapping(version = "1.1")` + `ApiVersionConfigurer` |
| `@Nullable` của Spring | null-safety theo JSpecify |
| JUnit 5 | JUnit 6 — vẫn là Jupiter, cùng annotation `org.junit.jupiter.api` |
| Testcontainers 1.x (`org.testcontainers:postgresql`) | Testcontainers 2.0.x, artifact đổi tên (`testcontainers-postgresql`…) |
| springdoc-openapi 2.x | springdoc-openapi 3.x cho Boot 4 |
| Undertow làm embedded server | Boot 4 không còn hỗ trợ Undertow (Tomcat/Jetty) |
| Hibernate 5: SQL alias `product0_`, bind log `org.hibernate.type.descriptor.sql` | Hibernate 6+: alias `p1_0`, bind log `org.hibernate.orm.jdbc.bind` |
| `/api/products/` khớp `/api/products` (Boot 2) | Từ Spring 6 (Boot 3) không còn khớp trailing slash → `404` |

Baseline Boot 4: **Java 17** trở lên (demo chạy JDK 21), **Jakarta EE 11**, Tomcat 11, Hibernate 7.

**Lịch sử `javax` → `jakarta` (để trả lời "sao import khác tutorial"):**
- Java EE được chuyển từ Oracle sang Eclipse Foundation và đổi tên thành **Jakarta EE**. Từ Jakarta EE 9,
  namespace đổi từ `javax.*` sang `jakarta.*`.
- **Spring Boot 3 / Spring Framework 6** là bản đầu tiên dùng `jakarta.*` và yêu cầu Java 17. Nên code
  Boot 2 (`javax.persistence.Entity`) không chạy trên Boot 3/4.
- Không phải mọi `javax` đều đổi: `javax.sql.DataSource`, `javax.crypto…` là của **Java SE**, vẫn giữ
  nguyên. Chỉ các API Jakarta EE (persistence, validation, servlet, transaction…) mới đổi.

**Hiểu lầm:**
- "Boot 4 là học lại từ đầu." → Không. Kiến trúc và annotation cốt lõi (`@RestController`, `@Service`,
  `@Transactional`, `@Entity`, `@Valid`) không đổi. Đổi chủ yếu ở dependency, package và các API đã
  deprecated từ trước.
- "Import `jakarta.transaction.Transactional` cũng như nhau." → Không có `readOnly`. Trong code Spring,
  dùng `org.springframework.transaction.annotation.Transactional`.
- "Tutorial có `@MockBean` thì project mình sai." → Tutorial viết cho Boot 3 trở về trước. Ở Boot 4
  thay bằng `@MockitoBean`.

**Câu hỏi follow-up:**
- *"Project của mình còn Boot 2/3, học Boot 4 có ích không?"* → Có. Mọi khái niệm hôm nay áp dụng
  nguyên. Khác biệt khi mang về:
  - **Boot 3**: code gần như giống hệt; chỉ khác tên starter (`spring-boot-starter-web`), package của
    test annotation, và dùng `@MockBean` (từ Boot 3.4 đã deprecated, khuyên chuyển sang `@MockitoBean`).
  - **Boot 2**: thêm `javax.*` thay vì `jakarta.*`, có thể chạy Java 8/11. Boot 2 đã hết hỗ trợ bản
    open source → nên có kế hoạch nâng cấp.
  - Nâng cấp nên đi **từng bước**: lên bản 3.x cuối trước, xử lý hết cảnh báo deprecated, rồi mới lên
    4.x, theo *Migration Guide* chính thức của Spring Boot. Có công cụ tự động (ví dụ OpenRewrite) hỗ
    trợ đổi import, nhưng vẫn phải chạy lại toàn bộ test.
- *"Làm sao biết một bài viết trên mạng dành cho version nào?"* → Nhìn import: `javax.persistence` →
  Boot 2; có `@MockBean` hoặc `spring-boot-starter-web` → Boot 3 trở về trước; SQL alias kiểu
  `product0_` → Hibernate 5. Nguồn đáng tin nhất vẫn là docs chính thức của đúng version
  (`docs.spring.io`).
- *"IntelliJ gợi ý import lạ thì chọn thế nào?"* → Nhiều class trùng tên đều có thật trên classpath
  (`java.awt.print.Pageable`, `org.hibernate.query.Page`, `jakarta.transaction.Transactional`…).
  Quy tắc: Spring Data → `org.springframework.data.domain`; transaction → `org.springframework.transaction.annotation`;
  JPA → `jakarta.persistence`; test → `org.junit.jupiter.api` và `org.mockito`.
