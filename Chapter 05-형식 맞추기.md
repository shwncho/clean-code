# 5장 형식 맞추기

- 형식을 맞추는 목적
    - 코드 형식은 의사소통의 일환
    - 맨 처음 잡아놓은 구현 스타일과 가독성 수준은 유지보수 **용이성과 확장성에 계속 영향을 미친다.**
- 적절한 행 길이를 유지하라
    - 신문 기사처럼 작성하라
        - 이름은 간단하면서도 설명이 가능하게 짓는다.
        - 소스파일 첫 부분은 개념과 알고리즘을 설명, 내려갈수록 의도를 세세하게 묘사
    - 개념은 빈 행으로 분리하라
        
        ```java
        public class BoldWidget extends ParentWidget {
        	public static final String REGEXP = "'''.+'''";
        	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
        		Pattern.MULTILINE + Pattern.DOTALL
        	);
        
        	public BoldWidget(ParentWidget parent, String text) throws Exception{
        		super(parent);
        		Matcher match = pattern.matcher(text);
        		match.find();
        		addChildWidgets(match.group(1));
        	}
        
        	public String render() throws Exception {
        		StringBuffer html = new StringBuffer("<b>");
        		html.append(childHtml()).append("</b>");
        		return html.toString();
        	}
        }
        ```
        
    
    - 세로 밀집도
        - 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다.
    - 수직 거리
        - 변수는 사용하는 위치에 최대한 가까이 선언한다.
        
        ```java
        public int countTestCases(){
        	int count = 0;
        	for ( Test each : tests)
        		count += each.countTestCases();
        	return count;
        }
        ```
        
        - 인스턴스 변수는 클래스 맨 처음에 선언한다.
        - 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다.
        - 친화도가 높을수록 코드를 가까이 배치한다.
            - 비슷한 동작을 수행하는 함수를 가까이 놓는다.
    - 세로 순서

- 가로 형식 맞추기
    - 가로 공백과 밀집도
    - 가로 정렬
    - 들여쓰기
    - 가짜 범위
- 팀 규칙
    - 팀은 한 가지 규칙에 합의해야 한다.
- 밥 아저씨의 형식 규칙