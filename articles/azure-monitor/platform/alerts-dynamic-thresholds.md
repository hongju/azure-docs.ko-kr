---
title: Azure Monitor에서 동적 임계값을 사용하여 경고 만들기
description: 기계 학습 기반 동적 임계값을 사용한 경고 만들기
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 30f853bd65c83b922faf008fbb5279c28f197f68
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339009"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Azure Monitor의 동적 임계값을 사용한 메트릭 경고(공개 미리 보기)

동적 임계값 검색을 사용하는 메트릭 경고는 고급 ML(기계 학습)을 사용하여 메트릭의 과거 동작을 학습하고, 가능한 서비스 문제를 나타내는 패턴과 변칙을 식별합니다. 이 기능은 사용자가 완전 자동화 방식으로 Azure Resource Manager API를 통해 경고 규칙을 구성할 수 있도록 하여 간단한 UI와 대규모 작업을 지원합니다.

경고 규칙을 만들면 모니터링된 메트릭이 조정된 임계값을 기준으로 예상대로 동작하지 않을 때만 경고를 발생합니다.

여러분의 의견을 환영합니다. <azurealertsfeedback@microsoft.com>으로 의견을 보내주세요.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>동적 조건 형식은 언제 어떤 이유로 권장되나요?

1. **확장 가능한 경고** – 동적 임계값 경고 규칙은 한 번에 수백 개의 메트릭 시리즈에 대해 조정된 임계값을 만들 수 있습니다. 그렇지만 단일 메트릭에서 경고 규칙을 정의하는 것만큼 쉽습니다. UI 또는 Azure Resource Manager API를 사용하면 관리할 경고 규칙 수가 줄어듭니다. 이 확장 가능한 접근 방법은 메트릭 차원을 다루거나 모든 구독 리소스와 같은 여러 리소스에 적용할 때 특히 유용합니다. 두 경우 모두 경고 규칙을 관리 및 만들 때 상당한 시간 절약 효과를 가져옵니다. [템플릿을 사용하여 동적 임계값으로 메트릭 경고를 구성하는 방법에 자세히 알아봅니다](alerts-metric-create-templates.md).

1. **스마트 메트릭 패턴 인식** – 고유한 ML 기술을 사용하여 메트릭 패턴을 자동으로 검색하고, 종종 계절성(매시간/매일/매주)을 포함할 수 있는 시간에 따른 메트릭 변경에 맞게 조정할 수 있습니다. 시간에 따른 메트릭 동작에 맞게 조정하고, 해당 패턴에서의 편차에 따라 경고를 발생하면 각 메트릭의 "정확한" 임계값을 알지 못해도 됩니다. 동적 임계값에 사용되는 ML 알고리즘은 예상되는 패턴을 따르지 않는 노이즈(낮은 정밀도) 또는 넓은(낮은 재현율) 임계값을 방지하도록 디자인되었습니다.

1. **직관적인 구성** – 동적 임계값을 사용하면 고급 개념을 통해 메트릭 경고를 설정할 수 있으므로 메트릭에 대해 광범위한 도메인 지식을 보유할 필요성이 줄어듭니다.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>동적 임계값을 사용하여 경고 규칙을 구성하는 방법

