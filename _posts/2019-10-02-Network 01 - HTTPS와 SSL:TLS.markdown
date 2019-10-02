---
layout: post
title:  " HTTP,HTTPS와 SSL/TLS"
date:   2019-10-02 22:30 +0530
categories: Network
---

* HTTPS 란?

    - HyperText Transfer Protocol over Secure Socket Layer의 약자로서
      HyperText인 HTML을 전송하기 위한 통신규약인 'HTTP'의 보안이 강화된 프로토콜을 의미한다.
      SSL/TLS 프로토콜을 통해 세션 데이터를 암호화하여 전송한다.

* SSL/TLS 프로토콜이란?
    
    - 넷스케이프 사에서 웹서버와 클라이언트(브라우저)간의 보안을 위해 만들었으며, 공캐키/개인키, 대칭키 기반으로 사용한다.


<img src = "/assets/ssllayers.gif">

SSL/TLS 프로토콜을 통해 어떻게 데이터가 암호화되고 전송될까?

서버와 클라이언트가 통신을 할 때는 크게 3단계로 나눠진다.
    handshake -> 전송 -> 세션종료

과정을 설명하기 전에 알아야할 용어 몇 가지를 소개하겠다.

1) CA/Root Certificate  
    - SSL/TLS 인증서를 발급하는 기관 (신뢰할 수 있는 제3자)
      브라우저들은 기본적인 CA 리스트를 내장하고 있다.

2) SSL/TLS 인증서
    - SSL/TLS 프로토콜의 핵심.
      클라이언트와 서버간의 통신을 제3자가 보증해주는 전자화된 문서이다.
      클라이언트가 서버에 접속한 직후에 서버는 클라이언트에게 이 인증서 정보를 전달한다.
      
      클라이언트가 접속한 서버가 신뢰할 수 있는 서버임을 보장한다.
       - 어떻게?
         1. 브라우저에 내장된 CA 리스트 확인
         2. 서버에서 받은 인증서의 발급자가 CA 리스트에 존재한다면 해당 서버는 신뢰할 수 있는 사이트이다. 
            신뢰할 수 있는 기관인 CA가 발급한 인증서이기 때문이다.

3) 대칭 키
    - 서버와 클라이언트가 데이터를 암호화/복호화 할때 사용하는 키.
      키가 하나여서 상대방에서 전달해줘야 한다. 전달 시 키 자체는 암호화 되지 않으므로 노출될 가능성이 있다.

4) 공개키/개인키
    - private key (개인키)와 public key(공개키) 쌍으로 존재한다.
      개인키로 암호화 -> 공개키로 복호화 또는
      공개키로 암호화 -> 개인키로 복호화 만 가능하다.
      주로 서버에서 개인키를 가지고 클라이언트에게 공개키를 전달하는 방식으로 보안을 유지한다.
      공개키가 유출되더라도 개인키는 서버가 소유하고 있으므로 복호화 할 수 없다.      


서버와 클라이언트간의 통신과정은 3단계로 나누어진다고 헀다.

    handshake -> 전송 -> 세션종료

1) handshake

    첫 번째 과정인 handshake에서 데이터가 암호화 되어 통신할 준비가 끝이 난다.
    handshake를 해석하면 악수다.
    사람 간에도 처음에 만나 악수를 하며 서로를 살피는 것 처럼
    서버와 클라이언트도 처음에 handshake를 하는 과정을 하며
    상대방이 존재하는 지, 어떤 방식으로 데이터를 주고 받을지를 파악한다.

<img src="/assets/Handshake1.jpg">
<img src="/assets/Handshake2.jpg">


2) 전송

    handshake가 끝난 서버와 클라이언트는 자유롭게 데이터를 암호화하여 전송한다.

3) 세션 종료

    데이터의 전송이 끝나면 SSL 통신이 끝났음을 서로에게 알려준다. 이 때 통신에서 사용한 대칭키인 세션키를 폐기한다.  


ref. https://sites.google.com/site/tlsssloverview/ssl-tls-protocol-layers
ref. https://opentutorials.org/course/228/4894#signiture
ref. https://ko.wikipedia.org/wiki/HTTPS



