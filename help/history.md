---
title: HTL의 역사
description: AEM의 오랜 사용자들을 위해, 이 문서는 HTL, HTL이 JSP를 대체하는 방법 및 Sightly에서의 이름 변경에 대한 배경을 제공합니다.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 100%

---


# HTL의 역사 {#history-of-htl}

AEM의 오랜 사용자들을 위해, 이 문서는 HTL, HTL이 JSP를 대체하는 방법 및 Sightly에서의 이름 변경에 대한 배경을 제공합니다.

## 이전 명칭 Sightly {#sightly}

HTML 템플릿 언어(HTL)는 HTML에 대한 Adobe Experience Manager의 기본 및 권장 서버측 템플릿 시스템입니다. 이전 버전의 AEM에서 사용된 JSP(JavaServer Pages)를 대신합니다.

## JSP 대신 HTL {#htl-over-jsp}

JSP에 비해 여러 이점을 제공하므로 새 AEM 프로젝트에 HTML 템플릿 언어를 사용하는 것이 좋습니다. 그러나 기존 프로젝트의 경우, 향후 몇 년 동안 기존 JSP를 유지 관리하는 것보다 노력이 덜 들 것으로 추정되는 경우에만 마이그레이션이 의미가 있습니다.

하지만 HTL로 작성된 구성 요소는 JSP 또는 ESP로 작성된 구성 요소와 호환되기 때문에 HTL로의 전환이 반드시 양자택일의 문제는 아닙니다. 기존 프로젝트가 문제 없이 새 구성 요소에 대해서는 HTL을 사용하고 기존 구성 요소에 대해서는 JSP를 유지할 수 있다는 의미입니다.

동일한 구성 요소 내에서도 HTL 파일을 JSP 및 ESP와 함께 사용할 수 있습니다. 다음 예제의 **라인 1**&#x200B;에서는 JSP 파일에서 HTL 파일을 포함하는 방법을, **라인 2**&#x200B;에서는 HTL 파일에서 JSP 파일을 포함하는 방법을 보여 줍니다.

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## FAQ {#frequently-asked-questions}

다음은 HTL을 처음 사용하는 숙련된 AEM 개발자들이 자주 묻는 몇 가지 질문입니다.

### HTL에는 JSP에 없는 제한이 있습니까? {#limitations}

JSP로 할 수 있는 일을 HTL로도 달성할 수 있어야 한다는 점에서 JSP와 비교하여 HTL에 있는 제한은 사실 없습니다. 하지만 HTL은 여러 측면에서 JSP보다 더 엄격하게 설계되었습니다. 단일 JSP 파일에서 모두 달성할 수 있는 것은 HTL에서 달성할 수 있도록 Java 클래스 또는 JavaScript 파일로 분리해야 할 수도 있습니다. 그러나 일반적으로는 논리와 마크업 사이의 문제를 잘 분리하기 위해 바람직한 것입니다.

### HTL은 JSP 태그 라이브러리를 지원합니까? {#tag-libraries}

아니요. 하지만 시작 설명서의 [클라이언트 라이브러리 로드](getting-started.md#loading-client-libraries) 섹션에서 볼 수 있듯이 템플릿 및 호출 문은 유사한 패턴을 제공합니다.

### AEM 프로젝트에서 HTL 기능을 확장할 수 있습니까? {#extended}

아니요. 할 수 없습니다. HTL은 논리 재사용을 위한 강력한 확장 메커니즘([Use-API](#use-api-for-accessing-logic))과 프로젝트 코드를 모듈화하는 데 사용할 수 있는 마크업(템플릿 및 호출 문)을 제공합니다.

### JSP와 비교하여 HTL의 주요 이점은 무엇입니까? {#benefits}

보안 및 프로젝트 효율성이 주요 이점이며 [개요](overview.md)에 자세히 설명되어 있습니다.

### JSP는 결국 없어집니까? {#go-away}

이 제품군에 대한 계획은 없습니다.

## 명칭에 담긴 의미는 무엇입니까? {#what-is-in-a-name}

AEM 6.0 및 6.1에서 HTL은 **Sightly**&#x200B;라고 불렸었습니다. Adobe는 무엇을 위한 사양인지 명확히 하고 Adobe의 명명 지침에 맞추기 위해 **HTML 템플릿 언어** 또는 **HTL**&#x200B;로 명칭을 변경했습니다. 이런 명칭 변경은 2016년 8월부터 적용되었으며 AEM 버전 6.0 이상에 적용됩니다.

>[!NOTE]
>
>이런 명칭 변경은 코드나 API에 영향을 미치지 않으므로 호환성에는 영향을 미치지 않습니다. 자세한 내용은 [이 공지 비디오를 참조하십시오.](https://helpx.adobe.com/kr/experience-manager/how-to/announce-htl.html)

HTL에 대한 자세한 정보와 좋은 시작 지점은 [HTML 템플릿 언어(HTL) 시작 안내서](overview.md)에서 찾아볼 수 있습니다.
