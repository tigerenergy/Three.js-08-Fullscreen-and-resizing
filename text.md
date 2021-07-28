한국어 번역

소개
현재 캔버스의 고정 해상도는 800x600. 전체 화면에 맞도록 WebGL이 반드시 필요한 것은 아니지만 몰입형 경험을 원한다면 더 좋을 수 있습니다.

먼저 캔버스가 사용 가능한 모든 공간을 차지하도록 하고 싶습니다. 그런 다음 사용자가 창 크기를 조정해도 여전히 맞는지 확인해야 합니다. 마지막으로 사용자에게 전체 화면에서 경험을 실험할 수 있는 방법을 제공해야 합니다.

설정
스타터에는 이전 단원에서 완료한 내용이 포함되어 있습니다. 중앙에 큐브가 있고 드래그 앤 드롭으로 카메라를 이동할 수 있습니다.

뷰포트에 맞추기
캔버스 대신에 고정 번호를 사용하는 뷰포트에 완벽하게 맞게 만들려면 sizes사용, 변수 window.innerWidth및 window.innerHeight:

// ...

// Sizes
const sizes = {
width: window.innerWidth,
height: window.innerHeight
}

// ...
자바스크립트

이제 캔버스에 뷰포트의 너비와 높이가 있음을 알 수 있습니다. 불행히도 흰색 여백과 스크롤 막대가 있습니다(스크롤 막대가 보이지 않으면 스크롤을 시도하십시오).

문제는 모든 브라우저에 더 중요한 제목, 밑줄이 그어진 링크, 단락 사이의 공백 및 페이지의 패딩과 같은 기본 스타일이 있다는 것입니다. 이를 수정하는 방법은 여러 가지가 있으며 웹사이트의 나머지 부분에 따라 다를 수 있습니다. 다른 콘텐츠가 있는 경우 이 작업을 수행하는 동안 해당 콘텐츠를 깨뜨리지 마십시오.

우리는 일을 단순하게 유지하고 CSS를 사용하여 캔버스의 위치를 ​​수정할 것입니다.

템플릿은 이미 의 CSS 파일에 연결되어 src/style.css있습니다. Webpack에 익숙하지 않으면 이상하게 보일 수 있지만 CSS 파일은 script.js첫 번째 줄 에서 직접 가져옵니다 .

import './style.css'
자바스크립트
평소처럼 표준 CSS를 작성할 수 있으며 페이지가 자동으로 다시 로드됩니다.

먼저 와일드카드를 사용하여 모든 유형의 margin또는 padding모든 요소 를 제거하는 것이 좋습니다 \*.

\*
{
margin: 0;
padding: 0;
}
CSS
그런 다음 webgl클래스를 사용하여 캔버스 를 선택 하여 왼쪽 상단의 캔버스를 수정할 수 있습니다 .

.webgl
{
position: fixed;
top: 0;
left: 0;
}
CSS
메소드 를 호출할 때 Three.js가 이미 처리하고 있기 때문에 캔버스에서 width또는 를 지정할 필요가 없습니다 .heightrenderer.setSize(...)

이것은 우리 캔버스의 작은 문제를 해결할 좋은 기회입니다. 드래그 앤 드롭할 때 파란색 윤곽선을 본 적이 있을 것입니다. 이것은 대부분 최신 버전의 Chrome에서 발생합니다. 이 문제를 해결하기 위해 다음을 추가하면 outline: none;됩니다 .webgl.

.webgl
{
position: fixed;
top: 0;
left: 0;
outline: none;
}
자바스크립트
터치 스크린에서도 스크롤 유형을 제거하려면 및 다음 overflow: hidden모두에 추가할 수 있습니다 .htmlbody

html,
body
{
overflow: hidden;
}
CSS
/assets/lessons/08/step-03.png

이제 모든 영광에서 WebGL을 즐길 수 있습니다. 불행히도 창의 크기를 조정하면 캔버스가 따라가지 않습니다.

크기 조정을 처리해야 합니다.

핸들 크기 조정
캔버스 크기를 조정하려면 먼저 창 크기가 조정되는 시점을 알아야 합니다. 이를 위해 resize창 에서 이벤트를 들을 수 있습니다 .

변수 resize바로 뒤에 리스너를 추가합니다 sizes.

