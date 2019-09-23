---
title: HTL 블록 문
seo-title: HTL 블록 문
description: HTL(HTML 템플릿 언어) 블록 문은 기존 HTML에 직접 추가되는 사용자 지정 데이터 속성입니다.
seo-description: 'HTL(HTML 템플릿 언어) 블록 문은 기존 HTML에 직접 추가되는 사용자 지정 데이터 속성입니다. '
uuid: 0624fb6e-6989-431b-abc-1138325393f1
contentOwner: 사용자
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: 참조
discoiquuid: 58aa6ea8-1d45-4f6f-a77e-4819f593a19d
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: afc29cbad83caeb549097da3fc33fd9147f1157a

---


# HTL 블록 문 {#htl-block-statements}

HTL(HTML 템플릿 언어) 블록 문은 기존 HTML에 직접 추가되는 사용자 지정 `data` 속성입니다. 이렇게 하면 프로토타입 정적 HTML 페이지에 대한 간편하고 완벽한 주석을 달 수 있으므로 HTML 코드의 유효성을 끊지 않고 기능적인 동적 템플릿으로 변환할 수 있습니다.

## 교묘한 요소 {#sly-element}

결과 HTML에 **&lt;sly&gt; 요소가** 표시되지 않으며, 데이터-슬라이 해제 대신 사용할 수 있습니다. &lt;sly&gt; 요소의 목표는 요소가 출력되지 않음을 더 확실하게 하는 것입니다. 그래도 데이터-슬라이 해제 기능을 사용할 수 있습니다.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

데이터 중심의 비래핑(unwrap)과 마찬가지로 이 기능의 사용을 최소화하십시오.

## use {#use}

**`data-sly-use`**:JavaScript 또는 Java에 정의된 도우미 개체를 초기화하고 변수를 통해 표시합니다.

소스 파일이 템플릿과 동일한 디렉토리에 있는 JavaScript 개체를 초기화합니다. 파일 이름은 반드시 사용해야 합니다.

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

매개 변수는 *옵션을*&#x200B;사용하여 초기화에 전달할 수 있습니다. 일반적으로 이 기능은 `data-sly-template` 블록 내에 있는 HTL 코드에서만 사용해야 합니다.

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

다음을 사용하여 호출할 수 있는 다른 HTL 템플릿을 초기화합니다. **`data-sly-call`**

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Use-API에 대한 자세한 내용은 다음을 참조하십시오.
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)
>



## 감싸기 {#unwrap}

**`data-sly-unwrap`**:생성된 마크업에서 호스트 요소를 제거하고 컨텐츠를 유지합니다. 따라서 HTL 프레젠테이션 로직의 일부로서 필요하지만 실제 출력에서 원하지 않는 요소를 제외할 수 있습니다.

그러나 이 성명은 제한적으로 사용해야 한다. 일반적으로 HTL 마크업을 원하는 출력 마크업과 가능한 한 가까이 유지하는 것이 좋습니다. 즉, HTL 블록 명령문을 추가할 때 새로운 요소를 포함하지 않고도 가능한 한 많은 작업을 수행하여 기존 HTML에 주석을 답니다.

예를 들어,

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

생산물

```xml
<p>Hello World</p>
```

반면에,

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

생산물

```xml
Hello World
```

요소의 래핑을 조건부로 취소할 수도 있습니다.

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## text {#text}

**`data-sly-text`**:호스트 요소의 컨텐츠를 지정된 텍스트로 바꿉니다.

예를 들어,

```xml
<p>${properties.jcr:description}</p>
```

는

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

둘 다 의 값을 단락 **`jcr:description`** 텍스트로 표시합니다. 두 번째 방법의 이점은 정적 자리 표시자 컨텐츠를 원본 디자이너에게 그대로 유지하면서 HTML의 완벽한 주석을 달 수 있다는 것입니다.

## attribute {#attribute}

**data-sly-attribute**:호스트 요소에 속성을 추가합니다.

예를 들어,

```xml
<div title="${properties.jcr:title}"></div>
```

는

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

두 개 모두 `title` 속성을 값으로 설정합니다 **`jcr:title`**. 두 번째 방법의 이점은 정적 자리 표시자 컨텐츠를 원본 디자이너에게 그대로 유지하면서 HTML의 완벽한 주석을 달 수 있다는 것입니다.

