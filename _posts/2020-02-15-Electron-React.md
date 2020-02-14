---
layout  : post
title   : "[Electron] Electron에서 React 사용하기"
date    : 2020-02-15 00:59:55 +0900
category: Electron
---

이번 시간에는 Electron에서 React를 사용해보겠습니다.

# Environment Setting

`npm install -g yarn` 명령어로 `yarn`을 설치합니다.

> YARN 이란 Facebook에서 만든 새로운 자바스크립트 패키지 매니저입니다. 기존에 존재하는 npm 과 동일한 기능을 수행합니다. 출처 : [YARN, 새로운 Package Manger - FELog](https://jaeyeophan.github.io/2017/04/21/YARN-New-Package-Manger/)

그리고 `yarn global add create-react-app` 명령어로 `create-react-app`를 설치합니다. `create-react-app`은 React를 설정해주는 패키지입니다.  
`create-react-app`을 설치하고, `PATH`에 `yarn global bin` 명령어를 입력하면 나오는 경로를 추가합니다.

![Add PATH]({{ site.url }}/assets/image/2020-02-15-Electron-React/2020-02-15-Electron-React_1.png)

# Create React Application

아래 명령어로 React Application 디렉터리를 생성합니다.

```
create-react-app electron-react-app
```

이 명령어는 `electron-react-app` 폴더를 만들고, 필요한 패키지들을 모두 자동으로 설치해줍니다.

# Install Electron

위에서 생성한 `electron-react-app` 폴더로 이동한 뒤, 아래 명령으로 최신 버전의 [Electron](https://www.npmjs.com/package/electron)을 설치합니다.

```
yarn add -D electron@latest
```

# Install Foreman

Electron과 React를 동시에 사용하려면, 여러 개의 Thread가 필요하게 됩니다. 여기서 Electron은 Client 역할을 하고 React는 Web Server 역할을 하게 됩니다.  
여기서 [Foreman](https://www.npmjs.com/package/foreman)은 위의 두 개의 Process를 관리하는 역할을 맡게 됩니다.  
아래 명령어로 Foreman도 설치합니다.

```
yarn add -D foreman@latest
```

# Electron ❤ React

Electron을 실행하기 위해서는 실행 파일이 필요합니다. 아래의 내용을 `src/electron-starter.js`에 저장합니다.

```javascript
const { app, BrowserWindow } = require('electron')
const url = require('url')
const path = require('path')

function createWindow () {
  // 브라우저 창을 생성합니다.
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  // React를 빌드할 경우 결과물은 build 폴더에 생성되기 때문에 loadURL 부분을 아래와 같이 작성합니다.
  const startUrl = process.env.ELECTRON_START_URL || url.format({
    pathname: path.join(__dirname, '/../build/index.html'),
    protocol: 'file:',
    slashes: true
  });
  win.loadURL(startUrl);
}

// 이 메소드는 Electron의 초기화가 완료되고
// 브라우저 윈도우가 생성될 준비가 되었을때 호출된다.
// 어떤 API는 이 이벤트가 나타난 이후에만 사용할 수 있습니다.
app.whenReady().then(createWindow)

// 모든 윈도우가 닫히면 종료된다.
app.on('window-all-closed', () => {
  // macOS에서는 사용자가 명확하게 Cmd + Q를 누르기 전까지는
  // 애플리케이션이나 메뉴 바가 활성화된 상태로 머물러 있는 것이 일반적입니다.
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', () => {
  // macOS에서는 dock 아이콘이 클릭되고 다른 윈도우가 열려있지 않았다면
  // 앱에서 새로운 창을 다시 여는 것이 일반적입니다.
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow()
  }
})

// 이 파일에는 나머지 앱의 특정 주요 프로세스 코드를 포함시킬 수 있습니다. 별도의 파일에 추가할 수도 있으며 이 경우 require 구문이 필요합니다.
```

그리고, `package.json`을 수정해줍니다.  
`main`을 추가해주고, `scripts`에 있는 `start`와 `build`를 각각 `react-start`, `react-build`로 변경해줍니다.  
`scripts`에 `electron`도 추가해줍니다.

```json
"main": "src/electron-starter.js",
"scripts": {
  "react-start": "react-scripts start",
  "react-build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject",
  "electron": "electron ."
},
```

이제 Electron과 React가 동시에 실행되도록 해봅시다. 위에서 말했듯이 Foreman은 여러 프로세스를 동시에 실행할 수 있게 해줍니다. `Procfile`를 아래와 같이 작성합니다.

```
react: npm run react-start
electron: node src/electron-wait-react
```

그리고 `src/electron-wait-react.js`를 아래와 같이 작성합니다. Electron과 React가 동시에 실행된 이후 Electron이 React의 완전한 실행을 기다리도록 하는 파일입니다.

```javascript
const net = require('net');
const port = process.env.PORT ? (process.env.PORT - 100) : 3000;

process.env.ELECTRON_START_URL = `http://localhost:${port}`;

const client = new net.Socket();

let startedElectron = false;
const tryConnection = () => client.connect({port: port}, () => {
        client.end();
        if(!startedElectron) {
            console.log('starting electron');
            startedElectron = true;
            const exec = require('child_process').exec;
            exec('yarn run electron');
        }
    }
);

tryConnection();

client.on('error', (error) => {
    setTimeout(tryConnection, 1000);
});
```

마지막으로 `package.json` 파일의 `scripts`에 `start`를 추가해줍니다.

```json
"main": "src/electron-starter.js",
"scripts": {
  "react-start": "react-scripts start",
  "react-build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject",
  "electron": "electron .",
  "start" : "nf start"
},
```

`yarn start` 명령어를 입력하여 실행하면, 아래와 같이 React 화면이 Electron 위에 출력됩니다.

![React App]({{ site.url }}/assets/image/2020-02-15-Electron-React/2020-02-15-Electron-React_2.png)

# Create Installation File

설치 파일을 만들기 위해서 `electron-builder`가 필요합니다. 아래 명령어로 `electron-builder`를 설치합니다.

```
yarn add -D electron-builder@latest
```

`package.json`에 `homepage`를 추가합니다. `create-react-app`이 파일을 참조할 때 절대 경로를 사용하기 때문에, 이를 상대 경로로 바꾸기 위해서 `homepage`를 추가합니다.

```json
"homepage": "./",
```

그리고, `scripts`에 `dist`를 추가하고, `build`도 추가합니다.

```json
"homepage": "./",
"main": "src/electron-starter.js",
"scripts": {
  "react-start": "react-scripts start",
  "react-build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject",
  "electron": "electron .",
  "start": "nf start",
  "dist": "yarn run react-build && electron-builder ."
},
"build": {
  "extends": null,
  "directories": {
    "buildResources": "public"
  },
  "target" : "zip"
},
```

모두 설정되었다면 `yarn run dist` 명령어로 설치 파일을 빌드 합니다. 빌드가 완료되면 `dist` 폴더에 설치 파일이 생성됩니다.

![Installation File]({{ site.url }}/assets/image/2020-02-15-Electron-React/2020-02-15-Electron-React_3.png)
