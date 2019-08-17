# Spring MVC



1. 프로젝트 만들기
   - File -> New -> Project -> Spring Initializr
   - project 이름 설정
   - Java version 선택
   - Maven/Gradle 설정
   - 의존성 추가(Web==Spring Boot Starter, Lombok, Thymleaf)



## 1부. Spring MVC 동작 원리

1. MVC란?

   - M: Model

     - 평범한 자바 객체 POJO
     - 도메인 객체
     - DTO로 화면에 전달할 객체
     - 화면에서 전달 받은 데이터를 담고 있는 객체

   - V: View

     - HTML, JSP, Thymleaf 등
     - 데이터를 보여주는 역할
     - HTML, JSON, XML 등 다양한 형태로 보여줄 수 있다.

   - C: Controller

     - Spring @MVC

     - 사용자 입력을 받아 모델 객체의 데이터를 변경

     - 사용자 입력을 받아 모델 객체를 뷰에 전달하는 역할

       - 입력값 검증

       - 입력 받은 데이터로 모델 객체 변경

       - 변경된 모델 객체를 뷰에 전달

2. MVC 장점

   - 동시 다발적(Simultaneous) 개발: 백엔드 개발자와 프론테엔드 개발자가 독립적으로 개발을 진행할 수 있다.
   - 높은 결합도: 논리적으로 관련있는 기능을 하나의 컨트롤러로 묶거나, 특정 모델과 관련있는 뷰를 그룹화 할 수 있다.
   - 낮은 의존성: 뷰, 모델, 컨트롤러는 각각 독립적이다.
   - 개발 용이성: 책임이 구분되어 있어 코드 수정하는 것이 편한다.
   - 한 모델에 대한 여려 형태의 뷰를 가질 수 있다.

3. MVC 단점
   - 코드 네비게이션 복잡함
   - 코드 일관성 유지에 노력이 필요함
   - 높은 학습 곡선

4. 서블릿(Servlet)
   - 자바 엔터프라이즈 에디션은 웹 애플리케이션 개발용 스펙과 API 제공
   - 요청 당 Thread 사용( 새로 만들거나, Pool에서 가져옴)
   - 그 중에 가장 중요한 클래스중 하나가 HttpServlet.
   - CGI(Common Gatewat Interface): Servlet 이전에 사용하던 기술
5. 서블릿 장점
   - 빠르다.
   - 플랫폼에 독립적이다.
   - 보안이 제공된다.
   - 이식성이 있다.
6. 서블릿 엔진/컨테이너 종류
   - 톰캣
   - 제티
   - 언더토 
7. 서블릿 엔진/컨테이너 역할
   - 세션 관리
   - 네트워크 서비스
   - MIME 기반 메시지 인코딩 디코팅
   - 서블릿 생명주기 관리
8. 서블릿 생명주기
   - Servlet Container가 Servlet instance init() 메소드를 호출하여 초기화 한다.
     - 최초 요청을 받았을 때, 한번 초기화 하고 나면 그 다음 요청부터는 이 과정이 생략된다.
   - Servlet이 초기화 된 다음부터 클라이언트의 요청을 처리할 수 있다.
     - 각 요청은 별도의 쓰레드로 처리하고 이때, Servlet instance service() 메소드를 호출한다.
     - service() 메소드 안에서, http 요청/응답을 처리한다.
     - service() 메소드는 보통 HTTP Method에 따라 doGet(), doPost() 등으로 처리를 위임한다.
     - 즉, 보통 doGet(), doPost() 메소드에서 요청에 대한 처리를 로직을 구현한다.
   - Servlet Container의 판단에 따라, Servlet instance를 메모리에서 내려야 할 시점에 destroy()를 호출한다.
9. 서블릿 생명주기 요약
   - init
   - doGet
   - doPost
   - destroy

10. 서블릿 리스너와 필터

    - 서블릿 리스터: 웹 애플리케이션에서 발생하는 주요 이벤트를 감지하고, 각 이벤트에 특별한 작업이 필요한 경우에 사용할 수 있다.

      - Servlet Context level event(db 연결/해제시 사용 할 수 있다.)
        - Context LifeCycle event
        - Context Attribute Change event
      - Session level event
        - Session LifeCycle event
        - Session Attribute Change event

    - 서블릿 필터: 클라이언트 요청을 서블릿으로 보내기전에, 서블릿의 응답을 클라이언트로 보내기 전에, 특별한 처리가 필요한 경우에 사용할 수 있다.

      - 체인 형태 구조(A,B 순서대로 적용)

      - doFilter() 메소드를 통해 사용

        ![servlet-filter](./img/servlet-filter.png)



### 7) Spring IoC Container 연동

**Servlet에서 Spring이 제공하는 IoC 컨테이너 활용하는 방법**

