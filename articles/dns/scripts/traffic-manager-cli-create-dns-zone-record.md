---
title: CLI 예제 - 도메인 이름에 대한 DNS 영역 및 레코드 만들기 - Azure | Microsoft Docs
description: Azure CLI 스크립트 예제에서는 도메인 이름에 대한 DNS 영역 및 레코드를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: traffic-manager
author: vhorne
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: 59ec8d4f93b18469818c9ead2e965679e41360ae
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694655"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI 스크립트 예제: DNS 영역 및 레코드 만들기

이 Azure CLI 스크립트 예제에서는 도메인 이름에 대한 DNS 영역 및 레코드를 만듭니다. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

```azurecli-interactive

# Create a resource group.
az group create \
  -n myResourceGroup \
  -l eastus

# Create a DNS zone. Substitute zone name "contoso.com" with the values for your own.

az network dns zone create \
  -g MyResourceGroup \
  -n contoso.com

# Create a DNS record. Substitute zone name "contoso.com" and IP address "1.2.3.4* with the values for your own.

az network dns record-set a add-record \
  -g MyResourceGroup \
  -z contoso.com \
  -n www \
  -a 1.2.3.4

# Get a list the DNS records in your zone
az network dns record-set list \
  -g MyResourceGroup \ 
  -z contoso.com
```

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, DNS 영역 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Azure DNS 영역을 만듭니다. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | *A* 레코드를 DNS 영역에 추가합니다. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | DNS 영역에서 모든*A* 레코드 집합을 나열합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

