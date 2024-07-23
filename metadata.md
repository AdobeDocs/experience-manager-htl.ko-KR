---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.ko-KR
index: y
recommendations: noDisplay
source-git-commit: 22f62868df0fcfc558e5d62434dde843a9f3ca83
workflow-type: tm+mt
source-wordcount: '81'
ht-degree: 40%

---


# 내부 사용을 위한 메타데이터

GitHub 작성 시스템은 메타데이터를 계층적으로 정의하며, 다음과 같이 선례 수준이 높아집니다.

1. metadata.md
1. ToC
1. 문서

metadata.md 파일에 정의된 메타데이터는 전체 리포지토리에 적용되지만 ToC 및 문서 수준에서 재정의될 수 있습니다. 메타데이터 재정의는 가능한 한 낮은 수준에서 수행해야 합니다.

`experience-manager-core-components.en` 저장소의 메타데이터는 필요한 최소값입니다.

metadata.md

* `product`
* `git-repo`
* `index: y`

더 이상 사용되지 않음:

* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

ToCs

* `sub-product`
* `user-guide-title`

문서

* `title`
* `description`
* `index: n` (이전 버전의 구성 요소만 해당)

