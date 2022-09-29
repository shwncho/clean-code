# 4장 주석
- 주석은 나쁜 코드를 보완하지 못한다
  - 코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문이다.
  - 표현력이  풍부하고 깔끔한 주석이 거의 없는 코드가 훨씬 좋다.

- 코드로 의도를 표현하라
  - 주석으로 달려는 설명을 함수로 만들어 표현해도 충분하다.

- 좋은 주석
  - 법적인 주석
    - 소스 파일 첫 머리에 주석으로 들어가는 저작권 정보와 소유권 정보
  - 정보를 제공하는 주석
    - 
    ```java
    // kk:mm:ss EEE, MMM dd, yyyy 형식이다.
    Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d*\\w*, \\w*\\d*,\\d*");
    ```
    위 코드는 시각과 날짜를 변환하는 클래스를 만들어 코드를 옮겨주면 더 좋고 더 깔끔해진다.
  - 의도를 설명하는 주석
    - 
    ```java
    public int compareTo(Object o)
    {
      if(o instanceof WikiPagePath)
      {
          WikiPagePath p = (WikiPagePath) o;

          String compressedName = StringUtil.join(names,"");
          String compressedArgumentName = StringUtil.join(p.names, "");
          return compressedName.compareTo(compressedArgumentName);
      }
      return 1; // 오른쪽 유형이므로 정렬 순위가 더 높다.
    }

  - 의미를 명료하게 밝히는 주석
      -
      ```java
      public void testCompareTo() throws Exception
      {
          WikiPagePath a = PathParser.parse("PageA");
          WikiPagePath ab =  PathParser.parse("PageA.PageB");
          WikiPagePath b =  PathParser.parse("PageB");
          WikiPagePath aa =  PathParser.parse("PageA.PageA");
          WikiPagePath bb =  PathParser.parse("PageB.PageB");
          WikiPagePath ba =  PathParser.parse("PageB.PageA");

          assertTrue(a.compareTo(a)==0); // a==a
          assertTrue(a.compareTo(b)!=0); // a!=b
          assertTrue(ab.compareTo(ab)==0); // ab==ab
          assertTrue(a.compareTo(b)==-1); // a<b
          assertTrue(aa.compareTo(ab)==-1); // aa==ab
          assertTrue(ba.compareTo(bb)==-1); // ba<bb
          assertTrue(b.compareTo(a)==1); // b>a
          assertTrue(ab.compareTo(aa)==1); // ab>aa
          assertTrue(bb.compareTo(ba)==1); // bb==ba
      }
      ```
  - 결과를 경고하는 주석
    - 
    ```java
    // 여유 시간이 충분하지 않다면 실행하지 마십시오.
    public void _testWithReallyBigFile()
    {
        writeLinesToFile(1000000000);

        response.setBody(testFile);
        response.readyToSend(this);
        String responseString = output.toString();
        assertSubString("COntent-length: 1000000000", responseString);
        assertTrue(bytesSent > 1000000000);
    }
    - TODO 주석
      - 
      ```java
      protected VersionInfo makeVersion() throws Exception
      {
        return null;
      }
    - 중요성을 강조하는 주석
      - 
      ```java
      String listItemContent = match.group(3).trim();
      // 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
      // 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다.
      new ListItemWidget(this, listItemContent, this.level+1);
      return buildList(text.substring(match.end()));
    - 공개 API에서 Javadocs
- 나쁜 주석
  - 주절거리는 주석
  - 같은 이야기를 중복하는 주석
  - 오해할 여지가 있는 주석
  - 의무적으로 다는 주석
  - 이력을 기록하는 주석
  - 있으나 마나 한 주석
  - 무서운 잡음
  - 함수나 변수로 표현할 수 있다면 주석을 달지 마라
  - 위치를 표시하는 주석
  - 닫는 괄호에 다는 주석
  - 공로를 돌리거나 저자를 표시하는 주석
  - 주석으로 처리한 코드
  - HTML 주석
  - 전역 정보
    - 근처에 있는 코드만 기술하라
  - 너무 많은 정보
  - 모호한 관계
  - 함수 헤더
  - 비공개 코드에서 Javadocs