window.addEventListener('resize', () =>
{
console.log('window has been resized')
})
자바스크립트
이제 창 크기가 조정될 때 함수를 트리거하므로 코드에서 몇 가지 사항을 업데이트해야 합니다.

먼저 sizes변수 를 업데이트해야 합니다 .

window.addEventListener('resize', () =>
{
// Update sizes
sizes.width = window.innerWidth
sizes.height = window.innerHeight
})
자바스크립트
둘째, 속성 camera을 변경 하여 종횡비 를 업데이트해야 합니다 aspect.

window.addEventListener('resize', () =>
{
// ...

    // Update camera
    camera.aspect = sizes.width / sizes.height

})
자바스크립트
카메라 속성을 변경할 때 를 aspect사용하여 투영 행렬도 업데이트해야 합니다 camera.updateProjectionMatrix(). 우리는 나중에 행렬에 대해 이야기할 것입니다:

window.addEventListener('resize', () =>
{
// ...

    camera.updateProjectionMatrix()

})
자바스크립트
마지막으로 업데이트해야 합니다 renderer. 렌더러를 업데이트하면 캔버스 너비와 높이가 자동으로 업데이트됩니다.

window.addEventListener('resize', () =>
{
// ...

    // Update renderer
    renderer.setSize(sizes.width, sizes.height)

})
자바스크립트
모두 함께:

window.addEventListener('resize', () =>
{
// Update sizes
sizes.width = window.innerWidth
sizes.height = window.innerHeight

    // Update camera
    camera.aspect = sizes.width / sizes.height
    camera.updateProjectionMatrix()

    // Update renderer
    renderer.setSize(sizes.width, sizes.height)

})
자바스크립트
창의 크기를 원하는 대로 조정할 수 있습니다. 캔버스는 스크롤 막대나 오버플로 없이 뷰포트를 덮어야 합니다.

픽셀 비율 처리
여러분 중 일부는 가장자리에 계단 모양의 흐릿한 렌더링 및 인공물(앨리어싱이라고 함)을 볼 수 있지만 모두는 아닙니다. 그렇다면 픽셀 비율이 1. 보다 큰 화면에서 테스트하고 있기 때문 입니다.

픽셀 비율은 소프트웨어 부품의 한 픽셀 단위에 대해 화면에 있는 물리적 픽셀 수에 해당합니다.

일부 역사
몇 년 전만 해도 모든 화면에 의 픽셀 비율이 1있었고 모든 것이 정상이었습니다. 하지만 화면을 자세히 보면 그 픽셀들이 보일 수 있는데, 이는 이미지가 얼마나 정밀하고 글꼴이 가늘어야 하는지에 대한 한계였습니다.

그 일을 가장 많이 한 회사는 애플이었습니다. 애플은 기회를 포착하고 2레티나라 는 픽셀 비율로 화면을 구성하기 시작했습니다 . 이제 많은 생성자가 이를 수행하고 있으며 훨씬 더 높은 픽셀 비율의 화면을 볼 수 있습니다.

이것은 이미지 품질에 좋은 것이지만 픽셀 비율은 2렌더링할 픽셀이 4배 더 많다는 것을 의미합니다. 그리고 픽셀 비율은 3렌더링할 픽셀이 9배 더 많다는 것을 의미합니다.

그리고 무엇을 추측? 가장 높은 픽셀 비율은 일반적으로 가장 약한 장치인 모바일 장치에 있습니다.

거기에 당신의 프레임 속도가 간다.

픽셀 비율 처리
사용할 수 있는 화면 픽셀 비율을 가져 window.devicePixelRatio오고 렌더러의 픽셀 비율을 업데이트하려면 다음을 호출하기만 하면 됩니다.renderer.setPixelRatio(...)

단순히 장치 픽셀 비율을 해당 메서드로 보내고 싶지만 높은 픽셀 비율 장치에서 성능 문제가 발생하게 됩니다.

픽셀 비율이 더 큰 2것은 대부분 마케팅입니다. 당신의 눈은간에 거의 차이를 볼 수 2하고 3있지만, 성능 문제를 만들고 더 빨리 배터리를 비울 것입니다. 픽셀 비율을 로 제한할 수 있습니다 2. 그렇게 하려면 다음을 사용할 수 있습니다 Math.min().

renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
자바스크립트
/assets/lessons/08/step-05.png

픽셀 비율이 변경될 때 알림을 받는 기술이 있지만 픽셀 비율이 다른 여러 화면을 사용하는 사용자에게만 해당되며 일반적으로 화면에서 다른 화면으로 변경할 때 창 크기를 조정합니다. 이것이 바로 이 메소드를 resize콜백에도 추가하는 이유입니다 .

window.addEventListener('resize', () =>
{
// Update sizes
sizes.width = window.innerWidth
sizes.height = window.innerHeight

    // Update camera
    camera.aspect = sizes.width / sizes.height

    // Update renderer
    renderer.setSize(sizes.width, sizes.height)
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))

})
자바스크립트
전체 화면 처리
이제 올바른 픽셀 비율로 사용 가능한 모든 공간을 차지하는 캔버스가 있으므로 전체 화면에 대한 지원을 추가할 차례입니다.

먼저 전체 화면 모드를 트리거할 작업을 결정해야 합니다. HTML 버튼일 수도 있지만 대신 더블 클릭을 사용하겠습니다.

두 번 클릭이 발생하면 전체 화면으로 전환됩니다. 즉, 창이 전체 화면이 아닌 경우 두 번 클릭하면 전체 화면 모드가 활성화되고 창이 이미 전체 화면에 있는 경우 두 번 클릭하면 전체 화면 모드가 종료됩니다.

먼저 더블 클릭 이벤트를 수신해야 하며 이벤트로 이를 수행할 수 있습니다 dblclick.

window.addEventListener('dblclick', () =>
{
console.log('double click')
})
자바스크립트
이 이벤트는 Chrome Android를 제외한 대부분의 최신 브라우저에서 작동합니다. https://developer.mozilla.org/docs/Web/API/Element/dblclick_event

이제 이벤트가 있으므로 3가지가 필요합니다.

이미 전체 화면인지 확인하는 방법
전체 화면 모드로 이동하는 방법
전체 화면 모드를 종료하는 방법
이미 전체 화면인지 여부를 확인하려면 다음을 사용할 수 있습니다 document.fullscreenElement.

window.addEventListener('dblclick', () =>
{
if(!document.fullscreenElement)
{
console.log('go fullscreen')
}
else
{
console.log('leave fullscreen')
}
})
자바스크립트
전체 화면을 요청하는 메서드는 요소와 연결됩니다. 전체 화면으로 표시할 항목을 선택할 수 있기 때문입니다. 전체 페이지, 모든 DOM 요소 또는 <canvas>.

우리는 그것에 <canvas>대한 requestFullscreen()메소드를 사용 하고 호출할 것입니다:

window.addEventListener('dblclick', () =>
{
if(!document.fullscreenElement)
{
canvas.requestFullscreen()
}
else
{
console.log('leave fullscreen')
}
})
자바스크립트
전체 화면 모드를 종료하는 방법은 document다음에서 직접 사용할 수 있습니다 .

window.addEventListener('dblclick', () =>
{
if(!document.fullscreenElement)
{
canvas.requestFullscreen()
}
else
{
document.exitFullscreen()
}
})
자바스크립트
전체 화면 모드를 전환하려면 아무 곳이나 두 번 클릭하여 결과를 테스트할 수 있습니다. 불행히도 이것은 Safari에서 작동하지 않습니다.

이 브라우저는 전체 화면과 같은 공식적으로 간단한 기능을 지원하는 데 시간을 할애하고 있으며 document.fullscreenElement, canvas.requestFullscreen, 및 에서 작동하려면 접두사 버전을 사용해야 합니다 document.exitFullscreen.

window.addEventListener('dblclick', () =>
{
const fullscreenElement = document.fullscreenElement || document.webkitFullscreenElement

    if(!fullscreenElement)
    {
        if(canvas.requestFullscreen)
        {
            canvas.requestFullscreen()
        }
        else if(canvas.webkitRequestFullscreen)
        {
            canvas.webkitRequestFullscreen()
        }
    }
    else
    {
        if(document.exitFullscreen)
        {
            document.exitFullscreen()
        }
        else if(document.webkitExitFullscreen)
        {
            document.webkitExitFullscreen()
        }
    }

})
자바스크립트
모든 최신 브라우저에서 모든 것이 잘 작동해야 합니다.