1. ContextLoaderListner
   - Servlet Listner 구현체
   - ApplicationContext를 만들어 준다.
   - ApplicationContext를 Servlet Context LifeCycle에 따라 등록하고 소멸시켜준다.
   - Servlet에서 IoC Container를 ServletContext를 통해 꺼내 사용할 수 있다.
2. 사용법
   - pom.xml에 spring-webmvc dependency 추가
   - web.xml 파일에 <listener>태그를 추가한다.
   - <listener>태그 내부에 <listener-class>org.springframework.web.context.ContextLoaderListener를 추가한다.
3. 동작 과정
   - Spring IoC Container(즉, ApplicationContext)를 Servlet Context에 등록해주게 됨.
   - 그러면, Servlet 생명주기에 맞춰서 spring ApplicationContext가 바인딩 되어 동작하게 됨.
   - Servlet이 종료되는 시점에 spring ApplicationContext를 제거해줌.

![dispatcher-servlet](./img/dispatcher-servlet.png)



### 8) Spring MVC 연동

**Spring이 제공하는 Servlet 구현체 DispatcherServlet 사용하기**

1. DispatcherServlet

   - Spring MVC의 핵심
   - Front Controller 역할 수행
     - 모든 요청을 받아 적절하게 다른 Controller에게 dispatch(= delegate, =위임)

   - 등록 방법
     - web.xml 파일에 여러개의 servlet과 servlet-mapping을 등록하여 servlet application을 구축할 수 있다는 것을 기억하자.
     - web.xml 파일에 <servlet>태그를 이용해 Dispatcher Servlet 등록
     - <init-param>태그를 통해 contextClass와 contextConfigLoacation을 등록
     - WebConfig.java 파일에서 @ComponentScan 어노테인션을 사용해 Bean 등록
     - 우리는 Spring MVC를 구현하기 위해서, Spring이 제공하는 DispatcherServlet을 web.xml파일에 등록하고, 적절한 경로를 servlet-mapping을 통해 등록할 수 있다.

   ![dispatcher-servlet-registration](./img/dispatcher-servlet-registration.png)

**주의**

- Spring Boot 프로젝트는 Spring이라는 Java Application 안에, Tomcat이 뜨고, 그 안에 Servlet Container에 DispatcherServlet이 코드로 들어가 있는 형태
  - 즉, Spring이 밖에 나와 있는 형태
- Spring MVC 프로젝트는 tomcat이 먼저 뜨고, 그 안에 Servelt Container에 DispatcherServlet을 등록하는 형태
  - 즉, Spring이 안에 들어가 있는 형태



### 9) DispatcherServlet 동작 원리 1부

1. DispatcherServlet 초기화
   - 아래의 특별한 타입의 Bean들을 찾거나, 기본 전략에 해당하는 Bean들을 등록한다.
   - HandlerMapping: 핸들러를 찾아주는 인터페이스(디자인 패턴: Strategy pattern 사용)
   - HanlderAdapter: 핸들러를 실행하는 인터페이스(디자인 패턴: Strategy pattern 사용)
   - HandlerExceptionResolver
   - ViewResolver
   - ...
2. DispatcherServlet 동작 순서
   - 요청을 분석한다.(Locale, Theme, MultiPart 등)
   - (핸들러 맵핑을 위임하여)요청을 처리할 핸들러를 찾는다.
   - (등록되어 있는 핸들러 어댑터 중에) 해당 핸들러를 실행할 수 있는 "핸들러 어댑터"를 찾는다.
   - 찾아낸 "핸들러 어댑터"를 사용해서 핸들러의 응답을 처리한다.
     - 핸들러의 리턴값을 보고 어떻게 처리할지 판단한다.
     - View 이름에 해당하는 View를 찾아서 Model 데이터를 렌더링한다.
     - @ResponseEntity가 있다면 Convert를 사용해서 응답 본문을 만든다.
   - (부가적으로) 예외가 발생했다면, 예외 처리 핸들러에 요청 처리를 위임한다.
   - 최종적으로 응답을 보낸다.
3. HandlerMapping
   - RequestMappingHandlerMapping
4. HandlerAdapter
   - RequestMappingHandlerAdapter

**추가**

- @RestController = @Controller + @ResponseBody
- @ResponseBody가 없으면 문자열을 ModelAndView로 인식한다. -> model -> view -> 응답
- @ResponseBody가 있으면 문자열을 ModelAndView로 인식하지 않는다! -> 문자열 응답



### 10) DispatcherServlet 동작 원리 2부: SimpleController

- Dispatcher Servlet에는 기본적으로 아래 2개의 핸들러 맵핑이 등록되어 있기 때문에, 우리가 어노테이션으로 요청을 맵핑할 수 있는 것

1. HanlderMapping
   - BeanNameUrlHandlerMapping
   - RequestMappingHandlerMapping
