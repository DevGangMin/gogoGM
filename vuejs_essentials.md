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

`ref`가 단순 변수 대신 필요한 이유가 궁금할 수 있음.
이를 설명하기 위해 Vue의 반응성 시스템이 어떻게 작동하는지 간단히 이야기하겠음.

ref를 템플릿에서 사용하고 나중에 ref의 값을 변경하면 Vue는 자동으로 변경 사항을 감지하고 DOM을 업데이트함.
이는 의존성 추적 기반의 반응성 시스템 덕분임. 컴포넌트가 처음 렌더링될 때, Vue는 렌더링 중 사용된 모든 ref를 추적함.
이후 ref가 변형되면 이를 추적하는 컴포넌트가 다시 렌더링됨.

표준 js에서는 단순 변수의 접근이나 변형을 감지할 방법이 없음.
그러나, 객체의 속성의 getter & setter 메서드를 사용하여 접근 및 변형을 가로챌 수 있음.

`.value` 속성은 Vue가 ref가 접근되거나 변형될 때 이를 감지할 기회를 제공함.
내부적으로 Vue는 getter에서 추적을 수행하고, setter에서 triggering(반응성 과정의 일부로, 상태 변화가 발생했을 때 UI 업데이트를 유도하는 특정 이벤트를 지칭)을 수행함.

개념적으로 ref를 다음과 같이 생각할 수 있음:

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

refs의 또 다른 장점은 일반 변수와 달리 refs를 함수에 전달해도 최신 값과 반응성 연결을 유지한다는 것임.
이는 복잡한 로직을 재사용 가능한 코드로 리팩토링할 때 특히 유용함.

반응성 시스템에 대한 자세한 내용은 반응성 심층 섹션에서 논의함.

## Deep Reactivity

Refs는 원시 값 외에도 deep한 중첩 객체, 배열 또는 Map과 같은 Javascript 내장 데이터 구조를 포함할 수 있음.
ref는 값을 깊이 반응적으로 만듬.

즉, 중첩 객체나 배열을 변형해도 변경 사항이 감지됨:

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

반응 상태를 변형할 때 DOM은 자동으로 업데이트됨.
하지만 DOM 업데이트는 동기적으로 적용되지 않음.
대신, Vue는 "다음 틱"의 업데이트 주기까지 이를 버퍼링하여 각 컴포넌트가 상태 변경 횟수와 상관없이 한 번만 업데이트되도록 함.

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

반응 상태를 선언하는 또 다른 방법은 `reactive()` API임.
`ref()`는 내부 값을 특별한 객체로 감싸는 것과 달리, `reactive()`는 객체 자체를 반응형으로 만듬듬:

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

반응형 객체는 js 프록시이며 일반 객체처럼 동작함.
차이점은 Vue가 반응성 추적 및 트리거링을 위해 반응형 객체의 모든 속성에 대한 접근과 변형을 가로챌 수 있다는 것임.
`reactive()`는 객체를 깊게 변환함. 중첩 객체도 접근할 때 `reactive()`로 감싸짐.
또한 ref의 값이 객체일 때 내부에서 `reactive()`가 호출됨. 얕은 반응성에서 제외할 수 있는 `shallowReactive()` API도 있음.

## Reactive Proxy vs. Raw

`reactive()`에서 반환된 값은 원본 객체의 프록시이며 원본 객체와 같지 않음:

```js
const raw = {}
const proxy = reactive(raw)

// 프록시는 원본과 같지 않음.
console.log(proxy === raw) // false
```

오직 프록시만 반응적이며 원본 객체를 변형해도 업데이트가 트리거되지 않음.
따라서 Vue의 반응성 시스템을 사용할 때는 상태의 프록시 버전만 사용해야 함.

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
템플릿 내 표현식은 매우 편리하지만, 간단한 작업에 적합함. 템플릿에 너무 많은 논리를 포함하면 복잡해지고 유지보수가 어려워질 수 있음.

예를 들어, 중첩 배열을 가진 객체가 있을 때:

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

계산된 속성 대신 메소드를 정의할 수 있음. 결과적으로 두 접근 방식은 동일함. 그러나 차이점은 계산된 속성이 반응형 종속성에 기반하여 캐시된다는 것에 있음.
계산된 속성은 그 반응형 종속성이 변경될 때만 다시 평가됨.

즉, `author.books`가 변경되지 않는 한, `publishedBooksMessage`에 여러 번 접근해도 이전에 계산된 결과를 즉시 반환함.

반면 메소드 호출은 렌더링이 발생할 때마다 항상 함수를 실행함.

캐싱이 왜 필요할까? 만약 우리가 방대한 배열을 반복하고 많은 계산을 요구하는 비싼 계산된 속성을 가지고 있다면, 그 속성에 의존하는 다른 계산된 속성도 있을 수 있음. 캐싱이 없으면 불필요하게 `list`의 getter를 여러 번 실행하게 됨! 캐싱을 원하지 않는 경우에는 대신 메소드 호출을 사용해야함.

## Writable Computed

계산된 속성은 기본적으로 getter 전용임.
계산된 속성에 새 값을 할당하려고 하면 런타임 경고를 받게 됨.
드물게 "쓰기 가능한" 계산된 속성이 필요할 경우, getter와 setter를 모두 제공하여 생성할 수 있음:

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
  - 계산된 getter 함수는 순수 계산만 수행해야 하며 부작용이 없어야 함.
  - 예를 들면, 계산된 getter 내에서 다른 상태를 변경하거나, 비동기 요청을 하거나, DOM을 변경해서는 안 됨.
  - 계산된 속성을 다른 값 기반으로 값을 도출하는 방법을 선언적으로 설명하는 것으로 생각해야함.
  - getter 함수의 유일한 책임은 해당 값을 계산하고 반환하는 것임.

- **계산된 값을 변경하면 안됨.**
  - 계산된 속성에서 반환된 값은 파생 상태임.
  - 이를 일종의 임시 스냅샷으로 생각해야함.
  - 원본 상태가 변경될 때마다 새로운 스냅샷이 생성됨.
  - 스냅샷을 변경하는 것은 의미가 없으므로, 계산된 반환 값은 읽기 전용으로 취급하고 절대 변경하지 않아야 함.
  - 변경하는 대신, 새로운 계산을 유도하기 위해 의존하는 원본 상태를 업데이트해야함.

