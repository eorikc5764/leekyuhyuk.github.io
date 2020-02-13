---
layout  : post
title   : "[Electron] SQLite3를 사용한 간단한 메모장 만들기"
date    : 2020-02-12 16:19:48 +0900
category: Electron
---

이번 시간에는 SQLite3를 사용한 간단한 메모장을 만들어보겠습니다.

다음과 같은 파일들이 필요합니다.

```
├── app.css
├── app.js
├── database.db
├── index.html
├── main.js
└── package.json
```

우선 `package.json` 부터 작성해보겠습니다. `electron`, `electron-rebuild`, `sqlite3` 패키지가 필요합니다.  
`scripts`에 `rebuild`는 왜 등록했는지는 뒤에서 설명하도록 하겠습니다.  
아래의 내용을 모두 작성하고 `npm install` 명령을 실행합니다.

```json
{
  "name": "elememo",
  "version": "1.0.0",
  "description": "Electron Memo Application",
  "main": "main.js",
  "scripts": {
    "start": "electron .",
    "rebuild": "electron-rebuild -w sqlite3 -p"
  },
  "author": "KyuHyuk Lee",
  "license": "MIT",
  "devDependencies": {
    "electron": "^8.0.0",
    "electron-rebuild": "^1.10.0"
  },
  "dependencies": {
    "sqlite3" : "^4.1.1"
  }
}
```

`main.js`는 아래와 같이 작성합니다.

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
}

// 이 메소드는 Electron의 초기화가 완료되고
// 브라우저 윈도우가 생성될 준비가 되었을때 호출됩니다.
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
```

`index.html`은 아래와 같이 우선 `app.js`를 실행할 정도로만 작성합니다.

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>EleMemo</title>
    <!-- https://electronjs.org/docs/tutorial/security#csp-meta-tag -->
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
</head>

<body>
    <script src="app.js"></script>
</body>

</html>
```

이제 우리가 작성한 메모가 작성될 데이터베이스 파일(SQLite3)을 생성합니다.  
`memo` 테이블을 생성하고, `contents`와 `data`를 추가합니다.

```
$ sqlite3 database.db
sqlite> CREATE TABLE `memo` ( `contents` TEXT, `date` TEXT );
sqlite> .exit
```

![SQLite3 Database Create]({{ site.url }}/assets/image/2020-02-12-Electron-EleMemo/2020-02-12-Electron-EleMemo_1.png)

이제 `app.js`를 작성해봅시다. 위에서 만든 `database.db`에 데이터를 기록하고 출력하는 코드부터 작성해봅시다.

```javascript
const sqlite3 = require('sqlite3').verbose();
const db = new sqlite3.Database('database.db');

function addMemo(contents, date) {
    const query = `INSERT INTO memo(contents, date) VALUES ('${contents}', '${date}')`;
    db.serialize();
    db.each(query);
}

function listMemo() {
    db.serialize(function () {
        db.each("SELECT rowid AS id, contents, date FROM memo", function (err, row) {
            console.log(row.id + " - " + row.contents + "[" + row.date + "]");
        });
    });
}

addMemo("토요일에 어디서 놀지 생각하기", "2020-02-11");
addMemo("오후 6시 30분에 여자친구 만나기", "2020-02-12");
listMemo();
```

`npm start`로 실행해봅시다.

그런데 무슨 일일까요? `Uncaught Error: Cannot find module` 에러를 보이고 있습니다. 우리가 소스코드를 잘못 입력한 것일까요? 아닙니다. 아까 위에서 `package.json`을 작성할 때, `rebuild`를 넣은 게 기억나시나요? `electron-rebuild` 패키지로 Electron에 대해 모듈을 다시 빌드 해야 `sqlite3` 패키지를 사용할 수 있습니다.

![EleMemo Error]({{ site.url }}/assets/image/2020-02-12-Electron-EleMemo/2020-02-12-Electron-EleMemo_2.png)

`npm run rebuild` 명령을 한 뒤, `npm start`로 다시 실행해봅시다.

실행하면 아래와 같이 우리가 추가한 메모가 정상적으로 출력되는 것을 확인할 수 있습니다.

![EleMemo]({{ site.url }}/assets/image/2020-02-12-Electron-EleMemo/2020-02-12-Electron-EleMemo_3.png)

`listMemo()`를 수정하여 화면에 메모 내용이 출력되게 수정해봅시다. 우선 `index.html`에 메모가 띄워질 `template`를 만들어놓습니다.

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>EleMemo</title>
    <!-- https://electronjs.org/docs/tutorial/security#csp-meta-tag -->
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
    <link rel="stylesheet" href="app.css" />
</head>

<body>
    <div id="main-area">
        <template id="card-template">
            <div class="card">
                <div class="container">
                    <h4><b>
                            <p class="date">2020-01-01</p>
                        </b></h4>
                    <p class="contents">Elememo</p>
                </div>
            </div>
        </template>
    </div>
    <script src="app.js"></script>
</body>

</html>
```

그리고 `app.css`는 아래와 같이 작성합니다.

```css
.card {
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
    transition: 0.3s;
    float: left;
    width: 40%;
    margin: 20px;
}

.card:hover {
    box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}

.container {
    padding: 2px 16px;
}
```

`app.js`에는 `displayMemo()`를 추가하고, `listMemo()`를 아래와 같이 수정합니다.

```javascript
const sqlite3 = require('sqlite3').verbose();
const db = new sqlite3.Database('database.db');

const mainArea = document.getElementById('main-area');
const template = document.querySelector('#card-template');

function addMemo(contents, date) {
    const query = `INSERT INTO memo(contents, date) VALUES ('${contents}', '${date}')`;
    db.serialize();
    db.each(query);
}

