---
title: Azure에 SQL Server Integration Services 패키지 마이그레이션 | Microsoft Docs
description: Azure에 SQL Server Integration Services 패키지를 마이그레이션하는 방법을 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 884af4624c1e92ee765353c90fd189220664381d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183427"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>Azure에 SQL Server Integration Services 패키지 마이그레이션
SSIS(SQL Server Integration Services)를 사용하고 SQL Server에서 호스팅되는 SSISDB 원본에서 Azure SQL Database 서버 또는 Azure SQL Database Managed Instance에 의해 호스팅되는 대상 SSISDB에 SSIS 프로젝트/패키지를 마이그레이션하려는 경우 Integration Services 배포 마법사를 사용하여 다시 배포할 수 있습니다. SSMS(SQL Server Management Studio) 내에서 마법사를 시작할 수 있습니다.

사용하는 SSIS 버전이 2012 이전인 경우 SSIS 프로젝트/패키지를 프로젝트 배포 모델로 재배포하기 전에 먼저 SSMS에서 시작할 수도 있는 Integration Services 프로젝트 변환 마법사를 사용하여 변환해야 합니다. 자세한 내용은 문서 [프로젝트를 프로젝트 배포 모델로 변환](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)을 참조하세요.

> [!NOTE]
> DMS(Azure Database Migration Service)는 현재 원본 SSISDB의 마이그레이션을 지원하지 않지만 다음 프로세스를 사용하여 SSIS 프로젝트/패키지를 다시 배포할 수 있습니다. 

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 원본 SSIS 프로젝트/패키지를 평가합니다.
> * Azure에 SSIS 프로젝트/패키지를 마이그레이션합니다.

## <a name="prerequisites"></a>필수 조건
이러한 단계를 완료하려면 다음이 필요합니다.

- SSMS 버전 17.2 이상
- SSISDB를 호스트할 대상 데이터베이스 서버의 인스턴스
 
  아직 없는 경우:
    - Azure SQL Database의 경우 SQL Server(논리 서버만 해당) [양식](https://ms.portal.azure.com/#create/Microsoft.SQLServer)으로 이동하여 Azure Portal을 사용하여 (데이터베이스 없이) Azure SQL Database 서버를 만듭니다.
    - Azure SQL Database Managed Instance의 경우 [Azure SQL Database Managed Instance 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) 문서의 세부 지침을 따릅니다.

- SSIS는 Azure SQL Database 서버의 인스턴스([Azure Data Factory에서 Azure-SSIS Integration Runtime 프로비전](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) 문서에서 설명된 대로) 또는 Azure SQL Database Managed Instance([Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime) 문서에서 설명된 대로)에서 호스팅되는 대상 SSISDB를 사용하여 Azure-SSIS IR(Integration Runtime)을 포함하는 ADF(Azure Data Factory)에서 프로비전되어야 합니다. 

## <a name="assess-source-ssis-projectspackages"></a>원본 SSIS 프로젝트/패키지 평가
원본 SSISDB의 평가는 DMA(Database Migration Assistant) 또는 Azure DMS(Database Migration Service)로 아직 통합되지 않았지만 SSIS 프로젝트/패키지는 Azure SQL Database 서버 또는 Azure SQL Database Managed Instance에서 호스팅되는 대상 SSISDB에 재배포되므로 평가/유효성이 검사됩니다.

## <a name="migrate-ssis-projectspackages"></a>SSIS 프로젝트/패키지 마이그레이션
SSIS 프로젝트/패키지를 Azure SQL Database 서버 또는 Azure SQL Database Managed Instance로 마이그레이션하려면 다음 단계를 수행합니다.

1.  SSMS를 연 다음, **옵션**을 선택하여 **서버에 연결** 대화 상자를 표시합니다.

2.  **로그인** 탭에서 대상 SSISDB를 호스트하는 Azure SQL Database 서버 또는 Azure SQL Database Managed Instance에 연결하는 데 필요한 정보를 지정합니다.

    ![SSIS 로그인 탭](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  **연결 속성** 탭의 **데이터베이스에 연결** 텍스트 상자에서 **SSISDB**를 선택하거나 입력한 다음, **연결**을 선택합니다.

    ![SSIS 연결 속성 탭](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  SSMS 개체 탐색기에서 **Integration Services 카탈로그** 노드를 확장하고, **SSISDB**를 확장하고, 기존 폴더가 없는 경우 **SSISDB**를 마우스 오른쪽 단추로 클릭하고 새 폴더를 만듭니다.

5.  **SSISDB** 아래에서 폴더를 확장하고, **프로젝트**를 마우스 오른쪽 단추로 클릭한 다음, **프로젝트 배포**를 선택합니다.

    ![SSIS SSISDB 노드 확장됨](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  Integration Services 배포 마법사의 **소개** 페이지에서 정보를 검토한 다음, **다음**을 선택합니다.

    ![배포 마법사 소개 페이지](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  **원본 선택** 페이지에서 배포하려는 기존 SSIS 프로젝트를 지정합니다.

    SSMS도 원본 SSISDB를 호스팅하는 SQL Server에 연결된 경우 **Integration Services 카탈로그**를 선택한 다음, 프로젝트를 직접 배포할 카탈로그의 서버 이름 및 프로젝트 경로를 입력합니다.

    또는 **프로젝트 배포 파일**을 선택한 다음, 프로젝트를 배포하도록 기존 프로젝트 배포 파일(.ispac)에 경로를 지정합니다.

    ![배포 마법사 원본 선택 페이지](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  **다음**을 선택합니다.
9.  **대상 선택** 페이지에서 프로젝트에 대한 대상을 지정합니다.

       a. 서버 이름 텍스트 상자에 정규화된 Azure SQL Database 서버 이름(<server_name>.database.windows.net) 또는 Azure SQL Database Managed Instance 이름(<server_name.dns_prefix>.database.windows.net)을 입력합니다.
 
       b. 인증 정보를 제공한 다음, **연결**을 선택합니다.
    
       ![배포 마법사 대상 선택 페이지](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    다. 찾아보기를 선택하여 SSISDB에서 대상 폴더를 지정한 다음, 다음을 선택합니다.

    > [!NOTE]
    > **연결**을 선택한 후에만 **다음** 단추가 활성화됩니다. 

10. **유효성 검사** 페이지에서 모든 오류/경고를 본 다음, 필요한 경우 패키지를 적절하게 수정합니다.

    ![배포 마법사 유효성 검사 페이지](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. **다음**을 선택합니다.

12. **검토** 페이지에서 배포 설정을 검토합니다.

    > [!NOTE]
    > 이전을 선택하거나 왼쪽 창의 단계 링크를 선택하여 설정을 변경할 수 있습니다.

13. **배포**를 선택하여 배포 프로세스를 시작합니다.

14. 배포 프로세스가 완료된 후에 각 배포 작업의 성공 여부를 표시하는 결과 페이지를 볼 수 있습니다.
    a. 작업이 실패한 경우 **결과** 열에서 **실패함**을 선택하여 오류의 설명을 표시합니다.
    b. 필요에 따라 **보고서 저장**을 선택하여 XML 파일에 결과를 저장합니다.

15. **닫기**를 선택하여 Integration Services 배포 마법사를 종료합니다.

프로젝트의 배포가 오류 없이 성공하면 Azure-SSIS IR에서 실행하기 위해 포함된 모든 패키지를 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 마이그레이션 지침을 검토합니다.