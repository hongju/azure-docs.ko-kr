---
title: Azure AD v2 JavaScript 빠른 시작 | Microsoft Docs
description: JavaScript 애플리케이션이 Azure Active Directory v2.0 엔드포인트로 보호되는 액세스 토큰을 필요로 하는 API를 호출하는 방법 알아보기
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a97e03f3c195b9fbd0ee7a09950414b7a940c7c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217482"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>빠른 시작: JavaScript 애플리케이션에서 사용자 로그인 및 액세스 토큰 획득

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

이 빠른 시작에서는 JavaScript SPA(단일 페이지 애플리케이션)에서 개인 계정, 회사 및 학교 계정에 로그인하고, Microsoft Graph API 또는 웹 API를 호출하기 위해 액세스 토큰을 가져오는 방법을 보여주는 코드 샘플을 사용하는 방법을 알아보겠습니다.

![이 빠른 시작에서 생성된 샘플 앱의 작동 방식](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>빠른 시작 애플리케이션 등록 및 다운로드
> 빠른 시작 애플리케이션을 시작하는 옵션은 두 가지가 있습니다.
> * [기본] [옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [수동] [옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
>
> 1. [Azure Portal - 애플리케이션 등록(미리 보기)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)으로 이동합니다.
> 1. 애플리케이션 이름을 입력하고 **등록**을 클릭합니다.
> 1. 지침에 따라 클릭 한 번으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
>
> 1. [Azure Portal](https://portal.azure.com/)에 로그인하여 애플리케이션을 등록합니다.
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
> 1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록(미리 보기) > 새 등록**을 차례로 선택합니다.
> 1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
> 1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
> 1. **리디렉션 URI** 섹션 아래에서 **웹** 플랫폼을 선택하고 해당 값을 `http://localhost:30662/`로 설정합니다.
> 1. 작업을 마쳤으면 **등록**을 선택합니다.  앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
> 1. 이 빠른 시작에서는 [암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 합니다. 등록된 애플리케이션의 왼쪽 탐색 창에서 **인증**을 선택합니다.
> 1. **고급 설정**의 **암시적 허용**에서 **ID 토큰** 및 **액세스 토큰** 확인란을 둘 다 사용하도록 설정합니다. 이 앱은 사용자를 로그인하고 API를 호출해야 하므로 ID 토큰 및 액세스 토큰이 필요합니다.
> 1. **저장**을 선택합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 리디렉션 URI를 `http://localhost:30662/`로 추가하고 **암시적 허용**을 사용하도록 설정해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [이러한 변경 내용 적용]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-javascript/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.

개발 환경에 적합한 이러한 옵션 중 하나를 선택할 수 있습니다.
* [웹 서버(예: Node.js)에 대한 주요 프로젝트 파일 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Visual Studio 프로젝트 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

로컬 폴더(예: **C:\Azure-Samples**)에 zip 파일 압축을 풉니다.

#### <a name="step-3-configure-your-javascript-app"></a>3단계: JavaScript 앱 구성

> [!div renderon="docs"]
> `index.html`을 편집하고 `applicationConfig` 아래에서 `clientID` 및 `authority` 값을 설정합니다.

> [!div class="sxs-lookup" renderon="portal"]
> `index.html`을 편집하고 `applicationConfig`를 다음으로 바꿉니다.

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> 위치:
> - `Enter_the_Application_Id_here` - 등록한 애플리케이션의 **애플리케이션(클라이언트) ID**입니다.
> - `Enter_the_Tenant_Info_Here` - 다음 옵션 중 하나로 설정됩니다.
>   - 애플리케이션이 **이 조직 디렉터리의 계정**을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: contoso.microsoft.com)으로 바꿉니다.
>   - 애플리케이션이 **모든 조직 디렉터리의 계정**을 지원하는 경우 이 값을 `organizations`로 바꾸세요.
>   - 애플리케이션이 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 지원하는 경우 이 값을 `common`으로 바꿉니다.
>
> > [!TIP]
> > **애플리케이션(클라이언트) ID**, **디렉터리(테넌트) ID** 및 **지원되는 계정 유형**의 값을 찾아보려면 Azure Portal에서 앱의 **개요** 페이지로 이동합니다.

> [!NOTE]
> 서버는 [Node.js](https://nodejs.org/en/download/) 프로젝트의 *server.js* 파일 및 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 프로젝트의 *.csproj* 파일에서 30662 포트를 수신 대기하도록 구성됩니다.
>

#### <a name="step-4-run-the-project"></a>4단계: 프로젝트 실행

* Node.js를 사용하는 경우:

    1. 프로젝트의 디렉터리에서 다음 명령을 실행하여 서버를 시작합니다.

        ```batch
        npm install
        node server.js
        ```

    1. 웹 브라우저를 열고 `http://localhost:30662/`로 이동합니다.
    1. **로그인** 단추를 클릭하여 로그인을 시작한 다음, Microsoft Graph API를 호출합니다.


* Visual Studio를 사용하는 경우 프로젝트 솔루션을 선택한 다음, **F5** 키를 눌러 프로젝트를 실행합니다.

## <a name="more-information"></a>추가 정보

### <a name="msaljs"></a>*msal.js*

MSAL은 사용자가 로그인하고 Microsoft Azure AD(Azure Active Directory)로 보호된 API에 액세스하는 데 사용되는 토큰을 요청하는 데 사용되는 라이브러리입니다. 빠른 시작의 *index.html*에는 라이브러리에 대한 참조가 포함됩니다.

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

또는 노드가 설치된 경우 npm을 통해 다운로드할 수 있습니다.

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL 초기화

빠른 시작 코드에서는 라이브러리를 초기화하는 방법도 보여줍니다.

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |Azure Portal에 등록된 애플리케이션의 애플리케이션 ID|
> |`authority`    |기관 URL입니다. *null*을 전달하면 기본 인증 기관을 `https://login.microsoftonline.com/common`으로 설정합니다. 앱이 단일 테넌트(디렉터리 하나에서 계정 대상 지정)인 경우 이 값을 `https://login.microsoftonline.com/<tenant name or ID>`로 설정합니다.|
> |`tokenReceivedCallback`| 인증이 앱으로 다시 리디렉션된 후에 호출되는 콜백 메서드. 여기서 `acquireTokenRedirectCallBack` 스트링이 전달됩니다. loginPopup을 사용하는 경우 Null입니다.|
> |`options`  |선택적 매개 변수의 컬렉션. 이 경우 `storeAuthStateInCookie` 및 `cacheLocation`이 선택적 매개 변수입니다. 옵션에 대한 자세한 내용은 [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options)를 참조하세요. |

### <a name="sign-in-users"></a>사용자 로그인

다음 코드 조각에서는 사용자에게 로그인하는 방법을 보여줍니다.

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (선택 사항) 로그인 시 사용자 동의를 요청하는 범위가 포함됩니다. 예를 들어 Microsoft Graph의 경우 `[ "user.read" ]` 또는 사용자 지정 Web API의 경우 `[ "<Application ID URL>/scope" ]`(즉, `api://<Application ID>/access_as_user`)입니다. 여기서 `applicationConfig.graphScopes` 스트링이 전달됩니다. |

> [!TIP]
> 또는 `loginRedirect` 메서드를 사용하여 현재 페이지를 팝업 창 대신 로그인 페이지로 리디렉션할 수 있습니다.

### <a name="request-tokens"></a>토큰 요청

MSAL에는 토큰을 획득하는 데 사용되는 세 가지 메서드인 `acquireTokenRedirect`, `acquireTokenPopup` 및 `acquireTokenSilent`가 있습니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `loginRedirect` 또는 `loginPopup` 메서드가 처음으로 실행된 후에 후속 호출 시 보호되는 리소스에 액세스하는 데 사용되는 토큰을 가져오는 데 일반적으로 사용되는 메서드가 `acquireTokenSilent`입니다. 요청 또는 갱신 토큰에 대한 호출은 자동으로 수행됩니다.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | API에 대한 액세스 토큰에서 반환되도록 요청되는 범위가 포함됩니다. 예를 들어 Microsoft Graph의 경우 `[ "user.read" ]` 또는 사용자 지정 Web API의 경우 `[ "<Application ID URL>/scope" ]`(즉, `api://<Application ID>/access_as_user`)입니다. 여기서 `applicationConfig.graphScopes` 스트링이 전달됩니다.|

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

사용자가 Azure AD v2.0 엔드포인트와 상호 작용하도록 해야 하는 경우가 있습니다. 예: 
* 암호가 만료되어 사용자가 자격 증명을 다시 입력해야 합니다.
* 애플리케이션이 사용자 동의가 필요한 추가 리소스 범위에 대한 액세스를 요청하고 있습니다.
* 2단계 인증이 필요합니다.

대부분의 애플리케이션에 대해 권장되는 일반적인 패턴은 먼저 `acquireTokenSilent`를 호출하고, 예외를 catch한 다음, `acquireTokenRedirect`(또는 `acquireTokenPopup`)를 호출하여 대화형 요청을 시작하는 것입니다.

`acquireTokenPopup(scope)`을 호출하면 로그인을 위한 팝업 창(또는 `acquireTokenRedirect(scope)`로 사용자가 Azure AD v2.0 엔드포인트로 리디렉션됨)이 표시되며, 여기에서 사용자는 해당 자격 증명을 확인하거나 필수 리소스에 동의하거나, 2단계 인증을 완료하여 상호 작용해야 합니다.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> 이 빠른 시작은 사용된 브라우저가 Internet Explorer인 경우, 팝업 창을 처리하는 것과 관련된 Internet Explorer 브라우저의 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)로 인해 `loginRedirect` 및 `acquireTokenRedirect` 메서드를 사용합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작의 애플리케이션을 빌드하는 방법에 대한 자세한 단계별 가이드에서는 아래 JavaScript 자습서를 사용해 보세요.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>이 빠른 시작의 애플리케이션을 만드는 단계 알아보기

> [!div class="nextstepaction"]
> [Graph API 호출 자습서](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>설명서, FAQ, 문제 등에 대한 MSAL 리포지토리 찾아보기

> [!div class="nextstepaction"]
> [msal.js GitHub 리포지토리](https://github.com/AzureAD/microsoft-authentication-library-for-js)
