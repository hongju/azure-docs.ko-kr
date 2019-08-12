---
title: 단일 데이터베이스 만들기 - Azure SQL Database | Microsoft Docs
description: Azure Portal, PowerShell 및 Azure CLI를 사용하여 Azure SQL Database에서 단일 데이터베이스를 만들고 쿼리합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 07/29/2019
ms.openlocfilehash: f9c2ed77075affdfdf464dbf8a468629fc2e610d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640057"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>빠른 시작: Azure Portal, PowerShell 및 Azure CLI를 사용하여 Azure SQL Database에서 단일 데이터베이스를 만듭니다.

Azure SQL Database에서 데이터베이스를 만드는 가장 쉽고 빠른 방법은 [단일 데이터베이스](sql-database-single-database.md)를 만드는 것입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 단일 데이터베이스를 만들고 쿼리하는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/). 

이 빠른 시작에서 모든 단계의 경우 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-single-database"></a>단일 데이터베이스 만들기

단일 데이터베이스는 서버리스(미리 보기) 또는 프로비저닝된 컴퓨팅 계층에서 만들 수 있습니다.

- 프로비저닝된 컴퓨팅 계층의 단일 데이터베이스에는 미리 할당된 지정된 양의 컴퓨팅 리소스와 함께 두 가지 [구매 모델](sql-database-purchase-models.md) 중 하나를 사용하는 메모리 및 스토리지 리소스 세트가 있습니다.
- 서버리스 컴퓨팅 계층의 단일 데이터베이스에는 자동으로 크기가 조정된 컴퓨팅 리소스의 범위와 함께 코어당 지정된 양의 메모리, 지정된 양의 스토리지 리소스가 있으며, [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)에서만 제공됩니다.

단일 데이터베이스를 만들 때 지정된 Azure 지역의 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 내에서 데이터베이스를 관리 및 배치하기 위한 [SQL Database 서버](sql-database-servers.md)도 정의합니다.

> [!NOTE]
> 이 빠른 시작에서는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용하지만 [DTU 기반 구매 모델](sql-database-service-tiers-DTU.md)도 사용할 수 있습니다.

AdventureWorksLT 샘플 데이터를 포함하는 단일 데이터베이스를 만드는 방법은 다음과 같습니다.

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>데이터베이스 쿼리

데이터베이스를 만들었으니, 이제 Azure Portal에서 기본 제공 쿼리 도구를 사용하여 데이터베이스에 연결하고 데이터를 쿼리합니다.

1. 데이터베이스의 **SQL Database** 페이지에서 왼쪽 메뉴에 있는 **쿼리 편집기(미리 보기)** 를 선택합니다.

   ![쿼리 편집기에 로그인](./media/sql-database-get-started-portal/query-editor-login.png)

2. 사용자의 로그인 정보를 입력하고 **확인**을 선택합니다.
3. 다음 쿼리를 **쿼리 편집기** 창에 입력합니다.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. **실행**을 선택한 다음, **결과** 창에서 쿼리 결과를 검토합니다.

   ![쿼리 편집기 결과](./media/sql-database-get-started-portal/query-editor-results.png)

5. **쿼리 편집기** 창을 닫고, 저장하지 않은 편집 내용을 삭제하라는 메시지가 표시될 때 **확인**을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)를 진행하려면 이 리소스 그룹, 데이터베이스 서버 및 단일 데이터베이스를 그대로 유지합니다. 다음 단계에서는 다른 메서드를 사용하여 데이터베이스를 연결하고 쿼리하는 방법을 보여줍니다.

이러한 리소스 사용을 완료한 경우 다음과 같이 해당 리소스를 삭제할 수 있습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택한 다음, **myResourceGroup**을 선택합니다.
2. 리소스 그룹 페이지에서 **리소스 그룹 삭제**를 선택합니다.
3. 텍스트 상자에 *myResourceGroup*을 입력한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 온-프레미스 또는 원격 도구에서 단일 데이터베이스에 연결하기 위한 서버 수준 방화벽 규칙을 만듭니다. 자세한 내용은 [서버 수준 방화벽 규칙 만들기](sql-database-server-level-firewall-rule.md)를 참조하세요.
- 서버 수준 방화벽 규칙을 만든 후 여러 다양한 도구 및 언어를 사용하여 데이터베이스를 [연결 및 쿼리](sql-database-connect-query.md)할 수 있습니다.
  - [SQL Server Management Studio를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)
  - [Azure Data Studio를 사용하여 연결 및 쿼리](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI를 사용하여 프로비저닝된 컴퓨팅 계층에서 단일 데이터베이스를 만들려면 [Azure CLI 샘플](sql-database-cli-samples.md)을 참조하세요.
- Azure PowerShell을 사용하여 프로비저닝된 컴퓨팅 계층에서 단일 데이터베이스를 만들려면 [Azure PowerShell 샘플](sql-database-powershell-samples.md)을 참조하세요.
- Azure Powershell을 사용하여 서버리스 컴퓨팅 계층에서 단일 데이터베이스를 만들려면 [서버리스 데이터베이스 만들기](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)를 참조하세요.
