# Kiến thức nâng cao về Spring và Spring Boot — dành cho Technical Lead / Solution Architect

Tài liệu này dành cho người đã nắm Spring Boot cơ bản và cần đi xa hơn: hiểu framework hoạt động
thế nào bên trong, ra quyết định kiến trúc có cân nhắc trade-off, nhận ra pitfall trước khi nó
xảy ra ở production, review code của team và trả lời được những câu hỏi khó.

Nó **không** cần cho buổi demo Product API. Kiến thức cho buổi demo nằm ở `01-study-guide-vi.md`
và `02-qa-bank-vi.md`. Khi học viên hỏi vượt ra ngoài phạm vi demo, hoặc khi bạn cần chuẩn bị cho
vai trò Tech Lead, hãy dùng tài liệu này.

## Cách đọc

Mỗi mục có cùng cấu trúc (bỏ phần không phù hợp):

| Nhãn | Nội dung |
|---|---|
| **Tóm tắt** | Những điều cốt lõi phải nắm |
| **Cơ chế bên dưới** | Điều thực sự xảy ra trong framework |
| **Quyết định & trade-off** | Khi nào chọn cách nào, và cái giá phải trả |
| **Pitfalls trong production** | Triệu chứng → nguyên nhân → cách xử lý |
| **Liên hệ với demo** | Demo đã đơn giản hóa điều gì, production nên làm gì |
| **Câu hỏi kiểm tra** | Câu hỏi mức senior kèm ý trả lời; dùng để tự ôn hoặc phỏng vấn |

Gợi ý lộ trình: đọc Phần I và II trước (nền tảng cho mọi quyết định khác), sau đó đọc Phần III
và IV theo nhu cầu. Mục 31 là checklist review code và bộ câu hỏi phỏng vấn, dùng được ngay.

## Phạm vi phiên bản

Nội dung viết cho **Spring Boot 4.1.x, Spring Framework 7.0.x, Spring Data 2026.0, Hibernate ORM 7,
Jackson 3, JUnit 6, Java 21** (có nhắc Java 25 khi liên quan). Khi hành vi khác giữa các phiên
bản, tài liệu ghi rõ ("từ Boot 3.4", "Framework 7"...). Rất nhiều bài viết trên mạng vẫn viết cho
Boot 2/3, nên hãy chú ý các ghi chú phiên bản này.

Các chi tiết phụ thuộc phiên bản (tên property, class, package, giá trị mặc định) đã được đối
chiếu với source của Spring Boot 4.0.5 / Framework 7.0.6 có trên máy, cùng docs và release notes
chính thức của Boot 4.1 / Framework 7.0 (tháng 9/2026). Không có gì được build hay chạy thử. Chỗ nào
chưa kiểm chứng được thì đã viết mềm đi (ví dụ "kiểm tra lại trong docs của phiên bản đang dùng").
Trước khi đưa một con số hoặc tên property vào hệ thống thật, hãy kiểm tra lại với phiên bản
dự án đang dùng.

## Mục lục

- **[Phần I — Spring Core nâng cao](#phần-i--spring-core-nâng-cao)**
  - [1. ApplicationContext và vòng đời bean](#1-applicationcontext-và-vòng-đời-bean)
  - [2. Cấu hình và đăng ký bean nâng cao](#2-cấu-hình-và-đăng-ký-bean-nâng-cao)
  - [3. Scope, injection và dependency resolution](#3-scope-injection-và-dependency-resolution)
  - [4. AOP và proxy](#4-aop-và-proxy)
  - [5. Application events](#5-application-events)
  - [6. Environment và PropertySource ở tầng Framework](#6-environment-và-propertysource-ở-tầng-framework)
  - [7. Những điểm mới đáng chú ý của Spring Framework 6.x–7](#7-những-điểm-mới-đáng-chú-ý-của-spring-framework-6x7)
- **[Phần II — Data, transaction và tính nhất quán](#phần-ii--data-transaction-và-tính-nhất-quán)**
  - [8. Transaction management chuyên sâu](#8-transaction-management-chuyên-sâu)
  - [9. JPA/Hibernate cho kiến trúc sư](#9-jpahibernate-cho-kiến-trúc-sư)
  - [10. Hiệu năng truy vấn](#10-hiệu-năng-truy-vấn)
  - [11. Concurrency control](#11-concurrency-control)
  - [12. Chọn công nghệ data access và connection pool](#12-chọn-công-nghệ-data-access-và-connection-pool)
  - [13. Schema migration và zero-downtime deployment](#13-schema-migration-và-zero-downtime-deployment)
  - [14. Tính nhất quán giữa các service](#14-tính-nhất-quán-giữa-các-service)
  - [15. Caching](#15-caching)
- **[Phần III — Spring Boot bên trong, Web và Security](#phần-iii--spring-boot-bên-trong-web-và-security)**
  - [16. Auto-configuration hoạt động thế nào](#16-auto-configuration-hoạt-động-thế-nào)
  - [17. Xây dựng starter nội bộ cho platform team](#17-xây-dựng-starter-nội-bộ-cho-platform-team)
  - [18. Externalized configuration nâng cao](#18-externalized-configuration-nâng-cao)
  - [19. SpringApplication startup: lifecycle, extension points và tối ưu](#19-springapplication-startup-lifecycle-extension-points-và-tối-ưu)
  - [20. Spring MVC request pipeline](#20-spring-mvc-request-pipeline)
  - [21. HTTP client và tích hợp service](#21-http-client-và-tích-hợp-service)
  - [22. Mô hình concurrency](#22-mô-hình-concurrency)
  - [23. Kiến trúc Spring Security](#23-kiến-trúc-spring-security)
- **[Phần IV — Vận hành, testing, kiến trúc và governance](#phần-iv--vận-hành-testing-kiến-trúc-và-governance)**
  - [24. Actuator, health và Kubernetes probes](#24-actuator-health-và-kubernetes-probes)
  - [25. Observability: metrics, traces, logs](#25-observability-metrics-traces-logs)
  - [26. Đóng gói và triển khai](#26-đóng-gói-và-triển-khai)
  - [27. Hiệu năng và resilience](#27-hiệu-năng-và-resilience)
  - [28. Chiến lược testing cho cả team](#28-chiến-lược-testing-cho-cả-team)
  - [29. Phong cách kiến trúc](#29-phong-cách-kiến-trúc)
  - [30. Nâng cấp phiên bản và quản lý dependency](#30-nâng-cấp-phiên-bản-và-quản-lý-dependency)
  - [31. Checklist cho Tech Lead](#31-checklist-cho-tech-lead)

---

## Phần I — Spring Core nâng cao

### 1. ApplicationContext và vòng đời bean

**Tóm tắt**
- Container làm việc với **BeanDefinition** (metadata: class, scope, lazy, factory method, constructor args, init/destroy method), không phải với object. Mọi annotation (`@Component`, `@Bean`, `@Import`, auto-configuration) đều được dịch thành BeanDefinition trước khi bất kỳ bean nào được tạo.
- `BeanFactory` là DI container tối thiểu (lazy, không tự đăng ký post-processor). `ApplicationContext` = `BeanFactory` + tự phát hiện và chạy post-processor, event, `MessageSource`, `Environment`, resource loading, lifecycle (`SmartLifecycle`). Ứng dụng thực tế luôn dùng `ApplicationContext`.
- Hai loại hook quan trọng nhất: **BeanFactoryPostProcessor** (sửa *định nghĩa* bean, chạy trước khi có bean) và **BeanPostProcessor** (bọc/sửa *instance* bean, chạy quanh init callback). Proxy cho AOP, `@Transactional`, `@Async` đều được tạo trong BeanPostProcessor.
- Thứ tự callback cần thuộc khi debug: constructor → inject → `*Aware` → `@PostConstruct` → `afterPropertiesSet()` → init-method → proxy → `SmartInitializingSingleton` → `SmartLifecycle.start()` → `ContextRefreshedEvent`.
- Khi shutdown: `SmartLifecycle.stop()` theo phase giảm dần → `@PreDestroy` → `DisposableBean.destroy()` → destroy-method.

**Cơ chế bên dưới**

`AbstractApplicationContext.refresh()` (Framework 7.0) gồm các bước chính:

| Bước | Việc xảy ra | Ghi chú cho Boot |
|---|---|---|
| `prepareRefresh()` | Khởi tạo property source, validate required properties | |
| `obtainFreshBeanFactory()`, `prepareBeanFactory()` | Có `DefaultListableBeanFactory`, đăng ký bean hạ tầng (`Environment`, `ApplicationContextAwareProcessor`...) | |
| `invokeBeanFactoryPostProcessors()` | Chạy `BeanDefinitionRegistryPostProcessor` rồi `BeanFactoryPostProcessor` (theo `PriorityOrdered` → `Ordered` → còn lại) | `ConfigurationClassPostProcessor` xử lý `@Configuration`, `@ComponentScan`, `@Import`, `@Bean`, auto-configuration và đánh giá `@Conditional` ở đây |
| `registerBeanPostProcessors()` | Tạo và đăng ký toàn bộ BeanPostProcessor | Bean nào bị tạo trước bước này sẽ không được BPP xử lý đầy đủ |
| `initMessageSource()`, `initApplicationEventMulticaster()` | | |
| `onRefresh()` | Hook cho subclass | Boot servlet context tạo embedded Tomcat ở đây (chưa nhận request) |
| `registerListeners()` | Đăng ký `ApplicationListener`, phát các early event | |
| `finishBeanFactoryInitialization()` | Tạo mọi singleton non-lazy; cuối cùng gọi `SmartInitializingSingleton.afterSingletonsInstantiated()` | Phần lớn thời gian startup nằm ở đây |
| `finishRefresh()` | `LifecycleProcessor.onRefresh()` start các `SmartLifecycle` theo phase; publish `ContextRefreshedEvent` | Web server thực sự start trong một `SmartLifecycle` của Boot |

Nếu có exception giữa chừng, `refresh()` stop các lifecycle bean đã start, destroy các singleton đã tạo, rồi ném lại exception (log `Exception encountered during context initialization - cancelling refresh attempt`).

Vòng đời một singleton (trong `AbstractAutowireCapableBeanFactory`):
1. Instantiate (constructor / factory method). `SmartInstantiationAwareBeanPostProcessor` có thể chọn constructor.
2. Populate: inject field/setter, `@Value` (`AutowiredAnnotationBeanPostProcessor`).
3. `BeanNameAware`, `BeanFactoryAware`; các `*Aware` của context (`ApplicationContextAware`...) đi qua `ApplicationContextAwareProcessor`.
4. `postProcessBeforeInitialization` của mọi BPP. `@PostConstruct` được gọi ở đây (bởi `CommonAnnotationBeanPostProcessor`).
5. `InitializingBean.afterPropertiesSet()` → init-method tùy chỉnh (`@Bean(initMethod = ...)`).
6. `postProcessAfterInitialization`: `AbstractAutoProxyCreator` (AOP, `@Transactional`, `@Cacheable`...) trả về **proxy** thay cho object gốc. Từ đây container giữ proxy.

Hệ quả: trong `@PostConstruct`, gọi method `@Transactional` của chính bean **không** đi qua proxy (proxy chưa tồn tại, và `this` luôn là object gốc). Việc cần transaction lúc khởi động nên đặt ở `ApplicationRunner` hoặc listener `ApplicationReadyEvent`, gọi qua một bean khác.

`SmartLifecycle`: `getPhase()` mặc định là `SmartLifecycle.DEFAULT_PHASE = Integer.MAX_VALUE`. Start theo phase tăng dần, stop theo phase giảm dần. Trong Boot 4, graceful shutdown của web server ở phase `DEFAULT_PHASE - 1024`, start/stop web server ở `DEFAULT_PHASE - 2048`. `DefaultLifecycleProcessor` mặc định chờ 10 giây mỗi phase khi stop; Boot cấu hình qua `spring.lifecycle.timeout-per-shutdown-phase`, mặc định **30s**.

Framework 7 thêm `ConfigurableApplicationContext.pause()` / `restart()` và `SmartLifecycle.isPauseable()` (mặc định `true`), nền tảng cho việc pause context trong TestContext cache (xem mục 7).

Vì sao BeanPostProcessor khai báo bằng `@Bean` phải là **static**:
```java
@Configuration(proxyBeanMethods = false)
class AuditConfig {
    @Bean
    static AuditBeanPostProcessor auditBpp() {  // static: không cần tạo instance AuditConfig
        return new AuditBeanPostProcessor();
    }
}
```
BPP phải được tạo ở `registerBeanPostProcessors()`, rất sớm. Nếu method không static, container buộc tạo `AuditConfig` (và dependency của nó) trước khi các BPP khác sẵn sàng. Những bean đó sẽ không được proxy, không được xử lý đầy đủ. Framework log WARN `Bean '...' of type [...] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)` kèm gợi ý "consider declaring it as static". Quy tắc tương tự cho `BeanFactoryPostProcessor` (ví dụ `PropertySourcesPlaceholderConfigurer`): method non-static trả về BFPP bị log INFO.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@PostConstruct` | Khởi tạo nhẹ, chỉ dùng state của chính bean | Chưa có proxy; exception làm hỏng startup; chạy trên main thread |
| `SmartInitializingSingleton` | Cần mọi singleton đã được tạo (ví dụ gom registry) | Vẫn trong `refresh()`, chưa có web server |
| `ApplicationRunner` / `ApplicationReadyEvent` | Warm-up cache, gọi service có `@Transactional` | Với `ApplicationReadyEvent`, server có thể đã nhận request; readiness probe phải tính tới |
| `SmartLifecycle` | Component có start/stop thật (consumer, scheduler, connection) | Phải tự quản lý `isRunning`, phase, timeout khi stop |
| `@Bean(bootstrap = Bean.Bootstrap.BACKGROUND)` (Framework 6.2+) | Bean khởi tạo chậm, độc lập | Cần executor tên `bootstrapExecutor` (Boot tự alias `applicationTaskExecutor` thành tên này nếu chưa có); thứ tự khó đoán; chỉ đáng dùng khi đo được lợi ích startup |

**Pitfalls trong production**
- WARN "not eligible for getting processed by all BeanPostProcessors" → BPP/BFPP khai báo non-static, hoặc BPP inject bean nghiệp vụ → bean đó mất proxy (`@Transactional` âm thầm không chạy). Xử lý: khai báo static; trong BPP chỉ lấy dependency lazily (`ObjectProvider`).
- Startup chậm hoặc treo → I/O nặng (gọi HTTP, load cache lớn) trong `@PostConstruct` → chuyển sang `ApplicationRunner` hoặc warm-up bất đồng bộ, gắn với readiness.
- Request bị cắt khi deploy → consumer/scheduler tự viết không implement `SmartLifecycle`, hoặc stop lâu hơn 30s → đặt phase hợp lý, tăng `timeout-per-shutdown-phase` và `terminationGracePeriodSeconds` của Kubernetes cho khớp.
- Rò rỉ tài nguyên → bean prototype không nhận destroy callback (container không quản lý việc hủy prototype) → caller tự đóng, hoặc đổi scope.

**Liên hệ với demo**
Demo không dùng hook vòng đời nào. Nếu cần seed dữ liệu khi khởi động ở production, dùng migration tool (Flyway/Liquibase) thay vì `@PostConstruct` gọi repository.

**Câu hỏi kiểm tra**
- **H:** Vì sao gọi `this.save()` có `@Transactional` trong `@PostConstruct` không mở transaction?
  **Đ:** `@PostConstruct` chạy ở `postProcessBeforeInitialization`, trước khi auto-proxy creator tạo proxy ở `postProcessAfterInitialization`; hơn nữa `this` luôn là object gốc. Dùng `ApplicationRunner` gọi qua bean khác.
- **H:** `BeanFactoryPostProcessor` và `BeanPostProcessor` khác nhau thế nào? Cho ví dụ mỗi loại.
  **Đ:** BFPP sửa BeanDefinition trước khi tạo bean (`ConfigurationClassPostProcessor`, `PropertySourcesPlaceholderConfigurer`); BPP xử lý instance (`AutowiredAnnotationBeanPostProcessor`, auto-proxy creator).
- **H:** Consumer tự viết cần dừng nhận message trước khi datasource bị đóng. Làm thế nào?
  **Đ:** Implement `SmartLifecycle`: `stop()` chạy ở giai đoạn lifecycle, trước khi singleton bị destroy, nên datasource vẫn còn. Chọn phase để consumer dừng trước các component khác mà nó dùng.
- **H:** Refresh thất bại ở bean thứ 200 thì 199 bean trước có được cleanup không?
  **Đ:** Có. `refresh()` stop các lifecycle bean đã start, gọi `destroyBeans()`, rồi ném lại exception.

### 2. Cấu hình và đăng ký bean nâng cao

**Tóm tắt**
- `@Configuration` mặc định chạy **full mode** (`proxyBeanMethods = true`): class được CGLIB subclass để lời gọi `@Bean` method nội bộ trả về đúng singleton. **Lite mode** (`proxyBeanMethods = false`, hoặc `@Bean` trong `@Component`) không có subclass, khởi động nhanh hơn, nhưng gọi method `@Bean` trực tiếp sẽ tạo object mới.
- Có 4 cơ chế "import" với mức kiểm soát tăng dần: `@Import(Config.class)` → `ImportSelector` (chọn class theo metadata) → `DeferredImportSelector` (chạy sau mọi `@Configuration` của người dùng; auto-configuration của Boot dựa vào đây) → `ImportBeanDefinitionRegistrar` / `BeanRegistrar` (đăng ký bean bằng code).
- `@Conditional` + `Condition` là nền của mọi `@ConditionalOn...` của Boot và của `@Profile`. Condition được đánh giá khi parse cấu hình, **trước** khi có bean.
- Boot tắt bean definition overriding mặc định (`spring.main.allow-bean-definition-overriding=false`): hai bean cùng tên → startup fail. Đây là tính năng bảo vệ, đừng bật lên để "chữa cháy".
- Framework 7 thêm `BeanRegistrar` (`org.springframework.beans.factory`): API đăng ký bean bằng code, gọn, thân thiện với AOT, thay thế phần lớn nhu cầu viết `ImportBeanDefinitionRegistrar`.

**Cơ chế bên dưới**

Full vs lite mode:
```java
@Configuration // full mode: CGLIB subclass
class ClientConfig {
    @Bean HttpClientProps props() { return new HttpClientProps(); }
    @Bean OrderClient orderClient() { return new OrderClient(props()); } // props() trả về singleton đã có
}

@Configuration(proxyBeanMethods = false) // lite mode
class ClientConfigLite {
    @Bean HttpClientProps props() { return new HttpClientProps(); }
    @Bean OrderClient orderClient(HttpClientProps props) { return new OrderClient(props); } // inject qua tham số
}
```
Ở lite mode, nếu vẫn viết `new OrderClient(props())` thì `props()` là lời gọi Java thuần: tạo object thứ hai ngoài container. Auto-configuration của Boot dùng `proxyBeanMethods = false` gần như toàn bộ; team nên theo cùng quy ước và luôn inject qua tham số.

Full mode đòi hỏi class và method `@Bean` không được `final`/`private` (CGLIB phải override). Từ Framework 6.0, `@Configuration(enforceUniqueMethods = true)` là mặc định: overload method `@Bean` cùng tên trong một class sẽ lỗi. Framework 7.0 deprecate thuộc tính này và luôn yêu cầu tên method `@Bean` là duy nhất (dependency tùy chọn thì dùng tham số `Optional`/`ObjectProvider`).

Thứ tự xử lý trong `ConfigurationClassPostProcessor`:
1. Parse các `@Configuration` người dùng (từ `@ComponentScan`), xử lý `@Import`, `ImportSelector` ngay lập tức.
2. Sau cùng mới xử lý các `DeferredImportSelector`. `AutoConfigurationImportSelector` của Boot là một `DeferredImportSelector`, đọc danh sách từ `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.
3. Vì vậy `@ConditionalOnMissingBean` trong auto-configuration "thấy" được bean người dùng đã định nghĩa, và bean người dùng thắng (back-off).

Condition tự viết:
```java
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;

class OnFeatureCondition implements Condition {
    @Override
    public boolean matches(ConditionContext ctx, AnnotatedTypeMetadata md) {
        var attrs = md.getAnnotationAttributes(ConditionalOnFeature.class.getName());
        String name = (String) attrs.get("value");
        return ctx.getEnvironment().getProperty("features." + name + ".enabled", Boolean.class, false);
    }
}
// @Target({TYPE, METHOD}) @Retention(RUNTIME) @Conditional(OnFeatureCondition.class)
// public @interface ConditionalOnFeature { String value(); }
```
Trong ứng dụng Boot, kế thừa `SpringBootCondition` thay vì `Condition` để kết quả xuất hiện trong Conditions Evaluation Report (`--debug`). Nếu condition cần biết bean nào đã đăng ký, implement `ConfigurationCondition` với phase `REGISTER_BEAN`; mặc định condition trên class `@Configuration` được đánh giá ở `PARSE_CONFIGURATION`, lúc đó registry chưa đầy đủ.

`BeanRegistrar` (Framework 7):
```java
class ClientsRegistrar implements BeanRegistrar {
    @Override
    public void register(BeanRegistry registry, Environment env) {
        for (String name : List.of("orders", "payments")) {
            registry.registerBean(name + "Client", RemoteClient.class, spec -> spec
                .lazyInit()
                .supplier(ctx -> new RemoteClient(env.getProperty("clients." + name + ".url"))));
        }
    }
}
// @Configuration @Import(ClientsRegistrar.class) class ClientsConfig {}
```
`BeanRegistry.Spec` hỗ trợ `primary()`, `fallback()`, `prototype()`, `scope(...)`, `lazyInit()`, `backgroundInit()`, `order(...)`, `notAutowirable()`, `supplier(...)`. Có thể đăng ký bằng `@Import` hoặc `GenericApplicationContext.register(BeanRegistrar...)`.

`@Profile` là tính năng tầng Framework: `@Profile("prod")` thực chất là `@Conditional(ProfileCondition.class)`, hỗ trợ biểu thức `"prod & !cloud"`, `"dev | test"`. Boot chỉ bổ sung cách kích hoạt profile và nạp `application-{profile}.yml`.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@Bean` trong `@Configuration(proxyBeanMethods = false)` | Mặc định cho code ứng dụng | Phải inject qua tham số, không gọi method `@Bean` trực tiếp |
| Full mode (`proxyBeanMethods = true`) | Code cũ gọi chéo `@Bean` method | CGLIB subclass, startup chậm hơn chút, cấm `final` |
| `@Import` / `ImportSelector` | Module nội bộ bật bằng annotation `@EnableXxx` | Người dùng phải nhớ thêm annotation |
| Auto-configuration riêng (starter nội bộ) | Thư viện dùng chung cho nhiều service | Phải viết đúng `@ConditionalOnMissingBean`, test bằng `ApplicationContextRunner` |
| `BeanRegistrar` | Số lượng bean phụ thuộc cấu hình (multi-tenant, N client) | Chỉ có từ Framework 7; code imperative khó đọc hơn `@Bean` |
| `ImportBeanDefinitionRegistrar` | Cần thao tác `BeanDefinition` cấp thấp, hoặc còn hỗ trợ Framework 6 | API dài dòng, kém thân thiện AOT |

**Pitfalls trong production**
- Startup fail `The bean 'x', defined in ..., could not be registered. A bean with that name has already been defined in ... and overriding is disabled.` → hai `@Bean` cùng tên method ở hai config, hoặc thư viện trùng tên bean → đổi tên bean (`@Bean("...")`) hoặc dùng `@ConditionalOnMissingBean`. Bật `allow-bean-definition-overriding` chỉ che giấu lỗi và làm kết quả phụ thuộc thứ tự nạp.
- Bean của người dùng không thay được bean auto-config → auto-config dùng `@ConditionalOnMissingBean` theo type khác (ví dụ khai báo `ObjectMapper` con trong khi condition xét type khác) → đọc Conditions Evaluation Report, khai báo đúng type/tên.
- Condition tự viết dựa vào bean khác cho kết quả sai → đánh giá ở phase parse, bean chưa đăng ký → dùng `ConfigurationCondition` phase `REGISTER_BEAN`, hoặc chuyển sang condition dựa trên property/classpath.
- Hai instance "singleton" của cùng object → lite mode + gọi method `@Bean` trực tiếp → inject qua tham số.

**Liên hệ với demo**
Demo chỉ dùng component scan. Khi nhiều service cùng cần một cấu hình (logging, error format, client), hãy đóng gói thành starter nội bộ với auto-configuration thay vì copy `@Configuration`.

**Câu hỏi kiểm tra**
- **H:** Vì sao auto-configuration luôn "nhường" bean do người dùng khai báo?
  **Đ:** Auto-configuration được nạp qua `DeferredImportSelector`, xử lý sau mọi cấu hình người dùng; `@ConditionalOnMissingBean` lúc đó thấy bean người dùng và bỏ qua.
- **H:** `proxyBeanMethods = false` ảnh hưởng gì, và khi nào nó gây bug?
  **Đ:** Không có CGLIB subclass; gọi method `@Bean` trực tiếp tạo object mới ngoài container. Bug xảy ra khi code dựa vào việc gọi chéo trả về singleton.
- **H:** Cần tạo N bean `RestClient` theo danh sách tenant trong config. Chọn cách nào?
  **Đ:** Framework 7: `BeanRegistrar` đọc `Environment` và đăng ký từng bean. Cách cũ: `ImportBeanDefinitionRegistrar` hoặc `BeanDefinitionRegistryPostProcessor`. Cân nhắc thêm một bean factory/registry duy nhất (`Map<String, RestClient>`) nếu không cần mỗi client là một bean.
- **H:** `@Profile` là tính năng của Boot hay Framework?
  **Đ:** Framework (`ProfileCondition`). Boot chỉ thêm cách kích hoạt (property, env var) và file `application-{profile}.yml`.

### 3. Scope, injection và dependency resolution

**Tóm tắt**
- Chọn bean cho một injection point: lọc theo **type** (tính cả generics) và `@Qualifier` → nếu còn nhiều: `@Primary` → bean duy nhất không phải `@Fallback` (Framework 6.2+) → tên tham số/field trùng tên bean → `@Priority` → không chọn được thì `NoUniqueBeanDefinitionException`.
- Inject `List<T>` / `T[]` / `ObjectProvider<T>.orderedStream()` được sắp theo `Ordered` / `@Order` / `@Priority`. `Map<String, T>` **không** được sắp theo `@Order`: key là tên bean, thứ tự là thứ tự đăng ký.
- Bean scope ngắn hơn (prototype, request, session) inject vào singleton sẽ bị "đóng băng" ở instance đầu tiên, trừ khi dùng `ObjectProvider`, `@Lookup` hoặc scoped proxy.
- Circular dependency bị Boot cấm mặc định từ 2.6 (`spring.main.allow-circular-references=false`). Coi cycle là dấu hiệu thiết kế sai, refactor thay vì bật cờ.

**Cơ chế bên dưới**

`DefaultListableBeanFactory.doResolveDependency()` (Framework 7.0) làm theo thứ tự:
1. Giá trị `@Value` (placeholder/SpEL) nếu có.
2. **Shortcut theo tên**: nếu có bean trùng tên tham số (hoặc trùng giá trị `@Qualifier`), đúng type, không phải `@Fallback` và không xung đột với một bean `@Primary` khác → chọn ngay.
3. Nếu injection point là `List`/`Set`/array/`Map`/`Stream` → gom mọi candidate.
4. Ngược lại, `findAutowireCandidates()` lọc theo type + qualifier + `autowireCandidate`. Một candidate → xong. Nhiều → `determineAutowireCandidate()`: `@Primary` → duy nhất non-`@Fallback` → match tên → `@Priority` cao nhất → bean `defaultCandidate` duy nhất.

Match theo tên tham số cần compile với `-parameters`. `spring-boot-starter-parent` đã bật sẵn; nếu tự cấu hình Maven/Gradle mà thiếu cờ này, injection theo tên sẽ âm thầm không hoạt động (Framework 6.1 đã bỏ việc đọc tên từ debug info).

```java
@Bean @Primary PaymentGateway stripeGateway() { ... }
@Bean @Fallback PaymentGateway noopGateway() { ... }       // chỉ dùng khi không còn ứng viên nào khác
@Bean(defaultCandidate = false) PaymentGateway auditGateway() { ... } // chỉ inject khi có @Qualifier("auditGateway")

// Generics làm qualifier ngầm: không cần @Qualifier
@Bean Repository<Order> orderRepo() { ... }
@Bean Repository<Invoice> invoiceRepo() { ... }
OrderService(Repository<Order> repo) { ... } // chọn orderRepo theo generic type
```

`@Primary` vs `@Fallback`: `@Primary` nói "chọn tôi", `@Fallback` nói "đừng chọn tôi nếu có bean khác". `@Fallback` hợp với bean mặc định trong thư viện/starter nội bộ mà không muốn tranh `@Primary` với ứng dụng.

Prototype/short-lived scope trong singleton:
```java
@Service
class ReportService {
    private final ObjectProvider<ReportBuilder> builders; // ReportBuilder là @Scope("prototype")
    ReportService(ObjectProvider<ReportBuilder> builders) { this.builders = builders; }
    Report build() { return builders.getObject().build(); } // mỗi lần gọi là instance mới
}
```
- `ObjectProvider<T>`: rõ ràng, test dễ, còn có `getIfAvailable()`, `getIfUnique()`, `orderedStream()`. Là lựa chọn mặc định.
- `@Lookup` trên method abstract/stub: container override method bằng CGLIB. Không dùng được cho bean tạo bằng `@Bean` factory method.
- Scoped proxy: inject một proxy cố định, mỗi lời gọi method tra instance thật theo scope hiện tại. `@RequestScope` / `@SessionScope` mặc định đã là `ScopedProxyMode.TARGET_CLASS`; `@Scope("prototype")` mặc định không có proxy.

`@Lazy` trên injection point: Spring inject một proxy, bean thật chỉ được tạo ở lời gọi đầu tiên. `spring.main.lazy-initialization=true` (Boot) làm mọi bean lazy: startup nhanh hơn nhưng lỗi cấu hình bị đẩy tới request đầu tiên.

Circular dependency: với constructor injection, cycle không thể giải (không có object nào tạo được trước). Với field/setter, container có thể trả "early reference" (qua `getEarlyBeanReference`, có thể là proxy sớm). Boot cấm cả hai mặc định, lỗi `The dependencies of some of the beans in the application context form a cycle`.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@Primary` | Một implementation rõ ràng là mặc định | Chỉ một `@Primary` mỗi type; khó override từ test/thư viện |
| `@Fallback` | Bean mặc định trong thư viện, ứng dụng có thể thay | Chỉ có từ Framework 6.2 |
| `@Qualifier` / custom qualifier annotation | Nhiều implementation cùng được dùng có chủ đích | Chuỗi tên dễ sai chính tả; custom annotation an toàn hơn |
| `List<T>` / `Map<String, T>` (strategy pattern) | Chọn implementation lúc runtime theo key | `Map` không theo `@Order`; key phụ thuộc tên bean |
| `ObjectProvider` | Prototype, optional dependency, lazy | Lookup mỗi lần gọi |
| Scoped proxy | Request/session state dùng ở singleton | Gọi ngoài request thread → `ScopeNotActiveException` (nguyên nhân: `IllegalStateException: No thread-bound request found`) |
| `@Lazy` để phá cycle | Tạm thời, khi chưa kịp refactor | Che giấu thiết kế sai; lỗi xuất hiện muộn |

**Pitfalls trong production**
- `required a single bean, but 2 were found` sau khi thêm dependency mới → thư viện đăng ký thêm bean cùng type (thường là `ObjectMapper`, `DataSource`, `Executor`) → khai báo `@Primary`/`@Qualifier` rõ ràng; với executor dùng tên bean chuẩn.
- Chiến lược chạy sai thứ tự → dùng `Map<String, T>` và kỳ vọng thứ tự `@Order` → dùng `List<T>` hoặc `orderedStream()`.
- Dữ liệu user A lộ sang user B → bean prototype/request inject thẳng vào singleton, hoặc singleton giữ state mutable → dùng scoped proxy/`ObjectProvider`, giữ singleton stateless.
- `@Async`/virtual thread truy cập request-scoped bean lỗi → request attributes gắn với thread gốc → truyền giá trị cần thiết dưới dạng tham số, không truyền bean scoped.
- Cycle `OrderService ↔ CustomerService` → tách phần dùng chung ra service thứ ba, hoặc dùng event (xem mục 5) để đảo chiều phụ thuộc.

**Liên hệ với demo**
Demo dùng constructor injection với một implementation mỗi type nên không cần qualifier. Khi thêm interface có nhiều implementation (ví dụ nhiều `PriceCalculator`), nên inject `List`/`Map` thay vì `if/else` theo type.

**Câu hỏi kiểm tra**
- **H:** Có hai bean `DataSource` tên `primaryDs` và `reportDs`, không có `@Primary`. Constructor `Repo(DataSource reportDs)` inject bean nào?
  **Đ:** `reportDs`: match theo tên tham số (cần `-parameters`). Nếu `primaryDs` có `@Primary` thì Spring chọn `primaryDs` (`@Primary` được xét trước tên tham số); muốn chắc chắn thì dùng `@Qualifier("reportDs")`.
- **H:** Vì sao inject bean prototype vào singleton "không hoạt động"?
  **Đ:** Injection chỉ xảy ra một lần khi tạo singleton. Muốn instance mới mỗi lần phải lookup lại (`ObjectProvider.getObject()`, `@Lookup`) hoặc dùng scoped proxy.
- **H:** `@Fallback` khác gì `@Primary` đặt ở bean còn lại?
  **Đ:** Kết quả tương tự khi có đúng 2 bean, nhưng `@Fallback` đặt ở phía thư viện nên ứng dụng không cần biết; ứng dụng vẫn có thể dùng `@Primary` của riêng mình mà không xung đột.
- **H:** Làm sao phá circular dependency mà không bật `allow-circular-references`?
  **Đ:** Tách trách nhiệm chung ra bean thứ ba; đảo chiều phụ thuộc bằng interface hoặc event; nếu thật sự cần, inject `ObjectProvider`/`@Lazy` ở một phía và ghi chú là nợ kỹ thuật.

### 4. AOP và proxy

**Tóm tắt**
- Spring AOP là **proxy-based**: chỉ lời gọi đi *từ ngoài vào qua proxy* mới bị intercept. `@Transactional`, `@Cacheable`, `@Async`, `@Retryable`, `@PreAuthorize`, `@Validated` đều chịu chung giới hạn này.
- Hai loại proxy: JDK dynamic proxy (implement interface) và CGLIB (subclass). Boot mặc định `spring.aop.proxy-target-class=true`, tức dùng CGLIB kể cả khi bean có interface.
- Self-invocation (`this.method()`), method `private`, `final`, `static` không bị intercept, và **không có lỗi hay cảnh báo nào**. Đây là nguồn bug phổ biến nhất khi review code.
- Khi nhiều annotation trên cùng method, thứ tự advisor quyết định ngữ nghĩa (retry bên ngoài hay bên trong transaction?). Mặc định: `@Async` ngoài cùng, rồi `@Retryable`/`@ConcurrencyLimit` (Framework 7), rồi `@Transactional`/`@Cacheable`.
- AspectJ weaving (compile-time hoặc load-time) bỏ được giới hạn proxy nhưng tăng độ phức tạp build/deploy. Hiếm khi đáng.

**Cơ chế bên dưới**

Proxy được tạo trong `postProcessAfterInitialization` bởi auto-proxy creator (xem mục 1). Boot đăng ký qua `AopAutoConfiguration`: có AspectJ weaver trên classpath thì bật `@EnableAspectJAutoProxy(proxyTargetClass = true)`; không có thì vẫn đăng ký auto-proxy creator hạ tầng với class-based proxy. Muốn dùng JDK proxy: `spring.aop.proxy-target-class=false`.

| | JDK dynamic proxy | CGLIB |
|---|---|---|
| Yêu cầu | Bean implement interface | Class không `final`, có thể subclass |
| Inject theo class cụ thể | Lỗi (proxy chỉ là interface) | Được |
| Method intercept được | Method public của interface | public, protected, package-visible; không `final`/`private`/`static` |
| Constructor | Không liên quan | Dùng Objenesis, constructor của class không bị gọi lần hai |

Với `@Transactional`: từ Framework 6.0, method `protected`/package-visible cũng được áp transaction với class-based proxy; với JDK proxy phải là public method trên interface.

Framework 7.0 áp dụng nhất quán cấu hình proxy mặc định toàn cục (ví dụ CGLIB như Boot) cho **mọi** proxy processor, kể cả `@Async` và các annotation tương tự; trước đó các processor này có thể tạo JDK proxy riêng. Muốn ngoại lệ cho một bean: `@Proxyable(ProxyType.INTERFACES)` hoặc `@Proxyable(ProxyType.TARGET_CLASS)` trên `@Bean` method hoặc class `@Component` (`org.springframework.context.annotation`, từ 7.0).

Self-invocation và các cách khắc phục:
```java
@Service
class OrderService {
    public void importAll(List<Order> orders) {
        orders.forEach(this::importOne);   // gọi qua this: KHÔNG có transaction riêng
    }
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void importOne(Order o) { ... }
}
```
1. **Tách bean** (khuyến nghị): chuyển `importOne` sang `OrderImporter`, `OrderService` inject và gọi qua proxy.
2. **Self-inject**: inject `ObjectProvider<OrderService>` hoặc `@Lazy OrderService self` rồi gọi `self.importOne(o)`. Chạy được nhưng khó đọc, dễ bị "dọn dẹp" nhầm.
3. **`AopContext.currentProxy()`**: cần `@EnableAspectJAutoProxy(exposeProxy = true)`; code gắn chặt vào Spring AOP, dựa trên ThreadLocal của thread gọi, nên không dùng được bên trong method chạy bởi `@Async` (method chạy trên thread của executor, `currentProxy()` ném `IllegalStateException`). Tránh.
4. **Programmatic**: `TransactionTemplate` cho đúng đoạn cần transaction. Rõ ràng, không phụ thuộc proxy.

Thứ tự advisor khi kết hợp annotation:
- `@Transactional` và `@Cacheable` do auto-proxy creator áp dụng; cả hai mặc định `order = Ordered.LOWEST_PRECEDENCE` (`@EnableTransactionManagement`, `@EnableCaching`). Khi order bằng nhau, thứ tự **không được đảm bảo**.
- `@Async` (`AsyncAnnotationBeanPostProcessor`), `@Retryable` và `@ConcurrencyLimit` (`RetryAnnotationBeanPostProcessor`, `ConcurrencyLimitBeanPostProcessor`) thêm advisor vào proxy có sẵn ở **vị trí đầu** (`beforeExistingAdvisors = true`). `@EnableResilientMethods` mặc định `order = LOWEST_PRECEDENCE - 1` để chạy trước `@EnableAsync`, nên kết quả là: `@Async` → `@Retryable` → (`@Transactional`, `@Cacheable`) → method.
- Hệ quả tốt: mỗi lần retry là một transaction mới. Nếu retry nằm trong transaction, lần thử lại chạy trên transaction đã bị đánh dấu rollback-only và vô nghĩa.
- Muốn cache hit không mở transaction: đặt `@EnableCaching(order = ...)` nhỏ hơn order của transaction (ví dụ `@EnableTransactionManagement(order = Ordered.LOWEST_PRECEDENCE)` và `@EnableCaching(order = Ordered.LOWEST_PRECEDENCE - 10)`). Với aspect tự viết, dùng `@Order` trên class `@Aspect`: số nhỏ hơn là bên ngoài.

Boot 4 đổi tên `spring-boot-starter-aop` thành `spring-boot-starter-aspectj`. Chỉ cần starter này khi thật sự dùng annotation `org.aspectj.lang.annotation.*` (`@Aspect`); `@Transactional`/`@Cacheable` không cần. Boot 4 cũng bỏ dependency management cho Spring Retry; khuyến nghị chuyển sang retry trong Framework 7 (xem mục 7).

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Spring AOP (proxy, mặc định) | Gần như mọi trường hợp: tx, cache, security, logging, metrics | Không intercept self-invocation, `final`, `private`; mỗi lời gọi qua chuỗi interceptor |
| AspectJ compile-time weaving | Cần intercept self-invocation/field access/constructor, hiệu năng cao | Plugin build riêng (ajc), xung đột với Lombok/record processor, IDE hỗ trợ kém |
| AspectJ load-time weaving | Không đổi build, cần sức mạnh AspectJ | Java agent khi chạy, startup chậm, khó debug, khó chạy trên môi trường bị khóa |
| Không dùng AOP (decorator, `TransactionTemplate`, filter) | Logic cắt ngang chỉ ở vài chỗ | Code lặp nhưng tường minh, dễ test |

Về hiệu năng: chi phí mỗi lời gọi qua proxy là vài lớp interceptor (thường ở mức nano- đến micro-giây), không đáng kể so với I/O. Vấn đề thật là pointcut quá rộng (ví dụ `execution(* com.example..*(..))`) làm mọi bean bị proxy, tăng startup và stack trace, và aspect logging serialize tham số lớn.

**Pitfalls trong production**
- Transaction/cache "không chạy" → self-invocation, method `private`/`final`, hoặc class Kotlin mặc định `final` → tách bean, bỏ `final` (Kotlin dùng plugin `kotlin-spring`), kiểm tra bằng `AopUtils.isAopProxy(bean)`.
- `BeanNotOfRequiredTypeException ... but was actually of type 'jdk.proxy...'` → đã tắt `proxy-target-class` mà inject theo class cụ thể → inject theo interface hoặc để mặc định CGLIB.
- Retry gây ghi dữ liệu trùng → retry nằm trong transaction hoặc method không idempotent → đảm bảo thứ tự retry ngoài transaction, chỉ retry thao tác idempotent, giới hạn exception được retry.
- Cache chứa dữ liệu không có trong DB (transaction đã rollback) → cache interceptor nằm trong transaction nên `@CachePut` ghi cache trước khi commit → đặt cache ngoài transaction (chỉnh order) hoặc dùng cache manager transaction-aware (`TransactionAwareCacheManagerProxy` trong `spring-context-support`, hoãn put tới sau commit).
- Aspect tự viết chạy "lúc có lúc không" → hai aspect cùng order → khai báo `@Order` tường minh cho mọi aspect.

**Liên hệ với demo**
`ProductService` gọi `existsBySku` và `save` qua repository (bean khác) nên đều đi qua proxy. Nếu sau này thêm method nội bộ có `@Transactional(REQUIRES_NEW)` gọi bằng `this`, nó sẽ âm thầm không có hiệu lực.

**Câu hỏi kiểm tra**
- **H:** Method có cả `@Retryable` và `@Transactional`. Retry chạy trong hay ngoài transaction?
  **Đ:** Framework 7 mặc định: `RetryAnnotationBeanPostProcessor` chèn advisor lên đầu chuỗi, nên retry ngoài transaction; mỗi lần thử là một transaction mới. Với Spring Retry cũ hoặc aspect tự viết, phải kiểm tra order.
- **H:** Vì sao Boot mặc định dùng CGLIB?
  **Đ:** Tránh lỗi khi inject theo class cụ thể và hành vi khác nhau giữa bean có/không có interface. Cái giá là không proxy được class/method `final`.
- **H:** Nêu 3 cách xử lý self-invocation và cách bạn chọn khi review.
  **Đ:** Tách bean (chọn mặc định), self-inject qua `ObjectProvider`/`@Lazy`, `AopContext.currentProxy()` (tránh). Với transaction còn có `TransactionTemplate`.
- **H:** Khi nào đáng dùng AspectJ weaving?
  **Đ:** Khi cần intercept thứ proxy không làm được (self-invocation diện rộng, domain object không phải bean, constructor) và team chấp nhận build phức tạp hơn. Hầu hết hệ thống business không cần.

### 5. Application events

**Tóm tắt**
- `ApplicationEventPublisher.publishEvent()` mặc định **đồng bộ**: listener `@EventListener` chạy ngay trên thread của người publish, trong cùng transaction. Exception của listener ném ngược về người publish và có thể rollback transaction của họ.
- Event trong Spring là cơ chế **in-process, không bền vững**: JVM crash hoặc listener lỗi thì event mất. Không phải message broker.
- `@TransactionalEventListener` hoãn listener tới một phase của transaction (mặc định `AFTER_COMMIT`). Không có transaction thì event bị **bỏ qua** trừ khi `fallbackExecution = true`.
- Ghi DB trong listener `AFTER_COMMIT` phải chạy trong transaction mới (`REQUIRES_NEW`). Từ Framework 6.1, gắn `@Transactional` thường (REQUIRED) lên `@TransactionalEventListener` bị từ chối ngay lúc startup (Framework 7.0 miễn kiểm tra cho phase `BEFORE_COMMIT`).
- Khi event phải "chắc chắn tới nơi": dùng transactional outbox, hoặc Event Publication Registry của Spring Modulith.

**Cơ chế bên dưới**

`AbstractApplicationContext.publishEvent()` chuyển cho `ApplicationEventMulticaster` (mặc định `SimpleApplicationEventMulticaster`). Multicaster không có executor nên gọi lần lượt từng listener theo `@Order`. Event có thể là object bất kỳ (được bọc thành `PayloadApplicationEvent`); listener chọn theo kiểu tham số, kể cả generic, và có thể lọc bằng SpEL `condition`.

```java
record OrderPlaced(long orderId, BigDecimal total) {}

@Service
class OrderService {
    private final ApplicationEventPublisher events;
    OrderService(ApplicationEventPublisher events) { this.events = events; }

    @Transactional
    public Order place(PlaceOrder cmd) {
        Order order = repo.save(Order.from(cmd));
        events.publishEvent(new OrderPlaced(order.getId(), order.getTotal()));
        return order;          // listener AFTER_COMMIT chỉ chạy nếu commit thành công
    }
}

@Component
class NotificationListener {
    @TransactionalEventListener   // phase = AFTER_COMMIT
    void on(OrderPlaced e) { mailClient.sendConfirmation(e.orderId()); }
}
```

Các phase của `@TransactionalEventListener` (`TransactionPhase`):

| Phase | Khi nào chạy | Dùng cho |
|---|---|---|
| `BEFORE_COMMIT` | Trước commit, vẫn trong transaction | Kiểm tra/ghi thêm cần nằm chung transaction |
| `AFTER_COMMIT` (mặc định) | Sau commit thành công | Gửi mail, gọi hệ thống ngoài, publish message |
| `AFTER_ROLLBACK` | Sau rollback | Ghi nhận thất bại, bù trừ |
| `AFTER_COMPLETION` | Sau commit hoặc rollback | Cleanup |

Cơ chế: listener được đăng ký như một `TransactionSynchronization` của transaction hiện tại. Ở các phase `AFTER_*`, transaction đã commit/rollback nhưng resource (connection) có thể vẫn còn gắn với thread. Javadoc cảnh báo: code truy cập dữ liệu lúc này vẫn "tham gia" transaction cũ nhưng thay đổi **không được commit**. Vì vậy ghi DB ở đây phải là `@Transactional(propagation = REQUIRES_NEW)`. Framework 6.1+ (`RestrictedTransactionalEventListenerFactory`, được đăng ký cùng `@EnableTransactionManagement`) ném `IllegalStateException` lúc startup nếu `@TransactionalEventListener` có `@Transactional` (trên method hoặc class) với propagation khác `REQUIRES_NEW` hoặc `NOT_SUPPORTED`. Framework 7.0 chỉ kiểm tra các phase `AFTER_*`; phase `BEFORE_COMMIT` được phép dùng propagation bất kỳ (gh-35150).

`@Async` + `@EventListener`: listener chạy trên executor, người publish không chờ, exception không về người publish (đi vào `AsyncUncaughtExceptionHandler`). Framework 6.2 bổ sung thuộc tính `defaultExecution` cho `@EventListener`; `fallbackExecution` của `@TransactionalEventListener` là alias của nó.

Spring Modulith (2.x cho Boot 4) cung cấp `@ApplicationModuleListener`, tương đương `@Async` + `@Transactional(propagation = REQUIRES_NEW)` + `@TransactionalEventListener`. Event Publication Registry ghi một bản ghi cho mỗi transactional listener vào event publication log **trong cùng transaction nghiệp vụ**; listener chạy thành công thì bản ghi được đánh dấu hoàn tất, lỗi thì giữ nguyên để resubmit. Chi tiết store, trạng thái publication, republish, `completion-mode` và externalization ra broker: xem mục 14.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@EventListener` đồng bộ | Tách module trong cùng transaction, cần all-or-nothing | Listener chậm làm chậm request; lỗi listener rollback người publish |
| `@TransactionalEventListener(AFTER_COMMIT)` | Side effect chỉ nên xảy ra khi dữ liệu đã commit | Mất event nếu crash sau commit, trước khi listener xong |
| `@Async` + transactional listener | Side effect chậm, không muốn giữ request | Mất event khi crash/lỗi; cần executor có giới hạn |
| Spring Modulith registry / outbox tự làm | Event quan trọng (thanh toán, tích hợp hệ thống khác) | Thêm bảng, job dọn dẹp, listener phải idempotent (at-least-once) |
| Message broker (Kafka, RabbitMQ) | Giao tiếp giữa các service | Hạ tầng, vận hành, vẫn cần outbox để tránh dual-write |

**Pitfalls trong production**
- Listener không bao giờ chạy → publish ngoài transaction (ví dụ từ method không có `@Transactional`, hoặc bị self-invocation) với `@TransactionalEventListener` → bật `fallbackExecution` nếu hợp lý, hoặc đảm bảo publish trong transaction.
- Dữ liệu listener ghi "biến mất" → ghi DB ở `AFTER_COMMIT` không có transaction mới → `@Transactional(propagation = REQUIRES_NEW)`.
- Startup lỗi `@TransactionalEventListener method must not be annotated with @Transactional unless when declared as REQUIRES_NEW or NOT_SUPPORTED` → đổi propagation.
- Mail gửi cho đơn hàng đã rollback → dùng `@EventListener` thường cho side effect ra ngoài → chuyển sang `AFTER_COMMIT`.
- Event mất sau khi deploy/restart → `@Async` listener đang chạy khi pod bị kill → registry/outbox + listener idempotent; cấu hình graceful shutdown cho executor.
- Bảng event publication phình to → completion mode `UPDATE` mà không purge → dùng `DELETE`/`ARCHIVE` hoặc job dọn định kỳ.

**Liên hệ với demo**
Demo không dùng event. Nếu thêm "gửi thông báo khi tạo sản phẩm", đặt publish trong `ProductService.create` (có transaction) và listener ở `AFTER_COMMIT`; nếu thông báo là bắt buộc, dùng outbox.

**Câu hỏi kiểm tra**
- **H:** `@EventListener` ném exception. Chuyện gì xảy ra với transaction của người publish?
  **Đ:** Listener đồng bộ chạy trong cùng thread/transaction; exception lan về `publishEvent()` và (nếu không bắt) làm transaction rollback.
- **H:** Vì sao `@TransactionalEventListener(AFTER_COMMIT)` + `@Async` vẫn không đảm bảo giao hàng?
  **Đ:** Event chỉ nằm trong bộ nhớ. Crash sau commit, hoặc listener lỗi, thì không còn dấu vết để retry. Cần lưu ý định gửi vào DB cùng transaction (outbox/registry).
- **H:** Event Publication Registry cho đảm bảo gì, và đòi hỏi gì ở listener?
  **Đ:** At-least-once: bản ghi được lưu cùng transaction nghiệp vụ, chỉ đánh dấu hoàn tất khi listener thành công, có thể resubmit. Listener phải idempotent vì có thể chạy lại.
- **H:** Khi nào nên dùng event nội bộ thay vì gọi service trực tiếp?
  **Đ:** Khi muốn đảo chiều phụ thuộc giữa module (module nguồn không cần biết ai quan tâm) và side effect có thể tách khỏi luồng chính. Không dùng event để che một lời gọi đồng bộ bắt buộc; luồng sẽ khó theo dõi hơn.

### 6. Environment và PropertySource ở tầng Framework

**Tóm tắt**
- `Environment` = danh sách **có thứ tự** các `PropertySource` + thông tin profile. Tra cứu property là đi từ đầu danh sách, gặp nguồn nào có key thì dừng. Mọi quy tắc precedence của Boot chỉ là việc Boot sắp xếp danh sách này.
- `${...}` là **placeholder** (tra `Environment`), `#{...}` là **SpEL** (biểu thức chạy lúc tạo bean). Hai thứ khác nhau hoàn toàn.
- Placeholder trong `@Value` được giải bởi `PropertySourcesPlaceholderConfigurer` (một `BeanFactoryPostProcessor`); Boot tự đăng ký nó bằng `static @Bean`.
- `@Value` hợp cho một vài giá trị lẻ; nhóm cấu hình của một component nên dùng `@ConfigurationProperties` (type-safe, validate, metadata, relaxed binding đầy đủ; xem mục 18).
- `Resource` trừu tượng hóa nguồn file (`classpath:`, `classpath*:`, `file:`, URL). Trong fat jar, resource classpath **không phải file**: đọc bằng `getInputStream()`, không bằng `getFile()`.

**Cơ chế bên dưới**

`StandardEnvironment` có sẵn 2 nguồn: `systemProperties` (JVM `-D`) rồi `systemEnvironment` (biến môi trường OS). Boot chèn thêm command-line args, `application*.yml` (config data), `random.*`, default properties... vào đúng vị trí để tạo precedence đã biết (command-line > system props > env > profile-specific > `application.yml`).

`@PropertySource` trên `@Configuration` là tính năng của Framework, nhưng trong Boot nó có độ ưu tiên **rất thấp** (chỉ trên default properties) và chỉ được thêm khi context refresh. Vì vậy nó quá muộn cho `logging.*`, `spring.main.*`. Nó cũng không nạp được YAML nếu không viết `PropertySourceFactory` riêng.

```java
@Component
class PricingPolicy {
    PricingPolicy(
        @Value("${pricing.max-discount:0.3}") BigDecimal maxDiscount,     // placeholder + default
        @Value("#{T(java.time.Duration).parse('${pricing.ttl:PT5M}')}") Duration ttl, // SpEL bọc placeholder
        Environment env) {
        boolean beta = env.matchesProfiles("beta & !prod");             // biểu thức profile
        String region = env.getRequiredProperty("app.region");          // fail rõ ràng nếu thiếu
    }
}
```
- `${key:default}` tách ở dấu `:` đầu tiên. Thiếu key và không có default → startup lỗi `Could not resolve placeholder 'key' in value "${key}"`.
- Từ Framework 6.2, placeholder hỗ trợ escape bằng `\`: `\${name}` cho ra chuỗi `${name}` nguyên văn. Có thể đổi/tắt ký tự escape mặc định qua property `spring.placeholder.escapeCharacter.default` (từ 6.2.7; đặt bằng JVM system property hoặc file `spring.properties`).
- Với `@Value`, dùng tên canonical dạng kebab-case (`${demo.item-price}`): Boot khi đó áp dụng relaxed binding giống `@ConfigurationProperties` (nhận `demo.itemPrice`, `DEMO_ITEMPRICE`). Viết `${demo.itemPrice}` sẽ không nhận `demo.item-price`.
- `Environment.matchesProfiles(...)` (từ 5.3.28) nhận biểu thức; `acceptsProfiles(String...)` đã deprecated.

So sánh 3 cách đọc cấu hình:

| | `@Value` | `Environment` | `@ConfigurationProperties` |
|---|---|---|---|
| Kiểu dữ liệu | Convert đơn giản | Tự convert từng lần gọi | Type-safe, nested, `List`/`Map`, `Duration`, `DataSize` |
| Relaxed binding | Giới hạn | Không (ngoài quy tắc env var của Boot) | Đầy đủ |
| Validation | Không | Không | `@Validated` + Bean Validation |
| Metadata cho IDE | Không | Không | Có (configuration processor) |
| SpEL | Có | Không | Không |
| Hợp với | 1–2 giá trị lẻ | Logic động (tên key tính lúc runtime), trong `Condition`/registrar | Nhóm cấu hình của một component/module |

SpEL ở đâu thì hợp lý: điều kiện `@EventListener(condition = "#event.amount > 1000")`, key của `@Cacheable(key = "#id")`, `@PreAuthorize`, biểu thức `@Scheduled` đơn giản. Không hợp lý: logic nghiệp vụ trong annotation, gọi bean `#{@someService.compute()}` để lấy cấu hình, biểu thức dài không test được. SpEL lỗi chỉ lộ ra lúc runtime, IDE hỗ trợ hạn chế, và khi nhận input người dùng thì có nguy cơ injection (không bao giờ parse SpEL từ dữ liệu bên ngoài với `StandardEvaluationContext`).

`Resource`:
- `classpath:config/rules.json`: một resource trên classpath.
- `classpath*:META-INF/rules/*.json`: gom mọi resource khớp trên **tất cả** jar (dùng cho plugin/module).
- `file:/etc/app/rules.json`: filesystem. Không prefix thì tùy loại `ResourceLoader` (web context có thể hiểu là tương đối).
- Inject trực tiếp: `@Value("classpath:rules.json") Resource rules`.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@Value` | Giá trị lẻ, cần default ngay tại chỗ | Rải rác, khó tìm tất cả key; không validate |
| `@ConfigurationProperties` | Mặc định cho cấu hình có cấu trúc | Thêm class; cần đăng ký (`@EnableConfigurationProperties`/scan) |
| `Environment` trực tiếp | Key động, code hạ tầng (condition, registrar) | Dễ lan ra code nghiệp vụ, khó test |
| `@PropertySource` | Thư viện không-Boot cần file properties riêng | Ưu tiên thấp trong Boot, không nạp YAML, nạp muộn |
| `spring.config.import` (Boot) | Nạp thêm file/nguồn cấu hình trong ứng dụng Boot | Chỉ trong Boot |

**Pitfalls trong production**
- Giá trị trong `application-prod.yml` không có hiệu lực → cùng key đặt qua `@PropertySource` hoặc được override bởi env var trên server → dùng `/actuator/env` (nếu bật và bảo vệ) hoặc log để biết nguồn thắng; bỏ `@PropertySource`, dùng config data.
- Mật khẩu chứa `\${` bị đổi sau khi nâng lên Framework 6.2+ → ký tự escape mặc định `\` → kiểm tra các giá trị chứa `\$`, hoặc đổi ký tự escape qua `spring.placeholder.escapeCharacter.default`.
- `FileNotFoundException ... does not reside in the file system: jar:...` khi chạy fat jar nhưng chạy tốt trong IDE → gọi `resource.getFile()` → dùng `getInputStream()`.
- Bean có `@Value` nhận chuỗi `${...}` nguyên văn → context không có `PropertySourcesPlaceholderConfigurer` (thường gặp trong test Framework thuần hoặc `@Bean` BFPP khai báo non-static) → khai báo `static @Bean PropertySourcesPlaceholderConfigurer`.
- Secret lộ trong log → log toàn bộ `Environment` hoặc exception chứa URL có mật khẩu → không log property thô; dùng cơ chế sanitize (ví dụ Actuator) và secret manager.

**Liên hệ với demo**
Demo dùng `${DB_URL:jdbc:h2:mem:demo}` (tách ở dấu `:` đầu tiên). Ở production, không nên để default trỏ tới DB thật trong file cấu hình; thiếu biến môi trường thì nên fail sớm (`getRequiredProperty` hoặc `@ConfigurationProperties` + `@Validated`).

**Câu hỏi kiểm tra**
- **H:** `${...}` và `#{...}` khác nhau thế nào? Ai xử lý mỗi loại?
  **Đ:** `${}` là placeholder tra `Environment`, do `PropertySourcesPlaceholderConfigurer`/embedded value resolver xử lý. `#{}` là SpEL, do `BeanExpressionResolver` đánh giá lúc tạo bean; SpEL có thể chứa placeholder bên trong.
- **H:** Vì sao `@PropertySource("classpath:app.yml")` không hoạt động và không nên dùng để set `logging.level`?
  **Đ:** Factory mặc định chỉ đọc định dạng properties; và `@PropertySource` được thêm khi refresh context, sau khi Boot đã đọc `logging.*`, `spring.main.*`. Dùng `spring.config.import`.
- **H:** Khi nào bạn chấp nhận `Environment` trong code nghiệp vụ?
  **Đ:** Hầu như không. Chấp nhận trong code hạ tầng (condition, `BeanRegistrar`, factory tạo bean theo key động). Code nghiệp vụ nên nhận `@ConfigurationProperties` đã bind và validate.

### 7. Những điểm mới đáng chú ý của Spring Framework 6.x–7

**Tóm tắt**
- Framework 7.0 (nền của Boot 4): baseline vẫn là JDK 17 (khuyến nghị JDK 25), Jakarta EE 11 (Servlet 6.1, JPA 3.2, Bean Validation 3.1), JUnit 6, Jackson 3. Không còn hỗ trợ `javax.annotation`/`javax.inject`; `spring-jcl` bị bỏ, Undertow bị bỏ, `ListenableFuture` bị xóa.
- Null-safety chuyển sang **JSpecify** (`@NullMarked`, `@Nullable` của `org.jspecify.annotations`). `org.springframework.lang.Nullable`/`NonNullApi` bị deprecated từ 7.0.
- **Resilience có sẵn trong core**: `RetryTemplate`/`RetryPolicy` (`org.springframework.core.retry`), `@Retryable`/`@ConcurrencyLimit`/`@EnableResilientMethods` (`org.springframework.resilience.annotation`). Không cần Spring Retry cho nhu cầu phổ biến.
- Web: API versioning built-in, HTTP service client registry (`@ImportHttpServices`), `HttpMessageConverters` builder mới. `RestTemplate`: Spring công bố kế hoạch deprecate, dự kiến `@Deprecated` ở 7.1, xóa ở 8.0; code mới dùng `RestClient`.
- Kiến trúc: `BeanRegistrar` (xem mục 2), proxy mặc định áp dụng nhất quán kể cả cho `@Async` và `@Proxyable` (xem mục 4), SpEL hỗ trợ `Optional`.
- Test: context pausing trong context cache, `SpringExtension` mặc định theo phạm vi test method, bean override dùng được cho non-singleton, `RestTestClient` mới, hỗ trợ JUnit 4 bị deprecated.

**Cơ chế bên dưới**

Lộ trình 6.x → 7, chỉ những điểm ảnh hưởng tới quyết định kiến trúc:

| Phiên bản | Điểm chính |
|---|---|
| 6.0 | Java 17, `jakarta.*`, AOT/GraalVM native, `@HttpExchange`, `ProblemDetail`, Micrometer Observation |
| 6.1 | `RestClient`, `JdbcClient`, hỗ trợ virtual threads, method validation built-in; bắt buộc `-parameters` (xem mục 3) |
| 6.2 | `@Fallback`, `@Bean(defaultCandidate = false)`, `@Bean(bootstrap = BACKGROUND)`, `@MockitoBean`/`@TestBean`, `MockMvcTester`, escape placeholder `\` (xem mục 6) |
| 7.0 | Jakarta EE 11, JSpecify, resilience, API versioning, HTTP service registry, `BeanRegistrar`, Jackson 3 |

Resilience trong core:
```java
@Configuration
@EnableResilientMethods          // bật xử lý @Retryable và @ConcurrencyLimit
class ResilienceConfig {}

@Service
class InventoryClient {
    @Retryable(includes = RestClientException.class, maxRetries = 4,
               delay = 200, multiplier = 2.0, maxDelay = 2000, jitter = 50)
    @ConcurrencyLimit(10)         // tối đa 10 lời gọi đồng thời, mặc định policy BLOCK
    public Stock fetch(String sku) { return rest.get().uri("/stock/{sku}", sku).retrieve().body(Stock.class); }
}

// Programmatic: cùng policy, không cần proxy
RetryPolicy policy = RetryPolicy.builder().maxRetries(4).delay(Duration.ofMillis(200))
        .multiplier(2.0).includes(RestClientException.class).build();
Stock s = new RetryTemplate(policy).invoke(() -> client.fetch("A-1")); // invoke(Supplier) từ 7.0.3
```
- Mặc định `@Retryable`: `maxRetries = 3`, `delay = 1000` ms, `multiplier = 1.0`, không jitter; có `timeout` từ 7.0.2 và `predicate` để lọc exception. Method trả kiểu reactive được retry bằng Reactor thay vì chặn thread.
- `@ConcurrencyLimit` policy `REJECT` (từ 7.0.3) ném `InvocationRejectedException` thay vì chờ. Giới hạn là **theo từng instance JVM**, không phải rate limit phân tán.
- Đây là annotation dựa trên proxy: chịu mọi giới hạn ở mục 4 (self-invocation, `final`). Thứ tự mặc định đặt retry **ngoài** `@Transactional`.
- `RetryTemplate.execute(Retryable)` ném checked `RetryException` chứa exception cuối; `invoke(...)` ném lại chính runtime exception cuối.
- Không thấy Boot auto-configure `@EnableResilientMethods`: phải khai báo tường minh. Boot 4 cũng bỏ dependency management cho Spring Retry.

API versioning: `@GetMapping(path = "/orders/{id}", version = "1.2")` (khớp đúng 1.2) hoặc `version = "1.2+"` (1.2 trở lên, dùng tiếp tới khi có method version cao hơn). Boot cấu hình qua `spring.mvc.apiversion.*` (ví dụ `use.header`, `use.path-segment`, `use.query-parameter`, `supported`, `default`, `required`) hoặc `spring.webflux.apiversion.*`; tùy biến sâu hơn bằng bean `ApiVersionResolver`, `ApiVersionParser`, `ApiVersionDeprecationHandler`.

HTTP service registry: khai báo interface `@HttpExchange`, rồi `@ImportHttpServices(group = "inventory", types = InventoryApi.class)` (hoặc `basePackages`) để Framework tạo proxy client cho cả nhóm; cấu hình client theo nhóm bằng `RestClientHttpServiceGroupConfigurer` hoặc `AbstractHttpServiceRegistrar`. Boot 4 auto-configure HTTP Service Clients. Thay cho việc tự tạo `HttpServiceProxyFactory` cho từng interface.

Jackson 3: package `tools.jackson.*` (annotation vẫn ở `com.fasterxml.jackson.annotation`). Không có tương đương `Jackson2ObjectMapperBuilder`; dùng `JsonMapper.builder()`. Hỗ trợ Jackson 2 bị deprecated, auto-detect sẽ tắt ở 7.1 và bị xóa ở 7.2. Converter cấu hình qua `WebMvcConfigurer#configureMessageConverters(HttpMessageConverters.ServerBuilder)`.

Thay đổi âm thầm dễ làm hỏng code khi nâng cấp: `HttpHeaders` không còn là `MultiValueMap`; trailing slash match và suffix pattern match đã bị xóa; `AntPathMatcher`/`PathMatcher` trong MVC deprecated (dùng `PathPattern`, nay hỗ trợ `/**` ở đầu pattern); SpEL mặc định giới hạn 10.000 operation mỗi biểu thức (từ 7.0.8, property `spring.expression.maxOperations`).

TestContext: `spring.test.context.cache.pause` = `ALWAYS` | `ON_CONTEXT_SWITCH` (mặc định từ 7.0.3) | `NEVER`. Context trong cache không dùng tới được pause (`SmartLifecycle` dừng scheduler, listener, consumer), nên các context cached không còn tranh message/job của nhau. Component nào không muốn bị pause thì trả `isPauseable() = false`.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@Retryable` / `@ConcurrencyLimit` của Framework 7 | Retry/bulkhead đơn giản trong một service | Không có circuit breaker, rate limit phân tán; phụ thuộc proxy |
| `RetryTemplate` | Retry một đoạn code cụ thể, cần listener hoặc không muốn proxy | Code tường minh hơn, dài hơn |
| Resilience4j | Cần circuit breaker, rate limiter, metrics chi tiết, cấu hình runtime | Thêm dependency và cấu hình; hai mô hình resilience nếu dùng lẫn |
| API versioning built-in | API public/đa client cần nhiều version song song | Chọn một chiến lược (header, path, query, media type) và giữ ổn định lâu dài |
| HTTP service registry | Nhiều client `@HttpExchange`, cần cấu hình theo nhóm | Ẩn chi tiết HTTP; lỗi mapping chỉ lộ lúc chạy nếu không có test |
| Ở lại Jackson 2 tạm thời | Thư viện nội bộ chưa sẵn sàng Jackson 3 | Deprecated; phải chuyển trước khi nâng lên 7.2 |

**Pitfalls trong production**
- Retry làm dịch vụ phía sau quá tải khi nó đang chậm → retry cố định không jitter, nhiều instance cùng retry → dùng `multiplier`, `maxDelay`, `jitter`; chỉ retry exception tạm thời qua `includes`; cân nhắc circuit breaker.
- `@Retryable` không có tác dụng → thiếu `@EnableResilientMethods`, hoặc self-invocation → khai báo annotation bật, gọi qua bean khác (xem mục 4).
- Upgrade xong, request `/orders/` trả 404 → trailing slash match đã tắt mặc định từ 6.0 và bị xóa hẳn ở 7.0 (ai từng bật lại bằng `setUseTrailingSlashMatch(true)` sẽ gặp khi lên 7) → chuẩn hóa URL ở client/gateway hoặc khai báo mapping tường minh.
- Code không compile hoặc behavior khác khi coi `HttpHeaders` là `Map` → `HttpHeaders` không còn implement `MultiValueMap` → dùng API của `HttpHeaders` (`get`, `add`, `set`...).
- JSON đổi format sau khi nâng Boot 4 → Jackson 3 thay đổi default và package; custom serializer/module Jackson 2 không còn được đăng ký → chuyển sang API `tools.jackson`, có test snapshot JSON cho API public.
- Biểu thức SpEL phức tạp lỗi sau 7.0.8 → vượt giới hạn 10.000 operation → đơn giản hóa biểu thức hoặc tăng `spring.expression.maxOperations`.

**Liên hệ với demo**
Demo chạy trên Boot 4.1.1 / Framework 7.0.x nên mặc định đã dùng Jackson 3, JUnit 6, `@MockitoBean`. Nếu thêm client gọi dịch vụ ngoài, dùng `RestClient` hoặc `@HttpExchange` + `@ImportHttpServices`, và `@Retryable` của Framework thay vì thêm Spring Retry.

**Câu hỏi kiểm tra**
- **H:** Team muốn thêm Spring Retry vào service Boot 4. Bạn khuyên gì?
  **Đ:** Dùng `@Retryable`/`RetryTemplate` trong Framework 7 (nhớ `@EnableResilientMethods`); Boot 4 không còn quản lý version Spring Retry. Nếu cần circuit breaker thì dùng Resilience4j, không phải Spring Retry.
- **H:** `@Retryable` + `@Transactional` trên cùng method: mỗi lần thử có transaction riêng không?
  **Đ:** Có, theo mặc định retry advisor nằm ngoài transaction (xem mục 4). Method vẫn phải idempotent.
- **H:** Vì sao context pausing quan trọng với test suite lớn?
  **Đ:** Context cached vẫn chạy scheduler, message listener; chúng có thể tiêu thụ message hoặc chạy job của test khác. Pause dừng các `SmartLifecycle` khi chuyển context và restart khi dùng lại.
- **H:** Code mới có nên dùng `RestTemplate` không?
  **Đ:** Không. Spring đã công bố kế hoạch deprecate `RestTemplate` (dự kiến `@Deprecated` ở 7.1, xóa ở 8.0). Dùng `RestClient` (API fluent, cùng hạ tầng converter/interceptor) hoặc HTTP interface.

Tài liệu cũ nói X — Framework 7 thực tế là Y:

| Tài liệu cũ nói X | Framework 7 / Boot 4 thực tế là Y |
|---|---|
| Retry cần thêm Spring Retry + `@EnableRetry` | Có sẵn `@Retryable`, `RetryTemplate` trong core; bật bằng `@EnableResilientMethods`; Boot 4 bỏ dependency management cho Spring Retry |
| `RestTemplate` là client HTTP chuẩn | Spring công bố kế hoạch deprecate, dự kiến `@Deprecated` ở 7.1, xóa ở 8.0; dùng `RestClient` hoặc `@HttpExchange` |
| Import `javax.annotation.PostConstruct`, `javax.inject.Inject` | Chỉ hỗ trợ `jakarta.*`; `javax.*` không còn được nhận |
| `@MockBean`, `@SpyBean` | Bị xóa trong Boot 4; dùng `@MockitoBean`, `@MockitoSpyBean` |
| Tùy biến JSON bằng `Jackson2ObjectMapperBuilder` | Jackson 3 (`tools.jackson`), dùng `JsonMapper.builder()`; Jackson 2 deprecated |
| Null-safety bằng `org.springframework.lang.Nullable` | JSpecify (`@NullMarked`, `org.jspecify.annotations.Nullable`) |
| `@Async` trên bean có interface tạo JDK proxy | Cấu hình proxy mặc định toàn cục (CGLIB trong Boot) áp dụng cho mọi processor, kể cả `@Async`; ngoại lệ bằng `@Proxyable` |
| `/orders` và `/orders/` cùng khớp một mapping | Tắt mặc định từ 6.0, bị xóa hẳn ở 7.0 |
| Muốn AOP thì thêm `spring-boot-starter-aop` | Đổi tên thành `spring-boot-starter-aspectj`; chỉ cần khi dùng `@Aspect` |
| Test với `@RunWith(SpringRunner.class)` | Hỗ trợ JUnit 4 deprecated; dùng JUnit Jupiter (JUnit 6) + `SpringExtension` |
| `HttpHeaders` là một `MultiValueMap` | Không còn kế thừa `MultiValueMap` |
| Versioning API tự làm bằng path/header thủ công | Built-in qua `version` trên `@RequestMapping` + `spring.mvc.apiversion.*` |

---

## Phần II — Data, transaction và tính nhất quán

### 8. Transaction management chuyên sâu

**Tóm tắt**
- `@Transactional` chỉ là metadata. Việc thật do `TransactionInterceptor` (một AOP advice, xem mục 4) làm: gọi `PlatformTransactionManager.getTransaction()` trước method, `commit()`/`rollback()` sau method. Với JPA, Boot cấu hình sẵn `JpaTransactionManager`.
- Transaction gắn với **thread**: connection/`EntityManager` và trạng thái transaction được giữ trong `ThreadLocal` qua `TransactionSynchronizationManager`. Code chạy trên thread khác (`@Async`, `CompletableFuture.supplyAsync`, parallel stream, virtual thread tự tạo) **không** nằm trong transaction của caller.
- Rollback mặc định: `RuntimeException` và `Error`. Checked exception **commit** (trừ khi khai báo `rollbackFor`). Từ Framework 6.2 có thể đổi toàn cục bằng `@EnableTransactionManagement(rollbackOn = RollbackOn.ALL_EXCEPTIONS)`.
- `readOnly = true` là tối ưu hóa và tín hiệu (Hibernate bỏ flush, connection read-only hint, routing sang replica), không phải cơ chế bảo mật.
- Transaction phải **ngắn**: không gọi HTTP/remote, không chờ người dùng, không xử lý file lớn bên trong. Transaction dài giữ connection của pool và giữ lock trong DB.

**Cơ chế bên dưới**

Luồng một lời gọi `@Transactional`:
1. Proxy gọi `TransactionInterceptor.invoke()` → `TransactionAspectSupport.invokeWithinTransaction()`.
2. Đọc `TransactionAttribute` (propagation, isolation, timeout, readOnly, rollback rules) từ annotation.
3. `PlatformTransactionManager.getTransaction(def)`: kiểm tra đã có transaction trên thread chưa, rồi áp propagation. Với `JpaTransactionManager`: tạo `EntityManager`, `begin()`, bind `EntityManagerHolder` và `ConnectionHolder` vào `TransactionSynchronizationManager` (key là `EntityManagerFactory`/`DataSource`).
4. Gọi method thật. Repository của Spring Data lấy `EntityManager` "shared" — một proxy tra `TransactionSynchronizationManager` để lấy đúng `EntityManager` của thread hiện tại.
5. Method ném exception → kiểm tra rollback rules → `rollback()` hoặc vẫn `commit()`. Method trả về bình thường → `commit()` (flush persistence context, rồi commit JDBC), chạy các callback `TransactionSynchronization.afterCommit()` (đây là nơi `@TransactionalEventListener(AFTER_COMMIT)` được kích hoạt, xem mục 5).

**7 propagation — khi nào dùng thật**

| Propagation | Hành vi | Khi nào dùng thật |
|---|---|---|
| `REQUIRED` (mặc định) | Tham gia transaction hiện có, không có thì tạo mới | 95% trường hợp |
| `REQUIRES_NEW` | Treo transaction hiện tại, mở transaction mới (connection mới) | Audit log / outbox phải được ghi dù nghiệp vụ rollback; ghi DB trong `AFTER_COMMIT` listener. Cẩn thận: cần 2 connection cùng lúc → dễ cạn pool, deadlock với chính mình |
| `SUPPORTS` | Có thì tham gia, không có thì chạy không transaction | Hiếm; method đọc dùng chung |
| `MANDATORY` | Bắt buộc đã có transaction, không có thì ném exception | Method cấp thấp chỉ được gọi từ service đã mở transaction; dùng để "khóa" thiết kế |
| `NOT_SUPPORTED` | Treo transaction hiện tại, chạy không transaction | Tác vụ dài không cần transaction (gọi remote, tạo report) giữa luồng có transaction |
| `NEVER` | Có transaction thì ném exception | Hiếm; bảo vệ code không được chạy trong transaction |
| `NESTED` | Savepoint JDBC trong transaction hiện có | Batch xử lý từng dòng bằng JDBC, lỗi dòng nào rollback về savepoint dòng đó. Với JPA: savepoint chỉ áp cho JDBC connection, **không** hoàn tác state trong persistence context → tránh dùng với entity. Từ Framework 7, `JpaTransactionManager` mặc định `nestedTransactionAllowed = false` (6.x bật `true` trong constructor) → `NESTED` ném `NestedTransactionNotSupportedException` nếu không tự bật |

**Isolation và anomaly**

| Isolation | Dirty read | Non-repeatable read | Phantom | Ghi chú |
|---|---|---|---|---|
| `READ_UNCOMMITTED` | Có thể | Có thể | Có thể | Gần như không dùng |
| `READ_COMMITTED` | Không | Có thể | Có thể | Mặc định của PostgreSQL, Oracle, SQL Server |
| `REPEATABLE_READ` | Không | Không | Có thể (theo chuẩn) | Mặc định của MySQL InnoDB; PostgreSQL cài đặt bằng snapshot |
| `SERIALIZABLE` | Không | Không | Không | DB có thể ném serialization failure → ứng dụng phải retry |

`Isolation.DEFAULT` nghĩa là dùng mặc định của DB. Lost update và write skew không được ngăn ở `READ_COMMITTED`; đừng trông vào isolation để giải quyết, dùng locking (xem mục 11). Isolation chỉ có hiệu lực khi method **mở** transaction mới; với method tham gia transaction đang có, isolation khai báo bị âm thầm bỏ qua (bật `validateExistingTransaction` trên transaction manager thì Spring ném `IllegalTransactionStateException` khi isolation/readOnly không khớp, thay vì bỏ qua).

**readOnly làm gì**
- Hibernate (qua `HibernateJpaDialect`): session chuyển `FlushMode.MANUAL` (không dirty checking, không flush) và `session.setDefaultReadOnly(true)` (entity load ra không giữ snapshot → tiết kiệm bộ nhớ).
- JDBC: `Connection.setReadOnly(true)` — driver/DB có thể tối ưu hoặc từ chối ghi (ví dụ driver PostgreSQL mở transaction ở chế độ read-only, lệnh ghi bị DB từ chối), nhưng không phải DB nào cũng chặn ghi.
- Routing datasource: `TransactionSynchronizationManager.isCurrentTransactionReadOnly()` là tín hiệu để chọn replica (xem mục 12).

**Programmatic transaction**
```java
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.support.TransactionTemplate;

@Service
class ImportService {
    private final TransactionTemplate tx;
    ImportService(PlatformTransactionManager tm) {
        this.tx = new TransactionTemplate(tm);
        this.tx.setTimeout(10); // giây
    }
    void importAll(List<Row> rows) {
        for (List<Row> chunk : chunks(rows, 500)) {
            tx.executeWithoutResult(status -> saveChunk(chunk)); // mỗi chunk một transaction
        }
    }
}
```
Dùng `TransactionTemplate` khi: ranh giới transaction nằm giữa method (chunk, vòng lặp), cần tránh self-invocation, hoặc cần gọi remote **ngoài** transaction rồi mới ghi DB trong cùng một method.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@Transactional` ở tầng service | Mặc định cho use case | Ranh giới = method; bị self-invocation, method private |
| `TransactionTemplate` | Chunk, ranh giới linh hoạt, tách phần gọi remote | Code dài hơn, dễ quên timeout |
| `@Transactional` trên repository/controller | Tránh: controller không nên sở hữu transaction | Transaction kéo dài qua serialization, gọi nhiều service |
| `rollbackOn = ALL_EXCEPTIONS` toàn cục | Codebase dùng nhiều checked exception | Thay đổi hành vi so với tài liệu phổ biến; phải thống nhất cả team |

**Pitfalls trong production**
- Pool cạn, p99 tăng vọt → transaction bao quanh lời gọi HTTP chậm (connection bị giữ suốt thời gian chờ) → tách: gọi remote trước/sau, chỉ bọc phần ghi DB; hoặc dùng outbox (xem mục 14).
- `UnexpectedRollbackException` lúc commit → method bên trong (REQUIRED) ném RuntimeException, transaction bị đánh dấu rollback-only, method ngoài catch rồi đi tiếp → đừng nuốt exception trong transaction; nếu thật sự muốn "thử và bỏ qua", đưa phần đó sang `REQUIRES_NEW` hoặc kiểm tra điều kiện trước.
- Checked exception nhưng dữ liệu vẫn được commit → quy tắc rollback mặc định → `rollbackFor = Exception.class` hoặc dùng unchecked exception cho lỗi nghiệp vụ.
- `@Async` method gọi từ method `@Transactional` không thấy dữ liệu chưa commit, hoặc `LazyInitializationException` → thread khác, transaction khác → truyền ID thay vì entity; kích hoạt xử lý async sau commit (`@TransactionalEventListener`).
- Deadlock giữa `REQUIRES_NEW` và transaction ngoài → transaction mới chờ lock dòng mà transaction ngoài (đang treo) đang giữ → không để transaction trong và ngoài đụng cùng dòng.
- `timeout` không ngắt code Java → timeout chỉ được áp ở tầng statement (Spring đặt query timeout theo thời gian còn lại, hoặc ném `TransactionTimedOutException` khi statement kế tiếp bắt đầu mà đã quá hạn); vòng lặp CPU hay HTTP call không bị cắt → đặt timeout ở cả HTTP client.

**Liên hệ với demo**
`ProductService.create()` dùng `@Transactional` mặc định và `getById()/getProducts()` dùng `readOnly = true` — đúng hướng. Nếu sau này `create()` cần gọi service khác (ví dụ kiểm tra tồn kho qua HTTP), gọi trước khi vào transaction.

**Câu hỏi kiểm tra**
- **H:** Service A (`@Transactional`) gọi `CompletableFuture.runAsync(() -> repo.save(x))`. Nếu A rollback thì `x` có bị rollback không?
  **Đ:** Không. Lambda chạy trên thread khác, không thấy transaction trong `ThreadLocal` của A; `save()` tự mở transaction riêng (từ `SimpleJpaRepository`) và commit độc lập.
- **H:** Khi nào `REQUIRES_NEW` gây cạn connection pool?
  **Đ:** Mỗi lớp `REQUIRES_NEW` giữ thêm một connection trong khi connection của transaction ngoài vẫn bị giữ. Với N request đồng thời và pool N, mọi request có thể giữ connection ngoài và chờ connection thứ hai → treo đến `connectionTimeout`.
- **H:** `@Transactional(isolation = SERIALIZABLE)` trên method được gọi từ method `@Transactional` mặc định có tác dụng không?
  **Đ:** Không; method trong tham gia transaction đã mở với isolation mặc định. Isolation chỉ áp dụng khi transaction được tạo mới.
- **H:** `readOnly = true` có ngăn được lệnh `UPDATE` không?
  **Đ:** Không chắc. Hibernate không flush thay đổi entity, nhưng `@Modifying` query hoặc JDBC vẫn gửi xuống; chặn hay không tùy driver/DB với connection read-only.

### 9. JPA/Hibernate cho kiến trúc sư

**Tóm tắt**
- **Persistence context** (`EntityManager`/`Session`) là một unit of work: first-level cache theo `(entity type, id)` và đảm bảo **identity**: trong một persistence context, cùng một dòng luôn là cùng một object Java. Với Spring, persistence context sống bằng transaction (trừ khi bật Open Session In View).
- Hibernate không "update khi gọi save()": nó **dirty checking** lúc flush — so state hiện tại với snapshot lúc load, sinh `UPDATE` cho entity managed đã thay đổi. Gọi `save()` cho entity managed là thừa.
- `save()` của Spring Data = `persist()` nếu entity "mới", ngược lại `merge()`. Định nghĩa "mới" dựa trên `@Version`/id — hiểu sai sẽ gây `SELECT` thừa hoặc lỗi.
- Bulk JPQL (`@Modifying` `UPDATE`/`DELETE`) đi thẳng xuống DB, **bỏ qua** persistence context → entity đã load trong context thành dữ liệu cũ.
- Không expose entity qua API; không dựa vào lazy loading ngoài transaction. Demo tắt `open-in-view` là đúng.

**Cơ chế bên dưới**

Vòng đời entity:

| State | Nghĩa | Chuyển sang |
|---|---|---|
| Transient | `new`, chưa gắn context, chưa có dòng DB | `persist()` → managed |
| Managed | Được context theo dõi, dirty checking áp dụng | Transaction kết thúc / `detach()` / `clear()` → detached; `remove()` → removed |
| Detached | Có id, có dòng DB, nhưng context không theo dõi | `merge()` → trả về **bản managed khác** (object truyền vào vẫn detached) |
| Removed | Đã đánh dấu xóa, `DELETE` khi flush | |

Flush (đẩy thay đổi xuống DB, chưa commit) xảy ra khi: commit; trước khi chạy query có thể bị ảnh hưởng bởi thay đổi đang chờ (FlushMode `AUTO`, mặc định); hoặc gọi `flush()` thủ công.
- `AUTO`: với JPQL/HQL, Hibernate flush nếu thay đổi đang chờ đụng tới bảng mà query dùng. Với native SQL, Hibernate không biết query đụng bảng nào; theo Hibernate 7 User Guide, native query chạy qua `EntityManager` (trường hợp của Spring Data JPA) luôn kích hoạt flush **toàn bộ** persistence context.
- `COMMIT`: chỉ flush lúc commit → query trong transaction có thể không thấy thay đổi của chính mình.
- `MANUAL`: chỉ flush khi gọi `flush()`; Spring dùng cho `readOnly = true` (xem mục 8).

Hệ quả thực tế: lỗi constraint (`unique`, `not null`) thường xuất hiện lúc **flush/commit**, không phải lúc `save()`. Nếu cần bắt lỗi ngay trong method (để trả 409), gọi `saveAndFlush()` hoặc `flush()`.

`save()` của Spring Data (`SimpleJpaRepository`) quyết định persist hay merge qua `JpaMetamodelEntityInformation.isNew()`:
1. Entity implement `Persistable` → dùng `isNew()` của nó.
2. Có thuộc tính `@Version` kiểu wrapper (`Long`, `Integer`) → mới khi version `null`.
3. Ngược lại → mới khi id `null` (hoặc `0` với id primitive).

Bẫy: id gán thủ công (UUID tự sinh trong constructor, mã nghiệp vụ) → id không `null` → `save()` gọi `merge()` → Hibernate `SELECT` trước để xem dòng có tồn tại không, rồi mới `INSERT`. Cách xử lý: thêm `@Version Long version` (null = mới) hoặc implement `Persistable`.

Hibernate 7 (đi cùng Boot 4) bỏ hẳn `Session#save`, `Session#update`, `Session#saveOrUpdate` và `org.hibernate.annotations.CascadeType.SAVE_UPDATE`; chỉ còn `persist`/`merge` theo chuẩn JPA. Persist/flush một entity có association `cascade = PERSIST/ALL` trỏ tới một instance **detached** giờ ném `EntityExistsException` → phải `merge()` hoặc `getReference()` trước khi gắn. Refresh hay lock entity detached cũng không còn được phép.

Bulk update:
```java
@Modifying(flushAutomatically = true, clearAutomatically = true)
@Query("update Product p set p.active = false where p.updatedAt < :cutoff")
int deactivateStale(@Param("cutoff") Instant cutoff);
```
`flushAutomatically` đẩy thay đổi đang chờ trước khi chạy; `clearAutomatically` xóa persistence context sau đó để lần đọc sau lấy dữ liệu mới. Bulk update không tăng `@Version`, không chạy lifecycle callback (`@PreUpdate`), không cập nhật second-level cache theo từng entity.

equals/hashCode cho entity (id do DB sinh):
```java
@Override public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Product other)) return false;   // instanceof: chấp nhận Hibernate proxy
    return getId() != null && getId().equals(other.getId()); // getId(): proxy không init field
}
@Override public int hashCode() { return getClass().hashCode(); } // hằng số: không đổi sau persist
```
Không dùng Lombok `@Data`/`@EqualsAndHashCode` mặc định trên entity (dùng mọi field, kể cả lazy collection → trigger load, vòng lặp vô hạn với quan hệ hai chiều). Nếu có natural key bất biến (SKU), dùng nó cho equals/hashCode là lựa chọn tốt hơn.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `open-in-view=false` | Mặc định nên chọn cho REST API | Phải fetch đủ dữ liệu trong service (DTO, fetch join); lazy ngoài transaction → `LazyInitializationException` |
| `open-in-view=true` (mặc định Boot, kèm WARN) | App MVC render view đơn giản, prototype | Connection lấy lần đầu được giữ tới hết request, kể cả lúc render/serialize (Spring cấu hình Hibernate ở chế độ `DELAYED_ACQUISITION_AND_HOLD`); query lazy ẩn trong tầng web; N+1 khó thấy |
| Trả entity từ controller | Không nên | Lộ cấu trúc DB, trigger lazy load khi serialize, vòng lặp JSON, mass assignment khi nhận entity làm request body |
| Trả DTO/record | Mặc định | Thêm lớp mapping (tay hoặc MapStruct) |
| Dirty checking (sửa field, không gọi `save`) | Use case cập nhật entity đã load | Người mới khó thấy "vì sao dữ liệu bị update"; mọi thay đổi vô tình trên entity managed đều được ghi |

**Pitfalls trong production**
- `LazyInitializationException: could not initialize proxy - no Session` → truy cập association lazy sau khi transaction đóng (trong controller, trong Jackson serializer, trong `@Async`) → fetch trong service bằng `JOIN FETCH`/`@EntityGraph`, map sang DTO trước khi rời transaction. Không "chữa" bằng `FetchType.EAGER` hay `hibernate.enable_lazy_load_no_trans`.
- Dữ liệu cũ sau bulk update → persistence context vẫn giữ entity load trước đó → `clearAutomatically = true` hoặc chạy bulk update trong transaction riêng.
- Entity được `UPDATE` dù không ai gọi `save()` → sửa field trên entity managed (ví dụ để format dữ liệu trả về) → map sang DTO rồi sửa DTO; dùng `readOnly` cho luồng đọc.
- `merge()` rồi tiếp tục dùng object cũ → object truyền vào vẫn detached, thay đổi sau đó bị mất → luôn dùng giá trị trả về của `merge()`/`save()`.
- Memory tăng dần trong job xử lý hàng trăm nghìn dòng trong một transaction → first-level cache giữ mọi entity → `flush()` + `clear()` theo chunk, hoặc dùng `StatelessSession`/JDBC cho batch (xem mục 10).

**Liên hệ với demo**
Demo đặt `spring.jpa.open-in-view=false` và trả `ProductResponse` (record) thay vì entity — đúng cho production. `create()` dùng `existsBySku` rồi `save()`: nếu muốn bắt vi phạm unique constraint ngay trong service, dùng `saveAndFlush()` (xem mục 11).

**Câu hỏi kiểm tra**
- **H:** Vì sao đoạn `var p = repo.findById(id).orElseThrow(); p.setPrice(x);` trong method `@Transactional` vẫn cập nhật DB dù không gọi `save()`?
  **Đ:** `p` là entity managed; lúc commit Hibernate flush, dirty checking thấy `price` khác snapshot và sinh `UPDATE`.
- **H:** Entity dùng UUID tự sinh trong constructor. Vì sao mỗi lần `save()` entity mới lại có thêm một `SELECT`?
  **Đ:** Id khác `null` nên Spring Data coi là không mới và gọi `merge()`; `merge()` phải `SELECT` để phân biệt insert/update. Sửa bằng `@Version` wrapper hoặc `Persistable`.
- **H:** Vì sao lỗi unique constraint trong `save()` lại xuất hiện ở tầng controller/advice dưới dạng lỗi lúc commit?
  **Đ:** `INSERT` có thể bị hoãn tới flush lúc commit (khi proxy của service kết thúc transaction), nên exception ném ra từ proxy, ngoài code của method. Dùng `saveAndFlush()` nếu cần xử lý tại chỗ. (Với id kiểu `IDENTITY`, `INSERT` chạy ngay lúc `persist` để lấy id.)
- **H:** Open Session In View sai ở đâu về mặt kiến trúc?
  **Đ:** Nó kéo dài persistence context và connection ra hết request, che giấu truy vấn lazy trong tầng web, làm N+1 và giữ connection lâu mà không ai thấy trong code service.

### 10. Hiệu năng truy vấn

**Tóm tắt**
- Vấn đề hiệu năng JPA phổ biến nhất là **số lượng query**, không phải tốc độ từng query: N+1, count query thừa, load entity đầy đủ chỉ để lấy 3 cột.
- Phát hiện trước khi sửa: bật log SQL ở môi trường dev/test, đếm query bằng Hibernate statistics, và viết test khẳng định số query cho luồng quan trọng.
- Công cụ theo thứ tự ưu tiên: DTO projection cho luồng đọc → `JOIN FETCH`/`@EntityGraph` cho aggregate cần entity → batch fetching làm lưới an toàn toàn cục.
- Ghi số lượng lớn: tránh `IDENTITY` nếu cần JDBC batch insert; dùng `SEQUENCE` + pooled optimizer, bật `hibernate.jdbc.batch_size`.
- Phân trang sâu (offset lớn) chậm tuyến tính; API cuộn vô hạn / export nên dùng keyset pagination (`Window`, `ScrollPosition`).

**Cơ chế bên dưới**

*N+1*: `findAll()` trả 100 `Order`, rồi code (hoặc Jackson) duyệt `order.getItems()` → 1 query cho order + 100 query cho items. `FetchType.LAZY` không gây N+1; **cách truy cập** gây ra. Mặc định JPA của `@ManyToOne`/`@OneToOne` là `EAGER` — nên đổi sang `LAZY` rồi fetch có chủ đích.

Phát hiện:
```yaml
spring:
  jpa:
    properties:
      hibernate:
        generate_statistics: true     # chỉ dev/test: log số query, số entity load mỗi session
        default_batch_fetch_size: 50  # lưới an toàn: gom lazy load thành IN (...) theo lô
        jdbc.batch_size: 50
        order_inserts: true
        order_updates: true
logging.level.org.hibernate.SQL: debug
```

Xử lý N+1:
```java
@Query("select o from Order o join fetch o.items where o.status = :status")
List<Order> findWithItems(@Param("status") Status status);

@EntityGraph(attributePaths = {"customer", "items"})
Optional<Order> findById(Long id);
```
`@BatchSize(size = 50)` trên association/entity cho tác dụng như `default_batch_fetch_size` nhưng cục bộ. Batch fetching biến N+1 thành 1 + N/50 query — không tối ưu bằng fetch join, nhưng áp dụng tự động cho mọi chỗ.

*JOIN FETCH collection + phân trang*: `join fetch o.items` kèm `Pageable` → SQL không thể `LIMIT` theo order (mỗi order nhân thành nhiều dòng). Hibernate tải **toàn bộ** kết quả vào bộ nhớ rồi cắt trang, và log WARN `HHH90003004: firstResult/maxResults specified with collection fetch; applying in memory`. Với bảng lớn đây là OOM chờ xảy ra. Bật `hibernate.query.fail_on_pagination_over_collection_fetch=true` để biến cảnh báo thành lỗi. Cách làm đúng: query 1 lấy trang ID (`select o.id ... order by ... limit`), query 2 `join fetch` theo danh sách ID đó; hoặc phân trang entity cha rồi để batch fetching nạp collection.

*DTO projection*:

| Kiểu | Ví dụ | Ghi chú |
|---|---|---|
| Interface (closed) | `interface ProductView { String getSku(); BigDecimal getPrice(); }` | Spring Data tạo proxy; với derived query chỉ select cột cần |
| Record/class DTO | `record ProductRow(String sku, BigDecimal price)` + `select new com.example...ProductRow(p.sku, p.price) from Product p` | Không có persistence context overhead, không dirty checking, an toàn nhất để serialize |
| Dynamic | `<T> List<T> findBySku(String sku, Class<T> type)` | Một method, nhiều view |

*ID generation và batch insert*:
- `GenerationType.IDENTITY`: DB sinh id lúc `INSERT`; Hibernate phải chạy `INSERT` ngay khi `persist()` để biết id → **tắt JDBC batch insert** cho entity đó.
- `GenerationType.SEQUENCE` + `allocationSize` (mặc định JPA là 50): Hibernate dùng pooled optimizer, một lần gọi sequence cấp cho 50 id → insert có thể gom lô. `INCREMENT BY` của sequence trong DB phải khớp `allocationSize` (migration phải tạo đúng). Từ Hibernate 7, `GenerationType.SEQUENCE` nếu trỏ tới generator thì chỉ được là `@SequenceGenerator` (không trộn với `@TableGenerator` hay generator khác); các bản trước không kiểm tra chặt điều này.
- `order_inserts`/`order_updates` sắp xếp statement theo entity để batch không bị cắt vụn khi xen kẽ nhiều loại entity.

*Pagination*:

| Kiểu | SQL | Ưu | Nhược |
|---|---|---|---|
| Offset (`Pageable`, `Page`) | `offset ? rows fetch first ? rows only` + `count(*)` | Nhảy tới trang bất kỳ, có tổng số trang | DB vẫn đọc và bỏ `offset` dòng; count tốn kém; dữ liệu chèn/xóa làm trùng/sót dòng |
| `Slice` | Lấy `size + 1` dòng, không count | Rẻ hơn `Page` | Không có tổng số |
| Keyset (`Window` + `ScrollPosition`) | `where name > ? or (name = ? and id > ?) order by name, id` + giới hạn số dòng | Thời gian ổn định ở mọi độ sâu, không trùng/sót | Không nhảy trang; sort phải ổn định (kết thúc bằng khóa duy nhất) và có index phù hợp |

```java
Window<Product> findFirst20ByActiveTrueOrderByNameAscIdAsc(ScrollPosition position);

Window<Product> w = repo.findFirst20ByActiveTrueOrderByNameAscIdAsc(ScrollPosition.keyset());
while (!w.isEmpty()) {
    process(w.getContent());
    if (!w.hasNext()) break;
    w = repo.findFirst20ByActiveTrueOrderByNameAscIdAsc(w.positionAt(w.size() - 1));
}
```
Với API, trả cho client một cursor mã hóa (base64 của các giá trị keyset), không trả offset. `WindowIterator` (`org.springframework.data.support`) gói sẵn vòng lặp trên.

*Second-level cache*: cache entity theo id dùng chung giữa các session (cần provider JCache như Ehcache/Infinispan). Hợp với dữ liệu tham chiếu đọc nhiều, ít đổi (danh mục, cấu hình). Không hợp với dữ liệu ghi nhiều, dữ liệu bị bulk update/ghi bởi hệ thống khác (cache không biết để invalidate), hoặc cluster nhiều node nếu provider không đồng bộ. Query cache còn hạn chế hơn: bị invalidate mỗi khi bảng liên quan thay đổi. Thường cache DTO ở tầng service (xem mục 15) dễ kiểm soát hơn.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `JOIN FETCH` / `@EntityGraph` | Cần aggregate đầy đủ để sửa, số dòng con nhỏ | Tích Descartes khi fetch 2 collection (`MultipleBagFetchException` nếu cả hai là `List`); không phân trang được |
| `default_batch_fetch_size` | Bật toàn cục làm lưới an toàn | Vẫn nhiều query hơn fetch join; che giấu thiết kế truy cập kém |
| DTO projection | Luồng đọc, list, report | Không dùng được để cập nhật; thêm class |
| `Page` | UI cần tổng số trang, dữ liệu vừa phải | Count query có thể đắt hơn query chính |
| Keyset `Window` | Infinite scroll, export, đồng bộ dữ liệu | Không nhảy trang, cần index đúng thứ tự sort |
| JDBC/`JdbcClient` cho batch lớn | Import/ETL hàng triệu dòng | Mất dirty checking, cascade, callback (xem mục 12) |

**Pitfalls trong production**
- Endpoint list chậm dần khi dữ liệu tăng, log có hàng trăm câu SQL giống nhau → N+1 do serialize entity hoặc map DTO truy cập lazy association → projection hoặc fetch join; thêm test đếm query.
- OOM hoặc GC dài khi mở trang danh sách → `HHH90003004` (phân trang in-memory do `join fetch` collection) → tách 2 query; bật `fail_on_pagination_over_collection_fetch` ở mọi môi trường để phát hiện sớm.
- Import 100k dòng mất hàng phút dù đã bật `batch_size` → entity dùng `IDENTITY` hoặc persistence context phình to → chuyển sang `SEQUENCE` pooled, `flush()`/`clear()` theo chunk, hoặc dùng JDBC batch.
- Trùng id / lỗi khóa chính sau khi đổi sang `SEQUENCE` → `INCREMENT BY` trong DB khác `allocationSize` → sửa migration cho khớp.
- Trang 5000 của `Page` mất vài giây → offset lớn + count toàn bảng → keyset pagination; nếu vẫn cần `Page`, giới hạn độ sâu trang và viết `countQuery` riêng tối ưu.

**Liên hệ với demo**
Demo trả `Page` và chấp nhận offset pagination với count query (count bị bỏ qua khi trang đầu chưa đầy). Production nên dùng `spring.data.web.pageable.serialization-mode=via-dto` để JSON ổn định, giới hạn `size`, và cân nhắc keyset cho danh sách lớn.

**Câu hỏi kiểm tra**
- **H:** `@OneToMany(fetch = LAZY)` rồi mà vẫn N+1. Vì sao?
  **Đ:** LAZY chỉ hoãn việc load; khi code/serializer duyệt collection của từng phần tử thì mỗi lần là một query. Phải fetch chủ động (fetch join, entity graph, batch fetching) hoặc dùng projection.
- **H:** Vì sao đổi `IDENTITY` sang `SEQUENCE` có thể tăng tốc insert hàng loạt nhiều lần?
  **Đ:** Với `IDENTITY`, Hibernate phải chạy từng `INSERT` ngay để lấy id nên không batch được; `SEQUENCE` pooled cấp id trước theo lô, cho phép gom nhiều `INSERT` vào một JDBC batch.
- **H:** Keyset pagination yêu cầu gì ở câu sort?
  **Đ:** Thứ tự phải xác định duy nhất (thêm khóa duy nhất như `id` vào cuối), có index khớp thứ tự đó; và không cho nhảy tới trang tùy ý.
- **H:** Khi nào không nên bật second-level cache cho một entity?
  **Đ:** Khi entity ghi nhiều, bị cập nhật bởi bulk query/hệ thống khác, hoặc chạy nhiều node mà provider không đồng bộ — cache sẽ trả dữ liệu cũ mà Hibernate không biết để invalidate.

### 11. Concurrency control

**Tóm tắt**
- **Lost update** là lỗi concurrency phổ biến nhất trong CRUD: hai request cùng đọc version cũ, cùng ghi, request sau đè request trước. `READ_COMMITTED` (mặc định của đa số DB) không ngăn được.
- **Optimistic locking** (`@Version`) là mặc định nên chọn cho web app: không giữ lock, phát hiện xung đột lúc ghi. Xung đột → 409 cho client (người dùng quyết định) hoặc retry tự động (khi thao tác có thể tính lại an toàn).
- **Pessimistic locking** (`SELECT ... FOR UPDATE`) khi xung đột thường xuyên và chi phí làm lại cao (trừ tồn kho, số dư). Cái giá: lock chờ, deadlock, transaction phải ngắn.
- Kiểm tra trước rồi ghi (`existsBy...` → `save`) luôn có race. **Constraint trong DB** (unique, check, foreign key) là chốt chặn cuối cùng; code ứng dụng chỉ để trả lỗi đẹp hơn.
- Idempotency và job queue giải quyết ở tầng DB: bảng idempotency key + unique constraint, upsert, `FOR UPDATE SKIP LOCKED`.

**Cơ chế bên dưới**

*Optimistic locking*:
```java
@Entity
public class Product {
    @Id @GeneratedValue(strategy = GenerationType.SEQUENCE) private Long id;
    @Version private Long version;   // wrapper: null = entity mới (xem mục 9)
    // ...
}
```
Hibernate sinh `update products set ..., version = ? where id = ? and version = ?`. Nếu số dòng bị ảnh hưởng là 0 → Hibernate ném `StaleObjectStateException`/`OptimisticLockException` → Spring dịch thành `ObjectOptimisticLockingFailureException` (con của `OptimisticLockingFailureException`). Exception này xuất hiện lúc **flush/commit**.

Với REST API, version phải đi vòng qua client: trả `version` (hoặc `ETag`) trong response GET, client gửi lại trong PUT/PATCH (`If-Match`). Service so sánh version client gửi với entity vừa load; khác → 409/412 ngay. Nếu chỉ dựa vào `@Version` của entity vừa load trong cùng request, bạn chỉ bảo vệ được khoảng thời gian rất ngắn giữa load và commit, không bảo vệ được "người dùng A mở form 5 phút trước".

*Retry xung đột*: retry phải bao **ngoài** transaction (mỗi lần thử là transaction mới, load lại dữ liệu mới). Cách chắc chắn nhất là đặt retry ở một bean khác gọi vào service `@Transactional`:
```java
import org.springframework.resilience.annotation.Retryable;

@Component
class StockFacade {
    private final StockService stockService; // có @Transactional
    StockFacade(StockService s) { this.stockService = s; }

    @Retryable(includes = OptimisticLockingFailureException.class, maxRetries = 3,
               delay = 50, multiplier = 2, jitter = 25)
    public void reserve(long productId, int qty) { stockService.reserve(productId, qty); }
}
```
(`@Retryable` của Framework 7 cần `@EnableResilientMethods`, xem mục 7.) Không retry mù quáng thao tác có side effect ngoài DB.

*Pessimistic locking*:
```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@QueryHints(@QueryHint(name = "jakarta.persistence.lock.timeout", value = "3000"))
@Query("select s from Stock s where s.productId = :id")
Optional<Stock> findForUpdate(@Param("id") long productId);
```
Sinh `select ... for update` (cú pháp theo dialect). Lock giữ tới hết transaction. Lock timeout hint không phải DB nào cũng hỗ trợ theo từng query; hãy đặt thêm lock timeout ở cấp DB/session. Lỗi lock được Spring dịch thành `PessimisticLockingFailureException` / `CannotAcquireLockException` (`DeadlockLoserDataAccessException` đã deprecated từ Framework 6.0.3).

Deadlock: transaction T1 khóa A rồi chờ B, T2 khóa B rồi chờ A → DB giết một bên. Phòng tránh: luôn khóa theo thứ tự cố định (ví dụ sắp xếp theo id), transaction ngắn, khóa ít dòng nhất có thể.

*Unique constraint là chốt chặn*:
```java
@Transactional
public ProductResponse create(CreateProductRequest req) {
    try {
        return toResponse(repo.saveAndFlush(toEntity(req))); // flush để lỗi xảy ra trong try
    } catch (DataIntegrityViolationException e) {
        throw new DuplicateSkuException(req.sku());           // advice map sang 409
    }
}
```
`DataIntegrityViolationException` bao gồm cả vi phạm not-null, foreign key, độ dài cột; nếu cần chắc chắn là unique violation, kiểm tra tên constraint hoặc SQLState (`23505` trên PostgreSQL/H2). Sau exception này transaction đã hỏng (rollback-only); không thực hiện thêm thao tác DB trong cùng transaction.

*Idempotency ở tầng DB*:
- Bảng `idempotency_keys(key PRIMARY KEY, request_hash, response, created_at)`. Trong **cùng transaction** với thao tác nghiệp vụ: insert key → xử lý → lưu response. Request trùng key → vi phạm unique → đọc response đã lưu và trả lại. `request_hash` để phát hiện client dùng lại key cho request khác.
- Upsert theo dialect: PostgreSQL `insert ... on conflict (sku) do nothing/do update`, MySQL `insert ... on duplicate key update`, `MERGE` (SQL chuẩn, Oracle/SQL Server). Viết bằng native query hoặc `JdbcClient`.

*Job queue bằng DB*: nhiều worker lấy việc không đụng nhau:
```java
@Query(value = """
    select * from jobs where status = 'PENDING'
    order by created_at limit :n
    for update skip locked""", nativeQuery = true)
List<Job> claimBatch(@Param("n") int n);
```
Gọi trong transaction, đổi `status = 'RUNNING'` rồi commit ngay (claim ngắn), xử lý bên ngoài transaction. `SKIP LOCKED` có trên PostgreSQL, MySQL 8+, Oracle (cú pháp giới hạn dòng khác nhau). Cần cơ chế thu hồi job bị kẹt (worker chết khi đang `RUNNING`).

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@Version` + 409 | Form chỉnh sửa, xung đột hiếm | Client phải gửi version; UX xử lý xung đột |
| `@Version` + retry tự động | Thao tác tính lại được (tăng bộ đếm, trừ tồn kho có kiểm tra) | Retry storm khi tranh chấp cao; phải retry ngoài transaction |
| `PESSIMISTIC_WRITE` | Tranh chấp cao trên ít dòng, làm lại tốn kém | Lock wait, deadlock, giảm throughput; transaction phải rất ngắn |
| Atomic update (`update stock set qty = qty - :n where id = :id and qty >= :n`) | Bộ đếm, tồn kho đơn giản | Bỏ qua persistence context (xem mục 9); logic nằm trong SQL |
| Isolation `SERIALIZABLE` | Bất biến phức tạp nhiều dòng (write skew) | Lỗi serialization phải retry; hiệu năng giảm |
| Distributed lock (Redis...) | Tài nguyên không nằm trong một DB | Phức tạp, lease/timeout; không thay constraint DB |

**Pitfalls trong production**
- Dữ liệu "thỉnh thoảng" bị đè, không có lỗi nào → lost update, entity không có `@Version` → thêm `@Version` và đưa version qua API.
- 500 với `ObjectOptimisticLockingFailureException` → xung đột không được map → advice trả 409 (mã lỗi riêng, ví dụ `CONCURRENT_MODIFICATION`) hoặc retry có giới hạn.
- Thread treo, pool cạn khi tải cao → pessimistic lock + transaction dài (hoặc gọi remote khi đang giữ lock) → rút ngắn transaction, đặt lock timeout, đổi sang optimistic/atomic update.
- Log DB báo deadlock định kỳ → khóa dòng theo thứ tự khác nhau ở hai luồng code → chuẩn hóa thứ tự khóa; retry transaction bị chọn làm nạn nhân.
- Bản ghi trùng dù đã `existsBy...` → race giữa check và insert → unique constraint + bắt `DataIntegrityViolationException`.

**Liên hệ với demo**
Demo dùng `existsBySku` rồi `save()` — có race khi hai request cùng SKU đến gần như đồng thời; unique constraint trên `sku` mới là bảo đảm thật. Production: giữ `existsBySku` để trả lỗi sớm cho trường hợp phổ biến, nhưng vẫn bắt `DataIntegrityViolationException` → 409 `DUPLICATE_SKU`. Entity nên có `@Version` trước khi thêm endpoint PUT.

**Câu hỏi kiểm tra**
- **H:** Có `@Version` rồi, vì sao hai người dùng mở cùng form vẫn đè dữ liệu của nhau?
  **Đ:** Vì server load entity mới (version mới nhất) trong request PUT rồi ghi; version người dùng thấy lúc mở form không được gửi lên và so sánh. Phải đưa version/ETag qua client.
- **H:** Đặt `@Retryable` và `@Transactional` trên cùng một method có vấn đề gì?
  **Đ:** Nếu advice transaction nằm ngoài advice retry, mọi lần thử chạy trong cùng một transaction đã bị đánh dấu rollback-only với persistence context cũ → retry vô nghĩa. Retry phải bao ngoài transaction. Với `@Retryable` của Framework 7 và order mặc định, retry advisor đã nằm ngoài transaction (xem mục 4); với Spring Retry hoặc order tự chỉnh thì phải kiểm tra lại (tách bean hoặc chỉnh order).
- **H:** Vì sao không thể thay locking bằng việc nâng isolation lên `REPEATABLE_READ`?
  **Đ:** Isolation quy định những gì transaction nhìn thấy, không tự đảm bảo bất biến nghiệp vụ; tùy DB, lost update/write skew vẫn xảy ra hoặc bị báo thành lỗi mà ứng dụng phải retry. Locking/constraint thể hiện ý định rõ ràng.
- **H:** Thiết kế job queue trên PostgreSQL cho 10 worker không xử lý trùng việc?
  **Đ:** `select ... for update skip locked limit n` trong transaction ngắn để claim (đổi trạng thái), commit, xử lý ngoài transaction; có timeout thu hồi job kẹt; handler idempotent vì vẫn có thể chạy lại.

### 12. Chọn công nghệ data access và connection pool

**Tóm tắt**
- Không có công nghệ "tốt nhất"; chọn theo hình dạng dữ liệu và kiểu truy cập. JPA mạnh ở aggregate có vòng đời và logic cập nhật; SQL-first (`JdbcClient`, jOOQ) mạnh ở query phức tạp, report, batch.
- Một service có thể dùng **cả hai**: Spring Data JPA cho luồng ghi, `JdbcClient`/jOOQ cho luồng đọc phức tạp — chung `DataSource` và transaction manager (`JpaTransactionManager` cũng quản lý JDBC trên cùng connection).
- Connection pool nhỏ thường nhanh hơn pool lớn. Mặc định HikariCP là 10 connection; tăng pool phải dựa trên đo đạc và giới hạn `max_connections` của DB chia cho số instance.
- `maxLifetime` phải ngắn hơn vài giây so với mọi giới hạn thời gian sống connection của DB, proxy, load balancer, firewall.
- Virtual threads (xem mục 22) gỡ giới hạn thread, nhưng không tạo thêm connection: pool trở thành nút thắt và là cơ chế backpressure.

**Cơ chế bên dưới**

So sánh công nghệ:

| Công nghệ | Mô hình | Mạnh | Yếu |
|---|---|---|---|
| Spring Data JPA (Hibernate) | ORM, persistence context, dirty checking, lazy loading | CRUD aggregate nhanh, `@Version`, cascade, derived query | Hành vi ngầm (flush, lazy, N+1), khó kiểm soát SQL, đường cong học tập |
| Spring Data JDBC | Aggregate đơn giản, không lazy, không persistence context; lưu cả aggregate mỗi lần `save` | Dễ đoán, ít "phép thuật", hợp DDD aggregate nhỏ | Không lazy, cập nhật collection = xóa rồi insert lại con; ít tính năng |
| `JdbcClient` (Framework 6.1+) | API fluent trên `JdbcTemplate`/`NamedParameterJdbcTemplate` | SQL tường minh, map record gọn, batch nhanh; Framework 7 thêm cấu hình fetch size, max rows, query timeout ở mức statement | Tự viết SQL và mapping; không type-safe khi đổi schema |
| jOOQ | SQL type-safe sinh từ schema | Query phức tạp (window function, CTE) type-safe, bắt lỗi lúc compile | Bước codegen trong build; dialect thương mại cần license |
| R2DBC (+ Spring Data R2DBC) | Non-blocking, reactive | Hợp stack WebFlux end-to-end | Không có ORM đầy đủ; toàn bộ chuỗi phải reactive; debug khó |

```java
record ProductRow(long id, String sku, BigDecimal price) {}

List<ProductRow> expensive(BigDecimal min) {
    return jdbcClient.sql("select id, sku, price from products where price > :min order by price desc")
            .param("min", min)
            .query(ProductRow.class)   // map theo tên cột → component của record
            .list();
}
```
Boot tự tạo bean `JdbcClient` khi có `DataSource`. Gọi trong method `@Transactional` của JPA, `JdbcClient` dùng chung connection của transaction; lưu ý Hibernate chỉ flush trước query JDBC nếu bạn tự `flush()` — JDBC không kích hoạt auto flush.

*HikariCP* (Boot 4.0.x và 4.1.x quản lý HikariCP 7.0.x; cấu hình qua `spring.datasource.hikari.*`):

| Thuộc tính | Mặc định | Ghi chú |
|---|---|---|
| `maximum-pool-size` | 10 | Điểm xuất phát từ wiki HikariCP ("About Pool Sizing"): `connections = (core_count × 2) + effective_spindle_count`, với core của **DB server** (không tính hyper-thread); spindle hiệu dụng ≈ 0 khi dữ liệu nằm hết trong cache. Sau đó đo |
| `minimum-idle` | bằng `maximum-pool-size` | Khuyến nghị giữ pool cố định kích thước |
| `connection-timeout` | 30000 ms | Thời gian chờ lấy connection; nên thấp hơn timeout của request để fail nhanh |
| `max-lifetime` | 1800000 ms (30 phút) | Ngắn hơn giới hạn của DB/LB/firewall vài giây |
| `keepalive-time` | 120000 ms | Ping connection idle để hạ tầng mạng không cắt |
| `leak-detection-threshold` | 0 (tắt) | Bật (ví dụ 2× thời gian transaction dài nhất) để log stack trace nơi mượn connection quá lâu |

Vì sao pool nhỏ tốt hơn: DB chỉ thực thi song song được khoảng số core (cộng phần chờ I/O); thêm connection chỉ làm tăng context switch, lock contention trong DB. Hãy nhìn metric `hikaricp.connections.pending` (số thread đang chờ connection) và `hikaricp.connections.acquire` (thời gian chờ lấy connection) — có khi có Actuator + Micrometer — trước khi tăng pool.

*Read replica*: định tuyến theo `readOnly` của transaction.
```java
import org.springframework.jdbc.datasource.LazyConnectionDataSourceProxy;

@Bean @Primary
DataSource dataSource(@Qualifier("primaryDs") DataSource primary,
                      @Qualifier("replicaDs") DataSource replica) {
    var ds = new LazyConnectionDataSourceProxy(primary);
    ds.setReadOnlyDataSource(replica);   // Framework 6.1.2+: transaction readOnly → replica
    return ds;
}
```
Vì sao cần `LazyConnectionDataSourceProxy`: transaction manager lấy connection ngay lúc bắt đầu transaction, trước khi cờ read-only được áp. Proxy trì hoãn việc lấy connection thật tới statement đầu tiên, lúc đó mới biết transaction là read-only. Cách cũ (`AbstractRoutingDataSource` đọc `TransactionSynchronizationManager.isCurrentTransactionReadOnly()`) cũng phải bọc trong `LazyConnectionDataSourceProxy` vì lý do tương tự. Khi tự khai báo `DataSource`, auto-configuration `DataSource` của Boot lùi lại; bạn tự tạo hai pool (ví dụ từ hai `DataSourceProperties`) và tự lo metrics/tên pool.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| JPA cho tất cả | Domain CRUD, team quen JPA | Report/batch chậm, dễ N+1 |
| JPA ghi + `JdbcClient`/jOOQ đọc | Hệ thống có cả aggregate và query phức tạp | Hai phong cách trong một codebase; cần quy ước rõ |
| Spring Data JDBC | Muốn đơn giản, dễ đoán, aggregate nhỏ | Mất lazy, dirty checking; cập nhật collection tốn |
| R2DBC | Đã chọn WebFlux, cần hàng chục nghìn kết nối đồng thời giữ lâu | Hệ sinh thái nhỏ hơn; virtual threads + JDBC thường là lựa chọn đơn giản hơn |
| Read replica | Tải đọc lớn, chấp nhận dữ liệu trễ | Replication lag: đọc ngay sau ghi có thể không thấy dữ liệu mới |

**Pitfalls trong production**
- `SQLTransientConnectionException: ... Connection is not available, request timed out after 30000ms` → pool cạn do transaction dài, gọi remote trong transaction, leak, hoặc `REQUIRES_NEW` lồng nhau → bật `leak-detection-threshold`, xem metric pending/usage, rút ngắn transaction trước khi tăng pool.
- Lỗi "connection closed/broken pipe" rải rác sau thời gian idle → LB/firewall/DB cắt connection trước `max-lifetime` → giảm `max-lifetime` dưới giới hạn đó, để `keepalive-time` hoạt động.
- DB báo quá `max_connections` khi scale out → mỗi instance có pool 10–50, nhân với số pod (cộng pod mới khi rolling deploy) → tính tổng; cân nhắc proxy pool phía DB (PgBouncer, RDS Proxy).
- Bật virtual threads, throughput không tăng mà timeout lấy connection tăng → hàng nghìn virtual thread tranh 10 connection → pool là giới hạn thật; giới hạn đồng thời ở tầng trên (`@ConcurrencyLimit`, semaphore) hoặc tăng pool có đo đạc.
- Đọc từ replica ngay sau khi ghi không thấy dữ liệu → replication lag → luồng read-your-writes dùng primary (transaction không readOnly), hoặc định tuyến theo phiên sau khi ghi.

**Liên hệ với demo**
Demo dùng Spring Data JPA và pool Hikari mặc định trên H2 in-memory. Khi lên DB thật, đặt `maximum-pool-size` và `max-lifetime` có chủ đích, bật metric pool qua Actuator (xem mục 25).

**Câu hỏi kiểm tra**
- **H:** Service có 200 thread Tomcat nhưng pool 10 connection. Có nên tăng pool lên 200?
  **Đ:** Thường không. DB chỉ xử lý song song hữu hạn; 200 connection mỗi instance nhân số pod dễ vượt `max_connections` và làm DB chậm hơn. Rút ngắn thời gian giữ connection, đo `pending`/acquire time, tăng từng bước.
- **H:** Vì sao định tuyến replica theo `readOnly` cần `LazyConnectionDataSourceProxy`?
  **Đ:** Connection được lấy lúc bắt đầu transaction, trước khi thông tin read-only được áp; proxy trì hoãn việc lấy connection thật tới statement đầu tiên nên chọn đúng datasource.
- **H:** Dùng `JdbcClient` trong method `@Transactional` đang sửa entity JPA thì cần chú ý gì?
  **Đ:** Chung connection và transaction, nhưng thay đổi entity chưa flush sẽ không thấy được từ SQL thuần; gọi `flush()` trước query JDBC. Ngược lại, ghi bằng JDBC không cập nhật persistence context.

### 13. Schema migration và zero-downtime deployment

**Tóm tắt**
- Schema production chỉ được thay đổi bằng migration có version, review được, nằm trong git (Flyway hoặc Liquibase). `ddl-auto` ở production chỉ là `none` hoặc `validate`.
- Boot 4 tách integration theo module: cần `spring-boot-starter-flyway` hoặc `spring-boot-starter-liquibase` (chỉ thêm `flyway-core` như Boot 3 là không đủ để có auto-configuration).
- Rolling deploy nghĩa là **phiên bản app cũ và mới chạy song song trên cùng schema**. Mọi migration phải tương thích với cả hai → dùng expand/contract (parallel change), tách thay đổi phá vỡ thành nhiều lần deploy.
- Migration "nhỏ" có thể khóa bảng lớn nhiều phút. Biết lệnh nào lấy lock gì trên DB của bạn, đặt `lock_timeout`, backfill theo lô.
- Chạy migration lúc app start là đơn giản nhất; chạy bằng job riêng tách quyền và kiểm soát tốt hơn. Chọn có chủ đích.

**Cơ chế bên dưới**

Boot 4:
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-flyway</artifactId>
</dependency>
<dependency> <!-- Flyway 10+ tách hỗ trợ từng DB thành module riêng -->
  <groupId>org.flywaydb</groupId>
  <artifactId>flyway-database-postgresql</artifactId>
</dependency>
```
Khi có Flyway/Liquibase, Boot chạy migration **trước** khi khởi tạo `EntityManagerFactory`, nên `ddl-auto=validate` kiểm tra entity với schema đã migrate. Nếu không đặt `ddl-auto`, Boot chỉ mặc định `create-drop` khi DB là embedded **và** không có công cụ migration quản lý schema; còn lại là `none`. Mặc định Flyway đọc `classpath:db/migration` (`V1__init.sql`, `V2__add_version_column.sql`...). Muốn tùy biến thời điểm/cách chạy (ví dụ chỉ validate lúc start), khai báo bean `FlywayMigrationStrategy`, hoặc tắt hẳn bằng `spring.flyway.enabled=false` và chạy bằng job riêng.

Nhiều instance start cùng lúc: Flyway khóa ở DB (PostgreSQL dùng advisory lock) để chỉ một instance migrate, các instance khác chờ. Liquibase dùng bảng `DATABASECHANGELOGLOCK`; pod bị kill giữa chừng có thể để lại lock "treo" phải gỡ tay.

*Expand/contract* — ví dụ đổi tên cột `name` → `display_name`, không downtime:

| Bước | Migration | App |
|---|---|---|
| 1. Expand | Thêm `display_name` (nullable) | v2: ghi cả hai cột, đọc `display_name` và fallback sang `name` |
| 2. Backfill | Copy dữ liệu theo lô (`update ... where id between ...`), không một lệnh `UPDATE` cả bảng | v2 chạy ổn định trên mọi instance |
| 3. Siết ràng buộc | `NOT NULL`, index trên `display_name` | v3: chỉ đọc/ghi `display_name` |
| 4. Contract | Xóa cột `name` (sau khi không còn instance nào dùng, và qua thời gian cho phép rollback) | |

Thay đổi tương thích ngược (an toàn một bước): thêm bảng mới; thêm cột nullable hoặc có default; thêm index (trên PostgreSQL dùng `CREATE INDEX CONCURRENTLY`); nới rộng kiểu (`varchar(50)` → `varchar(100)`, tùy DB có thể rewrite bảng).
Thay đổi phá vỡ (cần expand/contract): đổi tên/xóa cột hoặc bảng; thêm `NOT NULL` không default; thu hẹp kiểu; đổi ngữ nghĩa dữ liệu; thêm unique constraint trên dữ liệu có thể đang trùng.

Thứ tự deploy: migration expand chạy **trước** khi app mới nhận traffic; migration contract chạy **sau** khi app cũ đã biến mất hoàn toàn. Với `ddl-auto=validate`, app cũ không lỗi khi có thêm cột mới, nhưng app mới sẽ fail startup nếu cột nó cần chưa tồn tại.

Migration dài / khóa bảng (ví dụ PostgreSQL):
- Đặt `SET lock_timeout = '5s'` đầu migration: nếu không lấy được lock, fail nhanh thay vì xếp hàng và chặn mọi truy vấn phía sau.
- `CREATE INDEX CONCURRENTLY` không chạy được trong transaction. Parser PostgreSQL của Flyway tự nhận diện lệnh này (cùng `VACUUM`, `REINDEX ... DATABASE`...) và chạy migration đó ngoài transaction. Hãy để nó trong **một script riêng**: mặc định Flyway không cho trộn lệnh transactional và non-transactional trong cùng migration (`spring.flyway.mixed=false`). Muốn chỉ định tường minh cho từng script, dùng script configuration của Flyway (`executeInTransaction`; kiểm tra cú pháp trong docs của phiên bản đang dùng).
- Backfill dữ liệu lớn nên là job ứng dụng chạy theo lô, không phải một migration SQL chạy lúc startup.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Flyway | SQL thuần, team viết SQL tốt, đơn giản | Ít trừu tượng đa DB; rollback phải tự viết migration mới (forward-only) |
| Liquibase | Cần changelog đa DB (XML/YAML), precondition, rollback khai báo | Phức tạp hơn; lock table có thể kẹt |
| Migrate lúc app start | Ứng dụng nhỏ, ít instance, pipeline đơn giản | App cần quyền DDL; startup chậm hoặc fail vì migration; probe có thể kill pod đang migrate |
| Job riêng (K8s Job, bước CI/CD, init container) | Nhiều instance, DBA review, migration dài | Pipeline phức tạp hơn; phải đảm bảo thứ tự job → deploy |
| `ddl-auto=update` | Chỉ prototype/dev | Không có version, không review, không xóa cột, có thể tạo schema khác nhau giữa môi trường |

**Pitfalls trong production**
- Deploy xong, pod cũ lỗi hàng loạt `column "name" does not exist` → migration xóa/đổi tên cột trong cùng lần deploy với code mới → expand/contract, contract ở lần deploy sau.
- Toàn bộ API treo vài phút khi deploy → `ALTER TABLE` chờ lock sau một transaction dài, rồi mọi query khác xếp hàng sau nó → `lock_timeout`, chạy migration giờ thấp điểm, kiểm tra transaction dài trước khi migrate.
- Pod bị liveness probe kill giữa chừng khi migrate lúc start → migration dài hơn thời gian probe cho phép → chuyển migration sang job riêng hoặc dùng startup probe đủ dài.
- `Validate failed: Migrations have failed validation` → sửa file migration đã chạy (checksum đổi) → không bao giờ sửa migration đã áp dụng; viết migration mới.
- Môi trường dev chạy `ddl-auto=update` nên schema lệch với production → dùng cùng migration ở mọi môi trường; test với Testcontainers và `ddl-auto=validate` (xem mục 28).

**Liên hệ với demo**
Demo dùng `ddl-auto=update` và H2 để đơn giản hóa buổi học. Production: thêm `spring-boot-starter-flyway`, viết `V1__create_products.sql` (có unique constraint trên `sku`), đặt `spring.jpa.hibernate.ddl-auto=validate`.

**Câu hỏi kiểm tra**
- **H:** Vì sao không thể đổi tên cột trong một lần deploy rolling?
  **Đ:** Trong lúc rolling, pod cũ và mới chạy song song trên cùng schema; đổi tên làm một trong hai phiên bản lỗi. Phải expand (thêm cột mới, ghi cả hai), chuyển đọc, rồi contract ở lần deploy sau.
- **H:** Chạy Flyway lúc app start với 10 pod đồng thời có an toàn không?
  **Đ:** Về tính đúng thì có (Flyway khóa ở DB, chỉ một instance migrate), nhưng các pod khác chờ, startup chậm, app cần quyền DDL; migration dài dễ bị probe kill. Hệ thống lớn nên tách thành job riêng.
- **H:** `ddl-auto=validate` bảo vệ được gì và không bảo vệ được gì?
  **Đ:** Phát hiện entity map tới bảng/cột không tồn tại hoặc kiểu không khớp lúc startup. Không kiểm tra index, constraint, dữ liệu, cũng không sửa gì.

### 14. Tính nhất quán giữa các service

**Tóm tắt**
- *Dual-write*: ghi DB rồi gửi message (hoặc ngược lại) trong cùng một method **không** atomic. Crash, timeout hay rollback ở giữa sẽ để hai hệ thống lệch nhau. `@Transactional` không giải quyết được việc này.
- Cách chuẩn là **transactional outbox**: ghi bản ghi "sự kiện cần gửi" vào bảng outbox **trong cùng transaction DB** với dữ liệu nghiệp vụ, rồi một tiến trình khác đọc outbox và publish (polling hoặc CDC).
- Mọi kênh message thực tế là **at-least-once**, nên consumer phải **idempotent** (bảng dedup theo message id, hoặc thao tác tự nhiên idempotent).
- Nghiệp vụ trải qua nhiều service dùng **saga** (chuỗi transaction cục bộ + hành động bù trừ), không dùng transaction phân tán.
- "Exactly-once" của Kafka chỉ đúng **trong phạm vi Kafka** (đọc–xử lý–ghi topic). `KafkaTemplate` trong method `@Transactional` của DB là hai transaction được đồng bộ, **không phải XA**.

**Cơ chế bên dưới**

*Vì sao dual-write hỏng.* Ví dụ `createOrder()` gồm `orderRepository.save()` rồi `kafkaTemplate.send()`:
- Gửi trong transaction, trước commit: message đã đi nhưng commit DB có thể fail → consumer xử lý đơn hàng không tồn tại.
- Gửi sau commit (kể cả trong `@TransactionalEventListener(phase = AFTER_COMMIT)`, xem mục 5): DB đã commit nhưng process crash hoặc broker down trước khi gửi → mất message vĩnh viễn.
- Không có thứ tự nào của hai lệnh ghi an toàn; chỉ có thể biến hai lệnh ghi thành **một** lệnh ghi DB.

*Transactional outbox:*
```sql
create table outbox_event (
  id            uuid primary key,
  aggregate_type varchar(100) not null,
  aggregate_id  varchar(100) not null,   -- dùng làm message key để giữ thứ tự theo aggregate
  event_type    varchar(100) not null,
  payload       jsonb not null,
  created_at    timestamptz not null,
  published_at  timestamptz              -- null = chưa gửi (kiểu polling)
);
```
```java
@Transactional
public OrderDto createOrder(CreateOrderCommand cmd) {
    Order order = orderRepository.save(Order.from(cmd));
    outboxRepository.save(OutboxEvent.of("Order", order.getId(), "OrderCreated", toJson(order)));
    return OrderDto.from(order);          // commit: cả order và outbox, hoặc không gì cả
}
```
Relay đưa outbox ra broker theo một trong hai cách:
- **Polling publisher**: job định kỳ `select ... where published_at is null order by created_at limit 100 for update skip locked` (xem mục 11), gửi, rồi đánh dấu `published_at`. Crash giữa "gửi" và "đánh dấu" → gửi lại → trùng lặp (vẫn là at-least-once).
- **CDC (ví dụ Debezium)**: đọc transaction log của DB (WAL/binlog) và đẩy bản ghi outbox vào Kafka; ứng dụng không cần relay. Debezium có transform chuyên cho outbox (event router) — tên và cấu hình cụ thể xem docs Debezium của phiên bản đang dùng.

*Spring Modulith externalized events* (docs Modulith 2.1): event publication registry (`spring-modulith-starter-jpa` / `-jdbc` / `-mongodb` / `-neo4j`) lưu mỗi lần publish event cho listener `@ApplicationModuleListener` vào bảng ngay trong transaction của người publish, đánh dấu hoàn tất khi listener chạy xong. Từ 2.0, publication có trạng thái (PUBLISHED, PROCESSING, COMPLETED, FAILED, RESUBMITTED), có `IncompleteEventPublications` / `FailedEventPublications` để gửi lại, và `spring.modulith.events.republish-outstanding-events-on-restart` để gửi lại lúc khởi động. `spring.modulith.events.completion-mode` mặc định `UPDATE` (giữ bản ghi đã hoàn tất, phải tự purge); có `DELETE` và `ARCHIVE`. Event đánh dấu `@Externalized("order-created::#{#this.orderId()}")` (cú pháp `target::key`) được đẩy ra broker qua `spring-modulith-events-kafka` / `-amqp` / `-jms` / `-messaging`.
- Lưu ý: externalization gốc của Modulith được cài như một transactional event listener được registry bảo vệ. Chính docs Modulith nói nó thiếu những tính năng quan trọng của một outbox thật. Từ 2.1 có thể giao cho Namastack Outbox hoặc JobRunr (`spring-modulith-starter-namastack` / `-jobrunr`, `spring.modulith.events.externalization.mode=outbox`).
- Mặc định chỉ event nằm trong auto-configuration package mới được chọn để externalize.

*Idempotent consumer:*
```java
@KafkaListener(topics = "order-created")
@Transactional
public void on(OrderCreated event) {
    // processed_message(message_id primary key): insert trùng → vi phạm unique → bỏ qua
    if (!processedMessages.tryInsert(event.eventId())) return;
    inventoryService.reserve(event);   // cùng transaction DB với dòng dedup
}
```
- Dòng dedup và thay đổi nghiệp vụ phải nằm trong **cùng transaction DB**; nếu không, crash giữa hai bước lại tạo lệch.
- `tryInsert` nên dựa vào unique constraint (insert ... on conflict do nothing, hoặc bắt `DataIntegrityViolationException` trong transaction riêng — xem mục 11), không dùng `exists` rồi `insert`.
- Thay thế: thao tác tự nhiên idempotent (`set status = 'RESERVED' where status = 'NEW'`), hoặc dùng version của aggregate để bỏ qua event cũ.

*Kafka và transaction với Spring* (spring-kafka 4.x; Boot 4.1.1 quản lý spring-kafka 4.1.1):
- Đặt `spring.kafka.producer.transaction-id-prefix` → Boot cấu hình `KafkaTransactionManager` và gắn vào listener container. Mỗi instance phải có prefix khác nhau.
- Khi `KafkaTemplate.send()` chạy trong method `@Transactional` do transaction manager DB quản lý, Kafka transaction được **đồng bộ** theo: DB commit trước, Kafka commit sau. Nếu commit Kafka fail sau khi DB đã commit, exception được ném cho caller và ứng dụng phải tự bù trừ. Đây là "best effort 1PC", không phải XA.
- Template transactional mà gọi ngoài transaction sẽ ném `IllegalStateException` (trừ khi bật `allowNonTransactional`).
- Exactly-once đầu-cuối chỉ có với luồng *consume từ Kafka → xử lý → produce vào Kafka*: offset và record output được commit trong cùng Kafka transaction; consumer phía sau phải đọc với `isolation.level=read_committed` (Boot: `spring.kafka.consumer.isolation-level`, Boot 4.1 mặc định `read_uncommitted`). Mọi side effect ngoài Kafka (ghi DB, gọi HTTP) vẫn có thể lặp lại → vẫn cần consumer idempotent.
- `ChainedKafkaTransactionManager` đã deprecated từ spring-kafka 2.7 (không dùng cho code mới); docs khuyên dùng `KafkaTransactionManager` ở container và `@Transactional` trên listener cho transaction DB.

*Xử lý lỗi consumer Kafka* (docs spring-kafka, mục annotation error handling):
- Error handler mặc định của container là `DefaultErrorHandler` với `FixedBackOff(0L, 9)`: thử tổng cộng 10 lần liên tiếp, không có khoảng nghỉ, rồi **log ERROR và bỏ qua record**. Nếu không cấu hình recoverer, record coi như mất.
- Một số exception bị coi là không retry được và đi thẳng tới recoverer: `DeserializationException`, `MessageConversionException`, `ConversionException`, `MethodArgumentResolutionException`, `NoSuchMethodException`, `ClassCastException`. Thêm exception nghiệp vụ vào danh sách này bằng `addNotRetryableExceptions(...)`.
- Dead letter topic: `new DefaultErrorHandler(new DeadLetterPublishingRecoverer(kafkaTemplate), new FixedBackOff(1000L, 2L))` nghĩa là 3 lần thử, cách nhau 1 giây, sau đó gửi record sang DLT. Mặc định DLT tên `<topic>-dlt` và dùng **cùng partition** với record gốc, nên DLT phải có ít nhất bằng số partition của topic gốc. Header `KafkaHeaders.DLT_*` mang topic, partition, offset gốc và exception. Các phiên bản cũ dùng hậu tố khác, nên kiểm tra tên thực tế khi nâng cấp.
- Poison pill (payload không deserialize được): bọc deserializer bằng `ErrorHandlingDeserializer`, nếu không consumer lỗi ngay trong `poll()` và kẹt ở partition đó.
- Với Boot: khai báo một bean `CommonErrorHandler` (hoặc `AfterRollbackProcessor` khi dùng Kafka transaction), Boot sẽ gắn nó vào container factory tự cấu hình (`KafkaAnnotationDrivenConfiguration`, Boot 4.1.1).
- Mặc định backoff handler treo thread consumer cho hết thời gian chờ, nên retry chặn partition. Nếu tổng backoff dài hơn `max.poll.interval.ms`, dùng `ContainerPausingBackOffHandler` (tạm dừng container thay vì treo thread) hoặc retry không chặn qua retry topic (`@RetryableTopic`, không hỗ trợ batch listener).

*Vì sao tránh XA/JTA:* cần transaction coordinator và broker/DB hỗ trợ XA (nhiều broker và dịch vụ cloud không hỗ trợ); commit 2 pha giữ lock lâu hơn và chậm hơn; coordinator là điểm hỏng mà khi sự cố để lại transaction "in-doubt" phải xử lý tay; không áp dụng được cho HTTP call sang service khác.

*Saga:*
- **Orchestration**: một orchestrator (service hoặc workflow engine) gửi lệnh tới từng bước và giữ trạng thái saga trong DB. Dễ quan sát và xử lý timeout/bù trừ; orchestrator thành điểm tập trung logic.
- **Choreography**: mỗi service phản ứng với event của service trước. Ít coupling lúc triển khai; luồng tổng thể ẩn trong nhiều service, khó debug, dễ tạo vòng event.
- Bước bù trừ (`CancelReservation`, `RefundPayment`) cũng phải idempotent và có thể retry; saga không có isolation → cần trạng thái trung gian ("PENDING") và xử lý khi người khác đọc dữ liệu chưa hoàn tất.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Outbox + polling | Mặc định cho đa số hệ thống; không muốn thêm hạ tầng | Độ trễ bằng chu kỳ poll; tải query lên DB; phải dọn bảng outbox |
| Outbox + CDC (Debezium) | Throughput cao, độ trễ thấp, đã vận hành Kafka Connect | Thêm hạ tầng (Connect, connector, quyền đọc log DB); vận hành phức tạp |
| Spring Modulith event registry | Monolith module hóa, cần listener nội bộ đáng tin cậy, externalize đơn giản | Externalization gốc không phải outbox đầy đủ; phụ thuộc thêm một dự án |
| Gửi sau commit (`AFTER_COMMIT`) không outbox | Message mất cũng chấp nhận được (thông báo, cache warm) | Mất message khi crash/broker down |
| Kafka transaction (read-process-write) | Stream processing thuần Kafka | Chỉ exactly-once trong Kafka; thêm độ trễ; cấu hình `transactional.id` theo instance |
| XA/JTA | Hệ thống legacy bắt buộc, tất cả tài nguyên hỗ trợ XA | Chậm, phức tạp, in-doubt transaction, không áp dụng cho HTTP |
| Saga orchestration | Luồng nhiều bước, cần quan sát và timeout rõ ràng | Orchestrator là thành phần phải vận hành, tập trung logic |
| Saga choreography | Ít bước, service độc lập | Luồng khó theo dõi; khó thay đổi thứ tự bước |

**Pitfalls trong production**
- Consumer nhận event "OrderCreated" nhưng không tìm thấy order → send Kafka trong transaction, trước khi DB commit (hoặc DB rollback sau đó) → outbox, hoặc tối thiểu gửi `AFTER_COMMIT` và chấp nhận rủi ro mất.
- Thỉnh thoảng thiếu message sau khi deploy/restart → gửi sau commit trên thread khác, pod bị kill trước khi gửi → outbox hoặc event publication registry có republish.
- Trừ kho hai lần sau khi consumer rebalance → at-least-once, offset chưa commit khi xử lý xong → bảng dedup trong cùng transaction với thao tác nghiệp vụ.
- `ProducerFencedException` hoặc producer bị chặn khi scale nhiều instance → dùng cùng `transaction-id-prefix` cho mọi instance → prefix duy nhất theo instance (ví dụ gắn hostname/pod name).
- Consumer thấy record của transaction Kafka đã abort → consumer để `isolation-level` mặc định `read_uncommitted` → đặt `read_committed` khi producer dùng transaction.
- Bảng outbox phình to, query poll chậm dần → không dọn bản ghi đã gửi, thiếu index trên `published_at`/`created_at` → job dọn theo lô, index phù hợp (tương tự `completion-mode` DELETE/ARCHIVE của Modulith).
- Thứ tự event bị đảo ở consumer → relay gửi song song, hoặc key không theo aggregate → dùng aggregate id làm message key, relay giữ thứ tự theo aggregate.

**Liên hệ với demo**
Demo là một service đơn, không có broker nên không có dual-write. Nếu thêm "gửi event ProductCreated sang Kafka", đừng gọi `kafkaTemplate.send()` trong `ProductService.create` (method `@Transactional`); ghi outbox cùng transaction với insert product, hoặc dùng event publication registry của Modulith.

**Câu hỏi kiểm tra**
- **H:** Đặt `kafkaTemplate.send()` trong `@TransactionalEventListener(phase = AFTER_COMMIT)` có giải quyết dual-write không?
  **Đ:** Không. Nó chỉ tránh gửi message cho dữ liệu bị rollback; nếu process crash hoặc broker lỗi sau commit, message mất. Cần outbox (hoặc registry lưu trong cùng transaction) để có thể gửi lại.
- **H:** Đã bật Kafka transaction và `read_committed`, consumer có cần idempotent nữa không?
  **Đ:** Có, nếu consumer ghi DB hoặc gọi hệ thống ngoài. Exactly-once chỉ bao offset và record trong Kafka; ghi DB có thể lặp lại khi xử lý lại sau lỗi hoặc rebalance.
- **H:** Khi nào chọn saga orchestration thay vì choreography?
  **Đ:** Khi luồng có nhiều bước, cần timeout, bù trừ và quan sát trạng thái tập trung. Choreography hợp với luồng ngắn, ít bước, nơi các service chỉ cần phản ứng độc lập.

### 15. Caching

**Tóm tắt**
- Spring Cache là **abstraction**: `@Cacheable` / `@CachePut` / `@CacheEvict` qua AOP proxy (`CacheInterceptor`), còn lưu ở đâu, TTL, kích thước, eviction là việc của **provider** (Caffeine, Redis...). Spring không có thuộc tính TTL trên annotation.
- Cache là một bản sao dữ liệu **cố ý cũ**. Trước khi thêm cache, trả lời: chấp nhận cũ bao lâu, ai invalidate, chuyện gì xảy ra khi cache down.
- Key mặc định chỉ dựa vào **tham số**, không gồm tên method. Hai method dùng chung tên cache với cùng tham số sẽ đè lên nhau.
- `sync = true` chỉ chống stampede **trong một JVM**; nhiều instance vẫn cùng lúc gọi DB khi key hết hạn.
- Evict trong transaction chưa commit → request khác nạp lại dữ liệu cũ vào cache. Dùng cache transaction-aware (`TransactionAwareCacheManagerProxy`) hoặc evict sau commit.
- Cache DTO bất biến, không cache entity JPA. Self-invocation bỏ qua cache như bỏ qua `@Transactional` (xem mục 4).

**Cơ chế bên dưới**

```java
@Configuration
@EnableCaching
class CacheConfig { }

@Service
class ProductQueryService {
    @Cacheable(cacheNames = "product-by-sku", key = "#sku", unless = "#result == null")
    public ProductDto findBySku(String sku) { ... }

    @CachePut(cacheNames = "product-by-sku", key = "#result.sku()")
    public ProductDto update(UpdateProductCommand cmd) { ... }

    @CacheEvict(cacheNames = "product-by-sku", key = "#sku")
    public void delete(String sku) { ... }
}
```
- `@Cacheable`: có key trong cache → trả về, **không gọi method**; không có → gọi method rồi lưu kết quả. `Optional` được unwrap tự động (rỗng → lưu `null`).
- `@CachePut`: luôn gọi method rồi ghi kết quả vào cache. Không đặt `@CachePut` và `@Cacheable` cùng cache trên cùng method.
- `@CacheEvict`: mặc định evict **sau khi** method chạy thành công; `beforeInvocation = true` evict trước và bất kể kết quả; `allEntries = true` xóa cả cache (không dùng chung với `key`).
- `condition` đánh giá **trước** khi gọi method (không thấy `#result`); `unless` đánh giá **sau**, dùng được `#result` để từ chối lưu.
- `@Caching` gom nhiều thao tác; `@CacheConfig` ở class đặt `cacheNames`/`keyGenerator` mặc định.

*Key generation* (`SimpleKeyGenerator`): không tham số → `SimpleKey.EMPTY`; một tham số khác null, không phải mảng → chính tham số đó; còn lại → `SimpleKey` của mọi tham số. Hệ quả:
- `findById(Long id)` và `findStockById(Long id)` cùng cache `"products"` sẽ trả nhầm kiểu dữ liệu cho nhau → mỗi loại truy vấn một tên cache, hoặc key SpEL rõ ràng.
- Tham số làm key phải có `equals`/`hashCode` đúng và ổn định (record là lựa chọn tốt); với cache phân tán còn phải serialize được và biểu diễn chuỗi ổn định.
- Không đưa tham số không ảnh hưởng kết quả (ví dụ `Locale` bỏ qua, request id) vào key — tỉ lệ hit về 0.

*`sync = true`* dùng `Cache.get(key, valueLoader)` để chỉ một thread nạp giá trị cho một key. Javadoc ghi rõ đây là *hint* và provider có thể không hỗ trợ đồng bộ thực sự. Giới hạn: không dùng được `unless`, chỉ một cache, không kết hợp thao tác cache khác. Nó không đồng bộ giữa các instance. Với key "nóng" trên nhiều node, cân nhắc refresh trước khi hết hạn (Caffeine `refreshAfterWrite`), TTL có jitter, hoặc lock phân tán.

*Provider và cấu hình trong Boot* (cần `spring-boot-starter-cache`; tên property dưới đây khớp `CacheProperties` của Boot 4.1):
```properties
spring.cache.type=caffeine
spring.cache.cache-names=product-by-sku,category-tree
spring.cache.caffeine.spec=maximumSize=10000,expireAfterWrite=10m,recordStats
# Redis
spring.cache.redis.time-to-live=10m
spring.cache.redis.key-prefix=catalog::
spring.cache.redis.enable-statistics=true
```
- `spring.cache.caffeine.spec` áp cho mọi cache; muốn TTL khác nhau theo cache, khai báo `CaffeineCacheManager` và `registerCustomCache(...)` (hoặc `RedisCacheManager` với cấu hình theo từng cache).
- Không đặt `spring.cache.type` và không có provider nào → Boot dùng `ConcurrentMapCacheManager`: không TTL, không giới hạn kích thước → rò rỉ bộ nhớ ở production.
- Redis: auto-configuration của Boot đặt serializer giá trị là `JdkSerializationRedisSerializer` (key là String) — dễ vỡ khi đổi class và khó đọc. Nên dùng serializer JSON (khai báo bean `org.springframework.data.redis.cache.RedisCacheConfiguration` riêng; khi đó `time-to-live`, `key-prefix`, `cache-null-values` trong `spring.cache.redis.*` không còn được áp, phải tự cấu hình trong bean đó) và kiểm soát việc đổi cấu trúc DTO (đổi DTO → đổi key prefix hoặc version trong tên cache).

*Cache trong transaction:*
```
T1: update(product) chạy trong transaction → UPDATE ... → @CacheEvict chạy khi method return, nhưng transaction chưa commit
    (method được gọi từ một transaction bên ngoài, hoặc cache advice nằm bên trong transaction advice)
T2: findBySku() → cache miss → đọc DB (T1 chưa commit → vẫn thấy dữ liệu CŨ) → put vào cache
T1: commit                        → cache giữ giá trị cũ tới khi hết TTL
```
- `TransactionAwareCacheManagerProxy` (spring-context-support) bọc `CacheManager`; `put`/`evict`/`clear` được hoãn tới **after-commit** nếu có transaction synchronization, rollback thì bỏ. `putIfAbsent`/`evictIfPresent` thì chạy ngay.
- `CaffeineCacheManager` không có cờ transaction-aware nên phải bọc proxy; `RedisCacheManager` (Spring Data Redis) có tùy chọn transaction-aware trong builder.
- Vẫn còn cửa sổ nhỏ: T2 đọc DB cũ trước commit rồi put sau khi T1 đã evict → giá trị cũ. TTL ngắn là lưới an toàn bắt buộc; dữ liệu cần chính xác tuyệt đối (tồn kho, số dư) không nên cache.

*Cache DTO, không cache entity:* entity có lazy proxy/collection → `LazyInitializationException` khi đọc từ cache sau khi session đóng (xem mục 9); Caffeine trả về **cùng một object** cho mọi thread → caller sửa entity là sửa dữ liệu trong cache; entity từ cache là detached, `save()` lại dễ ghi đè dữ liệu mới hơn. Cache record/DTO bất biến. Cần cache mức entity thì dùng second-level cache của Hibernate (xem mục 10), vốn được Hibernate invalidate khi ghi qua ORM.

*Thứ tự advice:* khi method có cả `@Transactional` và `@Cacheable`, cache hit có thể trả về mà không mở transaction, tùy thứ tự advisor (xem mục 4). Cách rõ ràng nhất: đặt cache ở một bean facade gọi service transactional.

*Lỗi provider:* `CacheErrorHandler` mặc định (`SimpleCacheErrorHandler`) ném lại exception → Redis down làm hỏng request dù DB vẫn ổn. Cân nhắc `LoggingCacheErrorHandler` (cấu hình qua `CachingConfigurer`) để cache lỗi thì rơi về DB, kèm timeout ngắn cho client Redis.

*Observability:* Micrometer `CacheMeterBinder` xuất `cache.gets` (tag `result` hit/miss), `cache.puts`, `cache.evictions`, `cache.size`. Boot Actuator chỉ tự bind metric cho các cache có sẵn lúc khởi động; cache tạo động (on the fly) phải đăng ký thủ công qua bean `CacheMetricsRegistrar`. Caffeine cần `recordStats`, Redis cần `enable-statistics` thì mới có số liệu hit/miss. Endpoint `/actuator/caches` liệt kê và xóa cache.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Local (Caffeine) | Dữ liệu đọc nhiều, nhỏ, chấp nhận mỗi node lệch nhau trong TTL | Không nhất quán giữa node; evict chỉ có hiệu lực trên node hiện tại; tốn heap |
| Distributed (Redis) | Nhiều instance cần thấy cùng dữ liệu, evict toàn cụm, dữ liệu lớn hơn heap | Network hop và serialization mỗi lần đọc; Redis là dependency có thể down |
| Hai tầng (Caffeine + Redis) | Key rất nóng, cần độ trễ thấp và dùng chung | Phức tạp: invalidate hai tầng (pub/sub), khó lý giải độ cũ |
| TTL ngắn, không evict chủ động | Dữ liệu chấp nhận cũ vài giây–phút (catalog, config) | Luôn có độ trễ cập nhật; đơn giản và bền vững |
| Evict khi ghi (`@CacheEvict`, transaction-aware) | Cần cập nhật nhanh sau khi ghi | Phải bao phủ mọi đường ghi (kể cả batch, SQL tay, service khác) |
| Second-level cache Hibernate | Entity tham chiếu ít đổi, đọc theo id | Chỉ đúng khi mọi thay đổi đi qua Hibernate; cấu hình phức tạp |
| Không cache, tối ưu query/index | Dữ liệu cần chính xác, hoặc chưa đo thấy DB là nút thắt | Không có — nên là lựa chọn mặc định trước khi đo |

**Pitfalls trong production**
- `@Cacheable` không có tác dụng → gọi từ method khác cùng class (self-invocation), method `private`/`final`, hoặc quên `@EnableCaching` → tách bean, kiểm tra proxy (xem mục 4).
- `ClassCastException` khi đọc cache → hai method khác kiểu trả về dùng chung tên cache với cùng key → tên cache riêng cho mỗi loại truy vấn.
- Heap tăng dần tới OOM → `ConcurrentMapCacheManager` mặc định (không giới hạn), hoặc key chứa giá trị không giới hạn (query tự do, trang phân trang) → provider có `maximumSize`, xem lại key.
- Sau khi cập nhật vẫn đọc dữ liệu cũ tới hết TTL → evict trước commit, hoặc đường ghi khác (batch, service khác) không evict → cache transaction-aware, TTL làm lưới an toàn, event invalidate liên service.
- Chỉ node xử lý request ghi thấy dữ liệu mới → dùng Caffeine trên nhiều instance → chấp nhận TTL, chuyển Redis, hoặc broadcast invalidate.
- DB quá tải mỗi khi key nóng hết hạn hoặc sau deploy (cache lạnh) → stampede giữa nhiều node → `refreshAfterWrite`, TTL có jitter, warm-up có kiểm soát.
- Redis chậm/down kéo sập toàn bộ API → error handler mặc định ném lại, không timeout → `LoggingCacheErrorHandler`, timeout ngắn, circuit breaker.
- `SerializationException` sau deploy phiên bản mới → cấu trúc DTO trong Redis thay đổi → version hóa tên cache/key prefix khi đổi DTO.

**Liên hệ với demo**
Demo không dùng cache. Nếu thêm cache cho `GET /api/products/{id}`: đặt `@Cacheable` trên `ProductService.getById`, cache DTO response (kết quả của `toResponse`), không cache entity; key là `id`; Caffeine có `maximumSize` và `expireAfterWrite` ngắn. Khi sau này có endpoint update/delete, evict qua cache transaction-aware. Không cache `GET /api/products` (nhiều tổ hợp page/size/sort, tỉ lệ hit thấp, khó invalidate). Không cache `existsBySku` cho kiểm tra trùng: đó vẫn là race, và unique constraint của DB mới là bảo vệ thật (xem mục 11).

**Câu hỏi kiểm tra**
- **H:** `@Cacheable(sync = true)` có ngăn được 20 pod cùng truy vấn DB khi một key nóng hết hạn không?
  **Đ:** Không. `sync` chỉ đồng bộ các thread trong một JVM (và còn tùy provider). Giữa các pod cần refresh trước khi hết hạn, TTL có jitter hoặc lock phân tán.
- **H:** Vì sao `@CacheEvict` trên method `@Transactional` vẫn có thể để lại dữ liệu cũ trong cache?
  **Đ:** Evict chạy khi method return, trước khi transaction bên ngoài commit; request khác cache miss, đọc DB (chưa thấy thay đổi) và put giá trị cũ. Dùng cache transaction-aware để evict sau commit, kèm TTL làm lưới an toàn.
- **H:** Vì sao không nên cache entity JPA trong Caffeine?
  **Đ:** Entity có lazy association sẽ lỗi khi truy cập ngoài session; Caffeine chia sẻ cùng instance giữa các thread nên sửa entity là sửa cache; entity detached `save()` lại có thể ghi đè dữ liệu mới. Cache DTO bất biến.

---

## Phần III — Spring Boot bên trong, Web và Security

### 16. Auto-configuration hoạt động thế nào

**Tóm tắt**
- `@SpringBootApplication` = `@SpringBootConfiguration` + `@EnableAutoConfiguration` + `@ComponentScan` (kèm hai exclude filter: `TypeExcludeFilter`, `AutoConfigurationExcludeFilter`). Không có phép màu nào khác.
- `@EnableAutoConfiguration` import `AutoConfigurationImportSelector`, một `DeferredImportSelector`. Nó đọc danh sách class từ `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` của mọi jar trên classpath. Từ Boot 3, `spring.factories` không còn dùng để khai báo auto-configuration.
- Mỗi auto-configuration là một `@Configuration` có điều kiện (`@ConditionalOn...`). "User bean thắng" vì auto-config được xử lý SAU user config và thường gắn `@ConditionalOnMissingBean`.
- Boot 4 tách auto-configuration thành nhiều module nhỏ theo công nghệ (`spring-boot-webmvc`, `spring-boot-jdbc`, `spring-boot-jackson`...), package gốc `org.springframework.boot.<technology>`. Thiếu starter nghĩa là auto-config không có trên classpath, và tính năng "biến mất" mà không báo lỗi.
- Công cụ debug số một: `--debug` / `debug=true` để in CONDITIONS EVALUATION REPORT; nếu có Actuator thì dùng endpoint `/actuator/conditions`.

**Cơ chế bên dưới**

Trình tự khi context refresh:
1. `ConfigurationClassPostProcessor` parse class chính, chạy component scan, đăng ký bean definition của user.
2. Vì là `DeferredImportSelector`, `AutoConfigurationImportSelector` chạy sau khi toàn bộ user `@Configuration` đã được parse. Nó nạp candidate qua `ImportCandidates`, rồi bỏ các class nằm trong `exclude`, `excludeName` hoặc property `spring.autoconfigure.exclude`.
3. Lọc sớm bằng các `AutoConfigurationImportFilter` (đăng ký trong `spring.factories` của `spring-boot-autoconfigure`: `OnClassCondition`, `OnBeanCondition`, `OnWebApplicationCondition`). Bước này dùng metadata sinh lúc build (`spring-autoconfigure-metadata.properties`) nên không cần load class. Nhờ vậy startup không phải nạp hàng trăm class auto-config.
4. Sắp xếp theo `@AutoConfiguration(before/after/beforeName/afterName)` và `@AutoConfigureOrder`, rồi đánh giá condition đầy đủ cho từng class và từng `@Bean` method. Thứ tự này chỉ quyết định thứ tự ĐĂNG KÝ bean definition, không quyết định thứ tự tạo bean (cái đó do dependency giữa các bean).

Ví dụ thật từ Boot 4.0.5 (`org.springframework.boot.webmvc.autoconfigure.WebMvcAutoConfiguration`):

```java
@AutoConfiguration(after = { DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class },
        afterName = "org.springframework.boot.validation.autoconfigure.ValidationAutoConfiguration")
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
public final class WebMvcAutoConfiguration { ... }
```

Đọc được gì: chỉ áp dụng cho servlet app, khi có Spring MVC trên classpath, và khi user CHƯA khai báo `WebMvcConfigurationSupport`. Đó là lý do `@EnableWebMvc` tắt toàn bộ MVC auto-config của Boot. `afterName` dùng String để không phụ thuộc cứng vào module validation. Ở Boot 4 nhiều auto-config class là `final`, nên không thể kế thừa để "sửa".

Các condition hay gặp (package `org.springframework.boot.autoconfigure.condition`): `@ConditionalOnClass`/`OnMissingClass`, `@ConditionalOnBean`/`OnMissingBean`/`OnSingleCandidate`, `@ConditionalOnProperty` (mặc định match khi property tồn tại và khác `false`; có `havingValue`, `matchIfMissing`), `@ConditionalOnBooleanProperty`, `@ConditionalOnWebApplication`, `@ConditionalOnResource`, `@ConditionalOnThreading` (platform/virtual), `@ConditionalOnCloudPlatform`, `@ConditionalOnExpression`.

Vì sao `@ConditionalOnMissingBean` đáng tin ở auto-config nhưng không đáng tin ở user config: condition được đánh giá trên những bean definition đã đăng ký TẠI THỜI ĐIỂM đó. Auto-config luôn chạy sau user config nên thấy đủ bean của user. Giữa hai user config với nhau thì thứ tự không được đảm bảo. Docs khuyến nghị chỉ dùng `@ConditionalOnBean`/`OnMissingBean` trong auto-configuration.

Exclude ở Boot 4 phải dùng package mới:

```java
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.jdbc.autoconfigure.DataSourceAutoConfiguration;

@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
public class App { }
// hoặc: spring.autoconfigure.exclude=org.springframework.boot.jdbc.autoconfigure.DataSourceAutoConfiguration
```

**Boot 4 modularization — ảnh hưởng khi nâng cấp**
- Quy ước: module `spring-boot-<technology>`, package gốc `org.springframework.boot.<technology>`, starter `spring-boot-starter-<technology>`, test starter `spring-boot-starter-<technology>-test`. Ví dụ đã kiểm chứng: `org.springframework.boot.jdbc.autoconfigure.DataSourceAutoConfiguration`, `org.springframework.boot.hibernate.autoconfigure.HibernateJpaAutoConfiguration`, `org.springframework.boot.jackson.autoconfigure.JacksonAutoConfiguration`, `org.springframework.boot.security.autoconfigure.SecurityAutoConfiguration`.
- `spring-boot-autoconfigure` chỉ còn phần lõi (task execution/scheduling, `ConfigurationPropertiesAutoConfiguration`, AOP, JMX, SSL...). Auto-config actuator của từng công nghệ cũng nằm trong module công nghệ đó.
- Công nghệ trước đây chỉ cần thư viện third-party giờ cần starter riêng: Flyway cần `spring-boot-starter-flyway`, Liquibase cần `spring-boot-starter-liquibase`. Nếu chỉ thêm `flyway-core` như Boot 3 thì migration KHÔNG chạy và không có lỗi.
- Đường nâng cấp hai bước: dùng `spring-boot-starter-classic` / `spring-boot-starter-test-classic` để có lại classpath "đầy đủ như cũ", sửa import, rồi bỏ classic và thêm đúng các starter còn thiếu.
- Nhóm tự viết starter: Migration Guide khuyến cáo không hỗ trợ cả Boot 3 và Boot 4 trong cùng một artifact (xem mục 17).

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Khai báo bean cùng type để override | Muốn thay hẳn một bean (ví dụ `JsonMapper`, `RestClient.Builder`) | Mất các customization mà auto-config áp lên bean đó; nên ưu tiên customizer nếu có |
| Customizer / property (`*Customizer`, `spring.*`) | Chỉ muốn chỉnh một phần hành vi mặc định | Phải tìm đúng hook; vài hành vi chỉ chỉnh được bằng cách thay bean |
| `exclude` auto-configuration | Loại cả một tính năng (ví dụ DataSource trong app không có DB) | Dễ quên khi cần lại; exclude bằng class cần class đó có trên classpath (Boot 4 tách auto-config ra từng module), nếu không chắc thì dùng `excludeName` |
| `@EnableWebMvc` / tự cấu hình toàn phần | Rất hiếm, khi cần kiểm soát 100% MVC | Mất message converter, static resource, cấu hình `spring.mvc.*` của Boot |
| Classic starters khi lên Boot 4 | Bước trung gian để chạy được nhanh | Classpath to, che giấu dependency thiếu; phải gỡ sau |

**Pitfalls trong production**
- App chạy nhưng tính năng im lặng không hoạt động (Flyway không migrate, thiếu metrics) → sau khi lên Boot 4 thiếu starter của công nghệ → xem CONDITIONS EVALUATION REPORT, đối chiếu bảng starter trong Migration Guide.
- Thêm `@EnableWebMvc` "cho chắc" → cấu hình Jackson, static resource, `spring.mvc.*` bị bỏ qua → xóa `@EnableWebMvc`, dùng `WebMvcConfigurer`.
- Class auto-config của thư viện nội bộ bị component scan (cùng package gốc với app) → condition đánh giá sai thứ tự, bean trùng → đặt auto-config ở package riêng, chỉ nạp qua file `.imports`.
- `@Bean` trả về interface trong khi condition dựa trên concrete type → `@ConditionalOnMissingBean` không nhận ra bean đã có → khai báo return type cụ thể nhất có thể.
- User thêm một bean thứ hai cùng type (ví dụ `DataSource` thứ hai) → auto-config lùi lại hoặc lỗi "required a single bean" → đánh dấu `@Primary` hoặc tự cấu hình toàn bộ nhóm bean đó.

**Liên hệ với demo**
Demo dùng `spring-boot-starter-webmvc` (tên mới ở Boot 4; `spring-boot-starter-web` đã deprecated). Khi thêm Flyway/Actuator/Security phải thêm đúng starter, không thêm thư viện trần.

**Câu hỏi kiểm tra**
- **H:** Vì sao user khai báo một bean `DataSource` thì Boot không tạo DataSource nữa, mà không cần cấu hình gì thêm?
  **Đ:** Auto-config được import qua `DeferredImportSelector` nên được xử lý sau user config. Bean `DataSource` của Boot có điều kiện "chưa có bean cùng loại"; lúc đánh giá, bean definition của user đã có nên Boot bỏ qua.
- **H:** Một dev dùng `@ConditionalOnMissingBean` trong `@Configuration` của ứng dụng và thấy lúc được lúc không. Vì sao?
  **Đ:** Thứ tự xử lý giữa các user configuration không được đảm bảo; condition chỉ thấy các bean đã đăng ký tới thời điểm đó. Condition kiểu bean chỉ đáng tin trong auto-configuration.
- **H:** Lên Boot 4 xong, test pass nhưng staging thiếu bảng mới. Nghi ngờ gì đầu tiên?
  **Đ:** Flyway/Liquibase không chạy vì thiếu `spring-boot-starter-flyway`/`-liquibase`. Kiểm tra bằng `--debug` hoặc `/actuator/conditions`.
- **H:** Tại sao startup không phải load class của hàng trăm auto-config?
  **Đ:** `AutoConfigurationImportFilter` lọc sớm theo `OnClass`/`OnBean`/`OnWebApplication` bằng metadata sinh lúc build, loại candidate trước khi parse class.

### 17. Xây dựng starter nội bộ cho platform team

**Tóm tắt**
- Starter nội bộ là cách platform team "đóng gói quyết định": logging chuẩn, header tracing, error contract, client gọi service nội bộ, audit... Mục tiêu là mặc định đúng, nhưng team sản phẩm vẫn override được.
- Cấu trúc chuẩn gồm hai module: `acme-spring-boot` (auto-configuration + properties + API) và `acme-spring-boot-starter` (jar rỗng, chỉ gom dependency). Với starter nhỏ, không có tính năng tùy chọn, có thể gộp làm một module `acme-spring-boot-starter`.
- KHÔNG đặt tên bắt đầu bằng `spring-boot-` (kể cả khác groupId), KHÔNG dùng namespace property của Boot (`spring.*`, `server.*`, `management.*`). Dùng tiền tố của công ty.
- Mọi bean phải có điều kiện để người dùng thay được (`@ConditionalOnMissingBean`, `@ConditionalOnClass`, property bật/tắt). Test bằng `ApplicationContextRunner`, không cần `@SpringBootTest`.
- Starter là API công khai: đổi tên property, đổi bean, đổi hành vi mặc định đều là breaking change. Cần versioning và deprecation có kế hoạch.

**Cơ chế bên dưới**

Module autoconfigure:

```java
package com.acme.platform.audit.autoconfigure;

import org.springframework.boot.autoconfigure.AutoConfiguration;
import org.springframework.boot.autoconfigure.condition.*;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;

@AutoConfiguration
@ConditionalOnClass(AuditClient.class)
@ConditionalOnBooleanProperty(name = "acme.audit.enabled", matchIfMissing = true)
@EnableConfigurationProperties(AcmeAuditProperties.class)
public class AcmeAuditAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    DefaultAuditClient auditClient(AcmeAuditProperties props) {   // return type cụ thể
        return new DefaultAuditClient(props.endpoint(), props.timeout());
    }
}
```

Khai báo trong `src/main/resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`:

```
com.acme.platform.audit.autoconfigure.AcmeAuditAutoConfiguration
```

Properties (record + constructor binding, xem mục 18). Với record, mô tả property viết bằng Javadoc `@param` ở mức class để configuration processor đưa vào metadata:

```java
/**
 * @param endpoint URL của audit service.
 * @param timeout Timeout cho mỗi lần gửi audit event.
 */
@ConfigurationProperties("acme.audit")
public record AcmeAuditProperties(URI endpoint, @DefaultValue("2s") Duration timeout) { }
```

Những điểm bắt buộc:
- Dependency tới thư viện được auto-configure đánh dấu `optional` trong module autoconfigure. Không có thư viện thì `@ConditionalOnClass` không match và Boot lùi lại.
- Thêm `spring-boot-configuration-processor` vào `annotationProcessorPaths` của `maven-compiler-plugin` (3.12.0+), hoặc cấu hình `annotationProcessor` trong Gradle. Processor sinh `META-INF/spring-configuration-metadata.json` → IDE autocomplete và hiện mô tả. Kiểu dữ liệu nằm ở module khác thì đánh dấu `@ConfigurationPropertiesSource` (Boot 4) và chạy processor trong module đó.
- Thêm `spring-boot-autoconfigure-processor` để sinh `META-INF/spring-autoconfigure-metadata.properties`, giúp Boot lọc sớm auto-config không match (xem mục 16).
- Module starter phải phụ thuộc (trực tiếp hoặc gián tiếp) vào `spring-boot-starter`. Không kéo dependency tùy chọn vào starter.
- Auto-config không được bị component scan và không tự bật component scan; dùng `@Import` cụ thể.
- Deprecate hoặc đổi package một auto-config: thêm file `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.replacements` (`old.Class=new.Class`) để các `exclude`/`after` cũ vẫn chạy.

Test bằng `ApplicationContextRunner` (`org.springframework.boot.test.context.runner`):

```java
class AcmeAuditAutoConfigurationTests {
    private final ApplicationContextRunner runner = new ApplicationContextRunner()
            .withConfiguration(AutoConfigurations.of(AcmeAuditAutoConfiguration.class))
            .withPropertyValues("acme.audit.endpoint=http://audit");

    @Test void createsDefaultClient() {
        runner.run(ctx -> assertThat(ctx).hasSingleBean(DefaultAuditClient.class));
    }
    @Test void backsOffWhenUserDefinesClient() {
        runner.withBean(DefaultAuditClient.class, () -> mock(DefaultAuditClient.class))
              .run(ctx -> assertThat(ctx).hasSingleBean(DefaultAuditClient.class));
    }
    @Test void disabledByProperty() {
        runner.withPropertyValues("acme.audit.enabled=false")
              .run(ctx -> assertThat(ctx).doesNotHaveBean(DefaultAuditClient.class));
    }
    @Test void backsOffWithoutLibrary() {
        runner.withClassLoader(new FilteredClassLoader(AuditClient.class))
              .run(ctx -> assertThat(ctx).doesNotHaveBean(DefaultAuditClient.class));
    }
}
```

Khi cần context web thì dùng `WebApplicationContextRunner`. Có thể in condition report trong test bằng `.withInitializer(ConditionEvaluationReportLoggingListener.forLogLevel(LogLevel.INFO))`. Lưu ý `ApplicationContextRunner` không chạy được trong native image test.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Starter nhỏ, mỗi starter một mối quan tâm (audit, tracing header, error contract) | Mặc định nên chọn | Nhiều artifact hơn; cần BOM nội bộ để đồng bộ version |
| Starter "platform" gom mọi thứ | Gần như không bao giờ | Kéo dependency không cần, tăng startup và bề mặt tấn công, khó nâng cấp, team không biết cái gì đang được bật |
| Thư viện thường (không auto-config), team tự khai báo bean | Logic nghiệp vụ dùng chung, ít cấu hình hạ tầng | Mỗi team tự cấu hình, dễ lệch nhau |
| BOM nội bộ (import sau `spring-boot-dependencies`) | Tổ chức nhiều service | Phải duy trì ma trận tương thích với Boot |
| Hỗ trợ cả Boot 3 và Boot 4 trong một artifact | Không nên (Migration Guide khuyến cáo tránh) | Package/module khác nhau quá nhiều; nên tách nhánh major version theo Boot |

**Pitfalls trong production**
- Team override bean nhưng bean của starter vẫn được tạo → thiếu `@ConditionalOnMissingBean` hoặc `@Bean` trả về interface trong khi team khai báo concrete type khác → đặt condition trên mọi bean, return type cụ thể, có test "backs off".
- IDE không gợi ý property, typo `acme.audit.enbled` không ai phát hiện → quên configuration processor → thêm processor vào `annotationProcessorPaths`; property quan trọng thì validate (xem mục 18).
- Nâng Boot minor/major, starter nội bộ vỡ ở runtime (`NoSuchMethodError`, `ClassNotFoundException`) → starter compile với Boot khác version ứng dụng → release starter theo dòng Boot (ví dụ `2.x` cho Boot 3, `3.x` cho Boot 4), chạy CI trên ma trận version.
- Starter bật một filter/interceptor toàn cục làm thay đổi response của mọi service → hành vi xâm lấn bật mặc định → mặc định an toàn, có property tắt, ghi rõ trong README/changelog.
- Starter tự đăng ký property trong namespace `spring.*` → Boot phiên bản sau đổi nghĩa key → luôn dùng namespace riêng.

**Liên hệ với demo**
`ApiError` và `GlobalExceptionHandler` của demo là ứng viên tốt cho một starter "error contract" nội bộ, để mọi service trả cùng một format lỗi (xem mục 20).

**Câu hỏi kiểm tra**
- **H:** Tại sao dependency tới thư viện được auto-configure nên là `optional` trong module autoconfigure?
  **Đ:** Để module autoconfigure có thể có mặt mà không ép kéo thư viện. Thiếu thư viện thì `@ConditionalOnClass` không match và auto-config tự lùi; người dùng chọn thư viện qua starter.
- **H:** Làm sao chứng minh starter "để người dùng override được"?
  **Đ:** Test bằng `ApplicationContextRunner`: `withBean`/`withUserConfiguration` khai báo bean của người dùng rồi assert `hasSingleBean` và đó là bean của người dùng; thêm test tắt bằng property và test thiếu class bằng `FilteredClassLoader`.
- **H:** Đổi package của một auto-configuration trong starter có phải breaking change không? Giảm thiểu thế nào?
  **Đ:** Có, vì ứng dụng có thể đang `exclude` hoặc `after` theo tên class cũ. Dùng file `AutoConfiguration.replacements` để ánh xạ tên cũ sang mới, và chỉ liệt kê class mới trong `.imports`.

### 18. Externalized configuration nâng cao

**Tóm tắt**
- Dùng `@ConfigurationProperties` (ưu tiên record, immutable) cho mọi nhóm cấu hình có cấu trúc; `@Value` chỉ cho giá trị lẻ. Thêm `@Validated` để sai cấu hình thì app KHÔNG khởi động (fail-fast), thay vì lỗi lúc 2 giờ sáng.
- Nắm thứ tự precedence và quy tắc relaxed binding cho env var (`.` → `_`, bỏ `-`, viết hoa, index list `_0_`). Phần lớn sự cố "config không ăn" đến từ đây.
- `spring.config.import` là cửa ngõ để nạp thêm nguồn: `optional:`, `file:`, `classpath:`, `configtree:` (Kubernetes/Docker secrets), `env:`, và các loader của Spring Cloud (Vault...).
- Secret không nằm trong git, không nằm trong image. Nguồn đúng: secret manager (Vault, cloud secret manager) hoặc secret mount qua `configtree:`.
- Boot đọc config một lần lúc startup. Muốn đổi config lúc runtime thì hoặc restart (rolling deploy), hoặc chấp nhận độ phức tạp của Spring Cloud `@RefreshScope`.

**Cơ chế bên dưới**

Record + constructor binding + validation + conversion:

```java
import jakarta.validation.Valid;
import jakarta.validation.constraints.*;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.context.properties.bind.DefaultValue;
import org.springframework.util.unit.DataSize;
import org.springframework.validation.annotation.Validated;

@Validated
@ConfigurationProperties("inventory.client")
public record InventoryClientProperties(
        @NotNull URI baseUrl,
        @DefaultValue("2s") Duration connectTimeout,   // "500ms", "2s", "PT2S", hoặc số = mili giây
        @DefaultValue("5s") Duration readTimeout,
        @DefaultValue("1MB") DataSize maxResponseSize, // số trần = byte
        @Valid @DefaultValue Retry retry) {          // @DefaultValue rỗng: luôn có instance
    public record Retry(@Min(0) @Max(5) @DefaultValue("2") int maxAttempts) { }
}
```

- Đăng ký bằng `@EnableConfigurationProperties(...)` hoặc `@ConfigurationPropertiesScan`.
- Record/một constructor có tham số → constructor binding, không cần `@ConstructorBinding`. Muốn cascade validation vào object lồng nhau phải gắn `@Valid` trên field/component đó.
- Đơn vị mặc định: `Duration` là mili giây, `DataSize` là byte; đổi bằng `@DurationUnit` / `@DataSizeUnit`. Luôn ghi đơn vị trong giá trị (`30s`) để tránh nhầm.
- Boot 4.1: tham số `Optional` trong constructor binding được bind thành `Optional.empty()` thay vì `null`.

Relaxed binding từ env var: `spring.main.log-startup-info` → `SPRING_MAIN_LOGSTARTUPINFO`; `my.service[0].other` → `MY_SERVICE_0_OTHER`. Boot hạ chữ thường tên env var trước khi bind, nên key của `Map` bind từ env var sẽ thành chữ thường.

Import và multi-document:

```yaml
spring:
  config:
    import:
      - "optional:configtree:/etc/secrets/"         # mỗi file = một property
      - "optional:file:./local-override.yaml"
---
spring:
  config:
    activate:
      on-profile: "prod | staging"
      on-cloud-platform: "kubernetes"
# KHÔNG đặt spring.profiles.active trong document có activate.on-profile
inventory.client.read-timeout: 3s
```

- Giá trị từ file được import override file khai báo import. Mỗi location chỉ import một lần. Location không tồn tại mà không có `optional:` → `ConfigDataLocationNotFoundException`, app không start (đây là điều tốt cho config bắt buộc).
- Boot 4.1: chỉ định encoding khi import file properties: `classpath:import.properties[encoding=utf-8]` (mặc định vẫn là ISO-8859-1).
- `configtree:` biến cây thư mục thành property: file `/etc/secrets/db/password` với import `configtree:/etc/secrets/` → property `db.password`. Wildcard `configtree:/etc/config/*/` nạp mọi thư mục con.
- Profile groups: `spring.profiles.group.production[0]=proddb`, `...[1]=prodmq`. Tên profile từ Boot 3.x bị validate (chữ, số, `-_.+@`, bắt đầu/kết thúc bằng chữ hoặc số), tắt bằng `spring.profiles.validate=false`.

Precedence (thấp → cao, các mục chính theo docs Boot 4.1):
1. `SpringApplication.setDefaultProperties`
2. `@PropertySource` trên `@Configuration` (nạp muộn, quá trễ cho `logging.*`, `spring.main.*`)
3. Config data: `application.yml` trong jar → `application-{profile}.yml` trong jar → `application.yml` ngoài jar → `application-{profile}.yml` ngoài jar
4. `random.*`
5. OS environment variables
6. Java system properties (`-D`)
7. JNDI, ServletContext/ServletConfig init params
8. `SPRING_APPLICATION_JSON`
9. Command-line arguments (`--x=y`)
10. Trong test: `properties` của `@SpringBootTest`/slice, `@DynamicPropertySource`, `@TestPropertySource` (cao nhất)

Cùng một location có cả `.properties` và `.yml` thì `.properties` thắng.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Env var | Giá trị đơn giản theo môi trường, không nhạy cảm | Lộ qua `/proc`, `docker inspect`, crash dump, log của tool; khó quản lý khi nhiều |
| `configtree:` (K8s Secret/Docker secret mount) | Secret trên Kubernetes khi chưa có secret manager | Secret vẫn chỉ base64 trong etcd nếu không bật encryption at rest; xoay vòng cần restart pod |
| Vault / cloud secret manager qua Spring Cloud (`spring.config.import=vault://...`) | Tổ chức cần audit, xoay vòng, dynamic credentials | Thêm dependency hạ tầng lúc startup; phải xử lý khi secret manager không sẵn sàng |
| Restart để đổi config (rolling deploy) | Mặc định nên chọn | Đổi config mất vài phút; cần pipeline tốt |
| Spring Cloud `@RefreshScope` + `/actuator/refresh` hoặc Config Server | Cần đổi feature flag/threshold mà không deploy | Bean trong scope bị bọc proxy và tạo lại khi refresh; có thể thấy trạng thái nửa cũ nửa mới giữa các instance; không áp dụng cho bean hạ tầng (DataSource pool, web server); khó test |
| Feature flag service chuyên dụng | Bật/tắt tính năng theo user/phần trăm | Thêm hệ thống; nhưng tách được "config" khỏi "flag" |

Vì sao Boot không hot-reload: `Environment` được dựng một lần; bean đã đọc giá trị trong constructor/`@PostConstruct` không có cơ chế nhận giá trị mới; đổi giữa chừng những thứ như pool size hay URL DB sẽ làm trạng thái không nhất quán. Bất biến lúc runtime giúp dễ lý giải và phù hợp với container bất biến.

**Pitfalls trong production**
- Env var `SPRING_DATASOURCE_URL` không có tác dụng với property `my-app.dataSource.url` → tên env var sai quy tắc (có `-` hoặc camelCase) → dùng dạng canonical kebab-case trong code, env var theo quy tắc chữ hoa + `_`, bỏ `-`.
- Timeout `read-timeout: 30` hiểu nhầm là 30 giây, thực tế 30 ms → `Duration` mặc định mili giây → luôn viết `30s`; hoặc gắn `@DurationUnit`.
- Config sai (URL null) chỉ lộ ra ở request đầu tiên → không validate → `@Validated` + constraint trên properties để fail lúc startup; readiness probe không pass nên rolling deploy dừng lại.
- Secret lọt vào log hoặc `/actuator/env` → log toàn bộ properties lúc startup, hoặc mở endpoint env → không log config; giữ mặc định che giá trị của Actuator; không expose `env`/`configprops` ra ngoài.
- Thêm `spring.profiles.active` vào `application-prod.yml` → app không start (`InvalidConfigDataPropertyException`) → đặt profile active bằng env var/command-line; dùng profile group.
- `@PropertySource("classpath:x.yml")` không đọc được YAML, hoặc property `logging.*` trong đó không có hiệu lực → `@PropertySource` chỉ hỗ trợ properties và được nạp muộn → dùng `spring.config.import`.

**Liên hệ với demo**
Demo dùng `${DB_PASSWORD:}` với mặc định rỗng và `spring.profiles.active=${SPRING_PROFILES_ACTIVE:dev}` trong file. Production: không để default cho secret (thiếu thì phải fail), không mặc định profile `dev` trong artifact; nhóm tham số thành `@ConfigurationProperties` có `@Validated`.

**Câu hỏi kiểm tra**
- **H:** Pod trên Kubernetes có cả env var `INVENTORY_CLIENT_READTIMEOUT=3s` và `--inventory.client.read-timeout=10s` trong args. Giá trị nào thắng?
  **Đ:** Command-line argument (10s), vì nó đứng trên OS env var trong precedence.
- **H:** Vì sao nên dùng record cho `@ConfigurationProperties`?
  **Đ:** Immutable, không có setter nên không ai đổi được lúc runtime, binding qua constructor rõ ràng, kết hợp `@DefaultValue` và validation; tránh trạng thái nửa khởi tạo.
- **H:** `@RefreshScope` có làm DataSource đổi password ngay lập tức không?
  **Đ:** Không đáng tin. Refresh tạo lại bean trong scope ở lần truy cập sau, nhưng connection đang mở trong pool vẫn dùng credential cũ. Với credential xoay vòng, dùng dynamic secret có TTL kết hợp restart có kiểm soát, hoặc cơ chế xoay vòng ở tầng pool/driver.
- **H:** Khi nào nên dùng `optional:` và khi nào không?
  **Đ:** `optional:` cho nguồn chỉ có ở một số môi trường (file override local). Với nguồn bắt buộc (secret mount ở prod) thì bỏ `optional:` để app fail-fast nếu thiếu.

### 19. SpringApplication startup: lifecycle, extension points và tối ưu

**Tóm tắt**
- `SpringApplication.run()` không chỉ là "refresh context": nó dựng `Environment`, chạy `EnvironmentPostProcessor`, tạo context, gọi `ApplicationContextInitializer`, refresh, gọi runner, rồi mới báo ready. Mỗi bước có extension point riêng; chọn sai bước là nguồn của nhiều bug kiểu "property chưa có", "bean chưa có".
- Extension point chạy TRƯỚC khi có bean (EPP, initializer, listener của event sớm) phải đăng ký qua `META-INF/spring.factories`, không phải `@Component`.
- Việc khởi tạo lúc startup (warm cache, kiểm tra kết nối) đặt trong `ApplicationRunner`/`CommandLineRunner`, không đặt trong `@PostConstruct`, để gắn đúng với readiness.
- Đo trước khi tối ưu: `BufferingApplicationStartup` + endpoint `startup`, hoặc JFR. Các đòn bẩy lớn: bớt auto-config không cần, CDS/AOT cache của JVM, Spring AOT, và cuối cùng là GraalVM native image (đổi lấy closed-world).

**Cơ chế bên dưới**

Thứ tự event (theo docs Boot 4.1):

| Event | Khi nào | Có gì trong tay |
|---|---|---|
| `ApplicationStartingEvent` | Đầu `run()`, mới đăng ký listener/initializer | Chưa có `Environment`, chưa cấu hình logging |
| `ApplicationEnvironmentPreparedEvent` | `Environment` đã biết, chưa tạo context | Property sources đầy đủ (EPP chạy ở bước này) |
| `ApplicationContextInitializedEvent` | Context đã tạo, initializer đã chạy, chưa nạp bean definition | Context rỗng |
| `ApplicationPreparedEvent` | Bean definition đã nạp, sắp refresh | Chưa có bean singleton |
| (`WebServerInitializedEvent`, `ContextRefreshedEvent`) | Trong/cuối refresh | Web server đã sẵn sàng |
| `ApplicationStartedEvent` + `AvailabilityChangeEvent(LivenessState.CORRECT)` | Sau refresh, trước runner | Mọi singleton đã tạo |
| `ApplicationReadyEvent` + `AvailabilityChangeEvent(ReadinessState.ACCEPTING_TRAFFIC)` | Sau khi mọi runner chạy xong | Sẵn sàng nhận traffic |
| `ApplicationFailedEvent` | Có exception lúc startup | `FailureAnalyzer` in "APPLICATION FAILED TO START" |

Listener cho các event phát ra trước khi refresh (từ `ApplicationStartingEvent` tới `ApplicationPreparedEvent`) không thể là bean (context chưa có hoặc bean chưa được tạo). Đăng ký qua `SpringApplication.addListeners(...)` hoặc `spring.factories` với key `org.springframework.context.ApplicationListener`. Listener chạy trên cùng thread, không làm việc nặng.

Đăng ký extension point sớm (key đúng như `spring.factories` của chính Boot 4.0.5):

```properties
# META-INF/spring.factories
org.springframework.boot.EnvironmentPostProcessor=com.acme.platform.SecretsEnvironmentPostProcessor
org.springframework.context.ApplicationContextInitializer=com.acme.platform.TenantContextInitializer
org.springframework.boot.diagnostics.FailureAnalyzer=com.acme.platform.LicenseMissingFailureAnalyzer
```

- Boot 4 chuyển `EnvironmentPostProcessor` sang `org.springframework.boot.EnvironmentPostProcessor`. Interface cũ `org.springframework.boot.env.EnvironmentPostProcessor` bị deprecated từ 4.0.0, dự kiến xóa ở 4.2.0. Starter nội bộ cần cập nhật cả import lẫn key trong `spring.factories`.
- Chính Boot dùng EPP để nạp config data (`ConfigDataEnvironmentPostProcessor`), `random.*`, `SPRING_APPLICATION_JSON`. EPP của bạn có thể thêm `PropertySource` (ví dụ giải mã giá trị), dùng `Ordered`/`@Order` để chạy trước hoặc sau config data.
- `FailureAnalyzer` (thường kế thừa `AbstractFailureAnalyzer<T>`) biến exception thành "Description/Action" dễ đọc. Boot có sẵn analyzer cho port bị chiếm, bean trùng, vòng phụ thuộc, bind property lỗi... Starter nội bộ nên có analyzer cho lỗi cấu hình phổ biến của nó (xem mục 17).

Runner và readiness:

```java
@Component
@Order(1)
class WarmUpRunner implements ApplicationRunner {
    private final ProductCatalogCache cache;
    WarmUpRunner(ProductCatalogCache cache) { this.cache = cache; }

    @Override
    public void run(ApplicationArguments args) {
        cache.preload();   // readiness chưa ACCEPTING_TRAFFIC cho tới khi runner xong
    }
}
```

Exception trong runner làm startup thất bại (`ApplicationFailedEvent`). Runner chạy tuần tự trên main thread; runner dài thì startup dài.

Đo startup:

```java
var app = new SpringApplication(ProductApiApplication.class);
app.setApplicationStartup(new BufferingApplicationStartup(2048)); // org.springframework.boot.context.metrics.buffering
app.run(args);
```

Có Actuator thì endpoint `startup` trả JSON các `StartupStep` (bean nào tạo lâu). `FlightRecorderApplicationStartup` của Framework ghi step vào JFR để đối chiếu với GC, class loading.

Tối ưu ở tầng JVM và build:
- **CDS / AOT cache**: training run với `-Dspring.context.exit=onRefresh` trên jar đã extract; cache gắn với đúng bản build và JDK. Cú pháp theo phiên bản JDK và Dockerfile mẫu: xem mục 26.
- **Spring AOT trên JVM**: build có bước AOT (Maven: profile `-Pnative`), chạy với `-Dspring.aot.enabled=true`. Cấu hình `@Configuration` được "đóng băng" thành code sinh sẵn, bớt reflection và bớt đánh giá condition lúc startup. Có thể kết hợp với AOT cache.
- **GraalVM native image**: startup tính bằng mili giây, RAM thấp, đổi lại closed-world (tập bean và condition chốt lúc build) và phải có hint cho reflection/resource/proxy. Chi tiết và trade-off: xem mục 26.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Không tối ưu, JVM thường | Service chạy dài, deploy vài lần mỗi ngày | Startup vài giây tới vài chục giây; thường chấp nhận được |
| `spring.main.lazy-initialization=true` | Môi trường dev, test, hoặc CLI | Lỗi cấu hình lộ ra ở request đầu thay vì lúc startup; request đầu chậm; heap phải tính theo lúc "đã nóng". Docs khuyên không bật mặc định |
| CDS / AOT cache | Muốn giảm startup/memory mà không đổi code; container nhiều replica | Thêm bước training run trong pipeline; cache gắn với bản build và JDK |
| Spring AOT trên JVM | Muốn giảm thêm chi phí startup, chuẩn bị đường sang native | Hạn chế giống native về profile/condition lúc runtime |
| GraalVM native image | Serverless, scale-to-zero, CLI, sidecar cần RAM thấp | Build lâu, tốn RAM; thư viện dùng reflection cần hint; không có JIT như JVM nên throughput đỉnh có thể thấp hơn; debug khó hơn |

**Pitfalls trong production**
- Pod bị Kubernetes kill liên tục khi startup → liveness probe bắt đầu trước khi app start xong (warm-up dài) → dùng startup probe hoặc tăng ngưỡng; readiness dựa vào `ReadinessState`, không dựa vào "port đã mở".
- Nhận traffic khi cache chưa nạp → warm-up đặt trong listener `ApplicationReadyEvent` chạy async hoặc trong thread riêng → đặt trong `ApplicationRunner` để readiness chỉ chuyển sang ACCEPTING_TRAFFIC sau khi xong.
- `EnvironmentPostProcessor` viết như `@Component` và không bao giờ chạy → EPP phải đăng ký trong `spring.factories` → kiểm tra key đúng package Boot 4 (`org.springframework.boot.EnvironmentPostProcessor`).
- Bật lazy initialization, lỗi bean thiếu cấu hình chỉ lộ ở request đầu tiên sau deploy → lazy che lỗi startup → không bật ở prod, hoặc `@Lazy(false)` cho bean quan trọng.
- Native image bật/tắt tính năng qua property không có tác dụng → condition đã được đánh giá lúc build → chốt tập bean lúc build; dùng flag trong code nghiệp vụ thay vì `@ConditionalOnProperty`.

**Liên hệ với demo**
Demo khởi động vài giây, không cần tối ưu. Nếu chạy trên Kubernetes với nhiều replica, bước hợp lý đầu tiên là thêm Actuator probes và CDS/AOT cache, chưa cần native.

**Câu hỏi kiểm tra**
- **H:** Muốn giải mã các property dạng `ENC(...)` trước khi mọi bean đọc chúng, gắn logic vào đâu?
  **Đ:** `EnvironmentPostProcessor` đăng ký trong `META-INF/spring.factories`, thêm một `PropertySource` ưu tiên cao, chạy sau `ConfigDataEnvironmentPostProcessor` để đã có giá trị từ file. `@PostConstruct` hay `BeanFactoryPostProcessor` là quá muộn cho các bean hạ tầng và cho binding sớm.
- **H:** `ApplicationStartedEvent` và `ApplicationReadyEvent` khác nhau gì về ý nghĩa vận hành?
  **Đ:** Started: context refresh xong, app "live" nhưng runner chưa chạy. Ready: runner đã xong, readiness là ACCEPTING_TRAFFIC. Load balancer chỉ nên gửi traffic sau Ready.
- **H:** Vì sao native image không hỗ trợ đầy đủ đổi profile lúc runtime?
  **Đ:** Spring AOT đánh giá `@Configuration`, `@Conditional`, `@Profile` lúc build để sinh code đăng ký bean cố định. Tập bean đã chốt, nên đổi profile/property lúc runtime không thêm hay bớt được bean; chỉ giá trị property bình thường vẫn đổi được.
- **H:** Team đề xuất bật `spring.main.lazy-initialization=true` ở prod để giảm thời gian startup. Bạn phản hồi thế nào?
  **Đ:** Đo trước bằng `BufferingApplicationStartup`. Lazy dời chi phí sang request đầu và che lỗi cấu hình tới lúc có traffic. Ưu tiên CDS/AOT cache, loại auto-config không cần, sửa bean khởi tạo chậm.

### 20. Spring MVC request pipeline

**Tóm tắt**
- Một request đi qua hai thế giới: servlet `Filter` (trước Spring MVC, gồm cả Spring Security) và `DispatcherServlet` (handler mapping, interceptor, argument resolver, message converter, exception resolver). Biết request đang ở tầng nào thì mới biết lỗi được xử lý ở đâu.
- `@ControllerAdvice` chỉ bắt exception phát sinh bên trong `DispatcherServlet`. Lỗi trong filter (ví dụ 401 của Security) không đi qua advice (xem mục 23).
- Error contract phải thống nhất cho cả hệ thống: dùng `ProblemDetail` (RFC 9457), kế thừa `ResponseEntityExceptionHandler` để các lỗi 4xx của chính Spring cũng theo cùng format.
- Framework 7 có API versioning native (`version` trên `@RequestMapping`, `ApiVersionConfigurer`, property `spring.mvc.apiversion.*` của Boot 4).
- Boot 4 dùng Jackson 3: package `tools.jackson`, mapper bất biến, nhiều mặc định khác Jackson 2.

**Cơ chế bên dưới**

```
Tomcat → Filter chain (CharacterEncoding, FormContent, DelegatingFilterProxy/Security, filter của bạn...)
  → DispatcherServlet.doDispatch
     → HandlerMapping (RequestMappingHandlerMapping: path, method, consumes/produces, params, headers, version)
     → HandlerInterceptor.preHandle (theo thứ tự đăng ký)
     → HandlerAdapter (RequestMappingHandlerAdapter)
          → HandlerMethodArgumentResolver (@PathVariable, @RequestBody qua HttpMessageConverter, Pageable...)
          → validation (@Valid, method validation)
          → controller method
          → HandlerMethodReturnValueHandler (ResponseEntity, @ResponseBody qua HttpMessageConverter)
     → interceptor.postHandle (bỏ qua nếu có exception)
     → nếu có exception: HandlerExceptionResolver chain
          1. ExceptionHandlerExceptionResolver  (@ExceptionHandler trong controller, rồi @ControllerAdvice)
          2. ResponseStatusExceptionResolver     (@ResponseStatus, ResponseStatusException)
          3. DefaultHandlerExceptionResolver     (lỗi chuẩn của Spring → 400/405/415..., body rỗng)
     → interceptor.afterCompletion (luôn chạy)
  → không resolver nào xử lý / response.sendError → container forward tới /error → BasicErrorController (Boot)
```

- `BasicErrorController` (Boot 4: `org.springframework.boot.webmvc.autoconfigure.error`) dựng body `{timestamp,status,error,path}` từ `DefaultErrorAttributes`. Đây là nguồn của body "mặc định" khi không có handler.
- Chọn `@ExceptionHandler`: trong cùng advice, method có exception type gần nhất (theo độ sâu kế thừa) thắng. Giữa nhiều advice: theo `@Order`. Vì vậy `ProblemDetailsExceptionHandler` của Boot (bật bằng `spring.mvc.problemdetails.enabled=true`, mặc định false) có thể che advice của bạn.
- Framework 7: cấu hình converter qua `WebMvcConfigurer.configureMessageConverters(HttpMessageConverters.ServerBuilder)`. Boot 4 deprecate `HttpMessageConverters` của Boot và không còn nhặt bean `HttpMessageConverter` rời; dùng `ServerHttpMessageConvertersCustomizer`/`ClientHttpMessageConvertersCustomizer`.

Error contract thống nhất:

```java
@RestControllerAdvice
class ApiExceptionHandler extends ResponseEntityExceptionHandler { // 400/405/415... của Spring → ProblemDetail

    @ExceptionHandler(ProductNotFoundException.class)
    ProblemDetail notFound(ProductNotFoundException ex) {
        var pd = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        pd.setType(URI.create("https://errors.acme.com/product-not-found"));
        pd.setProperty("code", "PRODUCT_NOT_FOUND");   // mã ổn định cho client
        return pd;
    }

    @ExceptionHandler(Exception.class)                  // catch-all: không lộ stack trace
    ProblemDetail unexpected(Exception ex) {
        log.error("Unhandled", ex);
        return ProblemDetail.forStatusAndDetail(HttpStatus.INTERNAL_SERVER_ERROR, "Unexpected error");
    }
}
```

Catch-all an toàn ở đây vì các exception chuẩn của Spring đã có `@ExceptionHandler` cụ thể hơn trong `ResponseEntityExceptionHandler`. Content-Type của `ProblemDetail` là `application/problem+json`.

API versioning (Framework 7):

```java
@GetMapping(path = "/{id}", version = "1.0+")   // baseline: 1.0 và các bản sau
ProductResponse getV1(@PathVariable long id) { ... }

@GetMapping(path = "/{id}", version = "2.0")    // cố định: chỉ 2.0
ProductResponseV2 getV2(@PathVariable long id) { ... }
```

```yaml
spring.mvc.apiversion:
  use.header: X-API-Version    # hoặc query param, path segment, media type parameter
  default: 1.0
```

Mặc định version là bắt buộc (`MissingApiVersionException` → 400), trừ khi cấu hình version optional hoặc đặt `default` như trên; version không hỗ trợ → `InvalidApiVersionException` → 400. `SemanticApiVersionParser` so sánh major.minor.patch. `StandardApiVersionDeprecationHandler` gửi header `Deprecation`, `Sunset`, `Link`. Cần nhiều strategy cùng lúc thì override `configureApiVersioning(ApiVersionConfigurer)`.

Jackson 3 (kiểm tra trên jackson-databind 3.1):
- Package `tools.jackson.*` (annotation vẫn `com.fasterxml.jackson.annotation`). `JsonMapper` bất biến: cấu hình qua `JsonMapper.builder()` hoặc `mapper.rebuild()`, không `configure()` sau khi tạo. `JacksonException` là unchecked.
- Mặc định khác Jackson 2: `FAIL_ON_UNKNOWN_PROPERTIES=false`, `WRITE_DATES_AS_TIMESTAMPS=false` (ISO-8601), `SORT_PROPERTIES_ALPHABETICALLY=true` (creator/record properties vẫn đứng trước), `FAIL_ON_NULL_FOR_PRIMITIVES=true`, `FAIL_ON_TRAILING_TOKENS=true`, `FAIL_ON_EMPTY_BEANS=false`.
- Boot 4: thay mapper bằng bean `JsonMapper` (bean `ObjectMapper` không còn đủ); `Jackson2ObjectMapperBuilderCustomizer` → `JsonMapperBuilderCustomizer`; `@JsonComponent` → `@JacksonComponent`; `spring.jackson.read/write.*` dành cho JSON → `spring.jackson.json.read/write.*` (Boot 4.1 dùng lại `spring.jackson.read/write.*` cho các feature chung của mọi format); tự đăng ký mọi module trên classpath. Cần thời gian chuyển đổi: `spring.jackson.use-jackson2-defaults=true` hoặc module `spring-boot-jackson2` (deprecated, tạm thời).

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Servlet `Filter` (`OncePerRequestFilter`) | Cross-cutting cho mọi request kể cả static/actuator: correlation ID, logging, rate limit, security | Không biết handler nào; exception không qua `@ControllerAdvice`, phải tự ghi response |
| `HandlerInterceptor` | Logic cần biết handler (annotation trên method), audit theo endpoint, tenant resolution | Chỉ cho request tới `DispatcherServlet`; `postHandle` không chạy khi có exception |
| AOP `@Aspect` | Cross-cutting ở tầng service (metrics, retry, audit nghiệp vụ) | Proxy-based: không áp dụng cho self-invocation/private; khó thấy luồng |
| `@ControllerAdvice` | Chuyển exception thành error contract; `@InitBinder` chung | Chỉ trong phạm vi MVC |
| Version qua header | API nội bộ, gateway kiểm soát được header | Khó test bằng trình duyệt; cache phải `Vary` theo header |
| Version qua path (`/v1/...`) | API public, dễ nhìn, dễ route ở gateway | URL đổi theo version; client phải sửa link |

**Pitfalls trong production**
- Client nhận 3 format lỗi khác nhau (JSON của bạn, body Boot mặc định, HTML của Tomcat) → advice không kế thừa `ResponseEntityExceptionHandler`, filter tự ghi lỗi, lỗi ngoài MVC → chuẩn hóa `ProblemDetail`, kế thừa `ResponseEntityExceptionHandler`, cấu hình `AuthenticationEntryPoint`/`AccessDeniedHandler` cùng format (xem mục 23).
- Thêm catch-all `@ExceptionHandler(Exception.class)` rồi 405/415/JSON hỏng biến thành 500 → advice không kế thừa `ResponseEntityExceptionHandler` → kế thừa nó hoặc khai báo handler cụ thể cho các exception chuẩn.
- Stack trace hoặc SQL lọt ra response → `server.error.include-stacktrace`/`include-message` bật, hoặc trả `ex.getMessage()` của exception hạ tầng → chỉ trả message đã kiểm soát; log chi tiết kèm trace ID.
- Nâng Boot 4, JSON đổi thứ tự field, ngày tháng đổi format, request có `null` cho field `int` bị 400 → mặc định Jackson 3 khác → chạy contract test trước khi nâng; tạm dùng `use-jackson2-defaults` rồi xử lý từng điểm.
- Custom `ObjectMapper` bean không có tác dụng sau khi nâng Boot 4 → phải khai báo `JsonMapper`, hoặc tốt hơn là `JsonMapperBuilderCustomizer` để giữ cấu hình của Boot.
- Interceptor đo latency sai khi có lỗi → dùng `postHandle` → dùng `afterCompletion` hoặc filter; hoặc Micrometer Observation có sẵn cho MVC.

**Liên hệ với demo**
`GlobalExceptionHandler` của demo là `@RestControllerAdvice` đơn giản với `ApiError {code,message,timestamp}`, không catch-all, không kế thừa `ResponseEntityExceptionHandler`. Production: chuyển sang `ProblemDetail` + property `code`, kế thừa `ResponseEntityExceptionHandler`, thêm catch-all; `sort=unknownField` (500 `PropertyReferenceException`) nên được map thành 400.

**Câu hỏi kiểm tra**
- **H:** Filter tự ném `IllegalStateException`. `@RestControllerAdvice` có bắt được không? Client nhận gì?
  **Đ:** Không, filter nằm ngoài `DispatcherServlet`. Exception lên container, container forward tới `/error`, `BasicErrorController` trả body mặc định 500. Filter nên tự ghi response theo error contract hoặc dùng `HandlerExceptionResolver` inject vào filter.
- **H:** Vì sao `postHandle` không phải chỗ tốt để ghi audit "request thất bại"?
  **Đ:** `postHandle` không được gọi khi handler ném exception. Dùng `afterCompletion` (luôn chạy, nhận exception nếu có) hoặc filter.
- **H:** Khi nào chọn API version qua header thay vì path?
  **Đ:** Header phù hợp khi tài nguyên giữ nguyên URI và gateway/client kiểm soát header, cho phép baseline version (`1.0+`) giữ endpoint không đổi qua nhiều version. Path phù hợp với API public cần dễ dùng, dễ cache và route. Quan trọng hơn cách chọn là chính sách deprecation (header `Deprecation`/`Sunset`) và số version tồn tại song song.
- **H:** Nâng từ Boot 3 lên Boot 4, mobile client báo lỗi parse ngày tháng. Nguyên nhân khả dĩ?
  **Đ:** Jackson 3 đổi mặc định và Boot 4 đổi cách cấu hình: `spring.jackson.read.*`/`write.*` (JSON) đã chuyển sang `spring.jackson.json.*`, bean `ObjectMapper` tự khai báo không còn thay được mapper của Boot, `Jackson2ObjectMapperBuilderCustomizer` cũ không còn tác dụng. Kiểm tra mapper thực tế, dùng `JsonMapperBuilderCustomizer`, và có contract test cho format JSON.

### 21. HTTP client và tích hợp service

**Tóm tắt**
- Code đồng bộ (MVC, virtual threads): dùng `RestClient`. Code reactive hoặc cần streaming: `WebClient`. `RestTemplate` đã "feature complete"; Spring công bố kế hoạch deprecate, dự kiến `@Deprecated` ở 7.1, xóa ở 8.0. Không viết code mới bằng `RestTemplate`.
- Khai báo client bằng HTTP interface (`@HttpExchange`, `@GetExchange`...) và đăng ký hàng loạt bằng `@ImportHttpServices` (Framework 7); Boot 4 cấu hình từng nhóm qua `spring.http.serviceclient.<group>.*`.
- Luôn đặt connect timeout và read timeout rõ ràng. Không tin vào mặc định của client bên dưới: có client mặc định chờ rất lâu hoặc vô hạn.
- Tạo client từ builder do Boot cung cấp (`RestClient.Builder` inject được) để có sẵn message converter, observability (trace header, metrics) và cấu hình chung.
- Retry chỉ cho request idempotent, có backoff + jitter, có giới hạn; kết hợp timeout, circuit breaker/bulkhead để một dependency chậm không kéo sập cả service.

**Cơ chế bên dưới**

Boot 4 tách module: `spring-boot-starter-restclient` (RestClient/RestTemplate), `spring-boot-starter-webclient` (WebClient). Boot tự chọn thư viện HTTP theo classpath, thứ tự ưu tiên: Apache HttpClient → Jetty → Reactor Netty → JDK `HttpClient` → `HttpURLConnection`. Chọn cụ thể bằng `spring.http.clients.imperative.factory` (ví dụ `jetty`).

HTTP interface client và registry:

```java
@HttpExchange("/api/inventory")
public interface InventoryClient {
    @GetExchange("/{sku}")
    StockResponse stock(@PathVariable String sku);

    @PostExchange("/reservations")
    ReservationResponse reserve(@RequestBody ReservationRequest request);
}

@Configuration(proxyBeanMethods = false)
@ImportHttpServices(group = "inventory", types = InventoryClient.class) // org.springframework.web.service.registry
class HttpClientsConfig { }
```

```yaml
spring:
  http:
    clients:                       # áp dụng cho mọi HTTP client do Boot cấu hình
      connect-timeout: 1s
      read-timeout: 5s
    serviceclient:
      inventory:
        base-url: https://inventory.internal
        read-timeout: 2s           # override cho riêng group
```

- Mỗi group dùng chung một `RestClient` (mặc định) hoặc `WebClient` (`clientType`). Group gom các interface có cùng base URL, timeout, SSL bundle, header.
- Tùy biến sâu hơn: bean `RestClientHttpServiceGroupConfigurer` (hoặc bản `WebClient...`). Interface được tạo proxy, inject như bean thường.
- Framework 7: client hỗ trợ API versioning (`RestClient.builder().defaultApiVersion(...)` + `apiVersionInserter(ApiVersionInserter)`, hoặc `.apiVersion(...)` trên từng request), khớp với server (xem mục 20).

`RestClient` tự dựng, vẫn giữ cấu hình của Boot:

```java
@Bean
RestClient paymentClient(RestClient.Builder builder) {   // builder của Boot: converter + observation
    var settings = HttpClientSettings.defaults()
            .withConnectTimeout(Duration.ofSeconds(1))
            .withReadTimeout(Duration.ofSeconds(3));
    return builder.baseUrl("https://payment.internal")
            .requestFactory(ClientHttpRequestFactoryBuilder.detect().build(settings))
            .defaultStatusHandler(HttpStatusCode::is5xxServerError,
                    (req, res) -> { throw new PaymentUnavailableException(res.getStatusCode()); })
            .build();
}
```

- `RestClient.create()` hoặc `new RestTemplate()` bỏ qua cấu hình của Boot: không có observation (không lan truyền `traceparent`, không có metric `http.client.requests`), không có timeout chung.
- `RestClient.Builder` của Boot là prototype-scoped: mỗi lần inject nhận bản mới, tùy biến không ảnh hưởng client khác.
- Connection pool: Apache HttpClient và Reactor Netty có pool. Giới hạn kết nối trên mỗi route nhỏ có thể trở thành nút thắt ẩn khi tăng tải, nhất là khi bật virtual threads (xem mục 22). Đo `pending` connections, cấu hình pool bằng customizer của factory builder.

Retry trong Framework 7 (không cần Spring Retry; Boot 4 đã bỏ quản lý version Spring Retry):

```java
@Retryable(includes = PaymentUnavailableException.class, maxRetries = 2,
           delay = 200, multiplier = 2, jitter = 100)   // org.springframework.resilience.annotation
public StockResponse stock(String sku) { return inventoryClient.stock(sku); }
// Bật bằng @EnableResilientMethods; mặc định retry MỌI exception, maxRetries = 3, delay = 1000ms
```

Cùng package có `@ConcurrencyLimit` để giới hạn số lời gọi đồng thời (bulkhead đơn giản). Circuit breaker, rate limiter thì vẫn cần Resilience4j (qua Spring Cloud Circuit Breaker hoặc dùng trực tiếp).

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `RestClient` | Mặc định cho code đồng bộ, kể cả virtual threads | Blocking; streaming chưa tốt |
| HTTP interface + `@ImportHttpServices` | Nhiều endpoint, nhiều service; muốn client khai báo, dễ mock | Ẩn chi tiết HTTP; lỗi mapping chỉ lộ lúc gọi; cần hiểu group config |
| `WebClient` | Ứng dụng WebFlux, gọi song song nhiều service, streaming/SSE | Kéo theo Reactor; code khó đọc và debug hơn; `block()` trong MVC là anti-pattern trên thread quan trọng |
| `RestTemplate` | Chỉ trong code cũ chờ chuyển đổi | Không có tính năng mới (API versioning...), sẽ bị deprecate; có thể bọc bằng `RestClient.create(restTemplate)` để chuyển dần |
| Retry ở client | Lỗi tạm thời (503, timeout kết nối) cho request idempotent | Nhân tải lên dependency đang yếu (retry storm); tăng latency đuôi |
| Retry ở service mesh/gateway | Chính sách đồng nhất cho nhiều service | Mesh không biết request có idempotent không; dễ retry chồng retry |

**Pitfalls trong production**
- Thread pool cạn, service treo khi dependency chậm → không có read timeout, thread chờ phản hồi mãi → đặt `spring.http.clients.read-timeout` và timeout theo group; tổng timeout nhỏ hơn timeout của caller.
- Trace bị đứt giữa các service, không thấy span của lời gọi ra ngoài → client tạo bằng `RestClient.create()`/`new RestTemplate()` → luôn tạo từ `RestClient.Builder`/`RestTemplateBuilder` của Boot, hoặc dùng HTTP service client của Boot.
- POST tạo đơn hàng bị tạo hai lần → retry request không idempotent sau timeout (server đã xử lý nhưng response mất) → chỉ retry GET/PUT/DELETE, hoặc dùng idempotency key phía server.
- Một dependency sập kéo theo cả hệ thống (cascading failure) → retry không giới hạn, không backoff, không circuit breaker → giới hạn số lần, backoff + jitter, circuit breaker, bulkhead (`@ConcurrencyLimit`).
- Latency tăng vọt khi tải cao dù dependency khỏe → pool kết nối nhỏ (mặc định theo route) → đo và cấu hình pool; keep-alive, kiểm tra TTL kết nối với load balancer.
- 4xx của dependency biến thành 500 mù mờ → để `HttpClientErrorException` lan lên → `defaultStatusHandler`/`onStatus` chuyển thành exception nghiệp vụ, map sang error contract (xem mục 20).

**Liên hệ với demo**
Demo chưa gọi service ngoài. Nếu thêm một lời gọi (ví dụ kiểm tra tồn kho trước khi tạo product), dùng `@HttpExchange` + `@ImportHttpServices`, đặt timeout theo group `spring.http.serviceclient.<group>.*`, và map lỗi 4xx/5xx của dependency thành mã lỗi riêng trong `ApiError` thay vì để lọt thành 500.

**Câu hỏi kiểm tra**
- **H:** Service A gọi B với read timeout 5s, retry 3 lần. Client của A đặt timeout 10s. Có vấn đề gì?
  **Đ:** Thời gian xấu nhất ở A là khoảng 4 lần 5s cộng backoff, vượt 10s của caller: caller đã bỏ cuộc trong khi A vẫn tiếp tục gọi B, lãng phí tài nguyên và nhân tải. Cần "timeout budget": tổng timeout và retry của tầng dưới phải nhỏ hơn timeout tầng trên, hoặc lan truyền deadline.
- **H:** Vì sao nên inject `RestClient.Builder` thay vì gọi `RestClient.builder()`?
  **Đ:** Builder của Boot đã có message converter theo cấu hình (Jackson 3), `ObservationRegistry` để sinh span/metric và lan truyền trace header, các customizer của platform. `RestClient.builder()` tự tạo thì mất hết những thứ đó.
- **H:** Khi nào WebClient vẫn đáng dùng trong ứng dụng MVC chạy virtual threads?
  **Đ:** Khi cần streaming (SSE, response rất lớn xử lý dần) hoặc cần tổ hợp nhiều lời gọi reactive phức tạp. Với gọi song song đơn giản, virtual threads + `RestClient` (và structured concurrency khi ổn định) thường đủ và dễ đọc hơn.
- **H:** `@Retryable` của Framework 7 để mặc định trên một method gọi POST. Rủi ro là gì?
  **Đ:** Mặc định retry mọi exception, thử lại tối đa 3 lần (tổng 4 lần gọi): có thể gửi lặp POST không idempotent, và retry cả lỗi không tạm thời (400, lỗi validation). Giới hạn `includes` vào lỗi tạm thời và chỉ áp dụng cho thao tác idempotent.

### 22. Mô hình concurrency

**Tóm tắt**
- Spring MVC mặc định là thread-per-request: mỗi request giữ một thread Tomcat (mặc định tối đa 200) suốt thời gian xử lý, kể cả khi chờ DB hay HTTP. Khả năng chịu tải bị giới hạn bởi số thread và bởi tài nguyên phía sau (DB pool).
- Virtual threads (`spring.threads.virtual.enabled=true`, Java 21+, Boot khuyến nghị Java 24+) cho phép giữ code blocking mà vẫn phục vụ nhiều request chờ I/O. Nó không làm DB nhanh hơn; nút thắt thường dời sang connection pool.
- WebFlux chỉ đáng giá khi cả chuỗi là non-blocking (driver R2DBC, WebClient), cần streaming/backpressure, hoặc số kết nối đồng thời rất lớn. Cái giá là mô hình lập trình và debug khó hơn.
- Context (SecurityContext, MDC, transaction, observation) gắn với thread. Qua `@Async`, executor hay reactive thì phải propagate có chủ đích; transaction thì không bao giờ đi theo.
- `@Scheduled` chạy trên MỌI instance. Trong cluster cần lock phân tán (ShedLock) hoặc leader election.

**Cơ chế bên dưới**

`spring.threads.virtual.enabled=true` (Boot 4) tác động tới:
- Tomcat: request xử lý trên virtual thread (`TomcatVirtualThreadsWebServerFactoryCustomizer`); các property `server.tomcat.threads.*` không còn là giới hạn.
- `applicationTaskExecutor`: thành `SimpleAsyncTaskExecutor` dùng virtual threads (thay vì `ThreadPoolTaskExecutor`). Nó được dùng cho `@Async`, xử lý async của MVC, blocking execution trong WebFlux, bootstrap executor của JPA/context.
- Scheduler cho `@Scheduled`: thành `SimpleAsyncTaskScheduler` dùng virtual threads.
- HTTP client dựa trên JDK `HttpClient` do Boot auto-configure (từ Boot 4.0).
- Khi bật, các property cấu hình pool (`spring.task.execution.pool.*`...) không còn tác dụng.
- Virtual thread là daemon thread. App chỉ có `@Scheduled` (không có web server) có thể thoát ngay; đặt `spring.main.keep-alive=true`.

Executor mặc định khi KHÔNG bật virtual threads (Boot 4.0.5): `ThreadPoolTaskExecutor` core 8, queue `Integer.MAX_VALUE`, max `Integer.MAX_VALUE`, prefix `task-`. Queue vô hạn nghĩa là pool không bao giờ vượt 8 thread và task dồn trong queue tới khi hết RAM. Scheduler mặc định có 1 thread (`spring.task.scheduling.pool.size=1`): một job chạy lâu chặn mọi job khác.

Pinning và các bẫy của virtual threads:
- JDK 21: virtual thread bị "pin" vào carrier khi block bên trong `synchronized` (hoặc native call). Pin nhiều thì số carrier (mặc định bằng số CPU) cạn và throughput sụp. JDK 24 (JEP 491) gỡ pinning cho `synchronized`; native/JNI vẫn pin. Trên JDK 21: thay `synchronized` bao quanh I/O bằng `ReentrantLock`, kiểm tra thư viện (driver JDBC cũ), theo dõi bằng JFR event `jdk.VirtualThreadPinned`.
- Không pool virtual thread. Muốn giới hạn đồng thời (bảo vệ DB hoặc API bên ngoài) thì dùng semaphore, `@ConcurrencyLimit` (Framework 7), hoặc `spring.task.execution.simple.concurrency-limit`.
- `ThreadLocal` nặng (cache object lớn theo thread) nhân lên theo số virtual thread, có thể là hàng chục nghìn.
- 10.000 request đồng thời với Hikari pool 10 kết nối thì 9.990 request chờ connection; hết `connectionTimeout` (mặc định 30 giây) thì lỗi. Virtual threads khiến app nhận nhiều việc hơn DB chịu được; cần giới hạn đồng thời ở tầng ứng dụng và timeout ngắn.

Propagate context qua `@Async`/executor:

```java
@Bean  // Boot tự gắn mọi bean TaskDecorator vào executor auto-configured (nhiều bean → CompositeTaskDecorator)
TaskDecorator mdcAndSecurityDecorator() {
    return runnable -> {
        Map<String, String> mdc = MDC.getCopyOfContextMap();           // chụp ở thread gọi
        SecurityContext security = SecurityContextHolder.getContext();
        return () -> {
            try {
                if (mdc != null) MDC.setContextMap(mdc);
                SecurityContextHolder.setContext(security);
                runnable.run();
            } finally { MDC.clear(); SecurityContextHolder.clearContext(); }
        };
    };
}
```

- Observation/trace: `ContextPropagatingTaskDecorator` (Framework, dùng Micrometer Context Propagation). Boot 4.1 có property `spring.task.execution.propagate-context` để bật cho executor auto-configured. Reactor: `spring.reactor.context-propagation=auto`.
- Spring Security có sẵn `DelegatingSecurityContextExecutor`/`DelegatingSecurityContextRunnable` nếu không muốn tự viết.
- Transaction và persistence context gắn với thread: method `@Async` luôn chạy ngoài transaction của caller. Entity lazy truyền sang thread khác sẽ gây `LazyInitializationException`; truyền ID hoặc DTO.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| MVC + platform threads | Mặc định an toàn; tải vừa phải; nhiều thư viện dùng `synchronized`/`ThreadLocal`; JDK 21 | Throughput bị chặn bởi `threads.max`; request chờ I/O lâu làm cạn thread |
| MVC + virtual threads | Service I/O-bound (gọi DB, HTTP nhiều), muốn giữ code blocking dễ đọc; tốt nhất trên JDK 24+ | Nút thắt dời sang DB pool/downstream; cần giới hạn đồng thời; rủi ro pinning trên JDK 21; `ThreadLocal` nặng; không giúp tác vụ CPU-bound |
| WebFlux + Reactor | Gateway/proxy, streaming, SSE/WebSocket nhiều kết nối, cả chuỗi non-blocking (R2DBC, WebClient) | Học khó, stack trace khó đọc, dễ lỡ tay gọi blocking trên event loop; JPA/JDBC không dùng được trực tiếp; context phải propagate qua Reactor Context |
| `@Async` với executor mặc định | Việc nền nhỏ, mất cũng không sao | Queue vô hạn, mất task khi restart; không retry; context phải propagate |
| Message broker (Kafka, RabbitMQ) cho việc nền | Việc cần bền vững, retry, phân tán | Thêm hạ tầng; cần xử lý idempotent và outbox |

`@Scheduled` trong cluster:

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| ShedLock (lock trong DB/Redis) | Job định kỳ đơn giản, chỉ cần "tối đa một instance chạy" | Phụ thuộc bảng lock; phải chọn `lockAtMostFor` lớn hơn thời gian chạy dài nhất, nếu không job có thể chạy chồng |
| Leader election (Spring Integration, Kubernetes lease) | Nhiều job, hoặc cần một "leader" ổn định | Phức tạp hơn; xử lý chuyển leader |
| Job scheduler ngoài (Kubernetes CronJob, Quartz cluster, Spring Batch) | Job nặng, cần lịch sử, retry, giám sát | Thêm thành phần vận hành |

**Pitfalls trong production**
- Bật virtual threads, latency p99 tăng và thấy `Connection is not available, request timed out` → nhiều request đồng thời tranh nhau pool DB nhỏ → giới hạn đồng thời ở tầng ứng dụng, giảm `connectionTimeout` để fail nhanh, tối ưu query trước khi tăng pool.
- Trên JDK 21, bật virtual threads làm throughput giảm → pinning do `synchronized` quanh I/O (driver, thư viện cũ) → bật JFR `jdk.VirtualThreadPinned`, thay bằng `ReentrantLock`, hoặc lên JDK 24+/25 LTS.
- Việc `@Async` dồn ứ, RAM tăng dần → executor mặc định có queue vô hạn và chỉ 8 thread → cấu hình `queue-capacity` hữu hạn và chính sách từ chối, hoặc chuyển sang broker.
- Log của `@Async` mất `traceId`, audit ghi user "anonymous" → MDC và `SecurityContext` không theo sang thread mới → `TaskDecorator`, `ContextPropagatingTaskDecorator`, `spring.task.execution.propagate-context` (Boot 4.1).
- Job gửi email chạy 3 lần khi scale lên 3 pod → `@Scheduled` chạy trên mọi instance → ShedLock/leader election; job phải idempotent.
- Các job `@Scheduled` trễ lịch hàng loạt → scheduler mặc định có 1 thread, một job treo chặn tất cả → tăng `spring.task.scheduling.pool.size`, đặt timeout cho job, hoặc bật virtual threads.
- Gọi `.block()` trên event loop WebFlux → toàn bộ server treo → dùng `publishOn(Schedulers.boundedElastic())` cho đoạn blocking, hoặc không dùng WebFlux nếu phần lớn code là blocking.

**Liên hệ với demo**
Demo là MVC + platform threads + JPA, phù hợp. Nếu bật virtual threads cho demo, cần xem lại kích thước Hikari pool và timeout trước khi đo tải.

**Câu hỏi kiểm tra**
- **H:** Bật virtual threads có làm service nhanh hơn không?
  **Đ:** Không làm từng request nhanh hơn. Nó tăng số request chờ I/O có thể xử lý đồng thời mà không tốn thread OS. Nếu nút thắt là DB, CPU hoặc downstream thì throughput gần như không đổi, và có thể tệ hơn nếu dồn quá nhiều việc vào DB.
- **H:** Vì sao `@Async` gọi từ method `@Transactional` không thấy dữ liệu vừa insert?
  **Đ:** Method async chạy trên thread khác, ngoài transaction của caller, và có thể chạy trước khi caller commit. Dùng `@TransactionalEventListener(phase = AFTER_COMMIT)` hoặc outbox, và truyền ID thay vì entity.
- **H:** Khi nào bạn chọn WebFlux cho service mới năm 2026?
  **Đ:** Khi cần streaming/SSE/WebSocket quy mô lớn, backpressure, gateway/proxy, và cả chuỗi phụ thuộc hỗ trợ non-blocking. Với CRUD + JPA thông thường, MVC + virtual threads cho phần lớn lợi ích với code đơn giản hơn.
- **H:** ShedLock đặt `lockAtMostFor = 5m` cho job thường chạy 2 phút. Có rủi ro gì?
  **Đ:** Nếu một lần chạy vượt 5 phút (DB chậm), lock hết hạn và instance khác bắt đầu chạy chồng. `lockAtMostFor` phải lớn hơn thời gian chạy dài nhất có thể, và job vẫn nên idempotent.

### 23. Kiến trúc Spring Security

**Tóm tắt**
- Spring Security là một chuỗi servlet filter: `DelegatingFilterProxy` → `FilterChainProxy` → một trong nhiều `SecurityFilterChain`. Mọi quyết định (xác thực, CSRF, CORS, 401/403) xảy ra TRƯỚC `DispatcherServlet`, nên `@ControllerAdvice` không thấy các lỗi này.
- Tách bạch authentication (bạn là ai: `AuthenticationManager`/`AuthenticationProvider`) và authorization (bạn được làm gì: `AuthorizationManager`). Kết quả xác thực nằm trong `SecurityContextHolder` (mặc định `ThreadLocal`).
- Với API stateless: OAuth2 Resource Server + JWT, kiểm tra cả issuer, chữ ký, thời hạn VÀ audience; map authorities có chủ đích; phân quyền chi tiết bằng `@PreAuthorize`.
- Boot 4.1 dùng Spring Security 7.1. Security 7 bỏ `and()` trong DSL (bắt buộc lambda), bỏ `authorizeRequests`, bỏ `AntPathRequestMatcher`/`MvcRequestMatcher`, nhập Authorization Server vào Spring Security, thêm MFA.
- Error contract phải nhất quán: `AuthenticationEntryPoint` (401) và `AccessDeniedHandler` (403) trả cùng format `ProblemDetail` với phần còn lại (xem mục 20).

**Cơ chế bên dưới**

```
Tomcat filter chain
  → DelegatingFilterProxy("springSecurityFilterChain")   // cầu nối servlet container ↔ bean Spring
     → FilterChainProxy                                   // chọn SecurityFilterChain ĐẦU TIÊN có securityMatcher khớp
        → SecurityFilterChain (@Order nhỏ nhất trước)
           SecurityContextHolderFilter → HeaderWriterFilter → CorsFilter → CsrfFilter → LogoutFilter
           → BearerTokenAuthenticationFilter / UsernamePassword... / BasicAuthenticationFilter
           → AnonymousAuthenticationFilter → ExceptionTranslationFilter → AuthorizationFilter
  → DispatcherServlet → controller (method security @PreAuthorize chạy qua AOP proxy)
```

- `ExceptionTranslationFilter` bắt `AuthenticationException` → `AuthenticationEntryPoint` (401; với bearer token có header `WWW-Authenticate`) và `AccessDeniedException` → `AccessDeniedHandler` (403; hoặc 401 nếu đang anonymous).
- `SecurityContextHolder` có strategy `MODE_THREADLOCAL` (mặc định), `MODE_INHERITABLETHREADLOCAL`, `MODE_GLOBAL`. Context không tự đi theo `@Async`/executor (xem mục 22). Tránh `INHERITABLETHREADLOCAL` với thread pool: thread tái sử dụng mang context của user khác.
- Boot: khi bạn khai báo bất kỳ bean `SecurityFilterChain` nào, cấu hình web security mặc định của Boot lùi lại (kể cả phần bảo vệ Actuator).
- Security 7 dùng `PathPatternRequestMatcher` cho mọi matcher mà DSL tạo từ String. Pattern viết theo đường dẫn đầy đủ tính từ sau context path (có servlet path thì phải tính cả servlet path, hoặc dùng `PathPatternRequestMatcher.withDefaults().basePath(...)`).

Nhiều chain: Actuator và API tách riêng.

```java
@Bean @Order(1)
SecurityFilterChain actuatorChain(HttpSecurity http) throws Exception {
    http.securityMatcher(EndpointRequest.toAnyEndpoint())   // org.springframework.boot.security.autoconfigure.actuate.web.servlet
        .authorizeHttpRequests(a -> a
            .requestMatchers(EndpointRequest.to("health", "info")).permitAll()
            .anyRequest().hasRole("OPS"))
        .httpBasic(Customizer.withDefaults());
    return http.build();
}
```

```java
@Bean @Order(2)
SecurityFilterChain apiChain(HttpSecurity http, ProblemDetailSecurityHandler errors) throws Exception {
    http.securityMatcher("/api/**")
        .authorizeHttpRequests(a -> a
            .requestMatchers(HttpMethod.GET, "/api/products/**").hasAuthority("SCOPE_products:read")
            .anyRequest().authenticated())
        .oauth2ResourceServer(o -> o.jwt(Customizer.withDefaults())
            .authenticationEntryPoint(errors).accessDeniedHandler(errors))
        .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
        .csrf(c -> c.disable())                 // chỉ vì token nằm ở header Authorization, không ở cookie
        .cors(Customizer.withDefaults());       // dùng bean CorsConfigurationSource
    return http.build();
}
```

`ProblemDetailSecurityHandler` là class của bạn, implement cả `AuthenticationEntryPoint` và `AccessDeniedHandler`, ghi `application/problem+json` bằng `JsonMapper`.

JWT với Boot 4.1:

```yaml
spring.security.oauth2.resourceserver.jwt:
  issuer-uri: https://idp.acme.com/realms/prod   # discovery: lấy JWKS, kiểm tra claim iss
  audiences: product-api                          # KHÔNG có dòng này thì aud không được kiểm tra
  # authorities-claim-name: roles                 # mặc định: scope/scp → SCOPE_xxx
  # authority-prefix: ROLE_                       # bật hai dòng này thì chỉ còn ROLE_xxx từ claim roles
```

Hai dòng cuối để dạng comment vì chain ở trên dùng `SCOPE_products:read`: nếu đổi claim sang `roles` thì không còn authority `SCOPE_xxx`, rule phải chuyển sang `hasRole(...)`.

- Mặc định chữ ký kiểm tra với `RS256` (`jws-algorithms`). Boot 4.1 thêm `authorities-claim-expressions` (SpEL) cho claim lồng nhau, loại trừ với `authorities-claim-name`/`authorities-claim-delimiter`.
- JWKS được lấy từ IdP và cache; IdP xoay key phải công bố key mới trước khi dùng nó để ký.
- Method security: `@EnableMethodSecurity` rồi `@PreAuthorize("hasAuthority('SCOPE_products:write')")`, hoặc kiểm tra quyền sở hữu `@PreAuthorize("@productAccess.canEdit(#id, authentication)")`. Nó là AOP proxy nên cùng giới hạn với `@Transactional`: không áp dụng cho self-invocation/private method.

Thay đổi Spring Security 7 (tổng hợp từ trang "What's New in 7.0" và reference docs):
- DSL: `and()` bị xóa, chỉ còn lambda; `authorizeRequests` bị xóa, dùng `authorizeHttpRequests`; custom DSL dùng `.with()` thay `.apply()`.
- `AuthorizationManager#check` bị xóa, dùng `authorize`. Access API cũ (`AccessDecisionManager`, `AccessDecisionVoter`) chuyển sang module riêng `spring-security-access`.
- Spring Authorization Server và Kerberos Extension trở thành một phần của Spring Security (Boot 4: `spring-boot-starter-security-oauth2-authorization-server`).
- MFA: `@EnableMultiFactorAuthentication(authorities = {FactorGrantedAuthority.PASSWORD_AUTHORITY, FactorGrantedAuthority.OTT_AUTHORITY})`; mỗi cơ chế xác thực gắn một `FactorGrantedAuthority`.
- CSRF cho SPA: `http.csrf(csrf -> csrf.spa())`. Jackson 3 cho module serialization của Security (`SecurityJacksonModules`).
- Boot 4 đổi tên starter: `spring-boot-starter-security-oauth2-resource-server`, `...-oauth2-client`; test với `@WithMockUser` cần `spring-boot-starter-security-test`.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| JWT tự kiểm tra (resource server) | Microservice, API stateless, IdP chuẩn OIDC | Không thu hồi token ngay được: phải chờ hết hạn; cần TTL ngắn và refresh token |
| Opaque token + introspection | Cần thu hồi tức thì, token không lộ claim | Mỗi request gọi IdP (hoặc cache): thêm latency và phụ thuộc |
| Session cookie (BFF pattern) cho SPA | Web app first-party, không muốn token ở trình duyệt | Cần CSRF, sticky session hoặc session store (Spring Session) |
| Phân quyền ở URL (`authorizeHttpRequests`) | Quy tắc thô theo đường dẫn/method | Dễ lệch khi đổi route; không biết dữ liệu nghiệp vụ |
| Method security (`@PreAuthorize`) | Quy tắc theo nghiệp vụ, quyền sở hữu | Phân tán trong code; cần test; giới hạn của proxy |
| Actuator trên port quản trị riêng (`management.server.port`) | Có mạng nội bộ tách biệt | Thêm cấu hình network/probe; vẫn nên có xác thực |

**Pitfalls trong production**
- Preflight `OPTIONS` từ trình duyệt bị 401, frontend báo lỗi CORS → CORS chỉ cấu hình ở MVC (`WebMvcConfigurer.addCorsMappings`) nên Security chặn trước → cấu hình `http.cors(...)` với `CorsConfigurationSource`, không dùng `allowedOrigins("*")` cùng credentials.
- Token cấp cho service khác vẫn gọi được API của bạn → không kiểm tra audience → đặt `spring.security.oauth2.resourceserver.jwt.audiences`.
- `@PreAuthorize` từ chối nhưng client nhận 500 → catch-all `@ExceptionHandler(Exception.class)` trong advice bắt luôn exception từ chối quyền (ném ra trong `DispatcherServlet`, trước khi tới `ExceptionTranslationFilter`) → thêm handler riêng cho `AccessDeniedException` trả 403, hoặc ném lại để Security xử lý.
- Lỗi 401/403 có body khác hẳn các lỗi khác (hoặc body rỗng) → entry point/handler mặc định → cài `AuthenticationEntryPoint`/`AccessDeniedHandler` trả `ProblemDetail`.
- Actuator `/actuator/env`, `/heapdump` lộ ra Internet → expose `*` mà không có chain bảo vệ → chỉ expose endpoint cần thiết, chain riêng cho `EndpointRequest.toAnyEndpoint()`, hoặc port quản trị nội bộ (xem mục 24).
- Tắt CSRF "vì là REST API" trong khi xác thực bằng cookie → mở cửa cho CSRF → chỉ tắt khi token nằm ở header; dùng cookie thì bật CSRF (`csrf.spa()` cho SPA).
- Nâng lên Boot 4, cấu hình security không compile → `and()`, `authorizeRequests`, `antMatchers`/`mvcMatchers` đã bị xóa → lên Boot 3.5/Security 6.5 trước, sửa hết deprecation, rồi mới nâng major.
- Request đi vào chain sai (API bị form login) → nhiều chain không có `securityMatcher` rõ ràng hoặc sai `@Order` → chain cụ thể có order nhỏ, chain catch-all order lớn nhất; bật log `org.springframework.security` ở DEBUG để xem chain được chọn.

**Liên hệ với demo**
Demo không có Security. Khi thêm `spring-boot-starter-security`, mọi endpoint mặc định cần xác thực (form login/basic với user sinh ngẫu nhiên). Production: resource server JWT, chain riêng cho Actuator, entry point trả cùng format với `GlobalExceptionHandler`.

**Câu hỏi kiểm tra**
- **H:** Vì sao `@RestControllerAdvice` không xử lý được lỗi token hết hạn?
  **Đ:** Token được kiểm tra trong `BearerTokenAuthenticationFilter`, trước `DispatcherServlet`. Lỗi đi tới `AuthenticationEntryPoint` qua `ExceptionTranslationFilter`/filter xác thực. Muốn format thống nhất thì cài entry point riêng.
- **H:** JWT đã kiểm tra chữ ký và `exp`. Còn thiếu gì trước khi tin token?
  **Đ:** Issuer (`iss`) đúng IdP, audience (`aud`) đúng API này, thuật toán được phép (không chấp nhận `none`/HS khi dùng RS), và map authorities đúng claim. Với quyền sở hữu dữ liệu vẫn cần kiểm tra nghiệp vụ, không chỉ dựa vào role.
- **H:** Có hai `SecurityFilterChain`, một cho `/api/**` và một không có `securityMatcher`. Order nên thế nào?
  **Đ:** Chain có matcher cụ thể phải có `@Order` nhỏ hơn. `FilterChainProxy` chọn chain đầu tiên khớp; chain không có matcher khớp mọi request, nếu đứng trước sẽ nuốt hết.
- **H:** Khi nào 403 nên trả về thành 404?
  **Đ:** Khi việc biết tài nguyên tồn tại cũng là rò rỉ thông tin (ví dụ đơn hàng của người khác). Kiểm tra quyền sở hữu ở tầng service rồi trả 404 cho tài nguyên không thuộc về user; 403 dùng cho trường hợp user biết tài nguyên nhưng thiếu quyền.

---

## Phần IV — Vận hành, testing, kiến trúc và governance

### 24. Actuator, health và Kubernetes probes

**Tóm tắt**
- Actuator cung cấp endpoint vận hành (`/actuator/*`). Boot 4: mặc định **chỉ `health`** được expose qua HTTP và JMX. Mọi endpoint khác phải bật bằng `management.endpoints.web.exposure.include`.
- "Available" = **được phép truy cập** (`management.endpoint.<id>.access`, từ Boot 3.4 thay cho `...enabled`) **và** được expose. `shutdown` và `heapdump` mặc định bị chặn truy cập.
- Liveness trả lời "process còn sống, có cần restart không?". Readiness trả lời "có nên nhận traffic không?". Boot 4 bật sẵn hai health group `liveness`/`readiness` (Boot 3 chỉ tự bật khi phát hiện môi trường Kubernetes).
- Không đưa DB hay dependency bên ngoài vào liveness. DB chết thì mọi pod cùng bị restart, tạo cascading failure.
- Actuator là bề mặt tấn công. Coi nó như một API nội bộ: port riêng hoặc network policy, Spring Security, expose tối thiểu.

**Cơ chế bên dưới**
- Mỗi endpoint là một bean `@Endpoint` với `@ReadOperation`/`@WriteOperation`/`@DeleteOperation`. Boot map nó sang HTTP (`/actuator/{id}`) và JMX. Endpoint không được phép truy cập bị loại hẳn khỏi context.
- Health: `HealthContributorRegistry` gom mọi `HealthIndicator` (Boot 4, module `spring-boot-health`: `HealthIndicator`/`Health` ở package `org.springframework.boot.health.contributor`, registry ở `org.springframework.boot.health.registry`). `StatusAggregator` chọn status "tệ nhất" theo thứ tự. `DOWN` và `OUT_OF_SERVICE` map sang HTTP 503. `UP` và `UNKNOWN` trả 200.
- Availability: `ApplicationAvailabilityBean` (package `org.springframework.boot.availability`) lắng nghe `AvailabilityChangeEvent`. Boot tự publish trạng thái theo vòng đời:

| Giai đoạn | LivenessState | ReadinessState |
|---|---|---|
| Đang start | BROKEN | REFUSING_TRAFFIC |
| Context refresh xong, còn chạy runner | CORRECT | REFUSING_TRAFFIC |
| Sẵn sàng | CORRECT | ACCEPTING_TRAFFIC |
| Bắt đầu graceful shutdown | CORRECT | REFUSING_TRAFFIC |

- Code ứng dụng có thể tự đổi trạng thái, ví dụ tạm ngừng nhận traffic khi cache chưa warm-up xong:

```java
import org.springframework.boot.availability.AvailabilityChangeEvent;
import org.springframework.boot.availability.ReadinessState;
import org.springframework.context.ApplicationEventPublisher;

@Component
class CacheWarmer {
    private final ApplicationEventPublisher events;
    CacheWarmer(ApplicationEventPublisher events) { this.events = events; }

    void rebuild() {
        AvailabilityChangeEvent.publish(events, this, ReadinessState.REFUSING_TRAFFIC);
        try { /* nạp lại cache */ }
        finally { AvailabilityChangeEvent.publish(events, this, ReadinessState.ACCEPTING_TRAFFIC); }
    }
}
```

- Cấu hình production điển hình:

```yaml
management:
  server.port: 8081                       # port quản trị riêng, không public qua Ingress
  endpoints.web.exposure.include: health,info,prometheus
  endpoint:
    health:
      show-details: when-authorized       # mặc định: never
      probes.add-additional-paths: true   # /livez, /readyz trên port chính
      group.readiness.include: readinessState,db
  info.git.mode: simple                   # build/git contributor bật mặc định nếu có file
```

- `info`: contributor `build` (đọc `META-INF/build-info.properties`, sinh bởi goal `build-info` của Maven plugin) và `git` (đọc `git.properties`, sinh bởi git-commit-id plugin) bật mặc định khi có file. `env`, `java`, `os`, `process` tắt mặc định (`management.info.<id>.enabled`). Giúp trả lời nhanh câu hỏi "pod này đang chạy commit nào?".

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Actuator chung port với app | Môi trường cloud, có Spring Security chặn `/actuator/**` | Dễ lỡ expose ra Internet nếu Ingress route cả `/actuator` |
| `management.server.port` riêng | Muốn tách bằng network (Service/Ingress không route port này) | Probe có thể "xanh" trong khi connector chính đã nghẽn. Khắc phục bằng `add-additional-paths` để probe chạy trên port chính |
| Readiness có kiểm tra DB | Instance thực sự vô dụng khi mất DB, và có instance khác để route sang | Nếu DB chết, **mọi** pod cùng not-ready, Service không còn endpoint nào, client nhận lỗi kết nối thay vì 503 rõ ràng |
| Readiness chỉ có `readinessState` | Mặc định an toàn. Để circuit breaker/fallback xử lý lỗi dependency | Pod vẫn nhận traffic khi dependency lỗi, phải có xử lý lỗi tốt ở tầng app |
| `startupProbe` dùng liveness path | App start chậm (migration, warm-up lớn) | Thêm một cấu hình phải tinh chỉnh. Không có nó thì liveness có thể kill app giữa lúc start |

**Pitfalls trong production**
- Pod bị restart hàng loạt khi DB bảo trì → liveness group có `db` (hoặc custom indicator gọi service ngoài) → liveness chỉ phản ánh trạng thái nội bộ JVM/app; dependency đưa vào readiness một cách có chủ đích, hoặc không đưa.
- `/actuator/env` hoặc `/actuator/heapdump` truy cập được từ Internet → `exposure.include=*` và Ingress route mọi path → expose tối thiểu, port riêng không public, bảo vệ bằng Spring Security (chain riêng cho Actuator: xem mục 23). Heap dump chứa mọi thứ trong bộ nhớ: token, mật khẩu, dữ liệu khách hàng.
- `/actuator/health` chậm hoặc timeout làm probe fail → health indicator gọi hệ thống chậm (ví dụ Elasticsearch, SMTP) đồng bộ trên mỗi lần probe → loại indicator đó khỏi group probe, hoặc tắt (`management.health.<id>.enabled=false`), đặt timeout cho probe hợp lý.
- Health endpoint lộ chi tiết (tên DB, version, disk path) cho người lạ → `show-details: always` → dùng `when-authorized` với `management.endpoint.health.roles`.
- Startup fail sau khi đổi tên indicator → group `include` trỏ tới contributor không tồn tại (mặc định startup fail) → đây là hành vi tốt, giữ nguyên; sửa cấu hình group.
- Có Spring Security mà gọi `POST /actuator/loggers` bị 403 → CSRF bật mặc định → tắt CSRF riêng cho matcher actuator (`EndpointRequest.toAnyEndpoint()`) khi client là tool nội bộ.

**Liên hệ với demo**
Demo không có Actuator. Production tối thiểu cần `spring-boot-starter-actuator`, expose `health,info,prometheus`, probes trỏ tới `/actuator/health/liveness|readiness` (hoặc `/livez`, `/readyz`), và `build-info` để biết phiên bản đang chạy.

**Câu hỏi kiểm tra**
- **H:** Vì sao không nên đưa DB health vào liveness probe?
  **Đ:** Liveness fail thì kubelet restart container. Restart không sửa được DB. Khi DB chết, mọi pod cùng bị restart liên tục, mất cả cache/connection đang có, và dồn tải lên DB lúc nó hồi phục (cascading failure).
- **H:** Đặt `management.server.port` riêng có rủi ro gì với probe?
  **Đ:** Actuator chạy trên connector/thread pool khác. Connector chính có thể cạn thread nhưng probe vẫn 200. Dùng `management.endpoint.health.probes.add-additional-paths=true` để có `/livez`, `/readyz` trên port chính.
- **H:** Khi nào app nên tự publish `ReadinessState.REFUSING_TRAFFIC`?
  **Đ:** Khi instance tạm thời không phục vụ được nhưng không cần restart: đang warm-up/rebuild cache, đang drain một hàng đợi nội bộ, đang chuyển leader. Không dùng cho lỗi dependency ngoài chung cho mọi instance.
- **H:** Boot 4 khác Boot 3 thế nào về probes và quyền truy cập endpoint?
  **Đ:** Boot 4 bật liveness/readiness group mặc định ở mọi môi trường. Từ Boot 3.4, bật/tắt endpoint dùng `management.endpoint.<id>.access` (`none`/`read-only`/`unrestricted`) và có `management.endpoints.access.max-permitted` để giới hạn toàn app.

### 25. Observability: metrics, traces, logs

**Tóm tắt**
- Ba tín hiệu trả lời ba câu hỏi khác nhau. Metrics: "có vấn đề không, bao nhiêu?" (alert, dashboard, SLO). Traces: "chậm/lỗi ở đâu trong chuỗi gọi?". Logs: "chính xác chuyện gì đã xảy ra với request này?".
- Micrometer Observation API: instrument **một lần**, các `ObservationHandler` sinh ra cả metric (Timer) lẫn span. Spring MVC, RestClient/WebClient, Kafka... đã instrument sẵn; Spring Data repository có metric riêng (`spring.data.repository.invocations`). Với JDBC, Boot chỉ có metric của connection pool (`jdbc.connections.*`), không tự tạo observation cho từng câu lệnh: cần thêm project Datasource Micrometer.
- Cardinality là rủi ro số một của metrics. Tag chỉ được chứa tập giá trị nhỏ, hữu hạn. `userId`, email, URL thô, message lỗi không bao giờ đưa vào tag.
- Boot 4 có `spring-boot-starter-opentelemetry` để export metrics và traces qua OTLP. Sampling mặc định chỉ **10%** (`management.tracing.sampling.probability=0.1`).
- Structured logging (từ Boot 3.4): `logging.structured.format.console=ecs|logstash|gelf`. Log JSON kèm `traceId`/`spanId` giúp nhảy từ trace sang log.

**Cơ chế bên dưới**
- `Observation` có vòng đời start/stop/error, mang `lowCardinalityKeyValue` (vào cả metric tag và span attribute) và `highCardinalityKeyValue` (**chỉ vào span**). Đây là cách đúng để gắn `userId` vào trace mà không phá metrics.

```java
import io.micrometer.observation.Observation;
import io.micrometer.observation.ObservationRegistry;

@Service
class PricingService {
    private final ObservationRegistry registry;
    PricingService(ObservationRegistry registry) { this.registry = registry; }

    BigDecimal quote(String customerTier, long customerId) {
        return Observation.createNotStarted("pricing.quote", registry)
            .lowCardinalityKeyValue("tier", customerTier)          // vài giá trị: GOLD/SILVER...
            .highCardinalityKeyValue("customer.id", String.valueOf(customerId)) // chỉ trace
            .observe(() -> calculate(customerTier, customerId));
    }
}
```

- `@Observed`, `@Timed`, `@Counted`, `@NewSpan` chỉ hoạt động khi `management.observations.annotations.enabled=true` **và** có `aspectjweaver` (Boot 4: `spring-boot-starter-aspectj`, tên cũ `spring-boot-starter-aop`). Đây là AOP proxy, nên có cùng giới hạn self-invocation như `@Transactional`. Đặt `@Observed` lên controller/repository đã được instrument sẵn sẽ tạo observation trùng.
- HTTP server metric mặc định: `http.server.requests`, các tag chính gồm `method`, `uri` (URI **template** như `/api/products/{id}`, không phải URL thật), `status`, `outcome`, `exception` (danh sách tag đầy đủ có thể khác tùy phiên bản Micrometer; kiểm tra trên `/actuator/metrics/http.server.requests`).
- Percentile: `management.metrics.distribution.percentiles-histogram.<meter>=true` publish histogram bucket, backend (Prometheus) tính p95/p99 **cộng dồn được qua nhiều instance**. `...percentiles` tính trong app, **không cộng dồn được** (không thể lấy trung bình p99 của 10 pod). `...slo` publish bucket tại ngưỡng SLO (ví dụ 200ms, 500ms) để đo "% request dưới 500ms".
- Tracing: Micrometer Tracing là facade, bridge sang OpenTelemetry (hoặc Brave). Boot tự thêm `traceId`/`spanId` vào MDC, và pattern log mặc định có correlation ID. Tùy chỉnh bằng `logging.pattern.correlation`.
- Sampler mặc định với OpenTelemetry là `parent-based-trace-id-ratio`: nếu upstream đã quyết định sample thì theo quyết định đó; nếu không có parent thì lấy mẫu theo probability. Boot 4.1 thêm `management.opentelemetry.tracing.sampler` và đọc được phần lớn biến môi trường `OTEL_*` (ví dụ `OTEL_EXPORTER_OTLP_ENDPOINT`).
- Context propagation: observation nằm trong `ThreadLocal`. Sang thread khác thì mất trace. Với method `@Async` chạy trên `AsyncTaskExecutor` do Boot auto-configure, từ Boot 4.1 bật `spring.task.execution.propagate-context=true` (Boot 4.0 chưa có property này; có thể khai báo bean `ContextPropagatingTaskDecorator`, package `org.springframework.core.task.support`, để Boot gắn vào executor auto-configured). Executor tự tạo thì tự gọi `setTaskDecorator(new ContextPropagatingTaskDecorator())`. Reactor: `spring.reactor.context-propagation=auto`. Kết hợp với MDC/`SecurityContext`: xem mục 22.

```yaml
management:
  tracing.sampling.probability: 0.1          # mặc định; tăng ở dev, giữ thấp ở prod traffic lớn
  observations.annotations.enabled: true
  metrics.distribution:
    percentiles-histogram.http.server.requests: true
    slo.http.server.requests: 100ms,300ms,1s
logging.structured.format.console: ecs
spring.task.execution.propagate-context: true   # Boot 4.1+
```

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Micrometer + OTLP (starter của Boot) | Mặc định cho Boot 4. Được Spring team hỗ trợ chính thức | Chỉ có instrumentation mà Spring/Micrometer cung cấp. Thư viện lạ phải tự instrument |
| OpenTelemetry Java Agent | Nhiều runtime khác nhau, muốn auto-instrument thư viện không phải Spring | Thêm agent vào JVM (startup, overhead, xung đột phiên bản). Dễ trùng span với instrumentation của Spring |
| Sampling theo tỷ lệ ở app (head-based) | Traffic lớn, backend trace có hạn mức | Có thể bỏ sót đúng trace lỗi hiếm |
| Tail-based sampling ở OTel Collector | Muốn giữ 100% trace lỗi/chậm | Phải vận hành Collector có state, tốn bộ nhớ |
| Log JSON (structured) | Có hệ thống log tập trung (ELK, Loki, Graylog) | Khó đọc trên terminal khi dev. Chỉ bật ở profile không phải dev |
| Metric tag chi tiết | Phân tích theo chiều quan trọng (tier, region, endpoint) | Mỗi tổ hợp tag là một time series. Tag sai làm nổ bộ nhớ app và chi phí backend |

**Pitfalls trong production**
- Bộ nhớ app và Prometheus tăng dần, dashboard chậm → tag chứa giá trị không giới hạn (URL thô khi dùng `RestTemplate.getForObject(url + id)` thay vì URI template, `userId`, `exception.getMessage()`) → dùng URI template (`{id}`), chuyển dữ liệu định danh sang `highCardinalityKeyValue`, thêm `MeterFilter` chặn/giới hạn tag.
- Dashboard báo "latency trung bình 80ms" nhưng khách vẫn kêu chậm → nhìn average che mất đuôi phân phối → alert theo p95/p99 từ histogram, và theo tỷ lệ lỗi (xem mục 27).
- Trace bị đứt ở chỗ `@Async`/`CompletableFuture`/executor tự tạo → context không propagate qua thread → `propagate-context` hoặc `ContextPropagatingTaskDecorator`; không dùng `new Thread()` hay `ForkJoinPool.commonPool()` cho code nghiệp vụ.
- Không tìm thấy trace của request lỗi mà người dùng báo → sampling 10% → trả `traceId` trong response header hoặc body lỗi, tăng sampling cho lỗi bằng tail sampling ở Collector.
- Log chứa token, mật khẩu, số thẻ → log nguyên request body hoặc `toString()` của entity → chỉ log field cần thiết, mask dữ liệu nhạy cảm; structured logging không tự che dữ liệu.
- Đổi sang log JSON mà custom `logback-spring.xml` không đổi → cấu hình log tự viết không dùng `StructuredLogEncoder` → cập nhật encoder theo docs, hoặc bỏ file custom.

**Liên hệ với demo**
Demo chỉ log text và `show-sql` ở dev. Production nên log JSON có `traceId`, thêm `traceId` vào `ApiError` để support tra cứu, và theo dõi `http.server.requests` theo `uri`/`status` (ví dụ tỷ lệ 409 DUPLICATE_SKU tăng bất thường).

**Câu hỏi kiểm tra**
- **H:** Khác biệt giữa `lowCardinalityKeyValue` và `highCardinalityKeyValue`?
  **Đ:** Low cardinality vào cả metric tag và span. High cardinality chỉ vào span, nên gắn được ID người dùng/đơn hàng vào trace mà không tạo thêm time series.
- **H:** Vì sao không nên dùng `percentiles` (tính trong app) cho service chạy nhiều pod?
  **Đ:** Percentile không cộng dồn được. Không thể tính p99 toàn hệ thống từ p99 từng pod. Dùng `percentiles-histogram` để backend tính trên bucket gộp.
- **H:** Service A gọi service B, B không thấy chung trace với A. Kiểm tra gì?
  **Đ:** A có dùng client được instrument (RestClient/WebClient build từ builder của Boot, không `new`) để gửi header `traceparent` không; hai bên có cùng propagation format không; có gateway nào xóa header không; sampling của B có tôn trọng parent không.
- **H:** Mặc định Boot sample bao nhiêu phần trăm trace, và vì sao?
  **Đ:** 10% (`management.tracing.sampling.probability`), để không làm quá tải trace backend. Dev/test có thể đặt 1.0.

### 26. Đóng gói và triển khai

**Tóm tắt**
- Executable jar ("fat jar") = loader của Boot + `BOOT-INF/classes` + `BOOT-INF/lib/*.jar`. Tiện cho dev. Production trong container nên **extract** ra (`java -Djarmode=tools -jar app.jar extract`) để start nhanh hơn, layer Docker cache tốt hơn, và dùng được CDS/AOT cache.
- Docker image: build nhiều stage, tách layer `dependencies` / `spring-boot-loader` / `snapshot-dependencies` / `application`. Đổi code chỉ đẩy lại layer nhỏ nhất. Hoặc dùng Cloud Native Buildpacks (`mvn spring-boot:build-image`) để khỏi viết Dockerfile.
- JVM trong container: JVM hiện đại đã container-aware. Đặt heap theo phần trăm (`-XX:MaxRAMPercentage`), không đặt `-Xmx` bằng memory limit của pod.
- Graceful shutdown bật mặc định (từ Boot 3.4). Timeout mỗi phase mặc định 30s (`spring.lifecycle.timeout-per-shutdown-phase`). Kết hợp với `preStop` và `terminationGracePeriodSeconds` của Kubernetes.
- Tối ưu startup: CDS / AOT cache của Project Leyden (Java 24+; docs Boot khuyên dùng AOT cache thay CDS từ Java 24, Dockerfile mẫu một bước bên dưới cần Java 25+) giữ nguyên JVM; GraalVM native image (cần GraalVM 25+ với Boot 4.1) cho startup/memory tốt nhất nhưng tốn nhiều cái giá; CRaC là hướng thứ ba.

**Cơ chế bên dưới**
- Fat jar: `MANIFEST.MF` có `Main-Class` là launcher của Boot (Boot 3.2+: `org.springframework.boot.loader.launch.JarLauncher`), `Start-Class` là class `main` của bạn. Launcher tạo classloader đọc jar lồng nhau. `BOOT-INF/classpath.idx` giữ thứ tự classpath, `BOOT-INF/layers.idx` định nghĩa layer cho image.
- Dockerfile theo docs Boot 4.1 (rút gọn; `-XX:MaxRAMPercentage` là phần thêm vào, không có trong ví dụ gốc):

```dockerfile
FROM bellsoft/liberica-openjre-debian:25-cds AS builder
WORKDIR /builder
COPY target/*.jar application.jar
RUN java -Djarmode=tools -jar application.jar extract --layers --destination extracted

FROM bellsoft/liberica-openjre-debian:25-cds
WORKDIR /application
COPY --from=builder /builder/extracted/dependencies/ ./
COPY --from=builder /builder/extracted/spring-boot-loader/ ./
COPY --from=builder /builder/extracted/snapshot-dependencies/ ./
COPY --from=builder /builder/extracted/application/ ./
# Training run tạo AOT cache (cú pháp một bước này cần Java 25+). JDK trước 24 không có AOT cache: dùng CDS với -XX:ArchiveClassesAtExit
RUN java -XX:AOTCacheOutput=app.aot -Dspring.context.exit=onRefresh -jar application.jar
ENTRYPOINT ["java", "-XX:AOTCache=app.aot", "-XX:MaxRAMPercentage=75", "-jar", "application.jar"]
```

- Lưu ý: jar mode cũ `-Djarmode=layertools` đã deprecated (thay bằng `-Djarmode=tools` từ Boot 3.3) và **bị gỡ hẳn ở Boot 4.1**. Dockerfile cũ dùng `layertools` sẽ fail khi nâng lên 4.1. Training run với `-Dspring.context.exit=onRefresh` khởi động context rồi thoát ngay. Nếu bean khởi tạo gọi DB hay service ngoài lúc refresh, training run trong CI sẽ fail hoặc treo.
- Graceful shutdown trên Kubernetes, theo thứ tự:
  1. Pod bị xóa → kubelet chạy `preStop` (ví dụ `sleep 10`) **song song** với việc endpoint controller gỡ pod khỏi Service. `preStop` cho kube-proxy/Ingress kịp cập nhật, tránh request mới đến pod đang tắt.
  2. SIGTERM → Boot đóng context: Readiness chuyển `REFUSING_TRAFFIC`, web server ngừng nhận kết nối mới, chờ request đang chạy tối đa `timeout-per-shutdown-phase`.
  3. Sau `terminationGracePeriodSeconds` (mặc định 30s) mà chưa xong → SIGKILL.
  Quy tắc: `terminationGracePeriodSeconds` > thời gian `preStop` + timeout shutdown của Boot + thời gian đóng các bean khác.
- Buildpacks: `spring-boot:build-image` dùng builder Paketo (mặc định `paketobuildpacks/builder-noble-java-tiny`), tự đọc `layers.idx`, tự tính cấu hình bộ nhớ JVM theo memory limit (memory calculator), cần Docker daemon lúc build.
- Native image: Spring AOT chạy lúc build (`process-aot`) để sinh code định nghĩa bean và các hint (reflection, resource, proxy, serialization). GraalVM phân tích "closed world": classpath cố định lúc build, tập bean không đổi lúc runtime, `@Profile`/profile-specific config bị giới hạn, `@ConditionalOnProperty` và các property `.enabled` không được đánh giá lại lúc runtime. Hint tự khai báo: `RuntimeHintsRegistrar` + `@ImportRuntimeHints`, `@RegisterReflectionForBinding` (package `org.springframework.aot.hint`).

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| JVM + jar extract + AOT cache/CDS | Mặc định cho service chạy lâu | Thêm bước training run trong build. Cache gắn với đúng phiên bản app và JDK |
| Buildpacks | Team không muốn duy trì Dockerfile, cần base image được vá đều đặn | Ít kiểm soát chi tiết. Build cần Docker daemon, lần đầu chậm |
| Dockerfile tự viết | Cần base image công ty, agent, công cụ chẩn đoán riêng | Tự chịu trách nhiệm vá base image, cấu hình JVM |
| GraalVM native image | Serverless, scale-to-zero, CLI, rất nhiều instance nhỏ, cần start dưới 1 giây | Build lâu và tốn RAM; reflection/proxy/resource phải có hint; `@Profile`/`@ConditionalOnProperty` bị cố định lúc build; peak throughput thường thấp hơn JIT; debug/profiling khó hơn; thư viện bên thứ ba có thể chưa tương thích |
| CRaC | Cần start cực nhanh mà vẫn giữ JIT HotSpot | Chỉ Linux, cần JDK có CRaC (Azul, Liberica). Phải xử lý tài nguyên (socket, file, secret) khi checkpoint. Snapshot có thể chứa secret trong bộ nhớ |

**Pitfalls trong production**
- Pod bị OOMKilled dù heap chưa đầy → `-Xmx` bằng memory limit, quên metaspace, thread stack, direct buffer, code cache → heap khoảng 50–75% limit (`-XX:MaxRAMPercentage`), theo dõi RSS của container, không chỉ heap.
- Heap quá nhỏ dù pod có nhiều RAM → không cấu hình gì, JVM mặc định `MaxRAMPercentage=25` → đặt tường minh.
- Lỗi 502/connection reset mỗi lần deploy → không có `preStop`, pod nhận SIGTERM trước khi Ingress/kube-proxy gỡ nó → thêm `preStop` sleep ngắn, kiểm tra readiness và graceful shutdown.
- Deploy bị treo 30s rồi pod mới bị kill → có request dài hoặc consumer không dừng → xem request nào dài, đặt timeout hợp lý, đảm bảo `@Scheduled`/consumer tôn trọng shutdown.
- Native image chạy lỗi `ClassNotFoundException`/thiếu resource/JSON rỗng lúc runtime dù build thành công → reflection/resource không có hint (DTO của Jackson, thư viện bên thứ ba) → thêm `RuntimeHintsRegistrar` hoặc `@RegisterReflectionForBinding`, chạy test ở chế độ native trong CI.
- Image mỗi lần build nặng hàng trăm MB → copy nguyên fat jar thành một layer → dùng layer extract hoặc buildpacks.

**Liên hệ với demo**
Demo chạy `java -jar target/product-api-0.0.1-SNAPSHOT.jar`. Trong container, extract jar, dùng `-XX:MaxRAMPercentage`, giữ graceful shutdown mặc định và cấu hình `preStop` + probes (xem mục 24).

**Câu hỏi kiểm tra**
- **H:** Vì sao không đặt `-Xmx` bằng memory limit của container?
  **Đ:** Tổng bộ nhớ JVM gồm heap cộng metaspace, thread stack, code cache, direct/NIO buffer, GC overhead. Heap bằng limit thì RSS vượt limit và kernel OOM-kill container, không có `OutOfMemoryError` để chẩn đoán.
- **H:** Mô tả chuỗi sự kiện graceful shutdown trên Kubernetes và các thông số phải khớp nhau.
  **Đ:** preStop → SIGTERM → readiness REFUSING, server ngừng nhận mới, xử lý nốt request trong `timeout-per-shutdown-phase` → đóng bean → thoát. `terminationGracePeriodSeconds` phải lớn hơn tổng preStop và các timeout đó.
- **H:** Khi nào chọn native image thay vì JVM + AOT cache?
  **Đ:** Khi startup và memory footprint quan trọng hơn peak throughput và tốc độ build: serverless, scale-to-zero, CLI. Service chạy lâu, nhiều traffic thì JVM với JIT và AOT cache thường là lựa chọn cân bằng hơn.
- **H:** `jarmode=tools extract` giúp gì cho Docker?
  **Đ:** Tách dependency (ít thay đổi) khỏi code ứng dụng (thay đổi thường xuyên) thành các layer riêng, nên rebuild/pull chỉ tốn layer nhỏ. Layout extract cũng tương thích sẵn với CDS/AOT cache.

### 27. Hiệu năng và resilience

**Tóm tắt**
- Đo trước, tối ưu sau. Dựa vào p95/p99 và tỷ lệ lỗi dưới tải thực tế, không dựa vào trung bình hay cảm giác.
- Mọi lời gọi qua mạng phải có timeout: HTTP client, DB connection pool, query, transaction, và timeout tổng của request. Thiếu một tầng là đủ để cạn thread khi dependency chậm.
- Retry chỉ cho thao tác idempotent, có exponential backoff + jitter và giới hạn số lần. Retry sai cách biến sự cố nhỏ thành "retry storm".
- Circuit breaker, bulkhead, rate limiter bảo vệ hệ thống khỏi dependency chậm/lỗi. Framework 7 có sẵn `@Retryable` và `@ConcurrencyLimit` (không có circuit breaker). Cần circuit breaker thì dùng Resilience4j (có artifact `resilience4j-spring-boot4`) hoặc Spring Cloud CircuitBreaker.
- Little's Law (`L = λ × W`) là công cụ nhanh nhất để ước lượng số thread, connection pool, và kiểm tra cấu hình có hợp lý không.

**Cơ chế bên dưới**
- Tomcat (Boot 4, prefix `server.tomcat.*`, giá trị mặc định lấy từ `TomcatServerProperties`):

| Property | Mặc định | Ý nghĩa |
|---|---|---|
| `threads.max` | 200 | Số worker thread xử lý request đồng thời |
| `threads.min-spare` | 10 | Số thread luôn giữ sẵn |
| `max-connections` | 8192 | Số kết nối TCP Tomcat giữ (kể cả keep-alive đang rảnh) |
| `accept-count` | 100 | Hàng đợi của OS khi đã đạt `max-connections` |

  Khi 200 thread đều bận (thường vì chờ DB/HTTP chậm), request mới xếp hàng, latency tăng vọt. Tăng `threads.max` hiếm khi là cách sửa đúng: nó chỉ dời điểm nghẽn sang connection pool hoặc dependency.
- Virtual threads (`spring.threads.virtual.enabled=true`, Java 21+): mỗi request một virtual thread, không còn giới hạn 200 worker. Nhưng tài nguyên phía sau (Hikari pool mặc định 10 connection, quota của API ngoài) vẫn hữu hạn. Phải giới hạn đồng thời ở đó (xem `@ConcurrencyLimit` bên dưới). Cơ chế, những gì Boot đổi khi bật, và rủi ro pinning trên Java 21: xem mục 22.
- Timeout theo tầng (từ ngoài vào trong, tầng trong ngắn hơn tầng ngoài):

```yaml
spring:
  http.clients:                   # Boot 4: cho các HTTP client do Boot cấu hình (RestClient, HTTP interface...)
    connect-timeout: 2s
    read-timeout: 5s
  datasource.hikari:
    connection-timeout: 3000      # ms chờ lấy connection từ pool (mặc định của Hikari là 30s, quá dài cho API)
    maximum-pool-size: 20
  transaction.default-timeout: 10s
  jpa.properties:
    jakarta.persistence.query.timeout: 5000   # ms, gợi ý timeout cho query
```

- Framework 7 resilience (package `org.springframework.resilience.annotation`, cần `@EnableResilientMethods` trên một `@Configuration`; API và giá trị mặc định: xem mục 7). Mặc định `multiplier` 1.0 và không jitter, nên phải tự đặt để có backoff thật:

```java
import org.springframework.resilience.annotation.ConcurrencyLimit;
import org.springframework.resilience.annotation.Retryable;

@Service
class InventoryClient {
    @Retryable(includes = ResourceAccessException.class,
               maxRetries = 3, delay = 200, multiplier = 2, jitter = 100, maxDelay = 2000)
    @ConcurrencyLimit(20)          // tối đa 20 lời gọi đồng thời; mặc định policy BLOCK (7.0.3+ có REJECT)
    public StockDto getStock(String sku) { /* GET, idempotent */ }
}
```

  Cả hai là AOP proxy: self-invocation không có tác dụng (giống `@Transactional`).
- Little's Law: số request đồng thời = throughput × thời gian xử lý. Ví dụ 400 req/s, mỗi request giữ DB connection 25ms → trung bình cần 400 × 0.025 = 10 connection; cộng dư cho đỉnh tải thì khoảng 15–20. Cùng tải đó mà latency tăng lên 500ms (dependency chậm) thì cần 200 request đồng thời, vừa đúng lúc Tomcat cạn thread.

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `@Retryable`/`@ConcurrencyLimit` của Framework 7 | Retry/giới hạn đồng thời đơn giản, không muốn thêm thư viện | Không có circuit breaker, rate limiter, time limiter; ít metric có sẵn |
| Resilience4j | Cần circuit breaker, bulkhead, rate limiter, time limiter, cấu hình riêng từng dependency, metric qua Micrometer | Thêm thư viện và cấu hình. Phải hiểu thứ tự áp dụng các decorator |
| Spring Cloud CircuitBreaker | Muốn abstraction chung, có thể đổi implementation | Thêm một lớp trừu tượng; phải chọn release train Spring Cloud tương thích với Boot đang dùng |
| Rate limit ở gateway/API management | Bảo vệ theo client/API key, chặn sớm, thống nhất cho nhiều service | Không biết ngữ cảnh nghiệp vụ trong app |
| Rate limit trong app | Giới hạn theo tài nguyên nội bộ (ví dụ API đối tác có quota) | Mỗi instance đếm riêng; cần store chung (Redis) nếu muốn giới hạn toàn cụm |
| Tăng `threads.max` | Đã chứng minh CPU còn dư và nút cổ chai là số thread | Nhiều thread hơn = nhiều bộ nhớ, context switch, dồn tải xuống DB |

**Pitfalls trong production**
- Một dependency chậm làm toàn bộ API treo → HTTP client không có read timeout, thread Tomcat bị giữ hết → đặt connect/read timeout, bulkhead hoặc `@ConcurrencyLimit` riêng cho từng dependency, circuit breaker để fail fast.
- Sự cố nhỏ ở service B biến thành sập hẳn → A retry 3 lần, gateway retry 3 lần, client retry 3 lần, tải lên B nhân lên nhiều lần → retry ở một tầng duy nhất, có backoff + jitter, có giới hạn; circuit breaker ngắt khi tỷ lệ lỗi cao.
- Tạo đơn hàng hai lần → retry `POST` không idempotent sau read timeout (request thực ra đã thành công ở server) → chỉ retry thao tác idempotent, hoặc dùng idempotency key lưu phía server.
- Retry không có tác dụng với lỗi DB → retry nằm **trong** transaction đã bị đánh dấu rollback-only (Spring Retry, order tự chỉnh, hoặc retry gọi từ bên trong method `@Transactional`; xem mục 4) → retry phải bọc **ngoài** transaction, mỗi lần thử là một transaction mới.
- `HikariPool-1 - Connection is not available, request timed out after 30000ms` → pool cạn vì connection bị giữ lâu (gọi HTTP trong transaction, OSIV bật, query chậm) → rút lời gọi remote ra khỏi transaction, tắt OSIV, sửa query; không chỉ tăng pool.
- Benchmark "rất nhanh" nhưng production chậm → đo khi JVM chưa warm-up, dữ liệu test nhỏ, không có network latency, nhìn trung bình → load test với dữ liệu gần thật, bỏ qua giai đoạn warm-up, xem p95/p99 và error rate khi tải tăng dần.

**Công cụ đo**
- JFR (JDK Flight Recorder): overhead thấp, bật được trên production (`-XX:StartFlightRecording` hoặc `jcmd <pid> JFR.start`), xem bằng JDK Mission Control. Tốt cho GC, lock contention, allocation, I/O.
- async-profiler: flame graph CPU/allocation/wall-clock, tìm hot method chính xác.
- Load test: Gatling hoặc k6, kịch bản tăng tải dần, đo throughput, p95/p99, error rate; chạy trên môi trường giống production.

**Liên hệ với demo**
Demo không có dependency ngoài và chạy H2 in-memory, nên số đo trên máy dev không nói lên gì về production. Trước khi load test: thay H2 bằng DB thật, bật Actuator để theo dõi `http.server.requests` và `jdbc.connections.*`, rồi đối chiếu với Little's Law (Tomcat 200 thread, Hikari mặc định 10 connection). Không đặt `@Retryable` lên `create()`: POST không idempotent.

**Câu hỏi kiểm tra**
- **H:** Service xử lý 300 req/s, mỗi request trung bình 150ms, trong đó 100ms chờ DB. Cần khoảng bao nhiêu thread và connection?
  **Đ:** Thread: 300 × 0.15 = 45 request đồng thời trung bình. Connection (nếu chỉ giữ connection trong lúc chạy query): 300 × 0.1 = 30. Cộng dư cho đỉnh tải và kiểm tra DB chịu được tổng connection của mọi instance.
- **H:** Vì sao retry cần jitter?
  **Đ:** Không có jitter, mọi client lỗi cùng lúc sẽ retry cùng lúc theo cùng lịch, tạo các đợt tải đồng bộ đập vào dependency đang hồi phục. Jitter rải đều các lần thử.
- **H:** `@Retryable` của Framework 7 khác Resilience4j thế nào?
  **Đ:** Framework 7 chỉ có retry và concurrency limit (annotation hoặc `RetryTemplate` trong `org.springframework.core.retry`), không có circuit breaker hay rate limiter. Đủ cho retry đơn giản. Cần circuit breaker, bulkhead cấu hình riêng từng dependency và metric chi tiết thì dùng Resilience4j.
- **H:** Bật virtual threads có giải quyết được việc cạn connection pool không?
  **Đ:** Không. Virtual threads bỏ giới hạn số thread, nhưng connection pool và quota phía sau vẫn hữu hạn. Nhiều request tranh connection hơn còn có thể làm tình trạng tệ đi. Vẫn cần giới hạn đồng thời và timeout.

### 28. Chiến lược testing cho cả team

**Tóm tắt**
- Chọn đúng "kích thước" test cho đúng rủi ro: unit test cho logic nghiệp vụ, slice test cho từng tầng tích hợp với framework (web, JPA, JSON, HTTP client), `@SpringBootTest` + Testcontainers cho luồng đầu-cuối quan trọng.
- Test DB bằng đúng loại DB production (Testcontainers + `@ServiceConnection`), không bằng H2. H2 khác dialect, khác hành vi lock, constraint, kiểu dữ liệu.
- Tốc độ CI phụ thuộc chủ yếu vào **số lần tạo ApplicationContext**. Context cache chỉ tái sử dụng khi cấu hình giống hệt nhau. Mỗi `@MockitoBean` khác nhau, mỗi profile/property khác nhau là một context mới.
- Boot 4: `@MockBean`/`@SpyBean` đã bị gỡ, dùng `@MockitoBean`/`@MockitoSpyBean` của Framework. Mỗi slice có starter test riêng theo module (`spring-boot-starter-webmvc-test`, `spring-boot-starter-data-jpa-test`...).
- Kiến trúc cũng cần test: ArchUnit hoặc Spring Modulith `ApplicationModules.verify()` chặn vi phạm phụ thuộc ngay trong CI.

**Cơ chế bên dưới**
- Test pyramid (nhiều unit, ít E2E) phù hợp khi logic nằm trong domain. Với microservice mỏng, chủ yếu là "glue" giữa HTTP và DB, mô hình honeycomb (nhiều integration test, ít unit test thuần và ít E2E) thường cho độ tin cậy cao hơn trên cùng chi phí.
- Slice test và package ở Boot 4:

| Annotation | Package (Boot 4) | Nạp gì |
|---|---|---|
| `@WebMvcTest` | `org.springframework.boot.webmvc.test.autoconfigure` | Controller, advice, converter, filter, `MockMvc`. Không nạp `@Service`/`@Repository` |
| `@DataJpaTest` | `org.springframework.boot.data.jpa.test.autoconfigure` | Entity, repository, `TestEntityManager`; mỗi test là một transaction rollback |
| `@JsonTest` | `org.springframework.boot.test.autoconfigure.json` | Jackson mapper, `JacksonTester` để kiểm tra format JSON |
| `@RestClientTest` | `org.springframework.boot.restclient.test.autoconfigure` | `RestClient.Builder`/`RestTemplateBuilder` + `MockRestServiceServer` |
| `@SpringBootTest` | `org.springframework.boot.test.context` | Toàn bộ context |

- Testcontainers + `@ServiceConnection` (module `spring-boot-testcontainers`, package `org.springframework.boot.testcontainers.service.connection`): Boot tạo `JdbcConnectionDetails` từ container, không cần `@DynamicPropertySource`. Testcontainers 2.x đổi tên artifact (ví dụ `testcontainers-postgresql`). Từ Boot 3.4, `@AutoConfigureTestDatabase` tự nhận ra DB lấy từ container, nên `@DataJpaTest` dùng được với container mà không cần `replace = Replace.NONE`.

```java
@TestConfiguration(proxyBeanMethods = false)
public class TestcontainersConfig {
    @Bean
    @ServiceConnection
    PostgreSQLContainer postgres() {             // Testcontainers 2.x: org.testcontainers.postgresql
        return new PostgreSQLContainer("postgres:17-alpine");
    }
}

@SpringBootTest
@AutoConfigureMockMvc
@Import(TestcontainersConfig.class)
public abstract class IntegrationTestBase {       // mọi IT kế thừa → một context duy nhất
    @MockitoBean PaymentGateway paymentGateway;   // khai báo mock MỘT lần ở đây
}
```

- Context cache: key là `MergedContextConfiguration` (class cấu hình, profile, property, `ContextCustomizer` như bean override). Mặc định giữ tối đa 32 context (`spring.test.context.cache.maxSize`), vượt thì loại context ít dùng nhất. Framework 7 còn "pause" context không dùng (dừng các `SmartLifecycle` như scheduler, listener) và tự khởi động lại khi cần; từ 7.0.3 điều khiển bằng `spring.test.context.cache.pause` (xem mục 7).
- Những thứ làm vỡ cache: bộ `@MockitoBean`/`@MockitoSpyBean` khác nhau giữa các class; `@ActiveProfiles`, `@TestPropertySource`, `properties = ...` khác nhau; `@DirtiesContext`; `@Import` cấu hình test khác nhau; `webEnvironment` khác nhau.
- Data isolation: với `webEnvironment = RANDOM_PORT`, request chạy ở thread của server, **không** nằm trong transaction của test, nên `@Transactional` trên test không rollback được dữ liệu. Cách làm: dữ liệu duy nhất cho từng test (SKU ngẫu nhiên), hoặc dọn bảng trong `@BeforeEach`/`@AfterEach` (`JdbcTemplate`, `@Sql`).

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Unit test thuần (Mockito) | Logic nghiệp vụ, tính toán, quy tắc | Không bắt được lỗi mapping, cấu hình, SQL, JSON |
| Slice test | Kiểm tra một tầng với framework thật (validation, mapping JSON, query) | Mỗi slice là một context riêng; dùng nhiều mock khác nhau thì cache kém |
| `@SpringBootTest` + Testcontainers | Luồng quan trọng đầu-cuối, migration, transaction, constraint | Chậm hơn, cần Docker trên máy dev và CI |
| H2 thay DB thật | Demo, prototype | Test xanh nhưng production lỗi (dialect, lock, JSON/array type, sequence) |
| Contract test (Spring Cloud Contract, Pact) | Nhiều team, nhiều consumer gọi một API | Thêm quy trình và hạ tầng (broker, stub) |
| ArchUnit / Modulith verify | Muốn giữ ranh giới layer/module khi team lớn dần | Phải duy trì bộ quy tắc; quy tắc quá chặt gây phiền |

**Pitfalls trong production**
- CI chạy 20 phút, log thấy Spring banner in ra hàng chục lần → mỗi class test có tổ hợp `@MockitoBean`/property riêng → gom vào vài base class, chuẩn hóa profile test, cấm `@DirtiesContext` nếu không có lý do được review.
- Test pass ở local, fail ngẫu nhiên trên CI → dữ liệu dùng chung giữa các test (context cache dùng chung DB), thứ tự test thay đổi → dữ liệu duy nhất mỗi test hoặc dọn dữ liệu; không phụ thuộc thứ tự.
- Query chạy được trên H2 nhưng lỗi trên PostgreSQL → dùng H2 cho test → Testcontainers với đúng image/phiên bản production.
- `@WebMvcTest` báo `NoSuchBeanDefinitionException` → controller cần service nhưng slice không nạp `@Service` → khai báo `@MockitoBean` cho dependency đó.
- Nâng Boot 4, test không compile → `@MockBean`, `@AutoConfigureMockMvc` ở package cũ, thiếu test starter theo module → dùng `@MockitoBean` (`org.springframework.test.context.bean.override.mockito`), đổi import, thêm starter test tương ứng.
- Test "xanh" nhưng không kiểm tra gì → chỉ kiểm tra status 200, mock trả về chính giá trị được assert → review test như review code: assert trên hành vi và dữ liệu thật.

**Liên hệ với demo**
Demo dùng H2 và `@SpringBootTest` MOCK với SKU riêng cho mỗi test vì không rollback. Production nên có base class dùng chung, Testcontainers cho DB thật, và `@WebMvcTest` cho các case 400/404/409.

**Câu hỏi kiểm tra**
- **H:** Vì sao thêm `@MockitoBean` vào một class test lại làm cả suite chậm đi?
  **Đ:** Bean override là một phần của cache key. Bộ mock khác nhau thì context khác nhau, Spring phải khởi động context mới. Gom mock vào base class dùng chung.
- **H:** Tại sao `@Transactional` trên test không rollback với `RANDOM_PORT`?
  **Đ:** Request đi qua HTTP thật, được xử lý ở thread của server, trong transaction riêng đã commit. Transaction của test ở thread khác nên không bao được.
- **H:** Testcontainers + `@ServiceConnection` hơn `@DynamicPropertySource` ở điểm nào?
  **Đ:** Boot tự tạo `ConnectionDetails` từ container, ít code, không phải nhớ tên property, và dùng chung được cho cả `spring-boot:test-run` khi chạy app local với container.
- **H:** Khi nào nên đầu tư contract test?
  **Đ:** Khi provider và consumer do các team khác nhau phát triển và deploy độc lập. Contract test phát hiện thay đổi phá vỡ API trước khi deploy, rẻ hơn E2E test chạy cả hệ thống.

### 29. Phong cách kiến trúc

**Tóm tắt**
- Không có kiến trúc "đúng" tuyệt đối. Chọn theo độ phức tạp nghiệp vụ, quy mô team, và tốc độ thay đổi. Kiến trúc tốt là kiến trúc làm cho thay đổi thường gặp trở nên rẻ.
- Layered theo kỹ thuật (controller/service/repository như demo) đơn giản nhưng không mở rộng tốt khi hệ thống lớn. Package-by-feature và modular monolith giữ ranh giới nghiệp vụ rõ hơn.
- Hexagonal/clean đáng giá khi domain phức tạp và có nhiều adapter. Với CRUD đơn giản, nó thường chỉ thêm lớp mapping và interface.
- Modular monolith với Spring Modulith (2.x cho Boot 4: 2.0.x cho Boot 4.0, 2.1.x cho Boot 4.1) là điểm khởi đầu hợp lý trước microservices: ranh giới module được kiểm chứng tự động, giao tiếp bằng event.
- Microservices giải bài toán tổ chức (nhiều team deploy độc lập), không phải bài toán hiệu năng. Cái giá là vận hành, dữ liệu phân tán, testing và observability phức tạp hơn nhiều.

**Cơ chế bên dưới**
- Package-by-feature: `com.example.shop.product`, `...order`, `...customer`, mỗi package chứa controller, service, repository, entity của feature đó. Class nội bộ để package-private. Java không có "module-private" giữa các package con, nên cần công cụ để giữ ranh giới (ArchUnit, Modulith, hoặc JPMS).
- Hexagonal (ports & adapters): domain và use case ở trung tâm, không import Spring/JPA. Port = interface do domain định nghĩa (`LoadOrderPort`). Adapter = cài đặt bằng công nghệ cụ thể (JPA, REST, Kafka). Với Spring: domain là POJO, adapter là bean Spring, wiring trong `@Configuration`.
- Spring Modulith:
  - Mặc định mỗi **package con trực tiếp** của package chứa class `@SpringBootApplication` là một application module. Type ở package gốc của module là API công khai. Type ở package con của module (ví dụ `order.internal`) là nội bộ, module khác không được dùng dù chúng `public`.
  - `ApplicationModules.of(Application.class).verify()` (dựa trên ArchUnit) fail khi có vòng phụ thuộc giữa các module hoặc truy cập type nội bộ của module khác.
  - Giao tiếp giữa module bằng event: `@ApplicationModuleListener` = listener chạy sau commit, bất đồng bộ, trong transaction mới. Event Publication Registry lưu event vào DB cùng transaction nghiệp vụ và cho phép gửi lại event chưa hoàn thành (xem mục 5, 14).

```java
// Module order publish event, không gọi trực tiếp service của module inventory
@Service
class OrderService {
    private final ApplicationEventPublisher events;
    OrderService(ApplicationEventPublisher events) { this.events = events; }

    @Transactional
    public void complete(Order order) {
        order.complete();
        events.publishEvent(new OrderCompleted(order.id()));   // lưu vào registry cùng transaction
    }
}

// Module inventory
@Component
class InventoryUpdater {
    @ApplicationModuleListener
    void on(OrderCompleted event) { /* trừ tồn kho, transaction riêng, sau commit */ }
}

// Test kiến trúc
class ModularityTests {
    @Test void verify() { ApplicationModules.of(ShopApplication.class).verify(); }
}
```

- Spring Cloud và Kubernetes giải quyết nhiều vấn đề trùng nhau:

| Nhu cầu | Spring Cloud | Kubernetes-native |
|---|---|---|
| Service discovery | Eureka/Consul + load balancer phía client | `Service` + DNS của cluster |
| Cấu hình tập trung | Config Server (Git) | `ConfigMap`/`Secret` gắn qua env hoặc file (Boot đọc bằng `spring.config.import=configtree:`) |
| API gateway | Spring Cloud Gateway | Ingress/Gateway API controller, service mesh |
| Resilience | Spring Cloud CircuitBreaker + Resilience4j | Service mesh (retry, timeout, outlier detection) |
| HTTP client khai báo | OpenFeign (**feature-complete**, chỉ nhận bugfix, Spring Cloud khuyên chuyển sang HTTP Service Clients) | HTTP interface của Framework (`@HttpExchange`, Framework 7 có `@ImportHttpServices`, Boot 4 cấu hình qua `spring.http.serviceclient.<group>.*`) |

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Layered theo kỹ thuật | App nhỏ, một team, CRUD là chính | Feature trải trên nhiều package; service phình to; ranh giới nghiệp vụ mờ dần |
| Package-by-feature | Hầu hết ứng dụng nghiệp vụ vừa và lớn | Cần kỷ luật và công cụ để các feature không gọi lung tung vào nhau |
| Hexagonal / clean | Domain phức tạp, sống lâu, nhiều adapter (REST, messaging, batch), muốn test domain không cần Spring | Nhiều interface và mapping (entity JPA ↔ domain model), team cần hiểu rõ mới làm đúng |
| Modular monolith (Spring Modulith) | Một hoặc vài team, muốn ranh giới rõ, giữ khả năng tách service sau này | Vẫn deploy chung, scale chung; một module lỗi bộ nhớ ảnh hưởng cả app |
| Microservices | Nhiều team cần deploy độc lập, yêu cầu scale/công nghệ khác nhau rõ rệt giữa các phần | Giao dịch phân tán (saga, outbox), nhất quán cuối cùng, network failure, observability, contract testing, chi phí platform |
| Spring Cloud (Config, Gateway, discovery) | Không chạy trên Kubernetes, hoặc cần tính năng mà platform không có | Thêm hạ tầng phải vận hành; phải khớp release train Spring Cloud với Boot (2025.1.x "Oakwood" cho Boot 4.0.x và 4.1.x; với Boot 4.1 dùng patch 2025.1 mới nhất và kiểm tra bảng tương thích trên start.spring.io) |
| Kubernetes-native | Đã chạy trên Kubernetes | Cấu hình rải ở YAML của platform; dev local cần môi trường giống (kind, Docker Compose) |

**Pitfalls trong production**
- "Distributed monolith": mỗi thay đổi phải deploy 5 service cùng lúc → tách service theo layer kỹ thuật hoặc theo entity thay vì theo năng lực nghiệp vụ, gọi đồng bộ dây chuyền → tách theo bounded context, giao tiếp bất đồng bộ khi có thể, mỗi service sở hữu dữ liệu riêng.
- Hai service cùng ghi vào một schema → "tách service" nhưng chung DB → mỗi service sở hữu schema; service khác lấy dữ liệu qua API hoặc event.
- Mất event khi app crash giữa lúc commit DB và gửi Kafka → dual write không nguyên tử → outbox pattern (Event Publication Registry của Modulith, hoặc bảng outbox + CDC).
- Package `common`/`util` phình thành nơi mọi module phụ thuộc → đẩy code dùng chung "cho tiện" vào đó → code thuộc module nào để ở module đó; chỉ chia sẻ kiểu dữ liệu thực sự dùng chung và ổn định.
- Hexagonal nửa vời: domain model vẫn là entity JPA, port chỉ là wrapper mỏng của repository → thêm lớp mà không được lợi ích → hoặc làm đúng (domain không phụ thuộc JPA), hoặc giữ package-by-feature đơn giản.
- `@ApplicationModuleListener` fail âm thầm → listener chạy bất đồng bộ sau commit, lỗi không quay về người gọi → dùng Event Publication Registry, giám sát publication chưa hoàn thành, thiết kế listener idempotent.

**Liên hệ với demo**
Demo là layered theo kỹ thuật (`controller`, `service`, `repository`...) cho một entity, phù hợp với quy mô đó. Khi có thêm `order`, `customer`, nên chuyển sang package-by-feature (`com.example.product`, `com.example.order`) và thêm test `ApplicationModules.verify()`.

**Câu hỏi kiểm tra**
- **H:** Khi nào bạn khuyên tách microservice từ một monolith?
  **Đ:** Khi có nhu cầu tổ chức hoặc vận hành rõ ràng: các team bị chặn nhau khi deploy, một phần cần scale hoặc SLA khác hẳn, ranh giới bounded context đã ổn định. Trước đó, modular monolith cho phần lớn lợi ích với chi phí thấp hơn nhiều.
- **H:** Spring Modulith xác định module và ranh giới như thế nào?
  **Đ:** Mỗi package con trực tiếp của package ứng dụng là một module. Package gốc của module là API, package con là nội bộ. `ApplicationModules.verify()` fail khi có vòng phụ thuộc hoặc truy cập vào phần nội bộ.
- **H:** Đang chạy trên Kubernetes, có cần Eureka và Config Server không?
  **Đ:** Thường là không. Service discovery dùng `Service`/DNS, cấu hình dùng ConfigMap/Secret. Config Server còn đáng dùng khi cần lịch sử cấu hình qua Git, refresh động, hoặc chạy lẫn môi trường ngoài Kubernetes.
- **H:** Vì sao không nên dùng OpenFeign cho dự án mới trên Boot 4?
  **Đ:** Spring Cloud OpenFeign đã feature-complete, chỉ nhận bugfix. Framework 7 và Boot 4 có HTTP Service Clients (`@HttpExchange`, `@ImportHttpServices`, cấu hình theo group) dựa trên `RestClient`, không phụ thuộc Spring Cloud.

### 30. Nâng cấp phiên bản và quản lý dependency

**Tóm tắt**
- `spring-boot-dependencies` (BOM) quản lý phiên bản của hàng trăm thư viện đã được test cùng nhau. Không tự ghi version cho dependency mà BOM đã quản lý, trừ khi có lý do (CVE) và có ghi chú.
- Mỗi minor của Boot có khoảng 13 tháng OSS support; commercial support thường kéo dài thêm khoảng 12 tháng (riêng 3.5.x, dòng 3.x cuối cùng, có commercial support đến 2032-06-30). Tại 2026-09: **3.5.x đã hết OSS support (2026-06-30)**, 4.0.x hết OSS vào 2026-12-31, 4.1.x đến 2027-07-31. Hệ thống còn ở Boot 3 cần lên kế hoạch nâng cấp ngay.
- Nâng cấp từng minor một, luôn lên patch mới nhất của minor hiện tại trước, đọc release notes và migration guide, dùng `spring-boot-properties-migrator` và OpenRewrite để tự động hóa phần cơ học.
- Boot 3 → 4 là major: Jakarta EE 11/Servlet 6.1, Framework 7, Jackson 3, modular starters, Undertow bị bỏ, `@MockBean` bị gỡ, nhiều package đổi.
- Java: Boot 4 yêu cầu Java 17+, Boot 4.1.1 hỗ trợ tới Java 26. Chọn LTS: Java 21 là mức an toàn, Java 25 (LTS, 9/2025) để có AOT cache và cải tiến virtual threads.

**Cơ chế bên dưới**
- Hai cách dùng BOM trong Maven:

```xml
<!-- Cách 1: parent. Override bằng property -->
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>4.1.1</version>
</parent>
<properties>
  <jackson-bom.version>3.1.6</jackson-bom.version>   <!-- ví dụ: vá CVE, bỏ khi Boot patch đã có -->
</properties>

<!-- Cách 2: import BOM (khi đã có parent công ty). Property KHÔNG còn tác dụng override -->
<dependencyManagement><dependencies>
  <dependency>   <!-- BOM muốn override phải khai báo TRƯỚC spring-boot-dependencies -->
    <groupId>tools.jackson</groupId><artifactId>jackson-bom</artifactId>
    <version>3.1.6</version><type>pom</type><scope>import</scope>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId><artifactId>spring-boot-dependencies</artifactId>
    <version>4.1.1</version><type>pom</type><scope>import</scope>
  </dependency>
</dependencies></dependencyManagement>
```

- Tên property tra trong `spring-boot-dependencies` của đúng phiên bản. Ví dụ Boot 4.1.1: `jackson-bom.version` (Jackson 3), `jackson-2-bom.version` (Jackson 2 vẫn được quản lý cho thư viện cần), `hibernate.version`, `tomcat.version`, `testcontainers.version`.
- Parent POM còn cấu hình sẵn plugin (compiler `-parameters`, resource filtering `@..@`, `spring-boot-maven-plugin`). Dùng import BOM thì phải tự cấu hình các phần này. Thiếu `-parameters` sẽ gây lỗi với `@PathVariable`/`@RequestParam` không ghi tên.
- `spring-boot-properties-migrator` (scope runtime): lúc startup in ra property đã đổi tên/bị gỡ và tạm ánh xạ sang tên mới. Chỉ dùng trong lúc nâng cấp, gỡ ra sau đó.
- OpenRewrite (`rewrite-spring`): recipe `org.openrewrite.java.spring.boot4.UpgradeSpringBoot_4_0` đổi dependency, package, annotation, property. Cũng có recipe `UpgradeSpringBoot_4_1` cho bước lên 4.1 (tài liệu đã chuyển sang docs Moderne). Các recipe Boot 4 được phát hành theo Moderne Source Available License, nên kiểm tra điều khoản license trước khi dùng. Luôn review diff; recipe không hiểu hết ngữ nghĩa.

**SBOM và chuỗi cung ứng dependency**
- Parent POM đã cấu hình sẵn `cyclonedx-maven-plugin` trong `pluginManagement` (kiểm trên parent 4.0.5): goal `makeAggregateBom` ở phase `generate-resources`, `projectType=application`, xuất ra `META-INF/sbom/application.cdx.json` trong output build. Chỉ cần khai báo plugin (`org.cyclonedx:cyclonedx-maven-plugin`) trong `<build><plugins>` là bật. Gradle: plugin CycloneDX, Boot 4 yêu cầu tối thiểu 3.0.0.
- Actuator có endpoint `sbom`: trả SBOM "application" (tự nhận file CycloneDX ở vị trí trên). Format khác hoặc vị trí khác: `management.endpoint.sbom.application.location` / `.media-type`; thêm SBOM phụ (ví dụ SBOM của base image) qua `management.endpoint.sbom.additional.<name>.location`.
- Endpoint này không được expose qua HTTP mặc định (chỉ `health`). Nếu expose, đặt sau chain bảo mật riêng cho Actuator (xem mục 23), vì SBOM liệt kê chính xác version từng thư viện, rất có ích cho kẻ tấn công.
- Trong CI, quét file SBOM (hoặc dependency tree) bằng công cụ phân tích lỗ hổng, fail build theo ngưỡng mức độ. BOM của Boot chỉ giúp các version tương thích với nhau, không đảm bảo chúng không có CVE; bản vá thường đến qua patch release, nên lên patch mới nhất đều đặn.

**Checklist Boot 3.x → 4.x (theo Migration Guide chính thức)**

| Hạng mục | Thay đổi | Việc cần làm |
|---|---|---|
| Điểm xuất phát | Lên 3.5.x mới nhất trước, dọn hết deprecation (API deprecated ở 3.x đã bị gỡ) | Build không còn warning deprecation |
| Nền tảng | Java 17+, Jakarta EE 11, Servlet 6.1, Framework 7, Kotlin 2.2+, GraalVM 25+ | Cập nhật JDK/CI, kiểm tra thư viện `javax.*` còn sót |
| Web server | **Undertow bị bỏ** (chưa hỗ trợ Servlet 6.1). Còn Tomcat 11 và Jetty 12.1 | Chuyển sang Tomcat/Jetty |
| Starters | Modular: mỗi tính năng một module/starter. `spring-boot-starter-web` → `-webmvc`; `-oauth2-client` → `-security-oauth2-client`; `-aop` → `-aspectj`. Có `spring-boot-starter-classic` để chuyển tiếp | Có thể đi hai bước: classic starters trước, sau đó bỏ classic và thêm starter còn thiếu |
| Package | Mỗi module có package `org.springframework.boot.<module>` (ví dụ health: `org.springframework.boot.health.contributor`) | Sửa import (OpenRewrite làm phần lớn) |
| Jackson 3 | Group/package `tools.jackson` (annotation vẫn `com.fasterxml.jackson.annotation`); `@JsonComponent` → `@JacksonComponent`; customizer, bean `JsonMapper`, property `spring.jackson.*` và mặc định mới: xem mục 20 | Test lại format JSON (date, null, enum). Tạm thời có `spring.jackson.use-jackson2-defaults=true` hoặc module `spring-boot-jackson2` |
| Testing | `@MockBean`/`@SpyBean` bị gỡ → `@MockitoBean`/`@MockitoSpyBean`; `@SpringBootTest` không tự có MockMvc (thêm `@AutoConfigureMockMvc`) hay `TestRestTemplate` (thêm `@AutoConfigureTestRestTemplate` + `spring-boot-resttestclient` và `spring-boot-restclient`); starter test theo module (`spring-boot-starter-webmvc-test`, `-data-jpa-test`...); `MockitoTestExecutionListener` bị gỡ nên `@Mock`/`@Captor` cần `MockitoExtension` | Sửa test trước, chạy full suite |
| Actuator | Liveness/readiness bật mặc định | Kiểm tra probe path, security rule |
| Dependency | Spring Retry không còn được BOM quản lý (dùng retry của Framework 7); `hibernate-jpamodelgen` → `hibernate-processor`; loader CLASSIC và launch script "fully executable jar" bị gỡ | Dọn build file |
| Bên thứ ba | Spring Cloud 2025.1.x, springdoc-openapi 3.x, Testcontainers 2.x, Spring Modulith 2.x | Xác định version tương thích **trước** khi nâng |

**Quyết định & trade-off**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| `spring-boot-starter-parent` | Project độc lập, không có parent công ty | Chỉ một parent; muốn parent công ty thì parent đó phải kế thừa Boot parent |
| Import BOM | Công ty có parent POM riêng | Tự cấu hình plugin; override phiên bản phải bằng BOM/dependencyManagement thay vì property |
| Theo sát mỗi minor (6 tháng/lần) | Mặc định khuyến nghị | Tốn công đều đặn, nhưng mỗi lần nhỏ và ít rủi ro |
| Ở lại một minor lâu, mua commercial support | Hệ thống ít thay đổi, quy định nghiêm | Chi phí license; nợ nâng cấp dồn lại, lần nhảy sau rất đau |
| Nhảy thẳng nhiều minor/major một lần | Hiếm khi nên | Không rõ lỗi do thay đổi nào; khó rollback |

**Pitfalls trong production**
- Lỗi `NoSuchMethodError`/`ClassNotFoundException` lúc runtime sau khi thêm thư viện → ghi version cứng cho một thư viện mà BOM đã quản lý, lệch với các module cùng họ → bỏ version cứng, hoặc override cả BOM của họ thư viện đó; kiểm tra `mvn dependency:tree`.
- Override property phiên bản "không ăn" → project dùng import BOM, không phải parent → import BOM của thư viện đó trước `spring-boot-dependencies`.
- Sau nâng cấp, API trả JSON khác (format ngày, thứ tự field, null) mà không test nào fail → không có test hợp đồng JSON → thêm `@JsonTest`/contract test trước khi nâng Jackson.
- Property cấu hình bị bỏ qua âm thầm sau nâng cấp → property đổi tên → chạy với `spring-boot-properties-migrator` ở staging, đọc log startup.
- Nâng Boot nhưng quên Spring Cloud hoặc thư viện bên thứ ba → xung đột lúc startup → kiểm tra bảng tương thích; nâng các thư viện này cùng lúc.

**Liên hệ với demo**
Demo đã dùng Boot 4.1.1 với `spring-boot-starter-webmvc` và `spring-boot-starter-webmvc-test`. Nếu học viên có project Boot 3, những điểm dễ vấp nhất là tên starter, `@MockBean` → `@MockitoBean`, `@AutoConfigureMockMvc` đổi package, và Jackson 3.

**Câu hỏi kiểm tra**
- **H:** Có parent POM công ty, làm sao override phiên bản Jackson mà Boot quản lý?
  **Đ:** Với import BOM, property như `jackson-bom.version` không tác dụng. Import `jackson-bom` với phiên bản mong muốn **trước** `spring-boot-dependencies` trong `dependencyManagement`.
- **H:** Chiến lược nâng một hệ thống từ Boot 3.2 lên 4.1?
  **Đ:** Lên 3.5.x mới nhất (qua từng minor, đọc release notes mỗi bản), dọn deprecation, chạy properties-migrator; xác định version tương thích của thư viện ngoài; lên 4.0.x (có thể qua classic starters, dùng OpenRewrite), sửa test, ổn định; rồi lên 4.1.x. Mỗi bước deploy được và đo được.
- **H:** Vì sao nói tháng 9/2026 là lúc gấp cho hệ thống còn chạy Boot 3?
  **Đ:** 3.5.x (dòng 3.x cuối) đã hết OSS support từ 30/06/2026. Không còn bản vá bảo mật miễn phí; chỉ còn commercial support.
- **H:** `spring-boot-starter-classic` dùng để làm gì?
  **Đ:** Công cụ chuyển tiếp khi nâng lên Boot 4: đưa toàn bộ module/auto-configuration lên classpath (không kéo dependency bắc cầu) để build chạy được sớm, sửa import dần; sau đó bỏ classic và thêm đúng các starter cần.

### 31. Checklist cho Tech Lead

**Tóm tắt**
- Phần lớn sự cố production với Spring không đến từ framework. Chúng đến từ một số mẫu lặp lại: proxy/transaction hiểu sai, thiếu timeout, N+1, lộ entity, nuốt exception, cấu hình dev lọt vào prod.
- Code review nên soát theo checklist cố định bên dưới để không phụ thuộc trí nhớ người review.
- Quyết định kiến trúc quan trọng được ghi thành ADR ngắn để người sau hiểu *vì sao*, không chỉ *cái gì*.
- Phỏng vấn senior: hỏi cơ chế và trade-off, không hỏi tên annotation.

**Cơ chế bên dưới — bảng soát code review**

| Dấu hiệu trong code | Vì sao nguy hiểm | Cách sửa |
|---|---|---|
| Field injection (`@Autowired` trên field) | Ẩn dependency, khó test không có context, cho phép vòng phụ thuộc | Constructor injection, field `final` |
| `@Transactional` trên method `private`, hoặc gọi method `@Transactional` từ cùng class | Proxy không chặn được lời gọi này, nên không có transaction và không ai biết | Đưa sang bean khác, hoặc dùng `TransactionTemplate` |
| Gọi HTTP/queue bên trong transaction | Giữ connection DB trong lúc chờ mạng: pool cạn, lock kéo dài; rollback không hủy được lời gọi ngoài | Gọi ngoài transaction; dùng outbox hoặc event sau commit |
| Controller trả entity JPA hoặc `Page` trực tiếp | Lộ cấu trúc DB, lazy loading lúc serialize, JSON thay đổi khi entity đổi | DTO/record; bọc kết quả phân trang trong DTO riêng |
| `catch (Exception e)` rồi log và bỏ qua, hoặc trả `null` | Mất rollback (exception không ra khỏi proxy), lỗi bị che | Để exception lan ra, xử lý tập trung bằng `@RestControllerAdvice` + `ProblemDetail` |
| HTTP client, DB query, lock không có timeout | Một dependency chậm làm cạn thread của cả service (xem mục 27) | Đặt connect/read timeout cho mọi client; query timeout; circuit breaker khi cần |
| Vòng lặp truy cập quan hệ lazy | N+1 query, chậm theo số dòng | Fetch join, `@EntityGraph`, projection; bật thống kê Hibernate khi test |
| `spring.jpa.open-in-view` để mặc định (bật) | Lazy loading chạy trong lúc render view, giữ connection suốt request | Tắt; tải đủ dữ liệu trong service |
| `ddl-auto=update` hoặc `create` ở môi trường thật | Schema thay đổi không kiểm soát, không rollback được | Flyway/Liquibase, `ddl-auto=validate` hoặc `none` |
| Kiểm tra "exists rồi insert" (như `existsBySku`) | Race condition giữa hai request | Unique constraint trong DB, bắt lỗi vi phạm constraint và trả 409 |
| Log password, token, số thẻ, cả request body | Lộ dữ liệu nhạy cảm qua hệ thống log | Log có chọn lọc, che dữ liệu; review cấu hình log |
| Secret trong `application.yml` commit vào git | Ai đọc repo cũng có secret; khó xoay vòng | Biến môi trường, secret manager, K8s Secret; quét secret trong CI |
| Endpoint Actuator mở ra ngoài | Lộ env, heap dump, cấu hình (xem mục 24) | Chỉ expose `health`; port quản trị riêng; bảo vệ bằng security |
| `@Async`/virtual threads/`parallelStream` không giới hạn | Tràn tài nguyên downstream, mất context (security, trace) | Executor có giới hạn, `@ConcurrencyLimit`, propagate context |
| Test dùng `@SpringBootTest` cho mọi thứ, mỗi test một cấu hình khác | Build chậm, context cache bị vỡ (xem mục 28) | Test slice, unit test thuần, chuẩn hóa cấu hình test |

**Quyết định & trade-off — câu hỏi trước một quyết định kiến trúc**

| Lựa chọn | Khi nào dùng | Cái giá phải trả |
|---|---|---|
| Trả lời "vấn đề là gì, đo được không?" trước khi chọn công nghệ | Luôn luôn | Tốn thời gian ban đầu; tránh được giải pháp cho vấn đề không có thật |
| Chọn phương án đảo ngược được (modular monolith, feature flag) | Khi còn nhiều điều chưa biết | Có thể không tối ưu ngay từ đầu |
| Chọn phương án khó đảo ngược (tách microservice, đổi DB, native image) | Khi có số đo và lý do rõ ràng | Chi phí vận hành và chi phí quay lại cao; bắt buộc ghi ADR |

Các câu nên hỏi: Ai vận hành lúc 2 giờ sáng? Lỗi được phát hiện bằng metric/alert nào (xem mục 25)? Rollback thế nào? Team có đủ kỹ năng không? Chi phí thật khi tải tăng 10 lần là gì? Có cách đơn giản hơn không?

Mẫu ADR ngắn (một file markdown trong repo, ví dụ `docs/adr/0007-dung-modular-monolith.md`):

```text
# ADR-0007: <quyết định, viết dạng khẳng định>
Trạng thái: Đề xuất | Chấp nhận | Thay thế bởi ADR-00xx      Ngày: 2026-09-26
Bối cảnh: vấn đề, ràng buộc (tải, team, deadline, quy định), số đo hiện có.
Các phương án đã xét: A, B, C, mỗi phương án 1-2 dòng ưu/nhược.
Quyết định: chọn gì, phạm vi áp dụng.
Hệ quả: tốt, xấu, rủi ro; việc phải làm tiếp; khi nào xem lại.
```

**Pitfalls trong production**
- Review chỉ nhìn style, bỏ qua transaction và timeout → không có checklist chung → đưa bảng trên vào template pull request.
- Quyết định lớn được đưa ra trong cuộc họp, sáu tháng sau không ai nhớ lý do → không có ADR → bắt buộc ADR cho quyết định khó đảo ngược.
- Cả team chờ Tech Lead review mọi pull request → Tech Lead thành nút thắt → đưa quy tắc vào công cụ (ArchUnit, Spring Modulith verify, Checkstyle, quét secret) để máy soát phần cơ học (xem mục 28, 29).

**Câu hỏi phỏng vấn senior Spring (kèm ý chính của câu trả lời tốt)**
1. *Auto-configuration hoạt động thế nào?* Các lớp khai báo trong file `AutoConfiguration.imports`, áp dụng theo điều kiện (`@ConditionalOnClass`, `@ConditionalOnMissingBean`...), bean của người dùng được ưu tiên. Xem lý do bằng `--debug` hoặc endpoint `conditions`.
2. *Vì sao `@Transactional` đôi khi không có tác dụng?* Proxy: self-invocation, method private/final, exception checked không rollback theo mặc định, bean không được Spring quản lý.
3. *Propagation `REQUIRES_NEW` dùng khi nào, rủi ro gì?* Ghi audit/log độc lập với transaction ngoài; tốn thêm một connection, dễ deadlock hoặc cạn pool khi tải cao.
4. *Giải thích và xử lý N+1.* Nhận biết qua log SQL hoặc thống kê Hibernate; sửa bằng fetch join, entity graph, batch fetching, projection DTO; cẩn thận fetch join kết hợp phân trang.
5. *Optimistic và pessimistic locking?* `@Version` cho xung đột hiếm, xử lý lỗi bằng retry hoặc báo người dùng; lock DB khi xung đột nhiều, đánh đổi throughput.
6. *Virtual threads giải quyết gì và không giải quyết gì?* Giảm chi phí thread cho I/O blocking; không tăng số connection DB, không thay được giới hạn tải; cần giới hạn đồng thời ở downstream.
7. *Thiết kế liveness/readiness cho service có DB?* Liveness không kiểm tra dependency bên ngoài; readiness có thể kiểm tra; hiểu hậu quả restart dây chuyền (mục 24).
8. *Làm sao trace một request qua 3 service?* Micrometer Observation/Tracing, propagate header W3C, OTLP, correlation ID trong log, sampling (mục 25).
9. *Retry an toàn khi nào?* Chỉ với thao tác idempotent hoặc có idempotency key; backoff + jitter; giới hạn tổng thời gian; kết hợp circuit breaker (mục 27).
10. *Chiến lược test cho một microservice?* Nhiều unit test, test slice cho web/JPA, integration test với Testcontainers qua `@ServiceConnection`, contract test cho API; giữ context cache (mục 28).
11. *Khi nào tách microservice?* Khi ranh giới domain đã ổn định, cần deploy/scale độc lập và team đủ sức vận hành; trước đó dùng modular monolith (mục 29).
12. *Quy trình nâng Boot 3 lên 4?* Lên 3.5.x mới nhất, dọn deprecation, properties-migrator, OpenRewrite, Jackson 3, test changes, Undertow (mục 30).
13. *Xử lý lỗi API thống nhất thế nào?* `@RestControllerAdvice`, `ProblemDetail` (RFC 9457), mã lỗi ổn định, không lộ stack trace, có handler dự phòng cho lỗi không lường trước.
14. *Giảm thời gian khởi động và bộ nhớ?* Đo trước; lazy init có chọn lọc, AOT cache/CDS, native image khi phù hợp, và hiểu trade-off của từng cách (mục 26).

**Lộ trình tự học (tài liệu chính thức)**
1. Spring Framework Reference, phần Core (IoC, AOP, transaction) và Data Access: nền tảng cho mọi câu hỏi "vì sao".
2. Spring Boot Reference: auto-configuration, externalized configuration, testing, Actuator, packaging và deployment.
3. Release notes và Migration Guide của Spring Boot trên GitHub wiki: đọc cho mỗi phiên bản mới.
4. Hibernate ORM User Guide (fetching, batching, locking) và Spring Data JPA Reference.
5. Micrometer docs (Observation, tracing) và OpenTelemetry docs.
6. Spring Modulith Reference; Testcontainers docs; Spring Security Reference cho phần bảo mật.
7. Thực hành: tự áp dụng từng mục của checklist trên vào demo Product API rồi đo trước và sau.

**Liên hệ với demo**
Demo có sẵn nhiều dấu hiệu trong bảng soát: `ddl-auto=update`, race `existsBySku`, trả `Page` trực tiếp, thiếu handler dự phòng, chưa có Actuator và Security. Đây là bài tập review tốt: cho học viên dùng bảng để tìm và đề xuất cách sửa theo thứ tự ưu tiên.

**Câu hỏi kiểm tra**
- **H:** Trong review, bạn thấy service gọi API thanh toán bên trong method `@Transactional`. Góp ý gì?
  **Đ:** Connection DB bị giữ trong lúc chờ mạng, pool dễ cạn; nếu transaction rollback thì lời gọi thanh toán không được hoàn tác. Tách lời gọi ra ngoài transaction, dùng outbox hoặc event sau commit, idempotency key và timeout.
- **H:** Vì sao ADR cần mục "Hệ quả" và "khi nào xem lại"?
  **Đ:** Để ghi rõ cái giá đã chấp nhận và điều kiện khiến quyết định hết đúng (tải, team, công nghệ). Nhờ vậy người sau biết lúc nào nên xem lại thay vì làm theo mù quáng hoặc đảo ngược mà không hiểu lý do.
- **H:** Ba mục nào trong bảng soát bạn sửa đầu tiên trên demo, vì sao?
  **Đ:** Race `existsBySku` (dữ liệu sai), `ddl-auto=update` (schema không kiểm soát), handler dự phòng (lộ lỗi, trả mã sai). Chúng ảnh hưởng tính đúng đắn và bảo mật; trả `Page` trực tiếp là vấn đề hợp đồng API, xử lý sau.
