# HTML을 시작하며

HTML은 Hyper Text Markup Language의 약자로, Tim Berners-Lee가 개발한 마크업 언어입니다.


# 웹 브라우저

  웹 브라우저(크롬, 엣지, 파이어폭스, 사파리)의 목적은 HTML 문서를 읽고 올바르게 표시하는 것입니다.
  브라우저는 HTML 태그를 표시하지 않지만, 이를 사용하여 문서를 표시하는 방법을 결정합니다.

  HTML 요소는 시작 태그부터 끝 태그까지의 모든 것을 말합니다.

    <h1>내 첫 번째 제목</h1>
    <p> 첫 번째 문단입니다.</p>

  일부 HTML 요소에는 내용이 없습니다.(ex: <br>).
  이러한 요소를 빈 요소라고 합니다. 빈 요소에는 종료 태그(</요소>)가 없습니다.


## 기본적인 HTML 구조
  
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

  body 섹션 내부의 콘텐츠는 브라우저에 표시됩니다.
  title 요소 내부의 콘텐츠는 브라우저의 타이틀 바 또는 페이지 탭에 표시됩니다.

  처음 배울 땐 전문적인 지식이 없으니 메모장이나 써줍시다.
  Mac 사용자라면 TextEdit 쓰시구요.
  그냥 해당하는 텍스트에디터 열고 아래 코드를 복붙한다음,

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

## 웹 페이지의 언어 선언

  웹 페이지의 언어를 선언하려면 태그 lang내부에 속성을 항상 포함해야 합니다 . 이는 검색 엔진과 브라우저를 지원하기 위한 것입니다.
  <!DOCTYPE html> 밑에 <html lang="en"> 이건 영어선언이고, <html lang="en-US"> 미국, 영어 선언입니다. 한국같은 경우는 <html lang="ko"> 혹은 <html lang="ko-kr">를   쓰고, 언어와 국가를 변경하고 싶다면 각각 en 자리에 해당하는 언어 코드를 넣고, - 뒤의 us 자리에 해당하는 국가 코드를 넣으면 됩니다.
  해당하는 코드는 ISO 639-1(언어 코드 정의), ISO 3166-1 alpha-2(국가 코드 정의) 를 찾아보시면 됩니다.

  이게 제목입니다. (여기서 제목이라고 한게 웹페이지 위에 뜨는 제목이 아니라 내부에 뜨는 제목, 부제 등입니다.)

  <h1>h1은 가장 큰 제목</h1>
  <h2>h2는 두 번째로 큰 제목</h2>
  <h3>h3은 세 번째로 큰 제목</h3>

  h1 부터 h6까지 점점 작아집니다.

  HTML 문단입니다.

    <p>This is a paragraph.</p>
    <p>This is another paragraph.</p>

  <p> 태그는 문단 전환이기에 p 태그는 줄바꿈이 되지만, p 태그 안에서는 space나 단순 enter로 인한 줄바꿈은 html이 읽지 않습니다.
  아래 두개를 txt파일에 집어넣고 확장자를 html로 만들고 열어보면 이해 할 수 있습니다.
  HTML 표준에서는 소문자 태그를 요구하지 않지만, XHTML과 같은 더 엄격한 문서 유형에서는 소문자를 요구합니다.
  그렇기에 기본적으로 태그 쓸 땐 소문자 태그를 권장합니다. 아 참고로 w3school에서 공부할거면 대소문자 구분 안하면 틀린걸로 취급하니 유의하시기 바랍니다.


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
  
  CTRL + U를 누르면 HTML 코드를 볼 수 있습니다.

## a 태그 

  문서를 링크 시키기 위해 사용하는 태그
  

  ### href 속성

  <a href="https://www.w3schools.com">w3schools로 가는 하이퍼링크</a>
  하이퍼링크로써 안에 있는 텍스트를 누르면 하이퍼링크로 연결이 됩니다.
  a href 옆에 title= 을 넣으면 해당 텍스트에 마우스 커서를 올리면 그 내용이 들어갑니다.
  속성 에서 URL을 지정하는 방법은 두 가지가 있습니다. 바로 절대 URL과 상대 URL 이죠.

  1. 절대 URL - 다른 웹사이트에 호스팅된 외부 이미지로의 링크. 예: src="https://www.w3schools.com/images/img_girl.jpg".
  2. 상대 URL - 웹사이트 내에 호스팅된 이미지에 대한 링크입니다. 여기서 URL에는 도메인 이름이 포함되지 않습니다. URL이 슬래시 없이 시작하면 현재 페이지에 상대적입니다. 예: src="img_girl.jpg". URL이 슬래시로 시작하면 도메인에 상대적입니다. 예: src="/images/img_girl.jpg".

  팁: 상대 URL을 사용하는 것이 거의 항상 가장 좋습니다. 도메인을 변경해도 깨지지 않습니다.

  ### target 속성

  링크를 클릭할 때 창을 어떻게 열지를 설정합니다.
  target 속성의 주요 값:
  _blank: 새 탭에서 링크 열기
  _self: 현재 창에서 링크 열기 (기본값)
  _parent: 부모 프레임에서 링크 열기 (프레임 내에서 사용할 때)
  _top: 전체 창에서 링크 열기 (프레임이 있을 때, 부모 프레임을 무시하고  전체 창에서 열기)


  ### title 속성

  해당 링크에 마우스 커서를 올릴 때 도움말 설명을 설정합니다.
  
  예시로,
  <a title="네이버" href="https://www.naver.com">naver로 가는 링크</a>
  
  여기에 있는 "naver로 가는 링크"에 커서 갖다대면 '네이버' 라고 뜨게 만들 수 있죠.
  
  글자는 

  
