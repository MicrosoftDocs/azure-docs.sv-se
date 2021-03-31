---
title: Mappa fel söknings läge för data flöde
description: Starta en interaktiv felsökningssession när du skapar data flöden
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/19/2021
ms.openlocfilehash: 0aa472aca40acbaf3f8c8a09469d08fe6b37187a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101699767"
---
# <a name="mapping-data-flow-debug-mode"></a>Mappa fel söknings läge för data flöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Översikt

Med fel söknings läget Azure Data Factory mappnings data flödet kan du interaktivt titta på DataForm-transformeringen medan du skapar och felsöker dina data flöden. Felsökningssessionen kan användas både i data flödets design sessioner samt vid körning av pipeline-felsökning av data flöden. Om du vill aktivera fel söknings läge använder du knappen "fel sökning av data Flow" överst i design ytan.

![Felsöka skjutreglage](media/data-flow/debugbutton.png "Felsöka skjutreglage")

När du har aktiverat skjutreglaget uppmanas du att välja vilken integration runtime-konfiguration som du vill använda. Om AutoResolveIntegrationRuntime väljs, kommer ett kluster med åtta kärnor i allmän beräkning med en standardinställd 60-minuters tid att leva upp. Om du vill tillåta mer inaktiva team innan tids gränsen uppnås, kan du välja en högre TTL-inställning. Mer information om data flödes integrerings körningar finns i [prestanda för data flöde](concepts-data-flow-performance.md#ir).

![Felsök IR-val](media/data-flow/debug-new-1.png "Felsök IR-val")

När fel söknings läget är aktiverat kan du interaktivt bygga ditt data flöde med ett aktivt Spark-kluster. Sessionen stängs när du har aktiverat fel sökning i Azure Data Factory. Du bör vara medveten om de timkostnad som Azure Databricks under tiden som du har aktiverat felsökningssessionen.

I de flesta fall är det en bra idé att skapa dina data flöden i fel söknings läge så att du kan validera affärs logiken och Visa dina data transformationer innan du publicerar arbetet i Azure Data Factory. Använd knappen "Felsök" på pipeline-panelen för att testa ditt data flöde i en pipeline.

![Visa fel söknings sessioner för data flöde](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Varje felsökningssession som en användare startar från sitt användar gränssnitt för ADF-webbläsare är en ny session med ett eget Spark-kluster. Du kan använda vyn övervakning för att felsöka sessioner ovan för att visa och hantera fel söknings sessioner per fabrik. Du debiteras för varje timme som varje felsökningssession körs, inklusive TTL-tiden.

## <a name="cluster-status"></a>Klusterstatus

Kluster status indikatorn överst i design ytan blir grön när klustret är klart för fel sökning. Om klustret redan är varmt kommer den gröna indikatorn att visas nästan omedelbart. Om klustret inte redan kördes när du angav fel söknings läge, kommer Spark-klustret att utföra en kall omstart. Indikatorn kommer att snurra tills miljön är klar för interaktiv fel sökning.

När du är färdig med fel sökningen aktiverar du fel söknings knappen så att ditt Spark-kluster kan avslutas och du kommer inte längre att faktureras för fel söknings aktivitet.

## <a name="debug-settings"></a>Fel söknings inställningar

När du har aktiverat fel söknings läge kan du redigera hur ett data flöde för hands Grans kar data. Du kan redigera fel söknings inställningar genom att klicka på "Felsök inställningar" i verktygsfältet data flödes arbets yta. Du kan välja den rad gräns eller fil källa som ska användas för varje käll omvandling här. Rad begränsningarna i den här inställningen gäller bara för den aktuella felsökningssessionen. Du kan också välja den mellanlagrade länkade tjänst som ska användas för en Azure Synapse Analytics-källa. 

![Fel söknings inställningar](media/data-flow/debug-settings.png "Fel söknings inställningar")

Om du har parametrar i ditt data flöde eller någon av dess refererade data uppsättningar, kan du ange vilka värden som ska användas vid fel sökning genom att välja fliken **parametrar** .

Använd samplings inställningarna här för att peka på exempelfiler eller exempel tabeller med data så att du inte behöver ändra dina käll data uppsättningar. Genom att använda en exempel fil eller tabell här kan du behålla samma logik och egenskaps inställningar i ditt data flöde samtidigt som du testar mot en delmängd data.

![Parametrar för fel söknings inställningar](media/data-flow/debug-settings2.png "Parametrar för fel söknings inställningar")

Standard-IR som används för fel söknings läge i ADF-dataflöden är en mindre 4-core-arbetsnod med en enda nod med fyra kärnor. Detta fungerar bra med mindre exempel på data när du testar din data flödes logik. Om du expanderar rad gränserna i fel söknings inställningarna under data förhands granskningen eller anger ett högre antal rader med rader i din källa under fel sökning av pipeline, kanske du vill överväga att ange en större beräknings miljö i en ny Azure Integration Runtime. Sedan kan du starta om din felsökningssession med hjälp av den större beräknings miljön.

## <a name="data-preview"></a>Förhandsgranskning

Vid fel sökning på visas fliken Data förhands granskning på den nedre panelen. Utan fel söknings läge på visar data flödet bara aktuella metadata i och ut ur var och en av dina omvandlingar på fliken Granska. Data förhands granskningen kommer bara att fråga antalet rader som du har angett som gräns i fel söknings inställningarna. Klicka på **Uppdatera** för att hämta data förhands granskningen.

![Förhandsgranskning](media/data-flow/datapreview.png "Förhandsgranskning")

> [!NOTE]
> Fil källor begränsar bara de rader som visas, inte raderna som läses. För mycket stora data mängder rekommenderar vi att du tar en liten del av filen och använder den för testning. Du kan välja en temporär fil i fel söknings inställningar för varje källa som är en fil data uppsättnings typ.

När du kör i fel söknings läge i data flöde kommer dina data inte att skrivas till omvandlingen av mottagare. En felsökningssession är avsedd att fungera som ett test-nät för dina transformeringar. Sinks krävs inte under fel sökning och ignoreras i ditt data flöde. Om du vill testa att skriva data i din mottagare kör du data flödet från en Azure Data Factory pipeline och använder fel söknings körningen från en pipeline.

Data förhands granskning är en ögonblicks bild av dina transformerade data med rad gränser och data sampling från data ramar i Spark-minnet. Därför används inte Sink-drivrutinerna eller testas i det här scenariot.

### <a name="testing-join-conditions"></a>Testa kopplings villkor

Se till att du använder en liten uppsättning kända data för ditt test när du vill testa kopplingar, finns eller söka omvandlingar. Du kan använda alternativet för fel söknings inställningar ovan för att ange en temporär fil som ska användas för testningen. Detta är nödvändigt eftersom när du begränsar eller raderar rader från en stor data uppsättning kan du inte förutsäga vilka rader och vilka nycklar som ska läsas in i flödet för testning. Resultatet är icke-deterministiskt, vilket innebär att anslutnings villkoren inte kan fungera.

### <a name="quick-actions"></a>Snabb åtgärder

När du ser data förhands granskningen kan du generera en snabb omvandling till typecast, ta bort eller göra en ändring i en kolumn. Klicka på kolumn rubriken och välj sedan ett av alternativen i verktygsfältet för förhands granskning.

![Skärm bild som visar verktygsfältet för förhands granskning med alternativ: typecast, ändra, statistik och ta bort.](media/data-flow/quick-actions1.png "Snabb åtgärder")

När du har valt en ändring uppdateras data förhands granskningen omedelbart. Klicka på **Bekräfta** i det övre högra hörnet för att generera en ny omvandling.

![Skärm bilden visar knappen Bekräfta.](media/data-flow/quick-actions2.png "Snabb åtgärder")

**Typecast** och **ändra** genererar en härledd kolumn omvandling och **tar bort** genererar en SELECT-omvandling.

![Skärm bild som visar härledd kolumns inställningar.](media/data-flow/quick-actions3.png "Snabb åtgärder")

> [!NOTE]
> Om du redigerar ditt data flöde måste du hämta data förhands granskningen igen innan du lägger till en snabb omvandling.

### <a name="data-profiling"></a>Data profilering

Genom att markera en kolumn på fliken Data förhands granskning och klicka på **statistik** i verktygsfältet för förhands granskning visas ett diagram längst till höger i data rutnätet med detaljerad statistik om varje fält. Azure Data Factory görs ett avgörande baserat på data samplingen för vilken typ av diagram som ska visas. Fält med hög kardinalitet kommer att standardvärdet är NULL/NOT NULL-diagram medan kategoriska och numeriska data som har låg kardinalitet visar stapeldiagram med data värdes frekvens. Du kan också se max längd längd på sträng fält, minsta/högsta värden i numeriska fält, standard utveckling, percentiler, antal och genomsnitt.

![Kolumn statistik](media/data-flow/stats.png "Kolumn statistik")

## <a name="next-steps"></a>Nästa steg

* När du är klar med att skapa och felsöka ditt data flöde kan du [köra det från en pipeline.](control-flow-execute-data-flow-activity.md)
* När du testar din pipeline med ett data flöde använder du [alternativet för körning](iterative-development-debugging.md) av pipeline-körnings körning.
