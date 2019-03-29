---
title: HTL Java Use-API
seo-title: HTL Java Use-API
description: HTML 템플릿 언어(HTL의 Java Use-API)를 사용하면 HTL 파일이 사용자 지정 Java 클래스의 보조 메서드에
  액세스하도록 설정할 수 있습니다.
seo-description: HTML 템플릿 언어(HTL의 Java Use-API)를 사용하면 HTL 파일이 사용자 지정 Java 클래스의 보조
  메서드에 액세스하도록 설정할 수 있습니다.
uuid: B 340 F 8 F 7-A 193-45 C 8-AA 39-5 C 6 E 2 C 0194 EA
contentOwner: 사용자
products: sg_ Experiencemanager/HTL
topic-tags: HTML-template-language
content-type: 참조
discoiquuid: 126 EBC 9 D -5 F 7 B -47 A 4-AEA 2-C 8840 D 34864 C
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# HTL Java Use-API{#htl-java-use-api}

HTL (HTML Template Language) Java Use-API를 사용하면 HTL 파일이 사용자 정의 Java 클래스의 보조 메서드에 액세스할 수 있습니다. 이렇게 하면 모든 복잡한 비즈니스 로직을 Java 코드로 캡슐화할 수 있으며 HTL 코드는 Direct Markup Production 에만 적용됩니다.

## 간단한 예 {#a-simple-example}

