---
title: Flytta en Azure automanage-virtuell dator över flera regioner
description: Lär dig hur du flyttar en hanterad virtuell dator över flera regioner
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 99371b8618756c196b75858288c5c4785272a7e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101650473"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Flytta en Azure automanage-virtuell dator till en annan region
Den här artikeln beskriver hur du behåller automanage-funktionen på en virtuell dator när du flyttar den till en annan region. Du kanske vill flytta dina virtuella datorer till en annan region av olika orsaker. Till exempel för att dra nytta av en ny Azure-region för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven. De virtuella datorer som du flyttar kan hanteras för närvarande och du kanske vill att de ska förbli hanterade efter flytten.

## <a name="prerequisites"></a>Förutsättningar
* Se till att mål regionen [stöds av automanage](./automanage-virtual-machines.md#prerequisites).
* Se till att din Log Analytics arbets ytans region, Automation-kontots region och mål region är alla regioner som stöds av region mappningarna [här](../automation/how-to/region-mappings.md).

## <a name="prepare-your-automanaged-vms-for-moving"></a>Förbered dina autohanterade virtuella datorer för flytt
Inaktivera autohantering på de autohanterade virtuella datorerna. Du kan göra detta genom att välja de virtuella datorerna på bladet hantera och klicka på **inaktivera automanagement** i bladet hantera automatiska inställningar.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Flytta dina autohanterade virtuella datorer och återaktivera autohantering
Mer information om hur du flyttar dina virtuella datorer finns i den här [artikeln](../resource-mover/tutorial-move-region-virtual-machines.md).

När du har flyttat dina virtuella datorer över flera regioner kan du återaktivera den automatiska hanteringen igen. Information finns [här](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Azure automanage](./automanage-virtual-machines.md)
* [Visa vanliga frågor om Azure automanage](./faq.md)