---
title: HTL Java Use-API
seo-title: HTL Java Use-API
description: 'HTML 템플릿 언어(HTL의 Java Use-API)를 사용하면 HTL 파일이 사용자 지정 Java 클래스의 보조 메서드에 액세스하도록 설정할 수 있습니다. '
seo-description: 'HTML 템플릿 언어(HTL의 Java Use-API)를 사용하면 HTL 파일이 사용자 지정 Java 클래스의 보조 메서드에 액세스하도록 설정할 수 있습니다. '
uuid: b340f8f7-a193-45c8-aa39-5c6e2c0194ea
contentOwner: 사용자
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: 참조
discoiquuid: 126ebc9d-5f7b-47a4-aea2-c8840d34864c
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 6de5ed20e4463c0c2e804e24cb853336229a7c1f

---


# HTL Java Use-API{#htl-java-use-api}

HTL(HTML 템플릿 언어) Java Use-API를 사용하면 HTL 파일에서 사용자 지정 Java 클래스의 도우미 메서드에 액세스할 수 있습니다. 이렇게 하면 모든 복잡한 비즈니스 로직을 Java 코드로 캡슐화할 수 있고 HTL 코드는 직접 마크업 프로덕션만 처리합니다.

## 간단한 예 {#a-simple-example}

먼저 use-class가 *없는* HTL 구성 요소로 시작합니다. 하나의 파일로 구성되어 있고 `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

이 구성 요소에 대한 일부 컨텐트도 다음 위치에 **`/content/my-example/`**&#x200B;추가합니다.

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

이 콘텐트에 액세스하면 HTL 파일이 실행됩니다. HTL 코드 내에서 컨텍스트 객체를 **`properties`** 사용하여 현재 리소스에 `title` 액세스하고 `description` 표시합니다. 출력 HTML은 다음과 같습니다.

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### 사용 클래스 추가 {#adding-a-use-class}

현재 **info** 구성 요소는 사용 클래스(매우 간단한) 기능을 수행할 필요가 없습니다. 그러나 HTL에서 수행할 수 없는 작업을 수행해야 하는 경우가 있으므로 사용 클래스가 필요합니다. 그러나 다음 사항에 유의하십시오.

>[!NOTE]
>
>*use-class는 HTL에서만 수행할 수 없는 경우에만 사용해야 합니다.*

예를 들어, `info` 구성 요소가 리소스의 `title` 및 **`description`** 속성을 표시하지만 모두 소문자로 표시한다고 가정합니다. HTL에는 문자열 소문자에 대한 메서드가 없으므로 use-class가 필요합니다. 이렇게 하려면 Java 사용 클래스를 추가하고 **`info.html`** 다음과 같이 변경합니다.

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

Java 사용 클래스는 다음 두 가지 방법으로 설치할 수 있습니다. **로컬** 또는 **번들**. *이 예에서는 로컬 설치를 사용합니다.*

로컬 설치에서 Java 소스 파일은 동일한 저장소 폴더에 HTL 파일과 함께 배치됩니다. 소스는 요청 시 자동으로 컴파일됩니다. 별도의 컴파일 또는 패키징 단계는 필요하지 않습니다.

번들 설치에서 Java 클래스는 표준 AEM 번들 배포 메커니즘을 사용하여 OSGi 번들 내에서 컴파일하고 배포해야 합니다(번들 Java [클래스 참조](#bundled-java-class)).

>[!NOTE]
>
>use-class가 해당 구성 요소에 한정되어 있는 경우 **로컬 Java use-class** 를 사용하는 것이 좋습니다.
>
>Java 코드가 여러 HTL 구성 요소에서 액세스되는 서비스를 구현할 때 **번들 Java 사용 클래스가** 권장됩니다.

### Java 패키지가 저장소 경로임 {#java-package-is-repository-path}

로컬 설치를 사용하는 경우 use-class의 패키지 이름은 저장소 폴더 위치의 패키지 이름과 일치해야 하며 경로의 모든 하이픈이 패키지 이름의 밑줄로 대체됩니다.

이 경우 패키지는 **`Info.java`** 에 **`/apps/my-example/components/info`** 있으므로 패키지는 **`apps.my_example.components.info`** :

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
>저장소 항목 이름에 하이픈을 사용하는 것이 AEM 개발에서 권장됩니다. 그러나 Java 패키지 이름 내에서는 하이픈이 잘못되었습니다. 따라서 저장소 경로의 **모든 하이픈을 패키지 이름에서**&#x200B;밑줄로 변환해야 합니다.

### 확장 `WCMUsePojo`{#extending-wcmusepojo}

HTL에 Java 클래스를 통합하는 방법은 여러 가지가 있지만(대체 요소 참조), 가장 간단한 방법은 `WCMUsePojo``WCMUsePojo` 클래스를 확장하는 것입니다.

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### 클래스를 초기화하는 중 {#initializing-the-class}

use-class가 에서 확장되면 **`WCMUsePojo`**&#x200B;다음 **`activate`** 메서드를 재정의하여 초기화가 수행됩니다.

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

일반적으로 [activate](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) 메서드는 현재 컨텍스트(예: 현재 요청 및 리소스)를 기반으로 HTL 코드에 필요한 값을 미리 계산하고 저장하는 데 사용됩니다.

이 `WCMUsePojo` 클래스는 HTL 파일 내에서 사용할 수 있는 동일한 컨텍스트 객체 세트에 대한 액세스를 제공합니다(전역 객체 [참조](global-objects.md)).

확장되는 클래스에서 **`WCMUsePojo`**** 컨텍스트 객체는

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

또는 일반적으로 사용되는 컨텍스트 객체는 적절한 **편의 방법으로**&#x200B;직접 액세스할 수 있습니다.

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [페이지](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [페이지](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [디자이너](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [디자인](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [스타일](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [구성 요소](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [리소스](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Getter 메서드 {#getter-methods}

use-class가 초기화되면 HTL 파일이 실행됩니다. 이 단계 동안 HTL은 일반적으로 use-class의 다양한 멤버 변수의 상태를 가져와서 프레젠테이션용으로 렌더링합니다.

HTL 파일 내에서 이러한 값에 대한 액세스를 제공하려면 다음 명명 규칙에 **따라 use-class에서 사용자 정의 getter 메서드를 정의해야**&#x200B;합니다.

* 양식의 메서드는 HTL 파일 내에 xyz라는 개체 속성을 **`getXyz`** 표시합니다 ******.

예를 들어 다음 예제에서는 메서드와 **`getTitle`** 그 결과 객체 속성이 **`getDescription`** 생성되어 HTL 파일의 컨텍스트 내에서 액세스할 수 **`title`** **`description`** 있게 됩니다.

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

이 **`data-sly-use`** 속성은 HTL 코드 내에서 use-class를 초기화하는 데 사용됩니다. 예제에서 `data-sly-use` 속성은 클래스를 사용한다고 선언합니다 **`Info`**. 로컬 설치를 사용하고 있으므로 클래스의 로컬 이름만 사용할 수 있습니다. Java 소스 파일은 HTL 파일과 동일한 폴더에 있습니다. 번들 설치를 사용하는 경우 정규화된 클래스 이름을 지정해야 합니다(클래스 번들 [설치 참조](#LocalvsBundleJavaClass)).

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 로컬 식별자 {#local-identifier}

HTL 파일 내에서 식별자 '**`info`**'(인 점 **`data-sly-use.info`**&#x200B;뒤)가 클래스를 식별하는 데 사용됩니다. 이 식별자의 범위는 선언된 후 파일 내에서 전역적입니다. 이 `data-sly-use` 문은 문을 포함하는 요소에 국한되지 않습니다.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 속성 가져오기 {#getting-properties}

이 식별자는 `info` 개체 속성에 액세스하는 데 사용되며 getter 메서드 **`title`** 및 **`description`** `Info.getTitle` **`Info.getDescription`**&#x200B;를 통해 노출되었습니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 출력 {#output}

이제 액세스할 때 **`/content/my-example.html`** 다음 HTML이 반환됩니다.

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## 기본 사항 넘어 {#beyond-the-basics}

이 섹션에서는 위의 간단한 예제를 뛰어넘는 몇 가지 추가 기능을 소개합니다.

* 매개 변수를 use-class로 전달합니다.
* 번들 Java 사용 클래스.
* 대체 요소 `WCMUsePojo`

### 매개 변수 전달 {#passing-parameters}

매개 변수는 초기화 시 use-class로 전달할 수 있습니다. 예를 들어 다음과 같은 작업을 수행할 수 있습니다.

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

여기서 Adobe는 Adobe **`text`**&#x200B;라는 매개 변수를 전달합니다. Use-class를 선택한 다음 검색하는 문자열을 맨 위에 적용하고 결과를 표시합니다 `info.upperCaseText`. 다음은 조정된 사용 클래스입니다.

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

매개 변수는 `WCMUsePojo` 메서드를 통해 액세스합니다

[ `<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

