---
title: Förstå priser för Azure Data Share
description: Lär dig hur priser för Azure Data Share fungerar
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88137041"
---
# <a name="understand-azure-data-share-pricing"></a>Förstå priser för Azure Data Share

För ögonblicks bilds-baserad delning, debiteras Azure Data Share för data uppsättning ögonblicks bilder och ögonblicks bild. Den här artikeln förklarar hur du beräknar data uppsättnings ögonblicks bilder och ögonblicks bild körning med information om ögonblicks bild För närvarande debiteras dataprovidern för ögonblicks bilden av data uppsättningen och ögonblicks bilds körningen.

## <a name="dataset-snapshot"></a>Data uppsättnings ögonblicks bild

Data uppsättnings ögonblicks bild är åtgärden att flytta data uppsättningen från källan till målet. När ögonblicks bilden tas för en resurs är ögonblicks bilden av varje data uppsättning i resursen en data uppsättning för ögonblicks bilder. Följ stegen nedan om du vill visa en lista över ögonblicks bilder av data uppsättningar. 

1. I Azure Portal navigerar du till din data resurs resurs.

1. Välj en resurs från en skickad resurs eller mottagen resurs.

1. Klicka på fliken **Historik** .

1. Klicka på Start tid för en ögonblicks bild.
 
    ![Ögonblicks bilds historik](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Ögonblicks bilds historik") 

1. Visa listan över åtgärder för data uppsättnings ögonblicks bilder. Varje rad objekt motsvarar en data uppsättning för ögonblicks bilder. I det här exemplet finns det två ögonblicks bilder av data uppsättningar.

    ![Åtgärd för data uppsättnings ögonblicks bild](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Åtgärd för data uppsättnings ögonblicks bild")

## <a name="snapshot-execution"></a>Ögonblicks bild körning

Ögonblicks bilds körningen innehåller de resurser som krävs för att flytta en data uppsättning från källan till målet. För varje data uppsättning ögonblicks bilds åtgärd beräknas ögonblicks bilds körningen som antalet virtuella kärnor multiplicerat med ögonblicks bildens varaktighet. Avgifterna beräknas per minut och avrundas uppåt. Antalet vCore väljs dynamiskt baserat på källans mål par och data mönster. Följ stegen nedan för att Visa ögonblicks bildens start tid, slut tid och virtuella kärnor som används för en data uppsättning för ögonblicks bilder.

1. I Azure Portal navigerar du till din data resurs resurs.

1. Välj en resurs från en skickad resurs eller mottagen resurs.

1. Klicka på fliken **Historik** .

1. Klicka på Start tid för en ögonblicks bild.

    ![Ögonblicks bilds historik](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Ögonblicks bilds historik") 

1. Klicka på status för en data mängds ögonblicks bild åtgärd.

    ![Status för data uppsättnings ögonblicks bild](./media/concepts/concepts-pricing/pricing-snapshot-status.png "Status för data uppsättnings ögonblicks bild")

1. Visa start tid, slut tid och virtuella kärnor som används för den här data uppsättningens ögonblicks bild åtgärd. 

    ![Ögonblicks bild körning](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "Ögonblicks bild körning")

## <a name="next-steps"></a>Nästa steg

- Hämta den senaste pris informationen – [priser för Azure Data Share](https://azure.microsoft.com/pricing/details/data-share/)
- Använd pris Kalkylatorn för Azure för att beräkna kostnad – [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/)
