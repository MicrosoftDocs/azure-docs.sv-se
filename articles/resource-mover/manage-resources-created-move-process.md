---
title: Hantera resurser som skapas under flyttningen av den virtuella datorn i Azure Resource superprocessen
description: Lär dig hur du hanterar resurser som skapas under flyttnings processen för virtuella datorer i Azure Resource-arbetskraften
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7ad0e73a90e733af0dd752100ebc71908f68181a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388489"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Hantera resurser som har skapats för flytt av virtuella datorer

Den här artikeln beskriver hur du hanterar resurser som skapas direkt av [Azure Resource-arbetskraften](overview.md) för att under lätta migreringen av den virtuella datorn. 

När du har flyttat virtuella datorer över flera regioner finns det ett antal resurser som har skapats av resurs förflyttning som bör rensas manuellt.

## <a name="delete-resources-created-for-vm-move"></a>Ta bort resurser som har skapats för flytt av virtuella datorer

Ta bort flyttnings samlingen manuellt och Site Recovery resurser som skapats för flyttning av den virtuella datorn.

1. Granska resurserna i resurs gruppen ```ResourceMoverRG-<sourceregion>-<target-region>``` .
2. Kontrol lera att den virtuella datorn och alla andra käll resurser i flyttnings samlingen har flyttats/tagits bort. Detta säkerställer att det inte finns några väntande resurser som använder dem.
2. Ta bort dessa resurser.

    - Namnet på flyttnings samlingen är ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` .
    - Namnet på cachens lagrings konto är ```resmovecache<guid>```
    - Valv namnet är ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Nästa steg

Försök att [flytta en virtuell dator](tutorial-move-region-virtual-machines.md) till en annan region med resurs förflyttning.
