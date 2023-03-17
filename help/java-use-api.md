---
title: HTL Java Use-API
description: HTL Java Use-API를 사용하면 HTL 파일이 사용자 지정 Java 클래스의 도우미 메서드에 액세스할 수 있습니다.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: f295fe81062f87d4f3f46705fb1e3d706d2a9d49
workflow-type: ht
source-wordcount: '1510'
ht-degree: 100%

---


# HTL Java Use-API {#htl-java-use-api}

HTL Java Use-API를 사용하면 HTL 파일이 사용자 지정 Java 클래스의 도우미 메서드에 액세스할 수 있습니다.

## 사용 사례 {#use-case}

HTL Java Use-API를 사용하면 HTL 파일이 `data-sly-use`를 통해 사용자 지정 Java 클래스의 도우미 메서드에 액세스할 수 있습니다. 이를 통해 모든 복잡한 비즈니스 논리를 Java 코드에 캡슐화할 수 있지만 HTL 코드는 직접 마크업 생성만 처리합니다.

Java Use-API 오브젝트는 POJO의 기본 생성자를 통해 특정 구현에 의해 인스턴스화된 간단한 POJO가 될 수 있습니다.

Use-API POJO는 다음 서명을 사용하여 init라고 하는 공용 메서드를 노출할 수도 있습니다.

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

`bindings` 맵에는 Use-API 오브젝트가 처리에 사용할 수 있는 현재 실행된 HTL 스크립트에 컨텍스트를 제공하는 오브젝트가 포함될 수 있습니다.

## 간단한 예 {#a-simple-example}

이 예제에서는 Use-API의 사용 방법에 대해 설명합니다.

