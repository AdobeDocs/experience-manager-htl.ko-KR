---
title: HTL Java Use-API
description: HTML 템플릿 언어(HTL) Java Use-API를 사용하면 HTL 파일이 사용자 지정 Java 클래스의 도우미 메서드에 액세스할 수 있습니다.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '2558'
ht-degree: 100%

---

# HTL Java Use-API {#htl-java-use-api}

HTML 템플릿 언어(HTL) Java Use-API를 사용하면 HTL 파일이 `data-sly-use`를 통해 사용자 지정 Java 클래스의 도우미 메서드에 액세스할 수 있습니다. 이를 통해 모든 복잡한 비즈니스 논리를 Java 코드에 캡슐화할 수 있지만 HTL 코드는 직접 마크업 생성만 처리합니다.

Java Use-API 개체는 POJO의 기본 생성자를 통해 특정 구현에 의해 인스턴스화된 간단한 POJO가 될 수 있습니다.

Use-API POJO는 다음 서명을 사용하여 init라고 하는 공용 메서드를 노출할 수도 있습니다.

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

`bindings` 맵에는 Use-API 개체가 처리에 사용할 수 있는 현재 실행된 HTL 스크립트에 컨텍스트를 제공하는 개체가 포함될 수 있습니다.

## 간단한 예 {#a-simple-example}

use-class가 없는 HTL 구성 요소부터 시작하겠습니다. 단일 파일 `/apps/my-example/components/info.html`로 구성됩니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

또한 `/content/my-example/`에서 렌더링할 이 구성 요소에 대한 몇 가지 콘텐츠도 추가합니다.

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

