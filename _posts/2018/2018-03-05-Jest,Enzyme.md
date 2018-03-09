---
layout: post
title: (javascript) Jest, Enzyme React Component Testing.
categories: [javascript, React, Jest, Enzyme, Test, npm]
tags: [javascript, React, Jest, Enzyme, Test, npm]
#fullview: true
description: Jest, Enzyme을 이용한 React Component Testing.
comments: true
published: true
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
\+ 버튼과 - 버튼을 이용해 state에 있는 값을 변경하는 컴포넌트이다.

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

### 스냅샷 테스팅
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
 FAIL  src\components\Counter.test.js
  ● Counter View › 스냅샷 일치함.

    expect(value).toMatchSnapshot()

    Received value does not match stored snapshot 1.

    - Snapshot
    + Received

    @@ -1,8 +1,8 @@
     <div>
       <h1>
    -    Counter
    +    카운터
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
Time:        0.437s, estimated 2s
Ran all test suites.
```

> 기존의 스냅샷과 비교한 결과 다른점이 발견되어 테스트 실패한것을 볼 수 있다.<br/> 위 상태에서 엔터를 누르면 다시 테스트를 진행하며, `U`키를 누르면 변경된 스냅샷으로 업데이트 할 수 있다. 단점은, 스냅샷을 업데이트 하는데에 제약이 없기 때문에 오류이지만 업데이트를 해 버린다면, 다음 테스트에는 오류난 시점이 정상으로 되버릴 것이다.

### 메소드 호출 및 state 조회
* react-test-render는 실제로 컴포넌트가 렌더링 되기때문에, 컴포넌트의 `state`와 `메소드`에도 접근이 가능하다.<br/>
`메소드`를 실행해 `state`를 업데이트 시켜보고, 의도한대로 렌더링이 되는지 확인해 보자.

```javascript
it('카운트 UP이 정상적으로 됨.', () => {
        component.getInstance().onPlus();
        expect(component.getInstance().state.number).toBe(2);
        const snap = component.toJSON();
        expect(snap).toMatchSnapshot();
    });

    it('카운드 DOWN이 정상적으로 됨.', () => {
        component.getInstance().onMinus();
        expect(component.getInstance().state.number).toBe(1);
        const snap = component.toJSON();
        expect(snap).toMatchSnapshot();
    });

 PASS  src\components\Counter.test.jsx
  Counter View
    √ 초기 렌더링이 정상적으로 됨. (3ms)
    √ 스냅샷 일치함. (2ms)
    √ 카운트 UP이 정상적으로 됨. (3ms)
    √ 카운드 DOWN이 정상적으로 됨. (3ms)

Test Suites: 1 passed, 1 total
Tests:       4 passed, 4 total
Snapshots:   3 passed, 3 total
Time:        0.104s, estimated 1s
Ran all test suites.

Watch Usage: Press w to show more.
```

> 위 테스트 결과로 state가 변하는것을 스냅샷을 통한 테스트를 통해 확인 가능하다.<br/>
아까 작성했던 `NameForm`, `NameList`에도 초기 렌더링 테스트 코드를 작성 한다.

* src/component/NameList.test.jsx
```javascript
import React from 'react';
import renderer from 'react-test-renderer';
import NameList from './NameList.jsx';

describe('NameList View', () => {
    let component = null;

    it('NameList View 초기 렌더링', () =>{
        component = renderer.create(<NameList names={['웅식', '킴웅식']} />);
    });
    
    it('NameList View Snapshot check', () =>{
        const snap = component.toJSON();
        expect(snap).toMatchSnapshot();
    });
});
```

* src/componemt/NameForm.test.jsx
```javascript
import React from 'react';
import renderer from 'react-test-renderer';
import NameForm from './NameForm.jsx';

describe('NameForm View', () => {
    let component = null;

    it('NameForm View 초기 렌더링', () =>{
        component = renderer.create(<NameForm />);
    });
    
    it('NameForm View Snapshot check', () =>{
        const snap = component.toJSON();
        expect(snap).toMatchSnapshot();
    });
});
```

* src/App.test.js
```javascript
import React from 'react';
import renderer from 'react-test-renderer';
import App from './App';

describe('App View', () => {
  let component = null;

  it('App View 초기 렌더링', () =>{
    component = renderer.create(<App />);
  });

  it('App View Snapshot check', () =>{
    const snap = component.toJSON();
    expect(snap).toMatchSnapshot();
  });
});
```

> 테스트를 진행하면서, 불필요하지만 테스트 코드는 그대로 유지하고 싶은경우에는 `skip`을 이용하면 된다.

```javascript
describe('App View', () => {
  let component = null;

  it('App View 초기 렌더링', () =>{
    component = renderer.create(<App />);
  });

  it.skip('App View Snapshot check', () =>{
    const snap = component.toJSON();
    expect(snap).toMatchSnapshot();
  });

});

 PASS  src\App.test.js
  App View
    √ App View 초기 렌더링 (3ms)
    ○ skipped 1 test

Test Suites: 1 passed, 1 total
Tests:       1 skipped, 1 passed, 2 total
Snapshots:   0 total
Time:        0.086s, estimated 1s

