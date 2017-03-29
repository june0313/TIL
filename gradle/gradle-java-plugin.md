# Gradle Java 플러그인

## Gradle 플러그인이란?
자주 사용할만한 빌드 명령어들을 묶어놓은것. 일종의 라이브러리. 작업 목적에 맞는 플러그인을 찾아서 빌드 파일(build.gradle)에 적용하기만 하면 복잡한 빌드 스크립트를 직접 작성하지 않아도 된다.

## Gradle Java 플러그인
Java 플러그인에는 자바 소스를 컴파일하고 테스트하는 작업과 관련된 명령어들과 jar파일을 생성하는 명령어등이 들어있다.

### Java 플러그인 사용하기
build.gradle파일에 아래와 같이 플러그인을 추가한다.
```groovy
apply plugin: 'java'
```

### 프로젝트 기본 구조
java 플러그인이 소스코드나 리소스 파일을 참조하기위해 기본으로 인식하는 폴더 구조가 있는데, 아래와 같다.

디렉토리 | 설명
-------- | -----
src/main/java | 자바 소스 파일을 두는 디렉토리
src/main/resources | 프로그램 실행 중에 참조하는 기타 파일을 두는 폴더
src/test/java | 단위 테스트를 위한 테스트 자바 소스 파일을 두는 폴더
src/test/resources | 단위 테스트를 위한 테스트 관련 기타 파일을 두는 폴더

>intellij에서 gradle java 프로젝트를 생성하면 생기는 디렉토리 구조가 바로 java 플러그인이 인식하는 디렉토리 구조였다.


### 주요 Task
- `compileJava` : javac를 사용하여 자바 소스 파일을 컴파일한다. 컴파일된 클래스 파일들은 `build/clasess/main` 디렉토리에 둔다.
- `processResource` : 프로그램 실행시에 참조하는 리소스 파일들을 `build/resources/main` 디렉토리로 복사한다.
- `classes` : 클래스 및 리소스 파일을 모은다. 
    - 선행작업 : `compileJava` -> `processResource`
- `compileTestJava` : javac를 사용하여 테스트 자바 소스 파일을 컴파일한다. 컴파일된 클래스 파일들은 `build/clasess/test` 디렉토리에 둔다.
    - 선행작업 : `compileJava` -> `processResource` -> `classes`
- `processTestResource` : 테스트 실행시에 참조하는 리소스 파일들을 `build/resources/test` 디렉토리로 복사한다.
- `testClasses` : 테스트 클래스 및 테스트 리소스 파일을 모은다. 
    - 선행작업 : `compileTestJava` -> `processTestResource`
- `jar` : 클래스 파일과 리소스 파일을 묶어서 JAR파일을 생성한다. 생성된 JAR파일은 `build/libs` 디렉토리에 둔다.
    - 선행작업 : `compileJava` -> `processResource` -> `classes`
- `javadoc` : javadoc을 사용하여 API문서를 생성한다. 생성된 문서 파일들은 `build/docs/javadoc` 디렉토리에 둔다.
- `test` : JUnit 또는 TestNG를 사용하여 단위 테스트를 수행한다.
- `clean` : build 디렉토리를 삭제한다.
- `build` : 프로젝트의 모든 빌드 작업을 수행한다.