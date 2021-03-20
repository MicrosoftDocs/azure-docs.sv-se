---
title: Azure VMware-lösning av CloudSimple-offentlig IP-adress
description: Lär dig mer om offentliga IP-adresser och deras fördelar med Azure VMware-lösningen av CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77024984"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Översikt över CloudSimple offentlig IP-adress

En offentlig IP-adress gör det möjligt för Internet resurser att kommunicera inkommande till privata moln resurser på en privat IP-adress. Den privata IP-adressen är en virtuell dator eller en belastningsutjämnare för program vara i ditt privata moln vCenter. Med den offentliga IP-adressen kan du exponera tjänster som körs i ditt privata moln till Internet.

Den offentliga IP-adressen är dedikerad till den privata IP-adressen tills du tar bort tilldelningen. En offentlig IP-adress kan bara tilldelas en privat IP-adress.

En resurs som är associerad med en offentlig IP-adress använder alltid den offentliga IP-adressen för Internet åtkomst. Som standard tillåts endast utgående Internet åtkomst på en offentlig IP-adress.  Inkommande trafik på den offentliga IP-adressen nekas.  Om du vill tillåta inkommande trafik skapar du en brand Väggs regel för den offentliga IP-adressen till den aktuella porten.

## <a name="benefits"></a>Fördelar

Genom att använda en offentlig IP-adress för att kommunicera inkommande tillhandahåller:

* DDoS-skydd (distributed denial of Service). Det här skyddet aktive ras automatiskt för den offentliga IP-adressen.
* Always on trafikövervakning och real tids minskning av vanliga attacker på nätverks nivå. De här försvaren är samma försvar som används av Microsoft onlinetjänster.
* Hela skalan för det globala Azure-nätverket. Nätverket kan användas för att distribuera och minimera angrepps trafik mellan regioner.  

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [allokerar en offentlig IP-adress](public-ips.md)