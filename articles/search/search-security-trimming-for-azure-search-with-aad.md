---
title: Säkerhets filter för att trimma resultat med Active Directory
titleSuffix: Azure Cognitive Search
description: Lär dig hur du implementerar säkerhets privilegier på dokument nivå för Azure Kognitiv sökning Sök resultat, med säkerhets filter och Azure Active Directory (AD) identiteter.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629518"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Säkerhets filter för att trimma Azure-Kognitiv sökning resultat med hjälp av Active Directory identiteter

Den här artikeln visar hur du använder Azure Active Directory (AD) säkerhets identiteter tillsammans med filter i Azure Kognitiv sökning för att trimma Sök resultat baserat på användar grupp medlemskap.

Den här artikeln beskriver följande uppgifter:
> [!div class="checklist"]
> - Skapa Azure AD-grupper och användare
> - Koppla användaren till den grupp som du har skapat
> - Cachelagra de nya grupperna
> - Indexera dokument med tillhör ande grupper
> - Utfärda en Sök förfrågan med ett filter för grupp identifierare
> 
> [!NOTE]
> Exempel kod avsnitt i den här artikeln är skrivna i C#. Du hittar den fullständiga källkoden [på GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Förutsättningar

Ditt index i Azure Kognitiv sökning måste ha ett [säkerhets fält](search-security-trimming-for-azure-search.md) för att lagra listan över grupp identiteter som har Läs behörighet till dokumentet. Det här användnings fallet förutsätter en en-till-en-korrespondens mellan ett skydds Bart objekt (till exempel en enskild persons skolprogram) och ett säkerhets fält som anger vem som har åtkomst till det objektet (anställnings personal).

Du måste ha administratörs behörighet för Azure AD, som krävs i den här genom gången för att skapa användare, grupper och associationer. 

Ditt program måste också vara registrerat med Azure AD som en app för flera klienter, enligt beskrivningen i följande procedur.

### <a name="register-your-application-with-azure-active-directory"></a>Registrera ditt program med Azure Active Directory

I det här steget integreras ditt program med Azure AD i syfte att godkänna inloggningar av användar-och grupp konton. Om du inte är klient organisations administratör i din organisation kan du behöva [skapa en ny klient](../active-directory/develop/quickstart-create-new-tenant.md) för att utföra följande steg.