동적 임계값을 사용한 경고는 Azure Monitor의 메트릭 경고를 통해 구성할 수 있습니다. [메트릭 경고를 구성하는 방법에 대해 자세히 알아봅니다](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>임계값은 어떻게 계산되나요?

동적 임계값은 메트릭 시리즈의 데이터를 지속적으로 학습하고, 일단의 알고리즘과 메서드를 사용하여 이를 모델링하려고 합니다. 이 기능은 계절성(매시간/매일/매주)과 같은 데이터의 패턴을 검색하고, 분산이 낮은 메트릭(예: 가용성 및 오류 비율) 뿐만 아니라 노이즈 메트릭(예: 컴퓨터 CPU 또는 메모리)을 처리할 수 있습니다.

이러한 임계값에서의 편차가 메트릭 동작의 변칙을 나타내는 방식으로 임계값이 선택됩니다.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>동적 임계값에서 '민감도' 설정은 무엇을 의미하나요?

경고 임계값 민감도는 경고를 트리거하는 데 필요한 메트릭 동작에서의 편차 크기를 제어하는 고급 개념입니다.
이 옵션을 사용하면 메트릭에 대한 도메인 지식(예: 정적 임계값)이 필요하지 않습니다. 제공되는 옵션은 다음과 같습니다.

- 높음 – 임계값이 좀 더 조밀하고 메트릭 시리즈 패턴에 근접합니다. 경고 규칙이 가장 작은 편차에서 트리거되므로 경고가 더 많이 발생합니다.
- 중간 - 높음 민감도(기본값)보다 임계값은 덜 조밀하고 좀 더 균일하며 경고 횟수는 더 적습니다.
- 낮음 - 임계값은 메트릭 시리즈 패턴에서 느슨하게 더 멀리 떨어져 있습니다. 경고 규칙이 큰 편차에서만 트리거되므로 경고가 더 적게 발생합니다.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>동적 임계값에서 '작업자' 설정 옵션이란 무엇인가요?

동적 임계값 경고 규칙은 동일한 경고 규칙을 사용하여 상한 및 하한 둘 다에 대해 메트릭 동작을 토대로 조정된 임계값을 만들 수 있습니다.
다음 세 가지 조건 중 하나에서 경고가 트리거되도록 선택할 수 있습니다.

- 상한 임계값보다 크거나 하한 임계값보다 낮습니다(기본값).
- 상한 임계값보다 큽니다.
- 하한 임계값보다 낮습니다.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>동적 임계값의 고급 설정은 무엇을 의미하나요?

**실패 기간** - 동적 임계값을 사용하여 특정 기간 내에서 시스템이 경고를 발생하는 데 필요한 최소 편차를 나타내는 "경고를 트리거할 위반 수"도 구성할 수 있습니다(기본 시간 범위는 20분 내의 4개 편차임). 사용자는 실패 기간 및 시간 범위를 변경하여 실패 기간을 구성하고, 경고할 대상을 선택할 수 있습니다. 이 기능은 일시적인 스파이크에 의해 생성된 경고 노이즈를 줄여줍니다. 예: 

문제가 주어진 5분씩 4번 연속해서 20분간 지속되는 경우 경고를 트리거하려면 다음 설정을 사용합니다.

![주어진 5분씩 4번 연속해서 20분간 지속되는 문제에 대한 실패 기간 설정](media/alerts-dynamic-thresholds/0008.png)

기간이 5분일 때 지난 30분 중 20분 동안 동적 임계값 위반이 발생한 경우 경고를 트리거하려면 다음 설정을 사용합니다.

![5분씩 묶은 지난 30분 중에서 20분 동안 지속되는 문제에 대한 실패 기간 설정](media/alerts-dynamic-thresholds/0009.png)

**다음 이전의 데이터 무시:** -사용자는 경우에 따라 시스템에서 임계값 계산을 시작하는 시작 날짜를 정의할 수도 있습니다. 일반적인 사용 사례는 리소스가 테스트 모드에서 실행되고 있었으나 이제 프로덕션 워크로드를 제공하도록 승격되므로, 테스트 단계 동안의 메트릭 동작을 무시해야 하는 경우일 수 있습니다.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>메트릭의 느린 동작 변경이 경고를 트리거하나요?

그렇지 않을 가능성이 있습니다. 동적 임계값은 느리게 진화하는 문제보다는 상당한 편차를 검색하는 데 유용합니다.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>임계값을 미리 본 다음, 계산하는 데 사용하는 데이터 양은 얼마나 되나요?

메트릭에 대 한 경고 규칙 생성 되기 전에 차트에 표시 되는 임계값을 계산 하는 시간 또는 매일 계절성 패턴 (10 일)을 계산 하려면 충분 한 기록 데이터를 기반으로 합니다. '주 단위 패턴 표시'에 키를 눌러 매주 계절성 패턴 (28 일)을 계산 하려면 충분 한 기록 데이터 취득 합니다. 경고 규칙을 만든 후 동적 임계값 보다 정확 하 게 임계값 수 있도록 필요한 모든 기록 데이터를 사용할 수 있으며 계속 해 서 배웁니다 및 새 데이터를 기반으로 적용 사용 합니다.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>얼마나 많은 데이터 경고를 트리거하는 데 필요한가?

새 리소스 또는 누락 된 메트릭 데이터에 있는 경우에 동적 임계값 3 일 분량의 데이터를 사용할 정확한 임계값을 확인 하기 전에 경고를 트리거하지 않습니다.

## <a name="dynamic-thresholds-best-practices"></a>동적 임계값 모범 사례

동적 임계값은 Azure Monitor의 모든 플랫폼 또는 사용자 지정 메트릭에 적용할 수 있으며, 일반 애플리케이션 및 인프라 메트릭에 맞게 조정되었습니다.
다음 항목은 동적 임계값을 사용하여 이러한 메트릭 중 일부에 대한 경고를 구성하는 방법에 대한 모범 사례입니다.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>가상 머신 CPU 백분율 메트릭의 동적 임계값

1. [Azure Portal](https://portal.azure.com)에서 **모니터**를 클릭합니다. [모니터] 보기는 모든 모니터링 설정과 데이터를 하나의 보기로 통합합니다.

2. **경고**를 클릭한 다음, **+ 새로운 경고 규칙**을 클릭합니다.

    > [!TIP]
    > 대부분의 리소스 블레이드도 **모니터링** 아래의 리소스 메뉴에 **경고**가 있으며, 여기서 경고를 만들 수 있습니다.

3. **대상 선택**을 클릭하고, 로드되는 컨텍스트 창에서 경고를 사용할 대상 리소스를 선택합니다. **구독** 및 **'Virtual Machines' 리소스 종류** 드롭다운을 사용하여 모니터링하려는 리소스를 찾습니다. 검색 창을 사용하여 리소스를 검색할 수도 있습니다.

4. 대상 리소스를 선택한 후에는 **조건 추가**를 클릭합니다.

5. **'CPU 백분율'** 을 선택합니다.

6. 필요에 따라 **기간** 및 **집계**를 조정하여 메트릭을 구체화합니다. 이 메트릭 유형은 동작을 잘 나타내지 않으므로 '최대' 집계 유형을 사용하지 않는 것이 좋습니다. '최대' 집계 유형의 경우 정적 임계값이 더 적절할 수 있습니다.

7. 지난 6시간의 메트릭에 대한 차트가 표시됩니다. 경고 매개 변수를 다음과 같이 정의합니다.
    1. **조건 유형** - '동적' 옵션을 선택합니다.
    1. **민감도** - 중간/낮음 민감도를 선택하여 경고 노이즈를 줄입니다.
    1. **연산자** - 동작이 애플리케이션 사용량을 나타내지 않는 경우이면 '보다 큼'을 선택합니다.
    1. **빈도** - 경고의 비즈니스 영향에 따라 줄이는 것이 좋습니다.
    1. **실패 기간 수**(고급 옵션) - 뒤돌아 보기 시간 범위는 15분 이상이어야 합니다. 예를 들어 기간을 5분으로 설정하는 경우 실패 기간 수는 3개 이상이어야 합니다.

8. 최근 데이터에 기반하여 계산된 임계값이 메트릭 차트에 표시됩니다.

9. **Done**을 클릭합니다.

10. **경고 규칙 이름**, **설명** 및 **심각도**와 같은 **경고 세부 정보**를 입력합니다.

11. 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어서 경고에 작업 그룹을 추가합니다.

12. **완료**를 클릭하여 메트릭 경고 규칙을 저장합니다.

> [!NOTE]
> 포털을 통해 생성되는 메트릭 경고는 대상 리소스와 동일한 리소스 그룹에 생성됩니다.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Application Insights HTTP 요청 실행 시간의 동적 임계값

1. [Azure Portal](https://portal.azure.com)에서 **모니터**를 클릭합니다. [모니터] 보기는 모든 모니터링 설정과 데이터를 하나의 보기로 통합합니다.

2. **경고**를 클릭한 다음, **+ 새로운 경고 규칙**을 클릭합니다.

    > [!TIP]
    > 대부분의 리소스 블레이드도 **모니터링** 아래의 리소스 메뉴에 **경고**가 있으며, 여기서 경고를 만들 수 있습니다.

3. **대상 선택**을 클릭하고, 로드되는 컨텍스트 창에서 경고를 사용할 대상 리소스를 선택합니다. **구독** 및 **'Application Insights' 리소스 종류** 드롭다운을 사용하여 모니터링하려는 리소스를 찾습니다. 검색 창을 사용하여 리소스를 검색할 수도 있습니다.

4. 대상 리소스를 선택한 후에는 **조건 추가**를 클릭합니다.

5. **'HTTP 요청 실행 시간'** 을 선택합니다.

6. 필요에 따라 **기간** 및 **집계**를 조정하여 메트릭을 구체화합니다. 이 메트릭 유형은 동작을 잘 나타내지 않으므로 '최대' 집계 유형을 사용하지 않는 것이 좋습니다. '최대' 집계 유형의 경우 정적 임계값이 더 적절할 수 있습니다.

7. 지난 6시간의 메트릭에 대한 차트가 표시됩니다. 경고 매개 변수를 다음과 같이 정의합니다.
    1. **조건 유형** - '동적' 옵션을 선택합니다.
    1. **연산자** - 기간 단축 시 발생하는 경고를 줄이려면 '보다 큼'을 선택합니다.
    1. **빈도** - 경고의 비즈니스 영향에 따라 줄이는 것이 좋습니다.

8. 최근 데이터에 기반하여 계산된 임계값이 메트릭 차트에 표시됩니다.

9. **Done**을 클릭합니다.

10. **경고 규칙 이름**, **설명** 및 **심각도**와 같은 **경고 세부 정보**를 입력합니다.

11. 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어서 경고에 작업 그룹을 추가합니다.

12. **완료**를 클릭하여 메트릭 경고 규칙을 저장합니다.

> [!NOTE]
> 포털을 통해 생성되는 메트릭 경고는 대상 리소스와 동일한 리소스 그룹에 생성됩니다.
