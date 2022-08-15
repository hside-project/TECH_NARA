## [Docker\] Dockerfile

| Write  | contect            | Date       |
| ------ | ------------------ | ---------- |
| 김현호 | lhyun058@gmail.com | 2022.08.14 |

> **📝정리**
>
> Docker를 이용해 `jar`파일을 배포하면서 프로젝트에 환경변수 설정을 하는 부분이 있어 `Dockerfile`에 환경 변수를 추가하였습니다. 
> 환경 변수 추가를 위해 `Dockerfile`에 대한 조사를 하면서 학습한 내용을 정리하려고 합니다. 

## 1.Dockerfile 소개

`Dockerfile`은 `DockerImage`를 생성하기 위한 설정 파일입니다. 
(`Dockerfile`을 빌드하면 `Docker Image`를 생성해준다) 
`Dockerfile`의 장점은 이미지가 어떻게 만들어졌느지를 기록하며, 배포이 용이하고 컨테이너가 특정 행동을 수행할 수 있도록 해줍니다.

## 2. Dockerfile 구성

```dockerfile
FROM openjdk:11-jdk
EXPOSE 80
ARG JAR_FILE=target/*.jar
ADD ${JAR_FILE} api.jar
ENV JASYPT_SECRET="value"
WORKDIR /home/api
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/api.jar"]
```

* FROM - Base 이미지
  * `FROM <이미지>`
* EXPOSE - 생성된 이미지에서 열어줄 포트번호
  * `EXPOSE <포트번호>`
  * 컨테이너 생성시 `-p`옵션의 컨테이너 포트 값으로 EXPOSE에 작성한 값을 적어야 한다.
* ADD - build 명령 중간에 호스트의 파일 또는 폴더를 이미지에 가져온다
* ARG - docker build로 이미지를 빌드 시 옵션을 통해 넘길 수 있는 인자를 정의
  * `ARG <이름>`
* ENV - 이미지에서 사용할 환경 변수 값을 저장
  * `ENV <KEY> <VALUE>`
* WORKDIR - 컨테이너 상에서 작업 디렉토리를 지정
  * `WORKDIR <이동할 경로>`
  * 해당 디렉토리가 없으면 새로 생성
  * `RUN`,`CMD`,`ENTRYPOINT`,`COPY`,`ADD` 명령문은 해당 디렉토리를 기준으로 싱행
* ENTRYPOINT - `docker run` 실행 시 항상 실행되야 하는 커맨드를 지정할 때 사용
  * `ENTRYPOINT ["커맨드","파라미터1","파라미터2"]`
  * Docker Image를 하나의 실행 파일처럼 사용

---

### 참고

💡 [https://freedeveloper.tistory.com/188](https://freedeveloper.tistory.com/188)

