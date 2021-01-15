---
title: Flytta Azures offentliga IP-konfiguration till en annan Azure-region Azure Portal
description: Använd en mall för att flytta den offentliga Azure-IP-konfigurationen från en Azure-region till en annan med hjälp av Azure Portal.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: b00fca8cf39bc44e0e53a112a332e6f6c5f0194e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218611"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Flytta Azures offentliga IP-konfiguration till en annan region med hjälp av Azure Portal

Det finns olika scenarier där du vill flytta dina befintliga offentliga Azure-IP-konfigurationer från en region till en annan. Du kanske till exempel vill skapa en offentlig IP-adress med samma konfiguration och SKU för testning. Du kanske också vill flytta en offentlig IP-konfiguration till en annan region som en del av Disaster Recovery-planeringen.

**Offentliga Azure-IP-adresser är regions information och kan inte flyttas från en region till en annan.** Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen av en offentlig IP-adress.  Du kan sedan mellanlagra resursen i en annan region genom att exportera den offentliga IP-adressen till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen.  Mer information om Resource Manager och mallar finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Förutsättningar

- Se till att den offentliga Azure-IP-adressen finns i den Azure-region som du vill flytta från.

- Det går inte att flytta offentliga Azure-IP-adresser mellan regioner.  Du måste associera den nya offentliga IP-adressen till resurser i mål regionen.

- Om du vill exportera en offentlig IP-konfiguration och distribuera en mall för att skapa en offentlig IP-adress i en annan region behöver du rollen som nätverks deltagare eller högre.

- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper (NSG: er) och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa offentliga IP-adresser i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att kunna lägga till offentliga IP-adresser för den här processen.  Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder den offentliga IP-adressen för konfigurations flyttning med en Resource Manager-mall och flyttar den offentliga IP-konfigurationen till mål regionen med hjälp av Azure Portal.

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportera mallen och distribuera från ett skript

1. Logga in på [Azure Portal](https://portal.azure.com)  >  **resurs grupper**.
2. Leta upp resurs gruppen som innehåller den offentliga käll-IP-adressen och klicka på den.
3. Välj   >  **Exportera mall** för > inställningar.
4. Välj **distribuera** på bladet **Exportera mall** .
5. Klicka på **mall**  >  **Redigera parametrar** för att öppna **parameters.jspå** filen i redigeraren online.
8. Om du vill redigera parametern för det offentliga IP-namnet ändrar du egenskapen under **parameter**  >  **värde** från den offentliga IP-adressen till namnet på din mål-offentliga IP-adress, se till att namnet är inom citat tecken:

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
8.  Klicka på **Spara** i redigeraren.

9.  Klicka på **mall**  >  **Redigera mall** för att öppna **template.jspå** filen i redigeraren online.

10. Om du vill redigera mål regionen där den offentliga IP-adressen ska flyttas ändrar du egenskapen **location** under **resurser**:

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

11. Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA**  =  **, centrala**.

12. Du kan också ändra andra parametrar i mallen om du väljer, och de är valfria beroende på dina krav:

    * **SKU** – du kan ändra SKU: n för den offentliga IP-adressen i konfigurationen från standard till Basic eller Basic till standard genom att ändra egenskapen **SKU**-  >  **namn** i **template.jspå** filen:

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

        Mer information om skillnaderna mellan grundläggande och standard-SKU: er för offentliga IP-adresser finns i [skapa, ändra eller ta bort en offentlig IP-adress](./virtual-network-public-ip-address.md):

    * **Metod för offentlig IP-tilldelning** och **tids gräns för inaktivitet** – du kan ändra båda alternativen i mallen genom att ändra egenskapen **publicIPAllocationMethod** från **dynamisk** till **statisk** eller **statisk** till **dynamisk**. Tids gränsen för inaktivitet kan ändras genom att ändra egenskapen **idleTimeoutInMinutes** till önskad mängd.  Standardvärdet är **4**:

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

        Mer information om fördelnings metoder och tids gräns värden för inaktivitet finns i [skapa, ändra eller ta bort en offentlig IP-adress](./virtual-network-public-ip-address.md).


13. Klicka på **Spara** i redigeraren online.

14. Klicka på **grundläggande**  >  **prenumeration** för att välja den prenumeration där den offentliga mål-IP-adressen ska distribueras.

15. Klicka på **grundläggande**  >  **resurs grupp** för att välja den resurs grupp där den offentliga mål-IP-adressen ska distribueras.  Du kan klicka på **Skapa ny** för att skapa en ny resurs grupp för den offentliga IP-adressen.  Se till att namnet inte är samma som käll resurs gruppen för den befintliga offentliga IP-adressen.

16. Kontrol lera att **grundläggande**  >  **platser** är inställt på den mål plats där du vill att den offentliga IP-adressen ska distribueras.

17. Verifiera under **Inställningar** att namnet matchar namnet som du angav i parameter redigeraren ovan.

18. Markera kryss rutan under **allmänna** villkor.

19. Klicka på knappen **köp** för att distribuera den offentliga mål-IP-adressen.

## <a name="discard"></a>Ignorera

Ta bort den resurs grupp som innehåller den offentliga IP-adressen om du vill ta bort den offentliga mål-IP-adressen.  Det gör du genom att välja resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på sidan Översikt.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av den offentliga IP-adressen tar du bort den offentliga käll-IP-adressen eller resurs gruppen. Det gör du genom att välja den offentliga IP-adressen eller resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat en offentlig Azure-IP-adress från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)