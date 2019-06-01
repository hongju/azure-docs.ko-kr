---
title: 질문과 대답 - Computer Vision
titlesuffix: Azure Cognitive Services
description: Azure Cognitive Services의 Computer Vision API에 대한 질문과 대답의 대답을 가져옵니다.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 579a47e70f292222914723606d032737b98822bc
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004876"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API 질문과 대답

> [!TIP]
> 이 FAQ에서 원하는 질문에 대한 답변을 찾을 수 없는 경우 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive)에서 Computer Vision API 커뮤니티에 물어보거나 [UserVoice의 도움말 및 지원](https://cognitive.uservoice.com/)에 문의하세요.

-----

**질문**: *사용자 지정 태그를 사용하도록 Computer Vision API를 학습할 수 있나요?  예를 들어 고양이 품종 사진을 주고 AI를 '교육'한 다음, AI 요청에서 품종 값을 받고 싶습니다.*

**대답**: 이 기능은 현재 이용할 수 없습니다. 하지만 Computer Vision에 이 기능을 도입하기 위해 엔지니어들이 열심히 작업 중입니다.

-----

**질문**: *인터넷에 연결하지 않고 Computer Vision을 로컬로 사용할 수 있나요?*

**대답**: 현재는 온-프레미스 또는 로컬 솔루션이 제공되지 않습니다.

-----

**질문**: *Computer Vision을 사용하여 자동차 번호판을 읽을 수 있나요?*

**대답**: Vision API는 OCR을 사용하여 우수한 텍스트 감지 성능을 제공하지만, 현재는 자동차 번호판에 최적화되지 않았습니다. Microsoft는 서비스를 개선하기 위해 지속적으로 노력하고 있으며, 자동차 번호판 인식을 위한 OCR를 기능 요청 목록에 추가했습니다.

-----

**질문**: *필기 인식에 지원되는 쓰기 표면은 무엇인가요?*

**대답**: 이 기술은 화이트보드, 백서, 노란색 스티커 메모를 포함하여 다양한 표면에서 작동합니다.

-----

**질문**: *필기 인식 작업에 시간이 얼마나 걸리나요?*

**대답**: 텍스트 길이에 따라 걸리는 시간이 달라집니다. 텍스트 길이가 길면 수초가 걸릴 수 있습니다. 따라서 필기 텍스트 인식 작업이 완료된 후 필기 텍스트 작업 결과 가져오기 작업을 사용하여 결과를 검색하려면 잠시 기다려야 할 수 있습니다.

-----

**질문**: *필기 인식 기술은 캐럿을 사용하여 줄 중간에 삽입된 텍스트를 어떻게 처리하나요?*

**대답**: 이러한 텍스트는 필기 인식 작업을 통해 별도의 줄로 반환됩니다.

-----

**질문**: *필기 인식 기술은 선을 그어 지운 단어 또는 줄을 어떻게 처리하나요?*

**대답**: 단어를 인식할 수 없도록 단어에 선을 여러 개 그으면 필기 인식 작업에서 해당 단어를 선택하지 않습니다. 그러나 단어에 선을 하나만 그으면 그 선이 노이즈로 취급되고, 필기 인식 작업에서 해당 단어를 선택합니다.

-----

**질문**: *필기 인식 기술에 어떤 텍스트 방향이 지원되나요?*

**대답**: 최대 30~40도 기울어진 텍스트는 필기 인식 작업에서 선택할 수 있습니다.

-----
