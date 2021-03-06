---
title: Azure Stack에서 MySQL 데이터베이스를 사용 하 여 | Microsoft Docs
description: MySQL Server 리소스 공급자 어댑터를 배포 하는 빠른 단계와 Azure Stack의 서비스로 MySQL 데이터베이스를 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 52e13068b6ecd732a64b60926366ac300731dae8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58186717"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Azure Stack에서 MySQL 리소스 공급자 배포

MySQL 서버 리소스 공급자를 사용하여 MySQL 데이터베이스를 Azure Stack 서비스로 노출합니다. MySQL 리소스 공급자는 Windows Server 2016 Server Core 가상 머신 (VM)에서 서비스로 실행 됩니다.

> [!IMPORTANT]
> 항목을 만드는 서버에서 해당 호스트 SQL 또는 MySQL 리소스 공급자에만 사용할 수 있습니다. 리소스 공급자에 의해 생성 되지 않은 호스트 서버에서 생성 하는 항목 일치 하지 않는 상태가 될 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure Stack MySQL 리소스 공급자를 배포 하기 전에 준비에서 되어야 하는 몇 가지 필수 구성 요소가 있습니다. 이러한 요구 사항에 맞게, 권한 있는 끝점을 VM에 액세스할 수 있는 컴퓨터에서이 문서의 단계를 완료 합니다.

* 따라서 아직 수행 하지 않은 경우 [Azure Stack 등록](./azure-stack-registration.md) Azure 마켓플레이스 항목을 다운로드할 수 있도록 Azure를 사용 합니다.
* 이 설치를 실행할 시스템에서 Azure 및 Azure Stack PowerShell 모듈을 설치 해야 합니다. 해당 시스템에는.NET 런타임의 최신 버전을 사용 하 여 Windows 10 또는 Windows Server 2016 이미지를 해야 합니다. 참조 [Azure Stack 용 PowerShell 설치](./azure-stack-powershell-install.md)합니다.
* 다운로드 하 여 Azure Stack marketplace에 필요한 Windows Server core VM을 추가 합니다 **Windows Server 2016 Datacenter Server Core** 이미지입니다.

* 이진 MySQL 리소스 공급자를 다운로드 하 고 임시 디렉터리에 콘텐츠를 추출할 자동 압축 풀기 프로그램을 실행 합니다.

  >[!NOTE]
  >인터넷 액세스가 없는 시스템에서 MySQL 공급자를 배포 하려면 다음을 복사 합니다 [mysql 커넥터-net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) 파일로 로컬 경로입니다. 사용 하 여 경로 이름을 제공 합니다 **DependencyFilesLocalPath** 매개 변수입니다.

