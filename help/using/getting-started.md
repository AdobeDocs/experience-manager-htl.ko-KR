---
title: HTL 시작하기
description: HTML에 대한 AEM의 기본 및 권장 서버측 템플릿 시스템인 HTL에 대해 알아보고 언어 및 기본 구성의 주요 개념에 대해 알아봅니다.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: ht
source-wordcount: '2050'
ht-degree: 100%

---


# HTL 시작하기 {#getting-started-with-htl}

HTML 템플릿 언어(HTL)은 Adobe Experience Manager에서 HTML용으로 주로 사용되고 권장되는 서버측 템플릿 시스템입니다. 모든 HTML 서버측 템플릿 시스템과 마찬가지로, HTL 파일은 HTML 코드, 기본적인 화면 표시 로직, 런타임 시에 평가될 변수를 지정하여 브라우저로 전송될 출력을 정의합니다.

이 문서는 HTL의 목적에 대한 개요와 더불어 언어의 기본 개념 및 구성에 대한 소개를 제공합니다.

>[!TIP]
>
>이 문서는 HTL의 목적 및 그 기본 구조와 개념에 대한 개요를 제공합니다. 특정 구문에 대한 질문이 있는 경우 [HTL 사양](specification.md)을 참조하십시오.

## HTL 레이어 {#layers}

AEM에서 HTL은 여러 레이어로 이루어져 있습니다.

1. **[HTL 사양](specification.md)** - HTL은 오픈 소스이며 특정 플랫폼에 구애받지 않는 사양으로, 누구나 자유롭게 구현할 수 있습니다.
1. **[Sling HTL 스크립팅 엔진](specification.md)** - Sling 프로젝트는 AEM에서 사용되는 HTL의 참조 구현을 만들었습니다.
1. **[AEM 확장 기능](specification.md)** - AEM은 Sling HTL 스크립팅 엔진을 기반으로 하여 AEM에 특화된 편리한 기능을 개발자에게 제공합니다.

이 HTL 문서는 HTL을 사용하여 AEM 솔루션을 개발하는 방법을 설명하는 데 중점을 둡니다. 따라서, 세 가지 레이어를 모두 다루며, 필요한 경우 외부 자료를 참고할 수 있도록 안내합니다.

## HTL의 기본 개념 {#fundamental-concepts-of-htl}

HTML 템플릿 언어는 표현식 언어를 사용하여 콘텐츠 조각을 렌더링된 마크업에 삽입하고 HTML5 데이터 속성을 사용하여 마크업 블록(예: 조건 또는 반복)에 대한 명령문을 정의합니다. HTL이 Java Servlet으로 컴파일되기 때문에, 표현식과 HTL 데이터 속성이 모두 서버측에서 평가되어 결과 HTML에 그 흔적이 남지 않습니다.

