## 서론

평소에 emmet을 사용하긴 했지만 대충 클래스 이름을 포함해서 태그를 작성하는 것으로만 사용했는데, 익숙하게 사용할 수 있으면 좋을 것 같아 정리해보려고 한다.

## Emmet 이란?

HTML과 CSS 워크플로우를 크게 개선할 수 있는 에밋은 웹 개발자를 위한 필수 툴킷이라고 설명한다.

강력한 자동완성으로 HTML 작성 속도를 향상시켜준다.

### 다양한 연산자들

다양한 연산자를 중첩, 체이닝해서 사용할 수 있다. 기본적으로 타이핑 후에 tab을 눌러서 작성한다.

#### Child : > (자식요소)

<div> 안에 <ul>안에 <li>태그가 있는 상태로 자동완성 시켜줌

```
<!-- div>ul>li -->

<div>
  <ul>
      <li></li>
  </ul>
</div>
```

#### Sibling : + (형제요소)

같은 레벨으로 태그를 작성해줌

```
<!-- div+p+bq -->

<div></div>
<p></p>
<blockquote></blockquote>
```

#### Clime-up : ^ (등반, > 연산자를 써서 자식레벨로 내려갔던걸 다시 위로 올릴수 있음)

말로 설명하기 힘드니 한번 사용해보자!

```
<!--div+div>p>span+em-->

<div></div>
<div>
    <p><span></span><em></em></p>
</div>
<!-- 여기서 p 태그의 자식태그를 다 생성하고나서, 형제로 항목을 추가 하고싶으면 -->

<!--div+div>p>span+em^p  ^p 추가-->

<div></div>
<div>
    <p><span></span><em></em></p>
    <p></p>
</div>
```

원하는 만큼 ^를 중첩해서 사용할 수 있다. ex) ^^(2단계 위로 이동), ^^^(3단계 위로 이동)

#### Multiplication : \* (곱하기), Item Numbering : $

\* 보통 <ul>태그안에 여러개의 <li>태그를 작성할때 이용함

$ 도 같이 자주 쓰임 $사인 자리에 1부터 숫자가 들어간다. 중첩해서 $$(01, 02, 03) $$$(001,002,003) 사용가능

$뒤에 @-을 사용하면 역방향(큰 숫자부터 1으로 줄어든다.)

$뒤에 @n(n은 숫자)를 사용하면 1부터가 아닌 n부터 시작한다.

```
<!-- ul>li*3 -->

<ul>
  <li></li>
  <li></li>
  <li></li>
</ul>

<!-- ul>li.item$*3 -->
<ul>
  <li class="item1"></li>
  <li class="item2"></li>
  <li class="item3"></li>
</ul>
```

#### #, . 아이디와 클래스

css 연산자와 동일하다

```
<!--div.container-->
<div class="container"></div>

<!--div#container-->
<div id="container"></div>

<!--div#footer.class1.class2.class3-->
<div id="footer" class="class1 class2 class3"></div>
```

#### Grouping : ( ) (그룹화)

말그대로 사칙연산에서 쓰는 괄호와 같음

```
<!-- div>(header>ul>li*2>a)+footer>p -->

<div>
    <header>
      <ul>
        <li><a href=""></a></li>
        <li><a href=""></a></li>
      </ul>
    </header>
    <footer>
      <p></p>
    </footer>
</div>

<!-- div>header>ul>li*2>a+footer>p 괄호가 없을 때-->
<div>
  <header>
    <ul>
      <li>
        <a href=""></a>
        <footer>
           <p></p>
        </footer>
      </li>
      <li>
        <a href=""></a>
        <footer>
          <p></p>
        </footer>
      </li>
    </ul>
  </header>
</div>
```

#### Text : { } 중괄호 안에 요소에 들어간 텍스트를 추가할 수 있음

```
<!-- button{Click Me}.btn-->

<button class="btn">Click Me</button>
```

### Emmet 치트시트

[Cheat Sheet (emmet.io)](https://docs.emmet.io/cheat-sheet/)

[Cheat Sheet

Download cheat sheet as printable PDF A5

docs.emmet.io](https://docs.emmet.io/cheat-sheet/)