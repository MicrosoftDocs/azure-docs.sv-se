---
title: Implementera haveriberedskap med hjälp av säkerhetskopiering och återställning i API Management
titleSuffix: Azure API Management
description: Lär dig hur du använder säkerhetskopiering och återställning för att utföra haveriberedskap i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2020
ms.author: apimpm
ms.openlocfilehash: 090eda3c3310a1b793733e37725c62758445d6b2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587374"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management

Genom att publicera och hantera dina API:er via Azure API Management utnyttjar du feltolerans- och infrastrukturfunktioner som du annars skulle utforma, implementera och hantera manuellt. Azure-plattformen minskar en stor del av potentiella fel till en bråkdel av kostnaden.

Om du vill återställa från tillgänglighetsproblem som påverkar den region som är värd för din API Management-tjänst, kan du när som helst vara redo att rekonfigurera tjänsten i en annan region. Beroende på ditt mål för återställningstid kanske du vill behålla en väntelägestjänst i en eller flera regioner. Du kan också försöka behålla konfigurationen och innehållet synkroniserat med den aktiva tjänsten enligt ditt mål för återställningspunkt. Funktionerna för säkerhetskopiering och återställning av tjänster tillhandahåller de byggstenar som krävs för att implementera en strategi för haveriberedskap.

Säkerhetskopierings- och återställningsåtgärder kan också användas för att replikera API Management mellan driftsmiljöer, till exempel utveckling och mellanlagring. Se till att även körningsdata som användare och prenumerationer kopieras, vilket kanske inte alltid är önskvärt.

Den här guiden visar hur du automatiserar säkerhetskopierings- och återställningsåtgärder och hur du säkerställer en lyckad autentisering av säkerhetskopierings- och återställningsbegäranden Azure Resource Manager.

> [!IMPORTANT]
> Återställningsåtgärden ändrar inte konfigurationen av det anpassade värdnamnet för måltjänsten. Vi rekommenderar att du använder samma anpassade värdnamn och TLS-certifikat för både aktiva tjänster och väntelägestjänster, så att trafiken kan dirigeras om till standby-instansen med en enkel DNS CNAME-ändring när återställningen har slutförts.
>
> Säkerhetskopieringsåtgärden samlar inte in föraggregering av loggdata som används i rapporter som visas på Analytics-bladet i Azure Portal.

> [!WARNING]
> Varje säkerhetskopia upphör att gälla efter 30 dagar. Om du försöker återställa en säkerhetskopia när förfalloperioden på 30 dagar har gått ut, misslyckas återställningen med ett `Cannot restore: backup expired` meddelande.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autentisera Azure Resource Manager begäranden

> [!IMPORTANT]
> Den REST API för säkerhetskopiering och återställning använder Azure Resource Manager och har en annan autentiseringsmekanism än REST-API:erna för att hantera dina API Management entiteter. Stegen i det här avsnittet beskriver hur du autentiserar Azure Resource Manager begäranden. Mer information finns i [Autentisera Azure Resource Manager begäranden.](/rest/api/index)

Alla uppgifter som du utför på resurser med hjälp av Azure Resource Manager måste autentiseras med Azure Active Directory med hjälp av följande steg:

-   Lägg till ett program i Azure Active Directory klientorganisationen.
-   Ange behörigheter för det program som du har lagt till.
-   Hämta token för autentisering av begäranden till Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory program

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Använd prenumerationen som innehåller din API Management-tjänstinstans och gå **till fliken Appregistreringar** i **Azure Active Directory** (Azure Active Directory > Manage/Appregistreringar).

    > [!NOTE]
    > Om standardkatalogen Azure Active Directory inte visas för ditt konto kontaktar du administratören för Azure-prenumerationen för att bevilja de behörigheter som krävs för ditt konto.

3. Klicka på **Ny programregistrering**.

    Fönstret **Skapa** visas till höger. Det är där du anger relevant information för AAD-appen.

4. Ange ett namn på programmet.
5. Som programtyp väljer du **Intern**.
6. Ange en platshållar-URL, till exempel för `http://resources` **omdirigerings-URI:n,** eftersom det är ett obligatoriskt fält, men värdet används inte senare. Klicka på kryssrutan för att spara programmet.
7. Klicka på **Skapa**.

### <a name="add-an-application"></a>Lägga till ett program

