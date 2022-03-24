☝🏻1분코딩 - 애플 인터랙티브 클론
===========================================


📅  2022.03.01 ~ 2022.03.23

⏰ 강의 12시간 / 소요시간 18시간

[https://github.com/Gyuri-v/study-appleclone](https://github.com/Gyuri-v/study-appleclone)

[애플 웹사이트 인터랙션 클론! - 인프런 | 강의](https://www.inflearn.com/course/%EC%95%A0%ED%94%8C-%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8-%EC%9D%B8%ED%84%B0%EB%9E%99%EC%85%98-%ED%81%B4%EB%A1%A0/dashboard)

* 유료강의 이므로, html & css 는 생략하고 일부 내용만 입력

## JAVASCRIPT

---

### 🤍 각 영역 Height 세팅 & 현재 활성 씬 반영

1. sceneInfo 객체 생성 (각 영역별로, 영역안에서 일어날 동작을 위해)
    1. heightNum : window.innerHeight * 해당 값 으로 height 세팅
    2. scrollHeight : height 값이 될 변수
    3. objs : 해당 영역 안에서 사용될 오브젝트들 정의
    
2. 스크롤 섹션의 높이 세팅 ( scrollHeight )

1. 현재 활성화 씬 반영 ( currentScene,  prevScrollHeight)

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
	// 현재 활성화 씬 반영
  prevScrollHeight = 0; // 해당 영역 이전까지의 htight 값

	// 현재 씬이 바뀌고 currentScene 값이 올라가면, prevScrollHeight 값도 증가
  for (let i = 0; i < currentScene; i++) {
    prevScrollHeight += sceneInfo[i].scrollHeight;
  }

	// currentScene 변경
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

### 🤍 특정 타이밍 스크롤 적용

1. sceneInfo.values 값 추가 - {start: , end: } 시점도 추가

```jsx
const sceneInfo = [
  {
    // 0
    type: 'sticky',
    heightNum: 5,
    scrollHeight: 0,
    objs: {
      container: document.querySelector('#scroll-section-0'),
      messageA: document.querySelector('#scroll-section-0 .main-message.a'),
    },
    values: {
      messageA_opacity_in: [0, 1, { start: 0.1, end: 0.2 }],
      messageA_translateY_in: [20, 0, { start: 0.1, end: 0.2 }],
      messageA_opacity_out: [1, 0, { start: 0.25, end: 0.3 }],
      messageA_translateY_out: [0, -20, { start: 0.25, end: 0.3 }],
    },
  },
  {
    // 1
    type: 'normal',
    // heightNum: 5, // type normal 에서는 필요없음
    scrollHeight: 0,
    objs: {
      container: document.querySelector('#scroll-section-1'),
    },
  },
  {
      // 2
      type: 'sticky',
      heightNum: 5,
      scrollHeight: 0,
      objs: {
          container: document.querySelector('#scroll-section-2'),
          messageA: document.querySelector('#scroll-section-2 .a'),
      },
      values: {
          messageA_translateY_in: [20, 0, { start: 0.15, end: 0.2 }],
          messageA_opacity_in: [0, 1, { start: 0.15, end: 0.2 }],
          messageA_translateY_out: [0, -20, { start: 0.3, end: 0.35 }],
          messageA_opacity_out: [1, 0, { start: 0.3, end: 0.35 }],
      }
  },
];
```

1. 해당 영역안에서의 {start: , end: } 시점 값 구해서,  if문 체크 
    
    - value[0] → value[1] 으로 애니메이션 되도록 적용
    
    - else 는 value[0], value[1] 으로 값 고정
    

```jsx
function calcValues(values, currentYOffset) {
  let rv;
  // 현재 씬에서 스크롤된 범위를 비율로 구하기
  const scrollHeight = sceneInfo[currentScene].scrollHeight;
  const scrollRatio = currentYOffset / scrollHeight;

  if (values.length === 3) {
    // start ~ end 사이에 애니메이션 실행
    const partScrollStart = values[2].start * scrollHeight;
    const partScrollEnd = values[2].end * scrollHeight;
    const partScrollHeight = partScrollEnd - partScrollStart;

    if ( currentYOffset >= partScrollStart && currentYOffset <= partScrollEnd ) {
      rv = ((currentYOffset - partScrollStart) / partScrollHeight) * (values[1] - values[0]) + values[0];
    } else if (currentYOffset < partScrollStart) {
			// start, end 위치 밖에선, value[0], value[1] 으로 값 고정
      rv = values[0];
    } else if (currentYOffset > partScrollEnd) {
      rv = values[1];
    }
  } else {
    rv = scrollRatio * (values[1] - values[0]) + values[0];
  }

  return rv;
}

function playAnimation() {
  const objs = sceneInfo[currentScene].objs;
  const values = sceneInfo[currentScene].values;
  const currentYOffset = yOffset - prevScrollHeight;  // 현재 영역에서의 yOffset 값
  const scrollHeight = sceneInfo[currentScene].scrollHeight;  // 현재 영역 height
  const scrollRatio = currentYOffset / scrollHeight; // 현재 영역에서의 스크롤 비율

  switch (currentScene) {
    case 0:
      if (scrollRatio <= 0.22) {
          // in
          objs.messageA.style.opacity = calcValues(values.messageA_opacity_in, currentYOffset);
          objs.messageA.style.transform = `translate3d(0, ${calcValues(values.messageA_translateY_in, currentYOffset)}%, 0)`;
      } else {
          // out
          objs.messageA.style.opacity = calcValues(values.messageA_opacity_out, currentYOffset);
          objs.messageA.style.transform = `translate3d(0, ${calcValues(values.messageA_translateY_out, currentYOffset)}%, 0)`;
      }
      break;

    case 1:
      break;

    case 2:
      if (scrollRatio <= 0.25) {
          // in
          objs.messageA.style.opacity = calcValues(values.messageA_opacity_in, currentYOffset);
          objs.messageA.style.transform = `translate3d(0, ${calcValues(values.messageA_translateY_in, currentYOffset)}%, 0)`;
      } else {
          // out
          objs.messageA.style.opacity = calcValues(values.messageA_opacity_out, currentYOffset);
          objs.messageA.style.transform = `translate3d(0, ${calcValues(values.messageA_translateY_out, currentYOffset)}%, 0)`;
      }
      break;

    case 3:
      break;
  }
}
```

### 🤍 비디오 인터랙션 적용

- 비디오 제어 방법
    1. video 태그의 duration 을 스크롤 비율대로 변경 (고화질 영상일 경우 끊김 형상)
    2. 동영상 프레임 이미지 저장 후, 스크롤 비율에 맞춰 이미지 변경 (사이즈 크고, 고화질일 경우 약간의 버벅임)
    3. 동영상 프레임 이미지 저장 후, canvas 를 활용하여 context.drawImage(videoImages[0], 0, 0); 으로 제어
    
1. sceneInfo 객체 값 추가

```jsx
const sceneInfo = [
  {
    // 0
    type: 'sticky',
    heightNum: 5,
    scrollHeight: 0,
    objs: {
			// 캔버스랑 context 설정
      canvas: document.querySelector('#video-canvas-0'),
			context: document.querySelector('#video-canvas-0').getContext('2d'),
      videoImages: [],
    },
    values: {
      videoImageCount: 300, // 총 이미지 개수
      imageSequence: [0, 299],
			canvas_opacity: [1, 0, { start: 0.9, end: 1 }],
    },
  },
];
```

1. 해당 영역 안에서 비디오 재생시키도록 구현 & Opacity 적용

```jsx
function setCanvasImages() {
  let imgElem;
  for (let i = 0; i < sceneInfo[0].values.videoImageCount; i++) {
    imgElem = new Image();
    imgElem.src = `./video/001/IMG_${6726 + i}.JPG`;
    sceneInfo[0].objs.videoImages.push(imgElem);
  }
}

function calcValues(values, currentYOffset) {
  let rv;
  switch (currentScene) {
    case 0:
      // console.log('0 play');
      let sequence = Math.round( calcValues(values.imageSequence, currentYOffset) );
      objs.context.drawImage(objs.videoImages[sequence], 0, 0);
			objs.canvas.style.opacity = calcValues(values.canvas_opacity, currentYOffset);
		
			// ~~~~~~~
});

setCanvasImages();
```

1. 반응형 작업 - window.innerHeight 값에 따라서 스케일 조정

```jsx
function setLayout() {
	// ~~~~~~

  const heightRatio = window.innerHeight / 1080;
  sceneInfo[0].objs.canvas.style.transform = `translate3d(-50%, -50%, 0) scale(${heightRatio})`;
}
```

1. 스크롤 작업 전에도, window.load 되면 첫번째 이미지가 나오도록 설정

```jsx
window.addEventListener('load', () => {
  setLayout();
  sceneInfo[0].objs.context.drawImage(sceneInfo[0].objs.videoImages[0], 0, 0);
});
```

### 🤍 캔버스를 활용하여 이미지 드로우 / 블렌딩

![ezgif-2-b73756108d.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/effefc8e-48ab-43cd-af04-5d50a30680e6/ezgif-2-b73756108d.gif)

1. **sceneInfo 객체 값 추가**

```jsx
const sceneInfo = [
	{
      // 3
      type: 'sticky',
      heightNum: 5,
      scrollHeight: 0,
      objs: {
          container: document.querySelector('#scroll-section-3'),
          canvasCaption: document.querySelector('.canvas-caption'),
          canvas: document.querySelector('.image-blend-canvas'),
          context: document.querySelector('.image-blend-canvas').getContext('2d'),
          imagesPath: [
            './images/blend-image-1.jpg',
            './images/blend-image-2.jpg'
          ],
          images: []
      },
      values: {
        rect1X: [0, 0, { start: 0, end: 0 } ],
        rect2X: [0, 0, { start: 0, end: 0 } ],
        blendHeight: [0, 0, { start: 0, end: 0 } ],
        rectStartY: 0,
      }
    },
];
```

1. **setCanvasImage 에 씬3 캔버스 추가**

```jsx
function setCanvasImages() {
  // ~~~ 

  let imgElem3;
  for(let i = 0; i < sceneInfo[3].objs.imagesPath.length; i++){
    imgElem3 = new Image();
    imgElem3.src = sceneInfo[3].objs.imagesPath[i];
    sceneInfo[3].objs.images.push(imgElem3);
  }
}
```

1. **캔버스 드로우 애니메이션** 
    
    좌우 흰색박스가 양 옆으로 사라지면서, 이미지가 화면 꽉 차게 구현
    
    1) 반응형 대응 - 좌우에 맞춰서(contain) 캔버스 스케일 조정
    
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
    
    2) 좌우 흰색 박스 생성
    
    ```jsx
    objs.context.fillRect(values.rect1X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);  // x, y, width, height
    objs.context.fillRect(values.rect2X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);
    ```
    
    → 더 정확한 값을 위해 기존의 calcValue 함수를 활용하여 값 계산
    
    ```jsx
    objs.context.fillRect( parseInt(calcValues(values.rect1X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
    objs.context.fillRect( parseInt(calcValues(values.rect2X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
    ```
    
    3) 박스 색 변경 ( — 검정이 디폴트 )
    
    ```jsx
    objs.context.fillStyle = '#fff';
    ```
    
    4)  애니메이션 시작 / 끝 시점 & x, y 좌표 계산 ( canvas 내에서 박스의 x좌표 계산 )
    
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
    
    ✏️ 코드 정리본 
    
    ```jsx
    function playAnimation() {
    	switch (currentScene) {
    	// ~~~~~
      case 3:
        let step = 0;
        // 가로/세로 모두 꽉 차게 하기위해 여기서 세팅(계산)
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
        objs.context.fillStyle = '#fff';
        objs.context.drawImage(objs.images[0], 0, 0);
    
        // 캔버스 사이즈에 맞춰 가정한 InnerWidth 와 innerHeight
        const recalculatedInnerWidth = document.body.offsetWidth / canvasScaleRatio;
        const recalculatedInnerHeight = document.body.offsetWidth / canvasScaleRatio;
    
        // 애니메이션 시작 / 끝 시점
        // getBoundingClientRect() : 화면상에 있는 오브젝트의 크기와 위치를 가져올 수 있음 -- 단순
        if( !values.rectStartY ){
          //values.rectStartY = objs.canvas.getBoundingClientRect().top;
          values.rectStartY = objs.canvas.offsetTop + (objs.canvas.height - (objs.canvas.height * canvasScaleRatio)) / 2;
          values.rect1X[2].start = (window.innerHeight / 2) / scrollHeight;
          values.rect2X[2].start = (window.innerHeight / 2) / scrollHeight;
          values.rect1X[2].end = values.rectStartY / scrollHeight;
          values.rect2X[2].end = values.rectStartY / scrollHeight;
        }
    
    		// 좌우 흰색 박스 크기 및 좌표
        const whiteRectWidth = recalculatedInnerWidth * 0.15;
        values.rect1X[0] = (objs.canvas.width - recalculatedInnerWidth) / 2;
        values.rect1X[1] = values.rect1X[0] - whiteRectWidth;
        values.rect2X[0] = values.rect1X[0] + recalculatedInnerWidth - whiteRectWidth;
        values.rect2X[1] = values.rect2X[0] + whiteRectWidth;
    
        // 좌우 흰색 박스 그리기
        // objs.context.fillRect(values.rect1X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);  // x, y, width, height
        // objs.context.fillRect(values.rect2X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);
        objs.context.fillRect( parseInt(calcValues(values.rect1X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
        objs.context.fillRect( parseInt(calcValues(values.rect2X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
    	}
    }
    ```
    

