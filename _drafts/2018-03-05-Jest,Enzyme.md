---
layout: post
title: (javascript) Jest, Enzyme React Component Testing.
categories: [javascript, React, Jest, Enzyme, Test, npm]
tags: [javascript, React, Jest, Enzyme, Test, npm]
#fullview: true
description: Jest, Enzyme을 이용한 React Component Testing.
comments: true
published: false
---

Jest, Enzyme React Component Testing.
===

`타자치는 곰 - `
<br/>


### Jest, Enzyme
* Jest - 페이스북에서 만든 테스팅 프레임워크.
* Enzyme - 에어 비앤비에서 만든 Reactjs 자바스크립트 테스팅 라이브러리.

### 유닛 테스팅?
* 소프트웨어를 기능별로 나누고, 나눈 기능 내부에서 사용되는 함수도 나눠서<br/>
작은 단위로 테스팅 하는것을 의미한다.<br/>
일생생활에 비유해, '커피 끓여 먹기'를 테스팅에 비유하면 아래와 같다.<br/>
  1. 물 끓이기.
  2. 커피를 컵에 담기.
  3. 커피를 담은 컵에 끓인물 붓기.
  4. 저어주기.

* 만든 소프트웨어가 정상적으로 작동하는지 테스팅 하기 위해서, 직접 조작을 하면서 테스팅을 할 수 있다. 하지만, 프로젝트 단위가 커진다면 매번 코드를 수정하거나, 새로만들경우 모든 작업이 제대로 되었는지 일일히 직접 확인한다면 놓치는 부분도 있을 것이며, 비효율적일것이다.<br/>
이런 작업을, 직접 일일히 하는것이 아닌 테스트코드를 작성해 진행 할 수 있는데,<br/>이것을 테스트 자동화라 한다.

> 유닛 테스팅은, 내가 작성한 코드가 다른 코드들을 망가뜨리지 않도록, 적어도 사전에 정의한 상황을 보장해준다.

### React Component Testing.
* 리액트 프로젝트는 컴포넌트 단위 하나하나 테스트 로직을 정해줄 수 있다.<br/>
리액트 컴포넌트를 테스팅할때는 주로 아래와 같은 형식으로 한다.
  1. 특정 props에 따라 컴포넌트가 크래쉬 없이 잘 렌더링 되는지 확인.
  2. 이전에 렌더링했던 결과와, 새로 렌더링한 결과가 일치하는지 확인.
  3. 특정 DOM 이벤트를 시뮬레이트 하여, 원하는 변화가 제대로 발생하는지 확인.
  4. 렌더링된 결과물을 `이미지`로 저장하여 픽셀을 하나하나 확인해서 모두 일치하는지 확인. (이 경우는 `스토리북`을 이용 하는것이 효율적이고 편하다.)

> npm `create-react-app` 을 이용해서 프로젝트를 생성한다.
`create-react-app`에는 기본적인 테스트 환경이 구성 되어있으며, 기본 테스트 파일도 존재 한다.(App.test.js)

* `src/App.test.js
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});
```
> 컴포넌트가 크래쉬 없이 정상적으로 렌더링 되는지를 확인 해 준다.<br/>
실행하기 위해서는 터미널로 프로젝트의 경로에서 `npm test`를 입력하면 된다.

```javascript
Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.526s
Ran all test suites related to changed files.

Watch Usage
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run.
```
> 정상적으로 완료되는것을 확인 할 수 있다.

### 카운터 컴포넌트
> 단순한 카운터 프로그램을 만들어 보자.<br/>
+ 버튼과 - 버튼을 이용해 state에 있는 값을 변경하는 컴포넌트이다.

* src/components/Counter.jsx
```javascript
import React, { Component } from 'react';

class Counter extends Component {
    state = {
        number: 1
    }
    onPlus = ()=>{
        this.setState(({number}) => ({number: number + 1}));
    }
    onMinus = ()=>{
        this.setState(({number}) => ({number: number - 1}));
    }
    render() {
        const {number} = this.state;
        const {onPlus, onMinus} = this;
        return (
            <div>
                <h1>Counter</h1>
                <h2>{number}</h2>
                <button onClick={onPlus}> + </button>
                <button onClick={onMinus}> - </button>
            </div>
        );
    }
}

export default Counter;
```

