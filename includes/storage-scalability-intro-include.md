---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75477153"
---
Den här referensen beskriver skalbarhet och prestanda mål för Azure Storage. De skalbarhets-och prestanda mål som anges här är avancerade mål, men kan nås. I samtliga fall beror den begär ande frekvensen och bandbredden som uppnås av ditt lagrings konto på storleken på objekt som lagras, åtkomst mönster som används och vilken typ av arbets belastning som programmet utför.

Se till att testa tjänsten för att avgöra om prestandan uppfyller dina krav. Undvik om möjligt plötsliga toppar i trafik hastigheten och se till att trafiken är väl distribuerad mellan partitioner.

När ditt program når gränsen för vad en partition kan hantera för arbets belastningen börjar Azure Storage returnera felkoden 503 (servern är upptagen) eller felkod 500 (åtgärds tids gräns). Om 503-fel uppstår bör du överväga att ändra ditt program så att det använder en exponentiell backoff-princip för återförsök. Med exponentiell backoff kan belastningen på partitionen minskas, och för att under lätta toppar i trafik till den partitionen.
