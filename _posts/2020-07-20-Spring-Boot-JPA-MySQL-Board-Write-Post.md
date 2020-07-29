---
layout  : post
title   : "[Spring Boot] 게시판 구현 하기 (1) - 글 작성 & 글 목록 출력"
date    : 2020-07-20 07:21:24 +0900
category: Spring-Boot
---

이번 시간에는 Spring Boot와 MySQL를 연동하고 게시판 기능의 글 작성과 글 목록 출력을 구현해보겠습니다.

# MySQL Installation

[MySQL](https://www.mysql.com)에 접속하여 'Download'로 들어갑니다.  
![Enter MySQL Site]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_1.png)

[MySQL Community (GPL) Downloads](https://dev.mysql.com/downloads)로 들어갑니다.  
![Click MySQL Community (GPL) Downloads]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_2.png)

[MySQL Installer for Windows](https://dev.mysql.com/downloads/windows)로 들어갑니다.  
![Click MySQL Installer for Windows]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_3.png)

'Download' 버튼을 눌러 MySQL 설치 파일을 다운로드합니다.  
![Download MySQL Installer for Windows]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_4.png)

다운로드가 완료되면, 설치를 시작합니다.  
'Developer Default'를 선택하고, 'Next'를 누릅니다.  
![Install MySQL Step 1]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_5.png)

'Execute'를 눌러 필요한 프로그램을 설치합니다.  
![Install MySQL Step 2]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_6.png)

'Execute'를 눌러 MySQL 설치를 진행합니다.  
![Install MySQL Step 3]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_7.png)

'Standalone MySQL Server / Classic MySQL Relication'를 선택하고, 'Next'를 누릅니다.  
![Install MySQL Step 4]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_8.png)

Root 암호를 설정하고, 'Next'를 누릅니다.  
![Install MySQL Step 5]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_9.png)

'Execute'를 눌러 MySQL 설정을 하고, 완료되면 'Finish'를 눌러 설치를 완료합니다.  
![Install MySQL Step 6]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_10.png)

'시스템 환경변수'의 `PATH`에 `C:\Program Files\MySQL\MySQL Server 8.0\bin`를 추가하고, '명령 프롬프트'에서 아래와 같이 잘 작동하는지 확인해봅니다.  
![MySQL Command Line]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_11.png)

# 프로젝트 생성하기

[Spring Initializr](https://start.spring.io)에 접속하여 아래와 같이 설정하여 프로젝트를 생성합니다.  
Dependencies에는 'Spring Web', 'Spring Data JPA', 'MySQL Driver', 'Thymeleaf', 'Lombok'을 추가합니다.  
![Spring Initializr]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_12.png)

`build.gradle`를 보면 아래와 같이 작성이 되어 있을 것입니다.

```gradle
plugins {
	id 'org.springframework.boot' version '2.3.2.RELEASE'
	id 'io.spring.dependency-management' version '1.0.9.RELEASE'
	id 'java'
}

group = 'kr.kyuhyuk'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '14'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'mysql:mysql-connector-java'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
}

test {
	useJUnitPlatform()
}
```

손쉽게 화면을 구현할 수 있도록 Bootstrap를 추가해 줍시다.  
`dependencies`에 `runtimeOnly 'org.webjars:bootstrap:4.5.0'`를 추가하면 됩니다.

```gradle
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'mysql:mysql-connector-java'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
	runtimeOnly 'org.webjars:bootstrap:4.5.0'
}
```

# IntelliJ IDEA에 Lombok 플러그인 설치

'Lombok'은 Getter, Setter, toString와 같은 것들을 자동 생성해 주는 유용한 라이브러리입니다.  
이것을 IntelliJ IDEA에서 사용하려면, Gradle에 추가하는 것뿐만 아니라 플러그인도 설치해야 합니다.

IntelliJ IDEA에서 'File'→'Settings'에 들어갑니다.  
![IntelliJ IDEA Settings]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_13.png)

'Plugins'에서 'Lombok'을 검색하고, 설치합니다.  
![IntelliJ IDEA Settings]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_14.png)

# MySQL에 데이터베이스 만들고 설정하기

