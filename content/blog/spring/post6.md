---
title: spring boot 프로젝트 생성 시, could not download javadoc 에러
date: 2019-11-11
category: spring
draft: false
---

스프링 부트를 사용하려고 spring starter project를 이용하여 프로젝트를 생성하려고 했다.
그런데!
could not download javadoc... 어쩌구 에러창과 함께 라이브러리 설치도 끝마치지 않고 프로젝트가 생성되어 버리는 이상한 문제가...ㅠㅠ
메이븐 관련 오류인 것 같아 검색해보니 해결 방법은 아주 간단했다.

`C:\사용자\USER\.m2` 에 있는 repository 폴더를 삭제하면 된다.

참고로 폴더를 삭제하게 되면 설치된 메이븐 라이브러리가 전부 사라져 다른 프로젝트의 라이브러리까지 재설치 해야하는 번거로움이 있다는 것을 알아두자.
