# study-appleclone


# 3.1

### 강의) 1분 코딩 - 애플 웹사이트  인터렉션 클론

[https://www.inflearn.com/course/애플-웹사이트-인터랙션-클론/dashboard](https://www.inflearn.com/course/%EC%95%A0%ED%94%8C-%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8-%EC%9D%B8%ED%84%B0%EB%9E%99%EC%85%98-%ED%81%B4%EB%A1%A0/dashboard)

1. 자료 다운 및 구현 내용 미리보기
2. 웹페이지 골격 만들기 - html, css 마크업
3. **스크롤을 이용한 인터랙션 구현**
    1. sceneInfo 객체 생성 - type, heightNum, scrollHeight, objs
    2. setLayout() - 각 섹션의 높이 세팅(heightNum * window.innerHeight) / 해당 높이 style 로 height 값 지정
    3. currentScene, prevScrollHeight - 스크롤시 해당 씬에 맞춰 body의 id 변경 작업 - sticky 요소들 on/off 효과를 위해
    4. 새로고침시 해당 씬에 맞춰 body에 id 값 적용
    5. setLayout() - resize 될때 재실행  / load 될때 재실행
    6. 스크롤 애니메이션 구현 - 애니메이션 주고싶은 요소와 그 애니메이션의 value 를 객체 안에 넣고, 
    
          switch 문을 활용하여 해당 영역안에서의 value 변화 부여
    
    ✏️   **js - 스크롤시 해당 영역에 맞는 id 값 body 에 부여**
    
    ```jsx
    let yOffset = 0; // window.pageYOffset 대신 쓸 변수
    let prevScrollHeight = 0; // 현재 스크롤 위치(yOffset)보다 이전에 위치한 스크롤 섹션들의 스크롤 높이값의 합
    let currentScene = 0; // 현재 활성화된(눈 앞에 보고있는) 씬(scroll-section)
    
    const sceneInfo = [
      {
        // 0
        heightNum: 5,
        scrollHeight: 0,
        objs: {
          container: document.querySelector('#scroll-section-0'),
        },
      },
      {
        // 1
        heightNum: 5,
        scrollHeight: 0,
        objs: {
          container: document.querySelector('#scroll-section-1'),
        },
      },
    ];
    
    function setLayout() {
      // 각 스크롤 섹션의 높이 세팅
      for (let i = 0; i < sceneInfo.length; i++) {
        sceneInfo[i].scrollHeight = sceneInfo[i].heightNum * window.innerHeight;
        sceneInfo[
          i
        ].objs.container.style.height = `${sceneInfo[i].scrollHeight}px`;
      }
    }
    
    function scrollLoop() {
      prevScrollHeight = 0;
      for (let i = 0; i < currentScene; i++) {
        prevScrollHeight += sceneInfo[i].scrollHeight;
      }
    
      if (yOffset > prevScrollHeight + sceneInfo[currentScene].scrollHeight) {
        currentScene++;
        document.body.setAttribute('id', `show-scene-${currentScene}`);
      }
    
      if (yOffset < prevScrollHeight) {
        if (currentScene === 0) return;
        currentScene--;
        document.body.setAttribute('id', `show-scene-${currentScene}`);
      }
    }
    
    setLayout();
    window.addEventListener('scroll', () => {
      yOffset = window.pageYOffset;
      scrollLoop();
    });
    ```
    

🍊 여담) 스크롤 애니메이션 jquery 로 할때는 쉬웠는데, 스크립트로 하니까 꽤 어렵당,,

# 3.2

### 강의) 1분 코딩 - 애플 웹사이트  인터렉션 클론

1. 특정 타이밍 스크롤 애니메이션 기능 추가
    1. sceneInfo.values 에 {start: , end: }를 추가하여 어느 지점에서 부터 어느지점까지 애니메이션이 될지 정한다.
    2. if 문으로 스크롤시 start 지점의 값(스크롤 영역 높이값 * start 값) 보다 위면 0 / end 지점보다 아래면 0 처리
    3. 해당 영역(start, end 사이) 이라면, 그 영역안에서의 0 ~ 1의 값을 구해 opacity 를 적용 
        - currentYOffset : section 안에서의 스크롤 값 ( pageYOffset - section의 offsetTop)
        - partScrollHeight : 해당 영역(start, end 사이) 의 height
    
    ```jsx
    ((currentYOffset - partScrollStart) / partScrollHeight) * (values[1] - values[0]) + values[0];
    ```
    
    d. 응용하여 translate3d 적용 / 다른 요소들도 적용
    

