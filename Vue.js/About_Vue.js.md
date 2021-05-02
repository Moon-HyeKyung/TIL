# vue.js
- 2014년 release를 시작으로 발전하고 있는 자바스크립트 프레임워크
- 컨트롤러 대신 뷰 모델을 가지는 MVVM(Model-View-ViewModel) 패턴을 기반으로 디자인
- JavaScript 객체를 Vue 인스턴스 data옵션으로 정의 할 때 모든 속성들을 Object.defineProperty를 사용하여 Getter/Setter로 변환
  - ES5에서 새로 등장한 기능 -> 하위 호환 불가능(IE8이하 지원 X)

<br/>
<br/>

# 특징
1. MVVM(Model-View-ViewModel) 패턴을 사용
2. Virtual DOM을 사용
3. Angular, React에 비해 매우 작고 가벼우며 복잡도가 낮음
    - 그 이유:
4. Template과 Component를 사용 -> 재사용이 가능한 User Interface를 묶고 View Layer를 정리하여 사용

<br/>
<br/>

# MVVM 패턴
![MVVM_Pattern](https://wikidocs.net/images/page/17701/view-model.png)
- Model-View-ViewModel의 줄임말
- 로직과 UI의 분리를 위해 설계된 패턴
- 웹페이지는 DOM과 Javascript로 만들어 짐
  - **View**: DOM, **Model**: Javascript
- 뷰모델이 없는 경우에는 직접 모델과 뷰를 연결해야 하지만 **뷰모델이 중간에서 연결해 주는 것이 MVVM 모델**

<br/>
<br/>

# 가상 돔(Virtual DOM)
- DOM 요소가 많아지면 Javascript로 DOM을 핸들링하는 일이 무거워 짐   
  ->DOM과 비슷한 구조로 Javascript를 생성
- 진짜 DOM과 달리 메모리에 올라가 있기 때문에 비교적 매우 빠른 성능을 보임
- Vue js가 가상돔을 수정하면 DOM을 수정하는 것보다 빠름
  - <이유 찾기>
- Vue는 가상돔이 변경될 때매다 진짜 DOM과 비교해서 차이를 찾고 차이난 부분의 DOM만 수정하는 동작을 함

<br/>
<br/>

# 컴포넌트(Component)
![commponent](https://wikidocs.net/images/page/17701/component.png)
- 화면에 비춰지는 뷰의 단위를 쪼개어 재사용이 가능한 형태로 관리하는 것
- Vue 인스턴스: Vue에서 컴포넌트는 미리 정의된 옵션(attribute)을 가진 것
- 전역 등록과 지역 등록이 존재

<br/>

![component.vue](https://wikidocs.net/images/page/17701/vue-component-structure.png)
- Vue는 재사용이 가능한 컴포넌트로 웹페이지 구성 가능
- 확장자가 .vue인 단일 파일에 HTML, JS, CSS코드로 구성해 사용

<br/>
<br/>

# 생명주기(LifeCycle)
![Vue_LifeCycle](https://wikidocs.net/images/page/17701/vue%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4_%EB%9D%BC%EC%9D%B4%ED%94%84_%EC%82%AC%EC%9D%B4%ED%81%B4.jpg)
- **Creation**: 컴포넌트 초기화 단계
- **Mountion**: DOM 작성 단계
- **Updating**: 상태 변화로 인한 렌더링 단계
- **Destruction**: 소멸 단계

<br/>
<br/>

# Vue 인스턴스
new Vue로 선언해 만들어진 객체
  - el: 태그에 지정한 ID, class명, tag명으로 해당 태그와 vue 인스턴스를 연결하는 옵션
  - data: key와 value를 지정하는 json형식으로 데이터 입력 옵션
  - computed: getter/setter 지정하는 옵션
  ```vue
  <div id = “app”
    {{message}}
  </div>


  <script>
    var model = {
        message : “뷰 생성”
    }

    new Vue({  
    //옵션
        el : ‘#app’,
        data : model
    })

  </script>
  ```
 ![code to MVVM](https://user-images.githubusercontent.com/75727995/116805855-2faedb80-ab64-11eb-9ddf-dccebe693f4e.png)
