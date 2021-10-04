---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://git.corp.adobe.com/AdobeDocs/experience-manager-htl.ko-KR
index: y
source-git-commit: 89b9e89254f341e74f1a5a7b99735d2e69c8a91e
workflow-type: tm+mt
source-wordcount: '107'
ht-degree: 91%

---


# 내부 사용을 위한 메타데이터

GitHub 제작 시스템의 메타데이터는 계층적이며 다음과 같이 증가하는 선례 수준으로 정의됩니다.

1. metadata.md
1. ToC
1. 문서

metadata.md 파일에 정의된 메타데이터는 전체 리포지토리에 적용되지만 ToC 및 문서 수준에서 재정의될 수 있습니다. 메타데이터 재정의는 가능한 한 낮은 수준에서 수행해야 합니다.

experience-manager-core-components.en 리포지토리의 메타데이터는 요구되는 최소값입니다.

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

메타데이터에 대한 추가 정보는 [내부 제작 안내서](https://experienceleague.adobe.com/docs/authoring-guide-exl/using/authoring/features/metadata.html#solution)에서 확인할 수 있습니다.
