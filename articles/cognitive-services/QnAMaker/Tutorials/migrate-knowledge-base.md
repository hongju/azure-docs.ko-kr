---
title: 기술 자료 마이그레이션 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker로 만든 기술 자료를 새 기술 자료로 이동합니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/13/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: e91f41633bf6cf6581c237e7634150a5b92746a7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245918"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>내보내기-가져오기를 사용하여 기술 자료 마이그레이션

기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다. 

## <a name="prerequisites"></a>필수 조건

* 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료 마이그레이션
1. [QnA Maker 포털](https://qnamaker.ai)에 로그인합니다.
1. 마이그레이션할 기술 자료를 선택합니다.

1. **설정** 페이지에서 **기술 자료 내보내기**를 선택하여 기술 자료의 콘텐츠(질문, 답변, 메타데이터 및 추출된 데이터 원본 이름)가 포함된 .tsv 파일을 다운로드합니다.

1. 위쪽 메뉴에서 **기술 자료 만들기**를 선택한 다음, 빈 기술 자료를 만듭니다. 

    ![데이터 원본 설정](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 서비스의 **이름**을 지정합니다. 중복되는 이름과 특수 문자도 지원됩니다.

1. **만들기**를 선택합니다.

    ![기술 자료 만들기](../media/qnamaker-how-to-create-kb/create-kb.png)

1. 이 새 기술 자료에서 **설정** 탭을 열고, **기술 자료 가져오기**를 선택합니다. 이렇게 하면 질문, 답변 및 메타데이터를 가져오고 이러한 항목이 추출된 데이터 원본 이름이 유지됩니다.

   ![기술 자료 가져오기](../media/qnamaker-how-to-migrate-kb/Import.png)

1. 테스트 패널을 사용하여 새 기술 자료를 **테스트**합니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.
1. 기술 자료를 **게시**합니다. [기술 자료 게시](../How-To/publish-knowledge-base.md) 방법을 알아보세요.
1. 애플리케이션 또는 봇 코드에서 엔드포인트를 사용합니다. 여기에서 [QnA 봇을 만드는](../Tutorials/create-qna-bot.md) 방법을 참조하세요.

    ![QnA Maker 값](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

    이때 모든 기술 자료 콘텐츠인 질문, 답변 및 메타데이터와 URL 및 원본 파일의 이름을 새 기술 자료로 가져옵니다. 

## <a name="chat-logs-and-alterations"></a>채팅 로그 및 변경 내용
대/소문자를 구분하지 않는 변경 내용(동의어)은 자동으로 가져오지 않습니다. 이전 기술 자료의 변경 내용을 내보내려면 [V2 API](https://aka.ms/qnamaker-v2-apis)를 사용하고, 새 기술 자료에서 변경 내용을 이동하려면 [V4 API](https://aka.ms/qnamaker-v4-apis)를 사용합니다.

새 기술 자료는 Application Insights를 사용하여 채팅 로그를 저장하기 때문에 채팅 로그를 마이그레이션할 방법이 없습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-To/edit-knowledge-base.md)
