# 1.1~1.3

- Template Method Pattern
  - 추상 메소드나 오버라이딩 가능한 protected 메소드 등으로 만든 뒤, 서브클래스에서 이런 메서드를 필요에 맞게 구현해서 사용하는 방법
  - 상속을 통해 슈퍼클래스의 기능을 확장할 때 사용하는 가장 대표적인 방법
  - hook 메서드 : 슈퍼클래스에서 디폴트 기능을 정의해두거나 비워뒀다가 서브클래스에서 선택적으로 오버라이드할 수 있도록 만들어둔 메서드
- Factory Method Pattern
  - 오브젝트를 어떻게 생성할 것인지 결정하는 방법 (생성 패턴?)
  - 서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게 하는 것
  - 상속을 통해 기능을 확장
  - factory 메서드 : 서브클래스에서 오브젝트 생성 방법과 클래스를 결정할 수 있도록 미리 정의해둔 메서드
  - 오브젝트 생성 방법을 슈퍼클래스의 기본 코드에서 독립시키는 방법

> UserDao에 팩토리 메서드 패턴을 적용해서 getConnection()을 분리

- 디자인 패턴
  - 클래스 상속
  - 오브젝트 합성

> 상속을 통해 간단하고 효과적으로 관심사를 분리할 수 있지만, 여러 단점이 존재함.
> 필용상속관계는 두가지 다른 관심사에 대해 긴밀한 결합을 허용함. 그래서 슈퍼클래스 변경이 발생하면 서브클래스도 함께 수정하거나 개발해야할 수 있음.
> ⇒ 클래스의 분리!

### 클래스의 분리

관심사가 다르고 변화의 성격이 다른 기능을 분리하자

1. 메서드 분리
2. 상속을 통한 상하위 클래스 분리
3. 독립적인 클래스로 분리! ← 여전히 문제 존재함. 커넥션을 가져오는 구체적인 방법에 종속적임.
4. 인터페이스 도입 (추상화)
   - 두 개의 클래스 간 긴밀하게 연결되어 있지 않도록 추상화.
   - 여전히 구체 클래스를 알아야 함 → UserDao 변경 없이는 DB 커넥션 기능의 확장이 자유롭지 못함.
     → 관심사 분리가 완전히 되지 않았기 때문임. (UserDao와 UserDao가 사용할 ConnectionMaker의 특정 구현 클래스 사이의 관계를 설정해주는 것에 대한 관심이 분리되지 않은 상태)
     ⇒ 오브젝트와 오브젝트 사이의 관계가 만들어지기 위해서, 직접 생성자를 호출할 수 있지만, 외부(메서드 또는 생성자 파라미터)에서 만들어준 것을 가져오는 방법도 있음.
   - 클래스 사이 관계가 아닌, 오브젝트 사이에 dynamic한 관계 만들기
     - 런타임 오브젝트 관계를 갖는 구조로 만들어주는 것은 **클라이언트의 책임**
   - code
     ```java
     // UserDao의 생성자에서 파라미터로 오브젝트 관계 설정
     public UserDao(ConnectionMaker connectionMaker) {
     	this.connectionMaker = connectionMaker;
     }

     // UserDaoTest <- 클라이언트 역할
     public class UserDaoTest {
     	public static void main(String[] args) throws ClassNotFoundException, SQLException {
     		ConnectionMaker connectionMaker = new DConnectionMaker();

     		// 두 오브젝트 사이의 의존관계 설정
     		UserDao dao = new UserDao(connectionMaker);
     	}
     }
     ```

### 1.3.4 원칙과 패턴

- 개방 패쇄 원칙 (OCP, Open-Closed Principle)
  - 클래스나 모듈은 확장에는 열려 있어야 하고 변경에는 닫혀 있어야 한다
- 높은 응집도와 낮은 결합도 (high coherence and low coupling)
  - 응집도가 높다 == 하나의 모듈, 클래스가 하나의 책임 또는 관심사에만 집중되어 있다
  - 낮은 결합도 == 하나의 변경이 발생할 때, 변경에 대한 요구가 전파되지 않는 상태
