---
title: .NET Core 지원 Azure App Configuration용 빠른 시작 | Microsoft Docs
description: .NET Core 앱 지원 Azure App Configuration 사용 빠른 시작
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: cd4115aaeec15d14d48dcb71cbdc75212c6dc2db
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960679"
---
# <a name="quickstart-create-an-net-core-app-with-app-configuration"></a>빠른 시작: App Configuration을 사용하여 .NET Core 앱 만들기

Azure App Configuration은 Azure의 관리 구성 서비스로서, 코드와 구분하여 한 곳에서 모든 애플리케이션 설정을 쉽게 저장하고 관리할 수 있습니다. 이 빠른 시작은 .NET Core 콘솔 앱으로 서비스를 통합하는 방법을 보여줍니다.

이 빠른 시작의 단계를 완료하려면 아무 코드 편집기나 사용할 수 있습니다. 그러나 [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 뛰어난 옵션입니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>앱 구성 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>.NET Core 콘솔 앱 만들기

[.NET Core CLI(명령줄 인터페이스)](https://docs.microsoft.com/dotnet/core/tools/)를 사용하여 새 .NET Core Console App 프로젝트를 만듭니다. Visual Studio보다 .NET Core CLI를 사용할 때의 이점은 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있다는 것입니다.

1. 프로젝트에 대한 새 폴더를 만듭니다.

2. 새 폴더에서 다음 명령을 실행하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다.

        dotnet new console

## <a name="connect-to-app-configuration-store"></a>앱 구성 저장소에 연결

1. 다음 명령을 실행하여 `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet 패키지에 대한 참조를 추가합니다.

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. 다음 명령을 실행하여 프로젝트에 대한 패키지를 복원합니다.

        dotnet restore

3. *Program.cs*를 열고 `builder.AddAzureAppConfiguration()` 메서드를 호출하여 App Configuration을 사용하도록 `Main` 메서드를 업데이트합니다.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString**이라는 환경 변수를 설정하고 앱 구성 저장소에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고 명령 프롬프트를 다시 시작하여 변경 사항을 적용합니다.

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell을 사용하는 경우 다음 명령을 사용합니다.

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    macOS 또는 Linux를 사용하는 경우 다음 명령을 사용합니다.

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 다음 명령을 실행하여 콘솔 앱을 빌드합니다.

        dotnet build

3. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 앱을 로컬로 실행합니다.

        dotnet run

    ![빠른 시작 앱 실행](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 앱 구성 저장소를 만들고 .NET Core 콘솔 앱에 사용해봤습니다. App Configuration을 사용하는 방법을 자세히 알아보려면 인증을 보여주는 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Resources Integration에 대한 관리 ID](./integrate-azure-managed-service-identity.md)