>[!TIP]
>
>이 페이지에 제공된 대부분의 예제를 실행하기 위해 [Read Eval Print Loop](https://github.com/adobe/aem-htl-repl)라는 라이브 실행 환경을 사용할 수 있습니다.

### 블록 및 표현식 {#blocks-and-expressions}

다음은 `template.html` 파일에 있는 그대로 포함될 수 있는 첫 번째 예입니다.

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

두 가지 다른 종류의 구문을 구별할 수 있습니다.

* **블록 문** - `<h1>` 요소를 조건부로 표시하려면 `data-sly-test` HTML5 데이터 속성을 사용하십시오. HTL이 제공하는 이러한 여러 가지 속성을 통해 HTML 요소에 특정 동작을 추가할 수 있으며, 모든 속성은 `data-sly` 접두사로 시작합니다.
* **표현식 언어** - `${` 및 `}` 문자가 HTL 표현식을 구분합니다. 런타임 시 이러한 표현식이 평가되며 해당 값은 발신 HTML 스트림에 삽입됩니다.

두 구문에 대한 자세한 내용은 [HTL 사양](specification.md)을 참조하십시오.

### SLY 요소 {#the-sly-element}

HTL의 핵심 개념은 기존의 HTML 요소를 재사용하여 블록 문을 정의할 수 있다는 점입니다. 이렇게 하면 명령문이 시작하고 끝나는 위치를 정의하기 위해 추가 구분 기호를 삽입할 필요가 없습니다. 마크업에 주석을 자연스럽게 추가하여 HTML의 유효성을 해치지 않으면서 정적 HTML을 동적 템플릿으로 변환할 수 있으며, 정적 파일 형태로 저장되어도 화면에 제대로 표시됩니다.

하지만, 블록 문을 삽입해야 할 바로 그 위치에 기존 요소가 없을 수도 있습니다. 이러한 경우에는 특수 `sly` 요소를 삽입할 수 있습니다. 이 요소는 연결된 블록 문을 실행할 때 자동으로 출력에서 사라지고, 그 안에 있는 콘텐츠만 표시됩니다.

다음은 예시입니다.

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

이 예시는 `jcr:title` 및 `jcr:description` 속성이 모두 적절하게 정의되어 있고, 둘 다 비어 있지 않은 경우에만 다음과 같은 HTML을 출력합니다.

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

유념해야 할 한 가지는 기존 요소에 블록 문으로 주석을 추가할 수 없는 경우에만 `sly` 요소를 사용하는 것입니다. 그 이유는 정적 HTML을 동적으로 만들 때 원래 있던 HTML을 변경하지 않는 HTL의 장점을 `sly`가 저해하기 때문입니다.

예를 들어, 이전 예시가 `div` 요소로 감싸져 있었다면, `sly` 요소를 불필요하게 추가한 것입니다.

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

이 경우, `div` 요소에 조건을 바로 추가할 수 있었기 때문입니다.

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL 주석 {#htl-comments}

다음 예시의 첫 번째 줄에 HTL 주석이, 두 번째 줄에는 HTML 주석이 있습니다.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL 주석은 자바스크립트와 유사한 구문이 추가된 HTML 주석입니다. 프로세서는 HTL 주석 전체와 그 안에 포함된 콘텐츠를 완전히 무시하며, 이를 출력에서 제거합니다.

반면에, 표준 HTML 주석의 콘텐츠는 그대로 전달되며, 주석 안에 포함된 표현식이 평가됩니다.

HTML 주석은 HTL 주석을 포함할 수 없으며, 그 반대도 마찬가지입니다.

### 특수 컨텍스트 {#special-contexts}

HTL을 효과적으로 사용하려면 HTL이 HTML 구문을 기반으로 작동한다는 사실과 그로 인해 생기는 결과를 제대로 이해하는 것이 중요합니다.

자세한 내용은 HTL 사양의 [표시 컨텍스트 섹션](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context)을 참조하십시오.

### 요소 및 속성 이름 {#element-and-attribute-names}

표현식은 HTML 텍스트나 속성 값 안에만 사용할 수 있으며, 요소 이름이나 속성 이름 안에는 사용할 수 없습니다. 그렇게 하면 더 이상 유효한 HTML이 아니기 때문입니다. 요소 이름을 동적으로 설정하려면, 원하는 요소에 `data-sly-element` 문을 사용할 수 있으며, 속성 이름을 동적으로 설정하려면, 여러 속성을 한 번에 설정할 수 있는 `data-sly-attribute` 문을 사용할 수 있습니다.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### 블록 문이 없는 컨텍스트 {#contexts-without-block-statements}

HTL은 데이터 속성을 사용하여 블록 문을 정의하기 때문에 다음 컨텍스트 내에서 이러한 블록 문을 정의할 수 없으며 표현식만 사용할 수 있습니다.

* HTML 주석
* 스크립트 요소
* 스타일 요소

그 이유는 이러한 컨텍스트의 콘텐츠가 HTML이 아닌 텍스트이고 포함된 HTML 요소가 단순 문자 데이터로 간주되기 때문입니다. 따라서 실제 HTML 요소가 없으면 `data-sly` 속성도 실행할 수 없습니다.

이 접근 방식은 상당한 제약처럼 들릴 수 있습니다. 하지만 HTML 템플릿 언어는 유효한 HTML 출력만 생성해야 하므로, 이 방식이 더 선호됩니다. 아래의 [로직 액세스를 위한 Use-API](#use-api-for-accessing-logic) 섹션에서는 템플릿에서 추가적인 로직을 호출하는 방법을 소개합니다. 이러한 컨텍스트에 대한 복잡한 출력을 준비하는 데 필요한 경우 이 방법을 사용할 수 있습니다. 백엔드에서 프론트엔드 스크립트로 데이터를 전송하려면, 컴포넌트의 로직을 사용해 JSON 문자열을 생성하고, 이를 간단한 HTL 표현식을 사용하여 데이터 속성에 삽입하면 됩니다.

다음 예시는 HTML 주석에서의 동작을 보여주지만, 스크립트나 스타일 요소에서도 동일하게 작동합니다.

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

아래의 [자동 컨텍스트 인식 이스케이프](#automatic-context-aware-escaping) 섹션에서 설명한 것처럼, HTL의 목표 중 하나는 모든 표현식에 컨텍스트에 인식 이스케이프를 자동으로 적용하여 크로스 사이트 스크립팅(XSS) 취약점을 줄이는 것입니다. HTL은 HTML 마크업의 표현식 컨텍스트를 감지하지만 인라인 JavaScript 또는 CSS는 분석하지 않으므로 개발자가 이러한 표현식을 위한 정확한 컨텍스트를 지정해야 합니다.

올바른 이스케이프 결과를 적용하지 않으면 XSS 취약성이 발생하므로 HTL은 컨텍스트가 선언되지 않은 경우 스크립트 및 스타일 컨텍스트에 있는 모든 표현식의 출력을 제거합니다.

다음은 스크립트 및 스타일 내부에 배치된 표현식에 대한 컨텍스트를 설정하는 방법의 예입니다.

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

이스케이프 제어 방법에 대한 자세한 내용은 HTL 사양의 [표현식 언어 표시 컨텍스트](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) 섹션을 참조하십시오.

## HTL의 일반 기능 {#general-capabilities-of-htl}

이 섹션에서는 HTML 템플릿 언어의 일반적인 기능을 빠르게 살펴봅니다.

### 로직 액세스를 위한 Use-API {#use-api-for-accessing-logic}

HTML 템플릿 언어(HTL) Java Use-API를 사용하면 HTL 파일이 `data-sly-use`를 통해 사용자 정의 Java 클래스의 도우미 메서드에 액세스할 수 있습니다. 이를 통해 복잡한 비즈니스 로직은 모두 Java 코드에 캡슐화되고, HTL 코드는 직접적인 마크업 생성만 처리하게 됩니다.

자세한 내용은 [HTL Java Use-API](java-use-api.md) 문서를 참조하십시오.

### 자동 컨텍스트 인식 이스케이프 {#automatic-context-aware-escaping}

다음 예를 생각해 보십시오.

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

대부분의 템플릿 언어에서는 이 예시가 크로스 사이트 스크립팅(XSS) 취약점을 일으킬 수 있습니다. 모든 변수가 자동으로 HTML 이스케이프 처리되더라도, `href` 속성은 URL 이스케이프 처리가 별도로 필요하기 때문입니다. 이 같은 누락은 간과되기 쉽고, 자동화된 방식으로는 발견하기 어려운 가장 일반적인 오류 중 하나입니다.

이를 해결하기 위해, HTML 템플릿 언어는 각 변수가 사용되는 컨텍스트에 맞게 자동으로 이스케이프 처리를 해줍니다. 이는 HTL이 HTML 구문을 이해하고 있기 때문에 가능한 접근 방식입니다.

다음은 `logic.js` 파일의 예시입니다.

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

초기 예시는 다음과 같은 결과를 출력합니다.

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

이 예시에서 두 속성이 각각 다르게 이스케이프 처리된 것을 볼 수 있습니다. HTL이 `href`와 `src` 속성은 URI 컨텍스트에서 이스케이프 처리해야 한다는 것을 알고 있기 때문입니다. 또한 URI가 `javascript:`로 시작했다면 컨텍스트가 명시적으로 다른 것으로 변경된 경우 외에는 속성이 완전히 제거되었을 것입니다.

이스케이프 제어 방법에 대한 자세한 내용은 HTL 사양의 [표현식 언어 표시 컨텍스트](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) 섹션을 참조하십시오.

### 빈 속성 자동 제거 {#automatic-removal-of-empty-attributes}

다음 예를 생각해 보십시오.

```xml
<p class="${properties.class}">some text</p>
```

`class` 속성 값이 비어 있으면 HTML 템플릿 언어는 자동으로 전체 `class` 속성을 출력에서 제거합니다.

이 과정이 가능한 이유는 HTL이 HTML 구문을 이해하고 있어, 동적 값이 비어 있지 않은 경우에만 조건부로 속성을 표시할 수 있기 때문입니다. 이 기능은 속성 주위에 조건 블록을 추가할 필요를 없애, 마크업을 잘못된 구조로 만들거나 가독성을 해치지 않도록 해줍니다.

또한 표현식에 배치된 변수의 유형이 중요합니다.

* **문자열(String):**
   * **not empty:** 문자열을 속성 값으로 설정합니다.
   * **empty:** 속성을 모두 제거합니다.

* **숫자(Number):** 숫자를 속성 값으로 설정합니다.

* **부울(Boolean):**
   * **true:** 값이 없는 속성을 표시합니다(부울 HTML 속성으로).
   * **false:** 속성을 모두 제거합니다.

다음은 부울 표현식을 사용하여 부울 HTML 속성을 제어하는 방법의 예입니다.

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

속성을 설정할 때는 `data-sly-attribute` 문도 유용할 수 있습니다.

## HTL의 일반적인 패턴 {#common-patterns-with-htl}

이 섹션에서는 몇 가지 일반적인 시나리오를 소개합니다. HTML 템플릿 언어로 이러한 시나리오를 가장 잘 해결할 수 있는 방법을 소개합니다.

### 클라이언트 라이브러리 로드 {#loading-client-libraries}

HTL에서 클라이언트 라이브러리는 `data-sly-use`를 통해 액세스할 수 있는 AEM에서 제공하는 도우미 템플릿을 통해 로드됩니다. 이 파일에는 `data-sly-call`을 통해 호출할 수 있는 세 가지 템플릿이 있습니다.

* **`css`** - 참조된 클라이언트 라이브러리의 CSS 파일만 로드합니다.
* **`js`** - 참조된 클라이언트 라이브러리의 JavaScript 파일만 로드합니다.
* **`all`** - 참조된 클라이언트 라이브러리의 모든 파일을 로드합니다(CSS 및 JavaScript 모두).

각 도우미 템플릿에는 원하는 클라이언트 라이브러리를 참조하기 위한 `categories` 옵션이 필요합니다. 해당 옵션은 문자열 값의 배열이거나 쉼표로 구분된 값 목록을 포함하는 문자열일 수 있습니다.

다음은 두 가지 짧은 예입니다.

#### 여러 클라이언트 라이브러리를 한 번에 완전히 로드 {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

#### 페이지의 다른 섹션에서 클라이언트 라이브러리 참조 {#referencing-a-client-library-in-different-sections-of-a-page}

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

이 예시에서 HTML의 `head`와 `body` 요소가 각각 다른 파일에 있을 경우, `clientlib.html` 템플릿을 각 파일에서 따로 로드해야 합니다.

[HTL 사양](specification.md)의 템플릿 및 호출 문 섹션은 이러한 템플릿을 선언하고 호출하는 방법에 대한 자세한 내용을 제공합니다.

### 클라이언트에 데이터 전달 {#passing-data-to-the-client}

일반적으로(특히, HTL의 경우) 데이터를 클라이언트에 전달하는 가장 훌륭하고 우아한 방법은 `data` 속성을 사용하는 것입니다.

다음 예시는 오브젝트를 JSON(Java로도 가능)으로 직렬화하여 클라이언트로 전달하는 방법을 보여줍니다. 이 JSON 데이터는 `data` 속성에 쉽게 넣을 수 있습니다.

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

이후 클라이언트측 JavaScript가 해당 속성에 접근해 JSON을 다시 파싱하는 방법은 매우 간단합니다. 예를 들어, 다음과 같은 JavaScript 코드를 클라이언트 라이브러리에 넣어 사용할 수 있습니다.

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### 클라이언트측 템플릿 작업 {#working-with-client-side-templates}

[특수 컨텍스트의 제한 해제](#lifting-limitations-of-special-contexts) 섹션에서 설명된 기술을 적합하게 사용할 수 있는 한 가지 특별한 경우는 클라이언트측 템플릿(예: 핸들바와 같은 템플릿)을 `scrip` 요소 내에 작성하는 경우입니다. 이 경우 이 기술을 안전하게 사용할 수 있는 이유는 `script` 요소에 가정한 대로 JavaScript가 포함되지 않고 추가 HTML 요소가 포함되기 때문입니다. 다음은 이 방법을 적용한 예시입니다.

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

`script` 요소의 마크업은 명시적 컨텍스트 없이도 HTL 블록 문을 포함할 수 있으며 이는 핸들바 템플릿의 콘텐츠가 자체 파일에 격리되었기 때문입니다. 또한, 이 예시는 서버 측에서 실행되는 HTL(예: `h2` 요소)과 클라이언트 측에서 실행되는 템플릿 언어(예: `h3` 요소에 사용된 핸들바)를 혼합하는 방법을 보여줍니다.

하지만 보다 현대적인 방법은 HTML `template` 요소를 대신 사용하는 것으로, 템플릿의 콘텐츠를 별도의 파일로 격리할 필요 없이 작업할 수 있는 장점이 있습니다.

### 특수 컨텍스트의 제한 해제 {#lifting-limitations-of-special-contexts}

스크립트, 스타일 및 주석 컨텍스트의 제한을 우회해야 하는 특별한 경우에는 해당 콘텐츠를 별도의 HTL 파일로 격리할 수 있습니다. HTL은 포함된 위치의 모든 제한적 컨텍스트를 무시하고 자체 파일 내의 모든 내용을 표준 HTML 조각으로 해석합니다.

예를 보려면 더 아래에 있는 [클라이언트측 템플릿 작업](#working-with-client-side-templates) 섹션을 참조하십시오.

>[!CAUTION]
>
>이 기술은 XSS(크로스 사이트 스크립팅) 취약성을 유발할 수 있습니다. 이 방법을 사용하는 경우 보안 측면을 주의 깊게 검토해야 합니다. 이 방식에 의존하지 않고도 동일한 기능을 구현할 수 있는 더 나은 방법들이 보통 존재합니다.
