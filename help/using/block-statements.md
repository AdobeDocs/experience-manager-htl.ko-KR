---
title: HTL 블록 설명
seo-title: HTL 블록 설명
description: HTML (HTML Template Language) 블록 문은 기존 HTML에 직접 추가된 사용자 지정 데이터 특성입니다.
seo-description: HTML (HTML Template Language) 블록 문은 기존 HTML에 직접 추가된 사용자 지정 데이터 특성입니다.
uuid: 0624 FB 6 E -6989-431 B-AABC -1138325393 F 1
contentOwner: 사용자
products: sg_ Experiencemanager/HTL
topic-tags: HTML-template-language
content-type: 참조
discoiquuid: 58 AA 6 EA 8-1 D 45-4 F 6 F-A 77 E -4819 F 593 A 19 D
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL 블록 설명 {#htl-block-statements}

HTML (HTML Template Language) 블록 문은 기존 HTML에 직접 추가된 사용자 `data` 지정 속성입니다. 이렇게 하면 HTML 코드의 유효성을 그대로 유지하면서 프로토타입 정적 HTML 페이지의 간단하면서도 완벽한 주석을 사용하여 기능을 다이내믹한 동적 템플릿으로 변환할 수 있습니다.

## Sly 요소 {#sly-element}

The **& amp; lt; sly & amp; gt; 요소가** 결과 HTML에 표시되지 않으며 데이터-선택-해제 대신 사용할 수 있습니다. The & amp의 목표lt; sly & amp; gt; 요소는 요소가 출력되지 않도록 하기 위한 것입니다. If you want you can still use data-sly-unwrap.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

data-sly-unwrap와 마찬가지로, 이 기능의 사용을 최소화해 보십시오.

## use {#use}

**`data-sly-use`**: 도우미 객체 (JavaScript 또는 Java에 정의됨) 를 초기화하여 변수를 통해 노출합니다.

소스 파일이 템플릿과 동일한 디렉토리에 있는 JavaScript 개체를 초기화합니다. 파일 이름은 반드시 사용해야 합니다.

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

소스 파일이 템플릿과 동일한 디렉토리에 있는 Java 클래스를 초기화합니다. 파일 이름은 아닌 클래스 이름을 사용해야 합니다.

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Osgi 번들의 일부로 해당 클래스가 설치된 Java 클래스를 초기화합니다. 정규화된 클래스 이름은 반드시 사용해야 합니다.

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

옵션을 사용하여 *매개 변수에 매개 변수를 전달할*수 있습니다. 일반적으로 이 기능은 `data-sly-template` 블록 내에 있는 HTL 코드에서만 사용해야 합니다.

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

다음 아이콘을 **`data-sly-call`**사용하여 호출할 수 있는 다른 HTL 템플릿을 초기화합니다.

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Use-API에 대한 자세한 내용은 다음을 참조하십시오.
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript use-api](use-api-javascript.md)
>



## 감싸기 {#unwrap}

**`data-sly-unwrap`**: 생성된 마크업에서 컨텐츠를 유지한 상태로 호스트 요소를 제거합니다. 따라서 HTL 프레젠테이션 로직의 일부로 필요하지만 실제 출력에서는 원하지 않는 요소를 제외할 수 있습니다.

그러나 이 문은 반드시 사용해야 합니다. 일반적으로 HTL 마크업을 의도한 출력 마크업에 최대한 가깝게 유지하는 것이 좋습니다. 즉, HTL 블록 명령문을 추가할 때 새 요소를 만들지 않고 기존 HTML에 주석을 달 수 있도록 최대한 시도해 봅니다.

예를 들어

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

Will Produce

```xml
<p>Hello World</p>
```

반면에,

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

Will Produce

```xml
Hello World
```

조건부로 요소를 감싸지 않을 수도 있습니다.

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## text {#text}

**`data-sly-text`**: 호스트 요소의 컨텐츠를 지정된 텍스트로 바꿉니다.

예를 들어

```xml
<p>${properties.jcr:description}</p>
```

는

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

둘 다 단락 텍스트로 값을 **`jcr:description`** 표시합니다. 두 번째 방법의 장점은 원본 디자이너의 정적 자리 표시자 내용을 유지하면서 HTML의 방해가 되지 않는 주석을 사용할 수 있다는 것입니다.

## 속성 {#attribute}