1. När programmet har skapats klickar du på **API-behörigheter.**
2. Klicka på **+ Lägg till en behörighet**.
4. Tryck **på Välj Microsoft-API:er.**
5. Välj **Azure Service Management.**
6. Tryck **på Välj**.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="Skärmbild som visar hur du lägger till appbehörigheter."::: 

7. Klicka **på Delegerade** behörigheter bredvid det nyligen tillagda programmet och markera kryssrutan för **Access Azure Service Management (förhandsversion)**.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="Skärmbild som visar tillägg av delegerade appbehörigheter.":::

8. Tryck **på Välj**.
9. Klicka **på Lägg till behörigheter.**

### <a name="configuring-your-app"></a>Konfigurera din app

Innan du anropar API:erna som genererar säkerhetskopian och återställer den måste du hämta en token. I följande exempel används [NuGet-paketet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) för att hämta token.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Ersätt `{tenant id}` `{application id}` , och med följande `{redirect uri}` instruktioner:

1. Ersätt `{tenant id}` med klientorganisations-ID:t för Azure Active Directory som du skapade. Du kan komma åt ID:t genom **att klicka Appregistreringar**  ->  **slutpunkter**.

    ![Slutpunkter][api-management-endpoint]

2. Ersätt `{application id}` med det värde som du får genom att gå till **sidan** Inställningar.
3. Ersätt med `{redirect uri}` värdet från fliken **Omdirigerings-URI:er** i Azure Active Directory program.

    När värdena har angetts ska kodexe exemplet returnera en token som liknar följande exempel:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token kan upphöra att gälla efter en viss tid. Kör kodexeletten igen för att generera en ny token.

## <a name="calling-the-backup-and-restore-operations"></a>Anropa säkerhetskopierings- och återställningsåtgärder

REST-API:erna är [Api Management-tjänsten – Backup](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) och Api [Management-tjänsten – Återställ](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Innan du anropar åtgärderna "säkerhetskopiering och återställning" som beskrivs i följande avsnitt anger du rubriken för auktoriseringsbegäran för REST-anropet.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Back up an API Management service

Om du vill API Management en tjänst utfärdar du följande HTTP-begäran:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

där:

-   `subscriptionId` – ID för prenumerationen som innehåller API Management-tjänsten som du försöker backa upp
-   `resourceGroupName` – namnet på resursgruppen för din Azure API Management tjänst
-   `serviceName` – namnet på den API Management-tjänst som du gör en säkerhetskopia av som angavs när den skapades
-   `api-version` – ersätt med `2019-12-01`

I brödtexten i begäran anger du Azure Storage-målkontots namn, åtkomstnyckel, blobcontainernamn och säkerhetskopieringsnamn:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ange värdet för `Content-Type` begärandehuvudet till `application/json` .

Säkerhetskopiering är en långvarig åtgärd som kan ta mer än en minut att slutföra. Om begäran lyckades och säkerhetskopieringen påbörjades får du en `202 Accepted` svarsstatuskod med ett `Location` -huvud. Gör "GET"-begäranden till URL:en `Location` i rubriken för att ta reda på åtgärdens status. När säkerhetskopieringen pågår får du fortfarande statuskoden "202 Accepted". Svarskoden anger `200 OK` att säkerhetskopieringen har slutförts.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Återställa en API Management tjänst

Om du vill API Management en tjänst från en tidigare skapad säkerhetskopia gör du följande HTTP-begäran:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

där:

-   `subscriptionId` – ID för den prenumeration som innehåller API Management-tjänsten som du återställer en säkerhetskopia till
-   `resourceGroupName` – namnet på den resursgrupp som innehåller Azure API Management-tjänsten som du återställer en säkerhetskopia till
-   `serviceName` – namnet på den API Management som återställs till angivet när den skapades
-   `api-version` – ersätt med `api-version=2019-12-01`

I brödtexten i begäran anger du platsen för säkerhetskopieringsfilen. Det innebär att du lägger till Azure Storage-kontonamnet, åtkomstnyckeln, blobcontainerns namn och säkerhetskopieringsnamnet:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ange värdet för `Content-Type` begärandehuvudet till `application/json` .

Återställning är en långvarig åtgärd som kan ta upp till 30 minuter eller mer att slutföra. Om begäran lyckades och återställningsprocessen påbörjades får du en `202 Accepted` svarsstatuskod med ett `Location` -huvud. Gör GET-begäranden till URL:en i `Location` rubriken för att ta reda på åtgärdens status. Medan återställningen pågår får du statuskoden "202 Accepted". Svarskoden `200 OK` anger att återställningsåtgärden har slutförts.

> [!IMPORTANT]
> **SKU:n** för tjänsten som återställs **till måste matcha** SKU:n för den säkerhetskopierade tjänst som återställs.
>
> **Ändringar** som görs i tjänstkonfigurationen (till exempel API:er, principer, utseendet på utvecklarportalen) medan återställningsåtgärden **pågår kan skrivas över**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Säkerhetskopierings- och återställningsåtgärder kan också utföras med kommandona PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) och [_Restore-AzApiManagement._](/powershell/module/az.apimanagement/restore-azapimanagement)