먼저 Use-Class가 *없는* HTL 구성 요소에서 시작합니다. 단일 파일로 구성됩니다. `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

이 구성 요소에 대한 일부 컨텐츠도 추가하여 다음 위치에서 **`/content/my-example/`**렌더링합니다.

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

이 컨텐츠를 액세스하면 HTL 파일이 실행됩니다. HTL 코드 내에서 컨텍스트 개체****`properties`를 사용하여 현재 리소스에 액세스하고 `title``description` 표시합니다. 출력 HTML는 다음과 같습니다.

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Use a use-class {#adding-a-use-class}

**The info** component as it stands does not need a use-class to perform its (very simple) functions. HTL에서 수행할 수 없는 작업을 수행해야 하는 경우가 있습니다. 그러나 다음 사항에 주의하십시오.

>[!NOTE]
>
>*use-class should only be used when something cannot be done in HTL alone.*

예를 들어 `info` , 구성 요소가 리소스의 및 `title`**`description`** 속성을 표시하려고 하지만 모두 소문자로 표시되기를 원한다고 가정합니다. HTL 에는 문자열을 소화하는 방법이 없으므로 use-class가 필요합니다. Java Use-Class를 추가하고 **`info.html`** 다음과 같이 변경할 수 있습니다.

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

다음 섹션에서는 코드의 다양한 부분을 살펴봅니다.

### 로컬 및 번들 Java 클래스 {#local-vs-bundle-java-class}

Java Use-Class는 다음 두 가지 방법으로 설치할 수 있습니다. **로컬** 또는 **번들을**참조하십시오. *이 예에서는 로컬 설치를 사용합니다.*

로컬 설치에서 Java 소스 파일은 동일한 저장소 폴더에 HTL 파일과 함께 배치됩니다. 소스는 On-Demand 방식으로 컴파일됩니다. 별도의 컴파일 또는 패키징 단계가 필요하지 않습니다.

번들 설치에서 Java 클래스는 표준 AEM Bundle 배포 메커니즘을 사용하여 osgi 번들 내에서 컴파일되고 배포해야 합니다 [(번들 Java 클래스](#bundled-java-class)참조).

>[!NOTE]
>
>**Use-class is specific** to the component is specific to question when-class is specific.
>
>**번들 Java 사용 클래스는** Java 코드가 여러 HTL 구성 요소에서 액세스하는 서비스를 구현할 때 사용하는 것이 좋습니다.

### Java 패키지는 저장소 경로입니다. {#java-package-is-repository-path}

로컬 설치를 사용하는 경우 use-class의 패키지 이름은 저장소 폴더 위치의 패키지 이름과 일치해야 하며 경로의 하이픈은 패키지 이름의 밑줄로 대체됩니다.

이 경우 **`Info.java`** 패키지의 **`/apps/my-example/components/info`****`apps.my_example.components.info`** 위치는 다음과 같습니다.

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
>AEM 개발 시 저장소 항목 이름에 하이픈을 사용하는 것이 좋습니다. 그러나 Java 패키지 이름에는 하이픈이 불법입니다. 따라서 저장소 **경로의 모든 하이픈은 패키지 이름에서 밑줄로**변환해야 합니다.

### extending `WCMUsePojo`{#extending-wcmusepojo}

HTL와 Java 클래스를 통합하는 방법에는 몇 가지가 있지만 가장 간단한 방법은 `WCMUsePojo``WCMUsePojo` 클래스를 확장하는 것입니다.

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### 클래스 초기화 {#initializing-the-class}

use-class is extended from **`WCMUsePojo`**, initializiation is performed by override the **`activate`** method:

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

일반적으로 [활성화](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) 메서드는 현재 컨텍스트 (예: 현재 요청 및 리소스) 를 기반으로 HTL 코드에 필요한 값을 멤버 변수에서 미리 계산하고 저장하는 데 사용됩니다.

`WCMUsePojo` 이 클래스는 HTL 파일에서 사용할 수 있는 것과 동일한 컨텍스트 객체 세트에 대한 액세스를 제공합니다 ( [전역 개체](global-objects.md)참조).

확장되는 **`WCMUsePojo`**클래스에서 *컨텍스트* 객체는

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

또는, 일반적으로 사용되는 컨텍스트 개체는 **해당 편의 방법으로 직접 액세스할**수 있습니다.

|  |
|---|---|
| [Pagemanager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [Getpagemanager ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [페이지](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getcurrentpage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [페이지](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getresourcepage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getpageproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [디자이너](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [Getdesigner ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [디자인](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [Getcurrentdesign ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [스타일](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [Getcurrentstyle ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [구성 요소](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [Getcomponent ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getinheritedproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [리소스](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [Getresource ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [Resourceresolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [Getresourceresolver ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [Slinghttpservletrequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [Getrequest ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [Slinghttpservletresponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [Getresponse ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [Slingscripthelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [Getslingscripthelper ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### getter 메서드 {#getter-methods}

use-class has initialized, the HTL file is run. 이 단계 HTL 동안에는 일반적으로 사용 클래스의 다양한 멤버 변수 상태를 가져와서 프레젠테이션에 렌더링합니다.

HTL 파일에서 이러한 값에 대한 액세스를 제공하려면 다음 명명 규칙에 따라 use-class **에서 사용자 지정 getter 메서드를 정의해야**합니다.

* 양식 **`getXyz`** 메서드는 HTL 파일 내에 XYZ 라는 ***개체 속성을 표시합니다***.

예를 들어, 다음 예제에서는 메서드를 **`getTitle`** 호출하고 **`getDescription`** HTL 파일의 **`title`** 컨텍스트 내에서 액세스할 수 **`description`** 있게 됩니다.

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

속성은 HTL **`data-sly-use`** 코드 내에서 사용 클래스를 초기화하는 데 사용됩니다. 이 예제에서 `data-sly-use` 속성은 클래스를 **`Info`**사용할 것임을 선언합니다. 로컬 설치 (Java 소스 파일은 HTL 파일과 동일한 폴더에 있음) 를 사용하고 있으므로 해당 클래스의 로컬 이름만 사용할 수 있습니다. 번들 설치를 사용하고 있는 경우 정규화된 Classname를 지정해야 합니다 ( [use-class bundle install](#LocalvsBundleJavaClass)참조).

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 로컬 식별자 {#local-identifier}

The identifier "**`info`**(after the dot in **`data-sly-use.info`**) is used within the HTL file to identify the class. 이 ID의 범위는 파일 내에서 선언한 후 전역적입니다. 문이 들어 있는 요소로 제한되지 `data-sly-use` 않습니다.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 속성 가져오기 {#getting-properties}

그런 `info` 다음 식별자는 getter 메서드를 **`title`****`description`**`Info.getTitle` 통해 노출되고 개체 속성에 액세스하는 데 사용됩니다 **`Info.getDescription`**.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 출력 {#output}

이제 액세스하면 **`/content/my-example.html`** 다음 HTML 이 반환됩니다.

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## 기본 사항 외에도 {#beyond-the-basics}

이 섹션에서는 위의 간단한 예를 넘어서는 몇 가지 추가 기능을 살펴봅니다.

* use parameters to a use-class.
* 번들 Java 사용
* 대체 요소 `WCMUsePojo`

### 매개 변수 전달 {#passing-parameters}

매개 변수를 초기화할 때 사용할 수 있습니다. 예를 들어 다음과 같은 작업을 수행할 수 있습니다.

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

여기에서 매개 변수를 전달했습니다 **`text`**. use-class then uppercase that retrieve and display the result with `info.upperCaseText`. 다음은 조정된 사용 클래스입니다.

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

매개 변수는 `WCMUsePojo` 메서드를 통해 액세스합니다.

[ `<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