속성은 왼쪽에서 오른쪽으로 해결되며, 속성(문자 또는 via를 통해 정의됨)의 오른쪽 가장 오른쪽 인스턴스가 **`data-sly-attribute`**&#x200B;해당 왼쪽에 정의된 동일한 속성(문자 또는 via **`data-sly-attribute`**)의 인스턴스보다 우선합니다.

값이 빈 문자열로 **`literal`** 평가되는 **`data-sly-attribute`**&#x200B;속성(또는 를 통해 ** 설정)은 최종 마크업에서 제거됩니다. 이 규칙의 한 가지 예외는 *리터럴* 빈 문자열로 설정된 *리터럴* 속성이 보존된다는 것입니다. 예,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

products,

```xml
<div></div>
```

하지만

```xml
<div class="" data-sly-attribute.id=""></div>
```

products,

```xml
<div class=""></div>
```

여러 속성을 설정하려면, 맵 개체에 속성 및 해당 값에 해당하는 키-값 쌍을 전달합니다. 예를 들어

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

products,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## 요소 {#element}

**`data-sly-element`**:호스트 요소의 요소 이름을 바꿉니다.

예,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

를 **`h1`** 의 값으로 대체합니다 **`titleLevel`**.

보안상의 이유로 `data-sly-element` 다음 요소 이름만 허용됩니다.

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

다른 요소를 설정하려면 XSS 보안을 해제해야 합니다( `@context='unsafe'`).

## 테스트 {#test}

**`data-sly-test`** :호스트 요소와 컨텐츠를 조건부로 제거합니다. 값이 요소를 `false` 제거합니다.값이 요소를 `true` 보유합니다.

예를 들어, 요소와 `p` 컨텐츠는 `isShown` `true`다음과 같은 경우에만 렌더링됩니다.

```xml
<p data-sly-test="${isShown}">text</p>
```

테스트 결과를 나중에 사용할 수 있는 변수에 할당할 수 있습니다. 이것은 명시적인 else 문이 없으므로 일반적으로 "if else" 논리를 구성하는 데 사용됩니다.

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

일단 설정되면 이 변수에는 HTL 파일 내에 전역 범위가 있습니다.

다음은 값 비교에 대한 몇 가지 예입니다.

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div> 

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

## repeat {#repeat}

데이터-유사한 반복을 사용하면 지정된 목록에 따라 요소를 여러 번 *반복할* 수 있습니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

컨테이너 요소가 필요하지 않는다는 점을 제외하면 데이터 슬라이딩 목록과 동일한 방식으로 작동합니다.

