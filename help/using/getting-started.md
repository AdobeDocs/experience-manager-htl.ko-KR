---
title: HTL 시작하기
description: AEM에서 지원하는 HTL은 AEM에서 HTML에 대한 선호되고 권장되는 서버측 템플릿 시스템으로 JSP를 대체합니다.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: tm+mt
source-wordcount: '2471'
ht-degree: 0%

---

# HTL 시작하기 {#getting-started-with-htl}

AEM(Adobe Experience Manager)에서 지원하는 HTL(HTML Template Language)은 AEM에서 HTML에 대한 기본 및 권장 서버 측 템플릿 시스템입니다. 이전 AEM 버전에서 사용된 JSP(JavaServer 페이지) 대신

>[!NOTE]
>
>이 페이지에서 제공되는 대부분의 예를 실행하려면 [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl)라는 라이브 실행 환경을 사용할 수 있습니다.

## JSP {#htl-over-jsp} 를 통한 HTL

JSP에 비해 여러 이점이 있으므로 새 AEM 프로젝트에서 HTML 템플릿 언어를 사용하는 것이 좋습니다. 그러나 기존 프로젝트의 경우 마이그레이션은 향후 몇 년간 기존 JSP를 유지 관리하는 것보다 노력이 적은 경우에만 적절합니다.

그러나 HTL로 이동하는 것은 HTL로 작성된 구성 요소가 JSP 또는 ESP로 작성된 구성 요소와 호환되므로 꼭 사용하거나 사용하지 않을 필요가 없습니다. 즉, 기존 프로젝트에서 새 구성 요소에 대해 HTL을 사용하고 기존 구성 요소에 대해서는 JSP를 그대로 사용하면 문제가 없습니다.

동일한 구성 요소 내에서 HTL 파일을 JSP 및 ESP와 함께 사용할 수 있습니다. 다음 예제는 **라인 1**&#x200B;에 JSP 파일에서 HTL 파일을 포함하는 방법과 **라인 2**&#x200B;에 JSP 파일을 HTL 파일에서 포함할 수 있는 방법을 보여 줍니다.

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### FAQ{#frequently-asked-questions}

HTML 템플릿 언어를 시작하기 전에 JSP 및 HTL 항목과 관련된 몇 가지 질문에 먼저 답변해 보겠습니다.

**HTL에 JSP에 없는 제한 사항이 있습니까?** - HTL에는 JSP에 비해 HTL에서 수행할 수 있는 작업이 HTL에서도 가능해야 한다는 의미에서 HTL에 대한 제한이 없습니다. 그러나 HTL은 여러 측면에서 JSP보다 엄격한 디자인을 사용하므로 단일 JSP 파일에서 모두 구현할 수 있는 것은 Java 클래스나 JavaScript 파일로 분리하여 HTL에서 구현할 수 있어야 합니다. 그러나 일반적으로 논리와 마크업 간에 문제를 잘 구분하기 위해 이 방법이 필요합니다.

