### 오늘의 일기
---
#### 스프링 파일 업로드
+ 스프링은 `MultipartFile` 이라는 인터페이스로 멀티파트 파일을 매우 편리하게 지원한다.
+ 파일 업로드 컨트롤러
  ```java
  if (!file.isEmpty()) {
     String fullPath = fileDir + file.getOriginalFilename();
     log.info("파일 저장 fullPath={}", fullPath);
     file.transferTo(new File(fullPath));
  }
  ```
  + `@RequestParam MultipartFile file` : 업로드하는 HTML Form의 name에 맞추어 @RequestParam 을 적용하면 된다.(`@ModelAttribute` 에서도 `MultipartFile` 을 동일하게 사용할 수 있다.)
  + `file.getOriginalFilename()` : 업로드 파일 명
  + `file.transferTo(...)` : 파일 저장
 
+ 업로드 파일 정보보관
  ```java
  @Data
  public class UploadFile {
       private String uploadFileName;
       private String storeFileName;
       
       public UploadFile(String uploadFileName, String storeFileName) {
         this.uploadFileName = uploadFileName;
         this.storeFileName = storeFileName;
     }
  }
  ```
  + `uploadFileName` : 사용자가 업로드한 파일명
  + `storeFileName` : 서버 내부에서 관리하는 파일명
  + 사용자가 업로드한 파일명으로 서버 내부에 파일을 저장하면 안된다.🎃
  + 왜냐하면 서로 다른 사용자가 같은 파일이름을 업로드 하는 경우 기존 파일 이름과 충돌이 날 수 있다. 
  + 서버에서는 저장할 파일명이 겹치지 않도록 내부에서 관리하는 별도의 파일명이 필요하다.🎃

+ 멀티파트 파일을 서버에 저장
    ```java
    private String createStoreFileName(String originalFilename) {
     String ext = extractExt(originalFilename);
     String uuid = UUID.randomUUID().toString();
     return uuid + "." + ext;
   }
   private String extractExt(String originalFilename) {
     int pos = originalFilename.lastIndexOf(".");
     return originalFilename.substring(pos + 1);
   }
   ```
  + 저장할 파일 이름 구성
    + `createStoreFileName()`: 서버 내부에서 관리하는 파일명은 유일한 이름을 생성하는 UUID 를 사용해서 충돌하지 않도록 한다. 
  + 확장자 추출
    + extractExt() : 확장자를 별도로 추출해서 서버 내부에서 관리하는 파일명에도 붙여준다. 
    + 예를 들어, 사용자가 `photo.png` 라는 이름으로 업로드 하면 `51041c62-86e4-4274-801d-614a7d994edb.png` 와 같이 저장한다.
+ 엔티티
  + `List<MultipartFile> imageFiles` : 이미지를 다중 업로드 하기 위해 `MultipartFile` 를 사용한다.

+ 컨트롤러
  ```java
  @ResponseBody
  @GetMapping("/images/{filename}")
  public Resource downloadImage(@PathVariable String filename) throws MalformedURLException {
    return new UrlResource("file:" + fileStore.getFullPath(filename));
  }
  ```
  + `@GetMapping("/images/{filename}")` : <img> 태그로 이미지를 조회할 때 사용한다. 
  + `UrlResource`로 이미지 파일을 읽어서 `@ResponseBody` 로 이미지 바이너리를 반환한다.
  ```java
  @GetMapping("/attach/{해당클래스id}")
  public ResponseEntity<Resource> downloadAttach(@PathVariable Long 해당클래스id) throws MalformedURLException {
   해당클래스 지정이름 = 해당클래스레퍼지토리.findById(해당클래스id);
   String storeFileName = 지정이름.getAttachFile().getStoreFileName();
   String uploadFileName = 지정이름.getAttachFile().getUploadFileName();
   
   UrlResource resource = new UrlResource("file:" + fileStore.getFullPath(storeFileName));
   log.info("uploadFileName={}", uploadFileName);
   String encodedUploadFileName = UriUtils.encode(uploadFileName, StandardCharsets.UTF_8);
   String contentDisposition = "attachment; filename=\"" + encodedUploadFileName + "\""; // attachment; filename="업로드 파일명" 값 주기
   
   return ResponseEntity.ok()
       .header(HttpHeaders.CONTENT_DISPOSITION, contentDisposition)
       .body(resource);
  }
  ```
  + `@GetMapping("/attach/{해당클래스id}")`: 파일을 다운로드 할 때 실행한다.
  + 파일 다운로드 시 권한 체크같은 복잡한 상황까지 생각하여 이미지 id 를 요청한다.
  + 파일 다운로드시에는 사용자가 업로드한 파일 이름으로 다운로드 하는게 좋다. 
  + 이때는 `Content-Disposition` 해더에 `attachment; filename="업로드 파일명"` 값을 주면 된다.

+ 뷰(.html)
  + 다중파일 업로드를 하려면 Form 태그에 `enctype="multipart/form-data"` 추가해주어야 한다.
  + 그리고 input 태그에 `multiple="multiple"` 옵션을 추가해 주면된다.


참고📢 `File.mkdir()`과 `File.mkdirs()`의 차이점  
+ `File.mkdir()`: 만들고자 하는 디렉토리의 상위 디렉토리가 존재하지 않을 경우, 생성 불가
  + 예를들어 `C:\abc\aaa` : aaa 디렉토리를 만들고자 하는데, abc 디렉토리가 없는 경우, 생성 불가
+ `File.mkdirs()`: 만들고자 하는 디렉토리의 상위 디렉토리가 존재하지 않을 경우, 상위 디렉토리까지 생성
  + `C:\abc\aaa`: aaa 디렉토리를 만들고자 하는데, abc 디렉토리가 없는 경우, abc 디렉토리까지 생성













  
