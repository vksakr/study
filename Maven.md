# Maven 기초

Maven이란 빌드툴의 한 종류이다.

빌드란 컴파일을 하고, 필요한 라이브러리들과 함께 application이 동작할 수 있는 형태로 만들어주는것을 말한다.



### LifeCycle

Maven은 라이프사이클을 가지며 순서를 갖는 단계(Phase)로 구성된다.

메이븐은 총 3개의 라이프사이클을 제공하며 다음과 같은 단계(Phase) 를 가진다.
(실제로는 라이프사이클 별로 더 많은 Phase 가 있음)

1. Default 라이프사이클
   - compile
     - 소스 코드를 컴파일한다.
   - test
     - 단위 테스트를 한다.
   - package
     - 컴파일 된 코드와 자원들을 배포 형식에 맞춰 패키지로 만든다.
   - install
     - 로컬 저장소에 패키지를 배포한다.
   - deploy
     - 원격 저장소에 패키지를 배포한다.
2. Clean 라이프사이클
   - clean
     - 빌드 과정에서 생성된 결과물을 삭제한다.
3. Site 라이프사이클
   - site
     - 프로젝트에 대한 문서 사이트를 생성한다.
   - site-deply
     - 생성한 문서를 배포한다.

### Phase

라이프 사이클 각각의 단계를 의미한다.

특정 단계를 수행하려 할땐 처음부터 해당 단계까지 순차적으로 빌드가 실행된다.

Phase 는 순서를 나타낼뿐 실질적인 작업은 Plugin이 수행한다.

### Goal 과 Plugin

Maven 의 실질적인 작업은 플러그인이 수행한다.

플러그인은 하나 이상의 Goal 을 가지고 있고 이 Goal 이 실행할 명령을 의미한다.

각 Phase 에는 Plugin 의 Goal 이 연결돼있다.

따라서 Maven 명령어로 Phase 를 실행하면 각 Phase 에 연결된 plugin의 Goal이 실행된다.

ex) mvn compile 명령어 실행시 compile 에 연결된 compiler 플러그인의 compile 골이  실행된다. 

> tomcat에 배포해보기
>
> maven 에서 빌드 수행시 그 결과는 기본적으로 target 디렉토리에 생성된다.
> 
>tomcat 설정(server.xml) 에 생성된 패키지의 경로를 추가해주면 서비스가 동작하는것을 확인할 수 있다.
> 
>```xml
> <Context path="/myservice" docBase="\target\myservice-1.0" reloadable="false"/>
> ```

## Maven 설정

### package

compile 된 class 파일들과 빌드 결과물인 package 파일은 target 디렉토리에 생성된다.

그러나 보통 서비스할 파일(package)은 class 파일들과 구분하여 관리하고,

plugin 에서 디렉토리를 별도로 지정할 수 있다.

> ex) war plugin 의 경우
>
> ```xml
> <plugin>
>     <artifactId>maven-war-plugin</artifactId>
>     <version>3.2.2</version>
>     <configuration>
>         <!-- basedir 은 pom.xml 이 위치하는 디렉토리 -->
>         <webappDirectory>${basedir}/deploy</webappDirectory>
>     </configuration>
> </plugin>
> ```



### clean

위처럼 빌드 결과물(배포 폴더)과 컴파일 폴더를 따로 설정하면 clean 실행시 컴파일폴더만 삭제되는것을 볼 수 있다.

재빌드시 배포 폴더를 지우지 않고 덮어쓰게 되므로 새로 생성한 파일만 반영되는 반면, 삭제한 파일이 사라지지 않고 남아있게 된다.

이러한 경우를 막기위해 Clean Plugin 이 기존 빌드 결과물도 삭제할 수 있도록 설정을 해준다.

>```xml
><plugin>
><artifactId>maven-clean-plugin</artifactId>
><version>3.1.0</version>
><configuration>
>   <filesets>
>       <fileset>
>           <directory>${basedir}/deploy</directory>
>           <!-- 삭제될 파일의 유형 -->
>           <includes>
>               <include>**/*</include>
>           </includes>
>           <!-- 삭제하지 않을 파일의 유형 -->
>           <excludes>
>               <exclude>**/important.log</exclude>
>               <exclude>**/another-important.log</exclude>
>           </excludes>
>       </fileset>
>   </filesets>
></configuration>
></plugin>
>```



## 저장소(Repository)

메이븐은 의존 관계에 있는 라이브러리를 관리할 수 있는 기능을 제공한다.

이 기능은 저장소(Repository)와 관련이 있으며 저장소는 라이브러리를 모아 관리하는곳이다.

