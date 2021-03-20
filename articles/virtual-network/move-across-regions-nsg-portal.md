---
title: Flytta Azures nätverks säkerhets grupp (NSG) till en annan Azure-region med hjälp av Azure Portal
description: Använd Azure Resource Manager mall för att flytta Azures nätverks säkerhets grupp från en Azure-region till en annan med hjälp av Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 11b0260eb6c1f5edfb246dcf97a43bdd874bf337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217285"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Flytta Azures nätverks säkerhets grupp (NSG) till en annan region med hjälp av Azure Portal

Det finns olika scenarier där du vill flytta befintliga NSG: er från en region till en annan. Du kanske till exempel vill skapa en NSG med samma konfigurations-och säkerhets regler för testning. Du kanske också vill flytta en NSG till en annan region som en del av Disaster Recovery-planeringen.

Det går inte att flytta Azure-säkerhetsgrupper från en region till en annan. Du kan dock använda en Azure Resource Manager mall för att exportera befintliga konfigurations-och säkerhets regler för en NSG.  Du kan sedan mellanlagra resursen i en annan region genom att exportera NSG till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen.  Mer information om Resource Manager och mallar finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Förutsättningar

- Kontrol lera att Azure-nätverks säkerhets gruppen finns i den Azure-region som du vill flytta.

- Det går inte att flytta Azures nätverks säkerhets grupper mellan regioner.  Du måste associera den nya NSG till resurser i mål regionen.

- Om du vill exportera en NSG-konfiguration och distribuera en mall för att skapa en NSG i en annan region behöver du rollen som nätverks deltagare eller högre.

- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, offentliga IP-adresser och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa NSG: er i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att kunna lägga till NSG: er för den här processen.  Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder nätverks säkerhets gruppen för konfigurationen och säkerhets regeln flyttas med hjälp av en Resource Manager-mall och flyttar NSG-konfigurationen och säkerhets reglerna till mål regionen via portalen.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportera mallen och distribuera från portalen

1. Logga in på [Azure Portal](https://portal.azure.com)  >  **resurs grupper**.
2. Leta upp resurs gruppen som innehåller käll-NSG och klicka på den.
3. Välj   >  **Exportera mall** för > inställningar.
4. Välj **distribuera** på bladet **Exportera mall** .
5. Klicka på **mall**  >  **Redigera parametrar** för att öppna **parameters.jspå** filen i redigeraren online.
6. Om du vill redigera parametern för NSG-namnet ändrar du egenskapen **Value** under **parametrar**:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Ändra värdet för källans NSG i redigeraren till ett namn som du väljer för mål NSG. Se till att du omger namnet med citat tecken.

8.  Klicka på **Spara** i redigeraren.

9.  Klicka på **mall**  >  **Redigera mall** för att öppna **template.jspå** filen i redigeraren online.

10. Om du vill redigera mål regionen där NSG-konfigurationen och säkerhets reglerna ska flyttas ändrar du egenskapen **location** under **resurser** i online-redigeraren:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA**  =  **, centrala**.

12. Du kan också ändra andra parametrar i mallen om du väljer, och de är valfria beroende på dina krav:

    * **Säkerhets regler** – du kan redigera vilka regler som distribueras till mål-NSG genom att lägga till eller ta bort regler i **securityRules** -avsnittet i **template.jspå** filen:

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Om du vill slutföra tillägg eller borttagning av reglerna i mål-NSG måste du också redigera anpassade regel typer i slutet av **template.jspå** filen i formatet i exemplet nedan:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Klicka på **Spara** i redigeraren online.

14. Klicka på **grundläggande**  >  **prenumeration** för att välja den prenumeration där mål-NSG ska distribueras.

15. Klicka på **grundläggande**  >  **resurs grupp** för att välja den resurs grupp där mål-NSG ska distribueras.  Du kan klicka på **Skapa ny** för att skapa en ny resurs grupp för mål-NSG.  Se till att namnet inte är samma som käll resurs gruppen för det befintliga NSG.

16. Kontrol lera att **grundläggande**  >  **platser** är inställt på den mål plats där du vill att NSG ska distribueras.

17. Verifiera under **Inställningar** att namnet matchar namnet som du angav i parameter redigeraren ovan.

18. Markera kryss rutan under **allmänna** villkor.

19. Klicka på knappen **köp** för att distribuera mål nätverks säkerhets gruppen.

## <a name="discard"></a>Ignorera

Om du vill ignorera mål-NSG tar du bort resurs gruppen som innehåller mål-NSG.  Det gör du genom att välja resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på sidan Översikt.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av NSG tar du bort käll NSG eller resurs gruppen. Det gör du genom att välja nätverks säkerhets grupp eller resurs grupp på instrument panelen i portalen och välja **ta bort** överst på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat en Azure-nätverks säkerhets grupp från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)