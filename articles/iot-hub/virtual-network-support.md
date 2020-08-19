---
title: Azure IoT Hub stöd för virtuella nätverk
description: Så här använder du anslutnings mönster för virtuella nätverk med IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: jlian
ms.openlocfilehash: 3c097260812e72dfaa3678a4aade556a337e6a6c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272908"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub stöd för virtuella nätverk med privat länk och hanterad identitet

Som standard mappas IoT Hub-värdnamn till en offentlig slut punkt med en offentligt dirigerad IP-adress via Internet. Olika kunder delar den här IoT Hub offentliga slut punkten och IoT-enheter i över WAN-nätverk och lokala nätverk kan komma åt dem.

![IoT Hub offentlig slut punkt](./media/virtual-network-support/public-endpoint.png)

IoT Hub funktioner som [meddelanderoutning,](./iot-hub-devguide-messages-d2c.md) [fil uppladdning](./iot-hub-devguide-file-upload.md)och [Mass import/export](./iot-hub-bulk-identity-mgmt.md) kräver även anslutning från IoT Hub till en Azure-resurs som ägs av en kund via den offentliga slut punkten. Dessa anslutnings vägar utgör tillsammans den utgående trafiken från IoT Hub till kund resurser.

Du kanske vill begränsa anslutningen till dina Azure-resurser (inklusive IoT Hub) via ett virtuellt nätverk som du äger och använder. Dessa orsaker är:

* Introduktion till nätverks isolering för din IoT-hubb genom att förhindra anslutningens exponering för det offentliga Internet.

* Att aktivera en privat anslutning från dina lokala nätverks resurser och se till att dina data och trafik skickas direkt till Azure stamnät nätverket.

* Förhindra exfiltrering attacker från känsliga lokala nätverk. 

* Följande etablerade Azure-wide-anslutnings mönster använder [privata slut punkter](../private-link/private-endpoint-overview.md).

Den här artikeln beskriver hur du uppnår dessa mål med hjälp av en [privat Azure-länk](../private-link/private-link-overview.md) för ingångs anslutning till IoT Hub och använder ett betrott Microsoft Services-undantag för utgående anslutning från IoT Hub till andra Azure-resurser.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Ingress-anslutning till IoT Hub med hjälp av en privat Azure-länk