1. I [Azure Portal](https://portal.azure.com)letar du upp Azure Active Directory resurs för din prenumeration.

1. Till vänster, under **Hantera**, Välj **Appregistreringar** och välj sedan **ny registrering**.

1. Ge registreringen ett namn, kanske ett namn som liknar Sök programmets namn. Välj **Register** (Registrera).

1. Kopiera program-ID: t när appens registrering har skapats. Du måste ange den här strängen för ditt program.

   Om du går igenom [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)klistrar du in det här värdet i **app.config** -filen.

   Upprepa för klient-ID.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Program-ID i avsnittet Essentials":::

1. Välj **API-behörigheter** till vänster och välj sedan **Lägg till en behörighet**. 

1. Välj **Microsoft Graph** och välj **delegerade behörigheter**.

1. Sök efter och Lägg sedan till följande delegerade behörigheter:

   - **Directory. ReadWrite. all**
   - **Group.ReadWrite.All**
   - **User. ReadWrite. all**

Microsoft Graph tillhandahåller ett API som ger programmatisk åtkomst till Azure AD via en REST API. Kod exemplet för den här genom gången använder behörigheterna för att anropa Microsoft Graph-API: et för att skapa grupper, användare och associationer. API: erna används också för att cachelagra grupp identifierare för snabbare filtrering.

## <a name="create-users-and-groups"></a>Skapa användare och grupper

Om du lägger till en sökning i ett etablerat program kan du ha befintliga användar-och grupp identifierare i Azure AD. I så fall kan du hoppa över nästa tre steg. 

Men om du inte har befintliga användare kan du använda Microsoft Graph-API: er för att skapa säkerhets objekt. Följande kodfragment visar hur du genererar identifierare, som blir datavärdena för säkerhets fältet i Azure Kognitiv sökning indexet. I vårt hypotetiska universitets ansöknings program är detta säkerhets identifierarna för anställda.

Användar-och grupp medlemskap kan vara mycket flytande, särskilt i stora organisationer. Kod som skapar användar-och grupp identiteter bör köras tillräckligt ofta för att hämta ändringar i organisationens medlemskap. På samma sätt kräver ditt Azure Kognitiv sökning-index ett liknande uppdaterings schema för att återspegla den aktuella statusen för tillåtna användare och resurser.

### <a name="step-1-create-group"></a>Steg 1: [Skapa grupp](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>Steg 2: [skapa användare](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>Steg 3: koppla användare och grupp

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>Steg 4: cachelagra grupp-ID: n

Om du vill minska nätverks fördröjningen kan du cachelagra användar grupps kopplingarna så att när en Sök förfrågan utfärdas, returneras grupper från cachen och sparar en tur och retur till Azure AD. Du kan använda [Azure AD batch API](/graph/json-batching) för att skicka en enda http-begäran med flera användare och bygga cachen.

Microsoft Graph har utformats för att hantera en stor mängd begär Anden. Om det uppstår ett överbelastat antal begär Anden, Miss lyckas begäran med HTTP-statuskod 429 i Microsoft Graph. Mer information finns i [Microsoft Graph begränsning](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indexera dokument med deras tillåtna grupper

Fråga-åtgärder i Azure-Kognitiv sökning körs över ett Azure Kognitiv sökning-index. I det här steget importerar en indexerings åtgärd sökbara data till ett index, inklusive de identifierare som används som säkerhets filter. 

Azure Kognitiv sökning autentiserar inte användar identiteter eller ger logik för att fastställa vilket innehåll en användare har behörighet att visa. Användnings fallet för säkerhets trimning förutsätter att du anger associationen mellan ett känsligt dokument och grupp-ID: t som har åtkomst till dokumentet, som importeras intakt i ett sökindex. 

I det hypotetiska exemplet skulle bröd texten i begäran i ett Azure Kognitiv sökning-index innehålla en sökandes skol uppsats eller avskrift tillsammans med grupp-ID: t som har behörighet att visa innehållet. 

I det allmänna exemplet som används i kod exemplet för den här genom gången kan index åtgärden se ut så här:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Utfärda en Sök förfrågan

För säkerhets putsning är värdena i säkerhets fältet i indexet statiska värden som används för att inkludera eller exkludera dokument i Sök resultaten. Om grupp identifieraren för-inhämtare till exempel är "A11B22C33D44-E55F66G77-H88I99JKK" inkluderas alla dokument i ett Azure Kognitiv sökning-index som har identifieraren i den arkiverade säkerhets kopian (eller exkluderas) i Sök resultaten som skickas tillbaka till begär Anden.

Granska följande steg för att filtrera dokument som returneras i Sök resultat baserat på grupper av användaren som utfärdade begäran.

### <a name="step-1-retrieve-users-group-identifiers"></a>Steg 1: Hämta användarens grupp identifierare

Om användarens grupper inte redan har cachelagrats, eller om cachen har upphört att gälla, utfärdar du [grupp](/graph/api/directoryobject-getmembergroups) förfrågan.

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>Steg 2: Skriv Sök förfrågan

Förutsatt att du har användarens grupp medlemskap kan du utfärda Sök förfrågan med lämpliga filter värden.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>Steg 3: hantera resultaten

Svaret innehåller en filtrerad lista med dokument, som består av de som användaren har behörighet att visa. Beroende på hur du skapar Sök Resultat sidan kanske du vill inkludera visuella tips som visar den filtrerade resultat uppsättningen.

## <a name="next-steps"></a>Nästa steg

I den här genom gången har du lärt dig ett mönster för att använda Azure AD-inloggningar för att filtrera dokument i Azure Kognitiv sökning resultat och trimma resultatet av dokument som inte matchar filtret som anges i begäran. För ett alternativt mönster som kan vara enklare, eller för att gå tillbaka till andra säkerhetsfunktioner, se följande länkar.

- [Säkerhets filter för trimning av resultat](search-security-trimming-for-azure-search.md)
- [Säkerhet i Azure Kognitiv sökning](search-security-overview.md)