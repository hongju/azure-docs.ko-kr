---
title: Azure Advisor로 Azure 애플리케이션의 성능 향상 | Microsoft Docs
description: Advisor를 사용하여 Azure 배포 성능을 최적화합니다.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: f3c6e39203fb0d864ecf952e0468959d66931e1f
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491589"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Azure Advisor로 Azure 애플리케이션의 성능 향상

Azure Advisor 성능 권장 사항은 업무에 중요한 애플리케이션의 속도 및 응답성을 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **성능** 탭에서 Advisor를 사용하여 성능 권장 사항을 볼 수 있습니다.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>정상 엔드포인트로 더 빠르게 장애 조치(failover)할 수 있게 Traffic Manager의 DNS TTL(Time To Live) 단축

Traffic Manager의 [TTL(Time to Live) 설정](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)을 사용하면 특정 엔드포인트가 쿼리에 응답을 멈추었을 때 엔드포인트로 전환하는 속도를 지정할 수 있습니다. TTL 값을 줄면 클라이언트가 작동하는 엔드포인트로 더 빠르게 라우팅됩니다.

Azure Advisor는 긴 TTL이 구성된 Traffic Manager 프로필을 식별하여 [빠른 장애 조치(Failover)](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)를 위해 프로필이 구성되었는지 여부에 따라 20초 또는 60초로 TTL을 구성하도록 권장합니다.

## <a name="improve-database-performance-with-sql-db-advisor"></a>SQL DB Advisor로 데이터베이스 성능 개선

Advisor는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다. SQL Database Azure와 통합되므로 SQL Azure Database의 성능을 향상시키기 위한 권장 사항이 표시됩니다. SQL Database Advisor는 사용 기록을 분석하여 SQL Azure Database의 성능을 평가합니다. 그런 후 데이터베이스의 일반적인 워크로드를 실행하는 데 가장 적합한 권장 사항을 제공합니다.

> [!NOTE]
> 권장 사항을 가져오려면 데이터베이스에 일주일의 사용 기간이 필요하고, 그 기간 내에 일관된 활동이 필요합니다. SQL Database Advisor는 일관성 있는 쿼리 패턴을 임의 활동 버스트보다 더욱 쉽게 최적화할 수 있습니다.

SQL Database Advisor에 대한 자세한 내용은 [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)를 참조하세요.

## <a name="improve-app-service-performance-and-reliability"></a>App Service 성능 및 안정성 향상

Azure Advisor는 App Services 환경을 개선하고 관련 플랫폼 기능을 검색하기 위한 모범 사례 권장 사항을 통합합니다. App Services 권장 사항 예제:
* 완화 옵션을 사용하여 메모리나 CPU 리소스가 앱 런타임에서 소모되는 인스턴스 검색
* Web Apps 및 데이터베이스를 함께 배치할 때 성능을 향상시키고 비용을 절감할 수 있는 인스턴스 검색

App Services 권장 사항에 대한 자세한 내용은 [Azure App Service에 대한 모범 사례](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)를 참조하세요.

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Managed Disks를 사용하여 디스크 I/O 제한 방지

Advisor는 해당 확장성 목표에 도달하는 스토리지 계정에 속한 가상 머신을 식별합니다. 이 조건으로 해당 VM이 I/O 제한에 걸리기 쉽습니다. Advisor는 성능 저하를 방지하기 위해 Managed Disks를 사용하도록 추천합니다.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Premium Storage를 사용하여 가상 머신 디스크의 성능 및 안정성 향상

Advisor는 스토리지 계정에 많은 양의 트랜잭션이 있는 표준 디스크를 갖춘 가상 머신을 식별하고 프리미엄 디스크로 업그레이드하도록 추천합니다. 

Azure Premium Storage는 I/O 사용량이 많은 작업을 실행하는 가상 머신에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. Premium Storage 계정을 사용하는 가상 머신 디스크는 SSD(솔리드 스테이트 드라이브)에 데이터를 저장합니다. 애플리케이션의 성능을 최상으로 높이려면 높은 IOPS가 필요한 모든 가상 머신 디스크를 프리미엄 스토리지로 마이그레이션하는 것이 좋습니다.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>쿼리 성능을 향상시키려면 SQL 데이터 웨어하우스 테이블에서 데이터 기울이기 제거

데이터 기울이기는 워크 로드를 실행하는 경우 불필요한 데이터 이동이나 리소스 병목 현상을 일으킬 수 있습니다. Advisor는 15%를 초과한 배포 데이터 기울이기를 감지하면 데이터를 다시 배포하고 테이블 배포 키를 다시 선택하도록 권고합니다. 기울이기 식별 및 제거에 대한 자세한 정보는 [기울이기 문제 해결](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)을 참조합니다.

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>쿼리 성능을 향상시키려면 SQL 데이터 웨어하우스 테이블에서 오래된 테이블 통계 업데이트 또는 만들기

Advisor는 최신 [테이블 통계](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)가 없는 테이블을 식별하고 테이블 통계를 만들거나 업데이트할 것을 권고합니다. SQL 데이터 웨어하우스 쿼리 최적화 프로그램은 최신 통계를 사용하여 쿼리 결과의 행 수 또는 카디널리티를 예상하며, 이를 통해 쿼리 최적화 프로그램이 가장 빠른 성능에 대한 고품질 쿼리 계획을 만들 수 있습니다.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>SQL Data Warehouse 테이블에서 캐시 사용률을 최적화하도록 확장하여 쿼리 성능 향상

Azure Advisor는 SQL Data Warehouse에서 캐시 사용률이 높고 적중 비율이 낮은지를 감지합니다. 이 조건은 SQL Data Warehouse의 성능에 영향을 줄 수 있는 높은 캐시의 제거를 나타냅니다. 관리자는 SQL Data Warehouse를 확장하여 워크로드에 충분한 캐시 용량을 할당하도록 제안합니다.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>SQL Data Warehouse 테이블을 복제된 테이블로 변환하여 쿼리 성능 향상

Advisor는 복제된 테이블이 아니지만 변환을 통해 이득을 얻을 수 있는 테이블을 식별하고 이러한 테이블을 변환하도록 제안합니다. 추천 사항은 SQL Data Warehouse 테이블의 복제된 테이블 크기, 열 수, 테이블 배포 유형 및 파티션 수를 기반으로 합니다. 컨텍스트에 대한 추천 사항에 추가적인 추론이 제공될 수 있습니다. 이 추천 사항을 확인하는 방법에 대한 자세한 내용은 [SQL Data Warehouse 추천 사항](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)을 참조하세요. 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>저장소 계정을 Azure Resource Manager로 마이그레이션하여 최신 Azure 기능 모두 이용하기

스토리지 계정 배포 모델을 Azure Resource Manager(Resource Manager)로 마이그레이션하여 템플릿 배포, 추가 보안 옵션, Azure Storage의 최신 기능 활용을 위해 GPv2 계정으로 업그레이드하는 기능을 활용하세요. Advisor는 Classic 배포 모델을 사용 중인 독립형 스토리지 계정을 식별하고 Resource Manager 배포 모델로 마이그레이션할 것을 권장합니다.

> [!NOTE]
> Azure Monitor의 클래식 경고가 2019년 6월에 사용 중지될 예정입니다. 새 플랫폼에서 경고 기능을 유지하려면 Resource Manager를 사용하도록 클래식 스토리지 계정을 업그레이드하는 것이 좋습니다. 자세한 내용은 [클래식 경고 사용 중지](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)를 참조하세요.

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor에서 성능 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **성능** 탭을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.

* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
