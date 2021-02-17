---
title: Konfigurera IP brand Väggs regler för Azure Service Bus
description: Hur du använder brand Väggs regler för att tillåta anslutningar från vissa IP-adresser till Azure Service Bus.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: e73f566533cb2357653f7f584ec9ca77333c0a63
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560868"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Tillåt åtkomst till Azure Service Bus namnrymd från vissa IP-adresser eller intervall
Som standard är Service Bus-namnrymder tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Den här funktionen är användbar i scenarier där Azure Service Bus bör endast vara tillgängliga från vissa välkända webbplatser. Med brand Väggs regler kan du konfigurera regler för att acceptera trafik som kommer från vissa IPv4-adresser. Om du till exempel använder Service Bus med [Azure Express Route][express-route], kan du skapa en **brand Väggs regel** som tillåter trafik från enbart den lokala infrastrukturens IP-adresser eller adresser för en Corporate NAT-gateway. 

> [!IMPORTANT]
> - Brand väggar och virtuella nätverk stöds endast på **Premium** -nivån för Service Bus. Om du inte väljer att uppgradera till **Premier** -nivån rekommenderar vi att du behåller signaturen för signaturen för delad åtkomst (SAS) och dela den med endast behöriga användare. Information om SAS-autentisering finns i [autentisering och auktorisering](service-bus-authentication-and-authorization.md#shared-access-signature).
> - Ange minst en IP-regel eller en regel för virtuella nätverk för namn området för att tillåta trafik enbart från de angivna IP-adresserna eller under nätet för ett virtuellt nätverk. Om det inte finns några IP-och virtuella nätverks regler kan namn området nås via det offentliga Internet (med hjälp av åtkomst nyckeln).  

## <a name="ip-firewall-rules"></a>Regler för IP-brandvägg
IP-brandväggens regler tillämpas på Service Bus namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Eventuella anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Service Bus namn området nekas som obehörig. Svaret innehåller ingen IP-regel. IP filter regler tillämpas i ordning och den första regeln som matchar IP-adressen avgör vilken åtgärd som godkänns eller nekas.

Implementering av brand Väggs regler kan förhindra att andra Azure-tjänster interagerar med Service Bus. Som ett undantag kan du tillåta åtkomst till Service Bus resurser från vissa betrodda tjänster även när IP-filtrering är aktive rad. En lista över betrodda tjänster finns i [betrodda tjänster](#trusted-microsoft-services). 

Följande Microsoft-tjänster måste finnas i ett virtuellt nätverk
- Azure App Service
- Azure Functions

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet visar hur du använder Azure Portal för att skapa IP-brandvägg för ett Service Bus namn område. 

1. Navigera till **Service Bus namn området** i [Azure Portal](https://portal.azure.com).
2. På den vänstra menyn väljer du alternativet **nätverk** under **Inställningar**.  

    > [!NOTE]
    > Fliken **nätverk** visas endast för **Premium** -namnområden.  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Sidan nätverk – standard" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Om du väljer alternativet **alla nätverk** accepterar Service Bus namn området anslutningar från alla IP-adresser. Standardvärdet motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0. 

    ![Skärm bild av sidan Azure Portal nätverk. Alternativet för att tillåta åtkomst från alla nätverk väljs på fliken brand väggar och virtuella nätverk.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Om du bara vill tillåta åtkomst från angiven IP-adress väljer du alternativet **valda nätverk** om det inte redan är valt. I avsnittet **brand vägg** följer du dessa steg:
    1. Välj alternativet **Lägg till klientens IP-adress** för att ge din aktuella klient-IP åtkomst till namn området. 
    2. För **adress intervall** anger du en angiven IPv4-adress eller ett intervall med IPv4-adresser i CIDR-notering. 
    3. Ange om du vill **tillåta att betrodda Microsoft-tjänster kringgår den här brand väggen**. 

        >[!WARNING]
        > Om du väljer alternativet **valda nätverk** och inte lägger till minst en IP-brandväggsregel eller ett virtuellt nätverk på den här sidan, kan namn området nås via offentliga Internet (med hjälp av åtkomst nyckeln).    

        ![Skärm bild av sidan Azure Portal nätverk. Alternativet att tillåta åtkomst från valda nätverk är markerat och brand Väggs avsnittet är markerat.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Spara inställningarna genom att välja **Spara** i verktygsfältet. Vänta några minuter tills bekräftelsen visas på Portal meddelandena.

    > [!NOTE]
    > Information om hur du begränsar åtkomsten till vissa virtuella nätverk finns i [Tillåt åtkomst från vissa nätverk](service-bus-service-endpoints.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar
Det här avsnittet innehåller ett exempel på en Azure Resource Manager mall som lägger till ett virtuellt nätverk och en brand Väggs regel i ett befintligt Service Bus-namnområde.

**ipMask** är en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation 70.37.104.0/24 representerar till exempel 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255, med 24 som anger antalet signifikanta prefix för intervallet.

När du lägger till regler för virtuella nätverk eller brand väggar ställer du in värdet `defaultAction` till `Deny` .


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Följ anvisningarna för [Azure Resource Manager][lnk-deploy]om du vill distribuera mallen.

> [!IMPORTANT]
> Om det inte finns några IP-och virtuella nätverks regler, flödar all trafik till namn området även om du ställer in `defaultAction` till `deny` . Namn området kan nås via det offentliga Internet (med hjälp av åtkomst nyckeln). Ange minst en IP-regel eller en regel för virtuella nätverk för namn området för att tillåta trafik enbart från de angivna IP-adresserna eller under nätet för ett virtuellt nätverk.  


## <a name="next-steps"></a>Nästa steg

Information om hur du begränsar åtkomsten till Service Bus till virtuella Azure-nätverk finns i följande länk:

- [Virtual Network tjänst slut punkter för Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services