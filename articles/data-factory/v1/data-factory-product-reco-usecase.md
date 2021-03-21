---
title: Data Factory användnings fall – produkt rekommendationer
description: Lär dig mer om ett användnings fall som implementeras med hjälp av Azure Data Factory tillsammans med andra tjänster.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b20eef90cebab59f823e1766d28eb355f317463d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387197"
---
# <a name="use-case---product-recommendations"></a>Användnings fall – produkt rekommendationer
Azure Data Factory är en av många tjänster som används för att implementera Cortana Intelligence Suite av lösnings acceleratorer.  Se [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) sida för information om den här sviten. I det här dokumentet beskriver vi ett vanligt användnings fall som Azure-användare redan har löst och implementerat med hjälp av Azure Data Factory och andra Cortana Intelligence komponent tjänster.

## <a name="scenario"></a>Scenario
Online-detaljister vill ofta locka sina kunder att köpa produkter genom att presentera dem med produkter som de förmodligen är intresserade av, och som därför är mest sannolika att köpa. För att åstadkomma detta måste online-detaljisterna anpassa sin användares onlineupplevelse genom att använda anpassade produkt rekommendationer för den aktuella användaren. De här anpassade rekommendationerna måste göras baserade på deras aktuella och historiska shopping beteende data, produkt information, nyligen introducerade varumärken och information om produkt-och kund segment.  Dessutom kan de tillhandahålla användar produkt rekommendationer baserat på analys av övergripande användnings beteende från alla sina användare.

Målet med dessa åter försäljare är att optimera för användarnas klicknings-till-försäljning-konverteringar och tjäna större försäljnings intäkter.  De uppnår den här omvandlingen genom att leverera sammanhangsbaserade, beteendebaserade produkt rekommendationer baserat på kundernas intressen och åtgärder. För det här användnings fallet använder vi onlinebutiker som ett exempel på företag som vill optimeras för sina kunder. Dessa principer gäller dock för alla verksamheter som vill engagera sina kunder runt sina varor och tjänster och förbättra sina kunders köp upplevelse med anpassade produkt rekommendationer.

## <a name="challenges"></a>Utmaningar
Det finns många utmaningar som online-detaljister möter vid försök att implementera den här typen av användnings fall. 

Först måste data av olika storlekar och former matas in från flera data källor, både lokalt och i molnet. Dessa data omfattar produkt data, historiska kund beteende data och användar data när användaren bläddrar Online-webbplatsen. 

För det andra måste anpassade produkt rekommendationer vara rimligen och beräknas och förutsägas noggrant. Förutom produkt-, märkes-och kund beteende och webb läsar data, behöver online-detaljister även ta med kundfeedback om tidigare inköp till faktor för att fastställa de bästa produkt rekommendationerna för användaren. 

I tredje fall måste rekommendationerna slutföras direkt till användaren för att tillhandahålla en sömlös webb-och inköps upplevelse och tillhandahålla de senaste och relevanta rekommendationerna. 

Slutligen måste åter försäljare mäta effektiviteten för deras tillvägagångs sätt genom att spåra övergripande försäljnings-och Sälj försäljnings-och Sälj försäljnings-och Sälj försäljnings-till-konvertering och anpassa till framtida rekommendationer.

## <a name="solution-overview"></a>Lösning: översikt
Detta exempel på användnings fall har lösts och implementerats av verkliga Azure-användare med hjälp av Azure Data Factory och andra Cortana Intelligence komponent tjänster, inklusive [HDInsight](https://azure.microsoft.com/services/hdinsight/) och [Power BI](https://powerbi.microsoft.com/).

Online-detaljisten använder Azure Blob Store, en lokal SQL Server, Azure SQL Database och en Relations data mart som data lagrings alternativ i hela arbets flödet.  BLOB Store innehåller kund information, kund beteende data och produkt informations data. Produkt informations data innehåller information om produkt anpassning och en produkt katalog som lagras lokalt i en Azure Synapse-analys. 

Alla data kombineras och matas in i ett produkt rekommendations system för att tillhandahålla anpassade rekommendationer baserat på kundernas intressen och åtgärder, medan användaren bläddrar i produkterna i katalogen på webbplatsen. Kunderna kan också se produkter som är relaterade till den produkt de tittar på baserat på de övergripande användnings mönster för webbplatser som inte är relaterade till någon användare.

![användnings Falls diagram](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabyte av RAW-webbloggfiler genereras dagligen från online-webbplatsens webbplats som en delvis strukturerad fil. RAW-webbloggfiler och kund-och produkt katalog information matas in regelbundet i en Azure Blob Storage med hjälp av Data Factory globalt distribuerad data förflyttning som en tjänst. RAW-loggfilerna för dagen partitioneras (per år och månad) i Blob Storage för långsiktig lagring.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) används för att partitionera de råa loggfilerna i BLOB-arkivet och bearbeta de inmatade loggarna i skala med hjälp av både Hive-och gris-skript. Data för partitionerade webb loggar bearbetas sedan för att extrahera nödvändiga indata för ett Machine Learning-rekommenderat system för att skapa anpassade produkt rekommendationer.

Rekommendations systemet som används för Machine Learning i det här exemplet är en plattform för Machine Learning-rekommendation med öppen källkod från [Apache Mahout](https://mahout.apache.org/).  Alla [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eller anpassade modeller kan tillämpas på scenariot.  Mahout-modellen används för att förutsäga likheten mellan objekt på webbplatsen baserat på övergripande användnings mönster och för att generera anpassade rekommendationer baserat på den enskilda användaren.

Slutligen flyttas resultat uppsättningen med anpassade produkt rekommendationer till en Relations data mart för användning av åter försäljnings webbplatsen.  Resultat uppsättningen kan också nås direkt från Blob Storage av ett annat program eller flyttas till ytterligare butiker för andra konsumenter och användnings fall.

## <a name="benefits"></a>Fördelar
Genom att optimera strategin för produkt rekommendation och justera den med affärs mål, uppfyllde lösningen Onlines marknadsförings-och marknadsförings mål. Dessutom har de kunnat operationalisera och hantera arbets flödet för produkt rekommendationer på ett effektivt, tillförlitligt och kostnads effektivt sätt. Metoden gjorde det enkelt för dem att uppdatera sin modell och finjustera dess effektivitet baserat på måtten för att genomföra försäljnings klickning till konvertering. Genom att använda Azure Data Factory har de kunnat överge sin tids krävande och dyra manuell hantering av moln resurser och flytta till moln resurs hantering på begäran. De kunde därför spara tid, pengar och minska deras tid till lösnings distribution. Data härkomst vyer och drift tjänsten hälsa blev lätta att visualisera och felsöka med det intuitiva Data Factory övervaknings-och hanterings gränssnittet som finns tillgängligt från Azure Portal. Deras lösning kan nu schemaläggas och hanteras så att färdiga data skapas och levereras till användare på ett tillförlitligt sätt, och data-och bearbetnings beroenden hanteras automatiskt utan mänsklig inblandning.

Genom att tillhandahålla den här anpassade kund upplevelsen skapade online-detaljisten en mer konkurrens kraftig, engagerad kund upplevelse och ökar därför försäljningen och den övergripande kund nöjdheten.

