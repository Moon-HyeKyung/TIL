# What is Css Processor?
- CSS 전처리기
- Sass(SCSS), Less, Stylus 등..
- CSS가 동작하기 전에 사용하는 기능
- 전처리기는 CSS 문법과 유사하지만 **선택자의 중첩(Nesting)이나 조건문, 반복문 다양한 단위(Unit)의 연산** 등 표준 CSS보다 훨씬 많은 기능을 사용해서 편리하게 작성 가능
<br/>
<br/>

# How to use?
웹에서는 CSS만 동작합니다.
Sass, Less, Stylus 같은 전처리기는 직접 동작 시킬 수 없습니다.
1. CSS는 불편하므로 우선 전처리기로 코딩합니다.
2. 웹에서 동작 가능한 표준 CSS로 컴파일합니다.
3. 컴파일된 CSS를 웹에서 사용합니다.

# How to Compile?
- 전처리기 종류마다 방법이 조금씩 다릅니다.
- Sass(SCSS):sass style.scss style.css
- Less: lessc style.less style.css
- Stylus: stylus style.styl style.css

# Difference between Sass and SCSS
||Sass|SCSS|
|----|----|----|
|Meaning|Syntacitically Awesom Style Sheets| Sassy CSS|
|Syntax|- { } (중괄호)와 ; (세미콜론) 사용 x| - { } (중괄호)와 ; (세미콜론) 사용 o|

# Comparision of Sass, Less and Stylus
![comparison of CSS Procecssors](https://user-images.githubusercontent.com/75727995/113090849-a2572f00-9225-11eb-88ad-deb6e9d6e32d.png)