'명령 프롬프트'에서 아래 명령어를 입력하여 `example` 데이터베이스를 만들고, `user` 계정(비밀번호는 `UserPassword`입니다)을 만듭니다.

```
mysql -u root -p

mysql> create database example; -- example라는 데이터베이스를 생성합니다.
mysql> create user 'user'@'%' identified by 'UserPassword'; -- user라는 사용자를 생성합니다.
mysql> grant all on example.* to 'user'@'%'; -- user 사용자에게 example 데이터베이스의 모든 권한을 줍니다.
```

![MySQL Create Database and User]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_15.png)

데이터베이스와 사용자를 생성했으면, `src\main\resources`에 있는 `application.properties`를 아래와 같이 작성합니다.

```
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/example?serverTimezone=Asia/Seoul&characterEncoding=UTF-8
spring.datasource.username=user
spring.datasource.password=UserPassword
```

- `spring.jpa.hibernate.ddl-auto` : JAVA의 Entity를 참고하여, Spring Boot 실행 시점에 자동으로 필요한 데이터베이스의 테이블 설정을 자동으로 해줍니다.
	- `none` : 아무것도 실행하지 않습니다.
	- `create` : SessionFactory 시작 시점에 Drop을 실행하고 Create를 실행합니다.
	- `create-drop` : SessionFactory 시작 시점에 Drop 후 Create를 실행하며, SessionFactory 종료 시 Drop 합니다.
	- `update` : 변경된 Schema를 적용합니다. (데이터는 유지됩니다)
	- `validate` : `update`처럼 Object를 검사하지만, Schema는 아무것도 건들지 않습니다. 변경된 Schema가 존재하면 변경사항을 출력하고 서버를 종료합니다.
- `spring.datasource.url` : 데이터베이스의 URL입니다. 위의 URL은 example 데이터베이스를 입력했습니다.
	- `serverTimezone` : 데이터베이스 서버의 시간을 'Asia/Seoul'로 설정합니다.
	- `characterEncoding` : 인코딩 방식을 'UTF-8'로 설정합니다.
- `spring.datasource.username` : 데이터베이스에 접근할 사용자명 입니다.
- `spring.datasource.password` : 데이터베이스에 접근할 사용자의 비밀번호 입니다.

# 게시물 목록 및 작성 페이지 만들기

`src\main\resources\templates`에 `board`와 `common` 폴더를 만듭니다.  
`board` 폴더에는 게시판과 관련된 `html` 파일이 저장되고, `common` 폴더에는 `board`에서 사용하는 공통적인 파일들을 관리할 것입니다.

`common` 폴더에 `header.html` 파일을 만들고, 아래와 같이 작성합니다. 이 파일은 각 페이지의 헤더(Header)를 담당할 것입니다.

```html
<div class="navbar navbar-dark bg-dark shadow-sm mb-3">
    <div class="container d-flex justify-content-between">
        <a href="/" class="navbar-brand d-flex align-items-center">
            <strong>게시판</strong>
        </a>
    </div>
</div>
```

게시물 목록을 나타내는 페이지를 만들어봅시다.  
`board` 폴더에 `list.html` 파일을 만들고, 아래와 같이 작성합니다.

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>게시판 - 목록</title>
    <link rel='stylesheet' href='/webjars/bootstrap/4.5.0/css/bootstrap.min.css'>
</head>
<body>
<header th:insert="common/header.html"></header>
<div class="container">
    <table class="table">
        <thead class="thead-light">
        <tr class="text-center">
            <th scope="col">#</th>
            <th scope="col">제목</th>
            <th scope="col">작성자</th>
            <th scope="col">작성일</th>
        </tr>
        </thead>
        <tbody>
        <tr class="text-center" th:each="post : ${postList}">
            <th scope="row">
                <span th:text="${post.id}"></span>
            </th>
            <td>
                <a th:href="@{'/post/' + ${post.id}}">
                    <span th:text="${post.title}"></span>
                </a>
            </td>
            <td>
                <span th:text="${post.author}"></span>
            </td>
            <td>
                <span th:text="${#temporals.format(post.createdDate, 'yyyy-MM-dd HH:mm')}"></span>
            </td>
        </tr>
        </tbody>
    </table>
    <div class="row">
        <div class="col-auto mr-auto"></div>
        <div class="col-auto">
            <a class="btn btn-primary" th:href="@{/post}" role="button">글쓰기</a>
        </div>
    </div>