# Class and Style Bindings

데이터 바인딩의 일반적인 필요는 요소의 클래스 목록과 인라인 스타일을 조작하는 것임.
클래스와 스타일은 모두 속성이므로 `v-bind`를 사용하여 문자열 값을 동적으로 할당할 수 있음.
그러나 문자열 연결을 사용하여 이러한 값을 생성하려고 하면 번거롭고 오류가 발생하기 쉬워짐.
이 때문에 Vue는 `v-bind`가 클래스 및 스타일과 함께 사용될 때 특별한 확장을 제공함.
문자열 외에도 표현식은 객체 또는 배열로 평가될 수 있음.

## Binding HTML Classes

### Binding to Objects
`:class`(짧게 `v-bind:class`)에 객체를 전달하여 클래스를 동적으로 토글할 수 있음:

```vue
<template>
  <div :class="{ active: isActive }"></div>
</template>
```

위 구문은 `isActive` 데이터 속성의 진리값에 따라 `active` 클래스의 존재 여부가 결정됨을 의미함.
여러 클래스를 토글하려면 객체에 더 많은 필드를 추가하면 됨.
또한 `:class` 지시자는 일반 `class` 속성과 함께 공존할 수 있음.

아래과 같은 상태가 주어졌을 때:

```js
const isActive = ref(true);
const hasError = ref(false);
```

아래과 같은 템플릿이 있을 때:

```vue
<template>
  <div class="static" :class="{ active: isActive, 'text-danger': hasError }"></div>
</template>
```

렌더링 결과는 아래과 같음:

```html
<div class="static active"></div>
```

`isActive` 또는 `hasError`가 변경되면 클래스 목록이 accordingly 업데이트됨.
예를 들어, `hasError`가 `true`가 되면 클래스 목록은 `"static active text-danger"`가 됨.
바인딩된 객체는 인라인으로 작성할 필요가 없음:

```js
const classObject = reactive({
  active: true,
  'text-danger': false
});
```

```vue
<template>
  <div :class="classObject"></div>
</template>
```

이것은 아래과 같이 렌더링됨:

```html
<div class="active"></div>
```

계산된 속성을 바인딩하여 객체를 반환할 수도 있음. 이는 일반적이고 강력한 패턴임:

```js
const isActive = ref(true);
const error = ref(null);

const classObject = computed(() => ({
  active: isActive.value && !error.value,
  'text-danger': error.value && error.value.type === 'fatal'
}));
```

```vue
<template>
  <div :class="classObject"></div>
</template>
```

### Binding to Arrays
`:class`에 배열을 바인딩하여 클래스 목록을 적용할 수 있음:

```js
const activeClass = ref('active');
const errorClass = ref('text-danger');
```

```vue
<template>
  <div :class="[activeClass, errorClass]"></div>
</template>
```

이는 아래과 같이 렌더링됨:

```html
<div class="active text-danger"></div>
```

조건부로 클래스 목록에 클래스를 토글하고 싶다면 삼항 표현식을 사용할 수 있음:

```vue
<template>
  <div :class="[isActive ? activeClass : '', errorClass]"></div>
</template>
```

이렇게 하면 항상 `errorClass`가 적용되지만, `activeClass`는 `isActive`가 참일 때만 적용됨.
그러나 조건부 클래스가 여러 개일 경우 다소 장황할 수 있음.
그래서 배열 구문 안에서 객체 구문을 사용할 수 있음:

```vue
<template>
  <div :class="[{ [activeClass]: isActive }, errorClass]"></div>
</template>
```

## With Components

이 섹션은 컴포넌트에 대한 지식을 가정함. 필요하다면 건너뛰었다가 나중에 다시 돌아오세요.
컴포넌트의 `class` 속성을 사용할 때, 루트 요소에 해당 클래스가 추가되며, 기존의 클래스와 병합됨.
예를 들어, `MyComponent`라는 컴포넌트가 아래과 같은 템플릿을 가지고 있다면:

```vue
<template>
  <p class="foo bar">Hi!</p>
</template>
```

컴포넌트를 사용할 때 클래스를 추가하면:

```vue
<template>
  <MyComponent class="baz boo" />
</template>
```

렌더링되는 HTML은 아래과 같음:

```html
<p class="foo bar baz boo">Hi!</p>
```

클래스 바인딩의 경우도 동일함:

```vue
<template>
  <MyComponent :class="{ active: isActive }" />
</template>
```

`isActive`가 참일 때, 렌더링되는 HTML은 아래과 같음:

```html
<p class="foo bar active">Hi!</p>
```

만약 컴포넌트에 여러 루트 요소가 있다면, 어떤 요소가 이 클래스를 받을지 정의해야 함.

이는 `$attrs` 컴포넌트 속성을 사용하여 수행할 수 있음:

```vue
<template>
  <p :class="$attrs.class">Hi!</p>
  <span>This is a child component</span>
</template>
```

```vue
<template>
  <MyComponent class="baz" />
</template>
```

렌더링 결과는:

```html
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

## Binding Inline Styles

### Binding to Objects

`:style`은 JavaScript 객체 값에 바인딩을 지원하며, 이는 HTML 요소의 `style` 속성에 해당함:

```js
const activeColor = ref('red');
const fontSize = ref(30);
```

```vue
<template>
  <div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
</template>
```

camelCase 키를 사용하는 것이 권장되지만, `:style`은 CSS에서 사용되는 방식을 따르는 kebab-case CSS 속성 키도 지원함. 

예를 들어:

```vue
<template>
  <div :style="{ 'font-size': fontSize + 'px' }"></div>
</template>
```

스타일 객체에 직접 바인딩하는 것이 템플릿을 더 깔끔하게 유지하는 경우가 많음:

```js
const styleObject = reactive({
  color: 'red',
  fontSize: '30px'
});
```

```vue
<template>
  <div :style="styleObject"></div>
