## application instance

모든 Vue 애플리케이션은 createApp 함수로 새로운 application instance를 생성하여 시작함.

    import { createApp } from 'vue'
    const app = createApp({
      /* root component 옵션 */
    })

## root component

createApp에 전달하는 객체는 실제로 component임.

모든 애플리케이션은 다른 component를 자식으로 포함할 수 있는 "root component"가 필요함.

단일 파일 component를 사용하는 경우, 일반적으로 다른 파일에서 root component를 가져옴.

    import { createApp } from 'vue' // 단일 파일 component -> root component App 가져오기
    import App from './App.vue'

    const app = createApp(App)

대부분의 실제 애플리케이션은 중첩 가능하고 재사용 가능한 component의 트리 구조로 조직됨.

예를 들어, Todo 애플리케이션의 component 트리는 다음과 같을 수 있음:

    App (root component)
    ├─ TodoList
    │  └─ TodoItem
    │     ├─ TodoDeleteButton
    │     └─ TodoEditButton
    └─ TodoFooter
       ├─ TodoClearButton
       └─ TodoStatistics

## 앱 마운팅

application instance는 .mount() method가 호출될 때까지 아무것도 rendering하지 않음.
이 method는 "container" 인수를 기대하며, 이는 실제 DOM 요소일 수도 있고 선택자 문자열일 수도 있음:

    <div id="app"></div>
    app.mount('#app')

앱의 root component 내용은 container 요소 내부에 rendering됨.

container 요소 자체는 앱의 일부로 간주되지 않음.

.mount() method는 모든 앱 구성 및 자산 등록이 완료된 후에 호출되어야 함.

또한 이 method의 반환 값은 application instance가 아닌 root component 인스턴스임.

## In-DOM root component templete

root component의 templete은 일반적으로 component 자체의 일부이지만, mount container 내부에 직접 작성하여 templete을 별도로 제공할 수도 있음:

    <div id="app">
      <button @click="count++">{{ count }}</button>
    </div>
    import { createApp } from 'vue'

    const app = createApp({
      data() {
        return {
          count: 0
        }
      }
    })

    app.mount('#app')

root component에 templete 옵션이 없는 경우, Vue는 자동으로 container의 innerHTML을 templete으로 사용함.

인-DOM templete은 빌드 단계 없이 Vue를 사용하는 애플리케이션에서 자주 사용됨. server 측 프레임워크와 함께 사용할 때, 루트 templete은 server에 의해 동적으로 생성될 수 있음.

## 앱 구성

application instance는 .config 객체를 노출하여 몇 가지 앱 수준 옵션을 구성할 수 있게 해줌.

예를 들어, 모든 하위 component의 오류를 캡처하는 앱 수준 오류 처리기를 정의할 수 있음:

    app.config.errorHandler = (err) => {
      /* 오류 처리 */
    }

    application instance는 또한 앱 범위 자산을 등록하기 위한 몇 가지 method를 제공함. 예를 들어, component를 등록하는 방법임:

    app.component('TodoDeleteButton', TodoDeleteButton)

이렇게 하면 TodoDeleteButton을 앱 어디에서나 사용 가능함.

또한 application instance API의 전체 목록을 API 참조에서 찾아볼 수 있음.

앱을 mount하기 전에 모든 앱 구성을 적용해야 함.

## multiple application instance (다중 애플리케이션 인스턴스)

같은 페이지에 단일 application instance에 제한되지 않음.

createApp API를 사용하면 동일한 페이지에서 여러 Vue 애플리케이션이 공존할 수 있으며, 각 애플리케이션은 구성 및 전역 자산에 대한 고유한 범위를 가짐:

    const app1 = createApp({
      /* ... */
    })
    app1.mount('#container-1')

    const app2 = createApp({
      /* ... */
    })
    app2.mount('#container-2')
    
server rendering(서버로부터 HTML 파일을 받아 브라우저에 뿌려주는 과정) HTML을 개선하기 위해 Vue를 사용하는 경우, 대형 페이지의 특정 부분만 제어하기 위해 전체 페이지에 단일 Vue application instance를 mount하지 않는 것이 좋음.

여러 개의 작은 application instance를 생성하고 그들이 책임지는 요소에 mount하는게 좋음.

