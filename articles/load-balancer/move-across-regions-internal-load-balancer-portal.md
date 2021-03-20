---
title: Flytta interna Azure-Load Balancer till en annan Azure-region med hjälp av Azure Portal
description: Använd Azure Resource Manager mall för att flytta interna Azure-Load Balancer från en Azure-region till en annan med hjälp av Azure Portal
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698505"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Flytta interna Azure-Load Balancer till en annan region med hjälp av Azure Portal

Det finns olika scenarier där du vill flytta din befintliga interna belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en intern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en intern belastningsutjämnare till en annan region som en del av Disaster Recovery-planeringen.

Det går inte att flytta Azures interna belastningsutjämnare från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen och det virtuella nätverket för en intern belastningsutjämnare.  Du kan sedan mellanlagra resursen i en annan region genom att exportera belastningsutjämnaren och virtuella nätverk till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallarna till den nya regionen.  Mer information om Resource Manager och mallar finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Förutsättningar

- Se till att den interna belastningsutjämnaren i Azure är i den Azure-region som du vill flytta.

- Det går inte att flytta Azures interna belastningsutjämnare mellan regioner.  Du måste koppla den nya belastningsutjämnaren till resurser i mål regionen.

- Om du vill exportera en intern belastnings Utjämnings konfiguration och distribuera en mall för att skapa en intern belastningsutjämnare i en annan region behöver du rollen som nätverks deltagare eller högre.

- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper, virtuella datorer och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa interna belastningsutjämnare i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att lägga till belastningsutjämnare för den här processen.  Se [begränsningar, kvoter och begränsningar för Azure-prenumerationen och tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder den interna belastningsutjämnaren för flytt med hjälp av en Resource Manager-mall och flyttar den interna belastnings Utjämnings konfigurationen till mål regionen med hjälp av Azure Portal.  Som en del av den här processen måste den virtuella belastnings utjämningens virtuella nätverks konfiguration tas med och måste utföras först innan den interna belastningsutjämnaren flyttas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Exportera mallen för det virtuella nätverket och distribuera den från Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com)  >  **resurs grupper**.
2. Leta upp resurs gruppen som innehåller det virtuella käll nätverket och klicka på den.
3. Välj   >  **Exportera mall** för > inställningar.
4. Välj **distribuera** på bladet **Exportera mall** .
5. Klicka på **mall**  >  **Redigera parametrar** för att öppna **parameters.jspå** filen i redigeraren online.
6. Om du vill redigera parametern för det virtuella nätverks namnet ändrar du egenskapen **Value** under **parametrar**:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Ändra värdet för namnet på den virtuella käll datorn i redigeraren till ett namn som du väljer för det virtuella mål nätverket. Se till att du omger namnet med citat tecken.

8. Klicka på **Spara** i redigeraren.

9. Klicka på **mall**  >  **Redigera mall** för att öppna **template.jspå** filen i redigeraren online.

10. Om du vill redigera mål regionen där det virtuella nätverket ska flyttas ändrar du egenskapen **location** under resurser:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

11. Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA**  =  **, centrala**.

12. Du kan också ändra andra parametrar i **template.js** i filen om du väljer, och de är valfria beroende på dina krav:

    * **Adress utrymme** – det virtuella nätverkets adress utrymme kan ändras innan du sparar genom att ändra avsnittet **Resources**  >  **addressSpace** och ändra egenskapen **addressPrefixes** i **template.jspå** filen:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Undernät** – under nätets namn och under nätets adress utrymme kan ändras eller läggas till genom att ändra avsnittet **undernät** i **template.jsi** filen. Namnet på under nätet kan ändras genom att ändra egenskapen **Name** . Du kan ändra adress utrymmet för under nätet genom att ändra egenskapen **addressPrefix** i **template.jspå** filen:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         I **template.jspå** filen, för att ändra adressprefixet, måste det redige ras på två platser, avsnittet som anges ovan och i avsnittet **typ** nedan.  Ändra egenskapen **addressPrefix** så att den matchar det som beskrivs ovan:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

13. Klicka på **Spara** i redigeraren online.

14. Klicka på **grundläggande**  >  **prenumeration** för att välja den prenumeration där det virtuella mål nätverket ska distribueras.

15. Klicka på **grundläggande**  >  **resurs grupp** för att välja den resurs grupp där det virtuella mål nätverket ska distribueras.  Du kan klicka på **Skapa ny** för att skapa en ny resurs grupp för det virtuella mål nätverket.  Se till att namnet inte är samma som käll resurs gruppen för det befintliga virtuella nätverket.

16. Kontrol lera att **grundläggande**  >  **platser** är inställt på den målplats där du vill att det virtuella nätverket ska distribueras.

17. Verifiera under **Inställningar** att namnet matchar namnet som du angav i parameter redigeraren ovan.

18. Markera kryss rutan under **allmänna** villkor.

19. Klicka på knappen **köp** för att distribuera det virtuella mål nätverket.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportera den interna belastnings Utjämnings mal len och distribuera från Azure PowerShell