## <img> 태그

    <img src="w3schools.jpg" alt="W3Schools.com" width="104" height="142">
  여기서 img src에서 경로를 불러올 수도 있고, 아니면 로컬 내부의 파일을 불러올 수도 있습니다.
  width와 height으로 이미지 개체의 크기를 조절할 수 있고, %나 수치로 설정 가능합니다.

    <a href="https://www.w3schools.com"><img src="w3schoos.jpg"></a>
  a href 안에 img src를 넣어서 이미지를 하이퍼링크화해서 이미지를 누르면 이동하도록 설정할 수도 있습니다.

  
    <img src="img_girl.jpg" alt="Girl with a jacket">  
  
  ### <alt> 태그
  <img> 어떤 이유로 이미지를 표시할 수 없는 경우에, 이미지의 대체 텍스트를 지정합니다.
  이는 느린 연결, 속성 오류 src또는 사용자가 화면 판독기를 사용하는 경우 때문일 수 있습니다.
  당연히 없는 이미지 쓰면 텍스트만 나오고 이미지는 나오지 않습니다.

## <style> 속성
  
  속성 style은 색상, 글꼴, 크기 등의 스타일을 요소에 추가하는 데 사용됩니다.
      
    <tagname style="property:value;"> <- 이 양식을 따르면 됩니다.
    예를 들면,
    <p style="color:red;">This is a red paragraph.</p>
    
  이건 단락 내부의 텍스트 색을 red로 설정한 것입니다. color 외에도 여러가지가 있습니다.
  
### <title> 속성 (title - 실제론 추가정보 속성)

  앞서 설명했지만, 아래 코드와 같이 원하는 속성 옆에 title= 을 넣으면 요소에 대한 추가 정보를 정의합니다.
  해당 텍스트에 마우스 커서를 올리면 그 내용이 title로써 뜬다는 말이죠.
  
  해보면 압니다. 복붙하시거나 따라 적어보세요. 원하시는 HTML에 스타일을 붙여서 바꾸시면 더욱 좋습니다.
  
    <h2 title="I'm a header">The title Attribute</h2>
    <p title="I'm a header">This is just one of the paragraph.</p>
    <a href="https://www.w3schools.com" title="aw3schools로 가는 하이퍼링크">w3schools로 가는 하이퍼링크</a>

### 내부 CSS(internal CSS)

  내부 CSS는 단일 HTML 페이지의 스타일을 정의하는 데 사용됩니다.

  내부 CSS는 HTML 페이지의 섹션에 정의되어 있습니다. 요소 내에서.<head><style>

  다음 예제에서는 모든 요소의 텍스트 색상을 설정합니다 (해당 페이지에서) 파란색으로, 모든 요소의 텍스트 색상을 빨강. 또한 페이지는 "powderblue" 배경으로 표시됩니다. 색:
   
    <!DOCTYPE html>
    <html>
    <head>
    <style> <!-- 스타일 설정 -->
    body {background-color: powderblue;} //바디 색깔 
    h1 {color: blue;} // 
    p {color: red;}
    </style>
    </head>
    <body>

    <h1>This is a heading</h1>
    <p>This is a paragraph.</p>

    </body>
    </html>

### 외부 CSS(external CSS)

  외부 스타일시트는 많은 HTML 페이지의 스타일을 정의하는 데 사용됩니다.
  외부 스타일 시트를 사용하려면 각 HTML 페이지의 섹션에 링크를 추가합니다.

    <!DOCTYPE html>
    <html>
    <head>
      <link rel="stylesheet" href="styles.css">
    </head>
    <body>

      <h1>This is a heading</h1>
      <p>This is a paragraph.</p>

    </body>
    </html>
  외부 스타일시트는 모든 텍스트 편집기에서 작성할 수 있습니다.
  파일에는 어떤 것도 포함되어서는 안 됩니다. HTML 코드이며 .css 확장자로 저장해야 합니다.