1. 비디오 인터랙션
    1. 비디오 제어 방법 
        1. video 태그의 duration 을 스크롤 비율대로 변경 (고화질 영상일 경우 끊김 형상)
        2. 동영상 프레임 이미지 저장 후, 스크롤 비율에 맞춰 이미지 변경 (사이즈 크고, 고화질일 경우 약간의 버벅임)
        3. 동영상 프레임 이미지 저장 후, canvas 를 활용하여 context.drawImage(videoImages[0], 0, 0); 으로 제어
    

🍊 여담) 비디오 인터랙션은 생각보다 쉽당,, 스크롤애니가 어려워서 인 듯 하다 ㅎㅎ

# 3.7

### 강의) 1분 코딩 - 애플 웹사이트  인터렉션 클론

1. 비디오 화면에 따라 조정 - 비디오 canvas 영역에 transform으로 scale 조정
    - 해당 비율 : window.innerHeight / 1080(캔버스 크기)

# 3.22

### 강의) 1분 코딩 - 애플 웹사이트  인터렉션 클론

1. 3번째 영역 - 블렌딩 캔버스 
    1. 가로/세로 꽉차게 하도록 scale 세팅
        
        ```jsx
        const widthRatio = window.innerWidth / objs.canvas.width;
        const heightRatio = window.innerHeight / objs.canvas.height;
        let canvasScaleRatio;
        
        if( widthRatio <= heightRatio ){
          // 캔버스보다 브라우저가 홀쭉한 경우
          canvasScaleRatio = heightRatio;
        }else{
          // 캔버스보다 브라우저가 납작한 경우
          canvasScaleRatio = widthRatio;
        }
        objs.canvas.style.transform = `scale(${canvasScaleRatio})`;
        ```
        
    2. 좌우 흰색 박스 
        
        ```jsx
        objs.context.fillRect(values.rect1X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);  // x, y, width, height
        objs.context.fillRect(values.rect2X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);
        ```
        
        >> 더 정확한 값을 위해 기존의 calcValue 함수를 활용하여 값 계산
        
        ```jsx
        objs.context.fillRect( parseInt(calcValues(values.rect1X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
        objs.context.fillRect( parseInt(calcValues(values.rect2X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
        ```
        
        - 박스 색 변경 — 검정이 디폴트
        
        ```jsx
        objs.context.fillStyle = '#fff';
        ```
        
    3. 좌우 박스가 양쪽으로 사라지는 애니메이션을 위해 시작/끝 계산, canvas 내에서 박스의 x좌표 계산
        
        ```jsx
        // 캔버스 사이즈에 맞춰 가정한 InnerWidth 와 innerHeight
        const recalculatedInnerWidth = document.body.offsetWidth / canvasScaleRatio;
        const recalculatedInnerHeight = document.body.offsetWidth / canvasScaleRatio;
        
        if( !values.rectStartY ){
        	//values.rectStartY = objs.canvas.getBoundingClientRect().top;
        	values.rectStartY = objs.canvas.offsetTop + (objs.canvas.height - (objs.canvas.height * canvasScaleRatio)) / 2;
        	values.rect1X[2].start = (window.innerHeight / 2) / scrollHeight;
        	values.rect2X[2].start = (window.innerHeight / 2) / scrollHeight;
        	values.rect1X[2].end = values.rectStartY / scrollHeight;
        	values.rect2X[2].end = values.rectStartY / scrollHeight;
        }
        
        const whiteRectWidth = recalculatedInnerWidth * 0.15;
        values.rect1X[0] = (objs.canvas.width - recalculatedInnerWidth) / 2;
        values.rect1X[1] = values.rect1X[0] - whiteRectWidth;
        values.rect2X[0] = values.rect1X[0] + recalculatedInnerWidth - whiteRectWidth;
        values.rect2X[1] = values.rect2X[0] + whiteRectWidth;
        ```
        
        values.rectStartY : 애니메이션이 시작하는 y 값 
        
        = 기존 canvas 사이즈의 offsetTop 값 + ( 기존 canvas의 크기에서 축소된 크기의 height 차이 / 2 )
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4a1dcc68-6888-440a-9ae5-0cd99519bc6a/Untitled.png)
        
        values.rect1X[0] = 왼쪽 박스의 초기 x값 : 기존 canvas 의 width 값 - body width 값 
        
        values.rect1X[1] = 왼쪽 박스의 스크롤 후 x 값 : 위의 값 - 왼쪽 박스의 width 값
        
    4. 이미지 블렌딩 처리 = 이미지 변경 
        - 이미지를 fixed 처리 한 후, canvas 의 2번째 이미지를 y 값과 height 값으로 변경해 주어야 함
        - *drawImage(image, dx, dy, dWidth, dHeight)*
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a2c54f08-8851-4b0d-9d09-1bd06679f075/Untitled.png)
        
        - position fixed 할때 top 값은, scale 이 들어가 있으니, 계산해 줘서 넣어야함
            
            objs.canvas.style.top = `${-(objs.canvas.height - (objs.canvas.height * canvasScaleRatio)) / 2}px`;
            
        - canvas 의 2번째 이미지의 최하단에서부터 조금씩 보여야 하므로, y 값과 height 값을 계산하여 변경
        
        ```jsx
        if( scrollRatio < values.rect1X[2].end ){ 
        // 캔버스가 브라우저 상단에 닿지 않았다면
          step = 1;
          objs.canvas.classList.remove('sticky');
        }else{
          step = 2;
          // 캔버스 닿은 후
          values.blendHeight[0] = 0;
          values.blendHeight[1] = objs.canvas.height;
          values.blendHeight[2].start = values.rect1X[2].end;
          values.blendHeight[2].end = values.blendHeight[2].start + 0.2;
          const blendHeight = calcValues(values.blendHeight, currentYOffset);
        
          objs.context.drawImage(objs.images[1], 
            0, objs.canvas.height - blendHeight, objs.canvas.width, blendHeight,
            0, objs.canvas.height - blendHeight, objs.canvas.width, blendHeight 
          );
        
          objs.canvas.classList.add('sticky');
          objs.canvas.style.top = `${-(objs.canvas.height - (objs.canvas.height * canvasScaleRatio)) / 2}px`;
        }
        ```
        
    5. 블렌드 이미지 축소 
        
        ```jsx
        // 블렌드 이미지 축소
        if( scrollRatio > values.blendHeight[2].end ){
          // console.log('블렌드 끝나고');
          values.canvas_scale[0] = canvasScaleRatio;
          values.canvas_scale[1] = document.body.offsetWidth / (objs.canvas.width * 1.5);
          values.canvas_scale[2].start = values.blendHeight[2].end;
          values.canvas_scale[2].end = values.canvas_scale[2].start + 0.2;
        
          objs.canvas.style.transform = `scale(${calcValues(values.canvas_scale, currentYOffset)})`;
          objs.canvas.style.marginTop = 0;
        }
        ```
        
    6. 다시 스크롤 시작 - canvas에 margin-top 설정 & 하단 텍스트 애니적용
        
        ```jsx
        // 다시 스크롤 시작
        if( scrollRatio > values.canvas_scale[2].end && values.canvas_scale[2].end > 0 ){
          objs.canvas.classList.remove('sticky');
          objs.canvas.style.marginTop = `${ scrollHeight * 0.4 }px`
        
          // 하단 텍스트 올라오기
          values.canvasCaption_opacity[2].start = values.canvas_scale[2].end;
          values.canvasCaption_opacity[2].end = values.canvasCaption_opacity[2].start + 0.1;
          values.canvasCaption_translateY[2].start = values.canvas_scale[2].end;
          values.canvasCaption_translateY[2].end = values.canvasCaption_opacity[2].start + 0.1;
        
          objs.canvasCaption.style.opacity = calcValues(values.canvasCaption_opacity, currentYOffset);
          objs.canvasCaption.style.transform = `translate3d(0, ${calcValues(values.canvasCaption_translateY, currentYOffset)}%, 0)`
        }
        ```
        
2. requestAnimationFrame() : 화면을 그릴 준비가 완료 됐을때, 최적화를 해서 애니메이션을 부드럽게 작동 / setInterver 은 모바일 배터리 등의 이슈로 인해 대체되고 있음
    - canvas 에서 많이 사용함
    - 1초에 60회 정도 하려고 노력함
    - cancleAnimationFrame( requestAnimationFrame(render) ) 으로 멈출 수 있음
