---
title: HTL JavaScript Use-API
seo-title: HTL JavaScript Use-API
description: HTML 템플릿 언어 - HTL - JavaScript Use-API를 사용하면 HTML 파일에서 JavaScript로 작성된 헬퍼 코드에 액세스할 수 있습니다.
seo-description: HTML 템플릿 언어 - HTL - JavaScript Use-API를 사용하면 HTML 파일에서 JavaScript로 작성된 헬퍼 코드에 액세스할 수 있습니다.
uuid: 7ab34b10-30ac-44d6-926b-0234f52e5541
contentOwner: 사용자
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: 참조
discoiquuid: 18871af8-e44b-4eec-a483-fcc765dae58f
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: bd1962e25d152be4f1608d0a83d8d5b3e728b4aa

---


# HTL JavaScript Use-API {#htl-javascript-use-api}

HTL(HTML 템플릿 언어) JavaScript Use-API를 사용하면 HTL 파일에서 JavaScript로 작성된 헬퍼 코드에 액세스할 수 있습니다. 이렇게 하면 모든 복잡한 비즈니스 로직을 JavaScript 코드로 캡슐화할 수 있고 HTL 코드는 직접 마크업 프로덕션만 처리합니다.

## 간단한 예 {#a-simple-example}

Adobe는 `info`

**`/apps/my-example/components/info`**

여기에는 두 개의 파일이 포함되어 있습니다.

* **`info.js`**:use-class를 정의하는 JavaScript 파일입니다.
* `info.html`:구성 요소를 정의하는 HTL `info`파일입니다. 이 코드는 use-API를 `info.js` 통해 의 기능을 사용합니다.

### /apps/my-example/component/info/info.js {#apps-my-example-component-info-info-js}

```java
"use strict";
use(function () {
    var info = {};    
    info.title = resource.properties["title"];
    info.description = resource.properties["description"];    
    return info;
});
```

### /apps/my-example/component/info/info.html {#apps-my-example-component-info-info-html}

```xml
<div data-sly-use.info="info.js">
    <h1>${info.title}</h1>
    <p>${info.description}</p>
</div>
```

Adobe에서는 다음 위치에서 **`info`** 구성 요소를 사용하는 컨텐트 노드를 만듭니다.

**`/content/my-example`**, with properties:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

다음은 결과 저장소 구조입니다.

### 저장소 구조 {#repository-structure}

```java
{
  "apps": {
    "my-example": {
      "components": {
        "info": {
          "info.html": {
            ...
          }, 
          "info.js": {
            ...
          }
        }
      }
    }
 },     
 "content": {
    "my-example": {
      "sling:resourceType": "my-example/component/info",
      "title": "My Example",
      "description": "This is some example content."
    }
  }
}
```

다음 구성 요소 템플릿을 고려하십시오.

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

템플릿 바로 옆에 있는 ***파일에 있는 다음 서버측*** `component.js` JavaScript를 사용하여 해당 논리를 작성할 수 있습니다.

```
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };
 
    var title = currentPage.getNavigationTitle() || currentPage.getTitle() || currentPage.getName();
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);
 
    return {
        title: title,
        description: description
    };
});
```

이렇게 하면 서로 다른 소스의 `title` 내용을 가져와 설명을 50자로 자릅니다.

## 종속성 {#dependencies}

탐색 제목의 기본 로직이나 문자열을 특정 길이로 잘리는 등 이미 고급 기능이 포함된 유틸리티 클래스가 있다고 가정해 보겠습니다.

```
use(['../utils/MyUtils.js'], function (utils) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };
 
    var title = utils.getNavigationTitle(currentPage);
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);
 
    return {
        title: title,
        description: description
    };
});
```

## 확장 {#extending}

종속성 패턴을 사용하여 다른 구성 요소(일반적으로 현재 구성 요소의 논리)의 논리를 확장할 `sling:resourceSuperType` 수도 있습니다.

상위 구성 요소가 이미 해당 구성 요소를 `title`제공하고, Adobe가 **`description`** 다음을 추가한다고 가정합니다.

```
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };
 
    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);
 
    return component;
});
```

## 템플릿에 매개 변수 전달 {#passing-parameters-to-a-template}

구성 요소와 독립적인 **`data-sly-template`** 문의 경우 관련 Use-API에 매개 변수를 전달하는 것이 유용할 수 있습니다.

구성 요소에서 다른 파일에 있는 템플릿을 호출해 보겠습니다.

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

그러면 다음 위치에 있는 템플릿이 `template.html`표시됩니다.

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

해당 로직은 템플릿 파일 바로 옆에 있는 ***파일에 있는 다음 서버측*** `template.js` JavaScript를 사용하여 작성할 수 있습니다.

```
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description"
    };
 
    var title = this.page.getNavigationTitle() || this.page.getTitle() || this.page.getName();
    var description = this.page.getProperties().get(Constants.DESCRIPTION_PROP, "").substr(0, this.descriptionLength);
 
    return {
        title: title,
        description: description
    };
});
```

전달된 매개 변수는 `this` 키워드에 설정됩니다.