1. 중앙 저장소
   - 오픈 소스 라이브러리, Maven 플러그인, Maven 아키타입을 관리하는 저장소이다. ([중앙 저장소](https://repo1.maven.org/maven2/))
   - 원격 저장소의 한 종류이며 개발자가 임의로 라이브러리를 배포할 수 없다.
2. 원격 저장소
   - 중앙 저장소에 없는 라이브러리들이 필요한 경우 오픈된 외부 저장소나 사내 저장소를 구축하거나 이용할 수 있다.
3. 로컬 저장소
   - 중앙 저장소나 원격 저장소에서 다운로드한 라이브러리들이 저장되는 개발자 PC의 저장소이다.
   - 기본 위치는 USER_HOME/.m2/repository

> 원격 저장소 설정
>
> ```xml
> <repositories>
> 	<repository>
> 		<id><!-- 원격 저장소 id --></id>
> 		<url><!-- 원격 저장소 url --></url>
> 		<snapshots>
>             <!-- snapshot포함 최신버전을 가져온다 -->
> 			<enabled>true</enabled> 
> 		</snapshots>
> 	</repository>
> </repositories>
> ```



### 의존 라이브러리 관리

Maven에서 의존 관계 라이브러리는 pom.xml 파일로 관리한다.

의존 관계를 가지는 라이브러리는 로컬 저장소에 다운받아지며

존재하는 라이브러리는 다시 다운받지 않으므로 중복을 피할수있다.

또 프로젝트 공유시 pom.xml 만 공유하면 되므로 공유할 리소스의 양이 줄고 관리가 용이하다.

#### 의존성 전이

메이븐의 의존성 관리 메커니즘

A가 B에 의존하고 B가 C에 의존한다고 할 때, 즉 A -> B -> C 관계에서 A는 B에 대한 의존관계만 설정하면 메이븐이 알아서 C까지도 가져와서 적절한 스코프에 포함시켜 준다.

의존성 전이로 인해 의도치 않은 라이브러리와 의존 관계를 가지지 않도록 설정 기능을 제공한다.

#### 의존성 전이 설정

1. 의존성 중개
   - 더 가까운 의존관계 있는 pom 설정을 참고한다.
   - ex) A->B->C->D 2.0 / A->E->D 1.0 일때 A는 D1.0 과 의존관계를 가진다.
2. 의존성 관리
   - 의존 관계에 있는 라이브러리와 버전을 명시적으로 정의한다. (<dependencyManagement/>)
   - ex) 의존성 중개규칙을 따르지 않고 D 2.0 과 의존관계 설정
3. 의존성 스코프
   - 스코프별 빌드 작업에 사용되는 라이브러리를 지정하여 의존성 전이를 제한한다.
   - [스코프 종류](# 스코프(Scope))
4. 의존성 예외
   - 의존 관계에 있는 라이브러리를 명시적으로 제외한다.
     <exclusion/>
5. 선택적 의존성
   - A->B->C 관계이고, B는 C를 <optional/>로 설정시
   - A 프로젝트를 빌드할 때 C와 의존관계를 가지지않는다.

#### 스코프(Scope)

 - compile 
    - 기본 스코프. 컴파일 및 배포 시 포함되어야 하는 라이브러리
 - provided 
    - 컴파일시 사용되지만 배포시 포함되지 않는 라이브러리
    - servlet.jar는 서블릿 컨테이너에서 제공하므로 컴파일시엔 필요하지만, 배포할 때 포함될 필요는 없다.
 - runtime 
    - 컴파일에 사용되지 않지만 애플리케이션을 실행할 때 사용되어 배포에 포함되는 라이브러리
 - test 
    - 테스트하는 시점에만 사용하는 라이브러리에 대한 스코프 ex) junit
 - system 
    - provided와 비슷하지만. 직접 jar 파일을 제공해야 한다.
 - import 
    - 다른 POM 설정 파일에 정의되어 있는 의존 관계 설정을 현재 프로젝트로 가져온다.



## 프로파일(Profile)

#### 빌드 이식성
특별한 수정이나 변경 없이도 다양한 환경에 배포하고 운영하는 것이 가능해야 한다는 것을 뜻한다.

개발 환경(로컬, 개발, 운영 서버)에 따라 설정이 다를수 있다. 

개발 환경마다 다른 빌드 스크립트를 만들지 않으면서 하나의 빌드 스크립트에서 서로 다른 설정이 가능해야 한다.

이를 위해 Maven 은 Profile 기능을 제공한다.

>local 프로파일과 release 프로파일을 사용하는 예제
>
>```xml
><profiles>
>    <profile>
>       <id>local</id>
>           <properties>
>                <jdbc.host>127.0.0.1</jdbc.host>
>            </properties>
>         </profile>
>         <profile>
>           <id>release</id>
>           <properties>
>                <jdbc.host>10.10.10.11</jdbc.host>
>            </properties>
>         </profile>
>     </profiles>
>    ```
>
>${profile.id} 으로 프로파일의 id 를 이용
>
>```xml
><bulid>
>    <resources>
>       <resource>
>               <filtering>true</filtering>
>                <directory>src/main/resources/resource-${profile.id}</directory>
>            </resource>
>         </resources>
>     </bulid>
>    ```

빌드 실행시 -P<id>옵션을 통해 프로파일을 지정할 수도 있고 (mvn -Plocal test )

빌드 환경에 따라 자동으로 프로파일을 활성화(activation) 할 수 있다.

>```xml
><profile>
>    <id>local</id>
>    <activation>
>        <activeByDefault>false</activeByDefault>
>        <!-- 아래 옵션중 일치하는 값이 있는 프로파일이 활성화 된다 -->
>        <jdk><!-- jdk 버전 --></jdk>
>        <os><!-- os 종류 --></os>
>        <property><!-- 속성 값 --></property>
>        <file><!-- 파일 유무 --></file>
>    </activation>
></profile>
>```



### 참고자료

https://kmjoo91.github.io/Maven/

https://m.blog.naver.com/remagine/220726121992

http://hobakc.blogspot.com/2014/02/maven-lifecycle.html

https://cornswrold.tistory.com/271