> 위 컴포넌트를 App에서 렌더링 하자.

* src/App.js
```javascript
import React, { Component } from 'react';
import Counter from './components/Counter.jsx';
class App extends Component {
  render() {
    return (
      <div>
        <Counter />
      </div>
    );
  }
}

export default App;
```

> 터미널에서 `npm start`로 실행 후 화면이 정상적으로 렌더링 되는것을 확인한다.

### NameForm, NameList 컴포넌트
> 이번 컴포넌트는 `NameForm`에 이름을 등록하면 `NameList`에 나타나는 컴포넌트 이다.<br/>
input의 상태값은 `NameForm`에 넣고, `NameList`에 보여줄 이름 목록의 상태값은 `App`에서 `NameList`로 `props`를 통해 전달 하도록 한다.

* src/components/NameForm.jsx
```javascript
import React, { Component } from 'react';

class NameForm extends Component {
    static defaultProps = {
        onSubmit: () => console.warn('onSubmit not defined'),
    }
    state = {
        name: ''
    }
    onChange = (e) => {
        this.setState({
            name: e.target.value
        });
    }
    onSubmit = (e) => {
        const {name} = this.state;
        const {onInsert} = this.props;
        // 이름을 추가하고, name 값 초기화
        onInsert(name);
        this.setState({
            name: ''
        });
        // submit 하면 기본적으로는 페이지가 새로고쳐지는 것을 방지함.
        e.preventDefault(); 
    }
    render() {
        const {onSubmit, onChange} = this;
        const {name} = this.state;
        return (
            <form onSubmit={onSubmit}>
                <label>이름</label>
                <input type="text" value={name} onChange={onChange}/>
                <button type="submit">등록</button>
            </form>
        );
    }
}

export default NameForm;
```

* src/components/NameList.jsx
```javascript
import React, { Component } from 'react';

class NameList extends Component {
    static defultProps = {
        names: []
    }
    renderList(){
        const {names} = this.props;
        const NameList = names.map(
            (name, i) => (<li key={i}>{name}</li>)
        );
        return NameList;
    }
    render() {
        return (
            <ul>
                {this.renderList()}
            </ul>
        );
    }
}

export default NameList;
```

> 위 새로 만든 컴포넌트를 `App`에서 렌더링 한다. 컴포넌트를 추가하면서 `App`에 `state`값과 `onInsert` 메소드를 추가 한다.

* src/App.js
```javascript
import React, { Component } from 'react';
import Counter from './components/Counter.jsx';
import NameForm from './components/NameForm.jsx';
import NameList from './components/NameList.jsx';

class App extends Component {
  state = {
    names: ['웅식', '킴웅식']
  }
  onInsert = (name) => {
    this.setState(({names}) => ({names: names.concat(name)}));
  }
  render() {
    const {names} = this.state;
    const {onInsert} = this;
    return (
      <div>
        <Counter />
        <hr/>
        <h1>이름 목록</h1>
        <NameForm onInsert={onInsert}/>
        <NameList names={names}/>
      </div>
    );
  }
}

export default App;
```

> 컴포넌트를 추가 한 후, 정상적으로 화면이 정상적으로 렌더링 되는것을 확인한다.

### 스탭샷 테스팅
> 컴포넌트를 렌더링하고, 그 결과물을 파일로 저장한다. 그리고, 다음번에 테스팅을 진행하게 되었을때, 이전의 결과물과 일치하는지를 확인한다.<br/>
초기 렌더링 결과를 비교 할 수 있지만, 컴포넌트 내부 메소드를 호출시키고, 다시 렌더링 시켜서 그 결과물도 스냅샷을 저장시켜서, 각 상황을 이전에 렌더링 했던 결과와 일치하는지를 비교 할 수 있다.
<br/>
스냅샷 테스팅을 진행하려면 `react-test-renderer` npm 모듈을 설치 해야 한다.<br/>
설치 후 테스트 코드를 작성 한다.

