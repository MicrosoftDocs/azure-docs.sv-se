---
title: Avsnittet Aktivera hanterad identitet i Azure Event Grid system
description: I den här artikeln beskrivs hur du aktiverar hanterad tjänst identitet för ett Azure Event Grid system-ämne.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 66b418787e5570dc5da06a5332dd834ccbfd4aef
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630602"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Tilldela en Systemhanterad identitet till ett Event Grid system-ämne
I den här artikeln får du lära dig hur du aktiverar Systemhanterad identitet för ett befintligt Event Grid system-ämne. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> För närvarande kan du inte aktivera en Systemhanterad identitet när du skapar ett nytt system avsnitt, det vill säga när du skapar en händelse prenumeration på en Azure-resurs som har stöd för system ämnen. 


## <a name="use-azure-portal"></a>Använda Azure-portalen
Följande procedur visar hur du aktiverar Systemhanterad identitet för ett system ämne. 

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Sök efter **Event Grid system-ämnen** i Sök fältet högst upp.
3. Välj det **system ämne** för vilket du vill aktivera den hanterade identiteten. 
4. Välj **identitet** på den vänstra menyn. Du ser inte det här alternativet för ett system ämne som finns på den globala platsen. 
5. Aktivera **växeln** för att aktivera identiteten. 
1. Spara inställningen genom att välja **Spara** i verktygsfältet. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Sidan identitet för ett system ämne"::: 
1. Välj **Ja** i bekräftelse meddelandet. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Tilldela identitet till ett system ämne – bekräftelse"::: 
1. Bekräfta att du ser objekt-ID: t för den systemtilldelade hanterade identiteten och se en länk för att tilldela roller. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Tilldela identitet till ett system ämne-slutfört"::: 

## <a name="global-azure-sources"></a>Globala Azure-källor
Du kan endast aktivera Systemhanterad identitet för de regionala Azure-resurserna. Du kan inte aktivera det för system avsnitt som är kopplade till globala Azure-resurser som Azure-prenumerationer, resurs grupper eller Azure Maps. System ämnena för de här globala källorna är inte heller kopplade till en speciell region. Du ser inte sidan **identitet** för system-ämnet vars plats har angetts till **Global**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="System ämne med plats inställt på global"::: 



## <a name="next-steps"></a>Nästa steg
Lägg till identiteten i en lämplig roll (till exempel Service Bus data avsändare) på målet (till exempel en Service Bus kö). Detaljerade anvisningar finns i [lägga till identitet till Azure-roller på mål platser](add-identity-roles.md). 