# 10장 클래스

- 클래스 체계
    - 정적 공개 상수, 정적 비공개 변수, 비공개 인스턴스 변수, 공개 함수, 비공개 함수 순으로 나온다.
    - 즉, 추상화 단계가 순차적으로 내려간다
    - 캡슐화
        - 변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫다.
        - 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.

&nbsp;

- 클래스는 작아야 한다!
    - 클래스를 설계할 때도, 함수와 마찬가지로 ‘작게’가 기본 규칙이다.
    - 클래스 이름은 해당 클래스 책임을 기술해야 한다.
    - 단일 책임 원칙
        - 클래스나 모듈을 **변경할 이유**가 단 하나뿐이어야 한다.
        - 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다.
    - 응집도
        - 클래스는 인스턴스 변수 수가 작아야 한다.
        - 메서드가 변수를 더 많이 사용할수록 메서드와 클래스는 응집도가 더 높다.
    - 응집도를 유지하면 작은 클래스 여럿이 나온다
        - 클래스가 응집력을 잃는다면 쪼개라!

&nbsp;

- 변경하기 쉬운 클래스
    - 새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 시스템 구조가 바람직하다.
    - 이상적인 시스템은 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지는 않는다.
    
    ```java
    abstract public class Sql{
    	public Sql(String table, Column[] columns)
    	abstract public String generate();
    }
    
    public class CreateSql extends Sql{
    	public CreateSql(String table, Column[] columns)
    	@Override public String generate()
    }
    
    public class InsertSql extends Sql{
    	public InsertSql(String table, Column[] columns, Object[] fields)
    	@Override public String generate()
    	private String valuesList(Object[] fields, final Column[] columns)
    }
    
    public class SelectWithCriteriaSql extends Sql{
    	public SelectWithCriteriaSql(
    		String table, Column[] columns, Criteria criteria)
    	@Override public String generate()
    }
    
    public class SelectWithMatchSql extends Sql{
    	public SelectWithMatchSql(
    		String table, Column[] columns, Column column, String pattern)
    	@Override public String generate()
    }
    
    public class FindByKeySql extends Sql{
    	public FindByKeySql(
    		String table, Column[] columns, String keyColumn, String keyValue)
    	@Override public String generate()
    }
    
    public class PreparedInsertSql extends Sql{
    	public PreparedInsertSql(String table, Column[] columns)
    	@Override public String generate()
    	private String placeholderList(Column[] columns)
    }
    
    public class Where{
    	public Where(String criteria)
    	public String generate()
    }
    
    public class ColumnList{
    	public ColumnList(Column[] columns)
    	public String generate()
    }
    ```
    
    - 변경으로부터 격리
        - 시스템의 결합도를 낮추면 유연성과 재사용성도 더욱 높아진다.
        - ex) 환율
        ```java
        public interface StockExchange{
            Money currentPrice(String symbol);
        }

        public Portfolio{
            private StockExchange exchange;
            public Portfolio(StockExchange exchange){
                this.exchange = exchange;
            }
            
            // ...
        }
        ```