</div>
<script src="/webjars/jquery/3.5.1/jquery.min.js"></script>
<script src="/webjars/bootstrap/4.5.0/js/bootstrap.min.js"></script>
</body>
</html>
```

마지막으로 게시물을 등록하는 페이지를 만들어봅시다.  
`board` 폴더에 `post.html` 파일을 만들고, 아래와 같이 작성합니다.

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>게시판 - 글쓰기</title>
    <link rel='stylesheet' href='/webjars/bootstrap/4.5.0/css/bootstrap.min.css'>
</head>
<body>
<header th:insert="common/header.html"></header>
<div class="container">
    <form action="/post" method="post">
        <div class="form-group row">
            <label for="inputTitle" class="col-sm-2 col-form-label"><strong>제목</strong></label>
            <div class="col-sm-10">
                <input type="text" name="title" class="form-control" id="inputTitle">
            </div>
        </div>
        <div class="form-group row">
            <label for="inputAuthor" class="col-sm-2 col-form-label"><strong>작성자</strong></label>
            <div class="col-sm-10">
                <input type="text" name="author" class="form-control" id="inputAuthor">
            </div>
        </div>
        <div class="form-group row">
            <label for="inputContent" class="col-sm-2 col-form-label"><strong>내용</strong></label>
            <div class="col-sm-10">
                <textarea type="text" name="content" class="form-control" id="inputContent"></textarea>
            </div>
        </div>
        <div class="row">
            <div class="col-auto mr-auto"></div>
            <div class="col-auto">
                <input class="btn btn-primary" type="submit" role="button" value="글쓰기">
            </div>
        </div>
    </form>
</div>
<script src="/webjars/jquery/3.5.1/jquery.min.js"></script>
<script src="/webjars/bootstrap/4.5.0/js/bootstrap.min.js"></script>
</body>
</html>
```

# 게시물 작성 구현하기

게시물을 작성하면, MySQL에 데이터가 저장되도록 구현해보겠습니다.

## Controller 구현하기

Controller는 사용자의 HTTP 요청이 진입하는 지점이며, 사용자에게 서버에서 처리된 데이터를 View와 함께 응답하게 해줍니다.

`controller` 패키지를 만들고, 그 안에 `BoardController`라는 클래스를 생성하고 아래의 내용을 입력합니다.

```java
package kr.kyuhyuk.board.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class BoardController {

    @GetMapping("/")
    public String list() {
        return "board/list.html";
    }

    @GetMapping("/post")
    public String post() {
        return "board/post.html";
    }
}
```

실행하면, 아래와 같이 출력될 것입니다.

![Board - List]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_16.png)

![Board - Write Post]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_17.png)

제목, 작성자, 내용을 작성하면 입력한 데이터가 데이터베이스에 저장되어야 합니다. 글쓰기 페이지에서 '글쓰기' 버튼을 누르면, Post 방식으로 `/post`에 요청이 옵니다.

우리는 Post 방식의 요청을 받아서, Service에서 처리되도록 할 것입니다.  
> DTO(Data Access Object)를 통하여 Controller와 Service 사이의 데이터를 주고받을 것입니다.

## Entity 구현하기

Entity는 데이터베이스 테이블과 매핑되는 객체입니다.

`domain` 패키지를 만들고, 그 안에 `entity` 패키지를 만듭니다.  
`domain.entity` 패키지 안에 `Board` 클래스를 생성하고 아래의 내용을 입력합니다.

```java
package kr.kyuhyuk.board.domain.entity;

import lombok.AccessLevel;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@Getter
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@EntityListeners(AuditingEntityListener.class) /* JPA에게 해당 Entity는 Auditiong 기능을 사용함을 알립니다. */
public class Board {

    @Id
    @GeneratedValue
    private Long id;

    @Column(length = 10, nullable = false)
    private String author;

    @Column(length = 100, nullable = false)
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime modifiedDate;

    @Builder
    public Board(Long id, String author, String title, String content) {
        this.id = id;
        this.author = author;
        this.title = title;
        this.content = content;
    }
}
```

