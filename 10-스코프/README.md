# 13장 스코프

## 13.1 스코프란?

- #### 스코프는 식별자가 유효한 범위를 말한다. 

- **함수의 경우**

  **함수의 매개변수**는 함수 몸체 내부에서만 참조할 수 있다. 참조할 수 있는 유효범위(매개변수의 스코프)가 함수 몸체 내부로 한정되기 때문이다.

```javascript
function add(x, y) {
  // 매개변수는 함수 몸체 내부에서만 참조할 수 있다.
  // 즉, 매개변수의 스코프(유효범위)는 함수 몸체 내부다.
  console.log(x, y); // 2 5
  return x + y;
}

add(2, 5);

// 매개변수는 함수 몸체 내부에서만 참조할 수 있다.
console.log(x, y); // ReferenceError: x is not defined
```

- **변수의 경우**

  변수는 코드의 가장 바깥 영역뿐 아니라 코드 블록이나 함수 몸체에서도 선언할 수 있다. 

  변수는 **자신이 선언된 위치**에 의해 자신이 **유효한 범위**, 즉 **다른 코드가 변수 자신을 참조할 수 있는 범위**가 결정된다. (식별자도 동일)

  즉, 모든 식별자(변수 이름, 함수 이름, 클래스 이름 등)는 자신이 선언된 위치에 의해 다른 코드가 식별자 자신을 참조할 수 있는 유효범위가 결정된다. 이를 스코프라 한다. 

```javascript
var x = 'global';

function foo() {
  var x = 'local';
  console.log(x); // local 
}

foo();

console.log(x); // global
```

- #### 스코프는 자바스크립트 엔진이 식별자를 검색할 때 사용하는 규칙이다.

- 식별자 결정 : 자바스크립트 엔진은 이름이 같은 두 변수 중 어떤 변수를 참조해야할 것인지를 결정한다. 

- 코드의 문맥과 환경

  자바스크립트 엔진은 코드를 실행할 때 **코드의 문맥(context)** 을 고려한다.

  렉시컬 환경(lexical environment) : 코드가 어디서 실행되며 주변에 어떤 코드가 있는지

  코드의 문맥(context) : 렉시컬 환경으로 이루어짐

  실행 컨텍스트(execution context) : 코드의 문맥을 구현한 것. 모든 코드는 실행 컨텍스트에서 평가되고 실행된다. 

- #### 스코프는 네임스페이스다.

- 스코프를 통해 식별자인 변수 이름의 충돌을 방지하여 같은 이름의 변수를 사용할 수 있게 한다. 스코프 내에서 식별자는 유일해야하지만, 다른 스코프에는 같은 이름의 식별자를 사용할 수 있다.

  ```javascript
  function foo() {
    var x = 1;
    // var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
    // 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
    var x = 2;
    console.log(x); // 2
  }
  foo();
  
  function bar() {
    let x = 1;
    // let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
    let x = 2; // SyntaxError: Identifier 'x' has already been declared
  }
  bar();
  ```



## 13.2 스코프의 종류

코드는 전역과 지역으로 구분할 수 있다.

| 구분 | 설명                  | 스코프      | 변수      |
| ---- | --------------------- | ----------- | --------- |
| 전역 | 코드의 가장 바깥 영역 | 전역 스코프 | 전역 변수 |
| 지역 | 함수 몸체 내부        | 지역 스코프 | 지역 변수 |

변수는 자신이 전언된 위치(전역/지역)에 의해 자신이 유효한 범위인 스코프(전역 스코프/지역 스코프)가 결정된다. 

### 13.2.1 전역과 전역 스코프

- 전역 변수는 어디서든지 참조할 수 있다.

### 13.2.2 지역과 지역 스코프

- 지역이란 함수 몸체 내부를 말한다.

```javascript
var x = 'global';

function foo() {
  var x = 'local';
  console.log(x); // local 
}

foo();

console.log(x); // global
```

foo 함수 내부에서 선언된 x는 **지역변수**다. 지역 변수 x는 자신의 지역 스코프인 함수 foo 내부에서만 참조할 수 있다. 하지만 지역 변수 x를 전역 또는 foo 함수 내부 이외의 지역에서 참조하면 **참조 에러**가 발생한다.

그런데 foo 함수 내부에서 선언된 x 변수 이외에 이름이 같은 **전역 변수** x가 존재한다. 이 때 foo 함수 내부에서 x 변수를 참조하면 전역 변수 x를 참조하는 것이 아니라 foo 함수 내부에서 선언된 x 변수를 참조한다. 이는 자바스크립트 엔진이 **스코프 체인**을 통해 참조할 변수를 검색(identifier resolution)했기 때문이다.



