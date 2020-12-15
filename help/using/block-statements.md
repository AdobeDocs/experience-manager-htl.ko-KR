---
title: HTL 블록 문
description: HTML 템플릿 언어(HTL) 블록 문은 기존 HTML에 직접 추가되는 사용자 지정 데이터 특성입니다.
translation-type: tm+mt
source-git-commit: 2336ce8860056f5bc6470b23ee4cda135f7107d8
workflow-type: tm+mt
source-wordcount: '1555'
ht-degree: 1%

---


# HTL 블록 문 {#htl-block-statements}

HTML 템플릿 언어(HTL) 블록 문은 기존 HTML에 직접 추가된 사용자 지정 `data` 속성입니다. 이렇게 하면 프로토타입 정적 HTML 페이지에 대한 쉽고 완벽한 주석을 달 수 있으므로 HTML 코드의 유효성을 깨뜨리지 않고 기능적인 동적 템플릿으로 변환할 수 있습니다.

## 블록 개요 {#overview}

HTL 블록 플러그인은 HTML 요소에 설정된 `data-sly-*` 특성으로 정의됩니다. 요소에는 닫기 태그가 있거나 자체 닫기 태그가 있을 수 있습니다. 속성에는 값(정적 문자열 또는 표현식이 될 수 있음)이 있거나 단순히 부울 속성(값 없이)이 될 수 있습니다.

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

평가된 모든 `data-sly-*` 속성은 생성된 마크업에서 제거됩니다.

### 식별자 {#identifiers}

블록 문 뒤에는 식별자가 올 수도 있습니다.

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

다음 예제는 블록 명령문에서 식별자를 다양한 방법으로 사용할 수 있습니다.

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

최상위 수준 식별자는 대/소문자를 구분하지 않는 HTML 속성을 통해 설정할 수 있으므로 대/소문자를 구분하지 않지만 모든 속성은 대/소문자를 구분합니다.

## 사용 가능한 블록 문 {#available-block-statements}

사용할 수 있는 블록 명령문이 여러 개 있습니다. 동일한 요소에서 사용할 경우 다음 우선순위 목록은 블록 명령문이 평가되는 방식을 정의합니다.

1. `data-sly-template`
1. `data-sly-set`,  `data-sly-test`  `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`,  `data-sly-include`  `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

두 블록 명령문의 우선 순위가 같은 경우 평가 순서는 왼쪽에서 오른쪽으로 지정됩니다.

### {#use} 사용

`data-sly-use` helper 객체(JavaScript 또는 Java에 정의됨)를 초기화하고 변수를 통해 표시합니다.

소스 파일이 템플릿과 동일한 디렉토리에 있는 JavaScript 객체를 초기화합니다. 파일 이름을 사용해야 합니다.

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

소스 파일이 템플릿과 동일한 디렉토리에 있는 Java 클래스를 초기화합니다. 클래스 이름은 파일 이름이 아니라 사용해야 합니다.

```xml
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```

OSGi 번들의 일부로 해당 클래스가 설치된 Java 클래스를 초기화합니다. 정규화된 클래스 이름을 사용해야 합니다.

```xml
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

옵션을 사용하여 매개변수를 초기화에 전달할 수 있습니다. 일반적으로 이 기능은 `data-sly-template` 블록 내에 있는 HTL 코드에서만 사용해야 합니다.

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

다른 HTL 템플릿을 초기화하여 `data-sly-call`을 사용하여 호출할 수 있습니다.

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Use-API에 대한 자세한 내용은 다음을 참조하십시오.
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)


#### {#data-sly-use-with-resources} 리소스를 사용한 데이터-사용

이렇게 하면 `data-sly-use`이(가) 있는 HTL에서 바로 리소스를 가져올 수 있으며 리소스를 얻기 위해 코드를 작성할 필요가 없습니다.

예:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>섹션 [경로가 항상 필요하지 않습니다.](#path-not-required)

### {#unwrap} 랩 취소

`data-sly-unwrap` 컨텐츠는 그대로 유지하면서 생성된 마크업에서 호스트 요소를 제거합니다. 따라서 HTL 프레젠테이션 로직의 일부이지만 실제 출력에서는 원하지 않는 요소를 제외할 수 있습니다.

그러나 이 말은 제한적으로 사용해야 한다. 일반적으로 HTL 마크업을 원하는 출력 마크업에 최대한 가깝게 유지하는 것이 좋습니다. 즉, HTL 블록 명령문을 추가할 때 새로운 요소를 적용하지 않고도 가능한 한 기존 HTML에 주석을 답니다.

예를 들어,

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

생산

```xml
<p>Hello World</p>
```

반면에,

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

생산

```xml
Hello World
```

요소를 조건부로 래핑하지 않을 수도 있습니다.

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

###  설정{#set}

`data-sly-set` 사전 정의된 값으로 새 식별자를 정의합니다.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### text {#text}

`data-sly-text` 호스트 요소의 컨텐츠를 지정된 텍스트로 바꿉니다.

예를 들어,

```xml
<p>${properties.jcr:description}</p>
```

은(는)

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

둘 다 `jcr:description` 값을 단락 텍스트로 표시합니다. 두 번째 방법은 원래 디자이너의 정적 자리 표시자 컨텐츠를 유지하면서 HTML의 완벽한 주석을 달 수 있는 것입니다.

### attribute {#attribute}

`data-sly-attribute` 속성을 호스트 요소에 추가합니다.

예를 들어,

```xml
<div title="${properties.jcr:title}"></div>
```

은(는)

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

둘 다 `title` 속성을 `jcr:title` 값으로 설정합니다. 두 번째 방법은 원래 디자이너의 정적 자리 표시자 컨텐츠를 유지하면서 HTML의 완벽한 주석을 달 수 있는 것입니다.

속성은 속성(리터럴 또는 `data-sly-attribute`을 통해 정의됨)의 가장 오른쪽 인스턴스가 해당 왼쪽에 정의된 동일한 속성(문자 그대로 또는 `data-sly-attribute`을 통해 정의됨)의 인스턴스보다 우선하므로 속성을 왼쪽에서 오른쪽으로 확인합니다.

값이 빈 문자열로 평가되는 속성(`literal` 또는 `data-sly-attribute`을 통해 설정)은 최종 마크업에서 제거됩니다. 이 규칙의 한 가지 예외는 리터럴 빈 문자열에 설정된 리터럴 속성이 보존된다는 것입니다. 예,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

producer,

```xml
<div></div>
```

하지만

```xml
<div class="" data-sly-attribute.id=""></div>
```

producer,

```xml
<div class=""></div>
```

여러 특성을 설정하려면 특성 및 해당 값에 해당하는 키-값 쌍을 가진 지도 객체를 전달합니다. 예를 들어

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

그런 다음,

```xml
<div data-sly-attribute="${attrMap}"></div>
```

producer,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

### 요소 {#element}

`data-sly-element` 호스트 요소의 요소 이름을 대체합니다.

예,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

`h1`을 `titleLevel` 값으로 대체합니다.

보안상의 이유로 `data-sly-element`은(는) 다음 요소 이름만 허용합니다.

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

다른 요소를 설정하려면 XSS 보안을 해제해야 합니다( `@context='unsafe'`).

### 테스트 {#test}

`data-sly-test` 호스트 요소와 컨텐츠를 조건부로 제거합니다. `false` 값은 요소를 제거합니다.`true` 값은 요소를 유지합니다.

예를 들어 `p` 요소와 해당 컨텐츠는 `isShown`이 `true`인 경우에만 렌더링됩니다.

```xml
<p data-sly-test="${isShown}">text</p>
```

테스트 결과를 나중에 사용할 수 있는 변수에 할당할 수 있습니다. 명시적 다른 문이 없으므로 일반적으로 &quot;if&quot; 논리를 구성하는 데 사용됩니다.

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

한 번 설정되면 이 변수는 HTL 파일 내에 전역 범위가 있습니다.

다음은 값 비교의 몇 가지 예입니다.

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div>

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

### 반복 {#repeat}

`data-sly-repeat`을 사용하면 지정된 목록을 기반으로 요소를 여러 번 반복할 수 있습니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

컨테이너 요소가 필요하지 않는다는 점을 제외하면 `data-sly-list`과 동일한 방식으로 작동합니다.

다음 예제에서는 속성에 대해 *item*&#x200B;을 참조할 수도 있음을 보여 줍니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list` 제공된 객체의 각 enumerable 속성에 대한 호스트 요소의 컨텐츠를 반복합니다.

다음은 간단한 루프입니다.

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

목록 범위 내에서 다음 기본 변수를 사용할 수 있습니다.

* `item`:반복의 현재 항목입니다.
* `itemList`:다음 속성을 포함하는 개체:
* `index`:0부터 시작하는 카운터(  `0..length-1`).
* `count`:1기반 카운터(  `1..length`).
* `first`: `true` 현재 항목이 첫 번째 항목인 경우.
* `middle`: `true` 현재 항목이 첫 번째도 마지막 항목이 아닌 경우.
* `last`: `true` 현재 항목이 마지막 항목인 경우.
* `odd`: `true` 홀수 `index` 인 경우
* `even`: `true` 정품일  `index` 경우

`data-sly-list` 문에서 식별자를 정의하면 `itemList` 및 `item` 변수의 이름을 변경할 수 있습니다. `item` 이  `<variable>` 되고  `itemList` 또 그렇게 될 것이다 `<variable>List`.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

속성을 동적으로 액세스할 수도 있습니다.

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

### 리소스 {#resource}

`data-sly-resource` 는 슬링 해상도 및 렌더링 프로세스를 통해 지정된 리소스를 렌더링한 결과를 포함합니다.

간단한 리소스는 다음과 같습니다.

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### 경로가 항상 필요하지는 않음 {#path-not-required}

이미 리소스가 있는 경우에는 `data-sly-resource`이(가) 있는 경로를 사용할 필요가 없습니다. 이미 리소스가 있는 경우 직접 사용할 수 있습니다.

예를 들어, 다음은 올바른 경우입니다.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

하지만 다음 사항도 완벽하게 수용할 수 있다.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

다음 이유로 가능한 경우 리소스를 직접 사용하는 것이 좋습니다.

* 이미 리소스가 있는 경우 경로를 사용하여 다시 확인하는 것은 불필요한 작업입니다.
* 리소스를 이미 보유하고 있는 경로를 사용하면 Sling 리소스를 래핑하거나 합성할 수 있으며 해당 경로에서 제공되지 않을 수 있으므로 예기치 않은 결과가 발생할 수 있습니다.

#### 옵션 {#resource-options}

옵션을 사용하면 다음과 같은 여러 추가 변형을 사용할 수 있습니다.

리소스 경로를 조작합니다.

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

선택기를 추가(또는 바꾸기):

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

여러 선택기를 추가, 교체 또는 제거합니다.

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

기존 선택기에 선택기를 추가합니다.

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

기존 선택기에서 일부 선택기를 제거합니다.

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

모든 선택기 제거:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

리소스의 리소스 유형을 무시합니다.

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

WCM 모드를 변경합니다.

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

기본적으로 AEM 데코레이션 태그는 비활성화되며 데코레이션 태그 이름 옵션을 사용하면 다시 가져올 수 있으며 cssClassName을 사용하여 해당 요소에 클래스를 추가할 수 있습니다.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM은 포함된 요소를 둘러싸는 데코레이션 태그를 제어하는 명확하고 간단한 로직을 제공합니다. 자세한 내용은 개발 구성 요소 설명서의 [데코레이션 태그](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/decoration-tag.html)를 참조하십시오.

### 포함 {#include}

`data-sly-include` 호스트 요소의 컨텐츠를 지정된 HTML 템플릿 파일(HTL, JSP, ESP 등)에 의해 생성된 마크업으로 바꿉니다. 해당 템플릿 엔진에서 처리하는 경우. 포함된 파일의 렌더링 컨텍스트에는 포함 파일의 현재 HTL 컨텍스트가 포함되지 않습니다.따라서 HTL 파일을 포함하려면 현재 `data-sly-use`이 포함된 파일에서 반복되어야 합니다(이 경우 일반적으로 `data-sly-template` 및 `data-sly-call` 사용).

간단한 포함:

```xml
<section data-sly-include="path/to/template.html"></section>
```

JSP는 다음과 같은 방식으로 포함할 수 있습니다.

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

옵션을 사용하면 파일의 경로를 조작할 수 있습니다.

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

WCM 모드를 변경할 수도 있습니다.

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

### 요청 특성 {#request-attributes}

`data-sly-include` 및 `data-sly-resource`에서 `requestAttributes`를 전달하여 수신 HTL 스크립트에서 사용할 수 있습니다.

이를 통해 스크립트 또는 구성 요소에 매개 변수를 올바르게 전달할 수 있습니다.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings"
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Settings 클래스의 Java 코드로, 맵은 requestAttributes를 전달하는 데 사용됩니다.

```xml
public class Settings extends WCMUsePojo {

  // used to pass is requestAttributes to data-sly-resource
  public Map<String, Object> settings = new HashMap<String, Object>();

  @Override
  public void activate() throws Exception {
    settings.put("layout", "flex");
  }
}
```

예를 들어 Sling-Model을 통해 지정된 `requestAttributes`의 값을 사용할 수 있습니다.

이 예에서 레이아웃은 Use-class의 Map을 통해 삽입됩니다.

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### 템플릿 및 호출 {#template-call}

템플릿 블록은 함수 호출과 같이 사용할 수 있습니다.선언에서 매개 변수를 얻을 수 있으며, 이를 호출하면 매개 변수를 전달할 수 있습니다. 재귀를 허용합니다.

`data-sly-template` 템플릿을 정의합니다. 호스트 요소 및 해당 컨텐츠가 HTL로 출력되지 않습니다.

`data-sly-call` 데이터 전용 템플릿으로 정의된 템플릿을 호출합니다. 호출된 템플릿의 컨텐츠(선택적으로 매개 변수화된)는 호출의 호스트 요소의 컨텐츠를 대체합니다.

정적 템플릿을 정의한 다음 이를 호출합니다.

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

동적 템플릿을 정의한 다음 매개 변수를 사용하여 호출합니다.

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

다른 파일에 있는 템플릿은 `data-sly-use`으로 초기화할 수 있습니다. 이 경우 `data-sly-use` 및 `data-sly-call`도 동일한 요소에 배치할 수 있습니다.

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

템플릿 재귀가 지원됩니다.

```xml
<template data-sly-template.nav="${ @ page}">
    <ul data-sly-list="${page.listChildren}">
        <li>
            <div class="title">${item.title}</div>
            <div data-sly-call="${nav @ page=item}" data-sly-unwrap></div>
        </li>
    </ul>
</template>
<div data-sly-call="${nav @ page=currentPage}" data-sly-unwrap></div>
```

## sly 요소 {#sly-element}

`<sly>` HTML 태그를 사용하여 현재 요소를 제거할 수 있으므로 해당 하위 요소만 표시할 수 있습니다. 이 기능은 `data-sly-unwrap` 블록 요소와 유사합니다.

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

유효한 HTML 5 태그는 아니지만 `<sly>` 태그는 `data-sly-unwrap`을 사용하여 최종 출력에서 표시할 수 있습니다.

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

`<sly>` 요소의 목표는 요소가 출력되지 않는다는 것을 더 분명히 하는 것입니다. 필요한 경우 `data-sly-unwrap`을(를) 계속 사용할 수 있습니다.

`data-sly-unwrap`과 마찬가지로 이 항목의 사용을 최소화하십시오.
