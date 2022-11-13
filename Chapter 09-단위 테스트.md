# 9장 단위테스트

- TDD 법칙 세 가지
    - 첫째 법칙: 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
    - 둘째 법칙: 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
    - 셋째 법칙: 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

&nbsp;

- 깨끗한 테스트 코드 유지하기
    - 테스트 코드는 실제 코드 못지 않게 중요하다.
    - 테스트는 유연성, 유지보수성, 재사용성을 제공한다.
        - 이 제공에 대한 버팀목이 **단위 테스트**다.

&nbsp;

- 깨끗한 테스트 코드
    - 세 가지가 필요하다. 가독성, 가독성, 가독성.
    - 테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.
    
    ```java
    public void testGetPageHierarchyAsXml() throws Exception {
    	makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    
    	submitRequest("root", "type:pages");
    
    	assertResponseIsXML();
    	aasertResponseContains(
    		"<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    	);
    }
    
    public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception{
    	WikiPage page = makePage("PageOne");
    	makePages("PageOne.ChildOne", "PageTwo");
    
    	addLinkTo(page, "PageTwo", "SymPage");
    
    	submitRequest("root", "type:pages");
    
    	assertResponseIsXML();
    	assertResponseContains(
    		"<name>PageOne</name>", "<name>PageTwo</name>", <name>ChildOne</name>"
    	);
    	assertResponseDoesNotContain("SymPage");
    }
    
    public void testGetDataAsXml() throws Exception {
    	makePageWithContent("TestPageOne", "test page");
    	submitRequest("TestPageOne", "type:data");
    
    	assertResponseIsXML();
    	assertResponseContains("test page", "<Test");
    }
    ```
    
    - 도메인에 특화된 테스트 언어
    - 이중 표준
        - 실제 코드만큼 효율적일 필요는 없다.
        - 실제 환경에서는 절대 안되지만 테스트 환경에서는 전혀 문제없는 방식

&nbsp;

- 테스트 당 assert 하나
    - 상황에 따라서 assert문을 여러개 사용하는 것이 더 좋기도 하지만, assert 문 개수는 최대한 줄여야 좋다.
    - 테스트 당 개념 하나
    
    ⇒ **개념 당 assert 문 수를 최소로 줄여라 + 테스트 함수 하나는 개념 하나만 테스트 하라**
    

&nbsp;

- F.I.R.S.T
    - 빠르게(Fast)
        - 테스트는 빨리 돌아야 한다.
    - 독립적으로(Independent)
        - 각 테스트는 서로 의존하면 안된다.
    - 반복가능하게(Repeatable)
        - 테스트는 어떤 환경에서도 반복 가능해야 한다.
    - 자가검증하는(Self-Validating)
        - 테스트는 부울(bool)값으로 결과를 내야 한다.
    - 적시에(Timely)
        - 테스트는 적시에 작성해야 한다.

&nbsp;

## 결론

- 테스트 코드는 지속적으로 깨끗하게 관리하자.
- 표현력을 높이고 간결하게 정리하자.
- 테스트 API를 구현해 도메인 특화 언어를 만들자.
- 테스트 코드가 방치되어 망가지면 실제 코드도 망가진다. 테스트 코드를 깨끗하게 유지하자.