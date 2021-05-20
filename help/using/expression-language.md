---
title: HTL 표현식 언어
description: HTML 템플릿 언어에서는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다.
exl-id: 57e3961b-8c84-4d56-a049-597c7b277448
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: tm+mt
source-wordcount: '1854'
ht-degree: 0%

---

# HTL 표현식 언어 {#htl-expression-language}

HTML 템플릿 언어에서는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다. 이러한 표현식은 `${` 및 `}` 문자로 구분됩니다. 잘못된 포맷의 HTML을 방지하기 위해 표현식은 속성 값, 요소 컨텐츠 또는 주석에만 사용할 수 있습니다.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

표현식은 `\` 문자를 앞에 추가하여 이스케이프할 수 있습니다. 예를 들어 `\${test}`은 `${test}`를 렌더링합니다.

>[!NOTE]
>
>이 페이지에서 제공된 예제를 사용해 보려면 [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl)라는 라이브 실행 환경을 사용할 수 있습니다.

표현식 구문에는 [변수](#variables), [리터럴](#literals), [연산자](#operators) 및 [옵션](#options)이 포함됩니다.

## 변수 {#variables}

변수는 데이터 값 또는 개체를 저장하는 컨테이너입니다. 변수의 이름을 식별자라고 합니다.

HTL에서는 아무 것도 지정하지 않고 `global.jsp` 을 포함하여 JSP에서 일반적으로 사용할 수 있었던 모든 객체에 액세스할 수 있습니다. [전역 개체](global-objects.md) 페이지는 HTL에서 액세스할 수 있는 모든 개체 목록을 제공합니다.

### 속성 액세스 {#property-access}

점 표기법 또는 대괄호 표기법으로 변수의 속성에 액세스하는 방법에는 두 가지가 있습니다.

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

대부분의 경우 보다 간단한 점 표기법을 사용하는 것이 좋지만, 대괄호 표기법을 사용하여 잘못된 식별자 문자가 포함된 속성에 액세스하거나 속성에 동적으로 액세스해야 합니다. 다음 두 장은 이 두 사건에 대한 세부 사항을 제공합니다.

액세스한 속성은 함수일 수 있지만 인수 전달은 지원되지 않으므로 getter와 같이 인수를 예상하지 않는 함수만 액세스할 수 있습니다. 이것은 표현식에 포함된 로직의 양을 줄이기 위한 것입니다. 필요한 경우 [`data-sly-use`](block-statements.md#use) 문을 사용하여 매개 변수를 로직에 전달할 수 있습니다.

위의 예제에서 알 수 있듯이 `getTitle()`과 같은 Java getter 함수는 `get` 앞에 있지 않고, 다음에 오는 문자의 대/소문자를 줄임으로써 액세스할 수 있습니다.

### 유효한 식별자 문자 {#valid-identifier-characters}

식별자라고 하는 변수의 이름은 특정 규칙을 따릅니다. 문자(`A`-`Z` 및 `a`-`z`) 또는 밑줄(`_`)로 시작해야 하며, 그 다음 문자는 숫자(`0`-`9`) 또는 콜론(`:`)일 수도 있습니다. 식별자에는 `å` 및 `ü` 등의 유니코드 문자를 사용할 수 없습니다.

콜론(`:`) 문자는 AEM 속성 이름에 공통인 경우 올바른 식별자 문자여야 한다는 점을 강조해야 합니다.

`${properties.jcr:title}`

대괄호 표기법은 아래 예제에서 공백 문자와 같이, 잘못된 식별자 문자가 포함된 속성에 액세스하는 데 사용할 수 있습니다.

`${properties['my property']}`

### 동적으로 {#accessing-members-dynamically} 멤버 액세스

```xml
${properties[myVar]}
```

### Null 값 처리 허용 {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## 리터럴 {#literals}

리터럴은 고정 값을 나타내는 표기법입니다.

### 부울 {#boolean}

부울은 논리 엔티티를 나타내며 두 개의 값을 가질 수 있습니다.`true` 및 `false`

`${true} ${false}`

### 숫자 {#numbers}

숫자 유형은 하나만 있습니다.양의 정수. 부동 소수점 같은 다른 숫자 형식은 변수에서 지원되지만 리터럴로 표현될 수 없습니다.

`${42}`

### 문자열 {#strings}

문자열은 텍스트 데이터를 나타내며, 단일 또는 이중 따옴표일 수 있습니다.

`${'foo'} ${"bar"}`

일반 문자 외에 다음과 같은 특수 문자를 사용할 수 있습니다.

* `\\` 백슬래시 문자
* `\'` 작은 따옴표(또는 아포스트로피)
* `\"` 큰따옴표
* `\t` 탭
* `\n` 새 줄
* `\r` 캐리지 리턴
* `\f` 양식 피드
* `\b` 백스페이스
* `\uXXXX` 4개의 16진수 XXXX로 지정된 유니코드 문자입니다.\
   유용한 유니코드 이스케이프 시퀀스는 다음과 같습니다.

   * `\u0022` 대상 `"`
   * `\u0027` 대상 `'`