이 콘텐츠에 액세스하면 HTL 파일이 실행됩니다. HTL 코드 내에서 컨텍스트 개체 `properties`를 사용하여 현재 리소스의 `title` 및 `description`에 액세스하고 이를 표시합니다. 출력 HTML은 다음과 같습니다.

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Use-Class 추가 {#adding-a-use-class}

**info** 구성 요소는 (매우 간단한) 기능을 수행하기 위해 use-class가 필요하지 않습니다. 단, HTL에서 수행할 수 없는 작업을 수행해야 하므로 use-class가 필요한 경우가 있습니다. 하지만 다음 사항에 유의하십시오.

>[!NOTE]
>
>use-class는 HTL만으로는 수행할 수 없는 작업에만 사용해야 합니다.

예를 들어 `info` 구성 요소가 리소스의 `title` 및 `description` 속성을 표시하지만 모두 소문자로 표시하기를 원한다고 가정합니다. HTL에는 문자열을 소문자로 변환하는 메서드가 없으므로 use-class가 필요합니다. Java use-class를 추가하고 `info.html`을 다음과 같이 변경하여 이를 수행할 수 있습니다.

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

### 로컬과 번들 Java 클래스 비교 {#local-vs-bundle-java-class}

Java use-class는 **로컬** 또는 **번들**&#x200B;의 두 가지 방법으로 설치할 수 있습니다. 이 예에서는 로컬 설치를 사용합니다.

로컬 설치에서 Java 소스 파일은 HTL 파일과 함께 동일한 저장소 폴더에 배치됩니다. 소스는 요청 시 자동으로 컴파일됩니다. 별도의 컴파일 또는 패키징 단계가 필요하지 않습니다.

번들 설치에서 Java 클래스는 표준 AEM 번들 배포 메커니즘을 사용하여 OSGi 번들 내에서 컴파일되고 배포되어야 합니다([번들 Java 클래스](#bundled-java-class) 참조).

>[!NOTE]
>
>use-class가 해당 구성 요소와 관련된 경우 **로컬 Java use-class**&#x200B;가 권장됩니다.
>
>Java 코드가 여러 HTL 구성 요소에서 액세스되는 서비스를 구현하는 경우 **번들 Java 사용 클래스**&#x200B;가 권장됩니다.

### Java 패키지는 저장소 경로입니다. {#java-package-is-repository-path}

로컬 설치를 사용하는 경우 use-class의 패키지 이름은 저장소 폴더 위치의 패키지 이름과 일치해야 하며 경로의 모든 하이픈은 패키지 이름의 밑줄로 대체됩니다.

이 경우 `Info.java`는 `/apps/my-example/components/info`에 있으므로 패키지는 `apps.my_example.components.info`입니다.

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
>저장소 항목 이름에 하이픈을 사용하는 것은 AEM 개발에서 권장되는 방법입니다. 단, 하이픈은 Java 패키지 이름 내에서 사용할 수 없습니다. 이러한 이유로 **저장소 경로의 모든 하이픈은 패키지 이름에서 밑줄로 변환되어야 합니다**.

### `WCMUsePojo` 확장 {#extending-wcmusepojo}

Java 클래스를 HTL과 통합하는 방법은 여러 가지가 있지만(`WCMUsePojo`에 대한 대안 참조) 가장 간단한 방법은 `WCMUsePojo` 클래스를 확장하는 것입니다.

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### 클래스 초기화 {#initializing-the-class}

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

일반적으로 [활성화](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) 메서드는 현재 컨텍스트(예: 현재 요청 및 리소스)를 기반으로 HTL 코드에 필요한 값을 미리 계산하고 (멤버 변수에) 저장하는 데 사용됩니다.

`WCMUsePojo` 클래스는 HTL 파일 내에서 사용 가능한 것과 동일한 컨텍스트 개체 집합에 대한 액세스를 제공합니다([전역 개체](global-objects.md)참조).

`WCMUsePojo`을 확장하는 클래스에서 컨텍스트 개체는 다음을 사용하여 이름으로 액세스할 수 있습니다.

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

또는 적절한 **편의 메서드**&#x200B;를 통해 일반적으로 사용되는 컨텍스트 개체에 직접 액세스할 수 있습니다.

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [페이지](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [페이지](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [디자이너](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [디자인](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [스타일](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [구성 요소](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [리소스](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### getter 메서드 {#getter-methods}

use-class가 초기화되면 HTL 파일이 실행됩니다. 이 단계에서 HTL은 일반적으로 use-class의 다양한 멤버 변수의 상태를 가져와 표시하기 위해 렌더링합니다.

HTL 파일 내에서 이러한 값에 대한 액세스를 제공하려면 다음 명명 규칙에 따라 use-class에서 사용자 지정 getter 메서드를 정의해야 합니다.

* `getXyz` 형식의 메서드는 `xyz`라는 개체 속성을 HTL 파일 내에서 노출합니다.

다음 예에서 `getTitle` 및 `getDescription` 메서드는 HTL 파일의 컨텍스트 내에서 액세스할 수 있는 개체 속성 `title` 및 `description`를 생성합니다.

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

`data-sly-use` 속성은 HTL 코드 내에서 use-class를 초기화하는 데 사용됩니다. 이 예에서 `data-sly-use` 속성은 `Info` 클래스를 사용하겠다고 선언합니다. 로컬 설치(Java 소스 파일이 HTL 파일과 동일한 폴더에 있음)를 사용하기 때문에 클래스의 로컬 이름만 사용할 수 있습니다. 번들 설치를 사용하는 경우 정규화된 클래스 이름을 지정해야 합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 로컬 식별자 {#local-identifier}

식별자 `info`(`data-sly-use.info`의 점 뒤)는 클래스를 식별하기 위해 HTL 파일 내에서 사용됩니다. 선언된 후에 이 식별자의 범위는 파일 내에서 전역입니다. `data-sly-use` 문을 포함하는 요소에 국한되지 않습니다.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 속성 가져오기 {#getting-properties}

그런 다음 식별자 `info`는 getter 메서드 `Info.getTitle` 및 `Info.getDescription`를 통해 노출된 개체 속성 `title` 및 `description`에 액세스하는 데 사용됩니다.

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

## 추가 정보 {#beyond-the-basics}

이 섹션에서는 위의 간단한 예 이상의 몇 가지 추가 기능을 소개합니다.

* use-class에 매개 변수를 전달합니다.
* 번들 Java use-class.
* `WCMUsePojo`에 대한 대안

### 매개 변수 전달 {#passing-parameters}

매개 변수는 초기화 시 use-class에 전달할 수 있습니다. 예를 들어 다음과 같이 할 수 있습니다.

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

여기에서는 `text`라는 매개 변수를 전달합니다. 그런 다음 use-class는 검색한 문자열을 대문자로 표시하고 `info.upperCaseText`로 결과를 표시합니다. 조정된 use-class:는 다음과 같습니다.

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

매개 변수는 `WCMUsePojo` 메서드 [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)을 통해 액세스됩니다.

이 경우에서 명령문은 다음과 같습니다.

`get("text", String.class)`

그런 다음 문자열이 반전되고 메서드를 통해 노출됩니다.

`getReverseText()`

### data-sly-template에서 매개 변수만 전달 {#only-pass-parameters-from-data-sly-template}

위의 예는 기술적으로 정확하지만 해당 값을 HTL 코드의 실행 컨텍스트에서 사용할 수 있을 때(또는 위와 같이 평범하게 값이 정적일 때) HTL에서 값을 전달하여 use-class를 초기화하는 것은 실제로 의미가 없습니다.

use-class가 항상 HTL 코드와 동일한 실행 컨텍스트에 액세스할 수 있기 때문입니다. 이것은 모범 사례의 가져오기 지점을 나타냅니다.

>[!NOTE]
>
>전달해야 하는 매개 변수가 있는 다른 HTL 파일에서 자체적으로 호출되는 `data-sly-template` 파일에서 use-class가 사용될 때만 매개 변수를 use-class에 전달해야 합니다.

예를 들어 기존 예와 더불어 별도의 `data-sly-template` 파일을 생성해 보겠습니다. 파일을 `extra.html`이라고 합니다. 여기에는 `extra`라는 `data-sly-template` 블록이 포함되어 있습니다.

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

템플릿 `extra`는 단일 매개 변수 `text`를 사용합니다. 그런 다음 Java use-class `ExtraHelper`를 로컬 이름 `extraHelper`로 초기화하고 템플릿 매개 변수 `text`의 값을 use-class 매개 변수 `text`로 전달합니다.

템플릿의 본문은 속성 `extraHelper.reversedText`(후드에서 실제로 `ExtraHelper.getReversedText()`를 호출함)를 가져오고 해당 값을 표시합니다.

또한 이 새 템플릿을 사용하도록 기존 `info.html`을 조정합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

`info.html` 파일에는 이제 두 개의 `data-sly-use` 명령문이 포함되어 있습니다. `Info` Java use-class를 가져오는 원래 명령문과 로컬 이름 `extra`로 템플릿 파일을 가져오는 새 명령문입니다.

두 번째 `data-sly-use`를 피하기 위해 `info.html` 파일 내부에 템플릿 블록을 배치할 수도 있었겠지만 별도의 템플릿 파일이 더 일반적이고 재사용이 가능합니다.

`Info` 클래스는 이전과 같이 사용되며 해당 HTL 속성 `info.lowerCaseTitle` 및 `info.lowerCaseDescription`를 통해 getter 메서드 `getLowerCaseTitle()` 및 `getLowerCaseDescription()`을 호출합니다.

그런 다음 템플릿 `extra`에 `data-sly-call`을 수행하고 `properties.description` 값을 `text` 매개 변수로서 전달합니다.

Java use-class `Info.java`가 새 텍스트 매개 변수를 처리하도록 변경되었습니다.

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

`text` 매개 변수는 `get("text", String.class)`로 검색되며 값이 반전되어 getter `getReversedText()`를 통해 HTL 개체 `reversedText`로서 사용할 수 있습니다.

### 번들 Java 클래스 {#bundled-java-class}

번들 use-class에서 표준 OSGi 번들 배포 메커니즘을 사용하여 AEM에서 클래스를 컴파일하고 패키징하고 배포해야 합니다. 로컬 설치와 달리 use-class **패키지 선언**&#x200B;의 이름은 일반적으로 다음과 같이 지정해야 합니다.

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

그리고 `data-sly-use` 문은 로컬 클래스 이름이 아닌 정규화된 클래스 이름을 참조해야 합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### `WCMUsePojo`에 대한 대안 {#alternatives-to-wcmusepojo}

Java use-class를 만드는 가장 일반적인 방법은 `WCMUsePojo`를 확장하는 것입니다. 하지만 다른 여러 옵션들도 있습니다. HTL `data-sly-use` 문이 내부에서 어떻게 작동하는지 이해하는 것이 이러한 변형을 이해하는 데 도움이 됩니다.

다음 `data-sly-use` 문이 있다고 가정합니다.

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

시스템은 다음과 같이 명령문을 처리합니다.

(1)

* HTL 파일과 동일한 디렉터리에 로컬 파일`UseClass.java`가 있는 경우 해당 클래스를 컴파일하고 로드하십시오. 성공하면 (2)로 이동합니다.
* 그렇지 않으면 `UseClass`를 완전한 클래스 이름으로 해석하고 OSGi 환경에서 로드를 시도하십시오. 성공하면 (2)로 이동합니다.
* 그렇지 않으면 `UseClass`를 HTL 또는 JavaScript 파일의 경로로 해석하고 해당 파일을 로드하십시오. 성공하면 (4)로 이동합니다.

(2)

* 현재 `Resource`를 `UseClass`로 조정해 보십시오. 성공하면 (3)으로 이동합니다.
* 그렇지 않으면 현재 `Request`를 `UseClass`로 조정해 보십시오. 성공하면 (3)으로 이동합니다.
* 그렇지 않으면 인수가 없는 생성자로 `UseClass`를 인스턴스화하십시오. 성공하면 (3)으로 이동합니다.

(3)

* HTL 내에서 새로 적용되거나 생성된 개체를 `localName`이라는 이름에 바인딩하십시오.
* `UseClass`가 [`io.sightly.java.api.Use`](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)를 구현하는 경우 `init` 메서드를 호출하여 현재 실행 컨텍스트(`javax.scripting.Bindings` 개체의 형태로)를 전달하십시오.

(4)

* `UseClass`가 `data-sly-template`이 포함된 HTL 파일의 경로인 경우 템플릿을 준비하십시오.
* 그렇지 않으면 `UseClass`가 JavaScript use-class에 대한 경로인 경우 use-class를 준비하십시오([JavaScript Use-API](use-api-javascript.md) 참조).

위의 설명에 대한 몇 가지 중요한 사항:

* `Resource`에서 조정할 수 있거나 `Request`에서 조정할 수 있거나 인수가 없는 생성자가 있는 모든 클래스는 use-class가 될 수 있습니다. 클래스는 `WCMUsePojo`를 확장하거나 `Use`를 구현할 필요가 없습니다.
* 단, use-class가 `Use`를 *구현하면* 해당 `init`메서드가 현재 컨텍스트와 함께 자동으로 호출되어 해당 컨텍스트에 따라 초기화 코드를 배치할 수 있습니다.
* `WCMUsePojo`를 확장하는 use-class는 `Use`를 구현하는 특별한 경우일 뿐입니다. 이는 편리한 컨텍스트 메서드를 제공하며 해당 `activate` 메서드는 `Use.init`에서 자동으로 호출됩니다.

### 인터페이스 사용 직접 구현 {#directly-implement-interface-use}

use-class를 생성하는 가장 일반적인 방법은 `WCMUsePojo`를 확장하는 것이지만, [`io.sightly.java.api.Use`](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) 인터페이스 자체를 직접 구현하는 것도 가능합니다.

`Use` 인터페이스는 하나의 메서드만 정의합니다.

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/kr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

`init` 메서드는 모든 컨텍스트 개체와 use-class에 전달된 매개 변수를 보유하는 `Bindings` 개체를 사용하여 클래스를 초기화할 때 호출됩니다.

모든 추가 기능(예를 들어 `WCMUsePojo.getProperties()`)은 [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html) 개체를 사용하여 명시적으로 구현해야 합니다. 예:

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

`WCMUsePojo`를 확장하는 대신 `Use` 인터페이스를 직접 구현하는 주요 사례는 이미 존재하는 클래스의 하위 클래스를 use-class로 사용하려는 경우입니다.

### 리소스에서 조정 가능 {#adaptable-from-resource}

또 다른 옵션은 `org.apache.sling.api.resource.Resource`에서 조정할 수 있는 도우미 클래스를 사용하는 것입니다.

DAM 에셋의 mimetype을 표시하는 HTL 스크립트를 작성해야 한다고 가정해 보겠습니다. 이 경우 HTL 스크립트가 호출될 때 노드 유형이 `dam:Asset`인 JCR `Node`를 래핑하는 `Resource` 컨텍스트 내에 있다는 것을 압니다.

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

여기에서는 예제 프로젝트 geometrixx의 일부로 AEM의 기본 설치와 함께 제공되는 에셋(JPEG 이미지)을 보여 줍니다. 에셋은 `jane_doe.jpg`이고 mimetype은 `image/jpeg`입니다.

HTL 내에서 에셋에 액세스하려면 [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)를 `data-sly-use` 문에서 클래스로 선언한 다음 `Asset`의 get 메서드를 사용하여 원하는 정보를 검색할 수 있습니다. 예:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

`data-sly-use` 문은 HTL이 현재 `Resource`를 `Asset`으로 변경하고 로컬 이름 `asset`를 지정하도록 지시합니다. 그런 다음 HTL getter 줄임 표기 `asset.mimeType`을 사용하여 `Asset`의 `getMimeType` 메서드를 호출합니다.

### 요청에서 조정 가능 {#adaptable-from-request}

[`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)에서 조정할 수 있는 모든 클래스를 use-class로 사용할 수도 있습니다.

`Resource`에서 조정할 수 있는 위의 use-class의 경우와 같이, [`SlingHttpServletRequest`](https://helpx.adobe.com/kr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)에서 조정할 수 있는 use-class는 `data-sly-use` 문에서 지정할 수 있습니다. 실행 시 현재 요청이 지정된 클래스에 맞게 조정되고 결과 개체가 HTL 내에서 사용할 수 있게 됩니다.
