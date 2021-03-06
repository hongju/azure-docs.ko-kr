---
title: Azure Cosmos DB를 사용하는 애플리케이션에 대한 올바른 일관성 수준 선택
description: Azure Cosmos DB에서 애플리케이션에 대한 올바른 일관성 수준 선택
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/24/2018
ms.reviewer: sngun
ms.openlocfilehash: 11eb849567079bfb1293c3c1e8ce97c43b66d493
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116847"
---
# <a name="choose-the-right-consistency-level"></a>올바른 일관성 수준 선택 

고가용성, 낮은 대기 시간 또는 둘 다에 대한 복제에 의존하는 분산 데이터베이스는 읽기 일관성과 가용성, 대기 시간 및 처리량 간의 기본적인 절충을 수행합니다. 상업적으로 사용할 수 있는 대부분의 분산 데이터베이스는 개발자가 두 가지의 극단적인 일관성 모델: 강력한 일관성과 최종 일관성 간에 선택하도록 합니다. 개발자는 Azure Cosmos DB를 통해 5가지 잘 정의된 일관성 모델(강력, 제한된 부실, 세션, 일관적인 접두사, 결과적) 중에서 선택할 수 있습니다. 이러한 각 일관성 모델은 잘 정의되었으며, 직관적이고, 특정 실제 시나리오에 사용할 수 있습니다. 5가지 일관성 모델은 각각 [가용성 및 성능 장단점](consistency-levels-tradeoffs.md)을 제공하고 포괄적인 SLA로 지원됩니다. 다음 간단한 고려 사항은 여러 가지 일반적인 시나리오에서 올바른 선택을 하는 데 도움이 됩니다.

## <a name="sql-api-and-table-api"></a>SQL API 또는 Table API

Cosmos DB SQL API 또는 Table API를 사용하여 애플리케이션이 빌드되는 경우 다음 사항을 고려합니다.

- 많은 실제 시나리오의 경우 세션 일관성이 최적이며 권장되는 옵션입니다. 자세한 내용은 [애플리케이션에 대한 세션 토큰을 관리하는 방법](how-to-manage-consistency.md#utilize-session-tokens)을 참조하세요.

- 애플리케이션에 강력한 일관성이 필요한 경우 제한된 부실 일관성 수준을 사용하는 것이 좋습니다.

- 세션에서 제공되는 것이 아닌 더 엄격한 일관성 보장 및 쓰기에 대한 한 자릿수 밀리초 대기 시간이 필요한 경우 제한된 부실 일관성 수준을 사용하는 것이 좋습니다.  

- 애플리케이션에 결과적 일관성이 필요한 경우 일관된 접두사 일관성 수준을 사용하는 것이 좋습니다.

- 세션 일관성에서 제공되는 것보다 덜 엄격한 일관성 보장이 필요한 경우 일관된 접두사 일관성 수준을 사용하는 것이 좋습니다.

- 가장 높은 가용성 및 가장 낮은 대기 시간이 필요한 경우 최종 일관성 수준을 사용합니다.

- 성능 저하 없이 데이터 내구성을 더 높이려면 애플리케이션 계층에서 사용자 지정 일관성 수준을 만들면 됩니다. 자세한 내용은 [애플리케이션에서 사용자 지정 동기화를 구현하는 방법](how-to-custom-synchronization.md)을 참조하세요.

## <a name="cassandra-mongodb-and-gremlin-api"></a>Cassandra, MongoDB 및 Gremlin API

- Apache Cassandra의 “읽기 일관성 수준”과 Cosmos DB 일관성 수준 간 매핑에 대한 자세한 내용은 [일관성 수준 및 Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping)를 참조하세요.

- MongoDB의 "읽기 문제"와 Azure Cosmos DB 일관성 수준 간의 매핑에 대한 자세한 내용은 [일관성 수준 및 Cosmos DB API](consistency-levels-across-apis.md#mongo-mapping)를 참조하세요.

## <a name="consistency-guarantees-in-practice"></a>실제 일관성 보증

실제로 더 강력한 일관성 보증을 가져올 수 있습니다. 새로 고침 및 요청하는 데이터베이스의 상태 순서에 해당하는 읽기 작업에 대한 일관성 보장입니다. 읽기 일관성은 쓰기/업데이트 작업의 순서 지정 및 전파에 연결됩니다.  

* 일관성 수준이 **제한된 부실**로 설정된 경우 Cosmos DB는 클라이언트에서 항상 부실 창으로 바인딩된 지연으로 이전 쓰기의 값을 읽도록 보장합니다.

* 일관성 수준이 **강력**으로 설정된 경우 부실 창은 0과 동일하며 클라이언트에서 쓰기 작업의 커밋된 최신 값을 읽도록 보장됩니다.

* 나머지 세 가지 일관성 수준의 경우 부실 창은 워크로드에 따라 크게 달라집니다. 예를 들어 데이터베이스에서 발생하는 쓰기가 없는 경우 **최종**, **세션** 또는 **일관된 접두사** 일관성 수준이 있는 읽기 작업은 강력한 일관성 수준이 있는 읽기 작업과 동일한 결과를 발생할 수 있습니다.

Cosmos DB 계정이 강력한 일관성 이외의 일관성 수준으로 구성된 경우 PBS(확률적 제한된 부실) 메트릭을 확인하여 워크로드에 대한 강력한 일관성 읽기를 가져오도록 클라이언트의 확률을 확인할 수 있습니다. 이 메트릭은 Azure Portal에서 노출됩니다. 자세히 알아보려면 [PBS(확률적 제한된 부실) 메트릭 모니터링](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)을 참조하세요.

확률적 제한된 부실은 최종이 어떻게 최종 일관성인지 보여줍니다. 이 메트릭은 현재 Cosmos DB 계정에서 구성한 일관성 수준보다 얼마나 자주 더 강력한 일관성을 가져올 수 있는지에 대한 통찰력을 제공합니다. 즉, 쓰기 및 읽기 영역 조합에 대한 강력한 일관성 읽기를 가져오는 확률(밀리초로 제한됨)을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 일관성 수준에 대해 자세히 알아봅니다.

* [Cosmos DB API에서 일관성 수준 매핑](consistency-levels-across-apis.md)
* [다양한 일관성 수준에 대한 가용성 및 성능의 장단점](consistency-levels-tradeoffs.md)
* [애플리케이션에 대한 세션 토큰을 관리하는 방법](how-to-manage-consistency.md#utilize-session-tokens)
* [PBS(확률적 제한된 부실) 메트릭 모니터링](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
