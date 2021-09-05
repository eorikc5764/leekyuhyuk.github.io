---
layout: post
title: "HEIC를 JPG로 손쉽게 바꿔주는 iHEIC 개발 후기"
date: 2021-09-06 00:49:09 +0900
category: Ordinary-Life
---

여자친구가 회사 업무로 아이폰으로 찍은 사진을 Windows PC로 옮길 일이 잦은데 일일이 변환해 주기 귀찮기도 하고, 그렇다고 웹 사이트에서 JPG로 변환해서 쓰라고 하기에는 서비스를 제공하는 웹사이트의 서버에 저장되는 걸 원치 않을 거 같아 iHEIC라는 프로그램을 만들어보았습니다.

제가 기존에 사용했던 프로그램은 [strukturag](https://github.com/strukturag)님이 만든 [libheif](https://github.com/strukturag/libheif)를 이용하였습니다. 문제는 터미널에서 명령어를 입력해야 하기 때문에 이런 것에 익숙하지 않은 사람들은 사용하기 힘들 것으로 보여 iHEIC를 만들었습니다.

**iHEIC는 아래와 같이 동작합니다:**

1. iHEIC에 변환할 HEIC 파일을 드래그하여 추가합니다.
2. Start 버튼을 눌러 변환을 추가합니다.
3. iHEIC에 추가한 파일이 있었던 위치에 변환된 JPG 파일이 위치하게 됩니다.

![iHEIC Preview]({{ site.url }}/assets/image/2021-09-06-iHEIC-Development-Story/2021-09-06-iHEIC-Development-Story_1.gif)

## 왜 Electron으로 개발했나요?

Windows, Linux, macOS 모두 지원하는 프로그램을 만들고 싶었습니다!

Electron으로 개발하게 되면 소스코드 하나로 여러 OS에서 동작하는 프로그램을 만들 수 있습니다.  
또한 [React](https://reactjs.org/)와 [Ant Design](https://ant.design/) Component를 사용한다면 간편하게 GUI 프로그램을 만들 수 있습니다.

![iHEIC running on Windows, Linux and macOS]({{ site.url }}/assets/image/2021-09-06-iHEIC-Development-Story/2021-09-06-iHEIC-Development-Story_2.png)

## 개발하면서 힘들었던 점은 무엇인가요?

[libheif](https://github.com/strukturag/libheif)에 있는 `heif-convert`를 정적으로 컴파일하여 libjpeg, libde265, libheif 라이브러리가 설치되어 있지 않은 환경에서도 사용할 수 있게 만들고 싶었습니다.  
하지만, libc가 정적으로 빌드가 되어있지 않아서 Linking하는 단계에서 오류가 발생하여 결국 [x86_64-static-linux-musl](https://github.com/LeeKyuHyuk/x86_64-static-linux-musl)라는 크로스 컴파일러를 만들어 해결하였습니다.  
문제는 여기서 끝이 아니었습니다. `heif-convert`를 Windows, macOS으로 크로스 컴파일을 하려고 했는데 Windows는 `x86_64-w64-mingw32` 크로스 컴파일러를 빌드 하여 해결하였지만 macOS는 `x86_64-apple-darwin`와 같은 크로스 컴파일러를 만드는 방법을 몰라 직접 Macbook Air에서 빌드 하려고 했습니다.  
macOS는 기본으로 LLVM을 사용하는데 `LDFLAGS`에 `-static`을 넣어 빌드를 할 수 없다는 걸 한 시간이 지나서야 알게 되었습니다.

> [ld: library not found for -lcrt0.o on OSX 10.6 with gcc/clang -static flag](https://stackoverflow.com/questions/3801011/ld-library-not-found-for-lcrt0-o-on-osx-10-6-with-gcc-clang-static-flag)

이런저런 힘들었던 일이 있었지만 iHEIC를 다 만들고 돌아보니 이러한 과정도 좋은 경험이었다고 생각이 듭니다.

## 마무리 인사

오랜만에 즐겁게 코딩을 할 수 있어서 너무 좋았습니다! 아마 다음 코딩은 Electron 기반의 게임을 만들 거 같습니다.  
왜냐면... 제가 요즘 카이로소프트의 '던전마을 스토리2'를 너무 재밌게 하고 있어서요...!  
이런 스타일의 게임을 만들어보려고 합니다.  
시간 나면 React Native도 사용해보려 합니다!

긴 글 읽어주셔서 감사합니다!😀
