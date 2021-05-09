# Declarative Rendering(선언적 렌더링)
### 1. 간단한 템플릿 구문을 사용하여 DOM에서 데이터를 선언적으로 렌더링할 수 있다.

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

### 2. 엘리먼트 속성(element attribute)을 바인딩할 수 있다.

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

------      
<br/>

# 조건문 반복문(if, for)

### 조건문

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

### 반복문

Vue 엘리먼트가 Vue에 삽입/업데이트/제거될 때 자동으로 트랜지션 효과를 적용할 수 있음

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
