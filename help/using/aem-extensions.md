---
title: AEM 확장 기능
description: AEM은 개발자의 편의를 위해 AEM에 대한 HTL 사양의 확장을 제공합니다.
exl-id: d78cb84d-f958-45e2-9c6c-df86a68277d5
source-git-commit: ebeac25c38b81c92011c163c7860688f43547a7d
workflow-type: tm+mt
source-wordcount: '228'
ht-degree: 64%

---

# AEM 확장 기능 {#aem-extensions}

[HTL 사양의 Apache Sling 확장 기능](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1)과 마찬가지로 AEM은 HTL 스크립트에서 직접 AEM 개념을 사용하여 더 간편하게 작업할 수 있도록 하는 몇 가지 추가 표현식 옵션을 제공합니다.

## i18n {#i18n}

Apache Sling과 동일한 [세 가지 추가 옵션](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n)을 `i18n`과 함께 사용할 수 있습니다.

* `locale`
* `hint`
* `basename`

그러나 AEM에서는 HTL에 대한 [국제화 지원](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/implementing/developing/components/internationalization/i18n-dev)은 `com.day.cq.i18n` 패키지의 API를 통해 구현됩니다.

## `data-sly-include` {#data-sly-include}

AEM에서 `data-sly-include`는 포함된 스크립트에 대한 [WCM 모드](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html)를 제어하는 추가 `wcmmode` 옵션을 사용할 수 있습니다. 허용된 값은 사용할 수 있는 열거형 상수의 이름입니다.

## `data-sly-resource` {#data-sly-resource}

경로 및 `Resources` 이외에 `data-sly-resource` 블록 요소는 [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) 또는 [`Records`와 함께 작동할 수도 있습니다.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) 두 가지 접근 방식을 통해 `resourceName` 스트링 속성을 제공해야 합니다. 해당 값은 렌더링 컨텍스트에 포함된 [합성 리소스](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html)을(를) 만드는 데 사용됩니다. `data-sly-resource`에 전달된 `Record` 또는 `Map`의 나머지 속성은 일반 `Resource` 속성으로 사용됩니다. 이 맵에서 `sling:resourceType` 속성이 누락된 경우 리소스 유형은 `resourceType` [표현식 옵션](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource)의 값 또는 렌더링을 실행하는 현재 리소스의 리소스 유형으로 간주됩니다.

스크립트 범위에서 `map`으로 사용할 수 있는 다음 맵/레코드 속성

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

및 다음 마크업을 감안할 때

```html
<div class="outer" data-sly-resource="${map}"></div>
```

예상되는 출력은 다음과 같습니다.

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
