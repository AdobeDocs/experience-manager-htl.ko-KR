---
title: HTL JavaScript Use-API
description: HTL(HTML 템플릿 언어) JavaScript use-API를 사용하여 HTL 파일이 JavaScript로 작성된 헬퍼 코드에 액세스하는 방법을 알아봅니다.
exl-id: e98bfbd5-fa64-48c7-bd14-477d4c5e1788
source-git-commit: 7b53eff0652f650ffb8caae0e69aa349b5c548eb
workflow-type: tm+mt
source-wordcount: '326'
ht-degree: 93%

---

# HTL JavaScript Use-API {#htl-javascript-use-api}

HTML 템플릿 언어(HTL) JavaScript Use-API를 사용하면 HTL 파일이 JavaScript로 작성된 도우미 코드에 액세스할 수 있습니다. 이를 통해 모든 복잡한 비즈니스 논리를 JavaScript 코드에 캡슐화할 수 있지만 HTL 코드는 직접 마크업 생성만 처리합니다.

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

다음 위치에 있는 구성 요소 `info`를 정의합니다.

`/apps/my-example/components/info`

여기에는 두 개의 파일이 포함되어 있습니다.

* **`info.js`**: use-class를 정의하는 JavaScript 파일.
* **`info.html`**: 구성 요소 `info`를 정의하는 HTL 파일. 이 코드는 use-API를 통해 `info.js`의 기능을 사용합니다.

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

또한 다음 위치에 `info` 구성 요소를 사용하는 콘텐츠 노드도 생성합니다

`/content/my-example`, 속성 포함:

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

다음 구성 요소 템플릿을 고려하십시오.

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

해당 논리는 템플릿 바로 옆에 있는 `component.js` 파일에 있는 다음과 같은 서버측 JavaScript를 사용하여 작성할 수 있습니다.

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

이렇게 하면 다른 원본에서 `title`을 가져오고 설명을 50자로 자릅니다.

## 종속성 {#dependencies}

탐색 제목에 대한 기본 논리나 문자열을 특정 길이로 잘 자르는 것과 같은 스마트 기능이 이미 장착된 유틸리티 클래스가 있다고 가정해 보겠습니다.

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

종속성 패턴은 다른 구성 요소(일반적으로 현재 구성 요소의 `sling:resourceSuperType`)의 논리를 확장하는 데 사용할 수도 있습니다.

상위 구성 요소가 이미 `title`을 제공하고 `description`도 추가하려고 한다고 상상해 보십시오.

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

구성 요소와 독립적일 수 있는 `data-sly-template` 문의 경우 관련 Use-API에 매개 변수를 전달하는 것이 유용할 수 있습니다.

그럼, 구성 요소에서 다른 파일에 있는 템플릿을 호출해 보겠습니다.

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

다음은 `template.html`에 있는 템플릿입니다.

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

해당 논리는 템플릿 파일 바로 옆에 있는 `template.js` 파일에 있는 다음과 같은 서버측 JavaScript를 사용하여 작성할 수 있습니다.

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

전달된 매개 변수는 `this` 키워드에 설정됩니다.
