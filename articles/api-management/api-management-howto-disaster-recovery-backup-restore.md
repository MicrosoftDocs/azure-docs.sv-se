---
title: Implementera haveri beredskap med hjälp av säkerhets kopiering och återställning i API Management
titleSuffix: Azure API Management
description: Lär dig hur du använder säkerhets kopiering och återställning för att utföra haveri beredskap i Azure API Management.
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
ms.openlocfilehash: 223d119786d99eac611ece597fc0e8de4fcaf6bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762400"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management

Genom att publicera och hantera dina API: er via Azure API Management drar du nytta av fel tolerans och infrastruktur funktioner som du annars skulle utforma, implementera och hantera manuellt. Azure-plattformen minskar en stor del möjliga problem i en bråkdel av kostnaden.

För att återställa från tillgänglighets problem som påverkar den region som är värd för din API Management-tjänst kan du när som helst göra din tjänst i en annan region. Beroende på ditt återställnings tids mål kanske du vill behålla en standby-tjänst i en eller flera regioner. Du kan också försöka att underhålla konfigurationen och innehållet synkroniserat med den aktiva tjänsten enligt målet för återställnings punkten. Funktionerna för säkerhets kopiering och återställning av tjänsten innehåller nödvändiga Bygg stenar för att implementera katastrof återställnings strategi.

Säkerhets kopierings-och återställnings åtgärder kan också användas för att replikera API Management tjänst konfiguration mellan drift miljöer, t. ex. utveckling och mellanlagring. Tänk på att körnings data som användare och prenumerationer också kommer att kopieras, vilket kanske inte alltid är önskvärt.

Den här guiden visar hur du automatiserar säkerhets kopierings-och återställnings åtgärder och hur du säkerställer en lyckad autentisering av säkerhets kopiering och återställning av Azure Resource Manager.

> [!IMPORTANT]
> Restore-åtgärden ändrar inte den anpassade värd konfigurationen för mål tjänsten. Vi rekommenderar att du använder samma anpassade värdnamn och TLS-certifikat för både aktiva och vänte läge, så att när återställnings åtgärden har slutförts kan trafiken dirigeras om till vänte läges instansen med en enkel DNS CNAME-ändring.
>
> Säkerhets kopierings åtgärden fångar inte in församlade loggdata som används i rapporter som visas på bladet analys i Azure Portal.

> [!WARNING]
> Varje säkerhets kopiering upphör att gälla efter 30 dagar. Om du försöker återställa en säkerhets kopia efter att förfallo perioden på 30 dagar har upphört att gälla, Miss lyckas återställningen med ett `Cannot restore: backup expired` meddelande.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autentisera Azure Resource Manager begär Anden

> [!IMPORTANT]
> REST API för säkerhets kopiering och återställning använder Azure Resource Manager och har en annan autentiseringsmekanism än REST-API: er för hantering av API Management entiteter. Stegen i det här avsnittet beskriver hur du autentiserar Azure Resource Manager begär Anden. Mer information finns i [autentisera Azure Resource Manager begär Anden](/rest/api/index).

Alla aktiviteter som du gör på resurser som använder Azure Resource Manager måste autentiseras med Azure Active Directory med hjälp av följande steg:

-   Lägg till ett program i Azure Active Directory klient organisationen.
-   Ange behörigheter för det program som du har lagt till.
-   Hämta token för att autentisera begär anden till Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory program

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Med prenumerationen som innehåller din API Management tjänst instans navigerar du till fliken **Appregistreringar** i **Azure Active Directory** (Azure Active Directory > hantera/Appregistreringar).

    > [!NOTE]
    > Om Azure Active Directory standard katalogen inte är synlig för ditt konto, kontakta administratören för Azure-prenumerationen för att ge de behörigheter som krävs för ditt konto.

3. Klicka på **Ny programregistrering**.

    Fönstret **skapa** visas till höger. Det är här som du anger information om AAD-appen.

4. Ange ett namn på programmet.
5. För program typ väljer du **intern**.
6. Ange en plats hållares URL, till exempel `http://resources` för **omdirigerings-URI**, eftersom det är ett obligatoriskt fält, men värdet används inte senare. Klicka i kryss rutan för att spara programmet.
7. Klicka på **Skapa**.

### <a name="add-an-application"></a>Lägga till ett program

