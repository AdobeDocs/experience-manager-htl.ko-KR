---
title: HTL 표현식 언어
description: AEM에서 HTL 표현식 언어 사용에 대해 알아봅니다. HTML 템플릿 언어는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다.
exl-id: 57e3961b-8c84-4d56-a049-597c7b277448
source-git-commit: 7b53eff0652f650ffb8caae0e69aa349b5c548eb
workflow-type: tm+mt
source-wordcount: '1860'
ht-degree: 99%

---

# HTL 표현식 언어 {#htl-expression-language}

HTML 템플릿 언어는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다. 이 표현식은 `${` 및 `}` 문자로 구분됩니다. 잘못된 HTML을 방지하기 위해 표현식은 속성 값, 요소 내용 또는 주석에만 사용할 수 있습니다.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

표현식은 `\` 문자를 앞에 추가하여 이스케이프할 수 있습니다. 예를 들어 `\${test}`는 `${test}`를 렌더링합니다.

>[!NOTE]
>
>이 페이지에 제공된 예제를 시도해 보기 위해 [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl)라는 라이브 실행 환경을 사용할 수 있습니다.

표현식 구문에는 [변수](#variables), [리터럴](#literals), [연산자](#operators), [옵션](#options)이 포함됩니다.

## 변수 {#variables}

변수는 데이터 값이나 개체를 저장하는 컨테이너입니다. 변수의 이름을 식별자라고 합니다.

아무것도 지정하지 않아도 HTL은 `global.jsp`를 포함시킨 후 JSP에서 일반적으로 사용할 수 있었던 모든 개체에 대한 액세스를 제공합니다. [전역 개체](global-objects.md) 페이지는 HTL이 액세스할 수 있는 모든 개체의 목록을 제공합니다.

### 속성 액세스 {#property-access}

점 표기법 또는 대괄호 표기법을 사용하여 변수 속성에 액세스하는 두 가지 방법이 있습니다.

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

대부분의 경우 더 간단한 점 표기법이 선호되어야 하며, 잘못된 식별자 문자가 포함된 속성에 액세스하거나 속성에 동적으로 액세스하려면 대괄호 표기법을 사용해야 합니다. 다음 두 챕터에서는 이 두 가지 경우에 대해 자세히 설명합니다.

액세스된 속성은 함수일 수 있지만 인수 전달은 지원되지 않으므로 getter와 같이 인수가 필요하지 않는 함수만 액세스할 수 있습니다. 이는 표현식에 임베드된 논리의 양을 줄이기 위한 원하는 제한 사항입니다. 필요한 경우 [`data-sly-use`](block-statements.md#use) 문을 사용하여 매개 변수를 논리에 전달할 수 있습니다.

또한 위의 예에서는 `getTitle()`과 같은 Java getter 함수에 `get`을 앞에 추가하지 않고 뒤에 오는 문자를 소문자로 만들어 액세스할 수 있다는 것도 보여 줍니다.

### 유효한 식별자 문자 {#valid-identifier-characters}

식별자라는 변수의 이름은 특정 규칙을 따릅니다. 글자(`A`-`Z` 및 `a`-`z`) 또는 밑줄(`_`)로 시작해야 하며 후속 문자는 숫자(`0`-`9`) 또는 콜론(`:`)일 수도 있습니다. `å` 및 `ü` 같은 유니코드 문자는 식별자에 사용할 수 없습니다.

콜론(`:`) 문자가 AEM 속성 이름에 일반적이라는 점을 감안하여 알맞게 이 문자는 유효한 식별자 문자라는 점을 강조해야 합니다.

`${properties.jcr:title}`

대괄호 표기법을 사용하여 아래 예시의 공백 문자와 같이 잘못된 식별자 문자가 포함된 속성에 액세스할 수 있습니다.

`${properties['my property']}`

### 동적으로 멤버 액세스 {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Null 값의 허용 처리 {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## 리터럴 {#literals}

리터럴은 고정 값을 나타내는 표기법입니다.

### 부울 {#boolean}

부울은 논리적 엔티티를 나타내며 `true` 및 `false`의 두 가지 값을 가질 수 있습니다.

`${true} ${false}`

### 숫자 {#numbers}

숫자 유형은 양의 정수뿐입니다. 부동 소수점 같은 다른 숫자 형식은 변수에서 지원되지만 리터럴로 표현할 수는 없습니다.

`${42}`

### 문자열 {#strings}

문자열은 텍스트 데이터를 나타내며 작은따옴표나 큰따옴표로 묶을 수 있습니다.

`${'foo'} ${"bar"}`

일반 문자 외에 다음과 같은 특수 문자를 사용할 수 있습니다.

* `\\` 백슬래시 문자
* `\'` 작은따옴표(또는 아포스트로피)
* `\"` 큰따옴표
* `\t` 탭
* `\n` 새 줄
* `\r` 캐리지 리턴
* `\f` 폼피드
* `\b` 백스페이스
* `\uXXXX` 4개의 16진수 XXXX로 지정된 유니코드 문자입니다.\
   몇 가지 유용한 유니코드 이스케이프 시퀀스는 다음과 같습니다.

   * `\u0022` 대상 `"`
   * `\u0027` 대상 `'`

