---
title: Växla eller växla distributioner i Azure Cloud Services (utökat stöd)
description: Lär dig hur du växlar mellan distributioner i Azure Cloud Services (utökat stöd).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748846"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Växla eller växla distributioner i Azure Cloud Services (utökat stöd)

Du kan växla mellan två oberoende molntjänstdistributioner i Azure Cloud Services (utökat stöd). Till skillnad Azure Cloud Services (klassisk) använder Azure Resource Manager-modellen i Azure Cloud Services (utökat stöd) inte distributionsfack. I Azure Cloud Services (utökat stöd) kan du göra molntjänsten "växlingsbar" med en befintlig molntjänst i Azure Cloud Services (utökat stöd) när du distribuerar en ny version av en molntjänst.

När du har växlat distributionerna kan du mellanfasa och testa den nya versionen med hjälp av den nya molntjänstdistributionen. Växlingen främjar i praktiken en ny molntjänst som mellanlagring sker i produktionsutgåvningen.

> [!NOTE]
> Du kan inte växla mellan en Azure Cloud Services (klassisk) distribution och en Azure Cloud Services (utökat stöd) distribution.

Du måste göra så att en molntjänst kan växlas med en annan molntjänst när du distribuerar den andra av ett par molntjänster.

Du kan växla distributionerna med hjälp av en Azure Resource Manager mall (ARM-mall), Azure Portal eller REST API.

## <a name="arm-template"></a>ARM-mall

Om du använder en distributionsmetod för ARM-mallar för att göra molntjänsterna utbytbara anger du egenskapen i i -objektet till ID:t för den `SwappableCloudService` `networkProfile` `cloudServices` parkopplade molntjänsten:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure Portal

Så här växlar du en distribution Azure Portal:

1. I portalmenyn väljer du Cloud Services **(utökat stöd) eller** **Instrumentpanel.**
1. Välj den molntjänst som du vill uppdatera.
1. I **Översikt** för molntjänsten väljer du **Växla**:

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Skärmbild som visar växlingsfliken för molntjänsten.":::

1. I fönstret för att bekräfta växlingen verifierar du distributionsinformationen och väljer **sedan OK** för att växla distributionerna:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Skärmbild som visar bekräftelse av distributionsväxlingsinformationen.":::

Distributioner växlar snabbt eftersom det enda som ändras är den virtuella IP-adressen för den molntjänst som distribueras.

För att minska beräkningskostnaderna kan du ta bort en av molntjänsterna (som anges som en mellanlagringsmiljö för programdistributionen) när du har verifierat att den växlade molntjänsten fungerar som förväntat.

## <a name="rest-api"></a>REST-API

Om du vill REST API till att växla till en ny distribution av molntjänster i Azure Cloud Services (utökat stöd) använder du följande kommando och JSON-konfiguration:

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
}
```

## <a name="common-questions-about-swapping-deployments"></a>Vanliga frågor om att växla distributioner

Läs dessa svar på vanliga frågor om distributionsväxlingar i Azure Cloud Services (utökad support).

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Vilka är kraven för att växla till en ny distribution av molntjänster?

Du måste uppfylla två viktiga krav för en lyckad distributionsväxling i Azure Cloud Services (utökat stöd):

* Om du vill använda en statisk eller reserverad IP-adress för en av de växlingsbara molntjänsterna måste den andra molntjänsten också använda en reserverad IP-adress. Annars misslyckas växlingen.
* Alla instanser av dina roller måste köras för att växlingen ska lyckas. Om du vill kontrollera statusen för dina instanser går  du Azure Portal till Översikt för den nyligen distribuerade molntjänsten eller använder kommandot `Get-AzRole` i Windows PowerShell.

Uppdateringar av gästoperativsystem och tjänstläsningsåtgärder kan orsaka att ett distributionsbyte misslyckas. Mer information finns i [Felsöka distributioner av molntjänster.](../cloud-services/cloud-services-troubleshoot-deployment-problems.md)

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Kan jag göra ett VIP-byte parallellt med en annan muteringsåtgärd?

Nej. En VIP-växling är en nätverksändring som måste slutföras innan någon annan beräkningsåtgärd startas i en molntjänst. Om du startar en uppdaterings-, borttagnings- eller autoskalningsåtgärd för en molntjänst medan en VIP-växling pågår eller utlöser en VIP-växling medan en annan beräkningsåtgärd pågår kan det leda till att molntjänsten får ett oåterkalleligt fel.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Uppstår ett växlingsavbrott för mitt program och hur ska jag hantera det?

Ett molntjänstbyte går vanligtvis snabbt eftersom det bara är en konfigurationsändring i Azure Load Balancer. I vissa fall kan växlingen ta 10 eller fler sekunder och resultera i tillfälliga anslutningsfel. Överväg att implementera logik för klientförsök för att begränsa effekten av bytet på användarna.

## <a name="next-steps"></a>Nästa steg 

* Granska [distributionens krav](deploy-prerequisite.md) för Azure Cloud Services (utökat stöd).
* Läs [vanliga frågor och svar](faq.md) om Azure Cloud Services (utökad support).
* Distribuera en Azure Cloud Services (utökat stöd) molntjänst med något av följande alternativ:
  * [Azure-portalen](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [ARM-mall](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
