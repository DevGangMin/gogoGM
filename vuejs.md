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

Refs는 원시 값 외에도 deep한 중첩 객체, 배열 또는 Map과 같은 Javascript 내장 데이터 구조를 포함할 수 있음. ref는 값을 깊이 반응적으로 만듭니다. 즉, 중첩 객체나 배열을 변형해도 변경 사항이 감지됨:

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

성능을 최적화하거나 내부 상태가 외부 라이브러리로 관리되는 경우에는 얕은 refs를 사용하여 deep한 반응성에서 제외할 수도 있음.

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

이 규칙은 중첩 객체에도 적용됨. deep한 반응성 덕분에 반응형 객체의 중첩 객체도 프록시임:

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
- `reactive()`는 객체 및 배열을 직접 변형하여 deep한 반응성을 제공함.
- `reactive()`에서 반환된 값은 원본 객체의 프록시이며, 이 프록시만 반응적임.
- DOM 업데이트는 비동기적으로 발생하며, 상태 변형 후 즉시 업데이트를 확인하려면 `nextTick()`을 사용할 수 있음.

## Additional Ref Unwrapping Details​

반응형 객체의 속성으로서 ref는 반응형 객체의 속성으로 접근하거나 변경될 때 자동으로 unwrapping(unwrapping)됨.

즉, 일반 속성과 같이 작동함.

```js
const count = ref(0)
const state = reactive({
  count
})
console.log(state.count) // 0
state.count = 1
console.log(count.value) // 1
```

기존 ref와 연결된 속성에 새 ref를 할당하면, 기존 ref는 대체됨.

```js
const otherCount = ref(2)
state.count = otherCount
console.log(state.count) // 2
// 기존 ref는 이제 state.count와 연결이 끊김
console.log(count.value) // 1
```

ref unwrapping은 deep한 반응형 객체 내에서만 발생하며, 얕은 반응형 객체의 속성으로 접근할 때는 적용되지 않음.

### 배열 및 컬렉션에서의 주의사항

반응형 객체와는 달리, ref가 반응형 배열의 요소나 기본 컬렉션 타입(Map 등)으로 접근될 때는 unwrapping이 수행되지 않음.

```js
const books = reactive([ref('Vue 3 Guide')])
// .value 필요
console.log(books[0].value)
const map = reactive(new Map([['count', ref(0)]]))
// .value 필요
console.log(map.get('count').value)
```

### 템플릿에서 unwrapping 시 주의사항

템플릿에서 ref의 unwrapping은 템플릿 렌더링 컨텍스트의 최상위 속성일 때만 적용됨.

```js
const count = ref(0)
const object = { id: ref(1) }

```

따라서 아래 표현식은 예상대로 작동함:

```html
{{ count + 1 }}
```

반면 아래 표현식은 작동하지 않음:

```html
{{ object.id + 1 }}
```

이 경우 렌더링 결과는 [object Object]1이 되며, object.id는 unwrapping되지 않고 여전히 ref 객체로 남아 있음. 이를 수정하려면 id를 최상위 속성으로 구조 분해 할당할 수 있음.

```js
const { id } = object
```

```html
{{ id + 1 }}
```

이제 렌더링 결과는 2가 됨.

또 하나 유의할 점은, ref는 텍스트 보간의 최종 평가값일 경우 unwrapping됨(즉, {{ }} 태그). 따라서 아래는 1을 렌더링함:

```html
{{ object.id }}
```
이는 텍스트 보간의 편리한 기능이며, {{ object.id.value }}와 동등함.

# Computed Properties

## 기본 예시
템플릿 내 표현식은 매우 편리하지만, 간단한 작업에 적합함. 템플릿에 너무 많은 논리를 포함하면 복잡해지고 유지보수가 어려워질 수 있음. 예를 들어, 중첩 배열을 가진 객체가 있을 때:

```javascript
const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})
```

작가가 이미 책을 가지고 있는지에 따라 다른 메시지를 표시하려고 할 때:

```html
<p>Has published books:</p>
<span>{{ author.books.length > 0 ? 'Yes' : 'No' }}</span>
```

이 시점에서 템플릿이 조금 복잡해지고 있음. `author.books`에 따라 계산을 수행하는 것을 깨닫기 위해 약간의 시간을 할애해야 함.

더 중요한 것은 이 계산을 템플릿에 여러 번 포함해야 한다면, 중복되는 것을 원치 않을 것임.

그래서 복잡한 로직이 포함된 reactive 데이터를 사용할 때는 계산된 속성을 사용하는 것이 좋음. 다음은 동일한 예제를 리팩토링한 것:

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// 계산된 ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

