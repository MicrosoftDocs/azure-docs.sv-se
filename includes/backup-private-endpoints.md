---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294152"
---
Du kan nu använda [privata slut punkter](../articles/private-link/private-endpoint-overview.md) för att säkerhetskopiera dina data på ett säkert sätt från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slut punkten använder en IP-adress från VNET-adressutrymmet för ditt valv. Nätverks trafiken mellan resurserna i det virtuella nätverket och valvet överförs över ditt virtuella nätverk och en privat länk i Microsoft stamnät nätverket. Detta eliminerar exponeringen från det offentliga Internet. Privata slut punkter kan användas för att säkerhetskopiera och återställa SQL-och SAP HANA-databaser som körs i dina virtuella Azure-datorer. Det kan också användas för lokala servrar som använder MARS-agenten.

Den virtuella Azure-säkerhetskopieringen kräver inte Internet anslutning och kräver därför inte privata slut punkter för att tillåta nätverks isolering.

Läs mer om privata slut punkter för Azure Backup [här](../articles/backup/private-endpoints.md).