---
title: Antigravity
tags:
  - Vibe Coding
  - Google AI Pro
---

## Antigravity
<!--more-->
 요즘 핫한 바이브 코딩에 회의적이었다. Gemini CLI를 테스트로 사용해 보았는데, 생각보다 성능이 별로였다. 그럴듯 하지만 무언가 되지 않는 코드들...
 얼마전 전 직장 동료가 Claude에 대한 이야기를 하여 Google AI Pro를 구독하여 사용해 보기로 하였고, Setting을 하고 테스트하여 결과가 꽤 흡족하여 정리 한다.

### 환경 설정
- Antigravity 다운로드.  
  - Antigravity 검색  
![그림](/img/post/20260124/01.png){: width="600" height="100%"}
  
  - Window 버전 다운로드  
![그림](/img/post/20260124/02.png){: width="600" height="100%"}
  
- Antigravity 설치.  
![그림](/img/post/20260124/03.png){: width="300" height="100%"}
![그림](/img/post/20260124/04.png){: width="300" height="100%"}
![그림](/img/post/20260124/05.png){: width="300" height="100%"}
![그림](/img/post/20260124/06.png){: width="300" height="100%"}
![그림](/img/post/20260124/07.png){: width="300" height="100%"}
![그림](/img/post/20260124/08.png){: width="300" height="100%"}
![그림](/img/post/20260124/09.png){: width="300" height="100%"}
  
- 셋팅 및 Extensions 설치.  
   ![그림](/img/post/20260124/10.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/11.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/12.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/13.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/14.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/15.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/16.png){: width="300" height="100%"}
     
   - Korean 언어팩 : 설치 후 좌하단의 Restart.  
   ![그림](/img/post/20260124/17.png){: width="200" height="100%"}
   ![그림](/img/post/20260124/18.png){: width="300" height="100%"}
     
   - View Image : Open VSX -> View Image 검색 -> 다운로드 -> VSIX 설치 -> 파일 선택.  
   ![그림](/img/post/20260124/20.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/21.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/22.png){: width="300" height="100%"}
   ![그림](/img/post/20260124/23.png){: width="300" height="100%"}
     
   - 모델 사용량 Helper : Antigravity Quota 설치 -> 우하단 Quota 클릭 -> 체크.  
   ![그림](/img/post/20260124/24.png){: width="600" height="100%"}
   ![그림](/img/post/20260124/25.png){: width="600" height="100%"}
  
### 사용 팁1 : Rule.  
- Agent의 Custumizations를 클릭하여 Rule을 설정한다.  
- Global Rule, Workspace Rule을 설정 할 수 있다.  
- Global Rule은 Antigravity를 사용할 때 적용되는 기본 Rule이다. (예를 들어 '모든 대답은 한글로 대답해줘'와 같은.)  
- Workspace Rule에서는 작업하는 Workspace의 Rule을 설정한다. (예를 들어 SDK 버전이라던지, 접근 가능한 파일 정의라던지)  
   ![그림](/img/post/20260124/26.png){: height="400"}
   ![그림](/img/post/20260124/27.png){: height="400"}
   ![그림](/img/post/20260124/28.png){: height="400"}

### 사용 팁2 : Skills.  
 - Claude Code의 Skills라는 기능을 Antigravity에서 사용 할 수 있다.
 - 사용법은 해당 리포지토리를 다운로드 하고, Skill 폴더를 Workspace에 놓고 Agent를 사용하면 된다.  
 - Skill은 Agent에 사전에 특정한 기능을 위한 사전 프롬프트다.
   (https://github.com/anthropics/skills)  
   ![그림](/img/post/20260124/29.png){: height="400"}