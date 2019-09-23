---
title: HTL 전역 개체
seo-title: HTL 전역 개체
description: 아무 것도 지정하지 않아도 HTL은 global.jsp를 포함하여 JSP에서 일반적으로 사용할 수 있는 모든 객체에 대한 액세스를 제공합니다.
seo-description: '아무 것도 지정하지 않아도 HTL은 global.jsp를 포함하여 JSP에서 일반적으로 사용할 수 있는 모든 객체에 대한 액세스를 제공합니다. '
uuid: e03afbb-a683-4323-8224-53d8ef59caef
contentOwner: 사용자
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: 참조
discoiquuid: fe071a7e-0dae-45c1-9f86-80c558483f87
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: c3beb0d02f18483b1b000c1bf70cd59a3dcc2035

---


# HTL 전역 개체{#htl-global-objects}

HTL은 아무 것도 지정하지 않아도 JSP에서 일반적으로 사용할 수 있었던 모든 객체에 대한 액세스 권한을 제공합니다 `global.jsp`. 이러한 개체는 Use-API를 통해 소개될 수 있는 [모든 개체에 추가됩니다](use-api.md).

## 열거할 수 있는 개체 {#enumerable-objects}

이러한 개체는 일반적으로 사용되는 정보에 편리하게 액세스할 수 있습니다. 이러한 컨텐츠는 점 표기법으로 액세스할 수 있으며 `data-sly-list` 또는 을 사용하여 반복할 수 `data-sly-repeat`있습니다.

| 변수 이름 | 설명 |
|--- |--- |
| 속성 | 현재 리소스의 속성 목록입니다. Backed by [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| pageProperties | 현재 페이지의 페이지 속성 목록입니다. Backed by [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.hmtl) |
| 상속된 페이지 속성 | 현재 페이지의 상속된 페이지 속성 목록입니다. Backed by [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |


## Java 지원 개체 {#java-backed-objects}

다음 각 개체는 해당 Java 개체의 지원을 받습니다.

아래 표에서 가장 유용한 변수는 굵게 강조 표시됩니다.

| 변수 이름 | 설명 |  |
|---|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |  |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |  |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |  |
| `currentNode` | `javax.jcr.Node` |  |
| `currentPage` | `com.day.cq.wcm.api.Page` |  |
| `currentSession` | `javax.servlet.http.HttpSession` |  |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |  |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |  |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |  |
| `log` | `org.slf4j.Logger` |  |
| `out` | `java.io.PrintWriter` |  |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |  |
| `reader` | `java.io.BufferedReader` |  |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |  |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |  |
| `resource` | `org.apache.sling.api.resource.Resource` |  |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |  |
| `resourcePage` | `com.day.cq.wcm.api.Page` |  |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |  |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |  |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |  |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |  |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |  |

## JavaScript에서 지원하는 개체 {#javascript-backed-objects}

또한 JavaScript로 지원되는 객체를 사용할 수도 있습니다. 그러나 AEM 6.2부터 이러한 개체는 여전히 실험적인 개체이며 동일한 작업을 수행할 수 있는 Java 지원 개체를 사용하는 것이 좋습니다.

<!-- 

Comment Type: draft

<p> </p> 
<p>JS-specific context variables: These supply access to asynchronous implementions of all the Java objects listed below). To write HTL code that is protable to granite.js, you must use the variables provided by aem and sly, not the native Java variables.</p> 
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
<p>JS specific non-HTL related variables. Present due to JS-implementaion. Generally not used in templating:</p> 
<ul> 
 <li>console: JS Object</li> 
 <li>exports: JS Object</li> 
 <li>module: JS Object</li> 
 <li>setImmediate: JS Function</li> 
 <li>setTimeout: JS Function</li> 
 <li>use: JS Function</li> 
</ul>
-->
