---
title: Konfigurera IP-brandvägg för Azure Relay namnrymd
description: Den här artikeln beskriver hur du använder brand Väggs regler för att tillåta anslutningar från vissa IP-adresser till Azure Relay namn områden.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ad8feed5df49dcc4503226a5fae50195bb9d48aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91999497"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Konfigurera IP-brandvägg för ett Azure Relay-namnområde
Som standard är relä namn rymder tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Den här funktionen är användbar i scenarier där Azure Relay bör endast vara tillgängliga från vissa välkända webbplatser. Med brand Väggs regler kan du konfigurera regler för att acceptera trafik som kommer från vissa IPv4-adresser. Om du till exempel använder relä med [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), kan du skapa en **brand Väggs regel** som tillåter trafik från enbart lokala infrastruktur-IP-adresser. 


> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. 


## <a name="enable-ip-firewall-rules"></a>Aktivera IP-brandväggens regler
IP-brandväggens regler tillämpas på namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Alla anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel i namn området nekas som obehörig. Svaret innehåller ingen IP-regel. IP filter regler tillämpas i ordning och den första regeln som matchar IP-adressen avgör vilken åtgärd som godkänns eller nekas.

### <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet visar hur du använder Azure Portal för att skapa IP-brandvägg för ett namn område. 

1. Navigera till ditt **relä namn område** i [Azure Portal](https://portal.azure.com).
2. Välj alternativet **nätverk** på den vänstra menyn. Om du väljer alternativet **alla nätverk** i avsnittet  **Tillåt åtkomst från** accepterar relä namn området anslutningar från alla IP-adresser. Den här inställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0. 

    ![Skärm bild som visar sidan nätverk med alternativet alla nätverk valt.](./media/ip-firewall/all-networks-selected.png)
1. Om du vill begränsa åtkomsten till vissa nätverk och IP-adresser väljer du alternativet **valda nätverk** . I avsnittet **brand vägg** följer du dessa steg:
    1. Välj alternativet **Lägg till klientens IP-adress** för att ge din aktuella klient-IP åtkomst till namn området. 
    2. För **adress intervall** anger du en angiven IPv4-adress eller ett intervall med IPv4-adresser i CIDR-notering. 

        ![Brand vägg – alternativet alla nätverk är valt](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Spara inställningarna genom att välja **Spara** i verktygsfältet. Vänta några minuter tills bekräftelsen visas på Portal meddelandena.


### <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar
Följande Resource Manager-mall gör det möjligt att lägga till en IP-filterlista till ett befintligt relä namn område.

Mallen tar en parameter: **ipMask**, som är en enskild IPv4-adress eller ett block med IP-adresser i CIDR-format. I CIDR-notation 70.37.104.0/24 representerar till exempel 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255, med 24 som anger antalet signifikanta prefix för intervallet.

> [!NOTE]
> Även om det inte finns några tillåtna nekade regler, har Azure Resource Manager mal len standard åtgärden inställd på **Tillåt** , vilket inte begränsar anslutningar.
> När du skapar Virtual Network-eller brand Väggs regler måste vi ändra ***"defaultAction"***
> 
> Från
> ```json
> "defaultAction": "Allow"
> ```
> på
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.Relay/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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
          "virtualNetworkRules": [],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Följ anvisningarna för [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)om du vill distribuera mallen.



## <a name="next-steps"></a>Nästa steg
Mer information om andra funktioner för nätverks säkerhet finns i [nätverks säkerhet](network-security.md).


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
