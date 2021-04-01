---
title: Övervaka Azures data resurs
description: Lär dig hur du övervakar status för inbjudan, delar prenumerationer och ögonblicks bilds historik i Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910482"
---
# <a name="monitor-azure-data-share"></a>Övervaka Azure Data Share  

Den här artikeln förklarar hur du kan övervaka dina data resurser med Azure Data Share. Som data leverantör kan du övervaka olika aspekter av dina data delnings relationer. Information som till exempel om dina data konsumenter har accepterat din inbjudan till data resursen, samt om de har skapat en resurs prenumeration och börjat använda dina data, är tillgängliga för övervakning. 

Som data konsument kan du övervaka ögonblicks bilderna som har utlösts i din Azure-prenumeration. 

## <a name="monitor-invitation-status"></a>Status för övervaka inbjudan

Visa statusen för dina inbjudningar för data delning genom att navigera till skickade resurser – > inbjudningar. 

![Status för inbjudan](./media/invitation-status.png "Status för inbjudan") 

Din inbjudan kan vara i tre lägen:

* Väntande-data delnings mottagare har ännu inte accepterat inbjudan.
* Godkänd – mottagaren av data delning har accepterat inbjudan.
* Avvisad-data delnings mottagare har avvisat inbjudan.

> [!IMPORTANT]
> Om du tar bort en inbjudan när den redan har godkänts är den inte likvärdig med att återkalla åtkomst. Om du vill stoppa framtida ögonblicks bilder från att kopieras till ditt data konsument lagrings konto måste du återkalla åtkomsten via fliken *dela prenumerationer* . 

## <a name="monitor-share-subscriptions"></a>Övervaka resurs prenumerationer

Visa statusen för dina resurs prenumerationer genom att navigera till skickade resurser-> dela prenumerationer. Detta ger dig information om aktiva prenumerationer som skapats av dina data konsumenter när du har accepterat din inbjudan. Du kan stoppa framtida uppdateringar av din data konsument genom att välja resurs prenumerationen och välja *återkalla*. 

## <a name="snapshot-history"></a>Ögonblicks bilds historik 

På fliken **Historik** i en resurs kan du se när data kopieras från dataprovidern till data konsumentens data lager. Du kan övervaka frekvens, varaktighet och status för varje ögonblicks bild. 

![Skärm bild som visar skickade resurser i Azure Portal.](./media/sent-shares.png "Ögonblicks bilds historik") 

Du kan visa mer information om varje ögonblicks bild körning genom att klicka på start datum för körning. Klicka sedan på status för varje data uppsättning för att visa mängden data som överförs, antalet filer/poster som har kopierats, varaktigheten för ögonblicks bilden, antalet virtuella kärnor som används och fel meddelandet om det finns några. 

Upp till 30 dagar efter ögonblicks bilds historik visas. Om du behöver spara och se mer än 30 dagar i historiken kan du utnyttja den diagnostiska inställningen.

## <a name="diagnostic-setting"></a>Diagnostisk inställning

Du kan konfigurera diagnostikinställningar för att spara loggdata eller händelser. Navigera till övervakning – > diagnostikinställningar och välj **Lägg till diagnostisk inställning**. Välj de logg data eller händelser som du är intresse rad av och var du vill lagra eller skicka dem. 

![Skärm bild som visar sidan diagnostikinställningar i Azure Portal.](./media/diagnostic-settings.png "Diagnostikinställningar") 

## <a name="next-steps"></a>Nästa steg 

Lär dig mer om [Azure Data Share-terminologi](terminology.md)