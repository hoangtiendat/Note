# Kho câu hỏi của học viên (Q&A bank)

Tài liệu chuẩn bị cho trainer — buổi "Java Spring Boot — From Controller to Database" (Spring Boot 4.1.1,
Spring Framework 7, Hibernate 7, JUnit 6). Câu hỏi xếp theo thứ tự nội dung của buổi học.

## Cách dùng

1. **Nhắc lại câu hỏi** trước khi trả lời, nhất là câu gõ trong chat: "Bạn A hỏi: …". Cả lớp nghe được
   câu hỏi, và mình có thêm vài giây để nghĩ.
2. **Nói câu trả lời ngắn trước** (phần *Trả lời ngắn*). Chỉ đi vào phần *Bên dưới* khi người hỏi muốn
   biết thêm hoặc lớp đang có thời gian.
3. **Không chắc thì nói thật.** Mỗi câu có sẵn một dòng *Nếu không chắc* để nói. Câu an toàn chung:
   "Mình không muốn trả lời sai — mình kiểm tra lại và gửi trong nhóm chat." Ghi lại tên người hỏi.
4. **Parking lot:** câu dài, lạc đề hoặc cần chạy thử → "Câu này hay, mình ghi vào parking lot, cuối buổi
   (hoặc sau buổi) mình trả lời." Không để một câu hỏi làm trễ checkpoint của demo.
5. Mỗi câu tối đa khoảng 2 phút. 11:15 luôn chuyển sang tổng kết, dù đang trả lời dở.

## Ký hiệu

| Tag | Người hỏi thường là |
|---|---|
| `[Fresher]` | Mới đi làm, còn mơ hồ về khái niệm cơ bản |
| `[Dev]` | Đang làm dự án Java/Spring, hỏi để áp dụng ngay |
| `[Senior]` | Nhiều kinh nghiệm, hay hỏi bẫy hoặc bắt bẻ chi tiết |
| `[.NET]` / `[Node]` | Chuyển từ .NET / Node.js sang, hay so sánh với nền tảng cũ |
| `[QA]` | Tester / automation, quan tâm tới status code, format lỗi, dữ liệu biên |

Độ khó: ★ cơ bản · ★★ trung bình · ★★★ nâng cao hoặc câu bẫy.
Dấu **🔥 Hay gặp** = câu rất có khả năng bị hỏi, nên đọc trước buổi.

## 🔥 12 câu hay gặp nhất

