---
title: HTL Use-API
seo-title: Adobe HTL Use-API
description: HTL에 이용 가능한 두 가지 API - Java Use-API 및 Javascript Use-API
seo-description: Adobe HTL에 이용 가능한 두 가지 API - Java Use-API 및 Javascript Use-API
uuid: ab44aa5c-ce7e-40b9-97fb-e86c6a28405c
contentOwner: 사용자
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: 참조
discoiquuid: 89004426-eb59-4b63-913f-51bf9866273
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 5cbaf9c747acf748d12559c2c8e3aba4600cf9a4

---


# HTL Use-API {#htl-use-api}

다음 표는 각 API의 장단점에 대한 개요를 제공합니다.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **전문가** | <ul><li>faster</li><li>디버거를 사용하여 검사할 수 있습니다.</li><li>간편한 유닛 테스트</li></ul> | <ul><li>프런트 엔드 개발자가 수정할 수 있음</li><li>는 구성 요소 내에 있으며, 구성 요소의 보기 논리를 해당 템플릿에 가깝게 유지합니다.</li></ul> |
| **반대** | <ul><li>프런트 엔드 개발자는 수정할 수 없습니다.</li></ul> | <ul><li>느림</li><li>디버거 없음(아직)</li><li>유닛 테스트의 어려움</li></ul> |


페이지 구성 요소의 경우, 모든 모델 논리가 Java에 있는 혼합 모델을 사용하여 보기에서 발생하는 모든 것(즉, 구성 요소 내)에 관계없이 명확한 API를 제공하는 것이 좋습니다. AEM에는 대부분의 경우를 처리할 수 있는 페이지 또는 리소스 API와 같은 뛰어난 기본 모델이 포함되어 있습니다.

구성 요소에만 해당하는 모든 보기 로직은 해당 구성 요소에 속하므로 해당 구성 요소 내에 JavaScript로 배치해야 합니다.
