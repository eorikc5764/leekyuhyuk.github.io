---
layout  : post
title   : "[Linux] 리눅스 기본 명령어"
date    : 2020-01-22 20:46:11 +0900
category: Linux
---

이 글에서는 파일, 디렉토리 등을 탐색하는 기본 명령어를 배웁니다.

# Shell

Shell은 기본적으로 키보드에서 명령을 가져와 운영체제로 보내 수행하는 프로그램입니다. 리눅스 GUI 환경에서 아마도 "Terminal" 또는 "Console"과 같은 프로그램을 보았을 것입니다. 이 프로그램들은 Shell을 시작하는 프로그램입니다.

이 글에서는 Shell 프로그램 bash (Bourne Again Shell)를 사용합니다. Shell은 ksh, zsh, tsch와 같이 여러 가지 종류가 있습니다.

리눅스 배포판에 따라 Shell Prompt가 다를 수는 있지만, 대부분은 다음과 같은 형식입니다.

```
사용자명@호스트네임:현재_디렉토리 $
leekyuhyuk@KyuDevelop:/home/leekyuhyuk $
```

간단한 명령인 `echo`로 시작하겠습니다. `echo` 명령은 텍스트를 화면에 출력합니다.

```
$ echo Hello World
```

![echo Command]({{ site.url }}/assets/image/2020-01-22-Linux-Command-Line/2020-01-22-Linux-Command-Line_1.png)

# pwd (Print Working Directory)

Linux의 모든 파일은 계층적인 디렉토리 트리로 구성됩니다. 파일 시스템의 첫 번째 디렉토리(`/`) 이름은 루트 디렉토리 입니다. 루트 디렉토리에는 더 많은 폴더와 파일 등을 저장할 수 있는 많은 폴더와 파일이 있습니다. 디렉토리 트리의 예는 다음과 같습니다.

```
/
|-- bin
|   |-- file1
|   |-- file2
|-- etc
|   |-- file3
|   `-- directory1
|       |-- file4
|       `-- file5
|-- home
|-- var
```

이러한 파일과 디렉토리의 위치를 경로라고 합니다. 루트 디렉토리(`/`)에 `home`이라는 폴더가 있고 그 안에 `leekyuhyuk`이라는 폴더가 있는 경우 해당 경로는 `/home/leekyuhyuk`과 같습니다.

내가 지금 있는 위치를 알면 파일 시스템을 탐색하기 쉬워집니다. 현재 위치를 확인하려면 `pwd` 명령을 사용하면 됩니다. 이 명령은 **P**rint **W**orking **D**irectory를 의미하며 사용자가 현재 위치하고 있는 디렉토리를 표시합니다.

![pwd Command]({{ site.url }}/assets/image/2020-01-22-Linux-Command-Line/2020-01-22-Linux-Command-Line_2.png)

# cd (Change Directory)

현재 위치를 알았으니 이제 파일 시스템을 탐색해봅시다. 경로를 사용하여 탐색하며, 절대 경로와 상대 경로를 사용하여 경로를 지정할 수 있습니다.

- 절대 경로 : 루트 디렉토리의 경로입니다. 루트 디렉토리는 일반적으로 슬래시(`/`)로 표시됩니다. 경로가 `/`로 시작할 때는 루트 디렉토리에서 시작한다는 의미입니다. 예를 들면 `/home/leekyuhyuk/Desktop` 같은 걸 절대 경로라고 합니다.
- 상대 경로 : 현재 위치에 있는 상대적인 경로입니다. `/home/leekyuhyuk/Documents` 위치에 있고 `Works`라는 `Documents`내의 디렉토리로 이동하려면 `/home/leekyuhyuk/Documents/Works`와 같이 루트에서 전체 경로를 지정할 필요 없이 `Works`를 사용하면 됩니다.

원하는 디렉토리로 이동하려면 `cd` 명령을 사용하면 됩니다. 이 명령은 **C**hange **D**irectory를 의미합니다.

```
$ cd /home/leekyuhyuk/Pictures
```

위의 명령을 사용하여 디렉토리 위치를 `/home/leekyuhyuk/Pictures`로 변경하였습니다.  
이제 이 디렉토리에 `Canada`라는 폴더가 있다고 가정하면, 다음을 사용하여 해당 폴더로 이동할 수 있습니다.

```
$ cd Canada
```

이미 `/home/leekyuhyuk/Pictures`에 있었기 때문에 폴더 이름을 사용하여 디렉토리 위치를 변경할 수 있습니다.

항상 절대 경로 또는 상대 경로로 탐색하는 것이 꽤 귀찮을 수도 있습니다. 아래는 탐색할 때 도움이 되는 몇 가지 바로 가기 입니다.

- `.` (현재 디렉토리) : 현재 디렉토리 입니다.
- `..` (부모 디렉토리) : 현재 위치에서 위의 디렉토리 입니다.
- `~` (홈 디렉토리) : `/home/leekyuhyuk`와 같은 '홈 디렉토리' 입니다.
- `-` (이전 디렉토리) : 방금 전의 디렉토리로 이동합니다.

