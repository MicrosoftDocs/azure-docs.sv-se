---
title: Vad är API:et för avvikelseidentifiering?
titleSuffix: Azure Cognitive Services
description: Använd Avvikelseidentifiering-API:et för att tillämpa avvikelseidentifiering på dina tidsseriedata.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: avvikelseidentifiering, maskininlärning, algoritmer
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d63399d0f492f85a4a2d57a595a6d8ef5b606d92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599527"
---
# <a name="what-is-the-anomaly-detector-api"></a>Vad är API:et för avvikelseidentifiering?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Med Avvikelseidentifiering-API:et kan du övervaka och identifiera avvikelser i dina tidsseriedata utan att behöva känna till maskininlärning. Algoritmerna Avvikelseidentifiering API:et anpassas genom att automatiskt identifiera och tillämpa de bäst anpassade modellerna på dina data, oavsett bransch, scenario eller datavolym. Med hjälp av dina tidsseriedata fastställer API:et gränser för avvikelseidentifiering, förväntade värden och vilka datapunkter som är avvikelser.

![Identifiera mönsterändringar i tjänstbegäranden](./media/anomaly_detection2.png)

Användning av Avvikelseidentifiering kräver ingen tidigare erfarenhet av maskininlärning, och MED RESTful-API:et kan du enkelt integrera tjänsten i dina program och processer.

Den här dokumentationen innehåller följande typer av artiklar:
* [Snabbstarterna](./Quickstarts/client-libraries.md) är stegvisa instruktioner som gör att du kan göra anrop till tjänsten och få resultat på kort tid. 
* [Instruktionsguiderna innehåller](./how-to/identify-anomalies.md) instruktioner för att använda tjänsten på mer specifika eller anpassade sätt.
* [Konceptuella](./concepts/anomaly-detection-best-practices.md) artiklar innehåller detaljerade förklaringar av tjänstens funktioner och funktioner.
* [Självstudierna](./tutorials/batch-anomaly-detection-powerbi.md) är längre guider som visar hur du använder den här tjänsten som en komponent i bredare affärslösningar.

## <a name="features"></a>Funktioner

Med Avvikelseidentifiering kan du automatiskt identifiera avvikelser i dina tidsseriedata eller när de inträffar i realtid.

|Funktion  |Beskrivning  |
|---------|---------|
|Avvikelseidentifiering i realtid. | Identifiera avvikelser i dina strömmande data med hjälp av tidigare datapunkter för att avgöra om den senaste är en avvikelse. Den här åtgärden genererar en modell med hjälp av de datapunkter som du skickar och avgör om målpunkten är en avvikelse. Genom att anropa API:et med varje ny datapunkt som du genererar kan du övervaka dina data när de skapas. |
|Identifiera avvikelser i hela datauppsättningen som en batch. | Använd tidsserien för att identifiera eventuella avvikelser som kan finnas i dina data. Den här åtgärden genererar en modell med hela tidsseriedata, där varje punkt analyseras med samma modell.         |
|Identifiera ändringspunkter i hela datauppsättningen som en batch. | Använd tidsserien för att identifiera eventuella trendändringspunkter som finns i dina data. Den här åtgärden genererar en modell med hjälp av hela tidsseriedata, där varje punkt analyseras med samma modell.    |
| Få ytterligare information om dina data. | Få användbar information om dina data och eventuella observerade avvikelser, inklusive förväntade värden, avvikelsegränser och positioner. |
| Justera gränser för avvikelseidentifiering. | API:Avvikelseidentifiering skapar automatiskt gränser för avvikelseidentifiering. Justera dessa gränser för att öka eller minska API:ets känslighet för dataavvikelser och passar bättre för dina data. |

## <a name="demo"></a>Demo

Kolla in den [här interaktiva demonstrationen](https://aka.ms/adDemo) för att förstå hur Avvikelseidentifiering fungerar.
För att köra demonstrationen måste du skapa en Avvikelseidentifiering och hämta API-nyckeln och slutpunkten.

## <a name="notebook"></a>Notebook-fil

Om du vill lära dig hur du anropar Avvikelseidentifiering-API:et kan du prova den här [notebook-datorn.](https://aka.ms/adNotebook) Den Jupyter Notebook visar hur du skickar en API-begäran och visualiserar resultatet.

Utför följande steg för att köra notebook-datorn:

1. Hämta en giltig Avvikelseidentifiering API-prenumerationsnyckel och en API-slutpunkt. Avsnittet nedan innehåller anvisningar för att registrera dig.
1. Logga in och välj Klona i det övre högra hörnet.
1. Avmarkera alternativet "offentlig" i dialogrutan innan du slutför kloningsåtgärden. Annars är notebook-datorn, inklusive eventuella prenumerationsnycklar, offentlig.
1. Välj **Kör på kostnadsfri beräkning**
1. Välj en av notebook-anteckningsböckerna.
1. Lägg till din Avvikelseidentifiering API-prenumerationsnyckel i `subscription_key` variabeln .
1. Ändra `endpoint` variabeln till slutpunkten. Exempelvis: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. På den översta menyraden väljer du **Cell** och sedan **Kör alla.**

## <a name="workflow"></a>Arbetsflöde

API:Avvikelseidentifiering är en RESTful-webbtjänst, vilket gör det enkelt att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

När du har loggat in:

1. Ta tidsseriedata och konvertera dem till ett giltigt JSON-format. Använd [bästa praxis när](concepts/anomaly-detection-best-practices.md) du förbereder dina data för att få bästa möjliga resultat.
1. Skicka en begäran till Avvikelseidentifiering-API:et med dina data.
1. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="algorithms"></a>Algoritmer

* Se följande tekniska bloggar för information om de algoritmer som används:
    * [Introduktion till API för Azure-avvikelseidentifiering](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Översikt över SR-CNN-algoritmen i Azure Avvikelseidentifiering](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Du kan läsa artikeln [Time-Series Anomaly Detection Service på Microsoft](https://arxiv.org/abs/1906.03821) (accepteras av KDD 2019) om du vill veta mer om SR-CNN-algoritmer som utvecklats av Microsoft.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>Tjänsttillgänglighet och redundans

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Är Avvikelseidentifiering-tjänstzonen motståndskraftig?

Ja. Tjänsten Avvikelseidentifiering är zontålig som standard.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>Hur gör jag för att konfigurera Avvikelseidentifiering-tjänsten så att den är zontålig?

Ingen kundkonfiguration krävs för att aktivera zonåter återhämtning. Zonåter återhämtning för Avvikelseidentifiering resurser är tillgänglig som standard och hanteras av själva tjänsten.

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuera lokalt med Docker-containrar

[Använd Avvikelseidentifiering för](anomaly-detector-container-howto.md) att distribuera API-funktioner lokalt. Med Docker-containrar kan du föra tjänsten närmare dina data av efterlevnads-, säkerhets- eller andra driftskäl.

## <a name="join-the-anomaly-detector-community"></a>Delta i communityn för avvikelseidentifiering

* Gå med [Avvikelseidentifiering Advisors-gruppen i Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Se valt [användargenererat innehåll](user-generated-content.md)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Identifiera avvikelser i dina tidsseriedata med hjälp av Avvikelseidentifiering](quickstarts/client-libraries.md)
* Onlinedemo Avvikelseidentifiering [API:et](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook)
* Referens Avvikelseidentifiering [REST API för](https://aka.ms/anomaly-detector-rest-api-ref)
