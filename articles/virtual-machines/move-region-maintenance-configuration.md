---
title: Flytta en underhålls konfiguration till en annan Azure-region
description: Lär dig hur du flyttar en konfiguration för underhåll av virtuella datorer till en annan Azure-region
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 91a6adecc9cf0db56fa4c433f388b05aa1bdef6a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202920"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Flytta en konfiguration för underhålls kontroll till en annan region

Följ den här artikeln för att flytta en konfiguration av en underhålls kontroll till en annan Azure-region. Du kanske vill flytta en konfiguration av flera skäl. Till exempel för att dra nytta av en ny region, för att distribuera funktioner eller tjänster som är tillgängliga i en bestämd region, för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planeringen.

Med [underhålls kontroll](maintenance-control.md), med anpassade underhålls konfigurationer, kan du styra hur plattforms uppdateringar tillämpas på virtuella datorer och till Azure-dedikerade värdar. Det finns ett par scenarier för att flytta underhålls kontroll över flera regioner:

- Följ anvisningarna i den här artikeln om du vill flytta din konfiguration för underhålls kontroll, men inte de resurser som är associerade med konfigurationen.
- Om du vill flytta resurserna som är associerade med en underhålls konfiguration, men inte själva konfigurationen, följer du [dessa anvisningar](move-region-maintenance-configuration-resources.md).
- Följ anvisningarna i den här artikeln om du vill flytta både underhålls konfigurationen och de resurser som är kopplade till den. Följ sedan [de här anvisningarna](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar flytta en konfiguration för underhålls kontroll:

- Underhålls konfigurationer är associerade med virtuella Azure-datorer eller Azure-dedikerade värdar. Se till att de virtuella datorerna/värd resurserna finns i den nya regionen innan du börjar.
- Hitta 
    - Befintliga konfigurationer för underhålls kontroll.
    - Resurs grupperna där befintliga konfigurationer finns. 
    - De resurs grupper som konfigurationerna ska läggas till efter när de flyttas till den nya regionen. 
    - De resurser som är associerade med den underhålls konfiguration som du vill flytta.
    - Kontrol lera att resurserna i den nya regionen är desamma som de som är associerade med de aktuella underhålls konfigurationerna. Konfigurationerna kan ha samma namn i den nya regionen som de gjorde i det gamla, men det är inte obligatoriskt.

## <a name="prepare-and-move"></a>Förbered och flytta 

1. Hämta alla underhålls konfigurationer i varje prenumeration. Kör kommandot CLI [AZ Maintenance Configuration List](/cli/azure/ext/maintenance/maintenance/configuration#ext-maintenance-az-maintenance-configuration-list) för att göra detta och ersätt $subId med ditt PRENUMERATIONS-ID.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Granska den returnerade tabell listan över konfigurations poster i prenumerationen. Här är ett exempel. Listan innehåller värden för din speciella miljö.

    **Namn** | **Plats** | **Resursgrupp**
    --- | --- | ---
    Hoppa över underhåll | usaöstra2 | konfiguration – resurs grupp
    IgniteDemoConfig | usaöstra2 | konfiguration – resurs grupp
    defaultMaintenanceConfiguration – öst | USA, östra | test konfiguration
    

3. Spara listan för referens. När du flyttar konfigurationerna hjälper det dig att kontrol lera att allt har flyttats.
4. Som referens mappar du varje konfiguration/resurs grupp till den nya resurs gruppen i den nya regionen.
5. Skapa nya underhålls konfigurationer i den nya regionen med [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Koppla konfigurationerna till resurserna i den nya regionen med [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Verifiera flytten

När du har flyttat konfigurationerna jämför du konfigurationerna och resurserna i den nya regionen med den tabell lista som du har för berett.


## <a name="clean-up-source-resources"></a>Rensa käll resurser

Efter flytten bör du överväga att ta bort de flyttade underhålls konfigurationerna i käll regionen, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Nästa steg

Följ [dessa anvisningar](move-region-maintenance-configuration-resources.md) om du behöver flytta resurser som är associerade med underhålls konfigurationer. 
