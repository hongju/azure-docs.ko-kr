---
title: '예제: 이미지에서 얼굴 식별 - Face API'
titleSuffix: Azure Cognitive Services
description: Face API를 사용하여 이미지에서 얼굴을 식별합니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 013467cb64220b525d429c901c48028bf65b3852
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864930"
---
# <a name="example-how-to-identify-faces-in-images"></a>예제: 이미지에서 얼굴을 식별하는 방법

이 가이드에서는 미리 알려진 사람들로 만든 PersonGroups를 사용하여 알 수 없는 얼굴을 식별하는 방법을 보여 줍니다. 샘플은 Face API 클라이언트 라이브러리를 사용하여 C#에서 작성되었습니다.

## <a name="concepts"></a>개념

이 가이드에서 다음 개념에 대해 익숙지 않다면 언제든지 [용어집](../Glossary.md)에 나와 있는 정의를 검색하세요.

- 얼굴 - 감지
- 얼굴 - 식별
- PersonGroup

## <a name="preparation"></a>준비

이 샘플에서는 다음에 대해 설명합니다.

- PersonGroup을 만드는 방법 - 이 PersonGroup에는 알려진 사용자 목록이 포함되어 있습니다.
- 각 사용자에게 얼굴을 할당하는 방법 - 이러한 얼굴은 사람을 식별하는 기준으로 사용됩니다. 사진 ID와 마찬가지로, 선명한 정면 얼굴을 사용하는 것이 좋습니다. 좋은 사진 모음에는 다른 동작을 하거나, 다른 색상의 옷을 입거나, 다른 머리 스타일을 하고 있는 같은 사람의 얼굴이 포함되어 있습니다.

이 샘플의 데모를 수행하려면 다음과 같은 여러 장의 사진을 준비해야 합니다.

- 사람 얼굴을 포함하는 몇 장의 사진. Anna, Bill 및 Clare의 [샘플 사진을 다운로드하려면 여기를 클릭하세요](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data).
- 식별을 테스트하는 데 사용되는 Anna, Bill 또는 Clare의 얼굴을 포함하거나 포함하지 않을 수 있는 일련의 테스트 사진. 이전 링크에서 일부 샘플 이미지를 선택할 수도 있습니다.

## <a name="step-1-authorize-the-api-call"></a>1단계: API 호출 권한 부여

