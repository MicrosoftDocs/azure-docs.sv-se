---
title: Flytta en underhållskonfiguration till en annan Azure-region
description: Lär dig hur du flyttar en vm-underhållskonfiguration till en annan Azure-region
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: f833f3eb9e3d94da6178a0a9a9cf4f95ec0682e7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865377"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Flytta en konfiguration för underhållskontroll till en annan region

Följ den här artikeln om du vill flytta en konfiguration av underhållskontrollen till en annan Azure-region. Du kanske vill flytta en konfiguration av flera olika skäl. Om du till exempel vill dra nytta av en ny region, distribuera funktioner eller tjänster som är tillgängliga i en viss region, uppfylla interna princip- och styrningskrav eller som svar på kapacitetsplaneringen.

[Med underhållskontroll,](maintenance-control.md)med anpassade underhållskonfigurationer, kan du styra hur plattformsuppdateringar tillämpas på virtuella datorer och på Azure Dedicated Hosts. Det finns ett par scenarier för att flytta underhållskontroll mellan regioner:

- Om du vill flytta konfigurationen av underhållskontroll, men inte de resurser som är associerade med konfigurationen, följer du anvisningarna i den här artikeln.
- Följ dessa instruktioner om du vill flytta de resurser som är associerade med en underhållskonfiguration, men inte själva [konfigurationen.](move-region-maintenance-configuration-resources.md)
- Om du vill flytta både underhållskonfigurationen och de resurser som är associerade med den följer du först anvisningarna i den här artikeln. Följ sedan dessa [anvisningar.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar flytta en konfiguration av underhållskontrollen:

- Underhållskonfigurationer är associerade med virtuella Azure-datorer eller Dedikerade Azure-värdar. Kontrollera att vm-/värdresurser finns i den nya regionen innan du börjar.
- Identifiera: 
    - Befintliga konfigurationer för underhållskontroll.
    - De resursgrupper där befintliga konfigurationer finns för närvarande. 
    - De resursgrupper som konfigurationerna ska läggas till i efter flytten till den nya regionen. 
    - De resurser som är associerade med den underhållskonfiguration som du vill flytta.
    - Kontrollera att resurserna i den nya regionen är desamma som de som är associerade med de aktuella underhållskonfigurationerna. Konfigurationerna kan ha samma namn i den nya regionen som de gjorde i den gamla, men detta krävs inte.

## <a name="prepare-and-move"></a>Förbereda och flytta 

1. Hämta alla underhållskonfigurationer i varje prenumeration. Kör kommandot CLI [az maintenance configuration list för](/cli/azure/maintenance/configuration#az_maintenance_configuration_list) att göra detta och ersätt $subId med ditt prenumerations-ID.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Granska den returnerade tabelllistan med konfigurationsposter i prenumerationen. Här är ett exempel. Din lista innehåller värden för din specifika miljö.

    **Namn** | **Plats** | **Resursgrupp**
    --- | --- | ---
    Hoppa över underhåll | usaöstra2 | configuration-resource-group
    IgniteDemoConfig | usaöstra2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | USA, östra | testkonfiguration
    

3. Spara listan som referens. När du flyttar konfigurationerna hjälper det dig att verifiera att allt har flyttats.
4. Som referens mappar du varje konfiguration/resursgrupp till den nya resursgruppen i den nya regionen.
5. Skapa nya underhållskonfigurationer i den nya regionen med [Hjälp av PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Associera konfigurationerna med resurserna i den nya regionen med [hjälp av PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Verifiera flytten

När du har flyttat konfigurationerna jämför du konfigurationer och resurser i den nya regionen med den tabelllista som du förberedde.


## <a name="clean-up-source-resources"></a>Rensa källresurser

Efter flytten bör du överväga att ta bort de flyttade underhållskonfigurationerna i källregionen, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Nästa steg

Följ [de här instruktionerna](move-region-maintenance-configuration-resources.md) om du behöver flytta resurser som är associerade med underhållskonfigurationer. 
