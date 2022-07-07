---
title: HTL 블록 문이란 무엇입니까?
description: HTL 블록 문 또는 HTML 템플릿 언어(HTL)에 대해 알아보십시오. 블록 문은 기존 HTML에 직접 추가된 사용자 지정 데이터 속성입니다.
exl-id: a517dcef-ab7a-4d4c-a1a9-2e57aad034f7
source-git-commit: 79d299766da07dae001708b396b05c73cd70d4cc
workflow-type: ht
source-wordcount: '1563'
ht-degree: 100%

---

# HTL 블록 문 {#htl-block-statements}

HTML 템플릿 언어(HTL) 블록 문은 기존 HTML에 직접 추가된 사용자 지정 `data` 속성입니다. 프로토타입 정적 HTML 페이지의 쉬운 비간섭적 주석을 허용하여 HTML 코드의 유효성을 손상시키지 않고 작동하는 동적 템플릿으로 변환합니다.

## 블록 개요 {#overview}

HTL 블록 플러그인은 HTML 요소에 설정된 `data-sly-*` 속성으로 정의됩니다. 요소에는 닫는 태그가 있거나 자동으로 닫힐 수 있습니다. 속성은 값(정적 문자열 또는 표현식일 수 있음)을 갖거나 단순히 부울 속성(값 없음)일 수 있습니다.

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

블록 문 뒤에 식별자가 올 수도 있습니다.

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

식별자는 블록 문에서 다양한 방식으로 사용할 수 있습니다. 여기 몇 가지 예가 있습니다.

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

상위 최상위 수준 식별자는 대소문자를 구분하지 않지만(대소문자를 구분하지 않는 HTML 속성을 통해 설정할 수 있기 때문에) 모든 속성은 대소문자를 구분합니다.

## 사용 가능한 블록 문 {#available-block-statements}

사용할 수 있는 여러 블록 문이 있습니다. 동일한 요소에서 사용될 때 다음 우선 순위 목록은 블록 문이 평가되는 방법을 정의합니다.

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`, `data-sly-include`, `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

두 개의 블록 문이 동일한 우선 순위를 가질 때 평가 순서는 왼쪽에서 오른쪽입니다.

### use {#use}

`data-sly-use`는 도우미 개체(JavaScript 또는 Java로 정의됨)를 초기화하고 변수를 통해 노출합니다.

소스 파일이 템플릿과 동일한 디렉터리에 있는 JavaScript 개체를 초기화합니다. 파일 이름을 사용해야 합니다.

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

소스 파일이 템플릿과 동일한 디렉터리에 있는 Java 클래스를 초기화합니다. 파일 이름이 아닌 클래스 이름을 사용해야 합니다.

```xml
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```

해당 클래스가 OSGi 번들의 일부로 설치된 Java 클래스를 초기화합니다. 정규화된 클래스 이름을 사용해야 합니다.