위에 나열되지 않은 문자의 경우 백슬래시 문자 앞에 오류가 표시됩니다.

다음은 문자열 이스케이프를 사용하는 방법의 몇 가지 예입니다.

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

HTL이 컨텍스트별 이스케이프를 적용하기 때문에 다음과 같은 결과가 출력됩니다.

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### 배열 {#arrays}

배열은 이름과 인덱스로 참조할 수 있는 정렬된 값 집합입니다. 요소의 유형은 혼합될 수 있습니다.

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

이러한 연산자는 일반적으로 부울 값과 함께 사용되지만 JavaScript에서처럼 실제로 지정된 피연산자 중 하나의 값을 반환하므로 부울이 아닌 값과 함께 사용할 경우 부울이 아닌 값을 반환할 수 있습니다.

값을 `true`로 변환할 수 있는 경우 값은 이른바 truthy입니다. 값을 `false`로 변환할 수 있는 경우 값은 이른바 falsy입니다. `false`로 변환할 수 있는 값은 정의되지 않은 변수, null 값, 숫자 0, 빈 문자열입니다.

#### 논리적 NOT {#logical-not}

`${!myVar}`는 단일 피연산자를 `true`로 변환할 수 있는 경우 `false`를 반환합니다. 그렇지 않으면 `true`를 반환합니다.

예를 들어 하위 페이지가 없는 경우에만 요소를 표시하는 것과 같이 테스트 조건을 반전하는 데 사용할 수 있습니다.

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### 논리적 AND {#logical-and}

`${varOne && varTwo}`은 falsy이면 `varOne`을 반환합니다. 그렇지 않으면 `varTwo`를 반환합니다.

이 연산자는 두 속성의 존재 확인과 같이 한 번에 두 조건을 테스트하는 데 사용할 수 있습니다.

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

HTL은 false 또는 빈 문자열로 평가되는 값이 동적으로 설정된 속성을 제거하기 때문에 논리적 AND 연산자를 사용하여 HTML 속성을 조건부로 표시할 수도 있습니다. 따라서 아래 예에서 `class` 속성은 `logic.showClass`가 truthy이고 `logic.className`이 존재하고 비어 있지 않은 경우에만 표시됩니다.

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### 논리적 OR {#logical-or}

`${varOne || varTwo}`은 truthy이면 `varOne`을 반환합니다. 그렇지 않으면 `varTwo`를 반환합니다.

이 연산자는 하나 이상의 속성이 있는지 확인하는 것과 같이 두 가지 조건 중 하나가 적용되는지 테스트하는 데 사용할 수 있습니다.

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

논리적 OR 연산자는 truthy인 첫 번째 변수를 반환하므로 대체 값을 제공하는 데에도 매우 편리하게 사용할 수 있습니다.

HTL은 false 또는 빈 문자열로 평가되는 표현식에 의해 설정된 값이 있는 속성을 제거하기 때문에 HTML 속성을 조건부로 표시하는 데 사용할 수도 있습니다. 따라서 아래 예는 존재하고 비어 있지 않은 경우 **`properties.jcr:`** 제목을 표시하고, 존재하고 비어 있지 않은 경우 **`properties.jcr:description`**&#x200B;를 표시하는 것으로 대체하고, 그렇지 않으면 “제목 또는 설명이 제공되지 않음” 메시지를 표시합니다.

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### 조건부(삼항) 연산자 {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}`는 `varCondition`이 truthy인 경우 `varOne`을 반환합니다. 그렇지 않으면 `varTwo`를 반환합니다.

