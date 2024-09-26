# HTML을 시작하며

HTML은 Hyper Text Markup Language의 약자로, Tim Berners-Lee가 개발한 마크업 언어입니다.


## 웹 브라우저

웹 브라우저(크롬, 엣지, 파이어폭스, 사파리)의 목적은 HTML 문서를 읽고 올바르게 표시하는 것다.

브라우저는 HTML 태그를 표시하지 않지만, 이를 사용하여 문서를 표시하는 방법을 결정합니다.

HTML 요소는 시작 태그부터 끝 태그까지의 모든 것을 말합니다.

< h1 > 내 첫 번째 제목 < /h1 >
< p > 첫 번째 문단입니다. < /p >

일부 HTML 요소에는 내용이 없습니다.(ex: <br>).
이러한 요소를 빈 요소라고 합니다. 빈 요소에는 종료 태그(</요소>)가 없습니다.


### 기본적인 HTML 구조

<html>
<head>
<title>페이지 제목</title>
</head>
<body>
<h1>이것은 제목입니다.</h1>
<p>이것은 문단입니다.</p>
<p>이것은 또 다른 문단입니다.</p>
</body>
</html>

<body> 섹션 내부의 콘텐츠는 브라우저에 표시됩니다.
<title> 요소 내부의 콘텐츠는 브라우저의 타이틀 바 또는 페이지 탭에 표시됩니다.

처음 배울 땐 전문적인 지식이 없으니 메모장이나 써줍시다.
Mac 사용자라면 TextEdit 쓰시구요.

그냥 열고 아래 코드를 복붙한다음,

<!DOCTYPE html> /*HTML5에 대한 선언*/
<html>
<body>

<h1>My First Heading</h1>

<p>My first paragraph.</p>

</body>
</html>

파일 > 다른 이름으로 저장을 선택하세요.
파일 이름을 "index.html" 으로 지정 하고, 인코딩을 UTF-8 (HTML 파일에 가장 적합한 인코딩)로 선택합니다.
확장자로는 htm, html 둘 다 됩니다.(둘 다 같음)

아까 코드를 보면  <!DOCTYPE html> /*HTML5에 대한 선언*/ 라고 있는데 제가 주석을 달아놓은 그대로, 이 문서 파일은 HTML5를 쓰는 문서라는 뜻입니다.

이게 제목입니다. (여기서 제목이라고 한게 웹페이지 위에 뜨는 제목이 아니라 내부에 뜨는 제목, 부제 등입니다.)

<h1>가장 큰 제목</h1>
<h2>두 번째로 큰 제목</h2>
<h3>세 번째로 큰 제목</h3>

h1 부터 h6까지 점점 작아집니다.


HTML 문단입니다.

<p>This is a paragraph.</p>
<p>This is another paragraph.</p>

<p> 태그는 문단 전환이기에 p 태그는 줄바꿈이 되지만, p 태그 안에서는 space나 단순 enter로 인한 줄바꿈은 html이 읽지 않습니다.
아래 두개를 txt파일에 집어넣고 확장자를 html로 만들고 열어보면 이해 할 수 있어용.

<p>
This paragraph
contains a lot of lines
in the source code,
but the browser
ignores it.
</p>

<p>
This paragraph
contains         a lot of spaces
in the source         code,
but the        browser
ignores it.
</p>

CTRL + U를 누르면 HTML 코드를 볼 수 있습니다

### HTML 링크

  <a href="https://www.w3schools.com">w3schools로 가는 하이퍼링크</a>
  하이퍼링크로써 안에 있는 텍스트를 누르면 하이퍼링크로 연결이 됩니다
  a href 옆에 title= 을 넣으면 해당 텍스트에 마우스 커서를 올리면 그 내용이 들어갑니다.
  속성 에서 URL을 지정하는 방법은 두 가지가 있습니다. 바로 절대 URL과 상대 URL 이죠.