위에 나열되지 않은 문자의 경우 백슬래시 문자 앞에 오류가 표시됩니다.

다음은 문자열 이스케이프를 사용하는 방법의 예입니다.

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

HTL이 컨텍스트별 이스케이프를 적용하므로 다음 출력이 발생합니다.

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### 어레이 {#arrays}

배열은 이름과 인덱스로 참조할 수 있는 순서가 지정된 값 세트입니다. 요소의 유형을 혼합할 수 있습니다.

```xml
${[1,2,3,4]}
${myArray[2]}
```

배열은 템플릿의 값 목록을 제공하는 데 유용합니다.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## 연산자 {#operators}

### 논리 연산자 {#logical-operators}

이 연산자는 일반적으로 부울 값과 함께 사용되지만, JavaScript에서와 같이, 지정된 피연산자 중 하나의 값을 실제로 반환하므로 부울이 아닌 값과 함께 사용하면 부울이 아닌 값을 반환할 수 있습니다.

값을 `true`(으)로 변환할 수 있는 경우 이 값을 이른바 truthy라고 합니다. 값을 `false`(으)로 변환할 수 있는 경우 이 값을 이른바 falsy라고 합니다. `false`(으)로 변환할 수 있는 값은 정의되지 않은 변수, null 값, 숫자 0 및 빈 문자열입니다.

#### 논리 NOT {#logical-not}

`${!myVar}` 단일 피연산자가 로 변환할 수  `false` 있는 경우 를  `true`반환합니다.그렇지 않으면 가  `true`반환됩니다.

예를 들어 하위 페이지가 없는 경우에만 요소를 표시하는 것과 같이 테스트 조건을 반전시키는 데 사용할 수 있습니다.

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### 논리 AND {#logical-and}

`${varOne && varTwo}` falsy `varOne` 이면 를 반환합니다.그렇지 않으면 가  `varTwo`반환됩니다.

이 연산자를 사용하여 두 속성이 있는지 확인하는 것처럼 두 가지 조건을 한 번에 테스트할 수 있습니다.

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

HTL은 false로 동적으로 평가되는 값이 설정된 특성을 제거하거나 빈 문자열로 설정되기 때문에 논리 AND 연산자를 사용하여 HTML 속성을 조건부로 표시할 수도 있습니다. 따라서 아래 예에서 `class` 속성은 `logic.showClass` 가 참 같은 경우에만 표시되고 `logic.className` 가 존재하고 비어 있지 않은 경우에만 표시됩니다.

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### 논리 OR {#logical-or}

`${varOne || varTwo}` 참 `varOne` 이 같은 경우 를 반환합니다.그렇지 않으면 가  `varTwo`반환됩니다.

이 연산자는 하나 이상의 속성이 있는지 확인하는 것과 같이 두 조건 중 하나가 적용되는지 테스트하는 데 사용할 수 있습니다.

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

논리 OR 연산자는 truthy인 첫 번째 변수를 반환하므로 폴백 값을 제공하는 데 매우 편리하게 사용할 수도 있습니다.

HTL은 false로 평가되는 표현식으로 설정된 값이나 빈 문자열로 설정된 속성을 제거하므로 조건부로 HTML 속성을 표시하는 데에도 사용할 수 있습니다. 따라서 아래 예에는 **`properties.jcr:`** 제목이 있고 비어 있지 않으면 표시됩니다. 그렇지 않으면 **`properties.jcr:description`** 이 존재하고 비어 있지 않으면 메시지가 &quot;제공된 제목 또는 설명 없음&quot;으로 표시됩니다.

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### 조건부(터너리) 연산자 {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` 참 `varOne` 이  `varCondition` 같은 경우 를 반환합니다.그렇지 않으면  `varTwo`를 반환합니다.

