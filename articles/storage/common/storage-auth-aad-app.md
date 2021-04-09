---
title: Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program
titleSuffix: Azure Storage
description: Använd Azure Active Directory för att autentisera inifrån ett klient program, hämta en OAuth 2,0-token och auktorisera begär anden till Azure Blob Storage och Queue Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: baad1a0b173ae89fec9d160572224c6cb0aa615d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574636"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program

En viktig fördel med att använda Azure Active Directory (Azure AD) med Azure Blob Storage eller Queue Storage är att dina autentiseringsuppgifter inte längre behöver lagras i din kod. I stället kan du begära en OAuth 2,0-åtkomsttoken från Microsoft Identity Platform. Azure AD autentiserar säkerhets objekt (en användare, grupp eller tjänstens huvud namn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begär anden till Azure Blob Storage eller Queue Storage.

Den här artikeln visar hur du konfigurerar ditt egna program eller webb program för autentisering med Microsoft Identity Platform med ett exempel program som kan laddas ned. Exempel programmet har funktioner för .NET, men andra språk använder samma metod. Mer information om Microsoft Identity Platform finns i [Översikt över Microsoft Identity Platform](../../active-directory/develop/v2-overview.md).

En översikt över OAuth 2,0 Code Granting Flow finns i bevilja [åtkomst till Azure Active Directory webb program med hjälp av OAuth-kod för kod beviljande i 2,0 OAuth](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="about-the-sample-application"></a>Om exempel programmet

Exempel programmet ger en komplett upplevelse som visar hur du konfigurerar ett webb program för autentisering med Azure AD i en lokal utvecklings miljö. Om du vill visa och köra exempel programmet ska du först klona eller ladda ned det från [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Följ sedan stegen som beskrivs i artikeln för att konfigurera en Azure App-registrering och uppdatera programmet för din miljö.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Tilldela en roll till ett säkerhets objekt i Azure AD

Om du vill autentisera ett säkerhets objekt från ditt Azure Storage-program måste du först konfigurera inställningar för Azure-rollbaserad åtkomst kontroll (Azure RBAC) för säkerhets objekt. Azure Storage definierar inbyggda roller som omfattar behörigheter för behållare och köer. När Azure-rollen tilldelas till ett säkerhets objekt beviljas detta säkerhets objekt åtkomst till resursen. Mer information finns i [Hantera åtkomst behörigheter till Azure blob och Queue data med Azure RBAC](./storage-auth-aad-rbac-portal.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program med en Azure AD-klient

Det första steget i att använda Azure AD för att ge åtkomst till lagrings resurser är att registrera klient programmet med en Azure AD-klient från [Azure Portal](https://portal.azure.com). När du registrerar klient programmet anger du information om programmet till Azure AD. Azure AD tillhandahåller sedan ett klient-ID (kallas även ett *program-ID*) som du använder för att associera ditt program med Azure AD vid körning. Mer information om klient-ID finns i [program-och tjänst huvud objekt i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). Registrera ditt Azure Storage program genom att följa stegen i [snabb start: registrera ett program med Microsoft Identity Platform](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). 

Följande bild visar vanliga inställningar för att registrera ett webb program. Observera att i det här exemplet är omdirigerings-URI: n inställd på `http://localhost:5000/signin-oidc` för att testa exempel programmet i utvecklings miljön. Du kan ändra den här inställningen senare under **autentiseringsinställningarna** för ditt registrerade program i Azure Portal:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Skärm bild som visar hur du registrerar ditt lagrings program med Azure AD":::

> [!NOTE]
> Om du registrerar ditt program som ett internt program kan du ange en giltig URI för **omdirigerings-URI: n**. För interna program behöver det här värdet inte vara en riktig URL. För webb program måste omdirigerings-URI: n vara en giltig URI, eftersom den anger den URL till vilken tokens anges.

När du har registrerat ditt program visas program-ID: t (eller klient-ID) under **Inställningar**:

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="Skärm bild som visar klient-ID":::

Mer information om hur du registrerar ett program med Azure AD finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-register-app.md).

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Ge dina registrerade program behörigheter till Azure Storage

Ge sedan dina program behörigheter för att anropa Azure Storage API: er. Det här steget gör att ditt program kan auktorisera begär anden till Azure Storage med Azure AD.

1. På sidan **API-behörigheter** för ditt registrerade program väljer du **Lägg till en behörighet**.
1. Under fliken **Microsoft API: er** väljer du **Azure Storage**.
1. I rutan **begär API-behörigheter** under **vilken typ av behörighet kräver ditt program?**, Observera att den tillgängliga behörighets typen är **delegerad behörighet**. Det här alternativet är valt som standard.
1. Under **behörigheter** markerar du kryss rutan bredvid **user_impersonation** och väljer sedan knappen **Lägg till behörigheter** .

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="Skärm bild som visar behörigheter för Storage API":::

1. Ge sedan administratörs medgivande för dessa behörigheter genom att klicka på **bevilja administrativt medgivande för standard katalogen**.

Fönstret **API-behörigheter** visar nu att ditt registrerade Azure AD-program har åtkomst till både Microsoft Graph-och Azure Storage-API: er och att detta medgivande beviljas för standard katalogen. Behörigheter beviljas till Microsoft Graph automatiskt när du först registrerar din app med Azure AD.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="Skärm bild som visar API-behörigheter för registrerad app":::

### <a name="create-a-client-secret"></a>Skapa en klient hemlighet

Programmet behöver en klient hemlighet för att bevisa sin identitet när en token begärs. Följ dessa steg om du vill lägga till klient hemligheten:

1. Navigera till din app-registrering i Azure Portal.
1. Välj inställningen för **certifikat & hemligheter** .
1. Under **klient hemligheter** klickar du på **ny klient hemlighet** för att skapa en ny hemlighet.
1. Ange en beskrivning av hemligheten och välj önskat giltighets intervall.
1. Kopiera omedelbart värdet för den nya hemligheten till en säker plats. Det fullständiga värdet visas bara en gång.

    ![Skärm bild som visar klient hemlighet](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Aktivera implicit beviljande av flöde

Konfigurera sedan det implicita tilldelnings flödet för ditt program. Följ de här stegen:

1. Navigera till din app-registrering i Azure Portal.
1. I avsnittet **Hantera** väljer du **autentiserings** inställningen.
1. I avsnittet **implicit bidrag** markerar du kryss rutan för att aktivera ID-tokens, som du ser i följande bild:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Skärm bild som visar hur du aktiverar inställningar för implicit beviljande av flöde":::

## <a name="client-libraries-for-token-acquisition"></a>Klient bibliotek för hämtning av token

När du har registrerat ditt program och beviljat det behörighet att komma åt data i Azure Blob Storage eller Queue Storage kan du lägga till kod i programmet för att autentisera ett säkerhets objekt och hämta en OAuth 2,0-token. Om du vill autentisera och hämta token kan du använda antingen en av [Microsofts identitets plattforms bibliotek](../../active-directory/develop/reference-v2-libraries.md) eller ett annat bibliotek med öppen källkod som stöder OpenID Connect 1,0. Ditt program kan sedan använda åtkomsttoken för att auktorisera en begäran mot Azure Blob Storage eller Queue Storage.

En lista över scenarier där du kan hämta token finns i avsnittet om [autentiserings flöden](../../active-directory/develop/msal-authentication-flows.md) i dokumentationen för [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) .

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Välkända värden för autentisering med Azure AD

Om du vill autentisera ett säkerhets objekt med Azure AD måste du ta med några välkända värden i koden.

### <a name="azure-ad-authority"></a>Azure AD-auktoritet

För Microsofts offentliga moln är bas-Azure AD-utfärdaren följande, där *klient-ID* är ditt Active Directory klient-ID (eller katalog-ID):

`https://login.microsoftonline.com/<tenant-id>/`

Klient-ID: t identifierar den Azure AD-klient som ska användas för autentisering. Det kallas även för katalog-ID. Hämta klient-ID: t genom att gå till sidan **Översikt** för din app-registrering i Azure Portal och kopiera värdet därifrån.

### <a name="azure-storage-resource-id"></a>Resurs-ID för Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kod exempel: skapa en Block-Blob

I kod exemplet visas hur du hämtar en åtkomsttoken från Azure AD. Åtkomsttoken används för att autentisera den angivna användaren och sedan auktorisera en begäran om att skapa en Block-Blob. Följ de steg som beskrivs i föregående avsnitt för att få det här exempel arbetet.

För att du ska kunna begära token behöver du följande värden från appens registrering:

- Namnet på din Azure AD-domän. Hämta det här värdet från **översikts** sidan för Azure Active Directory.
- Klient organisations-eller katalog-ID: t. Hämta det här värdet från **översikts** sidan för din app-registrering.
- Klient-eller program-ID: t. Hämta det här värdet från **översikts** sidan för din app-registrering.
- URI för omdirigering av klient. Hämta det här värdet från **autentiseringsinställningarna** för din app-registrering.
- Värdet för klient hemligheten. Hämta det här värdet från den plats dit du kopierade det tidigare.

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagrings konto och en behållare

Om du vill köra kod exemplet skapar du ett lagrings konto i samma prenumeration som din Azure Active Directory. Skapa sedan en behållare inom det lagrings kontot. Exempel koden skapar en block-BLOB i den här behållaren.

Nu ska du uttryckligen tilldela rollen **Storage BLOB data Contributor** till det användar konto som du vill köra exempel koden för. Instruktioner för hur du tilldelar den här rollen i Azure Portal finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> När du skapar ett Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data via Azure AD. Du måste uttryckligen tilldela dig själv en Azure-roll för Azure Storage. Du kan tilldela den på nivån för din prenumeration, resurs grupp, lagrings konto eller behållare eller kö.
>
> Innan du tilldelar dig själv en roll för data åtkomst kommer du att kunna komma åt data i ditt lagrings konto via Azure Portal eftersom Azure Portal även kan använda konto nyckeln för data åtkomst. Mer information finns i [Välj hur du godkänner åtkomst till BLOB-data i Azure Portal](../blobs/authorize-data-operations-portal.md).

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Skapa ett webb program som tillåter åtkomst till Blob Storage med Azure AD

När ditt program får åtkomst till Azure Storage sker detta å användarens vägnar, vilket innebär att BLOB-eller Queue-resurserna kan nås med behörigheterna för den användare som är inloggad. För att testa det här kod exemplet behöver du ett webb program som kräver att användaren loggar in med en Azure AD-identitet. Du kan skapa egna eller använda det exempel program som tillhandahålls av Microsoft.

Ett slutfört exempel webb program som hämtar en token och använder den för att skapa en BLOB i Azure Storage finns på [GitHub](https://aka.ms/aadstorage). Att granska och köra det färdiga exemplet kan vara användbart för att förstå kod exemplen. Instruktioner för hur du kör det färdiga exemplet finns i avsnittet med rubriken [Visa och köra det färdiga exemplet](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Lägg till referenser och using-instruktioner  

Installera klient biblioteket för Azure Storage från Visual Studio. Öppna menyn **Verktyg**. Välj **NuGet-pakethanterare** och sedan **Package Manager-konsolen**. Skriv följande kommandon i konsol fönstret för att installera de nödvändiga paketen från Azure Storage-klient biblioteket för .NET:

# <a name="net-v12-sdk"></a>[.NET V12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Lägg sedan till följande using-instruktioner till filen HomeController. CS:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET V11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Lägg sedan till följande using-instruktioner till filen HomeController. CS:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Skapa en Block-Blob

Lägg till följande kodfragment för att skapa en Block-Blob. Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

# <a name="net-v12-sdk"></a>[.NET V12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET V11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> Om du vill auktorisera blob-och Queue-åtgärder med en OAuth 2,0-token måste du använda HTTPS.

I exemplet ovan hanterar .NET-klient biblioteket auktoriseringen av begäran om att skapa block-bloben. Azure Storage klient bibliotek för andra språk hanterar även auktoriseringen av begäran åt dig. Men om du anropar en Azure Storage-åtgärd med en OAuth-token med hjälp av REST API måste du skapa ett **Authorization** -huvud med hjälp av OAuth-token.

Om du vill anropa blob-och Kötjänst-åtgärder med OAuth-åtkomsttoken skickar du åtkomsttoken i **Authorization** -huvudet med hjälp av **Bearer** -schemat och anger en tjänst version på 2017-11-09 eller högre, som visas i följande exempel:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Hämta en åtkomsttoken från Azure AD

Lägg sedan till en metod som begär en token från Azure AD för användarens räkning. Den här metoden definierar omfattningen för vilka behörigheter ska beviljas. Mer information om behörigheter och omfattningar finns [i behörigheter och medgivande i slut punkten för Microsoft Identity Platform](../../active-directory/develop/v2-permissions-and-consent.md).

Använd resurs-ID för att konstruera det omfång som du vill hämta token för. Exemplet konstruerar omfånget med hjälp av resurs-ID: t tillsammans med det inbyggda `user_impersonation` omfånget, vilket indikerar att token begärs för användarens räkning.

Tänk på att du kan behöva presentera användaren med ett gränssnitt som gör det möjligt för användaren att begära token för deras räkning:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Samtycke är en användare som ger behörighet till ett program för att få åtkomst till skyddade resurser för deras räkning. Microsoft Identity Platform stöder ett stegvist godkännande, vilket innebär att ett säkerhets objekt kan begära en lägsta uppsättning behörigheter initialt och lägga till behörigheter över tid efter behov. När din kod begär en åtkomsttoken anger du omfattningen av de behörigheter som appen behöver. Mer information om stegvisa medgivande finns i [stegvisa och dynamiska medgivande](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

## <a name="view-and-run-the-completed-sample"></a>Visa och köra det färdiga exemplet

För att köra exempel programmet ska du först klona eller ladda ned det från [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Uppdatera sedan programmet enligt beskrivningen i följande avsnitt.

### <a name="provide-values-in-the-settings-file"></a>Ange värden i inställnings filen

Uppdatera *appsettings.jspå* filen med dina egna värden enligt följande:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>",
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Uppdatera lagrings kontot och behållar namnet

I filen *HomeController. cs* uppdaterar du URI: n som refererar till block-bloben för att använda namnet på ditt lagrings konto och din behållare, och ersätter värdena i vinkelparenteser med dina egna värden:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Nästa steg

- [Microsoft identitetsplattform](../../active-directory/develop/index.yml)
- [Hantera åtkomst behörigheter till lagrings data med Azure RBAC](./storage-auth-aad-rbac-portal.md)
- [Autentisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md)