1. 절대 URL - 다른 웹사이트에 호스팅된 외부 이미지로의 링크. 예: src="https://www.w3schools.com/images/img_girl.jpg".
2. 상대 URL - 웹사이트 내에 호스팅된 이미지에 대한 링크입니다. 여기서 URL에는 도메인 이름이 포함되지 않습니다. URL이 슬래시 없이 시작하면 현재 페이지에 상대적입니다. 예: src="img_girl.jpg". URL이 슬래시로 시작하면 도메인에 상대적입니다. 예: src="/images/img_girl.jpg".

팁: 상대 URL을 사용하는 것이 거의 항상 가장 좋습니다. 도메인을 변경해도 깨지지 않습니다.

  
### HTML 이미지

  <img src="w3schools.jpg" alt="W3Schools.com" width="104" height="142">
  여기서 img src에서 경로를 불러올 수도 있고, 아니면 로컬 내부의 파일을 불러올 수도 있습니다.
  width와 height으로 이미지 개체의 크기를 조절할 수 있고, %나 수치로 설정 가능합니다.

  <a href="https://www.w3schools.com"><img src="w3schoos.jpg"></a>
  a href 안에 img src를 넣어서 이미지를 하이퍼링크화해서 이미지를 누르면 이동하도록 설정할 수도 있습니다.

  
  <img src="img_girl.jpg" alt="Girl with a jacket">  
  
  alt태그 <img> 어떤 이유로 이미지를 표시할 수 없는 경우에, 이미지의 대체 텍스트를 지정합니다.
  이는 느린 연결, 속성 오류 src또는 사용자가 화면 판독기를 사용하는 경우 때문일 수 있습니다.
  당연히 없는 이미지 쓰면 텍스트만 나오고 이미지는 나오지 않습니다.

스타일 속성
  
  속성 style은 색상, 글꼴, 크기 등의 스타일을 요소에 추가하는 데 사용됩니다.
  <p style="color:red;">This is a red paragraph.</p>
  이건 단락 내부의 텍스트 색을 red로 설정한 것입니다. color 외에도 여러가지가 있습니다.
  font-size: 글자의 크기를 설정합니다.
  font-weight: 글자의 두께를 설정합니다.
  font-family: 글꼴 종류를 설정합니다.
  
<p style="font-size: 18px; font-weight: bold; font-family: Arial, sans-serif;">
  이 단락은 Arial 글꼴을 사용하며, 크기는 18px이고, 두께는 굵게 설정되었습니다.

배경 관련 속성
  
  background-color: 배경 색상을 설정합니다.
  background-image: 배경 이미지를 설정합니다.
  background-size: 배경 이미지의 크기를 설정합니다.
  
  <div style="background-color: #f0f0f0; padding: 20px;">
    이 div는 연한 회색 배경과 20px의 패딩을 가지고 있습니다.
  </div>

  margin: 요소의 외부 여백을 설정합니다.
  padding: 요소의 내부 여백을 설정합니다.

  <div style="margin: 10px; padding: 15px; border: 1px solid #ccc;">
    이 div는 10px의 외부 여백과 15px의 내부 여백을 가지고 있으며, 회색 테두리가 있습니다.
  </div>
  
테두리 관련 속성

  border: 요소의 테두리를 설정합니다.
  border-radius: 테두리의 모서리를 둥글게 설정합니다.
  <button style="border: 2px solid #4CAF50; border-radius: 5px; padding: 10px 20px;">둥근 테두리 버튼</button>
  
레이아웃 관련 속성

  display: 요소의 표시 방식을 설정합니다 (ex: block, inline, flex).
  width 및 height: 요소의 너비와 높이를 설정합니다.
  float: 요소를 왼쪽 또는 오른쪽으로 띄웁니다.

  <div style="display: flex; justify-content: center; align-items: center; height: 100vh;">
    <p style="width: 200px; height: 100px; background-color: #ffeb3b; text-align: center; line-height: 100px;">
      중앙 정렬된 박스
    </p>
  </div>

텍스트 정렬 및 장식
  
  text-align: 텍스트의 정렬을 설정합니다 (예: left, right, center).
  text-decoration: 텍스트의 장식을 설정합니다 (예: underline, none).

  <h1 style="text-align: center; text-decoration: underline;">중앙 정렬되고 밑줄이 있는 제목</h1>

  