Face API를 호출할 때마다 구독 키가 필요합니다. 이 키는 쿼리 문자열 매개 변수를 통해 전달되거나 요청 헤더에서 지정될 수 있습니다. 쿼리 문자열을 통해 구독 키를 전달하려면 [얼굴 - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에 대한 요청 URL을 참조하세요. 다음은 예제입니다.
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

대안으로 HTTP 요청 헤더에서 구독 키 **ocp-apim-subscription-key: &lt;구독 키&gt;** 를 지정할 수도 있습니다. 클라이언트 라이브러리를 사용하는 경우 구독 키는 FaceServiceClient 클래스의 생성자를 통해 전달됩니다. 예를 들면 다음과 같습니다.
 
```C# 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
구독 키는 Azure Portal의 Marketplace 페이지에서 얻을 수 있습니다. [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요.

## <a name="step-2-create-the-persongroup"></a>2단계: PersonGroup 만들기

이 단계에서는 세 명의 사용자 Anna, Bill, Clare를 포함하는 "MyFriends"라는 PersonGroup을 만들었습니다. 각 사람은 몇 개의 얼굴을 등록했습니다. 얼굴은 이미지에서 검색되어야 합니다. 이러한 모든 단계를 수행하면 다음 이미지와 같은 PersonGroup이 생성됩니다.

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1 PersonGroup에 대한 사람 정의
사람은 식별의 기본 단위입니다. 사람은 하나 이상의 알려진 얼굴을 등록할 수 있습니다. 그러나 PersonGroup은 사람들의 모음이며, 각 사람은 특정 PersonGroup 내에서 정의됩니다. 식별은 PersonGroup에 대해 수행됩니다. 따라서 PersonGroup을 만들고, 여기에 Anna, Bill, Clare와 같은 사람을 만듭니다.

먼저 새 PersonGroup을 만들어야 합니다. 이 작업은 [PersonGroup - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API를 사용하여 실행됩니다. 해당 클라이언트 라이브러리 API는 FaceServiceClient 클래스에 대한 CreatePersonGroupAsync 메서드입니다. 그룹을 만들기 위해 지정된 그룹 ID는 각 구독에 대해 고유합니다. 따라서 다른 PersonGroup API를 사용하여 PersonGroup을 가져오거나 업데이트하거나 삭제할 수도 있습니다. 그룹이 정의되면, [PersonGroup Person - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API를 사용하여 그룹 안에 사람을 정의할 수 있습니다. 클라이언트 라이브러리 메서드는 CreatePersonAsync입니다. 각 사용자를 만든 후에는 얼굴을 추가할 수 있습니다.

```C# 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 얼굴을 감지한 후 올바른 사람에게 등록
감지는 HTTP 요청 본문에 이미지 파일을 사용하여 [얼굴 - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API로 "POST" 웹 요청을 전송하여 진행됩니다. 클라이언트 라이브러리를 사용할 때는 FaceServiceClient 클래스에 대한 DetectAsync 메서드를 통해 얼굴 감지가 실행됩니다.

감지된 각 얼굴에 대해 [PersonGroup Person – 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)를 호출하여 올바른 사용자에게 추가할 수 있습니다.

다음 코드에서는 이미지에서 얼굴을 감지한 후 사람에게 추가하는 방법 프로세스를 보여 줍니다.
```C# 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
이미지에 둘 이상의 얼굴이 포함되어 있는 경우 가장 큰 얼굴만 추가됩니다. "targetFace = left, top, width, height" 형식의 문자열을 [PersonGroup Person - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API의 targetFace 쿼리 매개 변수에 제공하거나, AddPersonFaceAsync 메서드에 대해 targetFace 선택적 매개 변수를 사용하여 사람에게 다른 얼굴을 추가할 수 있습니다. 사람에게 추가한 각 얼굴에는 고유한 지속형 얼굴 ID가 지정되며 이 ID는 [PersonGroup Person – 얼굴 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) 및 [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에서 사용될 수 있습니다.

## <a name="step-3-train-the-persongroup"></a>3단계: PersonGroup 학습

PersonGroup을 사용하여 식별을 수행하려면 먼저 PersonGroup이 학습되어야 합니다. 그뿐 아니라 사람을 추가 또는 제거한 후에나 사람의 등록된 얼굴이 편집된 경우 다시 학습되어야 합니다. 학습은 [PersonGroup – 학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API를 통해 수행됩니다. 클라이언트 라이브러리를 사용할 때 다음과 같이 TrainPersonGroupAsync 메서드를 호출하기만 하면 됩니다.
 
```C# 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
학습은 비동기 프로세스입니다. TrainPersonGroupAsync 메서드가 반환된 후에도 완료되지 않을 수 있습니다. [PersonGroup - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API 또는 클라이언트 라이브러리의 GetPersonGroupTrainingStatusAsync 메서드를 사용하여 학습 상태를 쿼리해야 할 수도 있습니다. 다음 코드에서는 PersonGroup 학습이 완료될 때까지 기다리는 간단한 논리를 보여 줍니다.
 
```C# 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>4단계: 정의된 PersonGroup에 대해 얼굴 식별

식별을 수행할 때 Face API는 그룹 내의 모든 얼굴 중에서 테스트 얼굴의 유사성을 계산할 수 있으며, 해당 테스트 얼굴과 가장 비슷한 사람을 반환합니다. 이 작업은 [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API 또는 클라이언트 라이브러리의 IdentifyAsync 메서드를 통해 수행됩니다.

테스트 얼굴이 이전 단계를 사용하여 감지되면 얼굴 ID가 또 다른 인수로서 식별 API로 전달됩니다. 여러 얼굴 ID를 바로 식별할 수 있으며 결과에 모든 식별 결과가 포함됩니다. 기본적으로 식별을 수행하면 테스트 얼굴과 가장 잘 일치하는 1명의 사람만 반환됩니다. 원할 경우 선택적 매개 변수 maxNumOfCandidatesReturned를 지정하여 식별 기능이 더 많은 후보를 반환하도록 할 수 있습니다.

다음 코드에서는 식별 프로세스를 보여 줍니다.

```C# 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

이러한 단계를 완료한 경우, 다른 얼굴을 식별해보고, 얼굴 감지를 위해 업로드된 이미지에 따라 Anna, Bill 또는 Clare의 얼굴이 올바르게 식별될 수 있는지 확인할 수 있습니다. 다음 예제를 참조하세요.

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>5단계: 대규모에 대한 요청

알려진 것처럼 PersonGroup은 이전 디자인 제한점 때문에 최대 10,000명의 사람을 보유할 수 있습니다.
백만 명 규모까지 확장하는 시나리오에 대한 자세한 내용은 [대규모 기능을 사용하는 방법](how-to-use-large-scale.md)을 참조하세요.

## <a name="summary"></a>요약

이 가이드에서는 PersonGroup을 만들고 사람을 식별하는 프로세스를 배웠습니다. 다음 항목을 통해 앞에서 설명하고 시연한 기능을 다시 한 번 간략하게 살펴보시기 바랍니다.

- [얼굴 - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API를 사용하여 얼굴 감지
- [PersonGroup - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API를 사용하여 PersonGroups 만들기
- [PersonGroup Person - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API를 사용하여 사람 만들기
- [PersonGroup - 학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API를 사용하여 PersonGroups 학습
- [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API를 사용하여 PersonGroup에 대해 알 수 없는 얼굴 식별

## <a name="related-topics"></a>관련 항목

- [이미지에서 얼굴을 감지하는 방법](HowtoDetectFacesinImage.md)
- [얼굴을 추가하는 방법](how-to-add-faces.md)
- [대규모 기능을 사용하는 방법](how-to-use-large-scale.md)