function displayMemo(contents, date) {
    let clone = document.importNode(template.content, true);
    clone.querySelector('.contents').innerText = contents;
    clone.querySelector('.date').innerText = date;
    mainArea.appendChild(clone);
}

function listMemo() {
    db.serialize(function () {
        db.each("SELECT rowid AS id, contents, date FROM memo", function (err, row) {
            displayMemo(row.contents, row.date);
        });
    });
}

listMemo();
```

실행하면 아래와 같이 메모 목록이 예쁘게(?) 출력됩니다!

![EleMemo]({{ site.url }}/assets/image/2020-02-12-Electron-EleMemo/2020-02-12-Electron-EleMemo_4.png)

메모를 추가하는 메뉴도 추가해야겠죠?  
`index.html`을 아래와 같이 수정합니다.

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>EleMemo</title>
    <!-- https://electronjs.org/docs/tutorial/security#csp-meta-tag -->
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
    <link rel="stylesheet" href="app.css" />
</head>

<body>
    <div id="header">
        <input id="input-memo" type="text" placeholder="메모">
        <button onclick="addMemo()">등록하기</button>
    </div>
    <br>
    <div id="main-area">
        <template id="card-template">
            <div class="card">
                <div class="container">
                    <h4><b><p class="date">2020-01-01</p></b></h4>
                    <p class="contents">Elememo</p>
                </div>
            </div>
        </template>
    </div>

    <script src="app.js"></script>
</body>

</html>
```

`input`과 `button`도 예쁘게 나오도록 `app.css`도 아래와 같이 수정합니다.

```css
.card {
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
    transition: 0.3s;
    float: left;
    width: 40%;
    margin: 20px;
}

.card:hover {
    box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}

.container {
    padding: 2px 16px;
}

input {
    float: left;
    font-size: 18px;
    padding: 10px 10px 10px 5px;
    display: block;
    width: 50%;
    border: none;
    border-bottom: 1px solid #757575;
    margin: 20px;
}

input:focus {
    outline: none;
}

button {
    background: #1AAB8A;
    color: #fff;
    border: none;
    position: relative;
    height: 40px;
    font-size: 18px;
    padding: 0 1.5em;
    cursor: pointer;
    transition: 800ms ease all;
    outline: none;
    margin: 20px;
}

button:hover {
    background: #fff;
    color: #1AAB8A;
}

button:before, button:after {
    content: '';
    position: absolute;
    top: 0;
    right: 0;
    height: 2px;
    width: 0;
    background: #1AAB8A;
    transition: 400ms ease all;
}

button:after {
    right: inherit;
    top: inherit;
    left: 0;
    bottom: 0;
}

button:hover:before, button:hover:after {
    width: 100%;
    transition: 800ms ease all;
}
```

우리는 '등록하기' 버튼을 누르면 `addMemo()`가 실행되도록 만들었습니다. `input`에 있는 내용을 저장할 수 있도록 `app.js`에 있는 `addMemo()`를 수정해봅시다.

```javascript
const sqlite3 = require('sqlite3').verbose();
const db = new sqlite3.Database('database.db');

const mainArea = document.getElementById('main-area');
const template = document.querySelector('#card-template');
const contents = document.getElementById('input-memo');

/* 날짜를 YYYY-MM-DD로 출력하기 위해 사용됩니다. */
Date.prototype.yyyymmdd = function() {
    var yyyy = this.getFullYear().toString();
    var mm = (this.getMonth() + 1).toString();
    var dd = this.getDate().toString();
    return  yyyy + "-" + (mm[1] ? mm : "0" + mm[0]) + "-" + (dd[1] ? dd : "0" + dd[0]);
}

function addMemo() {
    date = new Date().yyyymmdd();
    const query = `INSERT INTO memo(contents, date) VALUES ('${contents.value}', '${date}')`;
    db.serialize();
    db.each(query);
    contents.value = ""; /* input에 입력된 값을 없앱니다. */
    listMemo(); /* 메모가 추가되면, 화면에 새로 업데이트해줍니다. */
}

function displayMemo(contents, date) {
    let clone = document.importNode(template.content, true);
    clone.querySelector('.contents').innerText = contents;
    clone.querySelector('.date').innerText = date;
    mainArea.appendChild(clone);
}

function listMemo() {
    mainArea.innerHTML = ""; /* 메모 목록을 보여주기 전에 mainArea를 비웁니다. */
    db.serialize(function () {
        db.each("SELECT rowid AS id, contents, date FROM memo", function (err, row) {
            displayMemo(row.contents, row.date);
        });
    });
}

listMemo();
```

![EleMemo]({{ site.url }}/assets/image/2020-02-12-Electron-EleMemo/2020-02-12-Electron-EleMemo_5.png)

거의 다 완성이 되어가고 있습니다! 보니까 최신 메모가 아래에 있습니다. 최신 메모를 위로 올려주기 위해 `listMemo()`의 쿼리에 `ORDER BY id DESC`를 추가해줍니다.

```javascript
function listMemo() {
    mainArea.innerHTML = ""; /* 메모 목록을 보여주기 전에 mainArea를 비웁니다. */
    db.serialize(function () {
        db.each("SELECT rowid AS id, contents, date FROM memo ORDER BY id DESC", function (err, row) {
            displayMemo(row.contents, row.date);
        });
    });
}
```

![EleMemo]({{ site.url }}/assets/image/2020-02-12-Electron-EleMemo/2020-02-12-Electron-EleMemo_6.png)

EleMemo가 완성되었습니다! 메모 수정, 삭제 기능은 각자 알아서 구현해봅시다. :smiley:
