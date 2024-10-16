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


# 반응성 기초

## Declaring Reactive State

### ref()

컴포지션 API에서 반응 상태를 선언하는 권장 방법은 `ref()` 함수를 사용하는 것임:

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()`는 인자를 받아서 `.value` 속성을 가진 ref 객체로 반환함. 참고로 ref는 .value 안쓰면 값이 안나옴.

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

템플릿에서 refs에 접근하려면 컴포넌트의 `setup()` 함수에서 선언하고 반환해야 함:

```js
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    // ref를 템플릿에 노출함.
    return {
      count
    }
  }
}
```

```html
<template>
  <div>{{ count }}</div>
</template>
```

템플릿에서 ref를 사용할 때는 `.value`를 추가할 필요가 없음. 편의상 refs는 템플릿 내에서 자동으로 언랩됨(몇 가지 예외가 있음).

이벤트 핸들러에서 ref를 직접 수정할 수도 있음:

```html
<template>
  <button @click="count++">
    {{ count }}
  </button>
</template>
```

더 복잡한 로직의 경우 refs를 수정하는 함수를 선언하고, 상태와 함께 메서드로 노출할 수 있음:

```js
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // js에서 .value가 필요함.
      count.value++
    }

    // 함수도 노출하는 것을 잊으면 안되요 ^^.
    return {
      count,
      increment
    }
  }
}
```

노출된 메서드는 이벤트 핸들러로 사용할 수 있음:

```html
<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

`<script setup>`을 사용하면 수동으로 상태와 메서드를 노출하는 과정을 간소화할 수 있음:

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

최상위 임포트, 변수 및 `<script setup>`에 선언된 함수는 같은 컴포넌트의 템플릿에서 자동으로 사용할 수 있음. 템플릿은 같은 범위에 선언된 js 함수처럼 생각할 수 있음. 따라서 자연스럽게 모든 선언된 것에 접근할 수 있음.

## Why Ref?

`ref`가 단순 변수 대신 필요한 이유가 궁금할 수 있음. 이를 설명하기 위해 Vue의 반응성 시스템이 어떻게 작동하는지 간단히 이야기하겠음.

ref를 템플릿에서 사용하고 나중에 ref의 값을 변경하면 Vue는 자동으로 변경 사항을 감지하고 DOM을 업데이트함. 이는 의존성 추적 기반의 반응성 시스템 덕분임. 컴포넌트가 처음 렌더링될 때, Vue는 렌더링 중 사용된 모든 ref를 추적함. 이후 ref가 변형되면 이를 추적하는 컴포넌트가 다시 렌더링됨.

표준 js에서는 단순 변수의 접근이나 변형을 감지할 방법이 없음. 그러나, 객체의 속성의 getter & setter 메서드를 사용하여 접근 및 변형을 가로챌 수 있음.

`.value` 속성은 Vue가 ref가 접근되거나 변형될 때 이를 감지할 기회를 제공함. 내부적으로 Vue는 getter에서 추적을 수행하고, setter에서 triggering(반응성 과정의 일부로, 상태 변화가 발생했을 때 UI 업데이트를 유도하는 특정 이벤트를 지칭)을 수행함. 개념적으로 ref를 다음과 같이 생각할 수 있음:

```js
// 의사 코드, 실제 구현이 아님
const myRef = {
  _value: 0,
  get value() {
    track()
    return this._value
  },
  set value(newValue) {
    this._value = newValue
    trigger()
  }
}
```

refs의 또 다른 장점은 일반 변수와 달리 refs를 함수에 전달해도 최신 값과 반응성 연결을 유지한다는 것임. 이는 복잡한 로직을 재사용 가능한 코드로 리팩토링할 때 특히 유용함.

반응성 시스템에 대한 자세한 내용은 반응성 심층 섹션에서 논의함.

## Deep Reactivity

Refs는 원시 값 외에도 깊은 중첩 객체, 배열 또는 Map과 같은 Javascript 내장 데이터 구조를 포함할 수 있음. ref는 값을 깊이 반응적으로 만듭니다. 즉, 중첩 객체나 배열을 변형해도 변경 사항이 감지됨:

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // 이러한 작업은 예상대로 작동함.
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

비원시 값은 `reactive()`를 통해 반응형 프록시로 변환됨.

성능을 최적화하거나 내부 상태가 외부 라이브러리로 관리되는 경우에는 얕은 refs를 사용하여 깊은 반응성에서 제외할 수도 있음.

## DOM Update Timing

