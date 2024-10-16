## application instance

모든 Vue 애플리케이션은 createApp 함수로 새로운 application instance를 생성하여 시작함.
```js
import { createApp } from 'vue'
const app = createApp({
  /* root component 옵션 */
})
```
## root component

createApp에 전달하는 객체는 실제로 component임.

모든 애플리케이션은 다른 component를 자식으로 포함할 수 있는 "root component"가 필요함.

단일 파일 component를 사용하는 경우, 일반적으로 다른 파일에서 root component를 가져옴.

```js
import { createApp } from 'vue' // 단일 파일 component -> root component App 가져오기
import App from './App.vue'
const app = createApp(App)
```

대부분의 실제 애플리케이션은 중첩 가능하고 재사용 가능한 component의 트리 구조로 조직됨.

예를 들어, Todo 애플리케이션의 component 트리는 다음과 같을 수 있음:
```cmd
App (root component)
├─ TodoList
│  └─ TodoItem
│     ├─ TodoDeleteButton
│     └─ TodoEditButton
└─ TodoFooter
   ├─ TodoClearButton
   └─ TodoStatistics    ## 앱 마운팅
```

application instance는 .mount() method가 호출될 때까지 아무것도 rendering하지 않음.

이 method는 "container" 인수를 기대하며, 이는 실제 DOM 요소일 수도 있고 선택자 문자열일 수도 있음:
```html
<div id="app"></div>
app.mount('#app')
```
앱의 root component 내용은 container 요소 내부에 rendering됨.

container 요소 자체는 앱의 일부로 간주되지 않음.

.mount() method는 모든 앱 구성 및 자산 등록이 완료된 후에 호출되어야 함.

또한 이 method의 반환 값은 application instance가 아닌 root component 인스턴스임.

## In-DOM root component templete

root component의 templete은 일반적으로 component 자체의 일부이지만, mount container 내부에 직접 작성하여 templete을 별도로 제공할 수도 있음:
```html
<div id="app">
  <button @click="count++">{{ count }}</button>
</div>
```
```js
import { createApp } from 'vue'
const app = createApp({
  data() {
    return {
      count: 0
    }
  }
})
app.mount('#app')
```
root component에 templete 옵션이 없는 경우, Vue는 자동으로 container의 innerHTML을 templete으로 사용함.

인-DOM templete은 빌드 단계 없이 Vue를 사용하는 애플리케이션에서 자주 사용됨. server 측 프레임워크와 함께 사용할 때, 루트 templete은 server에 의해 동적으로 생성될 수 있음.

## 앱 구성

application instance는 .config 객체를 노출하여 몇 가지 앱 수준 옵션을 구성할 수 있게 해줌.

예를 들어, 모든 하위 component의 오류를 캡처하는 앱 수준 오류 처리기를 정의할 수 있음:
```js
app.config.errorHandler = (err) => {
  /* 오류 처리 */
}
```
application instance는 또한 앱 범위 자산을 등록하기 위한 몇 가지 method를 제공함. 예를 들어, component를 등록하는 방법임:
app.component('TodoDeleteButton', TodoDeleteButton)

이렇게 하면 TodoDeleteButton을 앱 어디에서나 사용 가능함.

또한 application instance API의 전체 목록을 API 참조에서 찾아볼 수 있음.

앱을 mount하기 전에 모든 앱 구성을 적용해야 함.

## multiple application instance (다중 애플리케이션 인스턴스)

같은 페이지에 단일 application instance에 제한되지 않음.

createApp API를 사용하면 동일한 페이지에서 여러 Vue 애플리케이션이 공존할 수 있으며, 각 애플리케이션은 구성 및 전역 자산에 대한 고유한 범위를 가짐:
```js
const app1 = createApp({
  /* ... */
})
app1.mount('#container-1')
const app2 = createApp({
  /* ... */
})
app2.mount('#container-2')
``` 

server rendering(서버로부터 HTML 파일을 받아 브라우저에 뿌려주는 과정) HTML을 개선하기 위해 Vue를 사용하는 경우, 대형 페이지의 특정 부분만 제어하기 위해 전체 페이지에 단일 Vue application instance를 mount하지 않는 것이 좋음.

여러 개의 작은 application instance를 생성하고 그들이 책임지는 요소에 mount하는게 좋음.



## Vue 템플릿 구문

Vue는 HTML 기반의 템플릿 구문을 사용하여 렌더링된 DOM을 기본 component instance의 데이터에 선언적으로 바인딩할 수 있게 함. 모든 Vue 템플릿은 구문적으로 유효한 HTML로, 규격을 준수하는 브라우저와 HTML 파서가 파싱할 수 있음.

