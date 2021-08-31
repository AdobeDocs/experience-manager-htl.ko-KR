---
title: HTL 전역 개체
description: 아무것도 지정하지 않아도 HTL은 global.jsp를 포함시킨 후 JSP에서 일반적으로 사용할 수 있었던 모든 개체에 대한 액세스를 제공합니다.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '220'
ht-degree: 100%

---

# HTL 전역 개체 {#htl-global-objects}

아무것도 지정하지 않아도 HTL은 `global.jsp`를 포함시킨 후 JSP에서 일반적으로 사용할 수 있었던 모든 개체에 대한 액세스를 제공합니다. 이러한 개체는 [Use-API](use-api.md)를 통해 도입될 수 있는 모든 것에 추가됩니다.

## 열거형 개체 {#enumerable-objects}

이러한 개체는 일반적으로 사용되는 정보에 대한 편리한 액세스를 제공합니다. 콘텐츠는 점 표기법으로 액세스할 수 있으며 `data-sly-list` 또는 `data-sly-repeat`를 사용하여 반복할 수 있습니다.

| 변수 이름 | 설명 |
|--- |--- |
| `properties` | 현재 리소스의 속성 목록입니다. [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) 지원 |
| `pageProperties` | 현재 페이지의 페이지 속성 목록입니다. [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) 지원 |
| `inheritedPageProperties` | 현재 페이지의 상속된 페이지 속성 목록입니다. [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) 지원 |

## Java 지원 개체 {#java-backed-objects}

다음 각 개체는 해당 Java 개체에 의해 지원됩니다.

아래 표에서 가장 유용한 변수는 굵게 강조 표시되어 있습니다.

| 변수 이름 | 설명 |
|---|---|
| **`component`** | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| **`currentDesign`** | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| **`currentPage`** | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| **`request`** | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| **`resource`** | `org.apache.sling.api.resource.Resource` |
| **`resourceDesign`** | `com.day.cq.wcm.api.designer.Design` |
| **`resourcePage`** | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| **`wcmmode`** | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## JavaScript 지원 개체 {#javascript-backed-objects}

JavaScript로 HTL 논리를 백업하는 것이 가능합니다. 단, 기본 및 권장 메서드는 [Sling 모델](https://sling.apache.org/documentation/bundles/models.html)을 사용하는 것입니다.

<!-- 

Comment Type: draft

<p> </p> 
<p>JS-specific context variables: These supply access to asynchronous implementations of all the Java objects listed below). To write HTL code that is portable to granite.js, you must use the variables provided by aem and sly, not the native Java variables.</p> 
<ul> 
 <li>wcm
  <ul> 
   <li>currentPage</li> 
   <li>nativePage: [com.day.cq.wcm.apiPage]</li> 
   <li>properties: {<i>enumerable</i>}</li> 
  </ul> </li> 
 <li>granite
  <ul> 
   <li>request
    <ul> 
     <li>parameters: {<i>enumerable</i>}</li> 
     <li>nativeRequest: [org.apache.sling.scripting.core.impl.helper.OnDemandReaderRequest]</li> 
     <li>pathInfo
      <ul> 
       <li>nativePathInfo: [SlingRequestPathInfo: path='/content/geometrixx/en/jcr:content/par/text', selectorString='null', extension='html', suffix='null']</li> 
      </ul> </li> 
    </ul> </li> 
   <li>resource
    <ul> 
     <li>nativeResource: [Paragraph, path=/content/geometrixx/en/jcr:content/par/text, type=wcm/foundation/components/text, cssClass=default, column=0/0, diffInfo=[null], resource=[JcrNodeResource, type=wcm/foundation/components/text, superType=null, path=/content/geometrixx/en/jcr:content/par/text]]</li> 
     <li>path: "/content/geometrixx/en/jcr:content/par/text"</li> 
     <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
    </ul> </li> 
   <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
  </ul> </li> 
</ul> 
<p>JS specific non-HTL related variables. Present due to JS-implementation. Generally not used in templating:</p> 
<ul> 
 <li>console: JS Object</li> 
 <li>exports: JS Object</li> 
 <li>module: JS Object</li> 
 <li>setImmediate: JS Function</li> 
 <li>setTimeout: JS Function</li> 
 <li>use: JS Function</li> 
</ul>
-->