2. HandlerAdapter
   - SimpleControllerHandlerAdapter

```java
//이렇게 우리가 직접 구현할 수도 있다.
@org.springframework.stereotype.Controller("/simple")
public class SimpleController implements Controller{
  @Override
  public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throw Exception{
    return new ModelAndView("/WEB-INF/simple.jsp")
  }
}
```



### 11) DispatcherServlet 동작 원리 3부: 커스텀 ViewResolver

1. ViewResolver
   - InternalResourceViewResolver
2. InternalResourceViewResolver
   - Prefix
   - Suffix

```java
@Configuration
@ComponentScan
public class WebConfig{
  @Bean
  public InternalResourceViewResolver viewResolver(){
    InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
    viewResolver.setPrefix("/WEB-INF");
    viewResolver.setSuffix(".jsp");
    return viewResolver;
  }
}
```

```java
@org.springframework.stereotype.Controller("/simple")
public class SimpleController implements Controller{
  @Override
  public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throw Exception{
    return new ModelAndView("simple") // Prefix, Suffix 덕분에 이렇게 사용가능
  }
}
  
```



### 12) Spring MVC 기본 구성 요소

![spring-mvc-components](./img/spring-mvc-components.png)

1. DispatcherServlet의 기본 전략
   - DispatcherServlet.properties
2. MultipartResolver
   - 파일 업로드 요청 처리에 필요한 인터페이스
   - HttpServletRequest를 MultipartHttpServletReuqest로 변환해주어 요청이 담고 있는 File을 꺼낼 수 있는 API 제공
   - Spring Boot는 MultipartResolver 구현체를 기본 Bean으로 등록해서 제공함.
3. LocalResolver
   - 클라이언트 위치(Locale) 정보를 파악하는 인터페이스
   - 기본 전략은 요청의 accept-language를 보고 판단.
4. ThemeResolver
   - 애플리케이션에 설정된 테마를 파악하고 변경할 수 있는 인터페이스
   - 예륻들어, dark mode 변환 같은 것
5. HandlerMapping(여래개 빈 사용)
   - 요청을 처리할 핸들러를 찾는 인터페이스
   - RequestMappingHanlderMappling: 우리가 어노테이션을 이용해 설정한 핸들러를 기준으로 찾아줌
   - BeanNameUrlHandlerMapping: Bean이름을 기반으로 핸들러를 찾아줌
6. HandlerAdapter(여러개 빈 사용)
   - HandlerMapping이 찾아낸 "핸들러"를 처리하는 인터페이스
   - Spring MVC 확장력의 핵심
7. HandlerExceptionResolver(여러개 빈 사용)
   - 요청 처리 중에 발생한 에러를 처리하는 인터페이스
   - @ExceptionHandler로 정의한 메서드를 찾아서 사용
8. RequestToViewNameTranslator
   - 핸들러에는 View 이름을 명시적으로 리턴하지 않은 경우, 요청을 기반으로 뷰 이름을 판단하는 인터페이스
9. ViewResolver
   - View 이름(String)에 해당하는 View를 찾아내는 인터페이스
10. FlashMapManager
    - FlashMap 인스턴스를 가져오고 저장하는 인터페이스
    - Post요청에서 form 중복 요청을 거를 수 있다.
    - FlashMap은 주로 리다이렉션을 사용할 때 요청 매개변수를 사용하지 않고, Url을 더럽히지 않고, 깔끔하게 데이터를 전달하고 정리할 때 사용한다.
    - redirect:/events



### 13. Spring MVC 동작 원리 정리

1. 결국엔 Spring MVC는 굉장히 복잡한 Servlet = DispatcherServlet 이라고 생각하면 된다.

2. DispatcherServlet 초기화

   - 특정 타입에 해당하는 빈을 찾는다.
   - 없으면 기본 전략을 사용한다.(DispatcherServlet.properties)

3. Spring Boot를 사용하지 않는 Spring MVC

   - Servlet Container(ex. tomcat)에 등록한 웹 어플리케이션(WAR)에 DispatcherServlet을 등록한다.

     - 방법1) web.xml에 서블릿 등록

     - 방법2) WebApplicationInitializer에 자바 코드로 서블릿 등록(Spring 3.1+, Servlet 3.0 +)

       ![web-application-initializer](./img/web-application-initializer.png)

   - 세부 구성 요소는 Bean 설정하기 나름

4. Spring Boot를 사용하는 Spring MVC

   - 자바 어플리케이션에 내장 톰캣을 만들어 그 안에 DispatcherServlet을 등록한다.
     - Spring Boot이 자동으로 설정해줌
   - Spring Boot 주관에 따라 여러 인터페이스 구현체를 빈으로 등록한다.



## 2부. Spring MVC 설정



### 1. @Configuration을 사용한 자바 설정 파일에 직접 @Bean을 사용해서 Spring MVC 구성 요소 등록하기(구식)

