---
title: AEM 확장
description: AEM은 개발자로서 편리하게 AEM에 HTL 사양의 확장을 제공합니다.
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---


# AEM 확장 {#aem-extensions}

와 비슷합니다 [HTL 사양의 Apache Sling 확장,](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEM에서는 HTL 스크립트에서 AEM 개념을 보다 쉽게 사용할 수 있도록 해주는 몇 가지 추가 표현식 옵션을 제공합니다.

## i18n {#i18n}

동일 [세 가지 추가 옵션](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) Apache Sling에서와 마찬가지로 과 을 함께 사용할 수 있습니다. `i18n`:

* `locale`
* `hint`
* `basename`

하지만 AEM에서는 [국제화 지원](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) htl용 API는 `com.day.cq.i18n` 패키지.

## data-sly-include {#data-sly-include}

AEM에서, `data-sly-include` 추가 `wcmmode` 제어 옵션 [WCM 모드](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) 포함된 스크립트의 경우 허용되는 값은 사용 가능한 열거형 상수의 이름입니다.

## data-sly-resource {#data-sly-resource}

경로 및 `Resources`, `data-sly-resource` 블록 요소를 [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) 또는 [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) 두 접근 방법 모두 `resourceName` 문자열 속성을 제공해야 합니다. 이 값은 [합성 리소스](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) 렌더링 컨텍스트에 포함됩니다. 의 나머지 속성 `Record` 또는 `Map` 에 전달되었습니다. `data-sly-resource` 는 정상적으로 사용됩니다 `Resource` 속성을 사용합니다. 만약 `sling:resourceType` 이 맵에서 속성이 누락되어 리소스 유형이 `resourceType` [식 옵션](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) 또는 렌더링을 구동하는 현재 리소스의 리소스 유형입니다.

스크립트 범위에서 다음 맵/레코드 속성을 `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

그리고 다음 마크업이 제공됩니다.

```html
<div class="outer" data-sly-resource="${map}"></div>
```

다음 출력이 필요합니다.

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
