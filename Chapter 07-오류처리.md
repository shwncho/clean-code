# 7장 오류처리

- 오류 코드보다 예외를 사용하라
    
    ```java
    public class DeviceController{
    
    	public void sendShutDown(){
    		try{
    			tryToShutDown();
    		} catch (DeviceShotDownError e){
    			logger.log(e);
    		}
    	}
    
    	private void tryToShutDown() throws DeviceShutDownError{
    		DeviceHandle handle = getHandle(DEV1);
    		DeviceRecord record = retrieveDeviceRecord(handle);
    	
    		pauseDevice(handle);
    		clearDeviceWorkQueue(handle);
    		closeDevice(handle);
    	}
    	
    	private DeviceHandle getHandle(DeviceID id){
    		...
    		throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    		...
    	}
    
    	...
    }
    
    ```
    
&nbsp;
- Try-Catch-Finally 문부터 작성하라
    - try 블록에서 무슨 일이 생기든지 catch 블록은 프로그램 상태를 일관성 있게 유지해야 하므로 예외가 발생할 코드를 짤 때는 try-catch-finally 문으로 시작하는 편이 낫다.
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName){
    	try{
    		FileInputStream stream = new FileInputStream(sectionName);
    		stream.close();
    	} catch (FileNotFoundException e){
    		throw new StorageException("retrieval error", e);
    	}
    	return new ArrayList<RecordedGrip>();
    }
    ```
    
&nbsp;
- 미확인 예외를 사용하라
    - 확인된 예외는 OCP(개방-폐쇄 원칙)를 위반한다.
        - 즉, 하위 단계에서 코드를 변경하면 상위 단계 메서드 선언부를 전부 고쳐야 한다.
  
&nbsp;
- 예외에 의미를 제공하라
    - 오류 메세지에 정보를 담아 예외를 함께 던진다.
    - 실패한 연산 이름과 실패 유형도 언급한다.

&nbsp;

- 호출자를 고려해 예외 클래스를 정의하라
    - 외부 API를 사용할 때는 감싸기 기법이 최선
    - 특정 업체가 API를 설계한 방식에 발목 잡히지 않는다.

&nbsp;

- 정상 흐름을 정의하라
    
    ```java
    try{
    	MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    	m_total += expenses.getTotal();
    } catch(MealExpensesNotFound e){
    	m_total += getMealPerDiem();
    }
    ```
    
    - 위 코드는 논리를 따라가기 어려움
  
    
    ```java
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
    
    //위처럼 간결하게 작성하기 위해 ExpenseReportDAO를 재정의
    
    public class PerDiemMealExpenses implements MealExpenses{
    	public int getTotal(){
    		//기본값으로 일일 기본 식비를 반환한다.
    	}
    }
    ```
    
    - 이를 특수 사례 패턴이라 부른다.
        - 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식
  
&nbsp;

- null을 반환하지 마라
    
    ```java
    List<Employee> employees = getEmployees();
    for(Employee e : employees){
    	totalPa += e.getPay();
    }
    
    public List<Employee> getEmployees(){
    	if( .. 직원이 없다면 .. )
    		return Collections.emptyList();
    }
    ```
    
&nbsp;

- null을 전달하지 마라
    - 메서드로 null을 전달하는 코드는 최대한 피한다.
    
    ```java
    public class MetricsCalculator{
    	public double xProjection(Point p1, Point p2){
    		if(p1==null || p2 ==null){
    			throw InvalidArgumentException(
    				"Invalid argument for MetricsCalculator.xProjection");
    		}
    		return (p2.x - p1.x) * 1.5;
    	}
    }
    ```
    
    - 또 다른 대안 assert 문
    
    ```java
    public class MetricsCalculator{
    	public double xProjection(Point p1, Point p2){
    		assert p1 != null : "p1 should not be null";
    		assert p2 != null : "p2 should not be null";
    		return (p2.x - p1.x) * 1.5;
    	}
    }
    ```
    
    - 대다수 프로그래밍 언어는 호출자가 실수로 넘기는 null을 적절히 처리하는 방법이 없다.
  
    &nbsp;

        **애초에 null을 넘기지 못하도록 금지하는 정책이 합리적이다.**
    
&nbsp;

- 결론
    
    **깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.**