Vue는 템플릿을 매우 최적화된 JavaScript 코드로 컴파일함. 반응성 시스템과 결합되어, Vue는 앱 상태가 변경될 때 최소한의 component를 재렌더링하고 최소한의 DOM 조작을 적용할 수 있음.

가상 DOM 개념에 익숙하고 JavaScript의 원초적인 힘을 선호하는 경우, 템플릿 대신 렌더 함수로 직접 작성할 수도 있으며, 선택적으로 JSX 지원도 가능함. 그러나 이러한 방법은 템플릿만큼의 컴파일 타임 최적화를 누릴 수 없다는 점에 유의해야 함.

## 텍스트 보간

가장 기본적인 데이터 바인딩 형태는 "머스터시" 구문(중괄호 두 개)을 사용하는 텍스트 보간임:

```html
<span>Message: {{ msg }}</span>
```

머스터시 태그는 해당 component instance의 `msg` 속성 값으로 대체됨. 또한 `msg` 속성이 변경될 때마다 자동으로 업데이트됨.

## Raw HTML(원시 HTML)

이중 중괄호는 데이터를 일반 텍스트로 해석하며, HTML로 출력하려면 `v-html` directive를 사용해야 함:

```html
<p>Using text interpolation: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

위의 예제에서 `v-html`은 특수 속성으로, Vue에서 제공하는 특별한 속성으로 접두사 `v-`가 붙음. 이 속성은 렌더링된 DOM에 특별한 반응성을 적용함. 여기서 우리는 "현재 활성 instance의 `rawHtml` 속성과 함께 이 요소의 내부 HTML을 최신 상태로 유지하라"고 말하고 있는 것임.

`span`의 내용은 `rawHtml` 속성의 값으로 대체되며, 이는 일반 HTML로 해석되며 데이터 바인딩은 무시됨. `v-html`을 사용하여 템플릿 부분을 구성할 수 없다는 점에 유의하세요. Vue는 문자열 기반 템플릿 엔진이 아니며, 대신 UI 재사용 및 구성을 위한 기본 단위로 component를 선호함.
```
! 보안 경고

웹사이트에서 임의의 HTML을 동적으로 렌더링하는 것은 XSS 취약점을 초래할 수 있어 매우 위험할 수 있음. `v-html`은 신뢰할 수 있는 콘텐츠에만 사용하고, 사용자 제공 콘텐츠에는 사용하지 마세요.
```
## Attribute Bindings(속성 바인딩)

머스터시는 HTML 속성 내에서 사용할 수 없음. 대신 `v-bind` directive를 사용해야 함:

```html
<div v-bind:id="dynamicId"></div>
```

`v-bind` directive는 Vue에게 요소의 `id` 속성을 `dynamicId` 속성과 동기화하라고 지시함. 바인딩된 값이 `null` 또는 `undefined`일 경우, 해당 속성은 렌더링된 요소에서 제거됨.

## Shorthand(축약 구문)

`v-bind`는 매우 자주 사용되기 때문에 전용 축약 구문이 있음:

```html
<div :id="dynamicId"></div>
```

`:id`와 같이 시작하는 속성은 일반 HTML과는 조금 다른 모습이지만, 속성 이름으로 유효한 문자이며 모든 Vue 지원 브라우저는 이를 올바르게 파싱할 수 있음.
또한 최종 렌더링된 마크업에는 나타나지 않음.
축약 구문은 선택적이지만, 이후 사용법을 배울 때 유용함을 느낄 것임.

가이드의 나머지 부분에서는 Vue 개발자들이 가장 일반적으로 사용하는 축약 구문을 코드 예제에서 사용할 것임.

## 동일한 이름의 축약 구문

3.4+에서만 지원됨

속성 이름이 바인딩되는 JavaScript 값과 동일한 경우, 속성 값을 생략하여 구문을 더욱 간단하게 만들 수 있음:

```html
<!-- :id="id"와 동일 -->
<div :id></div>

<!-- 이것도 작동함 -->
<div v-bind:id></div>
```

이는 JavaScript에서 객체를 선언할 때 속성 축약 구문과 유사함. 이 기능은 Vue 3.4 이상에서만 사용할 수 있음.

## Boolean (불리언)

불리언 속성은 요소에 존재함으로써 true/false 값을 나타낼 수 있는 속성임. 예를 들어 `disabled`는 가장 일반적으로 사용되는 불리언 속성 중 하나임.

이 경우 `v-bind`는 조금 다르게 작동함:

```html
<button :disabled="isButtonDisabled">Button</button>
```

`isButtonDisabled`가 truthy한 값을 가질 경우 `disabled` 속성이 포함됨. 값이 빈 문자열인 경우에도 속성이 포함됨. 다른 falsy 값의 경우 속성은 생략됨.

## Dynamically Binding Multiple Attributes(여러 속성 동적 바인딩)

다음과 같이 여러 속성을 나타내는 JavaScript 객체가 있을 경우:

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}
```

