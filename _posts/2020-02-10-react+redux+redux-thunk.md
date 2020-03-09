---
title: react+ts+redux+redux-thunk
date: 2020-02-10
categories:
- react
tags:
- react
- ts
- redux
- redux-thunk
---



我也不知道为什么拖着拖着就到现在才开始这部分_(:з」∠)_，明明应该早就完成的，拖延症伤不起......

默认已经有环境react+ts，接着之前的环境继续做......中间件这次就用redux-thunk吧！~~（才不是因为saga太难）~~



## 安装

反正不管怎么样第一个步骤都是安装啦，由于是ts环境，所以还需要一些ts的安装

```bash
npm install redux --save
npm install react-redux --save
npm install redux-thunk -save
npm install @types/react-redux --save-dev
```



## 相关介绍

简单说一下会用到的一些基本的东西吧

### react-redux
- `Provider`

  React Redux提供了`<Provider>`，这使得Redux的store可用于应用程序
  
  一般用法
  
  ```typescript
  import React from 'react'
  import ReactDOM from 'react-dom'
  
  import { Provider } from 'react-redux'
  import store from './store'
  
  import App from './App'
  
  const rootElement = document.getElementById('root')
  ReactDOM.render(
    <Provider store={store}>
      <App />
    </Provider>,
    rootElement
  )
  ```

- `connect()`
	React Redux 提供connect函数，将组件连接到store
	
	一般用法
	
	```typescript
	import { connect } from 'react-redux'
	import { increment, decrement, reset } from './actionCreators'
	
	// const Counter = ...
	
	const mapStateToProps = (state /*, ownProps*/) => {
	  return {
	    counter: state.counter
	  }
	}
	
	const mapDispatchToProps = { increment, decrement, reset }
	
	export default connect(
	  mapStateToProps,
	  mapDispatchToProps
	)(Counter)
	```
	



### redux-thunk

Redux的中间件（middleware），用于dispatch一个action之后，到达reducer之前，进行一些额外的操作

用法
```typescript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers/index';

// Note: this API requires redux@>=3.1.0
const store = createStore(rootReducer, applyMiddleware(thunk));
```





## 配置

1. 创建store，添加中间件

store.ts

```typescript
import {createStore, applyMiddleware} from 'redux';

import reducers from './reducers'

import thunk from 'redux-thunk'

import {composeWithDevTools} from 'redux-devtools-extension'

const store = createStore(reducers, composeWithDevTools(applyMiddleware(thunk)))

export default store

```

2. 引入store

index.tsx

```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import store from './redux-files/store'
import {Provider} from 'react-redux'

ReactDOM.render(<Provider store={store}><App /></Provider>, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about services workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();

```

3. 简单的一个例子

action-creators.ts

```typescript

export const test1 = (data?: string | number | object ): object => {
    return {
        type: 'test',
        data: data
    }
}

```



reducers.ts

```typescript
import {combineReducers} from 'redux';

function test1(prevState: any, action: any): any {
    switch (action.type) {
        case 'test':
            console.log('2333333',action)
            return prevState + +action.data;
        default:
            return 'test1'
    }
}

function test2(prevState: any, action: any): any {
    switch (action.type) {
        case 'test':
            return prevState + +action.data;
        default:
            return 'test2'
    }

}

export default combineReducers({
    test1, test2
})

```



layout.tsx

```typescript
import {Layout, Menu, Breadcrumb, Icon} from 'antd';
import React from 'react'
import {connect} from 'react-redux'
import {test1} from '@/redux-files/action-creators'
import {Carousel} from 'antd';
import './index.scss'

const {
    Header, Content, Footer, Sider,
} = Layout;
const SubMenu = Menu.SubMenu;
const Item = Menu.Item

interface IState {
    collapsed?: boolean,
}

interface IProps {
    props?: string | number | object | Function,
    flag: (data:string) => void
}

class SiderDemo extends React.Component<IProps,IState> {
    constructor(props: IProps) {
        super(props)
    }
    flag: number = 123

    componentDidMount() {
        console.log(this.props)
        const result = this.FunctionTest()
        console.log(result)
        this.props.flag('111')
    }

    FunctionTest(): Promise<number | string | object | boolean> {
        //返回一个Promise，它内部的返回值是number|string|object|boolean其中的一种
        return Promise.resolve(false)
    }

    render() {
        return (
            <Layout style={{minHeight: '100vh'}}>
                <Sider
                    collapsible
                >
                    <div className="logo"/>
                    <Menu theme="dark" defaultSelectedKeys={['1']} mode="inline">
                        <Item key="1">
                            <Icon type="pie-chart"/>
                            <span>Option 1</span>
                        </Item>
                        <Item key="2">
                            <Icon type="desktop"/>
                            <span>Option 2</span>
                        </Item>
                        <SubMenu
                            key="sub1"
                            title={<span><Icon type="user"/><span>User</span></span>}
                        >
                            <Item key="3">Tom</Item>
                            <Item key="4">Bill</Item>
                            <Item key="5">Alex</Item>
                        </SubMenu>
                        <SubMenu
                            key="sub2"
                            title={<span><Icon type="team"/><span>Team</span></span>}
                        >
                            <Item key="6">Team 1</Item>
                            <Item key="8">Team 2</Item>
                        </SubMenu>
                        <Item key="9">
                            <Icon type="file"/>
                            <span>File</span>
                        </Item>
                    </Menu>
                </Sider>
                <Layout>
                    <Header style={{background: '#fff', padding: 0}}/>
                    <Content style={{margin: '0 16px'}}>
                        <Breadcrumb style={{margin: '16px 0'}}>
                            <Breadcrumb.Item>User</Breadcrumb.Item>
                            <Breadcrumb.Item>Bill</Breadcrumb.Item>
                        </Breadcrumb>
                        <Carousel autoplay>
                            <div className='slide'>TypeScript</div>
                            <div className='slide'>React</div>
                            <div className='slide'>Redux</div>
                            <div className='slide'>Ant-Design</div>
                        </Carousel>

                    </Content>
                    <Footer style={{textAlign: 'center'}}>
                        Ant Design ©2018 Created by Ant UED
                    </Footer>
                </Layout>
            </Layout>
        );
    }
}

const mapStateToProps = (state: object | string | number | any) => ({test: state.test1});
const mapDispatchToProps = (dispatch: Function) => {
    return {
        /*flag()这个方法是自己随意命名，这里的命名跟传入的props中的方法名是一致的,
         在上面生命周期函数或者自定义方法中通过this.props.flag()调用触发对应的reducer调用从而生成状态更新store中的数据
         */
        flag(data: string) {
            const action = test1(data)
            dispatch(action)
        }
    }
};
export default connect(mapStateToProps, mapDispatchToProps)(SiderDemo);

//TS不支持修饰器，所以这里使用最传统的Redux写法。
/*
JavaScript版本的简写版
export default connect(
    (state)=>({test:state.test1}), 这里也可以写null,表示不使用store统一管理的任何状态
    (test1) //这里想要使用创建什么action对象直接写名字就行，在import 引入对应的action函数 直接在生命周期
    函数或者自定义方法中  test1()调用即可
)(SiderDemo)
*/

```



## 拓展阅读

[Redux中间件之redux-thunk使用详解](https://juejin.im/post/5bfbaeea5188254e2a0435c9)