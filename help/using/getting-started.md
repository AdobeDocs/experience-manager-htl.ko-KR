---
title: HTL 시작하기
seo-title: HTL 시작하기
description: AEM에서 지원하는 HTL은 AEM의 HTML에 대해 선호되고 권장되는 서버측 템플릿 시스템으로 JSP를 대체합니다.
seo-description: Adobe Experience Manager에서 지원하는 HTML 템플릿 언어 - HTL은 AEM의 HTML에 대해 선호되고 권장되는 서버측 템플릿 시스템으로 사용됩니다.
uuid: 4a7d6748-8cdf-4280-a85d-6c5319abf487
content-type: 참조
topic-tags: 소개
discoiquuid: 3bf2ca75-0d68-489d-bd1c-1d4fd730c61a
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 6de5ed20e4463c0c2e804e24cb853336229a7c1f

---


# HTL 시작하기 {#getting-started-with-htl}

AEM(Adobe Experience Manager)에서 지원하는 HTML 템플릿 언어(HTL)는 JSP(JavaServer Pages)를 AEM의 HTML에 대해 선호되고 권장되는 서버측 템플릿 시스템으로 대체합니다.

>[!NOTE]
>
>이 페이지에서 제공되는 대부분의 예제를 실행하려면 평가 인쇄 [루프라는 라이브 실행 환경을](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) 사용할 수 있습니다.
>
>AEM 커뮤니티에서 HTL 사용과 관련된 일련의 [아티클, 비디오 및 웨비나를](related-community-articles.md) 생성했습니다.

## HTL over JSP {#htl-over-jsp}

새 AEM 프로젝트에서 HTML 템플릿 언어를 사용하는 것이 좋습니다. JSP에 비해 여러 이점이 있습니다. 그러나 기존 프로젝트의 경우, 마이그레이션은 향후 몇 년간 기존 JSP를 유지 관리하는 것보다 노력이 적은 경우에만 의미가 있습니다.

HTL로 작성된 구성 요소는 JSP 또는 ESP로 작성된 구성 요소와 호환되기 때문에 HTL로 이동하는 것이 반드시 모든 것을 선택하는 것은 아닙니다. 즉, 기존 프로젝트는 새 구성 요소에 HTL을 사용하고 기존 구성 요소에 대해서는 JSP를 유지할 수 있습니다.

동일한 구성 요소 내에서도 HTL 파일을 JSP 및 ESP와 함께 사용할 수 있습니다. 다음 예는 **줄 1** 및 **줄 2** 시 HTL 파일에서 HTL 파일을 포함하는 방법과 HTL 파일에서 JSP 파일을 포함할 수 있는 방법을 보여줍니다.

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### FAQ{#frequently-asked-questions}

HTML 템플릿 언어를 시작하기 전에 먼저 JSP와 HTL 주제와 관련된 몇 가지 질문에 대해 먼저 답변해 보겠습니다.

**HTL에는 JSP에 없는 제한이 있습니까?**
HTL은 JSP와 비교할 때 HTL을 사용하여 수행할 수 있는 작업이 HTL에서도 가능할 수 있다는 점에서 JSP에 비해 실제로 제한이 없습니다. 그러나 HTL은 여러 측면에서 JSP보다 더 엄격한 디자인으로 적용되며 단일 JSP 파일에서 모든 것이 수행될 수 있는 것은 Java 클래스 또는 JavaScript 파일로 구분되어 HTL에서 구현할 수 있어야 합니다. 그러나 이는 일반적으로 논리와 마크업 간에 우려 사항을 적절히 구분하기 위해 필요합니다.