1. När programmet har skapats klickar du på **API-behörigheter**.
2. Klicka på **+ Lägg till en behörighet**.
4. Tryck på **Välj Microsoft API: er**.
5. Välj **Azure Service Management**.
6. Tryck på **Välj**.

    ![Lägga till behörigheter](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klicka på **delegerade behörigheter** bredvid det nyligen tillagda programmet, markera kryss rutan för **åtkomst till Azure Service Management (för hands version)**.
8. Tryck på **Välj**.
9. Klicka på **bevilja behörigheter**.

### <a name="configuring-your-app"></a>Konfigurera din app

Innan du anropar API: erna som genererar säkerhets kopian och återställer det måste du hämta en token. I följande exempel används [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-paketet för att hämta token.

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

Ersätt `{tenant id}` , `{application id}` och `{redirect uri}` Använd följande instruktioner:

1. Ersätt `{tenant id}` med klient-ID: t för det Azure Active Directory program som du har skapat. Du kan komma åt ID: t genom att klicka på **Appregistreringar**  ->  **slut punkter**.

    ![Slutpunkter][api-management-endpoint]

2. Ersätt `{application id}` med värdet du får genom att gå till sidan **Inställningar** .
3. Ersätt `{redirect uri}` med-värdet från fliken **omdirigerings-URI: er** i Azure Active Directory-programmet.

    När värdena har angetts bör kod exemplet returnera en token som liknar följande exempel:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token kan ha upphört att gälla efter en viss period. Kör kod exemplet igen för att generera en ny token.

## <a name="calling-the-backup-and-restore-operations"></a>Anropa säkerhets kopierings-och återställnings åtgärderna

REST-API: erna är [API Management Service – backup](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) och [API Management-tjänsten – Återställ](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Innan du anropar åtgärderna "säkerhets kopiering och återställning" som beskrivs i följande avsnitt anger du huvudet för auktoriseringsbegäran för REST-anropet.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Säkerhetskopiera en API Management-tjänst

Om du vill säkerhetskopiera en API Management tjänst utfärdar du följande HTTP-begäran:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

där:

-   `subscriptionId` -ID för den prenumeration som innehåller den API Management-tjänst som du försöker säkerhetskopiera
-   `resourceGroupName` – namnet på resurs gruppen för din Azure API Management-tjänst
-   `serviceName` – namnet på den API Management tjänst som du skapar en säkerhets kopia av som anges när den skapas
-   `api-version` -Ersätt med `2019-12-01`

I bröd texten i begäran anger du namnet på Azure Storage-kontot, åtkomst nyckeln, namn på BLOB container och säkerhets kopians namn:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ange värdet för `Content-Type` begär ande rubriken till `application/json` .

Backup är en tids krävande åtgärd som kan ta mer än en minut att slutföra. Om begäran lyckades och säkerhets kopierings processen började visas, får du en `202 Accepted` svars status kod med ett `Location` sidhuvud. Gör GET-begäranden till URL: en i `Location` rubriken för att ta reda på status för åtgärden. När säkerhets kopieringen pågår fortsätter du att ta emot status koden 202. Svars koden `200 OK` visar att säkerhets kopieringen har slutförts.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Återställa en API Management-tjänst

Om du vill återställa en API Management tjänst från en tidigare skapad säkerhets kopia gör du följande HTTP-begäran:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

där:

-   `subscriptionId` -ID för den prenumeration som innehåller API Management tjänsten som du återställer en säkerhets kopia till
-   `resourceGroupName` – namnet på den resurs grupp som innehåller Azure API Management-tjänsten som du återställer en säkerhets kopia till
-   `serviceName` – namnet på API Managements tjänsten som återställs till angivet när den skapades
-   `api-version` -Ersätt med `api-version=2019-12-01`

Ange platsen för säkerhets kopian i bröd texten i begäran. Det innebär att du kan lägga till namnet på Azure Storage-kontot, åtkomst nyckeln, namnet på BLOB-behållaren och namnet på säkerhets kopian:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ange värdet för `Content-Type` begär ande rubriken till `application/json` .

Restore är en tids krävande åtgärd som kan ta upp till 30 minuter att slutföra. Om begäran lyckades och återställnings processen började, får du en `202 Accepted` svars status kod med ett `Location` sidhuvud. Gör GET-begäranden till URL: en i `Location` rubriken för att ta reda på status för åtgärden. När återställningen pågår fortsätter du att ta emot status koden 202. Svars koden `200 OK` visar att återställningen har slutförts.

> [!IMPORTANT]
> **SKU: n** för den tjänst som återställs till **måste matcha** SKU: n för den säkerhetskopierade tjänsten som återställs.
>
> **Ändringar** som gjorts i tjänst konfigurationen (till exempel API: er, principer, utvecklings portalens utseende) medan återställnings åtgärden pågår **kan skrivas över**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Säkerhets kopierings-och återställnings åtgärder kan också utföras med PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) och [_restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) kommandon.

## <a name="constraints-when-making-backup-or-restore-request"></a>Begränsningar när du gör en säkerhets kopierings-eller återställnings förfrågan

-   Den **behållare** som anges i begär ande texten **måste finnas**.
-   När säkerhets kopiering pågår, **Undvik hanterings ändringar i tjänsten** , till exempel SKU-uppgradering eller nedgradering, ändring i domän namn med mera.
-   Återställning av en **säkerhets kopia garanteras endast i 30 dagar** sedan den skapades.
-   **Ändringar** som gjorts i tjänst konfigurationen (till exempel API: er, principer och utvecklares Portal utseende) medan säkerhets kopieringen pågår **kan uteslutas från säkerhets kopian och kommer att gå förlorade**.
-   Om Azure Storage-kontot är aktive rad för [brand vägg][azure-storage-ip-firewall] måste kunden **tillåta** att [Azure API Management kontroll plan IP-adresser][control-plane-ip-address] på lagrings kontot för säkerhets kopiering eller återställning från fungerar. Azure Storage kontot kan finnas i valfri Azure-region förutom det där API Managements tjänsten finns. Om API Management tjänsten t. ex. är i västra USA kan Azure Storage-kontot ligga i västra USA 2 och kunden måste öppna kontroll planens IP-13.64.39.16 (API Management kontroll planens IP-adress för USA, västra) i brand väggen. Detta beror på att begär anden till Azure Storage inte SNATed till en offentlig IP-adress från Compute (Azure API Management Control plan) i samma Azure-region. Lagrings förfrågan mellan regioner kommer att SNATed till den offentliga IP-adressen.
-   [Resurs delning mellan ursprung (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) bör **inte** aktive ras på BLOB service i Azure Storage kontot.
-   **SKU: n** för den tjänst som återställs till **måste matcha** SKU: n för den säkerhetskopierade tjänsten som återställs.

## <a name="what-is-not-backed-up"></a>Vad säkerhets kopie ras inte
-   **Användnings data** som används för att skapa analys rapporter **ingår inte** i säkerhets kopian. Använd [Azure API Management REST API][azure api management rest api] för att regelbundet hämta analys rapporter för förvaring.
-   [Anpassade TLS/SSL-](configure-custom-domain.md) certifikat för domän.
-   [Anpassat CA-certifikat](api-management-howto-ca-certificates.md), som innehåller mellanliggande eller rot certifikat som har överförts av kunden.
-   Inställningar för integrering av [virtuella nätverk](api-management-using-with-vnet.md) .
-   [Hanterad identitets](api-management-howto-use-managed-service-identity.md) konfiguration.
-   [Azure Monitor diagnostik](api-management-howto-use-azure-monitor.md) Inställningarna.
-   [Protokoll och krypterings](api-management-howto-manage-protocols-ciphers.md) inställningar.
-   Innehåll i [Developer-portalen](api-management-howto-developer-portal.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) .

Den frekvens med vilken du utför säkerhets kopiering av tjänster påverkar återställnings punkt målet. För att minimera det rekommenderar vi att du implementerar regelbundna säkerhets kopieringar och utför säkerhets kopieringar på begäran när du har gjort ändringar i API Managements tjänsten.

## <a name="next-steps"></a>Nästa steg

Kolla in följande resurser för olika genom gångar av säkerhets kopierings-/återställnings processen.

-   [Replikera Azure API Management-konton](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatisera API Management-säkerhetskopiering och -återställning med Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: säkerhetskopiera och återställa konfigurationen](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
     _Metoden som beskrivs av Stuart överensstämmer inte med den officiella vägledningen, men det är intressant._

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