이 연산자는 일반적으로 페이지의 상태에 따라 다른 메시지를 표시하는 것과 같이 표현식 내에서 조건을 정의하는 데 사용할 수 있습니다.

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>식별자에서도 콜론 문자가 허용되므로 구문 분석기에 명확성을 제공하기 위해 공백을 사용하여 삼중 연산자를 구분하는 것이 좋습니다.

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### 비교 연산자 {#comparison-operators}

같음 연산자와 같지 않음 연산자는 동일한 유형의 피연산자만 지원합니다. 유형이 일치하지 않으면 오류가 표시됩니다.

* 문자열의 문자 시퀀스가 같은 경우 문자열은 같습니다.
* 숫자가 같은 값을 갖는 경우 숫자가 같습니다
* 둘 다 `true` 이거나 모두 `false`인 경우 부울은 동일합니다.
* Null 또는 정의되지 않은 변수는 자신과 서로 같습니다.

`${varOne == varTwo}` 와  `true` 가  `varOne` 같으면  `varTwo` 를 반환합니다.

`${varOne != varTwo}` 와  `true` 가  `varOne` 같지  `varTwo` 않으면 를 반환합니다.

관계형 연산자는 숫자인 연산자만 지원합니다. 다른 모든 유형의 경우 오류가 표시됩니다.

`${varOne > varTwo}` 가  `true` 보다  `varOne` 큰 경우 를  `varTwo`반환합니다.

`${varOne < varTwo}` 가  `true` 보다  `varOne` 작으면 를  `varTwo`반환합니다.

`${varOne >= varTwo}` 다음보다  `true` 크거나  `varOne` 같은 경우 를  `varTwo`반환합니다.

`${varOne <= varTwo}` 가  `true` 작거나  `varOne` 같으면 를  `varTwo`반환합니다.

### 괄호 {#grouping-parentheses} 그룹화

그룹화 연산자 `()`는 식의 평가 우선 순위를 제어합니다.

`${varOne && (varTwo || varThree)}`

## 옵션 {#options}

표현식 옵션이 표현식에 대해 작동하여 수정하거나 블록 문과 함께 사용할 때 매개 변수로 사용할 수 있습니다.

`@` 뒤의 모든 것은 선택 사항입니다.

```xml
${myVar @ optOne}
```

옵션에는 값(변수 또는 리터럴)이 있을 수 있습니다.

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

여러 옵션은 쉼표로 구분됩니다.

```xml
${myVar @ optOne, optTwo=bar}
```

옵션만 포함된 패라메트릭 표현식도 가능합니다.

```xml
${@ optOne, optTwo=bar}
```

### 문자열 서식 {#string-formatting}

열거된 자리 표시자 {*n*}를 해당 변수로 대체하는 옵션입니다.

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

## URL 조작 {#url-manipulation}

새로운 URL 조작 세트를 사용할 수 있습니다.

사용법에 대해서는 다음 예를 참조하십시오.

경로에 html 확장을 추가합니다.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

html 확장 및 선택기를 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

