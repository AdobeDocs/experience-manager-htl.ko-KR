---
title: HTL 시작하기
description: HTML에 대한 AEM의 기본 및 권장 서버측 템플릿 시스템으로서 AEM에서 지원하는 HTL은 JSP를 대신합니다.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '2471'
ht-degree: 100%

---

# HTL 시작하기 {#getting-started-with-htl}

Adobe Experience Manager(AEM)에서 지원하는 HTML 템플릿 언어(HTL)는 HTML에 대한 AEM의 기본 및 권장 서버측 템플릿 시스템입니다. 이전 버전의 AEM에서 사용된 JSP(JavaServer Pages)를 대신합니다.

>[!NOTE]
>
>이 페이지에 제공된 대부분의 예제를 실행하기 위해 [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl)라는 라이브 실행 환경을 사용할 수 있습니다.

## JSP 대신 HTL {#htl-over-jsp}

JSP에 비해 여러 이점을 제공하므로 새 AEM 프로젝트에 HTML 템플릿 언어를 사용하는 것이 좋습니다. 그러나 기존 프로젝트의 경우, 향후 몇 년 동안 기존 JSP를 유지 관리하는 것보다 노력이 덜 들 것으로 추정되는 경우에만 마이그레이션이 의미가 있습니다.

하지만 HTL로 작성된 구성 요소는 JSP 또는 ESP로 작성된 구성 요소와 호환되기 때문에 HTL로의 전환이 반드시 양자택일의 문제는 아닙니다. 기존 프로젝트가 문제 없이 새 구성 요소에 대해서는 HTL을 사용하고 기존 구성 요소에 대해서는 JSP를 유지할 수 있다는 의미입니다.

동일한 구성 요소 내에서도 HTL 파일을 JSP 및 ESP와 함께 사용할 수 있습니다. 다음 예제의 **라인 1**&#x200B;에서는 JSP 파일에서 HTL 파일을 포함하는 방법을, **라인 2**&#x200B;에서는 HTL 파일에서 JSP 파일을 포함하는 방법을 보여 줍니다.

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### FAQ{#frequently-asked-questions}

HTML 템플릿 언어를 시작하기 전에 JSP와 HTL 비교 주제와 관련된 몇 가지 질문에 대한 답변부터 시작하겠습니다.

**HTL에는 JSP에 없는 제한이 있습니까?** - JSP로 할 수 있는 일을 HTL로도 달성할 수 있어야 한다는 점에서 JSP와 비교하여 HTL에 있는 제한은 사실 없습니다. 하지만 HTL은 여러 측면에서 JSP보다 더 엄격하게 설계되었으며 단일 JSP 파일에서 모두 달성할 수 있는 것은 HTL에서 달성할 수 있도록 Java 클래스 또는 JavaScript 파일로 분리해야 할 수도 있습니다. 그러나 일반적으로는 논리와 마크업 사이의 문제를 잘 분리하기 위해 바람직한 것입니다.

