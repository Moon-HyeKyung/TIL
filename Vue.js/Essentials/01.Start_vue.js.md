# 1. Declarative Rendering(선언적 렌더링)

### 1) 선언적 렌더링

- 간단한 템플릿 구문을 사용하여 DOM에서 데이터를 선언적으로 렌더링할 수 있다.

  - message라는 key에 'Hello Vue!'라는 value를 할당(선언)	

  ```html
  <div id="app">
    {{ message }}
  </div>
  ```

  ```js
  var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue!'
    }
  })
  ```

  - vue앱이 DOM요소(#app)에 연결되어 DOM 요소를 제어
  - HTML은 엔트리 포인트일 뿐 다른 모든 것은 새롭게 생성된 Vue 인스턴스 내에서 발생

<br/>

### 2) 엘리먼트 속성 바인딩

- 엘리먼트 속성(element attribute)을 바인딩

```html
<div id="app-2">
  <span v-bind:title="message">
    내 위에 잠시 마우스를 올리면 동적으로 바인딩 된 title을 볼 수 있습니다!
  </span>
</div>
```

```js
var app2 = new Vue({
    el: '#app-2',
    data: {
        message: '이 페이지는 ' + new Date() + ' 에 로드되었습니다.'
    }
})
```

- **v-bind 속성**: directive
- **directive**
  - Vue에서 제공하는 특수 속성: **v-**접두어가 붙어있음
  - 렌더링 된 DOM에 특수 반응형 동작을 함
- Javascript에 app2.message = 'new Message'입력
  -> title 속성이 업데이트 됨

<br/>
<br/>

# 2. 조건문 반복문(if, for)

### 1) 조건문

```html
<div id="app-3">
  <p v-if="seen">이제 나를 볼 수 있어요</p>
</div>
```

```js
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

- Javascript에 app3.seen = false 입력 
  -> 메시지가 사라짐
- text와 attribute 뿐 아니라 DOM 구조에도 데이터 바인딩을 할 수 있음을 보여줌

<br/>

### 2) 반복문

- Vue 엘리먼트가 Vue에 삽입/업데이트/제거될 때 자동으로 트랜지션 효과를 적용할 수 있음

```html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```

```js
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'JavaScript 배우기' },
      { text: 'Vue 배우기' },
      { text: '무언가 멋진 것을 만들기' }
    ]
  }
})
```

- Javascript에서 app4.todos.push({ text: 'New Item' }) 입력
  -> Todo 목록에 새 항목이 동적으로 추가됨



<br/>
<br/>

# 3. 사용자 입력 핸들링

### 1) v-on

- ```v-on``` 디렉티브를 사용해 Vue 인스턴스에서 **메소드를 호출하는 이벤트 리스너를 추가**할 수 있다.

```html
<div id = "app-5">
    <p>{{ message }}</p>
    <button v-on:click="reverseMessage">메시지 뒤집기</button>
</div>
```

```js
var app5 = new vue({
    el: '#app-5',
    data: {
    message: '안녕하세요!'
	},
    methods: {
        reverseMessage: () => {
            this.message = this.message.split('').reverse().join('')
        }
    }                 
})
```

- 직접적으로 DOM을 건드리지 않고 앱의 상태만 업데이트 함
- 모든 DOM의 조작은 Vue에 의해 처리

<br/>

### 2) v-model

- ```v-model```을 이용해 **form에 대한 입력과 앱 상태를 양방향으로 바인딩** 함

```html
<div id="app-6">
    <p>{{ message }}</p>
    <input v-model="message">
</div>
```

```js
var app6 = new vue({
    el: '#app-6',
    data: {
        message: '안녕하세요 vue!'
    }
})
```

- input 박스의 내용을 바꾸면 **```v-model``` 디렉티브**를 통해 vue 인스턴스 내용(message)이 변경됨
  -> 양방향 바인딩이 됨

<br/>

<br/>



# 4. 컴포넌트 사용

### 1) 컴포넌트란?

: 대규모 애플리케이션을 구축할 수 있게 해주는 작고 독립적이며 재사용할 수 있는 추상적 개념 

1. 작고 독립적임
2. 재사용 가능
3. 컴포넌트들로 대규모 애플리케이션을 구축 가능



- 거의 모든 유형의 애플리케이션 인터페이스를 **컴포넌트 트리**로 추상화 가능![components_tree](https://kr.vuejs.org/images/components.png)

> [그림 1] 컴포넌트 트리

<br/>

### 2) 컴포넌트 등록 방법

- component = 미리 정의된 옵션을 가진 vue 인스턴스

  ```js
  Vue.component('todo-item', {
      template: '<li>할일 항목 하나입니다.</li>'
  })
  ```

  ```html
  <ol>
      <!--todo-item 컴포넌트의 인스턴스 생성-->
      <todo-item></todo-item>
  </ol>
  ```

  - 똑같은 텍스트를 렌더링하는 것 뿐임

  

- prop(속성 객체)을 전달 받기

  - 부모 영역의 데이터를 자식 컴포넌트에 전달하기 위해 사용

  ```html
  <div id="app-7">
      <ol>
          <!--:는 v-bind의 약어-->
          <!--각 todo-item에 todo 객체를 제공
  			화면에 나오므로, 각 항목의 컨텐츠는 동적으로 바뀔 수 있음
  			각 구성 요소에 "key"를 제공해야함 (이유는 나중에)-->
          <todo-item
                     v-for="item in groceryList"
                     :todo="item"
                     :key="item.id"
         	></todo-item>
      </ol>
  </div>
  ```

  ```js
  Vue.component('todo-item', {
      // 이제 todo-item 컴포넌트는 "prop"이라 하는 사용자 정의 속성 입력받기 가능
      // 이 prop은 todo라는 이름
      prop:['todo'],
      template: '<li>{{ todo.text }}</li>'
  })
  
  var app7 = new Vue({
      el: '#app-7',
      data: {
          groceryList: [
              { id: 0, text: 'Vegetables' },
              { id: 1, text: 'Cheese'},
              { id: 2, text: 'Fruits'}
          ]
      }
  })
  ```



<br/>

<br/>

# Referneces

- https://kr.vuejs.org/v2/guide/index.html
