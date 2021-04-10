---
title: Skala och hantera anpassad kunskap
titleSuffix: Azure Cognitive Search
description: Lär dig verktygen och teknikerna för att effektivt skala ut en anpassad färdighet för maximalt data flöde. Anpassade kunskaper anropar anpassade AI-modeller eller logik som du kan lägga till i en AI-fördefinierad indexerings pipeline i Azure Kognitiv sökning.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 4fdc222fa20aef6639bf6d5d485f7dcf6b6ca535
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641136"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Skala ut en anpassad färdighet effektivt

Anpassade kunskaper är webb-API: er som implementerar ett speciellt gränssnitt. En anpassad kunskap kan implementeras på alla offentligt adresser bara resurser. De vanligaste implementeringarna för anpassade kunskaper:
* Azure Functions för anpassade logik kunskaper
* Azure webapps för enkla AI-kunskaper i behållare
* Azure Kubernetes-tjänsten för mer komplexa eller större färdigheter.

## <a name="prerequisites"></a>Förutsättningar

+ Granska det [anpassade kunskaps gränssnittet](cognitive-search-custom-skill-interface.md) för en introduktion till gränssnittet för indata/utdata som en anpassad färdighet ska implementera.

+ Konfigurera din miljö. Du kan börja med [den här självstudien från slut punkt till slut punkt](/azure/azure-functions/create-first-function-vs-code-python) för att konfigurera en server lös Azure-funktion med Visual Studio Code och python-tillägg.

## <a name="skillset-configuration"></a>Färdigheter-konfiguration

Att konfigurera en anpassad färdighet för att maximera data flödet för indexerings processen kräver att du förstår kunskaper, indexerare och hur kunskapen är relaterad till varje dokument. Till exempel antalet gånger som en färdighet anropas per dokument och den förväntade varaktigheten per anrop.

### <a name="skill-settings"></a>Kunskaps inställningar

Följande parametrar har angetts för den [anpassade kompetensen](cognitive-search-custom-skill-web-api.md) .

1. Ange `batchSize` den anpassade kunskapen för att konfigurera antalet poster som skickas till kunskapen i ett enda anrop till färdigheten.

2. Ställ in `degreeOfParallelism` för att kalibrera antalet samtidiga förfrågningar som indexeraren kommer att göra till din färdighet.

3. Ange `timeout` ett värde som räcker för att kunskapen ska svara med ett giltigt svar.

4. I `indexer` definitionen anger [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) du det antal dokument som ska läsas från data källan och berikas samtidigt.

### <a name="considerations"></a>Överväganden

Att ställa in variablerna för att optimera indexerings prestandan kräver att du fastställer om din kunskap fungerar bättre med många samtidiga små förfrågningar eller färre stora begär Anden. Några frågor att överväga är:

* Vad är kardinalitet för färdighets anrop? Körs kunskapen en gång för varje dokument, t. ex. en kunskap om dokument klassificering, eller skulle kunna utföra flera gånger per dokument, en kunskap om stycke klassificering?

* I genomsnitt, hur många dokument läses från data källan för att fylla i en färdighets förfrågan baserat på kompetens parti storleken? Helst bör detta vara mindre än indexets batchstorlek. Med batch-storlekar som är större än 1 kan din kunskap ta emot poster från flera käll dokument. Om antalet indexerare är till exempel 5 och antalet kunskaper om batch är 50 och varje dokument bara genererar fem poster, måste indexeraren fylla i en anpassad färdighets förfrågan över flera indexerare-batchar.

* Genomsnittligt antal begär Anden som en indexerare batch kan generera bör ge dig en optimal inställning för grader av parallellitet. Om din infrastruktur som är värd för kompetensen inte stöder den nivån av samtidighet, bör du försöka att ringa upp graderingen av parallellitet. Vi rekommenderar att du testar konfigurationen med några få dokument för att verifiera dina val i parametrarna.