**data-sly-attribute**: 호스트 요소에 속성을 추가합니다.

예를 들어

```xml
<div title="${properties.jcr:title}"></div>
```

는

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

both will set the `title` attribute of **`jcr:title`**the value of. 두 번째 방법의 장점은 원본 디자이너의 정적 자리 표시자 내용을 유지하면서 HTML의 방해가 되지 않는 주석을 사용할 수 있다는 것입니다.

속성은 왼쪽에서 오른쪽으로 해결되며, 속성 (문자 또는 정의된 문자 **`data-sly-attribute`**중 하나) 의 오른쪽 인스턴스는 왼쪽 또는 오른쪽을 통해 정의된 동일한 속성의 인스턴스보다 우선합니다 (문자 또는 via 중 하나로 **`data-sly-attribute`**정의됨).

값이 빈 문자열로 평가되는 속성 (either **`literal`** 또는 set via **`data-sly-attribute`**) *는* 최종 마크업에 제거됩니다. 이 규칙에 대한 한 가지 예외는 *리터럴* 특성이 *리터럴* 빈 문자열로 설정되어 있다는 것입니다. 예,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

제작,

```xml
<div></div>
```

그러나,

```xml
<div class="" data-sly-attribute.id=""></div>
```

제작,

```xml
<div class=""></div>
```

여러 속성을 설정하려면 맵 개체에 특성 및 해당 값에 해당하는 키-값 쌍을 전달합니다. 예를 들어,

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

제작,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## 요소 {#element}

**`data-sly-element`**: 호스트 요소의 요소 이름을 대체합니다.

예,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

의 **`h1`** 값으로를 대체합니다 **`titleLevel`**.

보안상의 이유로 `data-sly-element` 다음 요소 이름만 수락합니다.

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

다른 요소를 설정하려면 XSS 보안이 꺼져 있어야 합니다 ( `@context='unsafe'`).

## 테스트 {#test}

**`data-sly-test`:** 조건부로 호스트 요소와 컨텐츠를 제거합니다. 값은 요소를 `false` 제거합니다. 값은 `true` 요소를 유지합니다.

예를 들어 `p` , 요소 및 해당 컨텐츠는 다음과 같은 경우에만 `isShown``true`렌더링됩니다.

```xml
<p data-sly-test="${isShown}">text</p>
```

테스트 결과는 나중에 사용할 수 있는 변수에 할당할 수 있습니다. 명시적 else 문이 없으므로 일반적으로 "if else" 논리를 구성하는 데 사용됩니다.

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

이 변수에는 일단 설정되면 HTL 파일 내에 전역 범위가 있습니다.

값을 비교하는 예는 다음과 같습니다.

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

data-sly-repeat를 사용하면 지정된 목록을 기반으로 한 요소를 여러 번 *반복할* 수 있습니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

이는 컨테이너 요소가 필요하지 않다는 점을 제외하면 data-sly-list와 동일한 방식으로 작동합니다.

