---
title: Hantera en privat slut punkts anslutning i Azure
description: Lär dig hur du hanterar privata slut punkts anslutningar i Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95531380"
---
# <a name="manage-a-private-endpoint-connection"></a>Hantera en privat slutpunktsanslutning
Azures privata länk fungerar på en flödes modell för godkännande av samtal där mottagare av den privata länk tjänsten kan begära en anslutning till tjänst leverantören för att konsumera tjänsten. Tjänste leverantören kan sedan bestämma om konsumenten ska tillåtas att ansluta eller inte. Med Azures privata länk kan tjänst leverantörer hantera den privata slut punkts anslutningen på sina resurser. Den här artikeln innehåller anvisningar om hur du hanterar anslutningar för privata slut punkter.

![Hantera privata slut punkter](media/manage-private-endpoint/manage-private-endpoint.png)

Det finns två metoder för godkännande av anslutningar som en privat länk tjänst konsument kan välja bland:
- **Automatisk**: om tjänste konsumenten har Azure RBAC-behörighet för tjänst leverantörs resursen, kan konsumenten välja metoden för automatiskt godkännande. I detta fall krävs ingen åtgärd från tjänst leverantören när begäran når tjänst leverantörs resursen och anslutningen godkänns automatiskt. 
- **Manuellt**: om tjänste konsumenten inte har Azure RBAC-behörighet för tjänst leverantörs resursen kan konsumenten välja metoden för manuellt godkännande. I det här fallet visas anslutnings förfrågan på tjänst resurserna som **väntande**. Tjänste leverantören måste godkänna begäran manuellt innan anslutningar kan upprättas. I manuella fall kan tjänste konsument också ange ett meddelande med begäran om att tillhandahålla mer kontext till tjänst leverantören. Tjänste leverantören har följande alternativ för att välja bland alla anslutningar för privata slut punkter: **godkänd**, **avvisa**, **ta bort**.

Tabellen nedan visar de olika tjänste leverantörs åtgärderna och de resulterande anslutnings tillstånden för privata slut punkter.  Tjänste leverantören kan också ändra anslutnings status för privat slut punkts anslutning vid ett senare tillfälle utan konsument åtgärder. Åtgärden kommer att uppdatera status för slut punkten på konsument sidan. 


|Tjänst leverantörs åtgärd   |Status för privat slut punkt för tjänst förbrukare   |Beskrivning   |
|---------|---------|---------|
|Ingen    |    Väntar     |    Anslutningen skapas manuellt och väntar på godkännande av resurs ägaren för den privata länken.       |
|Godkänn    |  Godkända       |  Anslutningen godkändes automatiskt eller manuellt och är redo att användas.     |
|Avvisa     | Avslagen        | Anslutningen avvisades av ägaren till den privata länk resursen.        |
|Ta bort    |  Frånkopplad       | Anslutningen togs bort av ägaren till den privata länk resursen, den privata slut punkten blir informativ och bör tas bort för rensning.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Hantera privata slutpunktsanslutningar på Azure PaaS-resurser
Portal är den bästa metoden för att hantera privata slut punkts anslutningar på Azure PaaS-resurser. För närvarande har vi inte PowerShell/CLI-stöd för hantering av anslutningar i Azure PaaS-resurser.
1. Logga in på Azure Portal på https://portal.azure.com.
2. Navigera till Private Link Center.
3. Under **resurser** väljer du den resurs typ som du vill hantera anslutningar för privata slut punkter.
4. För var och en av dina resurs typer kan du se hur många privata slut punkts anslutningar som är associerade med den. Du kan filtrera resurserna efter behov.
5. Välj anslutningar för privata slut punkter.  Under de anslutningar som visas väljer du den anslutning som du vill hantera. 
6. Du kan ändra status för anslutningen genom att välja bland alternativen överst.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Hantera anslutningar för privata slut punkter på en kund/partner som äger privat länk tjänst

Azure PowerShell och Azure CLI är de bästa metoderna för att hantera anslutningar för privata slut punkter på Microsoft partner tjänster eller kund ägda tjänster. För närvarande har vi inget Portal stöd för att hantera anslutningar i en privat länk-tjänst.  
 
### <a name="powershell"></a>PowerShell 
  
Använd följande PowerShell-kommandon för att hantera anslutningar för privata slut punkter.  
#### <a name="get-private-link-connection-states"></a>Hämta anslutnings tillstånd för privat anslutning 
Använd `Get-AzPrivateLinkService` cmdleten för att hämta anslutningar för privata slut punkter och deras tillstånd.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slut punkts anslutning 
 
Använd `Approve-AzPrivateEndpointConnection` cmdleten för att godkänna en privat slut punkts anslutning. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Neka privat slut punkts anslutning 
 
Använd `Deny-AzPrivateEndpointConnection` cmdleten för att avvisa en privat slut punkts anslutning. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Ta bort anslutning till privat slutpunkt 
 
Använd `Remove-AzPrivateEndpointConnection` cmdleten för att ta bort en privat slut punkts anslutning. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Använd `az network private-link-service update` för att hantera dina privata slut punkts anslutningar. Anslutnings status anges i ```azurecli connection-status``` parametern. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Nästa steg
- [Lär dig om privata slut punkter](private-endpoint-overview.md)
 
