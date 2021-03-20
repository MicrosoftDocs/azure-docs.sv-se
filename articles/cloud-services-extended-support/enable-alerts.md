---
title: Aktivera övervakning i Cloud Services (utökad support) med hjälp av Azure Portal
description: Aktivera övervakning av Cloud Services-instanser (utökade support) med hjälp av Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744856"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Aktivera övervakning för Cloud Services (utökad support) med hjälp av Azure Portal

I den här artikeln förklaras hur du aktiverar aviseringar för befintliga distributioner av moln tjänster (utökad support). 

## <a name="add-monitoring-rules"></a>Lägg till övervaknings regler
1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj den moln tjänst (utökad support) distribution som du vill aktivera aviseringar för. 
3. Välj bladet **aviseringar** . 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Bilden visar hur du väljer fliken aviseringar i Azure Portal.":::

4. Välj ikonen **ny avisering** .
     :::image type="content" source="media/enable-alerts-2.png" alt-text="Bilden visar hur du väljer alternativet Lägg till ny avisering.":::

5. Mata in önskade villkor och nödvändiga åtgärder baserat på vilka mått du vill spåra. Du kan definiera regler baserat på enskilda mått eller aktivitets loggen. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="Bild visar var du kan lägga till villkor i aviseringar.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="Bild som visar konfiguration av signal logik.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="Bild som visar hur du konfigurerar åtgärds grupp logik.":::

6. När du är klar med att konfigurera aviseringar sparar du ändringarna och baseras på de mått som kon figurer ATS. du kommer att börja se att **aviserings** bladet fylls med tid.

## <a name="next-steps"></a>Nästa steg 
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).