이 연산자는 일반적으로 페이지 상태에 따라 다른 메시지를 표시하는 것과 같이 표현식 내에서 조건을 정의하는 데 사용할 수 있습니다.

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>식별자에 콜론 문자도 허용되므로 파서에 명확성을 제공하기 위해 삼항 연산자를 공백으로 구분하는 것이 가장 좋습니다.

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### 비교 연산자 {#comparison-operators}

같음 및 같지 않음 연산자는 유형이 동일한 피연산자만 지원합니다. 유형이 일치하지 않으면 오류가 표시됩니다.

* 문자 시퀀스가 같을 때 문자열은 같습니다.
* 값이 같을 때 숫자는 같습니다.
* 둘 다 `true`이거나 둘 다 `false`인 경우 부울은 같습니다.
* Null 또는 정의되지 않은 변수는 자체 및 서로 같습니다.

`${varOne == varTwo}`는 `varOne`과 `varTwo`가 같으면 `true`를 반환합니다.

`${varOne != varTwo}`는 `varOne`과 `varTwo`가 같지 않으면 `true`를 반환합니다.

관계 연산자는 숫자인 피연산자만 지원합니다. 다른 모든 유형의 경우 오류가 표시됩니다.

`${varOne > varTwo}`는 `varOne`이 `varTwo`보다 큰 경우 `true`를 반환합니다.

`${varOne < varTwo}`는 `varOne`이 `varTwo`보다 작은 경우 `true`를 반환합니다.

`${varOne >= varTwo}`는`varOne`이 `varTwo`보다 크거나 같은 경우 `true`를 반환합니다.

`${varOne <= varTwo}`는`varOne`이 `varTwo`보다 작거나 같은 경우 `true`를 반환합니다.

### 그룹화 괄호 {#grouping-parentheses}

그룹화 연산자 `()`는 표현식의 평가 우선 순위를 제어합니다.

`${varOne && (varTwo || varThree)}`

## 옵션 {#options}

표현식 옵션은 표현식에 작용하여 수정할 수 있습니다. 또는 블록 문과 함께 사용될 때 매개 변수 역할을 합니다.

`@` 뒤의 모든 항목은 옵션입니다.

```xml
${myVar @ optOne}
```

옵션은 변수이거나 리터럴일 수 있는 값을 가질 수 있습니다.

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

옵션만 포함하는 매개 변수 표현식도 가능합니다.

```xml
${@ optOne, optTwo=bar}
```

### 문자열 서식 {#string-formatting}

열거된 자리 표시자 {*n*}을 해당 변수로 바꾸는 옵션:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

## URL 조작 {#url-manipulation}

새로운 URL 조작 집합을 사용할 수 있습니다.

다음의 사용법에 대한 예를 참조하십시오.

html 확장자를 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

html 확장자와 선택자를 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

