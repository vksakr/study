### Jenkins 설치 가이드

>OS : CentOS 7.4 64bit
>
>Web Server : apache 2.4.41
>
>Web Application Server : tomcat 8.5.51
>
>Java : jdk 1.8.0.202



- tomcat/webapps
  wget "http://mirrors.jenkins.io/war-stable/latest/jenkins.war"

  > wget - web get 의 약어로 웹상의 파일을 다운로드 받는 명령어

- tomcat/conf/server.xml
  Context 추가

  ```xml
  <Context path="/jenkins" docBase="jenkins.war" reloadable="false"/>
  ```

- 서버ip:port/jenkins 로 접속하면 Jenkins 설정이 시작된다.
- ![12-2](https://github.com/vksakr/study/blob/master/image/jenkins/1.png)
- 화면에 나오는 경로 `HOME/.jenkins/~` 로 접근하여 Admin password 를 보고 입력하여 로그인한다.
- ![12-3](https://github.com/vksakr/study/blob/master/image/jenkins/2.png)
- Install suggested plugins : 추천 플러그인들을 설치한다. (선택)
  select plugins to install : 원하는 플러그인을 선택하여 설치한다.
- ![12-5](https://github.com/vksakr/study/blob/master/image/jenkins/3.png)
- 설치가 진행된다.
- ![12-6](https://github.com/vksakr/study/blob/master/image/jenkins/4.png)
- Continue as admin - save - start
- 설치완료

위의 과정으로 설치 진행시 Admin User 생성 과정을 넘겼기 때문에 Jenkins 에 접근 가능한 회원(혹은 관리자) 가 없는 상태이다.

Github 의 로그인 시스템을 연동해보자.



### Github 연동

- Github 로 접속후 연동할 본인 계정 혹은 조직계정의 Setting 으로 이동한다.
- Developer settings - OAuth Apps - Register a new OAuth application 이동
- ![image-20200324225357183](https://github.com/vksakr/study/blob/master/image/jenkins/6.png)
- 위처럼 Jenkins URL 을 입력해준뒤 Register application 버튼을 누른다.
- ![image-20200324225653617](https://github.com/vksakr/study/blob/master/image/jenkins/7.png) 
- 위와 같이 Clinet ID 과 Secret 이 발급된다.
- Jenkins 로 와서 Jenkins 관리 - 플러그인 관리로 이동한다
- 설치가능 목록중 GitHub Authentication plugin 을 체크하고 '재시작 없이 설치하기' 를 누른다.
- Jenkins 관리 - Configure Global Security
- ![image-20200324224022508](https://github.com/vksakr/study/blob/master/image/jenkins/5.png)

- Security Realm 의 Github Authentication Plugin 체크
- GitHub Web URI : GitHub 주소
  GitHub API URI : GitHub API 주소 (엔터프라이즈버전은 GitHub URI/api/v3)
  Client ID & Client Secret : GitHub 에서 발급받은 값 입력
- ![image-20200324230744148](https://github.com/vksakr/study/blob/master/image/jenkins/8.png)

- Authorization - Matrix-based security 을 체크하고
- Authenticated Users 의 모든 항목에 체크해준다.
- 하단의 Save 를 눌러서 설정 저장
- 로그아웃 후 로그인시
- ![image-20200324231156578](https://github.com/vksakr/study/blob/master/image/jenkins/9.png) 
- Github 와 로그인이 연동된것을 확인할 수 있다.
