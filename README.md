###不能再多了——热更新、ES6/7、LESS、Router、async／await、本地node服务器，真实路由...
==========================================

email：点击<a href="mailto:1130216245@qq.com">1130216245@qq.com</a>发邮件给我  

本源码gitbook教程：https://www.gitbook.com/book/hyy1115/react-redux/details  （正在撰写中）

https://hyy1115.github.io/huangyongyue/  

2016-12-11更新：  

1、优化了多个组件，统一采用无状态组件的方式，复用性更强大  
2、优化了action，reducer，component直接的逻辑关系  
3、给大部分文件增加了文字解释，让新手能够看得懂代码为什么这样写  
4、增加了移动端点击事件插件，解决300毫秒延迟问题  
5、最近更新的都是我从近来项目重构过程中总结出来的解决方案，为了让项目的结构更加的合理清晰  

请同时下载我写的简易服务端做联合测试[node-express-server](https://github.com/hyy1115/node-express-server)，因为工程结构是通过服务端返回html模板来加载js的，这是我在企业级开发中经常使用到的功能。

=========================

注意：这份代码不只是一个demo，不只是一个todo List，而是一个完整的react-redux-webpack开发方案，我个人用这套总结的方案已经发布了多个企业项目（公众号：速投盈，法狗狗，法纳）。

该方案包含了开发阶段的调试，代码检查，开发效率，实时更新，state存储模式，异步模式，组件结构的管理，打包部署到服务器。服务端渲染没有做，这个功能还待考虑，个人觉得前后端分离方案已经足够解决目前的问题，服务端渲染会增加前后端的耦合。


与github上其他开源不同的是，这份代码采用了合理的redux架构，适合个人或者团队开发。  

有遇到issue的开发者可以反馈给我，有其他插件使用不明白的也可以反馈。

==========================
####Installation教程
1, Method 1: 下载源代码到本地  

2, Method 2: 或者本地运行shell命令  
```
 git clone https://github.com/hyy1115/react-redux-webpack.git
 
 
 服务端项目，具体安装教程看 https://github.com/hyy1115/node-express-server
 git clone https://github.com/hyy1115/node-express-server.git 
```
 
3, 安装依赖包，已经解决了一些依赖包安装最新版可能出现的bug，如果还有问题，可以看相关社区的issue。
```
npm install 或者cnpm install
```

4, 运行demo。
   ```
    mac
    npm run start-mac
    
    windows
    npm run start-win
   ```

5, 将会开启3011端口，这个时候要注意，不是在浏览器访问3011端口，而是访问
```
http://localhost:9009

9009是node服务器的端口号，你最好先运行我发布的node开源项目，然后再和你的node合并解决方案。  

为什么是访问服务器端口而不是前端开启的端口呢？

这里涉及到跨域问题，跨域的本质就不说了，说说我的解决办法，我把index.html放在服务器端，每次请求服务器端口地址的时候，都将会返回一个html，这个html
中的script标签会链接3011端口的mobile.bundle.js，这样就很巧妙的解决了跨域的问题，同时还能保证前端代码热更新可用。
```

6, 发布,参考http://blgxbook.win/  (主机屋已经过期)

```
mac
npm run build-mac

windows
npm run build-win
```

===========================================

####如何应用本框架到你的项目上？
1，在container文件夹下面新建你的页面父容器，比如本例子中的homeContainer，一个基本的container模板如下所示。  
```
import React, { Component } from 'react';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

@connect(
    state => state,
    dispatch => bindActionCreators(。。。, dispatch)
)
export class HomeContainer extends Component {

    constructor(props) {
        super(props);
    }

    componentWillMount() {
        
    }

    render() {
        return(
            <div className="">
                这里调用导入的各个子组件模块
            </div>
        );
    }
}
```

2，接着你就需要将定义好的container写入路由，这样你就可以在浏览器上通过路由访问这个页面了。  
```
    import React from 'react';
    import { Route } from 'react-router';
    
    /* containers */
    import { AppContainer } from 'appContainer';
    import { HomeContainer } from 'containers/Home/homeContainer';
    
    export default (
        <Route path="/" component={AppContainer}>
            <Route path="home" component={HomeContainer} />
        </Route>
    );
```

3，假设你现在已经在component下面写好了一个导航组件nav，然后你要在container发action去异步请求后端的导航API，将获取的数据dispatch到reducer中。  

container部分
```
componentWillMount() {
        const { navMain } = this.props.nav //这个叫做es6的解构赋值
        if (navMain.length === 0) {
        //如果state中的navMain对象为空，则调用getNav方法
            this.props.getNav();
        }
    }
```
action部分
```
import { getData, postData } from 'utils/fetchData'

//这个叫做action，用于更新reduer中的state
const receiveNav = (response) => {
    return {
        type: 'RECEIVE_NAV',
        navMain: response.data
    }
}
//获取服务器的参数，并且返回一个异步的dispatch，dispatch的对象是自己定义的action
export const getNav = () => {
    return async (dispatch) => {
        try {
            let response = await getData(`/book/navigation`)
            await dispatch(receiveNav(response))
        } catch (error) {
            console.log('error: ', error)
        }
    }
}
```

reducers部分
```
// 初始化状态
let initNavList = {
    navMain: []
}

export function nav(state = initNavList, action) {
    switch (action.type) {
        case 'RECEIVE_NAV':
            return {
                ...state,   //三个点是展开符
                navMain: action.navMain
            }

        default:
            return {...state};
    }
}
```

4，到这一步，你已经完成了基本的一个数据流的控制了，需要注意的是，reducers中自定义的reducer需要在reducers文件夹下面的index.js里面注册。
  
```
import { combineReducers } from 'redux';
import { routeReducer } from 'redux-simple-router';
import {reducer as formReducer} from 'redux-form';

import { nav } from './nav';

//注册reducer，每个自定义的reducer都要来这里注册！！！不注册会报错。
const rootReducer = combineReducers({
  form: formReducer,
  routing: routeReducer,
  /* your reducers */
  nav, //导航相关
});

export default rootReducer;
```

5，store文件夹下面的js已经配置好了，除非你需要加上react的谷歌调试插件，否则不需要做任何修改。  

6，最重要的功能，跟node服务器端成功实现了交互，注意遵循restful规范。

[node-express-server](https://github.com/hyy1115/node-express-server)

===================================================

####常用的排查bug方法  
1、react和这么多的插件搭配使用，开发过程中不可避免会遇到很多的bug，有一些甚至无法从chrome找到答案，那么我们遇到这些问题的时候该怎么办呢？  

2、第一步，打开chrome调试工具，如果你喜欢火狐或者微信调试之类的，我建议能用chrome的尽量用chrome，chrome开发者工具的console和netWork调试界面是
开发react最经常用到的。有的人也会用chrome上的react调试插件，每次打开我都觉得很麻烦，所以本源码用的是logger插件，直接在console看到每个页面和事件触发的action。  

3、如果chrome工具找不出bug的话，npm命令的控制台也可以看到详细的报错信息，大部分报错的情况是某个插件没有install，或者某个参数未定义。参数未定义的情况出现在异步ajax，
state初始为空，初次渲染页面必然会导致某个参数为空，那么只需要在组件里面做个简单的判断即可，防止把空参数传递进来，也有其他的解决办法，比如我在header组件里面用到的判断。

4、webpack配置错误，也会导致热更新失败，或者打包失败，不了解webpack机制的还需要多多研究webpack的用法。



=====================================================

####版本更新说明
从第一次发布react-redux版本到现在，已经做了好几次重大修复，抛弃了一些方案，也增加了一些方案。

sass/scss：在第二版发布到时候就已经删除，并没有有效提高样式到管理效率和布局效率，而且需要安装framework 2.0，看到这我就默默到选择了放弃，不想折腾mac。

less：目前到版本采用到方案，安装依赖包简单，语法也和scss相似，后期我打算用flex来做开发，不排除抛弃less，重新回到css到怀抱。

路由到选择：有2个方案，hashHistory 和 browserHistory，初期采用到是hashHistory，但是由于不是真实路由，所以目前到版本我把它更换成了browserHistory，
browserHistory有个问题要注意，当刷新网页到时候，会找不到真实路径，需要在server做一些配置，具体可以看我在本地server上写到代码。

JSX：JSX语法不再只是支持三元表达式，还支持if（）else（），强大到没话可说。

异步用fetch还是axios：fetch到方案我也用过，具体看http://sutouying.cn  ，axios是我目前使用到插件，具体看http://www.fagougou.com  ，
这2个方案可操作性都差不多，在使用babel编译到条件下，没有明显区别，都可以使用。

async还是promise：不用纠结这个问题，我们知道fetch通常和promise结合使用，而用axios，我更喜欢使用async，有时候也会用到promise。

==================================================

####Finally, JavaScript is the world's best language, if demand, you can directly send me an email  