In our case, the statement

`get("text", String.class)`

그런 다음 메서드를 통해 문자열을 반전시켜 노출합니다.

**`getReverseText()`**

### data-sly-template의 매개 변수만 전달합니다. {#only-pass-parameters-from-data-sly-template}

위의 예제는 기술적으로 올바르지만, 문제의 값을 HTL 코드의 실행 컨텍스트에서 사용할 수 있을 때 (또는, 상기에 언급된 것처럼 값이 정적인 경우), HTL에서 값을 전달하여 use-class를 초기화하는 것은 실제로 적합하지 않습니다.

The reason is that the use-class will always access to the same execution context as the HTL code. 이렇게 하면 우수 사례 가져오기가 표시됩니다.

>[!NOTE]
>
>use a parameter to a use-class should only be done-class is used when the **use-class is used** from another HTL file that itself from another HTL file that need to be passed on.

예를 들어 기존 예제와 함께 `data-sly-template` 별도의 파일을 만들어 보겠습니다. 새 파일을 `extra.html`호출합니다. **`data-sly-template`****`extra`**여기에는

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

템플릿은 **`extra`****`text`**단일 매개 변수를 사용합니다. 그런 다음 Java use-class `ExtraHelper` 를 로컬 이름으로 **`extraHelper`** 초기화하고 템플릿 매개 변수의 **`text`** 값을 use-class 매개 변수로 **`text`**전달합니다.

템플릿의 본문은 속성 `extraHelper.reversedText` (실제로 호출하는 경우) 를 가져오고 해당 `ExtraHelper.getReversedText()`값을 표시합니다.

또한 기존 **`info.html`** 템플릿을 사용하여 새로운 템플릿을 사용합니다.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

`info.html` 이제 파일에는 Java use-class를 가져오는 **`data-sly-use`** 두 개의 문과 로컬 **`Info`** 이름 아래의 템플릿 파일을 가져오는 새 문이 포함되어 `extra`있습니다.

두 번째 **`info.html`****`data-sly-use`**경우를 피하기 위해 템플릿 블록에 템플릿 블록을 배치할 수 있지만, 별도의 템플릿 파일이 더 일반적이며, 다시 사용할 수 있습니다.

**`Info`** 이 클래스는 이전에 getter 메서드를 **`getLowerCaseTitle()`** 호출하고 해당 htl 속성을 `getLowerCaseDescription()``info.lowerCaseTitle` 통해 사용됩니다 **`info.lowerCaseDescription`**.

그런 다음 템플릿을 **`data-sly-call`****`extra`** 사용하여 값을 매개 변수로 `properties.description`**`text`**전달합니다.

Java use-class `Info.java` 가 변경되어 새 텍스트 매개 변수를 처리합니다.

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

**`text`** 매개 변수가 함께 **`get("text", String.class)`**검색되면 값이 반전되어 getter를 통해 HTL 개체로 `reversedText` 사용할 수 있게 `getReversedText()`됩니다.

### 번들로 묶인 Java 클래스 {#bundled-java-class}

번들 사용 클래스는 표준 Osgi 번들 배포 메커니즘을 사용하여 AEM에서 컴파일, 패키징 및 배포해야 합니다. 로컬 설치와 반대로 use-class **패키지 선언은** 정상적으로 명명되어야 합니다.

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

and, `data-sly-use` this statement must reference the *fully qualified class name*as reference as the local class name:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### 대체 요소 `WCMUsePojo`{#alternatives-to-wcmusepojo}

Java Use-Class를 만드는 가장 일반적인 방법은 확장하는 `WCMUsePojo`것입니다. 하지만 다양한 옵션이 있습니다. 이러한 변형을 이해하기 위해 HTL `data-sly-use` 문이 백그라운드에서 작동하는 방식을 이해하는 데 도움이 됩니다.

다음과 같은 `data-sly-use` 문이 있다고 가정합니다.

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

시스템이 다음과 같이 문을 처리합니다.

(1)

* HTL 파일과 같은 디렉토리에 로컬 파일 *useclass. java* 가 있는 경우 해당 클래스를 컴파일하여 로드해 보십시오. 성공하면 (2) 로 이동합니다.

* 그렇지 않으면 *Useclass* 를 정규화된 클래스 이름으로 해석하고 osgi 환경에서 로드를 시도합니다. 성공하면 (2) 로 이동합니다.
* 그렇지 않으면 *Useclass* 를 HTL 또는 JavaScript 파일의 경로로 해석하고 해당 파일을 로드합니다. 성공 goto (4).