반응 상태를 변형할 때 DOM은 자동으로 업데이트됨. 하지만 DOM 업데이트는 동기적으로 적용되지 않음. 대신, Vue는 "다음 틱"의 업데이트 주기까지 이를 버퍼링하여 각 컴포넌트가 상태 변경 횟수와 상관없이 한 번만 업데이트되도록 함.

상태 변경 후 DOM 업데이트가 완료되기를 기다리려면 `nextTick()` 글로벌 API를 사용할 수 있음:

```js
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // 이제 DOM이 업데이트 됨.
}
```

## reactive()

반응 상태를 선언하는 또 다른 방법은 `reactive()` API임. `ref()`는 내부 값을 특별한 객체로 감싸는 것과 달리, `reactive()`는 객체 자체를 반응형으로 만듭니다:

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

템플릿에서의 사용:

```html
<template>
  <button @click="state.count++">
    {{ state.count }}
  </button>
</template>
```

반응형 객체는 js 프록시이며 일반 객체처럼 동작함. 차이점은 Vue가 반응성 추적 및 트리거링을 위해 반응형 객체의 모든 속성에 대한 접근과 변형을 가로챌 수 있다는 것임.

`reactive()`는 객체를 깊게 변환함. 중첩 객체도 접근할 때 `reactive()`로 감싸집니다. 또한 ref의 값이 객체일 때 내부에서 `reactive()`가 호출됨. 얕은 반응성에서 제외할 수 있는 `shallowReactive()` API도 있음.

## Reactive Proxy vs. Raw

`reactive()`에서 반환된 값은 원본 객체의 프록시이며 원본 객체와 같지 않음:

```js
const raw = {}
const proxy = reactive(raw)

// 프록시는 원본과 같지 않음.
console.log(proxy === raw) // false
```

오직 프록시만 반응적이며 원본 객체를 변형해도 업데이트가 트리거되지 않음. 따라서 Vue의 반응성 시스템을 사용할 때는 상태의 프록시 버전만 사용해야 함.

동일한 객체에서 `reactive()`를 호출하면 항상 동일한 프록시를 반환하며, 기존 프록시에 대해 `reactive()`를 호출하면 그 프록시를 반환함:

```js
// 동일한 객체에서 호출하면 동일한 프록시를 반환함.
console.log(reactive(raw) === proxy) // true

// 프록시에 대해 호출하면 자신을 반환함.
console.log(reactive(proxy) === proxy) // true
```

이 규칙은 중첩 객체에도 적용됨. 깊은 반응성 덕분에 반응형 객체의 중첩 객체도 프록시임:

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### Limitations of reactive()

`reactive()` API에는 몇 가지 한계가 있음:

- **제한된 값 타입**: 객체 타입(객체, 배열, Map 및 Set과 같은 컬렉션 타입)에 대해서만 작동함. 원시 타입(문자열, 숫자 또는 불리언)을 보유할 수 없음.
- **전체 객체 교체**: 전체 객체를 교체할 수는 있지만, 이 경우 내부 속성에 대한 반응성 추적을 잃게 됨. 이 경우 `ref()`를 사용하는 것이 좋음.

```js
const state = reactive({
  items: [{ id: 1, name: 'apple' }]
})

state.items = [{ id: 2, name: 'banana' }] // 반응적이지 않음!
```

## Reactive Objects and Arrays

`reactive()`를 사용하여 배열을 반응형으로 만들 수 있음:

```js
import { reactive } from 'vue'

const state = reactive({
  items: ['apple', 'banana', 'orange']
})

state.items.push('grape') // 반응적으로 업데이트
```

배열의 메서드도 자동으로 반응형임. 예를 들어, `push()`, `pop()`, `shift()`, `unshift()`, `splice()` 등과 같은 배열 메서드는 모두 반응성을 유지함.

## Reactive State Summary

- `ref()`와 `reactive()`는 Vue의 Composition API에서 반응 상태를 정의하는 두 가지 주요 방법임.
- `ref()`는 원시 값 또는 객체를 감싸는 데 사용되며, `.value` 속성을 통해 접근함.
- `reactive()`는 객체 및 배열을 직접 변형하여 깊은 반응성을 제공함.
- `reactive()`에서 반환된 값은 원본 객체의 프록시이며, 이 프록시만 반응적임.
- DOM 업데이트는 비동기적으로 발생하며, 상태 변형 후 즉시 업데이트를 확인하려면 `nextTick()`을 사용할 수 있음.