1. **캔버스 이미지 블렌딩 → 축소 후 → 다시 스크롤 까지**
    
    
    **1) 이미지 블렌딩 처리 = 이미지 변경** 
    
    - 이미지를 fixed 처리 한 후, canvas 의 2번째 이미지를 y 값과 height 값으로 변경
    - *drawImage(image, dx, dy, dWidth, dHeight)*
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a2c54f08-8851-4b0d-9d09-1bd06679f075/Untitled.png)
    
    - position fixed 할때 top 값은 반응형으로 scale 이 들어가 있으니, 계산이 필요
        
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
    
    **2) 블렌드 이미지 축소** 
    
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
    
    **3) 다시 스크롤 시작 - canvas에 margin-top 설정 & 하단 텍스트 애니적용**
    
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
    
    ✏️ 코드 정리본 
    
    ```jsx
    function playAnimation() {
    	switch (currentScene) {
    	// ~~~~~
      case 3:
    		// ~~~~~~
    
        if( scrollRatio < values.rect1X[2].end ){ // 캔버스가 브라우저 상단에 닿지 않았다면
          step = 1;
          // console.log('캔버스 닿기 전');
          objs.canvas.classList.remove('sticky');
        }else{
          step = 2;
          // 이미지 블렌드
          // console.log('캔버스 닿은 후');
          values.blendHeight[0] = 0;
          values.blendHeight[1] = objs.canvas.height;
          values.blendHeight[2].start = values.rect1X[2].end;
          values.blendHeight[2].end = values.blendHeight[2].start + 0.2;
          const blendHeight = calcValues(values.blendHeight, currentYOffset);
    
          // .drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight);
          objs.context.drawImage(objs.images[1], 
            0, objs.canvas.height - blendHeight, objs.canvas.width, blendHeight,
            0, objs.canvas.height - blendHeight, objs.canvas.width, blendHeight 
          );
    
          objs.canvas.classList.add('sticky');
          objs.canvas.style.top = `${-(objs.canvas.height - (objs.canvas.height * canvasScaleRatio)) / 2}px`;
        }
    	}
    }
    ```
    

