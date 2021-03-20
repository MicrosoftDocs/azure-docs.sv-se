---
title: Fördelar med att använda Azure NetApp Files för elektronisk design automatisering | Microsoft Docs
description: Förklarar lösnings Azure NetApp Files ger till gång till behoven hos halvledar-och krets design branschen. Visar test scenarier som kör ett standard bransch riktmärke för elektronisk design automatisering (EDA) med hjälp av Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82160161"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>Fördelar med att använda Azure NetApp Files för elektronisk designautomatisering

Time-to-marknad (TTM) är ett kritiskt övervägande för halvledar-och krets design branschen. Branschen har hög bandbredd och låg latens behov för lagring. I den här artikeln förklaras lösnings Azure NetApp Files som uppfyller branschens behov. Det visar test scenarier som kör ett standard bransch riktmärke för elektronisk design automatisering (EDA) med hjälp av Azure NetApp Files. 

## <a name="test-scenario-configurations"></a>Konfiguration av test scenario

Testerna omfattar tre scenarier med följande konfigurationer. 

|    Scenario    |    Volymer    |    Klienter<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    En         |    1          |    1                           |
|    Två         |    6          |    24                          |
|    Tre       |    12         |    24                          |

Det första scenariot tar itu med hur mycket en enskild volym kan drivas.  

Den andra och de tredje scenarierna utvärderar gränserna för en enda Azure NetApp Files slut punkt. De undersöker de potentiella fördelarna med I/O-övre gränser och svars tider.

## <a name="test-scenario-results"></a>Resultat av test scenario

I följande tabell sammanfattas resultaten av test scenarier.

|    Scenario       |    I/O-hastighet<br>  vid 2 MS     |    I/O-hastighet<br>  i kanten     |    Dataflöde<br>  vid 2 MS     |    Dataflöde<br>  i kanten     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 volym       |    39 601                 |    49 502                      |    692 MiB/s                 |    866 MiB/s                      |
|    6 volymer      |    255 613                |    317 000                     |    4 577 MiB/s               |    5 568 MiB/s                    |
|    12 volymer     |    256 612                |    319 196                     |    4 577 MiB/s               |    5 709 MiB/s                    |

Scenariot med en enskild volym representerar den grundläggande program konfigurationen. Det är bas linje scenariot för test scenarier med uppföljning.  

Det sex volym scenariot visar en linjär ökning (600%) i förhållande till arbets belastningen för en enskild volym.  Alla volymer i ett enda virtuellt nätverk nås via en enskild IP-adress.  

12-volyms scenariot visar en allmän minskning av svars tiden i det sex volym scenariot. Men det har inte en motsvarande ökning av det data flöde som kan uppnås.   

Följande diagram illustrerar svars tiden och åtgärdernas hastighet för EDA-arbetsbelastningen på Azure NetApp Files.  

![Latens och åtgärds takt för EDA-arbetsbelastningen på Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

Följande diagram illustrerar svars tiden och data flödet för EDA-arbetsbelastningen på Azure NetApp Files.  

![Svars tid och data flöde för EDA-arbetsbelastningen på Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>Layout för test scenarier 

I tabellen nedan sammanfattas layouten för test scenarier.

|    Test scenario     |    Totalt antal kataloger     |    Totalt antal filer     |
|----------------------|------------------------------------|------------------------------|
|    1 volym          |    88 000                          |    880 000                   |
|    6 volymer         |    568 000                         |    5 680 000                 |
|    12 volymer        |    568 000                         |    5 680 000                 |

Den kompletta arbets belastningen är en blandning av pågående funktionella och fysiska faser. Den representerar ett typiskt flöde från en uppsättning EDA-verktyg till en annan.   

Funktions fasen består av inledande specifikationer och en logisk design. Den fysiska fasen äger rum när den logiska designen konverteras till ett fysiskt chip. Under avstängnings-och band faserna slutförs de slutliga kontrollerna och designen levereras till en gjuteri för tillverkning.  

Funktions fasen innehåller en blandning av sekventiella och slumpmässiga Läs-och skriv-I/O. Funktions fasen är metadata intensiv, t. ex. fil stats-och åtkomst anrop. Även om metadata-åtgärder effektivt inte är i storlek utan storlek, är Läs-och skriv åtgärder mellan mindre än 1 K och 16 KB. De flesta läsningar är mellan 4 K och 16 KB.  De flesta skrivningar är 4 K eller mindre. Den fysiska fasen består av sekventiella Läs-och skriv åtgärder helt och hållet med en blandning av 32 K och 64 K OP-storlekar.  

I diagrammen ovan kommer merparten av data flödet från den sekventiella fysiska fasen av arbets belastningen. I/O kommer från den små slumpmässiga och metadata-intensiva funktions fasen. Båda faserna sker parallellt. 

I slutet kan du länka Azure Compute med Azure NetApp Files för EDA-design för att få skalbar bandbredd. 

## <a name="next-steps"></a>Nästa steg

- [Lösningsarkitekturer med Azure NetApp Files](azure-netapp-files-solution-architectures.md)