| # | Câu hỏi | Trả lời một dòng |
|---|---|---|
| [Q3](#q3) | Singleton mà 100 request cùng lúc có race condition không? | An toàn vì service **stateless**, không phải vì là singleton. |
| [Q8](#q8) | Hai bean cùng kiểu thì Spring chọn cái nào? | Không chọn: startup lỗi *"required a single bean, but 2 were found"*. Dùng `@Primary` / `@Qualifier`. |
| [Q17](#q17) | Sao không return entity cho nhanh? | Entity là persistence, DTO là API contract; trả entity là lộ cấu trúc DB. |
| [Q20](#q20) | Gửi `50` mà GET ra `50.00`? | Cột DB `numeric(38,2)`; POST trả object từ request, GET đọc từ DB. |
| [Q22](#q22) | PUT khác PATCH thế nào? | PUT thay toàn bộ, idempotent; PATCH cập nhật một phần, không đảm bảo idempotent. |
| [Q34](#q34) | Sao không thêm `@ExceptionHandler(Exception.class)`? | Được, nhưng nếu không extend `ResponseEntityExceptionHandler` thì 4xx của Spring thành 500. |
| [Q41](#q41) | `ddl-auto: update` dùng cho production được không? | Không. Dùng Flyway/Liquibase + `validate` hoặc `none`. |
| [Q52](#q52) | `existsBySku` rồi mới save, hai request cùng lúc thì sao? | Check-then-act bị race; unique constraint ở DB mới là chốt chặn, map sang 409. |
| [Q54](#q54) | WARN khi trả `Page` là sao? | Cấu trúc JSON của `PageImpl` không ổn định; dùng DTO riêng hoặc `via-dto`. |
| [Q56](#q56) | `sort=unknownField` thì sao? | Hiện tại ra 500 (`PropertyReferenceException`); nên whitelist field và trả 400. |
| [Q60](#q60) | Env var có phải ưu tiên cao nhất không? | Cao nhất trong 3 tầng trên slide 12; `--arg` và `-D` còn cao hơn. |
| [Q69](#q69) | App đang chạy 8080 sao test vẫn pass? | `@SpringBootTest` mặc định là MOCK: không mở port, JVM và H2 riêng. |

---

## 1. Spring Boot & Dependency Injection

<a id="q1"></a>
### Q1. Anh ơi bean là gì vậy? Nó khác gì object mình tự `new`?

`[Fresher]` · ★

**Trả lời ngắn (nói trong lớp):**
> Bean là object do Spring tạo ra, tiêm dependency vào và quản lý vòng đời. Object mình tự `new` thì
> Spring không biết tới: không được inject gì, không có transaction, không có proxy.

**Bên dưới:**
- Container chính là `ApplicationContext`; `SpringApplication.run(...)` trong `ProductApplication` dựng
  nó lên và trả về nó.
- Trong demo: `ProductController`, `ProductService`, và proxy của `ProductRepository` đều là bean.
- Bean chỉ là object Java bình thường (POJO), không có gì "đặc biệt" ngoài việc Spring quản lý nó.

**Nếu bị hỏi tiếp:** *"Nếu em tự `new ProductService(repo)` trong controller thì sao?"* → Chạy được,
nhưng object đó nằm ngoài container: không có proxy nên `@Transactional` trên nó không có tác dụng.

**Nếu không chắc:** "Bean là object Spring tạo và quản lý giúp mình" — đủ cho câu hỏi này, không cần
đi vào `BeanFactory`.

<a id="q2"></a>
### Q2. Bên .NET em có AddSingleton / AddScoped / AddTransient. Spring mặc định là cái nào?

`[.NET]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Mặc định là **singleton**, giống `AddSingleton`: cả app một instance. `prototype` gần giống Transient.
> Web app có thêm scope `request` và `session`, gần với Scoped.

**Bên dưới:**
- Các scope: `singleton` (mặc định), `prototype`, `request`, `session`, `application`, `websocket`.
- Với `prototype`, Spring không quản lý trọn vòng đời (không gọi callback destroy).
- Khác với `DbContext` scoped bên .NET: `EntityManager` inject vào repository là một proxy dùng chung,
  gắn theo transaction hiện tại, nên repository singleton vẫn dùng an toàn.

**Nếu bị hỏi tiếp:** *"Vậy DbContext tương đương gì?"* → Gần nhất là persistence context (`EntityManager`)
— mỗi transaction một cái, Spring tự lo.

**Nếu không chắc:** "Mình chắc chắn default là singleton; chi tiết cơ chế proxy của `EntityManager` mình
gửi link docs sau."

<a id="q3"></a>
### Q3. `ProductService` là singleton, 100 request cùng lúc dùng chung 1 instance thì có race condition không?

`[Senior]` · ★★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Singleton không tự làm class thread-safe. `ProductService` an toàn vì nó **stateless**: field duy nhất
> là `productRepository`, lại là `final`. Dữ liệu của từng request nằm trong tham số và biến local, mỗi
> thread có stack riêng.

**Bên dưới:**
- Mỗi request chạy trên một thread của Tomcat; Spring không `synchronize` gì trên bean.
- Nếu thêm field thay đổi được (counter, `List` làm cache, `private Product current;`) thì có race
  condition thật. Khi đó: bỏ state đi, hoặc dùng `AtomicLong` / `ConcurrentHashMap`.
- Repository proxy và `EntityManager` dùng chung được resolve theo thread/transaction, nên an toàn.

**Nếu bị hỏi tiếp:** *"Field `log` (static final Logger) có sao không?"* → Không. Logger của SLF4J dùng
được từ nhiều thread, và field đó không đổi.

**Nếu không chắc:** Đừng bao giờ nói "Spring singleton thì thread-safe". Câu an toàn: "an toàn vì
stateless, không phải vì là singleton".

<a id="q4"></a>
### Q4. `@Component`, `@Service`, `@Repository`, `@Controller` khác gì nhau? Sao `ProductRepository` không gắn `@Repository` mà vẫn chạy?

`[Fresher]` · ★

**Trả lời ngắn (nói trong lớp):**
> Cả bốn đều là `@Component`, nên đều được component scan tìm thấy và tạo bean. `@Service` chủ yếu để
> nói rõ vai trò. `ProductRepository` chỉ là interface extends `JpaRepository`; Spring Data tự tìm và tạo
> implementation lúc chạy, nên không cần `@Repository`.

**Bên dưới:**
- `@Repository` có thêm exception translation: đổi lỗi persistence thành `DataAccessException` của Spring.
- `@RestController` = `@Controller` + `@ResponseBody`: giá trị trả về được ghi thẳng vào body (JSON).
- Implementation mặc định bên dưới proxy của repository là `SimpleJpaRepository`.

**Nếu bị hỏi tiếp:** *"Thêm `@Repository` vào interface có sai không?"* → Không sai, chỉ thừa.

**Nếu không chắc:** "Spring Data tạo proxy lúc runtime" — đừng mô tả chi tiết nội bộ nếu chưa xem code.

<a id="q5"></a>
### Q5. Constructor của `ProductService` không có `@Autowired`, sao Spring inject được? Em có cần thêm vào không?

`[Fresher]` · ★

**Trả lời ngắn (nói trong lớp):**
> Không cần. Class chỉ có **một constructor** thì Spring tự dùng constructor đó để inject (từ Spring 4.3).
> Thêm vào không sai, nhưng thừa.

**Bên dưới:**
- Chỉ khi có nhiều constructor (và không có constructor mặc định) mới phải đánh dấu `@Autowired` lên
  một cái để Spring biết dùng cái nào.
- Quy tắc này dành cho bean. Test class thì khác — xem [Q7](#q7).

**Nếu bị hỏi tiếp:** *"Constructor có quá nhiều tham số thì sao?"* → Đó là tín hiệu class làm quá nhiều
việc. Constructor injection giúp lộ ra vấn đề đó sớm.

**Nếu không chắc:** "Một constructor thì không cần `@Autowired`" — câu này chắc chắn đúng.

<a id="q6"></a>
### Q6. Code cũ công ty em toàn `@Autowired private` field. Có sao không? Dùng Lombok `@RequiredArgsConstructor` được không?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Field injection vẫn chạy bình thường, chỉ là không nên làm mặc định: dependency bị ẩn, field không
> `final` được, unit test phải dùng reflection hoặc dựng Spring. Lombok `@RequiredArgsConstructor` sinh
> constructor cho các field `final`, kết quả tương đương constructor injection.

**Bên dưới:**
- Constructor injection: dependency rõ ràng, immutable, test chỉ cần `new ProductService(mockRepo)`.
- Setter injection hợp với dependency tùy chọn.
- Demo không dùng Lombok (không có trong `pom.xml`) để mọi người thấy rõ cơ chế.

**Nếu bị hỏi tiếp:** *"Có nên refactor hết code cũ không?"* → Không cần làm một lần; sửa dần khi đụng vào
class đó.

**Nếu không chắc:** Đừng chê code legacy của người hỏi. "Nếu có dịp refactor thì nên chuyển dần."

<a id="q7"></a>
### Q7. Slide nói tránh field injection, vậy sao integration test của anh lại `@Autowired private MockMvc mockMvc`?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Câu hỏi hay. Test class do JUnit tạo, không phải bean bình thường, và mình không bao giờ tự `new` test
> class — nên lý do "dễ test" không áp dụng. Field injection trong test là cách viết chuẩn.

**Bên dưới:**
- Muốn constructor injection trong test thì phải gắn `@Autowired` lên constructor, hoặc set
  `spring.test.constructor.autowire.mode=all`. Mặc định JUnit không tự autowire constructor của test.
- Quy tắc "một constructor thì không cần `@Autowired`" chỉ áp dụng cho bean.

**Nếu bị hỏi tiếp:** *"Vậy slide 6 sai à?"* → Không, slide 6 nói về production bean.

**Nếu không chắc:** "Quy tắc ở slide 6 là cho production bean, test class là trường hợp khác."

<a id="q8"></a>
### Q8. Em có interface `PaymentService`, hai class `VnPayService` và `MomoService` đều `@Service`. Inject `PaymentService` thì Spring chọn cái nào?

`[.NET]` · ★★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Spring không tự chọn. App không khởi động được, lỗi đại ý: *"Parameter 0 of constructor in … required a
> single bean, but 2 were found"*. Cách sửa: `@Primary` cho implementation mặc định, `@Qualifier` ở chỗ
> inject, hoặc inject `List<PaymentService>` nếu cần tất cả.

**Bên dưới:**
- Exception bên dưới là `NoUniqueBeanDefinitionException`; dòng thông báo trên là của failure analyzer
  của Spring Boot, kèm gợi ý dùng `@Primary` / `@Qualifier`.
- Có một fallback: nếu tên tham số constructor trùng tên bean (ví dụ `momoService`) thì Spring chọn bean
  đó. Chạy được nhưng rất dễ vỡ khi ai đó đổi tên biến — đừng dựa vào.
- `Map<String, PaymentService>` (key = tên bean) hợp khi cần chọn implementation lúc runtime.

**Nếu bị hỏi tiếp:** *"Bên .NET đăng ký hai lần thì lấy cái cuối, Spring có vậy không?"* → Không. Spring
báo lỗi ngay lúc startup thay vì chọn ngầm.

**Nếu không chắc:** Nếu không nhớ phần khớp tên tham số, chỉ nói "dùng `@Primary` hoặc `@Qualifier`" và
đừng hứa hành vi fallback.

<a id="q9"></a>
### Q9. Nếu `ProductService` cần `OrderService` và `OrderService` lại cần `ProductService` thì sao?

`[Node]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Với constructor injection, app fail ngay lúc startup: *"The dependencies of some of the beans in the
> application context form a cycle"*. Spring Boot cấm circular dependency mặc định từ bản 2.6. Đó là tín
> hiệu thiết kế cần tách lại.

**Bên dưới:**
- Cách đúng: tách phần logic dùng chung ra bean thứ ba, hoặc dùng event.
- `spring.main.allow-circular-references=true` chỉ là chữa cháy, và với constructor injection thì vòng
  lặp vẫn không gỡ được.

**Nếu bị hỏi tiếp:** *"Dùng `@Lazy` được không?"* → Là workaround, không phải giải pháp chính.

**Nếu không chắc:** "Nó lỗi ngay lúc khởi động, và cách sửa đúng là thiết kế lại."

<a id="q10"></a>
### Q10. Class của thư viện (ví dụ `java.time.Clock`) mình đâu gắn `@Component` được, muốn inject thì làm sao?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Tạo class `@Configuration`, viết method `@Bean` trả về object, ví dụ
> `@Bean Clock clock() { return Clock.systemUTC(); }`, rồi inject `Clock` vào service như bình thường.

**Bên dưới:**
- `@Component` cho class mình viết; `@Bean` cho object mình tự khởi tạo hoặc class của thư viện.
- Auto-configuration của Boot thật ra cũng là rất nhiều method `@Bean` có điều kiện.
- Inject `Clock` còn giúp test phần thời gian dễ hơn (truyền `Clock.fixed(...)` trong test).

**Nếu bị hỏi tiếp:** *"Muốn cấu hình `ObjectMapper` thì sao?"* → Nhớ Boot 4 dùng Jackson 3 (package
`tools.jackson`); đừng copy code mẫu Jackson 2 cũ.

**Nếu không chắc:** Chỉ cần nói "`@Configuration` + `@Bean`", không cần code live.

<a id="q11"></a>
### Q11. Auto-configuration nghe như magic. Làm sao nó biết phải cấu hình cái gì?

`[Senior]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Không phải magic. Mỗi class auto-configuration có điều kiện, kiểu "có class này trên classpath không",
> "mình đã tự khai báo bean này chưa". Điều kiện thỏa thì nó tạo bean; mình tự khai báo thì Boot lùi lại.

**Bên dưới:**
- Danh sách class nằm trong `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`
  của mỗi module.
- Điều kiện: `@ConditionalOnClass`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty`…
- Chạy app với `debug: true` (hoặc `--debug`) → Boot in **CONDITIONS EVALUATION REPORT**: cái nào match,
  cái nào không, vì sao.

**Nếu bị hỏi tiếp:** *"Thêm `@EnableWebMvc` thì sao?"* → Nó tắt auto-configuration MVC của Boot, vì
`WebMvcAutoConfiguration` chỉ chạy khi mình chưa tự cấu hình MVC. Ở Boot, thường không nên thêm.

**Nếu không chắc:** Nếu còn thời gian, bật `debug: true` cho cả lớp xem report. Nếu không, chỉ giải thích.

<a id="q12"></a>
### Q12. Sao trong `pom.xml` các dependency không ghi version? `spring-boot-starter-web` với `spring-boot-starter-webmvc` khác gì?

`[Node]` · ★

**Trả lời ngắn (nói trong lớp):**
> `spring-boot-starter-parent` quản lý version tập trung qua BOM, các version đã được test tương thích với
> nhau — gần giống lockfile nhưng do Spring duy trì. Ở Boot 4, `spring-boot-starter-web` vẫn còn nhưng đã
> **deprecated**, khuyến nghị dùng `spring-boot-starter-webmvc` như demo.

**Bên dưới:**
- Starter chỉ là dependency "gom nhóm": `spring-boot-starter-data-jpa` kéo Hibernate, Spring Data JPA,
  HikariCP…
- Boot 4 chia nhỏ module hơn: test MVC dùng `spring-boot-starter-webmvc-test`, Flyway cần
  `spring-boot-starter-flyway`, H2 console là module riêng `spring-boot-h2console`.
- Thư viện không nằm trong BOM (ví dụ JaCoCo plugin) thì phải tự ghi version.

**Nếu bị hỏi tiếp:** *"Migrate project cũ vướng quá thì sao?"* → Boot 4 có `spring-boot-starter-classic`
để giữ classpath kiểu cũ trong lúc chuyển dần.

**Nếu không chắc:** Đừng hứa `starter-web` bị xóa ở version nào. Chỉ nói "deprecated".

<a id="q13"></a>
### Q13. Em để entity và repository ở package `com.example.common` cho dùng chung, có được không?

`[Fresher]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Mặc định Boot chỉ scan package của class `@SpringBootApplication` — `com.example.product` — và các
> package con. Để ngoài thì không được scan, app lỗi lúc khởi động. Cách đơn giản nhất: dời vào dưới
> `com.example.product`.

**Bên dưới:**
- Bean không được tạo → nơi inject nó lỗi, đại ý *"required a bean of type … that could not be found"*.
- Áp dụng cho cả component scan, entity scan và repository scan.
- `scanBasePackages` chỉ tác động tới component scan; entity và repository ở ngoài cần thêm `@EntityScan`
  và `@EnableJpaRepositories`.

**Nếu bị hỏi tiếp:** *"Sao main class phải ở package gốc?"* → Chính vì scan bắt đầu từ package của nó
trở xuống.

**Nếu không chắc:** "Nó sẽ lỗi lúc startup." Đừng đọc thuộc lòng message lỗi của entity.

<a id="q14"></a>
### Q14. Không cần cài Tomcat hả anh? Máy em port 8080 đang có app khác thì đổi thế nào?

`[QA]` · ★

**Trả lời ngắn (nói trong lớp):**
> Đúng, Tomcat 11 chạy nhúng ngay trong app, build ra JAR là chạy bằng `java -jar`. Port trong demo là
> `${SERVER_PORT:8080}`: set biến môi trường `SERVER_PORT` (PowerShell: `$env:SERVER_PORT="9090"`) rồi
> chạy lại, hoặc sửa trong Run Configuration của IntelliJ.

**Bên dưới:**
- Port bị chiếm thì app dừng với thông báo đại ý *"Web server failed to start. Port 8080 was already in
  use."*
- JAR của demo: `target/product-api-0.0.1-SNAPSHOT.jar`.
- Đổi sang Jetty: exclude Tomcat, thêm starter Jetty.

**Nếu bị hỏi tiếp:** *"`--server.port=9090` được không?"* → Được, và command-line argument còn ưu tiên cao
hơn env var — xem [Q60](#q60).

**Nếu không chắc:** Máy trainer không có Maven trên PATH — đừng demo `mvn spring-boot:run` trong terminal.

---

## 2. REST, DTO và JSON

<a id="q15"></a>
### Q15. Request từ Postman vào tới controller đi qua những gì? Ai biến JSON thành `CreateProductRequest`?

`[Node]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Tomcat nhận request, chuyển cho `DispatcherServlet`. Nó tìm method controller khớp URL và HTTP method,
> dùng Jackson (qua `HttpMessageConverter`) đọc JSON thành record, chạy `@Valid`, rồi gọi controller.
> Kết quả trả về lại đi qua Jackson để thành JSON.

**Bên dưới:**
- Validation với `@Valid @RequestBody` chạy **trước** khi method controller được gọi — controller và
  service không bao giờ nhận object lỗi.
- Có exception thì `DispatcherServlet` hỏi lần lượt: `ExceptionHandlerExceptionResolver` (chỗ
  `@RestControllerAdvice` của mình) → `ResponseStatusExceptionResolver` → `DefaultHandlerExceptionResolver`.
- Boot 4 dùng Jackson 3.

**Nếu bị hỏi tiếp:** *"Filter với interceptor nằm đâu?"* → Filter chạy trước `DispatcherServlet`,
interceptor chạy quanh handler. Không đi sâu hôm nay.

**Nếu không chắc:** Dùng sơ đồ slide 4 để trả lời ở mức tầng, không cần kể tên từng class.

<a id="q16"></a>
### Q16. Sao DTO dùng `record` mà entity `Product` lại là class có getter/setter?

`[Dev]` · ★

**Trả lời ngắn (nói trong lớp):**
> Record ngắn gọn và immutable, rất hợp làm DTO. Entity JPA thì cần constructor không tham số, class
> không `final`, field thay đổi được — để Hibernate tạo object, theo dõi thay đổi và tạo proxy. Record là
> `final` và field `final`, nên không làm entity được.

**Bên dưới:**
- Record (Java 16+) tự có constructor, accessor, `equals`/`hashCode`/`toString`.
- Accessor của record là `name()`, không phải `getName()`.
- `Product` không khai báo constructor nào nên Java tự sinh constructor rỗng `public`.
- Record vẫn dùng được cho projection (kết quả query) và `@Embeddable`.

**Nếu bị hỏi tiếp:** *"Vậy dùng Lombok cho entity?"* → Được, nhưng tránh `@Data` trên entity:
`equals`/`hashCode`/`toString` trên mọi field dễ gây vấn đề với lazy loading và quan hệ hai chiều.

**Nếu không chắc:** Đừng khẳng định record làm `@Entity` được.

<a id="q17"></a>
### Q17. Return `Product` luôn cho nhanh, sao phải viết thêm `ProductResponse`?

`[Senior]` · ★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Entity đại diện cho persistence, DTO đại diện cho API contract (slide 9, slide 14). Trả thẳng entity
> thì mỗi lần đổi DB là đổi API, dễ lộ field nhạy cảm, và client có thể gửi lên những field không được
> phép như `id`, `active`.

**Bên dưới:**
- DTO giúp kiểm soát chính xác field nào đi ra và field nào được nhận vào.
- Demo tắt `open-in-view`; nếu sau này entity có quan hệ LAZY mà trả thẳng ra, Jackson serialize ngoài
  transaction sẽ gặp `LazyInitializationException`.
- Record serialize theo đúng thứ tự khai báo component, nên JSON của `ProductResponse` ổn định.
- `CreateProductRequest` chỉ dành cho tạo mới; update nên có DTO riêng — xem [Q19](#q19).

**Nếu bị hỏi tiếp:** *"Mapping thủ công mệt, dùng MapStruct được không?"* → Được, dự án nhiều DTO hay dùng
MapStruct (sinh code lúc compile). Hôm nay viết tay (`toResponse`) cho rõ. Cần kiểm tra version annotation
processor tương thích.

**Nếu không chắc:** Hai lý do chắc chắn: **contract ổn định** và **không lộ dữ liệu**.

<a id="q18"></a>
### Q18. Nếu em POST kèm `"id": 999` và `"active": false` thì có ghi đè được không?

`[QA]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Không. `CreateProductRequest` không có field `id` hay `active`, và Jackson trong Boot mặc định bỏ qua
> field lạ. `id` vẫn do DB sinh, `active` vẫn là `true`. Đây chính là lợi ích của DTO.

**Bên dưới:**
- `FAIL_ON_UNKNOWN_PROPERTIES` là `false` trong Boot, nên field thừa bị bỏ qua âm thầm, không báo lỗi.
- Nếu request nhận thẳng entity thì client gửi được `id`, `active` — lỗi kiểu "mass assignment".

**Nếu bị hỏi tiếp:** *"Muốn gửi field lạ là báo 400 thì sao?"* → Bật lại feature
`FAIL_ON_UNKNOWN_PROPERTIES` qua cấu hình Jackson (`spring.jackson.deserialization.*`).

**Nếu không chắc:** Nếu phải nói tên property chính xác: "mình kiểm tra `spring.jackson.deserialization.*`
trong docs rồi gửi sau".

<a id="q19"></a>
### Q19. Làm API update thì dùng lại `CreateProductRequest` cho nhanh được không?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Không nên. `CreateProductRequest` là contract cho **tạo mới**. Update nên có DTO riêng, ví dụ
> `UpdateProductRequest` — lúc đầu có thể giống hệt, nhưng hai contract sẽ khác nhau theo thời gian.

**Bên dưới:**
- Ví dụ: update có thể cho đổi `active`, có thể không cho đổi `sku`, có thể cần `version` cho optimistic
  locking ([Q53](#q53)).
- Dùng chung một DTO thì sửa rule cho update sẽ vô tình đổi luôn contract của create.
- Đây là bài tập về nhà (Phụ lục B của kịch bản): `PUT /api/products/{id}`, `404` nếu không có, `409`
  nếu SKU trùng với **sản phẩm khác**, dùng `existsBySkuAndIdNot`.

**Nếu bị hỏi tiếp:** *"Update có cần gọi `save()` không?"* → Không bắt buộc. Entity lấy ra trong
`@Transactional` được Hibernate theo dõi (dirty checking); thay đổi được `UPDATE` khi commit.

**Nếu không chắc:** "Mỗi use case một DTO" — câu này an toàn.

<a id="q20"></a>
### Q20. Sao price là `BigDecimal` mà không phải `double`? Em gửi `50` mà GET lại ra `50.00`, gửi `79.999` thì ra `80.00` — bug hả anh?

`[QA]` · ★★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> `double` là số nhị phân, `0.1 + 0.2` ra `0.30000000000000004`, không dùng cho tiền. Còn `50.00` là do
> cột DB: Hibernate mặc định map `BigDecimal` thành `numeric(38,2)`. POST trả lại chính object từ request
> nên vẫn là `50`; GET đọc từ DB nên ra `50.00`.

**Bên dưới:**
- `79.999` bị DB làm tròn âm thầm thành `80.00` — đó mới là vấn đề thật với tiền.
- Cách chặt hơn: validate ở request bằng `@Digits(integer = 8, fraction = 2)` (khớp 10 chữ số tổng, 2 chữ số thập phân) và khai báo rõ
  `@Column(precision = 10, scale = 2)`. Demo không làm, chỉ bàn ở đây.
- So sánh `BigDecimal` dùng `compareTo`, không dùng `equals`: `equals` so cả scale, nên `50` và `50.00`
  không bằng nhau.
- QA nên so sánh giá theo giá trị số, không so chuỗi.

**Nếu bị hỏi tiếp:** *"Sao scale là 2?"* → Default mapping của Hibernate cho `BigDecimal` khi không khai
báo gì. Production nên khai báo precision/scale rõ ràng.

**Nếu không chắc:** Nếu bị hỏi rounding mode của từng DB: "tùy DB; trên H2 thì ra `80.00` như vừa thấy".

<a id="q21"></a>
### Q21. Sao POST trả `201` chứ không phải `200`? Bên .NET có `CreatedAtAction` kèm header `Location`, Spring có không?

`[.NET]` · ★

**Trả lời ngắn (nói trong lớp):**
> `201 Created` nói rõ là đã tạo resource mới; GET thành công thì `200`. Đúng chuẩn thì `201` nên kèm
> `Location: /api/products/{id}`. Demo trả body đã có `id` nên bỏ `Location` cho gọn.

**Bên dưới:**
- Muốn thêm: `ResponseEntity.created(uri).body(...)`, với `uri` build từ
  `ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(id).toUri()`.
- GET trả thẳng `ProductResponse` thì mặc định là `200`; POST cần `ResponseEntity` vì muốn đổi status.

**Nếu bị hỏi tiếp:** *"Dùng `@ResponseStatus(HttpStatus.CREATED)` được không?"* → Được, cho trường hợp
status cố định. `ResponseEntity` linh hoạt hơn khi cần header.

**Nếu không chắc:** "Đây là lựa chọn đơn giản hóa có chủ ý của demo."

<a id="q22"></a>
### Q22. PUT với PATCH khác gì? Idempotent là sao? Em bấm Send POST hai lần thì sao?

`[Node]` · ★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> PUT gửi **toàn bộ** resource để thay thế, và là idempotent: gửi n lần thì trạng thái server như gửi một
> lần. PATCH gửi **một phần** thay đổi, và **không đảm bảo** idempotent. POST không idempotent: gửi hai
> lần cùng SKU thì lần hai nhận `409 DUPLICATE_SKU`.

**Bên dưới:**
- PATCH có idempotent hay không tùy cách thiết kế: "đặt price = 50" thì lặp lại vẫn vậy, nhưng "tăng
  stock thêm 1" thì mỗi lần một khác.
- `GET`, `PUT`, `DELETE` là idempotent; `POST` thì không.
- `409` khi bấm hai lần là nhờ `existsBySku`. Hai request **đồng thời** thì khác — xem [Q52](#q52).

**Nếu bị hỏi tiếp:** *"Muốn client retry POST an toàn?"* → Có pattern `Idempotency-Key` header. Nằm ngoài
phạm vi hôm nay.

**Nếu không chắc:** Nhớ: đừng bao giờ gọi PATCH là "thay thế toàn bộ".

<a id="q23"></a>
### Q23. Field `timestamp` trong response lỗi lệch 7 tiếng so với giờ máy em, bug hả anh?

`[QA]` · ★

**Trả lời ngắn (nói trong lớp):**
> Không phải bug. `ApiError` dùng `Instant` — một thời điểm tuyệt đối theo UTC — và Jackson in ra chuỗi
> ISO-8601 có chữ `Z` ở cuối. `02:30Z` chính là 9:30 sáng giờ Việt Nam. Hiển thị giờ local là việc của
> frontend.

**Bên dưới:**
- `Z` = UTC. Truyền thời gian giữa các hệ thống bằng UTC tránh được lỗi múi giờ.
- Số chữ số phần giây có thể khác nhau giữa các lần.

**Nếu bị hỏi tiếp:** *"Muốn trả giờ Việt Nam luôn?"* → Có thể dùng `OffsetDateTime` với offset `+07:00`,
nhưng API nên thống nhất một quy ước; UTC là lựa chọn phổ biến.

**Nếu không chắc:** Nếu bị hỏi số chữ số thập phân: "tùy độ chính xác của clock", đừng nói con số.

<a id="q24"></a>
### Q24. Có Swagger UI để em test API mà không cần Postman không anh?

`[QA]` · ★

**Trả lời ngắn (nói trong lớp):**
> Hôm nay không đưa vào demo. Thư viện thông dụng là springdoc-openapi; nhánh 3.x dành cho Spring Boot 4,
> artifact `springdoc-openapi-starter-webmvc-ui`. Thêm vào là có UI để thử API và file spec OpenAPI.

**Bên dưới:**
- Đường dẫn thường gặp: UI ở `/swagger-ui.html`, spec JSON ở `/v3/api-docs`.
- springdoc không phải dự án của Spring; cần kiểm tra bản nào tương thích Boot 4.1 trước khi dùng.
- Trong IntelliJ, file `demo-requests.http` cũng dùng để gọi API ngay trong IDE.

**Nếu bị hỏi tiếp:** *"Spec có tự đọc annotation validation không?"* → springdoc có đọc một số constraint
Bean Validation; chi tiết cần xem docs.

**Nếu không chắc:** Đừng hứa một version cụ thể chạy được với 4.1.1.

---

## 3. Validation

<a id="q25"></a>
### Q25. Nếu chưa có `GlobalExceptionHandler` thì body lỗi validation trông thế nào? Bên .NET `[ApiController]` tự trả chi tiết lỗi từng field.

`[.NET]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Spring cũng tự chặn và trả `400`, nhưng body mặc định rất nghèo: chỉ `{timestamp, status, error, path}`,
> không nói field nào sai. Console có một dòng WARN từ `DefaultHandlerExceptionResolver`. Vì vậy mình viết
> handler để trả `ApiError` có `VALIDATION_ERROR` và message từng field.

**Bên dưới:**
- Đây chính là request #2 trong `demo-requests.http` — chạy trước và sau khi thêm handler để so sánh.
- Boot mặc định không đưa message, binding errors hay stack trace vào body lỗi (vì an toàn).
- Message của handler: các cặp `field: message`, đã sort, nối bằng `, `.

**Nếu bị hỏi tiếp:** *"Có format chuẩn nào như ProblemDetails bên .NET không?"* → Có, `ProblemDetail`
(RFC 9457) — xem [Q36](#q36).

**Nếu không chắc:** "Status 400 đúng, chỉ là body mặc định không có chi tiết field" — câu này chắc chắn.

<a id="q26"></a>
### Q26. `@Valid` với `@Validated` khác gì? Em thấy có project gắn `@Validated` trên class controller.

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> `@Valid` là annotation chuẩn Jakarta, dùng để validate object và cascade xuống object lồng nhau.
> `@Validated` là của Spring, hỗ trợ validation groups. Đặt trên `@RequestBody` thì dùng cái nào cũng ra
> `MethodArgumentNotValidException` → `400`.

**Bên dưới:**
- `@Validated` ở **cấp class** controller bật cơ chế AOP method validation. Khi đó constraint đặt trực
  tiếp trên tham số (ví dụ `@PathVariable @Positive Long id`) ném `ConstraintViolationException` và
  mặc định thành `500`.
- Từ Spring 6.1 có method validation dựng sẵn trong MVC, không cần `@Validated` ở class — xem [Q29](#q29).
- Demo chỉ dùng `@Valid` trên `@RequestBody`.

**Nếu bị hỏi tiếp:** *"Validation groups dùng khi nào?"* → Khi một DTO dùng cho nhiều tình huống với rule
khác nhau. Nhưng thường tách DTO riêng ([Q19](#q19)) dễ đọc hơn.

**Nếu không chắc:** "Trên `@RequestBody` thì hai cái như nhau; khác biệt nằm ở validation groups và
validation ở cấp method."

<a id="q27"></a>
### Q27. Nếu em quên `@Valid` trước `@RequestBody` thì chuyện gì xảy ra?

`[Fresher]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Annotation trên record vẫn còn nhưng không ai kích hoạt validation. Request #2 (`name` rỗng, `sku` rỗng,
> price `-1`) sẽ được lưu vào DB và trả `201` — dữ liệu rác đi thẳng vào database.

**Bên dưới:**
- Constraint nằm trên DTO chứ không nằm trên entity, nên Hibernate không có gì để kiểm tra thêm.
- Nếu `price` là `null` thì nó đi tới ràng buộc `NOT NULL` của cột; nhiều khả năng ra lỗi DB và client
  nhận `500` thay vì `400`.
- Integration test gửi body sai và expect `400` sẽ bắt được lỗi quên `@Valid`. Unit test của service
  thì không, vì nó không đi qua controller.

**Nếu bị hỏi tiếp:** *"IntelliJ có cảnh báo không?"* → Đừng khẳng định; kiểm tra bằng test là chắc nhất.

**Nếu không chắc:** Nói chắc: "validation sẽ không chạy". Phần `500` khi `null` thì nói "nhiều khả năng".

<a id="q28"></a>
### Q28. Em lỡ đặt `@NotBlank` lên `price` (`BigDecimal`), compile vẫn qua. Có sao không?

`[Fresher]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Compile qua và app vẫn khởi động, nhưng khi request đầu tiên tới, Hibernate Validator ném
> `UnexpectedTypeException` mã `HV000030` — không có validator của `@NotBlank` cho kiểu `BigDecimal`.
> `@NotBlank` chỉ dành cho chuỗi; với số thì dùng `@NotNull` + `@Positive` như demo.

**Bên dưới:**
- Lỗi xuất hiện lúc validate lần đầu, không phải lúc startup — nên dễ lọt nếu không có test.
- Exception này không có handler nên nhiều khả năng client nhận `500`.
- Bảng nhớ nhanh: chuỗi → `@NotBlank`, `@Size`; số → `@NotNull`, `@Positive`, `@DecimalMin`, `@Digits`.

**Nếu bị hỏi tiếp:** *"`@NotEmpty` với `@NotBlank` khác gì?"* → `@NotEmpty` chỉ cấm rỗng; `@NotBlank`
cấm cả chuỗi toàn khoảng trắng.

**Nếu không chắc:** Nói chắc là lỗi lúc runtime, không phải compile. Status `500` thì nói "nhiều khả năng".

<a id="q29"></a>
### Q29. Em thêm `@Positive` vào `@PathVariable Long id` rồi gọi `/api/products/-1`. Handler `VALIDATION_ERROR` có bắt không?

`[QA]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Không bắt. Constraint đặt trực tiếp trên tham số dùng method validation có sẵn từ Spring 6.1, ném
> `HandlerMethodValidationException`, không phải `MethodArgumentNotValidException`. Handler của mình không
> khai báo exception này nên client nhận `400` với body mặc định, không phải `ApiError`.

**Bên dưới:**
- Muốn cùng format: thêm `@ExceptionHandler(HandlerMethodValidationException.class)`.
- Nếu project gắn `@Validated` ở cấp class thì lại là `ConstraintViolationException` → `500` mặc định
  ([Q26](#q26)).

**Nếu bị hỏi tiếp:** *"Vậy validate id ở đâu là tốt nhất?"* → Thường không cần: id âm thì `findById`
không thấy, trả `404` là đủ rõ.

**Nếu không chắc:** Nói chắc: "status 400 nhưng format khác `ApiError`".

<a id="q30"></a>
### Q30. Em chạy hai lần cùng một body mà message lỗi ra thứ tự khác nhau. Sao vậy?

`[QA]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Bean Validation trả về một `Set` các lỗi, không đảm bảo thứ tự. Vì vậy handler của mình **sort** các cặp
> `field: message` rồi mới nối lại, để test và frontend nhận kết quả ổn định.

**Bên dưới:**
- Không sort thì đừng assert nguyên chuỗi message trong test.
- Sau khi sort, thứ tự theo tên field: `name`, `price`, `sku`.

**Nếu bị hỏi tiếp:** *"Frontend muốn highlight từng ô input?"* → Nên trả thêm mảng `fieldErrors` dạng
`[{field, message}]` thay vì một chuỗi — xem [Q37](#q37).

**Nếu không chắc:** Nếu output live khác slide: "đó đúng là vấn đề mình vừa nói, nên mình sort".

<a id="q31"></a>
### Q31. Khách hàng muốn message lỗi bằng tiếng Việt, ví dụ "Tên không được để trống", làm sao anh?

`[QA]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Hibernate Validator không có sẵn bản tiếng Việt nên mặc định ra tiếng Anh (`must not be blank`). Cách
> nhanh: ghi thẳng `message = "..."` trong annotation. Cách chuẩn: dùng key như
> `message = "{product.name.required}"` và khai báo trong `ValidationMessages.properties` hoặc `messages`.

**Bên dưới:**
- Boot nối `MessageSource` vào validator, nên key có thể đặt trong `messages.properties`.
- Có cơ chế chọn ngôn ngữ theo header `Accept-Language`, cần thử trước khi hứa với khách.
- Khuyến nghị: backend trả `code` ổn định, phần dịch để frontend lo.

**Nếu bị hỏi tiếp:** *"File properties tiếng Việt có bị lỗi font không?"* → Cần chú ý encoding của file;
kiểm tra kỹ khi làm.

**Nếu không chắc:** "Cơ chế có sẵn, nhưng mình chưa chạy thử end-to-end với tiếng Việt — mình gửi ví dụ sau."

---

## 4. Error handling

<a id="q32"></a>
### Q32. Validation fail thì phải là `422` chứ, sao demo trả `400`?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Cả hai đều gặp trong thực tế. `400` là request sai nói chung; `422` là cú pháp đúng nhưng nội dung không
> hợp lệ. Spring mặc định trả `400` cho `MethodArgumentNotValidException`, nên demo theo mặc định để thống
> nhất với các lỗi `400` khác. Quan trọng nhất là cả team thống nhất một contract.

**Bên dưới:**
- Ở Spring 7, hằng số là `HttpStatus.UNPROCESSABLE_CONTENT`; `UNPROCESSABLE_ENTITY` đã deprecated.
- Muốn đổi sang `422`: sửa status trong handler, và sửa luôn test.

**Nếu bị hỏi tiếp:** *"Dự án em dùng 422 thì có sai không?"* → Không sai, miễn là nhất quán.

**Nếu không chắc:** Đừng nói `422` là sai. "Là lựa chọn contract; demo theo default của Spring."

<a id="q33"></a>
### Q33. Em gọi `GET /api/products/abc`, response không có field `code` như `ApiError`. Sao không consistent như slide nói?

`[QA]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> `abc` không đổi sang `Long` được. Handler của mình không có `@ExceptionHandler` cho lỗi này, nên Spring
> tự xử lý: trả `400` với body mặc định `{timestamp, status, error, path}`. Hôm nay mình cố ý chỉ chuẩn hóa
> các case trên slide; production nên handle thêm.

**Bên dưới:** các case "rơi ra ngoài" `ApiError` trong demo:

| Tình huống | Kết quả mặc định |
|---|---|
| `/api/products/abc` (sai kiểu) | `400` |
| JSON hỏng | `400` |
| Thiếu `Content-Type: application/json` (Postman chọn raw Text) | `415` |
| Sai HTTP method | `405`, có header `Allow` |
| Sai URL hoặc dấu `/` thừa ở cuối | `404` — Spring 6+ không còn tự khớp dấu `/` cuối |
| `sort=unknownField` | `500` — xem [Q56](#q56) |

**Nếu bị hỏi tiếp:** *"Handle hết thế nào cho gọn?"* → Cho handler extend `ResponseEntityExceptionHandler`
(nó xử lý sẵn các exception chuẩn của Spring MVC), rồi override để trả format của mình.

**Nếu không chắc:** "Đây là giới hạn có chủ ý của demo, không phải bug của Spring."

<a id="q34"></a>
### Q34. Nếu có `NullPointerException` mình không lường trước thì sao? Sao không thêm `@ExceptionHandler(Exception.class)` cho chắc?

`[Senior]` · ★★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Hiện tại client nhận `500` với body mặc định của Boot, không lộ stack trace; log server có ERROR kèm stack
> trace. Thêm catch-all được, nhưng có bẫy: nếu advice **không** extend `ResponseEntityExceptionHandler`,
> handler `Exception.class` sẽ bắt luôn các lỗi 4xx của Spring (405, 415, JSON hỏng) và biến chúng thành
> `500`.

**Bên dưới:**
- Handler trong advice được hỏi **trước** `DefaultHandlerExceptionResolver` (bộ biến exception chuẩn của
  Spring thành 4xx). Catch-all nuốt hết trước khi Spring kịp trả đúng status.
- Cách làm đúng: extend `ResponseEntityExceptionHandler` để lỗi của Spring vẫn đi đúng status, rồi mới
  thêm handler `Exception.class` làm "lưới cuối".
- Handler cuối: log ở mức ERROR kèm exception, trả message chung chung. Không trả `ex.getMessage()` hay
  stack trace cho client.
- Slide 14 "Catch-all errors" nói về `catch (Exception e)` nuốt lỗi trong code — khác với handler cuối
  viết cẩn thận.

**Nếu bị hỏi tiếp:** *"`GlobalExceptionHandler` không log gì, production debug kiểu gì?"* → Đúng, demo chưa
log. Production: 4xx log WARN/INFO không cần stack trace; 5xx log ERROR kèm stack trace.

**Nếu không chắc:** "Catch-all không sai, nhưng phải viết cẩn thận để không biến 4xx thành 500."

<a id="q35"></a>
### Q35. Sao `ProductNotFoundException` extends `RuntimeException` mà không extends `Exception`?

`[Senior]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Dùng unchecked để không phải khai báo `throws` xuyên qua mọi tầng. Quan trọng hơn: `@Transactional` mặc
> định chỉ rollback khi gặp `RuntimeException` hoặc `Error` — checked exception thì transaction vẫn commit.

**Bên dưới:**
- `@RestControllerAdvice` bắt được cả checked và unchecked.
- Nhiều handler cùng khớp thì Spring chọn handler có kiểu exception gần nhất trong cây kế thừa.
- Chi tiết rollback: [Q49](#q49).

**Nếu bị hỏi tiếp:** *"Checked exception không phải tốt hơn sao?"* → Là tranh luận lâu năm; hệ sinh thái
Spring nghiêng về unchecked.

**Nếu không chắc:** "Lý do thực tế nhất là rollback mặc định của `@Transactional`."

<a id="q36"></a>
### Q36. Spring có `ProblemDetail` theo chuẩn RFC rồi, sao mình phải tự viết record `ApiError`?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> `ProblemDetail` là implementation của RFC 9457 có sẵn (`type`, `title`, `status`, `detail`, `instance`,
> content type `application/problem+json`), có thể thêm field riêng như `code`. `ApiError` tự viết thì đơn
> giản, dễ hiểu cho buổi học. Dự án thật dùng `ProblemDetail` là lựa chọn tốt.

**Bên dưới:**
- `spring.mvc.problemdetails.enabled` mặc định là `false`.
- Đừng bật nó trong demo: handler của Boot khi đó có `@Order(0)` và có thể giành xử lý trước
  `GlobalExceptionHandler` của mình.
- Cách sạch: cho advice của mình extend `ResponseEntityExceptionHandler` (khi đó Boot không tạo handler
  của nó nữa).

**Nếu bị hỏi tiếp:** *"Ví dụ code?"* → `ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, "...")`,
rồi `setProperty("code", "PRODUCT_NOT_FOUND")`.

**Nếu không chắc:** "Bật flag đó phải test lại handler của mình" — đừng khẳng định cái nào thắng.

<a id="q37"></a>
### Q37. Em viết test automation thì nên assert message hay code? Frontend muốn highlight từng ô input bị lỗi thì sao?

`[QA]` · ★

**Trả lời ngắn (nói trong lớp):**
> Assert **HTTP status** và **`code`** (`VALIDATION_ERROR`, `PRODUCT_NOT_FOUND`, `DUPLICATE_SKU`). Code là
> contract ổn định; message có thể đổi câu chữ hoặc được dịch. Frontend cũng nên switch theo `code`.

**Bên dưới:**
- Branch `solution` có test làm đúng như vậy: `jsonPath("$.code").value("VALIDATION_ERROR")` (test gõ live hôm nay chỉ kiểm tra `$.name` của case `201`).
- Highlight từng field: trả thêm mảng `fieldErrors` dạng `[{field, message}]`. Đây là gợi ý cải tiến,
  demo không làm.

**Nếu bị hỏi tiếp:** *"Có nên thêm `traceId` vào `ApiError`?"* → Hữu ích khi tra log production; cần thêm
tracing/MDC, ngoài phạm vi hôm nay.

**Nếu không chắc:** "`fieldErrors` là gợi ý, demo không có."

---

## 5. JPA & Spring Data

<a id="q38"></a>
### Q38. Em bên .NET quen Entity Framework. JPA, Hibernate, Spring Data JPA khác nhau sao? Cái nào tương đương EF?

`[.NET]` · ★

**Trả lời ngắn (nói trong lớp):**
> JPA (Jakarta Persistence) chỉ là **specification** — bộ annotation và interface như `@Entity`, `@Id`,
> `EntityManager`. Hibernate là **implementation**, ORM thật sự sinh SQL — gần EF Core nhất. Spring Data JPA
> nằm trên cùng: mình chỉ khai báo interface repository, Spring tự sinh implementation, bên dưới vẫn gọi
> Hibernate.

**Bên dưới:**
- Vì vậy `Product` import từ `jakarta.persistence`, không phải `org.hibernate`.
- So sánh tương đối: `DbContext` ≈ `EntityManager` / persistence context; change tracking ≈ dirty checking
  của Hibernate; LINQ ≈ derived query / JPQL.
- Demo dùng Hibernate ORM 7.4.

**Nếu bị hỏi tiếp:** *"Khi nào không nên dùng JPA?"* → Query báo cáo phức tạp, bulk update, cần kiểm soát
từng câu SQL → `JdbcClient`/`JdbcTemplate`, MyBatis hoặc jOOQ hợp hơn. Nhiều dự án dùng cả hai, giấu sau
tầng repository.

**Nếu không chắc:** Đừng nói Spring Data JPA "thay thế" Hibernate — nó dùng Hibernate bên dưới.

<a id="q39"></a>
### Q39. `existsBySku` em chỉ khai báo trong interface, không viết code gì, sao chạy được? Lỡ gõ sai tên thì sao?

`[Fresher]` · ★

**Trả lời ngắn (nói trong lớp):**
> Đây là **derived query**. Spring Data đọc tên method: `exists` là loại query, `BySku` là điều kiện theo
> field `sku` của `Product`, rồi tự tạo query. Gõ sai thành `existsBySkuu` thì compile vẫn qua, nhưng app
> không khởi động được vì `Product` không có property `skuu` — lỗi hiện ra sớm.

**Bên dưới:**
- Bật `show-sql` sẽ thấy SQL: `select p1_0.id from products p1_0 where p1_0.sku=? fetch first ? rows only`
  — chỉ lấy id, tối đa 1 dòng, có dòng là `true`.
- Một số tiền tố khác: `findBy…`, `countBy…`, `deleteBy…`; điều kiện ghép bằng `And`, `Or`.

**Nếu bị hỏi tiếp:** *"Tên method dài quá thì sao?"* → Chuyển sang `@Query` — xem [Q40](#q40).

**Nếu không chắc:** Đừng đọc nguyên văn message lỗi khi gõ sai tên. "App sẽ không start được."

<a id="q40"></a>
### Q40. Query phức tạp, tên method dài kiểu `findByNameContainingAndPriceBetweenAndActiveTrue` thì sao? Viết query tay được không?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Được, dùng `@Query` trên method repository. Mặc định là JPQL, viết theo tên entity và field Java:
> `select p from Product p where p.sku = :sku` — không dùng tên bảng `products`. Cần SQL riêng của DB thì
> thêm `nativeQuery = true`.

**Bên dưới:**
- Câu `UPDATE`/`DELETE` cần thêm `@Modifying` và phải chạy trong transaction.
- Kinh nghiệm: 1–2 điều kiện dùng derived query; dài hơn chuyển sang `@Query` cho dễ đọc.
- Filter động (người dùng chọn điều kiện): `Specification`, Querydsl hoặc Criteria API.
- Native query với `Pageable` có thể cần khai báo `countQuery`.

**Nếu bị hỏi tiếp:** *"Viết sai JPQL thì lỗi lúc nào?"* → Thường là lúc khởi động, vì query được kiểm tra
khi tạo repository.

**Nếu không chắc:** Nếu bị hỏi native query có được kiểm tra lúc startup không: "không chắc, coi như DB mới
kiểm tra lúc chạy".

<a id="q41"></a>
### Q41. `ddl-auto: update` tiện quá, deploy production cứ để vậy được không? Nếu xóa dòng đó đi thì bảng có còn tự tạo không?

`[Senior]` · ★★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Không dùng `update` cho production. Nó chỉ thêm chứ không xóa, đổi tên cột thì thành thêm cột mới, không
> migrate dữ liệu, không có lịch sử version. Production để `validate` hoặc `none`, schema quản lý bằng
> Flyway hoặc Liquibase với script nằm trong Git.

**Bên dưới:**
- Xóa dòng `ddl-auto`: với DB nhúng như H2 (và không có Flyway/Liquibase), Boot mặc định dùng
  `create-drop`, nên demo vẫn chạy. Với PostgreSQL/MySQL mặc định là `none` — bảng không tự tạo.
- Boot 4: phải thêm `spring-boot-starter-flyway`; chỉ có `flyway-core` là không đủ.
- `@Column(unique = true)` chỉ tạo constraint khi Hibernate sinh schema; dùng Flyway thì phải tự viết
  `UNIQUE` trong migration.

**Nếu bị hỏi tiếp:** *"Vậy `validate` làm gì?"* → Hibernate so entity với schema lúc khởi động, lệch thì
báo lỗi, nhưng không sửa gì.

**Nếu không chắc:** Điều chắc chắn: `update` không dùng cho prod. Chi tiết module Flyway cho từng DB: "mình
kiểm tra docs".

<a id="q42"></a>
### Q42. `Product` không có quan hệ nên demo không thấy N+1. Thực tế `Order` có `@ManyToOne Customer` thì xử lý N+1 thế nào? Đổi sang EAGER được không?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> N+1 là 1 query lấy N order, rồi mỗi order bắn thêm 1 query lấy customer. Cách sửa: `JOIN FETCH` trong
> `@Query`, `@EntityGraph(attributePaths = "customer")` trên method repository, hoặc bật batch fetch size để
> Hibernate load theo lô bằng `IN (...)`. **EAGER không phải cách sửa** — thường còn tệ hơn.

**Bên dưới:**
- `@ManyToOne` và `@OneToOne` mặc định đã là EAGER; với query JPQL/derived, EAGER vẫn có thể bắn thêm một
  query cho mỗi dòng. Nên để LAZY rồi fetch khi cần.
- Batch size: `spring.jpa.properties.hibernate.default_batch_fetch_size`.
- Cách phát hiện nhanh nhất: bật `show-sql` và đếm số câu `SELECT`.
- Cẩn thận: fetch join một **collection** kèm phân trang thì Hibernate phân trang trong memory.

**Nếu bị hỏi tiếp:** *"DTO projection có giúp không?"* → Có, query chỉ lấy đúng cột cần, không load entity.

**Nếu không chắc:** Nếu bị hỏi chính xác bao nhiêu query: "chạy thử với `show-sql` rồi đếm", đừng đoán.

<a id="q43"></a>
### Q43. Mặc định Boot có WARN về `spring.jpa.open-in-view`. Mình tắt đi thì lazy loading có bị lỗi không?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Open Session In View mặc định bật: giữ session DB mở suốt request, kể cả lúc Jackson serialize. Tiện,
> nhưng dễ bắn query ngầm ngoài service và giữ connection lâu, nên Boot in WARN và demo tắt đi
> (`open-in-view: false`). Hệ quả: truy cập quan hệ LAZY ngoài transaction sẽ ném
> `LazyInitializationException`.

**Bên dưới:**
- Demo an toàn vì entity được map sang `ProductResponse` ngay trong method `@Transactional` của service,
  và `Product` chưa có quan hệ nào.
- Load dữ liệu lazy đúng cách: `JOIN FETCH`, `@EntityGraph`, hoặc DTO projection bên trong transaction
  của service.

**Nếu bị hỏi tiếp:** *"Vậy bật OSIV là sai?"* → Không hẳn; nhiều app vẫn bật. Đây là lựa chọn có đánh đổi.

**Nếu không chắc:** Đọc WARN thì nói "đại ý Boot cảnh báo query có thể chạy lúc render response".

<a id="q44"></a>
### Q44. Chuyển từ H2 sang PostgreSQL thì phải sửa code không anh?

`[Node]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Code Java gần như không đổi. Thêm dependency driver `org.postgresql:postgresql` (Boot quản lý version),
> rồi truyền `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` qua biến môi trường — `application.yml` đã viết
> `${DB_URL:...}` nên không phải sửa file.

**Bên dưới:**
- Hibernate tự nhận dialect từ kết nối JDBC, không cần khai báo.
- Việc cần làm thêm: chuyển `ddl-auto` sang `validate` + Flyway ([Q41](#q41)).
- Test lại trên DB thật: H2 khác PostgreSQL về kiểu dữ liệu, so sánh chuỗi, locking. Đó là lý do có
  Testcontainers ([Q73](#q73)).

**Nếu bị hỏi tiếp:** *"Connection pool thì sao?"* → Boot dùng HikariCP sẵn; cấu hình qua
`spring.datasource.hikari.*`.

**Nếu không chắc:** Nếu bị hỏi khác biệt cụ thể H2 và DB X: "phải test trên DB thật", đừng liệt kê bừa.

<a id="q45"></a>
### Q45. Tắt app bật lại thì dữ liệu mất hết? Em muốn mở H2 console xem bảng được không?

`[Fresher]` · ★

**Trả lời ngắn (nói trong lớp):**
> Đúng, mất hết. `jdbc:h2:mem:demo` là DB **in-memory**, sống cùng JVM; tắt app là mất. Vì vậy mỗi lần chạy
> lại phải chạy `seed.http`. Muốn giữ dữ liệu thì dùng H2 dạng file, hoặc DB thật.

**Bên dưới:**
- H2 dạng file: URL kiểu `jdbc:h2:file:./data/demo` — truyền qua `DB_URL`, không cần sửa code.
- H2 console ở Boot 4 là module riêng (`spring-boot-h2console`), demo không thêm. Thêm vào thì cần bật
  `spring.h2.console.enabled=true`.
- Test chạy trong JVM riêng nên có DB in-memory riêng — xem [Q69](#q69).

**Nếu bị hỏi tiếp:** *"Sao không thấy file SQL tạo bảng?"* → Bảng do Hibernate sinh từ entity
(`ddl-auto`) — xem [Q41](#q41).

**Nếu không chắc:** Chi tiết cấu hình H2 console ở Boot 4: "mình kiểm tra docs rồi gửi".

<a id="q46"></a>
### Q46. `show-sql` chỉ in dấu `?`, làm sao xem giá trị tham số? Bật `show-sql` trên production được không?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> `show-sql` in thẳng ra console, không có giá trị tham số và không đi qua logging framework. Cách tốt hơn
> là dùng logger: `logging.level.org.hibernate.SQL=DEBUG` để in SQL, thêm
> `logging.level.org.hibernate.orm.jdbc.bind=TRACE` để thấy giá trị. Production thì không nên bật.

**Bên dưới:**
- Log SQL trên production: rất nhiều log, ảnh hưởng hiệu năng, và có thể lộ dữ liệu nhạy cảm.
- Vì vậy demo chỉ bật `show-sql` trong profile `dev` (`application-dev.yml`).
- In SQL đẹp hơn: `spring.jpa.properties.hibernate.format_sql=true`.

**Nếu bị hỏi tiếp:** *"Sao GET list có hai câu SELECT?"* → Câu thứ hai là count cho `Page` — xem [Q58](#q58).

**Nếu không chắc:** Nếu logger bind không in ra: "tên category đổi theo version Hibernate, mình kiểm tra
lại" — đừng đoán tên khác.

---

## 6. Transactions

<a id="q47"></a>
### Q47. `@Transactional` nên đặt ở controller, service hay repository?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Đặt ở **service**, vì một use case (kiểm tra SKU rồi lưu) là một đơn vị công việc. Controller chỉ lo
> HTTP. Repository của Spring Data đã có transaction sẵn cho từng method, nhưng mỗi lần gọi là một
> transaction riêng — không gom được nhiều bước.

**Bên dưới:**
- `SimpleJpaRepository` có `@Transactional(readOnly = true)` ở cấp class; `save`/`delete` override bằng
  `@Transactional` thường. Riêng query method tự khai báo trong interface (như `existsBySku`) **không**
  có transaction mặc định — nó chạy trong transaction của service gọi nó.
- `create` có `@Transactional`: `existsBySku` và `save` chạy chung một transaction, gọi chung một
  persistence context.
- Không đặt ở controller: transaction kéo dài cả phần xử lý HTTP và serialize JSON.

**Nếu bị hỏi tiếp:** *"Gắn `@Transactional` ở cấp class service được không?"* → Được, áp dụng cho mọi
method public; method nào cần khác thì gắn riêng để override.

**Nếu không chắc:** "Quy ước phổ biến: transaction ở tầng service."

<a id="q48"></a>
### Q48. `readOnly = true` trên `getById` để làm gì? Có chặn được lệnh ghi không? Sao em gõ `readOnly` thì IntelliJ báo lỗi?

`[Senior]` · ★★

**Trả lời ngắn (nói trong lớp):**
> `readOnly` là một **gợi ý** tối ưu: Hibernate để session ở FlushMode `MANUAL` nên không tự flush và bỏ
> công dirty checking, driver JDBC nhận hint read-only. Nó **không** phải cơ chế bảo vệ, không đảm bảo
> chặn ghi trên mọi DB. Còn lỗi khi gõ `readOnly` là do import nhầm `jakarta.transaction.Transactional`.

**Bên dưới:**
- Annotation `jakarta.transaction.Transactional` không có thuộc tính `readOnly`. Phải import
  `org.springframework.transaction.annotation.Transactional`.
- Muốn chặn ghi thật: dùng user DB chỉ có quyền đọc, hoặc route sang DB replica.

**Nếu bị hỏi tiếp:** *"Trong method readOnly mà em sửa entity thì sao?"* → Không có flush tự động nên thay
đổi thường không được ghi; nhưng đừng dựa vào đó làm cơ chế bảo vệ.

**Nếu không chắc:** Câu an toàn: "readOnly là hint để tối ưu, không phải cơ chế bảo mật."

<a id="q49"></a>
### Q49. Service ném checked exception (ví dụ `IOException`) thì transaction có rollback không?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> **Không.** Mặc định Spring chỉ rollback khi gặp `RuntimeException` hoặc `Error`. Checked exception thì
> transaction vẫn **commit**. Muốn rollback: `@Transactional(rollbackFor = Exception.class)`, hoặc dùng
> unchecked exception như `ProductNotFoundException`.

**Bên dưới:**
- Quy tắc này theo truyền thống EJB: checked exception được coi là "tình huống nghiệp vụ", không phải lỗi.
- Bắt exception bên trong method rồi không ném lại → Spring không thấy lỗi → vẫn commit.

**Nếu bị hỏi tiếp:** *"Bên .NET `TransactionScope` không `Complete()` là rollback."* → Spring ngược lại:
method kết thúc bình thường là commit, chỉ exception mới làm rollback.

**Nếu không chắc:** Quy tắc "RuntimeException rollback, checked thì không" là chắc chắn.

<a id="q50"></a>
### Q50. Em gắn `@Transactional` lên method `private`, hoặc gọi `this.save()` từ method khác cùng class, sao không có transaction?

`[Dev]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> `@Transactional` chạy qua **proxy**. Controller gọi service thực ra là gọi proxy, proxy mở transaction
> rồi mới gọi object thật. Method `private` thì proxy không chặn được, còn `this.method()` gọi thẳng vào
> object thật, đi vòng qua proxy — nên annotation không có tác dụng.

**Bên dưới:**
- Cách sửa: gắn `@Transactional` lên method public được gọi **từ bên ngoài**; hoặc tách method đó sang
  bean khác.
- Demo: `toResponse` là private nhưng được gọi bên trong `create`, lúc đó đã có transaction của `create`,
  nên không sao.
- Cùng cơ chế proxy nên `@Async`, `@Cacheable` cũng có bẫy self-invocation.

**Nếu bị hỏi tiếp:** *"Có cách nào dùng được self-invocation không?"* → AspectJ weaving, hoặc tự inject
chính proxy của mình — ít khi đáng làm.

**Nếu không chắc:** "Proxy không thấy lời gọi nội bộ" — đủ để trả lời.

<a id="q51"></a>
### Q51. Em muốn ghi audit log kể cả khi nghiệp vụ lỗi rollback. Làm sao? Propagation `REQUIRES_NEW` là gì?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Mặc định là `REQUIRED`: method bên trong tham gia chung transaction bên ngoài. `REQUIRES_NEW` treo
> transaction ngoài và mở một transaction riêng, commit/rollback độc lập — hợp cho audit log. Method đó
> phải nằm ở **bean khác** thì proxy mới hoạt động.

**Bên dưới:**
- Bẫy của `REQUIRED`: method bên trong ném `RuntimeException`, transaction chung bị đánh dấu
  rollback-only. Nếu bên ngoài `catch` rồi đi tiếp, lúc commit sẽ nhận `UnexpectedRollbackException`.
- `REQUIRES_NEW` dùng thêm một connection DB trong lúc transaction ngoài đang treo — dùng nhiều dễ cạn
  connection pool.

**Nếu bị hỏi tiếp:** *"Còn các propagation khác?"* → `SUPPORTS`, `MANDATORY`, `NESTED`… ít dùng, xem docs
khi cần.

**Nếu không chắc:** "Mình chỉ nhớ chắc `REQUIRED` và `REQUIRES_NEW`; các loại khác mình gửi link docs."

<a id="q52"></a>
### Q52. Service gọi `existsBySku` rồi mới `save`. Nếu hai request cùng SKU tới cùng lúc thì sao?

`[Senior]` · ★★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Có race condition. Kiểu "check-then-act" này không an toàn: cả hai request có thể cùng thấy "chưa có"
> rồi cùng insert. `@Transactional` không ngăn được chuyện đó. Chốt chặn thật là **unique constraint ở
> DB** (`unique = true` trên cột `sku`): request thứ hai bị DB từ chối.

**Bên dưới:**
- Request thứ hai khi đó nhận `DataIntegrityViolationException` (H2 lỗi `23505`). Handler hiện tại chưa bắt
  exception này → client nhận `500`.
- Cách sửa: thêm `@ExceptionHandler(DataIntegrityViolationException.class)` trả `409`. Giữ `existsBySku` để
  có message thân thiện cho trường hợp thường gặp.
- Cẩn thận: exception này cũng xảy ra với vi phạm ràng buộc khác (ví dụ `NOT NULL`). Muốn chính xác thì
  kiểm tra tên constraint.
- Đây chính là lý do trước step-09, gửi trùng SKU ra `500`: lỗi đi thẳng từ DB lên.

**Nếu bị hỏi tiếp:** *"Dùng `synchronized` được không?"* → Không: chỉ có tác dụng trong một JVM, chạy 2
instance là hết tác dụng. Lock ở DB mới đúng.

**Nếu không chắc:** Hai ý chắc chắn: "check-then-act bị race" và "unique constraint ở DB là chốt chặn".

<a id="q53"></a>
### Q53. Hai người cùng sửa một sản phẩm, người sau ghi đè mất thay đổi của người trước (lost update). Chống thế nào?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Dùng **optimistic locking**: thêm field `@Version Long version` vào entity. Hibernate thêm
> `where version = ?` vào câu `UPDATE`; nếu người khác đã sửa trước, câu update không khớp dòng nào và
> Spring ném `ObjectOptimisticLockingFailureException`. Map exception đó sang `409`.

**Bên dưới:**
- Client phải gửi kèm `version` đã đọc được lên — lại thêm lý do update cần DTO riêng ([Q19](#q19)).
- Pessimistic locking (`@Lock(PESSIMISTIC_WRITE)` → `SELECT ... FOR UPDATE`) giữ khóa suốt transaction;
  dùng khi xung đột nhiều.
- `@Version` chống **lost update**, không chống insert trùng SKU — cái đó vẫn là unique constraint
  ([Q52](#q52)).
- Phụ lục B của kịch bản nhắc `@Version` là phần nâng cao của bài tập PUT.

**Nếu bị hỏi tiếp:** *"ETag / `If-Match` liên quan gì?"* → Là cách đưa `version` lên tầng HTTP. Nằm ngoài
phạm vi hôm nay.

**Nếu không chắc:** "Optimistic locking bằng `@Version`" là câu trả lời chuẩn; tên exception có thể nói
"một exception optimistic locking của Spring".

---

## 7. Pagination & sorting

<a id="q54"></a>
### Q54. Gọi GET list thì console có một dòng WARN về `PageImpl`. Có sao không? JSON trả về nên có dạng gì?

`[Dev]` · ★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Spring Data cảnh báo (một lần) rằng serialize thẳng `PageImpl` ra JSON là cấu trúc **không ổn định** —
> version sau có thể đổi. Không làm hỏng response. Muốn contract ổn định: đặt
> `spring.data.web.pageable.serialization-mode=via-dto`, hoặc tự viết DTO kiểu `PageResponse`.

**Bên dưới:** với `via-dto`, JSON có dạng:

```json
{
  "content": [ { "id": 1, "name": "...", "sku": "...", "price": 79.99, "active": true } ],
  "page": { "size": 5, "number": 0, "totalElements": 9, "totalPages": 2 }
}
```

- Chế độ mặc định (`DIRECT`) in ra nhiều field nội bộ như `pageable`, `sort`… và chính chúng là phần
  không được đảm bảo.
- Demo giữ nguyên cho gọn. Khi WARN xuất hiện, trainer chỉ cần nói "đại ý là format này không ổn định".

**Nếu bị hỏi tiếp:** *"Frontend đang đọc `totalElements` ở cấp ngoài cùng thì sao?"* → Đổi sang `via-dto`
là đổi contract: field đó chuyển vào trong `page`. Phải báo frontend trước.

**Nếu không chắc:** Đừng đọc nguyên văn dòng WARN. "Đại ý Spring Data khuyên không serialize `PageImpl`
trực tiếp."

<a id="q55"></a>
### Q55. Em là QA, em gửi `size=1000000` hoặc `page=-1` thì server có sập không?

`[QA]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Không sập. `size` bị giới hạn **âm thầm** ở tối đa 2000; `page` âm hoặc giá trị không hợp lệ bị đưa về
> mặc định. Page đánh số từ **0**, không phải 1.

**Bên dưới:**
- Giới hạn chỉnh bằng `spring.data.web.pageable.max-page-size`.
- "Âm thầm" nghĩa là client xin 1.000.000 dòng nhưng chỉ nhận 2000 mà không có thông báo — nên ghi rõ
  trong tài liệu API.
- Muốn page bắt đầu từ 1: `spring.data.web.pageable.one-indexed-parameters=true`.
- Bẫy import: IntelliJ có thể gợi ý `java.awt.print.Pageable` — phải là
  `org.springframework.data.domain.Pageable`. Tương tự, `Page` phải là của `org.springframework.data.domain`,
  không phải `org.hibernate.query.Page`.

**Nếu bị hỏi tiếp:** *"Có nên trả 400 khi size quá lớn không?"* → Là lựa chọn thiết kế; cần tự validate vì
mặc định Spring chỉ cắt bớt.

**Nếu không chắc:** Nói chắc về giới hạn 2000 và page bắt đầu từ 0; đừng đoán giá trị cụ thể sau khi
clamp.

<a id="q56"></a>
### Q56. Client tự truyền `sort` từ URL vào `ORDER BY` thì có bị SQL injection không? Sort theo field không tồn tại thì sao?

`[Senior]` · ★★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Với repository JPA, Spring Data đối chiếu tên field sort với entity, không nối chuỗi thô vào SQL. Field
> không tồn tại thì hiện tại ra **500**: `PropertyReferenceException`, message đại ý *"No property
> 'unknownField' found for type 'Product'"*. Đó là lỗi của client nên đáng ra phải là 400 — đây là request
> #9 trong demo.

**Bên dưới:**
- Cách sửa: bắt `PropertyReferenceException` trong handler và trả `400`; tốt hơn nữa là **whitelist** các
  field được phép sort (`name`, `price`, …).
- Whitelist còn tránh được việc client sort theo cột không có index, làm query chậm.
- Trong MockMvc test, lỗi này hiện ra dạng `ServletException` chứ không phải response `500` — xem
  [Q71](#q71).

**Nếu bị hỏi tiếp:** *"Native query với `Sort` có an toàn như vậy không?"* → Mình không khẳng định cho
native query; nguyên tắc là whitelist.

**Nếu không chắc:** Nói chắc "hiện tại ra 500, nên sửa thành 400". Về injection thì phạm vi an toàn nói
được: "với derived query/JPQL".

<a id="q57"></a>
### Q57. Em gọi `sort=price,desc` thì mặc định sort theo `name` còn không? Nhiều sản phẩm cùng tên thì sang trang sau có bị lặp không?

`[QA]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Client truyền `sort` thì nó **thay thế** sort mặc định của `@PageableDefault`, không cộng thêm. Còn nếu
> nhiều dòng cùng giá trị sort thì thứ tự giữa chúng không được đảm bảo, nên một dòng có thể lặp hoặc mất
> khi chuyển trang. Cách sửa: luôn thêm cột duy nhất làm tie-breaker, ví dụ `sort=name,asc&sort=id,asc`.

**Bên dưới:**
- Tie-breaker mặc định có thể khai báo ngay: `@PageableDefault(size = 10, sort = {"name", "id"})`.
- Request #7 trong demo (`sort=price,desc`) là ví dụ sort mặc định bị thay thế.
- Nếu gọi bằng PowerShell thì nhớ đặt URL trong dấu nháy, vì `&` và `,desc` bị PowerShell hiểu sai.

**Nếu bị hỏi tiếp:** *"Sort nhiều chiều khác nhau?"* → `sort=price,desc&sort=name,asc` — mỗi tham số
`sort` là một cột.

**Nếu không chắc:** "Sort của client thay thế sort mặc định" là chắc chắn.

<a id="q58"></a>
### Q58. Sao GET list bắn hai câu SELECT? Bảng lớn thì câu count có chậm không?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Câu thứ nhất lấy dữ liệu của trang (`offset ? rows fetch first ? rows only` trên H2), câu thứ hai là
> `select count(...)` để tính `totalElements` và `totalPages`. Nếu trang đầu không đầy thì Spring Data bỏ
> qua câu count. Bảng lớn mà không cần tổng số thì dùng `Slice` thay vì `Page`.

**Bên dưới:**
- `Slice` không đếm; nó lấy dư một dòng để biết còn trang sau (`hasNext`). Hợp cho kiểu "cuộn vô tận".
- Muốn dùng `Slice` thì khai báo method repository trả về `Slice<Product>` với tham số `Pageable`.
- Với `@Query` phức tạp, có thể cần tự viết `countQuery` cho nhanh hơn.

**Nếu bị hỏi tiếp:** *"`totalPages` có chính xác không nếu có người thêm dữ liệu giữa chừng?"* → Hai câu
query chạy riêng, nên con số chỉ là ảnh chụp tại thời điểm đó.

**Nếu không chắc:** Đừng hứa hiệu năng cụ thể. "Phải đo trên dữ liệu thật."

<a id="q59"></a>
### Q59. Slide 11 nói 1 triệu dòng. `page=50000` thì có chậm không?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Có thể chậm. Offset lớn nghĩa là DB vẫn phải quét và bỏ qua rất nhiều dòng trước khi lấy trang cần.
> Với dữ liệu lớn, dùng **keyset pagination**: "lấy 10 dòng sau giá trị cuối cùng trang trước", dựa trên
> index, nên tốc độ gần như không phụ thuộc số trang.

**Bên dưới:**
- Spring Data (từ 3.1) hỗ trợ scrolling với `Window<T>` và `ScrollPosition` (có keyset).
- Đánh đổi: không nhảy thẳng tới trang bất kỳ được, chỉ đi tới/lui.
- Keyset cũng cần sort ổn định với cột duy nhất ([Q57](#q57)) và có index trên các cột sort.

**Nếu bị hỏi tiếp:** *"Code mẫu?"* → Để parking lot; gửi link docs "Scrolling" của Spring Data.

**Nếu không chắc:** "Offset lớn chậm, keyset là hướng giải quyết" — không cần code.

---

## 8. Configuration & Profiles

<a id="q60"></a>
### Q60. Slide 12 ghi env var ưu tiên cao nhất. Vậy `--server.port=9090` hay `-D` thì sao? Cùng một key ở nhiều nơi thì cái nào thắng?

`[Senior]` · ★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Slide đơn giản hóa: trong ba tầng trên slide, env var cao nhất. Thứ tự đầy đủ hơn, từ cao xuống thấp:
> **command-line args** (`--x=y`) > **Java system properties** (`-Dx=y`) > **OS env var** >
> `application-{profile}.yml` > `application.yml`.

**Bên dưới:**
- Env var map sang key nhờ relaxed binding: `SERVER_PORT` ↔ `server.port`, `SPRING_PROFILES_ACTIVE` ↔
  `spring.profiles.active`.
- Ý nghĩa thực tế: file yml chứa giá trị mặc định; môi trường triển khai ghi đè bằng env var hoặc argument
  mà không phải build lại.
- Danh sách đầy đủ trong docs dài hơn (test properties, config ngoài JAR…).

**Nếu bị hỏi tiếp:** *"Vậy slide sai?"* → Không sai, chỉ lược bớt các tầng không dùng trong demo.

**Nếu không chắc:** "Mình nhớ chắc năm tầng này; danh sách đầy đủ mình gửi link docs."

<a id="q61"></a>
### Q61. `${DB_URL:jdbc:h2:mem:demo}` có nhiều dấu `:` vậy, Spring tách thế nào?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Cú pháp `${TÊN:mặc_định}` tách ở **dấu `:` đầu tiên**. Tên biến là `DB_URL`, mọi thứ sau đó —
> `jdbc:h2:mem:demo` — là giá trị mặc định. Có biến môi trường `DB_URL` thì dùng nó, không có thì dùng mặc
> định.

**Bên dưới:**
- `${DB_PASSWORD:}` = mặc định là chuỗi rỗng. Không có dấu `:` và không có biến → lỗi lúc startup vì không
  resolve được placeholder.
- Placeholder cũng tham chiếu được property khác, không chỉ env var.

**Nếu bị hỏi tiếp:** *"Giá trị mặc định có chứa `}` được không?"* → Trường hợp hiếm, nên tránh.

**Nếu không chắc:** Chỉ cần nói "tách ở dấu hai chấm đầu tiên".

<a id="q62"></a>
### Q62. Em muốn đọc config riêng như `app.max-upload-size`. Dùng `@Value` hay `@ConfigurationProperties`?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> `@Value("${app.max-upload-size}")` hợp cho một hai giá trị lẻ. Nhóm config có nhiều field thì dùng
> `@ConfigurationProperties(prefix = "app")` — type-safe, có validation, relaxed binding, và IDE gợi ý được.

**Bên dưới:**
- `@ConfigurationProperties` dùng được với record, ví dụ `record AppProperties(int maxUploadSize)`, đăng ký
  bằng `@ConfigurationPropertiesScan` hoặc `@EnableConfigurationProperties`.
- Thêm `@Validated` để kiểm tra config lúc startup — config sai thì app không khởi động, tốt hơn lỗi lúc
  chạy.

**Nếu bị hỏi tiếp:** *"IDE gợi ý key là sao?"* → Thêm `spring-boot-configuration-processor` để sinh
metadata cho IDE.

**Nếu không chắc:** "Một giá trị thì `@Value`, một nhóm thì `@ConfigurationProperties`" là đủ.

<a id="q63"></a>
### Q63. Dùng `application.yml` hay `application.properties`? Để cả hai thì sao?

`[Fresher]` · ★

**Trả lời ngắn (nói trong lớp):**
> Hai định dạng tương đương, chọn một và dùng thống nhất. YAML gọn hơn cho config lồng nhau nhưng nhạy
> thụt đầu dòng. Để cả hai cùng chỗ thì `.properties` thắng khi trùng key — dễ gây nhầm, nên đừng trộn.

**Bên dưới:**
- YAML phải thụt đầu dòng bằng **dấu cách**, không dùng tab.
- Hai key gốc `spring:` trong cùng một file YAML → Boot báo lỗi lúc startup. Gộp vào một khối.

**Nếu bị hỏi tiếp:** *"Sao demo chọn YAML?"* → Dễ đọc cấu trúc lồng nhau trên slide.

**Nếu không chắc:** "`.properties` thắng khi trùng key" là chắc chắn.

<a id="q64"></a>
### Q64. Mật khẩu DB production để trong `application-prod.yml` rồi commit lên Git được không? Deploy Docker/K8s thì để đâu?

`[Senior]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Không. Slide 12 đã nói: profile không phải là công cụ quản lý secret. Trong yml chỉ để placeholder như
> `${DB_PASSWORD:}`; giá trị thật truyền qua env var, Kubernetes Secret, hoặc secret manager như Vault.

**Bên dưới:**
- Kubernetes mount secret thành file: Boot đọc được bằng `spring.config.import=configtree:<thư mục>`.
- Bẫy của demo: `spring.profiles.active=${SPRING_PROFILES_ACTIVE:dev}` → server production quên set biến
  thì app **chạy profile dev** (log DEBUG, in SQL) mà không báo lỗi. Production nên để mặc định an toàn,
  hoặc bắt buộc truyền profile.
- Password đã lỡ commit thì phải đổi password; xóa khỏi Git history là chưa đủ.

**Nếu bị hỏi tiếp:** *"Env var có an toàn tuyệt đối không?"* → Không; ai có quyền đọc process hoặc cấu hình
container vẫn thấy được. Secret manager an toàn hơn.

**Nếu không chắc:** "Không commit secret" là nguyên tắc chắc chắn. Chi tiết Vault: "ngoài phạm vi hôm nay".

<a id="q65"></a>
### Q65. Em đổi sang profile `prod` mà app trông y hệt. Làm sao biết đang chạy profile nào? Gõ sai tên profile thì sao?

`[QA]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Xem dòng log lúc khởi động: *`The following 1 profile is active: "prod"`*. Khác biệt nhìn thấy được giữa
> dev và prod trong demo chỉ có: câu SQL do `show-sql` in ra và dòng `log.debug` "Creating product
> sku=...". Gõ sai, ví dụ `prdo`, thì Spring **không báo lỗi**: không có file nào khớp, app chạy với config
> gốc.

**Bên dưới:**
- Không có profile nào: log *`No active profile set, falling back to 1 default profile: "default"`*.
- Gõ sai tên thì dòng log hiện `"prdo"` — nhìn log là phát hiện được.
- Đặt `spring.profiles.active` bên trong file theo profile (ví dụ trong `application-dev.yml`) → lỗi lúc
  startup (`InvalidConfigDataPropertyException`).

**Nếu bị hỏi tiếp:** *"Có cách nào chặn tên profile sai không?"* → Không có sẵn mặc định; có thể tự kiểm
tra lúc startup. Ngoài phạm vi hôm nay.

**Nếu không chắc:** Chỉ cần chỉ vào dòng log profile ở đầu console.

<a id="q66"></a>
### Q66. Em chạy JAR trong PowerShell với profile prod mà không ăn, gọi curl cũng lỗi. Sao vậy?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> PowerShell xử lý tham số khác bash. Cách chắc ăn nhất: set biến môi trường `$env:SPRING_PROFILES_ACTIVE="prod"`
> rồi chạy `java -jar target/product-api-0.0.1-SNAPSHOT.jar`. Nếu truyền `-D` thì đặt cả cụm trong dấu
> nháy, ví dụ `"-Dspring-boot.run.profiles=prod"` khi chạy qua Maven.

**Bên dưới:**
- Trong Windows PowerShell 5.1 (thường là terminal mặc định của IntelliJ trên Windows), `curl` là alias của
  `Invoke-WebRequest`, không phải curl thật. Gõ `curl.exe` thì đúng ở mọi phiên bản PowerShell.
- URL có `&` hoặc `,desc` phải để trong dấu nháy, nếu không PowerShell hiểu sai.
- Trong IntelliJ: điền profile vào ô "Active profiles" của Run Configuration, khỏi lo cú pháp shell.

**Nếu bị hỏi tiếp:** *"Env var set trong cửa sổ PowerShell có giữ lại không?"* → `$env:X` chỉ có hiệu lực
trong phiên PowerShell đó.

**Nếu không chắc:** "Dùng `$env:` hoặc Run Configuration của IntelliJ" — cả hai chắc chắn chạy.

---

## 9. Testing

<a id="q67"></a>
### Q67. Em test bằng Postman thấy chạy rồi, sao phải viết test? Unit test với integration test khác gì?

`[Fresher]` · ★

**Trả lời ngắn (nói trong lớp):**
> Postman là kiểm tra bằng tay, một lần. Test tự động chạy lại mỗi khi sửa code và bắt lỗi hồi quy. Unit
> test (`ProductServiceTest`) test service với repository giả, không cần Spring, chạy rất nhanh. Integration
> test (`ProductApiIntegrationTest`) dựng cả app, đi qua HTTP → validation → handler → JPA → H2.

**Bên dưới:**
- Unit test bắt lỗi logic (ví dụ SKU trùng phải ném `DuplicateSkuException` và không gọi `save`).
- Integration test bắt lỗi "nối dây": quên `@Valid`, sai mapping URL, handler trả sai status. Unit test của
  service không thấy những lỗi này.
- Slide 15: "Test behavior, not just syntax" — assert status và `code`, không chỉ assert "không có exception".

**Nếu bị hỏi tiếp:** *"Nên có bao nhiêu unit, bao nhiêu integration?"* → Nhiều unit test nhanh, ít
integration test cho các luồng chính (mô hình kim tự tháp). Không có con số cố định.

**Nếu không chắc:** "Hai loại bổ sung cho nhau, không thay thế nhau."

<a id="q68"></a>
### Q68. `@Mock` với `@MockBean` khác gì? Sao em gõ `@MockBean` trong Boot 4 không có?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> `@Mock` là của Mockito thuần: tạo object giả, không liên quan Spring — dùng trong unit test như
> `ProductServiceTest`. `@MockBean` là thay một bean trong Spring context bằng mock; ở Boot 4 nó **đã bị
> xóa**, thay bằng `@MockitoBean` của Spring Framework (package
> `org.springframework.test.context.bean.override.mockito`).

**Bên dưới:**
- `@SpyBean` cũng đổi thành `@MockitoSpyBean`.
- Test slice chỉ dựng một phần app, nhanh hơn `@SpringBootTest`:
  - `@WebMvcTest(ProductController.class)` dựng tầng web, service phải là `@MockitoBean`. Ở Boot 4 nằm
    trong `spring-boot-starter-webmvc-test`, package `org.springframework.boot.webmvc.test.autoconfigure`.
  - `@DataJpaTest` dựng tầng JPA; ở Boot 4 cần thêm `spring-boot-starter-data-jpa-test`.
- Demo dùng `@SpringBootTest` cho đơn giản.

**Nếu bị hỏi tiếp:** *"`@InjectMocks` làm gì?"* → Mockito tạo `ProductService` thật và truyền mock
repository vào constructor.

**Nếu không chắc:** Nói chắc: "`@MockBean` đã bị xóa ở Boot 4, dùng `@MockitoBean`."

<a id="q69"></a>
### Q69. App em đang chạy port 8080, em chạy integration test mà không bị lỗi port. Test có gọi vào app đang chạy không? Test có làm bẩn dữ liệu của app không?

`[QA]` · ★★ · **🔥 Hay gặp**

**Trả lời ngắn (nói trong lớp):**
> Không. `@SpringBootTest` mặc định dùng môi trường **MOCK**: không mở port thật, request đi qua `MockMvc`
> ngay trong bộ nhớ. Test chạy trong **JVM riêng**, nên có H2 in-memory riêng — dữ liệu của app đang chạy
> không bị đụng tới.

**Bên dưới:**
- Muốn chạy server thật trong test: `@SpringBootTest(webEnvironment = RANDOM_PORT)` — port ngẫu nhiên, vẫn
  không đụng 8080.
- Vì cùng lý do, dữ liệu từ `seed.http` không có trong test; test phải tự tạo dữ liệu nó cần.

**Nếu bị hỏi tiếp:** *"Vậy test có chạy thật qua Tomcat không?"* → Với MOCK thì không; `MockMvc` giả lập
request, nhưng vẫn đi qua `DispatcherServlet`, validation, handler như thật.

**Nếu không chắc:** Hai ý chắc chắn: "không mở port" và "JVM riêng nên DB riêng".

<a id="q70"></a>
### Q70. Sao mỗi test phải dùng SKU khác nhau (`KB-001`, `IT-DUP-001`)? Test có tự rollback không? Chạy riêng thì pass, chạy cả bộ thì fail?

`[Dev]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Không rollback. Spring cache test context giữa các test, nên các test dùng **chung** một H2. `MockMvc`
> test mặc định không rollback, dữ liệu test trước còn lại cho test sau. Dùng chung SKU thì test sau có thể
> nhận `409` bất ngờ.

**Bên dưới:**
- Thứ tự chạy test của JUnit không phải thứ tự viết trong file. Test phụ thuộc thứ tự là test hỏng.
- Cách xử lý: mỗi test dùng dữ liệu riêng (SKU riêng); hoặc dọn dữ liệu trong `@BeforeEach`
  (`productRepository.deleteAll()`); hoặc gắn `@Transactional` lên test class — với môi trường MOCK thì
  test sẽ rollback sau mỗi method.
- `@Transactional` trên test không có tác dụng với `RANDOM_PORT`, vì request chạy trên thread khác của
  server.

**Nếu bị hỏi tiếp:** *"`@DirtiesContext` thì sao?"* → Buộc dựng lại context — chạy được nhưng chậm, nên
dùng hạn chế.

**Nếu không chắc:** "Context được cache và dùng chung DB" là giải thích chắc chắn.

<a id="q71"></a>
### Q71. Sao dùng `MockMvc` mà không dùng RestAssured hay `TestRestTemplate`? Em viết test cho `sort=unknownField` expect 500 mà lại bị `ServletException`?

`[QA]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> `MockMvc` đi kèm Spring, nhanh, không cần port thật, và đi qua đúng tầng MVC. Nhưng nó không có servlet
> container thật, nên exception **không ai xử lý** (như `PropertyReferenceException`) không thành response
> `500` mà bị ném ra ngoài dạng `ServletException`. Muốn test "như client thật" thì chạy `RANDOM_PORT` với
> HTTP client.

**Bên dưới:**
- Ở Boot 4: `TestRestTemplate` cần `@AutoConfigureTestRestTemplate` và dependency
  `spring-boot-resttestclient`. `RestTestClient` là lựa chọn mới của Spring Framework 7.
- RestAssured là thư viện ngoài, QA automation hay quen; dùng được với `RANDOM_PORT`.
- Test hành vi `500` bằng `MockMvc`: dùng `assertThrows(ServletException.class, ...)`. Tốt hơn là sửa để
  trả `400` rồi test `400` ([Q56](#q56)).

**Nếu bị hỏi tiếp:** *"Nên dùng cái nào?"* → Dùng thống nhất trong team; `MockMvc` là điểm bắt đầu tốt.

**Nếu không chắc:** Đừng khẳng định API cụ thể của `RestTestClient`. "Đó là lựa chọn mới, mình gửi docs."

<a id="q72"></a>
### Q72. Chạy test thấy WARNING đỏ về Mockito "self-attaching", và có lúc bị `UnnecessaryStubbingException`. Là lỗi gì?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Dòng WARNING (đại ý Mockito đang tự gắn agent, "A Java agent has been loaded dynamically") trên JDK 21 là
> **vô hại**, test vẫn pass. Còn `UnnecessaryStubbingException` là do `MockitoExtension` chạy chế độ
> `STRICT_STUBS`: mình `when(...)` một thứ mà test không bao giờ gọi tới — Mockito báo để code test sạch.

**Bên dưới:**
- JDK tương lai có thể chặn việc nạp agent động; khi đó cấu hình Mockito thành `-javaagent` trong Surefire.
  Hôm nay chưa cần.
- Sửa `UnnecessaryStubbingException`: xóa stub thừa. Chỉ dùng `lenient()` khi thật sự cần.
- Ví dụ trong `ProductServiceTest` của branch `solution` (test `create_rejectsDuplicateSku`): chỉ stub `existsBySku` và `verify(..., never()).save(...)`,
  không stub `save`.

**Nếu bị hỏi tiếp:** *"Tắt WARNING được không?"* → Được bằng cách cấu hình Mockito làm java agent trong
build. Không bắt buộc.

**Nếu không chắc:** "WARNING vô hại, test vẫn xanh" — nói chắc.

<a id="q73"></a>
### Q73. Test trên H2 mà production chạy PostgreSQL thì có đáng tin không? Testcontainers là gì?

`[Senior]` · ★★

**Trả lời ngắn (nói trong lớp):**
> H2 nhanh và tiện cho demo, nhưng khác PostgreSQL về kiểu dữ liệu, SQL, locking. Testcontainers dùng
> Docker để bật một PostgreSQL thật cho test, rồi tắt khi xong. Boot có `@ServiceConnection` để tự nối
> datasource vào container, không cần cấu hình URL.

**Bên dưới:**
- Boot 4 đi với Testcontainers 2.0.x, tên artifact đã đổi (ví dụ `testcontainers-postgresql`). Code mẫu cũ
  trên mạng có thể dùng tên artifact cũ.
- Máy chạy test (và CI) phải có Docker.
- Kết hợp phổ biến: unit test không cần DB; integration test quan trọng chạy trên Testcontainers.

**Nếu bị hỏi tiếp:** *"Máy công ty không có Docker thì sao?"* → Vẫn dùng H2 cho phần lớn test, và có một
môi trường test với DB thật trước khi release.

**Nếu không chắc:** Chi tiết tên artifact: "đã đổi ở bản 2.0, mình kiểm tra lại rồi gửi".

<a id="q74"></a>
### Q74. Coverage bao nhiêu là đủ? Có nên làm TDD không?

`[QA]` · ★

**Trả lời ngắn (nói trong lớp):**
> Không có con số thần kỳ. Coverage cao mà không assert hành vi thì vô nghĩa (slide 15: "Test behavior, not
> just syntax"). Đo coverage thì dùng JaCoCo, nhưng phải tự khai báo version plugin vì Boot BOM không quản
> lý nó. TDD là cách làm tốt, nhưng hôm nay mình viết code trước rồi test sau cho dễ theo dõi.

**Bên dưới:**
- Ưu tiên test: luồng chính, các nhánh lỗi (400/404/409), và logic nghiệp vụ có điều kiện.
- Coverage thấp là tín hiệu thiếu test; coverage cao chưa chắc đã đủ test.

**Nếu bị hỏi tiếp:** *"Dự án em yêu cầu 80%?"* → Theo quy định của dự án, nhưng ưu tiên chất lượng assert.

**Nếu không chắc:** Không cần bảo vệ một con số cụ thể.

---

## 10. Versions (Boot 4, Java 21, JUnit 6)

<a id="q75"></a>
### Q75. Slide 8 ghi JUnit 5, mà project dùng JUnit 6. Khác nhau nhiều không? Code JUnit 5 cũ có chạy không?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Boot 4 đi kèm JUnit 6 (Jupiter 6.0.3). Với người viết test, gần như không khác: vẫn `@Test`,
> `@BeforeEach`, `assertThrows`, vẫn package `org.junit.jupiter.api`. Slide ghi JUnit 5 vì cách viết giống
> hệt; test kiểu JUnit 5 thông thường chạy được.

**Bên dưới:**
- JUnit 6 yêu cầu Java 17 trở lên, và thống nhất số version giữa các module của JUnit.
- Test JUnit 4 cũ (`org.junit.Test`) là chuyện khác — cần engine Vintage hoặc chuyển sang Jupiter.

**Nếu bị hỏi tiếp:** *"Vậy tính năng mới của JUnit 6 là gì?"* → Để parking lot; gửi release notes.

**Nếu không chắc:** Đừng liệt kê tính năng mới của JUnit 6 nếu không chắc. "Với code hôm nay, cách viết
không đổi."

<a id="q76"></a>
### Q76. Dự án em đang Boot 3 (hoặc Boot 2, còn `javax.*`). Lên Boot 4 cần chú ý gì?

`[Senior]` · ★★★

**Trả lời ngắn (nói trong lớp):**
> Boot 4 dựa trên Spring Framework 7, Jakarta EE 11, Java 17 trở lên, Jackson 3. Những chỗ hay vướng:
> starter và annotation test bị tách module, `@MockBean` bị xóa, Jackson đổi package. Nếu đang Boot 2 thì
> trước đó còn phải đổi `javax.*` sang `jakarta.*` — thay đổi đó xảy ra từ Boot 3.

**Bên dưới:**

| Boot 3 | Boot 4 |
|---|---|
| `spring-boot-starter-web` | `spring-boot-starter-webmvc` (bản cũ deprecated) |
| `spring-boot-starter-test` đủ cho mọi test | starter test theo module: `-webmvc-test`, `-data-jpa-test`… |
| `@MockBean`, `@SpyBean` | `@MockitoBean`, `@MockitoSpyBean` |
| `AutoConfigureMockMvc` trong `org.springframework.boot.test.autoconfigure.web.servlet` | trong `org.springframework.boot.webmvc.test.autoconfigure` |
| Jackson 2 (`com.fasterxml.jackson.databind`) | Jackson 3 (`tools.jackson`); annotation vẫn ở `com.fasterxml.jackson.annotation` |
| `HttpStatus.UNPROCESSABLE_ENTITY` | `HttpStatus.UNPROCESSABLE_CONTENT` |

- Mới ở Spring 7: API versioning có sẵn (ví dụ `@GetMapping(version = "1.1")`), null-safety với JSpecify.
- `spring-boot-starter-classic` giúp giữ classpath kiểu cũ trong lúc chuyển dần.

**Nếu bị hỏi tiếp:** *"Có tool tự migrate không?"* → Có công cụ như OpenRewrite; mình chưa thử với 4.1, nên
xem release notes và migration guide chính thức trước.

**Nếu không chắc:** "Đọc migration guide chính thức của Boot 4" — đừng hứa danh sách đầy đủ.

<a id="q77"></a>
### Q77. Demo dùng Java 21 thì có dùng virtual threads không? Có nên chuyển sang WebFlux cho nhanh?

`[Senior]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Demo không dùng tính năng riêng của Java 21; code chủ yếu dùng record và text block (có từ Java 16 và 15),
> và Boot 4 chỉ yêu cầu Java 17. Virtual threads bật bằng `spring.threads.virtual.enabled=true`, giữ nguyên
> kiểu code blocking. WebFlux là mô hình reactive khác hẳn — với API CRUD như hôm nay thường không cần.

**Bên dưới:**
- Virtual threads giúp khi có nhiều request chờ I/O. Phải đo, và để ý giới hạn khác như connection pool DB.
- WebFlux cần code reactive xuyên suốt (driver R2DBC thay vì JDBC/JPA). Chuyển sang là thay đổi lớn.

**Nếu bị hỏi tiếp:** *"GraalVM native image?"* → Boot có hỗ trợ; khởi động nhanh hơn nhưng build lâu và có
hạn chế với reflection. Ngoài phạm vi hôm nay.

**Nếu không chắc:** "Phải đo trên workload thật trước khi bật" là câu an toàn.

---

## 11. Beyond today (security, microservices, production-ready)

<a id="q78"></a>
### Q78. Thông báo khóa học ghi "production-ready REST APIs". Code này đem đi production được chưa?

`[Senior]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Chưa. Hôm nay là **nền tảng** đúng cách để đi tới production: tách tầng, DTO, validation, error format,
> transaction, test. Muốn lên production còn thiếu: bảo mật, migration DB, giám sát, log, và xử lý đầy đủ
> các lỗi.

**Bên dưới:**
- Danh sách còn thiếu:
  - Spring Security ([Q79](#q79)).
  - Flyway + `ddl-auto: validate` ([Q41](#q41)).
  - Actuator cho health check và metrics.
  - Handler cho mọi lỗi và log có ngữ cảnh ([Q33](#q33), [Q34](#q34)).
  - Map `DataIntegrityViolationException` sang 409 ([Q52](#q52)).
  - Secret không nằm trong Git ([Q64](#q64)).
  - Test trên DB thật ([Q73](#q73)).
  - Tài liệu API ([Q24](#q24)).
- Nói thật ở đây sẽ giúp mình được tin tưởng hơn là cố bảo vệ.

**Nếu bị hỏi tiếp:** *"Có khóa tiếp theo không?"* → Ghi nhận vào parking lot và chuyển cho ban tổ chức.

**Nếu không chắc:** "Đây là nền móng, chưa phải bản production hoàn chỉnh."

<a id="q79"></a>
### Q79. Thêm login / JWT thì làm sao? Thêm Spring Security vào là xong hả anh?

`[Dev]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Thêm `spring-boot-starter-security` thì mặc định **mọi endpoint đều bị khóa**. Boot tạo user `user` với
> mật khẩu ngẫu nhiên in ra console lúc khởi động (đại ý *"Using generated security password: ..."*). Chỉ để
> thử; thực tế phải tự cấu hình `SecurityFilterChain`. JWT thì thường dùng OAuth2 Resource Server.

**Bên dưới:**
- Mới thêm starter, request `POST` có thể bị `403` vì CSRF protection bật mặc định. Với API stateless dùng
  token, cấu hình CSRF cho phù hợp — hiểu rõ lý do trước khi tắt.
- Bẫy cho integration test: thêm security là các test hiện tại nhận `401`/`403`; cần công cụ test của
  Spring Security.
- Là một chủ đề lớn, nên để buổi riêng.

**Nếu bị hỏi tiếp:** *"Phân quyền theo role?"* → `authorizeHttpRequests` trong `SecurityFilterChain`, hoặc
`@PreAuthorize` trên method. Để parking lot.

**Nếu không chắc:** "Security là một buổi riêng; mình không muốn nói nửa vời" — hoàn toàn chấp nhận được.

<a id="q80"></a>
### Q80. Có nên tách Product API này thành microservices luôn không?

`[Node]` · ★★

**Trả lời ngắn (nói trong lớp):**
> Không nên bắt đầu bằng microservices. Nó giải quyết bài toán tổ chức và scale, nhưng đổi lại rất nhiều
> độ phức tạp: gọi qua mạng, transaction phân tán, deploy, giám sát. Bắt đầu bằng một ứng dụng chia package
> rõ ràng như hôm nay, khi thật sự cần thì tách dần.

**Bên dưới:**
- Mỗi microservice nên có DB riêng; khi đó không còn `@Transactional` chung giữa các service.
- Hệ sinh thái Spring có Spring Cloud (gateway, config, service discovery…) khi cần đi tiếp.
- Cấu trúc tầng controller → service → repository hôm nay vẫn dùng nguyên bên trong mỗi service.

**Nếu bị hỏi tiếp:** *"Modular monolith là gì?"* → Một ứng dụng nhưng chia module có ranh giới rõ; Spring
có dự án Spring Modulith hỗ trợ. Để parking lot.

**Nếu không chắc:** "Tùy bài toán và quy mô team" là câu trả lời trung thực.

---

*Hết. 80 câu, 11 chủ đề. Câu chưa có ở đây: nhắc lại câu hỏi, trả lời phần mình chắc, phần còn lại đưa vào
parking lot và gửi sau buổi trong nhóm chat.*
