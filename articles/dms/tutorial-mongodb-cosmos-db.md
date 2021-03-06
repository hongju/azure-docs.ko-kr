---
title: '자습서: Azure Database Migration Service를 사용하여 오프라인에서 MongoDB를 Azure Cosmos DB의 API for MongoDB로 마이그레이션 | Microsoft Docs'
description: Azure Database Migration Service를 사용하여 오프라인으로 MongoDB 온-프레미스에서 Azure Cosmos DB의 API for MongoDB로 마이그레이션하는 방법에 대해 알아봅니다.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 5fd3200ab787a26b11feb121b5db125e4a79365c
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960388"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>자습서: DMS를 사용하여 오프라인에서 MongoDB를 Azure Cosmos DB의 API for MongoDB로 마이그레이션
Azure Database Migration Service를 사용하여 오프라인(1회)으로 데이터베이스를 MongoDB 온-프레미스 또는 클라우드 인스턴스에서 Azure Cosmos DB의 API for MongoDB로 마이그레이션할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

이 자습서에서는 Azure Database Migration Service를 사용하여 Azure Virtual Machine에서 호스트되는 MongoDB의 데이터 세트를 Azure Cosmos DB의 API for MongoDB로 마이그레이션합니다. MongoDB 원본을 설정하지 않은 경우 [Azure의 Windows VM에서 MongoDB 설치 및 구성](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb) 문서를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.
- [Azure Cosmos DB의 API for MongoDB 계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 VNET을 만듭니다. 이를 통해 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용하여 온-프레미스 원본 서버에서 사이트 간 연결을 제공합니다.
- Azure VNET(Virtual Network) 네트워크 보안 그룹 규칙이 다음과 같은 통신 포트를 차단하지 않는지 확인합니다. 443, 53, 9354, 445 및 12000 Azure VNET NSG 트래픽 필터링에 대한 자세한 정보는 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 문서를 참조하세요.
- Azure Database Migration Service에서 기본적으로 27017 TCP 포트인 원본 MongoDB 서버에 액세스할 수 있도록 Windows 방화벽을 엽니다.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록
1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음 **리소스 공급자**를 선택합니다.
 
    ![리소스 공급자 보기](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.
 
    ![리소스 공급자 등록](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>인스턴스 만들기
1.  Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.
 
    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. Azure Database Migration Service의 인스턴스를 만들 위치를 선택합니다. 

5. 기존 가상 네트워크(VNET)를 선택하거나 새로 만듭니다.

    VNET은 원본 MongoDB 인스턴스 및 대상 Azure Cosmos DB 계정에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공합니다.

    Azure Portal에서 VNET을 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](https://aka.ms/DMSVnet) 문서를 참조하세요.

6. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    적합한 Azure Database Migration Service 계층을 선택하는 데 도움이 필요할 경우 [여기](https://go.microsoft.com/fwlink/?linkid=861067)에 있는 블로그 게시물에서 권장 사항을 참조하세요.  

     ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기
서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.
 
      ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색하고 인스턴스를 선택합니다.

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.

4. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 형식** 텍스트 상자에서 **MongoDB**를 선택하고, **대상 서버 형식** 텍스트 상자에서 **CosmosDB(MongoDB API)** 를 선택한 다음, **작업 형식 선택**에서 **오프라인 데이터 마이그레이션**을 선택합니다. 

    ![Database Migration Service 프로젝트 만들기](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  **작업 만들기 및 실행**을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정
1. **원본 세부 정보** 화면에서 원본 MongoDB 서버에 대한 연결 세부 정보를 지정합니다.
    
   또한 마이그레이션하려는 컬렉션 데이터를 덤프한 위치에서 연결 문자열 모드를 사용하고 Blob 저장소 파일 컨테이너의 위치를 제공할 수 있습니다.

   > [!NOTE]
   > Azure Database Migration Service는 bson 문서나 json 문서를 Azure Cosmos DB의 API for MongoDB 컬렉션으로 마이그레이션할 수도 있습니다.
    
   또한 DNS 이름을 확인할 수 없는 경우에는 IP 주소를 사용할 수도 있습니다.

   ![원본 세부 정보 지정](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. **저장**을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정
1. **마이그레이션 대상 세부 정보** 화면에서 대상 Azure Cosmos DB 계정에 대한 연결 세부 정보를 지정합니다. 이 계정은 MongoDB 데이터를 마이그레이션할 미리 프로비전된 Azure Cosmos DB의 API for MongoDB 계정입니다.

    ![대상 세부 정보 지정](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. **저장**을 선택합니다.

## <a name="map-to-target-databases"></a>대상 데이터베이스에 매핑
1. **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 대상 데이터베이스를 선택합니다.

    **만들기** 문자열이 데이터베이스 이름 옆에 나타나는 경우 Azure Database Migration Service가 대상 데이터베이스를 찾을 수 없으므로 해당 서비스가 데이터베이스를 만든다는 것을 나타냅니다.

    마이그레이션의 이 시점에서 [처리량을 프로비전](https://docs.microsoft.com/azure/cosmos-db/set-throughput)할 수 있습니다. Cosmos DB에서 데이터베이스 수준에서 또는 컬렉션 수준에서 개별적으로 처리량을 프로비전할 수 있습니다. 처리량은 RU([요청 단위](https://docs.microsoft.com/azure/cosmos-db/request-units))로 측정됩니다. [Azure Cosmos DB 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/)에 대해 자세히 알아봅니다.

    ![대상 데이터베이스에 매핑](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. **저장**을 선택합니다.
3. **컬렉션 설정** 화면에서 컬렉션 목록을 확장한 다음, 마이그레이션할 컬렉션 목록을 검토합니다.

    Azure Database Migration Service는 대상 Azure Cosmos DB 계정에 존재하지 않는 원본 MongoDB 인스턴스에 존재하는 모든 컬렉션을 자동으로 선택합니다. 이미 데이터를 포함하는 컬렉션을 다시 마이그레이션하려면 이 블레이드에서 컬렉션을 명시적으로 선택해야 합니다.

    컬렉션에서 사용할 RU의 수량을 지정할 수 있습니다. Azure Database Migration Service는 컬렉션 크기에 따라 스마트 기본값을 제공합니다.

    확장성을 최적화하기 위해 [Azure Cosmos DB에서 분할](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)을 활용하도록 분할된 데이터베이스 키를 지정할 수도 있습니다. [분할된 데이터베이스/파티션 키를 선택하는 모범 사례](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)를 검토해야 합니다.

    ![컬렉션 선택 테이블](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. **저장**을 선택합니다.

5. **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

    ![마이그레이션 요약](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행
- **마이그레이션 실행**을 선택합니다.

    마이그레이션 작업 창이 나타나고, 작업 **상태**는 **시작되지 않음**입니다.

    ![작업 상태](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링
- 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **완료됨**으로 표시될 때까지 디스플레이를 업데이트합니다.

   > [!NOTE]
   > 데이터베이스 및 컬렉션 수준 마이그레이션 메트릭의 세부 정보를 가져오는 작업을 선택할 수 있습니다.

    ![작업 상태 완료됨](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DB에서 데이터 확인

- 마이그레이션이 완료되면 Azure Cosmos DB 계정을 검사하여 모든 컬렉션이 성공적으로 마이그레이션되었는지 확인할 수 있습니다.

    ![작업 상태 완료됨](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>추가 리소스

 * [Cosmos DB 서비스 정보](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>다음 단계
- Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)에서 추가 시나리오에 대한 마이그레이션 지침을 검토합니다.