```java
//아래와 같이 설정해주면, 기본 Spring MVC 구성 요소 대신, 우리가 정의한 빈으로 사용해줌

@Configuration
@ComponentScan
public class WebConfig{
  @Bean
  public HandlerMapping handlerMapping(){
    RequestMappingHandlerMapping handlerMapping = new RequestMappingHandlerMapping();
    handlerMapping.setInterceptors();
    handlerMapping.setOrder(Ordered.HIGHEST_PRECEDENCE);
    return handlerMapping;
  }
  
  @Bean
  public HandlerAdapter handlerAdapter(){
    //request handler에서 @PathVariable int id, @RequestParam String name 등을 arguments로 받는 것을 처리하기 위해서
    RequestMappingHandlerAdapter handlerAdapter = new RequestMappingHandlerAdapter();
    handlerAdapter.
    return handlerAdapter;
  }
  
  @Bean
  public ViewResolver viewResolver(){
    InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
    viewResolver.setPrefix("/WEB-INF/");
    viewResolver.setSuffix(".jsp");
    return viewResolver;
  }
}
```



### 2. @EnableWebMvc 어노테이션을 사용하여 Spring MVC를 사용할 때 편리한 웹 MVC 기본 설정 방법 사용하기

- @EnableWebMvc: 기존 설정 빈 + 유요한 서정 빈들을 Import해서 제공함
- 단, @EnableWebMvc 어노테이션을 사용하는 경우, servletContext를 셋팅해줘야함(내부적으로 종종 사용하기 때문에)
- @EnableWebMvc는 Delegation하는 구조로 되어 있음.(확장성을 위해)

```java
public class WebApplication implements WebApplicationInitialier{
  @Overrride
  public void onStartup(ServletContext servletContext) throws ServletException{
    AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
    context.setServletContext(servletContext);
    context.register(WebConfig.class);
    context.refresh();
    
    DispatcherServlet dispatcherServlet = new DispatcherServlet(context);
		...
  }
}

@Configuration
@EnableWebMvc
public class WebConfig{
  
}
```

### 3. WebMvcConfigure 인터페이스: @EnableWebMvc가 제공하는 빈을 커스터마이징 할 수 있는 기능 제공

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer{
  @Override
  public void configureViewResolvers(ViewResolverRegistry registry){
    registry.jsp("/WEB-INF/","jsp"); // prefix, suffix를 제공하는 기능
  }
}
```

- WebMvcConfigurer 인터페이스가 제공하는 메소드를 커스터마이징 해서, 위 2번 방법보다 간편하게 설정할 수 있음

### 4. Spring Boot의 Spring MVC 설정

1. Spring Boot의 "주관"이 적용된 자동 설정이 동작한다.
   - JSP 보다 Thymeleaf 선호
   - JSON 지원
   - 정적 리소스 지원(+ Welcome 페이지, favicon 등 지원)
2. Spring MVC 커스터마이징
   - application.properties(spring boot에서 사용)
   - @Configuration + Implements WebMvcConfigurer(= Spring Boot의 Spring MVC 자동설정 + 직접 추가 설정)
   - @Configuration + **@EnableWebMvc** + Implements WebMvcConfigurer(= Spring Boot의 **자동설정을 사용하지 않고 직접 추가 설정**)

![spring-boot-spring-mvc](./img/spring-boot-spring-mvc.png)

3. **Spring Boot는 handlerMapping에서 SimpleUrlHandlerMapping을 지원하고, baseName="resourceHandlerMapping"이기 때문에, resources/static 또는 resources/templates 밑에 view 파일을 두고 처리하는 것이 가능하다.**
4. Spring Boot는 View를 찾아오기 위해, ContentNegotiatingViewResolver라는 것을 사용하여, 아래 ViewResolver중에서 적절한 viewResolver를 선택하여 제공
   - BeanNameViewResolver
   - ThymeleafViewResolver(타임리프)
   - ViewResolverComposite
   - InternalResourceViewResolver(기본)

### 5. Spring Boot에서 JSP 사용하기

1. 제약사항
   - JAR 프로젝트로 만들 수 없음, WAR 프로젝트로 만들어야 함
   - Java-JAR로 실행할 수는 있지만, "실행가능한 JAR 파일"은 지원하지 않음
   - 언더토우(JBoss에서 만든 Servlet Conainer)는 JSP를 지원하지 않음
   - Whitelabel 에러 페이지를 error.jsp로 오버라이딩 할 수 없음.
2. 의존성 추가

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
</dependency>
<dependenct>
	<groupId>org.apache.tomcat.embed</groupId>
  <artifactId>tomcat-embed-jasper</artifactId>
  <scope>provided</scope>
</dependenct>
```

3. 태그 선언

   ```jsp
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
   ```

