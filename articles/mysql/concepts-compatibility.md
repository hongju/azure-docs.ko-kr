---
title: Azure Database for MySQL 드라이버 및 관리 도구 호환성
description: 이 문서에서는 Azure Database for MySQL과 호환되는 MySQL 드라이버 및 관리 도구를 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 05f48145973777052590f8d10e1a2ce1fd22ec7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525385"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Azure Database for MySQL과 호환되는 MySQL 드라이버 및 관리 도구
이 문서에서는 Azure Database for MySQL과 호환되는 드라이버 및 관리 도구를 설명합니다.

## <a name="mysql-drivers"></a>MySQL 드라이버
Azure Database for MySQL은 전 세계에서 가장 인기 있는 커뮤니티 버전의 MySQL 데이터베이스를 사용합니다. 따라서 다양한 프로그래밍 언어 및 드라이버와 호환됩니다. 세 가지 최신 버전의 MySQL 드라이버를 지원하는 것이 목표이며, MySQL 드라이버의 기능 및 사용 편의를 개선하기 위해 지속적으로 오픈 소스 커뮤니티의 작성자와 협력하고 있습니다. 다음 표에는 테스트를 통해 Azure Database for MySQL 5.6 및 5.7과 호환되는 것으로 확인된 드라이버 목록이 정리되어 있습니다.

| **드라이버** | **연결** | **호환되는 버전** | **호환되지 않는 버전** | **참고 사항** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | SSL MySQLi를 사용하여 PHP 7.0 연결을 하는 경우 연결 문자열에 MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT를 추가합니다. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO 집합: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` 옵션을 false로 설정합니다.|
| .NET | [GitHub의 MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [Nuget의 설치 패키지](https://www.nuget.org/packages/MySqlConnector/) | 0.27 이상 | 0.26.5 이하 | |
| MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | 인코딩 버그로 인해 UTF8이 아닌 Windows 시스템 중 일부에서 연결이 실패할 수 있습니다. |
| Nodejs |  [Github의 MySQLjs](https://github.com/mysqljs/mysql/) <br> NPM의 설치 패키지:<br> NPM에서 `npm install mysql` 실행 | 2.15 | 2.14.1 이하 | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 이하 | 버전 1.3에 대한 연결 문자열에서는 `allowNativePasswords=true` 를 사용합니다. 버전 1.4는 수정 사항을 포함하고 `allowNativePasswords=true` 가 필요 하지 않습니다. |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 이하 | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 이하 | |

## <a name="management-tools"></a>관리 도구
호환성 장점은 데이터베이스 관리 도구까지 확장됩니다. 데이터베이스 조작이 사용자 권한의 범위 내에서 이루어지는 한, 기존 도구가 Azure Database for MySQL과 계속 호환됩니다. 다음 표에는 테스트를 통해 Azure Database for MySQL 5.6 및 5.7과 호환되는 것으로 확인된 세 가지 일반적인 데이터베이스 관리 도구가 정리되어 있습니다.

|                                     | **MySQL Workbench 6.x 이상** | **Navicat 12** | **PHPMyAdmin 4.x 이상** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| 만들기, 업데이트, 읽기, 쓰기, 삭제 | X | X | X |
| SSL 연결 | X | X | X |
| SQL 쿼리 자동 완성 | X | X |  |
| 데이터 가져오기 및 내보내기 | X | X | X |
| 여러 형식으로 내보내기 | X | X | X |
|  및 복원 |  | X |  |
| 서버 매개 변수 표시 | X | X | X |
| 클라이언트 연결 표시 | X | X | X |

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL에 대한 연결 문제 해결](howto-troubleshoot-common-connection-issues.md)
