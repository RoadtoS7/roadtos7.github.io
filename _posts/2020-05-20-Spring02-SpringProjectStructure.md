---
title: "Spring 02 - 스프링 폴더 구조"
date: "2020-05-20 21:58:17 -400"
categories: Spring
---

## 스프링 폴더 구조
스프링 폴더 구조는 maven 프로젝트 구조와 같습니다.  
예시를 통해서 더 자세히 알아보도록 하겠습니다.  
예시 프로젝트는 [Pet-Clinic](https://github.com/spring-projects/spring-petclinic)입니다.  
<img src="https://user-images.githubusercontent.com/57262833/82396553-24fd5280-9a89-11ea-9c30-e18d8e37438b.png" height="300">


### src/main/java
<img src="https://user-images.githubusercontent.com/57262833/82393509-8a4d4580-9a81-11ea-9388-c9d652a7d603.png" height="300">
- java 파일들이 들어있습니다.
- Controller 와 Model에 해당하는 파일들이 들어있습니다.
- 이중에서 Controller라는 단어가 붙은 파일들은 아래와 같이 Controller annotation을 가지고 있습니다.

```
@Controller
class OwnerController {

	private static final String VIEWS_OWNER_CREATE_OR_UPDATE_FORM = "owners/createOrUpdateOwnerForm";

	private final OwnerRepository owners;

	private VisitRepository visits;

	public OwnerController(OwnerRepository clinicService, VisitRepository visits) {
		this.owners = clinicService;
		this.visits = visits;
	}

	@InitBinder
	public void setAllowedFields(WebDataBinder dataBinder) {
		dataBinder.setDisallowedFields("id");
	}

	@GetMapping("/owners/new")
	public String initCreationForm(Map<String, Object> model) {
		Owner owner = new Owner();
		model.put("owner", owner);
		return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
	}

	@PostMapping("/owners/new")
	public String processCreationForm(@Valid Owner owner, BindingResult result) {
		if (result.hasErrors()) {
			return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
		}
		else {
			this.owners.save(owner);
			return "redirect:/owners/" + owner.getId();
		}
	}

```

- Controller annotation 이 붙은 파일은 클라이언트의 요청을 비즈니스 로직으로 분기 및 수행결과를 응답하는 역할을 합니다.
- 즉, 클라이언트 요청이 들어오면 Controller 파일 내부에서 GetMapping anntation 이나 PostMapping annotation이 달린 함수로 요청이 맵핑되서 처리됩니다.

### src/main/resources
<img src="https://user-images.githubusercontent.com/57262833/82395960-afdd4d80-9a87-11ea-9181-0a0d79d25e80.png" height="300">

- Resources들(자바코드에서 사용할 리소스들 html, db 등)이 저장되는 디렉터리입니다.


### src/test/java
<img src="https://user-images.githubusercontent.com/57262833/82397114-9a1d5780-9a8a-11ea-8fbd-c944a34f53ff.png" height="450">
- 테스트 코드가 들어있습니다.

### src/web/WEB-INF
- WEB-INF 폴더는 외부에서 직접접근할 수 없습니다.  
- 컨트롤러를 통하여 경유하여 접근할 수 있습니다.
- 이유: 컴파일된 클래스와 스프링 환경 설정파일이 존재하기 때문입니다.

#### src/web/WEB-INF/views
- 출력할 웹페이지 html, jsp 파일을 가지고 있습니다.

### pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
    
  <groupId>org.springframework.samples</groupId>
  <artifactId>spring-petclinic</artifactId>
  <version>2.3.0.BUILD-SNAPSHOT</version>
  
  <dependencies>
    <!-- Spring and Spring Boot dependencies -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-cache</artifactId>
    </dependency>
  </dependencies>
</project>

```
- maven에서 참조하는 설정파일입니다.  
- maven은 빌드와 관련된 정보를 프로젝트 객체모델(Proejct Object Model)이라는 이름으로 정의하고 사용하는데, 이 객체모델이 pom.xml입니다.
- 설정하고 싶은 라이브러리가 있으면 pom.xml파일에서 dependency 태그를 추가하고 라이브러리를 추가하면 됩니다. 
- maven이 pom.xml을 읽어서 서술된 라이브러리를 jar파일로 로컬저장소에 다운 받아줍니다.
- = spring의 장점: 기존의 웹프로젝트는 모든 라이브러리 파일을 직접 다운 받아서 적용해야했습니다. 반면에 spring은 dependency 태그만 적어주면, 알아서 적용시켜줍니다. 또한 다른 프로젝트에서 같은 dependency 태그를 적어주면, 별도의 다운로드 없이 로컬 저장소에 저장된 라이브러리를 자동으로 가져다가 사용합니다.
 






> 참고 : https://doublesprogramming.tistory.com/16
