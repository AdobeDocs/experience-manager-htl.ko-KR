---
title: HTL JavaScript Use-API
seo-title: HTL JavaScript Use-API
description: HTML 템플릿 Langugae - HTL - JavaScript - JavaScript Use-API를 사용하면 HTL 파일에서
  JavaScript로 작성한 보조 코드를 액세스할 수 있습니다.
seo-description: HTML 템플릿 Langugae - HTL - JavaScript - JavaScript Use-API를 사용하면 HTL
  파일에서 JavaScript로 작성한 보조 코드를 액세스할 수 있습니다.
uuid: 7 AB 34 B 10-30 AC -44 D 6-926 B -0234 F 52 E 5541
contentOwner: 사용자
products: sg_ Experiencemanager/HTL
topic-tags: HTML-template-language
content-type: 참조
discoiquuid: 18871 AF 8-E 44 B -4 EEC-A 483-FCC 765 DAE 58 F
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL JavaScript Use-API {#htl-javascript-use-api}

HTML 템플릿 Langugae (HTL) JavaScript use-API를 사용하면 HTL 파일에서 JavaScript로 작성한 보조 코드를 액세스할 수 있습니다. 이렇게 하면 모든 복잡한 비즈니스 논리를 JavaScript 코드로 캡슐화할 수 있으며 HTL 코드는 Direct Markup Production 에만 적용됩니다.

## 간단한 예 {#a-simple-example}

`info`Adobe 는

**`/apps/my-example/components/info`**

이 파일에는 두 개의 파일이 포함되어 있습니다.

* **`info.js`**: use-class를 정의하는 JavaScript 파일.
* `info.html`: 구성 요소를 정의하는 HTL 파일 `info`. This code will use the functionality of `info.js` the use-API.

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

We also create a content node that uses the **`info`** component at

**`/content/my-example`**, with properties:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

다음은 결과 리포지토리 구조입니다.

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

해당 로직은 템플릿 옆에 있는 파일에 있는 ***다음 서버측*** JavaScript를 사용하여 `component.js` 작성할 수 있습니다.

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

이렇게 하면 다른 소스에서 가져온 다음 `title` 설명을 50 자로 자릅니다.

## 종속성 {#dependencies}

탐색 제목용 기본 로직과 같은 스마트 기능과 특정 길이의 문자열을 오가며 사용할 수 있는 유틸리티 클래스가 있다고 가정해봅시다.

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

## extending {#extending}

종속성 패턴을 사용하여 다른 구성 요소 (일반적으로 현재 구성 요소) 의 논리를 `sling:resourceSuperType` 확장할 수도 있습니다.

상위 구성 요소가 이미를 `title`제공하고 A **`description`** 도 추가한다고 가정해 보겠습니다.

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

구성 요소와 독립적인 **`data-sly-template`** 명령문의 경우 연결된 use-API에 매개 변수를 전달하는 것이 유용할 수 있습니다.

따라서 구성 요소에서 다른 파일에 있는 템플릿을 호출합니다.

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

이 템플릿은 다음 위치에 있습니다 `template.html`.

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

해당 로직은 템플릿 파일 옆에 있는 파일에 있는 ***다음 서버측*** JavaScript `template.js` 를 사용하여 작성할 수 있습니다.

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

전달된 매개 변수가 `this` 키워드에 대해 설정됩니다.