- 전략 패턴 (Strategy Pattern)
  - 자신의 기능 맥락(context)에서, 필요에 따라 변경이 필요한 알고리즘을 인터페이슬르 통해 통쨰로 외부로 분리시키고, 이를 구현한 구체적인 알고리즘 클래스를 필요에 따라 바꿔서 사용할 수 있게 하는 디자인 패턴

# 1.4 제어의 역전 (IoC)

### 1.4.1 오브젝트 팩토리

- 팩토리 (Factory)
  - 객체의 생성 방법을 결정하고 그렇게 만들어진 오브젝틀르 돌려주는 오브젝트
  - 오브젝트를 생성하는 쪽과 생성된 오브젝트를 사용하는 쪽의 역할과 책임을 깔끔하게 분리하려는 목적
    **⇒ 애플리케이션의 ‘컴포넌트’ 역할을 하는 오브젝트와, 애플리케이션의 ‘구조’를 결정하는 오브젝트를 분리**

### 1.4.2 오브젝트 팩토리의 활용

### 1.4.3 제어권의 이전을 통한 제어관계 역전

- 제어 흐름 구조가 뒤바뀌는 것
- 오브젝트가 자신이 사용할 오브젝트를 스스로 선택하지 않는다.
- 모든 제어 권한을 자신이 아닌 다른 대상에게 위임
- ex
  - Servlet : 서블릿 안에 main() 메서드가 있어서 직접 실행시키는 것이 아니라, 서블릿의 제어 권한을 가진 컨테이너가 적절한 시점에 서블릿 클래스의 오브젝트를 만들고 메서드 호출함.
  - 프레임워크 : 라이브러리와 달리, 애플리케이션 코드가 프레임워크에 의해 사용됨.
  - 예제 코드에서 UserDao, DaoFactory : ConnectionMaker의 구현 클래스를 결정하고 오브젝틀르 생성하는 제어권이 DaoFactory에 넘겨짐.
- 제어의 역전에서는 애플리케이션 컴포넌트의 생성과 관계설정, 사용, 생명주기 관리 등을 관장하는 존재가 필요함

# 1.5 스프링의 IoC

### 1.5.1 오브젝트 팩토리를 이용한 스프링 IoC

- 애플리케이션 컨텍스트와 설정 정보(configuration metadata)
  - Spring Bean : 스프링 컨테이너가 생성과 관계설정, 사용 등을 제어해주는 제어의 역전이 적용된 오브젝트
  - Bean Factory : 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트
    → Bean Factory를 확장한 Application Context
- DaoFactory를 사용하는 application context
  - `@Configuration` : bean factory를 위한 오브젝트 설정을 담당하는 클래스
    → DaoFactory
  - `@Bean` : 오브젝트를 만들어주는 메서드
    → UserDao(), connectionMaker()
  - code
    ```java
    @Configuration
    public class DaoFactory {
    	@Bean
    	public UserDao userDao() {
    		return new UserDao(connectionMaker());
    	}

    	@Bean
    	public ConnectionMaker connectionMaker() {
    		return new DConnectionMaker();
    	}
    }
    ```

### 1.5.2 애플리케이션 컨텍스트의 동작방식

- Application Context == IoC 컨테이너 == Spring Container == Bean Factory
- ApplicationContext — implements —> BeanFactory
- 직접 오브젝트 생성하고 관계를 맺지 않음. 생성정보와 연관관계 정보는 별도의 설정정보(configuration metadata)를 통해 얻음
  → configuration metatdata == `@Configuration` 이 붙은 클래스
- 설정정보에 `@Bean`이 붙은 메서드의 이름을 가져와 빈 목록을 만듦
- 장점
  - 클라이언트는 구체적인 팩토리 클래스를 알 필요가 없음
  - application context 종합 IoC 서비스를 제공
  - application context는 빈을 검색하는 다양한 방법 제공

### 1.5.3 스프링 IoC의 용어 정리

- Bean
  - 스프링이 IoC 방식으로 관리하는 오브젝트 (managed object)
