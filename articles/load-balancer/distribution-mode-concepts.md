---
title: Azure Load Balancer distributions lägen
description: Kom igång med att lära dig om de olika distributions lägena för Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99551445"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure Load Balancer distributions lägen

Azure Load Balancer stöder två distributions lägen för att dirigera anslutningar till ditt belastningsutjämnade program:

* Hash-baserad
* Källans IP-tillhörighet

## <a name="hash-based"></a>Hash-baserad

Standarddistributionsläget för Azure Load Balancer är en hash med fem tupplar. 

Tuppeln består av:
* **Käll-IP-adress**
* **Källport**
* **Mål-IP-adress**
* **Målport**
* **Protokoll typ**

Hashen används för att mappa trafik till de tillgängliga servrarna. Algoritmen ger varaktighet endast inom en transport-session. Paket i samma session dirigeras till samma data Center-IP bakom den belastningsutjämnade slut punkten. När klienten startar en ny session från samma käll-IP ändras käll porten och gör att trafiken går till en annan data Center slut punkt.

![Hash-baserat distributions läge för fem tupler](./media/distribution-mode-concepts/load-balancer-distribution.png)

Hash-baserat läge har en konfigurations typ:

* **Ingen (Hash-baserad)** – anger att efterföljande begär Anden från samma klient kan hanteras av alla virtuella datorer.

## <a name="source-ip-affinity"></a>Källans IP-tillhörighet

Det här distributionsläget kallas även för sessionstillhörighet eller klientens IP-tillhörighet. I läget används en hash med två tupler (käll-IP och mål-IP) eller tre tupler (käll-IP, mål-IP och protokoll typ) för att mappa trafik till de tillgängliga servrarna. 

Med hjälp av Källans IP-tillhörighet går anslutningar som startas från samma klient dator till samma data Center slut punkt.

Följande bild illustrerar en konfiguration med två tupler. Observera hur två tupler körs via belastningsutjämnaren till den virtuella datorn 1 (VM1). VM1 säkerhets kopie ras sedan av VM2 och VM3.

![Distributions läge för två tuple-tillhörighet](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Läget för källans IP-tillhörighet har två konfigurations typer:

* **Klientens IP-adress (käll-IP-tillhörighet 2-tupel)** – anger att efterföljande förfrågningar från samma klient-IP-adress ska hanteras av samma virtuella dator.
* **Klientens IP och protokoll (Källans IP-tillhörighet 3-tupel)** – anger att efterföljande förfrågningar från samma klient-IP-adress och protokoll kombination ska hanteras av samma virtuella dator.

## <a name="use-cases"></a>Användningsfall

Käll-IP-tillhörighet med klient-IP och protokoll (IP-tupel för käll-IP-adress) löser inkompatibilitet mellan Azure Load Balancer och Fjärrskrivbordsgateway (RD Gateway). 

Ett annat användnings Falls scenario är medie uppladdning. Data överföringen sker via UDP, men kontroll planet uppnås via TCP:

* En klient startar en TCP-session med den belastningsutjämnade offentliga adressen och dirigeras till en speciell DIP. Kanalen är aktiv för att övervaka anslutnings hälsan.
* En ny UDP-session från samma klient dator startas till samma belastningsutjämnade offentliga slut punkt. Anslutningen dirigeras till samma DIP-slutpunkt som den tidigare TCP-anslutningen. Medie uppladdningen kan köras vid högt data flöde och samtidigt upprätthålla en kontroll kanal via TCP.

> [!NOTE]
> När en belastningsutjämnad uppsättning ändras genom att ta bort eller lägga till en virtuell dator, beräknas distributionen av klient begär Anden om. Du kan inte vara beroende av nya anslutningar från befintliga klienter för att få slut på samma server. Dessutom kan distributions läget för källans IP-tillhörighet orsaka en ojämn fördelning av trafiken. Klienter som kör bakom proxyservrar kan ses som ett unikt klient program.


## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar distributions läget för Azure Load Balancer finns i [Konfigurera distributions läget för Azure Load Balancer](load-balancer-distribution-mode.md).