>[!NOTE]
>
>이 예제는 사용 방법을 간단하게 설명하기 위해 간소화되어 있습니다. 프로덕션 환경에서는 [Sling 모델](https://sling.apache.org/documentation/bundles/models.html)을 사용하는 것이 좋습니다.

use-class가 없는 HTL 구성 요소(`info`라고 함)부터 시작하겠습니다. 단일 파일 `/apps/my-example/components/info.html`로 구성됩니다.

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

또한 `/content/my-example/`에서 렌더링할 이 구성 요소에 대한 몇 가지 콘텐츠도 추가합니다.

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

이 콘텐츠에 액세스하면 HTL 파일이 실행됩니다. HTL 코드 내에서 컨텍스트 오브젝트 `properties`를 사용하여 현재 리소스의 `title` 및 `description`에 액세스하고 이를 표시합니다. 출력 파일 `/content/my-example.html`은 다음과 같습니다.

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Use-Class 추가 {#adding-a-use-class}

`info` 구성 요소는 간단한 기능을 수행하기 위해 use-class가 필요하지 않습니다. 단, HTL에서 수행할 수 없는 작업을 수행해야 하므로 use-class가 필요한 경우가 있습니다. 하지만 다음 사항에 유의하십시오.

>[!NOTE]
>
>use-class는 HTL만으로는 수행할 수 없는 작업에만 사용해야 합니다.

예를 들어 `info` 구성 요소가 리소스의 `title` 및 `description` 속성을 표시하지만 모두 소문자로 표시하기를 원한다고 가정합니다. HTL에는 문자열을 소문자로 변환하는 메서드가 없으므로 use-class가 필요합니다. Java use-class를 추가하고 `/apps/my-example/component/info/info.html`을 다음과 같이 변경하여 이를 수행할 수 있습니다.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

또한 `/apps/my-example/component/info/Info.java`를 생성하게 됩니다.

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

자세한 내용은 [`com.adobe.cq.sightly.WCMUsePojo`용 JavaDoc](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)을 참조하십시오.

이제 코드의 여러 부분을 살펴보겠습니다.

### 로컬 및 번들 Java 클래스 비교 {#local-vs-bundle-java-class}

Java use-class는 두 가지 방법으로 설치할 수 있습니다.

* **로컬** - 로컬 설치에서 Java 소스 파일은 HTL 파일과 함께 동일한 저장소 폴더에 배치됩니다. 소스는 요청 시 자동으로 컴파일됩니다. 별도의 컴파일 또는 패키징 단계가 필요하지 않습니다.
* **번들** - 번들 설치에서 Java 클래스는 표준 AEM 번들 배포 메커니즘을 사용하여 OSGi 번들 내에서 컴파일되고 배포되어야 합니다([번들 Java 클래스](#bundled-java-class) 섹션 참조).

언제 어떤 방법을 사용해야 하는지 알기 위해서는 두 가지 사항을 염두에 두어야 합니다.

* use-class가 해당 구성 요소와 관련된 경우 **로컬 Java use-class**&#x200B;가 권장됩니다.
* Java 코드가 여러 HTL 구성 요소에서 액세스되는 서비스를 구현하는 경우 **번들 Java 사용 클래스**&#x200B;가 권장됩니다.

이 예에서는 로컬 설치를 사용합니다.

### Java 패키지는 저장소 경로입니다. {#java-package-is-repository-path}

로컬 설치를 사용하는 경우 use-class의 패키지 이름은 저장소 폴더 위치의 패키지 이름과 일치해야 하며 경로의 모든 하이픈은 패키지 이름의 밑줄로 대체됩니다.

이 경우 `Info.java`는 `/apps/my-example/components/info`에 있으므로 패키지는 `apps.my_example.components.info`입니다.

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

Java 클래스를 HTL과 통합하는 방법은 여러 가지가 있지만([`WCMUsePojo`](#alternatives-to-wcmusepojo)에 대한 대안 섹션 참조) 가장 간단한 방법은 `WCMUsePojo` 클래스를 확장하는 것입니다. `/apps/my-example/component/info/Info.java` 예제의 경우:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### 클래스 초기화 {#initializing-the-class}

use-class가 `WCMUsePojo`에서 확장되면 `activate` 메서드를 재정의하여 초기화가 수행됩니다(이 경우 `/apps/my-example/component/info/Info.java`에서 수행됨).

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

일반적으로 [활성화](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) 메서드는 현재 컨텍스트(예: 현재 요청 및 리소스)를 기반으로 HTL 코드에 필요한 값을 미리 계산하고 (멤버 변수에) 저장하는 데 사용됩니다.

`WCMUsePojo` 클래스는 HTL 파일 내에서 사용 가능한 것과 동일한 컨텍스트 오브젝트 집합에 대한 액세스를 제공합니다([전역 오브젝트](global-objects.md) 문서 참조).

`WCMUsePojo`을 확장하는 클래스에서 컨텍스트 오브젝트는 다음을 사용하여 이름으로 액세스할 수 있습니다.

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

또는 이 표에 나열된 것처럼 적절한 편의 메서드를 통해 일반적으로 사용되는 컨텍스트 오브젝트에 직접 액세스할 수 있습니다.

| 오브젝트 | 편의 메서드 |
|---|---|
| [`PageManager`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/PageManager.html) | [`getPageManager()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageManager--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getCurrentPage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentPage--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getResourcePage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourcePage--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getPageProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageProperties--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getProperties--) |
| [`Designer`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [`getDesigner()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getDesigner--) |
| [`Design`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Design.html) | [`getCurrentDesign()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentDesign--) |
| [`Style`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Style.html) | [`getCurrentStyle()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentStyle--) |
| [`Component`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/components/Component.html) | [`getComponent()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getComponent--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getInheritedProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getInheritedPageProperties--) |
| [`Resource`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/Resource.html) | [`getResource()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResource--) |
| [`ResourceResolver`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [`getResourceResolver()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourceResolver--) |
| [`SlingHttpServletRequest`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [`getRequest()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getRequest--) |
| [`SlingHttpServletResponse`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [`getResponse()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResponse--) |
| [`SlingScriptHelper`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [`getSlingScriptHelper()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getSlingScriptHelper--) |

### getter 메서드 {#getter-methods}

use-class가 초기화되면 HTL 파일이 실행됩니다. 이 단계에서 HTL은 일반적으로 use-class의 다양한 멤버 변수의 상태를 가져와 표시하기 위해 렌더링합니다.

HTL 파일 내에서 이러한 값에 대한 액세스를 제공하려면 다음 명명 규칙에 따라 use-class에서 사용자 지정 getter 메서드를 정의해야 합니다.

* `getXyz` 형식의 메서드는 `xyz`라는 오브젝트 속성을 HTL 파일 내에서 노출합니다.

다음 예제 파일 `/apps/my-example/component/info/Info.java`에서 `getTitle` 및 `getDescription` 메서드는 HTL 파일의 컨텍스트 내에서 액세스할 수 있는 오브젝트 속성 `title` 및 `description`를 생성합니다.

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

이 `/apps/my-example/component/info/info.html` 예제에서 사용 방법을 참고하십시오.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 로컬 식별자 {#local-identifier}

식별자 `info`(`data-sly-use.info`의 점 뒤)는 클래스를 식별하기 위해 HTL 파일 내에서 사용됩니다. 선언된 후에 이 식별자의 범위는 파일 내에서 전역입니다. `data-sly-use` 문을 포함하는 요소에 국한되지 않습니다.

이 `/apps/my-example/component/info/info.html` 예제에서 사용 방법을 참고하십시오.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 속성 가져오기 {#getting-properties}

그런 다음 식별자 `info`는 getter 메서드 `Info.getTitle` 및 `Info.getDescription`를 통해 노출된 오브젝트 속성 `title` 및 `description`에 액세스하는 데 사용됩니다.

이 `/apps/my-example/component/info/info.html` 예제에서 사용 방법을 참고하십시오.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 출력 {#output}

이제 `/content/my-example.html`에 액세스하면 다음 `/content/my-example.html` 파일이 반환됩니다.

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>이 예제는 사용 방법을 간단하게 설명하기 위해 간소화되었습니다. 프로덕션 환경에서는 [Sling 모델](https://sling.apache.org/documentation/bundles/models.html)을 사용하는 것이 좋습니다.

## 추가 정보 {#beyond-the-basics}

이 섹션에서는 이전에 설명된 간단한 예 이상의 몇 가지 추가 기능을 소개합니다.

* use-class에 매개변수 전달
* 번들 Java use-class

### 매개변수 전달 {#passing-parameters}

매개변수는 초기화 시 use-class에 전달할 수 있습니다.

자세한 내용은 Sling [HTL 스크립팅 엔진 설명서](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects)를 참조하십시오.

### 번들 Java 클래스 {#bundled-java-class}

번들 use-class에서 표준 OSGi 번들 배포 메커니즘을 사용하여 AEM에서 클래스를 컴파일하고 패키징하고 배포해야 합니다. 로컬 설치와 달리 use-class 패키지 선언의 이름은 일반적으로 이 `/apps/my-example/component/info/Info.java` 예제와 같이 지정해야 합니다.

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

그리고 `data-sly-use` 문은 이 `/apps/my-example/component/info/info.html` 예제와 같이 로컬 클래스 이름이 아닌 정규화된 클래스 이름을 참조해야 합니다.

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
