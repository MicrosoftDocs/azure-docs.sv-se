---
title: Växling/växel mellan två Azure-Cloud Services (utökad support)
description: Växling/växel mellan två Azure-Cloud Services (utökad support)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294339"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Växling/växel mellan två Azure-Cloud Services (utökad support)
Med moln tjänster (utökad support) kan du växla mellan två oberoende moln tjänst distributioner. Till skillnad från moln tjänster (klassisk) finns inte begreppet platser med Azure Resource Manager modellen. När du bestämmer dig för att distribuera en ny version av en moln tjänst (utökad support) kan du göra det "utbytbar" med en annan befintlig moln tjänst (utökad support) så att du kan mellanlagra och testa den nya versionen med hjälp av den här distributionen. En moln tjänst kan göras "utbytbar" med en annan moln tjänst endast vid tidpunkten för distributionen av den andra moln tjänsten (av paret). När du använder den mallbaserade distributions metoden för ARM gör du detta genom att ange egenskapen SwappableCloudService i nätverks profilen för Cloud service-objektet till ID: t för den kopplade moln tjänsten. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> Det går inte att växla mellan en moln tjänst (klassisk) och en moln tjänst (utökad support)

Använd **Växla** för att växla de URL: er som de två moln tjänsterna riktas mot, i praktiken befordrar en ny moln tjänst (mellanlagrad) till produktions lansering.
Du kan växla distributioner från Cloud Services sidan eller instrument panelen.

1. I [Azure Portal](https://portal.azure.com)väljer du den moln tjänst som du vill uppdatera. Det här steget öppnar bladet moln tjänst instans.
2. På bladet väljer du **Byt** 
    :::image type="content" source="media/swap-cloud-service-1.png" alt-text="bild visar alternativet för att byta moln tjänst":::
   
3. Följande bekräftelse visas
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="Bild som visar utbyte av moln tjänsten":::
   
4. När du har kontrollerat distributions informationen väljer du OK för att växla distributioner.
Växlingen sker snabbt eftersom det enda som ändras är de virtuella IP-adresserna (VIP) för de två moln tjänsterna.

Om du vill spara beräknings kostnader kan du ta bort en av moln tjänsterna (som en mellanlagrings miljö för program distributionen) när du har kontrollerat att den utbytta moln tjänsten fungerar som förväntat.

REST-API: et för att utföra en växling mellan två distributioner av utökad support för moln tjänster är nedan:
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
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
```
## <a name="common-questions-about-swapping-deployments"></a>Vanliga frågor om hur du byter distributioner

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Vilka är kraven för utbyte mellan två moln tjänster?
Det finns två viktiga krav för en lyckad moln tjänst växling (utökad support):
* Om du vill använda en statisk/reserverad IP-adress för en av de växlings bara moln tjänsterna måste den andra moln tjänsten också använda en reserverad IP-adress. Annars Miss lyckas växlingen.
* Alla instanser av dina roller måste köras innan du kan utföra växlingen. Du kan kontrol lera status för dina instanser på bladet översikt i Azure Portal. Du kan också använda kommandot Get-AzRole i Windows PowerShell.

Uppdateringar av gäst operativ system och tjänst återställnings åtgärder kan även göra att distributions växlingar inte kan köras. Mer information finns i Felsöka problem med distribution av moln tjänster.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Kan jag göra en VIP-växling parallellt med en annan Mutations åtgärd?
Nej. VIP-växling är en nätverks ändring som måste slutföras innan någon annan beräknings åtgärd utförs på moln tjänsterna. Utföra en uppdatering, borttagning eller skalnings åtgärd på moln tjänsten (en) medan en VIP-växling pågår eller utlösa en VIP-växling medan en annan beräknings åtgärd pågår kan lämna moln tjänsten i ett osäkert tillstånd från vilket återställningen kanske inte är möjlig. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Uppstår avbrott för mitt program i byte? Hur ska jag hantera det?
Som det beskrivs i föregående avsnitt är en moln tjänst växling normalt snabb eftersom det bara är en konfigurations ändring i Azure Load Balancer. I vissa fall kan det ta 10 sekunder och leda till tillfälliga anslutnings problem. Om du vill begränsa påverkan till dina kunder kan du överväga att implementera logik för omprövning av klienter.

## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
