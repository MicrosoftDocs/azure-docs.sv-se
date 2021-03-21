---
title: Flytta ett virtuellt Azure-nätverk till en annan Azure-region med hjälp av Azure Portal.
description: Flytta ett virtuellt Azure-nätverk från en Azure-region till en annan med hjälp av en Resource Manager-mall och Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0a80c731e4245b1a295364e5b8c87f90290f7f74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223316"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Flytta ett virtuellt Azure-nätverk till en annan region med hjälp av Azure Portal

Det finns olika scenarier för att flytta ett befintligt virtuellt Azure-nätverk från en region till en annan. Du kanske till exempel vill skapa ett virtuellt nätverk med samma konfiguration för testning och tillgänglighet som ditt befintliga virtuella nätverk. Eller så kanske du vill flytta ett virtuellt produktions nätverk till en annan region som en del av Disaster Recovery-planeringen.

Du kan använda en Azure Resource Manager-mall för att slutföra flyttningen av det virtuella nätverket till en annan region. Du gör detta genom att exportera det virtuella nätverket till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen. Mer information om Resource Manager-mallar finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Förutsättningar

- Se till att ditt virtuella nätverk finns i den Azure-region som du vill flytta från.

- Om du vill exportera ett virtuellt nätverk och distribuera en mall för att skapa ett virtuellt nätverk i en annan region måste du ha rollen som nätverks deltagare eller högre.

- Peering för virtuella nätverk återskapas inte, och de kommer inte att fungera om de fortfarande finns i mallen. Innan du exporterar mallen måste du ta bort alla virtuella nätverks-peer-datorer. Du kan sedan återupprätta dem efter det att det virtuella nätverket har flyttats.

- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper (NSG: er) och offentliga IP-adresser.

- Kontrol lera att din Azure-prenumeration låter dig skapa virtuella nätverk i mål regionen. Kontakta supporten om du vill aktivera den begärda kvoten.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att kunna lägga till virtuella nätverk för den här processen. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Förbered för flytt
I det här avsnittet förbereder du det virtuella nätverket för flytten med hjälp av en Resource Manager-mall. Sedan flyttar du det virtuella nätverket till mål regionen med hjälp av Azure Portal.

Så här exporterar du det virtuella nätverket och distribuerar det virtuella mål nätverket med hjälp av Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com)och välj sedan **resurs grupper**.
1. Leta upp resurs gruppen som innehåller det virtuella käll nätverket och markera den.
1. Välj **Inställningar**  >  **Exportera mall**.
1. I fönstret **Exportera mall** väljer du **distribuera**.
1. Om du vill öppna *parameters.js* filen i redigerings programmet för online väljer du **mall**  >  **Redigera parametrar**.
1. Om du vill redigera parametern för det virtuella nätverks namnet ändrar du egenskapen **Value** under **parametrar**:

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

1. Ändra värdet för det virtuella nätverks namnet i redigeraren till ett namn som du vill använda för det virtuella mål nätverket i redigeraren. Var noga med att skriva namnet inom citat tecken.

1. Välj **Spara** i redigeraren.

1. Om du vill öppna *template.js* filen i redigerings programmet för online väljer du **mall**  >  **Redigera mall**.

1. I online-redigeraren kan du redigera mål regionen där det virtuella nätverket ska flyttas, ändra **plats** egenskapen under **resurser**:

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

1. Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är region namnet, utan blank steg (till exempel **centrala USA**,  =  **centrala**).

1. Valfritt Du kan också ändra andra parametrar i mallen, beroende på dina krav:

    * **Adress utrymme**: innan du sparar filen kan du ändra adress utrymmet för det virtuella nätverket genom att ändra avsnittet **Resources**  >  **addressSpace** och ändra egenskapen **addressPrefixes** :

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

    * **Undernät**: du kan ändra eller lägga till i under nätets namn och under nätets adress utrymme genom att ändra mallens avsnittet **undernät** . Du kan ändra namnet på under nätet genom att ändra egenskapen **namn** . Och du kan ändra adress utrymmet för under nätet genom att ändra egenskapen **addressPrefix** :

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

        Om du vill ändra adressprefixet i *template.js* filen, redigerar du det på två platser: i koden i föregående avsnitt och i avsnittet **typ** i följande kod. Ändra egenskapen **addressPrefix** i följande kod så att den matchar egenskapen **addressPrefix** i koden i föregående avsnitt.

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

1. I redigeraren för online väljer du **Spara**.

1. Om du vill välja den prenumeration där det virtuella mål nätverket ska distribueras väljer du **grundläggande**  >  **prenumeration**.

1. Välj resurs gruppen **grundläggande** om du vill välja resurs grupp där det virtuella mål nätverket ska distribueras  >  . 

    Om du behöver skapa en ny resurs grupp för det virtuella mål nätverket väljer du **Skapa ny**. Kontrol lera att namnet inte är detsamma som käll resurs gruppens namn i det befintliga virtuella nätverket.

1. Kontrol lera att **grundläggande**  >  **platser** är inställt på den mål plats där du vill att det virtuella nätverket ska distribueras.

1. Under **Inställningar** kontrollerar du att namnet stämmer överens med det namn som du angav tidigare i parameter redigeraren.

1. Markera kryss rutan **allmänna** villkor.

1. Om du vill distribuera det virtuella mål nätverket väljer du **köp**.

## <a name="delete-the-target-virtual-network"></a>Ta bort det virtuella mål nätverket

Om du vill ta bort det virtuella mål nätverket tar du bort resurs gruppen som innehåller det virtuella mål nätverket. Så här gör du:
1. På instrument panelen Azure Portal väljer du resurs gruppen.
1. Klicka på **ta bort** överst i **översikts** fönstret.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra den virtuella nätverks flyttningen tar du bort det virtuella käll nätverket eller resurs gruppen. Så här gör du:
1. På instrument panelen Azure Portal väljer du det virtuella nätverket eller resurs gruppen.
1. Välj **ta bort** överst i varje fönster.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat ett virtuellt Azure-nätverk från en region till en annan med hjälp av Azure Portal och sedan rensade de onödiga käll resurserna. Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Flytta virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)