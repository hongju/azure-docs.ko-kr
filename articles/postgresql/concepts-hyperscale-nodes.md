---
title: Azure Database for PostgreSQL – 하이퍼스케일(Citus) (미리 보기)에서 노드
description: 서버 그룹에 있는 노드의 두 형식입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077277"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus) (미리 보기)에서 노드

하이퍼스케일 (Citus) (미리 보기)  호스팅 유형은 "비공유" 아키텍처에서 PostgreSQL용 Azure Database 서버가(노드라고 함) 서로 조정하도록 허용합니다. 서버 그룹의 노드는 단일 서버에 있을 때 보다 전체적으로 많은 데이터를 저장하고 더 많은 CPU 코어를 사용 합니다. 아키텍처에서는 서버 그룹에 더 많은 노드를 추가하여 데이터베이스를 확장할 수 있습니다.

## <a name="coordinator-and-workers"></a>코디네이터 및 작업자

각 서버 그룹에는 하나의 코디네이터 노드 및 여러 작업자 노드가 있습니다. 응용 프로그램은 코디네이터 노드로 쿼리를 보내고, 이 노드는 관련 작업자에 릴레이하고 그 결과를 누적합니다. 응용 프로그램은 작업자에 직접 연결할 수 없습니다.

각 쿼리에 대해 코디네이터는 단일 작업자 노드로 라우팅하거나  필요한 데이터가 단일 노드 또는 여러 노드에 있는지 여부에 따라 여러 노드에 걸쳐 병렬화합니다. 코디네이터는 메타 데이터 테이블을 참조하여 어떻게 처리할지 결정 합니다. 이러한 테이블은 DNS 이름 및 작업자 노드 상태, 노드 간의 데이터 분포를 추적합니다.

## <a name="next-steps"></a>다음 단계
- 노드가 [분산 데이터](concepts-hyperscale-distributed-data.md)를 저장하는 방법에 대해 알아봅니다. 
