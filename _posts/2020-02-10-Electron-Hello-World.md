---
layout  : post
title   : "[Electron] Hello World!"
date    : 2020-02-10 20:21:02 +0900
category: Electron
---

Electron은 JavaScript를 사용하여 데스크탑 프로그램을 만들 수 있습니다. 데스크탑 응용프로그램에 초점을 맞춘 Node.js라고 생각하면 편합니다.

# Windows에서 Electron 개발 환경 설정

[Node.js](https://nodejs.org/)에 접속하여, 설치 파일을 다운로드 받아 설치합니다. 간단하죠?  
![Download Node.js]({{ site.url }}/assets/image/2020-02-10-Electron-Hello-World/2020-02-10-Electron-Hello-World_1.png)


# Electron 프로젝트 생성하기

'Node.js command prompt'를 실행합니다.  
![Run 'Node.js command prompt']({{ site.url }}/assets/image/2020-02-10-Electron-Hello-World/2020-02-10-Electron-Hello-World_2.png)  
Electron 프로젝트를 생성하기 위해 빈 폴더를 하나 만든 다음, 새로 만든 폴더 경로에서 `npm init`을 실행합니다.

```bash
mkdir electron-helloworld
cd electron-helloworld
npm init
```

![npm init]({{ site.url }}/assets/image/2020-02-10-Electron-Hello-World/2020-02-10-Electron-Hello-World_3.png)

생성된 `package.json` 파일을 보면 아래와 비슷하게 작성되어 있을 것입니다.

```json
{
  "name": "electron-helloworld",
  "version": "1.0.0",
  "description": "Electron Hello, world!",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "KyuHyuk Lee",
  "license": "MIT"
}
```

`package.json`를 아래와 같이 수정합시다. (`scripts`를 수정하고, `devDependencies`를 추가합니다.)

```json
{
  "name": "electron-helloworld",
  "version": "1.0.0",
  "description": "Electron Hello, world!",
  "main": "main.js",
  "scripts": {
    "start": "electron ."
  },
  "author": "KyuHyuk Lee",
  "license": "MIT",
  "devDependencies": {
    "electron": "^8.0.0",
  }
}
```

위와 같이 수정한 다음, `npm install`을 실행하여 Electron을 설치합니다.

# Hello World!

아래의 소스코드는 Electron 응용프로그램이 준비될 때까지 기다렸다가 창을 여는 `main.js`의 소스코드입니다.

```javascript
const { app, BrowserWindow } = require('electron')

function createWindow () {
  // 브라우저 창을 생성합니다.
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  // index.html을 응용프로그램에 띄웁니다.
  win.loadFile('index.html')

  // 개발자 도구를 엽니다.
  win.webContents.openDevTools()
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

마지막으로 `index.html`을 작성하여 보고 싶은 화면을 작성합니다.
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
    <!-- https://electronjs.org/docs/tutorial/security#csp-meta-tag -->
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using node <script>document.write(process.versions.node)</script>,
    Chrome <script>document.write(process.versions.chrome)</script>,
    and Electron <script>document.write(process.versions.electron)</script>.
  </body>
</html>
```

이제 `main.js`, `index.html`, `package.json`을 작성했다면, `npm start` 명령어를 사용하여 응용프로그램을 실행할 수 있습니다.

![Hello World!]({{ site.url }}/assets/image/2020-02-10-Electron-Hello-World/2020-02-10-Electron-Hello-World_4.png)

> 출처 : [https://github.com/electron/electron-quick-start](https://github.com/electron/electron-quick-start)