## 13.3 스코프 체인

- **중첩함수** : 함수 몸체 내부에서 정의한 함수
- **외부함수** : 중첩함수를 포함하는 함수

- **스코프**는 함수의 중첩에 의해 **계층적 구조**를 갖는다.
- 외부함수의 지역 스코프를 중첩 함수의 상위 스코프라 한다. 
- 모든 지역 스코프의 최상위 스코프는 **전역 스코프**다.
- **스코프 체인**은 물리적인 실체로 존재한다.
  - 자바스크립트 엔진은 코드를 실행하기에 앞서 **렉시컬 환경**을 생성한다.  
  - **변수 선언**이 실행되면 변수 식별자가 렉시컬 환견에 키로 등록된다.
  - **변수 할당**이 일어나면 렉시컬 환경의 변수 식별자에 해당하는 값을 변경한다. 
  - **변수의 검색**도 렉시컬 환경 상에서 이루어진다.
  - **변수를 참조**할 때 자바스크립트 엔진은 **스코프 체인**을 통해 참조하는 코드의 스코프에서 시작하여 상위 스코프 방향으로 이동하며 선언된 변수를 검색한다. 
- **스코프 체인**은 실행 컨텍스트의 렉시컬 환경을 단방향으로 연결 한 것이다. 
  - **전역 렉시컬 환경** : 코드가 로드되면 곧바로 생성된다.
  - **함수의 렉시컬 환경** :  함수가 호출되면 곧바로 생성된다.

### 13.3.1 스코프 체인에 의한 변수 검색

- 상위 스코프에서 유효한 변수는 하위 스코프에서 자유롭게 참조할 수 있지만 하위 스코프에서 유효한 변수를 상위 스코프에서 참조할 수 없다.
- 스코프 체인으로 연결된 스코프의 계층적 구조는 부자관계로 이뤄진 상속과 유사하다. (상속 : 부모의 자산을 자식이 자유롭게 사용할 수 있지만 자식의 자산을 부모가 사용할 순 없다.)

### 13.3.2 스코프 체인에 의한 함수 검색

- 배경지식 : 함수 선언문으로 함수를 정의하면 런타임 이전에 함수 객체가 먼저 생성된다. 그리고 자바스크립트 엔진은 함수 이름과 동일한 이름의 식별자를 암묵적으로 선언하고 생성된 함수 객체를 할당한다.

- 함수도 식별자에 할당되기 때문에 스코프를 갖는다. 함수는 식별자에 함수 객체가 할당된 것 외에 일반 변수과 다를 게 없다. 따라서 스코프는 "**식별자를 검색하는 규칙**"이다.



## 13.4 함수 레벨 스코프

지역은 함수 몸체 내부를 말하고 지역은 지역 스코프를 만든다. 이는 코드 블록이 아닌 **함수에 의해서만 지역 스코프가 생성된다**는 의미다.

- 블록 레벨 스코프 : 모든 코드 블록(if, for, while, try/catch 등)이 지역 스코프를 만든다.
- 함수 레벨 스코프 : var 키워드로 선언된 변수는 오로지 함수의 코드 블록(함수 몸체) 만을 지역 스코프로 인정한다.

```javascript
var x = 1;

if (true) {
  // var 키워드로 선언된 변수는 함수의 코드 블록(함수 몸체)만을 지역 스코프로 인정한다.
  // 함수 밖에서 var 키워드로 선언된 변수는 코드 블록 내에서 선언되었다 할지라도 모두 전역 변수다.
  // 따라서 x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
  // 이는 의도치 않게 변수 값이 변경되는 부작용을 발생시킨다.
  var x = 10;
}

console.log(x); // 10
```



## 13.5 렉시컬 스코프 

```javascript
var x = 1;

function foo() {
  var x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```

- 자바스크립트는 렉시컬 스코프를 따른다.
- **렉시컬 스코프**(정적 스코프) : 상위 스코프가 동적으로 변하지 않고 함수 정의가 평가되는 시점에 상위 스코프가 정적으로 결정된다. 
- 즉, 함수를 어디서 정의했는지에 따라 상위 스코프를 결정한다. 
- 함수 정의(함수 선언문 또는 함수 표현식)가 실행되어 생성된 함수 객체는 결정된 상위 스코프를 기억한다.