경로에 html 확장 및 조각(#value)을 추가합니다.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

`@extension` 은 확장을 추가할지 여부를 확인하여 모든 시나리오에서 작동합니다.

```xml
${ link @ extension = 'html' }
```

### 숫자/날짜 형식 {#number-date-formatting}

HTL에서는 사용자 지정 코드를 작성하지 않고도 숫자 및 날짜를 기본적으로 서식을 지정할 수 있습니다. 이 작업에서는 시간대 및 로케일도 지원합니다.

다음 예제에서는 형식이 먼저 지정된 다음 형식이 필요한 값을 보여 줍니다.

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>사용할 수 있는 형식에 대한 자세한 내용은 [HTL-specification](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)을 참조하십시오.

### 다국어화 {#internationalization}

문자열을 현재 [dictionary](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/internationalization/i18n-translator.html)를 사용하여 현재 *source*(아래 참조)의 언어로 변환합니다. 번역을 찾을 수 없으면 원래 문자열이 사용됩니다.

```xml
${'Page' @ i18n}
```

힌트 옵션은 텍스트 사용 컨텍스트를 지정하여 변환기에 대한 설명을 제공하는 데 사용할 수 있습니다.

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

언어의 기본 소스는 `resource`입니다. 즉, 텍스트가 컨텐츠와 동일한 언어로 번역됩니다. 이 값을 `user`(으)로 변경할 수 있습니다. 즉, 브라우저 로케일 또는 로그인한 사용자의 로케일에서 언어를 가져옵니다.

```xml
${'Page' @ i18n, source='user'}
```

명시적 로케일을 제공하면 소스 설정을 재정의합니다.

```xml
${'Page' @ i18n, locale='en-US'}
```

변수를 번역된 문자열에 포함하려면 형식 옵션을 사용할 수 있습니다.

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### 배열 조인 {#array-join}

기본적으로 배열을 텍스트로 표시할 때 HTL에 쉼표로 구분된 값(공백 없음)이 표시됩니다.

조인 옵션을 사용하여 다른 구분자를 지정합니다.

```xml
${['one', 'two'] @ join='; '}
```

### 컨텍스트 {#display-context} 표시

HTL 표현식의 표시 컨텍스트는 HTML 페이지의 구조 내에서 해당 위치를 참조합니다. 예를 들어 표현식이 렌더링되면 텍스트 노드를 만드는 위치에 나타나면 `text` 컨텍스트에 있다고 합니다. 속성 값 내에 있는 경우 `attribute` 컨텍스트 등으로 표시됩니다.

스크립트(JS) 및 스타일(CSS) 컨텍스트를 제외하고 HTL은 XSS 보안 문제를 방지하기 위해 표현식 컨텍스트를 자동으로 감지하고 적절하게 이스케이프 처리합니다. 스크립트 및 CSS의 경우 원하는 컨텍스트 동작을 명시적으로 설정해야 합니다. 또한, 자동 동작의 무시를 원하는 다른 경우에는 컨텍스트 동작을 명시적으로 설정할 수도 있습니다.

여기에서는 세 가지 다른 컨텍스트에서 세 가지 변수가 있습니다.

* `properties.link` (  `uri` 컨텍스트)
* `properties.title` (`attribute` 컨텍스트)
* `properties.text` (`text` 컨텍스트)

HTL은 각 컨텍스트의 보안 요구 사항에 따라 이러한 각 규칙을 다르게 이스케이프 처리합니다. 다음과 같은 일반적인 경우에는 명시적 컨텍스트 설정이 필요하지 않습니다.

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

마크업을 안전하게 출력하려면(즉, 표현식 자체가 HTML로 평가되는 경우) `html` 컨텍스트가 사용됩니다.

```xml
<div>${properties.richText @ context='html'}</div>
```

스타일 컨텍스트에 대해 명시적 컨텍스트를 설정해야 합니다.

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

스크립트 컨텍스트에 대해 명시적 컨텍스트를 설정해야 합니다.

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

이스케이프 처리 및 XSS 보호 기능도 해제할 수 있습니다.

```xml
<div>${myScript @ context='unsafe'}</div>
```

### 컨텍스트 설정 {#context-settings}

| 컨텍스트 | 사용 시기 | 기능 |
|--- |--- |--- |
| `text` | 요소 내의 콘텐츠에 대한 기본값 | 모든 HTML 특수 문자를 인코딩합니다. |
| `html` | 마크업을 안전하게 출력하려면 | HTML을 필터링하여 규칙과 일치하지 않는 규칙을 제거합니다. |
| `attribute` | 속성 값의 기본값 | 모든 HTML 특수 문자를 인코딩합니다. |
| `uri` | href 및 src 속성 값에 대한 링크 및 경로 기본값 표시 | href 또는 src 속성 값으로 쓸 URI의 유효성을 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| `number` | 숫자를 표시하려면 | 정수를 포함하는 URI의 유효성을 검사하고, 유효성 검사에 실패하면 0을 출력합니다. |
| `attributeName` | 속성 이름을 설정할 때 data-sly-attribute에 대한 기본값 | 속성 이름을 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| `elementName` | data-sly-element의 기본값 | 요소 이름을 확인하고 유효성 검사에 실패하면 아무 것도 출력합니다. |
| `scriptToken` | JS 식별자, 리터럴 숫자 또는 리터럴 문자열의 경우 | JavaScript 토큰의 유효성을 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| `scriptString` | JS 문자열 내 | 문자열에서 벗어날 문자를 인코딩합니다. |
| `scriptComment` | JS 주석 내 | JavaScript 주석의 유효성을 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| `styleToken` | CSS 식별자의 경우, 숫자, 차원, 문자열, 16진수 색상 또는 함수. | CSS 토큰의 유효성을 검사하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| `styleString` | CSS 문자열 내 | 문자열에서 벗어날 문자를 인코딩합니다. |
| `styleComment` | CSS 주석 내 | CSS 주석의 유효성을 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| `unsafe` | 위의 작업 중 어느 것도 작업을 수행하지 않는 경우에만 | 이스케이프 및 XSS 보호를 완전히 사용하지 않습니다. |
