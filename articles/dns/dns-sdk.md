---
title: Skapa DNS-zoner och post uppsättningar med hjälp av .NET SDK
titleSuffix: Azure DNS
description: I den här utbildnings vägen kommer du igång med att skapa DNS-zoner och post uppsättningar i Azure DNS med hjälp av .NET SDK.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e116096afbd01af4914be49d5675881724d5069
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015068"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Skapa DNS-zoner och post uppsättningar med hjälp av .NET SDK

Du kan automatisera åtgärder för att skapa, ta bort eller uppdatera DNS-zoner, post uppsättningar och poster med hjälp av DNS SDK med .NET DNS-hanterings biblioteket. Det finns ett fullständigt Visual Studio-projekt [här.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Skapa ett konto för tjänstens huvud namn

Normalt beviljas program mässig åtkomst till Azure-resurser via ett dedikerat konto i stället för dina egna användarautentiseringsuppgifter. Dessa dedikerade konton kallas för tjänstens huvud konto. Om du vill använda det Azure DNS SDK-exempelprojektet måste du först skapa ett tjänst objekts konto och tilldela det rätt behörighet.

1. Följ [de här anvisningarna](../active-directory/develop/howto-authenticate-service-principal-powershell.md) för att skapa ett tjänst objekts konto (Azure DNS SDK-exempelprojektet antar lösenordsbaserad autentisering).
2. Skapa en resurs grupp ([så här](../azure-resource-manager/templates/deploy-portal.md)).
3. Använd Azure RBAC för att ge tjänstens huvud konto behörighet till resurs gruppen ([så här](../role-based-access-control/role-assignments-portal.md)).
4. Om du använder Azure DNS SDK-exempelprojektet redigerar du filen ' program. cs ' enligt följande:

   * Infoga rätt värden för `tenantId` , `clientId` (även kallat konto-ID), `secret` (konto lösen ord för tjänstens huvud namn) och `subscriptionId` som det används i steg 1.
   * Ange det resurs grupps namn som valdes i steg 2.
   * Ange ett valfritt namn för DNS-zonen.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-paket och namespace-deklarationer

Om du vill använda Azure DNS .NET SDK måste du installera **Azure DNS Management Library** NuGet-paketet och andra nödvändiga Azure-paket.

1. Öppna ett projekt eller nytt projekt i **Visual Studio**.
2. Gå till **verktyg** **>** **NuGet Package Manager** **>** **Hantera NuGet-paket för lösning.**..
3. Klicka på **Bläddra**, aktivera kryss rutan **Inkludera för hands version** och skriv **Microsoft. Azure. Management. DNS** i sökrutan.
4. Välj paketet och klicka på **Installera** för att lägga till det i Visual Studio-projektet.
5. Upprepa proceduren ovan för att även installera följande paket: **Microsoft. rest. ClientRuntime. Azure. Authentication** och **Microsoft. Azure. Management. ResourceManager**.

## <a name="add-namespace-declarations"></a>Lägga till namnrymdsdeklarationer

Lägg till följande deklarationer för namn områden

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Initiera DNS-hanterings klienten

`DnsManagementClient`Innehåller de metoder och egenskaper som krävs för att hantera DNS-zoner och post uppsättningar.  Följande kod loggar in på tjänstens huvud konto och skapar ett- `DnsManagementClient` objekt.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Skapa eller uppdatera en DNS-zon

Om du vill skapa en DNS-zon skapas först ett "Zone"-objekt som innehåller parametrarna för DNS-zonen. Eftersom DNS-zoner inte är länkade till en speciell region anges platsen till ' Global '. I det här exemplet läggs även en [Azure Resource Manager-tagg](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) till i zonen.

Om du faktiskt vill skapa eller uppdatera zonen i Azure DNS skickas det zon objekt som innehåller zon parametrarna till- `DnsManagementClient.Zones.CreateOrUpdateAsyc` metoden.

> [!NOTE]
> DnsManagementClient stöder tre arbets lägen: synkron (' CreateOrUpdate '), asynkron (' CreateOrUpdateAsync ') eller asynkron med åtkomst till HTTP-svaret (' CreateOrUpdateWithHttpMessagesAsync ').  Du kan välja något av dessa lägen, beroende på dina program behov.

Azure DNS stöder optimistisk samtidighet, som kallas [ETags](./dns-getstarted-powershell.md). I det här exemplet anger du "*" för rubriken "If-None-Match" och anger Azure DNS att skapa en DNS-zon om det inte redan finns en.  Anropet Miss lyckas om det redan finns en zon med det här namnet i den aktuella resurs gruppen.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Skapa DNS-postuppsättningar och-poster

DNS-poster hanteras som en post uppsättning. En post uppsättning är en uppsättning poster med samma namn och post typ inom en zon.  Post uppsättningens namn är i förhållande till zon namnet, inte det fullständigt kvalificerade DNS-namnet.

Om du vill skapa eller uppdatera en post uppsättning skapas ett "RecordSet"-parameter objekt och skickas till `DnsManagementClient.RecordSets.CreateOrUpdateAsync` . Precis som med DNS-zoner finns det tre drifts lägen: synkron (' CreateOrUpdate '), asynkron (' CreateOrUpdateAsync ') eller asynkron med åtkomst till HTTP-svaret (' CreateOrUpdateWithHttpMessagesAsync ').

Precis som med DNS-zoner inkluderar åtgärder på post uppsättningar stöd för optimistisk samtidighet.  I det här exemplet, eftersom ingen "If-Match" eller "If-None-Match" anges, skapas alltid post uppsättningen.  Det här anropet skriver över alla befintliga post uppsättningar med samma namn och post typ i den här DNS-zonen.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Hämta zoner och post uppsättningar

`DnsManagementClient.Zones.Get`Metoderna och `DnsManagementClient.RecordSets.Get` hämtar enskilda zoner och post uppsättningar. Post uppsättningar identifieras av deras typ, namn och zon och resurs grupp de finns i. Zoner identifieras med deras namn och den resurs grupp de finns i.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Uppdatera en befintlig post uppsättning

Om du vill uppdatera en befintlig DNS-postuppsättning måste du först hämta post uppsättningen och sedan uppdatera post uppsättningens innehåll och sedan skicka ändringen.  I det här exemplet anger vi "etag" från den hämtade post uppsättningen i parametern "If-Match". Anropet Miss lyckas om en samtidig åtgärd har ändrat post uppsättningen under tiden.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lista zoner och post uppsättningar

Om du vill visa en lista över zoner använder du metoderna *DnsManagementClient. Zones. list...* som stöd för att visa alla zoner i en specifik resurs grupp eller alla zoner i en specifik Azure-prenumeration (mellan olika resurs grupper.) Om du vill visa post uppsättningar använder du *DnsManagementClient. recordsets. list...* -metoder som stöder antingen en lista över alla post uppsättningar i en specifik zon eller endast de post uppsättningar av en specifik typ.

Observera när du visar en lista över zoner och post uppsättningar som kan ha sid brytningar.  I följande exempel visas hur du itererar igenom resultat sidorna. (En artificiellt liten sid storlek på "2" används för att framtvinga växling. i praktiken ska parametern utelämnas och standard sid storleken används.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Nästa steg

Hämta [exempel projektet Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), som innehåller ytterligare exempel på hur du använder Azure DNS .NET SDK, inklusive exempel för andra typer av DNS-poster.