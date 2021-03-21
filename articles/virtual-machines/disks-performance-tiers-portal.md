---
title: Ändra prestanda för Azure Managed disks med hjälp av Azure Portal
description: Lär dig hur du ändrar prestanda nivåer för nya och befintliga hanterade diskar med hjälp av Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901042"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Ändra prestanda nivån med hjälp av Azure Portal

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Komma igång

### <a name="new-disks"></a>Nya diskar

Följande steg visar hur du ändrar diskens prestanda nivå när du först skapar disken:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Navigera till den virtuella dator som du vill skapa en ny disk för.
1. När du väljer den nya disken väljer du först den storlek som du behöver.
1. När du har valt en storlek väljer du en annan prestanda nivå för att ändra dess prestanda.
1. Klicka på **OK** för att skapa disken.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Skärm bild av bladet disk skapande, en disk är markerad och list rutan prestanda nivå är markerad." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Befintliga diskar

Följande steg beskriver hur du ändrar prestanda nivån för en befintlig disk:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Navigera till den virtuella dator som innehåller disken som du vill ändra.
1. Frigör den virtuella datorn eller koppla från disken.
1. Välj din disk
1. Välj **storlek + prestanda**.
1. I list rutan **prestanda nivå** väljer du en annan nivå än diskens aktuella prestanda nivå.
1. Välj **Ändra storlek**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Skärm bild av bladet storlek och prestanda är prestanda nivån markerad." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Nästa steg

Om du behöver ändra storlek på en disk för att kunna utnyttja de högre prestanda nivåerna kan du läsa följande artiklar:

- [Expandera virtuella hård diskar på en virtuell Linux-dator med Azure CLI](linux/expand-disks.md)
- [Expandera en hanterad disk som är ansluten till en virtuell Windows-dator](windows/expand-os-disk.md)