![cd Command]({{ site.url }}/assets/image/2020-01-22-Linux-Command-Line/2020-01-22-Linux-Command-Line_3.png)

# ls (List Directories)

이제 파일 시스템을 이동하는 방법을 알았으므로, `ls` 명령을 사용하여 디렉토리 안에 있는 내용을 봐봅시다. `ls` 명령은 기본적으로 현재 디렉토리의 폴더와 파일을 나열하지만, 원하는 디렉토리의 폴더와 파일도 나열할 수도 있습니다.

```
$ ls
$ ls /home/leekyuhyuk
```

`ls`는 매우 유용한 명령이며, 파일 및 디렉토리에 대한 자세한 정보도 표시합니다. 하지만 `ls`가 디렉토리의 모든 파일을 볼 수 있는 것은 아닙니다. 리눅스에서 `.`로 시작하는 파일 이름은 숨김 파일인데 `ls` 명령만으로는 볼 수가 없습니다. 숨김 파일까지 모두 보고 싶다면 `-a` 옵션을 추가해야 합니다. (`-a`는 All을 의미합니다.)

```
$ ls -a
```

또 하나의 유용한 `-l` 옵션이 있으며, 이 옵션은 파일의 자세한 목록을 보여줍니다. 파일 권한, 소유자 이름, 소유자 그룹, 파일 크기, 마지막 수정 시간, 파일 또는 디렉토리 이름 등등을 보여줍니다.

```
$ ls -l
drwxr-x--- 7 leekyuhyuk kyudevelop   4096 Nov 20 16:37 Desktop
drwxr-x--- 2 leekyuhyuk kyudevelop   4096 Oct 19 10:46  Documents
drwxr-x--- 4 leekyuhyuk kyudevelop   4096 Nov 20 09:30 Downloads
drwxr-x--- 2 leekyuhyuk kyudevelop   4096 Oct  7 13:13   Music
drwxr-x--- 2 leekyuhyuk kyudevelop   4096 Sep 21 14:02 Pictures
drwxr-x--- 2 leekyuhyuk kyudevelop   4096 Jul 27 12:41   Public
drwxr-x--- 2 leekyuhyuk kyudevelop   4096 Jul 27 12:41   Templates
drwxr-x--- 2 leekyuhyuk kyudevelop   4096 Jul 27 12:41   Videos
```

이러한 옵션들은 동시에 적용이 가능합니다 `-l`과 `-a`를 함께 사용하고 싶다면, `-la`를 입력하면 됩니다.

```
$ ls -la
```

# touch

파일을 만드는 간단한 방법은 `touch` 명령을 사용하는 것입니다. `touch` 명령을 사용하면, 빈 파일을 만들 수 있습니다.

```
$ touch 파일명
```

리다이렉션(Redirection) 및 텍스트 편집기(`vi`, `nano` 등등)와 같은 파일을 만드는 여러 가지 방법이 있지만, 자세한 것은 나중에 알아보도록 합시다.

# file

파일이 어떤 종류의 파일인지 확인하기 위해 file 명령을 사용할 수 있습니다. 파일 내용에 대한 설명이 표시됩니다.

![file Command]({{ site.url }}/assets/image/2020-01-22-Linux-Command-Line/2020-01-22-Linux-Command-Line_4.png)

# cat

파일을 읽는 방법을 알아보겠습니다. 파일을 읽는 제일 간단한 명령은 `cat` 명령으로, 'Concatenate'의 줄임말으로, 파일 내용을 표시할 뿐만 아니라 여러 파일을 결합하여 출력할 수도 있습니다.

![cat Command]({{ site.url }}/assets/image/2020-01-22-Linux-Command-Line/2020-01-22-Linux-Command-Line_5.png)

큰 파일을 보는 데는 좋지 않으며, 내용이 짧은 경우에 주로 사용됩니다.

# less

`less` 명령어는 내용이 짧은 텍스트 파일 보다는 내용이 길은 텍스트 파일을 볼 경우에 사용됩니다. 텍스트가 페이지 방식으로 표시되므로, 텍스트 파일을 한 페이지씩 탐색 할 수 있습니다.

`less` 명령을 사용하면, 키보드를 사용하여 파일을 탐색할 수 있습니다.

```
$ less 파일명
```

![less Command]({{ site.url }}/assets/image/2020-01-22-Linux-Command-Line/2020-01-22-Linux-Command-Line_6.png)

`less`를 사용하여 텍스트 파일을 탐색하려면 다음과 같이 사용하면 됩니다.
- `q` : `less`를 나와 다시 Shell로 돌아갈 때 사용합니다.
- Page up, Page Down, Up, Down : 화살표 키와 페이지 키를 사용하여 탐색합니다.
- `g` : 텍스트 파일의 시작으로 이동합니다.
- `G` : 텍스트 파일의 끝으로 이동합니다.
- `/` : 텍스트 문서 내에서 특정 텍스트를 검색할 수 있습니다.
  - 예) 텍스트 문서에서 `이규혁`을 검색하고 싶다면, `/이규혁`을 입력하고 Enter를 누르면 됩니다.
