---
title: 'Azure Cosmos DB용 ASP.NET MVC 자습서: 웹 애플리케이션 개발'
description: Azure Cosmos DB를 사용하여 MVC 웹 애플리케이션을 만드는 ASP.NET MVC 자습서 JSON을 저장하고 Azure Websites - ASP NET MVC 단계별 자습서에서 호스팅하는 todo 앱에서 데이터에 액세스합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 3f19c442d0f5806147ee05b3f0d2d32740a8ecdd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121743"
---
# <a name="_Toc395809351"></a>ASP.NET MVC 자습서: Azure Cosmos DB를 사용한 웹 애플리케이션 개발

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Azure Cosmos DB를 효율적으로 활용하여 JSON 문서를 저장 및 쿼리할 수 있는 방법을 강조하기 위해 이 문서에서는 Azure Cosmos DB를 사용하여 todo 앱을 빌드하는 방법을 보여 주는 종합적인 연습을 제공합니다. 작업은 Azure Cosmos DB에 JSON 문서로 저장됩니다.

![이 자습서에서 만든 할 일 모음 MVC 웹 애플리케이션의 스크린샷 - ASP NET MVC 단계별 자습서](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

이 연습에서는 Azure Cosmos DB 서비스를 사용하여 Azure에서 호스트되는 ASP.NET MVC 웹 애플리케이션의 데이터를 저장하고 액세스하는 방법을 보여 줍니다. ASP.NET MVC 구성 요소가 아닌 Azure Cosmos DB에만 집중하는 자습서를 찾는 경우 [Azure Cosmos DB C# 콘솔 응용 프로그램 빌드](sql-api-get-started.md)를 참조하세요.

> [!TIP]
> 이 자습서에서는 이전에 ASP.NET MVC 및 Azure Websites를 사용해 본 경험이 있다고 가정합니다. ASP.NET 또는 [필수 도구](#_Toc395637760)를 처음 사용하는 경우 [GitHub][GitHub]에서 전체 샘플 프로젝트를 다운로드하고 이 샘플의 지침을 따르는 것이 좋습니다. 프로젝트를 빌드하고 나면 이 문서를 검토하여 프로젝트의 컨텍스트에서 코드를 이해할 수 있습니다.
> 
> 

## <a name="_Toc395637760"></a>이 데이터베이스 자습서의 필수 조건
이 문서의 지침을 따르기 전에 다음이 있는지 확인해야 합니다.

* 활성 Azure 계정.  Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* Microsoft Azure SDK for .NET for Visual Studio 2017, Visual Studio 설치 관리자를 통해 사용 가능합니다.

이 문서의 모든 스크린샷은 Microsoft Visual Studio Community 2017을 사용하여 작성되었습니다. 시스템이 다른 버전으로 구성된 경우 화면과 옵션이 일부 달라질 수 있지만 위의 필수 구성 요소를 충족하면 솔루션을 사용할 수 있습니다.

## <a name="_Toc395637761"></a>1단계: Azure Cosmos DB 데이터베이스 계정 만들기
Azure Cosmos DB 계정을 만들어 시작해 보겠습니다. Azure Cosmos DB용 SQL 계정이 이미 있거나 이 자습서에 Azure Cosmos DB 에뮬레이터를 사용하고 있는 경우 [새 ASP.NET MVC 응용 프로그램 만들기](#_Toc395637762)로 건너뛸 수 있습니다.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
이제 새 ASP.NET MVC 애플리케이션을 처음부터 만드는 방법을 살펴보겠습니다. 

## <a name="_Toc395637762"></a>2단계: 새 ASP.NET MVC 애플리케이션 만들기

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 가리킨 후 **프로젝트**를 클릭합니다. **새 프로젝트** 대화 상자가 나타납니다.

2. **프로젝트 형식** 창에서 **템플릿**, **Visual C#**, **웹**을 확장한 후 **ASP.NET 웹 애플리케이션**을 선택합니다.

      ![ASP.NET 웹 애플리케이션 프로젝트 유형이 강조 표시된 새 프로젝트 대화 상자의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. **이름** 상자에 프로젝트의 이름을 입력합니다. 이 자습서에서는 "todo"라는 이름을 사용합니다. 다른 이름을 사용하도록 선택한 경우에는 이 자습서에서 todo 네임스페이스를 지칭할 때마다 지정한 응용 프로그램 이름을 사용하도록 제공된 코드 샘플을 조정해야 합니다. 
4. **찾아보기**를 클릭하여 프로젝트를 만들 폴더로 이동한 후 **확인**을 클릭합니다.
   
      **새 ASP.NET 웹 응용 프로그램** 대화 상자가 나타납니다.
   
    ![MVC 애플리케이션 템플릿이 강조 표시된 새 ASP.NET 웹 애플리케이션 대화 상자의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. 템플릿 창에서 **MVC**를 선택합니다.

6. **확인** 을 클릭하면 Visual Studio에서 빈 ASP.NET MVC 템플릿을 스캐폴딩합니다. 

          
7. Visual Studio에서 상용구 MVC 애플리케이션을 만들면 로컬에서 실행할 수 있는 빈 ASP.NET 애플리케이션을 갖게 됩니다.
   
    모두 ASP.NET "Hello World" 애플리케이션을 본 적이 있다고 확신하므로 프로젝트 로컬 실행은 건너뛰겠습니다. 바로 이 프로젝트에 Azure Cosmos DB를 추가하고 애플리케이션을 작성하겠습니다.

## <a name="_Toc395637767"></a>3단계: MVC 웹 애플리케이션 프로젝트에 Azure Cosmos DB 추가
이 솔루션에 필요한 대부분의 ASP.NET MVC 배관을 만들었으므로 이제 이 자습서의 실제 목적으로 돌아가 MVC 웹 애플리케이션에 Azure Cosmos DB를 추가해 보겠습니다.

1. Azure Cosmos DB .NET SDK는 패키지되어 NuGet 패키지로 배포되며, Visual Studio에서 NuGet 패키지를 다운로드하려면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭하여 Visual Studio에서 NuGet 패키지 관리자를 사용합니다.
   
    ![NuGet 패키지 관리가 강조 표시된 솔루션 탐색기 내 웹 애플리케이션 프로젝트에 대한 마우스 오른쪽 클릭 옵션의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    **NuGet 패키지 관리** 대화 상자가 나타납니다.
2. NuGet **찾아보기** 상자에 ***Azure DocumentDB***를 입력합니다. (패키지 이름은 Azure Cosmos DB로 업데이트되지 않았습니다.)
   
    결과에서 **Microsoft.Azure.DocumentDB by Microsoft** 패키지를 설치합니다. 그러면 Azure Cosmos DB 패키지 및 모든 종속성(예: Newtonsoft.Json)이 다운로드되어 설치됩니다. **미리 보기** 창에서 **확인**을 클릭하고 **라이선스 승인** 창에서 **동의**를 클릭하여 설치를 완료합니다.
   
    ![Microsoft Azure Cosmos DB Client Library가 강조 표시된 NuGet 패키지 관리 창의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      또는 패키지 관리자 콘솔을 사용하여 패키지를 설치할 수 있습니다. 이렇게 하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 프롬프트에 다음을 입력합니다.
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. 패키지가 설치되고 나면 Visual Studio 솔루션은 Microsoft.Azure.Documents.Client 및 Newtonsoft.Json이라는 두 개의 새 참조가 추가된 상태로 다음과 유사합니다.
   
    ![솔루션 탐색기에서 JSON 데이터 프로젝트에 추가된 두 참조의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>4단계: ASP.NET MVC 애플리케이션 설정
이제 이 MVC 애플리케이션에 모델, 뷰 및 컨트롤러를 추가합니다.

* [모델 추가](#_Toc395637764).
* [컨트롤러 추가](#_Toc395637765).
* [뷰 추가](#_Toc395637766).

### <a name="_Toc395637764"></a>JSON 데이터 모델 추가
먼저 MVC의 **M** 인 모델을 만들겠습니다. 

1. **솔루션 탐색기**에서 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **클래스**를 차례로 클릭합니다.
   
      **새 항목 추가** 대화 상자가 나타납니다.
2. 새 클래스의 이름을 **Item.cs**로 지정하고 **추가**를 클릭합니다. 
3. 이 새 **Item.cs** 파일에서 마지막 *using 문*뒤에 다음을 추가합니다.
   
        using Newtonsoft.Json;
4. 이제 이 코드를 
   
        public class Item
        {
        }
   
    다음 코드로 바꿉니다.
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Azure Cosmos DB의 모든 데이터가 네트워크를 통해 전달되고 JSON으로 저장됩니다. JSON.NET에서 개체를 직렬화/역직렬화하는 방식을 제어하기 위해 방금 만든 **Item** 클래스에서 본 것처럼 **JsonProperty** 특성을 사용할 수 있습니다. 꼭 **필요한** 과정은 아니지만 저는 제가 만든 속성이 JSON camelCase 명명 규칙을 따르는지 확인하고 싶습니다. 
   
    JSON의 경우 속성 이름 형식을 제어할 수 있을 뿐만 아니라, **Description** 속성에 대해 했던 것처럼 .NET 속성의 이름을 완전히 다시 지정할 수 있습니다. 

### <a name="_Toc395637765"></a>컨트롤러 추가
**M** 작업을 마쳤으므로 이제 MVC의 **C**인 컨트롤러 클래스를 만듭니다.

1. **솔루션 탐색기**에서 **Controllers** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **컨트롤러**를 차례로 클릭합니다.
   
    **스캐폴드 추가** 대화 상자가 나타납니다.
2. **MVC 5 컨트롤러 - 비어 있음**을 선택한 후 **추가**를 클릭합니다.
   
    ![MVC 5 컨트롤러 - 비어 있음 옵션이 강조 표시된 스캐폴드 추가 대화 상자의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. 새 컨트롤러의 이름을 **ItemController**
   
    ![컨트롤러 추가 대화 상자의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    파일을 만들고 나면 Visual Studio 솔루션은 **솔루션 탐색기**에 새 ItemController.cs 파일이 있는 상태로 다음과 유사합니다. 이전에 만든 새 Item.cs 파일도 표시됩니다.
   
    ![Visual Studio 솔루션의 스크린샷 - 새 ItemController.cs 파일 및 Item.cs 파일이 강조 표시된 솔루션 탐색기](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    나중에 돌아올 것이므로 ItemController.cs를 닫아도 됩니다. 

### <a name="_Toc395637766"></a>뷰 추가
이제 MVC의 **V** 인 뷰를 만듭니다.

* [항목 인덱스 뷰 추가](#AddItemIndexView).
* [새 항목 뷰 추가](#AddNewIndexView).
* [항목 편집 뷰 추가](#_Toc395888515).

#### <a name="AddItemIndexView"></a>항목 인덱스 뷰 추가
1. **솔루션 탐색기**에서 **Views** 폴더를 확장하고 앞에서 **ItemController**를 추가할 때 Visual Studio에서 만들어진 빈 **Item** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**, **보기**를 차례로 클릭합니다.
   
    ![뷰 추가 명령이 강조 표시된 Visual Studio에서 만든 항목 폴더를 보여 주는 솔루션 탐색기의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. **뷰 추가** 대화 상자에서 다음을 수행합니다.
   
   * **뷰 이름** 상자에 ***Index***를 입력합니다.
   * **템플릿** 상자에서 ***List***을 선택합니다.
   * **모델 클래스** 상자에서 ***Item(todo.Models)*** 을 선택합니다.
   * 레이아웃 페이지 상자에 ***~/Views/Shared/_Layout.cshtml***을 입력합니다.
     
   ![뷰 추가 대화 상자를 보여주는 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. 이러한 값이 모두 설정된 후 **추가** 를 클릭하면 Visual Studio에서 새 템플릿 뷰를 만듭니다. 완료되면 만들어진 cshtml 파일이 열립니다. 나중에 돌아올 것이므로 Visual Studio에서 해당 파일을 닫아도 됩니다.

#### <a name="AddNewIndexView"></a>새 항목 뷰 추가
**항목 인덱스** 뷰를 만든 방법과 유사하게 이제 새 **항목**을 만들기 위한 새 뷰를 만듭니다.

1. **솔루션 탐색기**에서 **Item** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **보기**를 차례로 클릭합니다.
2. **뷰 추가** 대화 상자에서 다음을 수행합니다.
   
   * **뷰 이름** 상자에 ***Create***를 입력합니다.
   * **템플릿** 상자에서 ***Create***를 선택합니다.
   * **모델 클래스** 상자에서 ***Item(todo.Models)*** 을 선택합니다.
   * 레이아웃 페이지 상자에 ***~/Views/Shared/_Layout.cshtml***을 입력합니다.
   * **추가**를 클릭합니다.
   
#### <a name="_Toc395888515"></a>항목 편집 뷰 추가
마지막으로, 이전과 동일한 방식으로 **항목** 을 편집하기 위한 최종 뷰를 추가합니다.

1. **솔루션 탐색기**에서 **Item** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **보기**를 차례로 클릭합니다.
2. **뷰 추가** 대화 상자에서 다음을 수행합니다.
   
   * **뷰 이름** 상자에 ***Edit***를 입력합니다.
   * **템플릿** 상자에서 ***Edit***를 선택합니다.
   * **모델 클래스** 상자에서 ***Item(todo.Models)*** 을 선택합니다.
   * 레이아웃 페이지 상자에 ***~/Views/Shared/_Layout.cshtml***을 입력합니다.
   * **추가**를 클릭합니다.

이 작업이 완료되면 나중에 이러한 뷰로 돌아올 것이므로 Visual Studio에서 모든 cshtml 문서를 닫습니다.

## <a name="_Toc395637769"></a>5단계: Azure Cosmos DB 연결
표준 MVC를 처리했으므로 이제 Azure Cosmos DB에 대한 코드를 추가해 보겠습니다. 

이 섹션에서는 다음을 처리하기 위한 코드를 추가합니다.

* [완료되지 않은 항목 나열](#_Toc395637770).
* [항목 추가](#_Toc395637771).
* [항목 편집](#_Toc395637772).

### <a name="_Toc395637770"></a>MVC 웹 응용 프로그램에서 완료되지 않은 항목 나열
먼저 Azure Cosmos DB에 연결하고 이를 사용할 모든 논리가 포함된 클래스를 추가합니다. 이 자습서에서는 이 모든 논리를 DocumentDBRepository라는 리포지토리 클래스로 캡슐화합니다. 

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다. 새 클래스의 이름을 **DocumentDBRepository**로 지정하고 **추가**를 클릭합니다.
2. 새로 만든 **DocumentDBRepository** 클래스에서 *네임스페이스* 선언 위에 다음 *using 문*을 추가합니다.
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net;
        
    이제 이 코드를 
   
        public class DocumentDBRepository
        {
        }
   
    다음 코드로 바꿉니다.
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. 구성에서 일부 값을 읽어올 것이므로 응용 프로그램의 **Web.config** 파일을 열고 `<AppSettings>` 섹션 아래에 다음 줄을 추가합니다.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. 이제 Azure Portal의 키 블레이드를 사용하여 *엔드포인트* 및 *authKey* 값을 업데이트합니다. 키 블레이드의 **URI**를 엔드포인트 설정 값으로 사용하고 키 블레이드의 **기본 키** 또는 **보조 키**를 authKey 설정 값으로 사용합니다.

    Azure Cosmos DB 리포지토리의 연결을 완료했으므로 이제 애플리케이션 논리를 추가해 보겠습니다.

1. todo 모음 애플리케이션으로 가장 먼저 할 일은 완료되지 않은 항목을 표시하는 것입니다.  다음 코드 조각을 **DocumentDBRepository** 클래스 내의 아무 곳에나 복사하여 붙여 넣습니다.
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. 앞에서 추가한 **ItemController** 를 열고 네임스페이스 선언 위에 다음 *using 문* 을 추가합니다.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    프로젝트 이름이 "todo"가 아닌 경우 프로젝트 이름을 반영하기 위해 "todo.Models";를 사용하여 업데이트해야 합니다.
   
    이제 이 코드를
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    다음 코드로 바꿉니다.
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. **Global.asax.cs**를 열고 **Application_Start** 메서드에 다음 줄을 추가합니다. 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

이때 오류 없이 솔루션을 작성할 수 있어야 합니다.

지금 애플리케이션을 실행하면 **HomeController** 및 해당 컨트롤러의 **Index** 뷰로 이동합니다. 이것은 시작할 때 선택한 MVC 템플릿 프로젝트에 대한 기본 동작이지만 여기서는 사용하지 않습니다. 이 동작을 변경하기 위해 이 MVC 응용 프로그램의 라우팅을 변경하겠습니다.

***App\_Start\RouteConfig.cs***를 열고 "defaults:"로 시작하는 줄을 찾은 후 다음과 같이 변경합니다.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

이 구문은 이제 ASP.NET MVC에 라우팅 동작을 제어하기 위한 URL에 값이 지정되지 않은 경우 **Home** 대신 **Item**을 컨트롤러로 사용하고 사용자 **Index**를 뷰로 사용하라고 지시합니다.

이제 애플리케이션을 실행하면 애플리케이션에서 리포지토리 클래스를 호출하는 **ItemController**를 호출하며 GetItems 메서드를 사용하여 완료되지 않은 모든 항목을 **Views**\\**Item**\\**Index** 뷰로 반환합니다. 

이 프로젝트를 지금 빌드하여 실행하면 이제 다음과 같이 표시됩니다.    

![이 데이터베이스 자습서에서 만든 할 일 모음 웹 애플리케이션의 스크린샷](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>항목 추가
빈 그리드 외에 확인할 항목이 있도록 데이터베이스에 일부 항목을 추가하겠습니다.

Azure Cosmos DB에 레코드를 저장하기 위해 Azure Cosmos DBRepository 및 ItemController에 일부 코드를 추가하겠습니다.

1. 다음 메서드를 **DocumentDBRepository** 클래스에 추가합니다.
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   이 메서드는 단순히 전달된 개체를 받아서 Azure Cosmos DB에 저장합니다.
2. ItemController.cs 파일을 열고 클래스 내에 다음 코드 조각을 추가합니다. 이를 통해 ASP.NET MVC에서 **Create** 작업을 위해 수행할 작업을 인식할 수 있습니다. 이 경우 앞에서 만든 관련 Create.cshtml 뷰를 렌더링합니다.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    이제 **Create** 뷰의 제출을 수락하는 코드를 이 컨트롤러에 더 추가해야 합니다.
3. 이 컨트롤러에 대한 폼 POST의 처리 방법을 ASP.NET MVC에 알리는 다음 코드 블록을 ItemController.cs 클래스에 추가합니다.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    이 코드는 DocumentDBRepository를 호출하고 CreateItemAsync 메서드를 사용하여 새로운 todo 항목을 데이터베이스에 유지합니다. 
   
    **보안 정보**: **ValidateAntiForgeryToken** 특성은 여기서 교차 사이트 요청 위조 공격으로부터 이 애플리케이션을 보호하는 데 사용됩니다. 이 특성을 추가하는 것 외에 뷰가 이 위조 방지 토큰과 작동하도록 해야 합니다. 이 주제에 대한 자세한 내용과 이를 올바르게 구현하는 방법의 예는 [교차 사이트 요청 위조 방지(영문)][Preventing Cross-Site Request Forgery]를 참조하세요. [GitHub][GitHub]에서 제공하는 소스 코드에는 완벽하게 구현되어 있습니다.
   
    **보안 정보**: 또한 메서드 매개 변수에 **Bind** 특성을 사용하여 과도한 게시 공격으로부터 보호할 수 있습니다. 자세한 내용은 [ASP.NET MVC의 기본 CRUD 작업(영문)][Basic CRUD Operations in ASP.NET MVC]을 참조하세요.

데이터베이스에 새 항목을 추가하는 데 필요한 코드가 완성되었습니다.

### <a name="_Toc395637772"></a>항목 편집
마지막으로 수행할 작업은 데이터베이스에서 **항목** 을 편집하고 완료로 표시하는 기능입니다. 편집용 뷰는 이미 프로젝트에 추가되었으므로 다시 컨트롤러와 **DocumentDBRepository** 클래스에 일부 코드를 추가하기만 하면 됩니다.

1. **DocumentDBRepository** 클래스에 다음을 추가합니다.
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    이러한 메서드 중 첫 번째 메서드인 **GetItem**은 Azure Cosmos DB에서 항목을 가져오며, 이 항목이 다시 **ItemController** 및 **Edit** 뷰로 전달됩니다.
   
    방금 추가한 메서드 중 두 번째 메서드는 Cosmos DB의 **문서**를 **ItemController**에서 전달된 **문서** 버전으로 바꿉니다.
2. **ItemController** 클래스에 다음을 추가합니다.
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    첫 번째 메서드는 사용자가 **Index** 뷰에서 **편집** 링크를 클릭할 때 발생하는 Http Get을 처리합니다. 이 메서드는 Azure Cosmos DB에서 [**문서**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx)를 가져와 **Edit** 뷰에 전달합니다.
   
    그런 다음 **Edit** 뷰는 **IndexController**에 Http Post를 수행합니다. 
   
    추가한 두 번째 메서드는 데이터베이스에 저장되도록 업데이트된 개체를 Azure Cosmos DB에 전달하는 작업을 처리합니다.

애플리케이션을 실행하는 데 필요한 모든 작업(완료되지 않은 **항목** 나열, 새 **항목** 추가 및 **항목** 편집)이 완료되었습니다.

## <a name="_Toc395637773"></a>6단계: 로컬에서 애플리케이션 실행
로컬 컴퓨터에서 애플리케이션을 테스트하려면 다음을 수행합니다.

1. 디버그 모드에서 응용 프로그램을 빌드하려면 Visual Studio에서 F5 키를 누릅니다. 애플리케이션이 빌드되고 앞에서 본 것처럼 빈 그리드 페이지가 포함된 상태로 브라우저가 시작되어야 합니다.
   
    ![이 데이터베이스 자습서에서 만든 할 일 모음 웹 애플리케이션의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. **새로 만들기** 링크를 클릭하고 **이름** 및 **설명** 필드에 값을 추가합니다. **완료** 확인란을 선택 취소된 상태로 둡니다. 그렇지 않으면 새 **항목**이 완료 상태로 추가되며 초기 목록에 나타나지 않습니다.
   
    ![뷰 만들기의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. **만들기**를 클릭하면 **인덱스** 뷰로 다시 리디렉션되고 **항목**이 목록에 나타납니다.
   
    ![인덱스 뷰의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Todo 목록에 **항목** 을 더 추가해도 됩니다.
    
4. 목록에서 **항목** 옆의 **편집**을 클릭합니다. **편집** 뷰로 이동되며, 여기서 **완료** 플래그를 비롯한 개체 속성을 업데이트할 수 있습니다. **완료** 플래그를 표시하고 **저장**을 클릭하면 **항목**이 완료되지 않은 작업 목록에서 제거됩니다.
   
    ![완료 상자가 선택된 인덱스 뷰의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. 앱을 테스트하고 나면 Ctrl+F5를 눌러 앱 디버깅을 중지합니다. 배포할 준비가 되었습니다!

## <a name="_Toc395637774"></a>7단계: Azure App Service에 애플리케이션 배포 
이제 전체 애플리케이션이 Azure Cosmos DB와 올바르게 작동하므로 Azure App Service에 이 웹앱을 배포하겠습니다.  

1. 이 애플리케이션을 게시하기 위해 할 일은 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭하는 것뿐입니다.
   
    ![솔루션 탐색기 내 게시 옵션의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. **게시** 대화 상자에서 **Microsoft Azure App Service**를 클릭한 다음 **새로 만들기**를 선택하여 App Service 프로필을 만들거나 **기존 항목 선택**을 클릭하여 기존 프로필을 사용합니다.

    ![Visual Studio에서 대화 상자 게시](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. 기존 Azure App Service 프로필이 있는 경우 구독 이름을 입력합니다. **보기** 필터를 사용하여 리소스 그룹 또는 리소스 종류별로 정렬한 다음 Azure App Service를 선택합니다. 
   
    ![Visual Studio의 App Service 대화 상자](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. 새 Azure App Service 프로필을 만들려면 **게시** 대화 상자에서 **새로 만들기**를 클릭합니다. **앱 서비스 만들기** 대화 상자에서 웹앱 이름 및 적절한 구독, 리소스 그룹 및 App Service 계획을 입력하고 **만들기**를 클릭합니다.

    ![Visual Studio의 App Service 만들기 대화 상자](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

몇 초 후에 Visual Studio에서 웹 애플리케이션 게시를 완료하고 브라우저를 시작하며, Azure에서 실행되는 작업 내용을 확인할 수 있습니다.



## <a name="_Toc395637775"></a>다음 단계
축하합니다! 지금까지 Azure Cosmos DB를 사용하여 첫 ASP.NET MVC 웹 애플리케이션을 빌드하고 Azure에 게시했습니다. 이 자습서에 포함되지 않은 세부 정보 및 삭제 기능을 비롯한 전체 애플리케이션 소스 코드는 [GitHub][GitHub]에서 다운로드하거나 복제할 수 있습니다. 따라서 이 내용을 앱에 추가하려는 경우 코드를 끌어와서 이 앱에 추가하면 됩니다.

애플리케이션에 기능을 더 추가하려면 [Azure Cosmos DB .NET 라이브러리](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)에서 사용 가능한 API를 검토하고 [GitHub][GitHub]의 Azure Cosmos DB .NET 라이브러리에 자유롭게 기여하세요. 

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
