---
title: Azure IoT Hub stöd för virtuella nätverk
description: Använda anslutningsmönster för virtuella nätverk med IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jlian
ms.openlocfilehash: df38f9b3482847ea0415af5cb47540e244b0510b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739898"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub stöd för virtuella nätverk med Private Link och hanterad identitet

Som standard IoT Hub värdnamnen till en offentlig slutpunkt med en offentligt dirigerbar IP-adress via Internet. Olika kunder delar IoT Hub offentliga slutpunkten, och IoT-enheter i över breda nätverk och lokala nätverk kan komma åt den.

![IoT Hub offentlig slutpunkt](./media/virtual-network-support/public-endpoint.png)

IoT Hub funktioner som meddelanderoutning, filuppladdning och [massimport/massenhetsexport](./iot-hub-bulk-identity-mgmt.md) kräver också anslutning från IoT Hub till en kundägd Azure-resurs via dess offentliga slutpunkt. [](./iot-hub-devguide-messages-d2c.md) [](./iot-hub-devguide-file-upload.md) Dessa anslutningsvägar utgör tillsammans den utgående trafiken från IoT Hub till kundresurser.

Du kanske vill begränsa anslutningen till dina Azure-resurser (inklusive IoT Hub) via ett VNet som du äger och använder. Dessa orsaker är:

* Introduktion till nätverksisolering för din IoT-hubb genom att förhindra att anslutningen exponeras för det offentliga Internet.

* Aktivera en privat anslutningsupplevelse från dina lokala nätverkstillgångar och se till att dina data och trafik överförs direkt till Azure-stamnätverket.

* Förhindra exfiltreringsattacker från känsliga lokala nätverk. 

* Efter etablerade anslutningsmönster för hela Azure med [privata slutpunkter.](../private-link/private-endpoint-overview.md)

Den här artikeln beskriver hur du uppnår dessa mål med [hjälp av Azure Private Link](../private-link/private-link-overview.md) för ingressanslutning till IoT Hub och använder betrott Microsoft-tjänster-undantag för utgående anslutning från IoT Hub till andra Azure-resurser.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Ingressanslutning till IoT Hub med Azure Private Link