html 확장자와 조각(#value)을 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

`@extension`은 모든 시나리오에서 작동하며 확장자를 추가할지 여부를 확인합니다.

```xml
${ link @ extension = 'html' }
```

### 숫자/날짜 서식 {#number-date-formatting}

HTL은 사용자 지정 코드를 작성하지 않고도 숫자와 날짜의 기본 서식을 허용합니다. 시간대 및 로케일도 지원합니다.

다음 예는 서식이 지정되었음을 먼저 보여 준 다음 서식 지정이 필요한 값을 보여 줍니다.

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>사용할 수 있는 서식에 대한 자세한 내용은[HTL 사양](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)을 참조하십시오.

### 다국어화 {#internationalization}

현재 [사전](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-translator.html?lang=ko-KR)을 사용하여 문자열을 현재 *원본*(아래 참조)의 언어로 번역합니다. 번역이 없으면 원래 문자열이 사용됩니다.

```xml
${'Page' @ i18n}
```

힌트 옵션은 텍스트가 사용되는 컨텍스트를 지정하여 번역자에게 주석을 제공하는 데 사용할 수 있습니다.

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

언어의 기본 원본은 `resource`이며, 이는 텍스트가 콘텐츠와 동일한 언어로 번역됨을 의미합니다. `user`로 변경할 수 있습니다. 이는 언어가 브라우저 로케일 또는 로그인한 사용자의 로케일에서 사용됨을 의미합니다.

```xml
${'Page' @ i18n, source='user'}
```

명시적 로케일을 제공하면 원본 설정이 재정의됩니다.

```xml
${'Page' @ i18n, locale='en-US'}
```

변환된 문자열에 변수를 임베드하려면 다음 서식 옵션을 사용하면 됩니다.

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### 배열 조인 {#array-join}

기본적으로 배열을 텍스트로 표시할 때 HTL은 공백 없이 쉼표로 구분된 값을 표시합니다.

조인 옵션을 사용하여 다른 구분 기호를 지정합니다.

```xml
${['one', 'two'] @ join='; '}
```

### 표시 컨텍스트 {#display-context}

HTL 표현식의 표시 컨텍스트는 HTML 페이지 구조 내에서의 위치를 나타냅니다. 예를 들어 렌더링되면 텍스트 노드를 생성하는 위치에 표현식이 나타나면 `text` 컨텍스트에 있는 것이고 속성 값 내에서 발견되면 `attribute` 컨텍스트에 있는 것입니다.

스크립트(JS) 및 스타일(CSS) 컨텍스트를 제외하고 HTL은 XSS 보안 문제를 방지하기 위해 표현식의 컨텍스트를 자동으로 감지하고 적절하게 이스케이프합니다. 스크립트 및 CSS의 경우 원하는 컨텍스트 동작을 명시적으로 설정해야 합니다. 또한 자동 동작의 재정의가 필요한 다른 모든 경우에 컨텍스트 동작을 명시적으로 설정할 수도 있습니다.

세 가지 다른 컨텍스트에서 세 가지 변수가 있습니다.

* `properties.link`(`uri` 컨텍스트)
* `properties.title`(`attribute` 컨텍스트)
* `properties.text`(`text` 컨텍스트)

HTL은 해당 컨텍스트의 보안 요구 사항에 따라 이들 각각을 다르게 이스케이프합니다. 다음과 같은 일반적인 경우에는 명시적인 컨텍스트 설정이 필요하지 않습니다.

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

이스케이프 및 XSS 보호도 끌 수 있습니다.

```xml
<div>${myScript @ context='unsafe'}</div>
```

### 컨텍스트 설정 {#context-settings}

| 컨텍스트 | 사용 시기 | 설명 |
|--- |--- |--- |
| `text` | 요소 내부 콘텐츠의 기본값 | 모든 HTML 특수 문자를 인코딩합니다. |
| `html` | 마크업을 안전하게 출력하려면 | AntiSamy 정책 규칙을 충족하도록 HTML을 필터링하고 규칙과 일치하지 않는 항목을 제거합니다. |
| `attribute` | 속성 값의 기본값 | 모든 HTML 특수 문자를 인코딩합니다. |
| `uri` | 링크 및 경로를 표시하려면 href 및 src 속성 값의 기본값 | href 또는 src 속성 값으로 작성하기 위한 URI의 유효성을 검사합니다. 유효성 검사가 실패하면 아무 것도 출력하지 않습니다. |
| `number` | 숫자를 표시하려면 | 정수를 포함하는 URI의 유효성을 검사합니다. 유효성 검사가 실패하면 0을 출력합니다. |
| `attributeName` | 속성 이름을 설정할 때 data-sly-attribute의 기본값 | 속성 이름의 유효성을 검사합니다. 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| `elementName` | data-sly-element의 기본값 | 요소 이름의 유효성을 검사합니다. 유효성 검사가 실패하면 아무 것도 출력하지 않습니다. |
| `scriptToken` | JS 식별자, 리터럴 숫자 또는 리터럴 문자열의 경우 | JavaScript 토큰의 유효성을 검사합니다. 유효성 검사가 실패하면 아무 것도 출력하지 않습니다. |
| `scriptString` | JS 문자열 내 | 문자열에서 벗어나는 문자를 인코딩합니다. |
| `scriptComment` | JS 주석 내 | JavaScript 주석의 유효성을 검사합니다. 유효성 검사가 실패하면 아무 것도 출력하지 않습니다. |
| `styleToken` | CSS 식별자, 숫자, 차원, 문자열, 16진수 색상 또는 함수의 경우. | CSS 토큰의 유효성을 검사합니다. 유효성 검사가 실패하면 아무 것도 출력하지 않습니다. |
| `styleString` | CSS 문자열 내 | 문자열에서 벗어나는 문자를 인코딩합니다. |
| `styleComment` | CSS 주석 내 | CSS 주석의 유효성을 검사합니다. 유효성 검사가 실패하면 아무 것도 출력하지 않습니다. |
| `unsafe` | 위의 어느 것도 작업을 수행하지 않는 경우에만 | 이스케이프 및 XSS 보호를 완전히 비활성화합니다. |