</template>
```

다시 한 번, 객체 스타일 바인딩은 계산된 속성과 함께 객체를 반환할 때 자주 사용됨.

### Binding to Arrays

`:style`에 여러 스타일 객체의 배열을 바인딩할 수 있음. 이러한 객체들은 병합되어 동일한 요소에 적용됨:

```vue
<template>
  <div :style="[baseStyles, overridingStyles]"></div>
</template>
```

### Auto-prefixing

`:style`에서 벤더 프리픽스가 필요한 CSS 속성을 사용할 경우, Vue는 자동으로 적절한 프리픽스를 추가함.
Vue는 런타임에서 현재 브라우저에서 지원되는 스타일 속성을 확인하여 이를 수행함.
특정 속성이 지원되지 않는 경우, 다양한 프리픽스 변형이 테스트되어 지원되는 것을 찾음.

### Multiple Values

스타일 속성에 여러 (프리픽스가 있는) 값을 제공할 수 있으며, 브라우저가 지원하는 마지막 값만 렌더링됨. 예를 들어:

```vue
<template>
  <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
</template>
```

이는 브라우저가 `flex`의 프리픽스 없는 버전을 지원하는 경우 `display: flex`로 렌더링됨.

# Conditional Rendering​  

## v-if  

`v-if` 지시어는 블록을 조건부로 렌더링하는 데 사용됨.

이 `v-if` 가 사용된 블록은 지시어의 표현식이 참(truthy) 값을 반환할 때만 렌더링됨.  

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```  

## v-else  
`v-else` 지시어를 사용하여 `v-if`의 "else 블록"을 나타낼 수 있음.  

```html
<button @click="awesome = !awesome">Toggle</button>

<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```  
`v-else` 요소는 `v-if` 또는 `v-else-if` 요소 바로 뒤에 와야 하며, 그렇지 않으면 인식되지 않음.  

### v-else-if  
`v-else-if`는 이름에서 알 수 있듯이 `v-if`의 "else if 블록" 역할을 함. 여러 번 체인할 수도 있음.  

```html
<div v-if="type === 'A'">
  타입A
</div>
<div v-else-if="type === 'B'">
  타입B
</div>
<div v-else-if="type === 'C'">
  타입C
</div>
<div v-else>
  Not A/B/C
</div>
```  
`v-else`와 마찬가지로 `v-else-if` 요소는 `v-if` 또는 다른 `v-else-if` 요소 바로 뒤에 와야 함.  

### v-if on <template>​  

`v-if`는 지시어이므로 단일 요소에 부착되어야 함.
그럼 여러 요소를 토글하고 싶을 땐 어찌 해야 하냐? 이 경우에는 `<template>` 요소에 `v-if`를 사용할 수 있음.
이 요소는 보이지 않는 래퍼 역할을 하며, 최종 렌더링 결과에는 `<template>` 요소가 포함되지 않음.  

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```  
`v-else` 및 `v-else-if`도 `<template>`에서 사용할 수 있음.  

### v-show​  

조건부로 요소를 표시하는 또 다른 옵션은 `v-show` 지시어임. 사용법은 대체로 동일함.  

```html
<h1 v-show="ok">Hello!</h1>
```  
차이점은 `v-show`가 있는 요소는 항상 렌더링되고 DOM에 남아 있다는 것임. `v-show`는 요소의 display CSS 속성만 토글함.  
`v-show`는 `<template>` 요소를 지원하지 않으며 `v-else`와 함께 작동하지 않음.  

### v-if vs. v-show​  

- `v-if`는 "진짜" 조건부 렌더링임. 이는 조건부 블록 내의 이벤트 리스너와 자식 컴포넌트가 적절히 파괴되고 재생성되도록 보장함.  
- `v-if`는 게으른(lazy) 방식임.
- 즉, 초기 렌더링에서 조건이 false인 경우 아무 것도 하지 않으며, 조건이 처음으로 true가 될 때까지 조건부 블록이 렌더링되지 않음.  
- 반면에 `v-show`는 훨씬 간단함. 요소는 초기 조건과 상관없이 항상 렌더링되며, CSS 기반으로 토글됨.  
- 일반적으로 `v-if`는 토글 비용이 더 높고, `v-show`는 초기 렌더링 비용이 더 높음.

>> 따라서 매우 자주 토글해야 하는 경우 `v-show`를 선호하고, 런타임 동안 조건이 변경될 가능성이 적은 경우 `v-if`를 선호함.  

### v-if with v-for 

`v-if`와 `v-for`가 같은 요소에 모두 사용되는 경우 `v-if`가 먼저 평가되는 암묵적인 우선순위가 있음.
이 때문에 같은 요소에서 `v-if`와 `v-for`를 함께 사용하는 것은 권장되지 않음.

# List Rendering​

## v-for

`v-for` 지시어를 사용해 배열을 기반으로 항목 목록을 렌더링할 수 있음. `v-for` 지시어는 `item in items` 형태의 특별한 구문이 필요함. 여기서 `items`는 소스 데이터 배열이고, `item`은 반복되는 배열 요소의 별칭임:

```js
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```
```html
<li v-for="item in items">
  {{ item.message }}
</li>
```

`v-for` 범위 안에서는 템플릿 표현식이 모든 부모 범위 속성에 접근할 수 있음. 추가로, `v-for`은 현재 항목의 인덱스를 위한 두 번째 별칭도 지원함:

```js
const parentMessage = ref('Parent')
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

```html
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```

결과:
```
Parent - 0 - Foo  
Parent - 1 - Bar
```

## v-for와 자바스크립트 범위 비교

`v-for`의 변수 범위는 다음과 같은 자바스크립트 코드와 유사함:

```js
const parentMessage = 'Parent'
const items = [
  /* ... */
]

items.forEach((item, index) => {
  console.log(parentMessage, item.message, index)
})
```

## v-for와 구조 분해 할당

`v-for` 항목 별칭에서도 함수 인자 구조 분해와 비슷한 구조 분해 할당을 사용할 수 있음:

```html
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- 인덱스 별칭 포함 -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

## 중첩된 v-for

중첩된 `v-for`에서도 부모 범위에 접근할 수 있음:

```html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

## v-for과 object

객체의 속성을 순회할 때도 `v-for`을 사용할 수 있음. 순회 순서는 `Object.values()` 호출 결과에 기반함:

```js
const myObject = reactive({
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})
```

```html
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

속성 이름을 위한 두 번째 별칭도 제공 가능함:

```html
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```

또한 인덱스에 대한 별칭도 추가할 수 있음:

```html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

## A range 와 함께 v-for 사용

`v-for`은 정수 범위도 받을 수 있음. 이 경우 1에서 n까지의 범위로 템플릿을 반복함:

```html
<span v-for="n in 10">{{ n }}</span>
```

## template 태그와 함께 v-for 사용

`<template>` 태그와 `v-for`을 함께 사용해 여러 요소를 렌더링할 수 있음:

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## v-for와 v-if 같이 사용

`v-if`와 `v-for`을 같은 요소에서 사용하면 암묵적인 우선순위 때문에 권장되지 않음. `v-if`가 `v-for`보다 높은 우선순위를 가짐:

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

이 문제를 해결하려면 `v-for`을 감싸는 `<template>` 태그로 이동해야 함:

```html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

## key 속성과 state 유지

`v-for`을 사용해 렌더링된 목록의 요소를 업데이트할 때 기본적으로 "in-place patch" 전략을 사용함. 이 모드는 효율적이지만, 자식 컴포넌트 상태나 임시 DOM 상태에 의존하지 않는 경우에만 적합함. 요소의 고유한 식별을 위해 `key` 속성을 제공해야 함:

```html
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

## component와 함께 v-for 사용

`v-for`을 컴포넌트에서 직접 사용할 수 있음. 데이터를 컴포넌트에 전달하려면 props를 사용해야 함:

```html
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```

## array 변경 detection

Vue는 배열의 변형 메서드 호출을 감지하고 업데이트를 트리거할 수 있음:

- `push()`, `pop()`, `shift()`, `unshift()`, `splice()`, `sort()`, `reverse()`

비변형 메서드를 사용할 때는 새 배열로 교체해야 함:

```js
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

## Filtered/Sorted 결과 표시

배열을 필터링하거나 정렬된 버전을 표시하려면, 비변형 메서드를 사용해 원본 데이터를 변경하지 않고 계산된 속성을 만들 수 있음:

```js
const evenNumbers = computed(() => {
  return numbers.value.filter((n) => n % 2 === 0)
})
```

`reverse()`와 `sort()`는 원본 배열을 변형하므로 계산 속성에서 사용할 때 주의해야 함.

# Event Handling

## Event Listening  

`v-on` 디렉티브를 사용할 수 있음. 

일반적으로 `@` 기호로 축약하여 사용하며, DOM Event를 리스닝하고 Event가 트리거될 때 자바스크립트를 실행할 수 있음.

사용법: `v-on:click="handler"` or `@click="handler"`

handler 값은 다음 중 하나일 수 있음:

- **inline handler**: Event가 트리거될 때 실행될 inline 자바스크립트(네이티브 `onclick` 속성과 유사).
- **Method handler**: 컴포넌트에 정의된 Method를 가리키는 속성 이름 또는 경로.

## inline handler  

inline handler는 주로 간단한 경우에 사용됨. 예를 들어:

```js
const count = ref(0)
```

```html
<button @click="count++">1 추가</button>
<p>카운트 값: {{ count }}</p>
```

## Method handler

더 복잡한 로직의 경우, inline handler는 적합하지 않을 수 있음.

그래서 `v-on`은 호출하고 싶은 컴포넌트 Method의 이름이나 경로도 허용함.

예를 들어:

```js
const name = ref('Vue.js')

function greet(event) {
  alert(`안녕하세요, ${name.value}!`)
  // `event`는 네이티브 DOM Event임.
  if (event) {
    alert(event.target.tagName)
  }
}
```

```html
<!-- `greet`는 위에서 정의된 Method의 이름임. -->
<button @click="greet">인사하기</button>
```

Method handler는 자동으로 트리거된 네이티브 DOM Event 객체를 받음.
위 예시에서, Event를 디스패치한 요소를 `event.target`을 통해 접근할 수 있음.

### Method와 inline handler 감지

템플릿 컴파일러는 `v-on` 값 문자열이 유효한 자바스크립트 식별자인지 또는 속성 접근 경로인지 확인하여 Method handler를 감지함.
예를 들어, `foo`, `foo.bar`, `foo['bar']`는 Method handler로 처리되고, `foo()`와 `count++`는 inline handler로 처리됨.

### inline handler에서 Method 호출  

Method 이름에 직접 바인딩하는 대신, inline handler에서 Method를 호출할 수도 있음.
이렇게 하면 네이티브 Event 대신 Method에 커스텀 인자를 전달할 수 있음.

```js
function say(message) {
  alert(message)
}
```

```html
<button @click="say('안녕하세요')">인사</button>
<button @click="say('안녕히 가세요')">작별 인사</button>
```

### inline handler에서 Event 인수 접근  

때로는 inline handler에서 원래의 DOM Event에 접근할 필요도 있음.
Method에 특수 변수 `$event`를 사용하여 전달할 수 있으며, inline 화살표 함수를 사용할 수도 있음.

```html
<!-- 특수 변수 $event 사용 -->
<button @click="warn('아직 제출할 수 없음.', $event)">
  제출
</button>

<!-- inline 화살표 함수 사용 -->
<button @click="(event) => warn('아직 제출할 수 없음.', event)">
  제출
</button>
```

```js
function warn(message, event) {
  // 네이티브 Event에 접근할 수 있음.
  if (event) {
    event.preventDefault()
  }
  alert(message)
}
```

### Event 수정자  

Event handler에서 `event.preventDefault()` 또는 `event.stopPropagation()`을 호출해야 하는 경우가 매우 흔함.
하지만 이런 작업을 Method 내에서 처리할 수 있다면, Method는 DOM Event의 세부사항 대신 데이터 로직에만 집중할 수 있음.
이를 해결하기 위해 Vue는 `v-on`을 위한 Event 수정자를 제공함.
수정자는 점(`.`)으로 표시되는 디렉티브 접미사임.

- `.stop`
- `.prevent`
- `.self`
- `.capture`
- `.once`
- `.passive`

```html
<!-- 클릭 Event의 전파가 중단됨 -->
<a @click.stop="doThis"></a>

