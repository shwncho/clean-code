# 3장 함수

- 작게 만들어라
    - 함수를 만드는 첫째 규칙은 작게!
    - 둘째 규칙도 더 작게!
- 한 가지만 해라
- 함수 당 추상화 수준은 하나로
- Switch 문
    - 다형성을 이용하는 방식
    
    ```java
    public Money calculatePay(Employee e)
    throws InvalidEmployeeType {
    	switch (e.type) {
    		case COMMISSIONED:
    			return calculateCommissionedPay(e);
    		case HOURLY;
    			return calculateHourlyPay(e);
    		case SALARIED:
    			return calculateSalariedPay(e);
    		default:
    			throw new InvalidEmployeeType(e.type);
    	}
    }
    ```
    
    그러나 이 방식은
    
    - 함수가 길다
    - SRP 위반
    - OCP 위반
    - 이 함수와 구조가 동일한 함수가 무한정 존재할 수 있다.
    
    위 문제를 해결하는 방식
    
    ```java
    public abstract class Employee{
    	public abstract boolean isPayday();
    	public abstract Money calculatePay();
    	public abstract void deliverPay(Money pay);
    }
    
    ---
    public interface EmployeeFactory{
    	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
    }
    
    ---
    
    public class EmployeeFactoryImpl implements EmployeeFactory {
    	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
    		switch (r.type) {
    			case COMMISSIONED:
    				return new CommissionedEmployee(r);
    			case HOURLY:
    				return new HourlyEmployee(r);
    			case SALARIED:
    				return new SalariedEmployee(r);
    			default:
    				throw new InvalidEmployeeType(r.type);
    		}
    	}
    }
    ```
    

- 서술적인 이름을 사용하라
    - 좋은 이름이 주는 가치는 아무리 강조해도 지나치지 않다.
- 함수 인수
    - 인수의 개수가 적을수록 좋다
- 부수 효과를 일으키지 마라
- 명령과 조회를 분리하라
    - 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야 한다
    - 객체 상태를 변경하거나 아니면 객체 정보를 반환하거나 둘 중 하나만
- 오류 코드보다 예외를 사용하라
    - 여러단계로 중첩되는 코드
    
    ```java
    if(deletePage(page) == E_OK) {
    	if(registry.deleteReference(page.name) == E_OK) {
    		if(configKeys.deleteKey(page.name.makeKey()) == E_OK){
    			logger.log("page deleted");
    		} else{
    			logger.log("configKey not deleted");
    		}
    	} else{
    		logger.log("deleteReference from registry failed");
    	}
    } else{
    	logger.log("delete failed");
    	return E_ERROR;
    }
    ```
    
    - 위 코드를 try-catch로 개선
    
    ```java
    try{
    	deletePage(page);
    	registry.deleteReference(page.name);
    	configKeys.deleteKey(page.name.makeKey());
    }
    catch (Exception e){
    	logger.log(e.getMessage());
    }
    ```
    
    - try-catch 블록은 원래 추하다. 함수로 뽑아내자
    
    ```java
    private void deletePageAndAllReferences(Page page) throws Exception{
    	deletePage(page);
    	registry.deleteReference(page.name);
    	configKeys.deleteKey(page.name.makeKey());
    }
    
    private void logError(Exception e){
    	logger.log(e.getMessage());
    }
    
    // 오류 처리도 한 가지 작업이다. 
    // 오류를 처리하는 함수는 오류만 처리해야 마땅하다.
    ```
    
- 반복하지 마라
- 구조적 프로그래밍
    - 루프 안에서 break나 continue를 사용해선 안되며, goto는 절대로 안된다
    - 그러나 함수가 작다면 위 규칙은 별 이익을 제공하지 못하고 함수가 아주 클 때만 상당한 이익을 제공한다.
    - 그러므로 함수를 작게 만든다면 간혹 return, break, continue를 여러 차례 사용해도 괜찮다.
- 함수를 어떻게 짜죠?
    - 소프트웨어를 짜는 행위는 여느 글짓기와 비슷하다.