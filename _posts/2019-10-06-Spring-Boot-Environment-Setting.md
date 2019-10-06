---
layout  : post
title   : "[Spring Boot] 개발 환경 설정"
date    : 2019-10-06 16:13:15 +0900
category: Spring-Boot
---

Spring Boot을 공부하기 앞서 개발 환경을 설정할 것입니다.  
이 글은 'Ubuntu 18.04 LTS' 환경에서 개발 환경을 설정합니다.

- JDK 8u221
- IntelliJ IDEA Community 2019.2.3

# JDK Installation

[Java SE Development Kit 8 Downloads](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에 접속하여 'Accept License Agreement'를 선택한 후 `jdk-8u221-linux-x64.tar.gz`를 다운로드 합니다.  
![Java SE Development Kit 8 Downloads]({{ site.url }}/assets/image/2019-10-06-Spring-Boot-Environment-Setting/2019-10-06-Spring-Boot-Environment-Setting_1.png)

다음 명령어를 통하여 JDK를 설치합니다.

```sh
$ sudo tar xvzf jdk-8u221-linux-x64.tar.gz -C /opt/
$ sudo update-alternatives --install "/usr/bin/java" "java" "/opt/jdk1.8.0_221/bin/java" 1
$ sudo update-alternatives --install "/usr/bin/javac" "javac" "/opt/jdk1.8.0_221/bin/javac" 1
$ sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/opt/jdk1.8.0_221/bin/javaws" 1
$ sudo update-alternatives --set java /opt/jdk1.8.0_221/bin/java
$ sudo update-alternatives --set javac /opt/jdk1.8.0_221/bin/javac
$ sudo update-alternatives --set javaws /opt/jdk1.8.0_221/bin/javaws
```

`java -version`와 `javac -version`으로 버전을 확인하여 설치가 잘 되었는지 확인합니다.
![Java SE Development Kit 8]({{ site.url }}/assets/image/2019-10-06-Spring-Boot-Environment-Setting/2019-10-06-Spring-Boot-Environment-Setting_2.png)

# IntelliJ IDEA Installation

[Download IntelliJ IDEA](https://www.jetbrains.com/idea/download)에 접속하여 IntelliJ IDEA Community를 다운로드 합니다.  
![Download IntelliJ IDEA]({{ site.url }}/assets/image/2019-10-06-Spring-Boot-Environment-Setting/2019-10-06-Spring-Boot-Environment-Setting_3.png)

다음 명령어를 통하여 IntelliJ IDEA를 설치합니다.

```sh
$ sudo tar xvzf ideaIC-2019.2.3.tar.gz -C /opt/
$ /opt/idea-IC-192.6817.14/bin/idea.sh
```

IntelliJ IDEA가 실행되면, 'Configure'를 클릭한 뒤, 'Structure for New Projects'를 선택합니다.
![IntelliJ IDEA]({{ site.url }}/assets/image/2019-10-06-Spring-Boot-Environment-Setting/2019-10-06-Spring-Boot-Environment-Setting_4.png)

'New...' 버튼을 눌러 'JDK'를 선택한 뒤, 위에서 설치한 JDK의 경로인 `/opt/jdk1.8.0_221`를 선택합니다.
![IntelliJ IDEA]({{ site.url }}/assets/image/2019-10-06-Spring-Boot-Environment-Setting/2019-10-06-Spring-Boot-Environment-Setting_5.png)