4. application.properties

   ```
   spring.mvc.view.prefix=/WEB-INF/jsp
   spring.mvc.view.suffix=.jsp=
   ```

5. spring boot의 ./mvnw package 라는 명령어를 사용하면, maven이 설치가 되어 있지 않아도 빌드가능

### 6. WAR 파일 실행/배포하기

1. java -jar를 사용해서 실행하기

   ```java
   @SpringBootApplication
   public static void main(String[] args){
     SpringApplicatoin.run(DemoJspApplication.class, args);
   }
   ```

   

   ![run-java-jar](./img/run-java-jar.png)

2. Servlet Container에 배포하기

```java
public class ServletInitializer extends SpringBootServeltInitializer{
  @Override
  protected SpringApplicationBuilder configure(SpringApplicationBuilder application){
    return application.sources(DemoJspApplication.class);
  }
}
```

![deploy-to-servlet-container](./img/deploy-to-servlet-container.png)



###  7. Formatter 추가하기

```java
@RestController
public class SampleController{
  @GetMapping("/hello/{name}")
  public String hello(@PathVariable("name") Person person){ // argument 부분을 formatting 하기 위해서 아래의 foramtter를 정희해서 추가하는 것임!!
    return "hello"+person.getName();
  }
}
```



1. Formatter
   - Printer: 해당 객체를 (Locale 정보를 참고하여) 문자열로 어떻게 출력할 것인가
   - Parser: 어떤 문자열을 (Locale 정보를 참고하여) 객체로 어떻게 변환할 것인가

```java
public class PersonFormatter implements Formatter<Person>{
  @Override
  public Person parse(String text, Locale locale) throws ParseException{
    Person person = new Person();
    person.setName(text);
    return person;
  }
  
  @Override
  public String print(Person object, Locale locale){
    return object.toString();
  }
}
```

1. 추가 방법1
   - WebMvcConfigurer의 addFormatters(FormatterRegistry) 메소드 정의

```java
@Configuration
public class WebConfig implements WebMvcConfigurer{
  @Override
  public void addFormatters(FormatterRegistry registry){
    registry.addFormatter(new PersonFormatter());
  }
}
```



1. 추가 방법2
   - Spring Boot 사용시에만 가능
   - 해당 Formatter를 Bean으로 등록하여 사용
   - Spring Boot 에서는 아래와 같이 빈으로 등록해주면, WebConfig 파일에서 따로 formatter를 추가할 필요가 없다.

```java
@Component
PersonFrmatter
```



### 8. 도메인 클래스 Convertter 자동 등록

1. Spring Data JPA는 Spring MVC용 도메인 클래스 Converter를 제공합니다.

2. 도메인 클래스 Converter

   - Spring Data JPA가 제공하는 Repository를 사용해서 ID에 해당하는 Entity를 읽어옵니다.

3. 의존성 설정

   ```xml
   <dependency>
   	<groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-jpa</artifactId>
   </dependency>
   
   <dependency>
   	<groupId>com.h2.database</groupId>
     <artifactId>h2</artifactId>
   </dependency>
   ```

4. Entity Mapping

   - @Generatedvalue: 데이터베이스에 들어갈때 자동으로 id값을 생성하고 싶은 경우 사용.

   ```java
   @Entity
   public class Person{
     @Id @Generatedvalue
     private Integer id;
     ...
   }
   ```

5. Repository 추가

   ```java
   public interface PersonRepository extends JpaRepository<Person, Integer>{}
   ```

