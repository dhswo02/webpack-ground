## webpack 이란?

webpack은 JavaScript 모듈 번들러(Bundler)다.

> 번들링 이란?<br/> webpack은 별도의 파일로 분리되어 있는 JavaScript 모듈들을 의존성을 통해 하나 혹은 여러개의 파일로 묶는다. 이것을 번들링이라고 한다.

#### Hello, world!

```aidl
$ npm i -D webpack
$ yarn add -D webpack
```

그 다음으로 webpack이 로드할 모듈을 구현

```aidl
// hello.js
module.exports = 'Hello';
```

```aidl
// world.js
module.exports = 'world';
```
위의 모듈을 사용하는 코드를 작성
```aidl
// entry.js
var hello = require('./hello');
var world = require('./world');
document.write(hello + ', ' + world + '!');
```
이제 파일을 번들링하기 위해 npm스크립트를 써준 뒤 사용
```aidl
{
    "scripts": {
        "build": "webpack entry.js bundle.js"
    },
    "devDependencies": {
        "webpack": "^4.2.0",
    }
}
```
이 상태에서 아래의 명령어 실행
```aidl
$ yarn build
$ npm run build
```

bundle.js라는 파일이 생겼음을 확인할 수 있다. 이제 이 파일을 HTML 파일에서 로드
```aidl
<!-- index.html -->
<html>
 <head>
  <meta charset="utf-8">
 </head>
 <body>
  <script src="bundle.js">
 </body>
</html>
```
위의 방법에서 `error` 가 발생한다면 `CLI`를 이용해서 webpack을 사용하자

#### Cofiguration
명령어가 금방 복잡해지고 한계가 생기므로 설정 파일을 사용하는 것이 일반적이다.

```aidl
$ yarn add -D webpack-cli
$ npm i -D webpack-cli
```

```aidl
// webpack.config.js
module.exports = {
    entry: {
        "entry": "./entry.js"
    },
    output: {
        filename: "bundle.js"
    }
}
```
설정 파일을 만들었다면 `webpack`이라는 명령어만 쳐도 파일 번들링할 수 있다.
```aidl
{
    "srcipts": {
        "build": "webpack"
    },
    "devDependencies": {
        "webpack": "^4.2.0",
        "webpack-cli": "^2.0.13"
    }
}
```

#### stylesheet
webpack은 Stylesheet도 JavaScript 파일로 번들링할 수 있다.

webpack에서 번들링된 CSS를 사용하는 방법은 크게 두가지가 있다.

1. HTML의 `<style>` 태그안에 CSS를 직접 넣는 방법
2. 별도의 엔트리 포인트를 만들어서 또 하나의 CSS 파일로 빌드하는 방법

첫 번째 방법만을 소개해보면, <br/>
style-loader는 정확히 위에 설명한 척 번째 방식으로 동작하는 로더다. 우선 사용하기 위해서 style-loader와 css-loader를 설치하자.
```aidl
$ yarn add -D style-loader css-loader
```

css-loader는 CSS의 ==@import==나 ==url()==문을 `require`처럼 해석할 수 있도록 만들어주는 로더다. CSS를 번들링 하기 위해서는 기본적으로 이 두가지 로더를 사용해야 한다.

먼저, 로더를 사용하려면 설정 파일을 수정해야 한다. 
```aidl
// webpack.config.js
module.exports = {
  entry: {
    'entry': './entry.js'
  },
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
         test: /\.css$/,
         use: [
           'style-loader',
           'css-loader'
         ]
      }
    ]
  }
};
```
`module`프로퍼티 아래에 약간의 설정을 추가했다. 간단하게 설명하면 `.css`확장자로 끝나는 파일을 로드하는 경우 css-loader와 style-loader를 거치도록 하겠다는 의미다. 같은 파일에 대해서 로더는 아래쪽부터 순서대로 동작한다.

```aidl
/* text.css */
body {
  color: blue;
  font-size: 24px;
}
```

```aidl
/* common.css */
@import 'text.css';
body {
  background-color: gray;
}
```

```aidl
// entry.js
require('./common.css');
var hello = require('./hello');
var world = require('./world');
document.write(hello + ', ' + world + '!');
```

#### ES2015
IE환경을 지원하면서도 ES2015를 쓰기 위해서는 babel같은 트랜스파일러(Transpiler)가 필수다. webpack에서는 이를 위해 `babel-loader`에 통과시켜서 ES5 이하의 JavaScript로 만든다.

```aidl
$ yarn add -D babel-loader babel-core babel-preset-env
```

```aidl
// webpack.config.js
module.exports = {
  // ...
  module: {
    rules: [
      // ...
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          presets: [[
            'env', {
              targets: {
                browsers: ['last 2 versions']
              }
            }
          ]]
        }
      }
      // ...
    ]
  }
};
```

babel에 대한 설명은 다음에..

이제부터 프로젝트에 포함된 모든 `.js` 확장자 파일은 babel-loader를 거치면서 ES5로 트랜스파일된다. 코드를 ES2015로 변경하여 테스트 해보자.
```aidl
// hello.js
export default 'Hello';
```
```aidl
// world.js
export default 'world';
```
```aidl
// entry.js
import './common.css';
import hello from './hello';
import world from './world';
document.write(`${hello}, ${world}!`);
```

#### Lint
webpack은 번들러지만 JavaScript 파일을 로드하기 때문에 Lint 작업도 수행할 수 있다.

```aidl
npm i -g eslint
```
```aidl
$ yarn add -D eslint eslint-loader
```

```aidl
eslint --init
```