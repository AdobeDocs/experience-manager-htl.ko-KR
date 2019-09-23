---
title: HTL 표현식 언어
seo-title: HTL 표현식 언어
description: HTML 템플릿 언어는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다.
seo-description: 'HTML 템플릿 언어는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다. '
uuid: 38b4a259-03b5-4847-91c6-e2037760070
contentOwner: 사용자
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: 참조
discoiquuid: 9ba37ca0-f318-48b0-a791-a944a72502ed
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 84ed515309831fe413abf317d8739f2bba79afdb

---


# HTL 표현식 언어 {#htl-expression-language}

HTML 템플릿 언어는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다. 이러한 표현식은 `${` 문자와 `}`로 구분됩니다. 형식이 잘못된 HTML을 방지하기 위해 표현식은 특성 값, 요소 컨텐츠 또는 주석에만 사용할 수 있습니다.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

표현식은 **`\`** 문자로 연기하여 escape할 수 있습니다. 예를 들어 **`\${test}`** 렌더링됩니다 **`${test}`**.

>[!NOTE]
>
>이 페이지에서 제공되는 예제를 살펴보려면 Read Eval Print [Loop라는 라이브 실행 환경을](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) 사용할 수 있습니다.

표현식 구문에는 [변수](#variables), [리터럴](#literals), [연산자](#operators) 및 [optionsFollowing](#options)이 포함됩니다.

## 변수 {#variables}

변수는 데이터 값 또는 개체를 저장하는 컨테이너입니다. 변수의 이름을 식별자라고 합니다.

HTL은 아무 것도 지정하지 않아도 JSP에서 일반적으로 사용할 수 있었던 모든 객체에 대한 액세스 권한을 제공합니다 `global.jsp`. [ [전역](global-objects.md) 객체] 페이지는 HTL에서 액세스할 수 있도록 제공된 모든 객체의 목록을 제공합니다.

### 속성 액세스 {#property-access}

점 표기법 또는 대괄호 표기법을 사용하여 변수의 속성에 액세스하는 방법에는 두 가지가 있습니다.

`${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}`

대부분의 경우 보다 간단한 점 표기법을 선호해야 하며 대괄호 표기법을 사용하여 잘못된 식별자 문자가 포함된 속성에 액세스하거나 속성을 동적으로 액세스해야 합니다. 다음 두 장은 이러한 두 사례에 대한 세부 사항을 제공합니다.

액세스된 속성은 함수일 수 있지만 인수 전달은 지원되지 않으므로 getter와 같이 인수를 예상하지 않는 함수만 액세스할 수 있습니다. 이 제한은 표현식에 임베드된 로직의 양을 줄이기 위한 것입니다. 필요한 경우 [`data-sly-use`](block-statements.md#use) 문을 사용하여 매개 변수를 로직에 전달할 수 있습니다.

위의 예에서 보듯이 Java getter 함수는 prepending 없이, `getTitle()`그리고 다음에 오는 문자의 대/소문자를 **`get`**&#x200B;줄여 액세스할 수 있습니다.

### 유효한 식별자 문자 {#valid-indentifier-characters}

식별자라고 하는 변수의 이름은 특정 규칙을 따릅니다. 문자(**`A`**-**`Z`** 및 **`a`****`z`**-**`_`**) 또는 밑줄(**`0`**)로 시작해야 하며, 그 뒤에 오는 문자도 숫자(**`9`**-**`:`**) 또는 콜론(콜론)으로 시작할 수 있습니다. Unicode letters such as **`å`** and **`ü`** cannot be used in identifiers.

Given that the colon (:) character is common in AEM property names, it is convenient that it is a valid identifier character:****

`${properties.jcr:title}`

The bracket notation can be used to access properties that contain invalid identifier characters, like the space character in the example below:

`${properties['my property']}`

### Accessing Members Dynamically {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### Null 값의 허용적 처리 {#permissive-handling-of-null-values}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${currentPage.lastModified.time.toString}
```

## Literals {#literals}

A literal is a notation for representing a fixed value.

### 부울 {#boolean}

Boolean represents a logical entity and can have two values: , and .**`true`****`false`**

`${true} ${false}`

### Numbers {#numbers}

There is only one number type: positive integers. While other number formats, like floating point, are supported in variables, but cannot be expressed as literals.

`${42}`

### Strings {#strings}

They represent textual data, and can be single or double quoted:

`${'foo'} ${"bar"}`

In addition to ordinary characters, following special characters can be used:

* **`\\`** Backslash character
* **`\'`** 작은 따옴표(또는 아포스트로피)
* **`\"`** 큰따옴표
* **`\t`** 변조
* **`\n`** 새 행
* **`\r`** 캐리지 리턴
* **`\f`** 양식 피드
* **`\b`** 백스페이스
* `\uXXXX` 4개의 16진수 XXXX로 지정된 유니코드 문자입니다.\
   유용한 유니코드 이스케이프 시퀀스는 다음과 같습니다.

   * **\u0022** for **"**
   * **\u0027** for **'**

위에 나열되지 않은 문자의 경우 백슬래시 문자 앞에 오류가 표시됩니다.

