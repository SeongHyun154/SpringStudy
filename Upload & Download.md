# Spring Boot 파일 업로드 코드 설명

이 코드는 Spring Boot와 간단한 HTML 인터페이스를 통해 단일 및 다중 파일 업로드와 DTO(Data Transfer Object)를 통한 메타데이터 전송 기능을 제공합니다. 각 구성 요소와 주요 기능을 아래에 정리했습니다.

---

## 1. 컨트롤러: `FileUpDownloadController`

Spring MVC `@Controller`로 파일 업로드를 처리하며, `/file` 경로로 매핑되어 있습니다. 이 컨트롤러에는 단일 파일, 다중 파일, DTO와 함께 다중 파일 업로드 기능이 포함되어 있습니다.

### 주요 어노테이션 및 상수

- **`@Controller`**: 이 클래스를 Spring MVC 컨트롤러로 지정합니다.
- **`@RequestMapping("/file")`**: 이 컨트롤러의 기본 URL 경로를 `/file`로 설정합니다.
- **`UPLOAD_PATH`**: 파일 저장 경로로 `c:\\upload`를 설정합니다.

### 메서드 설명

#### `GET` 매핑

- **`upload()`** (`/file/upload` 경로): 파일 업로드 페이지를 사용자에게 제공합니다.

#### `POST` 매핑

- **`upload_post()`**: 단일 파일 업로드를 처리합니다.
  - 파일 이름과 크기를 로그에 기록.
  - `UUID`로 고유 폴더를 생성.
  - 업로드된 파일을 해당 폴더에 저장.
  
- **`upload_post_multi()`**: 다중 파일 업로드를 처리합니다.
  - 단일 파일 업로드와 유사하나, `MultipartFile[] files` 배열을 반복하여 각 파일을 처리합니다.
  - 각 파일을 생성된 고유 폴더에 저장.

- **`upload_post_multi_dto()`**: DTO를 사용하여 메타데이터와 다중 파일 업로드를 처리합니다.
  - `FileDto` 객체로 파일 배열과 관련 데이터를 함께 캡처합니다.
  - `FileDto` 정보를 로그에 기록.
### 코드 예시
```java
package com.example.ex01.controller;

import java.io.File;
import java.io.IOException;
import java.util.UUID;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import com.example.ex01.domain.dto.FileDto;

import lombok.extern.slf4j.Slf4j;

@Controller
@Slf4j
@RequestMapping("/file")
public class FileUpDownloadController {

	public final static String UPLOAD_PATH = "c:\\upload";
	
	@GetMapping("/upload")
	public void upload() {
		log.info("GET /file/upload...");
	}
	
	@PostMapping("/upload")
	public void upload_post(@RequestParam("file") MultipartFile file) throws IllegalStateException, IOException {
		log.info("POST /file/upload..." +file);
		log.info(file.getOriginalFilename());
		log.info(file.getSize()+"byte");
		
		//폴더생성
		String dir = UPLOAD_PATH+File.separator+UUID.randomUUID();
		File dirPath = new File(dir);
		if(!dirPath.exists());
			dirPath.mkdirs();
			
		//파일명 추출
		String filename = file.getOriginalFilename();
		//파일객체 생성
		File fileObject = new File(dir,filename);
		//업로드 처리
		file.transferTo(fileObject);
		
	}
	
	@PostMapping("/multiUpload")
	public String upload_post_multi(@RequestParam("files") MultipartFile[] files) throws IllegalStateException, IOException {
		log.info("POST /file/upload..." +files);
		
		//폴더생성
		String dir = UPLOAD_PATH+File.separator+UUID.randomUUID();
		File dirPath = new File(dir);
		if(!dirPath.exists());
		dirPath.mkdirs();
		
		for(MultipartFile file : files) {
			String filename = file.getOriginalFilename();
			File fileObject = new File(dir,filename);
			file.transferTo(fileObject);
		}
		
		return "file/upload";
		
	}
	
	@PostMapping("/multiUpload_dto")
	public String upload_post_multi_dto(FileDto fileDto) throws IllegalStateException, IOException {
		log.info("POST /file/upload..." + fileDto);		
		return "file/upload";
	}
}
```

