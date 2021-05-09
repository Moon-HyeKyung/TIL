# 1. Vue Instance 만들기

- 모든 vue 앱은 ```Vue``` 함수로 **새 vue 인스턴스**를 만드는 것부터 시작

  ```js
  var vm = new Vue({
      // 옵션
  })
  ```

- Vue 인스턴스를 생성 시 **options 객체**를 전달해야 함

  - 전체 옵션 목록은 [API reference](https://vuejs.org/v2/api/) 에서 확인 가능

- **Vue 앱**

  - ```new Vue```를 통해 만들어진 ```Root Vue Instance```로 구성됨

  - 선택적으로 중첩이 가능

  - 재사용 가능한 **컴포넌트 트리**로 구성됨

  - Ex) Todo 앱의 컴포넌트 트리

    ```bash
    Root Instance
    └─ TodoList
       ├─ TodoItem
       │  ├─ DeleteTodoButton
       │  └─ EditTodoButton
       └─ TodoListFooter
          ├─ ClearTodosButton
          └─ TodoListStatistics
    ```

<br/>

<br/>



# 2. data &  method

### 1) data 객체

- Vue Instance가 생성 될 때 ```data``` 객체에 있는 모든 속성이 Vue의 **반응형 시스템**에 추가됨

- 각 속성 값 변경 시 vue가 반응 -> 새로운 값으로 업데이트

  ```js
  // 데이터 객체
  var data = { a: 1 }
  
  // Vue 인스턴스에 데이터 객체를 추가
  var vm = new Vue({
      data: data
  })
  
  // 인스턴스에 있는 속성은 원본 데이터에 있는 값을 반환
  vm.a === data.a // => true(type, value 모두 일치)
  
  // 인스턴스 속성 값 변경 -> 원본 데이터도 변경
  vm.a = 2
  console.log(data.a); // 콘솔 창에 2 출력
  
  // 원본 데이터 변경 -> 인스턴스 속성 값도 변경
  data.a = 3
  console.log(vm.a); // 콘솔 창에 3 출력
  ```



- 데이터가 변경되면 화면은 다시 렌더링 됨

- ```data```에 있는 속성들은 **인스턴스 생성 시에 존재하는 것들만 반응형**

  - Ex)  다음과 같이 새 속성을 추가했을 경우 **b가 변경되어도 화면은 갱신 x**

    ``` js
    vm.b = 'hi'
    ```

  - 속성이 나중에 필요하고 빈 값의 상태로 시작한다면 초기값을 정해줘야 함.

    - Ex) 추후에 필요한 속성의 초기화

      ```js
      data: {
          newTodoText: '',
          visitCount: 0,
          hideCompleteTodos: false,
          todos: [],
          error: null
      }
      ```

<br/>



### 2) Object.freeze()

- 기존 속성이 변경되는 것을 막음 -> 반응성 시스템이 추적하지 못함

  - Ex) obj의 속성이 변경되는 것을 막는 경우(const)

  ``` js
  var obj = {
      foo: 'bar'
  }
  
  Object.freeze(obj)
  
  new Vue({
      el: '#app',
      data: obj
  })
  ```

  ```html
  <div id="app">
      <p>{{ foo }}</p>
      <!-- obj.foo는 변하지 않음-->
      <button v-on:click="foo = 'baz'">Change it</button>
  </div>
  ```

<br/>



### 3) $

- 데이터 속성 이외의 유용한 **인스턴스 속성 및 메소드**

- **```$```**: **다른 사용자 정의 속성과 구분**하기 위한 *접두어*

  - Ex)

    ```js
    var data = { a: 1 }
    
    var vm = new Vue({
        el: '#example',
        data: data
    })
    
    vm.$data === data // => true
    vm.$el === document.getElementById('example') // => true
    
    // $watch는 인스턴스 메소드
    vm.$watch('a', (newVal, oldVal)=>{
        // newvalue와 oldvalue를 parameter로 갖음
        // vm.a가 변경되면 호출됨
    })
    
    ```