<!-- 제출 Event가 페이지를 더 이상 새로고침하지 않음 -->
<form @submit.prevent="onSubmit"></form>

<!-- 수정자를 체이닝할 수 있음 -->
<a @click.stop.prevent="doThat"></a>

<!-- 수정자만 사용 -->
<form @submit.prevent></form>

<!-- Event의 대상이 해당 요소일 때만 handler 트리거 -->
<div @click.self="doThat">...</div>
```

### 참고 

수정자를 사용할 때 순서는 중요함.
코드가 동일한 순서로 생성되기 때문에, `@click.prevent.self`는 기본 클릭 동작을 요소 및 자식에게 모두 방지하지만, `@click.self.prevent`는 요소 자체에서만 클릭의 기본 동작을 방지함.

`.capture`, `.once`, `.passive` 수정자는 네이티브 `addEventListener` Method의 옵션을 반영함:

```html
<!-- 캡처 모드로 Event 리스너 추가 -->
<div @click.capture="doThis">...</div>

<!-- 클릭 Event는 최대 한 번만 트리거 -->
<a @click.once="doThis"></a>

<!-- 스크롤 Event의 기본 동작(스크롤)이 바로 실행 -->
<div @scroll.passive="onScroll">...</div>
```

`.passive` 수정자는 주로 모바일 장치에서 터치 Event 리스너 성능을 개선하기 위해 사용됨.

## 키 수정자  

키보드 Event를 리스닝할 때는 특정 키를 확인해야 할 때가 많음.
Vue는 `v-on` 또는 `@`에서 키 Event를 리스닝할 때 키 수정자를 추가할 수 있음:

```html
<!-- `Enter` 키를 누를 때만 `submit` 호출 -->
<input @keyup.enter="submit" />
```

```html
<input @keyup.page-down="onPageDown" />
```

## 키 별칭  

Vue는 가장 일반적으로 사용되는 키에 대한 별칭을 제공함:

- `.enter`
- `.tab`
- `.delete` (Delete 및 Backspace 키 모두 캡처)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

## 시스템 수정 키  

다음 수정자를 사용하여 마우스 또는 키보드 Event 리스너가 해당 수정 키가 눌렸을 때만 트리거되도록 할 수 있음:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

```html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

## .exact 수정자  

`.exact` 수정자는 Event를 트리거하기 위한 시스템 수정 키 조합을 정확히 제어할 수 있게 해줌.

```html
<!-- Alt 또는 Shift가 함께 눌려도 트리거 -->
<button @click.ctrl="onClick">A</button>

<!-- Ctrl만 눌렸을 때만 트리거 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 시스템 수정 키가 없을 때만 트리거 -->
<button @click.exact="onClick">A</button>
```

## 마우스 버튼 수정자  

- `.left`
- `.right`
- `.middle`

이 수정자는 특정 마우스 버튼으로 트리거된 Event에만 handler를 제한함.

# **Form Input Bindings**  

프론트엔드에서 폼을 처리할 때, 우리는 종종 폼 입력 요소의 상태를 JavaScript 상태와 동기화해야 함.
값을 수동으로 바인딩하고 변경 이벤트 리스너를 설정하는 것은 번거로울 수 있음

```html
<input :value="text" @input="event => text = event.target.value">
```

`v-model` 디렉티브는 위와 같은 작업을 간소화할 수 있음:

```html
<input v-model="text">
```

추가로, `v-model`은 다양한 유형의 `input` 요소, `<textarea>`, `<select>`에서도 사용할 수 있음.
이 디렉티브는 사용하는 요소에 따라 자동으로 다른 DOM 속성 및 이벤트 쌍으로 확장됨.

- `<input>`과 텍스트 타입의 `<textarea>` 요소는 `value` 속성과 `input` 이벤트 사용
- `<input type="checkbox">`와 `<input type="radio">`는 `checked` 속성과 `change` 이벤트 사용
- `<select>`는 `value` 속성을 사용하며, `change` 이벤트를 발생시킴.

---

## **Note**

`v-model`은 폼 요소에 선언된 초기 `value`, `checked`, `selected` 속성을 무시함. 항상 바인딩된 JavaScript 상태를 진리로 취급함. 초기 값을 JavaScript 쪽에서 반응형 API를 사용해 선언하는 것이 좋음.

## **Basic Usage**  

### **Text**  

```html
<p>Message is: {{ message }}</p>
<input v-model="message" placeholder="edit me" />
```
```vue
<script setup>
import { ref } from 'vue'

const message = ref('')
</script>

<template>
  <p>Message is: {{ message }}</p>
	<input v-model="message" placeholder="edit me" />
</template>
```
---

IME(중국어, 일본어, 한국어 등)가 필요한 언어의 경우, `v-model`은 IME 조합 중에는 업데이트되지 않는 것을 알 수 있음.
이러한 업데이트에도 반응하려면, `v-model` 대신 자체 `input` 이벤트 리스너와 값 바인딩을 사용해야 함.

---

### **Multiline text**  

```html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

```vue
<script setup>
import { ref } from 'vue'

const message = ref('')
</script>

<template>
	<span>Multiline message is:</span>
	<p style="white-space: pre-line;">{{ message }}</p>
	<textarea v-model="message" placeholder="add multiple lines"></textarea>
</template>
```

`<textarea>` 내부에서 텍스트 삽입을 할 때는 `v-model`을 사용해야 하며, 단순 텍스트 삽입은 작동하지 않음.

```html
<!-- 잘못된 예 -->
<textarea>{{ text }}</textarea>

<!-- 올바른 예 -->
<textarea v-model="text"></textarea>
```

---

### Checkbox  
**체크박스**

단일 체크박스, 불리언 값:

```html
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">{{ checked }}</label>
```

```vue
<script setup>
import { ref } from 'vue'

