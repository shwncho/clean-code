# 13장 동시성

- 동시성이 필요한 이유?
    - 동시성은 무엇과 언제를 분리하는 전략이다.
        - 동시성의 미신과 오해
            - 동시성은 항상 성능을 높여준다.
            - 동시성을 구현해도 설계는 변하지 않는다.
            - 웹 또는 EJB 컨테이너를 사용하면 동시성을 이해할 필요가 없다.
        - 동시성과 관련된 타당한 생각
            - 동시성은 다소 부하를 유발한다.
            - 동시성은 복잡하다.
            - 일반적으로 동시성 버그는 재현하기 어렵다.
            - 동시성을 구현하려면 흔히 근본적인 설계 전략을 재고해야 한다.

&nbsp;

- 난관
    - 동시성을 구현하기 어려운 이유
    
    ```java
    public class X{
    	private int lastIdUsed;
    
    	public int getNextId(){
    		return ++lastIdUsed;
    	}
    }
    
    예상되는 결과
    
    한 스레드는 43을 받는다. 다른 스레드는 44를 받는다. lastIdUsed는 44가 된다.
    한 스레드는 44를 받는다. 다른 스레드는 43을 받는다. lastIdUsed는 44가 된다.
    한 스레드는 43을 받는다. 다른 스레드는 43을 받는다. lastIdUsed는 43이 된다.
    
    ```

&nbsp;


- 동시성 방어 원칙
    - 단일 책임 원칙(SRP)
        - 동시성 관련 코드는 다른 코드와 분리하라
    - 따름 정리: 자료 범위를 제한하라
        - 자료를 캡슐화하라. 공유 자료를 최대한 줄여라
    - 따름 정리: 자료 사본을 사용하라
        - 객체를 복사해 읽기 전용으로 사용하는 방법
        - 객체를 복사해 사용한 후 한 스레드가 해당 사본에서 결과를 가져오는 방법
    - 따름 정리: 스레드는 가능한 독립적으로 구현하라
        - 독자적인 스레드로, 가능하면 다른 프로세서에서, 돌려도 괜찮도록 자료를 독립적인 단위로 분할하라

&nbsp;

- 라이브러리를 이해하라
    - 스레드 환경에 안전한 컬렉션
        - ConcurrentHashMap
    - 언어가 제공하는 클래스를 검토하라. 자바에서는 java.util.concurrent, java.util.concurrent.atomic, java.util.concurrent.locks을 익혀라

&nbsp;

- 실행 모델을 이해하라
    - 기본 용어
        - 한정된 자원
        - 상호 배제
        - 기아
        - 데드락
        - 라이브락
    - 생산자-소비자
        - 생산자 스레드와 소비자 스레드가 사용하는 대기열은 **한정된 자원**이다.
    - 읽기-쓰기
        - 읽기 쓰레드와 쓰기 쓰레드 양쪽 균형을 잡으면서 동시 갱신 문제를 피하는 해법이 필요하다.
    - 식사하는 철학자들
    
    ⇒위에서 설명한 기본 알고리즘과 각 해법을 이해하라

&nbsp;


- 동기화하는 메서드 사이에 존재하는 의존성을 이해하라
    - 공유 클래스 하나에 동기화된 메서드가 여럿이라면 구현이 올바른지 다시 한 번 확인하라
    - 공유 객체 하나에는 메서드 하나만 사용하라
    - 공유 객체 하나에 여러 메서드가 필요한 상황일 경우 세 가지 방법을 고려
        - 클라이언트에서 잠금
            - 클라이언트에서 첫 번째 메서드를 호출하기 전에 서버를 잠근다.
        - 서버에서 잠금
            - 서버를 잠그고 모든 메서드를 호출한 후 잠금을 해제하는 메서드를 구현한다.
        - 연걸서버 잠금
            - 잠금을 수행하는 중간 단계를 생성한다.

&nbsp;

- 동기화하는 부분을 작게 만들어라
    - 동기화하는 부분을  최대한 작게 만들어라

&nbsp;

- 올바른 종료 코드는 구현하기 어렵다
    - 종료 코드를 개발 초기부터 고민하고 동작하게 초기부터 구현하라. 생각보다 오래 걸린다. 생각보다 어려우므로 이미 나온 알고리즘을 검토하라.

&nbsp;

- 스레드 코드 테스트하기
    - 충분한 테스트는 위험을 낮춘다.
    - 문제를 노출하는 테스트 케이스를 작성하라
    - 말이 안되는 실패는 잠정적인 스레드 문제로 취급하라
        - 시스템 실패를 ‘일회성’이라 치부하지 마라
    - 다중 스레드를 고려하지 않은 순차 코드부터 제대로 돌게 만들자
        - 스레드 환경 밖에서 생기는 버그와 스레드 환경에서 생기는 버그를 동시에 디버깅하지 마라. 먼저 스레드 환경 밖에서 코드를 올바로 돌려라
    - 다중 스레드를 쓰는 코드 부분을 다양한 환경에 쉽게 끼워 넣을 수 있게 스레드 코드를 구현하라
        - 다양한 설정에서 실행할 목적으로 다른 환경에 쉽게 끼워 넣을 수 있게 코드를 구현하라
    - 다중 스레드를 쓰는 코드 부분을 상황에 맞게 조율할 수 있게 작성하라
    - 프로세서 수보다 많은 스레드를 돌려보라
    - 다른 플랫폼에서 돌려보라
        - 처음부터 그리고 자주 모든 목표 플랫폼에서 코드를 돌려라
    - 코드에 보조 코드를 넣어 돌려라. 강제로 실패를 일으키게 해보라
        - 보조 코드 추가하는 방법
            - 직접 구현하기
                - 코드에다 직접 wait(), sleep(), yield(), priority() 함수를 추가한다.
            - 자동화
                - AOF,CGLIB,ASM 등과 같은 도구를 사용한다.
                - ThreadJigglePoint.jiggle()
                    - 무작위로 sleep이나 yield를 호출, 때로는 아무 동작도 하지 않는다.
                - 흔들기 기법을 사용해 오류를 찾아내라

&nbsp;

- 결론
    - 다중 스레드 코드는 올바로 구현하기 어렵다.
    - 무엇보다 먼저 SRP를 준수한다.
    - 동시성 오류를 일으키는 잠정적인 원인을 철저히 이해한다.
    - 사용하는 라이브러리와 기본 알고리즘을 이해한다.
    - 보호할 코드 영역을 찾아내는 방법과 특정 코드 영역을 잠그는 방법을 이해한다.