우리의 경우에는,

`get("text", String.class)`

그런 다음 이 문자열을 되돌릴 수 있으며 메서드를 통해 노출됩니다

**`getReverseText()`**

### 데이터-사용 템플릿의 매개 변수 전달만 {#only-pass-parameters-from-data-sly-template}

위의 예가 기술적으로 올바르지만, 실제로 HTL의 값을 전달하여 use-class를 초기화하는 것은 적절하지 않습니다. 이 경우 HTL 코드의 실행 컨텍스트에서 해당 값을 사용할 수 있습니다(또는 위의 정적 값).

이유는 use-class가 항상 HTL 코드와 동일한 실행 컨텍스트에 액세스할 수 있기 때문입니다. 그러면 가져오기 모범 사례가 표시됩니다.

>[!NOTE]
>
>use-class에 매개 변수를 전달하는 것은 전달해야 하는 매개 변수가 있는 다른 HTL 파일에서 자체적으로 호출되는 **데이터-** 경간 템플릿 파일에서 use-class를 사용하는 경우에만 수행해야 합니다.

예를 들어 기존 예와 함께 별도의 `data-sly-template` 파일을 만들어 보겠습니다. 새 파일을 `extra.html`불러드리겠습니다 여기에는 다음과 같은 **`data-sly-template`** 블록이 포함되어 **`extra`**&#x200B;있습니다.

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