En privat slut punkt är en privat IP-adress som tilldelas i ett kundägda VNet via vilken en Azure-resurs kan kontaktas. Via Azures privata länk kan du konfigurera en privat slut punkt för din IoT-hubb så att tjänsterna i ditt VNet kan uppnå IoT Hub utan att trafik måste skickas till IoT Hub offentliga slut punkten. På samma sätt kan dina lokala enheter använda [virtuella privata nätverk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -peering för att få anslutning till ditt VNet och din IoT Hub (via dess privata slut punkt). Det innebär att du kan begränsa eller helt blockera anslutningen till din IoT Hub offentliga slut punkter genom att använda [IoT Hub IP-filter](./iot-hub-ip-filtering.md) och [konfigurera routning så att inga data skickas till den inbyggda slut punkten](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint). Den här metoden ansluter till hubben med hjälp av den privata slut punkten för enheter. Huvud fokus för den här installationen är för enheter i ett lokalt nätverk. Den här installationen rekommenderas inte för enheter som distribueras i ett WAN-nätverk.

![IoT Hub offentlig slut punkt](./media/virtual-network-support/virtual-network-ingress.png)

Innan du fortsätter kontrollerar du att följande krav uppfylls:

* Du har [skapat ett Azure VNet](../virtual-network/quick-create-portal.md) med ett undernät som den privata slut punkten ska skapas i.

* För enheter som arbetar i lokala nätverk konfigurerar du [virtuella privata nätverk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privata peering i ditt Azure VNet.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Konfigurera en privat slut punkt för IoT Hub ingress

Privat slut punkt fungerar för IoT Hub enhets-API: er (t. ex. "enhet till molnet"-meddelanden) och service-API: er (som att skapa och uppdatera enheter

1. I Azure Portal väljer du **nätverk**, **anslutningar för privata slut punkter**och klickar på den **privata slut punkten**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Skärm bild som visar var du ska lägga till privat slut punkt för IoT Hub":::

1. Ange prenumeration, resurs grupp, namn och region för att skapa den nya privata slut punkten i. Vi rekommenderar att privat slut punkt skapas i samma region som hubben.

1. Klicka på **Nästa: resurs**och ange prenumerationen för din IoT Hub-resurs och välj **"Microsoft. Devices/IotHubs"** som resurs typ, ditt IoT Hub namn som **resurs**och **iotHub** som mål under resurs.

1. Klicka på **Nästa: konfiguration** och ange ditt virtuella nätverk och undernät för att skapa den privata slut punkten i. Välj alternativet att integrera med Azures privata DNS-zon, om så önskas.

1. Klicka på **Nästa: Taggar**och om du vill kan du även ange taggar för resursen.

1. Klicka på **Granska + skapa** för att skapa en privat länk resurs.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>Inbyggd Event Hub-kompatibel slut punkt stöder inte åtkomst över privat slut punkt

Den [inbyggda Event Hub-kompatibla slut punkten](iot-hub-devguide-messages-read-builtin.md) stöder inte åtkomst över privat slut punkt. När den har kon figurer ATS är en IoT Hub-privat slut punkt endast för inkommande anslutningar. Användning av data från inbyggd Event Hub-kompatibel slut punkt kan bara göras via det offentliga Internet. 

IoT Hubens [IP-filter](iot-hub-ip-filtering.md) styr också inte offentlig åtkomst till den inbyggda slut punkten. Om du vill blockera offentlig nätverks åtkomst fullständigt till din IoT-hubb måste du: 

1. Konfigurera åtkomst till privat slut punkt för IoT Hub
1. Inaktivera [offentlig nätverks åtkomst](iot-hub-public-network-access.md) eller Använd IP-filter för att blockera alla IP-adresser
1. Sluta använda den inbyggda Event Hub-slutpunkten genom att konfigurera [routning för att inte skicka data till den](iot-hub-devguide-messages-d2c.md)
1. Inaktivera [reserv vägen](iot-hub-devguide-messages-d2c.md#fallback-route)
1. Konfigurera utgående trafik till andra Azure-resurser med hjälp av [betrodda Microsoft-tjänster](#egress-connectivity-from-iot-hub-to-other-azure-resources)

### <a name="pricing-for-private-link"></a>Prissättning för privat länk

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Utgående anslutning från IoT Hub till andra Azure-resurser

IoT Hub kan ansluta till Azure Blob Storage, händelsehubben, Service Bus-resurser för [meddelanderoutning](./iot-hub-devguide-messages-d2c.md), [fil uppladdning](./iot-hub-devguide-file-upload.md)och [Mass import/export](./iot-hub-bulk-identity-mgmt.md) över resursernas offentliga slut punkt. Att binda din resurs till ett VNet blockerar anslutningen till resursen som standard. Det innebär att den här konfigurationen förhindrar att IoT Hub kommer att kunna skicka data till dina resurser. Åtgärda problemet genom att aktivera anslutning från din IoT Hub-resurs till ditt lagrings konto, Event Hub eller Service Bus-resurser via alternativet för den **betrodda Microsoft-tjänsten** .

### <a name="turn-on-managed-identity-for-iot-hub"></a>Aktivera hanterad identitet för IoT Hub

För att andra tjänster ska kunna hitta din IoT Hub som en betrodd Microsoft-tjänst, måste den ha en systemtilldelad hanterad identitet.

1. Navigera till **identitet** i IoT Hubs portalen

1. Under **status**väljer du **på**och klickar sedan på **Spara**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Skärm bild som visar hur du aktiverar hanterad identitet för IoT Hub":::

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Tilldela den hanterade identiteten till IoT Hub när den skapas med ARM-mall

Om du vill tilldela en hanterad identitet till din IoT-hubb vid resurs etablerings tiden använder du ARM-mallen nedan:

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
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
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

När du har angett värdena för din `name` resurs `location` , `SKU.name` och `SKU.tier` du kan använda Azure CLI för att distribuera resursen i en befintlig resurs grupp med hjälp av:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

När resursen har skapats kan du hämta den hanterade tjänst identiteten som tilldelats navet med Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Prissättning för hanterad identitet

Den betrodda Microsoft First parts Services-funktionen är kostnads fri. Avgifter för etablerade lagrings konton, händelse nav eller Service Bus-resurser gäller separat.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Utgående anslutning till lagrings konto slut punkter för routning

IoT Hub kan dirigera meddelanden till ett kundägda lagrings konto. För att routnings funktionen ska kunna komma åt ett lagrings konto medan brand Väggs begränsningarna är på plats måste IoT Hub ha en [hanterad identitet](#turn-on-managed-identity-for-iot-hub). När en hanterad identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för att komma åt ditt lagrings konto.

1. I Azure Portal navigerar du till ditt lagrings kontos **åtkomst kontroll (IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Storage BLOB data-deltagare** ([*inte* bidrags givare eller lagrings konto deltagare](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja IoT Hub resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i ditt lagrings konto och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan **Tillåt att betrodda Microsoft-tjänster får åtkomst till det här lagrings kontot**. Klicka på knappen **Spara**.

4. På resurs sidan för IoT Hub navigerar du till fliken **meddelanderoutning** .

5. Gå till avsnittet **anpassade slut punkter** och klicka på **Lägg till**. Välj **lagring** som typ av slut punkt.

6. På sidan som visas anger du ett namn för din slut punkt, väljer den behållare som du vill använda i blob-lagringen, ange kodning och fil namns format. Välj **identitets baserad** som **Autentiseringstyp** för lagrings slut punkten. Klicka på knappen **Skapa**.

Nu är din anpassade lagrings slut punkt inställd på att använda navets tilldelade identitet och har behörighet att komma åt lagrings resursen trots dess brand Väggs begränsningar. Du kan nu använda den här slut punkten för att ställa in en regel för routning.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Utgående anslutning till Event Hub-slutpunkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till en kundägda Event Hub-namnrymd. För att routnings funktionen ska kunna komma åt en resurs för händelse hubbar medan brand Väggs begränsningarna är på plats måste IoT Hub ha en hanterad identitet. När en hanterad identitet har skapats följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för att få åtkomst till dina händelse nav.

1. I Azure Portal navigerar du till fliken för Event Hub- **åtkomst kontroll (IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Event Hubs data avsändare** som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja din IoT Hubs resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i Event Hub och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan **Tillåt att betrodda Microsoft-tjänster får åtkomst till Event Hub**. Klicka på knappen **Spara**.

4. På resurs sidan för IoT Hub navigerar du till fliken **meddelanderoutning** .

5. Gå till avsnittet **anpassade slut punkter** och klicka på **Lägg till**. Välj **händelse nav** som typ av slut punkt.

6. Ange ett namn för din slut punkt på sidan som visas, Välj namn området för Event Hub och instansen och klicka på knappen **skapa** .

Nu konfigureras slut punkten för anpassade händelse nav till att använda navets tilldelade identitet och har behörighet att komma åt dina Event Hub-resurser trots dess brand Väggs begränsningar. Du kan nu använda den här slut punkten för att ställa in en regel för routning.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Utgående anslutning till Service Bus-slutpunkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till ett kundägda Service Bus-namnområde. För att routnings funktionen ska kunna få åtkomst till en Service Bus-resurs medan brand Väggs begränsningarna är på plats måste IoT Hub ha en hanterad identitet. När en hanterad identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för att få åtkomst till din Service Bus.

1. I Azure Portal navigerar du till fliken åtkomst kontroll för Service Bus **(IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Service Bus-avsändare** som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja din IoT Hubs resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i Service Bus och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan för **Tillåt att betrodda Microsoft-tjänster har åtkomst till den här Service Bus-tjänsten**. Klicka på knappen **Spara**.

4. På resurs sidan för IoT Hub navigerar du till fliken **meddelanderoutning** .

5. Gå till avsnittet **anpassade slut punkter** och klicka på **Lägg till**. Välj en **Service Bus-kö** eller ett **Service Bus ämne** (som tillämpligt) som typ av slut punkt.

6. På sidan som visas anger du ett namn för din slut punkt, väljer din Service Bus-namnrymd och kö eller ämne (om det är tillämpligt). Klicka på knappen **Skapa**.

Nu är din anpassade Service Bus-slutpunkt konfigurerad att använda navets tilldelade identitet och har behörighet att komma åt din Service Bus-resurs trots dess brand Väggs begränsningar. Du kan nu använda den här slut punkten för att ställa in en regel för routning.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Utgående anslutning till lagrings konton för fil uppladdning

Med IoT Hub fil överförings funktionen kan enheter Ladda upp filer till ett kundägda lagrings konto. För att tillåta fil uppladdning att fungera måste både enheter och IoT Hub ha anslutning till lagrings kontot. Om brand Väggs begränsningar finns på lagrings kontot, måste enheterna använda något av de lagrings konto funktioner som stöds (inklusive [privata slut punkter](../private-link/create-private-endpoint-storage-portal.md), [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md)eller [konfiguration av direkt brand vägg](../storage/common/storage-network-security.md)) för att få anslutning. Om brand Väggs begränsningarna finns på lagrings kontot måste IoT Hub konfigureras för åtkomst till lagrings resursen via det betrodda Microsoft Services-undantaget. För det här ändamålet måste IoT Hub ha en hanterad identitet. När en hanterad identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för att komma åt ditt lagrings konto.

1. I Azure Portal navigerar du till ditt lagrings kontos **åtkomst kontroll (IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Storage BLOB data-deltagare** ([*inte* bidrags givare eller lagrings konto deltagare](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja IoT Hub resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i ditt lagrings konto och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan **Tillåt att betrodda Microsoft-tjänster får åtkomst till det här lagrings kontot**. Klicka på knappen **Spara**.

4. På resurs sidan för IoT Hub navigerar du till fliken **fil uppladdning** .

5. På sidan som visas väljer du den behållare som du tänker använda i blob-lagringen, konfigurerar **inställningarna för fil meddelanden**, **SAS TTL**, standard- **TTL**och maximalt antal **leveranser** som önskas. Välj **identitets baserad** som **Autentiseringstyp** för lagrings slut punkten. Klicka på knappen **Skapa**.

Nu har din lagrings slut punkt för fil uppladdning kon figurer ATS för att använda navets tilldelade identitet och har behörighet att komma åt lagrings resursen trots dess brand Väggs begränsningar.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Utgående anslutning till lagrings konton för import/export av Mass enhet

IoT Hub stöder funktionerna för att [Importera/exportera](./iot-hub-bulk-identity-mgmt.md) enheters information i bulk från/till en lagrings-BLOB för kunden. För att tillåta Mass import/export-funktionen att fungera måste både enheter och IoT Hub ha anslutning till lagrings kontot.

Den här funktionen kräver anslutning från IoT Hub till lagrings kontot. För att få åtkomst till en Service Bus-resurs medan brand Väggs begränsningarna är på plats måste IoT Hub ha en hanterad identitet. När en hanterad identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för att få åtkomst till din Service Bus.

1. I Azure Portal navigerar du till ditt lagrings kontos **åtkomst kontroll (IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Storage BLOB data-deltagare** ([*inte* bidrags givare eller lagrings konto deltagare](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja IoT Hub resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i ditt lagrings konto och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan **Tillåt att betrodda Microsoft-tjänster får åtkomst till det här lagrings kontot**. Klicka på knappen **Spara**.

Nu kan du använda Azure IoT REST-API: erna för att [skapa import export jobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) för information om hur du använder Mass import/export-funktionen. Du måste ange `storageAuthenticationType="identityBased"` i din begär ande text och använda `inputBlobContainerUri="https://..."` och `outputBlobContainerUri="https://..."` som URL: er för indata och utdata för ditt lagrings konto.

Azure IoT Hub SDK: er har även stöd för den här funktionen i tjänst klientens register hanterare. Följande kodfragment visar hur du startar ett import jobb eller ett export jobb i med C# SDK.

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

Om du vill använda den här versionen av Azure IoT SDK: er med stöd för virtuella nätverk för C#, Java och Node.js:

1. Skapa en miljö variabel med namnet `EnableStorageIdentity` och ange dess värde till `1` .

2. Hämta SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
För python laddar du ned vår begränsade version från GitHub.

1. Gå till [sidan med GitHub-versionen](https://aka.ms/vnetpythonsdk).

2. Ladda ned följande fil, som du hittar längst ned på sidan version under rubriken namngivna **till gångar**.
    > *azure_iot_hub-2,2.0_limited-PY2. py3-none-any. WHL*

3. Öppna en Terminal och navigera till mappen med den nedladdade filen.

4. Kör följande kommando för att installera python service SDK med stöd för virtuella nätverk:
    > PIP install./azure_iot_hub-2,2.0_limited-PY2. py3-none-any. WHL


## <a name="next-steps"></a>Nästa steg

Använd länkarna nedan för att lära dig mer om IoT Hub funktioner:

* [Meddelanderoutning](./iot-hub-devguide-messages-d2c.md)
* [Fil uppladdning](./iot-hub-devguide-file-upload.md)
* [Import/export av Mass enhet](./iot-hub-bulk-identity-mgmt.md) 