---

## 2. 데이터 전송 객체 (DTO): `FileDto`

이 클래스는 파일과 함께 업로드되는 추가 데이터 필드를 포함합니다.

### 필드

- **`id`** (int): 파일의 식별자.
- **`category`** (String): 파일에 관련된 카테고리.
- **`price`** (int): 업로드와 관련된 가격 정보.
- **`files`** (MultipartFile[]): 업로드된 파일의 배열.

DTO를 통해 메타데이터(예: `category` 또는 `price`)와 파일을 함께 제출할 수 있어, 관련 정보를 포함한 업로드가 필요할 때 유용합니다.
### 코드 예시
```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<h1>FILE UPLOAD</h1>
	
	<h2>단일 파일 전송 - 동기방식</h2>	
	<form action="${pageContext.request.contextPath}/file/upload" method="post" enctype="multipart/form-data">
		<input type="file" name="file" />
		<input type="submit" value="단일파일전송" />		
	</form>
	
	<hr>
	
	<h2>다중 파일 전송 - 동기방식</h2>	
	<form action="${pageContext.request.contextPath}/file/multiUpload" method="post" enctype="multipart/form-data">
		<input type="file" name="files" multiple />
		<input type="submit" value="다중파일전송"  />		
	</form>
	
	<hr>
	
	<h2>다중 파일 전송(DTO) - 동기방식</h2>
	<form action="${pageContext.request.contextPath}/file/multiUpload_dto" method="post" enctype="multipart/form-data">
		<input type="text" name="id" />
		<input type="text" name="category" />
		<input type="text" name="price" />
		<input type="file"  name="files" multiple/>
		<input type="submit" value="단일파일전송" />
 	</form>	
	
</body>
</html>
```
---

## 3. HTML 파일 업로드 폼

HTML 페이지는 파일 업로드를 위한 세 가지 섹션을 포함합니다.

1. **단일 파일 업로드**:
   - `/file/upload` 엔드포인트 사용.
   - 파일 입력 (`name="file"`)과 제출 버튼 포함.

2. **다중 파일 업로드**:
   - `/file/multiUpload` 엔드포인트 사용.
   - 파일 입력 필드에 `multiple` 속성으로 여러 파일 선택 및 업로드 가능.

3. **DTO와 함께 다중 파일 업로드**:
   - `/file/multiUpload_dto` 엔드포인트 사용.
   - `id`, `category`, `price` 필드를 입력할 수 있는 텍스트 필드로 추가 데이터 캡처.
   - 다중 파일 입력 필드로 DTO 필드와 함께 제출.
### 코드 예시
```java
package com.example.ex01.domain.dto;

import org.springframework.web.multipart.MultipartFile;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class FileDto {
	private int id;
	private String category;
	private int price;
	MultipartFile [] files;
}
```
---

## 전체 흐름 요약

1. 사용자가 `/file/upload` 경로를 통해 파일 업로드 페이지에 접속합니다.
2. 사용자 단일 파일, 다중 파일 또는 메타데이터와 함께 다중 파일을 업로드.
3. 각 업로드가 처리되어 고유한 폴더에 저장되고, DTO 제공 시 해당 정보가 로그에 기록됩니다.

---

## 코드 주의 사항

- **에러 처리**: 파일 작업 시 발생할 수 있는 에러(예: 파일 쓰기 오류)에 대한 처리가 포함되어 있지 않으므로, 코드의 견고성을 위해 추가하는 것이 좋습니다.
- **디렉토리 생성**: `UUID.randomUUID()`로 고유한 이름의 디렉토리를 생성하여 각 업로드를 독립적으로 관리합니다.

이 구조는 다양한 유형의 파일 및 관련 데이터를 다루기에 유연하고 확장 가능한 형태로 설계되었습니다.