템플릿은 **`extra`**&#x200B;단일 매개 변수를 사용합니다 **`text`**. 그런 다음 Java use-class `ExtraHelper` 를 로컬 이름으로 **`extraHelper`** 초기화하고 템플릿 매개 변수의 값을 use-class 매개 변수로 전달합니다 **`text`** **`text`**.

템플릿의 본문은 속성( `extraHelper.reversedText` 백그라운드 아래에서 실제로 호출)을 가져와서 `ExtraHelper.getReversedText()`해당 값을 표시합니다.

또한 Adobe는 다음과 같은 새로운 템플릿을 **`info.html`** 사용하도록 기존 템플릿을 채택했습니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

이 파일에는 `info.html` 이제 Java use-class를 가져오는 원본 문과 로컬 이름 **`data-sly-use`** **`Info`** `extra`아래에 템플릿 파일을 가져오는 새 명령문이 포함됩니다.

두 번째 템플릿 파일을 방지하기 위해 **`info.html`** 파일 내부에 템플릿 블록을 배치할 **`data-sly-use`**&#x200B;수 있지만 별도의 템플릿 파일은 더 일반적이며 더 재사용 가능합니다.

이 **`Info`** 클래스는 getter 메서드를 호출하고 해당 HTL 속성 **`getLowerCaseTitle()`** 및 `getLowerCaseDescription()` `info.lowerCaseTitle` **`info.lowerCaseDescription`**&#x200B;을 통해 이전처럼 사용됩니다.

그런 다음 템플릿에 **`data-sly-call`** 대한 **`extra`** 작업을 수행하고 값을 `properties.description` 매개 변수로 **`text`**&#x200B;전달합니다.

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

매개 **`text`** 변수가 함께 검색되고 **`get("text", String.class)`**&#x200B;값이 반전되어 getter를 `reversedText` 통해 HTL 개체로 사용할 수 있습니다 `getReversedText()`.

### 번들 Java 클래스 {#bundled-java-class}

번들 사용 클래스를 사용하면 표준 OSGi 번들 배포 메커니즘을 사용하여 클래스를 컴파일하고 패키지하여 AEM에 배포해야 합니다. 로컬 설치와 달리 use-class **패키지 선언의** 이름은 일반적으로 다음과 같습니다.

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

또한 `data-sly-use` 문에서는 *정규화된 클래스 이름을*&#x200B;참조해야 하며, 이는 로컬 클래스 이름과는 다릅니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### 대체 `WCMUsePojo` 방법 {#alternatives-to-wcmusepojo}

Java 사용 클래스를 만드는 가장 일반적인 방법은 확장하는 `WCMUsePojo`것입니다. 그러나 다른 옵션들은 많이 있습니다. 이러한 변형을 이해하기 위해 HTL `data-sly-use` 문이 백그라운드에서 작동하는 방식을 이해하는 데 도움이 됩니다.

다음 `data-sly-use` 문이 있다고 가정합니다.

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

시스템은 다음과 같이 문을 처리합니다.

(1)

* HTL 파일과 *동일한 디렉토리에 로컬* 파일 UseClass.java가 있으면 해당 클래스를 컴파일하고 로드해 보십시오. 성공한 경우 (2)로 이동합니다.

* 그렇지 않은 경우 *UseClass* 를 정규화된 클래스 이름으로 해석하고 OSGi 환경에서 로드해 보십시오. 성공한 경우 (2)로 이동합니다.
* 그렇지 않으면 UseClass *를* HTL 또는 JavaScript 파일의 경로로 해석하고 해당 파일을 로드합니다. 성공적으로 goto (4).

(2)

* [성공 시] **`Resource`** 로 *`UseClass.`* 전류를 조정하십시오(3).