**HTL은 JSP 태그 라이브러리를 지원합니까?**
아니요. 하지만 클라이언트 라이브러리 [로드](getting-started.md#loading-client-libraries) 섹션에 표시된 대로 [템플릿 및 호출](block-statements.md#template-call) 명령문은 유사한 패턴을 제공합니다.

**AEM 프로젝트에서 HTL 기능을 확장할 수 있습니까?**
아니요. 하지만 클라이언트 라이브러리 [로드](getting-started.md#loading-client-libraries) 섹션에 표시된 대로 [템플릿 및 호출](block-statements.md#template-call) 명령문은 유사한 패턴을 제공합니다.
아니, 그들은 할 수 없어. HTL은 로직인 Use- [API](getting-started.md#use-api-for-accessing-logic) 및 마크업( [템플릿 및 호출](block-statements.md#template-call) 문)을 재사용할 수 있는 강력한 확장 메커니즘을 제공하므로 프로젝트 코드를 수정할 수 있습니다.

**JSP에 대한 HTL의 주요 이점은 무엇입니까?**
보안 및 프로젝트 효율성은 개요에 자세히 설명된 주요 [이점입니다](overview.md).

**JSP는 결국 사라질까요?**
현재 날짜에는 이 줄에 따른 계획이 없습니다.

## HTL의 기본 개념 {#fundamental-concepts-of-htl}

HTML 템플릿 언어는 표현식 언어를 사용하여 컨텐츠의 일부를 렌더링된 마크업에 삽입하고, HTML5 데이터 속성을 사용하여 마크업 블록(예: 조건 또는 반복)을 통해 문을 정의합니다. HTL이 Java Servlets로 컴파일되면 표현식과 HTL 데이터 속성이 모두 완전히 서버측에서 평가되며, 결과 HTML에 아무 것도 표시되지 않습니다.

### 블록 및 표현식 {#blocks-and-expressions}

다음은 **`template.html`** 파일에 있는 것처럼 포함할 수 있는 첫 번째 예입니다.

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

두 가지 다른 종류의 구문은 구별할 수 있습니다.

* **[블록](block-statements.md)**&#x200B;문 **&lt;h1&gt;** 요소를 조건부로 표시하려면 `[data-sly-test](block-statements.md#test)` HTML5 데이터 속성이 사용됩니다. HTL은 HTML 요소에 비헤이비어를 첨부할 수 있도록 하는 이러한 여러 가지 속성을 제공하며 모든 속성이 접두사로 `data-sly`사용됩니다.

* **[표현식 언어](expression-language.md)** HTL 표현식은 문자와 `${` 로 구분됩니다 `}`. 런타임 시 이러한 표현식은 평가되고 해당 값은 나가는 HTML 스트림에 삽입됩니다.

위에 링크된 두 페이지는 구문에 사용할 수 있는 기능의 세부 목록을 제공합니다.

### SLY 요소 {#the-sly-element}

>[!NOTE]
>
>SLY 요소는 AEM 6.1 또는 HTL 1.1에서 도입되었습니다.
>
>그 전에 속성을 `[data-sly-unwrap](block-statements.md)` 대신 사용해야 했습니다.

HTL의 중심적 개념은 기존 HTML 요소를 다시 사용하여 블록 문을 정의할 수 있는 가능성을 제공하여 명령문이 시작되고 끝나는 위치를 정의하는 추가 구분 기호를 삽입하지 않아도 됩니다. 정적 HTML을 기능적인 동적 템플릿으로 변환하기 위한 마크업에 대한 이 완벽한 주석을 사용하면 HTML 코드의 유효성을 끊지 않고 정적인 파일로도 제대로 표시할 수 있습니다.

그러나 블록 문을 삽입해야 하는 정확한 위치에 기존 요소가 없는 경우가 있습니다. 이러한 경우 연결된 블록 문을 실행하고 그에 따라 내용을 표시하는 동안 출력에서 자동으로 제거될 특수 SLY 요소를 삽입할 수 있습니다.

다음 예를 참조하십시오.

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

는 다음 HTML과 같은 것을 출력하지만 둘 다 정의된 **`jcr:title`** 속성과 **`jcr:decription`** 속성이 모두 있는 경우에만 출력합니다.

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

그렇지만 한 가지 주의해야 할 점은 SLY 요소가 블록 명령문으로 주석을 달 수 없을 때 SLY 요소만 사용하는 것입니다. SLY 요소는 동적으로 만들 때 정적인 HTML을 변경하지 못하도록 언어에서 제공하는 값을 억제하기 때문입니다.

예를 들어 이전 예제가 이미 DIV 요소 내에서 래핑된 경우 추가된 SLY 요소는 남용됩니다.

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

및 DIV 요소에 다음과 같은 조건이 추가되었습니다.

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL 댓글 {#htl-comments}

다음 예는 **줄 1** 및 HTL 댓글과 **줄 2** 및 HTML 주석을 보여줍니다.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL 주석은 JavaScript와 유사한 추가 구문을 사용하는 HTML 주석입니다. 전체 HTL 주석 및 그 안에 있는 모든 내용은 프로세서가 완전히 무시하고 출력에서 제거됩니다.

그러나 표준 HTML 주석의 컨텐츠는 전달되고 주석 내의 표현식은 평가됩니다.

HTML 댓글에는 HTL 주석을 포함할 수 없으며 HTML 댓글도 포함될 수 없습니다.

### 특수 컨텍스트 {#special-contexts}

HTL을 최대한 활용하려면 HTML 구문에 따라 HTL의 결과를 잘 이해하는 것이 중요합니다.

### 요소 및 속성 이름 {#element-and-attribute-names}

표현식은 HTML 텍스트 또는 속성 값에만 배치할 수 있지만 요소 이름이나 속성 이름 내에는 삽입할 수 없거나 더 이상 유효한 HTML이 아닙니다. 요소 이름을 동적으로 설정하려면 원하는 요소에서 [`data-sly-element`](block-statements.md#element) 문을 사용할 수 있고, 특성 이름을 동적으로 설정하고, 한 번에 여러 속성을 설정할 수도 있으므로 [`data-sly-attribute`](block-statements.md#attribute) 문을 사용할 수 있습니다.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### 블록 문이 없는 컨텍스트 {#contexts-without-block-statements}

HTL은 데이터 속성을 사용하여 블록 문을 정의하므로 다음 컨텍스트 내에서 해당 블록 문을 정의할 수 없으며, 여기서 표현식만 사용할 수 있습니다.

* HTML 주석
* 스크립트 요소
* 스타일 요소

이러한 컨텍스트의 컨텐츠는 HTML이 아닌 텍스트이며 포함된 HTML 요소는 간단한 문자 데이터로 간주되기 때문입니다. 따라서 실제 HTML 요소가 없으면 **`data-sly`** 속성을 실행할 수 없습니다.

HTML 템플릿 언어가 HTML이 아닌 출력을 생성하는 데 악용되어서는 안 되므로 이는 큰 제한인 것처럼 들릴 수 있습니다. 아래의 [논리 액세스를 위한 Use-API](getting-started.md#use-api-for-accessing-logic) 섹션에서는 템플릿에서 추가 로직을 호출할 수 있는 방법을 소개합니다. 이러한 컨텍스트에 대해 복잡한 출력을 준비하는 데 필요할 경우 사용할 수 있습니다. 예를 들어, 백엔드 스크립트에서 프런트 엔드 스크립트로 데이터를 전송하는 쉬운 방법은 구성 요소의 논리를 사용하여 JSON 문자열을 생성한 후 간단한 HTL 표현식을 사용하여 데이터 속성에 삽입할 수 있는 것입니다.

다음 예에서는 HTML 주석에 대한 동작을 보여주지만 스크립트 또는 스타일 요소에서는 동일한 비헤이비어가 관찰됩니다.

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

다음과 같은 HTML을 출력합니다.

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### 명시적 컨텍스트 필요 {#explicit-contexts-required}

아래의 자동 컨텍스트 인식 [이스케이프](getting-started.md#automatic-context-aware-escaping) 섹션에 설명된 것처럼, HTL의 한 가지 목표는 모든 표현식에 컨텍스트 인식 이스케이프를 자동으로 적용하여 크로스 사이트 스크립팅(XSS) 취약점을 도입하는 위험을 줄이는 것입니다. HTL은 HTML 마크업 내에 배치된 표현식의 컨텍스트를 자동으로 검색할 수 있지만 인라인 JavaScript 또는 CSS의 구문을 분석하지 않으므로 개발자는 이러한 표현식에 정확히 어떤 컨텍스트를 적용해야 하는지 명시적으로 지정합니다.

올바른 escape를 적용하지 않으면 XSS 취약점이 발생하므로 HTL은 컨텍스트가 선언되지 않았을 때 스크립트 및 스타일 컨텍스트에 있는 모든 표현식의 출력을 제거합니다.

다음은 스크립트 및 스타일 내에 배치된 표현식의 컨텍스트를 설정하는 방법의 예입니다.

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

escape를 제어하는 방법에 대한 자세한 내용은 표현식 언어 표시 [컨텍스트 섹션을 참조하십시오](expression-language.md#display-context) .

### 특수 컨텍스트의 제한 해제 {#lifting-limitations-of-special-contexts}

스크립트, 스타일 및 주석 컨텍스트의 제한을 우회하는 데 필요한 특별한 경우 별도의 HTL 파일에서 컨텐츠를 분리할 수 있습니다. 자체 파일에 있는 모든 내용은 HTL에 의해 일반 HTML 조각으로 해석되며, 이 HTML이 포함되었을 수 있는 제한 컨텍스트는 제외됩니다.

예를 보려면 [클라이언트측 템플릿](getting-started.md#working-with-client-side-templates) 작업 섹션을 참조하십시오.

>[!CAUTION]
>
>이 기술은 크로스 사이트 스크립팅(XSS) 취약점을 도입할 수 있으며, 이 기능이 사용되는 경우 보안 측면을 신중하게 검토해야 합니다. 일반적으로 같은 것을 실행하는 더 나은 방법이 이 관행에 의존하는 것보다 있다.

## HTL의 일반 기능 {#general-capabilities-of-htl}

이 섹션에서는 HTML 템플릿 언어의 일반 기능을 빠르게 살펴볼 수 있습니다.

### 로직 액세스를 위한 Use-API {#use-api-for-accessing-logic}

다음 예를 고려해 보십시오.

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

그리고 다음 `logic.js` 서버측에서 실행된 JavaScript 파일이 그 옆에 배치됩니다.

```
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

HTML 템플릿 언어는 마크업 내에서 코드를 혼합할 수 없으므로 Use-API 확장 메커니즘을 제공하여 템플릿에서 코드를 쉽게 실행할 수 있습니다.

위의 예에서는 서버측에서 실행되는 JavaScript를 사용하여 제목을 10자로 단축했지만, Java 코드를 사용하여 정규화된 Java 클래스 이름을 제공하여 동일한 작업을 수행했을 수도 있습니다. 일반적으로 비즈니스 로직은 Java로 작성되어야 하지만, 구성 요소에 Java API가 제공하는 내용에서 일부 보기 특정 변경 사항이 필요한 경우 일부 서버측에서 실행되는 JavaScript를 사용하여 이를 수행하는 것이 편리할 수 있습니다.

다음 섹션의 세부 정보:

* 데이터 [사용 문구의](block-statements.md#use) 섹션은 해당 문구로 수행할 수 있는 모든 것을 설명합니다.
* Use- [API 페이지는](use-api.md) Java로 로직을 작성하거나 JavaScript로 로직을 작성하는 데 도움이 되는 몇 가지 정보를 제공합니다.
* 로직을 작성하는 방법에 대해 자세히 알아보려면 JavaScript [Use-API와](use-api-javascript.md) Java [Use-API](use-api-java.md) 페이지가도움이 됩니다.

### 자동 컨텍스트 인식 이스케이프 {#automatic-context-aware-escaping}

다음 예를 고려해 보십시오.

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

대부분의 템플릿 언어에서 이 예제는 모든 변수가 자동으로 HTML에서 이스케이프된 경우에도 `href` 특히나 URL 이스케이프되어야 하므로 사이트 간 스크립팅(XSS) 취약점을 잠재적으로 만들 수 있습니다. 이 생략은 매우 쉽게 잊혀질 수 있고 자동화된 방식으로 발견하기가 어렵기 때문에 가장 일반적인 오류 중 하나입니다.

이를 돕기 위해 HTML 템플릿 언어는 각 변수를 배치된 컨텍스트에 맞게 자동으로 이스케이프합니다. 이는 HTML이 HTML의 구문을 이해하므로 가능합니다.

다음 `logic.js` 파일 가칭:

```
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

그런 다음 초기 예제에서는 다음 출력을 생성합니다.

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

HTL은 URI 컨텍스트에 대해 `href` 및 `src` 속성을 escape해야 한다는 것을 알기 때문에 두 속성이 어떻게 다르게 이스케이프되었는지 확인합니다. 또한 URI가 다음으로 시작하는 **`javascript:`**&#x200B;경우 컨텍스트가 명시적으로 다른 것으로 변경되지 않은 한 속성이 완전히 제거되었습니다.

escape를 제어하는 방법에 대한 자세한 내용은 표현식 언어 표시 [컨텍스트 섹션을 참조하십시오](expression-language.md#display-context) .

### 빈 속성 자동 제거 {#automatic-removal-of-empty-attributes}

다음 예를 고려해 보십시오.

```xml
<p class="${properties.class}">some text</p>
```

속성 값이 `class` 비어 있으면 HTML 템플릿 언어는 출력에서 전체 `class` 속성을 자동으로 제거합니다.

HTML이 HTML 구문을 이해하므로 HTL의 값이 비어 있지 않은 경우에만 동적 값의 속성을 조건부로 표시할 수 있습니다. 속성 주위에 조건 블록을 추가하는 것을 방지하기 때문에 마크업이 잘못되고 읽을 수 없게 되므로 매우 편리합니다.

또한 표현식에 배치된 변수의 유형은 다음과 같습니다.

* **String:**
   * **** 비어 있지 않음:문자열을 속성 값으로 설정합니다.
   * **** 비어 있음:속성을 모두 제거합니다.

* **** 번호:값을 속성 값으로 설정합니다.

* **부울:**
   * **** true:값이 없는 속성을 부울 HTML 특성으로 표시합니다.
   * **** false:속성을 모두 제거합니다.

다음은 부울 표현식으로 부울 HTML 속성을 제어하는 방법의 예입니다.

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

속성을 설정하는 경우 [`data-sly-attribute`](block-statements.md#attribute) 명령문이 유용할 수도 있습니다.

## HTL을 사용한 공통 패턴 {#common-patterns-with-htl}

이 섹션에서는 몇 가지 일반적인 시나리오와 HTML 템플릿 언어로 가장 잘 해결하는 방법을 소개합니다.

### 클라이언트 라이브러리 로드 {#loading-client-libraries}

HTL에서 클라이언트 라이브러리는 AEM에서 제공하는 도우미 템플릿을 통해 로드되며, 이 템플릿은 [`data-sly-use`](block-statements.md#use)통해 액세스할 수 있습니다. 이 파일에서 사용할 수 있는 템플릿은 [ 다음과 `data-sly-call`](block-statements.md#template-call)같습니다.

* **`css`** - 참조된 클라이언트 라이브러리의 CSS 파일만 로드합니다.
* **`js`** - 참조된 클라이언트 라이브러리의 JavaScript 파일만 로드합니다.
* **`all`** - 참조된 클라이언트 라이브러리의 모든 파일을 로드합니다(CSS와 JavaScript 모두).

각 헬퍼 템플릿에는 원하는 클라이언트 라이브러리를 참조하는 **`categories`** 옵션이 필요합니다. 이 옵션은 문자열 값의 배열 또는 쉼표로 구분된 값 목록을 포함하는 문자열일 수 있습니다.

다음은 간단한 두 가지 예입니다.

### 한 번에 여러 클라이언트 라이브러리 모두 로드 {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### 페이지의 여러 섹션에서 클라이언트 라이브러리 참조 {#referencing-a-client-library-in-different-sections-of-a-page}

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

위의 두 번째 예에서, HTML **`head`** 및 **`body`** 요소가 다른 파일에 삽입되는 경우 **`clientlib.html`** 템플릿이 필요한 각 파일에 템플릿을 로드해야 합니다.

템플릿 및 [호출](block-statements.md#template-call) 문의 섹션에서는 이러한 템플릿의 선언과 호출 방법에 대한 자세한 내용을 제공합니다.

### 클라이언트에 데이터 전달 {#passing-data-to-the-client}

일반적으로 데이터를 클라이언트로 전달하는 가장 효과적이고 세련된 방법은 데이터 속성을 사용하는 것입니다.

다음 예에서는 로직(Java로 작성할 수도 있음)을 사용하여 클라이언트에 전달될 개체인 JSON으로 손쉽게 직렬화할 수 있는 방법을 보여 줍니다. 이렇게 하면 데이터 속성에 손쉽게 배치할 수 있습니다.

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```
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

여기에서 클라이언트측 JavaScript가 어떻게 해당 속성에 액세스하여 JSON을 다시 구문 분석할 수 있는지 쉽게 상상할 수 있습니다. 예를 들어 클라이언트 라이브러리에 배치할 해당 JavaScript가 됩니다.

```
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### 클라이언트측 템플릿 작업 {#working-with-client-side-templates}

특수 컨텍스트의 제한 해제 섹션에 설명된 [기술을](getting-started.md#lifting-limitations-of-special-contexts) 합법적으로 사용할 수 있는 특별한 경우는 **스크립트** 요소 내에 있는 클라이언트측 템플릿(예: 핸들바)을 작성하는 것입니다. 이러한 경우에 이 기술을 안전하게 사용할 수 있는 이유는 **스크립트** 요소가 가정된 대로 JavaScript를 포함하지 않고 추가 HTML 요소를 포함하기 때문입니다. 다음은 어떻게 작동하는지에 대한 예입니다.

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

위에서 보듯이, 요소에 포함될 마크업은 HTL 블록 문을 포함할 수 있고 Handlebars 템플릿의 컨텐츠가 자체 파일에서 분리되었으므로 표현식이 명시적 컨텍스트를 제공할 필요가 없습니다. **`script`** 또한 이 예에서는 **`h2`** **`h3`** 요소에서처럼 서버측에서 실행되는 HTL을 Handlebars(요소에 표시)와 같이 클라이언트측에서 실행되는 템플릿 언어와 혼합할 수 있는 방법을 보여줍니다.

그러나 보다 현대적인 방법은 템플릿의 컨텐츠를 별도의 파일로 분리하지 않아도 되므로 HTML **`template`** 요소를 대신 사용하는 것입니다.

**다음 참조:**

* [표현식](expression-language.md) 언어 - HTL 표현식 내에서 수행할 수 있는 작업을 자세히 학습합니다.
* [블록](block-statements.md) 문 - HTL에서 사용할 수 있는 모든 블록 문과 이를 사용하는 방법을 검색합니다.
