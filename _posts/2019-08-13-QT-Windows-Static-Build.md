---
layout  : post
title   : "[QT 5.13.0] Windows에서 Static Build"
date    : 2019-08-13 23:11:01 +0900
category: QT
---

Python을 설치합니다. (Build 할 때 필요합니다)  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_1.png)

Python을 설치 할 때 'Add Python to PATH'를 꼭 선택합니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_2.png)

그리고 QT를 설치할 때, Sources도 함께 설치합니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_3.png)

`C:\Qt\Qt5.13.0\5.13.0`에 있는 `Src` 폴더를 `C:\Qt`에 복사합니다.  
`C:\Qt\Src\qtbase\mkspecs\win32-g++`에 있는 `qmake.conf`에 아래를 추가합니다.
```
QMAKE_LFLAGS += -static -static-libgcc
QMAKE_CFLAGS_RELEASE -= -O2
QMAKE_CFLAGS_RELEASE += -Os -momit-leaf-frame-pointer
DEFINES += QT_STATIC_BUILD
```

'Qt 5.13.0 (MinGW 7.3.0 64-bit)'를 실행합니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_4.png)

`cd C:\Qt\Src`로 이동한 뒤, 아래의 명령어를 입력합니다.
```
configure -static -release -platform win32-g++ -prefix C:\Qt\qt-5.13.0-static -qt-zlib -qt-pcre -qt-libpng -qt-libjpeg -qt-freetype -opengl desktop -no-openssl -opensource -confirm-license -make libs -nomake tools -nomake examples -nomake tests
```

완료되면 아래 명령어로 빌드를 합니다.
```
mingw32-make -k -j4
```

빌드가 완료되면 아래 명령어로 설치합니다.
```
mingw32-make -k install
```

설치가 완료되면, `C:\Qt\qt-5.13.0-static\mkspecs\win32-g++`의 `qmake.conf`에 아래 내용을 추가합니다.
```
CONFIG += static
```

'Tools' - 'Options...' - 'Kits'에 들어가서 'Qt Versions' 탭에 있는 'Add...'를 클릭한 뒤, `C:\Qt\qt-5.13.0-static\bin\qmake.exe`를 선택합니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_5.png)

'Kit' 탭에 있는 'Add'를 클릭합니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_6.png)

아래와 같이 입력 및 선택합니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_7.png)

'Projects'에 들어가서 기존에 있던 Kit을 Disable하고, 방금 생성한 'QT 5.13.0 Static Build'를 Enable합니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_8.png)

'Release'로 바꾸고, Build를 합니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_9.png)

빌드 폴더에 가보시면 정적빌드(Static Build)된 `.exe` 파일을 볼 수 있습니다.  
![QT Windows Static Build]({{ site.url }}/assets/image/2019-08-13-QT-Windows-Static-Build/2019-08-13-QT-Windows-Static-Build_10.png)
