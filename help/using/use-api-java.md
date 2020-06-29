---
title: HTL Java Use-API
description: 'HTML 템플릿 언어(HTL의 Java Use-API)를 사용하면 HTL 파일이 사용자 지정 Java 클래스의 보조 메서드에 액세스하도록 설정할 수 있습니다. '
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '2558'
ht-degree: 1%

---


# HTL Java Use-API {#htl-java-use-api}

The HTML Template Language (HTL) Java Use-API enables an HTL file to access helper methods in a custom Java class through `data-sly-use`. 이렇게 하면 모든 복잡한 비즈니스 로직을 Java 코드로 캡슐화할 수 있고, HTL 코드는 직접 마크업 프로덕션만 다룹니다.

Java Use-API 개체는 POJO의 기본 생성자를 통해 특정 구현에 의해 인스턴스화된 간단한 POJO일 수 있습니다.

Use-API POJO는 다음 서명을 사용하여 init라는 공개 메서드를 노출할 수도 있습니다.

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

맵에는 Use-API 개체가 해당 처리에 사용할 수 있는 현재 실행되는 HTL 스크립트에 컨텍스트를 제공하는 개체가 포함될 수 있습니다. `bindings`

## 간단한 예 {#a-simple-example}

사용 클래스가 없는 HTL 구성 요소로 시작합니다. 하나의 파일로 구성되며 `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

또한 이 구성 요소가 렌더링할 컨텐츠를 다음 위치에 추가할 수 있습니다 `/content/my-example/`.

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

이 콘텐트에 액세스하면 HTL 파일이 실행됩니다. HTL 코드 내에서 컨텍스트 객체를 사용하여 현재 리소스 `properties` 에 액세스하고 이를 표시 `title``description` 합니다. 출력 HTML은 다음과 같습니다.

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### 사용 클래스 추가 {#adding-a-use-class}

표시되는 **info** 구성 요소는 사용 클래스(매우 간단한) 함수를 수행할 필요가 없습니다. 그러나 HTL에서는 불가능한 일을 해야 하므로 사용 클래스가 필요합니다. 그러나 다음 사항에 주의하십시오.

>[!NOTE]
>
>use-class는 HTL에서만 수행할 수 없는 경우에만 사용해야 합니다.

예를 들어, 구성 요소에 `info` 리소스의 `title` 및 `description` 속성을 표시하되 모두 소문자로만 표시한다고 가정합니다. HTL에는 문자열 소문자에 대한 메서드가 없으므로 use-class가 필요합니다. 이렇게 하려면 Java 사용 클래스를 추가하고 다음과 `info.html` 같이 변경합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-1}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

다음 섹션에서는 코드의 여러 부분을 살펴봅니다.

### 로컬 및 번들 Java 클래스 {#local-vs-bundle-java-class}

Java 사용 클래스는 다음과 같은 두 가지 방법으로 설치할 수 있습니다. **로컬** 또는 **번들**. 이 예에서는 로컬 설치를 사용합니다.

로컬 설치에서는 Java 소스 파일이 동일한 저장소 폴더에 HTL 파일과 함께 배치됩니다. 소스는 요청 시 자동으로 컴파일됩니다. 별도의 컴파일 또는 패키지 단계가 필요하지 않습니다.

번들 설치에서 Java 클래스는 표준 AEM 번들 배포 메커니즘을 사용하여 OSGi 번들 내에서 컴파일하고 배포되어야 합니다(번들 [Java 클래스](#bundled-java-class)참조).

>[!NOTE]
>
>use-class는 해당 구성 요소에 따라 다르면 **로컬 Java** use-class가 권장됩니다.
>
>Java 코드가 여러 HTL 구성 요소에서 액세스하는 서비스를 구현할 때 **번들 Java 사용** 클래스가 권장됩니다.

### Java 패키지 저장소 경로 {#java-package-is-repository-path}

로컬 설치를 사용하는 경우 use-class의 패키지 이름은 저장소 폴더 위치의 패키지 이름과 일치해야 하며 경로의 모든 하이픈이 패키지 이름에 밑줄로 대체됩니다.

이 경우 패키지 `Info.java` 는 다음 위치에 `/apps/my-example/components/info` 있습니다 `apps.my_example.components.info`.

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-1}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>저장소 항목 이름에 하이픈을 사용하는 것이 AEM 개발에서 권장됩니다. 그러나 Java 패키지 이름 내에서는 하이픈이 잘못되었습니다. 따라서 저장소 경로의 **모든 하이픈을 패키지 이름에 있는 밑줄로 변환해야 합니다**.

### 확장 `WCMUsePojo` {#extending-wcmusepojo}

HTL에 Java 클래스를 통합하는 방법은 여러 가지가 있지만(대체 요소 참조), 가장 간단한 방법은 `WCMUsePojo``WCMUsePojo` 클래스를 확장하는 것입니다.

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### 클래스를 초기화하는 중 {#initializing-the-class}

use-class가 다음에서 확장되면 `WCMUsePojo`메서드를 재정의하여 `activate` 초기화가 수행됩니다.

### /apps/my-example/component/info/Info.java {#apps-my-example-component-info-info-java-3}

```java
...

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

...

}
```

### 컨텍스트 {#context}

일반적으로 [activate](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) 메서드는 현재 컨텍스트(예: 현재 요청 및 리소스)를 기준으로 HTL 코드에 필요한 값을 미리 계산하고 저장하는 데 사용됩니다(멤버 변수).

이 `WCMUsePojo` 클래스는 HTL 파일 내에서 사용할 수 있는 동일한 컨텍스트 객체 세트에 대한 액세스 권한을 제공합니다( [전역 객체](global-objects.md)참조).

확장되는 클래스에서 컨텍스트 개체 `WCMUsePojo`는

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

또는, 일반적으로 사용되는 컨텍스트 객체는 적절한 **편의 방법으로 직접 액세스할 수 있습니다**.

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [페이지](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [페이지](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [디자이너](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [디자인](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [스타일](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [구성 요소](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [리소스](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Getter 메서드 {#getter-methods}

use-class가 초기화되면 HTL 파일이 실행됩니다. 이 단계에서 HTL은 일반적으로 use-class의 다양한 멤버 변수의 상태를 가져와서 프레젠테이션용으로 렌더링합니다.

HTL 파일 내에서 이러한 값에 대한 액세스를 제공하려면 다음 명명 규칙에 따라 use-class에서 사용자 지정 getter 메서드를 정의해야 합니다.

* 양식의 메서드는 HTL 파일 내에 `getXyz` 호출되는 개체 속성을 노출합니다 `xyz`.

다음 예에서 메서드 `getTitle` 와 `getDescription` 결과는 개체 속성 `title` 을 만들고 HTL 파일 컨텍스트 내에서 액세스할 `description` 수 있게 됩니다.

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-4}

```java
...

public class Info extends WCMUsePojo {

