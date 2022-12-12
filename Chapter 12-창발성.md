# 12장 창발성

- 창발적 설계로 깔끔한 코드를 구현하자
    - 모든 테스트를 실행한다.
    - 중복을 없앤다.
    - 프로그래머 의도를 표현한다.
    - 클래스와 메서드 수를 최소로 줄인다.

&nbsp;

- 단순한 설계 규칙 1: 모든  테스트를 실행하라
    - 테스트 케이스를 만들고 계속 돌려라
        - 낮은 결합도
        - 높은 응집력
        
        ⇒ 객체 지향 방법론이 지향하는 목표를 달성. 즉, 테스트 케이스를 작성하면 설계 품질이 높아진다.

&nbsp;


- 단순한 설계 규칙 2~4: 리팩터링
    - 리팩터링 단계에서는 소프트웨어 설계 품질을 높이는 기법이라면 무엇이든 적용해도 괜찮다.

&nbsp;

- 중복을 없애라
    
    ```java
    public void scaleToOneDimension(
    	float desiredDimension, f loat imageDimension){
    	if(Math.abs(desiredDimension - imageDimension) < errorThreshold)
    		return;
    	float scalingFactor = desiredDimension / imageDimension;
    	scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
    
    	RenderedOp new Image = ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor);
    	image.dispose();
    	System.gc();
    	image = newImage;
    }
    
    public synchronized void rotate(int degrees){
    	RenderedOp newImage = ImageUtilities.getRotatedImage(image, degrees);
    	image.dispose();
    	System.gc();
    	image = newImage;
    }
    
    중복제거한 코드
    
    public void scaleToOneDimension(
    	float desiredDimension, f loat imageDimension){
    	if(Math.abs(desiredDimension - imageDimension) < errorThreshold)
    		return;
    	float scalingFactor = desiredDimension / imageDimension;
    	scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
    
    		replaceImage(ImageUtilities.getScaledImage(
    			image, scalingFactor, scalingFactor));
    }
    
    public synchronized void rotate(int degrees){
    	replaceImage(ImageUtilities.getRotatedImage(image, degrees));
    }
    
    private void replaceImage(RenderedOp newImage){
    	image.dispose();
    	System.gc();
    	image = newImage;
    }
    ```

&nbsp;


- 표현하라
    - 좋은 이름
    - 함수와 클래스 크기를 가능한 줄인다.
    - 표준 명칭을 사용한다.
    - 단위 테스트 케이스를 꼼꼼히 작성한다.

&nbsp;

- 클래스와 메서드 수를 최소로 줄여라

&nbsp;

- 결론
    - 경험을 대신할 단순한 개발 기업은 없다.
    - 이 책에서 소개하는 기법은 경험의 정수다.
    - 단순한 설계 규칙을 따른다면 우수한 기법과 원칙을 단번에 활용할 수 있다.