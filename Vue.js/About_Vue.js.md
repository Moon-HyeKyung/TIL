# vue.js
- 2014년 release를 시작으로 발전하고 있는 자바스크립트 프레임워크
- 컨트롤러 대신 뷰 모델을 가지는 MVVM(Model-View-ViewModel) 패턴을 기반으로 디자인
- JavaScript 객체를 Vue 인스턴스 data옵션으로 정의 할 때 모든 속성들을 Object.defineProperty를 사용하여 Getter/Setter로 변환
  - ES5에서 새로 등장한 기능 -> 하위 호환 불가능(IE8이하 지원 X)

# 특징
- MVVM(Model-View-ViewModel) 패턴을 사용
- Virtual DOM을 사용
- Angular, React에 비해 매우 작고 가벼우며 복잡도가 낮음
  - 그 이유:
- Template과 Component를 사용 -> 재사용이 가능한 User Interface를 묶고 View Layer를 정리하여 사용

# MVVM 패턴
![MVVM_Pattern](https://wikidocs.net/images/page/17701/view-model.png)
- Model-View-ViewModel의 줄임말
- 로직과 UI의 분리를 위해 설계된 패턴
- 웹페이지는 DOM과 Javascript로