다음 예에서는 속성에 대한 *항목을* 참조할 수도 있음을 보여줍니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`**:제공된 객체의 각 enumerable 속성에 대한 호스트 요소의 컨텐츠를 반복합니다.

다음은 간단한 루프입니다.

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

목록 범위 내에서 다음 기본 변수를 사용할 수 있습니다.

**`item`**:반복의 현재 항목입니다.

**`itemList`**:다음 속성을 포함하는 개체:

**`index`**:0부터 시작하는 카운터( `0..length-1`).

**`count`**:1기반 카운터( `1..length`).

`first`:현재 항목이 첫 번째 항목인 `true` 경우.

**`middle`**:현재 항목이 첫 번째 또는 마지막 항목이 아닌 `true` 경우.

**`last`**:현재 항목이 마지막 항목인 `true` 경우.

**`odd`**:홀수이면 `true` `index` 됩니다.

**`even`**:짝만 `true` 있으면 `index` 됩니다.

문에서 식별자를 정의하면 `data-sly-list` 및 **`itemList`** `item` 변수의 이름을 변경할 수 있습니다. **`item`** **** `<variable>`*** **`itemList`** 가 되고 **`*<variable>*List`**&#x200B;있습니다.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

다음 속성을 동적으로 액세스할 수도 있습니다.

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## 리소스 {#resource}

**`data-sly-resource`**:슬링 해상도 및 렌더링 프로세스를 통해 지정된 리소스를 렌더링한 결과를 포함합니다.

간단한 리소스는 다음과 같습니다.

```xml
<article data-sly-resource="path/to/resource"></article>
```

옵션을 사용하면 다음과 같은 여러 가지 추가 변형을 사용할 수 있습니다.

리소스 경로 조작:

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

기본적으로 AEM 데코레이션 태그는 비활성화되어 있고 데코레이션 태그 이름 옵션을 사용하면 다시 가져올 수 있으며 cssClassName을 사용하여 해당 요소에 클래스를 추가할 수 있습니다.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM 파섹 자세한 내용은 [개발 구성 요소](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/decoration-tag.html) 설명서의 데코레이션 태그를 참조하십시오.

## include {#include}

**`data-sly-include`**:호스트 요소의 컨텐츠를 지정된 HTML 템플릿 파일(HTL, JSP, ESP 등)에 의해 생성된 마크업으로 대체합니다. 해당 템플릿 엔진에서 처리하는 경우. 포함된 파일의 *렌더링 컨텍스트에는 현재 HTL 컨텍스트(* 포함 파일 **)가 포함되지 않습니다.따라서 HTL 파일을 포함하려면 포함된 파일에서 현재 파일을 반복해야 **`data-sly-use`** 합니다(이 경우 일반적으로 **`data-sly-template`** 및 `data-sly-call`를 사용하는 것이 더 좋습니다).

간단한 기능:

```xml
<section data-sly-include="path/to/template.html"></section>
```

JSP 파섹

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

## 템플릿 및 호출 {#template-call}

`data-sly-template`:템플릿을 정의합니다. 호스트 요소 및 해당 컨텐츠는 HTL로 출력되지 않습니다.

`data-sly-call`:데이터 전용 템플릿으로 정의된 템플릿을 호출합니다. 호출된 템플릿의 컨텐츠(선택적으로 매개 변수화된)는 호출의 호스트 요소의 컨텐츠를 대체합니다.

정적 템플릿을 정의한 다음 호출합니다.

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

동적 템플릿을 정의한 다음 매개 변수를 사용하여 호출합니다.

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

다른 파일에 있는 템플릿은 로 초기화할 수 있습니다 `data-sly-use`. 이 경우 동일한 요소에 배치할 `data-sly-use` 수도 `data-sly-call` 있습니다.

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

## i18n 및 로케일 개체 {#i-n-and-locale-objects}

i18n 및 HTL을 사용하는 경우 이제 사용자 지정 로케일 개체를 전달할 수도 있습니다.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## URL 조작 {#url-manipulation}

새로운 URL 조작 세트를 사용할 수 있습니다.

사용 방법에 대한 다음 예를 참조하십시오.

경로에 html 확장자를 추가합니다.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

HTML 확장명과 선택기를 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

html 확장명과 조각(#value)을 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## AEM 6.3에서 지원되는 HTL 기능 {#htl-features-supported-in-aem}

다음과 같은 새로운 HTL 기능이 AEM(Adobe Experience Manager) 6.3에서 지원됩니다.

### 숫자/날짜 서식 {#number-date-formatting}

AEM 6.3은 사용자 지정 코드를 작성하지 않고도 숫자 및 날짜에 대한 기본 서식을 지원합니다. 또한 시간대 및 로케일도 지원합니다.

다음 예제에서는 형식이 먼저 지정된 다음 서식이 필요한 값을 보여 줍니다.

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>사용할 수 있는 형식에 대한 자세한 내용은 HTL [사양을](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)참조하십시오.

### 리소스를 사용한 데이터 활용 {#data-sly-use-with-resources}

따라서 HTL에서 바로 데이터를 사용할 수 있으며 리소스를 얻기 위해 코드를 작성할 필요가 없습니다.

예:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### 요청 속성 {#request-attributes}

이제 *데이터-* include *및* 데이터- ** 경간 리소스에서requestAttributes를전달하여 수신 HTL 스크립트에서 사용할 수 있습니다.

이를 통해 스크립트 또는 구성 요소에 매개 변수를 올바르게 전달할 수 있습니다.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Settings 클래스의 Java 코드에서는 맵을 사용하여 requestAttributes를 전달합니다.

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

예를 들어 Sling-Model을 통해 지정된 requestAttributes의 값을 사용할 수 있습니다.

이 예에서 *레이아웃은* Use-class의 맵을 통해 삽입됩니다.

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### @extension 수정 {#fix-for-extension}

@extension은 AEM 6.3의 모든 시나리오에서 작동하므로 www.adobe.com.html ** 등의 결과를 얻고 확장을 추가할지 여부를 확인할 수 있습니다.

```xml
${ link @ extension = 'html' }
```
