# 6장 객체와 자료 구조

- 자료 추상화
    - 자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다.
- 자료/객체 비대칭
    - 객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며, 절차적인 코드에서 어려운 변경은 객체 지향 코드에서 쉽다
- 디미터 법칙
    - 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다.
    - 즉, 객체는 조회 함수로 내부 구조를 공개하면 안된다.
- 자료 전달 객체(DTO)
    
    ```java
    public class Address{
    	private String street;
    	private String streetExtra;
    	private String city;
    	private String state;
    	private String zip;
    
    	public Address(String street, String streetExtra, String city,
    									String state, String zip){
    		this.street = street;
    		this.streetExtra = streetExtra;
    		this.city = city;
    		this.state = state;
    		this.zip = zip;
    	}
    
    	public String getStreet(){
    		return street;
    	}
    
    	public String getStreetExtra(){
    		return streetExtra;
    	}
    
    	public String getCity(){
    		return city;
    	}
    
    	public String getState(){
    		return state;
    	}
    
    	public String getZip(){
    		return zip;
    	}
    }
    ```
    
- 결론
    - 객체는 동작을 공개하고 자료를 숨긴다.
    - 새로운 자료 타입을 추가하는 유연성이 필요하면 → 객체
    - 새로운 동작을 추가하는 유연성이 필요하면 → 자료 구조, 절차적인 코드