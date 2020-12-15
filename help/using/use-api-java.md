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

HTML 템플릿 언어(HTL) Java Use-API를 사용하면 HTL 파일이 `data-sly-use`을 통해 사용자 지정 Java 클래스의 도우미 메서드에 액세스할 수 있습니다. 이렇게 하면 모든 복잡한 비즈니스 로직을 Java 코드로 캡슐화할 수 있고, HTL 코드는 직접 마크업 프로덕션에만 다룹니다.

Java Use-API 개체는 POJO의 기본 생성자를 통해 특정 구현에 의해 인스턴스화된 간단한 POJO일 수 있습니다.

Use-API POJO는 다음 서명을 사용하여 init라는 public 메서드를 표시할 수도 있습니다.

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

`bindings` 맵에는 Use-API 객체가 처리에 사용할 수 있는 현재 실행된 HTL 스크립트에 컨텍스트를 제공하는 객체가 포함될 수 있습니다.

## 간단한 예 {#a-simple-example}

use-class가 없는 HTL 구성 요소로 시작합니다. 단일 파일 `/apps/my-example/components/info.html`으로 구성됩니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

이 구성 요소에 대한 일부 내용을 `/content/my-example/`에서 렌더링할 수도 있습니다.

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

이 콘텐트에 액세스하면 HTL 파일이 실행됩니다. HTL 코드 내에서 컨텍스트 객체 `properties`을 사용하여 현재 리소스의 `title` 및 `description`에 액세스하여 표시합니다. 출력 HTML은 다음과 같습니다.

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Use-Class {#adding-a-use-class} 추가

이 구성 요소가 있는 **info** 구성 요소는 사용 클래스가 필요하지 않으므로(매우 간단한) 함수를 수행할 수 있습니다. 그러나 HTL에서는 할 수 없는 일을 해야 하는 경우가 있으므로 사용 클래스가 필요합니다. 그러나 다음 사항에 유의하십시오.

>[!NOTE]
>
>use-class는 HTL에서만 수행할 수 없는 경우에만 사용해야 합니다.

예를 들어 `info` 구성 요소가 리소스의 `title` 및 `description` 속성을 표시하지만 모두 소문자가 되도록 가정해 봅니다. HTL에는 문자열 소문자용 메서드가 없으므로 use-class가 필요합니다. 다음과 같이 Java use-class를 추가하고 `info.html`을(를) 변경하면 됩니다.

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

### 로컬 및 번들 Java 클래스 {#local-vs-bundle-java-class} 비교

Java 사용 클래스는 다음과 같은 두 가지 방법으로 설치할 수 있습니다.**local** 또는 **bundle**. 이 예에서는 로컬 설치를 사용합니다.

로컬 설치에서 Java 소스 파일은 동일한 저장소 폴더에 HTL 파일과 함께 배치됩니다. 소스는 요청 시 자동으로 컴파일됩니다. 별도의 컴파일 또는 패키징 단계가 필요하지 않습니다.

번들 설치에서 Java 클래스는 표준 AEM 번들 배포 메커니즘을 사용하여 OSGi 번들 내에서 컴파일되고 배포되어야 합니다([번들로 제공되는 Java 클래스](#bundled-java-class) 참조).

>[!NOTE]
>
>use-class가 해당 구성 요소에 특정한 경우 **로컬 Java use-class**&#x200B;를 사용하는 것이 좋습니다.
>
>Java 코드가 여러 HTL 구성 요소에서 액세스하는 서비스를 구현할 때 **번들 Java use-class**&#x200B;를 사용하는 것이 좋습니다.

### Java 패키지가 저장소 경로 {#java-package-is-repository-path}

로컬 설치를 사용하는 경우 use-class의 패키지 이름은 저장소 폴더 위치의 패키지 이름과 일치해야 하고 경로의 모든 하이픈은 패키지 이름에 밑줄로 대체됩니다.

이 경우 `Info.java`은(는) `/apps/my-example/components/info`에 있으므로 패키지는 `apps.my_example.components.info`입니다.

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
>저장소 항목 이름에 하이픈을 사용하는 것은 AEM 개발 시 권장되는 방법입니다. 그러나 Java 패키지 이름 내에서는 하이픈이 잘못되었습니다. 따라서 저장소 경로의 **모든 하이픈을 패키지 이름**&#x200B;의 밑줄로 변환해야 합니다.

### `WCMUsePojo` {#extending-wcmusepojo} 확장

HTL에 Java 클래스를 통합하는 방법은 여러 가지가 있지만(`WCMUsePojo`의 대체 요소 참조), 가장 간단한 방법은 `WCMUsePojo` 클래스를 확장하는 것입니다.

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### {#initializing-the-class} 클래스를 초기화하는 중

use-class가 `WCMUsePojo`에서 확장되면 `activate` 메서드를 재정의하여 초기화가 수행됩니다.

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

`WCMUsePojo` 클래스는 HTL 파일 내에서 사용할 수 있는 동일한 컨텍스트 개체 집합에 대한 액세스를 제공합니다( [전역 개체](global-objects.md) 참조).

`WCMUsePojo`을(를) 확장하는 클래스에서 컨텍스트 객체는

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

또는 일반적으로 사용되는 컨텍스트 개체는 적절한 **편의 메서드**&#x200B;에 의해 직접 액세스할 수 있습니다.

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

HTL 파일 내에서 이러한 값에 대한 액세스를 제공하려면 다음 명명 규칙에 따라 use-class에서 사용자 정의 getter 메서드를 정의해야 합니다.

* `getXyz` 형식의 메서드는 HTL 파일 내에 `xyz`라는 개체 속성을 표시합니다.

다음 예제에서 `getTitle` 및 `getDescription` 메서드는 HTL 파일의 컨텍스트 내에서 객체 속성 `title` 및 `description`에 액세스할 수 있게 됩니다.

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

### data-sly-use 특성 {#data-sly-use-attribute}

`data-sly-use` 속성은 HTL 코드 내에서 use-class를 초기화하는 데 사용됩니다. 이 예제에서 `data-sly-use` 속성은 `Info` 클래스를 사용한다고 선언합니다. 로컬 설치를 사용하고 있기 때문에(Java 소스 파일을 HTL 파일과 동일한 폴더에 배치함) 클래스의 로컬 이름만 사용할 수 있습니다. 번들 설치를 사용하는 경우 정규화된 클래스 이름을 지정해야 합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 로컬 식별자 {#local-identifier}

`data-sly-use.info`의 점 뒤에 있는 식별자 `info`는 HTL 파일 내에서 클래스를 식별하는 데 사용됩니다. 이 식별자의 범위는 선언된 후 파일 내에서 전역적입니다. `data-sly-use` 문을 포함하는 요소에만 국한되지 않습니다.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 속성 {#getting-properties}을(를) 가져오는 중

그런 다음 식별자 `info`은 getter 메서드 `Info.getTitle` 및 `Info.getDescription`를 통해 노출된 개체 속성 `title` 및 `description`에 액세스하는 데 사용됩니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 출력 {#output}

이제 `/content/my-example.html`에 액세스하면 다음 HTML이 반환됩니다.

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## 기본 사항 외의 {#beyond-the-basics}

이 섹션에서는 위의 간단한 예제를 넘는 몇 가지 추가 기능을 살펴보겠습니다.

* use-class에 매개 변수를 전달합니다.
* 번들 Java use-class.
* `WCMUsePojo`의 대체 요소

### 매개 변수 {#passing-parameters} 전달

매개 변수는 초기화 시 use-class에 전달할 수 있습니다. 예를 들어 다음과 같은 작업을 수행할 수 있습니다.

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

여기서는 `text`이라는 매개 변수를 전달합니다. 그런 다음 use-class는 검색하고 결과를 `info.upperCaseText`로 표시합니다. 다음은 조정된 사용 클래스입니다.

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

매개 변수는 `WCMUsePojo` 메서드 [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)를 통해 액세스합니다.

우리의 경우에, 그 진술은 다음과 같습니다.

`get("text", String.class)`

그런 다음 이 문자열을 취소하고 메서드를 통해 노출합니다.

`getReverseText()`

### data-sly-template {#only-pass-parameters-from-data-sly-template}의 매개 변수만 전달

위의 예가 기술적으로 올바르지만 HTL의 값을 전달하여 use-class를 초기화하는 것은 사실상 의미가 없습니다. HTL 코드의 실행 컨텍스트에서 해당 값을 사용할 수 있을 때(또는, 일반적으로 이 값은 위와 같이 static임).

이유는 use-class가 항상 HTL 코드와 동일한 실행 컨텍스트에 액세스할 수 있기 때문입니다. 가져오기 작업 시 유용한 정보가 표시됩니다.

>[!NOTE]
>
>use-class에 매개 변수를 전달하는 것은 전달해야 하는 매개 변수가 있는 다른 HTL 파일에서 호출되는 use-class를 `data-sly-template` 파일에 사용하는 경우에만 수행해야 합니다.

예를 들어 기존 예제와 함께 별도의 `data-sly-template` 파일을 만듭니다. 새 파일 `extra.html`을(를) 호출합니다. 여기에는 `extra`이라는 `data-sly-template` 블록이 포함됩니다.

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

템플릿 `extra`은(는) 단일 매개 변수 `text`을 사용합니다. 그런 다음 Java use-class `ExtraHelper`을 로컬 이름 `extraHelper`으로 초기화하고 템플릿 매개 변수 `text`의 값을 use-class 매개 변수 `text`으로 전달합니다.

템플릿의 본문은 `extraHelper.reversedText` 속성을 가져오고(백그라운드에서 실제로 `ExtraHelper.getReversedText()` 호출) 해당 값을 표시합니다.

또한 이 새 템플릿을 사용하도록 기존 `info.html`을(를) 적용합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

이제 `info.html` 파일에는 두 개의 `data-sly-use` 문이 포함되어 있습니다. 이 명령문은 `Info` Java use-class를 가져오는 원본 명령문과 로컬 이름 `extra` 아래에 템플릿 파일을 가져오는 새 명령문입니다.

두 번째 `data-sly-use`을(를) 피하기 위해 `info.html` 파일 내에 템플릿 블록을 배치할 수 있었지만 별도의 템플릿 파일은 더 일반적이며 더 재사용 가능합니다.

`Info` 클래스는 이전 버전으로 사용되어 해당 HTL 속성 `info.lowerCaseTitle` 및 `info.lowerCaseDescription`을(를) 통해 getter 메서드 `getLowerCaseTitle()` 및 `getLowerCaseDescription()`를 호출합니다.

그런 다음 `data-sly-call`을 템플릿 `extra`에 수행하고 `properties.description` 값을 매개 변수 `text`으로 전달합니다.

Java use-class `Info.java`이(가) 새 텍스트 매개 변수를 처리하도록 변경되었습니다.

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

`text` 매개 변수는 `get("text", String.class)`과(와) 함께 검색되고, 값이 반전되어 getter `getReversedText()`을 통해 HTL 객체 `reversedText`로 사용할 수 있게 됩니다.

### 번들 Java 클래스 {#bundled-java-class}

번들 사용 클래스를 사용하면 표준 OSGi 번들 배포 메커니즘을 사용하여 클래스를 컴파일하고 패키지화하여 AEM에 배포해야 합니다. 로컬 설치와는 대조적으로 use-class **package 선언**&#x200B;의 이름은 정상적으로 지정해야 합니다.

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

및 `data-sly-use` 문은 로컬 클래스 이름과 달리 정규화된 클래스 이름을 참조해야 합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### `WCMUsePojo` {#alternatives-to-wcmusepojo}의 대체 요소

Java use-class를 만드는 가장 일반적인 방법은 `WCMUsePojo`을 확장하는 것입니다. 하지만 다른 옵션도 많이 있습니다. 이러한 변형을 이해하려면 HTL `data-sly-use` 문이 백그라운드에서 작동하는 방식을 이해하는 데 도움이 됩니다.

다음 `data-sly-use` 문이 있다고 가정합니다.

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

시스템은 다음과 같이 문을 처리합니다.

(1)

* HTL 파일과 동일한 디렉토리에 로컬 파일 `UseClass.java`이 있는 경우 해당 클래스를 컴파일하고 로드합니다. 성공한 경우 (2)로 이동합니다.
* 그렇지 않으면 `UseClass`을(를) 정규화된 클래스 이름으로 해석하고 OSGi 환경에서 로드해 보십시오. 성공한 경우 (2)로 이동합니다.
* 그렇지 않은 경우 `UseClass`을 HTL 또는 JavaScript 파일의 경로로 해석하고 해당 파일을 로드합니다. 성공적으로 goto (4)를 선택한 경우.

(2)

* 현재 `Resource`을(를) `UseClass`에 적용하려고 합니다. 성공하면 (3)로 이동합니다.
* 그렇지 않은 경우 현재 `Request`을 `UseClass`에 적용하십시오. 성공하면 (3)로 이동합니다.
* 그렇지 않은 경우 인수 제로 생성자로 `UseClass`을(를) 인스턴스화하십시오. 성공하면 (3)로 이동합니다.

(3)

* HTL 내에서 새로 적용되거나 만들어진 개체를 `localName` 이름으로 바인딩합니다.
* `UseClass`이 [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)를 구현하면 `init` 메서드를 호출하여 현재 실행 컨텍스트를 전달합니다(`javax.scripting.Bindings` 객체의 형식).

(4)

* `UseClass`이(가) `data-sly-template`이 포함된 HTML 파일의 경로인 경우 템플릿을 준비합니다.
* 그렇지 않은 경우 `UseClass`이(가) JavaScript use-class의 경로인 경우 use-class를 준비합니다([JavaScript Use-API](use-api-javascript.md) 참조).

위의 설명에 대한 몇 가지 중요 사항:

* `Resource`에서 적응할 수 있는 클래스, `Request`에서 적응할 수 있는 클래스 또는 인수 제로 생성자가 있는 클래스는 use-class일 수 있습니다. 클래스는 `WCMUsePojo`을(를) 확장하거나 `Use`을(를) 구현할 필요가 없습니다.
* 그러나 use-class *이*&#x200B;을(를) 구현할 경우 해당 `init` 메서드는 현재 컨텍스트와 함께 자동으로 호출되므로 해당 컨텍스트에 의존하는 초기화 코드를 해당 컨텍스트에 배치할 수 있습니다.`Use`
* `WCMUsePojo`을(를) 확장하는 use-class는 `Use`을(를) 구현하는 특별한 경우일 뿐입니다. 이 메서드는 편의성 컨텍스트 메서드를 제공하며 `Use.init`에서 해당 `activate` 메서드가 자동으로 호출됩니다.

### 인터페이스 직접 구현 {#directly-implement-interface-use} 사용

use-class를 만드는 가장 일반적인 방법은 `WCMUsePojo`을(를) 확장하는 것이지만 [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) 인터페이스 자체를 직접 구현할 수도 있습니다.

`Use` 인터페이스는 다음 방법 하나만 정의합니다.

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

`init` 메서드는 모든 컨텍스트 객체와 use-class로 전달된 모든 매개 변수를 포함하는 `Bindings` 객체가 있는 클래스 초기화 시 호출됩니다.

`WCMUsePojo.getProperties()`에 해당하는 모든 추가 기능(예: [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html) 개체를 사용하여 명시적으로 구현해야 합니다. 예:

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

`WCMUsePojo`을(를) 확장하지 않고 직접 `Use` 인터페이스를 구현하는 주요 사례는 이미 기존 클래스의 하위 클래스를 use-class로 사용하려는 경우입니다.

### 리소스 {#adaptable-from-resource}에서 적응할 수 있습니다.

다른 옵션은 `org.apache.sling.api.resource.Resource`에서 사용할 수 있는 도우미 클래스를 사용하는 것입니다.

DAM 자산의 MIMETYPE을 표시하는 HTL 스크립트를 작성해야 한다고 가정해 보겠습니다. 이 경우 HTL 스크립트가 호출될 때 JCR `Node`을 nodetype `dam:Asset`으로 래핑하는 `Resource` 컨텍스트 내에 있게 됩니다.

`dam:Asset` 노드에는 다음과 같은 구조가 있습니다.

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

다음은 예제 프로젝트 geometrixx의 일부로 AEM의 기본 설치와 함께 제공되는 에셋(JPEG 이미지)입니다. 에셋은 `jane_doe.jpg`이고 해당 MIMETYPE은 `image/jpeg`입니다.

HTL 내에서 자산에 액세스하려면 [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)을 `data-sly-use` 문의 클래스로 선언한 다음 `Asset`의 get 메서드를 사용하여 원하는 정보를 검색할 수 있습니다. 예:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

`data-sly-use` 문은 HTL이 현재 `Resource`을 `Asset`로 조정하고 로컬 이름 `asset`을 지정합니다. 그런 다음 HTL getter 축약어를 사용하여 `Asset`의 `getMimeType` 메서드를 호출합니다.`asset.mimeType`.

### 요청 {#adaptable-from-request}에서 적응할 수 있습니다.

또한 [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)에서 사용할 수 있는 모든 클래스는 use-class로 사용할 수도 있습니다.

위의 use-class 적응형 경우와 마찬가지로 `Resource`에서도 [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)의 use-class 적응을 `data-sly-use` 문에서 지정할 수 있습니다. 현재 요청이 실행되면 지정된 클래스에 맞게 조정되고 HTL 내에서 결과 객체를 사용할 수 있게 됩니다.
