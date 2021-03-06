---
title: Azure Sentinel 미리 보기에서 Windows 방화벽 데이터 수집 | Microsoft Docs
description: Sentinel Azure에서 Windows 방화벽 데이터를 수집 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2356a7e5426037ffe9fc8b304ac113f4a3fe2a17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103352"
---
# <a name="connect-windows-firewall"></a>Windows 방화벽 연결

> [!IMPORTANT]
> Azure Sentinel 현재 공개 미리 보기 중입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows 방화벽 커넥터를 사용 하면 Azure Sentinel 작업 영역에 연결 되어 있는 경우 Windows 방화벽 로그에 쉽게 연결할 수 있습니다. 이 연결을 사용 하면 대시보드를 볼, 사용자 지정 경고 및 조사를 향상 시킬 수 있습니다. 이 조직의 네트워크에 대 한 자세한 정보를 제공 하며 사용자 보안 작업 기능을 향상 시킵니다.  


> [!NOTE]
> 
> - 데이터 작업 영역의 Azure Sentinel 실행 하는 지리적 위치에 저장 됩니다.

## <a name="enable-the-connector"></a>커넥터를 사용 하도록 설정 

1. Sentinel Azure portal에서 선택 **데이터 컬렉션** 를 클릭 하 고는 **Windows 방화벽** 바둑판식으로 배열 합니다. 
1. 스트리밍 하려는 데이터 유형을 선택 합니다.
1. **Install**을 클릭합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

수준도 로그를 Log Analytics에 나타나기 시작 될 때까지 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Windows 방화벽 연결 하는 방법을 알아보았습니다. Azure에서 감시 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 합니다.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