### 🤍 requestAnimationFrame

화면을 그릴 준비가 완료 됐을때, 최적화를 해서 애니메이션을 부드럽게 작동 

- setInterver 은 모바일 배터리 등의 이슈로 인해 대체되고 있음
- canvas 에서 많이 사용
- 1초에 60회 정도
- cancleAnimationFrame( requestAnimationFrame(render) ) 으로 정지

→ 마우스 또는 키보드로 스크롤 한번씩 내릴 때, 애니메이션이 정적으로 딱딱 변경되는 것을 수정

→ 감속을 주어 흘러가듯 애니메이션 재생

```jsx
let acc = 0.1;
let delayedYOffset = 0;
let rafId;
let rafState;

// yOffset --> delayedYOffset 으로 변경
if (delayedYOffset > prevScrollHeight + sceneInfo[currentScene].scrollHeight) {
  enterNewScene = true;
  currentScene++;
  document.body.setAttribute('id', `show-scene-${currentScene}`);
}
if (delayedYOffset < prevScrollHeight) {
  enterNewScene = true;
  if (currentScene === 0) return;
  currentScene--;
}

// requestAnimationFrame 적용 세팅
function loop() {
  delayedYOffset = delayedYOffset + (yOffset - delayedYOffset) * acc;

  if( !enterNewScene ){
    if( currentScene === 0 || currentScene === 2 ){
      const objs = sceneInfo[currentScene].objs;
      const values = sceneInfo[currentScene].values;
      const currentYOffset = yOffset - prevScrollHeight;
      let sequence = Math.round( calcValues(values.imageSequence, currentYOffset) );
      if( objs.videoImages[sequence] ){
        objs.context.drawImage(objs.videoImages[sequence], 0, 0);
      }
    }
  }

  rafId = requestAnimationFrame(loop);

  if( Math.abs(yOffset - delayedYOffset) < 1 ){
    cancelAnimationFrame(rafId);
    rafState = false;
  }
}

window.addEventListener('scroll', () => {
  yOffset = window.pageYOffset;
  scrollLoop();
  checkMenu();

  if( !rafState ){
    rafId = requestAnimationFrame(loop); 
    rafState = true;
  }
});
```