- Bean Factory
  - 스프링의 IoC를 담당하는 핵심 컨테이너
  - 빈 등록, 생성, 조회 등 빈을 관리하는 기능 담당
  - BeanFactory을 확장한 ApplicationContext (상속)
  - Container (IoC 컨테이너), Spring Container라고도 함 ← IoC 방식으로 빈을 관리한다는 의미
- configuration metadata
  - Bean Factory가 IoC를 적용하기 위해 사용하는 메타정보
- 스프링 프레임워크
  - IoC 컨테이너, Application Context를 포함해서 스프링이 제공하는 모든 기능을 제공

# 1.6 싱글톤 레지스트리와 오브젝트 스코프

- 오브젝트의 동일성과 동등성
  - 동일한(identical) 오브젝트 ≠ 동일한 정보를 담고 있는(equivalent) 오브젝트
  - 동일성(identity) : `==` 연산자 이용
  - 동등성(equality) : `equals()` 메서드 이용

### 1.6.1 싱글톤 레지스트리로서의 애플리케이션 컨텍스트

- Application Context (IoC 컨테이너)는 싱글톤을 저장하고 관리하는 **Singleton Registry**
- 서버 애플리케이션과 싱글톤
  - 왜 싱글톤으로 빈을 만드는 것일까?
    → 스프링이 주로 적용되는 대상이 자바 엔터프라이즈 기술을 사용하는 서버환경이기 때문
    매번 클라이언트에서 요청이 올 때마다 각 로직을 담당하는 오브젝트를 새로 만들어서 사용하면, 아무리 자바의 오브젝트 생성과 GC 성능이 좋아도 부하가 걸리면 서버가 감당하기 어려움
    ⇒ 서블릿은 대부분 멀티스레드 환경에서 싱글톤으로 동작 (사용자의 요청을 담당하는 여러 스레드에서 하나의 오브젝트를 공유해 동시에 사용)
- Singleton Pattern 한계
  - private 생성자를 갖고 있기 때문에 상속 불가능
    - 객체지향의 장점인 상속과 이를 이용한 다형성 적용이 불가능함
  - 테스트의 어려움
    - 싱글톤은 만들어지는 방식이 제한적이기 때문에 mock 오브젝트 등으로 대체하기 어려움
  - 서버환경에서는 싱글톤이 하나만 만들어지는 것을 보장하지 못함
    - 클래스 로더를 어떻게 구성하고 있느냐에 따라 싱글톤 클래스임에도 하나 이상의 오브젝트가 만들어질 수 있음.
    - 자바 언어를 이용한 싱글톤 패턴 기법은 서버환경에서는 싱글톤이 보장된다고 볼 수 없음
    - 여러 개의 JVM에 분산돼서 설치 되는 경우에도 각각 오브젝트가 생기기 때문에 싱글톤으로서의 가치가 떨어짐
      → 딱히 동의되진 않음..
  - 싱글톤의 사용은 전역 상태를 만들 수 있기 때문에 바람직하지 못함
    - 싱글톤의 static method를 이용해 언제든지 쉽게 접근 가능 → 자연스럽게 global state로 사용되기 쉬움 → 객체지향 프로그래밍과 맞지 않음! → 차라리 스태틱 필드와 메서드로만 구성된 클래스 사용하는 편이 낫다
- 스프링의 싱글톤 레지스트리
  - 싱글톤 패턴의 단점을 극복하기 위해 스프링은 직접 싱글톤 형태의 오브젝트를 만들고 관리하는 기능을 제공함 ⇒ singleton registry! (spring container)
  - 싱글톤 방식으로 사용될 애플리케이션 클래스라도 public 생성자를 가질 수 있음.
    ⇒ 테스트 환경에서 자유롭게 오브젝트를 만들 수 있고, mock 오브젝트 대체하는 것도 간단함

### 1.6.2 싱글톤과 오브젝트의 상태

싱글톤은 멀티스레드 환경이라면 여러 스레드가 동시에 접근해서 사용 가능함 ⇒ 상태 관리에 주의!

파라미터, 로컬 변수, 리턴 값 등을 이용 ⇒ 매번 새로운 값을 저장할 독립적인 공간이 만들어지기 때문에 싱글톤이라고 해도 여러 스레드가 변수의 값을 덮으쓸 일 없음