* src/components/Counter.test.js
```javascript
import React from 'react';
import renderer from 'react-test-renderer';
import Counter from './Counter.jsx';

describe('Counter View', () => {  
    let component = null;

    it("초기 렌더링이 정상적으로 됨.", () => {
        component = renderer.create(<Counter />);
    });

    it('스냅샷 일치함.', () => {
        const snap = component.toJSON();
        expect(snap).toMatchSnapshot();
    });
});
```

* 테스트를 할 때, 주로 사용하는 함수 이다.
  * **describe**
    * `테스트 단위 구분`으로 생각할 수 있다.(각 화면 단위)
    * **describe** 안에 여러개의 **describe**를 넣을 수 있다.
  * **it**
    * 테스트 단위 내의 `기능 단위 구분`으로 생각 할 수 있다.(기능 테스트 단위)
    * **describe** 안에 여러개의 **it**을 넣을 수 있다.
  * **expect**
    * 특정값이 예상한 값으로 나왔는지 확인 할 때 사용한다.

> `describe` 와 `it`에는 두개의 파라미터가 필요한데, 첫번째 파라미터에는 기능 및 테스트 항목의 설명을 넣고, 두번째 파라미터에 실제 테스트 코드를 작성한다.

* 테스트 코드를 작성/수정 후 저장을 하게되면, 자동으로 테스트가 진행되며, 스냅샷이 생성된다. 스냅샷은 렌더링하는 파일의 경로의 `__snapshots__` 디렉토리에 저장되며, 확장자는 `.snap`이다.

* 테스트를 실행하고 생성된 스냅샷 파일을 열어보자.
```javascript
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`Counter View 스냅샷 일치함.1 1`] = `
<div>
  <h1>
    Counter
  </h1>
  <h2>
    1
  </h2>
  <button
    id="plus"
    onClick={[Function]}
  >
     + 
  </button>
  <button
    id="minus"
    onClick={[Function]}
  >
     - 
  </button>
</div>
`;
```
> 렌더링 한 결과물이 스냅샷으로 저장된것을 확인 할 수 있고,<br/>
작성했던 코드보다 간소화 되어있는 것을 볼 수 있다.<br/>
화면을 변경 하고 테스트를 할 경우 어떤 변화가 있는지 확인 해 보자.

* 작성했던 코드의 내용을 변경 한 후 테스트 결과이다.
```javascript
    - Snapshot
    + Received

    @@ -1,8 +1,8 @@
     <div>
       <h1>
    -    Counter
    +    카운터를 세보자.
       </h1>
       <h2>
         1
       </h2>
       <button

      at Object.it (src/components/Counter.test.js:17:22)
      at Promise.resolve.then.el (node_modules/p-map/index.js:46:16)
      at process._tickCallback (internal/process/next_tick.js:109:7)

 PASS  src\App.test.js

Snapshot Summary
 › 1 snapshot test failed in 1 test suite. Inspect your code changes or press `u` to update them.

Test Suites: 1 failed, 1 passed, 2 total
Tests:       1 failed, 2 passed, 3 total
Snapshots:   1 failed, 1 total
Time:        1.597s
Ran all test suites.

Watch Usage: Press w to show more.
```

> 기존의 스냅샷과 비교한 결과 다른점이 발견되어 테스트 실패한것을 볼 수 있다.<br/> 위 상태에서 엔터를 누르면 다시 테스트를 진행하며, `U`키를 누르면 변경된 스냅샷으로 업데이트 할 수 있다. 단점은, 스냅샷을 업데이트 하는데에 제약이 없기 때문에 오류이지만 업데이트를 해 버린다면, 다음 테스트에는 오류난 시점이 정상으로 되버릴 것이다.