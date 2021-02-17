---
title: Vad är API:et för avvikelseidentifiering?
titleSuffix: Azure Cognitive Services
description: Använd algoritmer för avvikelse detektorns API för att tillämpa avvikelse identifiering på dina tids serie data.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: avvikelse identifiering, maskin inlärning, algoritmer
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: ad3bd1eed6e5e7efb783c4a40958e16c3ca23691
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572079"
---
# <a name="what-is-the-anomaly-detector-api"></a>Vad är API:et för avvikelseidentifiering?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Med API: t för avvikelse identifiering kan du övervaka och identifiera avvikelser i dina tids serie data utan att behöva känna till Machine Learning. Algoritmerna för avvikelse detektorns API anpassas genom att automatiskt identifiera och tillämpa de bästa passnings modellerna för dina data, oavsett bransch, scenario eller data volym. Med hjälp av dina Time Series-data fastställer API gränser för avvikelse identifiering, förväntade värden och vilka data punkter som är avvikelser.

![Identifiera mönster ändringar i tjänst begär Anden](./media/anomaly_detection2.png)

Att använda avvikelse detektorn kräver ingen tidigare erfarenhet av Machine Learning, och RESTful-API: et gör att du enkelt kan integrera tjänsten i dina program och processer.

## <a name="features"></a>Funktioner

Med avvikelse identifieraren kan du automatiskt identifiera avvikelser i tids serie data, eller när de inträffar i real tid.

|Funktion  |Beskrivning  |
|---------|---------|
|Avvikelse identifiering i real tid. | Identifiera avvikelser i dina strömmande data genom att använda tidigare visade data punkter för att avgöra om det senaste är en avvikelse. Den här åtgärden genererar en modell med de data punkter som du skickar och avgör om mål punkten är en avvikelse. Genom att anropa API: et med varje ny data punkt som du skapar kan du övervaka dina data när de skapas. |
|Identifiera avvikelser i alla data uppsättningar som en batch. | Använd din tids serie för att identifiera eventuella avvikelser som kan finnas i dina data. Den här åtgärden genererar en modell med alla tids serie data, där varje punkt analyseras med samma modell.         |
|Identifiera ändrings punkter i data uppsättningen som en batch. | Använd din tids serie för att identifiera eventuella trend ändrings punkter som finns i dina data. Den här åtgärden genererar en modell med alla tids serie data, där varje punkt analyseras med samma modell.    |
| Få mer information om dina data. | Få värdefull information om dina data och observerade avvikelser, inklusive förväntade värden, avvikande gränser och positioner. |
| Justera gränser för avvikelse identifiering. | API: t för avvikelse detektor skapar automatiskt gränser för avvikelse identifiering. Justera gränserna för att öka eller minska API: ns känslighet till data avvikelser och bättre anpassa dina data. |

## <a name="demo"></a>Demo

Kolla den här [interaktiva demonstrationen](https://aka.ms/adDemo) för att förstå hur avvikelse detektor fungerar.
Om du vill köra demonstrationen måste du skapa en avvikelse identifierings resurs och hämta API-nyckeln och slut punkten.

## <a name="notebook"></a>Notebook-fil

Om du vill veta hur du anropar API: t för avvikelse detektor kan du prova den här [antecknings boken](https://aka.ms/adNotebook) Den här Jupyter Notebook visar hur du skickar en API-begäran och visualiserar resultatet.

Utför följande steg för att köra antecknings boken:

1. Hämta en giltig API-prenumerations nyckel för avvikelse detektor och en API-slutpunkt. Avsnittet nedan innehåller anvisningar för att registrera dig.
1. Logga in och välj klona i det övre högra hörnet.
1. Avmarkera alternativet "offentlig" i dialog rutan innan du slutför klonings åtgärden, annars är din bärbara dator, inklusive eventuella prenumerations nycklar, offentlig.
1. Välj **Kör vid kostnads fri beräkning**
1. Välj en av antecknings böckerna.
1. Lägg till din giltiga API-prenumerations nyckel för avvikelse detektor i `subscription_key` variabeln.
1. Ändra `endpoint` variabeln till din slut punkt. Exempelvis: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. I det övre meny fältet väljer du **cell** och **kör sedan alla**.

## <a name="workflow"></a>Arbetsflöde

API: t för avvikelse detektor är en RESTful-webbtjänst, vilket gör det enkelt att anropa från valfritt programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Efter registreringen:

1. Ta dina Time Series-data och konvertera dem till ett giltigt JSON-format. Använd [bästa praxis](concepts/anomaly-detection-best-practices.md) när du förbereder dina data för att få bästa möjliga resultat.
1. Skicka en begäran till avvikelse identifierings-API: et med dina data.
1. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="algorithms"></a>Algoritmer

* Se följande tekniska Bloggar för information om de algoritmer som används:
    * [Introduktion till API för Azure-avvikelseidentifiering](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Översikt över SR-CNN-algoritmen i Azure avvikelse detektor](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Du kan läsa pappers [seriens avvikelse identifierings tjänst på Microsoft](https://arxiv.org/abs/1906.03821) (godkänd av KDD 2019) för att lära dig mer om SR-CNN-algoritmerna som har utvecklats av Microsoft.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>Tjänst tillgänglighet och redundans

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Är avvikelse tjänst zonen elastisk?

Ja. Avvikelse detektor tjänsten är zon-elastisk som standard.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>Hur gör jag för att att konfigurera avvikelse detektor tjänsten att vara zon-elastisk?

Ingen kund konfiguration krävs för att aktivera zon återhämtning. Zon-återhämtning för avvikelser detektor resurser är tillgängligt som standard och hanteras av själva tjänsten.

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuera lokalt med Docker-behållare

[Använd avvikande detektor behållare](anomaly-detector-container-howto.md) för att distribuera API-funktioner lokalt. Docker-behållare gör att du kan ta tjänsten närmare dina data för efterlevnad, säkerhet eller andra drift orsaker.

## <a name="join-the-anomaly-detector-community"></a>Delta i communityn för avvikelseidentifiering

* Gå med i [gruppen avvikelser detektorer i Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Se valt [innehåll som skapats av användare](user-generated-content.md)

## <a name="next-steps"></a>Nästa steg

* [Snabb start: identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektorn](quickstarts/client-libraries.md)
* API: t online- [demon](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) för avvikelse detektor
* Avvikelse detektor [REST API referens](https://aka.ms/anomaly-detector-rest-api-ref)