```xml
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

매개 변수는 옵션을 사용하여 초기화에 전달할 수 있습니다. 일반적으로 이 기능은 `data-sly-template` 블록 내에 있는 HTL 코드에서만 사용해야 합니다.

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

`data-sly-call`을 사용하여 호출할 수 있는 다른 HTL 템플릿을 초기화합니다.

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Use-API에 대한 자세한 내용은 다음을 참조하십시오.
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)


#### 리소스와 data-sly-use {#data-sly-use-with-resources}

이를 통해 `data-sly-use`를 사용하여 HTL에서 직접 리소스를 가져올 수 있으며 가져오기 위해 코드를 작성할 필요가 없습니다.

예:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>[경로가 항상 필요하지는 않음](#path-not-required) 섹션도 참조하십시오.

### unwrap {#unwrap}

`data-sly-unwrap`은 콘텐츠를 유지하면서 생성된 마크업에서 호스트 요소를 제거합니다. HTL 프레젠테이션 논리의 일부로 필요하지만 실제 출력에서는 원하지 않는 요소를 제외할 수 있습니다.

단, 그러나 이 명령문은 드물게 사용해야 합니다. 일반적으로 HTL 마크업을 의도한 출력 마크업에 최대한 가깝게 유지하는 것이 좋습니다. 즉, HTL 블록 문을 추가할 때 새로운 요소를 삽입하지 말고 최대한 기존 HTML에 주석을 달도록 하십시오.

예를 들어

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

다음을 생성합니다.

```xml
<p>Hello World</p>
```

반면에

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

다음을 생성합니다.

```xml
Hello World
```

요소를 조건부로 래핑 해제하는 것도 가능합니다.

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

### set {#set}

`data-sly-set`은 사전 정의된 값으로 새 식별자를 정의합니다.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### text {#text}

`data-sly-text`는 호스트 요소의 콘텐츠를 지정된 텍스트로 바꿉니다.

예를 들어

```xml
<p>${properties.jcr:description}</p>
```

다음에 해당합니다.

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

둘 다 `jcr:description` 값을 단락 텍스트로 표시합니다. 두 번째 메서드의 장점은 원래 디자이너의 정적 자리 표시자 콘텐츠를 유지하면서 HTML의 비간섭적 주석을 허용한다는 것입니다.

### attribute {#attribute}

`data-sly-attribute`는 호스트 요소에 속성을 추가합니다.

예를 들어

```xml
<div title="${properties.jcr:title}"></div>
```

다음에 해당합니다.

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

둘 다 `title` 속성을 `jcr:title` 값으로 설정합니다. 두 번째 메서드의 장점은 원래 디자이너의 정적 자리 표시자 콘텐츠를 유지하면서 HTML의 비간섭적 주석을 허용한다는 것입니다.

속성은 왼쪽에서 오른쪽으로 해석되며 속성의 가장 오른쪽 인스턴스(리터럴 또는 `data-sly-attribute`을 통해 정의됨) 가 왼쪽에 정의된 동일한 속성(리터럴 또는 `data-sly-attribute`을 통해 정의됨)의 인스턴스보다 우선합니다.

값이 빈 문자열로 평가되는 속성(either `literal` 또는 `data-sly-attribute`를 통해 설정)은 최종 마크업에서 제거됩니다. 이 규칙의 한 가지 예외는 리터럴 빈 문자열로 설정된 리터럴 속성이 유지된다는 것입니다. 예를 들어

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

다음을 생성합니다.

```xml
<div></div>
```

하지만,

```xml
<div class="" data-sly-attribute.id=""></div>
```

다음을 생성합니다.

```xml
<div class=""></div>
```

여러 속성을 설정하려면 속성 및 해당 값에 해당하는 키-값 쌍을 보유하는 맵 개체를 전달합니다. 예를 들어 다음과 같이 가정합니다.

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

다음을 생성합니다.

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

### element {#element}

`data-sly-element`는 호스트 요소의 요소 이름을 바꿉니다.

예를 들어

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

`h1`을 `titleLevel` 값으로 바꿉니다.

보안상의 이유로 `data-sly-element`는 다음 요소 이름만 허용합니다.

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

다른 요소를 설정하려면 XSS 보안을 꺼야 합니다(`@context='unsafe'`).

### test {#test}

`data-sly-test`는 조건부로 호스트 요소와 그 콘텐츠를 제거합니다. `false` 값은 요소를 제거합니다. `true` 값은 요소를 유지합니다.

예를 들어 `p` 요소와 해당 콘텐츠는 `isShown`이 `true`인 경우에만 렌더링됩니다.

```xml
<p data-sly-test="${isShown}">text</p>
```

테스트 결과는 나중에 사용할 수 있는 변수에 할당할 수 있습니다. 명시적인 else 문이 없기 때문에 일반적으로 “if else” 논리를 구성하는 데 사용됩니다.

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

일단 설정되면 변수의 범위는 HTL 파일 내 전역입니다.

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

### repeat {#repeat}

`data-sly-repeat`을 사용하면 지정된 목록을 기반으로 요소를 여러 번 반복할 수 있습니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

이는 컨테이너 요소가 필요하지 않다는 점을 제외하고 `data-sly-list`와 동일한 방식으로 작동합니다.

다음 예는 속성에 대해 *항목*&#x200B;도 참조할 수 있음을 보여 줍니다.

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list`는 제공된 객체의 각 열거형 속성에 대해 호스트 요소의 콘텐츠를 반복합니다.

다음은 간단한 루프입니다.

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

목록 범위 내에서 다음 기본 변수를 사용할 수 있습니다.

* `item`: 반복의 현재 항목입니다.
* `itemList`: 다음 속성을 보유하는 개체입니다.
* `index`: 0부터 시작하는 카운터(`0..length-1`).
* `count`: 1부터 시작하는 카운터(`1..length`).
* `first`: 현재 항목이 첫 번째 항목인 경우 `true`입니다.
* `middle`: 현재 항목이 첫 번째 항목도 아니고 마지막 항목도 아닌 경우 `true`입니다.
* `last`: 현재 항목이 마지막 항목인 경우 `true`입니다.
* `odd`: `index`가 홀수인 경우 `true`입니다.
* `even`: `index`가 짝수인 경우 `true`입니다.

`data-sly-list` 문에 식별자를 정의하면 `itemList` 및 `item` 변수의 이름을 바꿀 수 있습니다. `item`은 `<variable>`이 되고 `itemList`는 `<variable>List`가 됩니다.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

속성에 동적으로 액세스할 수도 있습니다.

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

### resource {#resource}

`data-sly-resource`에는 sling 해상도 및 렌더링 프로세스를 통해 표시된 리소스를 렌더링한 결과가 포함됩니다.

