# 15장 JUnit 들여다보기

- JUnit 프레임 워크
- [ComparisonCompactor.java](http://ComparisonCompactor.java) (최종 버전)
    
    ```java
    package junit.framework;
    
    public class ComparisonCompactor {
    	private static final String ELLIPSIS = "...";
    	private static final String DELTA_END = "]";
      private static final String DELTA_START = "[";
    
    	private int contextLength;
    	private String expected;
    	private String actual;
    	private int prefixLength;
    	private int suffixLength;
    
    	public ComparisonCompactor(
    		int contextLength, String expected, String actual)
    	{
    		this.contextLength = contextLength;
    		this.expected = expected;
    		this.actual = actual;
    	}
    
    	public String formatCompactedComparison(String message) {
    		String compactExpected = expected;
    		String compactActual = actual;
    		if(shouldBeCompacted()) {
    			findCommonPrefixAndSuffix();
    			compactExpected = compact(expected);
    			compactActual = compact(actual);
    		}
    		return Assert.format(message, compactExpected, compactActual);
    	}
    
    	private boolean shouldBeCompacted() {
    		return !shouldNotBeCompacted();
    	}
    
    	private boolean shouldNotBeCompacted() {
    		return expected == null || actual == null || expected.equals(actual);
    	}
    
    	private void findCommonPrefixAndSuffix() {
    		findCommonPrefix();
    		suffixLength = 0;
    		for(; !suffixOverlapsPrefix(); suffixLength++){
    			if(charFromEnd(expected, suffixLength) !=
    					charFromEnd(actual, suffixLength)
    			) break;
    		}
    	}
    
    	private char charFromEnd(String s, int i) {
    		return s.charAt(s.length() -i -1);
    	}
    
    	private boolean suffixOverlapsPrefix() {
    		return actual.length() - suffixLength <= prefixLength ||
    						expected.length() - suffixLength <= prefixLength;
    	}
    
    	private void findCommonPrefix() {
    		prefixLength = 0;
    		int end = Math.min(expected.length(), actual.length());
    		for(; prefixLength < end; prefixLength++)
    			if (expected.charAt(prefixLength) != actual.charAt(prefixLength))
    				break;
    	}
    
    	private Stringc compact(String s) {
    		return new StringBuilder()
    			.append(startingEllipsis())
    			.append(startingContext())
    			.append(DELTA_START)
    			.append(delta(s))
    			.append(DELTA_END)
    			.append(endingContext())
    			.append(endingEllipsis())
    			.toString();
    	}
    
    	private String startingEllipsis() {
    		return prefixLength > contextLength ? ELLIPSIS : "";
    	}
    
    	private String startingContext() {
    		int contextStart = Math.max(0, prefixLength - contextLength);
    		int contextEnd = prefixLength;
    		return expected.substring(contextStart, contextEnd);
    	}
    
    	private String delta(String s) {
    		int deltaStart = prefixLength;
    		int deltaEnd = s.length() - suffixLength;
    		return s.substring(deltaStart, deltaEnd);
    	}
    
    	private String endingContext() {
    		int contextStart = expeted.length() - suffixLength;
    		int contextEnd =
    			Math.min(contextStart + contextLength + expeted.length());
    		return expected.substring(contextStart, contextEnd);
    	}
    
    	private String endingEllipsis() {
    		return (suffixLength > contextLength ? ELLIPSIS : "");
    	}
    }
    ```
    
    - 모듈(일련의 분석 함수 + 일련의 조합 함수)
    - 위상적 정렬했으므로 각 함수가 사용된 직후에 정의
    - 코드를 리팩터링 하다 보면 원래 했던 변경을 되돌리는 경우가 흔하다.

&nbsp;

- 결론
    - 세상에 개선이 불필요한 모듈은 없다.
    - 코드를 처음보다 조금 더 깨끗하게 만드는 책임은 우리 모두에게 있다.