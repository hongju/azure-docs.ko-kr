---
title: Operator 자격으로 PowerShell 사용 하 여 Azure Stack에 연결 | Microsoft Docs
description: Operator 자격으로 PowerShell 사용 하 여 Azure Stack에 연결 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 03/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: a0411e3aa8f4df1039a129fd73ebdad5a58c5a0c
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58257587"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Operator 자격으로 PowerShell 사용 하 여 Azure Stack에 연결

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack PowerShell을 사용 하 여 제안, 계획, 할당량 및 경고 만들기와 같은 리소스 관리를 구성할 수 있습니다. 이 항목에서는 운영자 환경을 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소를 실행 합니다 [개발 키트](./asdk/asdk-connect.md#connect-with-rdp) 또는 Windows 기반 외부 클라이언트의 경우 [VPN 통해 ASDK 연결할](./asdk/asdk-connect.md#connect-with-vpn)합니다. 

 - 설치할 [Azure Stack 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.  
 - 일관 된 [azure storage: 차이점 및 고려 사항](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Azure AD를 사용 하 여 연결

PowerShell을 사용 하 여 Azure Stack 운영자 환경을 구성 합니다. 다음 스크립트 중 하나를 실행 합니다. 사용자 고유의 환경 구성을 사용 하 여 Azure Active Directory (Azure AD) tenantName 및 Azure Resource Manager 끝점 값을 바꿉니다. <!-- GraphAudience endpoint -->

```PowerShell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>AD FS를 사용 하 여 연결

Azure Active Directory Federated Services (Azure AD FS)를 사용 하 여 PowerShell 사용 하 여 Azure Stack 운영자 환경에 연결 합니다. Azure Stack development kit를이 Azure Resource Manager 끝점으로 설정 됩니다 `https://adminmanagement.local.azurestack.external`합니다. Azure Stack 통합 시스템에 대 한 Azure Resource Manager 끝점을 가져오려고 서비스 공급자에 게 문의 합니다.

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS 사용자 id 사용 하 여 대화형 인증만을 지원 합니다. 자격 증명 개체를 필요한 경우 서비스 주체 (SPN)을 사용 해야 합니다. Azure Stack 및 AD FS를 사용 하 여 서비스 주체에 id 관리 서비스 설정에 대 한 자세한 내용은 참조 하세요. [AD FS에 대 한 관리 서비스 주체](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)합니다.

## <a name="test-the-connectivity"></a>연결 테스트

모든 항목을 가져온 했으므로 설정 PowerShell을 사용 하 여 Azure Stack에서 리소스를 만듭니다. 예를 들어, 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 머신 추가 수 있습니다. 다음 명령을 사용 하 여 명명 된 리소스 그룹을 만듭니다 **MyResourceGroup**합니다.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>다음 단계

- [Azure Stack용 템플릿 개발](user/azure-stack-develop-templates.md)
- [PowerShell을 사용하여 템플릿 배포](user/azure-stack-deploy-template-powershell.md)
  - [Azure Stack 모듈 참조](https://docs.microsoft.com/powershell/azure/azure-stack/overview)  
