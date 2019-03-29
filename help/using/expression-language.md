---
title: HTL 표현식 언어
seo-title: HTL 표현식 언어
description: HTML 템플릿 언어는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다.
seo-description: HTML 템플릿 언어는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다.
uuid: 38 B 4 A 259-03 B 5-4847-91 C 6-E 20377600070
contentOwner: 사용자
products: sg_ Experiencemanager/HTL
topic-tags: HTML-template-language
content-type: 참조
discoiquuid: 9 BA 37 CA 0-F 318-48 B 0-A 791-A 944 A 72502 ED
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# HTL 표현식 언어 {#htl-expression-language}

HTML 템플릿 언어는 표현식 언어를 사용하여 HTML 출력의 동적 요소를 제공하는 데이터 구조에 액세스합니다. 이러한 표현식은 문자와 `${``}`숫자로 구분됩니다. 형식이 잘못된 HTML를 피하려면 표현식은 속성 값, 요소 컨텐츠 또는 주석에서만 사용할 수 있습니다.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

예를 들어, **`\`** 문자가 렌더링되면 표현식을 이스케이프 처리하여 이스케이프 처리할 **`\${test}`****`${test}`**수 있습니다.

>[!NOTE]
>
>이 페이지에 제공된 예제를 사용해 보려면 [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) 라는 라이브 실행 환경을 사용할 수 있습니다.

표현식 구문에는 [변수](#variables), [리터럴](#literals), [연산자](#operators) 및 [옵션이 포함됩니다](#options).

## 변수 {#variables}

변수는 데이터 값이나 개체를 저장하는 컨테이너입니다. 변수의 이름을 식별자라고 합니다.

HTL는 아무 것도 지정하지 않아도, 포함 후 JSP에서 일반적으로 사용할 수 `global.jsp`있었던 모든 개체에 대한 액세스를 제공합니다. [전역 개체](global-objects.md) 페이지는 HTL에서 액세스할 수 있는 모든 개체 목록을 제공합니다.

### 속성 액세스 {#property-access}

도트 표기법을 사용하거나 괄호 표기법을 사용하여 변수 속성에 액세스하는 방법에는 두 가지가 있습니다.

`${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}`

대부분의 경우 간단한 점 표기법을 선호해야 하며, 괄호 표기법을 사용하여 잘못된 식별자 문자가 포함된 속성에 액세스하거나 속성에 동적으로 액세스할 수 있어야 합니다. 다음 두 장에서는 이러한 두 가지 사례에 대한 세부 정보를 제공합니다.

액세스한 속성은 함수일 수 있지만 인수를 전달하는 것은 지원되지 않으므로 인수를 getters 처럼 예상하지 못하는 함수만 사용할 수 있습니다. 이것은 표현식에 포함된 로직 양을 줄이기 위한 것입니다. 필요한 경우 [`data-sly-use`](block-statements.md#use) 문을 사용하여 매개 변수에 매개 변수를 전달할 수 있습니다.

위의 예에서, Java Getter 함수에는을 프리대기하지 않고 `getTitle()`액세스할 수 있고 다음에 나오는 **`get`**문자의 대/소문자를 낮춰서 액세스할 수 있습니다.

### 유효한 식별자 문자 {#valid-indentifier-characters}

변수라는 변수의 이름은 특정 규칙에 적용됩니다. they must start with a letter (**`A`**-**`Z`** and **`a`**-)**`z`**, or an underscore (**`_`**), and subsequent characters can be digit (**`0`**-**`9`**) or colon (**`:`**). 식별자와 같은 **`å`** 유니코드 문자는 **`ü`** 식별자에서 사용할 수 없습니다.

AEM 속성 이름에서 콜론 (**:**) 문자가 공통인 경우에는 올바른 식별자 문자인 것이 편리합니다.

`${properties.jcr:title}`

아래 예에서 공백 문자와 같이 잘못된 식별자 문자를 포함하는 속성에 대괄호 표기법을 사용할 수 있습니다.

`${properties['my property']}`

### 동적으로 멤버 액세스 {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### null 값 처리 {#permissive-handling-of-null-values}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${currentPage.lastModified.time.toString}
```

## 리터럴 {#literals}

리터럴은 고정된 값을 나타내는 표표입니다.

### 부울 {#boolean}

Boolean는 논리 엔티티를 나타내며 두 개의 값을 가질 수 있습니다. **`true`****`false`**및.

`${true} ${false}`

### numbers {#numbers}

