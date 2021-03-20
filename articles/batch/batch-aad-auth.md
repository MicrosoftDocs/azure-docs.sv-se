---
title: Autentisera Azure Batch tjänster med Azure Active Directory
description: Batch stöder Azure AD för autentisering från batch-tjänsten. Lär dig hur du autentiserar på ett av två sätt.
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 2ceefa538c44208750da8986fcf3d161f0c0865f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180028"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autentisera batch service-lösningar med Active Directory

Azure Batch stöder autentisering med [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Azure använder sig av Azure AD för att autentisera sina kunder, tjänst administratörer och organisations användare.

När du använder Azure AD-autentisering med Azure Batch kan du autentisera på ett av två sätt:

- Genom att använda **integrerad autentisering** för att autentisera en användare som interagerar med programmet. Ett program som använder integrerad autentisering samlar in en användares autentiseringsuppgifter och använder dessa autentiseringsuppgifter för att autentisera åtkomsten till batch-resurser.
- Genom att använda ett **huvud namn för tjänsten** för att autentisera ett obevakat program. Ett huvud namn för tjänsten definierar principen och behörigheterna för ett program för att representera programmet vid åtkomst till resurser vid körning.

Mer information om Azure AD finns i Azure Active Directory- [dokumentationen](../active-directory/index.yml).

## <a name="endpoints-for-authentication"></a>Slut punkter för autentisering

Om du vill autentisera batch-program med Azure AD måste du ta med några välkända slut punkter i din kod.

### <a name="azure-ad-endpoint"></a>Azure AD-slutpunkt

Bas-slut punkten för Azure AD-auktoritet är:

`https://login.microsoftonline.com/`

Om du vill autentisera med Azure AD använder du den här slut punkten tillsammans med klient-ID: t (katalog-ID). Klient-ID: t identifierar den Azure AD-klient som ska användas för autentisering. Hämta klient-ID: t genom att följa stegen som beskrivs i [Hämta klient-ID för din Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> Den klient-/regionsspecifika slut punkten krävs när du autentiserar med ett huvud namn för tjänsten.
>
> Den klient-/regionsspecifika slut punkten är valfri när du autentiserar med integrerad autentisering, men rekommenderas. Du kan dock också använda den vanliga Azure AD-slutpunkten. Den gemensamma slut punkten ger ett allmänt gränssnitt för insamling av autentiseringsuppgifter när en enskild klient inte har angetts. Den gemensamma slut punkten är `https://login.microsoftonline.com/common` .

Mer information om Azure AD-slutpunkter finns i [autentisering kontra auktorisering](../active-directory/develop/authentication-vs-authorization.md).

### <a name="batch-resource-endpoint"></a>Slut punkt för batch-resurs

Använd **Azure Batch resurs slut punkt** för att hämta en token för att autentisera begär anden till batch-tjänsten:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrera ditt program med en klient

Det första steget i att använda Azure AD för att autentisera är att registrera ditt program i en Azure AD-klient. Genom att registrera ditt program kan du anropa Azure- [Active Directory-autentiseringsbibliotek](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) från din kod. ADAL tillhandahåller ett API för autentisering med Azure AD från ditt program. Registrering av ditt program krävs om du planerar att använda integrerad autentisering eller ett huvud namn för tjänsten.

När du registrerar ditt program anger du information om ditt program till Azure AD. Azure AD tillhandahåller sedan ett program-ID (även kallat ett *klient-ID*) som du använder för att associera ditt program med Azure AD vid körning. Mer information om program-ID finns [i program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Registrera ditt batch-program genom att följa stegen i avsnittet **Registrera ett program** i [snabb start: registrera ett program med Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md). Om du registrerar ditt program som ett internt program kan du ange en giltig URI för **omdirigerings-URI: n**. Det behöver inte vara en riktig slut punkt.

När du har registrerat ditt program visas program-ID: t:

![Skärm bild av det program-ID som visas i Azure Portal.](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>Hämta klient-ID: t för din Active Directory

Klient-ID: t identifierar den Azure AD-klient som tillhandahåller Authentication Services för ditt program. Följ dessa steg för att hämta klient-ID: t:

1. I Azure Portal väljer du Active Directory.
1. Välj **Egenskaper**.
1. Kopiera GUID-värdet som har angetts för **katalog-ID: t**. Detta värde kallas även klient-ID.

![Skärm bild av katalog-ID: t i Azure Portal.](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Använd integrerad autentisering

Om du vill autentisera med integrerad autentisering måste du ge dina program behörigheter för att kunna ansluta till batch-tjänstens API. Det här steget gör att ditt program kan autentisera anrop till batch-tjänstens API med Azure AD.

När du har registrerat ditt program följer du de här stegen i Azure Portal för att ge åtkomst till batch-tjänsten:

1. I Azure Portal väljer du **alla tjänster** och väljer sedan **app-registreringar**.
1. Sök efter namnet på ditt program i listan med app-registreringar.
1. Välj programmet och välj **API-behörigheter**.
1. I avsnittet **API-behörigheter** väljer du **Lägg till en behörighet**.
1. I **Välj ett API** söker du efter batch-API: et. Sök efter var och en av de här strängarna tills du hittar API:t:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** är id:t för API:t.
1. När du har hittat batch-API: t väljer du det och väljer sedan **Välj**.
1. Markera kryss rutan bredvid **åtkomst Azure Batch tjänst** i **Välj behörigheter** och välj sedan **Lägg till behörigheter**.

Avsnittet **API-behörigheter** visar nu att Azure AD-programmet har åtkomst till både Microsoft Graph och batch-tjänstens API. Behörigheter beviljas till Microsoft Graph automatiskt när du först registrerar din app med Azure AD.

## <a name="use-a-service-principal"></a>Använd ett huvud namn för tjänsten

Om du vill autentisera ett program som körs obevakat använder du ett huvud namn för tjänsten. När du har registrerat ditt program följer du dessa steg i Azure Portal för att konfigurera ett huvud namn för tjänsten:

1. Begär en hemlighet för ditt program.
1. Tilldela Azure-rollbaserad åtkomst kontroll (Azure RBAC) till ditt program.

### <a name="request-a-secret-for-your-application"></a>Begär en hemlighet för ditt program

När ditt program autentiserar med ett huvud namn för tjänsten, skickar det både program-ID och en hemlighet till Azure AD. Du måste skapa och kopiera den hemliga nyckeln för att använda från din kod.

Följ dessa steg i Azure-portalen:

1. I Azure Portal väljer du **alla tjänster**. Välj **app-registreringar**.
1. Välj ditt program i listan med app-registreringar.
1. Välj programmet och välj sedan **certifikat & hemligheter**. I avsnittet **klient hemligheter** väljer du **ny klient hemlighet**.
1. Ange en beskrivning av hemligheten om du vill skapa en hemlighet. Välj sedan ett förfallo datum för hemligheten för ett år, två år eller ingen utgång.
1. Välj **Lägg till** för att skapa och Visa hemligheten. Kopiera det hemliga värdet till en säker plats, eftersom du inte kan komma åt det igen när du har lämnat sidan.

### <a name="assign-azure-rbac-to-your-application"></a>Tilldela Azure RBAC till ditt program

Om du vill autentisera med ett huvud namn för tjänsten måste du tilldela Azure RBAC till ditt program. Följ de här stegen:

1. I Azure Portal navigerar du till det batch-konto som används av ditt program.
1. I avsnittet **Inställningar** i batch-kontot väljer du **Access Control (IAM)**.
1. Välj fliken **roll tilldelningar** .
1. Välj **Lägg till rolltilldelning**.
1. I list rutan **roll** väljer du rollen *deltagare* eller *läsare* för ditt program. Mer information om de här rollerna finns i [komma igång med Azure rollbaserad åtkomst kontroll i Azure Portal](../role-based-access-control/overview.md).
1. Ange namnet på ditt program i fältet **Välj** . Välj ditt program i listan och välj sedan **Spara**.

Ditt program bör nu visas i inställningarna för åtkomst kontroll med en Azure-roll tilldelad.

![Tilldela en Azure-roll till ditt program](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Tilldela en anpassad roll

En anpassad roll ger detaljerad behörighet till en användare för att skicka jobb, uppgifter med mera. Detta ger möjlighet att förhindra att användare utför åtgärder som påverkar kostnader, till exempel skapa pooler eller ändra noder.

Du kan använda en anpassad roll för att bevilja behörighet till en Azure AD-användare, en grupp eller ett tjänst huvud namn för följande Azure RBAC-åtgärder:

- Microsoft.BatCH/batchAccounts/pool/Write
- Microsoft.BatCH/batchAccounts/pooler/ta bort
- Microsoft.BatCH/batchAccounts/pooler/läsa
- Microsoft.BatCH/batchAccounts/jobSchedules/Write
- Microsoft.BatCH/batchAccounts/jobSchedules/Delete
- Microsoft.BatCH/batchAccounts/jobSchedules/Read
- Microsoft.BatCH/batchAccounts/Jobs/Write
- Microsoft.BatCH/batchAccounts/Jobs/Delete
- Microsoft.BatCH/batchAccounts/Jobs/Read
- Microsoft.BatCH/batchAccounts/certificates/Write
- Microsoft.BatCH/batchAccounts/certificates/Delete
- Microsoft.BatCH/batchAccounts/certifikat/läsa
- Microsoft.BatCH/batchAccounts/Read (för alla Läs åtgärder)
- Microsoft.BatCH/batchAccounts/Listnycklar/Action (för alla åtgärder)

Anpassade roller är för användare som autentiseras av Azure AD, inte för batch-kontots autentiseringsuppgifter (delad nyckel). Observera att autentiseringsuppgifterna för batch-kontot ger fullständig behörighet till batch-kontot. Observera också att jobb som använder [autopoolen](nodes-and-pools.md#autopools) kräver behörigheter på grupp nivå.

> [!NOTE]
> Vissa roll tilldelningar måste anges i fältet åtgärd, medan andra måste anges i fältet DataAction. Mer information finns i [Azure Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftbatch).

Här är ett exempel på en anpassad roll definition:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Mer information om hur du skapar en anpassad roll finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Hämta klient-ID: t för din Azure Active Directory

Klient-ID: t identifierar den Azure AD-klient som tillhandahåller Authentication Services för ditt program. Följ dessa steg för att hämta klient-ID: t:

1. I Azure Portal väljer du Active Directory.
1. Välj **Egenskaper**.
1. Kopiera GUID-värdet som har angetts för **katalog-ID: t**. Detta värde kallas även klient-ID.

![Kopiera katalog-ID](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Kodexempel

I kod exemplen i det här avsnittet visas hur du autentiserar med Azure AD med integrerad autentisering och med ett huvud namn för tjänsten. De flesta av dessa kod exempel använder .NET, men begreppen liknar andra språk.

> [!NOTE]
> En token för Azure AD-autentisering upphör att gälla efter en timme. När du använder ett **metoden batchclient** -objekt med lång livs längd rekommenderar vi att du hämtar en token från ADAL på varje begäran för att säkerställa att du alltid har en giltig token.
>
> För att uppnå detta i .NET, skriver du en metod som hämtar token från Azure AD och skickar den metoden till ett **BatchTokenCredentials** -objekt som ett ombud. Metoden delegate anropas på varje begäran till batch-tjänsten för att säkerställa att en giltig token har angetts. Som standard cachelagrar ADAL-token, så en ny token hämtas endast från Azure AD vid behov. Mer information om tokens i Azure AD finns i [säkerhetstoken](../active-directory/develop/security-tokens.md).

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Kod exempel: använda Azure AD-integrerad autentisering med batch .NET

Om du vill autentisera med integrerad autentisering från batch .NET refererar du till [Azure Batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) -paketet och [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -paketet.

Inkludera följande `using` instruktioner i koden:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referera till Azure AD-slutpunkten i din kod, inklusive klient-ID. Hämta klient-ID: t genom att följa stegen som beskrivs i [Hämta klient-ID för din Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referera till resurs slut punkten för batch-tjänsten:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referera till ditt batch-konto:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID: t är tillgängligt från din app-registrering i Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Kopiera också den omdirigerings-URI som du har angett, om du har registrerat programmet som ett internt program. Den omdirigerings-URI som anges i din kod måste matcha den omdirigerings-URI som du angav när du registrerade programmet:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Skriv en callback-metod för att hämta autentiseringstoken från Azure AD. Återkallnings metoden **GetAuthenticationTokenAsync** som visas här anropar ADAL för att autentisera en användare som interagerar med programmet. **AcquireTokenAsync** -metoden som tillhandahålls av ADAL uppmanas användaren att ange sina autentiseringsuppgifter och programmet fortsätter när användaren tillhandahåller dem (om den inte redan har cachelagrade autentiseringsuppgifter):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Skapa ett **BatchTokenCredentials** -objekt som tar ombudet som en parameter. Använd dessa autentiseringsuppgifter för att öppna ett **metoden batchclient** -objekt. Du kan använda det **metoden batchclient** -objektet för efterföljande åtgärder mot batch-tjänsten:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Kod exempel: använda ett Azure AD-tjänstens huvud namn med batch .NET

Om du vill autentisera med ett huvud namn för tjänsten från batch .NET refererar du till [Azure Batch .net](https://www.nuget.org/packages/Azure.Batch/) -paketet och [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -paketet.

Inkludera följande `using` instruktioner i koden:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referera till Azure AD-slutpunkten i din kod, inklusive klient-ID. När du använder ett huvud namn för tjänsten måste du ange en klient-/regionsspecifika slut punkt. Hämta klient-ID: t genom att följa stegen som beskrivs i [Hämta klient-ID för din Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referera till resurs slut punkten för batch-tjänsten:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referera till ditt batch-konto:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Ange program-ID (klient-ID) för ditt program. Program-ID: t är tillgängligt från din app-registrering i Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Ange den hemliga nyckel som du kopierade från Azure Portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Skriv en callback-metod för att hämta autentiseringstoken från Azure AD. Återkallnings metoden **GetAuthenticationTokenAsync** som visas här anropar ADAL för oövervakad autentisering:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Skapa ett **BatchTokenCredentials** -objekt som tar ombudet som en parameter. Använd dessa autentiseringsuppgifter för att öppna ett **metoden batchclient** -objekt. Använd sedan det **metoden batchclient** -objektet för efterföljande åtgärder mot batch-tjänsten:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Kod exempel: använda ett Azure AD-tjänstens huvud namn med batch python

Om du vill autentisera med ett huvud namn för tjänsten från batch python installerar du och refererar till modulerna [Azure-Batch](https://pypi.org/project/azure-batch/) och [Azure-common](https://pypi.org/project/azure-common/) .

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

När du använder ett huvud namn för tjänsten måste du ange klient-ID: t. Hämta klient-ID: t genom att följa stegen som beskrivs i [Hämta klient-ID för din Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Referera till resurs slut punkten för batch-tjänsten:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Referera till ditt batch-konto:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Ange program-ID (klient-ID) för ditt program. Program-ID: t är tillgängligt från din app-registrering i Azure Portal:

```python
CLIENT_ID = "<application-id>"
```

Ange den hemliga nyckel som du kopierade från Azure Portal:

```python
SECRET = "<secret-key>"
```

Skapa ett **ServicePrincipalCredentials** -objekt:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Använd autentiseringsuppgifterna för tjänstens huvud namn för att öppna ett **BatchServiceClient** -objekt. Använd sedan det **BatchServiceClient** -objektet för efterföljande åtgärder mot batch-tjänsten.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Nästa steg

- Läs [Azure Active Directory-dokumentationen](../active-directory/index.yml). Djupgående exempel som visar hur du använder ADAL finns i [Azure kod exempel](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteket.
- Lär dig mer om [program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) och [hur du skapar ett Azure AD-program och tjänstens huvud namn som har åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md).
- Lär dig mer om att [autentisera lösningar för batch-hantering med Active Directory](batch-aad-auth-management.md).
- Ett python-exempel på hur du skapar en batch-klient autentiserad med hjälp av en Azure AD-token finns i avsnittet [distribuera Azure Batch anpassad avbildning med ett Python-skript](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) exempel.