**HTL이 JSP 태그 라이브러리를 지원합니까?** - 아니요. 하지만 클라이언트 라이브러리  [로드 섹션에 ](getting-started.md#loading-client-libraries) 표시된 대로  [템플릿 및 ](block-statements.md#template-call) 호출문은 유사한 패턴을 제공합니다.

**AEM 프로젝트에서 HTL 기능을 확장할 수 있습니까?** - 아니요, 그럴 수 없습니다. HTL에는 로직을 재사용하기 위한 강력한 확장 메커니즘이 있습니다. [Use-API](getting-started.md#use-api-for-accessing-logic) - 및 마크업([template &amp; call](block-statements.md#template-call) 문)은 프로젝트 코드를 현대화하는 데 사용할 수 있습니다.

**JSP에 비해 HTL의 주요 장점은 무엇입니까?** - 보안 및 프로젝트 효율성이  [개요](overview.md)에 자세히 설명되어 있는 주요 이점

**JSP는 결국 사라질까요?** - 현재 날짜에는 이 라인에 대한 계획이 없습니다.

## HTL {#fundamental-concepts-of-htl}의 기본 개념

HTML 템플릿 언어에서는 표현식 언어를 사용하여 렌더링된 마크업에 컨텐츠를 삽입하고 HTML5 데이터 속성을 사용하여 마크업 블록(예: 조건 또는 반복)에 있는 문을 정의합니다. HTL이 Java Servlet에 컴파일되면, 표현식과 HTL 데이터 속성이 모두 완전히 서버측에서 평가되며, 결과 HTML에 아무 것도 표시되지 않습니다.

### 블록 및 표현식 {#blocks-and-expressions}

다음은 **`template.html`** 파일에 있는 것으로 포함될 수 있는 첫 번째 예입니다.

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

다음과 같은 두 가지 서로 다른 종류의 구문을 구분할 수 있습니다.

* **[블록 문](block-statements.md)**  - 조건부로  **&lt;h1>** 요소에서  [`data-sly-test`](block-statements.md#test) HTML5 데이터 속성이 사용됩니다. HTL에서는 모든 HTML 요소에 동작을 첨부할 수 있는 이러한 속성을 여러 개 제공하고 모든 속성에 `data-sly` 접두사가 추가됩니다.

* **[표현식 언어](expression-language.md)**  - HTL 표현식은 문자  `${` 및  `}`로 구분됩니다. 런타임 시 이러한 표현식이 평가되고 해당 값이 나가는 HTML 스트림에 삽입됩니다.

위에 링크된 두 페이지는 구문에 사용할 수 있는 기능의 세부 목록을 제공합니다.

### SLY 요소 {#the-sly-element}

HTL의 중심 개념은 기존 HTML 요소를 다시 사용하여 블록 문을 정의할 수 있는 가능성을 제공하는 것입니다. 따라서 명령문이 시작되고 끝나는 위치를 정의하기 위해 추가 구분 기호를 삽입할 필요가 없습니다. 정적 HTML을 작동 중인 동적 템플릿으로 변환하는 마크업의 이러한 눈에 띄지 않는 주석은 HTML 코드의 유효성을 훼손하지 않으므로 정적 파일로도 여전히 제대로 표시할 수 있는 이점이 있습니다.

그러나 블록 문을 삽입해야 하는 정확한 위치에 기존 요소가 없는 경우가 있습니다. 이러한 경우, 첨부된 블록 문을 실행하고 그에 따라 해당 내용을 표시하는 동안 출력에서 자동으로 제거될 특수 SLY 요소를 삽입할 수 있습니다.

따라서 다음 예는 다음과 같습니다.

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

는 다음 HTML과 같은 것을 출력하지만, **`jcr:title`** 및 **`jcr:description`** 속성이 모두 정의되어 있고 둘 다 비어 있지 않은 경우에만 출력합니다.

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

한 가지 기억해야 할 사항은 SLY 요소가 동적으로 만들 때 정적 HTML을 변경하지 않도록 언어에서 제공하는 값을 억제하므로 기존 요소에 주석을 달 수 없을 때만 SLY 요소를 사용하는 것입니다.

예를 들어 이전 예를 이미 DIV 요소 내에 래핑했다면, 추가된 SLY 요소는 남용될 수 있습니다.

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

그리고 DIV 요소에 다음과 같은 조건이 주석을 달 수 있었습니다.

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL 댓글 {#htl-comments}

다음 예제는 **라인 1**&#x200B;에 HTL 설명을, 그리고 **라인 2**&#x200B;에 HTML 주석을 보여 줍니다.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL 주석은 추가적인 JavaScript와 같은 구문을 사용하는 HTML 주석입니다. 전체 HTL 주석 및 내의 모든 항목은 프로세서에 의해 완전히 무시되고 출력에서 제거됩니다.

하지만 표준 HTML 주석의 컨텐츠는 전달되고 주석 내의 표현식은 평가됩니다.

HTML 주석에는 HTL 주석이 포함될 수 없으며 그 반대의 경우도 마찬가지입니다.

### 특수 컨텍스트 {#special-contexts}

HTL을 최대한 활용하려면 HTML 구문을 기반으로 HTL의 결과를 잘 이해하는 것이 중요합니다.

### 요소 및 속성 이름 {#element-and-attribute-names}

표현식은 HTML 텍스트 또는 속성 값에만 배치할 수 있지만 요소 이름이나 속성 이름 내에는 배치할 수 없거나 더 이상 유효한 HTML이 아닙니다. 요소 이름을 동적으로 설정하기 위해 [`data-sly-element`](block-statements.md#element) 문을 원하는 요소에서 사용하고, 속성 이름을 동적으로 설정할 수 있으며, 한 번에 여러 속성을 설정하더라도 [`data-sly-attribute`](block-statements.md#attribute) 문을 사용할 수 있습니다.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### 블록 문이 없는 컨텍스트 {#contexts-without-block-statements}

HTL에서는 데이터 속성을 사용하여 블록 문을 정의하므로 다음 컨텍스트 내에서 이러한 블록 문을 정의할 수 없으며, 여기에서 표현식만 사용할 수 있습니다.

* HTML 댓글
* 스크립트 요소
* 스타일 요소

이유는 이러한 컨텍스트의 컨텐츠가 HTML이 아닌 텍스트이고 포함된 HTML 요소는 단순 문자 데이터로 간주되기 때문입니다. 따라서 실제 HTML 요소가 없으면 **`data-sly`** 속성을 실행할 수도 없습니다.

이는 큰 제한처럼 들릴 수 있지만, HTML 템플릿 언어가 HTML이 아닌 출력을 생성하는 데 악용되어서는 안 되므로 원하는 것입니다. 아래의 [논리 액세스에 Use-API](getting-started.md#use-api-for-accessing-logic) 섹션에서는 이러한 컨텍스트에 대한 복잡한 출력을 준비하는 데 필요한 경우 사용할 수 있는 템플릿에서 추가 로직을 호출하는 방법을 소개합니다. 예를 들어, 백 엔드에서 프런트 엔드 스크립트로 데이터를 전송하는 쉬운 방법은 구성 요소의 논리를 사용하여 JSON 문자열을 생성하는 것입니다. 이 로직은 간단한 HTL 표현식을 사용하여 데이터 속성에 배치할 수 있습니다.

다음 예에서는 HTML 주석에 대한 동작을 보여주지만 스크립트나 스타일 요소에서 동일한 동작을 관찰합니다.

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

는 다음 HTML과 같은 것을 출력합니다.

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### 명시적 컨텍스트 필수 {#explicit-contexts-required}

아래의 [자동 컨텍스트 인식 이스케이프](getting-started.md#automatic-context-aware-escaping) 섹션에 설명된 대로, HTL의 한 가지 목적은 모든 표현식에 컨텍스트 인식 이스케이프를 자동으로 적용하여 교차 사이트 스크립팅(XSS) 취약점을 도입하는 위험을 줄이는 것입니다. HTL은 HTML 마크업 내에 배치된 표현식의 컨텍스트를 자동으로 탐지할 수 있지만 인라인 JavaScript 또는 CSS의 구문을 분석하지 않으므로 개발자를 사용하여 그러한 표현식에 정확히 어떤 컨텍스트를 적용해야 하는지 명시적으로 지정합니다.

따라서 XSS 취약점이 올바른 이스케이프를 적용하지 않으므로, HTL에서는 컨텍스트가 선언되지 않은 경우 스크립트 및 스타일 컨텍스트에 있는 모든 표현식의 출력을 제거합니다.

다음은 스크립트 및 스타일 내에 배치된 표현식의 컨텍스트를 설정하는 방법의 예입니다.

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

이스케이프를 제어하는 방법에 대한 자세한 내용은 [표현식 언어 표시 컨텍스트](expression-language.md#display-context) 섹션을 참조하십시오.

### 특수 컨텍스트의 리프팅 제한 사항 {#lifting-limitations-of-special-contexts}

스크립트, 스타일 및 주석 컨텍스트의 제한을 무시해야 하는 특별한 경우 해당 컨텐츠를 별도의 HTL 파일에서 분리할 수 있습니다. 자체 파일에 있는 모든 것은 HTL에서 포함되었을 수 있는 제한 컨텍스트를 잊어버리고 일반 HTML 조각으로 해석됩니다.

예를 보려면 [클라이언트측 템플릿으로 작업](getting-started.md#working-with-client-side-templates) 섹션을 참조하십시오.

>[!CAUTION]
>
>이 기법에서는 XSS(교차 사이트 스크립팅) 취약점이 발생할 수 있으며 이 취약점이 사용되는 경우 보안 측면을 신중하게 조사해야 합니다. 일반적으로 이 방법을 사용하는 것보다 동일한 작업을 구현하는 더 좋은 방법이 있습니다.

## HTL {#general-capabilities-of-htl} 의 일반 기능

이 섹션에서는 HTML 템플릿 언어의 일반 기능을 빠르게 안내합니다.

### 논리 액세스를 위한 Use-API {#use-api-for-accessing-logic}

다음 예를 생각해 보십시오.

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

그리고 다음 `logic.js` 서버측에서 실행된 JavaScript 파일이 그 옆에 배치됩니다.

```javascript
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

HTML 템플릿 언어에서는 마크업 내에서 코드를 혼합할 수 없으므로 Use-API 확장 메커니즘 대신 템플릿에서 코드를 쉽게 실행할 수 있습니다.

위의 예에서는 서버 측에서 실행되는 JavaScript를 사용하여 제목을 10자로 단축하지만, 정규화된 Java 클래스 이름을 제공하여 Java 코드를 사용하여 동일하게 수행할 수도 있습니다. 일반적으로 비즈니스 로직은 Java로 작성하는 것이 낫지만, 구성 요소에서 Java API가 제공하는 것과 관련된 보기 특정 변경 사항이 필요한 경우 일부 서버측 실행 JavaScript를 사용하여 이를 수행하는 것이 편리할 수 있습니다.

다음 섹션의 세부 사항:

* [`data-sly-use` 문](block-statements.md#use)의 섹션에서는 해당 문으로 수행할 수 있는 모든 작업에 대해 설명합니다.
* [Use-API 페이지](use-api.md)는 Java나 JavaScript에서 논리를 작성하는 데 도움이 되는 몇 가지 정보를 제공합니다.
* 로직을 작성하는 방법에 대해 자세히 알려면 [JavaScript Use-API](use-api-javascript.md) 및 [Java Use-API](use-api-java.md) 페이지가 도움이 됩니다.

### 자동 컨텍스트 인식 이스케이프 {#automatic-context-aware-escaping}

다음 예를 생각해 보십시오.

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

대부분의 템플릿 언어에서는 모든 변수가 자동으로 HTML에서 이스케이프되는 경우에도 `href` 속성이 여전히 특별히 URL-이스케이프되어야 하므로 이 예제는 XSS(교차 사이트 스크립팅) 취약성이 발생할 수 있습니다. 이 생략은 가장 일반적인 오류 중 하나인데, 왜냐하면 그것은 매우 쉽게 잊혀질 수 있고, 자동화된 방식으로 발견하기가 어렵기 때문입니다.

이를 지원하기 위해 HTML 템플릿 언어에서는 각 변수가 배치된 컨텍스트에 따라 자동으로 이스케이프됩니다. 이는 HTL이 HTML의 구문을 이해하므로 가능합니다.

다음 `logic.js` 파일을 가정할 경우:

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

그러면 초기 예제가 다음 출력을 생성합니다.

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

HTL에서는 `href` 및 `src` 속성을 URI 컨텍스트에 대해 이스케이프해야 한다는 것을 알기 때문에 두 속성이 어떻게 다르게 이스케이프되었는지 확인합니다. 또한 URI가 **`javascript:`**&#x200B;으로 시작하는 경우 컨텍스트가 명시적으로 다른 것으로 변경되지 않은 한 속성이 완전히 제거되었을 수 있습니다.

이스케이프를 제어하는 방법에 대한 자세한 내용은 [표현식 언어 표시 컨텍스트](expression-language.md#display-context) 섹션을 참조하십시오.

### 빈 속성 {#automatic-removal-of-empty-attributes} 자동 제거

다음 예를 생각해 보십시오.

```xml
<p class="${properties.class}">some text</p>
```

`class` 속성 값이 비어 있으면 HTML 템플릿 언어가 출력에서 전체 `class` 속성을 자동으로 제거합니다.

다시 말하지만, HTL은 HTML 구문을 이해하고 따라서 값이 비어 있지 않은 경우에만 동적 값이 있는 속성을 조건부로 표시할 수 있으므로 이 작업이 가능합니다. 따라서 속성 주위에 조건 블록을 추가하지 않으면 마크업이 잘못되어 읽을 수 없게 되므로 매우 편리합니다.

또한 표현식에 배치된 변수의 유형은 다음과 같습니다.

* **String:**
   * **비어 있지 않음:** 문자열을 속성 값으로 설정합니다.
   * **empty:**  속성을 완전히 제거합니다.

* **숫자:**  값을 속성 값으로 설정합니다.

* **부울:**
   * **true:**  값이 없는 속성(부울 HTML 속성으로)을 표시합니다
   * **false:** 속성을 모두 제거합니다.

다음은 부울 표현식에서 부울 HTML 속성을 제어하는 방법의 예입니다.

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

속성을 설정하는 경우 [`data-sly-attribute`](block-statements.md#attribute) 문도 유용할 수 있습니다.

## HTL {#common-patterns-with-htl}이 있는 일반적인 패턴

이 섹션에서는 몇 가지 일반적인 시나리오와 HTML 템플릿 언어로 가장 잘 해결하는 방법을 소개합니다.

### 클라이언트 라이브러리 로드 중 {#loading-client-libraries}

HTL에서 클라이언트 라이브러리는 [`data-sly-use`](block-statements.md#use)을 통해 액세스할 수 있는 AEM에서 제공하는 도우미 템플릿을 통해 로드됩니다. 이 파일에는 [`data-sly-call`](block-statements.md#template-call)을 통해 호출할 수 있는 세 개의 템플릿을 사용할 수 있습니다.

* **`css`** - 참조된 클라이언트 라이브러리의 CSS 파일만 로드합니다.
* **`js`** - 참조된 클라이언트 라이브러리의 JavaScript 파일만 로드합니다.
* **`all`** - 참조된 클라이언트 라이브러리의 모든 파일(CSS와 JavaScript 모두)을 로드합니다.

각 도우미 템플릿에는 원하는 클라이언트 라이브러리를 참조하기 위한 **`categories`** 옵션이 필요합니다. 이 옵션은 문자열 값의 배열 또는 쉼표로 구분된 값 목록을 포함하는 문자열일 수 있습니다.

다음은 두 가지 간단한 예입니다.

### 여러 클라이언트 라이브러리를 한 번에 완전히 로드 {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### 페이지의 다른 섹션에서 클라이언트 라이브러리 참조 {#referencing-a-client-library-in-different-sections-of-a-page}

```xml
<!doctype html>
<html data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <head>
        <!-- HTML meta-data -->
        <sly data-sly-call="${clientlib.css @ categories='myCategory'}"/>
    </head>
    <body>
        <!-- page content -->
        <sly data-sly-call="${clientlib.js @ categories='myCategory'}"/>
    </body>
</html>
```

위의 두 번째 예에서 HTML **`head`** 및 **`body`** 요소가 다른 파일에 배치되는 경우 **`clientlib.html`** 템플릿을 필요한 각 파일에 로드해야 합니다.

[템플릿 및 호출](block-statements.md#template-call) 문의 섹션에서는 이러한 템플릿을 선언 및 호출하는 방법에 대한 자세한 내용을 제공합니다.

### 클라이언트에 데이터 전달 {#passing-data-to-the-client}

일반적으로 데이터를 클라이언트에 전달하는 가장 쉬우면서도 HTL을 사용하면 데이터 속성을 사용하는 것이 좋습니다.

다음 예는 논리(Java로도 쓸 수 있음)를 사용하여 클라이언트에 전달될 개체를 JSON으로 매우 간편하게 직렬화할 수 있는 방법을 보여줍니다. 그러면 데이터 속성에 매우 쉽게 배치할 수 있습니다.

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```javascript
/* logic.js file: */
use(function () {
    var myData = {
        str: "foo",
        arr: [1, 2, 3]
    };

    return {
        json: JSON.stringify(myData)
    };
});
```

여기에서 클라이언트측 JavaScript가 어떻게 해당 속성에 액세스하고 JSON을 다시 구문 분석할 수 있는지 쉽게 상상할 수 있습니다. 예를 들어 클라이언트 라이브러리에 배치할 해당 JavaScript입니다.

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### 클라이언트측 템플릿 작업 {#working-with-client-side-templates}

[특수 컨텍스트의 리프팅 제한 사항](getting-started.md#lifting-limitations-of-special-contexts) 섹션에 설명된 기술을 사용할 수 있는 특별한 경우에는 **script** 요소 내에 있는 클라이언트측 템플릿(예: Handlebars)을 작성하는 것입니다. 이 기술을 이 경우에 안전하게 사용할 수 있는 이유는 **script** 요소가 가정된 JavaScript가 아닌 추가 HTML 요소를 포함하지 않기 때문입니다. 작동 방법의 예는 다음과 같습니다.

```xml
<!--/* template.html file: */-->
<script id="entry-section" type="text/template"
    data-sly-include="entry-section.html"></script>

<!--/* entry-section.html file: */-->
<div class="entry-section">
    <h2 data-sly-test="${properties.entrySectionTitle}">
        ${properties.entrySectionTitle}
    </h2>
    {{#each entry}}<h3><a href="{{link}}">{{title}}</a></h3>{{/each}}
</div>
```

위에 표시된 대로 **`script`** 요소에 포함될 마크업에는 HTL 블록 문이 포함될 수 있으며 Handlebars 템플릿의 컨텐츠가 자체 파일에서 분리되었기 때문에 표현식이 명시적 컨텍스트를 제공할 필요가 없습니다. 또한 이 예는 **`h2`** 요소와 같이 서버측에서 실행되는 HTL을 Handlebars(**`h3`** 요소에 표시됨)와 같은 클라이언트측 실행 템플릿 언어와 혼합할 수 있는 방법을 보여줍니다.

그러나 완전히 최신 기법은 템플릿의 콘텐츠를 별도의 파일로 분리하지 않아도 되므로 HTML **`template`** 요소를 대신 사용하는 것입니다.

**다음 참조:**

* [표현식 언어](expression-language.md)  - HTL 표현식 내에서 수행할 수 있는 작업을 자세히 알아보십시오.
* [블록 문](block-statements.md)  - HTL에서 사용할 수 있는 모든 블록 문과 이를 사용하는 방법을 알아봅니다.
