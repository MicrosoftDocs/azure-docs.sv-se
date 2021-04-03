---
title: 'Snabb start: skapa och ändra en ExpressRoute-krets: Azure CLI'
description: Den här snabb starten visar hur du skapar, etablerar, verifierar, uppdaterar, tar bort och avetablerar en ExpressRoute-krets med Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91969100"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Snabb start: skapa och ändra en ExpressRoute-krets med Azure CLI

I den här snabb starten beskrivs hur du skapar en Azure ExpressRoute-krets med hjälp av kommando rads gränssnittet (CLI). Den här artikeln visar också hur du kontrollerar status, uppdaterar eller tar bort och avetablerar en krets.

## <a name="prerequisites"></a>Förutsättningar

* Granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installera den senaste versionen av CLI-kommandona (2,0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration

Logga in på ditt Azure-konto för att påbörja konfigurationen. Om du använder Cloud Shell "testa" är du inloggad automatiskt. Använd följande exempel för att ansluta:

```azurecli-interactive
az login
```

Kontrollera prenumerationerna för kontot.

```azurecli-interactive
az account list
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets för.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Hämta listan över providers, platser och bandbredder som stöds

Innan du skapar en ExpressRoute-krets behöver du en lista över anslutnings leverantörer, platser och bandbredds alternativ som stöds. CLI-kommandot `az network express-route list-service-providers` returnerar den här informationen, som du kommer att använda i senare steg:

```azurecli-interactive
az network express-route list-service-providers
```

Svaret liknar följande exempel:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Kontrol lera svaret för att se om din anslutnings leverantör visas. Anteckna följande information som du behöver när du skapar en krets:

* Name
* PeeringLocations
* BandwidthsOffered

Nu är du redo att skapa en ExpressRoute-krets.

### <a name="create-an-expressroute-circuit"></a>Skapa en ExpressRoute-krets

> [!IMPORTANT]
> Din ExpressRoute-krets debiteras från den tidpunkt då en tjänst nyckel utfärdas. Utför den här åtgärden när anslutnings leverantören är redo att etablera kretsen.
>
>

Om du inte redan har en resurs grupp måste du skapa en innan du skapar din ExpressRoute-krets. Du kan skapa en resurs grupp genom att köra följande kommando:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

I följande exempel visas hur du skapar en 200-Mbit/s ExpressRoute-krets via Equinix i Silicon dal. Om du använder en annan provider och andra inställningar ersätter du den informationen när du gör din begäran.

Se till att du anger rätt SKU-nivå och SKU-serien:

* SKU-nivån avgör om en ExpressRoute-krets är [lokal](expressroute-faqs.md#expressroute-local), standard eller [Premium](expressroute-faqs.md#expressroute-premium). Du kan ange *lokal*, * standard eller *Premium*. Du kan inte ändra SKU: n från *Standard-/Premium* till *lokal*.
* SKU-serien bestämmer fakturerings typen. Du kan ange *MeteredData* för ett data abonnemang med datapriser och *UnlimitedData* för ett obegränsat data abonnemang. Du kan ändra fakturerings typen från *MeteredData* till *UnlimitedData*, men du kan inte ändra typen från *UnlimitedData* till *MeteredData*. En *lokal* krets är endast *UnlimitedData* .


Din ExpressRoute-krets debiteras från den tidpunkt då en tjänst nyckel utfärdas. Följande exempel är en begäran om en ny tjänst nyckel:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Svaret innehåller tjänst nyckeln.

### <a name="list-all-expressroute-circuits"></a>Visa en lista över alla ExpressRoute-kretsar

Om du vill hämta en lista över alla ExpressRoute-kretsar som du har skapat kör du `az network express-route list` kommandot. Du kan hämta den här informationen när som helst genom att använda det här kommandot. Om du vill visa alla kretsar gör du ett anrop utan parametrar.

```azurecli-interactive
az network express-route list
```

Din tjänst nyckel visas i fältet *serviceKey* i svaret.

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Du kan få detaljerade beskrivningar av alla parametrar genom att köra kommandot med parametern "-h".

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Skicka tjänst nyckeln till din anslutnings leverantör för etablering

"ServiceProviderProvisioningState" innehåller information om det aktuella etablerings läget på tjänstens leverantörs sida. Statusen ger tillståndet på Microsoft-sidan. Mer information finns i artikeln om [arbets flöden](expressroute-workflows.md#expressroute-circuit-provisioning-states).

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Kretsen ändras till följande tillstånd när anslutnings leverantören för närvarande aktiverar den åt dig:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Om du vill använda ExpressRoute-kretsen måste den vara i följande tillstånd:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Kontrol lera regelbundet status och tillståndet för krets nyckeln

Genom att kontrol lera status och tillståndet för tjänst nyckeln kan du se när din-provider har allokerat din krets. När kretsen har kon figurer ATS visas *ServiceProviderProvisioningState* som *etablerad*, som du ser i följande exempel:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Svaret liknar följande exempel:

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="create-your-routing-configuration"></a>Skapa konfigurationen för routning

Stegvisa instruktioner finns i [ExpressRoute-kretsens konfigurations](howto-routing-cli.md) artikel för att skapa och ändra krets-peering.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med tjänst leverantörer som erbjuder Layer 2-anslutningstjänster. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis ett IP-VPN, som MPLS) konfigurerar och hanterar din anslutnings leverantör routning åt dig.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Länka ett virtuellt nätverk till en ExpressRoute-krets

Länka sedan ett virtuellt nätverk till din ExpressRoute-krets. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar](howto-linkvnet-cli.md) .

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan göra följande ändringar utan avbrott:

* Du kan aktivera eller inaktivera ett ExpressRoute Premium-tillägg för din ExpressRoute-krets. Det finns inte stöd för att ändra SKU: n från *Standard/Premium* till *lokal* .
* Du kan öka bandbredden för din ExpressRoute-krets om det finns tillgänglig kapacitet för porten. Nedgradering av en kretss bandbredd stöds dock inte.
* Du kan ändra Mät planen från datapriser till obegränsade data. Det går dock inte att ändra Mät planen från obegränsade data till datapriser.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Mer information om begränsningar och begränsningar finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aktivera ExpressRoute Premium-tillägget

Du kan aktivera ExpressRoute Premium-tillägget för din befintliga krets genom att använda följande kommando:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Kretsen har nu ExpressRoute Premium-tillägg aktiverade. Vi börjar med att debitera dig för Premium-tilläggs kapaciteten så snart kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Så här inaktiverar du ExpressRoute Premium-tillägget

> [!IMPORTANT]
> Den här åtgärden kan inte utföras om du använder resurser som är större än vad som tillåts för standard kretsen.
>
>

Innan du inaktiverar ExpressRoute Premium-tillägget förstår du följande kriterier:

* Innan du nedgraderar från Premium till standard måste du se till att antalet virtuella nätverk som är länkade till kretsen är mindre än 10. Om du inte gör det Miss lyckas din uppdateringsbegäran och vi debiteras till Premium priser.
* Alla virtuella nätverk i andra politiska regioner måste först avbrytas. Om du inte tar bort länken kommer din uppdateringsbegäran att Miss beta och vi fortsätter att fakturera dig till Premium-priser.
* Routningstabellen måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell storlek är större än 4 000 vägar tas BGP-sessionen bort. BGP-sessionen aktive ras inte igen förrän antalet annonserade prefix är under 4 000.

Du kan inaktivera ExpressRoute Premium-tillägget för den befintliga kretsen med hjälp av följande exempel:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Så här uppdaterar du ExpressRoute-kretsens bandbredd

För de bandbredds alternativ som stöds för din Provider, se [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md). Du kan välja valfri storlek som är större än den befintliga kretsens storlek.

> [!IMPORTANT]
> Om det finns otillräcklig kapacitet på den befintliga porten kan du behöva återskapa ExpressRoute-kretsen. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Med degradering av bandbredd måste du avetablera ExpressRoute-kretsen och sedan etablera om en ny ExpressRoute-krets.
>

När du har bestämt vilken storlek du behöver använder du följande kommando för att ändra storlek på kretsen:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Din krets kommer att uppgraderas på Microsoft-sidan. Sedan måste du kontakta din anslutnings leverantör för att uppdatera konfigurationerna på sidan för att matcha den här ändringen. När du har gjort den här aviseringen börjar vi debitera dig för alternativet uppdaterad bandbredd.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Flytta SKU: n från avgiftsbelagd till obegränsad

Du kan ändra SKU: n för en ExpressRoute-krets med hjälp av följande exempel:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Så här kontrollerar du åtkomsten till de klassiska och Resource Manager-miljöerna

Läs anvisningarna i [Flytta ExpressRoute-kretsar från den klassiska distributions modellen till Resource Manager](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Avetablera en ExpressRoute-krets

För att avetablera och ta bort en ExpressRoute-krets, se till att du förstår följande kriterier:

* Alla virtuella nätverk måste tas bort från ExpressRoute-kretsen. Om den här åtgärden Miss lyckas kontrollerar du om det finns några virtuella nätverk som är länkade till kretsen.
* Om etablerings statusen för ExpressRoute-kretsen **etableras** eller **etableras** måste du arbeta med tjänst leverantören för att avetablera kretsen på sin sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänste leverantören har slutfört avetableringen av kretsen och meddelar oss oss.
* Om tjänste leverantören har avetablerat kretsen som tjänst leverantörens etablerings status är inställt på **inte etablerad**, kan du ta bort kretsen. Faktureringen för kretsen avbryts sedan.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort ExpressRoute-kretsen genom att köra följande kommando:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din krets och etablerar den med leverantören fortsätter du till nästa steg för att konfigurera peer koppling:

> [!div class="nextstepaction"]
> [Skapa och ändra routning för din ExpressRoute-krets](howto-routing-cli.md)
