---
title: HTL use-API
seo-title: Adobe HTL Use-API
description: HTL에 이용 가능한 두 가지 API - Java Use-API 및 Javascript Use-API
seo-description: Adobe HTL에 이용 가능한 두 가지 API - Java Use-API 및 Javascript Use-API
uuid: ab 44 aa 5 c-ce 7 e -40 b 9-97 fb-e 86 c 6 a 28405 c
contentOwner: 사용자
products: sg_ Experiencemanager/HTL
topic-tags: HTML-template-language
content-type: 참조
discoiquuid: 89004426-EB 59-4 B 63-913 F -51 BF 98662773
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL use-API {#htl-use-api}

다음 표는 각 API의 장단점을 개괄적으로 설명합니다.

|  | **Java Use-API** | **JavaScript use-api** |
|--- |--- |--- |
| **전문가** | <ul><li>더욱 빨라진 속도</li><li>디버거로 검사할 수 있습니다.</li><li>간편한 유닛 테스트</li></ul> | <ul><li>프런트 엔드 개발자에 의해 수정 가능</li><li>는 구성 요소 내에서 해당 템플릿에 가까운 구성 요소의 보기 논리를 유지합니다.</li></ul> |
| **cons** | <ul><li>프런트 엔드 개발자가 수정할 수 없음</li></ul> | <ul><li>느림</li><li>디버거 없음 (아직)</li><li>유닛 테스트의 어려움</li></ul> |


페이지 구성 요소의 경우, 모든 모델 로직이 Java로 되어 있는 혼합 모델을 사용하는 것이 좋습니다. 예를 들어, 구성 요소 내 (예: 구성 요소 내) 에 발생하는 모든 것과 관련이 없는 명확한 API를 제공합니다. AEM 에는 대부분의 사례를 덮을 수 있는 페이지 또는 리소스 API와 같은 훌륭한 기본 모델이 포함되어 있습니다.

구성 요소에 관련된 모든 보기 로직은 해당 구성 요소에 속하므로 해당 구성 요소 내에 JavaScript로 배치됩니다.