여기서는 `publishedBooksMessage`라는 계산된 속성을 선언함. `computed()` 함수는 getter 함수를 인자로 받아야 하며, 반환된 값은 계산된 ref임. 일반 ref와 유사하게 `publishedBooksMessage.value`로 계산된 결과에 접근할 수 있음. 계산된 ref는 템플릿에서도 자동으로 언래핑되므로, 템플릿 표현식에서 `.value` 없이 참조할 수 있음.

계산된 속성은 반응형 종속성을 자동으로 추적함. Vue는 `publishedBooksMessage`의 계산이 `author.books`에 의존한다는 것을 인식하므로, `author.books`가 변경될 때 `publishedBooksMessage`에 의존하는 바인딩을 업데이트함.

## Computed Caching vs. Methods
우리가 같은 결과를 얻기 위해 메소드를 호출할 수도 있다는 것을 알았을 것임:

```html
<p>{{ calculateBooksMessage() }}</p>
```

```javascript
// 컴포넌트 내
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Yes' : 'No'
}
```

계산된 속성 대신 메소드를 정의할 수 있음. 결과적으로 두 접근 방식은 동일함. 그러나 차이점은 계산된 속성이 반응형 종속성에 기반하여 캐시된다는 것임. 계산된 속성은 그 반응형 종속성이 변경될 때만 다시 평가됨. 즉, `author.books`가 변경되지 않는 한, `publishedBooksMessage`에 여러 번 접근해도 이전에 계산된 결과를 즉시 반환함.

반면 메소드 호출은 렌더링이 발생할 때마다 항상 함수를 실행함.

캐싱이 왜 필요할까요? 만약 우리가 방대한 배열을 반복하고 많은 계산을 요구하는 비싼 계산된 속성을 가지고 있다면, 그 속성에 의존하는 다른 계산된 속성도 있을 수 있음. 캐싱이 없으면 불필요하게 `list`의 getter를 여러 번 실행하게 됨! 캐싱을 원하지 않는 경우에는 대신 메소드 호출을 사용해야함.

## Writable Computed
계산된 속성은 기본적으로 getter 전용임. 계산된 속성에 새 값을 할당하려고 하면 런타임 경고를 받게 됨. 드물게 "쓰기 가능한" 계산된 속성이 필요할 경우, getter와 setter를 모두 제공하여 생성할 수 있음:

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // 구조 분해 할당 문법을 사용함.
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

이제 `fullName.value = 'John Doe'`를 실행하면 setter가 호출되고 `firstName`과 `lastName`이 accordingly 업데이트됨.

## Getting the previous value
**3.4+ 버전에서만 지원**
필요할 경우, 계산된 속성이 반환하는 이전 값을 getter의 첫 번째 인수를 통해 가져올 수 있음:

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(2)

// 이 계산된 속성은 count가 3 이하일 때 count 값을 반환함.
// count가 4 이상일 때는 조건을 충족했던 마지막 값이 반환됨.
// 그 후 count가 3 이하일 때까지 계속 유지됨.
const alwaysSmall = computed((previous) => {
  if (count.value <= 3) {
    return count.value;
  }

  return previous;
})
</script>
```

쓰기 가능한 계산된 속성을 사용하는 경우:

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(2)

const alwaysSmall = computed({
  get(previous) {
    if (count.value <= 3) {
      return count.value;
    }

    return previous;
  },
  set(newValue) {
    count.value = newValue * 2;
  }
})
</script>
```

## Best Practices
- **Getter는 부작용이 없어야 함.**  
  계산된 getter 함수는 순수 계산만 수행해야 하며 부작용이 없어야 함. 예를 들면, 계산된 getter 내에서 다른 상태를 변경하거나, 비동기 요청을 하거나, DOM을 변경해서는 안 됨. 계산된 속성을 다른 값 기반으로 값을 도출하는 방법을 선언적으로 설명하는 것으로 생각해야함. 그것의 유일한 책임은 해당 값을 계산하고 반환하는 것임.

- **계산된 값을 변경하면 안됨.**  
  계산된 속성에서 반환된 값은 파생 상태임. 이를 일종의 임시 스냅샷으로 생각해야함. 원본 상태가 변경될 때마다 새로운 스냅샷이 생성됨. 스냅샷을 변경하는 것은 의미가 없으므로, 계산된 반환 값은 읽기 전용으로 취급하고 절대 변경하지 않아야 함. 대신, 새로운 계산을 유도하기 위해 의존하는 원본 상태를 업데이트해야함.
