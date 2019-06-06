---
title: Azure Cognitive Services에서 Text Analytics를 사용하여 감정 분석 | Microsoft Docs
description: Text Analytics REST API를 사용하여 감정을 감지하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: 0c42e7f8b1fffb9cf998f4cee8d30405a8df74a4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011302"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>예제: Text Analytics를 사용하여 감정을 감지하는 방법

[감정 분석 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)는 텍스트 입력을 평가하고 각 문서에 대한 감정 점수(0(부정) ~ 1(긍정))를 반환합니다.

이 기능은 소셜 미디어, 고객 검토 및 토론 포럼에서 긍정적이거나 부정적인 감정을 감지하는 데 유용합니다. 사용자는 콘텐츠를 제공하고, 서비스에서는 모델 및 학습 데이터를 제공합니다.

현재 감정 분석은 영어, 독일어, 스페인어 및 프랑스어를 지원합니다. 다른 언어는 미리 보기로 있습니다. 자세한 내용은 [지원되는 언어](../text-analytics-supported-languages.md)를 참조하세요.

> [!TIP]
> Text Analytics는 감정 분석을 위한 Linux 기반 Docker 컨테이너 이미지도 제공하므로 데이터와 가까이 [Text Analytics 컨테이너를 설치하고 실행](text-analytics-how-to-install-containers.md)할 수 있습니다.

## <a name="concepts"></a>개념

Text Analytics는 기계 학습 분류 알고리즘을 사용하여 0과1 사이의 감정 점수를 생성합니다. 점수가 1에 가까울수록 긍정적인 감정을 나타내는 반면, 0에 가까울수록 부정적인 감정을 나타냅니다. 이 모델은 감정과 관련된 광범위한 텍스트 본문으로 미리 학습되어 있습니다. 현재 사용자 고유의 학습 자료는 제공할 수 없습니다. 모델에서는 텍스트 분석 중에 텍스트 처리, 음성 부분 분석, 단어 배치, 단어 연결 등이 포함된 기술을 결합하여 사용합니다. 알고리즘에 대한 자세한 내용은 [Text Analytics 소개](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/)를 참조하세요.

텍스트의 특정 엔터티에 대한 감정을 추출하는 것과 달리 감정 분석은 전체 문서에서 수행됩니다. 실제로 큰 텍스트 블록이 아니라 한 두 문장이 문서에 포함되면 점수 매기기의 정확도가 향상되는 경향이 있습니다. 객관성 평가 단계에서는 모델에서 문서 전체가 객관적인지, 아니면 감정이 포함되어 있는지를 결정합니다. 대부분 객관적인 문서는 감정 감지 구로 진행되지 않으므로 추가 처리 없이 .50점이 됩니다. 파이프라인에서 계속 진행되는 문서의 경우 다음 단계에서는 문서에서 감지된 감정의 정도에 따라 .50점 이상 또는 이하의 점수를 생성합니다.

## <a name="preparation"></a>준비

감정 분석에서는 사용할 텍스트를 더 작은 청크로 분할하여 제공하면 더 높은 품질의 결과를 생성합니다. 이는 구 블록이 클수록 더 잘 수행되는 핵심 구 추출과는 반대입니다. 두 작업에서 최상의 결과를 얻으려면 이에 따라 입력을 다시 구성하는 것이 좋습니다.

다음 형식의 JSON 문서가 있어야 합니다. ID, text, language

문서 크기는 문서당 5,120자 미만이어야 하며, 컬렉션당 최대 1,000개의 항목(ID)을 포함할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다. 감정 분석을 위해 제출할 수 있는 콘텐츠의 예제는 다음과 같습니다.

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1단계: 요청 구성

요청 정의에 대한 자세한 내용은 [텍스트 분석 API를 호출하는 방법](text-analytics-how-to-call-api.md)에서 찾을 수 있습니다. 편의상 다음 사항을 다시 설명합니다.

+ **POST** 요청을 만듭니다. 이 요청에 대한 API 문서인 [감정 분석 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

+ Azure의 Text Analytics 리소스 또는 인스턴스화된 [Text Analytics 컨테이너](text-analytics-how-to-install-containers.md)를 사용하여 감정 분석을 위한 HTTP 엔드포인트를 설정합니다. `/sentiment` 리소스를 포함해야 합니다(예: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`).

+ Text Analytics 작업에 대한 액세스 키가 포함되도록 요청 헤더를 설정합니다. 자세한 내용은 [엔드포인트 및 액세스 키를 찾는 방법](text-analytics-how-to-access-key.md)을 참조하세요.

+ 이 분석을 위해 준비한 JSON 문서 컬렉션을 요청 본문에 제공합니다.

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md)을 사용하거나 [설명서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)에서 **API 테스트 콘솔**을 열어 요청을 구성하고, 서비스에 POST합니다.

## <a name="step-2-post-the-request"></a>2단계: 요청 게시

요청을 받으면 분석이 수행됩니다. 서비스에서는 분당 최대 100개의 요청을 허용합니다. 각 요청은 최대 1MB가 될 수 있습니다.

상태 비저장 서비스임에 유의하세요. 계정에는 아무 데이터도 저장되지 않습니다. 결과는 응답으로 즉시 반환됩니다.


## <a name="step-3-view-results"></a>3단계: 결과 보기

감정 분석기에서는 텍스트를 주로 긍정 또는 부정으로 분류하여 0에서 1 사이의 점수를 할당합니다. 0.5에 가까운 값은 중립적이거나 불확정인 상태이며, 0.5점은 중립성을 나타냅니다. 문자열에서 감정을 분석할 수 없거나 감정이 없는 경우 점수는 항상 정확히 0.5입니다. 예를 들어 영어 언어 코드가 있는 스페인어 문자열을 전달하면 점수는 0.5점이 됩니다.

출력은 즉시 반환됩니다. JSON을 승인하는 애플리케이션으로 결과를 스트림하거나 로컬 시스템의 파일에 출력을 저장하고, 데이터를 정렬, 검색 및 조작할 수 있는 애플리케이션으로 가져올 수 있습니다.

다음 예제에서는 이 문서의 문서 컬렉션에 대한 응답을 보여 줍니다.

```
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="summary"></a>요약

이 문서에서는 Cognitive Services의 Text Analytics를 사용하여 감정 분석에 대한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

+ [감정 분석 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)는 선택한 언어로 사용할 수 있습니다.
+ 요청 본문의 JSON 문서에는 ID,  text 및 language 코드가 포함됩니다.
+ POST 요청은 개인 설정된 [액세스 키와 구독에 유효한 엔드포인트](text-analytics-how-to-access-key.md)를 사용하여 `/sentiment` 엔드포인트에 대해 수행됩니다.
+ 각 문서 ID에 대한 감정 점수로 구성된 응답 출력은 Excel 및 Power BI를 포함하여 JSON을 허용하는 모든 앱으로 스트림할 수 있습니다.

## <a name="see-also"></a>참고 항목 

 [Text Analytics 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 제품 페이지](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [핵심 구 추출](text-analytics-how-to-keyword-extraction.md)
