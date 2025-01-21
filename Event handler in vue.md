# Event handler(이벤트 핸들러)

vue에서는 `v-on`의 축약형인 `@`을 주로 사용하여 DOM 이벤트를 수신하고 이벤트가 트리거될 때 JavaScript를 실행합니다.

## Inline handlers 와 Method handlers

Inline handlers = 이벤트가 트리거될 때 실행되는 인라인 JavaScript (네이티브 `onclick` 속성과 유사).<br>
Method handlers = 컴포넌트에 정의된 메서드를 가리키는 속성 이름 또는 경로

### Inline handlers
Inline handlers는 다음과 같은 간단한 경우에 사용됩니다.

```html
<button @click="count++">이 버튼을 누르면 카운트가 올라가지...</button>
<p>Count is: {{ count }}</p> // 물론 카운트 해야 되는게 여러개라면 이렇게 설정하면 안 됩니다.(하나 누르면 다 올라가기 때문). 
```
```js
const count = ref(0)
```

### Method handlers
Method handler는 `v-on` 호출하려는 컴포넌트 메서드의 이름이나 경로도 허용할 수 있습니다.
```html
<button @click="greet">인사</button>
```
```js
const name = ref('Vue.js')

function greet(event) {
  alert(`안녕, ${name.value}!`)
  // `event` is the native DOM event
  if (event) {
    alert(event.target.tagName)
  }
}
```

Method handler는 자동으로 해당 이벤트를 트리거하는 네이티브 DOM 이벤트 객체를 받습니다.<br>
위의 예제에서는 `event.target`을 통해 이벤트를 발송하는 요소에 접근할 수 있습니다.

템플릿 컴파일러는 v-on 값 문자열이 유효한 자바스크립트 식별자 또는 속성 접근 경로인지 확인함으로써 메서드 핸들러를 감지합니다.<br>
예를 들어, `foo`, `foo.bar`, `foo['bar']`는 메서드 핸들러로 처리되며, `foo()`와 `count++`는 인라인 핸들러로 처리됩니다.<br>
여기서 `foo`와 `bar`은 그냥 변수명, 함수명 예시임. 실제 상황에선 적합한 변수명과 함수명이 정의될 것입니다.

## Calling Methods in Inline Handlers​

메서드 이름을 직접 바인딩하는 대신, 인라인 핸들러에서 메서드를 호출할 수도 있습니다.<br>
이 방식을 사용하면 네이티브 이벤트 대신 메서드에 사용자 정의 인자를 전달할 수 있습니다.

```html
<button @click="say('hello')">Say hello</button>
<button @click="say('bye')">Say bye</button>
```
```js
function say(message) {
  alert(message)
}
```

## Accessing Event Argument in Inline Handlers

인라인 핸들러에서 때때로 원본 DOM 이벤트에 접근할 필요가 있습니다.<br>
특별한 $event 변수를 사용하거나 인라인 화살표 함수를 사용하여 메서드로 이벤트를 전달하여 이벤트 처리를 커스터마이즈 할 수 있습니다.

```html
template
<!-- $event 특별 변수 사용 -->
<button @click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

<!-- 인라인 화살표 함수 사용 -->
<button @click="(event) => warn('아직 양식을 제출할 수 없습니다.', event)"> <!-- 버튼 클릭 시 이 함수는 경고 메시지를 보여줍니다. -->
  Submit
</button>
```
```js
function warn(message, event) { // warn 함수가 메시지와 이벤트를 인자로 받습니다.
  // 이제 네이티브 이벤트에 접근할 수 있습니다
  if (event) {
    event.preventDefault() // 이벤트의 기본 행동을 막습니다. 제출 이벤트가 기본적으로 페이지를 새로고침하는 것을 방지합니다.
  }
  alert(message)
}
```

## Event Modifiers

vue.js에서 이벤트 처리를 할 때, DOM 이벤트의 세부 사항을 직접 다루기 보다는 데이터 로직에 집중할 수 있도록 돕기 위해 제공됩니다.<br>
이벤트 핸들러 내에서 event.preventDefault() 또는 event.stopPropagation()을 호출하는 것은 매우 흔한 요구 사항입니다.<br>
메서드 내에서 이를 쉽게 수행할 수 있지만, 메서드가 DOM 이벤트의 세부 사항을 다루기보다는 순수하게 데이터 로직에 관련된 것이라면 더욱 좋을 것입니다.<br>
이 문제를 해결하기 위해 Vue는 v-on에 대한 이벤트 수정자(event modifiers)를 제공합니다.<br>
수정자는 점(.)으로 표시되는 지시어 접미사입니다.

```
.stop =  이벤트 전파를 중단합니다.
.prevent = 이벤트의 기본 동작을 방지합니다.(ex: 우클릭시 사이트에서 기본적으로 제공하는 이벤트가 )
.self = 이벤트가 해당 요소에서 직접 발생했을 때만 핸들러를 트리거합니다. (즉, 자식 요소에서 발생한 이벤트는 무시)
.capture = 이벤트 캡쳐 단계에서 이벤트를 처리합니다.
.once = 이벤트를 한 번만 처리하고 이후에는 무시합니다.
.passive = event.preventDefault()를 호출하지 않겠다는 것을 브라우저에 알려, 성능을 향상시킬 수 있습니다. (주로 스크롤 이벤트에서 사용)
```

```html
<!-- 클릭 이벤트의 전파를 멈춥니다 -->
<a @click.stop="doThis"></a>

<!-- 제출 이벤트가 페이지를 새로고침하지 않도록 합니다 -->
<form @submit.prevent="onSubmit"></form>

<!-- 수정자를 연결하여 사용할 수 있습니다 -->
<a @click.stop.prevent="doThat"></a>

<!-- 단순히 수정자만 사용합니다 -->
<form @submit.prevent></form>

<!-- 이벤트가 해당 요소 자체에서 발생했을 때만 핸들러를 트리거합니다 -->
<div @click.self="doThat">...</div>
```
