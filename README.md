# 8장 병합과 충돌 요약 정리입니다.
# 🔔병합
❗독립된 브랜치에서 개발 작업이 끝나면 다시 원본 브랜치에 작업한 결과를 반영해야 합니다. 분리된 브랜치를 한 브랜치로 작업을 병합(합치기)이라고 합니다.두 코드를 하나씩 직접 비교해가며 수동으로 병합하거나 깃 도구를 사용하여 자동으로 병합할 수 있습니다.❗

## 📌8.1.1 하나씩 직접 비교하는 수동 병합
#### 수동으로 병합하려면 양쪽 파일을 일일이 비교하며 바뀐 점을 찾아서 적용해야 한다.하지만 오류 없이 코드를 병합하는 것은 간단하지 않다. <br>
❗예를들어 소스 코드가 2개보다 많다면 어떻게 될까요? 원본 소스 A를 복제하여 B를 수정하고, B를 복제하여 C도 수정하고 있습니다.<br>
수정이 완료되면 B는 원본 소스 A에 수정 내역을 반영하는 작업을 해야하고, C 또한 B를 반영하여 수정된 원본 소스 A에 또다시 수정 작업을 반영해야 합니다.병합하려면 코드의 변경된 시점을 기억해야 합니다.변경된 시점을 기준으로 순차적으로 병합 처리를 하는 것이 좀 더 쉽습니다.❗ <br>

![image](https://user-images.githubusercontent.com/105197524/200317261-770b1de7-a2cb-47fc-bb13-4209cc696593.png)

## 📌8.1.2 깃으로 자동 병합

1. 깃은 자동 병합은 원본을 기준으로 두 파일의 변경 이력을 비교합니다. <br>
2. 깃의 병합은 브랜치를 기준으로 합니다. <br>

따라서 과거에는 모든 병합 작업을 수동으로 해서 여려웠지만, 이제는 이러한 수동 병합 작업을 깃이 대신 처리합니다. 하지만 깃이 모든 코드의 병합을 완벽하게 처리는 할수 없고 아무리 좋은 도구라해도 자동으로 반영하지 못하는 것들이 있는데 이를 **충돌**이라 합니다.

## 📌8.1.2 병합 방식
깃은 병합을 위해 두 가지 기본적인 알고리즘 방식을 제공한다.
- Fast-Forword 병합
- 3-way 병합

두 방식으로 실습하기 전에 미리 별도의 저장소를 생성해 두겠습니다.

![제목 없음](https://user-images.githubusercontent.com/105197524/200338767-50a1d7b6-84e1-4e58-9d4b-c348dcb7e216.png)

## 📌Fast-Forward 병합
**깃의 가장 간단한 브랜치 병합은 Fast-Forward 방식이다.일반적으로 혼자 개발할 때 사용한다.**

## 📌 8.2.1 브랜치 생성과 수정 작업
브랜치를 생성 후 브랜치 커밋 확인
```
$ git checkout -b feature --- 브랜치 생성 및 이동
PC18@LAPTOP-L4CJE14H MINGW64 ~/Desktop/e/study08 (feature)

$ git rev-parse feature  --- 브랜치 커밋 확인
525bf0f445d8829a9759d215071901799791db5a
```
index.htm 파일 수정후 등록 및 커밋 후 커밋 이력 확인
```
$ code index.htm  --- feature 브랜치 안에 있는 index.htm 파일 수정

$ git commit -am "add header" --- 등록 및 커밋
[feature a209baf] add header
1 file changed, 2 insertions(+)

$ git log  --- 커밋 이력 확인하기
commit a209bafd738333feebf1c37c6f914f7df05e9fce (HEAD -> feature)
Author: uh004 <uh004@naver.com>
Date:   Mon Nov 7 23:54:56 2022 +0900

    add header

commit 525bf0f445d8829a9759d215071901799791db5a (main)
Author: uh004 <uh004@naver.com>
Date:   Mon Nov 7 23:43:53 2022 +0900

    frist
```
feature 브랜치에 등록 및 커밋 2번하기
```
$ code index.htm  --- feature 브랜치 안에 있는 index.htm 파일 수정

$ git commit -am "add menu1"  --- 등록 및 커밋
[feature 3c0edb4] add menu1
 1 file changed, 3 insertions(+)

$ code index.htm  --- feature 브랜치 안에 있는 index.htm 파일 수정

$ git commit -am "add menu2"  --- 등록 및 커밋
[feature 140f38d] add menu2
 1 file changed, 1 insertion(+)
```
**소스트리에서 브랜치 커밋 로그를 확인해 보면 feature 브랜치가 main 브랜치에서 분리되어 header, menu1, menu2 커밋 작업을 세 번 수행한 것을 볼수 있다.**
```
$ git log --graph --oneline --all  --- 모든 커밋 이력을 한줄그래프로 확인
* 140f38d (HEAD -> feature) add menu2
* 3c0edb4 add menu1
* a209baf add header
* 525bf0f (main) frist
```
소스트리에서 확인

![제목 없음](https://user-images.githubusercontent.com/105197524/200343762-e7737cdb-1a70-4c5e-b9f3-7bcfdfbf9965.png)

## 📌8.2.2 병합 위치
**브랜치를 병합하려면 가준과 대상이 있어야 합니다.기준은 체크아웃된 현재 브랜치입니다. 우리는 main 브랜치에서 feature 브랜치로 분기하여 작업했습니다.
병합을 하기위해서 main 브랜치로 체크아웃 하겠습니다.**
```
$ git checkout main
Switched to branch 'main'
```
main 브랜치로 체크아웃하고 파일을 확인하면 feature에서 했던 작업이 사려졌습니다. 이제 feature 브랜치에서 작업한 내용을 병합해 보겠습니다.
```
code index.htm --- VS Code 실행
```

## 📌8.2.3 Fast-Forward 병합 적용
커밋 작업은 분기된 feature 브랜치에서 모두 수행했습니다. 아직 main 브랜치에는 추가된 커밋이 없습니다. 이러한 상태에서 두 브랜치를 병합해보겠습니다.
```
$ git merge feature
Updating 525bf0f..140f38d
Fast-forward
 index.htm | 6 ++++++
 1 file changed, 6 insertions(+)
```
main 브랜치와 feature 브랜치가 잘 병합된것을 확인할 수 있다. vscode를 실행하면 feature에서 수정한 내용이 적용되었습니다.
```
$ git log --oneline
140f38d (HEAD -> main, feature) add menu2
3c0edb4 add menu1
a209baf add header
525bf0f frist

$ code index.htm  --- vscode 실행
```
**Fast-Forward 병합은 병합할 하나의 브랜치 파일을 기준 브랜치로 복하여 수정된 파일을 원본에 그대로 적용한 것과 같습니다.** <br>
**즉 원본에 추가된 내용이 없다는 가정하에 변경한 파일을 대체하는 것입니다.**
