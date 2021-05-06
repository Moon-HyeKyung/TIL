# MVVM 패턴
### 1) 구조
![MVVM_Pattern](https://wikidocs.net/images/page/17701/view-model.png)
- Model-View-ViewModel의 줄임말
  - **Model** : 어플리케이션에서 사용되는 데이터와 그 데이터를 처리하는 부분
  - **View** : 사용자에서 보여지는 UI 부분
  - **View Model** : View를 표현하기 위해 만든 View를 위한 Model, View를 나타내 주기 위한 Model이자 View를 나타내기 위한 데이터 처리를 하는 부분

- 로직과 UI의 분리를 위해 설계된 패턴
- 웹페이지는 DOM과 Javascript로 만들어 짐
  - **View**: DOM, **Model**: Javascript
- 뷰모델이 없는 경우에는 직접 모델과 뷰를 연결해야 하지만 **뷰모델이 중간에서 연결해 주는 것이 MVVM 모델**

<br/>
<br/>

### 2) 동작
MVVM 패턴의 동작 순서는 아래와 같습니다.

1. 사용자의 Action들은 View를 통해 들어오게 됩니다.
2. View에 Action이 들어오면, Command 패턴으로 View Model에 Action을 전달합니다.
3. View Model은 Model에게 데이터를 요청합니다.
4. Model은 View Model에게 요청받은 데이터를 응답합니다.
5. View Model은 응답 받은 데이터를 가공하여 저장합니다.
6. View는 View Model과 Data Binding하여 화면을 나타냅니다.



# Reference
- https://beomy.tistory.com/43