6. 테스트 코드 수정

   - 테스트용 이벤트 객체 생성
   - 이벤트 Repository에 저장
   - 저장한 이벤트의 ID로 조회 시도

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest
   @AutoConfigureMockMvc
   public class SampleControllerTest{
     @Autowired
     MockMvc mockMvc;
     @Autowired
     PersonRepository personRepository;
     @Test
     public void hello() throws Excpetion{
       Person person = new Person();
       Person.setName("keesun");
       Person savedPerson = personRepository.save(person);
       
       this.mockMvc.perform(get("/hello"))
         .param("id", savedPerson.getId().toString())
         .addDo(print()) // log 찍어주는 기능
         .addExpect(content().string("hello keesun")) // 결과값 확인하는 기능
     }
   }
   ```

   

### 9. 핸들러 인터셉터 1부: 개념

1. HandlerInterceptor

   - HandlerMapping에 설정하는 수 있는 인터셉터
   - Handler를 실행하기 전, 후(아직 rendering 전) 그리고 완료(rendering 끝난 이후)시점에 부가 작업을 하고 싶은 경우에 사용할 수 있다.
   - 여러 Handler에서 반복적으로 사용하는 코드를 줄이고 싶을 때 사용할 수 있다.
     - Logging, Auth, Locale 변경 등

2. boolean preHandle(request, response, handler)

   - Handler 실행하기 전에 호출 됨
   - Handler에 대한 정보를 사용할 수 있기 때문에, Servlet Filter에 비해 보다 세밀한 로직 구현 가능
   - return 값으로 계속 다음 인터셉터 또는 Handler로 요청, 응답을 전달할지(true), 응답 처리가 이곳에서 끝났는지(false)를 알린다.

3. void postHandle(request, response, modelAndView)

   - Handler 실행이 끝나고, 아직 View Rendering 하기 이전에 호출됨
   - View에 전달할 추가적이거나 여러 핸들러에 공통적인 Model 정보를 담는데 사용할 수 있다.
   - 이 메소드는 인터셉터 역순으로 호출된다.
   - 비동기적인 요청 처리 시에는 호출되지 않는다.

4. void afterCompletion(request, response, handler, ex)

   - 요청 처리가 완전히 끝난 뒤(View Rendering이 완료된 뒤)에 호출됨
   - preHandler에서 true를 리턴한 경우에만 호출됨
   - 이 메소드는 인터셉터 역순으로 호출됨
   - 비동기적인 요청 처리 시에는 호출되지 않는다.

5. Servlet Filter(doFilter())와 Handler Interceptor 비교

   - Servlet Filter 보다 구체적인 처리가 가능
   - Servelt 보다 일반적인 용도의 기능을 구현하는데 사용하기에 좋다.

   - 참고자료: 스프링 개발자 Mark Fisher의 Servlet Filter와의 차이점에 대한 찾아보면 좋다.
   - Cross-Site Scripting(XSS) attacks: 웹 브라우저 Form을 사용해 유저 정보를 빼내가는 일종의 해킹 방법
   - 이런 XSS를 처리하기 위해서는 Servlet Filter에서 처리해야함(naver에서 제공하는 Lucy)

```java
@RestController
public class SampleController{
  //preHandle1
  //prehandle2
  //요청처리
  //postHandle 2
  //postHandle 1
  //뷰 렌더링
  //afterCompletion2
  //afterCompletion1
  
