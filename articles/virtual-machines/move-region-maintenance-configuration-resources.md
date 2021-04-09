---
title: Flytta resurser som är associerade med en underhålls konfiguration till en annan region
description: Lär dig hur du flyttar resurser som är associerade med en konfiguration för underhåll av virtuella datorer till en annan Azure-region
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 88082c441dafdc7571f2b9775bfc07ebe3ca5aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730514"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Flytta resurser i en underhålls kontroll konfiguration till en annan region

Följ den här artikeln för att flytta resurser som är associerade med en underhålls kontroll konfiguration till en annan Azure-region. Du kanske vill flytta en konfiguration av flera skäl. Till exempel för att dra nytta av en ny region, för att distribuera funktioner eller tjänster som är tillgängliga i en bestämd region, för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planeringen.

Med [underhålls kontroll](maintenance-control.md), med anpassade underhålls konfigurationer, kan du styra hur plattforms uppdateringar tillämpas på virtuella datorer och till Azure-dedikerade värdar. Det finns ett par scenarier för att flytta underhålls kontroll över flera regioner:

- Om du vill flytta resurserna som är kopplade till en underhålls konfiguration, men inte själva konfigurationen, följer du den här artikeln.
- Följ [dessa instruktioner](move-region-maintenance-configuration.md)om du vill flytta din konfiguration för underhålls kontroll, men inte de resurser som är associerade med konfigurationen.
- Följ [dessa instruktioner](move-region-maintenance-configuration.md)för att flytta både underhålls konfigurationen och de resurser som är kopplade till den. Följ sedan instruktionerna i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar flytta resurserna som är associerade med en underhålls kontroll konfiguration:

- Kontrol lera att de resurser du flyttar finns i den nya regionen innan du börjar.
- Verifiera de konfigurationer för underhålls kontroll som är kopplade till de virtuella Azure-datorer och Azure-dedikerade värdar som du vill flytta. Kontrol lera varje resurs individuellt. Det finns för närvarande inget sätt att hämta konfigurationer för flera resurser.
- När du hämtar konfigurationer för en resurs:
    - Se till att du använder prenumerations-ID för kontot, inte ett dedikerat värd-ID för Azure.
    - CLI: tabell parametern--output används endast för läsbarhet och kan tas bort eller ändras.
    - PowerShell: parametern Format-Table Name används endast för läsbarhet och kan tas bort eller ändras.
    - Om du använder PowerShell får du ett fel meddelande om du försöker visa konfigurationer för en resurs som inte har några associerade konfigurationer. Felet ser ut ungefär så här: "åtgärden misslyckades med status:" hittades inte ". Information: 404-klient fel: hittades inte för URL: en.

    
## <a name="prepare-to-move"></a>Förbered för att flytta

1. Definiera dessa variabler innan du börjar. Vi har angett ett exempel för var och en.

    **Variabel** | **Information** | **Exempel**
    --- | ---
    $subId | ID för prenumerationen som innehåller underhålls konfigurationerna | "vår-Subscription-ID"
    $rsrcGroupName | Resurs grupps namn (virtuell Azure-dator) | "VMResourceGroup"
    $vmName | Namn på virtuell dator resurs |  MyVM
    $adhRsrcGroupName |  Resurs grupp (dedikerade värdar) | "HostResourceGroup"
    $adh | Dedikerat värdnamn | "värd"
    $adhParentName | Överordnat resurs namn | HostGroup
    
2. Hämta underhålls konfigurationerna med PowerShell [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) -kommandot:

    - För Azure-dedikerade värdar kör du:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - För virtuella Azure-datorer kör du:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Hämta underhålls konfigurationerna med kommandot CLI- [AZ underhålls tilldelning](/cli/azure/ext/maintenance/maintenance/assignment) :

    - För Azure-dedikerade värdar:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - För virtuella Azure-datorer:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Flytta 

1. [Följ de här anvisningarna](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) för att flytta de virtuella Azure-datorerna till den nya regionen.
2. När resurserna har flyttats tillämpar du underhålls konfigurationerna på resurserna i den nya regionen efter behov, beroende på om du flyttade underhålls konfigurationerna. Du kan använda en underhålls konfiguration för en resurs med hjälp av [PowerShell](../virtual-machines/maintenance-control-powershell.md) eller [CLI](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Verifiera flytten

Verifiera resurserna i den nya regionen och kontrol lera de associerade konfigurationerna för resurserna i den nya regionen. 

## <a name="clean-up-source-resources"></a>Rensa käll resurser

Överväg att ta bort de flyttade resurserna i käll regionen efter flytten.


## <a name="next-steps"></a>Nästa steg

Följ [dessa anvisningar](move-region-maintenance-configuration.md) om du behöver flytta underhålls konfigurationerna. 
