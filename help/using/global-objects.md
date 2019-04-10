---
title: HTL 글로벌 개체
seo-title: HTL 글로벌 개체
description: HTL를 지정하지 않고도 Global. jsp를 포함하여 JSP에서 일반적으로 사용할 수 있었던 모든 개체에 대한 액세스 권한을
  제공합니다.
seo-description: HTL를 지정하지 않고도 Global. jsp를 포함하여 JSP에서 일반적으로 사용할 수 있었던 모든 개체에 대한 액세스
  권한을 제공합니다.
uuid: E 03 AFFBB-A 683-4323-8224-53 D 8 EF 59 CAEF
contentOwner: 사용자
products: sg_ Experiencemanager/HTL
topic-tags: HTML-template-language
content-type: 참조
discoiquuid: FE 071 A 7 E -0 DAE -45 C 1-9 F 86-80 C 558483 F 87
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL 글로벌 개체{#htl-global-objects}

HTL는 아무 것도 지정하지 않아도, 포함 후 JSP에서 일반적으로 사용할 수 `global.jsp`있었던 모든 개체에 대한 액세스를 제공합니다. 이러한 개체는 [use-api를 통해 소개될 수 있는 것 외에도 적용됩니다](use-api.md).

## 열거 가능한 객체 {#enumerable-objects}

이러한 개체는 일반적으로 사용되는 정보에 편리하게 액세스할 수 있습니다. 도트 표기법을 사용하여 컨텐츠에 액세스할 수 있으며 또는을 사용하여 `data-sly-list` 반복할 수 `data-sly-repeat`있습니다.

| 변수 이름 | 설명 |
|--- |--- |
| 속성 | 현재 리소스의 속성 목록입니다. org. apache. sling. api. resource. valuemap에 의해 [지원됩니다.](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| Pageproperties | 현재 페이지의 페이지 속성 목록입니다. org. apache. sling. api. resource. valuemap에 의해 [지원됩니다.](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.hmtl) |
| Inheritedpageproperties | 현재 페이지의 상속된 페이지 속성 목록입니다. org. apache. sling. api. resource. valuemap에 의해 [지원됩니다.](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |


## Java 지원 개체 {#java-backed-objects}

다음 개체는 해당 Java 객체에 의해 지원됩니다.

아래 표에서 가장 유용한 변수는 굵은 색으로 강조 표시됩니다.

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

## JavaScript 지원 개체 {#javascript-backed-objects}

JavaScript에 의해 지원되는 개체 또한 있습니다. 그러나 AEM 6.2 부터는 이러한 개체가 여전히 실험적이며 Java 지원 개체를 사용하는 것이 더 낫습니다. 이 개체는 이를 수행할 수 있습니다.

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