  @GetMapping("/hello")
  public String hello(@RequestParam("id") Person person){
    return "hello"+person.getName();
  }
}
```



### 10. 핸들러 인터셉터 2부: 만들고 등록하기

1. Handler Interceptor 구현하기

   ```java
   public class GreetingInterceptor implements HandlerInterceptor{
     @Override
     public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception{
       System.out.println("preHandle1");
       return true;
     }
     
     @Override
     public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception{
       System.out.println("postHandle2");
     }
     
     @Override
     public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception{
       System.out.println("afterCompletion1");
     }
   }
   ```

2. Handler Interceptor 등록하기

   - 특정 패턴에 해당하는 요청에만 적용할 수도 있다.
   - 순서를 지정할 수 있다.

   ```java
   @Configuration
   public class WebConfig implements WebMvcConfigurer{
     @Override
     public void addInterceptors(InterceptorRegistry registry){
       registry.addInterceptor(new GreetingInterceptor()).order(0);
       registry.addInterceptor(new AnotherInterceptor())
         .addPathPatterns("/keesun")
         .order(-1);
     }
   }
   ```

   

### 11. Resource 핸들러

1. Image, JavaScript, CSS, HTML 파일과 같은 정적인 리소스를 처리하는 Handler 등록 방법

2. Default Servlet을 이용한 방법

   - Servlet Container가 기본으로 제공하는 Servlet으로 정적인 리소스를 처리할 때 사용한다.
   - https://tomcat.apache.org/tomcat-9.0-doc/default-servlet.html

3. Spring MVC Resource HandlerMapping 등록

   - 가장 낮은 우선 순위로 등록
     - 다른 HandlerMapping이 "/" 이하 요청을 처리하도록 허용하고, 최종적으로 Resource Handler가 처리하도록
     - DefaultServletHanlderConfigurer

4. Resource Handler 설정

   - 어떤 요청 패턴을 지원할 것인가
   - 어디서 리소스를 찾을 것인가
   - 캐싱(application.properties에서 사용여부 설정할 수 있음)
   - 개발할때는 캐시를 적용하지 않는것이 더 좋음
   - ResourceResolver: 요청에 해당하는 리소스를 찾는 전략(캐싱, gzie인코딩, brotli인코딩, WebJar ...)
   - ResourceTransformerr: 응답으로 보낼 리소스를 수정하는 전략(캐싱, CSS 링크, HTML5 AppCache ...)

   ```java
   @Configuration
   public class WebConfig implements WebMvcConfigurer{
     @Override
     public void addResourceHandlers(ResourceHandlerRegistry registry){
   		registry.addResourceHandler("/mobile/**") // 어떤 요청 패턴을 지원할 것인가
         .addResourceLocations("classpath:/mobile/") //어디서 리소스를 찾을 것 인가
         .setCachControl(CacheControl.maxAge(10, TimeUnit.MINUES)); //10분동안 리소스가 바뀌지 않는다면, 캐시를 유지함
       
           //.resouceChain(true): 캐시사용여부 설정
       		//.addResolver: 리소스 찾는 전략
       		//.addTransformer: 리소스 수정하는 전략
       		//spring boot는 위의 것을 모두 기본으로 제공함
     }
   }
   
   //테스태해보기
   
   @Test
   public void helloStatic() throws Exception{
     this.mockMvc.perform(get("/mobile/index.html"))
       .andDo(print())
       .andExpect(status().isOk())
       .andExpect(content().string(Matchers.containsString("Hello Mobile")))
       .adnExpect(header().exists(HttpHeaders.CACHE_CONTROL))
   }
   ```

   

5. Spring Boot

   - 기본 정적 리소스 핸들러와 캐싱 제공
   - resources/static, resources/templates, resources/public 등의 폴더 제공

   

6. [참고자료](https://www.slideshare.net/rstoya05/resource-handling-spring-framework-41)



### 12. HTTP Message Converter 1부: 개요

1. HTTP Message Converter

   - 요청 본문에서 메시지를 읽어들이거나(@RequestBody), 응답 본문에 메시지를 작성할 때(@ResponseBody)를 사용한다.

   ```java
   @GetMapping("/message")
   @ResponseBody // 이 메소드(핸들러)의 결과를 응답 본문에 넣어줌
   public String message(@RequestBody Person person){ //요청 본문값을 파라미터에 주입해줌
     return "hello person";
   }
   
   //여기서 요청 값과, 응답 값을 Message라고 하며, 이를 적절하게 변환시켜주는 역할을 하는게 Converter!
   ```

   

2. 기본 HTTP Message Converter

   - Byte Array Converter
   - String Converter
   - Resource Converter
   - Form Converter( Form data to/from MultiValueMap<String,String>)
   - (JAXB2 Converter)
   - (Jackson2 Converter)
   - (Jackson Converter)
   - (Gson Converter)
   - (Atom Converter)
   - (RSS Converter)

3. 설정 방법

   - 기본으로 등록해주는 Converter에 새로운 Converter 추가하기

     - extendMessageConverters

     ```java
     @Override
     public void extendMessageConverters(List<HtpMessageConverter<?>> converters){
       ...
     }
     ```

   - 기본으로 등록해주는 Converter는 다 무시하고 새롭게 Converter 설정하기

     - configureMessageConverters

     ```java
     public void configureMessageConverters(List<HttpMessageConverter<?>> converters){
       ...
     }
     ```

   - **의존성 추가로 Converter 등록하기(추천)**

     - Maven or Gradle 설정에 의존성을 추가하여, 그에 따른 Converter가 자동으로 등록 된다.
     - WebMvcConfigurationSupport에 기본 메시지 컨버터가 등록되있음(스프링 프레임워크의 기능임, 부트의 기능 아님)

   - [참고자료](https://www.baeldung.com/spring-httpmessageconverter-rest)

### 13. HTTP Message Converter 2부: JSON

1. Spring Boot를 사용하지 않는 경우
   - 사용하고 싶은 JSON 라이브러리를 의존성으로 추가
   - GSON
   - JacksonJSON
   - JacksonJSON2
2. Spring Boot를 사용하는 경우
   - 기본적으로 JacksonJSON2가 의존성에 들어가 있다.
   - 즉, JSON용 HTTP Message Converter가 기본으로 등록되어 있다.
3. 참고
   - JSON path 문법
   - https://github.com/json-path/JsonPath
   - http://jsonpath.com/

```java
@GetMapping("/jsonMessage")
public Person jsonMessage(@RequestBody Person person){
  return person;
}

//아래 부분은 Test Class에 작성되는 부분 입니다.
@Autowired
ObjectMapper objectMapper; //Jackson이 제공하는 기능

