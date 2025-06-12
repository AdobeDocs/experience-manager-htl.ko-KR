---
title: HTL 개요
description: AEM이 HTML 템플릿 언어(HTL)를 지원하여 우수한 생산성으로 보안을 강화하는 엔터프라이즈 수준 웹 프레임워크를 제공하는 방법을 알아보십시오. 이 프레임워크를 사용하면 Java 지식이 없는 HTML 개발자들이 AEM 프로젝트에 더 잘 참여할 수 있습니다.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 9735ca6ae09ca899c69edd9b63d2e85ad5d6f904
workflow-type: tm+mt
source-wordcount: '677'
ht-degree: 95%

---


# 개요 {#overview}

>[!TIP]
>
>**AEM용 Edge Delivery Services을 고려했습니까?**
>
>기존 프로젝트에 대해 이 문서에 설명된 방법을 계속 사용할 수 있습니다. 그러나 새 프로젝트의 경우 Adobe은 [Edge Delivery Services.](https://experienceleague.adobe.com/ko/docs/experience-manager-cloud-service/content/edge-delivery/overview)을(를) 활용할 것을 권장합니다

우수한 생산성으로 보안을 강화하는 엔터프라이즈 수준 웹 프레임워크를 제공하는 것이 AEM(Adobe Experience Manager)에서 지원하는 HTL(HTML 템플릿 언어)의 목적입니다. 이를 통해 Java 지식이 없는 HTML 개발자들이 AEM 프로젝트에 더 잘 참여할 수 있습니다.

[AEM 6.0에 도입된](history.md) HTML 템플릿 언어는 HTML에 대한 AEM의 기본 및 권장 서버측 템플릿 시스템입니다. 강력한 엔터프라이즈 웹 사이트를 구축해야 하는 웹 개발자의 경우 HTML 템플릿 언어가 강화된 보안 및 개발 효율성 달성에 도움이 됩니다.

## 강화되는 보안 {#increased-security}

HTML 템플릿 언어(HTL)는 모든 출력 변수에 컨텍스트 인식 이스케이프를 자동으로 적용하기 때문에 사이트의 보안이 기타 대다수 템플릿 시스템의 경우보다 강화됩니다. HTL에서는 HTML 구문을 이해하고 해당 지식을 사용하여 마크업에서의 위치를 기반으로 표현식에 필요한 이스케이프를 조정하므로 이 접근 방식이 가능합니다. 이 메서드를 통해 다른 속성이나 다른 위치에 배치된 표현식에서 다르게 이스케이프되는 `href` 또는 `src` 속성에 표현식이 배치됩니다.

JSP와 같은 템플릿 언어에서도 같은 결과를 얻을 수 있지만 개발자가 각 변수에 적절한 이스케이프가 적용되었는지 수동으로 확인해야 합니다. 적용된 이스케이프가 하나라도 생략되거나 실수가 있으면 XSS(크로스 사이트 스크립팅) 취약성의 원인이 될 가능성이 충분하므로 Adobe에서는 HTL로 이 작업을 자동화하기로 결정했습니다. 필요하면 개발자가 표현식에 서로 다른 이스케이프를 지정할 수도 있지만, HTL에서는 기본 동작이 원하는 동작에 해당할 가능성이 훨씬 많으므로 오류 가능성이 감소합니다.

## 간소화되는 개발 {#simplified-development}

HTML 템플릿 언어는 쉽게 배울 수 있으며 기능은 간단하고 복잡하지 않게 유지되도록 의도적으로 제한했습니다. 또한 마크업을 체계화하고 로직을 호출하는 동시에 마크업과 로직 간 문제의 엄격한 구분을 항상 강제로 적용하는 강력한 메커니즘이 있습니다. HTL은 표현식 및 데이터 속성을 사용하여 동적 비헤이비어로 마크업에 주석을 다는 표준 HTML5입니다. 이러한 접근 방식을 통해 마크업의 유효성 및 가독성을 유지할 수 있습니다. 표현식 및 데이터 속성에 대한 평가가 전적으로 서버측에서 수행되며 클라이언트측에 표시되지 않으므로 원하는 JavaScript 프레임워크를 방해 없이 사용할 수 있습니다.

이러한 기능을 사용하면 Java 지식이 없는 HTML 개발자들이 HTL 템플릿을 편집하고, 개발 팀에 통합하고, 전체 스택 Java 개발자와의 공동 작업을 간소화할 수 있습니다. 반대로, Java 개발자는 HTML에 대한 걱정 없이 백엔드 코드에 집중할 수 있습니다.

## 절감되는 비용 {#reduced-costs}

보안이 강화되고 개발이 간소화되며 팀 공동 작업이 개선되면 AEM 프로젝트에 드는 노력이 감소하여 TTM(시장 출시 기간)이 단축되고 TCO(총 소유 비용)가 절감됩니다.

HTML 템플릿 언어로 Adobe.com 사이트를 다시 구현한 결과, 프로젝트 비용 및 기간이 대략 최대 25% 단축되었습니다.

![효율성 증가 및 비용 감소](assets/chlimage_1.png)

위의 다이어그램은 HTL을 통해 가능해질 수 있는 다음과 같은 효율성 개선 사항을 보여 줍니다.

* **HTML / CSS / JS:** HTML 개발자가 HTL 템플릿을 바로 편집할 수 있으므로 더는 프론트엔드 디자인을 AEM 프로젝트에서 별도로 구현할 필요가 없습니다. 따라서 전체 스택 Java 개발자의 성가신 반복 작업이 감소합니다.
* **JSP/HTL:** HTL 자체가 Java 지식이 필요하지 않으며 간단하게 쓸 수 있으므로 개발자가 HTML 지식만 있으면 템플릿을 편집할 수 있습니다.
* **Java:** HTL에서 제공되는 Use-API를 명확하고 간단하게 사용할 수 있으므로 비즈니스 로직이 있는 인터페이스가 명확해지며 Java 개발에도 전반적으로 도움이 됩니다.

## 비디오 소개 {#video}

[AEM Gems 세션](https://experienceleague.adobe.com/ko/docs/events/experience-manager-gems-recordings/gems2014/aem-introduction-to-htl)의 다음 비디오에서는 HTL의 목적에 대한 개요 및 구현 예제를 제공합니다.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

이 비디오에서는 HTL이 [이전 명칭인 Sightly](history.md)로 지칭됩니다.

## 다음 단계 {#next-steps}

지금까지 HTL의 목표와 장점에 대해 알아보았습니다. 이제 언어 부분을 살펴보십시오. [HTML 템플릿 언어 시작하기](getting-started.md) 문서를 검토하십시오.
