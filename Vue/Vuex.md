Vuex는 Vue.js 에 대한 **상태 관리 패턴** + **라이브러리**

Vue의 이전 공식 상태 관리 라이브러리. 현재는 Pinia가 Vue의 공식 상태 관리 라이브러리로 지정이 되었다.

상태 관리 패턴이 의미하는 바는 다음과 같다.

일반적인 코드를 생각해보자.

```jsx
const Counter = {
  // state
  data () {
    return {
      count: 0
    }
  },
  // view
  template: `
    <div>{{ count }}</div>
  `,
  // actions
  methods: {
    increment () {
      this.count++
    }
  }
}createApp(Counter).mount('#app')
```

위의 Counter라는 변수에는 state, view, actions가 각기 들어 있다.

이는 각기 다음을 의미한다.

- 상태(**state) :** 앱을 구동하는 데이터. 서버와 주고받는다.
- **뷰(view)** : 데이터를 받아 그려주는 **ui 화면**
- **액션(Actions):** 사용자의 입력에 따라 반응적으로 데이터를 변경하는 **method**

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ab04f724-503a-447e-a1ce-aa7af700a237/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220512%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220512T161814Z&X-Amz-Expires=86400&X-Amz-Signature=1e75d9169738587f3e2d9146f6ef7e62f0f3333bf7aedd9524ed4b58038d6ff0&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

단 하나의 컴포넌트만 본다면 이렇게 하나의 흐름이 나온다.

그렇지만 이러한 하나의 흐름은 여러 개의 컴포넌트가 일반적인 state를 공유하면 바로 무너진다.

- 여러 뷰가 동일한 상태에 종속될 수 있다. ⇒ drill 문제, props를 넘겨주기 힘들다
- 다른 뷰의 액션은 동일한 상태 부분을 변경해야 할 수도 있다. ⇒ 패턴이 깨지기 쉽다

이를 위해 redux처럼 상태를 별도로 관리해야 한다

즉, 상태를 관리할 필요가 있는 모든 컴포넌트를 중앙식으로 집중관리하는 방법이다.

Vuex는 Redux보다 훨씬 쉽게 4가지 형태로 관리한다.(State, Mutations, Actions, Getter)

Nuxt에서는 Store 폴더 안의 파일들은 자동적으로 Vuex로 관리된다.

### State

Vue component에서는 원본 소스 역할인 data로 볼 수 있습니다.

state는 mutation을 통해서만 변경이 가능합니다.

### **Mutations**

유일하게 state를 변경할 수 있는 방법이며 메서드와 유사합니다.

commit을 통해서만 호출 할 수 있으며 함수로 구현됩니다.

API를 통해 전달받은 데이터를 mutations에서 가공하여 state를 변경합니다.

Nuxt에서 State와 Mutation의 사례는 다음과 같습니다.

```jsx
export const state = () => ({
  counter: 0,
});

export const mutations = {
  increment(state) {
    state.counter++;
  },
};
```

### **Actions**

비동기 작업이 가능합니다.

mutation을 호출하기 위한 commit이 가능합니다.

action은 dispatch를 통해 호출할 수 있습니다.

axios를 통한 api 호출과 그 결과에 대해 return을 하거나 mutation으로 commit하는 용도로 사용됩니다.

```jsx
actions: {
  nuxtServerInit ({ commit }, { req }) {
    if (req.session.user) {
      commit('user', req.session.user)
    }
  }
}
```

### **Getter**

Vue componet의 computed처럼 계산된 속성입니다.

state에 대해 연산을 하고 그 결과를 view에 바인딩 할 수 있습니다.

state의 변경 여부에 따라 view를 업데이트합니다.

위와 같이 개별적으로 놓을 수도 있지만, State, Mutations, actions, getter를 하나의 모듈로 만들어 사용할 수도 있습니다. Nuxt에서는 store 폴더를 만든 뒤, index.js(state 및 모듈을 모아서 사용할 곳), actions.js, mutations.js 등으로 나누어 관리할 수도 있습니다.

```jsx
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> `moduleA`'s state
store.state.b // -> `moduleB`'s state
```

---

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2580a880-641a-49a4-aba5-996d9372679e/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220512%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220512T161842Z&X-Amz-Expires=86400&X-Amz-Signature=c1cf510320fe4f043e99346fd0b160f188b7b12731cb90ac8b3fc7c11f5b3206&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

Vuex에서도 단방향으로 데이터가 흘러갑니다.

위 그림과 같이 우선

- Vue Component에서 Dispatch([action method name])를 통해 **action을 실행**
  합니다.
- **Action에서는 외부 Api를 호출하는 등 비동기 로직을 처리**합니다.
- 그 결과를 이용해 **동기 로직인 Mutaions을 호출**합니다.
  - Commit([mutations method name])를 통해 Mutation을 실행
- **Mutation에서 State(data)를 변경**합니다.
- getter를 이용하여 다시 **Compoent에 바인딩돼 화면을 갱신**합니다.

---

## Pinia

이때까지 Vue의 상태관리하면 당연하게도 Vuex를 떠올리곤 했는데, Pinia라는 상태관리 툴이 공식으로 지정되었습니다. 그렇다면 Vuex는 끝난 것인가? 아닙니다. Vuex5와 Pinia 사이에는 전환이 쉬울 것이라 했으므로 React가 Redux, Recoil처럼 선택지가 생긴 것처럼 Vue도 Vuex와 Pinia라는 선택지가 생겼습니다.

### 주된 특징

- mutations가 사라지고, action에서 처리하도록 바뀌었습니다. 또 Pinia는 Vue의 주된 특징 중 하나인 composition API에 익숙한 코드 작성 방식을 제공합니다.
