---
title: HTL 전역 개체
description: HTL에서 열거할 수 있는 개체, Java 지원 개체 및 JavaScript 지원 개체에 대해 알아봅니다.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '200'
ht-degree: 32%

---


# HTL 전역 오브젝트 {#htl-global-objects}

HTL에서는 아무 것도 지정하지 않고 개발자에게 유용한 많은 개체에 액세스할 수 있습니다. 이러한 객체는 [Use-API.](java-use-api.md)

>[!NOTE]
>
>AEM의 JSP 개발에 익숙한 개발자의 경우 HTL에서는 다음을 포함한 후 JSP에서 일반적으로 사용할 수 있는 모든 객체에 액세스할 수 있습니다 `global.jsp`.

## 열거형 오브젝트 {#enumerable-objects}

이러한 오브젝트는 일반적으로 사용되는 정보에 대한 편리한 액세스를 제공합니다. 해당 컨텐츠에 점 표기법으로 액세스할 수 있으며 를 사용하여 반복할 수 있습니다 `data-sly-list` 또는 `data-sly-repeat`.

| 변수 이름 | 설명 | 지원 |
|--- |--- |--- |
| `properties` | 현재 리소스의 속성 목록 | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | 현재 페이지의 페이지 속성 목록 | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | 현재 페이지의 상속된 페이지 속성 목록 | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Java 지원 오브젝트 {#java-backed-objects}

다음 각 오브젝트는 해당 Java 오브젝트에 의해 지원됩니다.

| 변수 이름 | 설명 |
|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| `currentContentPolicy` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentContentPolicyProperties` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| `currentPage` | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| `resource` | `org.apache.sling.api.resource.Resource` |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |
| `resourcePage` | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## JavaScript 지원 오브젝트 {#javascript-backed-objects}

JavaScript로 HTL 논리를 백업하는 것이 가능합니다. 단, 기본 및 권장 메서드는 [Sling 모델](https://sling.apache.org/documentation/bundles/models.html)을 사용하는 것입니다.