* Testa med ett mindre exempel på dokument, utvärdera körnings tiden för din skicklighet till den totala tid som krävs för att bearbeta del mängden av dokument. Ägnar indexeraren mer tid på att skapa en batch eller vänta på ett svar från din kompetens? 

* Ta hänsyn till de överordnade effekterna av parallellitet. Om indata till en anpassad färdighet är en utmatning från en tidigare färdighet, skalas alla kunskaper i färdigheter ut effektivt för att minimera svars tiden?

## <a name="error-handling-in-the-custom-skill"></a>Fel hantering i den anpassade kompetensen

Anpassade kunskaper ska returnera status kod för lyckad leverans till HTTP 200 när färdigheten har slutförts. Om en eller flera poster i en batch resulterar i fel, kan du överväga att returnera kod 207 med flera status värden. Listan fel eller varningar för posten ska innehålla rätt meddelande.

Alla objekt i en batch som fel leder till att motsvarande dokument Miss lyckas. Om du behöver dokumentet för att lyckas, returnerar du en varning.

Status kod över 299 utvärderas som ett fel och alla-anrikningen Miss lyckas resulterar i ett misslyckat dokument. 

### <a name="common-error-messages"></a>Vanliga felmeddelanden

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Ange parametern timeout på kunskapen för att tillåta en längre körnings tid.

* `Could not execute skill because Web Api skill response is invalid.` Väg LED ande av kunskapen att inte returnera ett meddelande i formatet för anpassad färdighets svar. Detta kan bero på ett ej infångat undantag i färdigheten.

* `Could not execute skill because the Web Api request failed.` Troligen orsakade av auktoriseringsfel eller undantag.

* `Could not execute skill.` Vanligt vis är resultatet av färdighets svaret mappat till en befintlig egenskap i dokumentets hierarki.

## <a name="testing-custom-skills"></a>Testa anpassade kunskaper

Börja med att testa din anpassade kunskap med en REST API-klient för att verifiera:

* Kunskapen implementerar det anpassade kunskaps gränssnittet för förfrågningar och svar

* Kompetensen returnerar en giltig JSON med `application/JSON` MIME-typen

* Returnerar en giltig HTTP-statuskod

Skapa en felsökningssession för att [lägga till din](cognitive-search-debug-session.md) kunskap till färdigheter och se till att den ger en giltig anrikning. Medan en felsökningssession inte tillåter att du finjusterar prestandan för kunskapen, kan du se till att kunskapen är konfigurerad med giltiga värden och returnera de förväntade berikade objekten.

## <a name="best-practices"></a>Bästa praxis

* Även om kunskaper kan godkänna och returnera större nytto laster bör du begränsa svaret till 150 MB eller mindre när du returnerar JSON.

* Överväg att ställa in batchstorleken på indexeraren och kunskapen för att säkerställa att varje data källa batch genererar en fullständig nytto last för din skicklighet.

* För tids krävande uppgifter ställer du in tids gränsen på ett högt tillräckligt värde för att säkerställa att indexeraren inte fel uppstår vid bearbetning av dokument samtidigt.

* Optimera Indexer batchstorlek, kompetensens storlek och kunskaps grader för att skapa belastnings mönstret som förväntar sig, färre stora förfrågningar eller många små förfrågningar.

* Övervaka anpassade kunskaper med detaljerade loggar av misslyckanden eftersom du kan ha scenarier där särskilda begär Anden konsekvent Miss lyckas på grund av data variationen.


## <a name="next-steps"></a>Nästa steg
Grattis! Din anpassade färdighet skalas nu till höger för att maximera data flödet på indexeraren. 

+ [Energi kunskaper: ett lager med anpassade kunskaper](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Lägg till en anpassad färdighet till en AI-pipeline för anrikning](cognitive-search-custom-skill-interface.md)
+ [Lägg till en Azure Machine Learning-kompetens](./cognitive-search-aml-skill.md)
+ [Testa ändringar med hjälp av fel söknings sessioner](./cognitive-search-debug-session.md)