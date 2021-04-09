---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008502"
---
Den här artikeln förutsätter att

1. En **plats-till-plats-VPN** eller en **Express Route**-anslutning redan har upprättats mellan ditt lokala nätverk och Azure Virtual Network.
2. Ditt användarkonto har behörighet att skapa en ny virtuell dator på den Azure-prenumeration som de virtuella datorerna har redundansväxlats till.
3. Din prenumeration har minst 8 kärnor tillgängliga för att skapa en ny virtuell dator för processervern.
4. Du har **konfigurationsserverns lösenfras** tillgänglig.

> [!TIP]
> Se till att du kan ansluta port 443 för konfigurationsservern (körs lokalt) från det virtuella Azure-nätverk som de virtuella datorerna har redundansväxlats till.
