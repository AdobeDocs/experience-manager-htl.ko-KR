---
title: HTL Use-API
description: HTL에 대해 두 가지 API(Java Use-API 및 Javascript Use-API) 사용 가능
exl-id: 8f95707e-952c-4efe-a44e-9a1ad501605e
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 8%

---

# HTL Use-API {#htl-use-api}

HTL에서는 비즈니스 로직이 마크업과 혼합되지 않도록 하여 문제를 분리하도록 권장합니다. 비즈니스 로직은 Use-API를 통해 구현할 수 있습니다.

다음 표는 각 API의 장점과 단점에 대한 개요를 제공합니다.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **장점** | <ul><li>더 빠름</li><li>디버거를 사용하여 검사할 수 있습니다.</li><li>간편한 단위 테스트</li></ul> | <ul><li>프런트 엔드 개발자가 수정할 수 있음</li><li>구성 요소 내에 있으며 구성 요소의 보기 논리를 해당 템플릿에 가깝게 유지합니다</li></ul> |
| **단점** | <ul><li>프런트 엔드 개발자는 수정할 수 없습니다.</li></ul> | <ul><li>느림</li><li>디버거 없음(아직)</li><li>단위 테스트의 어려움</li></ul> |

페이지 구성 요소의 경우 모든 모델 논리가 Java에 있는 혼합 모델을 사용하여 보기에서 발생하는 모든 작업(즉, 구성 요소 내)에 대해 알 수 없는 명확한 API를 제공하는 것이 좋습니다. AEM에는 대부분의 경우를 지원할 수 있는 페이지 또는 리소스 API와 같은 훌륭한 기본 모델이 포함되어 있습니다.

구성 요소에 고유한 모든 보기 로직은 해당 구성 요소에 속하므로 해당 구성 요소 내에 JavaScript로 배치해야 합니다.