@Test
public void jsonMessage() throws Exception{
  Person person = new Person();
  person.setId(2019);
  person.setName("keesun");
  String jsonString = objectMapper.writeValueAsString(person);
  
  this.mockMvc.perform(get("jsonMessage"))
    .contentType(MediaType.APPLICATION_JSON_UTF8) // 요청 본문 타입 JSON
    .accept(MediaType.APPLICATION_JSON_UTF8) // 응답 본문 타입 JSON
    .content(jsonString)
    .andDo(pring())
    .andExpect(status().isOk())
    .andExpect(jsonPath("$.name").value("keesun"))
    .andExpect(jsonPath("$.id").value(2019))
}
```



### 14. HTTP Message Converter 3부: XML

1. OXM(Object-XML Mapper) 라이브러리 중에 Spring이 지원하는 의존성 추가

   - JacksonXML
   - JAXB

2. Spring Boot를 사용하는 경우

   - 기본적으로 XML 의존성을 추가해주지 않는다.
   - JAXB 의존성 추가

   ```xml
   <!-- jaxb 인터페이스 -->
   <dependency>
      <groupId>javax.xml.bind</groupId>
      <artifactId>jaxb-api</artifactId>
   </dependency>
   <!-- jaxb 구현체 -->
   <dependency>
      <groupId>org.glassfish.jaxb</groupId>
      <artifactId>jaxb-runtime</artifactId>
   </dependency>
   <!-- marshalling을 추상화 해놓은 API를 제공 -->
   <!-- marshalling이란? XML to Object, Object to XML로 변환하는 것을 의미 -->
   <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-oxm</artifactId>
      <version>${spring-framework.version}</version>
   </dependency>
   ```

3. Marshaller 등록

   ```java
   @Bean
   public Jaxb2Marshaller marshaller(){
     Jaxb2Marshaller jaxb2Marshaller = new Jaxb2Marshaller();
     jaxb2Marshaller.setPackagesToScan(Event.class.getPackageName());
     return jaxb2Marshaller;
   }
   ```

4. 도메인 클래스(Person과 같은 Entitiy)에 @XmlRooElement 어노테이션 추가

   ```java
   @Autowired
   Marshaller marshaller
   
   @Test
   public void xmlMessage() throws Exception{
   	Person person = new Person();
   	person.setId(2019);
   	person.setName("keesun");
   	
   	StringWriter stringWriter = new StringWriter();
   	Result result = new StringResult(stringWriter); //jaxb result type
   	marshaller.marshal(person, result);
   	String xmlString = stringWriter.toString();
   	
   	 this.mockMvc.perform(get("xmlMessage"))
       .contentType(MediaType.APPLICATION_XML) // 요청 본문 타입 XML
       .accept(MediaType.APPLICATION_JSON_XML) // 응답 본문 타입 XML
       .content(xmlString)
       .andDo(pring())
       .andExpect(status().isOk())
       .andExpect(xpath("person/name").string("keesun"))
       .andExpect(xpath("person/id").string("2019"))
   }
   ```

   

5. Xpath 문법

- https://www.w3schools.com/xml/xpath_syntax.asp
- https://www.freeformatter.com/xpath-tester.html



### 15. 그 밖에 WebMvcConfigurer 설정

1. CORS 설정
   - Cross Origin 요청 처리 설정
   - 같은 도메인에서 온 요청이 아니더라도 처리를 허용하고 싶다면 설정할 것
2. Return Value Handler 설정
   - Spring MVC가 제공하는 기본 리턴값 핸들러 이외에 리턴 핸들러를 추가하고 싶을 때 설정한다.
3. Argument Resolver 설정
   - Spring MVC가 제공하는 기본 아규먼트 리졸버 이외에 커스텀한 아규먼트 리졸버를 추가하고 싶을 때 설정한다.
4. View Controller
   - 단순한게 요청 URL을 특정 View로 연결하고 싶을 때 사용할 수 있다.
5. Async 설정
   - 비동기 요청 처리에 사용할 타임아웃이나 TaskExecutor를 설정할 수 있다.
6. View Resolver 설정
   - Handler에서 Return하는 View 이름에 해당하는 문자열을 View instance로 바꿔줄 View Resolver를 설정한다.
7. Content Negotiation 설정
   - 요청 본문 또는 응답 본문을 어떤 타입(MIME)으로 보내야 하는지 결정하는 전략을 설정한다.



### 16. Spring MVC 설정 마무리

1. Spring MVC 설정은 즉, DispatcherServlet이 사용할 여러 빈을 설정하는 것이다.
   - HandlerMapper
   - HandlerAdapter
   - ViewResolver
   - ExceptionResolver
   - LocaleResolver
2. 일일일 등록하려니 너무 많고, 해당 빈들이 참조하는 또 다른 객체들까지 설정하려면, 설정할게 너무 많아 진다.
3. @EnableWebMvc를 사용하자
   - 어노테이션 기반의 Spring MVC 설정 간편화
   - WebMvcConfigurer가 제공하는 메소드를 구현하여 커스터마이징 가능
4. Spring Boot
   - Spring Boot 자동 설정을 통해 다양한 Spring MVC 기능을 아무런 설정 파일을 만들지 않아도 제공한다.
   - WebMvcConfigurer가 제공하는 메소드를 구현하여 커스터카이징 할 수 있다.
   - @EnableWebMvc를 사용하면 Spring Boot 자동 설정을 사용하지 못한다.
5. Spring MVC 설정 방법
   - Spring Boot를 사용하는 경우는 application.properties 부터 시작
   - WebMvcConfigurer로 시작
   - @Bean으로 MVC 구성 요소 직접 등록