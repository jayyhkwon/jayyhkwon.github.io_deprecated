---
layout: post
title:  " docker란 무엇인가? "
date:   2019-10-24 10:30 +0530
categories: DevOps
---

목차
1. Docker란 무엇인가?
2. 컨테이너
3. 이미지
4. 레이어 저장 방식
5. 정리
---
1. Docker 란 무엇인가?

도커는 컨테이너 기반의 오픈소스 가상화 플랫폼이다.
컨테이너라 하면 떠오르는 이미지인 배에 실는 네모난 화물 수송용 박스처럼 어떠한 것을 담고 있는 것이라 생각하면 된다.
그 어떠한 것은 다양한 프로그램, 실행환경이 될 수 있다.

<img src="/assets/logo.png">


2. 컨테이너

컨테이너는 격리된 공간에서 프로세스가 동작하는 기술이다.
기존의 가상화 방식은 호스트 OS위에 게스트 OS 전체를 가상화 하여 사용하는 방식이였으나,
이러한 방식은 사용법은 간단하지만 무겁고 느리다는 단점을 가지고 있다.

<img src="/assets/docker1.jpg">

이를 개선하기 위해 프로세스를 격리하는 방식이 등장하게 되었고 단순히 프로세스를 격리시키기 때문에
가볍고 빠르게 동작한다. cpu나 메모리는 딱 프로세스가 필요한 만큼만 추가로 사용하고 성능적으로도 거의 손실이 없다.
하나의 서버에 여러개의 컨테이너를 실행하면 서로 영향을 미치지 않고 독립적으로 실행되어 마치 가벼운 vm을 사용하는 느낌을 준다. 
또한 실행중인 컨테이너에 접속하여 명령어를 입력할 수 있고 사용자도 추가하고 여러개의 프로세스를 백그라운드로 실행 할 수도 있다.

3. 이미지

이미지는 컨테이너 실행에 필요한 파일과 설정값 등을 포함하고 있는 것으로 변하지 않는다(immutable). 컨테이너는 이미지를 실행한 상태라고 볼 수 있고 추가되거나 변하는 값은 컨테이너에 저장된다.
같은 이미지에서 여러 개의 컨테이너를 생성할 수 있고 컨테이너의 상태가 변하거나 삭제되더라도 이미지는 변하지 않고 그대로 남아있다.
ubuntu 이미지는 ubuntu를 실행하기 위한 모든 파일을 가지고 있고 MySQL이미지는 debian을 기반으로 MySQL을 실행하는 데 필요한 파일과 실행 명령어, 포트 정보등을 가지고 있다.
말그대로 이미지는 컨테이너를 실행하기 위한 모든 정보를 가지고 있기 때문에 더 이상 의존성 파일을 컴파일하고 이것저것 설치할 필요가 없다.


4. 레이어 저장방식

<img src="/assets/layer.png">

도커 이미지는 컨테이너를 실행하기 위한 모든 정보를 가지고 있기 때문에 보통 용량이 수백 메가 ~ 기가에 이른다. 처음 이미지를 다운 받을 땐 크게 부담이 안되지만 기존 이미지에 파일 하나를 추가했다고
수백 메가를 다시 다운 받는다면 매우 비효율적일 수 밖에 없다.

도커는 이런 문제를 해결하기 위해 레이어라는 개념을 사용하고 <a href="https://www.joinc.co.kr/w/man/12/docker/storage">유니온 파일 시스템</a>을 이용하여 여러 개의 레이어를 하나의 파일 시스템으로 사용 할 수 있게 해준다. 이미지는 여러개의 읽기 전용 read only 레이어로 구성되고 파일이 추가되거나 수정되면 새로운 레이어가 생성 된다.

ubuntu 이미지가 A + B + C의 집합이라면, ubuntu 이미지를 베이스로 만든 nginx 이미지는 A + B + C + nginx가 된다. webapp 이미지를 nginx 이미지 기반으로 만들었다면 예상대로 A + B + C + nginx + source 레이어로 구성된다. webapp 소스를 수정하면 A, B, C, nginx 레이어를 제외한 새로운 source(v2) 레이어만 다운받으면 되기 때문에 굉장히 효율적으로 이미지를 관리할 수 있다.

컨테이너를 생성할 때도 레이어 방식을 사용하는데 기존의 이미지 레이어 위에 읽기/쓰기read-write 레이어를 추가한다. 이미지 레이어를 그대로 사용하면서 컨테이너가 실행중에 생성하는 파일이나 변경된 내용은 읽기/쓰기 레이어에 저장되므로 여러개의 컨테이너를 생성해도 최소한의 용량만 사용한다.

가상화의 특성상 이미지 용량이 크고 여러대의 서버에 배포하는걸 감안하면 단순하지만 엄청나게 영리한 설계이다.

5. 정리

1) OS 독립적이라는 것
2) 서버를 코드로 구성하고 관리 할 수 있다는 것

docker는 도커 파일로 서버를 구성하고 운영하다가
원하는 시점에 이미지를 생성하고 배포하여 동일한 서버구성을 아주 손 쉽게 할 수 있다. 
마치 maven과 gradle을 이용하여 소스 코드를 빌드하여 war,jar 파일을 생성하는 것과 비슷한 맥락이다.

도커를 이용한 무중단 배포와 개발환경 구성에 관심이 있다면 다음 링크를 보면 많은 도움이 될 것 같다.
1. https://subicura.com/2016/06/07/zero-downtime-docker-deployment.html (도커를 이용한 웹서비스 무중단 배포하기) <br>
2. https://medium.com/sjk5766/nginx-%EC%A4%91%EB%8B%A8-%EC%97%86%EC%9D%B4-%EB%B3%80%EA%B2%BD%EB%90%9C-%EC%84%A4%EC%A0%95-%EB%B0%98%EC%98%81%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-b0cc36a5fe59
(Nginx 중단 없이 변경된 설정 반영하는 방법) <br>
3. https://www.44bits.io/ko/post/almost-perfect-development-environment-with-docker-and-docker-compose (도커 컴포즈를 활용하여 완벽한 개발 환경 구성하기)<br>



<img src="/assets/docker2.png">
ref. https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html<br>
ref. https://www.44bits.io/ko/post/why-should-i-use-docker-container <br>
ref. https://medium.com/platformer-blog/practical-guide-on-writing-a-dockerfile-for-your-application-89376f88b3b5<br>
ref. https://www.aquasec.com/wiki/display/containers/Docker+Containers+vs.+Virtual+Machines <br>
ref.