- 인스턴스 속성과 메소드 전체 목록: [API reference](https://vuejs.org/v2/api/)



<br/> <br/>



# 3. Instance Lifecycle Hook

- Vue 인스턴스는 생성 시 일련의 초기화 단계를 거침
  1. 데이터 관찰 설정이 필요한 경우
  2. 템플릿을 컴파일하는 경우
  3. 인스턴스를 DOM에 마운트하는 경우
  4. 데이터가 변경되어 DOM을 업데이트하는 경우

- 위 과정에서 사용자 정의 로직을 실행할 수 있는 **라이프 사이클 훅**이 호출됨

  - Ex) ```created``` 훅은 인스턴스가 생성된 후 호출됨

    ```js
    new Vue({
        data: {
            a: 1
        },
        created: function () {
            // this는 인스턴스를 가리킴
            console.log('a is: '+ this.a )
        }
    }) // => a is 1
    ```

- 인스턴스 라이프 사이클의 여러 단계에서 호출될 훅

  1. created

  2. mounted

  3. updated

  4. destroyed

- 모든 ```라이프사이클 훅```은 **```this 컨텍스트```가 호출하는 ```Vue 인스턴스```를 가리키며 호출**됨

- 컴포넌트의 사용자 지정 로직은 **라이프사이클 훅으로 분할**

> * options 속성이나 콜백에
>
>   * `created: () => console.log(this.a)`
>   * `vm.$watch('a',newValue => this.Method())`
>
>   와 같은 arrow function 사용을 지양해야 함
>
> * arrow funciton은 `this`를 갖지 않기 때문에 `this`는 다른 변수로 취급되거나 렉시컬하게 호출한 변수를 발견할 때까지 부모 스코프(상위 스코프)에서 해당 변수를 찾음                                                                                                                                                                                                                    
>
> - error
>   - `Uncaught TypeError: Cannot read property of undefined`
>   - ` Uncaught TypeError: this.myMethod is not a function`



<br/>

<br/>



# 4. Lifecycle Diagram

![Lifecycle_Diagram](https://kr.vuejs.org/images/lifecycle.png)

# 1. Vue Instance 만들기

- 모든 vue 앱은 ```Vue``` 함수로 **새 vue 인스턴스**를 만드는 것부터 시작

  ```js
  var vm = new Vue({
      // 옵션
  })
  ```

- Vue 인스턴스를 생성 시 **options 객체**를 전달해야 함

  - 전체 옵션 목록은 [API reference](https://vuejs.org/v2/api/) 에서 확인 가능

- **Vue 앱**

  - ```new Vue```를 통해 만들어진 ```Root Vue Instance```로 구성됨

  - 선택적으로 중첩이 가능

  - 재사용 가능한 **컴포넌트 트리**로 구성됨

  - Ex) Todo 앱의 컴포넌트 트리

    ```bash
    Root Instance
    └─ TodoList
       ├─ TodoItem
       │  ├─ DeleteTodoButton
       │  └─ EditTodoButton
       └─ TodoListFooter
          ├─ ClearTodosButton
          └─ TodoListStatistics
    ```

<br/>

<br/>



# 2. data &  method

### 1) data 객체

- Vue Instance가 생성 될 때 ```data``` 객체에 있는 모든 속성이 Vue의 **반응형 시스템**에 추가됨

- 각 속성 값 변경 시 vue가 반응 -> 새로운 값으로 업데이트

  ```js
  // 데이터 객체
  var data = { a: 1 }
  
  // Vue 인스턴스에 데이터 객체를 추가
  var vm = new Vue({
      data: data
  })
  
  // 인스턴스에 있는 속성은 원본 데이터에 있는 값을 반환
  vm.a === data.a // => true(type, value 모두 일치)
  
  // 인스턴스 속성 값 변경 -> 원본 데이터도 변경
  vm.a = 2
  console.log(data.a); // 콘솔 창에 2 출력
  
  // 원본 데이터 변경 -> 인스턴스 속성 값도 변경
  data.a = 3
  console.log(vm.a); // 콘솔 창에 3 출력
  ```



- 데이터가 변경되면 화면은 다시 렌더링 됨

- ```data```에 있는 속성들은 **인스턴스 생성 시에 존재하는 것들만 반응형**

  - Ex)  다음과 같이 새 속성을 추가했을 경우 **b가 변경되어도 화면은 갱신 x**

    ``` js
    vm.b = 'hi'
    ```

  - 속성이 나중에 필요하고 빈 값의 상태로 시작한다면 초기값을 정해줘야 함.

    - Ex) 추후에 필요한 속성의 초기화

      ```js
      data: {
          newTodoText: '',
          visitCount: 0,
          hideCompleteTodos: false,
          todos: [],
          error: null
      }
      ```

