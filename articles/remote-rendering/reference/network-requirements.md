---
title: Nätverkskrav
description: Nätverks krav och bästa nätverks praxis för optimal upplevelse
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fd8686cc396d5fcee20590fbac8bccaf187b024d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101735955"
---
# <a name="network-requirements"></a>Nätverkskrav

En stabil nätverks anslutning med låg latens till ett Azure-datacenter är avgörande för en utmärkt användar upplevelse i Azure Remote rendering. Dåliga nätverks förhållanden kan resultera i tappade anslutningar, instabila, Darr eller "hoppa"-hologram och en tydlig fördröjning vid uppdatering av scen diagrammet på Server sidan.

## <a name="guidelines-for-network-connectivity"></a>Rikt linjer för nätverks anslutning

De exakta nätverks kraven beror på ditt specifika användnings fall, t. ex. antal och frekvens för ändringar i grafen för fjärrscener samt komplexiteten för den renderade vyn, men det finns ett antal rikt linjer för att se till att din upplevelse är så bra som möjligt:

* Din Internet anslutning behöver stöd för minst **40 Mbit/s** och **5 Mbit/s uppströms** konsekvent för en enda användarsession av Azure fjärrrendering, förutsatt att det inte finns någon konkurrerande trafik i nätverket. Vi rekommenderar högre priser för bättre upplevelser. 
* **Wi-Fi** är den rekommenderade nätverks typen eftersom den stöder en låg latens, hög bandbredd och stabil anslutning. Vissa mobila nätverk introducerar Darr som kan leda till en dålig upplevelse. 
* Om du använder **5-ghz Wi-Fi-bandet** får du normalt bättre resultat än 2,4 GHz Wi-Fi-bandet, men båda bör fungera.
* Om det finns andra Wi-Fi nätverk i närheten bör du undvika att använda Wi-Fi kanaler som används av dessa andra nätverk. Du kan använda verktyg för nätverks genomsökning som [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) för att kontrol lera om de kanaler som Wi-Fi nätverket använder är fria från konkurrerande trafik.
* **Undvik att använda Wi-Fi repeterbarer** eller en LAN-överdrived vidarebefordring.
* **Undvik konkurrerande bandbredds krävande trafik** – till exempel video eller spel direkt uppspelning – på samma Wi-Fi nätverk.
* Om du har flera enheter på samma åtkomst punkt skalas kraven på motsvarande sätt. Om du har flera åtkomst punkter i en miljö, belastnings Utjämnings enheter över åtkomst punkterna så att de distribueras jämnt.
* Det är viktigt att ha **bra Wi-Fi signal styrka** . Om möjligt ligger du nära din Wi-Fi åtkomst punkt och undviker hinder mellan din klient enhet och åtkomst punkterna.
* Se till att du alltid ansluter till **närmaste Azure-datacenter** för din [region](regions.md). Ju närmare data centret, desto lägre nätverks fördröjning, vilket har stor inverkan på hologram stabiliteten.

> [!NOTE]
> Den underordnade bandbredden används främst av video strömmen, som i sin tur är uppdelad mellan färg-och djup information (både 60 Hz, stereo).

## <a name="network-performance-tests"></a>Tester av nätverks prestanda

Om du vill ha en inledande förståelse för om kvaliteten på din nätverks anslutning räcker för att köra Azure-fjärrrendering finns befintliga online-verktyg som du kan använda. Vi rekommenderar starkt att du kör de här online verktygen från en rimlig kraftfull bärbar dator som är ansluten till samma Wi-Fi som den enhet som du planerar att köra klient programmet för Azure-fjärrrendering på. Resultat som erhålls från att köra testerna på en mobil telefon eller HoloLens2 är vanligt vis mindre användbara eftersom de har beprövat för att visa betydande variation på slut punkts enheter med låg belastning. Den plats där du placerar den bärbara datorn bör vara ungefär på samma plats som du förväntar dig att använda enheten som kör klient programmet för Azure-fjärrrendering.

Här följer några enkla steg för att snabbt testa nätverks anslutningen:

1. **Kör ett nätverks test verktyg som www.speedtest.net för att hämta data om den övergripande svars tiden och den överordnade/underordnade bandbredden för nätverks anslutningen.**
Välj en server som är närmast dig och kör testet. Även om servern inte kommer att vara Azure-datacenter som Azure-fjärrrenderingen kommer att ansluta till, är de resulterande data fortfarande användbara för att förstå prestanda hos din Internet anslutning och Wi-Fi.
   * **Minimi krav** för Azure-fjärrrendering: ca. 40 Mbit/s och 5 Mbit/s överström.
   * **Rekommenderas** för Azure-fjärrrendering: ca. 100 Mbit/s och 10 Mbit/s överström.
Vi rekommenderar att du kör testet flera gånger och tar de sämsta resultaten.
1. **Använd ett verktyg som www.azurespeed.com som mäter svars tiden på Azures Data Center**. Välj det Azure-datacenter som stöds av Azure Remote rendering som är närmast dig (se [regioner som stöds](regions.md)) och kör ett **svars svar**. Om det finns variation i de siffror som du ser ger du resultatet lite tid för att stabilisera.
   * **Minimi krav** för Azure-fjärrrendering: fördröjningen bör konsekvent vara mindre än 80 ms.
   * **Rekommenderas** för Azure-fjärrrendering: fördröjningen bör konsekvent vara mindre än 40 MS.

Även om låg latens inte är en garanti för att Azures fjärrrendering fungerar bra i nätverket, har vi vanligt vis kunnat utföra böter i situationer där de här testerna lyckades.
Om du stöter på artefakter som instabila, Darr eller rör hologram när du kör Azure fjärrrendering, se [fel söknings guiden](../resources/troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

* [Snabb start: rendera en modell med Unity](../quickstarts/render-model.md)