- `h` : 도움말입니다. `less`를 사용하는 방법이 나와있습니다.

# history

Shell에는 이전에 입력 한 명령들을 기록합니다. `history` 명령을 사용하여 이전에 입력 한 명령들을 살펴볼 수 있습니다.

```
$ history
  1  pwd
  2  cd .
  3  pwd
  4  cd ..
  5  pwd
  6  cd -
  7  pwd
  8  cd ~
  9  pwd
 10  ls -R
 11  history
```

만약 이전에 동일한 명령을 실행하려면, ↑키를 눌러 선택할 수 있습니다.

# clear

사용하다 보면 화면이 지저분해질 때가 있습니다. `clear` 명령을 사용하여 화면에 있는 모든 것을 지울 수 있습니다.

# cp (Copy)

`cp` 명령어를 사용하여 파일을 복사하여 붙여 넣을 수 있습니다.

```
$ cp mywork /home/leekyuhyuk/Documents
```
`mywork`는 복사할 파일이고, `/home/leekyuhyuk/Documents`는 파일을 복사할 위치입니다.

와일드카드(Wildcard)를 사용하여 여러 파일과 디렉토리를 복사할 수 있습니다. 와일드카드는 모든 명령에 사용이 가능합니다.

- `*`는 길이가 0 이상인 임의의 문자열입니다.
  - 예를 들어 `a*`는 `a`, `ab`, `abc` 등 `a`로 시작하는 모든 글자를 의미합니다.
- `?`는 임의의 한 문자입니다.
  - 예를 들면, `123?`은 `1234`와 `1235`와 같은 글자를 의미합니다.

```
$ cp *.jpg /home/leekyuhyuk/Pictures
```

위의 명령은 현재 디렉토리에 확장자가 `.jpg`인 모든 파일이 `Pictures` 디렉토리에 복사됩니다.

`-r` 옵션을 사용하면, 디렉토리 내의 파일과 디렉토리를 복사할 수 있습니다.

```
$ cp -r works /home/leekyuhyuk/Documents
```

실수로 덮어쓰지 않으려는 파일이 있으면, `-i` 옵션을 사용하여 파일을 덮어쓰기 전에 메세지를 표시할 수 있습니다.

# mv (Move)

`mv` 명령은 파일을 이동하거나 이름을 바꾸는데 사용됩니다. `cp` 명령과 매우 유사합니다.  
다음과 같이 파일 이름을 바꿀 수 있습니다.

```
$ mv oldfile newfile
```

또는 파일을 다른 디렉토리로 옮길 수 있습니다.

```
$ mv myfile /home/leekyuhyuk/Documents
```

여러 개의 파일도 이동이 가능합니다.

```
$ mv file_1 file_2 /somedirectory
```

디렉토리의 이름을 바꿀 수도 있습니다.

```
$ mv directory1 directory2
```

`cp` 명령어와 마찬가지로 파일이나 디렉토리를 `mv`로 지정하면, 덮어쓰기도 가능합니다. `-i` 옵션을 사용하여 파일을 덮어쓰기 전에 메세지를 표시할 수 있습니다.

```
$ mv -i directory1 directory2
```

# mkdir (Make Directory)

`mkdir`을 사용하여 디렉토리를 만들 수 있습니다. 여러 디렉토리를 동시에 만들 수도 있습니다.

```
$ mkdir books paintings
```

`-p` 옵션을 사용하여 하위 디렉토리를 동시에 만들 수도 있습니다.

```
$ mkdir -p books/hemmingway/favorites
```

# rm (Remove)

파일을 제거하려면 `rm` 명령을 사용하면 됩니다, `rm` 명령은 파일과 디렉토리를 삭제하는데 사용됩니다.

```
$ rm file1
```

`-f` 옵션은 쓰기 권한이 있는지 여부에 관계없이 강제로 파일을 제거할 수 있습니다.

```
$ rm -f file1
```

위의 `cp`, `mv` 명령과 마찬가지로 `-i` 옵션을 사용하면 파일 또는 디렉토리를 제거할 것인지 묻는 메세지가 출력됩니다.

```
$ rm -i file
```

디렉토리를 삭제하려면 `-r` 옵션을 추가하여 사용하면 됩니다. 이는 모든 파일과 하위 디렉토리를 삭제합니다.

```
$ rm -r directory
```

# find

```
$ find /home -name cat.jpg
```

`find`를 사용하려면 검색할 디렉토리, 검색 대상을 지정해야 합니다. 위의 명령어의 경우에는 `cat.jpg`라는 이름으로 `/home`에서 파일을 찾으려고 합니다.

찾으려는 파일 형식을 지정할 수 있습니다.

```
$ find /home -type d -name MyFolder
```

위의 명령어는 `MyFolder`라는 디렉토리(`d`)에 대해 검색을 하게 됩니다.

# man

`man` 명령어를 사용하여 명령에 대한 매뉴얼을 볼 수 있습니다.

```
$ man ls
```

![man Command]({{ site.url }}/assets/image/2020-01-22-Linux-Command-Line/2020-01-22-Linux-Command-Line_7.png)
