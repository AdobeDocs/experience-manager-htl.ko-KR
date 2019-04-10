---
title: HTL 시작하기
seo-title: HTL 시작하기
description: AEM에서 지원하는 HTL는 AEM에서 기본 및 권장 서버측 템플릿 시스템으로 JSP의 위치를 고려합니다.
seo-description: Adobe Experience Manager에서 지원하는 HTML 템플릿 언어 - HTL는 AEM에서 기본 및 권장
  서버측 템플릿 시스템으로 JSP를 대신합니다.
uuid: 4 a 7 d 6748-8 cdf -4280-a 85 d -6 c 5319 abf 487
content-type: 참조
topic-tags: 소개
discoiquuid: 3 BF 2 CA 75-0 D 68-489 D-BD 1 C -1 D 4 FD 730 C 61 A
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL 시작하기 {#getting-started-with-htl}

AEM (Adobe Experience Manager) 에서 지원하는 HTML 템플릿 언어 (HTL) 는 AEM에서 기본 및 권장 서버측 템플릿 시스템으로 JSP (javaserver Pages) 의 위치를 가져옵니다.

>[!NOTE]
>
>이 페이지에 제공된 대부분의 예제를 실행하려면 [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) 라는 라이브 실행 환경을 사용할 수 있습니다.
>
>AEM Community는 HTL 사용과 관련된 [일련의 아티클, 비디오 및 웨비나를](related-community-articles.md) 생성했습니다.

## JSP를 통한 HTL {#htl-over-jsp}

새 AEM 프로젝트는 JSP와 비교할 때 여러 이점을 제공하므로 HTML 템플릿 언어를 사용하는 것이 좋습니다. 하지만 기존 프로젝트의 경우, 마이그레이션은 향후 Jsps를 유지 관리하는 것보다 노력이 덜 필요한 경우에만 의미가 있습니다.

HTL로 쓰여진 구성 요소는 JSP 또는 ESP로 쓰여진 구성 요소와 호환되므로 HTL로 이동하는 것은 반드시 필요한 것은 아닙니다. 즉, 기존 프로젝트를 통해 새로운 구성 요소에 HTL를 사용하지 않고도 기존 구성 요소에 대한 JSP를 유지할 수 있습니다.

동일한 구성 요소 내에서 HTL 파일을 JSPS 및 ESPS와 함께 사용할 수 있습니다. 다음 예제는 **JSP 파일에서 HTL 파일을 포함하는** 방법, 그리고 HTL 파일에서 JSP **파일을 포함할 수** 있는 방법을 라인 1에 보여줍니다.

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### FAQ{#frequently-asked-questions}

HTML 템플릿 언어를 시작하기 전에 JSP와 HTL 토픽과 관련된 몇 가지 질문에 대한 답변을 미리 살펴보겠습니다.

**HTL 에는 JSP가 없는 제한이 있습니까?**HTL는 HTL와 함께 수행할 수 있는 기능도 HTL를 통해 구현할 수 있다는 점에서 JSP와 비교할 때 제한 사항이 없습니다. 그러나 HTL는 여러 측면에서 JSP 보다 훨씬 더 엄격한 디자인을 디자인하고, 단일 JSP 파일에서 모두 달성될 수 있는 점은 HTL에서 성취하기 위해서는 Java 클래스 또는 JavaScript 파일로 분리해야 할 수 있습니다. 그러나 이는 일반적으로 논리와 마크업 간의 문제를 잘 구분하려는 경우가 많습니다.

**HTL는 JSP 태그 라이브러리를 지원합니까?**아니요. 그러나 [클라이언트 라이브러리 [불러오기]](getting-started.md#loading-client-libraries) 섹션에 표시된 대로 [템플릿 및 호출](block-statements.md#template-call) 문은 비슷한 패턴을 제공합니다.

**AEM 프로젝트에서 HTL 기능을 확장할 수 있습니까?**** 아니요, 그러나 [클라이언트 라이브러리 [불러오기]](getting-started.md#loading-client-libraries) 섹션에 표시된 대로 [템플릿 및 호출](block-statements.md#template-call) 문은 비슷한 패턴을 제공합니다.
아니요. HTL 에는 프로젝트의 코드를 모듈화하는 데 사용할 수 [있는 Use-API](getting-started.md#use-api-for-accessing-logic) - and of Markup ( [Template & Call](block-statements.md#template-call) Statements) 의 재사용을 위한 강력한 확장 메커니즘이 있습니다.

**JSP를 통한 HTL의 주요 이점은 무엇입니까?**개요에 자세히 나와 있는 주요 이점은 보안 및 프로젝트 [](overview.md)효율성입니다.

**JSP는 결국 없어집니까?**현재 날짜에 이러한 라인에 따른 계획은 없습니다.

## HTL의 기본 개념 {#fundamental-concepts-of-htl}

HTML 템플릿 언어는 표현식 언어를 사용하여 렌더링된 마크업에 컨텐츠를 삽입하고 HTML 5 데이터 속성을 사용하여 조건 또는 반복 같은 마크업 블록에 대한 설명을 정의합니다. HTL 이 Java 서블릿으로 컴파일되면 표현식과 HTL 데이터 속성이 모두 서버측에서 평가되고 결과 HTML 에는 아무 것도 표시되지 않습니다.

### 블록 및 표현식 {#blocks-and-expressions}

다음은 파일에 있는 것으로 포함될 수 있는 첫 **`template.html`** 번째 예입니다.

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

두 가지 종류의 구문을 구별할 수 있습니다.

* **[&](block-statements.md)****amp; lt; h 1 & amp; gt;** 요소, `[data-sly-test](block-statements.md#test)` HTML 5 데이터 속성이 사용됩니다. HTL는 모든 HTML 요소에 비헤이비어를 첨부할 수 있는 이러한 속성을 여러 개 제공하며 모두 접두사로 추가됩니다 `data-sly`.

* **[표현식 언어](expression-language.md)**
HTL 표현식은 문자와 `${``}`숫자로 구분됩니다. 런타임 시 이러한 표현식은 평가되고 값이 나가는 HTML 스트림에 삽입됩니다.

위에 연결된 두 페이지는 구문에 사용할 수 있는 기능에 대한 세부 목록을 제공합니다.

### sly 요소 {#the-sly-element}

>[!NOTE]
>
>SLY 요소는 AEM 6.1 또는 HTL 1.1에서 도입되었습니다.
>
>이전에는 `[data-sly-unwrap](block-statements.md)` 속성을 대신 사용해야 했습니다.

HTL의 중앙 개념은 기존 HTML 요소를 재사용하여 블록 명령문을 정의할 가능성을 제공하므로, 추가 구분 기호를 삽입하지 않아도 문이 시작되는 위치와 끝나는 위치를 정의할 수 있습니다. 정적 HTML를 기능적인 동적 템플릿으로 변형할 수 있는 마크업 주석을 사용하면 HTML 코드의 유효성을 깨지지 않고 정적 파일로도 올바르게 표시할 수 있습니다.

그러나 경우에 따라 블록 명령문이 삽입되어야 하는 정확한 위치에 기존 요소가 없을 수도 있습니다. 이러한 경우, 첨부된 블록 문을 실행하고 그에 따라 컨텐트를 표시할 때 출력에서 자동으로 제거될 특별한 sly 요소를 삽입할 수 있습니다.

따라서 다음 예를 참조하십시오.

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

다음 HTML와 같은 결과를 출력하지만, a **`jcr:title`** 와 a **`jcr:decription`** 속성이 모두 있는 경우와 속성이 모두 비어 있는 경우에만 다음과 같은 결과가 출력됩니다.

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

하지만, sly 요소는 동적 HTML 만들 때 정적 HTML를 변경하지 않도록 언어로 제공된 값을 거부하기 때문에, 기존 요소가 블록 문으로 주석을 달 수 없는 경우에만 sly 요소를 사용해야 합니다.

예를 들어 이전 예를 div 요소 안에 이미 래핑한 경우 추가된 sly 요소가 남용됩니다.

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

and the div element could have annotated with the condition:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL 주석 {#htl-comments}

다음 예제는 라인 1 **의 HTL** 주석과 **라인 2** 의 HTML 주석을 보여 줍니다.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL 주석은 JavaScript와 유사한 구문을 갖는 HTML 주석입니다. 전체 HTL 주석 및 내부의 모든 내용은 프로세서에서 완전히 무시되고 출력에서 제거됩니다.

하지만 표준 HTML 댓글 컨텐츠는 전달되지만 주석 내 표현식은 평가됩니다.

HTML 주석은 HTL 주석을 포함할 수 없으며 그 반대의 경우도 마찬가지입니다.

### 특별한 컨텍스트 {#special-contexts}

HTL를 가장 잘 활용할 수 있으려면 HTML 구문을 기반으로 하는 결과를 잘 이해하는 것이 중요합니다.

### 요소 및 속성 이름 {#element-and-attribute-names}

표현식은 HTML 텍스트나 특성 값에만 배치할 수 있지만 요소 이름이나 속성 이름에는 배치할 수 없으며, 더 이상 유효한 HTML 이 아닙니다. 요소 이름을 동적으로 설정하고 [`data-sly-element`](block-statements.md#element) , 문을 원하는 요소에서 사용할 수 있으며, 한 번에 여러 속성을 설정하는 경우에도 속성 이름을 동적으로 설정할 수 있도록 [`data-sly-attribute`](block-statements.md#attribute) 문을 사용할 수 있습니다.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### 블록 명령문이 없는 컨텍스트 {#contexts-without-block-statements}

HTL는 데이터 속성을 사용하여 블록 명령문을 정의하므로, 다음 컨텍스트 내에 이러한 블록 명령문을 정의할 수 없으며, 여기에서 표현식만 사용할 수 있습니다.

* HTML 주석
* 스크립트 요소
* 스타일 요소

이러한 컨텍스트의 내용은 HTML 이 아닌 텍스트이고 HTML 요소는 간단한 문자 데이터로 간주된다는 것입니다. 따라서 실제 HTML 요소가 없으면 **`data-sly`** 속성을 실행할 수 없습니다.

이는 큰 제한처럼 들리겠지만 HTML 템플릿 언어는 HTML 이 아닌 출력을 생성하기 위해 남용되어서는 안 되므로 매우 유용합니다. 아래의 [로직](getting-started.md#use-api-for-accessing-logic) 섹션에 액세스하기 위한 use-api는 템플릿에서 추가 논리를 호출할 수 있는 방법을 소개하고 이러한 컨텍스트에 대한 복잡한 출력을 준비해야 하는 경우 사용할 수 있습니다. 예를 들어 백엔드에서 프런트 엔드 스크립트로 데이터를 전송하는 쉬운 방법은 구성 요소의 논리를 통해 JSON 문자열을 생성하는 것으로, 간단한 HTL 표현식을 사용하여 데이터 속성에 배치할 수 있습니다.

다음 예에서는 HTML 주석에 대한 동작을 설명하지만 스크립트 또는 스타일 요소에서 동일한 비헤이비어가 관찰됩니다.

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

다음과 같은 HTML를 출력합니다.

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### 명시적 컨텍스트 필요 {#explicit-contexts-required}

아래의 [자동 컨텍스트 인식 이스케이프](getting-started.md#automatic-context-aware-escaping) 처리 섹션에 설명된 바와 같이 HTL의 한 가지 목표는 모든 표현식에 컨텍스트 인식 이스케이프 처리를 자동으로 적용하여 XSS (교차 사이트 스크립팅) 취약점을 발생하는 위험을 줄이는 것입니다. HTL는 HTML 마크업 내부에 배치된 표현의 컨텍스트를 자동으로 감지할 수 있지만, 인라인 JavaScript 또는 CSS의 구문을 분석하지 않으므로 개발자는 이러한 표현식에 적용해야 하는 정확한 컨텍스트를 명시적으로 지정하는 데 사용합니다.

XSS 취약점에 escape가 올바르게 적용되지 않으므로 HTL는 컨텍스트가 선언되지 않았을 때 스크립트 및 스타일 컨텍스트에 있는 모든 표현식의 출력을 제거합니다.

다음은 스크립트 및 스타일에 가져온 표현식의 컨텍스트를 설정하는 방법의 예입니다.

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

escape를 제어하는 방법에 대한 자세한 내용은 [표현식 언어 표시 컨텍스트](expression-language.md#display-context) 섹션을 참조하십시오.

### 특별한 컨텍스트의 한계 제한 {#lifting-limitations-of-special-contexts}

스크립트, 스타일 및 주석 컨텍스트의 제한을 무시해야 하는 특별한 경우 별도의 HTL 파일에서 해당 컨텐츠를 분리할 수 있습니다. 자체 파일에 있는 모든 파일은 일반 HTML 조각으로 HTL에 의해 해석되며, 이것이 포함되었을 수 있는 제한 컨텍스트를 잊습니다.

자세한 [내용은 클라이언트측 템플릿](getting-started.md#working-with-client-side-templates) 사용 섹션을 참조하십시오.

>[!CAUTION]
>
>이 기술은 XSS (교차 사이트 스크립팅) 취약점을 초래할 수 있으며, 이러한 취약점이 사용되는 경우 보안 측면을 신중하게 연구해야 합니다. 일반적으로 이러한 방식에 의존하는 것보다 동일한 것을 구현하는 것이 더 좋은 방법입니다.

## HTL의 일반 기능 {#general-capabilities-of-htl}

이 섹션에서는 HTML 템플릿 언어의 일반 기능을 빠르게 살펴봅니다.

### Use-API for Access Logic {#use-api-for-accessing-logic}

다음 예를 고려하십시오.

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

및 다음의 `logic.js` 서버측 실행 JavaScript 파일을 다음에 배치했습니다.

```
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

HTML 템플릿 언어는 마크업 내에서 코드를 혼합할 수 없으므로 대신 Use-API 확장 메커니즘을 제공하여 템플릿에서 코드를 쉽게 실행합니다.

위의 예에서는 서버측 Javascript를 사용하여 제목을 10 자로 단축하지만 Java 코드를 사용하여 정규화된 Java 클래스 이름을 제공하여 동일한 작업을 수행할 수도 있습니다. 일반적으로 비즈니스 로직은 Java로 구축되어야 하지만, 구성 요소에 Java API에서 제공하는 내용에서 일부 보기 전용 변경이 필요할 때는 이 작업을 위해 서버측 실행 JavaScript를 사용하는 것이 편리할 수 있습니다.

다음 섹션에 대한 자세한 내용을 살펴보십시오.

* [data-sly-use 문에](block-statements.md#use) 대한 섹션은 해당 문으로 수행할 수 있는 모든 작업을 설명합니다.
* [Use-API page](use-api.md) provides some information to help the logic in Java or in JavaScript.
* 또한 로직을 작성하는 방법을 자세히 알아보려면 [JavaScript use-API](use-api-javascript.md) 및 [Java Use-API](use-api-java.md) 페이지를 사용해야 합니다.

### 자동 상황 인식 이스케이프 처리 {#automatic-context-aware-escaping}

다음 예를 고려하십시오.

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

대부분의 템플릿 언어에서 이 예제는 모든 변수가 자동으로 HTML 이스케이프되는 경우에도 `href` 속성이 특별히 URL 이스케이프되어야 하므로 XSS (교차 사이트 스크립팅) 취약성을 일으킬 수 있습니다. 이 생략은 매우 쉽게 잊어버릴 수 있으므로 가장 일반적인 오류 중 하나입니다.

이를 돕기 위해 HTML 템플릿 언어는 각 변수가 배치된 컨텍스트에 따라 자동으로 이스케이프합니다. HTL 이 HTML의 구문을 이해한다는 점 덕분에 가능합니다.

다음 파일 가정 `logic.js` :

```
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

그러면 초기 예제의 결과가 다음과 같이 출력됩니다.

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

HTL 컨텍스트 및 `href``src` 속성이 URI 컨텍스트에 대해 escape 되어야 한다는 점을 알 수 있으므로 두 속성이 다르게 이스케이프됩니다. 또한 URI가 다음으로 **`javascript:`**시작된 경우 컨텍스트가 명시적으로 다른 항목으로 변경되지 않는 한 속성이 완전히 제거되었습니다.

escape를 제어하는 방법에 대한 자세한 내용은 [표현식 언어 표시 컨텍스트](expression-language.md#display-context) 섹션을 참조하십시오.

### 빈 속성 자동 제거 {#automatic-removal-of-empty-attributes}

다음 예를 고려하십시오.

```xml
<p class="${properties.class}">some text</p>
```

`class` 속성 값이 비어 있으면 HTML 템플릿 언어는 출력에서 `class` 전체 속성을 자동으로 제거합니다.

HTL는 HTML 구문이 HTML 구문을 이해하므로 값이 비어 있지 않은 경우에만 조건부로 속성을 표시할 수 있으므로 이 기능은 가능합니다. 이 기능은 속성 주위에 조건 블록을 추가하지 않으므로 마크업이 잘못되고 읽을 수 없게 만들므로 매우 편리합니다.

또한 표현식에 배치된 변수의 유형은 다음과 같습니다.

* **String:**
   * **비어 있지 않음:** 문자열을 속성 값으로 설정합니다.
   * **비어 있음:** 속성을 모두 제거합니다.

* **숫자:** 값을 속성 값으로 설정합니다.

* **부울:**
   * **true:** 값 없이 속성을 표시합니다 (부울 html 속성).
   * **False:** 속성을 모두 제거합니다.

다음은 부울 표현식에서 부울 HTML 속성을 제어할 수 있는 방법의 예입니다.

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

속성을 설정하는 경우 [`data-sly-attribute`](block-statements.md#attribute) 문서도 유용할 수 있습니다.

## HTL를 사용한 공통 패턴 {#common-patterns-with-htl}

이 섹션에서는 HTML 템플릿 언어를 사용하여 몇 가지 일반적인 시나리오와 이를 최선의 방법으로 해결하는 방법을 소개합니다.

### 클라이언트 라이브러리 로드 {#loading-client-libraries}

HTL에서 클라이언트 라이브러리는 액세스 가능한 [`data-sly-use`](block-statements.md#use)AEM에서 제공하는 보조 템플릿을 통해 로드됩니다. 이 파일에서 다음 항목을 통해 세 개의 템플릿을 사용할 수 [`data-sly-call`](block-statements.md#template-call)있습니다.

* **`css`** - 참조된 클라이언트 라이브러리의 CSS 파일만 로드합니다.
* **`js`** - 참조된 클라이언트 라이브러리의 JavaScript 파일만 로드합니다.
* **`all`** - 참조된 클라이언트 라이브러리 (CSS 및 JavaScript 모두) 의 모든 파일을 로드합니다.

각 보조 템플릿에는 원하는 클라이언트 라이브러리를 참조하는 **`categories`** 옵션이 필요합니다. 이 옵션은 문자열 값 배열 또는 쉼표로 구분된 값 목록이 들어 있는 문자열일 수 있습니다.

다음은 두 가지 간단한 예입니다.

### 한 번에 여러 클라이언트 라이브러리 로드 완료 {#loading-multiple-client-libraries-fully-at-once}

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

위의 두 번째 예에서, HTML **`head`** 및 **`body`** 요소가 다른 파일에 배치되는 경우 **`clientlib.html`** , 템플릿이 필요한 각 파일에서 로드되어야 합니다.

[템플릿 및 호출](block-statements.md#template-call) 명세서에 있는 섹션은 이러한 템플릿을 선언하고 호출하는 방법에 대한 자세한 내용을 제공합니다.

### 클라이언트에 데이터 전달 {#passing-data-to-the-client}

일반적으로 HTL를 사용하여 고객에게 데이터를 전달하는 가장 좋은 방법은 데이터 속성을 사용하는 것입니다.

다음 예제에서는 로직이 Java로 매우 편리하게 직렬화할 수 있는 방법을 보여 줍니다. 이 방법은 클라이언트에 전달될 개체를 JSON에 매우 편리하게 직렬화할 수 있으며, 이렇게 하면 매우 쉽게 데이터 속성에 삽입할 수 있습니다.

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

여기서 클라이언트측 JavaScript가 해당 속성에 액세스하고 JSON를 다시 분석할 수 있는 방법을 쉽게 생각해 볼 수 있습니다. 예를 들어, 해당 JavaScript는 클라이언트 라이브러리에 배치할 해당 JavaScript 입니다.

```
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### 클라이언트측 템플릿 작업 {#working-with-client-side-templates}

특별한 컨텍스트의 [제한 제한 섹션에 설명된 기법이 합법적으로 사용되는 한](getting-started.md#lifting-limitations-of-special-contexts) 가지 특별한 예는 **스크립트** 요소 내에 있는 클라이언트측 템플릿 (예: handlebars) 를 작성하는 것입니다. 이 기법을 안전하게 사용할 수 있는 이유는 **스크립트** 요소에 JavaScript가 포함되어 있지 않고 더 나아가 HTML 요소가 포함되어 있기 때문입니다. 다음은 작업 방법의 예입니다.

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

위에 표시된 것처럼 **`script`** 요소에 포함될 마크업은 HTL 블록 명령문을 포함할 수 있으며, handlebars 템플릿의 컨텐츠가 자체 파일에서 분리되었으므로 표현식은 명시적 컨텍스트를 제공할 필요가 없습니다. 또한 이 예제는 서버측 실행htl ( **`h2`** 요소 등) 를 handlebars (요소에 표시) 처럼 클라이언트측 실행 템플릿 언어와 혼합하는 방법을 **`h3`** 보여줍니다.

하지만 보다 현대적인 기법은 HTML **`template`** 요소를 대신 사용해야 하지만, 이점은 템플릿의 컨텐츠를 별도의 파일로 분리할 필요가 없다는 점입니다.

**다음을 참조하십시오.**

* [표현식 언어](expression-language.md) - HTL 표현식에서 수행할 수 있는 작업을 자세히 알아 봅니다.
* [블록 설명 -](block-statements.md) HTL에서 사용할 수 있는 모든 블록 명령문과 이것을 사용하는 방법을 검색합니다.
