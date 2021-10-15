# Typescript 编写和发布 npm 包

参考

1. https://www.jianshu.com/p/8fa2c50720e4
2. https://www.yisu.com/zixun/151864.html

### 初始化

```
git init

git remote add origin https://github.com/hsycc/cloud-node-sdk.git

npm init

npm i typescript -D

# 初始化  tsconfig.json
tsc --init

# 编码

mkdir lib && touch lib/index.ts


```

tsconfig.json

```
{
  "compilerOptions": {
    "target": "es5", // 指定ECMAScript目标版本
    "module": "commonjs", // 指定模块化类型
    "declaration": true, // 生成 `.d.ts` 文件
    "outDir": "./dist", // 编译后生成的文件目录
    "strict": true // 开启严格的类型检测
  }
}
```

### 编译

package.json

```
"scripts": {
    "build": "tsc -p  tsconfig.json"
  },
```

### 编写测试用例

```
# 1. 安装测试框架和断言库
npm install mocha chai  @types/mocha  @types/chai -D

# 2. 创建测试文件目录和文件

mkdir test && touch test/add.test.ts

vim test.js

echo "'use strict';
import { expect } from "chai";
import { add } from "../lib";

describe('function test', () => {
  it('should return 2', () => {
    const result = add(1, 1);
    expect(result).to.equal(2);
  });
});" > test/add.test.ts


# 3. package 添加测试脚本
package.json
"scripts": {
"test": "mocha --bail --exit  --reporters  --require ts-node/register  test/*.test.ts"
},

# --bail,  -b 参数指定只要有一个测试用例没有通过，就停止执行后面的测试用例。这对持续集成很有用。
# --reporter 参数用来指定测试报告的格式，默认是spec格式。
#  --require 在加载用户界面或测试文件之前需要一个模块。执行es6或者ts编写的测试用例,默认只执行es5的用例


mocha --require ts-node/register test/_.test.ts
or
mocha --require babel-core/register test/_.test.ts


```

### 发布

```

# 登录 npm， 若无账号，请在https://www.npmjs.com/ 注册账号

npm adduser
Username: ${your name}
Password: ${your password}
Email: (this IS public) ${your email}
Logged in as youthcity on https://registry.npmjs.org/

# 发布包

npm publish

# 删除发布的版本

# npm 做了相关的删除限制：

# 删除的版本 24 小时后方可重发!

# 只有发布 72 小时之内的包可以删除!

npm unpublish ${your package} --force

# 迭代

# npm 采用语义化版本，共三位，以’.’隔开，从左至右依次代表：主版本（major）、次要版本（minor）、补丁版本（patch）。

# 例如：

# 1.0.0

# major.minor.patch

```

### Travis CI 持续集成

1. 登录 [Travis CI](https://travis-ci.org/)
2. 点击 “Sign in with Github”
3. 勾选需要持续集成的项目
4. touch .travis.yml

.travis.yml

```
language : node_js
node_js :
 - stable
install:
 - npm install
script:
 - npm test
```

5. 将配置推送到 Github 的远程仓库。查看 travis 构建状态。

### coveralls 代码覆盖率

1. 使用 github 账号登录 [coveralls](https://coveralls.io/)，
   勾选需要执行代码覆盖率检查的仓库
2. 在项目中，添加开发依赖

```
npm i istanbul coveralls  -D
```

3. 在 package.json 的配置文件中，添加脚本

```
"cover": "istanbul cover npm run test"
```

4. 修改 .travis.yml 文件

```
language : node_js
node_js :
 - stable
install:
 - npm install
script:
 - npm run cover
 # Send coverage data to Coveralls
after_script: "cat coverage/lcov.info | node_modules/coveralls/bin/coveralls.js"

```

5. 运行命令

```
npm run cover
```

6. commit 和 推送代码到 github 仓库
