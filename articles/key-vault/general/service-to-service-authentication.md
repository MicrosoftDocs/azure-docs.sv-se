---
title: Tjänst-till-tjänst-autentisering för Azure Key Vault med .NET
description: Använd Microsoft. Azure. Services. AppAuthentication-biblioteket för att autentisera till Azure Key Vault med .NET.
keywords: lokal inloggning för Azure Key-valv-autentisering
author: msmbaldwin
services: key-vault
ms.author: mbaldwin
ms.date: 08/08/2020
ms.topic: how-to
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: af7cb87f8caf0176505c8af864ae8d3cb0c312a0
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585703"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Tjänst-till-tjänst-autentisering för Azure Key Vault med .NET

> [!NOTE]
> **Microsoft. Azure. Services. AppAuthentication** rekommenderas inte längre att använda med New Key Vault SDK. Den ersätts med nya Azure Identity Library- **DefaultAzureCredentials** som är tillgängliga för .net, Java, typescript och python och som ska användas för all ny utveckling. Mer information hittar du här: [autentisering och Azure SDK](https://devblogs.microsoft.com/azure-sdk/authentication-and-the-azure-sdk/).

För att kunna autentisera till Azure Key Vault behöver du en Azure Active Directory (Azure AD)-autentiseringsuppgift, antingen en delad hemlighet eller ett certifikat.

Det kan vara svårt att hantera sådana autentiseringsuppgifter. Det är frestande att paketera autentiseringsuppgifter i en app genom att inkludera dem i käll-eller konfigurationsfiler. `Microsoft.Azure.Services.AppAuthentication`För .net-biblioteket fören klar det här problemet. Den använder utvecklarens autentiseringsuppgifter för att autentisera under lokal utveckling. När lösningen senare distribueras till Azure växlar biblioteket automatiskt till programmets autentiseringsuppgifter. Att använda autentiseringsuppgifter för utvecklare under lokal utveckling är säkrare eftersom du inte behöver skapa autentiseringsuppgifter för Azure AD eller dela autentiseringsuppgifter mellan utvecklare.

`Microsoft.Azure.Services.AppAuthentication`Biblioteket hanterar autentisering automatiskt, vilket i sin tur gör att du kan fokusera på din lösning i stället för dina autentiseringsuppgifter. Den stöder lokal utveckling med Microsoft Visual Studio, Azure CLI eller Azure AD Integrated Authentication. När det distribueras till en Azure-resurs som har stöd för en hanterad identitet, använder biblioteket automatiskt [hanterade identiteter för Azure-resurser](../../active-directory/msi-overview.md). Inga kod-eller konfigurations ändringar krävs. Biblioteket stöder också direkt användning av autentiseringsuppgifter för Azure AD- [klient](../../azure-resource-manager/resource-group-authenticate-service-principal.md) när en hanterad identitet inte är tillgänglig, eller när utvecklarens säkerhets kontext inte kan fastställas under lokal utveckling.

## <a name="prerequisites"></a>Förutsättningar

- [Visual studio 2019](https://www.visualstudio.com/downloads/) eller [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Tillägget för app-autentisering för Visual Studio, tillgängligt som ett separat tillägg för Visual Studio 2017 uppdatering 5 och tillsammans med produkten i uppdatering 6 och senare. Med uppdatering 6 eller senare kan du kontrol lera installationen av tillägget för app-autentisering genom att välja Azure utvecklingsverktyg i Visual Studio Installer.

## <a name="using-the-library"></a>Använda biblioteket

För .NET-program är det enklaste sättet att arbeta med en hanterad identitet genom `Microsoft.Azure.Services.AppAuthentication` paketet. Så här kommer du igång:

1. Välj **verktyg**  >  **NuGet Package Manager**  >  **Hantera NuGet-paket för lösning** för att lägga till referenser till [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) -paket i projektet.

1. Lägg till följande kod:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider`Klassen cachelagrar token i minnet och hämtar den från Azure AD precis före förfallo datum. Så du behöver inte längre kontrol lera förfallo tiden innan du anropar- `GetAccessTokenAsync` metoden. Anropa bara metoden när du vill använda token.

`GetAccessTokenAsync`Metoden kräver en resurs identifierare. Mer information om Microsoft Azure tjänster finns i [Vad är hanterade identiteter för Azure-resurser](../../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Lokal utvecklings autentisering

Det finns två huvudsakliga autentiserings scenarier för lokal utveckling: [autentisera till Azure-tjänster](#authenticating-to-azure-services)och [autentisera till anpassade tjänster](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Autentisera till Azure-tjänster

Lokala datorer har inte stöd för hanterade identiteter för Azure-resurser. Därför `Microsoft.Azure.Services.AppAuthentication` använder biblioteket dina autentiseringsuppgifter för utvecklare för att köra i din lokala utvecklings miljö. När lösningen har distribuerats till Azure, använder-biblioteket en hanterad identitet för att växla till ett OAuth 2,0-klientens Credential Grant-flöde. Den här metoden innebär att du kan testa samma kod lokalt och via fjärr anslutning utan att oroa dig.

För lokal utveckling `AzureServiceTokenProvider` hämtar token med **Visual Studio**, **Azure Command-Line Interface** (CLI) eller **Azure AD Integrated Authentication**. Varje alternativ provas sekventiellt och biblioteket använder det första alternativet som lyckas. Om inget alternativ fungerar, `AzureServiceTokenProviderException` genereras ett undantag med detaljerad information.

#### <a name="authenticating-with-visual-studio"></a>Autentisering med Visual Studio

Autentisera med hjälp av Visual Studio:

1. Logga in i Visual Studio och Använd **verktyg** &nbsp; > &nbsp; **alternativ** för att öppna **alternativ**.

1. Välj **Azure Service Authentication**, Välj ett konto för lokal utveckling och välj **OK**.

Om du stöter på problem med hjälp av Visual Studio, till exempel fel som involverar token Provider-filen, så Läs noggrant igenom föregående steg.

Du kan behöva autentisera om din Developer-token. Det gör du genom att välja **verktyg** &nbsp; > &nbsp; **alternativ**och sedan **Azure &nbsp; service &nbsp; Authentication**. Sök efter en länk för att **autentisera på nytt** under det valda kontot. Välj den för att autentisera.

#### <a name="authenticating-with-azure-cli"></a>Autentisera med Azure CLI

Om du vill använda Azure CLI för lokal utveckling ser du till att du har version [Azure CLI v 2.0.12](/cli/azure/install-azure-cli) eller senare.

Så här använder du Azure CLI:

1. Sök efter Azure CLI i aktivitets fältet i Windows för att öppna **kommando tolken Microsoft Azure**.

1. Logga in på Azure Portal: *AZ login* för att logga in på Azure.

1. Verifiera åtkomst genom att ange *AZ Account get-Access-token--Resource https: \/ /Vault.Azure.net*. Om du får ett fel meddelande kontrollerar du att rätt version av Azure CLI är korrekt installerad.

   Om Azure CLI inte är installerat i standard katalogen kan du få en fel rapportering som `AzureServiceTokenProvider` inte kan hitta sökvägen för Azure CLI. Använd miljövariabeln **AzureCLIPath** för att definiera installations mappen för Azure CLI. `AzureServiceTokenProvider` lägger till den katalog som anges i miljövariabeln **AzureCLIPath** till **Path** -miljövariabeln när det behövs.

1. Om du är inloggad på Azure CLI med flera konton eller om ditt konto har åtkomst till flera prenumerationer måste du ange vilken prenumeration som ska användas. Ange kommandot *AZ Account set--subscription <prenumerations-id>*.

Det här kommandot genererar endast utdata vid ett haveri. Verifiera de aktuella konto inställningarna genom att ange kommandot `az account list` .

#### <a name="authenticating-with-azure-ad-authentication"></a>Autentisera med Azure AD-autentisering

Om du vill använda Azure AD-autentisering kontrollerar du att:

- Dina lokala Active Directory-synkroniseringar till Azure AD. Mer information finns i [Vad är hybrid identitet med Azure Active Directory?](../../active-directory/connect/active-directory-aadconnect.md).

- Koden körs på en domänansluten dator.

### <a name="authenticating-to-custom-services"></a>Autentisera till anpassade tjänster

När en tjänst anropar Azure-tjänster fungerar föregående steg eftersom Azure-tjänster tillåter åtkomst till både användare och program.

När du skapar en tjänst som anropar en anpassad tjänst ska du använda Azure AD-klientautentiseringsuppgifter för lokal utvecklings autentisering. Det finns två alternativ:

- Använd ett huvud namn för tjänsten för att logga in på Azure:

    1. Skapa ett huvudnamn för tjänsten. Mer information finns i [skapa ett Azure-tjänstens huvud namn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Använd Azure CLI för att logga in med följande kommando:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Eftersom tjänstens huvud namn kanske inte har åtkomst till en prenumeration använder du `--allow-no-subscriptions` argumentet.

- Använd miljövariabler för att ange information om tjänstens huvud namn. Mer information finns i [köra programmet med ett huvud namn för tjänsten](#running-the-application-using-a-service-principal).

När du har loggat in på Azure `AzureServiceTokenProvider` använder tjänstens huvud namn för att hämta en token för lokal utveckling.

Den här metoden gäller endast lokal utveckling. När din lösning distribueras till Azure växlar biblioteket till en hanterad identitet för autentisering.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Köra programmet med hanterad identitet eller användare tilldelad identitet

När du kör din kod på en Azure App Service eller en virtuell Azure-dator med en hanterad identitet aktive rad använder biblioteket automatiskt den hanterade identiteten. Inga kod ändringar krävs, men den hanterade identiteten måste ha *Get* -behörighet för nyckel valvet. Du kan ge den hanterade identiteten behörigheten *Hämta* via nyckel valvets *åtkomst principer*.

Alternativt kan du autentisera med en användardefinierad identitet. Mer information om användarspecifika identiteter finns i [om hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Om du vill autentisera med en tilldelad identitet måste du ange klient-ID: t för den användare som tilldelats identiteten i anslutnings strängen. Anslutnings strängen anges i stöd för [anslutnings strängen](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Köra programmet med ett huvud namn för tjänsten

Det kan vara nödvändigt att skapa en Azure AD-klientautentiseringsuppgifter för att autentisera. Den här situationen kan inträffa i följande exempel:

- Din kod körs i en lokal utvecklings miljö, men inte under utvecklarens identitet. Service Fabric till exempel använder [NetworkService-kontot](../../service-fabric/service-fabric-application-secret-management.md) för lokal utveckling.

- Din kod körs i en lokal utvecklings miljö och du autentiserar till en anpassad tjänst, så du kan inte använda din utvecklares identitet.

- Din kod körs på en Azure Compute-resurs som ännu inte har stöd för hanterade identiteter för Azure-resurser, t. ex. Azure Batch.

Det finns tre primära metoder för att använda ett huvud namn för tjänsten för att köra programmet. Om du vill använda någon av dem måste du först skapa ett huvud namn för tjänsten. Mer information finns i [skapa ett Azure-tjänstens huvud namn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Använd ett certifikat i lokal nyckel lagring för att logga in på Azure AD

1. Skapa ett tjänst huvud certifikat med hjälp av Azure CLI [-AZ AD SP Create-for-RBAC-](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommandot.

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Det här kommandot skapar en. PEM-fil (privat nyckel) som lagras i din Hem Katalog. Distribuera det här certifikatet till antingen *LocalMachine* -eller *CurrentUser* -butiken.

    > [!Important]
    > CLI-kommandot genererar en. PEM-fil, men Windows tillhandahåller endast inbyggt stöd för PFX-certifikat. Om du vill generera ett PFX-certifikat i stället använder du PowerShell-kommandona som visas här: [skapa tjänstens huvud namn med ett självsignerat certifikat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Dessa kommandon distribuerar även certifikatet automatiskt.

1. Ange en miljö variabel med namnet **AzureServicesAuthConnectionString** till följande värde:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Ersätt *{AppId}*, *{TenantId}* och *{tumavtryck}* med värden som genereras i steg 1. Ersätt *{CertificateStore}* med antingen *LocalMachine*eller *CurrentUser*, baserat på din distributions plan.

1. Kör programmet.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Använd delade hemliga autentiseringsuppgifter för att logga in på Azure AD

1. Skapa ett tjänst huvud certifikat med ett lösen ord med hjälp av Azure CLI- [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) -kommandot med parametern--SDK-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Ange en miljö variabel med namnet **AzureServicesAuthConnectionString** till följande värde:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Ersätt _{AppId}_, _{TenantId}_ och _{ClientSecret}_ med värden som genereras i steg 1.

1. Kör programmet.

När allt har kon figurer ATS korrekt behövs inga ytterligare kod ändringar. `AzureServiceTokenProvider` använder miljövariabeln och certifikatet för att autentisera till Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Använd ett certifikat i Key Vault för att logga in på Azure AD

Med det här alternativet kan du lagra ett tjänst objekts klient certifikat i Key Vault och använda det för autentisering av tjänstens huvud namn. Du kan använda det här alternativet för följande scenarier:

- Lokal autentisering, där du vill autentisera med hjälp av ett explicit huvud namn för tjänsten och vill behålla autentiseringsuppgifterna för tjänstens huvud namn på ett säkert sätt i ett nyckel valv. Developer-kontot måste ha åtkomst till nyckel valvet.

- Autentisering från Azure där du vill använda explicita autentiseringsuppgifter och vill behålla autentiseringsuppgifterna för tjänstens huvud namn på ett säkert sätt i ett nyckel valv. Du kan använda det här alternativet för ett scenario mellan klienter. Hanterad identitet måste ha åtkomst till nyckel valvet.

Den hanterade identiteten eller din utvecklares identitet måste ha behörighet att hämta klient certifikatet från Key Vault. AppAuthentication-biblioteket använder det hämtade certifikatet som tjänst huvud kontots klientautentiseringsuppgifter.

Använda ett klient certifikat för autentisering av tjänstens huvud namn:

1. Skapa ett tjänst huvud certifikat och lagra det automatiskt i Key Vault. Använd Azure CLI- [AZ AD SP Create-for-RBAC--The-/-cert-- \<keyvaultname> cert \<certificatename> --create-cert--Skip-Assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommando:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Certifikat-ID: n är en URL i formatet `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Ersätt `{KeyVaultCertificateSecretIdentifier}` i den här anslutnings strängen med certifikat-ID:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Om ditt nyckel valv exempelvis kallades *mittkeyvault* och du skapade ett certifikat med namnet " *cert*", skulle certifikat-ID: t vara:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Stöd för anslutnings sträng

Som standard `AzureServiceTokenProvider` försöker följande autentiseringsmetoder, i ordning, hämta en token:

- [En hanterad identitet för Azure-resurser](../..//active-directory/managed-identities-azure-resources/overview.md)
- Visual Studio-autentisering
- [Azure CLI-autentisering](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)
- [Integrerad Windows-autentisering](/aspnet/web-api/overview/security/integrated-windows-authentication)

Om du vill kontrol lera processen använder du en anslutnings sträng som skickas till `AzureServiceTokenProvider` konstruktorn eller anges i miljövariabeln *AzureServicesAuthConnectionString* .  Följande alternativ stöds:

| Alternativ för anslutnings sträng | Scenario | Kommentarer|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokal utveckling | `AzureServiceTokenProvider` använder AzureCli för att hämta token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokal utveckling | `AzureServiceTokenProvider` använder Visual Studio för att hämta token. |
| `RunAs=CurrentUser` | Lokal utveckling | Stöds inte i .NET Core. `AzureServiceTokenProvider` använder Azure AD-integrerad autentisering för att hämta token. |
| `RunAs=App` | [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` använder en hanterad identitet för att hämta token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Användar tilldelad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) | `AzureServiceTokenProvider` använder en användardefinierad identitet för att hämta token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autentisering av anpassade tjänster | `KeyVaultCertificateSecretIdentifier` är certifikatets hemliga ID. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Tjänstens huvudnamn | `AzureServiceTokenProvider` använder certifikat för att hämta token från Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Tjänstens huvudnamn | `AzureServiceTokenProvider` använder certifikat för att hämta token från Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Tjänstens huvudnamn |`AzureServiceTokenProvider` använder hemlighet för att hämta token från Azure AD. |

## <a name="samples"></a>Exempel

Se följande kod exempel för att se hur `Microsoft.Azure.Services.AppAuthentication` biblioteket fungerar.

- [Använd en hanterad identitet för att hämta en hemlighet från Azure Key Vault vid körning](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Distribuera en Azure Resource Manager-mall program mässigt från en virtuell Azure-dator med en hanterad identitet](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Använd .net Core-exempel och en hanterad identitet för att anropa Azure-tjänster från en virtuell Azure Linux-dator](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>AppAuthentication-felsökning

### <a name="common-issues-during-local-development"></a>Vanliga problem under lokal utveckling

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI är inte installerat, du är inte inloggad eller har inte den senaste versionen

Kör *AZ-konto get-Access-token* för att se om Azure CLI visar en token åt dig. Om det **inte finns något sådant program**, installerar du den [senaste versionen av Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Du kan bli uppmanad att logga in.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider kan inte hitta sökvägen för Azure CLI

AzureServiceTokenProvider letar efter Azure CLI på standard installations platserna. Om det inte går att hitta Azure CLI ställer du in miljövariabeln **AzureCLIPath** till installationsmappen för Azure CLI. AzureServiceTokenProvider kommer att lägga till miljövariabeln i miljövariabeln PATH.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Du är inloggad i Azure CLI med flera konton, samma konto har åtkomst till prenumerationer i flera klienter, eller så får du ett meddelande om nekad åtkomst när du försöker ringa samtal under lokal utveckling

Använd Azure CLI och Ställ in standard prenumerationen på en som har det konto som du vill använda. Prenumerationen måste finnas i samma klient organisation som den resurs som du vill komma åt: **AZ-konto uppsättning--prenumeration [Subscription-ID]**. Om inga utdata visas lyckades det. Kontrol lera att rätt konto är nu standard **konto listan med AZ**.

### <a name="common-issues-across-environments"></a>Vanliga problem i miljöer

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Obehörig åtkomst, åtkomst nekad, förbjuden eller liknande fel

Huvudkontot som används har inte åtkomst till resursen som det försöker få åtkomst till. Bevilja antingen ditt användar konto eller App Services MSI "Contributor"-åtkomst till en resurs. Vilken som är beroende av om du kör exemplet på din lokala dator eller distribuerat i Azure till din App Service. Vissa resurser, som nyckel valv, har också sina egna [åtkomst principer](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) som du använder för att ge åtkomst till huvud konton, till exempel användare, appar och grupper.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Vanliga problem vid distribution till Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Hanterad identitet har inte ställts in på App Service

Kontrol lera miljövariabler MSI_ENDPOINT och MSI_SECRET existerar med [kudu-felsökning](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Om dessa miljövariabler inte finns, aktive ras inte hanterad identitet på App Service.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Vanliga problem vid distribution lokalt med IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Det går inte att hämta tokens vid fel sökning av appen i IIS

Som standard körs AppAuth i en annan användar kontext i IIS. Därför har den inte åtkomst att använda din utvecklares identitet för att hämta åtkomsttoken. Du kan konfigurera IIS att köras med användar kontexten med följande två steg:
- Konfigurera programpoolen för webbappen så att den körs som ditt aktuella användar konto. Mer information finns [här](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Konfigurera "setProfileEnvironment" till "true". Mer information finns [här](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Gå till% windir% \System32\inetsrv\config\applicationHost.config
    - Sök efter "setProfileEnvironment". Om den är inställd på "falskt" ändrar du den till "true". Om den inte finns lägger du till den som ett attribut till processModel-elementet ( /configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment ) och anger det till "true".

- Lär dig mer om [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/index.yml).
- Läs mer om [Azure AD-autentisering](../../active-directory/develop/active-directory-authentication-scenarios.md).