읽기 전용일 경우는 인스턴스 변수를 사용해도 상관 없음

### 1.6.3 스프링 빈의 스코프

- Bean Scope : 빈이 생성되고 존재하고 적용되는 범위
- spring bean의 default scope == Singleton
- scope 종류 : prototype, request, session, singleton

# 1.7 의존관계 주입 (DI)

### 1.7.1 제어의 역전(IoC)과 의존관계 주입

- Spring IoC Container == Spring DI Container
- DI (Dependency Injection) : 오브젝트 레퍼런스를 외부로부터 제공(주입)받고 이를 통해 여타 오브젝트와 다이나믹하게 의존관계가 만들어짐

### 1.7.2 런타임 의존관계 설정

- 의존 오브젝트(dependent object) == 실제 사용대상인 오브젝트
- 의존관계 주입
  - 클래스 모델이나 코드에는 런타임 시점의 의존관계가 드러나지 않는다. 그러기 위해서는 인터페이스에만 의존하고 있어야 한다.
  - 런타임 시점의 의존관계는 컨테이너나 팩토리 같은 제3의 존재가 결정한다.
  - 의존관계는 사용할 오브젝트에 대한 레퍼런스를 외부에서 제공(주입)해줌으로써 만들어진다.

### 1.7.3 의존관계 검색과 주입

- DI (dependency injection)
  - UserDao와 ConnectionMaker 사이에 DI가 적용되려면 UserDao도 반드시 컨테이너가 만드는 bean 오브젝트여야 함
  - “다이나믹하게 구현 클래스를 결정해서 제공받을 수 있도록 인터페이스 타입의 파라미터를 통해 이뤄져야 한다”
- DL (dependency lookup)
  - 자신이 필요로 하는 의존 오브젝트를 능동적으로 찾음
  - ApplicatoinContext는 getBean() 메서드로 의존관계 검색
  - 검색하는 오브젝트는 자신이 스프링의 빈일 필요가 없음
  - 언제 사용?
    → 다시 확인

### 1.7.4 의존관계 주입의 응용

코드에는 런타임 클래스에 대한 의존관계가 나타나지 않고, 인터페이스를 통해 결합도가 낮은 코드를 만드므로, 다른 책임을 가진 사용 의존관계

### 1.7.5 메서드를 이용한 의존관계 주입

- 수정자(setter) 메서드를 이용한 주입
- 일반 메서드를 이용한 주입

# 1.8 XML을 이용한 설정

### 1.8.1 XML 설정

- Bean의 DI 정보 3가지
  - Bean의 이름
  - Bean의 클래스
  - Bean의 의존 오브젝트

```java
userDao.setConnectionMaker(connectionMaker());
```

```xml
<beans>
	<bean id="connectionMaker" class="springbook.user.dao.DConnectionMaker" />
	<bean id="userDao" class="springbook.dao.UserDao">
		<property name="connectionMaker" ref="connectionMaker" />
	</bean>
</beans>
```

- `name` : DI에 사용할 수정자 메소드의 프로퍼티 이름
- `ref` : 주입할 오브젝트를 정의한 빈의 ID

### 1.8.2 XML을 이용하는 애플리케이션 컨텍스트

### 1.8.3 DataSource 인터페이스로 변환

### 1.8.4 프로프티 값의 주입

- 값 주입
  - 의존 관계 설정이 아니라 dataSource에서 사용할 DB 연결정보와 가은 정보들은 어떻게?
    → 수정자 메서드에 넣어서 값을 주입한다
    ⇒ <property>, value 사용해서 정보 지정
- value 값의 자동 변환
  - 스프링이 property 값을, 수정자 메서드의 파라미터 타입을 참고해서 적절한 형태로 변환해줌

# 1.9 정리

- 책임 분리, 관심사 분리
- 전략패턴
- 개방 패쇄 원칙
- 낮은 결합도, 높은 응집도
- 제어의 역전 IoC
- 싱글톤 레지스트리
- DI 컨테이너, 의존관계 주입 DI
- 생성자 주입, 수정자 주입
- XML 설정
