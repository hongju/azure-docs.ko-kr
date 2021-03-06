---
title: Azure Portal을 사용한 Azure Database for MariaDB 서버 다시 시작
description: 이 문서에서는 Azure Portal을 사용하여 Azure Database for MariaDB 서버를 다시 시작하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 185e605db366fb392758ad9870a3c15badc0f321
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874871"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Azure Portal을 사용한 Azure Database for MariaDB 서버 다시 시작
이 항목에서는 Azure Database for MariaDB 서버를 다시 시작하는 방법을 설명합니다. 유지 관리를 위해 서버를 다시 시작해야 할 수 있지만 이 경우 서버가 해당 작업을 수행할 때 잠깐 가동이 중단됩니다.

서비스가 다른 작업 중이면 서버가 다시 시작되지 않습니다. 예를 들어, 서비스가 vCore 크기를 조정하는 것과 같이 이전에 요청된 작업을 처리할 수 있습니다.

다시 시작을 완료하는 데 필요한 시간은 MariaDB 복구 프로세스에 따라 달라집니다. 다시 시작 시간을 줄이려면 다시 시작 전에 서버에서 발생하는 작업의 양을 최소화하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [Azure Database for MariaDB 서버 및 데이터베이스](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>서버 다시 시작 수행

다음 단계에서는 MariaDB 서버를 다시 시작합니다.

1. Azure Portal에서 Azure Database for MariaDB 서버를 찾습니다.

2. 서버 **개요** 페이지의 도구 모음에서 **다시 시작**을 클릭합니다.

   ![Azure Database for MariaDB - 개요 - 다시 시작 단추](./media/howto-restart-server-portal/2-server.png)

3. **예**를 클릭하여 서버 다시 시작을 확인합니다.

   ![Azure Database for MariaDB - 다시 시작 확인](./media/howto-restart-server-portal/3-restart-confirm.png)

4. 서버 상태가 "다시 시작하는 중"으로 변경되는지 확인합니다.

   ![Azure Database for MariaDB - 다시 시작 상태](./media/howto-restart-server-portal/4-restarting-status.png)

5. 서버가 성공적으로 다시 시작되는지 확인합니다.

   ![Azure Database for MariaDB - 다시 시작 성공](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>다음 단계

[빠른 시작: Azure Portal을 사용한 Azure Database for MariaDB 서버 만들기](./quickstart-create-mariadb-server-database-using-azure-portal.md)