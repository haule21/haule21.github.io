---
layout: posts
permalink: /:categories/:title
title: "SpringBoot 버전 관련하여 기록"
categories: spring
---

처음에는 Spring 만 사용하여 외부 톰켓으로 이어 사용할 생각이였으나,
이제는 Spring Boot만 사용할 수 있게 되었다고 한다. Eclipse 2022-03 버전 부터 였던가.. 기억난다.

어쩔 수 없이 Spring Boot 를 사용하여 프로젝트를 진행 하는데
난 당연히 최신 버전을 선호 했고 많은 실수를 겪었다.

처음 버전 셋이다.
"Spring boot 3, Java 18, Tomcat 9.0.65, gradle-7.5.1"

무슨 문제가 있었을까? 잘 기억은 안 나지만.. Tomcat은 잘 켜지지만 Spring 프로젝트와 연결이 잘 되지 않는 것을 확인 했었다.
처음엔 버전 문제인지 전혀 모르고 몇 시간을 해맸지만 지금 생각하니 참 웃기다. 버전을 내리는 것을 선택 했다면 지금도 이렇게
고생하고 있지 않을 텐데.

다음 버전 셋이다.
"Spring boot 3, Java 17, Tomcat 10.0.27, gradle-7.5.1"

이 때 나는 javax 와 jakarta 에 대해서 정말 많이 읽어 봤다. 언제 쯤 알게 됐냐면 바로 Tiles를 Gradle에 추가하고서..
import 패키지에 전혀 찾을 수 없었기 때문이다. 정말 많은 글 들을 읽어봤었는데 그리고 나서 나는 Spring 6 때 부터 
해당 tiles 를 없앤 것을 알 수 있었다. 계속 버전을 수정하다보면 또 버전에 부딪히는 일이 생길 것으로 나는 생각 했고
이 때부터는 javax가 아닌 jakarta 설정을 진행 했고, jakarta로 변경하니 외장 톰켓에서 jsp 파일을 못 읽어
내장 톰켓으로 변경 까지 진행했다. 그래서.. tiles를 대체할 만한 건 없을까? 계속 찾아봐야겠다.