const checked = ref(true)
</script>

<template>
	<input type="checkbox" id="checkbox" v-model="checked" />
	<label for="checkbox">{{ checked }}</label>
</template>
```

여러 개의 체크박스를 동일한 배열이나 Set 값에 바인딩할 수도 있음:

```js
const checkedNames = ref([])
```

```html
<div>Checked names: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames" />
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
<label for="mike">Mike</label>
```

```vue
<script setup>
import { ref } from 'vue'

const checkedNames = ref([])
</script>

<template>
  <div>Checked names: {{ checkedNames }}</div>

  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
  <label for="jack">Jack</label>
 
  <input type="checkbox" id="john" value="John" v-model="checkedNames" />
  <label for="john">John</label>
 
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
  <label for="mike">Mike</label>
</template>
```

이 경우, `checkedNames` 배열에는 현재 선택된 체크박스의 값이 저장됨.

---

### Radio  

```html
<div>Picked: {{ picked }}</div>

<input type="radio" id="one" value="One" v-model="picked" />
<label for="one">One</label>

<input type="radio" id="two" value="Two" v-model="picked" />
<label for="two">Two</label>
```

```vue
<script setup>
import { ref } from 'vue'

const picked = ref('One')
</script>

<template>
  <div>Picked: {{ picked }}</div>

	<input type="radio" id="one" value="One" v-model="picked" />
	<label for="one">One</label>

	<input type="radio" id="two" value="Two" v-model="picked" />
  <label for="two">Two</label>
</template>
```

---

### Select

단일 선택:

```html
<div>Selected: {{ selected }}</div>

<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

```vue
<script setup>
import { ref } from 'vue'

const selected = ref('')
</script>

<template>
  <span> Selected: {{ selected }}</span>

  <select v-model="selected">
    <option disabled value="">Please select one</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</template>
```

---

## **Note**
**참고**

`v-model` 표현식의 초기 값이 선택 옵션 중 하나와 일치하지 않으면 `<select>` 요소는 "선택되지 않은" 상태로 렌더링됨. iOS에서는 이 경우 첫 번째 항목을 선택할 수 없으며, 변경 이벤트도 발생하지 않기 때문에 이 문제를 피하려면 빈 값을 가진 비활성 옵션을 제공하는 것이 좋음.

---

### **Multiple select (배열에 바인딩)**  

```html
<div>Selected: {{ selected }}</div>

<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

```vue
<script setup>
import { ref } from 'vue'

const selected = ref([])
</script>

<template>
  <div>Selected: {{ selected }}</div>

  <select v-model="selected" multiple>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</template>

<style>
select[multiple] {
  width: 100px;
}
</style>
```

`v-for`를 사용하여 선택 옵션을 동적으로 렌더링할 수도 있음:

```js
const selected = ref('A')

const options = ref([
  { text: 'One', value: 'A' },
  { text: 'Two', value: 'B' },
  { text: 'Three', value: 'C' }
])
```

```html
<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>

<div>Selected: {{ selected }}</div>
```

```vue
<script setup>
import { ref } from 'vue'

const selected = ref([])
</script>

<template>
  <div>Selected: {{ selected }}</div>

  <select v-model="selected" multiple>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
</template>

<style>
select[multiple] {
  width: 100px;
}
</style>
```

---

### **Value Bindings**  

---

라디오, 체크박스 및 선택 옵션의 `v-model` 바인딩 값은 일반적으로 정적 문자열(또는 체크박스의 경우 불리언 값)임.

```html
<!-- `picked`는 체크되었을 때 문자열 "a"가 됨. -->
<input type="radio" v-model="picked" value="a" />

<!-- `toggle`는 true 또는 false 값임. -->
<input type="checkbox" v-model="toggle" />

<!-- 첫 번째 옵션이 선택되었을 때 `selected`는 문자열 "abc"가 됨. -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

하지만 동적인 속성에 바인딩하고 싶을 때는 `v-bind`를 사용할 수 있음.
`v-bind`를 사용하면 입력 값을 문자열이 아닌 값에 바인딩할 수 있음.

---

### **Checkbox**  

```html
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no" />
```

`true-value`와 `false-value`는 Vue 전용 속성으로, `v-model`에서만 작동함.
여기서 체크박스가 선택되면 `toggle` 속성의 값은 'yes'로 설정되고, 선택이 해제되면 'no'로 설정됨.
또한 `v-bind`를 사용하여 동적 값에 바인딩할 수도 있음:

```html
<input
  type="checkbox"
  v-model="toggle"
  :true-value="dynamicTrueValue"
  :false-value="dynamicFalseValue" />
```

---

**Tip**  

`true-value`와 `false-value` 속성은 입력 요소의 `value` 속성에 영향을 미치지 않음. <- 브라우저는 선택되지 않은 체크박스를 폼 제출에 포함시키지 않기 때문.
"yes" 또는 "no"와 같은 두 가지 값 중 하나를 폼에 제출하려면 라디오 입력을 대신 사용해야 함.

---

### **Radio**  

```html
<input type="radio" v-model="pick" :value="first" />
<input type="radio" v-model="pick" :value="second" />
```

`pick`은 첫 번째 라디오 입력이 선택되면 `first`의 값으로 설정되고, 두 번째 라디오 입력이 선택되면 `second`의 값으로 설정됨.

---


# Lifecycle hook

각 Vue 컴포넌트 인스턴스는 생성될 때 일련의 초기화 단계를 거침.
예를 들어, 데이터 감시를 설정하고, 템플릿을 컴파일하고, 인스턴스를 DOM에 마운트하고, 데이터가 변경되면 DOM을 업데이트해야 함.
그 과정에서 생명 주기 훅(lifecycle hooks)이라 불리는 함수도 실행하여, 특정 단계에서 개발자가 의도하는 로직이 실행될 수 있도록 함.

## Lifecycle hook 등록하기​

예를 들어 onMounted 훅은 컴포넌트가 초기 렌더링 및 DOM 노드 생성이 완료된 후 코드를 실행하는 데 사용될 수 있음

