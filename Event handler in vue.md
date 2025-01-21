# Event handler(이벤트 핸들러)

vue에서는 `v-on`의 축약형인 `@`을 주로 사용하여 DOM 이벤트를 수신하고 이벤트가 트리거될 때 JavaScript를 실행함.

## Inline handlers 와 Method handlers

Inline handlers = 이벤트가 트리거될 때 실행되는 인라인 JavaScript (네이티브 `onclick` 속성과 유사).<br>
Method handlers = 컴포넌트에 정의된 메서드를 가리키는 속성 이름 또는 경로

### Inline handlers
Inline handlers는 다음과 같은 간단한 경우에 사용됨.

```html
<button @click="count++">이 버튼을 누르면 카운트가 올라가지...</button>
<p>Count is: {{ count }}</p> // 물론 카운트 해야 되는게 여러개라면 이렇게 설정하면 안 됨(하나 누르면 다 올라가기 때문). 
```
```js
const count = ref(0)
```

### Method handlers
Method handler는 `v-on` 호출하려는 컴포넌트 메서드의 이름이나 경로도 허용할 수 있음.
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

Method handler는 자동으로 해당 이벤트를 트리거하는 네이티브 DOM 이벤트 객체를 받음.<br>
위의 예제에서는 `event.target`을 통해 이벤트를 발송하는 요소에 접근할 수 있음.

템플릿 컴파일러는 v-on 값 문자열이 유효한 자바스크립트 식별자 또는 속성 접근 경로인지 확인함으로써 메서드 핸들러를 감지함.<br>
예를 들어, `foo`, `foo.bar`, `foo['bar']`는 메서드 핸들러로 처리되며, `foo()`와 `count++`는 인라인 핸들러로 처리됨.<br>
여기서 `foo`와 `bar`은 그냥 변수명, 함수명 예시임. 실제 상황에선 적합한 변수명과 함수명이 정의될 것.

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

Accessing Event Argument in Inline Handlers

인라인 핸들러에서 때때로 원본 DOM 이벤트에 접근할 필요가 있습니다. 특별한 $event 변수를 사용하거나 인라인 화살표 함수를 사용하여 메서드로 이벤트를 전달할 수 있습니다.

```html
template
<!-- $event 특별 변수 사용 -->
<button @click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

<!-- 인라인 화살표 함수 사용 -->
<button @click="(event) => warn('아직 양식을 제출할 수 없습니다.', event)">
  Submit
</button>
```
```js
function warn(message, event) {
  // 이제 네이티브 이벤트에 접근할 수 있습니다
  if (event) {
    event.preventDefault() // 이벤트의 기본 행동을 막습니다.
  }
  alert(message)
}
```