간단한 리소스는 다음과 같습니다.

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### 경로가 항상 필요하지는 않음 {#path-not-required}

리소스가 이미 있는 경우 `data-sly-resource`이 있는 경로를 사용할 필요가 없습니다. 리소스가 이미 있는 경우 직접 사용할 수 있습니다.

예를 들어 다음과 같은 것이 올바릅니다.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

그러나 다음과 같은 것도 완벽하게 허용됩니다.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

다음과 같은 이유로 가능한 한 리소스를 직접 사용하는 것을 권장합니다.

* 이미 리소스가 있는 경우 경로를 사용하여 다시 확인하는 것은 불필요한 추가 작업입니다.
* 이미 리소스가 있는 경우 경로를 사용하면 Sling 리소스가 래핑되거나 합성될 수 있고 지정된 경로에서 제공되지 않을 수 있으므로 예기치 않은 결과가 발생할 수 있습니다.

#### 옵션 {#resource-options}

옵션은 다음과 같은 몇 가지 추가 변형을 허용합니다.

리소스 경로 조작:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

선택기 추가(또는 교체):

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

여러 선택기 추가, 교체 또는 제거:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

기존 선택기에 선택기 추가:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

기존 선택기에서 일부 선택기 제거:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

모든 선택기 제거:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

리소스의 리소스 유형 재정의:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

WCM 모드 변경:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

기본적으로 AEM 장식 태그는 비활성화되어 있고, decorationTagName 옵션을 사용하면 다시 가져올 수 있으며, cssClassName을 사용하여 해당 요소에 클래스를 추가할 수 있습니다.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM은 포함된 요소를 래핑하는 장식 태그를 제어하는 명확하고 간단한 논리를 제공합니다. 자세한 내용은 개발 구성 요소 문서의 [장식 태그](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/developing/full-stack/components-templates/decoration-tag.html)를 참조하십시오.

### include {#include}

`data-sly-include`는 호스트 요소의 콘텐츠를 표시된 HTML 템플릿 파일(HTL, JSP, ESP 등)에 의해 생성된 마크업으로 대체합니다. 해당 템플릿 엔진에 의해 처리될 때. 포함된 파일의 렌더링 컨텍스트에는 현재 HTL 컨텍스트(포함 파일의 컨텍스트)가 포함되지 않습니다. 결과적으로 HTL 파일을 포함하려면 포함된 파일에서 현재 `data-sly-use`를 반복해야 합니다(이 경우 일반적으로 `data-sly-template` 및 `data-sly-call`을 사용하는 것이 좋습니다).

간단한 예는 다음과 같습니다.

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

### Request-attributes {#request-attributes}

수신 HTL 스크립트에서 사용하기 위해 `data-sly-include` 및 `data-sly-resource`에서 `requestAttributes`를 전달할 수 있습니다.

매개 변수를 스크립트나 구성 요소에 적절하게 전달할 수 있습니다.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings"
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

설정 클래스의 Java 코드인 맵은 requestAttributes를 전달하는 데 사용됩니다.

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

예를 들어 Sling 모델을 통해, 지정된 `requestAttributes`의 값을 사용할 수 있습니다.

이 예제에서 레이아웃은 Use-class의 맵을 통해 주입됩니다.

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### 템플릿 및 호출 {#template-call}

템플릿 블록은 함수 호출처럼 사용할 수 있습니다. 선언에서 매개 변수를 가져올 수 있으며 호출할 때 전달될 수 있습니다. 재귀도 허용합니다.

`data-sly-template`은 템플릿을 정의합니다. 호스트 요소와 해당 콘텐츠는 HTL에서 출력되지 않습니다.

`data-sly-call`은 data-sly-template으로 정의된 템플릿을 호출합니다. 호출된 템플릿의 콘텐츠(선택적으로 매개 변수화됨)는 호출의 호스트 요소 콘텐츠를 대체합니다.

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

다른 파일에 있는 템플릿은 `data-sly-use`로 초기화할 수 있습니다. 이 경우`data-sly-use` 및 `data-sly-call`을 동일한 요소에 배치할 수도 있습니다.

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

`<sly>` HTML 태그를 사용하여 현재 요소를 제거하고 해당 요소의 하위 요소만 표시할 수 있습니다. 그 기능은 `data-sly-unwrap` 블록 요소와 유사합니다.

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

유효한 HTML 5 태그는 아니지만 `data-sly-unwrap`을 사용하여 `<sly>` 태그를 최종 출력에 표시할 수 있습니다.

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

`<sly>` 요소의 목표는 요소가 출력되지 않는다는 것을 더 명확하게 만드는 것입니다. 원하는 경우 여전히 `data-sly-unwrap`을 사용할 수 있습니다.

As with `data-sly-unwrap`과 마찬가지로, 사용을 최소화하십시오.
