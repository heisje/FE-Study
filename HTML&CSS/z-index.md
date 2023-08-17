# Z-INDEX

위치 지정 요소와, 그 자손 또는 하위 플렉스 아이템의 Z축 순서를 지정한다. 



### 값은 auto와 Integer로 나뉜다 

- auto : 박스가 새로운 쌓음 맥락을 생성하지 않고, 현재 쌓음 맥락에서의 위치는 부모 요소와 동일하다. 
- <Integer> : 현재 쌓음 맥락에서의 위치로 이 값을 사용하며, 자신만의 쌓음 맥락을 생성하고, 해당 맥락에서 자신의 위치를 0으로 설정, 이로 인해 `자손의 z-index를 자기 외의 바깥 요소`와 비교하지 않는다.

```javascript
/*<integer> 값*/
z-index: 0;
z-index: 3;
z-index: 289; 
z-index: -1; /* 음수 값으로 우선순위를 낮출 수 있음 */
```



## z-index 가 없을 때 요소 간 상대적 깊이 ? 

`z-index` 속성에 대해서 알아보기 전에 먼저 `z-index` 가 없을 때 어떻게 요소가 배치 되는지, 어떻게 결정되는지에 대해 이해 해보자.

`z-index` 를 남발하면 나중에 유지 보수시에 어려움을 겪을 수 있기 때문에 가급적이면 `z-index` 속성을 사용하지 않는 편이 낫다. 

기본적으로 `z-index` 속성이 적용되지 않은 요소 간에는 HTML 문서 상에서 나중에 나오는 요소가 먼저 나오는 요소보다 위에 올라 오도록 되어 있다. 

```html
<div class="first">1</div>
<div class="second box">2</div> 
```

```css
.first{
  background:yellow;
}

.second{
  background:red;
  margin-top:-50px;
  margin-left:50px;
}
```

이런 코드가 있다면 노란색 박스인 first 위로 빨간 박스인 second가 올라오는 것을 볼 수 있다. 

하지만 `position` 속성이 `relative` 나 `absolute` , `fixed` , `sticky` 로 적용된 요소가 나타나면 겹치는 순서가 바뀔 수 있다. 

예를 들어, 첫 번째 상자의 `position` 속성을 `relative` 로 바꿔주면, first 노란 박스가 위로 올라오는 것을 볼 수 있다.  

만약, 두 번째 상자의 속성 또한 `relative` 로 바꿔주면, 다시 second 빨간 박스가 위로 올라오게 된다. 

### 정리 

- `position` 속성이 `static` 이 아닌 요소는 무조건 `position` 속성이 `static`인 속성 위로 올라온다. 
- `position` 속성이 `static` 인 요소 간에는 HTML 문서 상에서 나중에 나오는 요소가 먼저 나오는 요소 위로 올라온다. 
- `position` 속성이 `relative` 나 `absolute`,`fixed`,`sticky` 인 요소 간에는 HTML 문서 상에서 나중에 나오는 요소가 먼저 나오는 원소 위로 올라온다. 

## z-index가 있을 때 상대적 깊이 

`z-index`를 사용하면 다른 기본적인 규칙을 무시하고 해당 요소를 먼저 나오게 할 수 있다. 

브라우저는 `z-index` 가 낮은 요소를 먼저 그리고, `z-index` 가 높은 요소를 나중에 그리기 때문에 요소가 겹처 있을 경우`z-index` 속성 값이 큰 요소가 `z-index` 속성값이 작은 요소의 일부를 가리거나 전체를 덮을 수 있다. 

여기서 간과하기 쉬운 부분은 `position` 속성이 `static` 인 요소에는 `z-index` 속성이 아무 효력을 내지 못한다. 

왜냐하면 `position` 속성이 `static` 인 요소는 `z-index` 속성이 `auto` , 즉 0 으로 고정되어 있기 때문 