다음 예는 속성의 *항목을* 참조할 수도 있음을 보여줍니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`**: 제공된 객체의 열거 가능한 각 속성에 대해 호스트 요소의 내용을 반복합니다.

다음은 간단한 루프입니다.

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

다음 기본 변수는 목록 범위 내에서 사용할 수 있습니다.

**`item`**: 반복할 현재 항목입니다.

**`itemList`**: 개체는 다음 속성을 가집니다.

**`index`**: 제로 기반 카운터 ( `0..length-1`).

**`count`**: 1 기반 카운터 ( `1..length`).

`first`: `true` 현재 항목이 첫 번째 항목인 경우

**`middle`**: `true` 현재 항목이 첫 번째 항목이나 마지막 항목이 아닌 경우.

**`last`**: `true` 현재 항목이 마지막 항목인 경우

**`odd`**: `true` `index` 홀수인 경우

**`even`**: `true` if `index` is even.

`data-sly-list` 문에서 식별자를 정의하면 **`itemList`** AND `item` 변수의 이름을 변경할 수 있습니다. **`item`** *** `<variable>`*** **`itemList`****`*<variable>*List`**가 됩니다.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

동적으로 속성에 액세스할 수도 있습니다.

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## 리소스 {#resource}

**`data-sly-resource`**: Sling Resolution 및 Render Process를 통해 지정된 리소스를 렌더링한 결과를 포함합니다.

간단한 리소스에는 다음이 포함됩니다.

```xml
<article data-sly-resource="path/to/resource"></article>
```

옵션은 다음과 같은 다양한 변형을 허용합니다.

리소스 경로 조작:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

선택기를 추가 (또는 교체) 합니다.

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

여러 선택기 추가, 교체 또는 제거:

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

기본적으로 AEM 데코레이션 태그가 비활성화되고 decorationtagname 옵션이 이를 다시 가져와서 cssclassname를 통해 해당 요소에 클래스를 추가할 수 있습니다.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM 에서는 포함된 요소를 래핑하는 장식 태그를 제어하는 명확하고 간단한 논리를 제공합니다. 자세한 내용은 Developing Components 설명서에서 [장식 태그를](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/decoration-tag.html) 참조하십시오.

## include {#include}

**`data-sly-include`**: 호스트 요소의 컨텐츠를 지정된 HTML 템플릿 파일 (HTL, JSP, ESP 등) 에 의해 생성된 마크업으로 대체합니다. 해당 템플릿 엔진에서 처리할 때 The rendering context of the *included file* will not include the current HTL context (that of the *including file*); Consequently, for inclusion of HTL files, the current **`data-sly-use`** would have to be repeated in the included file (In such a case it is usually better to use **`data-sly-template`** and `data-sly-call`)

간단한 기능:

```xml
<section data-sly-include="path/to/template.html"></section>
```

JSP는 다음과 같은 방법으로 포함될 수 있습니다.

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

## 템플릿 및 호출 {#template-call}

`data-sly-template`: 템플릿을 정의합니다. 호스트 요소 및 해당 컨텐츠가 HTL에서 출력되지 않습니다.

`data-sly-call`: data-sly-template로 정의된 템플릿을 호출합니다. 호출된 템플릿 (선택 사항) 의 컨텐츠는 호출의 호스트 요소 컨텐츠를 대체합니다.

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

다른 파일에 있는 템플릿은 이니셜라이즈될 `data-sly-use`수 있습니다. 이 경우 `data-sly-use` 동일한 `data-sly-call` 요소에 배치할 수도 있습니다.

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

## I 18 N 및 로케일 개체 {#i-n-and-locale-objects}

i 18 n 및 HTL를 사용하는 경우 사용자 정의 로케일 개체를 전달할 수도 있습니다.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## URL 조작 {#url-manipulation}

새 URL 조작 세트를 사용할 수 있습니다.

사용 방법에 대한 다음 예를 참조하십시오.

HTML 확장명을 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

HTML 확장자와 선택기를 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

HTML 확장명과 조각 (# value) 를 경로에 추가합니다.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## AEM 6.3에서 지원되는 HTL 기능 {#htl-features-supported-in-aem}

다음과 같은 새로운 HTL 기능이 AEM (Adobe Experience Manager) 6.3에서 지원됩니다.

### 숫자/날짜 서식 {#number-date-formatting}

AEM 6.3는 사용자 지정 코드를 작성하지 않고도 숫자와 날짜의 기본 서식을 지원합니다. 표준 시간대 및 로케일도 지원합니다.

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
>사용할 수 있는 형식에 대한 자세한 내용은 [HTL-사양을](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)참조하십시오.

### 리소스를 사용한 데이터 활용 {#data-sly-use-with-resources}

따라서 데이터를-sly-use로 HTL에서 바로 가져올 수 있고 리소스를 작성할 코드를 작성할 필요가 없습니다.

예:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### request-attributes {#request-attributes}

*data-sly-include* 및 *data-sly-resource* 에서 *이제 requestattributes* 를 전달되어 HTL-Script 수신에 사용할 수 있습니다.

이를 통해 매개 변수를 스크립트 또는 구성 요소로 올바르게 전달할 수 있습니다.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Settings 클래스의 Java-Code는 requestattributes를 전달하는 데 사용됩니다.

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

예를 들어 sling-models를 통해 지정된 requestattributes의 값을 소비할 수 있습니다.

이 예제에서는 use-class의 맵을 통해 *레이아웃을* 주입합니다.

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### @ extension에 대한 수정 {#fix-for-extension}

@ extension는 *www.adobe.com.ht ML* 와 같은 결과를 가질 수 있고 확장을 추가할지 또는 추가하지 않을지를 확인하는 AEM 6.3의 모든 시나리오에서 작동합니다.

```xml
${ link @ extension = 'html' }
```