### 👑 최종본

[AirMug Pro](https://gyuri-v.github.io/study-appleclone/)

### **✔ LOG**

> **📑 2022.3.1**
> 
> 
> > 강의 시작
> > 
> > 
> > 1. 자료 다운 및 구현 내용 미리보기
> > 
> > 2. 웹페이지 골격 만들기 - html, css 마크업
> > 
> > 3. 스크롤을 이용한 인터랙션 구현
> > 
> 
> **📑 2022.3.2**
> 
> > 1. 특정 타이밍 스크롤 애니메이션 기능 추가
> > 
> > 
> > 2. 비디오 인터랙션 적용
> > 
> 
> **📑 2022.3.7**
> 
> > 1. canvas 스케일 계산하여 반응형 대응
> > 
> 
> **📑 2022.3.22**
> 
> > 1. 블렌딩 캔버스 - 이미지 드로우 & 블렌딩
> > 
> 
> **📑 2022.3.23**
> 
> > 1. requestAnimationFrame 적용
> > 
> > 
> > 2. 리사이즈 대응
> > 
> > 3. 로딩화면 및 이벤트 추가
> > 
> > 4. 초기값 설정 / css 최적화
> > 
> > 5. 버그 수정 / 하단 일반 영역 추가
> > 
> > 6. 이미지 로딩 최적화
> > 

### 📝 그외 기억하기

- CSS
    - will-change :
        1. css 에서 요소가 변화할거라고 미리 알려주는것. 최적화
        2. 스크립트로 활성,비활성화 시키는게 좋음
        3. 최후의 수단
        4. 초과사용은 과도한 메모리 사용을 초래
- JS
    - debugger: 브레이크를 걸어서 하나하나 실행하면서 테스트
    - setInterval() : 지정된 시간마다 반복
    - clearInterval( setInterval을 저장한 변수 ) : setInterval 정지
    - window.DOMContentLoaded : html 등 로드 되면 실행 / window.load 보다 먼저
    - finishedLoadingImages : (canvas 등) 의 이미지가 로드가 됐나 안됐나 체크