En privat slutpunkt är en privat IP-adress som allokeras i ett kundägt VNet som en Azure-resurs kan nås via. Via Azure Private Link kan du konfigurera en privat slutpunkt för din IoT-hubb så att tjänster i ditt VNet kan nå IoT Hub utan att trafik behöver skickas till IoT Hub offentliga slutpunkt. På samma sätt kan dina lokala enheter använda [VPN (Virtual Private Network)](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute-peering](https://azure.microsoft.com/services/expressroute/) för att få anslutning till ditt VNet och din IoT Hub (via dess privata slutpunkt). Därför kan du begränsa eller helt blockera anslutningen till din IoT-hubbs offentliga slutpunkter genom att IoT Hub [IP-filter](./iot-hub-ip-filtering.md) eller det offentliga [nätverksåtkomstreglaget](iot-hub-public-network-access.md). Den här metoden behåller anslutningen till hubben med hjälp av den privata slutpunkten för enheter. Huvudfokus för den här konfigurationen är enheter i ett lokalt nätverk. Den här konfigurationen rekommenderas inte för enheter som distribueras i ett brett nätverk.

![IoT Hub för virtuellt nätverk](./media/virtual-network-support/virtual-network-ingress.png)

Kontrollera att följande krav är uppfyllda innan du fortsätter:

* Du har skapat [ett azure-VNet](../virtual-network/quick-create-portal.md) med ett undernät där den privata slutpunkten ska skapas.

* För enheter som fungerar i lokala nätverk ställ in [vpn (virtuellt privat nätverk)](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [privat ExpressRoute-peering](https://azure.microsoft.com/services/expressroute/) till ditt virtuella Azure-nätverk.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Konfigurera en privat slutpunkt för IoT Hub ingress

Privat slutpunkt fungerar för IoT Hub-API:er (t.ex. meddelanden från enheten till molnet) samt tjänst-API:er (som att skapa och uppdatera enheter).

1. I Azure Portal väljer du **Nätverk,** **Privata slutpunktsanslutningar** och klickar på **+ Privat slutpunkt.**

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Skärmbild som visar var du lägger till privat slutpunkt för IoT Hub":::

1. Ange prenumerationen, resursgruppen, namnet och regionen som den nya privata slutpunkten ska skapas i. Helst ska den privata slutpunkten skapas i samma region som hubben.

1. Klicka **på Nästa:** Resurs och ange prenumerationen för din IoT Hub-resurs och välj **"Microsoft.Devices/IotHubs"** som resurstyp, ditt IoT Hub-namn som **resurs** och **iotHub som** målunderresurs.

1. Klicka **på Nästa: Konfiguration** och ange ditt virtuella nätverk och undernät för att skapa den privata slutpunkten i. Välj alternativet att integrera med den privata DNS-zonen i Azure om du vill.

1. Klicka **på Nästa: Taggar** och ange eventuella taggar för resursen om du vill.

1. Klicka **på Granska + skapa** för att skapa din privata länkresurs.

### <a name="built-in-event-hub-compatible-endpoint"></a>Inbyggd event hub-kompatibel slutpunkt 

Den [inbyggda Event Hub-kompatibla slutpunkten](iot-hub-devguide-messages-read-builtin.md) kan också nås via den privata slutpunkten. När private link har konfigurerats bör du se ytterligare en privat slutpunktsanslutning för den inbyggda slutpunkten. Det är det som finns `servicebus.windows.net` i FQDN.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="Bild som visar två privata slutpunkter för varje IoT Hub privat länk":::

IoT Hub [IP-filter kan](iot-hub-ip-filtering.md) eventuellt styra offentlig åtkomst till den inbyggda slutpunkten. 

Om du vill helt blockera offentlig nätverksåtkomst till din IoT-hubb stänger du av offentlig nätverksåtkomst eller använder IP-filter för att blockera alla IP-adresser och väljer alternativet för att tillämpa regler på den inbyggda slutpunkten. [](iot-hub-public-network-access.md)

### <a name="pricing-for-private-link"></a>Prissättning för Private Link

Prisinformation finns i [Azure Private Link priser.](https://azure.microsoft.com/pricing/details/private-link)

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Utgående anslutning från IoT Hub till andra Azure-resurser

IoT Hub kan ansluta till din Azure Blob Storage, händelsehubb, [](./iot-hub-devguide-file-upload.md)Service Bus-resurser för meddelanderoutning, [](./iot-hub-devguide-messages-d2c.md)filuppladdning och [massimport/massexport](./iot-hub-bulk-identity-mgmt.md) av enheter via resursernas offentliga slutpunkt. Om du binder resursen till ett VNet blockeras anslutningen till resursen som standard. Därför förhindrar den här konfigurationen IoT Hub inte att skicka data till dina resurser. Åtgärda problemet genom att aktivera anslutning från din IoT Hub till ditt lagringskonto, händelsehubb eller Service Bus-resurser via alternativet **för betrodd Microsoft-tjänst.**

### <a name="turn-on-managed-identity-for-iot-hub"></a>Aktivera hanterad identitet för IoT Hub

För att andra tjänster ska kunna hitta din IoT Hub som en betrodd Microsoft-tjänst måste den ha en system tilldelad hanterad identitet.

1. Gå till **Identity** i IoT Hub portalen

1. Under **Status** väljer du **På** och klickar sedan på **Spara.**

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Skärmbild som visar hur du aktiverar hanterad identitet för IoT Hub":::

Så här använder du Azure CLI för att aktivera hanterad identitet:

```azurecli-interactive
az iot hub update --name <iot-hub-resource-name> --set identity.type="SystemAssigned"
```

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Tilldela en hanterad identitet till IoT Hub när den skapas med hjälp av EN ARM-mall

Om du vill tilldela hanterad identitet till din IoT-hubb vid tidpunkten för resursetablering använder du ARM-mallen nedan. Den här ARM-mallen har två nödvändiga resurser, och båda måste distribueras innan du skapar andra resurser som `Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups` . 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "createIotHub",
      "dependsOn": [
        "[resourceId('Microsoft.Devices/IotHubs', '<provide-a-valid-resource-name>')]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

När du har ersättt värdena för din resurs , och kan du använda Azure CLI för att `name` distribuera resursen i en befintlig `location` `SKU.name` `SKU.tier` resursgrupp med hjälp av:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

När resursen har skapats kan du hämta den hanterade tjänstidentitet som tilldelats hubben med hjälp av Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Prissättning för hanterad identitet

Undantagsfunktionen för betrodda Microsoft-tjänster från första part är kostnadsfri. Avgifter för etablerade lagringskonton, händelsehubbbar eller Service Bus-resurser tillämpas separat.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Utgående anslutning till slutpunkter för lagringskonto för routning

IoT Hub kan dirigera meddelanden till ett kundägt lagringskonto. För att routningsfunktionen ska få åtkomst till ett lagringskonto när brandväggsbegränsningar finns måste hubben använda en hanterad identitet för att få åtkomst till lagringskontot. Först behöver hubben en [hanterad identitet](#turn-on-managed-identity-for-iot-hub). När en hanterad identitet har etablerats följer du stegen nedan för att ge Azure RBAC-behörighet till hubbens resursidentitet för att få åtkomst till ditt lagringskonto.

1. I Azure Portal du till fliken Åtkomstkontroll **(IAM)** för lagringskontot och klickar på Lägg till **under** avsnittet Lägg till **en rolltilldelning.**

2. Välj **Storage Blob Data-deltagare** (inte Deltagare eller Lagringskontodeltagare) som roll  [](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) **,** **Azure AD-användare,** grupp eller tjänstens huvudnamn som Tilldelar åtkomst till och välj ditt IoT Hub:s resursnamn i listrutan. Klicka på knappen **Spara**.

3. Gå till fliken **Brandväggar och virtuella nätverk i** lagringskontot och aktivera alternativet Tillåt åtkomst från **valda** nätverk. I listan **Undantag** markerar du kryssrutan för Tillåt betrodda **användare Microsoft-tjänster åtkomst till det här lagringskontot.** Klicka på knappen **Spara**.

4. På IoT Hub resurssidan går du till fliken **Meddelanderoutning.**

5. Gå till **avsnittet Anpassade slutpunkter** och klicka på Lägg **till**. Välj **Lagring** som slutpunktstyp.

6. På sidan som visas anger du ett namn för slutpunkten, väljer den container som du tänker använda i bloblagringen, anger kodning och filformat. Välj **Identitetsbaserad som** **Autentiseringstyp för** lagringsslutpunkten. Klicka på knappen **Skapa**.

Nu är din anpassade lagringsslutpunkt konfigurerad att använda hubbens systemtilldeade identitet, och den har behörighet att komma åt lagringsresursen trots dess brandväggsbegränsningar. Nu kan du använda den här slutpunkten för att konfigurera en routningsregel.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Utgående anslutning till event hubs-slutpunkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till ett kundägt event hubs-namnområde. För att routningsfunktionen ska få åtkomst till en händelsehubbresurs när brandväggsbegränsningar finns måste din IoT Hub använda en hanterad identitet för att få åtkomst till händelsehubbens resurs. Först behöver hubben en hanterad identitet. När en hanterad identitet har skapats följer du stegen nedan för att ge Azure RBAC-behörighet till hubbens resursidentitet för att få åtkomst till dina händelsehubbar.

1. I dialogrutan Azure Portal du till fliken Åtkomstkontroll för händelsehubbbar **(IAM)** och klickar på Lägg till **under** avsnittet Lägg till **en rolltilldelning.**

2. Välj **Event Hubs Data Sender** **som** roll, **Azure AD-användare,** grupp eller tjänstens huvudnamn som Tilldela åtkomst till och välj IoT Hub:s resursnamn i listrutan.  Klicka på knappen **Spara**.

3. Gå till fliken **Brandväggar och virtuella nätverk** i händelsehubben och aktivera **alternativet Tillåt åtkomst från valda** nätverk. Under **undantagslistan** markerar du kryssrutan för Tillåt **betrodda enheter Microsoft-tjänster komma åt händelsehubbbar**. Klicka på knappen **Spara**.

4. På IoT Hub resurssidan går du till fliken **Meddelanderoutning.**

5. Gå till **avsnittet Anpassade slutpunkter** och klicka på Lägg **till**. Välj **Event Hubs** som slutpunktstyp.

6. På sidan som visas anger du ett namn för slutpunkten och väljer händelsehubbens namnområde och instans. Välj **Identitetsbaserad** som **Autentiseringstyp** och klicka på **knappen** Skapa.

Nu är slutpunkten för din anpassade händelsehubb konfigurerad för att använda hubbens systemtilldeade identitet och den har behörighet att komma åt händelsehubbens resurs trots sina brandväggsbegränsningar. Nu kan du använda den här slutpunkten för att konfigurera en routningsregel.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Utgående anslutning till Service Bus-slutpunkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till ett kundägt Service Bus-namnområde. Om du vill tillåta routningsfunktionen att få åtkomst till en Service Bus-resurs när brandväggsbegränsningar finns måste din IoT Hub använda en hanterad identitet för att få åtkomst till Service Bus-resursen. Först behöver hubben en hanterad identitet. När en hanterad identitet har etablerats följer du stegen nedan för att ge Azure RBAC-behörighet till hubbens resursidentitet för att få åtkomst till din Service Bus.

1. I Azure Portal du till fliken Åtkomstkontroll **(IAM)** i Service Bus och klickar på Lägg till **under** avsnittet Lägg till **en rolltilldelning.**

2. Välj **Service Bus Data Sender** som roll, Azure **AD-användare,** grupp eller tjänstens huvudnamn som Tilldela åtkomst till och välj IoT Hub-användarens resursnamn i listrutan.   Klicka på knappen **Spara**.

3. Gå till fliken **Brandväggar och virtuella nätverk** i service bus och aktivera **alternativet Tillåt åtkomst från valda** nätverk. Under **undantagslistan** markerar du kryssrutan för Tillåt betrodda **användare Microsoft-tjänster komma åt den här Service Bus.** Klicka på knappen **Spara**.

4. På IoT Hub resurssidan går du till fliken **Meddelanderoutning.**

5. Gå till **avsnittet Anpassade slutpunkter** och klicka på Lägg **till**. Välj **Service Bus-kö** **eller Service Bus ämne** (om tillämpligt) som slutpunktstyp.

6. På sidan som visas anger du ett namn för slutpunkten, väljer service bus-namnområdet och kö eller ämne (om tillämpligt). Välj **Identitetsbaserad** som **Autentiseringstyp** och klicka på **knappen** Skapa.

Nu är din anpassade Service Bus-slutpunkt konfigurerad för att använda hubbens systemtilldeade identitet, och den har behörighet att komma åt Din Service Bus-resurs trots dess brandväggsbegränsningar. Nu kan du använda den här slutpunkten för att konfigurera en routningsregel.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Utgående anslutning till lagringskonton för filuppladdning

IoT Hub filuppladdningsfunktionen gör att enheter kan ladda upp filer till ett kundägt lagringskonto. För att filuppladdningen ska fungera måste både enheter IoT Hub ha anslutning till lagringskontot. Om det finns brandväggsbegränsningar på [lagringskontot](../private-link/tutorial-private-endpoint-storage-portal.md)måste enheterna använda någon av de lagringskontomekanismer som stöds (inklusive privata slutpunkter, tjänstslutpunkter eller direkt [](../storage/common/storage-network-security.md) [brandväggskonfiguration](../virtual-network/virtual-network-service-endpoints-overview.md)) för att få anslutning. Om det finns brandväggsbegränsningar på lagringskontot måste IoT Hub konfigureras för åtkomst till lagringsresursen via det betrodda Microsoft-tjänster undantag. För detta ändamål måste IoT Hub ha en hanterad identitet. När en hanterad identitet har etablerats följer du stegen nedan för att ge Azure RBAC-behörighet till hubbens resursidentitet för att få åtkomst till ditt lagringskonto.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. I Azure Portal du till fliken Åtkomstkontroll **(IAM)** för **lagringskontot** och klickar på Lägg till **under** avsnittet Lägg till en rolltilldelning.

2. Välj **Storage Blob Data-deltagare** (inte deltagare eller [ Lagringskontodeltagare)](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)som roll  **,** **Azure AD-användare,** grupp eller tjänstens huvudnamn som Tilldelar åtkomst till och välj resursnamnet för din IoT Hub i listrutan. Klicka på knappen **Spara**.

3. Gå till fliken **Brandväggar och virtuella nätverk i** lagringskontot och aktivera alternativet Tillåt åtkomst från **valda** nätverk. Under **undantagslistan** markerar du kryssrutan för Tillåt betrodda **användare Microsoft-tjänster komma åt det här lagringskontot.** Klicka på knappen **Spara**.

4. På IoT Hub resurssidan går du till fliken **Filuppladdning.**

5. På sidan som visas väljer du den container som du tänker använda i bloblagringen, konfigurerar filaviseringsinställningarna, **SAS TTL,** **standard-TTL** och **maximalt antal** leveranser efter behov. Välj **Identitetsbaserad som** **Autentiseringstyp till** lagringsslutpunkten. Klicka på knappen **Skapa**. Om du får ett felmeddelande i det här steget kan du tillfälligt ange lagringskontot så att det tillåter åtkomst från **Alla nätverk** och försök sedan igen. Du kan konfigurera brandväggen på lagringskontot när konfigurationen för filuppladdning är klar.

Nu är lagringsslutpunkten för filuppladdning konfigurerad att använda hubbens systemtilldelningsidentitet och den har behörighet att komma åt lagringsresursen trots dess brandväggsbegränsningar.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Utgående anslutning till lagringskonton för massimport/massexport av enheter

IoT Hub funktioner för att [importera/exportera](./iot-hub-bulk-identity-mgmt.md) enheters information i grupp från/till en lagringsblob som tillhandahålls av kunden. För att massimport/massexport ska fungera måste både enheter och IoT Hub ha anslutning till lagringskontot.

Den här funktionen kräver anslutning IoT Hub till lagringskontot. För att få åtkomst till en Service Bus-resurs när brandväggsbegränsningar finns måste IoT Hub ha en hanterad identitet. När en hanterad identitet har etablerats följer du stegen nedan för att ge Azure RBAC-behörighet till hubbens resursidentitet för att få åtkomst till din Service Bus.

1. I Azure Portal du till fliken Åtkomstkontroll **(IAM)** för lagringskontot och klickar på Lägg till **under** avsnittet Lägg till **en rolltilldelning.**

2. Välj **Storage Blob Data Contributor** (inte Deltagare eller [ Lagringskontodeltagare)](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)som roll  **,** **Azure AD-användare,** grupp eller tjänstens huvudnamn som Tilldelar åtkomst till och välj resursnamnet för din IoT Hub i listrutan. Klicka på knappen **Spara**.

3. Gå till fliken **Brandväggar och virtuella nätverk i** ditt lagringskonto och aktivera alternativet Tillåt åtkomst från **valda** nätverk. I listan **Undantag** markerar du kryssrutan för Tillåt betrodda **användare Microsoft-tjänster åtkomst till det här lagringskontot.** Klicka på knappen **Spara**.

Nu kan du använda Azure IoT REST-API:er för att skapa [importexportjobb](/rest/api/iothub/service/jobs/getimportexportjobs) för information om hur du använder massimport-/exportfunktionen. Du måste ange i begärandetexten och använda och `storageAuthenticationType="identityBased"` `inputBlobContainerUri="https://..."` som indata- respektive `outputBlobContainerUri="https://..."` utdata-URL:er för ditt lagringskonto.

Azure IoT Hub har även stöd för den här funktionen i tjänstklientens registerhanterare. Följande kodfragment visar hur du initierar ett importjobb eller exporterar jobb i med hjälp av C# SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

Så här använder du den här versionen av Azure IoT-SDK:er med stöd för virtuella nätverk för C#, Java och Node.js:

1. Skapa en miljövariabel med `EnableStorageIdentity` namnet och ange dess värde till `1` .

2. Ladda ned SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
För Python laddar du ned vår begränsade version från GitHub.

1. Gå till [GitHub-versionssidan](https://aka.ms/vnetpythonsdk).

2. Ladda ned följande fil, som du hittar längst ned på versionssidan under rubriken med namnet **assets**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Öppna en terminal och navigera till mappen med den nedladdade filen.

4. Kör följande kommando för att installera Python Service SDK med stöd för virtuella nätverk:
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Nästa steg

Använd länkarna nedan om du vill veta mer om IoT Hub funktioner:

* [Meddelanderoutning](./iot-hub-devguide-messages-d2c.md)
* [Filuppladdning](./iot-hub-devguide-file-upload.md)
* [Massimport/massexport av enheter](./iot-hub-bulk-identity-mgmt.md)
