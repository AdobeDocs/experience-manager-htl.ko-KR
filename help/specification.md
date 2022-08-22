---
title: HTL 사양
description: 자세한 구문 정보는 HTL 사양 을 참조하십시오.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: 942c5249cc38e49a7d617ba4cf65a878f2e229ab
workflow-type: tm+mt
source-wordcount: '152'
ht-degree: 6%

---


# HTL 사양 {#htl-specification}

HTL(HTML 템플릿 언어)은 HTML을 위한 기본 및 권장 서버 사이트 템플릿 시스템입니다.

## HTL 레이어 {#layers}

AEM에서 사용되는 HTL은 여러 레이어로 정의할 수 있습니다.

1. **[HTL 사양](https://github.com/adobe/htl-spec)** - HTL은 누구나 자유롭게 구현할 수 있는 오픈 소스, 플랫폼 지원 사양입니다. 사양은 GitHub 리포지토리에서 유지됩니다.
1. **[Sling HTL 스크립팅 엔진](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - Sling 프로젝트가 AEM에서 사용하는 HTL의 참조 구현을 생성했습니다. 설명서는 Sling 프로젝트에 의해 유지 관리됩니다.
1. **[AEM 확장](aem-extensions.md)** - AEM은 AEM과 관련된 편리한 기능을 개발자에게 제공하기 위해 Sling HTL 스크립팅 엔진 위에 빌드됩니다. 이러한 확장은 이 설명서 세트의 일부로 설명되어 있습니다.

AEM에서 사용하는 모든 HTL 레이어에 대한 전용 설명서에 대한 위의 링크를 따르십시오.