`z-index` 속성이 음수인 경우에는 브라우저가 가장 먼저 그리게 되기 때문에 `position` 이 `static` 인 요소보다도 더 뒤에 나타나게 된다. 

### 정리 

- `position` 속성이 `static` 인 요소에는 `z-index` 속성이 0으로 고정되어 있으며 바꿀 수 있다. 
- `z-index` 속성이 양수로 설정된 요소는 `position` 속성이 `static` 인 요소보다 앞으로 올라온다. (나중에 그려진다)
- `z-index` 속성이 음수로 설정된 요소는 `position` 속성이 `static` 인 요소보다 뒤로 내려간다. (먼저 그려진다)
- `z-index` 속성이 설정된 요소 간에는 크면 클 수록 앞으로 올라오고, 작으면 작을수록 뒤로 내려간다. 

## stacking context:z-index가 비교되는 범위

`z-index` 와 관련해서 가장 오해하기 쉬운 부분은 `z-index` 가 HTML 문서 전체 범위에서 비교 된다고 생각하는 것 

`z-index`는 특정 범위 내에서 비교되며, 이것을 CSS에서는 `stacking context` 라고 부른다. 

```html
<div class="wrapper">
  <div class="first">1</div>
</div>
<div class="second">2</div>
```

```css
.first{
  z-index:100;
  background:yellow;
  position:relative;
  top:50px;
  left:50px;
}

.second { 
	z-index: 2;
  background:red;
  position:relative
}

.wrapper{
  z-index:1;
  position:relative
}
```

z-index를 값이 높으면 위로 올라온다 라고만 오해하고 있으면 다음과 같은 실수를 할 수 있다. 

위 코드를 실행해보면 z-index가 2인 second 박스가 z-index가 100인 first 박스보다 위로 올라오게 된다. 

이것이 바로 stacking context에 있다. 다시 말해 두 상자의 `z-index`값이 서로 다른 범위에서 비교되기 때문 

같은 레벨에 있는 요소 끼리 비교 하기 때문이다. 

쉽게 말하면 부모 요소에 설정된 `z-index` 값이 자식 요소의 `z-index` 값 앞에 붙어 있다고 생각하면 좋을 것 같다. 

```html
<!-- z-index : 1 -->
<div class="wrapper">
  <!-- z-index : 1.100 -->
  <div class="first">
    1
  </div>
</div>
<!-- z-index : 2-->
<div class="second">
  2
</div>
```

`stacking context`와 관련해서 한 가지 주의할 점은 `stacking context` 구조는 반드시 HTML 문서의 DOM 구조와 일치하지 않는다는 것 

## 여담

`z-index` 에 대해서 제대로 이해하지 않고 `z-index`를 남용하게 되면 정말 유지보수에 어려움을 겪을 수 있다. 

얼마나 악명이 높으면 CSS 커뮤니티에서는 이 문제를 z-index 전쟁이라고 부르기도 한다. 

때문에 `z-index`를 제대로 이해하고 사용하지 않을 수 있으면 최대한 사용하지 않는게 좋을 것 같다. 



## 최종정리

CSS의 속성 중에 하나로 Element의 Z축 순서를 지정하는 속성이다. 

기본적으로 HTML 문서상 먼저 나오는 Element가 먼저 쌓이는 구조를 가지고 있으며, absolute,fixed,relative,sticky와 같이 position이 static이 아닌 요소가 static인 요소보다  위에 올라온다. 

여기서 static이 아닌 요소에서 z-index를 설정해주면 기본적인 규칙을 무시할 수 있다. 

주의해야 할점은 stacking context 이다. 

z-index는 전체 범위에서 비교하는게 아니라 특정 범위에서 비교된다. 같은 레벨 끼리 비교하는 규칙을 가지기 때문에 조심해야한다. 

이러한 복잡한 규칙을 가지고 있기 때문에 이를 남발하면 유지보수에서 어려움을 겪을 수 있다. 

때문에 최대한 사용하지 않되 불가피한 경우에만 사용해주도록 하자 





