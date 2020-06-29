---
title: HTL JavaScript Use-API
description: HTML 템플릿 언어 - HTL - JavaScript Use-API를 사용하면 HTML 파일에서 JavaScript로 작성된 헬퍼 코드에 액세스할 수 있습니다.
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '324'
ht-degree: 2%

---


# HTL JavaScript Use-API {#htl-javascript-use-api}

HTML 템플릿 언어(HTL) JavaScript 사용-API를 사용하면 HTML 파일에서 JavaScript로 작성된 헬퍼 코드에 액세스할 수 있습니다. 이렇게 하면 모든 복잡한 비즈니스 로직을 JavaScript 코드로 캡슐화할 수 있고, HTL 코드는 직접 마크업 프로덕션만 다룹니다.

다음 규칙이 사용됩니다.

```javascript
/**
 * In the following example '/libs/dep1.js' and 'dep2.js' are optional
 * dependencies needed for this script's execution. Dependencies can
 * be specified using an absolute path or a relative path to this
 * script's own path.
 *
 * If no dependencies are needed the dependencies array can be omitted.
 */
use(['dep1.js', 'dep2.js'], function (Dep1, Dep2) {
    // implement processing
  
    // define this Use object's behavior
    return {
        propertyName: propertyValue
        functionName: function () {}
    }
});
```

## 간단한 예 {#a-simple-example}

Adobe는 `info`

`/apps/my-example/components/info`

여기에는 두 개의 파일이 포함되어 있습니다.

* **`info.js`**: use-class를 정의하는 JavaScript 파일입니다.
* **`info.html`**: 구성 요소를 정의하는 HTL 파일입니다 `info`. 이 코드는 use-API를 통해 `info.js` 의 기능을 사용합니다.

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

Adobe에서는 `info`

`/content/my-example`, with properties:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

결과 저장소 구조는 다음과 같습니다.

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

다음 구성 요소 템플릿을 고려해 보십시오.

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

해당 로직은 템플릿 옆의 `component.js` 파일에 있는 다음 서버측 JavaScript를 사용하여 작성할 수 있습니다.

```javascript
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

이렇게 하면 다른 소스 `title` 에서 해당 설명을 50자로 자릅니다.

## 종속성 {#dependencies}

탐색 제목의 기본 논리나 특정 길이로 문자열을 잘리는 등 이미 지능적인 기능을 갖춘 유틸리티 클래스가 있다고 가정해 보겠습니다.

```javascript
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

종속성 패턴을 사용하여 다른 구성 요소의 논리(일반적으로 현재 구성 요소의 논리) `sling:resourceSuperType` 를 확장할 수도 있습니다.

상위 구성 요소가 이미 해당 구성 요소를 제공하고 `title`있으며, 다음과 같은 항목을 추가할 `description` 수도 있다고 가정해 보십시오.

```javascript
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

구성 요소와 독립적인 `data-sly-template` 명령문의 경우 관련 Use-API에 매개 변수를 전달하는 것이 유용합니다.

따라서 구성 요소에서 다른 파일에 있는 템플릿을 호출합니다.

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

다음은 템플릿에 있는 `template.html`템플릿입니다.

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

해당 로직은 템플릿 파일 바로 옆의 `template.js` 파일에 있는 다음 서버측 JavaScript를 사용하여 작성할 수 있습니다.

```javascript
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

전달된 매개 변수는 키워드에 `this` 설정됩니다.