**HTL은 JSP 태그 라이브러리를 지원합니까?** - 아니요, 하지만 [클라이언트 라이브러리 로드](getting-started.md#loading-client-libraries) 섹션에서 볼 수 있듯이 [템플릿 및 호출](block-statements.md#template-call) 문은 유사한 패턴을 제공합니다.

**AEM 프로젝트에서 HTL 기능을 확장할 수 있습니까?** - 아니요, 할 수 없습니다. HTL은 논리 재사용을 위한 강력한 확장 메커니즘([Use-API](getting-started.md#use-api-for-accessing-logic))과 프로젝트 코드를 모듈화하는 데 사용할 수 있는 마크업([템플릿 및 호출](block-statements.md#template-call) 문)을 제공합니다.

**JSP에 비교하여 HTL의 주요 이점은 무엇입니까?** - 보안 및 프로젝트 효율성이 주요 이점이며 [개요](overview.md)에 자세히 설명되어 있습니다.

**JSP는 결국 없어집니까?** - 현시점에는 이 제품군에 대한 계획이 없습니다.

## HTL의 기본 개념 {#fundamental-concepts-of-htl}

HTML 템플릿 언어는 표현식 언어를 사용하여 콘텐츠 조각을 렌더링된 마크업에 삽입하고 HTML5 데이터 속성을 사용하여 마크업 블록(예: 조건 또는 반복)에 대한 명령문을 정의합니다. HTL이 Java Servlet으로 컴파일되면 표현식과 HTL 데이터 속성은 모두 전적으로 서버측에서 평가되며 결과 HTML에는 아무것도 표시되지 않습니다.

### 블록 및 표현식 {#blocks-and-expressions}

다음은 **`template.html`** 파일에 있는 그대로 포함될 수 있는 첫 번째 예입니다.

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

두 가지 다른 종류의 구문을 구별할 수 있습니다.

* **[블록 문](block-statements.md)** - **&lt;h1>** 요소를 조건부로 표시하기 위해 [`data-sly-test`](block-statements.md#test) HTML5 데이터 속성이 사용됩니다. HTL은 이러한 여러 속성을 제공하여 모든 HTML 요소에 동작을 첨부할 수 있으며 모두 `data-sly` 접두사가 붙습니다.

* **[표현식 언어](expression-language.md)** - HTL 표현식은 `${` 및 `}` 문자로 구분됩니다. 런타임에 이러한 표현식이 평가되고 해당 값이 송신 HTML 스트림에 삽입됩니다.

위에 링크된 두 페이지는 구문에 사용할 수 있는 기능의 상세 목록을 제공합니다.

### SLY 요소 {#the-sly-element}

HTL의 핵심 개념은 블록 문을 정의하기 위해 기존 HTML 요소를 재사용할 수 있는 가능성을 제시하는 것입니다. 이렇게 하면 명령문이 시작하고 끝나는 위치를 정의하기 위해 추가 구분 기호를 삽입할 필요가 없습니다. 정적 HTML을 작동하는 동적 템플릿으로 변환하는 마크업의 이러한 비간섭적 주석은 HTML 코드의 유효성을 손상시키지 않는 이점을 제공합니다. 따라서 정적 파일로도 올바르게 표시됩니다.

단, 블록 문이 삽입되어야 하는 정확한 위치에 기존 요소가 없는 경우도 있을 수 있습니다. 이러한 경우에는 출력에서 자동으로 제거되는 특수 SLY 요소를 삽입하는 동시에 첨부된 블록 문을 실행하고 그에 따라 콘텐츠를 표시할 수 있습니다.

그로므로 다음 예를 참조하십시오.

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

다음 HTML과 같은 것을 출력하지만 **`jcr:title`** 및 **`jcr:description`** 속성이 모두 정의되어 있고 둘 다 비어 있지 않은 경우에만:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

유념해야 할 한 가지는 기존 요소에 블록 문으로 주석을 추가할 수 없는 경우에만 SLY 요소를 사용하는 것입니다.

예를 들어 이전 예제가 이미 DIV 요소 내부에 래핑된 경우 추가된 SLY 요소는 남용이 됩니다.

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

그리고 DIV 요소는 다음 조건으로 주석을 달 수 있습니다.

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL 주석 {#htl-comments}

다음 예제의 **라인 1**&#x200B;에서는 HTL 주석을 **라인 2**&#x200B;에서는 HTML 주석을 보여 줍니다.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL 주석은 JavaScript와 유사한 추가 구문이 있는 HTML 주석입니다. 전체 HTL 주석과 그 안의 모든 콘텐츠는 프로세서에서 완전히 무시되고 출력에서 제거됩니다.

그러나 표준 HTML 주석의 콘텐츠는 전달되고 주석 내의 표현식이 평가됩니다.

HTML 주석은 HTL 주석을 포함할 수 없으며 그 반대의 경우도 마찬가지입니다.

### 특수 컨텍스트 {#special-contexts}

HTL을 최대한 활용하려면 HTML 구문을 기반으로 하는 HTL의 결과를 잘 이해하는 것이 중요합니다.

### 요소 및 속성 이름 {#element-and-attribute-names}

표현식은 HTML 텍스트 또는 속성 값에만 배치할 수 있지만 요소 이름이나 속성 이름에는 배치할 수 없습니다. 그렇지 않으면 더 이상 유효한 HTML이 아닙니다. 요소 이름을 동적으로 설정하려면 원하는 요소에 [`data-sly-element`](block-statements.md#element) 문을 사용할 수 있으며, 속성 이름을 동적으로 설정하려면 여러 속성을 한 번에 설정하는 경우에도 [`data-sly-attribute`](block-statements.md#attribute) 문을 사용할 수 있습니다.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### 블록 문이 없는 컨텍스트 {#contexts-without-block-statements}

HTL은 데이터 속성을 사용하여 블록 문을 정의하기 때문에 다음 컨텍스트 내에서 이러한 블록 문을 정의할 수 없으며 표현식만 사용할 수 있습니다.

* HTML 주석
* 스크립트 요소
* 스타일 요소

그 이유는 이러한 컨텍스트의 콘텐츠가 HTML이 아닌 텍스트이고 포함된 HTML 요소가 단순 문자 데이터로 간주되기 때문입니다. 따라서 실제 HTML 요소가 없으면 **`data-sly`** 속성도 실행할 수 없습니다.

이것은 큰 제한처럼 들릴 수 있지만 HTML 템플릿 언어가 HTML이 아닌 출력을 생성하기 위해 남용되어서는 안 되므로 바람직한 것입니다. 아래 [액세스 논리를 위한 Use-API](getting-started.md#use-api-for-accessing-logic) 섹션에서는 템플릿에서 추가 논리를 호출하는 방법을 소개합니다. 이러한 컨텍스트에 대한 복잡한 출력을 준비하는 데 필요한 경우 이 방법을 사용할 수 있습니다. 예를 들어 백엔드에서 프론트엔드 스크립트로 데이터를 보내는 쉬운 방법은 간단한 HTL 표현식으로 데이터 속성에 배치할 수 있는 JSON 문자열을 생성하는 구성 요소의 논리를 갖는 것입니다.

다음 예제는 HTML 주석의 동작을 보여 주지만 스크립트 또는 스타일 요소에서 동일한 동작이 관찰됩니다.

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

다음 HTML과 같은 것을 출력합니다.

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### 명시적 컨텍스트 필요 {#explicit-contexts-required}

아래 [자동 컨텍스트 인식 이스케이프](getting-started.md#automatic-context-aware-escaping) 섹션에 설명된 대로 HTL의 한 가지 목표는 모든 표현식에 컨텍스트 인식 이스케이프를 자동으로 적용하여 XSS(크로스 사이트 스크립팅) 취약성 유발의 위험을 줄이는 것입니다. HTL은 HTML 마크업 내부에 배치된 표현식의 컨텍스트를 자동으로 감지할 수 있지만 인라인 JavaScript 또는 CSS의 구문을 분석하지 않으므로 개발자가 이러한 표현식에 적용해야 하는 정확한 컨텍스트를 명시적으로 지정해야 합니다.

올바른 이스케이프 결과를 적용하지 않으면 XSS 취약성이 발생하므로 HTL은 컨텍스트가 선언되지 않은 경우 스크립트 및 스타일 컨텍스트에 있는 모든 표현식의 출력을 제거합니다.

다음은 스크립트 및 스타일 내부에 배치된 표현식에 대한 컨텍스트를 설정하는 방법의 예입니다.

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

이스케이프 제어 방법에 대한 자세한 내용은 [표현식 언어 표시 컨텍스트](expression-language.md#display-context) 섹션을 참조하십시오.

### 특수 컨텍스트의 한계 해제 {#lifting-limitations-of-special-contexts}

스크립트, 스타일 및 주석 컨텍스트의 제한을 피해야 하는 특별한 경우에는 해당 콘텐츠를 별도의 HTL 파일로 격리할 수 있습니다. 자체 파일에 있는 모든 콘텐츠는 HTL에 의해 일반 HTML 조각으로 해석되어, 제한적인 컨텍스트가 포함되었을 수 있는 콘텐츠에서 제한을 무시합니다.

예를 보려면 더 아래에 있는 [클라이언트측 템플릿 작업](getting-started.md#working-with-client-side-templates) 섹션을 참조하십시오.

>[!CAUTION]
>
>이 기술은 XSS(크로스 사이트 스크립팅) 취약성을 유발할 수 있으며 이를 사용하는 경우 보안 측면을 주의 깊게 검토해야 합니다. 일반적으로 이 방법에 의존하는 것보다 동일한 것을 구현하는 더 좋은 방법이 있습니다.

## HTL의 일반 기능 {#general-capabilities-of-htl}

이 섹션에서는 HTML 템플릿 언어의 일반적인 기능을 빠르게 살펴봅니다.

### 액세스 논리를 위한 Use-API {#use-api-for-accessing-logic}

다음 예를 생각해 보십시오.

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

옆에 배치된 다음 `logic.js` 서버측 실행 JavaScript 파일:

```javascript
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

HTML 템플릿 언어는 마크업 내에서 코드를 혼합하는 것을 허용하지 않으므로 대신 템플릿에서 코드를 쉽게 실행할 수 있는 Use-API 확장 메커니즘을 제공합니다.

위의 예는 서버측 실행 JavaScript를 사용하여 제목을 10자로 줄이지만 완전한 Java 클래스 이름을 제공함으로써 Java 코드를 사용하여 동일한 작업을 수행할 수도 있습니다. 일반적으로 비즈니스 논리는 Java로 빌드되어야 하지만 Java API가 제공하는 논리에서 일부 보기별 변경이 구성 요소에 필요한 경우 이를 수행하기 위해 일부 서버측 실행 JavaScript를 사용하는 것이 편리할 수 있습니다.

다음 섹션에서 이에 대해 자세히 알아보십시오.

* [`data-sly-use` 명령문](block-statements.md#use) 섹션은 해당 명령문으로 할 수 있는 모든 것을 설명합니다.
* [Use-API 페이지](use-api.md)는 논리를 Java로 작성할지 JavaScript로 작성할지 선택하는 데 도움이 되는 몇 가지 정보를 제공합니다.
* 논리 작성 방법을 자세히 알아보려면 [JavaScript Use-API](use-api-javascript.md) 및 [Java Use-API](use-api-java.md) 페이지가 도움이 될 것입니다.

### 자동 컨텍스트 인식 이스케이프 {#automatic-context-aware-escaping}

다음 예를 생각해 보십시오.

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

대부분의 템플릿 언어에서 이 예제는 잠재적으로 XSS(크로스 사이트 스크립팅) 취약점을 생성할 수 있습니다. 모든 변수가 자동으로 HTML 이스케이프되는 경우에도 `href` 속성은 여전히 특정 URL로 이스케이프되어야 하기 때문입니다. 이런 생략은 매우 쉽게 무시될 수 있고 자동화된 방식으로 발견하기 어렵기 때문에 가장 일반적인 오류 중 하나입니다.

이를 지원하기 위해 HTML 템플릿 언어는 변수가 배치된 컨텍스트에 따라 각 변수를 자동으로 이스케이프합니다. HTL이 HTML의 구문을 이해한다는 사실 덕분에 가능한 것입니다.

다음 `logic.js` 파일을 가정합니다.

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

그러면 초기 예제는 다음과 같은 결과를 출력합니다.

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

HTL은 URI 컨텍스트에 대해 `href` 및 `src` 속성을 이스케이프해야 한다는 것을 알고 있으므로 두 속성이 어떻게 다르게 이스케이프되었는지 확인합니다. 또한 URI가 **`javascript:`**&#x200B;로 시작했다면 컨텍스트가 명시적으로 다른 것으로 변경된 경우 외에는 속성이 완전히 제거되었을 것입니다.

이스케이프 제어 방법에 대한 자세한 내용은 [표현식 언어 표시 컨텍스트](expression-language.md#display-context) 섹션을 참조하십시오.

### 빈 속성 자동 제거 {#automatic-removal-of-empty-attributes}

다음 예를 생각해 보십시오.

```xml
<p class="${properties.class}">some text</p>
```

`class` 속성 값이 비어 있으면 HTML 템플릿 언어는 자동으로 전체 `class` 속성을 출력에서 제거합니다.

다시 말하지만, HTL이 HTML 구문을 이해하고 따라서 값이 비어 있지 않은 경우에만 동적 값이 있는 속성을 조건부로 표시할 수 있기 때문에 가능한 것입니다. 마크업을 유효하지 않고 읽을 수 없게 만들 수 있는 속성에 조건 블록을 추가하는 것을 방지하기 때문에 매우 편리합니다.

또한 표현식에 배치된 변수의 유형이 중요합니다.

* **String:**
   * **not empty:** 문자열을 속성 값으로 설정합니다.
   * **empty:** 속성을 모두 제거합니다.

* **숫자:** 값을 속성 값으로 설정합니다.

* **부울:**
   * **true:** 값이 없는 속성을 표시합니다(부울 HTML 속성으로).
   * **false:** 속성을 모두 제거합니다.

다음은 부울 표현식을 사용하여 부울 HTML 속성을 제어하는 방법의 예입니다.

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

속성 설정의 경우[`data-sly-attribute`](block-statements.md#attribute) 문도 유용할 수 있습니다.

## HTL의 일반적인 패턴 {#common-patterns-with-htl}

이 섹션에서는 몇 가지 일반적인 시나리오와 HTML 템플릿 언어로 이를 가장 잘 해결하는 방법을 소개합니다.

### 클라이언트 라이브러리 로드 {#loading-client-libraries}

HTL에서 클라이언트 라이브러리는 [`data-sly-use`](block-statements.md#use)를 통해 액세스할 수 있는 AEM에서 제공하는 도우미 템플릿을 통해 로드됩니다. 이 파일에는 [`data-sly-call`](block-statements.md#template-call)을 통해 호출할 수 있는 세 가지 템플릿이 있습니다.

* **`css`** - 참조된 클라이언트 라이브러리의 CSS 파일만 로드합니다.
* **`js`** - 참조된 클라이언트 라이브러리의 JavaScript 파일만 로드합니다.
* **`all`** - 참조된 클라이언트 라이브러리의 모든 파일을 로드합니다(CSS 및 JavaScript 모두).

각 도우미 템플릿에는 원하는 클라이언트 라이브러리를 참조하기 위한 **`categories`** 옵션이 필요합니다. 해당 옵션은 문자열 값의 배열이거나 쉼표로 구분된 값 목록을 포함하는 문자열일 수 있습니다.

여기 몇 가지 짧은 예가 있습니다.

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

위의 두 번째 예에서 HTML **`head`** 및 **`body`** 요소가 다른 파일에 배치된 경우 **`clientlib.html`** 템플릿은 필요한 각 파일에 로드되어야 합니다.

[템플릿 및 호출](block-statements.md#template-call) 문 섹션은 이러한 템플릿을 선언하고 호출하는 방법에 대한 자세한 내용을 제공합니다.

### 클라이언트에 데이터 전달 {#passing-data-to-the-client}

일반적으로(특히, HTL의 경우) 데이터를 클라이언트에 전달하는 가장 훌륭하고 우아한 방법은 데이터 속성을 사용하는 것입니다.

다음 예제는 논리(Java로도 작성 가능)를 사용하여 클라이언트에 전달할 개체를 JSON으로 매우 편리하게 직렬화하는 방법을 보여 줍니다. 그러면 개체는 데이터 속성에 매우 쉽게 배치될 수 있습니다.

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

거기에서 클라이언트측 JavaScript가 해당 속성에 액세스하고 JSON을 다시 구문 분석하는 방법을 쉽게 상상할 수 있습니다. 예를 들어 클라이언트 라이브러리에 배치할 해당 JavaScript는 다음과 같습니다.

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### 클라이언트측 템플릿 작업 {#working-with-client-side-templates}

[특수 컨텍스트의 제한 해제](getting-started.md#lifting-limitations-of-special-contexts) 섹션에서 설명된 기술을 합법적으로 사용할 수 있는 한 가지 특별한 경우는 **스크립트** 요소 내에 있는 클라이언트측 템플릿(예: 핸들바)을 작성하는 것입니다. 이 경우 이 기술을 안전하게 사용할 수 있는 이유는 **script** 요소에 가정한 대로 JavaScript가 포함되지 않고 추가 HTML 요소가 포함되기 때문입니다. 다음은 작동 방식의 예입니다.

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

위에서 보여진 것처럼 **`script`** 요소에 포함될 마크업은 HTL 블록 문을 포함할 수 있으며 핸들바 템플릿의 콘텐츠가 자체 파일에서 격리되었기 때문에 표현식은 명시적 컨텍스트를 제공할 필요가 없습니다. 또한 이 예에서는 서버측 실행 HTL(예: **`h2`** 요소에서와 같이)이 핸들바(**`h3`** 요소에 표시된)와 같은 클라이언트측 실행 템플릿 언어와 어떻게 혼합될 수 있는지 보여 줍니다.

그러나 보다 현대적인 기술은 HTML **`template`** 요소를 대신 사용하는 것입니다. 그러면 템플릿의 콘텐츠를 별도의 파일로 분리할 필요가 없다는 이점이 있기 때문입니다.

**다음 참조:**

* [표현식 언어](expression-language.md) - HTL 표현식 내에서 수행할 수 있는 작업에 대해 자세히 알아봅니다.
* [블록 문](block-statements.md) - HTL에서 사용할 수 있는 모든 블록 문과 사용 방법을 알아봅니다.