1. Logga in på [Azure Portal](https://portal.azure.com)  >  **resurs grupper**.
2. Leta upp resurs gruppen som innehåller den interna belastningsutjämnaren och klicka på den.
3. Välj   >  **Exportera mall** för > inställningar.
4. Välj **distribuera** på bladet **Exportera mall** .
5. Klicka på **mall**  >  **Redigera parametrar** för att öppna **parameters.jspå** filen i redigeraren online.

6. Om du vill redigera parametern för den interna belastnings Utjämnings namnet ändrar du egenskapen **DefaultValue** för det interna namnet på den interna belastningsutjämnaren till namnet på den interna mål belastnings utjämningen, se till att namnet är inom citat tecken:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Om du vill redigera värdet för det virtuella mål nätverket som flyttades ovan måste du först hämta resurs-ID: t och sedan kopiera och klistra in det i **parameters.js** filen. Hämta ID:

    1. Logga in på [Azure Portal](https://portal.azure.com)  >  **resurs grupper** i en annan flik i webbläsaren eller fönstret.
    2. Leta upp mål resurs gruppen som innehåller det flyttade virtuella nätverket från stegen ovan och klicka på den.
    3. Välj > **Inställningar**  >  **Egenskaper**.
    4. Markera **resurs-ID** i bladet till höger och kopiera det till Urklipp.  Alternativt kan du klicka på knappen **Kopiera till Urklipp** till höger om sökvägen till **resurs-ID: t** .
    5. Klistra in resurs-ID: t i egenskapen **DefaultValue** i **Redigera parameter** redigeraren öppna i det andra webbläsarfönstret eller fliken:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Klicka på **Spara** i redigeraren online.

7. Klicka på **mall**  >  **Redigera mall** för att öppna **template.jspå** filen i redigeraren online.
8. Om du vill redigera mål regionen där den interna belastnings Utjämnings konfigurationen ska flyttas ändrar du egenskapen **location** under **resurser** i **template.jspå** filen:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA**  =  **, centrala**.

10. Du kan också ändra andra parametrar i mallen om du väljer, och de är valfria beroende på dina krav:

    * **SKU** – du kan ändra SKU: n för den interna belastningsutjämnaren i konfigurationen från standard till Basic eller Basic till standard genom att ändra egenskapen SKU-   >  **namn** i **template.jspå** filen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Mer information om skillnaderna mellan Basic-och standard SKU-belastningsutjämnare finns i [Översikt över Azure standard Load Balancer](./load-balancer-overview.md)

    * **Belastnings Utjämnings regler** – du kan lägga till eller ta bort belastnings Utjämnings regler i konfigurationen genom att lägga till eller ta bort poster i **loadBalancingRules** -avsnittet i **template.jspå** filen:

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
       Mer information om belastnings Utjämnings regler finns i [Vad är Azure Load Balancer?](./load-balancer-overview.md)

    * **Avsökningar** – du kan lägga till eller ta bort en avsökning för belastningsutjämnaren i konfigurationen genom att lägga till eller ta bort poster i avsnittet **avsökningar** i **template.js** filen:

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
       Mer information om Azure Load Balancer hälso avsökningar finns i [Load Balancer hälso avsökningar](./load-balancer-custom-probe-overview.md)

    * **Ingående NAT-regler** – du kan lägga till eller ta bort inkommande NAT-regler för belastningsutjämnaren genom att lägga till eller ta bort poster i **inboundNatRules** -avsnittet i **template.jspå** filen:

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
        För att slutföra tillägg eller borttagning av en inkommande NAT-regel måste regeln finnas eller tas bort som en **typ** egenskap i slutet av **template.jspå** filen:

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
        Mer information om inkommande NAT-regler finns i [Vad är Azure Load Balancer?](./load-balancer-overview.md)

12. Klicka på **Spara** i redigeraren online.

13. Klicka på **grundläggande**  >  **prenumeration** för att välja den prenumeration där den interna mål belastnings utjämningen ska distribueras.

15. Klicka på **grundläggande**  >  **resurs grupp** för att välja resurs gruppen där mål belastnings utjämningen ska distribueras.  Du kan klicka på **Skapa ny** för att skapa en ny resurs grupp för den interna mål belastnings utjämningen eller välja den befintliga resurs grupp som skapades ovan för det virtuella nätverket.  Se till att namnet inte är samma som käll resurs gruppen för den befintliga käll intern belastningsutjämnaren.

16. Kontrol lera att **grundläggande**  >  **platser** är inställt på mål platsen där du vill att den interna belastningsutjämnaren ska distribueras.

17. Verifiera under **Inställningar** att namnet matchar namnet som du angav i parameter redigeraren ovan.  Kontrol lera att resurs-ID: n är ifyllda för alla virtuella nätverk i konfigurationen.

18. Markera kryss rutan under **allmänna** villkor.

19. Klicka på knappen **köp** för att distribuera det virtuella mål nätverket.

## <a name="discard"></a>Ignorera

Om du vill ta bort det virtuella mål nätverket och den interna belastningsutjämnaren tar du bort resurs gruppen som innehåller det virtuella mål nätverket och den interna belastningsutjämnaren.  Det gör du genom att välja resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på sidan Översikt.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av det virtuella nätverket och den interna belastningsutjämnaren, tar du bort det virtuella käll nätverket och den interna belastningsutjämnaren eller resurs gruppen. Det gör du genom att välja det virtuella nätverket och den interna belastningsutjämnaren eller resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en intern Azure-belastningsutjämnare från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)