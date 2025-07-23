---
title: HTL 사양
description: 자세한 구문 정보는 HTL 사양을 참조하십시오.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: ht
source-wordcount: '134'
ht-degree: 100%

---


# HTL 사양 {#htl-specification}

HTML 템플릿 언어(HTL)는 HTML에 대한 기본 및 권장 서버측 템플릿 시스템입니다.

## HTL 레이어 {#layers}

AEM에서는 여러 계층으로 HTL을 정의할 수 있습니다.

1. **[HTL 사양](https://github.com/adobe/htl-spec)** - HTL은 누구나 무료로 구현할 수 있는 오픈 소스 플랫폼 애그노스틱 사양입니다. 사양은 해당 GitHub 저장소에서 유지됩니다.
1. **[Sling HTL 스크립팅 엔진](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - `Sling` 프로젝트는 AEM에서 사용되는 HTL의 참조 구현을 만들었습니다. `Sling` 프로젝트에 대해 해당 설명서가 유지됩니다.
1. **[AEM 확장 기능](aem-extensions.md)** - AEM은 `Sling` HTL 스크립팅 엔진을 기반으로 하여 AEM에 특화된 편리한 기능을 개발자에게 제공합니다. 이들 확장 기능은 이 설명서 세트의 일부로 문서화되어 있습니다.

AEM에서 사용되는 HTL의 모든 레이어에 대한 전용 설명서로의 위 링크를 따르십시오.
