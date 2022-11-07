# 8장 경계

- 외부 코드 사용하기
    - Map은 객체 유형을 제한하지 않는다
    
        ⇒사용자는 어떤 객체 유형도 추가할 수 있다
    
        ⇒인터페이스가 변할 경우 수정할 코드가 상당히 많아진다
    
    ```java
    public class Sensors{
    	private Map sensors = new HashMap();
    
    	public Sensor getById(String id){
    		return (Sensor) sensors.get(id);
    	}
    
    }
    ```
    
    ⇒Map 인터페이스가 변하더라도 나머지 프로그램에 영향을 미치지 않는다
    
    - Sensors 클래스 안에서 객체 유형을 관리하고 변환하기 때문
    - Sensors 클래스는 (나머지 프로그램이) 설계 규칙과 비즈니스 규칙을 따르도록 강제할 수 있다.
  
  &nbsp;
  
    
    ∴ Map을 여기저기 사용하지 말자
    
&nbsp;

- 경계 살피고 익히기
    - 외부코드를 사용할 경우 먼저 테스트 케이스를 작성해 외부 코드를 익히기(=학습 테스트)
    - 학습 테스트는 프로그램에서 사용하려는 방식대로 외부 API를 호출

&nbsp;

- log4j 익히기
    
    ```java
    public class LogTest{
    		private Logger logger;
    
    		@Before
    		public void initialize(){
    			logger = Logger.getLogger("logger");
    			logger.removeAllAppenders();
    			Logger.getRootLogger().removeAllAppenders();
    		}
    
    		@Test
    		public void basicLogger(){
    			BasicConfigurator.configure();
    			logger.info("basicLogger");
    		}
    
    		@Test
    		public void addAppenderWithStream(){
    			logger.addAppender(new ConsoleAppender(
    				new PatternLayout("%p %t %m%n"),
    				ConsoleAppender.SYSTEM_OUT);
    				logger.info("addAppenderWithStream");
    		}
    
    		@Test
    		public void addAppenderWithoutStream(){
    			logger.addAppender(new ConsoleAppender(
    				new PatternLayout("%p %t %m%n")));
    				logger.info("addAppenderWithoutStream");
    		}
    }
    ```
&nbsp;



- 학습 테스트는 공짜 이상이다
    - 학습 테스트는 이해도를 높여주는 정확한 실험이다

&nbsp;

- 아직 존재하지 않는 코드를 사용하기
    - 아는 코드와 모르는 코드를 분리하는 경계
    - 구현이 당장 안될경우 미루고 자체적으로 인터페이스를 정의
        - 인터페이스를 전적으로 통제한다는 장점
        - 코드 가독성도 높아지고 코드 의도도 분명해진다

&nbsp;

- 깨끗한 경계
    - 설계가 우수하다면 변경하는데 많은 투자와 재작업이 필요하지 않다
    - 경계에 위치하는 코드는 깔끔히 분리한다
    - 기대치를 정의하는 테스트 케이스를 작성한다
        - 새로운 클래스로 감싸거나
        - Adapter 패턴을 사용
        
        ⇒코드 가독성이 높아지고, 경계 인터페이스를 사용하는 일관성도 높아지며, 외부 패키지가 변했을 때 변경할 코드도 줄어든다.