There are only one number type: 양의 정수. 부동 소수처럼 다른 숫자 형식은 변수에서 지원되지만 리터럴로 표현할 수는 없습니다.

`${42}`

### 문자열 {#strings}

이들은 텍스트 데이터를 나타내며, 단일 또는 이중 인용될 수 있습니다.

`${'foo'} ${"bar"}`

일반 문자 외에도 다음 특수 문자를 사용할 수 있습니다.

* **`\\`** 백슬래시 문자
* **`\'`** 작은따옴표 (또는 아포스트로피)
* **`\"`** 큰따옴표
* **`\t`** Tabation
* **`\n`** 새 행
* **`\r`** 캐리지 리턴
* **`\f`** 양식 피드
* **`\b`** 백스페이스
* `\uXXXX` 네 자리 16 진수 XXXX로 지정된 유니코드 문자입니다.\
   유용한 일부 유니코드 이스케이프 시퀀스는 다음과 같습니다.

   * **\u0022** for **"**
   * **\u0027** for **'**

For characters not listed above, preceding a backslash caracter will display an error.

Here are some examples of how to use string escaping:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

which will result in following output, because HTL will apply context-specific escaping:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Arrays {#arrays}

An array is an ordered set of values that can be referred to with a name and an index. The types of its elements can be mixed.

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${[1,2,3,4]}
${myArray[2]}
```

Arrays are useful to provide a list of values from the template.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operators {#operators}

### Logical Operators {#logical-operators}

These operators are typically used with Boolean values, however, like in JavaScript, they actually return the value of one of the specified operands, so when used with non-Boolean values, they may return a non-Boolean value.

If a value can be converted to **`true`**, the value is so-called truthy. If a value can be converted to **`false`**, the value is so-called falsy. Values that can be converted to **`false`** are: undefined variables, null values, the number zero, and empty strings.

#### Logical NOT {#logical-not}

**`${!myVar}`** returns **`false`** if its single operand can be converted to `true`; otherwise, returns **`true`**.

This can for instance be used to invert a test condition, like displaying an element only if there are no child pages:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### Logical AND {#logical-and}

**`${varOne && varTwo}`** returns `varOne` if it is falsy; otherwise, returns **varTwo**.

This operator can be used to test two conditions at once, like verifying the existence of two properties:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

The logical AND operator can also be used to conditionally display HTML attributes, because HTL removes attributes with values set dynamically that evaluate to false, or to an empty string. So in the example below, the **`class`** attribute is only shown if **`logic.showClass`** is truthy and if **`logic.className`** exists and is not empty:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### Logical OR {#logical-or}

**`${varOne || varTwo}`** returns **varOne** if it is truthy; otherwise, returns **varTwo**.

This operator can be used to test if one of two conditions apply, like verifying the existence of at least one property:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

As the logical OR operator returns the first variable that is truthy, it can also very conveniently be used to provide fallback values.

conditionally display HTML attributes, because HTL removes attributes with values set by expressions that evaluate to false or to an empty string. So the example below will display **`properties.jcr:`**title if it exists and is not empty, else it falls back to dislaying **`properties.jcr:description`** if it exists and is not empty, else it will display the message "no title or description provided":

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Conditional (ternary) Operator {#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** returns **`varOne`** if **`varCondition`** is truthy; otherwise it returns **`varTwo`**.

This operator can typically be used to define conditions within expressions, like displaying a different message based on the status of the page:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

An important note, since colon characters are also permitted in identifiers, it is best to separate the ternary operators with a white space to provide clarity to the parser:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Comparison Operators {#comparison-operators}

The equality and inequality operators only support operands that are of identical types. When the types don't match, an error is displayed.

* Strings are equal when they have the same sequence of characters.
* Numbers are equal when they have the same value
* Booleans are equal if both are **`true`** or both are **`false`**.

* Null or undefined variables are equal to themselves and to each other.

**`${varOne == varTwo}`** returns **`true`** if **`varOne`** and **`varTwo`** are equal.

**`${varOne != varTwo}`** returns **`true`** if **`varOne`** and **`varTwo`** are not equal.

The relational operators only support operands that are numbers. For all other types, an error is displayed.

**`${varOne > varTwo}`** returns **`true`** if **`varOne`** is greater than **`varTwo`**.

**`${varOne < varTwo}`** returns **`true`** if **`varOne`** is smaller than **`varTwo`**.

**`${varOne >= varTwo}`** returns **`true`** if **`varOne`** is greater or equal to **`varTwo`**.

**`${varOne <= varTwo}`** returns **`true`** if **`varOne`** is smaller or equal to **`varTwo`**.

### Grouping parentheses {#grouping-parentheses}

The grouping operator **`(`** **`)`** controls the precedence of evaluation in expressions.

`${varOne && (varTwo || varThree)}`

## 옵션 {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

Expression options can act on the expression and modify it, or serve as parameters when used in conjunction with block statements.

Everything after the **`@`** is an option:

```xml
${myVar @ optOne}
```

Options can have a value, which may be a variable or a literal:

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

Multiple options are separated by commas:

```xml
${myVar @ optOne, optTwo=bar}
```

Parametric expressions containing only options are also possible:

```xml
${@ optOne, optTwo=bar}
```

### String Formatting {#string-formatting}

Option that replaces the enumerated placeholders, {*n*}, with the corresponding variable:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

### Internationalization {#internationalization}

Translates the string to the language of the current *source* (see below), using the current [dictionary](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator). If no translation is found, the original string is used.

```xml
${'Page' @ i18n}
```

The hint option can be used to provide a comment for translators, specifying the context in which the text is used:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

The default source for the language is 'resource', meaning that the text gets translated to the same language as the content. This can be changed to 'user', meaning that the language is taken from the browser locale or from the locale of the logged-in user:

```xml
${'Page' @ i18n, source='user'}
```

Providing an explicit locale overrides the source settings:

```xml
${'Page' @ i18n, locale='en-US'}
```

To embed variables into a translated string, the format option can be used:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Array Join {#array-join}

By default, when displaying an array as text, HTL will display comma separated values (without spacing).

Use the join option to specify a different separator:

```xml
${['one', 'two'] @ join='; '}
```

### Display Context {#display-context}

The display context of a HTL expression refers to its location within the structure of the HTML page. For example, if the expression appears in place that would produce a text node once rendered, then it is said to be in a **`text`** context. If it is found within the value of an attribute, then it is said to be in an **`attribute`** context, and so forth.

With the exception of script (JS) and style (CSS) contexts, HTL will automatically detect the context of expressions and escape them appropriately, to prevent XSS security problems. In the case of scripts and CSS, the desired context behavior must be explicitly set. Additionally, the context behavior can also be explicitly set in any other case where an override of the automatic behavior is desired.

Here we have three variables in three different contexts: **`properties.link`** ( `uri` context), **`properties.title`** (**`attribute`** context) and **`properties.text`**(**`text`** context). HTL will escape each of these differently in accordance with the security requirements of their respective contexts. No explicit context setting is required in normal cases such as this one:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

To safely output markup (that is, where the expression itself evaluates to HTML), the `html` context is used:

```xml
<div>${properties.richText @ context='html'}</div>
```

Explicit context must be set for style contexts:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

Explicit context must be set for script contexts:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

Escaping and XSS protection can also be turned off:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Context Settings {#context-settings}

| 컨텍스트 | When to use | What it does |
|--- |--- |--- |
| text | Default for content inside elements | Encodes all HTML special characters. |
| html | To safely output markup | Filters HTML to meet the AntiSamy policy rules, removing what doesn't match the rules. |
| attribute | Default for attribute values | Encodes all HTML special characters. |
| uri | To display links and paths Default for href and src attribute values | Validates URI for writing as an href or src attribute value, outputs nothing if validation fails. |
| 개수 | To display numbers | Validates URI for containing an integer, outputs zero if validation fails. |
| attributeName | Default for data-sly-attribute when setting attribute names | Validates the attribute name, outputs nothing if validation fails. |
| elementName | Default for data-sly-element | Validates the element name, outputs nothing if validation fails. |
| scriptToken | For JS identifiers, literal numbers, or literal strings | Validates the JavaScript token, outputs nothing if validation fails. |
| scriptString | Within JS strings | Encodes characters that would break out of the string. |
| scriptComment | Within JS comments | Validates the JavaScript comment, outputs nothing if validation fails. |
| styleToken | For CSS identifiers, numbers, dimensions, strings, hex colours or functions. | Validates the CSS token, outputs nothing if validation fails. |
| styleString | Within CSS strings | Encodes characters that would break out of the string. |
| styleComment | Within CSS comments | Validates the CSS comment, outputs nothing if validation fails. |
| unsafe | Only if none of the above does the job | Disables escaping and XSS protection completely. |

