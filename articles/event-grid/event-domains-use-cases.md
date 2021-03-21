---
title: Användnings fall för händelse domäner i Azure Event Grid
description: I den här artikeln beskrivs några användnings fall för att använda händelse domäner i Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204412"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Användnings fall för händelse domäner i Azure Event Grid
I den här artikeln beskrivs några användnings fall för att använda händelse domäner i Azure Event Grid. 

## <a name="use-case-1"></a>Användnings fall 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Användnings fall 2
Det finns en gräns på 500 händelse prenumerationer när du använder system ämnen. Om du behöver fler än 500 händelse prenumerationer för ett system ämne kan du använda domäner. 

Anta att du har skapat ett system avsnitt för en Azure-Blob Storage och du måste skapa fler än 500 prenumerationer på ämnet, men det är inte möjligt på grund av begränsningen (500 prenumerationer per ämne). I det här fallet kan du använda följande lösning som använder en händelse domän. 

1. Skapa en domän, som har stöd för upp till 100 000 ämnen och varje domän ämne kan ha 500 händelse prenumerationer. Den här modellen ger dig 500 * 100 000 händelse prenumerationer. 
1. Skapa en Azure Function-prenumeration för avsnittet Azure Storage systemet. När funktionen tar emot händelser från Azure Storage kan den utöka och publicera händelser till ett lämpligt domän ämne. Funktionen kan till exempel parsa BLOB-filnamnet för att fastställa mål domän ämnet och publicera händelsen till domän. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="AzureEvent Grid-domäner – användnings fall 2":::


## <a name="next-steps"></a>Nästa steg
Information om hur du konfigurerar händelse domäner, skapar ämnen, skapar händelse prenumerationer och publicerar händelser finns i [Hantera händelse domäner](./how-to-event-domains.md).
