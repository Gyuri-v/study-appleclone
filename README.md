βπ»1λΆμ½λ© - μ ν μΈν°λν°λΈ ν΄λ‘ 
===========================================



πΒ  2022.03.01 ~ 2022.03.23

β°Β κ°μ 12μκ° / μμμκ° 18μκ°

[https://github.com/Gyuri-v/study-appleclone](https://github.com/Gyuri-v/study-appleclone)

[μ ν μΉμ¬μ΄νΈ μΈν°λμ ν΄λ‘ ! - μΈνλ° | κ°μ](https://www.inflearn.com/course/%EC%95%A0%ED%94%8C-%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8-%EC%9D%B8%ED%84%B0%EB%9E%99%EC%85%98-%ED%81%B4%EB%A1%A0/dashboard)

* μ λ£κ°μ μ΄λ―λ‘, html & css λ μλ΅νκ³  μΌλΆ λ΄μ©λ§ μλ ₯

## JAVASCRIPT

---

### π€ κ° μμ­ Height μΈν & νμ¬ νμ± μ¬ λ°μ

1. sceneInfo κ°μ²΄ μμ± (κ° μμ­λ³λ‘, μμ­μμμ μΌμ΄λ  λμμ μν΄)
    1. heightNum : window.innerHeight * ν΄λΉ κ° μΌλ‘ height μΈν
    2. scrollHeight : height κ°μ΄ λ  λ³μ
    3. objs : ν΄λΉ μμ­ μμμ μ¬μ©λ  μ€λΈμ νΈλ€ μ μ
    
2. μ€ν¬λ‘€ μΉμμ λμ΄ μΈν ( scrollHeight )

1. νμ¬ νμ±ν μ¬ λ°μ ( currentScene,  prevScrollHeight)

```jsx
let yOffset = 0; // window.pageYOffset λμ  μΈ λ³μ
let prevScrollHeight = 0; // νμ¬ μ€ν¬λ‘€ μμΉ(yOffset)λ³΄λ€ μ΄μ μ μμΉν μ€ν¬λ‘€ μΉμλ€μ μ€ν¬λ‘€ λμ΄κ°μ ν©
let currentScene = 0; // νμ¬ νμ±νλ(λ μμ λ³΄κ³ μλ) μ¬(scroll-section)

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
  // κ° μ€ν¬λ‘€ μΉμμ λμ΄ μΈν
  for (let i = 0; i < sceneInfo.length; i++) {
    sceneInfo[i].scrollHeight = sceneInfo[i].heightNum * window.innerHeight;
    sceneInfo[
      i
    ].objs.container.style.height = `${sceneInfo[i].scrollHeight}px`;
  }
}

function scrollLoop() {
	// νμ¬ νμ±ν μ¬ λ°μ
  prevScrollHeight = 0; // ν΄λΉ μμ­ μ΄μ κΉμ§μ htight κ°

	// νμ¬ μ¬μ΄ λ°λκ³  currentScene κ°μ΄ μ¬λΌκ°λ©΄, prevScrollHeight κ°λ μ¦κ°
  for (let i = 0; i < currentScene; i++) {
    prevScrollHeight += sceneInfo[i].scrollHeight;
  }

	// currentScene λ³κ²½
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

### π€ νΉμ  νμ΄λ° μ€ν¬λ‘€ μ μ©

1. sceneInfo.values κ° μΆκ° - {start: , end: } μμ λ μΆκ°

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
    // heightNum: 5, // type normal μμλ νμμμ
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

1. ν΄λΉ μμ­μμμμ {start: , end: } μμ  κ° κ΅¬ν΄μ,  ifλ¬Έ μ²΄ν¬ 
    
    - value[0] β value[1] μΌλ‘ μ λλ©μ΄μ λλλ‘ μ μ©
    
    - else λ value[0], value[1] μΌλ‘ κ° κ³ μ 
    

```jsx
function calcValues(values, currentYOffset) {
  let rv;
  // νμ¬ μ¬μμ μ€ν¬λ‘€λ λ²μλ₯Ό λΉμ¨λ‘ κ΅¬νκΈ°
  const scrollHeight = sceneInfo[currentScene].scrollHeight;
  const scrollRatio = currentYOffset / scrollHeight;

  if (values.length === 3) {
    // start ~ end μ¬μ΄μ μ λλ©μ΄μ μ€ν
    const partScrollStart = values[2].start * scrollHeight;
    const partScrollEnd = values[2].end * scrollHeight;
    const partScrollHeight = partScrollEnd - partScrollStart;

    if ( currentYOffset >= partScrollStart && currentYOffset <= partScrollEnd ) {
      rv = ((currentYOffset - partScrollStart) / partScrollHeight) * (values[1] - values[0]) + values[0];
    } else if (currentYOffset < partScrollStart) {
			// start, end μμΉ λ°μμ , value[0], value[1] μΌλ‘ κ° κ³ μ 
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
  const currentYOffset = yOffset - prevScrollHeight;  // νμ¬ μμ­μμμ yOffset κ°
  const scrollHeight = sceneInfo[currentScene].scrollHeight;  // νμ¬ μμ­ height
  const scrollRatio = currentYOffset / scrollHeight; // νμ¬ μμ­μμμ μ€ν¬λ‘€ λΉμ¨

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

### π€ λΉλμ€ μΈν°λμ μ μ©

- λΉλμ€ μ μ΄ λ°©λ²
    1. video νκ·Έμ duration μ μ€ν¬λ‘€ λΉμ¨λλ‘ λ³κ²½ (κ³ νμ§ μμμΌ κ²½μ° λκΉ νμ)
    2. λμμ νλ μ μ΄λ―Έμ§ μ μ₯ ν, μ€ν¬λ‘€ λΉμ¨μ λ§μΆ° μ΄λ―Έμ§ λ³κ²½ (μ¬μ΄μ¦ ν¬κ³ , κ³ νμ§μΌ κ²½μ° μ½κ°μ λ²λ²μ)
    3. λμμ νλ μ μ΄λ―Έμ§ μ μ₯ ν, canvas λ₯Ό νμ©νμ¬ context.drawImage(videoImages[0], 0, 0); μΌλ‘ μ μ΄
    
1. sceneInfo κ°μ²΄ κ° μΆκ°

```jsx
const sceneInfo = [
  {
    // 0
    type: 'sticky',
    heightNum: 5,
    scrollHeight: 0,
    objs: {
			// μΊλ²μ€λ context μ€μ 
      canvas: document.querySelector('#video-canvas-0'),
			context: document.querySelector('#video-canvas-0').getContext('2d'),
      videoImages: [],
    },
    values: {
      videoImageCount: 300, // μ΄ μ΄λ―Έμ§ κ°μ
      imageSequence: [0, 299],
			canvas_opacity: [1, 0, { start: 0.9, end: 1 }],
    },
  },
];
```

1. ν΄λΉ μμ­ μμμ λΉλμ€ μ¬μμν€λλ‘ κ΅¬ν & Opacity μ μ©

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

1. λ°μν μμ - window.innerHeight κ°μ λ°λΌμ μ€μΌμΌ μ‘°μ 

```jsx
function setLayout() {
	// ~~~~~~

  const heightRatio = window.innerHeight / 1080;
  sceneInfo[0].objs.canvas.style.transform = `translate3d(-50%, -50%, 0) scale(${heightRatio})`;
}
```

1. μ€ν¬λ‘€ μμ μ μλ, window.load λλ©΄ μ²«λ²μ§Έ μ΄λ―Έμ§κ° λμ€λλ‘ μ€μ 

```jsx
window.addEventListener('load', () => {
  setLayout();
  sceneInfo[0].objs.context.drawImage(sceneInfo[0].objs.videoImages[0], 0, 0);
});
```

### π€ μΊλ²μ€λ₯Ό νμ©νμ¬ μ΄λ―Έμ§ λλ‘μ° / λΈλ λ©

![ezgif-2-b73756108d.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/effefc8e-48ab-43cd-af04-5d50a30680e6/ezgif-2-b73756108d.gif)

1. **sceneInfo κ°μ²΄ κ° μΆκ°**

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

1. **setCanvasImage μ μ¬3 μΊλ²μ€ μΆκ°**

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

1. **μΊλ²μ€ λλ‘μ° μ λλ©μ΄μ** 
    
    μ’μ° ν°μλ°μ€κ° μ μμΌλ‘ μ¬λΌμ§λ©΄μ, μ΄λ―Έμ§κ° νλ©΄ κ½ μ°¨κ² κ΅¬ν
    
    1) λ°μν λμ - μ’μ°μ λ§μΆ°μ(contain) μΊλ²μ€ μ€μΌμΌ μ‘°μ 
    
    ```jsx
    const widthRatio = window.innerWidth / objs.canvas.width;
    const heightRatio = window.innerHeight / objs.canvas.height;
    let canvasScaleRatio;
    
    if( widthRatio <= heightRatio ){
      // μΊλ²μ€λ³΄λ€ λΈλΌμ°μ κ° νμ­ν κ²½μ°
      canvasScaleRatio = heightRatio;
    }else{
      // μΊλ²μ€λ³΄λ€ λΈλΌμ°μ κ° λ©μν κ²½μ°
      canvasScaleRatio = widthRatio;
    }
    objs.canvas.style.transform = `scale(${canvasScaleRatio})`;
    ```
    
    2) μ’μ° ν°μ λ°μ€ μμ±
    
    ```jsx
    objs.context.fillRect(values.rect1X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);  // x, y, width, height
    objs.context.fillRect(values.rect2X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);
    ```
    
    β λ μ νν κ°μ μν΄ κΈ°μ‘΄μ calcValue ν¨μλ₯Ό νμ©νμ¬ κ° κ³μ°
    
    ```jsx
    objs.context.fillRect( parseInt(calcValues(values.rect1X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
    objs.context.fillRect( parseInt(calcValues(values.rect2X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
    ```
    
    3) λ°μ€ μ λ³κ²½ ( β κ²μ μ΄ λν΄νΈ )
    
    ```jsx
    objs.context.fillStyle = '#fff';
    ```
    
    4)  μ λλ©μ΄μ μμ / λ μμ  & x, y μ’ν κ³μ° ( canvas λ΄μμ λ°μ€μ xμ’ν κ³μ° )
    
    ```jsx
    // μΊλ²μ€ μ¬μ΄μ¦μ λ§μΆ° κ°μ ν InnerWidth μ innerHeight
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
    
    values.rectStartY : μ λλ©μ΄μμ΄ μμνλ y κ° 
    
    = κΈ°μ‘΄ canvas μ¬μ΄μ¦μ offsetTop κ° + ( κΈ°μ‘΄ canvasμ ν¬κΈ°μμ μΆμλ ν¬κΈ°μ height μ°¨μ΄ / 2 )
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4a1dcc68-6888-440a-9ae5-0cd99519bc6a/Untitled.png)
    
    values.rect1X[0] = μΌμͺ½ λ°μ€μ μ΄κΈ° xκ° : κΈ°μ‘΄ canvas μ width κ° - body width κ° 
    
    values.rect1X[1] = μΌμͺ½ λ°μ€μ μ€ν¬λ‘€ ν x κ° : μμ κ° - μΌμͺ½ λ°μ€μ width κ°
    
    βοΈΒ μ½λ μ λ¦¬λ³Έ 
    
    ```jsx
    function playAnimation() {
    	switch (currentScene) {
    	// ~~~~~
      case 3:
        let step = 0;
        // κ°λ‘/μΈλ‘ λͺ¨λ κ½ μ°¨κ² νκΈ°μν΄ μ¬κΈ°μ μΈν(κ³μ°)
        const widthRatio = window.innerWidth / objs.canvas.width;
        const heightRatio = window.innerHeight / objs.canvas.height;
        let canvasScaleRatio;
    
        if( widthRatio <= heightRatio ){
          // μΊλ²μ€λ³΄λ€ λΈλΌμ°μ κ° νμ­ν κ²½μ°
          canvasScaleRatio = heightRatio;
        }else{
          // μΊλ²μ€λ³΄λ€ λΈλΌμ°μ κ° λ©μν κ²½μ°
          canvasScaleRatio = widthRatio;
        }
    
        objs.canvas.style.transform = `scale(${canvasScaleRatio})`;
        objs.context.fillStyle = '#fff';
        objs.context.drawImage(objs.images[0], 0, 0);
    
        // μΊλ²μ€ μ¬μ΄μ¦μ λ§μΆ° κ°μ ν InnerWidth μ innerHeight
        const recalculatedInnerWidth = document.body.offsetWidth / canvasScaleRatio;
        const recalculatedInnerHeight = document.body.offsetWidth / canvasScaleRatio;
    
        // μ λλ©μ΄μ μμ / λ μμ 
        // getBoundingClientRect() : νλ©΄μμ μλ μ€λΈμ νΈμ ν¬κΈ°μ μμΉλ₯Ό κ°μ Έμ¬ μ μμ -- λ¨μ
        if( !values.rectStartY ){
          //values.rectStartY = objs.canvas.getBoundingClientRect().top;
          values.rectStartY = objs.canvas.offsetTop + (objs.canvas.height - (objs.canvas.height * canvasScaleRatio)) / 2;
          values.rect1X[2].start = (window.innerHeight / 2) / scrollHeight;
          values.rect2X[2].start = (window.innerHeight / 2) / scrollHeight;
          values.rect1X[2].end = values.rectStartY / scrollHeight;
          values.rect2X[2].end = values.rectStartY / scrollHeight;
        }
    
    		// μ’μ° ν°μ λ°μ€ ν¬κΈ° λ° μ’ν
        const whiteRectWidth = recalculatedInnerWidth * 0.15;
        values.rect1X[0] = (objs.canvas.width - recalculatedInnerWidth) / 2;
        values.rect1X[1] = values.rect1X[0] - whiteRectWidth;
        values.rect2X[0] = values.rect1X[0] + recalculatedInnerWidth - whiteRectWidth;
        values.rect2X[1] = values.rect2X[0] + whiteRectWidth;
    
        // μ’μ° ν°μ λ°μ€ κ·Έλ¦¬κΈ°
        // objs.context.fillRect(values.rect1X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);  // x, y, width, height
        // objs.context.fillRect(values.rect2X[0], 0, parseInt(whiteRectWidth), objs.canvas.height);
        objs.context.fillRect( parseInt(calcValues(values.rect1X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
        objs.context.fillRect( parseInt(calcValues(values.rect2X, currentYOffset )), 0, parseInt(whiteRectWidth), objs.canvas.height);
    	}
    }
    ```
    

1. **μΊλ²μ€ μ΄λ―Έμ§ λΈλ λ© β μΆμ ν β λ€μ μ€ν¬λ‘€ κΉμ§**
    
    
    **1) μ΄λ―Έμ§ λΈλ λ© μ²λ¦¬ = μ΄λ―Έμ§ λ³κ²½** 
    
    - μ΄λ―Έμ§λ₯Ό fixed μ²λ¦¬ ν ν, canvas μ 2λ²μ§Έ μ΄λ―Έμ§λ₯Ό y κ°κ³Ό height κ°μΌλ‘ λ³κ²½
    - *drawImage(image, dx, dy, dWidth, dHeight)*
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a2c54f08-8851-4b0d-9d09-1bd06679f075/Untitled.png)
    
    - position fixed ν λ top κ°μ λ°μνμΌλ‘ scale μ΄ λ€μ΄κ° μμΌλ, κ³μ°μ΄ νμ
        
        objs.canvas.style.top = `${-(objs.canvas.height - (objs.canvas.height * canvasScaleRatio)) / 2}px`;
        
    - canvas μ 2λ²μ§Έ μ΄λ―Έμ§μ μ΅νλ¨μμλΆν° μ‘°κΈμ© λ³΄μ¬μΌ νλ―λ‘, y κ°κ³Ό height κ°μ κ³μ°νμ¬ λ³κ²½
    
    ```jsx
    if( scrollRatio < values.rect1X[2].end ){ 
    // μΊλ²μ€κ° λΈλΌμ°μ  μλ¨μ λΏμ§ μμλ€λ©΄
      step = 1;
      objs.canvas.classList.remove('sticky');
    }else{
      step = 2;
      // μΊλ²μ€ λΏμ ν
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
    
    **2) λΈλ λ μ΄λ―Έμ§ μΆμ** 
    
    ```jsx
    // λΈλ λ μ΄λ―Έμ§ μΆμ
    if( scrollRatio > values.blendHeight[2].end ){
      // console.log('λΈλ λ λλκ³ ');
      values.canvas_scale[0] = canvasScaleRatio;
      values.canvas_scale[1] = document.body.offsetWidth / (objs.canvas.width * 1.5);
      values.canvas_scale[2].start = values.blendHeight[2].end;
      values.canvas_scale[2].end = values.canvas_scale[2].start + 0.2;
    
      objs.canvas.style.transform = `scale(${calcValues(values.canvas_scale, currentYOffset)})`;
      objs.canvas.style.marginTop = 0;
    }
    ```
    
    **3) λ€μ μ€ν¬λ‘€ μμ - canvasμ margin-top μ€μ  & νλ¨ νμ€νΈ μ λμ μ©**
    
    ```jsx
    // λ€μ μ€ν¬λ‘€ μμ
    if( scrollRatio > values.canvas_scale[2].end && values.canvas_scale[2].end > 0 ){
      objs.canvas.classList.remove('sticky');
      objs.canvas.style.marginTop = `${ scrollHeight * 0.4 }px`
    
      // νλ¨ νμ€νΈ μ¬λΌμ€κΈ°
      values.canvasCaption_opacity[2].start = values.canvas_scale[2].end;
      values.canvasCaption_opacity[2].end = values.canvasCaption_opacity[2].start + 0.1;
      values.canvasCaption_translateY[2].start = values.canvas_scale[2].end;
      values.canvasCaption_translateY[2].end = values.canvasCaption_opacity[2].start + 0.1;
    
      objs.canvasCaption.style.opacity = calcValues(values.canvasCaption_opacity, currentYOffset);
      objs.canvasCaption.style.transform = `translate3d(0, ${calcValues(values.canvasCaption_translateY, currentYOffset)}%, 0)`
    }
    ```
    
    βοΈΒ μ½λ μ λ¦¬λ³Έ 
    
    ```jsx
    function playAnimation() {
    	switch (currentScene) {
    	// ~~~~~
      case 3:
    		// ~~~~~~
    
        if( scrollRatio < values.rect1X[2].end ){ // μΊλ²μ€κ° λΈλΌμ°μ  μλ¨μ λΏμ§ μμλ€λ©΄
          step = 1;
          // console.log('μΊλ²μ€ λΏκΈ° μ ');
          objs.canvas.classList.remove('sticky');
        }else{
          step = 2;
          // μ΄λ―Έμ§ λΈλ λ
          // console.log('μΊλ²μ€ λΏμ ν');
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
    

### π€ requestAnimationFrame

νλ©΄μ κ·Έλ¦΄ μ€λΉκ° μλ£ λμλ, μ΅μ νλ₯Ό ν΄μ μ λλ©μ΄μμ λΆλλ½κ² μλ 

- setInterver μ λͺ¨λ°μΌ λ°°ν°λ¦¬ λ±μ μ΄μλ‘ μΈν΄ λμ²΄λκ³  μμ
- canvas μμ λ§μ΄ μ¬μ©
- 1μ΄μ 60ν μ λ
- cancleAnimationFrame( requestAnimationFrame(render) ) μΌλ‘ μ μ§

β λ§μ°μ€ λλ ν€λ³΄λλ‘ μ€ν¬λ‘€ νλ²μ© λ΄λ¦΄ λ, μ λλ©μ΄μμ΄ μ μ μΌλ‘ λ±λ± λ³κ²½λλ κ²μ μμ 

β κ°μμ μ£Όμ΄ νλ¬κ°λ― μ λλ©μ΄μ μ¬μ

```jsx
let acc = 0.1;
let delayedYOffset = 0;
let rafId;
let rafState;

// yOffset --> delayedYOffset μΌλ‘ λ³κ²½
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

// requestAnimationFrame μ μ© μΈν
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

### πΒ μ΅μ’λ³Έ

[AirMug Pro](https://gyuri-v.github.io/study-appleclone/)

### **βΒ LOG**

> **πΒ 2022.3.1**
> 
> 
> > κ°μ μμ
> > 
> > 
> > 1. μλ£ λ€μ΄ λ° κ΅¬ν λ΄μ© λ―Έλ¦¬λ³΄κΈ°
> > 
> > 2. μΉνμ΄μ§ κ³¨κ²© λ§λ€κΈ° - html, css λ§ν¬μ
> > 
> > 3. μ€ν¬λ‘€μ μ΄μ©ν μΈν°λμ κ΅¬ν
> > 
> 
> **πΒ 2022.3.2**
> 
> > 1. νΉμ  νμ΄λ° μ€ν¬λ‘€ μ λλ©μ΄μ κΈ°λ₯ μΆκ°
> > 
> > 
> > 2. λΉλμ€ μΈν°λμ μ μ©
> > 
> 
> **πΒ 2022.3.7**
> 
> > 1. canvas μ€μΌμΌ κ³μ°νμ¬ λ°μν λμ
> > 
> 
> **πΒ 2022.3.22**
> 
> > 1. λΈλ λ© μΊλ²μ€ - μ΄λ―Έμ§ λλ‘μ° & λΈλ λ©
> > 
> 
> **πΒ 2022.3.23**
> 
> > 1. requestAnimationFrame μ μ©
> > 
> > 
> > 2. λ¦¬μ¬μ΄μ¦ λμ
> > 
> > 3. λ‘λ©νλ©΄ λ° μ΄λ²€νΈ μΆκ°
> > 
> > 4. μ΄κΈ°κ° μ€μ  / css μ΅μ ν
> > 
> > 5. λ²κ·Έ μμ  / νλ¨ μΌλ° μμ­ μΆκ°
> > 
> > 6. μ΄λ―Έμ§ λ‘λ© μ΅μ ν
> > 

### πΒ κ·ΈμΈ κΈ°μ΅νκΈ°

- CSS
    - will-change :
        1. css μμ μμκ° λ³νν κ±°λΌκ³  λ―Έλ¦¬ μλ €μ£Όλκ². μ΅μ ν
        2. μ€ν¬λ¦½νΈλ‘ νμ±,λΉνμ±ν μν€λκ² μ’μ
        3. μ΅νμ μλ¨
        4. μ΄κ³Όμ¬μ©μ κ³Όλν λ©λͺ¨λ¦¬ μ¬μ©μ μ΄λ
- JS
    - debugger: λΈλ μ΄ν¬λ₯Ό κ±Έμ΄μ νλνλ μ€ννλ©΄μ νμ€νΈ
    - setInterval() : μ§μ λ μκ°λ§λ€ λ°λ³΅
    - clearInterval( setIntervalμ μ μ₯ν λ³μ ) : setInterval μ μ§
    - window.DOMContentLoaded : html λ± λ‘λ λλ©΄ μ€ν / window.load λ³΄λ€ λ¨Όμ 
    - finishedLoadingImages : (canvas λ±) μ μ΄λ―Έμ§κ° λ‘λκ° λλ μλλ μ²΄ν¬
