
> [JSON Server](https://github.com/typicode/json-server) 是一个创建伪RESTful服务器的工具，这里直接讲在vue-cli 中的用法。

#### 怎么配置JSON Server
1. 全局安装 
`npm i -g json-server`
2. 项目目录下创建`mock`文件夹
3. `mock`下添加一个db.json文件，内容如下：


```
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```
4. `package.json`中`scripts`里添加如下命令：

```
'mock':'json-server --watch mock/db.json',
'mockdev':'npm run mock & npm run dev'
```
5. `npm run mock`运行mock server
6.  访问[http://localhost:3000/]()

发现，db.json下第一级json对象被解析成可访问的路径：
![](http://ol02dlpzm.bkt.clouddn.com/14918955216055.jpg)


访问访问[http://localhost:3000/posts]()可获得

```
[
  {
    "id": 1,
    "title": "json-server",
    "author": "typicode"
  }
]
```

-------
#### faker.js生成大量数据
假如我们需要大量的数据，如果手动添加会比较费时间的，可以使用[faker.js](https://github.com/Marak/faker.js)生成。

1. 全局安装faker `npm i faker -g`
2. mock目录下创建`faker-data.js`，添加如下内容


```
module.exports=function(){
    var faker=require('faker');
    faker.locale='zh_CN';
    var _ = require('lodash');
    return {
    //伪造20条关于people的数据
        people: _.times(20,function(n){
            return {
                id: n,
                name: fake.name.findName(),
                avatar: fake.internet.avatar()
            }
        })
    }
}
```
3. 运行`json-server mock/faker-data.js`，在json server中使用faker
4. 访问[](http://localhost:3000/people)可以获取随机生成的20组数据
![](http://ol02dlpzm.bkt.clouddn.com/14918961418940.gif)

#### 添加中间件将POST请求转为GET请求

json server使用RESTful架构，GET请求获取数据，POST请求则是添加数据。开发过程中，若想直接模拟POST请求返回结果，可以添加express中间件将POST请求转为GET请求。

1. mock目录下创建`post-to-get.js`，内容如下


```
module.exports=function(req,res,next){
    req.method="GET";
    next()
}
```
2. `package.json`中`scripts`中的`mock`的姓名修改为：

```
'mock':'json-server --watch mock/db.json --m mock/post-to-get.js',
```

#### 代理设置
1. vue-cli工程里，在`config/index.js`的**proxyTable**将请求映射到 [](http://localhost:3000)
![](http://ol02dlpzm.bkt.clouddn.com/14918966922863.jpg)
在代码中添加请求以测试效果：
![](http://ol02dlpzm.bkt.clouddn.com/14918967579935.jpg)

2. `npm run mockdev`启动带mock数据的本地服务

    ![](http://ol02dlpzm.bkt.clouddn.com/14918968549557.jpg)
![](http://ol02dlpzm.bkt.clouddn.com/14918968608776.jpg)