Watch Usage: Press w to show more.
```

### Enzyme
>Enzyme를 이용하면 세밀한 리엑트 컴포넌트 테스팅을 할 수 있다.<br/>
Enzyme를 이용해 DOM 이벤트를 시뮬레이트(Button 클릭, Input 수정 등) 할 수 있고, 라이프사이클이 문제없이 정상적으로 진행되는지 확인 할 수 있다.<br/>
npm 모듈 `enzyme`와 `enzyme-adapter-react-16`을 설치 한다.<br/>
설치 후 `src` 디렉토리 밑에 `setupTests.js`파일을 생성 하고 아래 코드를 추가한다.(`create-react-app`로 프로젝트를 생성한 경우 사용할 수 있는 테스트 설정이다.)

* `Enzyme`의 `adapter`를 적용하는 `configure`를 제외한 대표 세가지 메소드가 있다.
  * shallow : 간단한 컴포넌트를 메모리에 렌더링 한다. 단일 컴포넌트 테스트시 유용하다.
  * mount : `HOC`나 자식 컴포넌트까지 모두 렌더링 한다. 다른 컴포넌트와의 관계를 테스트할때 유용하다.(`HOC : Higher-Order Components - 자바스크립트의 Function을 리턴하는대신 Component를 리턴 하는것.`)
  * render : 컴포넌트를 정적인 html로 렌더링 한다. 컴포넌트가 브라우저에서 어떻게 되는지 테스트할때 유용하다.


* src/setupTest.js

> 아래 코드를 작성하고, 재실행 한다.

```javascript
import {configure} from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({adapter: new Adapter()});
```

> 기존의 `Counter` 테스트 코드에 작성했던 `react-test-renderer`를 `Enzyme`으로 변경 한다.
```javascript
import React from 'react';
import Counter from './Counter.jsx';
import {shallow, configure} from 'enzyme';

describe('Counter View', () => {  
    let component = null;

    it("Counter View 초기 렌더링이 정상적으로 됨.", () => {
        component = shallow(<Counter />);
    });

    it('Counter View Snapshot check', () => {
        expect(component).toMatchSnapshot();
    });

    it('카운트 UP이 정상적으로 됨.', () => {
        component.find('#plus').simulate('click');
        expect(component.state().number).toBe(2);
        expect(component).toMatchSnapshot();
    });

    it('카운드 DOWN이 정상적으로 됨.', () => {
        component.find('#minus').simulate('click');
        expect(component.state().number).toBe(1);
        expect(component).toMatchSnapshot();
    });
});
```

> 코드를 수정하고, 테스트를 진행 후 스냅샷을 업데이트 한 후 스냅샷을 확인 해 보자. `Enzyme`를 사용한 스냅샷의 결과물은 가독성이 `react-test-renderer`보다 좋지 않은것을 볼 수 있다.

```javascript
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`Counter View Counter View Snapshot check 1`] = `
ShallowWrapper {
  "length": 1,
  Symbol(enzyme.__root__): [Circular],
  Symbol(enzyme.__unrendered__): <Counter />,
  Symbol(enzyme.__renderer__): Object {
    "batchedUpdates": [Function],
    "getNode": [Function],
    "render": [Function],
    "simulateEvent": [Function],
    "unmount": [Function],
  },
  Symbol(enzyme.__node__): Object {
    "instance": null,
    "key": undefined,
    "nodeType": "host",
    "props": Object {
      "children": Array [
        <h1>
          Counter
</h1>,
...
```

> `package.json`에 설치한 `enzyme-to-json`을 적용하면 `react-test-renderer`과 같이 깔끔하게 스냅샷 결과물을 얻을 수 있다.

```javascript
"jest": {
    "snapshotSerializers": ["enzyme-to-json/serializer"]
}
```

> 위 설정을 추가하고 테스트를 재 실행후 스냅샷을 업데이트 하고, 결과를 확인 해 보자. 설정을 추가하기 전과 확연히 다른것을 볼 수 있다.

```javascript
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`Counter View Counter View Snapshot check 1`] = `
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
```

### DOM 시뮬레이션
> 기존에는 테스트 할 수 없었던, DOM 이벤트 시뮬레이트를 할 수 있다.<br/>
화면에 `h1`과 `button`이 있는지 확인 하는 코드를 작성해보자.

* src/components/Counter.test.jsx
```javascript
describe('DOM 확인', () => {
    it('h1 여부 확인', () => {
        expect(component.find('h1').exists()).toBe(true);
    });

    it('button 여부 확인', () => {
        expect(component.find('button').exists()).toBe(true);
    });
});
```
> 렌더링을 하고난 뒤 `selector`를 통해 특정 DOM을 선택 할 수 있다.<br/>
선택할 수 있는 항목은 `css, props value, component, tag name`이다.

* src/components/Counter.test.jsx
```javascript
describe('Counter View 이벤트 시뮬레이트', () => {
    it('number 초기값 변경', () => {
        component.setState({number:3});
        expect(component.state().number).toBe(3);
    });
});
```
> `Counter.jsx`의 `number`값을 `3`으로 초기화 하고 확인시 정상적으로 PASS된다.

* src/components/NameForm.test.jsx
> `NameForm`의 `input`에 값을 넣고 추가되는것에 대한 테스트이다.
```javascript
describe('NameForm View', () => {
    let component = null;
    let changed = null;
    const onInsert = (name) => {
        changed = name;
    }

    it('NameForm View 초기 렌더링', () =>{
        component = shallow(<NameForm onInsert={onInsert}/>);
    });

    it('input 값 변경', () => {
        const changeInputValue = {
            target: {
                value: 'Gom'
            }
        };
        component.find('input').simulate('change', changeInputValue);
        expect(component.state().name).toBe('Gom');
    });

    it('submit 실행', () => {
        const changeInputValueSubmit = {
            preventDefault: () => null
        };
        component.find('form').simulate('submit', changeInputValueSubmit);
        expect(component.state().name).toBe('');
        expect(changed).toBe('Gom');
    });
});
```
> `input`의 값을 변경하고, `submit`까지 정상적으로 PASS된다.<br/>`Enzyme`의 여러가지 기능으로 테스트를 진행 할 수 있다.

> 참고 사이트 : [Jest](https://facebook.github.io/jest/docs/en/expect.html),
[Enzyme](http://airbnb.io/enzyme/docs/api/shallow.html)