다음은 문자열 이스케이프 사용 방법의 몇 가지 예입니다.

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

HTL이 컨텍스트별 escape를 적용하므로 다음 결과가 발생합니다.

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### 배열 {#arrays}

배열은 이름과 인덱스를 사용하여 참조할 수 있는 순차 값 세트입니다. 요소의 유형은 혼합할 수 있습니다.

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

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

이러한 연산자는 일반적으로 부울 값과 함께 사용되지만, JavaScript에서와 마찬가지로 지정된 피연산자 중 하나의 값을 실제로 반환하므로 부울이 아닌 값과 함께 사용하면 부울이 아닌 값을 반환할 수 있습니다.

값을 truthy로 변환할 수 **`true`**&#x200B;있는 경우 이 값을 truthy라고 합니다. 값을 (으)로 변환할 수 **`false`**&#x200B;있는 경우 이 값을 false라고 합니다. 변환할 수 있는 값은 **`false`** 다음과 같습니다.정의되지 않은 변수, null 값, 숫자 0 및 빈 문자열.

#### 논리 NOT {#logical-not}

**`${!myVar}`** 단일 피연산자가 **`false`** 로 변환될 수 있는 `true`경우 반환그렇지 않으면 반환 값이 **`true`**&#x200B;반환됩니다.

예를 들어 하위 페이지가 없는 경우에만 요소를 표시하는 것과 같이 테스트 조건을 변환하는 데 사용할 수 있습니다.

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### 논리 AND {#logical-and}

**`${varOne && varTwo}`** false인 `varOne` 경우 반환그렇지 않으면 varTwo를 **반환합니다**.

이 연산자는 두 속성이 있는지 확인하는 것처럼 두 가지 조건을 한 번에 테스트하는 데 사용할 수 있습니다.

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

HTL은 동적으로 설정된 값이 있는 속성을 false로 제거하거나 빈 문자열로 제거하므로 AND 논리 연산자를 사용하여 HTML 속성을 조건부로 표시할 수도 있습니다. So in the example below, the  attribute is only shown if  is truthy and if  exists and is not empty:**`class`****`logic.showClass`****`logic.className`**

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### Logical OR {#logical-or}

**`${varOne || varTwo}`** returns varOne if it is truthy; otherwise, returns varTwo.********

This operator can be used to test if one of two conditions apply, like verifying the existence of at least one property:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

As the logical OR operator returns the first variable that is truthy, it can also very conveniently be used to provide fallback values.