속성 없이 `v-bind`를 사용하여 단일 요소에 바인딩할 수 있음:

```html
<div v-bind="objectOfAttrs"></div>
```

## JavaScript 표현식 사용하기

Vue는 모든 데이터 바인딩 내에서 JavaScript 표현식의 전체 기능을 지원함:

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

이러한 표현식은 현재 component instance의 데이터 범위 내에서 JavaScript로 평가됨.

Vue 템플릿 내에서 JavaScript 표현식은 다음 위치에서 사용할 수 있음:

- 텍스트 보간(머스터시) 내
- 모든 Vue directive의 속성 값 내

## Expressions(표현식)만 사용하기

각 바인딩에는 하나의 표현식만 포함될 수 있음. 표현식은 값으로 평가될 수 있는 코드 조각임. 간단한 확인 방법은 `return` 뒤에 사용할 수 있는지 여부임.

따라서 다음은 작동하지 않음:

```html
<!-- 이건 표현식이 아닌 문장임 -->
{{ var a = 1 }}

<!-- 흐름 제어도 작동하지 않음. 삼항 표현식을 사용해야 함. -->
{{ if (ok) { return message } }}
```

## 함수 호출

바인딩 표현식 내에서 component에서 노출된 메소드를 호출하는 것도 가능함:

```html
<time :title="toTitleDate(date)" :datetime="date">
  {{ formatDate(date) }}
</time>
```

> **팁:** 바인딩 표현식 내에서 호출된 함수는 component가 업데이트될 때마다 호출되므로 데이터 변경이나 비동기 작업을 트리거하는 부작용이 있어서는 안 됨.

## Restricted 글로벌 접근

템플릿 표현식은 sandboxed 되어 있으며 제한된 글로벌 목록에만 접근할 수 있음. 이 목록은 Math와 Date와 같은 일반적으로 사용되는 내장 글로벌을 노출함.

리스트에 명시적으로 포함되지 않은 글로벌(예: `window`의 사용자 연결 속성)은 템플릿 표현식에서 접근할 수 없음. 그러나 `app.config.globalProperties`에 추가하여 모든 Vue 표현식에 대한 추가 글로벌을 명시적으로 정의할 수 있음.

## directive

directive는 `v-` 접두사가 붙은 특별한 속성임.

Vue는 위에서 소개한 `v-html` 및 `v-bind`를 포함하여 여러 내장 directive를 제공함.

directive 속성 값은 단일 JavaScript 표현식이어야 하며(예외: `v-for`, `v-on` 및 `v-slot`은 나중에 각각의 섹션에서 다룰 것임). 
directive의 역할은 표현식의 값이 변경될 때 DOM에 반응적으로 업데이트를 적용하는 것임. 예를 들어 `v-if`를 살펴보겠음:

```html
<p v-if="seen">Now you see me</p>
```

여기서 `v-if` directive는 `seen` 표현식의 진리값에 따라 `<p>` 요소를 제거하거나 삽입함.

## Arguments (인수)

일부 directive는 Arguments, 인수를 가질 수 있으며, 이는 directive 이름 뒤에 콜론으로 표시됨. 예를 들어, `v-bind` directive는 HTML 속성을 반응적으로 업데이트하는 데 사용됨:

```html
<a v-bind:href="url"> ... </a>
```

`v-on` directive도 인수를 가질 수 있음:

```html
<button v-on:click="doSomething">Click me</button>

<!-- 축약 -->
<button @click="doSomething">Click me</button>
```

`v-bind`는 속성을 바인딩하기 위해 사용되며, `v-on`은 이벤트 리스너를 추가하는 데 사용됨.

## Modifiers(수식)

일부 directive는 특별한 수식을 가질 수 있음. `v-for`는 여러 항목을 반복할 때 사용되며, 인수와 함께 사용됨:

```html
<ul>
  <li v-for="item in items" :key="item.id">{{ item.text }}</li>
</ul>
```

위 코드에서 `item`은 각 항목을 나타내며, `items`는 반복하는 데이터 배열임.


![image](https://github.com/user-attachments/assets/24672905-a1e0-44a0-b76b-5244f4ccb4f0)

