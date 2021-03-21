---
title: Konfigurera skalning för Azure Cloud Services (utökad support)
description: Aktivera skalnings alternativ för Azure Cloud Services (utökad support)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cfa5be01a0d36764086c6c9adf97e6cb166d2bb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728169"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Konfigurera skalnings alternativ med Azure Cloud Services (utökad support) 

Villkor kan konfigureras för att aktivera Cloud Services-distributioner (utökad support) för att skala in och ut. Dessa villkor kan baseras på processor användning, disk belastning och nätverks belastning. 

Överväg följande information när du konfigurerar skalning av moln tjänst distributioner:
- Skalning påverkar kärn användningen. Större roll instanser förbrukar fler kärnor och du kan bara skala inom din prenumerations kärn gräns. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Skalning som baseras på tröskeln för Queue Messaging stöds. Mer information finns i [Kom igång med Azure Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md).
- Se till att distribuera med två eller flera roll instanser för att säkerställa hög tillgänglighet för dina moln tjänst program (utökade support).
- Anpassad autoskalning kan bara utföras när alla roller har statusen **klar** .

## <a name="configure-and-manage-scaling"></a>Konfigurera och hantera skalning

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj den moln tjänst (utökad support) distribution som du vill konfigurera skalning på. 
3. Välj **skala** -bladet. 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Bild visar hur du väljer alternativet fjärr skrivbord i Azure Portal":::

4. På en sida visas en lista över alla roller där skalning kan konfigureras. Välj den roll som du vill konfigurera. 
5. Välj den typ av skala som du vill konfigurera
    - **Manuell skalning** anger det absoluta antalet instanser.
        1. Välj **manuell skalning**.
        2. Ange antalet instanser som du vill skala upp eller ned till.
        3. Välj **Spara**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Bild som visar inställning av manuell skalning i Azure Portal":::

        4. Skalnings åtgärden startar omedelbart. 
        
    - Med **anpassad autoskalning** kan du ange regler som styr hur mycket eller hur lite som ska skalas. 
        1. Välj **anpassad autoskalning**
        2. Välj att skala baserat på ett mått eller instans antal.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Bild som visar inställning av anpassad autoskalning i Azure Portal":::

        3. Om skalning baserat på ett mått lägger du till regler som behövs för att uppnå önskade skalnings resultat.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Bild som visar inställning av anpassade regler för autoskalning i Azure Portal":::

        4. Välj **Spara**.
        5. Skalnings åtgärderna börjar så snart en regel utlöses.
        
6. Du kan visa eller justera befintliga skalnings regler som tillämpas på dina distributioner genom att välja fliken **skala** .

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Bilden visar justering av en befintlig skalnings regel i Azure Portal":::

## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).