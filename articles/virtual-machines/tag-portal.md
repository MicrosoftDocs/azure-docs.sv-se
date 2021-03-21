---
title: Tagga en virtuell dator med hjälp av Azure Portal
description: Lär dig mer om att tagga en virtuell dator med hjälp av Azure Portal.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897416"
---
# <a name="tagging-a-vm-using-the-portal"></a>Tagga en virtuell dator med hjälp av portalen

Den här artikeln beskriver hur du lägger till taggar till en virtuell dator med hjälp av portalen. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resurs grupp. Azure har för närvarande stöd för upp till 50 Taggar per resurs och resurs grupp. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller läggs till i en befintlig resurs.


1. Navigera till den virtuella datorn i portalen.
1. I **Essentials** väljer **du klicka här om du vill lägga till taggar**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Skärm bild av avsnittet Essentials på sidan för den virtuella datorn.":::

1. Lägg till ett värde för **namn** och **värde** och välj sedan **Spara**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Skärm bild av sidan för att lägga till ett nyckel värde par som en tagg.":::

### <a name="next-steps"></a>Nästa steg

- Läs mer om att tagga dina Azure-resurser i [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md) och [använda taggar för att ordna dina Azure-resurser](../azure-resource-manager/management/tag-resources.md).
- Information om hur taggar kan hjälpa dig att hantera din användning av Azure-resurser finns i [förstå din Azure-faktura](../cost-management-billing/understand/review-individual-bill.md).