JPA Auditing 기능을 사용하기 위해 main 클래스(`BoardApplication`)에 `@EnableJpaAuditing` 어노테이션을 붙여줍니다.

```java
package kr.kyuhyuk.board;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@EnableJpaAuditing
@SpringBootApplication
public class BoardApplication {

	public static void main(String[] args) {
		SpringApplication.run(BoardApplication.class, args);
	}

}
```

## Repository 구현하기

Repository는 데이터 조작을 담당하며, JpaRepository를 상속받습니다.  
JpaRepository의 값은 매핑할 Entity와 Id의 타입입니다.

`domain` 패키지안에 `repository` 패키지를 만들고, 그 안에 `BoardRepository` 인터페이스를 만듭니다.

```java
package kr.kyuhyuk.board.domain.repository;

import kr.kyuhyuk.board.domain.entity.Board;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BoardRepository extends JpaRepository<Board, Long> {
}
```

## DTO 구현하기

Controller와 Service 사이에서 데이터를 주고받는 DTO(Data Access Object)를 구현합니다.

`dto` 패키지를 만들고, 그 안에 `BoardDto` 클래스를 만듭니다.  
아래 코드의 `toEntity()`는 DTO에서 필요한 부분을 빌더 패턴을 통해 Entity로 만드는 일을 합니다.

```java
package kr.kyuhyuk.board.dto;

import kr.kyuhyuk.board.domain.entity.Board;
import lombok.*;

import java.time.LocalDateTime;

@Getter
@Setter
@ToString
@NoArgsConstructor
public class BoardDto {
    private Long id;
    private String author;
    private String title;
    private String content;
    private LocalDateTime createdDate;
    private LocalDateTime modifiedDate;

    public Board toEntity() {
        Board build = Board.builder()
                .id(id)
                .author(author)
                .title(title)
                .content(content)
                .build();
        return build;
    }

    @Builder
    public BoardDto(Long id, String author, String title, String content, LocalDateTime createdDate, LocalDateTime modifiedDate) {
        this.id = id;
        this.author = author;
        this.title = title;
        this.content = content;
        this.createdDate = createdDate;
        this.modifiedDate = modifiedDate;
    }
}
```

## Service 구현하기

위에서 만들어준 Repository를 사용하여 Service를 구현합니다.  
글쓰기 Form에서 내용을 입력한 뒤, '글쓰기' 버튼을 누르면 Post 형식으로 요청이 오고, BoardService의 `savePost()`를 실행하게 됩니다.  
`service` 패키지를 만들고, 그 안에 `BoardService` 클래스를 만듭니다.

```java
package kr.kyuhyuk.board.service;

import kr.kyuhyuk.board.domain.repository.BoardRepository;
import kr.kyuhyuk.board.dto.BoardDto;
import org.springframework.stereotype.Service;

import javax.transaction.Transactional;

@Service
public class BoardService {
    private BoardRepository boardRepository;

    public BoardService(BoardRepository boardRepository) {
        this.boardRepository = boardRepository;
    }

    @Transactional
    public Long savePost(BoardDto boardDto) {
        return boardRepository.save(boardDto.toEntity()).getId();
    }
}

```

## Controller 수정하기

글쓰기 Form에서 받은 데이터는 '글쓰기' 버튼을 누르면 `/post`로 Post 요청을 하게 됩니다.  
Entity, Repository, DTO, Service를 구현했으니 `BoardController`에 Post로 받은 데이터를 데이터베이스에 추가하는 것을 추가해 줍니다.

```java
package kr.kyuhyuk.board.controller;

import kr.kyuhyuk.board.dto.BoardDto;
import kr.kyuhyuk.board.service.BoardService;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;

@Controller
public class BoardController {
    private BoardService boardService;

    public BoardController(BoardService boardService) {
        this.boardService = boardService;
    }

    @GetMapping("/")
    public String list() {
        return "board/list.html";
    }

    @GetMapping("/post")
    public String post() {
        return "board/post.html";
    }

    @PostMapping("/post")
    public String write(BoardDto boardDto) {
        boardService.savePost(boardDto);
        return "redirect:/";
    }
}
```

