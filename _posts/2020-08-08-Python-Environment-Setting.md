---
layout  : post
title   : "[Python] 개발 환경 설정하기"
date    : 2020-08-08 15:06:47 +0900
category: Python
---

Python를 시작하기전에 Python 개발 환경을 설정해보도록 하겠습니다.

[https://python.org](https://python.org)에 접속하여 Python을 다운로드합니다.  
2020년 8월 기준으로 Python 최신 버전은 3.8.5입니다.  
![Download Python 3.8.5]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_1.png)

Python을 설치하고, PyCharm을 설치합니다.  
**(Python을 설치할 때, 'Add Python 3.8 to PATH'는 꼭 선택해 줍니다.)**  
PyCharm은 [https://www.jetbrains.com/pycharm/download](https://www.jetbrains.com/pycharm/download)에서 다운로드할 수 있습니다. Community 버전으로 다운로드하고, 설치하면 됩니다.  
![Download PyCharm Community]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_2.png)

PyCharm를 실행해봅시다. 예제로 "Hello, World!"를 출력하는 프로그램을 만들어보겠습니다.  
'New Project' 버튼을 클릭하여 프로젝트를 생성합니다.  
![PyCharm - New Project]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_3.png)

'Location'을 `C:\Users\<사용자명>\PycharmProjects\<프로젝트명>`으로 설정하고, 'Create a main.py welcome script'는 해제합니다. 그리고 'Create' 버튼을 눌러 생성합니다.  
![PyCharm - New Project]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_4.png)

프로젝트가 생성되면, 폴더에 Python File을 생성합니다.  
![PyCharm - New Python File]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_5.png)

Python File 이름은 `HelloWorld`로 하겠습니다.  
`HelloWorld`라고 입력하고, 엔터를 누르면 `HelloWorld.py` 파일이 생성됩니다.  
![PyCharm - New Python File]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_6.png)

`HelloWorld.py` 파일을 아래와 같이 작성합니다.  
```python
print("Hello, World!")
```
그리고, 'Add Configuration...' 버튼을 눌러 설정을 합니다.  
![PyCharm - Configuration]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_7.png)

'+' 버튼을 누르고, 'Python'을 선택합니다.  
![PyCharm - Run/Debug Configuration]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_8.png)

'Name'을 `HelloWorld`로 설정하고, 'Script path'를 우리가 방금 생성한 `HelloWorld.py`를 선택해 줍니다. 모두 설정했다면, 'OK' 버튼을 눌러 설정을 완료합니다.  
![PyCharm - Run/Debug Configuration]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_9.png)

설정이 모두 완료되었다면, 'Add Configuration...'이 있던 자리에 'HelloWorld'가 보일 것입니다. 옆에 있는 실행 버튼을 누르면 하단에 우리가 작성한 프로그램이 실행이 되고 출력됩니다.  
우리가 작성한 프로그램은 "Hello, World!"를 출력하는 프로그램이기 때문에 "Hello, World!"가 출력됩니다.  
![PyCharm - Run Source Code]({{ site.url }}/assets/image/2020-08-08-Python-Environment-Setting/2020-08-08-Python-Environment-Setting_10.png)
