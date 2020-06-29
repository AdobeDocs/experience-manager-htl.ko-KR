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

HTML 템플릿 언어(HTL) 블록 문은 기존 HTML에 직접 추가되는 사용자 지정 `data` 속성입니다. 이를 통해 프로토타입 정적 HTML 페이지에 대한 간편하고 완벽한 주석을 달 수 있으므로 HTML 코드의 유효성을 벗어나지 않고도 작동하는 동적 템플릿으로 변환할 수 있습니다.

## 블록 개요 {#overview}

HTL 블록 플러그인은 HTML 요소에 설정된 `data-sly-*` 속성으로 정의됩니다. 요소에는 닫는 태그가 있거나 자체 닫힐 수 있습니다. 속성에는 값(정적 문자열 또는 표현식이 될 수 있음)이 있거나 단순히 부울 속성(값 없음)이 될 수 있습니다.

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

이 식별자는 블록 문에서 다양한 방법으로 사용할 수 있습니다. 다음은 몇 가지 예입니다.

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

사용할 수 있는 많은 블록 문이 있습니다. 동일한 요소에서 사용할 경우 다음 우선 순위 목록은 블록 문을 평가하는 방법을 정의합니다.

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`, `data-sly-include`, `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

두 블록 명령문의 우선 순위가 같은 경우 평가 순서는 왼쪽에서 오른쪽으로 지정됩니다.

### use {#use}

`data-sly-use` 도우미 개체(JavaScript 또는 Java에 정의됨)를 초기화하고 변수를 통해 노출합니다.

소스 파일이 템플릿과 동일한 디렉토리에 있는 JavaScript 개체를 초기화합니다. 파일 이름을 사용해야 합니다.

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

다음을 사용하여 호출할 수 있는 다른 HTL 템플릿을 초기화합니다. `data-sly-call`

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Use-API에 대한 자세한 내용은 다음을 참조하십시오.
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)


#### 리소스를 사용한 데이터 활용 {#data-sly-use-with-resources}

이를 통해 HTL에서 바로 리소스를 가져올 수 `data-sly-use` 있으며 코드를 작성할 필요가 없습니다.

예:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>경로 [가 항상 필요한 것은 아닙니다.](#path-not-required)

### 감싸기 {#unwrap}

`data-sly-unwrap` 생성된 마크업에서 호스트 요소를 제거하고 컨텐츠를 유지합니다. 따라서 HTL 프레젠테이션 로직의 일부로서 필요하지만 실제 출력에서는 원하지 않는 요소를 제외할 수 있습니다.

그러나 이 말은 제한적으로 사용해야 한다. 일반적으로 HTL 마크업을 원하는 출력 마크업에 최대한 가깝게 유지하는 것이 좋습니다. 즉, HTL 블록 문을 추가할 때 새로운 요소를 포함하지 않고도 가능한 한 기존 HTML에 간단하게 주석을 답니다.

예를 들어

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

생산물

```xml
<p>Hello World</p>
```

반면에

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

생산물

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

예를 들어

```xml
<p>${properties.jcr:description}</p>
```

은

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

둘 다 의 값을 단락 텍스트 `jcr:description` 로 표시합니다. 두 번째 방법의 장점은 정적 자리 표시자 컨텐츠를 원본 디자이너에게 그대로 유지하면서 HTML에 대한 완벽한 주석을 달 수 있다는 것입니다.

### attribute {#attribute}

`data-sly-attribute` 속성을 호스트 요소에 추가합니다.

예를 들어

```xml
<div title="${properties.jcr:title}"></div>
```

은

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

두 값 모두 `title` 속성을 값으로 설정합니다 `jcr:title`. 두 번째 방법의 장점은 정적 자리 표시자 컨텐츠를 원본 디자이너에게 그대로 유지하면서 HTML에 대한 완벽한 주석을 달 수 있다는 것입니다.

속성은 왼쪽에 정의된 동일한 속성(문자 또는 를 통해 정의됨)의 인스턴스보다 속성(리터럴 또는 정의됨 `data-sly-attribute`)의 가장 오른쪽 인스턴스 `data-sly-attribute`가 우선하므로 속성이 왼쪽에서 오른쪽으로 해결됩니다.

값이 빈 문자열로 평가되는 속성( `literal` 또는 를 통해 설정됨) `data-sly-attribute`은 최종 마크업에서 제거됩니다. 이 규칙의 한 가지 예외는 리터럴 빈 문자열에 설정된 리터럴 속성이 보존된다는 것입니다. 예,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produce,

```xml
<div></div>
```

하지만

```xml
<div class="" data-sly-attribute.id=""></div>
```

produce,

```xml
<div class=""></div>
```

여러 특성을 설정하려면, 맵 개체에 속성 및 해당 값에 해당하는 키-값 쌍을 전달합니다. 예를 들어

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

produce,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

### 요소 {#element}

`data-sly-element` 호스트 요소의 요소 이름을 대체합니다.

예,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

을 `h1` 의 값으로 대체합니다 `titleLevel`.

보안상의 이유로 다음 요소 이름만 `data-sly-element` 수락합니다.

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

다른 요소를 설정하려면 XSS 보안을 해제해야 합니다( `@context='unsafe'`).

### 테스트 {#test}

`data-sly-test` 호스트 요소와 컨텐츠를 조건부로 제거합니다. 값이 요소를 `false` 제거합니다. 요소를 유지하는 값 `true` .

예를 들어, 요소 및 `p` 컨텐츠는 다음 경우에만 렌더링됩니다. `isShown` is `true`

```xml
<p data-sly-test="${isShown}">text</p>
```

테스트 결과를 나중에 사용할 수 있는 변수에 할당할 수 있습니다. 명시적 else 문은 없으므로 일반적으로 &quot;if&quot; 논리를 구성하는 데 사용됩니다.

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

한 번 설정되면 HTL 파일 내에 전역 범위가 있습니다.

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

### repeat {#repeat}

지정된 목록에 따라 요소를 여러 번 반복할 `data-sly-repeat` 수 있습니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

컨테이너 요소가 필요하지 `data-sly-list`않는다는 점을 제외하면 이와 동일한 방식으로 작동합니다.

다음 예에서는 속성에 대한 *항목을 참조할 수도* 있음을 보여줍니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list` 제공된 개체의 각 enumerable 속성에 대한 호스트 요소의 컨텐츠를 반복합니다.

다음은 간단한 루프입니다.

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

다음 기본 변수를 목록 범위 내에서 사용할 수 있습니다.

* `item`: 반복의 현재 항목입니다.
* `itemList`: 다음 속성을 포함하는 개체:
* `index`: 0기반 카운터( `0..length-1`).
* `count`: 1기반 카운터( `1..length`).
* `first`: `true` if the current item is the first item.
* `middle`: `true` 현재 항목이 첫 번째도 마지막 항목이 아닌 경우.
* `last`: `true` if the current item is the last item.
* `odd`: `true` if `index` is odd.
* `even`: `true` 짝만 `index` 이 되면

문에서 식별자를 정의하면 `data-sly-list` 및 `itemList` `item` 변수의 이름을 변경할 수 있습니다. `item` 이 `<variable>` 가 되고 `itemList` 될 것이다 `<variable>List`.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

또한 다음 속성을 동적으로 액세스할 수도 있습니다.

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

#### 경로가 항상 필요하지 않음 {#path-not-required}

이미 리소스가 있는 경우에는 경로 `data-sly-resource` 를 사용할 필요가 없습니다. 이미 리소스가 있는 경우 직접 사용할 수 있습니다.

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

옵션을 사용하면 다음과 같은 여러 가지 추가 변형을 사용할 수 있습니다.

리소스 경로 조작:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

선택기를 추가(또는 교체):

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

여러 선택기 추가, 교체 또는 제거:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

기존 선택기를 추가합니다.

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

기본적으로 AEM 데코레이션 태그는 비활성화되어 있고, decorationTagName 옵션은 해당 태그를 다시 가져올 수 있으며, cssClassName은 해당 요소에 클래스를 추가할 수 있습니다.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM에서는 포함 요소를 래핑하는 데코레이션 태그를 제어하는 명확하고 간단한 로직을 제공합니다. 자세한 내용은 [개발 구성 요소 설명서의 데코레이션](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/decoration-tag.html) 태그를 참조하십시오.

### 포함 {#include}

`data-sly-include` 호스트 요소의 컨텐츠를 지정된 HTML 템플릿 파일(HTL, JSP, ESP 등)에 의해 생성된 마크업으로 대체 해당 템플릿 엔진에서 처리하는 경우. 포함된 파일의 렌더링 컨텍스트에는 현재 HTL 컨텍스트(포함 파일의 컨텍스트)가 포함되지 않습니다. 따라서 HTL 파일을 포함하려면 현재 파일 `data-sly-use` 이 포함된 파일에서 반복되어야 합니다(이 경우 일반적으로 사용 `data-sly-template` 및 `data-sly-call`).

간단한 기능:

```xml
<section data-sly-include="path/to/template.html"></section>
```

JSP는 다음과 같은 방식으로 포함될 수 있습니다.

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

옵션을 사용하면 파일 경로를 조작할 수 있습니다.

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

WCM 모드를 변경할 수도 있습니다.

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

### 요청 속성 {#request-attributes}

및 `data-sly-include` 에서 `data-sly-resource` 수신되는 HTL 스크립트에서 사용하기 `requestAttributes` 위해 전달할 수 있습니다.

이를 통해 스크립트 또는 구성 요소에 매개 변수를 올바르게 전달할 수 있습니다.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings"
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Settings 클래스의 Java 코드, 맵은 requestAttributes를 전달하는 데 사용됩니다.

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

예를 들어 슬링 모델을 통해 지정된 값을 사용할 수 있습니다 `requestAttributes`.

이 예에서 레이아웃은 Use-class의 Map을 통해 삽입됩니다.

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### 템플릿 및 호출 {#template-call}

템플릿 블록은 함수 호출과 같이 사용할 수 있습니다. 선언에서 매개 변수를 가져올 수 있으며, 이러한 매개 변수는 호출할 때 전달됩니다. 재귀를 허용합니다.

`data-sly-template` 템플릿을 정의합니다. 호스트 요소와 컨텐츠가 HTL로 출력되지 않습니다.

`data-sly-call` 데이터-경간 템플릿으로 정의된 템플릿을 호출합니다. 호출된 템플릿(선택적으로 매개 변수화된)의 컨텐츠는 호출의 호스트 요소의 컨텐츠를 대체합니다.

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

다른 파일에 있는 템플릿은 로 초기화할 수 있습니다 `data-sly-use`. 이 경우 `data-sly-use` 와 같은 요소에 배치할 `data-sly-call` 수도 있습니다.

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

## 슬라이 요소 {#sly-element}

HTML `<sly>` 태그는 현재 요소를 제거하는 데 사용할 수 있으므로 해당 하위 요소만 표시할 수 있습니다. 이 요소의 기능은 `data-sly-unwrap` 블록 요소와 유사합니다.

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

유효한 HTML 5 태그는 아니지만, `<sly>` 태그를 다음 방법을 사용하여 최종 출력에서 표시할 수 있습니다 `data-sly-unwrap`.

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

요소의 `<sly>` 목표는 요소가 출력되지 않는다는 것을 더 분명히 하는 것입니다. 그래도 쓸 수 있으면 `data-sly-unwrap`됩니다

이와 `data-sly-unwrap`같이, 이것의 사용을 최소화하도록 노력하세요.