    ...

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

### data-sly-use 속성 {#data-sly-use-attribute}

이 `data-sly-use` 속성은 HTL 코드 내에서 use-class를 초기화하는 데 사용됩니다. 이 예에서 속성은 `data-sly-use` 클래스를 사용한다고 선언합니다 `Info`. 로컬 설치를 사용하고 있으므로 클래스의 로컬 이름만 사용할 수 있습니다. Java 소스 파일은 HTL 파일과 동일한 폴더에 있습니다. 번들 설치를 사용하는 경우 정규화된 클래스 이름을 지정해야 합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 로컬 식별자 {#local-identifier}

HTL 파일 `info` 에서 식별자 `data-sly-use.info`(표시된 점 뒤)가 클래스를 식별하는 데 사용됩니다. 이 식별자의 범위는 선언된 후 파일 내에서 전역적입니다. 문이 포함된 요소에만 국한되지 `data-sly-use` 않습니다.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 속성 가져오기 {#getting-properties}

그런 다음 식별자 `info` 를 사용하여 개체 속성 `title` 에 액세스하고 getter 메서드 및 `description` 를 통해 노출되었습니다 `Info.getTitle` `Info.getDescription`.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 출력 {#output}

이제 액세스할 때 다음 HTML이 `/content/my-example.html` 반환됩니다.

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## 기본 기능 이상의 기능 {#beyond-the-basics}

이 섹션에서는 위에 나와 있는 간단한 예제를 벗어나는 몇 가지 추가 기능을 소개합니다.

* use-class에 매개 변수를 전달합니다.
* 번들 Java 사용 클래스입니다.
* 대체 요소 `WCMUsePojo`

### 매개 변수 전달 {#passing-parameters}

초기화 시 매개 변수를 use-class로 전달할 수 있습니다. 예를 들어 다음과 같은 작업을 수행할 수 있습니다.

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

여기서 Adobe는 `text` 그러면 use-class가 검색하는 문자열을 대문자로 만들어 결과를 표시합니다 `info.upperCaseText`. 다음은 조정된 사용 클래스입니다.

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-5}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

    ...

    private String reverseText;

    @Override
    public void activate() throws Exception {

        ...

        String text = get("text", String.class);
        reverseText = new StringBuffer(text).reverse().toString();

    }

    public String getReverseText() {
        return reverseText;
    }

    ...
}
```

매개 변수는 메서드를 통해 `WCMUsePojo` 액세스합니다 [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

우리의 경우, 진술은 다음과 같습니다.

`get("text", String.class)`

그런 다음 이 문자열을 취소하고 메서드를 통해 노출합니다.

`getReverseText()`

### 데이터-사용 템플릿의 매개 변수만 전달 {#only-pass-parameters-from-data-sly-template}

위의 예가 기술적으로 올바르지만, 실제로 HTL의 값을 전달하여 use-class를 초기화하는 것은 적합하지 않습니다. 이 경우 문제의 값을 HTL 코드의 실행 컨텍스트에서 사용할 수 있을 때(또는, 일반적으로 이 값은 위와 같이 static임).

이유는 use-class가 항상 HTL 코드와 동일한 실행 컨텍스트에 액세스할 수 있기 때문입니다. 이를 통해 가져오기 기능의 우수 사례가 나타납니다.

>[!NOTE]
>
>매개 변수를 use-class로 전달하는 것은 전달해야 하는 매개 변수가 있는 다른 HTL 파일에서 호출되는 `data-sly-template` 파일에서 use-class를 사용하는 경우에만 수행해야 합니다.

예를 들어 기존 예제와 함께 별도의 `data-sly-template` 파일을 만듭니다. 새 파일을 불러드리겠습니다 `extra.html`. 여기에는 다음과 같은 `data-sly-template` 블록이 포함됩니다 `extra`.

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

템플릿 `extra`은 단일 매개 변수를 사용합니다 `text`. 그런 다음 Java use-class를 로컬 이름 `ExtraHelper` 으로 초기화하고 템플릿 매개 변수 `extraHelper` 값을 use-class 매개 변수 `text` 로 전달합니다 `text`.

템플릿의 본문은 속성 `extraHelper.reversedText` (백그라운드에서 실제로 호출)을 가져와 해당 값을 `ExtraHelper.getReversedText()`표시합니다.

Adobe는 다음과 같은 새로운 템플릿 `info.html` 을 사용하기 위해 기존 템플릿을 채택했습니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

이 파일에는 `info.html` 이제 Java use-class를 가져오는 원본 명령문과 템플릿 파일을 로컬 이름으로 가져오는 새 명령문 `data-sly-use` `Info` `extra`이 있습니다.

두 번째 템플릿 파일을 피하기 위해 `info.html` 파일 내에 템플릿 블록 `data-sly-use`을 배치할 수 있었지만 별도의 템플릿 파일은 더 일반적이고 재사용 가능합니다.

이 `Info` 클래스는 getter 메서드를 호출하고 해당 HTL 속성 `getLowerCaseTitle()` 및 `getLowerCaseDescription()` 을 통해 이전과 같이 `info.lowerCaseTitle` 사용됩니다 `info.lowerCaseDescription`.

그런 다음 템플릿 `data-sly-call` 에 대한 값 `extra` 을 수행하고 값을 매개 변수 `properties.description` 로 전달합니다 `text`.

Java use-class `Info.java` 가 새 텍스트 매개 변수를 처리하도록 변경되었습니다.

### `/apps/my-example/component/info/ExtraHelper.java` {#apps-my-example-component-info-extrahelper-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class ExtraHelper extends WCMUsePojo {
    private String reversedText;
    ...

    @Override
    public void activate() throws Exception {
        String text = get("text", String.class);
        reversedText = new StringBuilder(text).reverse().toString();

        ...
    }

    public String getReversedText() {
        return reversedText;
    }
}
```

매개 변수 `text` 가 함께 검색되고, 값 `get("text", String.class)`이 반전되고 getter를 통해 HTL 개체 `reversedText` 로 사용할 수 있습니다 `getReversedText()`.

### 번들 Java 클래스 {#bundled-java-class}

번들 사용 클래스를 사용하면 표준 OSGi 번들 배포 메커니즘을 사용하여 클래스가 AEM에 컴파일되고 패키징되어 배포되어야 합니다. 로컬 설치와는 대조적으로 use-class **패키지 선언의 이름은** 정상적으로 지정해야 합니다.

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

그리고 `data-sly-use` 문장은 지역 클래스 이름과 달리 정규화된 클래스 이름을 참조해야 합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### 대체 요소 `WCMUsePojo` {#alternatives-to-wcmusepojo}

Java 사용 클래스를 만드는 가장 일반적인 방법은 확장입니다 `WCMUsePojo`. 그러나 다른 옵션들은 많이 있습니다. 이러한 변형을 이해하기 위해 HTL 문이 백그라운드에서 작동하는 방식을 이해하는 데 도움이 `data-sly-use` 됩니다.

다음 `data-sly-use` 문이 있다고 가정합니다.

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

시스템은 다음과 같이 문을 처리합니다.

(1)

* HTL 파일과 동일한 디렉토리 `UseClass.java` 에 로컬 파일이 있는 경우 해당 클래스를 컴파일하고 로드합니다. 성공한 경우 (2)로 이동합니다.
* 그렇지 않은 경우 정규화된 클래스 이름 `UseClass` 으로 해석하고 OSGi 환경에서 로드해 봅니다. 성공한 경우 (2)로 이동합니다.
* 그렇지 않은 경우 HTL 또는 JavaScript 파일 `UseClass` 의 경로로 해석하고 해당 파일을 로드합니다. 성공적으로 goto (4)를 선택한 경우

(2)

* 전류를 `Resource` 조절하도록 노력하라 `UseClass`. 성공하면 (3)으로 이동합니다.
* 그렇지 않으면, 전류를 `Request` 조절하도록 노력하라 `UseClass`. 성공하면 (3)으로 이동합니다.
* 그렇지 않으면 인수 제로 생성자 `UseClass` 로 인스턴스화하십시오. 성공하면 (3)으로 이동합니다.

(3)

* HTL 내에서 새로 적용되거나 만들어진 개체를 이름에 바인딩합니다 `localName`.
* 구현 `UseClass` 이 [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) 구현되면, 현재 실행 컨텍스트를 전달하여 `init` `javax.scripting.Bindings` 개체의 형태로 메서드를 호출합니다.

(4)

* HTML 파일 `UseClass` 이 들어 있는 경로인 경우 템플릿 `data-sly-template`을 준비합니다.
* 그렇지 않은 경우 `UseClass` JavaScript use-class의 경로인 경우 use-class를 준비하십시오( [JavaScript Use-API 참조](use-api-javascript.md)).

위의 설명에 대한 몇 가지 중요 사항:

* 사용할 수 있는 클래스, `Resource`적응할 수 `Request`있는 클래스 또는 인수 제로 생성자가 있는 모든 클래스는 use-class일 수 있습니다. 이 클래스는 확장 `WCMUsePojo` 또는 구현하지 않아도 됩니다 `Use`.
* 그러나 use-class가 *구현된* 경우 `Use``init` 해당 메서드가 현재 컨텍스트와 함께 자동으로 호출되므로 해당 컨텍스트에 따라 초기화 코드를 여기에 배치할 수 있습니다.
* 확장되는 use-class `WCMUsePojo` 는 구현 시 특별한 경우입니다 `Use`. 편리한 컨텍스트 메서드를 제공하고 해당 `activate` 메서드는 자동으로 호출됩니다 `Use.init`.

### 인터페이스 사용 직접 구현 {#directly-implement-interface-use}

use-class를 만드는 가장 일반적인 방법은 확장이지만 인터페이스 자체를 직접 구현할 수도 `WCMUsePojo`[`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) 있습니다.

인터페이스는 다음 방법 하나만 정의합니다. `Use`

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

이 `init` 메서드는 모든 컨텍스트 객체와 use-class로 전달된 모든 매개 변수를 포함하는 `Bindings` 객체를 사용하여 클래스를 초기화할 때 호출됩니다.

모든 추가 기능(예: 동급 `WCMUsePojo.getProperties()`)은 개체를 사용하여 명시적으로 구현해야 [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html) 합니다. 예:

### `Info.java` {#info-java}

```java
import io.sightly.java.api.Use;

public class MyComponent implements Use {
   ...
    @Override
    public void init(Bindings bindings) {

        // All standard objects/binding are available
        Resource resource = (Resource)bindings.get("resource");
        ValueMap properties = (ValueMap)bindings.get("properties");
        ...

        // Parameters passed to the use-class are also available
        String param1 = (String) bindings.get("param1");
    }
    ...
}
```

확장 대신 `Use` 인터페이스를 직접 구현하기 위한 주요 사례는 기존 클래스의 하위 클래스를 use-class로 사용하려는 `WCMUsePojo` 경우입니다.

### 리소스에서 적응할 수 있습니다. {#adaptable-from-resource}

다른 옵션은 사용할 수 있는 도우미 클래스를 사용하는 것입니다 `org.apache.sling.api.resource.Resource`.

DAM 자산의 MIMETYPE을 표시하는 HTL 스크립트를 작성해야 한다고 가정해 보겠습니다. 이 경우 HTL 스크립트가 호출되면 JCR을 nodetype으로 래핑하는 컨텍스트 `Resource` 내에 `Node` 있게 됩니다 `dam:Asset`.

노드 `dam:Asset` 는 다음과 같은 구조를 가지고 있습니다.

### 저장소 구조 {#repository-structure}

```java
{
  "content": {
    "dam": {
      "geometrixx": {
        "portraits": {
          "jane_doe.jpg": {
            ...
          "jcr:content": {
            ...
            "metadata": {
              ...
            },
            "renditions": {
              ...
              "original": {
                ...
                "jcr:content": {
                  "jcr:primaryType": "nt:resource",
                  "jcr:lastModifiedBy": "admin",
                  "jcr:mimeType": "image/jpeg",
                  "jcr:lastModified": "Fri Jun 13 2014 15:27:39 GMT+0200",
                  "jcr:data": ...,
                  "jcr:uuid": "22e3c598-4fa8-4c5d-8b47-8aecfb5de399"
                }
              },
              "cq5dam.thumbnail.319.319.png": {
                  ...
              },
              "cq5dam.thumbnail.48.48.png": {
                  ...
              },
              "cq5dam.thumbnail.140.100.png": {
                  ...
              }
            }
          }  
        }
      }
    }
  }
}
```

예제 프로젝트 geometrixx의 일부로 기본 AEM 설치 기능이 포함된 자산(JPEG 이미지)이 표시됩니다. 자산이 호출되고 `jane_doe.jpg` 그 모방유형이 `image/jpeg`표시됩니다.

HTL 내에서 자산에 액세스하려면 문 [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) 의 클래스로 `data-sly-use` 선언한 다음 get 메서드를 사용하여 원하는 정보를 `Asset` 검색할 수 있습니다. 예:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

이 `data-sly-use` 문에서는 HTL이 전류를 `Resource` 에 맞게 `Asset` 조정하고 그것을 지역 이름으로 지정합니다 `asset`. 그런 다음 HTL `getMimeType` getter 축약형 `Asset` 을 사용하는 방법을 호출합니다. `asset.mimeType`.

### 요청의 적응성 {#adaptable-from-request}

또한 [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)

상기의 사용 클래스 적응형 경우와 마찬가지로 `Resource`명령문에 사용 클래스 적응을 지정할 [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) 수 `data-sly-use` 있습니다. 실행 시 현재 요청은 주어진 클래스에 맞게 조정되며, 결과 개체는 HTL 내에서 사용할 수 있게 됩니다.
