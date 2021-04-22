---
title: Flytta resurser som är associerade med en underhållskonfiguration till en annan region
description: Lär dig hur du flyttar resurser som är associerade med en vm-underhållskonfiguration till en annan Azure-region
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4427071edf237d82e8a99d44678d77d23e180fff
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865251"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Flytta resurser i en underhållskontrollkonfiguration till en annan region

Följ den här artikeln om du vill flytta resurser som är associerade med en konfiguration av underhållskontroll till en annan Azure-region. Du kanske vill flytta en konfiguration av flera olika skäl. Om du till exempel vill dra nytta av en ny region, distribuera funktioner eller tjänster som är tillgängliga i en viss region, uppfylla interna princip- och styrningskrav eller som svar på kapacitetsplaneringen.

[Med underhållskontroll,](maintenance-control.md)med anpassade underhållskonfigurationer, kan du styra hur plattformsuppdateringar tillämpas på virtuella datorer och på Azure Dedicated Hosts. Det finns ett par scenarier för att flytta underhållskontroll mellan regioner:

- Följ den här artikeln om du vill flytta de resurser som är associerade med en underhållskonfiguration, men inte själva konfigurationen.
- Följ dessa instruktioner om du vill flytta konfigurationen av underhållskontroll, men inte de resurser som är associerade [med konfigurationen.](move-region-maintenance-configuration.md)
- Om du vill flytta både underhållskonfigurationen och de resurser som är associerade med den följer du [först dessa instruktioner.](move-region-maintenance-configuration.md) Följ sedan anvisningarna i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar flytta de resurser som är associerade med en konfiguration för underhållskontroll:

- Kontrollera att de resurser som du flyttar finns i den nya regionen innan du börjar.
- Kontrollera de konfigurationer för underhållskontroll som är associerade med de virtuella Azure-datorer och Dedikerade Azure-värdar som du vill flytta. Kontrollera varje resurs individuellt. Det finns för närvarande inget sätt att hämta konfigurationer för flera resurser.
- När du hämtar konfigurationer för en resurs:
    - Se till att du använder prenumerations-ID:t för kontot, inte ett Azure Dedicated Host-ID.
    - CLI: Tabellparametern --output används endast för läsbarhet och kan tas bort eller ändras.
    - PowerShell: Parametern Format-Table Name används endast för läsbarhet och kan tas bort eller ändras.
    - Om du använder PowerShell får du ett felmeddelande om du försöker lista konfigurationer för en resurs som inte har några associerade konfigurationer. Felet ser ut ungefär så här: "Åtgärden misslyckades med status: "Hittades inte". Information: 404 Client Error: Not Found for URL".

    
## <a name="prepare-to-move"></a>Förbereda flytten

1. Definiera dessa variabler innan du börjar. Vi har angett ett exempel för var och en.

    **Variabel** | **Information** | **Exempel**
    --- | ---
    $subId | ID för prenumeration som innehåller underhållskonfigurationerna | "vårt-prenumerations-ID"
    $rsrcGroupName | Resursgruppsnamn (virtuell Azure-dator) | "VMResourceGroup"
    $vmName | Resursnamn för virtuell dator |  "myVM"
    $adhRsrcGroupName |  Resursgrupp (dedikerade värdar) | "HostResourceGroup"
    $adh | Dedikerat värdnamn | "myHost"
    $adhParentName | Namn på överordnad resurs | "HostGroup"
    
2. Så här hämtar du underhållskonfigurationerna med [powershell-kommandot Get-AZConfigurationAssignment:](/powershell/module/az.maintenance/get-azconfigurationassignment)

    - För Dedikerade Azure-värdar kör du:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - För virtuella Azure-datorer kör du:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Så här hämtar du underhållskonfigurationerna med hjälp av [CLI-kommandot az maintenance assignment:](/cli/azure/maintenance/assignment)

    - För Dedikerade Azure-värdar:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - För virtuella Azure-datorer:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Flytta 

1. [Följ de här anvisningarna](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) för att flytta de virtuella Azure-datorerna till den nya regionen.
2. När resurserna har flyttats kan du tillämpa underhållskonfigurationerna på nytt på resurserna i den nya regionen efter behov, beroende på om du har flyttat underhållskonfigurationerna. Du kan tillämpa en underhållskonfiguration på en resurs med [hjälp av PowerShell](../virtual-machines/maintenance-control-powershell.md) eller [CLI.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>Verifiera flytten

Kontrollera resurserna i den nya regionen och kontrollera associerade konfigurationer för resurserna i den nya regionen. 

## <a name="clean-up-source-resources"></a>Rensa källresurser

Efter flytten bör du överväga att ta bort de flyttade resurserna i källregionen.


## <a name="next-steps"></a>Nästa steg

Följ [de här anvisningarna](move-region-maintenance-configuration.md) om du behöver flytta underhållskonfigurationer. 