conditionally display HTML attributes, because HTL removes attributes with values set by expressions that evaluate to false or to an empty string. So the example below will display ****title if it exists and is not empty, else it falls back to dislaying  if it exists and is not empty, else it will display the message "no title or description provided":`properties.jcr:`**`properties.jcr:description`**

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Conditional (ternary) Operator {#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** returns  if  is truthy; otherwise it returns .**`varOne`****`varCondition`****`varTwo`**

This operator can typically be used to define conditions within expressions, like displaying a different message based on the status of the page:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

An important note, since colon characters are also permitted in identifiers, it is best to separate the ternary operators with a white space to provide clarity to the parser:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Comparison Operators {#comparison-operators}

항등 연산자는 동일한 유형의 피연산자만 지원합니다. 유형이 일치하지 않으면 오류가 표시됩니다.

* 문자열이 동일한 문자 시퀀스를 가질 때 동일해집니다.
* Numbers are equal when they have the same value
* Booleans are equal if both are  or both are .**`true`****`false`**

* Null 또는 undefined 변수는 자신과 서로 같습니다.

**`${varOne == varTwo}`** returns  if  and  are equal.**`true`****`varOne`****`varTwo`**

**`${varOne != varTwo}`** 과 같지 **`true`** 않은 **`varOne`** **`varTwo`** 경우 을 반환합니다.

관계형 연산자는 숫자인 피연산자만 지원합니다. 다른 모든 유형의 경우 오류가 표시됩니다.

**`${varOne > varTwo}`** 보다 큰 **`true`** 경우 **`varOne`** 을 반환합니다 **`varTwo`**.

**`${varOne < varTwo}`** 보다 작은 **`true`** 경우 **`varOne`** 을 반환합니다 **`varTwo`**.

**`${varOne >= varTwo}`** 보다 크거나 **`true`** 같은 **`varOne`** 경우 을 **`varTwo`**&#x200B;반환합니다.

**`${varOne <= varTwo}`** 작거나 같은 **`true`** 경우 **`varOne`** 을 **`varTwo`**&#x200B;반환합니다.

### 그룹화 괄호 {#grouping-parentheses}

그룹화 연산자는 표현식의 평가 우선 순위를 **`(`** **`)`** 제어합니다.

`${varOne && (varTwo || varThree)}`

## 옵션 {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

표현식 옵션은 표현식에 대해 작동하고 이를 수정하거나 블록 문과 함께 사용할 때 매개 변수로 사용할 수 있습니다.

이후의 모든 **`@`** 것은 선택 사항입니다.

```xml
${myVar @ optOne}
```

옵션에는 변수 또는 리터럴일 수 있는 값이 있을 수 있습니다.

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

열거된 자리 표시자 {*n*}을(를) 해당 변수로 대체하는 옵션:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

### 국제화 {#internationalization}

현재 *사전을* 사용하여 문자열을 현재 소스의 [](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator)언어로 변환합니다(아래 참조). 번역이 없으면 원래 문자열이 사용됩니다.

```xml
${'Page' @ i18n}
```

힌트 옵션은 텍스트가 사용되는 컨텍스트를 지정하여 변환기에 대한 설명을 제공하는 데 사용할 수 있습니다.

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

언어의 기본 소스는 'resource'입니다. 즉, 텍스트가 컨텐츠와 동일한 언어로 번역됩니다. 이를 '사용자'로 변경할 수 있습니다. 즉, 브라우저 로케일 또는 로그인한 사용자의 로케일에서 언어를 가져옵니다.

```xml
${'Page' @ i18n, source='user'}
```

명시적인 로케일을 제공하면 소스 설정이 무시됩니다.

```xml
${'Page' @ i18n, locale='en-US'}
```

변환된 문자열에 변수를 포함하려면 형식 옵션을 사용할 수 있습니다.

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### 배열 연결 {#array-join}

기본적으로 배열을 텍스트로 표시할 때 HTL은 쉼표로 구분된 값(간격 없음)을 표시합니다.

연결 옵션을 사용하여 다른 구분 기호를 지정합니다.

```xml
${['one', 'two'] @ join='; '}
```

### 컨텍스트 표시 {#display-context}

HTL 표현식의 표시 컨텍스트는 HTML 페이지 구조 내의 위치를 나타냅니다. 예를 들어, 표현식이 한 번 렌더링되면 텍스트 노드를 생성하는 위치에 나타나면 **`text`** 컨텍스트에 있다고 합니다. 속성의 값 내에 있는 경우, 컨텍스트 내에 있는 것으로 **`attribute`** 간주됩니다.

스크립트(JS) 및 스타일(CSS) 컨텍스트를 제외하고 HTL은 XSS 보안 문제를 방지하기 위해 표현식 컨텍스트를 자동으로 감지하고 적절하게 escape합니다. 스크립트 및 CSS의 경우 원하는 컨텍스트 동작을 명시적으로 설정해야 합니다. 또한, 자동 동작의 재정의가 필요한 다른 경우에는 컨텍스트 동작을 명시적으로 설정할 수도 있습니다.

여기에는 세 가지 다른 컨텍스트 내에 세 가지 변수가 있습니다.( **`properties.link`** 컨텍스트), `uri` ( **`properties.title`** 컨텍스트) 및&#x200B;**`attribute`** ( **`properties.text`****`text`** 컨텍스트). HTL은 각 컨텍스트의 보안 요구 사항에 따라 각각 다르게 이스케이프 처리됩니다. 다음과 같은 일반적인 경우에는 명시적 컨텍스트 설정이 필요하지 않습니다.

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

이스케이프 및 XSS 보호 기능을 끌 수도 있습니다.

```xml
<div>${myScript @ context='unsafe'}</div>
```

### 컨텍스트 설정 {#context-settings}

| 컨텍스트 | 사용 시기 | 기능 |
|--- |--- |--- |
| text | 요소 내의 컨텐츠 기본값 | 모든 HTML 특수 문자를 인코딩합니다. |
| html | 마크업을 안전하게 출력하려면 | HTML을 필터링하여 AntiSamy 정책 규칙에 부합하지 않는 항목을 제거합니다. |
| attribute | 속성 값의 기본값 | 모든 HTML 특수 문자를 인코딩합니다. |
| uri | href 및 src 속성 값에 대한 링크 및 경로 기본값을 표시하려면 | URI를 href 또는 src 속성 값으로 쓸 수 있는지 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| 개수 | 숫자를 표시하려면 | 유효성 검사가 실패할 경우 0을 출력하여 정수를 포함하는지 URI를 확인합니다. |
| attributeName | 속성 이름을 설정할 때 data-sly-attribute에 대한 기본값 | 속성 이름을 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
|  elementName | Default for data-sly-element | 요소 이름을 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| scriptToken | JS 식별자, 리터럴 숫자 또는 리터럴 문자열 | JavaScript 토큰의 유효성을 검사하며 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| scriptString | JS 문자열 내 | 문자열에서 벗어나는 문자를 인코딩합니다. |
| scriptComment | JS 주석 내 | 유효성 검사에 실패하면 JavaScript 주석을 확인하고 출력하지 않습니다. |
| styleToken | CSS 식별자의 경우 숫자, 크기, 문자열, 16진수 색상 또는 함수입니다. | CSS 토큰의 유효성을 검사하며 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| styleString | CSS 문자열 내 | 문자열에서 벗어나는 문자를 인코딩합니다. |
| styleComment | CSS 주석 내 | CSS 주석을 확인하고 유효성 검사에 실패하면 아무 것도 출력하지 않습니다. |
| 안전하지 않음 | 위의 작업 중 어느 것도 작업을 수행하지 않는 경우에만 | 이스케이프 및 XSS 보호를 완전히 비활성화합니다. |

