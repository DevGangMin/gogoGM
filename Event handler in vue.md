# Event handler(이벤트 핸들러)

vue에서는 v-on의 축약형인 @을 주로 사용하여 DOM 이벤트를 수신하고 이벤트가 트리거될 때 JavaScript를 실행함.

## Inline handlers 와 Method handlers
<p>
Inline handlers = 이벤트가 트리거될 때 실행되는 인라인 JavaScript(네이티브 onclick속성과 유사).<br>
Method handlers = 컴포넌트에 정의된 메서드를 가리키는 속성 이름 또는 경로
</p>
### Inline handlers
Inline handlers는 다음과 같은 간단한 경우에 사용됨.

```html
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
```
```js
const count = ref(0)
```

### Method handlers
Method handler는 v-on 호출하려는 컴포넌트 메서드의 이름이나 경로도 허용할 수 있음.
```html
<button @click="greet">Greet</button>
```
```js
const name = ref('Vue.js')

function greet(event) {
  alert(`Hello ${name.value}!`)
  // `event` is the native DOM event
  if (event) {
    alert(event.target.tagName)
  }
}
```