(2)

* 성공할 경우 현재 **`Resource`** 설정을 *`UseClass.`* 적용해 보십시오.

* 그렇지 않으면 현재 **`Request`** 대상을 *`UseClass`*조정합니다. 성공하면 (3) 로 이동합니다.

* 그렇지 않으면 인수 생성자로 인스턴스화하십시오 *`UseClass`* . 성공하면 (3) 로 이동합니다.

(3)

* HTL 내에서 새로 조정되었거나 생성된 개체를 이름에 *`localName`*바인딩합니다.
* *`UseClass`* if implements [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) then call the `init` method, passing the current execution context (in a `javax.scripting.Bindings` object).

(4)

* 가 *`UseClass`* a `data-sly-template`를 포함하는 HTL 파일의 경로인 경우 템플릿을 준비합니다.

* 그렇지 않으면, JavaScript 사용 클래스에 대한 경로인 경우 *`UseClass`* use-class를 준비합니다 ( [JavaScript use-API](use-api-javascript.md)참조).

위의 설명에 대한 몇 가지 중요 포인트:

* 인수 생성자가 없거나, 이로부터 `Resource``Request`적응성이 높은 모든 클래스는 use-class 일 수 있습니다. 이 클래스는 확장 `WCMUsePojo` 또는 구현을 확장할 필요가 `Use`없습니다.

* 그러나 use-class *가* 구현하는 `Use`경우 **`init`** 해당 메서드는 현재 컨텍스트에서 자동으로 호출되므로 해당 컨텍스트에 의존하는 초기화 코드를 배치할 수 있습니다.

* 확장되는 `WCMUsePojo` 사용 클래스는 특별한 구현 **`Use`**사례입니다. 여기서는 편리한 컨텍스트 메서드를 제공하며 **`activate`** 이 메서드는 자동으로 `Use.init`호출됩니다.

### 인터페이스 사용 직접 구현 {#directly-implement-interface-use}

use-class to create a used-class is to extend `WCMUsePojo`, it is also possible to directly implement the `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`interface itself.

`Use` 인터페이스는 다음 한 가지 방법만 정의합니다.

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

**`init`** 이 메서드는 모든 컨텍스트 개체와 use-class에 전달되는 매개 변수를 포함하는 **`Bindings`** 객체가 있는 클래스의 초기화할 때 호출됩니다.

해당 개체를 사용하여 모든 추가 기능 (예: 동등한 `WCMUsePojo.getProperties()`기능) 를 명시적으로 내포해야 ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` 합니다. 예:

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

기존 클래스의 하위 클래스를 사용하기 위해 사용하는 것이 아니라 `Use``WCMUsePojo` 인터페이스를 직접 구현하기 위한 기본 경우를 사용하는 것이 좋습니다.

### 리소스 활용 극대화 {#adaptable-from-resource}

또 다른 방법은 적응성이 있는 도우미 클래스를 사용하는 **`org.apache.sling.api.resource.Resource`**것입니다.

DAM 자산의 mimetype를 표시하는 HTL 스크립트를 작성해야 한다고 가정합니다. 이 경우 HTL 스크립트가 호출되면 jcr를 nodetype **`Resource`****`Node`** 로 래핑하는 컨텍스트 내 컨텍스트가 표시됩니다 **`dam:Asset`**.

**`dam:Asset`** 노드는 다음과 같은 구조를 가지고 있습니다.

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

여기에서는 프로젝트 Geometrixx의 일부로 AEM의 기본 설치가 포함된 자산 (JPEG 이미지) 를 보여줍니다. 자산이 호출되고 **`jane_doe.jpg`** 해당 Mimetype는 입니다 **`image/jpeg`**.

HTL 내에서 자산에 액세스하려면 문에서 클래스로 선언할 ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)`**`data-sly-use`** 수 있습니다. 그런 다음 GET 메서드를 **`Asset`** 사용하여 원하는 정보를 검색합니다. 예:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

The `data-sly-use` statement directs HTL to adapt the current **`Resource`** to an **`Asset`** and give it the local name **`asset`**. 그런 다음 HTL getter **`getMimeType`** 축약법을 `Asset` 사용하는 메서드를 호출합니다. `asset.mimeType`.

### 요청에 맞게 조정 가능 {#adaptable-from-request}

또한 **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`**

as-class of a use-class of a use-class adapeable from `Resource`, as-class adaptable from [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) the statement in the `data-sly-use` statement. 실행 시 현재 요청은 지정된 클래스에 적용되고 결과 개체는 HTL 내에서 사용할 수 있게 됩니다.