```vue
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  console.log(`컴포넌트가 마운트 됐습니다.`)
})
</script>
```

인스턴스 생명 주기의 여러 단계에서 호출되는 다른 훅도 있으며, 가장 일반적으로 사용되는 것은 onMounted, onUpdated, onUnmounted가 있음.

onMounted를 호출하면, Vue는 등록된 콜백 함수를 현재 활성 컴포넌트 인스턴스와 자동으로 연결함.
이를 위해서는 컴포넌트 설정 중에 이러한 훅은 동기적으로 등록해야 함.

안좋은예:

```js
setTimeout(() => {
  onMounted(() => {
    // 작동 안함.
  })
}, 100)
```

이 훅이 setup() 또는 <script setup> 내에 배치된 코드 순서에 영향을 받는다는 것을 의미하는 것이 아님.
onMounted()는 호출 스택이 동기식임.
또한 setup() 내에서 시작되는 경우에만 외부 함수를 실행하는 방식으로 사용할 수 있음.

```js
import { onMounted } from 'vue'

export default {
  setup() {
    // onMounted() 훅은 setup 함수 내에서 사용하는 경우에만 마치 외부의 함수를 호출한 것처럼 작성할 수 있음.
    // 이렇게 작성된 onMounted() 훅은 컴포넌트가 마운트 된 이후 콜백 함수를 실행하지만 `this`를 통해 컴포넌트 인스턴스에 접근 불가함.
    onMounted(function() {
      // `this`를 통해 컴포넌트 인스턴스에 접근불가.
      console.log('onMounted가 호출 되었습니다:', this)
    })
  },

  // 컴포넌트가 마운트 된 후, 옵션 API 방식의 mounted() 훅을 실행.
  mounted() {
    // `this`를 통해 컴포넌트 인스턴스에 접근할 수 있음.
    console.log('mounted()가 호출 되었습니다:', this)
  }
}
```

# Watchers​

Computed properties는 선언적으로 파생 값을 계산할 수 있게 해줌.
그러나 상태 변경에 반응하여 "side effects"를 수행해야 하는 경우가 있음.

ex) DOM을 변경하는 경우, 비동기 작업 결과에 따라 다른 상태를 변경하는 경우 등

Composition API를 사용하면 반응형 상태가 변경될 때마다 콜백을 트리거하는 watch 함수를 사용할 수 있음

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Questions usually contain a question mark. ;-)')
const loading = ref(false)

// watch works directly on a ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.includes('?')) {
    loading.value = true
    answer.value = 'Thinking...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    } finally {
      loading.value = false
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" :disabled="loading" />
  </p>
  <p>{{ answer }}</p>
</template>
```

### Watch 소스 타입​

watch의 첫 번째 인수는 다양한 유형의 반응형 "소스"가 될 수 있음 (ref(계산된 ref 포함), 반응형 객체, getter 함수, 또는 여러 소스의 배열이 될 수 있음)

```js
const x = ref(0)
const y = ref(0)

// single ref
watch(x, (newX) => {
  console.log(`x is ${newX}`)
})

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`)
  }
)

// array of multiple sources
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`)
})
```

반응형 객체의 속성을 다음과 같이 감시할 수 없음.

```js
const obj = reactive({ count: 0 })

// watch()에 숫자를 전달하고 있기 때문에 작동하지 않음.
watch(obj.count, (count) => {
  console.log(`Count는: ${count}입니다`)
})
```

대신, 아래처럼 getter를 쓰면 됨.

```js
watch(
  () => obj.count,
  (count) => {
    console.log(`Count는: ${count}입니다`)
  }
)
```

## Deep Watchers

반응형 객체에서 직접 watch()를 호출하면 암시적으로 Deep Watchers가 생성됨 -> 콜백은 모든 중첩된 변경에 대해 트리거됨:

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // fires on nested property mutations
  // Note: `newValue` will be equal to `oldValue` here
  // because they both point to the same object!
})

obj.count++
```

반응형 객체를 반환하는 getter와는 차이가 있음 - 후자의 경우, getter가 다른 객체를 반환할 때만 콜백 실행:

```js
watch(
  () => state.someObject,
  () => {
    // fires only when state.someObject is replaced
  }
)
```

그러나, deep 옵션을 명시적으로 사용하여 두 번째 경우를 Deep Watcher로 만들 수 있음음:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // Note: `newValue` will be equal to `oldValue` here
    // *unless* state.someObject has been replaced
  },
  { deep: true }
)
```

**주의해서 사용**

Deep watcher는 감시된 객체의 모든 중첩된 속성을 순회해야 하므로, 대규모 데이터 구조에서 사용할 경우 cost가 많이 듬. 필요할 때만 사용하고 성능에 미치는 영향을 주의.

## Eager 감시자​

watch는 기본적으로 지연 모드임 <- 감시된 소스가 변경될 때까지 콜백 미호출.
하지만 경우에 따라 동일한 콜백 로직을 즉시 실행하고 싶을 수 있음(초기 데이터를 가져오고, 관련 상태가 변경될 때마다 데이터를 다시 가져오고 싶을 때 등)

immediate: true 옵션을 전달하여 watcher의 콜백을 즉시 실행하도록 강제할 수 있음

```js
watch(
  source,
  (newValue, oldValue) => {
    // executed immediately, then again when `source` changes
  },
  { immediate: true }
)
```
## Once 감시자 ​

감시자의 콜백은 감시된 소스가 변경될 때마다 실행됩니다. 콜백이 소스가 변경될 때 한 번만 트리거되기를 원한다면, once: true 옵션을 사용하십시오.

```js
watch(
  source,
  (newValue, oldValue) => {
    // when `source` changes, triggers only once
  },
  { once: true }
)
```

감시자 콜백이 소스와 동일한 반응형 상태를 사용하는 경우가 일반적임.
ex) todoId ref가 변경될 때마다 원격 리소스를 로드하는 감시자

```js
const todoId = ref(1)
const data = ref(null)