* 리소스 공급자에는 빌드를 최소 해당 Azure Stack에 있습니다.

  |Azure Stack의 최소 버전|MySQL RP 버전|
  |-----|-----|
  |버전 1808 (1.1808.0.97)|[MySQL 버전 1.1.33.0 RP](https://aka.ms/azurestackmysqlrp11330)|  
  |버전 1808 (1.1808.0.97)|[MySQL 버전 1.1.30.0 RP](https://aka.ms/azurestackmysqlrp11300)|
  |버전 1804 (1.0.180513.1)|[MySQL 버전 1.1.24.0 RP](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* 데이터 센터 통합 필수 구성 요소가 충족 되는지 확인 합니다.

    |필수 요소|참조|
    |-----|-----|
    |조건부 DNS 전달이 올바르게 설정 됩니다.|[Azure Stack 데이터 센터 통합-DNS](azure-stack-integrate-dns.md)|
    |리소스 공급자에 대 한 인바운드 포트가 열려 있습니다.|[Azure 데이터 센터 통합 스택-끝점 게시](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI 인증서 주체 및 SAN이 올바르게 설정 됩니다.|[Azure Stack 배포 필수 PKI 필수 조건](azure-stack-pki-certs.md#mandatory-certificates)[Azure Stack 배포 PaaS 인증서 필수 구성 요소](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>인증서

_통합된 시스템 설치용_합니다. 선택적 PaaS 인증서 섹션에서 설명한 SQL PaaS PKI 인증서를 제공 해야 합니다 [Azure Stack 배포에 대 한 PKI 요구 사항](./azure-stack-pki-certs.md#optional-paas-certificates)합니다. 지정 된 위치에.pfx 파일을 배치 합니다 **DependencyFilesLocalPath** 매개 변수입니다. ASDK 시스템에 대 한 인증서를 제공 하지 않습니다.

## <a name="deploy-the-resource-provider"></a>리소스 공급자 배포

모든 필수 구성 요소를 설치한 후 실행할 수 있습니다 합니다 **DeployMySqlProvider.ps1** MySQL 리소스 공급자를 배포 하는 스크립트입니다. Azure Stack의 버전에 대 한 다운로드 한 MySQL 리소스 공급자 설치 파일의 일부로 DeployMySqlProvider.ps1 스크립트를 추출 합니다.

 > [!IMPORTANT]
 > 리소스 공급자를 배포 하기 전에 새로운 기능, 수정 및 배포에 영향을 줄 수 있는 알려진된 문제에 대 한 자세한 릴리스 정보를 검토 합니다.

MySQL 리소스 공급자를 배포 하려면 (PowerShell ISE 없습니다 ()를 관리자 권한으로 새 PowerShell 창을 열고 MySQL 리소스 공급자 이진 파일의 압축을 푼 디렉터리로 변경 합니다. 새 PowerShell 창을 사용 하 여 이미 로드 되어 있는 PowerShell 모듈에 의해 발생 하는 잠재적인 문제를 방지 하는 것이 좋습니다.

실행 합니다 **DeployMySqlProvider.ps1** 다음 작업을 완료 하는 스크립트:

* Azure Stack에서 저장소 계정에 인증서 및 기타 아티팩트를 업로드합니다.
* 갤러리를 사용 하 여 MySQL 데이터베이스를 배포할 수 있도록 갤러리 패키지를 게시 합니다.
* 호스팅 서버를 배포에 대 한 갤러리 패키지를 게시 합니다.
* 다운로드 한 다음 MySQL 리소스 공급자를 설치는 Windows Server 2016 core 이미지를 사용 하 여 VM을 배포 합니다.
* 리소스 공급자 VM에 매핑되는 로컬 DNS 레코드를 등록 합니다.
* 연산자 계정에 대 한 로컬 Azure Resource Manager로 리소스 공급자를 등록합니다.

> [!NOTE]
> MySQL 리소스 공급자 배포를 시작할 때의 **system.local.mysqladapter** 리소스 그룹이 생성 됩니다. 이 리소스 그룹 하는 데 필요한 배포 완료 후 75 분까지 걸릴 수 있습니다.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 parameters

명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않으면, 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 묻는 메시지가 나타납니다.

| 매개 변수 이름 | 설명 | 주석 또는 기본 값 |
| --- | --- | --- |
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ |
| **AzCredential** | Azure Stack에 대 한 자격 증명을 서비스 관리자 계정입니다. Azure Stack 배포에 사용한 동일한 자격 증명을 사용 합니다. | _필수_ |
| **VMLocalCredential** | MySQL 리소스 공급자 VM의 로컬 관리자 계정의 자격 증명입니다. | _필수_ |
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ |
| **AzureEnvironment** | Azure Stack 배포에 사용 되는 서비스 관리자 계정의 Azure 환경입니다. Azure AD 배포에만 필요합니다. 지원 되는 환경 이름은 **AzureCloud**를 **AzureUSGovernment**에 중국의 Azure AD를 사용 하는 경우 또는 **AzureChinaCloud**합니다. | AzureCloud |
| **DependencyFilesLocalPath** | 통합된 시스템만을 위한 인증서.pfx 파일을이 디렉터리에 배치 되어야 합니다. 연결이 끊어진된 환경에 대 한 다운로드 [mysql 커넥터-net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) 이 디렉터리에 있습니다. 필요에 따라 하나의 Windows 업데이트 MSU이 패키지를 복사할 수 있습니다. | _선택적_ (_필수_ 통합된 시스템 또는 연결이 끊어진된 환경에 대 한) |
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ |
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하려는 횟수입니다.| 2 |
| **RetryDuration** | 시간 (초)에서 재시도 사이의 시간 제한 간격입니다. | 120 |
| **제거** | 리소스 공급자와 연결 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아닙니다. |
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아닙니다. |
| **AcceptLicense** | GPL 라이선스에 동의 하는 메시지를 건너뜁니다.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>사용자 지정 스크립트를 사용 하 여 MySQL 리소스 공급자 배포

수동 구성 리소스 공급자를 배포 하는 경우을 제거 하려면 다음 스크립트를 사용자 지정할 수 있습니다. Azure Stack 배포에 필요한 기본 계정 정보 및 암호를 변경 합니다.

```powershell
# Install the Azure and Azure Stack PowerShell modules as described in the prerequisites section above before running these commands.

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

리소스 공급자 설치 스크립트가 완료 되 면 브라우저를 새로 고쳐 최신 업데이트를 볼 수 있도록 합니다.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Azure Stack 포털을 사용 하 여 배포를 확인 합니다.

1. 서비스 관리자와 관리 포털에 로그인 합니다.
2. 선택 **리소스 그룹**
3. 선택 된 **시스템.\< 위치\>.mysqladapter** 리소스 그룹입니다.
4. 리소스 그룹 개요에 대 한 요약 페이지에 실패 한 배포가 없는 없어야 합니다.
5. 마지막으로 선택 **가상 머신** 확인 하려면 관리 포털에서 VM의 MySQL 리소스 공급자는 성공적을 만들고 실행 합니다.

## <a name="next-steps"></a>다음 단계

[호스팅 서버를 추가 합니다.](azure-stack-mysql-resource-provider-hosting-servers.md)
