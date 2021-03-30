---
title: Flytta en extern Azure-belastningsutjämnare till en annan Azure-region med hjälp av Azure Portal
description: Använd en Azure Resource Manager-mall för att flytta en extern belastningsutjämnare från en Azure-region till en annan med hjälp av Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f83ff3d1d03354daef3466c1f48eaa505e378634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94693757"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Flytta en extern belastningsutjämnare till en annan region med hjälp av Azure Portal

Det finns olika scenarier där du vill flytta en extern belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en annan extern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en extern belastningsutjämnare till en annan region som en del av Disaster Recovery-planeringen.

I en litteral mening kan du inte flytta en extern Azure-belastningsutjämnare från en region till en annan. Men du kan använda en Azure Resource Manager mall för att exportera den befintliga konfigurationen och den offentliga IP-adressen till en extern belastningsutjämnare. Du kan sedan mellanlagra resursen i en annan region genom att exportera belastningsutjämnaren och offentliga IP-adresser till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen. Mer information om Resource Manager och mallar finns i [Exportera resurs grupper till mallar](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Förutsättningar

- Se till att den externa Azure-belastningsutjämnaren finns i den Azure-region som du vill flytta.

- Det går inte att flytta externa Azure-belastningsutjämnare mellan regioner. Du måste koppla den nya belastningsutjämnaren till resurser i mål regionen.

- Om du vill exportera en extern belastnings Utjämnings konfiguration och distribuera en mall för att skapa en extern belastningsutjämnare i en annan region måste du ha rollen som nätverks deltagare eller högre.

- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper, offentliga IP-adresser och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa externa belastningsutjämnare i mål regionen. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att lägga till belastnings utjämning. Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

## <a name="prepare-and-move"></a>Förbered och flytta
Följande procedurer visar hur du förbereder den externa belastningsutjämnaren för flytt med hjälp av en Resource Manager-mall och flyttar den externa belastnings Utjämnings konfigurationen till mål regionen med hjälp av Azure Portal. Du måste först exportera den offentliga IP-konfigurationen för den externa belastningsutjämnaren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportera den offentliga IP-mallen och distribuera den offentliga IP-adressen från portalen

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.
2. Leta upp resurs gruppen som innehåller den offentliga käll-IP-adressen och välj den.
3. Välj **Inställningar**  >  **Exportera mall**.
4. Välj **distribuera** under **Exportera mall**.
5. Välj **mall**  >  **Redigera parametrar** för att öppna parameters.jspå filen i redigeraren online.
8. Om du vill redigera parametern för det offentliga IP-namnet ändrar du egenskapen **Value** under **parametrar** från det offentliga IP-domännamnet till namnet på din mål-offentliga IP. Omge namnet med citat tecken.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Välj **Spara** i redigeraren.

9.  Välj **mall**  >  **Redigera mall** för att öppna template.jspå filen i redigeraren online.

10. Om du vill redigera den mål region som den offentliga IP-adressen ska flyttas till ändrar du egenskapen **location** under **resurser**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är regions namnet utan blank steg. Koden för centrala USA är till exempel **Central**.
    
12. Du kan också ändra andra parametrar i mallen om du vill eller behöver, beroende på dina krav:

    * **SKU**. Du kan ändra SKU: n för den offentliga IP-adressen i konfigurationen från standard till Basic eller från Basic till standard genom att ändra egenskapen **namn** under **SKU** i template.jspå filen:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Information om skillnaderna mellan grundläggande och standard-SKU: er i offentliga IP-adresser finns i [skapa, ändra eller ta bort en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md).

    * **Tilldelnings metod för offentlig IP** och **tids gräns för inaktivitet**. Du kan ändra metoden för offentlig IP-tilldelning genom att ändra egenskapen **publicIPAllocationMethod** från **dynamisk** till **statisk** eller från **statisk** till **dynamisk**. Du kan ändra tids gränsen för inaktivitet genom att ändra egenskapen **idleTimeoutInMinutes** till önskat värde. Standardvärdet är **4**.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Information om tilldelnings metoder och tids gräns värden för inaktivitet finns i [skapa, ändra eller ta bort en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md).

 
13. Välj **Spara** i redigeraren online.

14. Välj **grundläggande**  >  **prenumeration** för att välja den prenumeration där den offentliga mål-IP-adressen ska distribueras.

15. Välj   >  **resurs gruppen** grundläggande för att välja den resurs grupp där den offentliga mål-IP-adressen ska distribueras. Du kan välja **Skapa ny** för att skapa en ny resurs grupp för den offentliga IP-adressen. Kontrol lera att namnet inte är samma som käll resurs gruppen för den befintliga offentliga IP-adressen.

16. Kontrol lera att **grundläggande**  >  **platser** är inställt på den mål plats där du vill att den offentliga IP-adressen ska distribueras.

17. Under **Inställningar** kontrollerar du att namnet matchar namnet som du angav tidigare i parameter redigeraren.

18. Markera kryss rutan **allmänna** villkor.

19. Välj **köp** för att distribuera den offentliga mål-IP-adressen.

20. Om du har en annan offentlig IP-adress som används för utgående NAT för den belastningsutjämnare som flyttas upprepar du föregående steg för att exportera och distribuera den andra utgående offentliga IP-adressen till mål regionen.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exportera den externa belastnings Utjämnings mal len och distribuera belastningsutjämnaren från Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.
2. Leta reda på resurs gruppen som innehåller den externa käll-belastningsutjämnaren och markera den.
3. Välj **Inställningar**  >  **Exportera mall**.
4. Välj **distribuera** under **Exportera mall**.
5. Välj **mall**  >  **Redigera parametrar** för att öppna parameters.jspå filen i redigeraren online.

5. Om du vill redigera parametern för den externa belastnings Utjämnings namnet ändrar du egenskapen **Value** för den externa källans namn på den externa belastningsutjämnaren till namnet på den externa mål belastnings utjämningen. Omge namnet med citat tecken.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Om du vill redigera värdet för den offentliga IP-adress som du flyttade i föregående steg, måste du först hämta resurs-ID: t och klistra in det i parameters.jspå filen. Hämta ID:

    1. I en annan flik i webbläsaren eller fönstret loggar du in på [Azure Portal](https://portal.azure.com) och väljer **resurs grupper**.
    2. Leta upp mål resurs gruppen som innehåller den offentliga IP-adress som du flyttade i föregående steg. Välj den.
    3. Välj **Inställningar** > **egenskaper**.
    4. Markera **resurs-ID** i bladet till höger och kopiera det till Urklipp. Alternativt kan du välja **Kopiera till Urklipp** till höger om sökvägen till **resurs-ID: t** .
    5. Klistra in resurs-ID: t i egenskapen **Value** i **Redigera parameter** redigeraren som är öppen i det andra webbläsarfönstret eller fliken:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Välj **Spara** i redigeraren online.


7.  Om du har konfigurerat utgående NAT och utgående regler för belastningsutjämnaren visas en tredje post i den här filen för det externa ID: t för den utgående offentliga IP-adressen. Upprepa föregående steg i **mål regionen** för att hämta ID: t för den utgående offentliga IP-adressen. Klistra in det ID: t i parameters.jspå filen:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Välj **mall**  >  **Redigera mall** för att öppna template.jspå filen i redigeraren online.
9.  Om du vill redigera mål regionen som den externa belastnings Utjämnings konfigurationen ska flyttas till, ändrar du egenskapen **location** under **resurser** i template.jspå filen:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är regions namnet utan blank steg. Koden för centrala USA är till exempel **Central**.

11. Du kan också ändra andra parametrar i mallen om du vill eller behöver, beroende på dina krav:

    * **SKU**. Du kan ändra SKU: n för den externa belastningsutjämnaren i konfigurationen från standard till Basic eller från Basic till standard genom att ändra egenskapen **namn** under **SKU** i template.jspå filen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Information om skillnaderna mellan Basic-och standard-SKU-belastningsutjämnare finns i [Översikt över Azure standard Load Balancer](./load-balancer-overview.md).

    * **Belastnings Utjämnings regler**. Du kan lägga till eller ta bort belastnings Utjämnings regler i konfigurationen genom att lägga till eller ta bort poster i **loadBalancingRules** -avsnittet i template.jspå filen:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Information om belastnings Utjämnings regler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Avsökningar**. Du kan lägga till eller ta bort en avsökning för belastningsutjämnaren i konfigurationen genom att lägga till eller ta bort poster i avsnittet **avsökningar** i template.jsfilen:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Mer information finns i [Load Balancer hälso avsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Ingående NAT-regler**. Du kan lägga till eller ta bort inkommande NAT-regler för belastningsutjämnaren genom att lägga till eller ta bort poster i **inboundNatRules** -avsnittet i template.jspå filen:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        För att slutföra tillägg eller borttagning av en inkommande NAT-regel måste regeln finnas eller tas bort som en **typ** egenskap i slutet av template.jspå filen:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Information om inkommande NAT-regler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Utgående regler**. Du kan lägga till eller ta bort utgående regler i konfigurationen genom att redigera egenskapen **outboundRules** i template.jspå filen:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Mer information finns i [Load Balancer utgående regler](./load-balancer-outbound-connections.md#outboundrules).

12. Välj **Spara** i redigeraren online.

13. Välj **grundläggande**  >  **prenumeration** för att välja den prenumeration där den externa mål belastnings balansen ska distribueras.

15. Välj   >  **resurs gruppen** grundläggande för att välja resurs gruppen där mål belastnings utjämningen ska distribueras. Du kan välja **Skapa ny** för att skapa en ny resurs grupp för den externa mål belastnings utjämningen. Eller så kan du välja den befintliga resurs grupp som du skapade tidigare för den offentliga IP-adressen. Kontrol lera att namnet inte är samma som käll resurs gruppen för den befintliga externa käll belastningsutjämnaren.

16. Kontrol lera att **grundläggande**  >  **platser** är inställt på den mål plats där du vill att den externa belastningsutjämnaren ska distribueras.

17. Under **Inställningar** kontrollerar du att namnet stämmer överens med det namn som du angav tidigare i parameter redigeraren. Kontrol lera att resurs-ID: n är ifyllda för alla offentliga IP-adresser i konfigurationen.

18. Markera kryss rutan **allmänna** villkor.

19. Välj **köp** för att distribuera den offentliga mål-IP-adressen.

## <a name="discard"></a>Ignorera

Om du vill ta bort den offentliga mål-IP-adressen och den externa belastningsutjämnaren tar du bort resurs gruppen som innehåller dem. Det gör du genom att välja resurs gruppen från din instrument panel i portalen och sedan välja **ta bort** överst på sidan Översikt.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av den offentliga IP-adressen och den externa belastningsutjämnaren, tar du bort den offentliga IP-adressen för källan och den externa belastningsutjämnaren eller resurs gruppen. Det gör du genom att välja resurs gruppen från din instrument panel i portalen och sedan välja **ta bort** överst på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en extern Azure-belastningsutjämnare från en region till en annan och rensade käll resurserna. Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)