watch(
  todoId,
  async () => {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    )
    data.value = await response.json()
  },
  { immediate: true }
)
```

특히, watcher가 todoId를 두 번 사용하는 방법에 주목하자.
한 번은 소스로, 그리고 다시 콜백 내에서 사용됨.

이를 watchEffect()를 사용하여 간소화할 수 있음.
watchEffect()는 콜백의 반응형 종속성을 자동으로 추적할 수 있게 해줌.
위의 감시자는 아래과 같이 다시 작성할 수 있음:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

- 콜백은 즉시 실행되며, immediate: true를 지정할 필요가 없음.
- 실행 중엔 todoId.value를 종속성으로 자동으로 추적(계산 속성과 유사).
- todoId.value가 변경될 때마다 콜백 재실행.
- 소스 값으로 todoId를 명시적으로 전달할 필요 x (watchEffect() 사용시).
- watchEffect()와 반응형 데이터 가져오기 작업의 실제 예시는 이 예제를 참고하자.

- 종속성(dependency)이 하나만 있는 경우 watchEffect()의 이점은 상대적으로 적음.
- 종속성이 여러 개인 watcher의 경우, watchEffect()를 사용하면 종속성 목록을 수동으로 유지하는 부담을 덜 수 있음.
- 중첩된 데이터 구조에서 여러 속성을 감시해야 할 경우, watchEffect()는 콜백에서 사용된 속성만 추적하므로 deep watcher보다 더 효율적일 수 있음.

**TIP**

	watchEffect는 동기적 실행 동안에만 종속성을 추적함.
	비동기(async) 콜백과 함께 사용할 때는 첫 번째 await 틱 전에 접근한 속성만 추적.

## watch와 watchEffect 비교​

watch와 watchEffect는 모두 반응형으로 부수 효과를 수행할 수 있게 해줌.
주요 차이점은 반응형 종속성을 추적하는 방식:

### watch의 장단점

watch는 명시적으로 감시된 소스만 추적함.
콜백 내부에서 접근한 것은 추적하지 않음.
또한, 소스가 실제로 변경되었을 때만 콜백이 실행됨.
watch는 종속성 추적을 부수 효과와 분리하여 콜백이 실행될 시기를 더 정확하게 제어할 수 있음.

### watchEffect의 장단점

watchEffect는 종속성 추적과 부수 효과를 하나의 단계로 결합함.
동기적 실행 동안 접근한 모든 반응형 속성을 자동으로 추적함.
이는 더 편리하며 일반적으로 더 간결한 코드를 작성할 수 있게 해주지만, 반응형 종속성이 덜 명확해지게 만든다는 단점이 있음.

##callback flush timing
​
반응형 상태를 변경하면 Vue 컴포넌트 업데이트와 사용자가 생성한 watcher 콜백을 모두 트리거할 수 있음.
사용자가 생성한 watcher 콜백은 중복 호출을 피하기 위해 배치됨.
ex) 감시되는 배열에 항목을 동기적으로 1,000개 푸시할 때 감시자가 1,000번 실행되는 것은 원치 않을 것임.

기본적으로, watcher의 콜백은 부모 컴포넌트 업데이트 후(있는 경우), 소유자 컴포넌트의 DOM 업데이트 전 호출됨.
이는 watcher 콜백 내부에서 소유자 컴포넌트의 DOM에 접근하려고 할 때, DOM이 업데이트 전 상태일 것임을 의미.

### post watcher
watcher 콜백에서 Vue가 DOM을 업데이트한 후 소유자 컴포넌트의 DOM에 접근하려면, flush: 'post' 옵션을 지정해야 함.

```js
watch(source, callback, {
  flush: 'post'
})

watchEffect(callback, {
  flush: 'post'
})
```
post-flush watchEffect()에는 약어로 쓴 watchPostEffect()가 있음:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* executed after Vue updates */
})
```

Sync Watchers​

Vue 관리 업데이트 이전에 동기적으로 실행되는 감시자를 만들 수도 있음:

```js
watch(source, callback, {
  flush: 'sync'
})

watchEffect(callback, {
  flush: 'sync'
})
```

sync watchEffect()에는 약어로 쓴 watchSyncEffect()가 있음:

```js
import { watchSyncEffect } from 'vue'
watchSyncEffect(() => {
  /* executed synchronously upon reactive data change */   /* 반응형 데이터 변경 시 동기적으로 실행됨 */
})
```

***주의해서 사용***

	동기 감시자는 배치가 없으며 반응형 변이가 감지될 때마다 트리거됨.
	간단한 불리언 값을 감시할 때는 사용해도 괜찮지만, 배열과 같이 동기적으로 여러 번 변경될 수 있는 데이터 소스에 사용하지 말자.

## Stopping a Watcher

setup()이나 <script setup> 내에서 'synchronously'하게 선언된 watcher는 소유자 컴포넌트 인스턴스에 바인딩되며, 소유자 컴포넌트가 마운트 해제될 때 자동으로 중지됨.

대부분의 경우 감시자를 직접 중지할 필요가 없음.
중요한 점은 감시자가 'synchronously'하게 생성되어야 한다는 것.
감시자가 비동기 콜백 내에서 생성되면 소유자 컴포넌트에 바인딩되지 않으며, 메모리 누수를 피하기 위해 아래와 같이 수동으로 중지해야 함.

```vue
<script setup>
import { watchEffect } from 'vue'

// 이 감시자는 자동으로 중지됨.
watchEffect(() => {})

// ...이 감시자는 중지되지 않음. 각각 다른 감시자임.
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

감시자를 수동으로 중지하려면 returned handle function을 사용하자.
-> watch와 watchEffect 모두에 대해 작동함:

```js
const unwatch = watchEffect(() => {})

// ...later, when no longer needed  (더 이상 필요하지 않을 때)
unwatch()
```

비동기적으로 감시자를 생성해야 하는 경우는 매우 드물며, 가능하면 동기적 생성을 선호해야 합니다. 비동기 데이터를 기다려야 하는 경우, 감시 로직을 조건부로 만들 수 있습니다:

```js
// data to be loaded asynchronously
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // do something when data is loaded (데이터를 불러왔을 때 무언가 작업할 내용 코딩하면 됨)
  }
})
```