* 그렇지 않으면, 전류를 **`Request`** 에 맞게 조정하도록 노력하세요 *`UseClass`*. 성공하면 (3)으로 이동합니다.

* 그렇지 않은 경우 인수 0을 *`UseClass`* 생성자로 인스턴스화하십시오. 성공하면 (3)으로 이동합니다.

(3)

* HTL 내에서 새로 적용되거나 생성된 개체를 이름에 바인딩합니다 *`localName`*.
* 구현이 *`UseClass`* 구현되면 [ 현재 실행 컨텍스트를 전달하여( `io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) `init` `javax.scripting.Bindings` 객체 형식) 메서드를 호출합니다.

(4)

* HTL *`UseClass`* 파일의 경로인 경우 템플릿을 `data-sly-template`준비합니다.

* 그렇지 않은 경우 JavaScript use-class *`UseClass`* 의 경로인 경우 use-class를 준비합니다(JavaScript Use- [API 참조](use-api-javascript.md)).

위의 설명에 대한 몇 가지 중요 사항:

* 사용할 수 있거나, `Resource`적응할 수 `Request`있거나, 인수 제로 생성자가 있는 모든 클래스는 use-class일 수 있습니다. 이 클래스는 확장 `WCMUsePojo` 또는 구현하지 않아도 `Use`됩니다.

* 그러나 use-class가 *구현되지* 않는 `Use`경우 해당 **`init`** 메서드가 현재 컨텍스트에서 자동으로 호출되므로 해당 컨텍스트에 따라 초기화 코드를 배치할 수 있습니다.

* 확장되는 use-class `WCMUsePojo` 는 구현 시 특별한 경우입니다 **`Use`**. 편리한 컨텍스트 메서드를 제공하고 이 **`activate`** 메서드는 자동으로 `Use.init`호출됩니다.

### 인터페이스 사용 직접 구현 {#directly-implement-interface-use}

use-class를 만드는 가장 일반적인 방법은 확장이지만 `WCMUsePojo`인터페이스 자체를 직접 구현할 수도 `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`있습니다.

이 `Use` 인터페이스는 다음 방법 하나만 정의합니다.

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

이 **`init`** 메서드는 모든 컨텍스트 객체 및 use-class로 전달된 모든 매개 변수를 포함하는 **`Bindings`** 객체를 사용하여 클래스 초기화 시 호출됩니다.

모든 추가 기능(예: 동급 `WCMUsePojo.getProperties()`)은 ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` 개체를 사용하여 명시적으로 구현해야 합니다. 예:

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

확장 대신 `Use` 인터페이스를 직접 구현하기 위한 주요 `WCMUsePojo` 사례는 기존 클래스의 하위 클래스를 use-class로 사용하려는 경우입니다.

### 리소스에서 적응할 수 있습니다. {#adaptable-from-resource}

또 다른 옵션은 사용할 수 있는 도우미 클래스를 사용하는 **`org.apache.sling.api.resource.Resource`**&#x200B;것입니다.

DAM 자산의 MIMETYPE을 표시하는 HTL 스크립트를 작성해야 한다고 가정합니다. 이 경우 HTL 스크립트가 호출되면 JCR을 nodetype으로 래핑하는 컨텍스트 내에 있게 **`Resource`** 됩니다 **`Node`****`dam:Asset`**.

노드의 구조는 다음과 같습니다. **`dam:Asset`**

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

다음은 예제 프로젝트 geometrixx의 일부로 기본 AEM 설치와 함께 제공되는 자산(JPEG 이미지)입니다. 자산이 호출되고 **`jane_doe.jpg`** 해당 MIMETYPE이 **`image/jpeg`**&#x200B;표시됩니다.

HTL 내에서 자산에 액세스하려면 ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)` 문에서 **`data-sly-use`** 클래스로 선언할 수 있습니다.그런 다음 get 메서드를 **`Asset`** 사용하여 원하는 정보를 검색합니다. 예:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

이 `data-sly-use` 문은 HTL이 전류를 **`Resource`** 에 맞게 **`Asset`** 적용하고 그 지역 이름을 **`asset`**&#x200B;지정합니다. 그런 다음 HTL getter 축약형 **`getMimeType`** `Asset` 사용 방법을 호출합니다. `asset.mimeType`Adobe

### 요청에서 적용 가능 {#adaptable-from-request}

또한 사용 클래스로 비어 있을 수 있으며 **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`**

위의 use-class 적응형 경우와 마찬가지로 `Resource`명령문에서 use-class adaptable을 지정할 [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) `data-sly-use` 수 있습니다. 현재 요청이 실행되면 주어진 클래스에 맞게 조정되고 HTL 내에서 결과 객체를 사용할 수 있게 됩니다.
