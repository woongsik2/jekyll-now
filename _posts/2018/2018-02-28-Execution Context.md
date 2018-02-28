실행 컨텍스트(Execution Context)
===

`타자치는 곰 - `
<br/>


### 실행 컨텍스트(Execution Context)

* `전역 컨텍스트` 와 `함수 컨텍스트`.<br/>
  * 코드를 처음 실행 하는 순간 모든것을 포함하는 `전역 컨텍스트`가 생긴다.<br/>
  (실행 하는 순간은, 브라우저가 스크립트를 로딩해서 실행하는것을 뜻한다.)<br/>
  * `전역 컨텍스트`는 모든것을 관리하는 환경이며, 페이지가 종료될 때까지 유지된다.<br/>
  * 함수를 호출 할 때마다 `함수 컨텍스트`가 하나씩 생긴다.

* 컨텍스트의 네 가지 원칙<br/>
  * `전역 컨텍스트` 하나 생성 후, 함수 호출시 마다 `컨텍스트`가 생성된다.
  * `컨텍스트` 생성 시, `컨텍스트`안에 변수객체(`arguments`,`variable`),`scope chain`, `this`가 생성된다.
  * `컨텍스트` 생성 후 함수가 실행되는데, 사용되는 변수들은 `변수객체` 안에서 값을 찾고, 없다면 `스코프체인`으로 올라가며 찾는다.
  * 함수 실행이 끝나면, 해당 `컨텍스트`는 사라진다.(`클로저`는 제외) 페이지가 종료되면, `전역 컨텍스트`가 사라진다.

#### 예제코드
```javascript
var result = "hello";
function func(s){
    console.log(s + " " + result);
}
function func1(){
    var result = "Bye";
    console.log(result);
    func("Hi,");
}
func1();
```

### 전역 컨텍스트
* `전역 컨텍스트`가 생성된 후 `변수객체`,`scope chain`, `this`가 생성된다.<br/>
`전역 컨텍스트`는 `arguments`가 없고, `variable`은 해당 스코프들의 변수들을 말한다.<br/>
위 코드에서는 `result`, `func`, `func1` 이 있다.<br/>
* `scope chain`은 자기 자신인 전역 변수객체이다.<br/>
  * `this`는 따로 설정되어 있지 않으면 `window`이다.<br/>
  * `this`를 바꾸는 방법은 `new`를 호출 하는 것이다.<br/>
  또는, 함수에 다른 `this`를 `bind`할 수 있다.
* 위 코드를 객체 형식으로 표현하면 아래와 같다.<br/>
```javascript
'전역 컨텍스트': {
    변수객체 : {
        arguments: null,
        variable: ['name', 'func', 'func1']
    },
    scopechain: ['전역 변수객체'],
    this: window
}
```
* 코드를 위에서부터 실행하게 되면, `func`와 `func1`은 `호이스팅` 때문에 선언과 동시에 대입이 된다. 그 후, `variable`의 `result`에 'hello'가 대입된다.
```javascript
variable: [{name: 'hello'}, {func: Function}, {func1: Function}]
```

### 함수 컨텍스트
* 위 예제코드에서 `func1()`를 하는 순간, 새로운 컨텍스트인 `func1()` `함수 컨텍스트`가 생성된다. 생성되었던 `전역 컨텍스트`는 그대로 유지 되고, `arguments`는 없고, `variable`은 `result`만 존재 한다. `scope chain`은 `func1` `변수객체`와 상위의 `전역 변수객체`이다. `this`는 따로 설정하지 않았으므로 `window`이다.

```javascript
'func1 컨텍스트': {
    변수객체: {
        arguments: null,
        variable: ['result']
    },
    scopechain: ['func1 변수객체', '전역 변수객체'],
    this: window
}
```
> `func1`를 호출한 후 차례로 실행 하는데, `variable`의 `result`에 `Bye`를 대입해주고, `console.log(result);` 의 `result` 변수는 `func1 컨텍스트` 안에서 찾으면 된다.<br/>
`variable`에 `result`가 `Bye`로 설정되어있으니, `result`가 콘솔에 찍히게 된다.<br/>
그 다음 `func("Hi,");`은 `func1 컨텍스트`안에서 `func` 변수를 찾을 수 없다.<br/>
찾을 수 없는경우, `scope chain`에 의해 상위 `변수객체`에서 찾는다. 상위인 `전역 변수객체`에서 찾고, `variable`에 `func`함수를 호출한다.<br/><br/>
`func`함수가 호출 된 후에는 `func 컨텍스트`도 생성된다. `arguments`는 `s = Hi,`이고, `scope chain`은 `func 스코프`와 `전역 스코프` 이다.<br/>
여기서 중요한것은, `lexical scoping`에 따라 `func` 함수의 `scope chain`은 선언시 이미 정해져 있다. `func1 스코프`는 `func 컨텍스트`의 `scope chain`이 아니다.<br/>
`variable`은 없고, `this`는 `window`이다.

```javascript
'func 컨텍스트': {
    변수객체: {
        arguments: [{s: 'Hi,'}],
        variable: null
    },
    scopechain: ['func 변수객체', '전역 변수객체'],
    this: window
}
```
> 컨텍스트가 생성된 후 함수가 실행 되는데, 나중에 생긴 `func`가 가장 먼저 실행된다.<br/>
`func` 함수 안에 `console.log(s + " " + result);`의 `s`와 `result`변수는 `func`컨텍스트에서 찾으면 된다. `s`는 `arguments`에서 찾을 수 있고, `result`는 `func 변수객체`에 없으므로, `scope chain`으로 `전역 스코프`에서 찾으면 된다.<br/>
`전역 변수객체`로 올라가면, `variable`에 `result`값이 `hello`라고 되어있기 때문에, `Hi, Hello`가 된다.<br/>
`func 컨텍스트`는 `func1 컨텍스트`와 전혀 관계가 없는것이다.<br/>
`func` 함수 종료 후 `func 컨텍스트`가 사라지고, `func1` 함수의 실행이 마무리가 된다. `func 컨텍스트`가 사라지고, `func1 컨텍스트`도 다음으로 사라지며, 마지막에 `전역 컨텍스트`도 사라진다.