<br/>



### 2) Object.freeze()

- 기존 속성이 변경되는 것을 막음 -> 반응성 시스템이 추적하지 못함

  - Ex) obj의 속성이 변경되는 것을 막는 경우(const)

  ``` js
  var obj = {
      foo: 'bar'
  }
  
  Object.freeze(obj)
  
  new Vue({
      el: '#app',
      data: obj
  })
  ```

  ```html
  <div id="app">
      <p>{{ foo }}</p>
      <!-- obj.foo는 변하지 않음-->
      <button v-on:click="foo = 'baz'">Change it</button>
  </div>
  ```

<br/>



### 3) $

- 데이터 속성 이외의 유용한 **인스턴스 속성 및 메소드**

- **```$```**: **다른 사용자 정의 속성과 구분**하기 위한 *접두어*

  - Ex)

    ```js
    var data = { a: 1 }
    
    var vm = new Vue({
        el: '#example',
        data: data
    })
    
    vm.$data === data // => true
    vm.$el === document.getElementById('example') // => true
    
    // $watch는 인스턴스 메소드
    vm.$watch('a', (newVal, oldVal)=>{
        // newvalue와 oldvalue를 parameter로 갖음
        // vm.a가 변경되면 호출됨
    })
    
    ```

- 인스턴스 속성과 메소드 전체 목록: [API reference](https://vuejs.org/v2/api/)



<br/> <br/>



# 3. Instance Lifecycle Hook

- Vue 인스턴스는 생성 시 일련의 초기화 단계를 거침
  1. 데이터 관찰 설정이 필요한 경우
  2. 템플릿을 컴파일하는 경우
  3. 인스턴스를 DOM에 마운트하는 경우
  4. 데이터가 변경되어 DOM을 업데이트하는 경우

- 위 과정에서 사용자 정의 로직을 실행할 수 있는 **라이프 사이클 훅**이 호출됨

  - Ex) ```created``` 훅은 인스턴스가 생성된 후 호출됨

    ```js
    new Vue({
        data: {
            a: 1
        },
        created: function () {
            // this는 인스턴스를 가리킴
            console.log('a is: '+ this.a )
        }
    }) // => a is 1
    ```

- 인스턴스 라이프 사이클의 여러 단계에서 호출될 훅

  1. created

  2. mounted

  3. updated

  4. destroyed

- 모든 ```라이프사이클 훅```은 **```this 컨텍스트```가 호출하는 ```Vue 인스턴스```를 가리키며 호출**됨

- 컴포넌트의 사용자 지정 로직은 **라이프사이클 훅으로 분할**

> * options 속성이나 콜백에
>
>   * `created: () => console.log(this.a)`
>   * `vm.$watch('a',newValue => this.Method())`
>
>   와 같은 arrow function 사용을 지양해야 함
>
> * arrow funciton은 `this`를 갖지 않기 때문에 `this`는 다른 변수로 취급되거나 렉시컬하게 호출한 변수를 발견할 때까지 부모 스코프(상위 스코프)에서 해당 변수를 찾음                                                                                                                                                                                                                    
>
> - error
>   - `Uncaught TypeError: Cannot read property of undefined`
>   - ` Uncaught TypeError: this.myMethod is not a function`



<br/>

<br/>



# 4. Lifecycle Diagram

![Lifecycle_Diagram](https://kr.vuejs.org/images/lifecycle.png)