Controller를 수정하고, 서버를 실행한 뒤 글쓰기에서 데이터를 입력하면 MySQL에 데이터가 기록된 것을 확인할 수 있습니다.  
![SQL Data Check]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_18.png)

# 게시물 목록 구현하기

위의 과정을 통해 게시물이 MySQL에 저장되는 것을 확인하였습니다.  
이제 저장된 데이터를 목록에 출력하는 것을 구현해보겠습니다.

## Service 수정하기

Service에서 게시물의 목록을 가져오는 `getBoardList()`를 구현합니다.  
Repository에서 모든 데이터를 조회하여, `BoardDto` List에 데이터를 넣어 반환합니다.  
`BoardService` 클래스에 아래와 같이 `getBoardList()`를 추가합니다.

```java
package kr.kyuhyuk.board.service;

import kr.kyuhyuk.board.domain.entity.Board;
import kr.kyuhyuk.board.domain.repository.BoardRepository;
import kr.kyuhyuk.board.dto.BoardDto;
import org.springframework.stereotype.Service;

import javax.transaction.Transactional;
import java.util.ArrayList;
import java.util.List;

@Service
public class BoardService {
    private BoardRepository boardRepository;

    public BoardService(BoardRepository boardRepository) {
        this.boardRepository = boardRepository;
    }

    @Transactional
    public Long savePost(BoardDto boardDto) {
        return boardRepository.save(boardDto.toEntity()).getId();
    }

    @Transactional
    public List<BoardDto> getBoardList() {
        List<Board> boardList = boardRepository.findAll();
        List<BoardDto> boardDtoList = new ArrayList<>();

        for(Board board : boardList) {
            BoardDto boardDto = BoardDto.builder()
                    .id(board.getId())
                    .author(board.getAuthor())
                    .title(board.getTitle())
                    .content(board.getContent())
                    .createdDate(board.getCreatedDate())
                    .build();
            boardDtoList.add(boardDto);
        }
        return boardDtoList;
    }
}
```

## Controller 수정하기

게시물의 목록을 가져오는 `getBoardList()`를 만들었으니, 가져온 데이터를 Model을 통해 View에 전달해 줍니다.  
`BoardController` 클래스의 `list()`를 아래와 같이 수정합니다.

```java
package kr.kyuhyuk.board.controller;

import kr.kyuhyuk.board.dto.BoardDto;
import kr.kyuhyuk.board.service.BoardService;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;

import java.util.List;

@Controller
public class BoardController {
    private BoardService boardService;

    public BoardController(BoardService boardService) {
        this.boardService = boardService;
    }

    @GetMapping("/")
    public String list(Model model) {
        List<BoardDto> boardDtoList = boardService.getBoardList();
        model.addAttribute("postList", boardDtoList);
        return "board/list.html";
    }

    @GetMapping("/post")
    public String post() {
        return "board/post.html";
    }

    @PostMapping("/post")
    public String write(BoardDto boardDto) {
        boardService.savePost(boardDto);
        return "redirect:/";
    }
}
```

`model.addAttribute("postList", boardDtoList);`를 통하여 `boardDtoList`를 `board/list.html`에 `postList`로 전달해 줍니다.  
아래는 `board/list.html`의 일부입니다.

```html
<tr class="text-center" th:each="post : ${postList}">
		<th scope="row">
				<span th:text="${post.id}"></span>
		</th>
		<td>
				<a th:href="@{'/post/' + ${post.id}}">
						<span th:text="${post.title}"></span>
				</a>
		</td>
		<td>
				<span th:text="${post.author}"></span>
		</td>
		<td>
				<span th:text="${#temporals.format(post.createdDate, 'yyyy-MM-dd HH:mm')}"></span>
		</td>
</tr>
```

위와 같이 `board/list.html`에서 `postList`를 받아, 데이터베이스에 있는 내용들을 출력해 줍니다.

서버를 실행하고 글목록을 보면 아래와 같이 MySQL에 있는 데이터들이 목록으로 출력되는 것을 확인할 수 있습니다.

![Board List]({{ site.url }}/assets/image/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post/2020-07-20-Spring-Boot-JPA-MySQL-Board-Write-Post_19.png)
