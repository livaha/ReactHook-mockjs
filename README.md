# React Hook demo + mockjs 数据模拟
最近练习react hook的使用，同时想做数据交互，因为没有后台接口，因此想通过模拟数据实现交互；之前使用jsonp但需要手动启用模拟服务器； 本次模拟数据使用mockjs，不需要用它随机生成的模拟数据，简单粗暴的把想要的实际数据写上即可。


## 版本介绍
```
react使用16.8以上版本(为支持react hook)
下载mockjs  (yarn add mockjs)
```
## 项目启动
```
# git clone https://github.com/livaha/ReactHook-mockjs.git
# cd ReactHook-mockjs
# yarn
# yarn start
```
## 项目使用说明 
> 首次进来是直接发送get请求

> 在搜索框输入搜索内容发送post请求
## 项目演示
get请求得到的模拟数据
![](https://user-gold-cdn.xitu.io/2019/11/26/16ea79a373c570d2?w=1414&h=869&f=png&s=839600)
post请求得到的模拟数据

![](https://user-gold-cdn.xitu.io/2019/11/26/16ea7911aa0a449b?w=1414&h=869&f=png&s=916738)

## 项目实现概览
这里不介绍hook的用法，只介绍mockjs的使用
### 1、下载mockjs
```yarn add mockjs```
### 2、在配置文件引入mockjs
这里只在开发环境的webpack中引入`config/webpack.config.dev.js`
```
entry: [
      isEnvDevelopment &&
        require.resolve('react-dev-utils/webpackHotDevClient'),
-      paths.appIndexJs,
+      paths.appIndexJs_dev,
    ].filter(Boolean),
    
    ....
    
    plugins: [
      new webpack.DefinePlugin({
          'process.env': {
              NODE_ENV: '"mork"'
          }
      }),
      ....
    ].filter(Boolean),
```
修改`config/paths.js`,使得只在开发环境中才会引入mockjs文件，这里是为了创建2个index.js，index.js为生产环境用，index_dev.js为开发环境用
```

module.exports = {
   appIndexJs: resolveModule(resolveApp, 'src/index'),
+  appIndexJs_dev: resolveModule(resolveApp, 'src/index_dev'),
};
```

### 3、引入不同的index.js
`config/webpack.config.dev.js`下的是index_dev.js
```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
+ process.env.NODE_ENV=='mork' && require('./data');

ReactDOM.render(<App />, document.getElementById('root'));

```
### 4、src/data目录
`src/data/index.js`(可以引入多个文件)
```
require('./movie.js')
```
---

`src/data/movie.js`(以下为省略的数据,注意get和post模拟数据)
```
// 使用 Mock
let Mock = require('mockjs');
Mock.mock('init_movie', 'get', {
	Search: [
		{
			Title: 'Ice Age',
			Year: '2002',
			imdbID: 'tt0268380',
			Type: 'movie',
			Poster:
				'https://m.media-amazon.com/images/M/MV5BMmYxZWY2NzgtYzJjZC00MDFmLTgxZTctMjRiYjdjY2FhODg3XkEyXkFqcGdeQXVyNjk1Njg5NTA@._V1_SX300.jpg',
		},
	],
	totalResults: '1107',
	Response: 'True',
});

Mock.mock('search_movie', 'post', {
    "Search": [
        {
            "Title": "Iron Man",
            "Year": "2008",
            "imdbID": "tt0371746",
            "Type": "movie",
            "Poster": "https://m.media-amazon.com/images/M/MV5BMTczNTI2ODUwOF5BMl5BanBnXkFtZTcwMTU0NTIzMw@@._V1_SX300.jpg"
        },
    ],
    "totalResults": "9592",
    "Response": "True"
})
```

## 大功告成
以后添加数据，为了区分出文件，可以在src/data/index.js中引入多个类似movie.js等模拟文件。