## <a name="constraints-when-making-backup-or-restore-request"></a>Begränsningar när du gör en säkerhetskopierings- eller återställningsbegäran

-   **Containern** som anges i **begärandetexten måste finnas**.
-   När säkerhetskopieringen pågår bör du **undvika hanteringsändringar** i tjänsten, till exempel uppgradering eller nedgradering av SKU, ändring av domännamn med mera.
-   Återställning av en **säkerhetskopia garanteras endast i 30 dagar** sedan den skapades.
-   **Ändringar** som görs i tjänstkonfigurationen (till exempel API:er, principer och utseendet på utvecklarportalen) medan säkerhetskopieringen pågår kan uteslutas från säkerhetskopieringen och **går förlorade.**
-   Om Azure Storage-kontot är [][azure-storage-ip-firewall] brandvägg aktiverat måste kunden  tillåta uppsättningen [med Azure API Management-kontrollplans-IP-adresser][control-plane-ip-address] på sitt lagringskonto för att kunna säkerhetskopiera till eller återställa från till arbete. Kontot Azure Storage kan finnas i valfri Azure-region förutom den där API Management-tjänsten finns. Om API Management-tjänsten till exempel finns i USA, västra kan Azure Storage-kontot vara i USA, västra 2 och kunden måste öppna kontrollen Plan-IP 13.64.39.16 (API Management Control Plane IP of West US) i brandväggen. Det beror på att begäranden till Azure Storage inte SNATed till en offentlig IP-adress från Compute (Azure Api Management Control Plane) i samma Azure-region. Lagringsbegäran mellan regioner kommer att SNATed till den offentliga IP-adressen.
-   [Resursdelning för korsande ursprung (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) **ska inte** aktiveras på Blob Service i Azure Storage konto.
-   **SKU:n** för den tjänst som återställs **till måste matcha** SKU:n för den säkerhetskopierade tjänst som återställs.

## <a name="what-is-not-backed-up"></a>Vad som inte säkerhetskopieras
-   **Användningsdata** som används för att skapa **analysrapporter ingår inte i** säkerhetskopieringen. Använd [Azure API Management REST API att][azure api management rest api] regelbundet hämta analysrapporter för säker lagring.
-   [TLS/SSL-certifikat för anpassad](configure-custom-domain.md) domän.
-   [Anpassat CA-certifikat](api-management-howto-ca-certificates.md), som innehåller mellanliggande certifikat eller rotcertifikat som laddats upp av kunden.
-   [Inställningar för integrering](api-management-using-with-vnet.md) av virtuella nätverk.
-   [Konfiguration av hanterad](api-management-howto-use-managed-service-identity.md) identitet.
-   [Azure Monitor diagnostik](api-management-howto-use-azure-monitor.md) Konfiguration.
-   [Protokoll och chifferinställningar.](api-management-howto-manage-protocols-ciphers.md)
-   [Innehåll på](api-management-howto-developer-portal.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) utvecklarportalen.

Hur ofta du utför tjänstsäkerhetskopior påverkar ditt mål för återställningspunkten. För att minimera det rekommenderar vi att du implementerar regelbundna säkerhetskopieringar och säkerhetskopieringar på begäran när du har gjort ändringar i API Management tjänsten.

## <a name="next-steps"></a>Nästa steg

Kolla in följande resurser för olika genomgångar av säkerhetskopierings-/återställningsprocessen.

-   [Replikera Azure API Management konton](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatisera API Management-säkerhetskopiering och -återställning med Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: Backing Up and Restoring Configuration](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
     _Den metod som beskrivs av Stuart matchar inte den officiella vägledningen, men den är intressant._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
