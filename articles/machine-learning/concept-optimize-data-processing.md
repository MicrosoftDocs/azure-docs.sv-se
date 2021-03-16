---
title: Optimera databearbetning
titleSuffix: Azure Machine Learning
description: Lär dig metod tips för optimering av data bearbetnings hastigheter och vilka integrations Azure Machine Learning har stöd för data bearbetning i stor skala.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: 5ab7bac635a0b670087800212727b0d2e2b96934
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472209"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Optimera data bearbetning med Azure Machine Learning

I den här artikeln får du lära dig mer om metod tips som hjälper dig att optimera data bearbetnings hastigheten lokalt och i stor skala.

Azure Machine Learning är integrerat med paket med öppen källkod och ramverk för data bearbetning. Genom att använda dessa integreringar och tillämpa rekommendationerna för bästa praxis i den här artikeln kan du förbättra data bearbetnings hastigheten både lokalt och i stor skala.

## <a name="parquet-and-csv-file-formats"></a>Parquet-och CSV-filformat

Filer med kommaavgränsade värden (CSV) är vanliga fil format för data bearbetning. Parquet-filformat rekommenderas dock för Machine Learning-uppgifter.

[Parquet filer](https://parquet.apache.org/) lagrar data i ett binärt kolumn format. Det här formatet är användbart om du behöver dela upp data i flera filer. Med det här formatet kan du också rikta in dig på relevanta fält för dina Machine Learning-experiment. I stället för att läsa en data fil på 20 GB kan du minska den data inläsningen genom att välja de kolumner som behövs för att träna din ML-modell. Parquet-filer kan också komprimeras för att minimera bearbetnings kraften och ta upp mindre utrymme.

CSV-filer används ofta för att importera och exportera data, eftersom de är lätta att redigera och läsa i Excel. Data i CSV: er lagras som strängar i ett rad-baserat format, och filerna kan komprimeras för att minska belastningen på data överföringar. Okomprimerade CSV: er kan utökas med en faktor på cirka 2-10 och komprimerade CSV: er kan öka ännu ytterligare. Så att 5 GB CSV i minnet ökar med den 8 GB RAM-minne som du har på din dator. Komprimerings beteendet kan öka fördröjningen för data överföring, vilket inte är idealiskt om du har stora mängder data att bearbeta. 

## <a name="pandas-dataframe"></a>Pandas dataframe

[Pandas-dataframes](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) används ofta för data manipulation och analys. `Pandas` fungerar bra för data storlekar som är mindre än 1 GB, men bearbetnings tiden för `pandas` dataframes saktar ned när fil storlekarna når cirka 1 GB. Den här minskningen beror på att storleken på dina data i lagring inte är densamma som storleken på data i en dataframe. Till exempel kan data i CSV-filer utökas upp till 10 gånger i en dataframe, så en CSV-fil på 1 GB kan bli 10 GB i en dataframe.

`Pandas` är en enkel tråd, vilket innebär att åtgärder utförs en i taget på en enda processor. Du kan enkelt parallellisera arbets belastningar till flera virtuella processorer på en enda Azure Machine Learning beräknings instans med paket som [Modie](https://modin.readthedocs.io/en/latest/) som omsluter `Pandas` med en distribuerad Server del.

Ändra bara den här kodraden till om du vill parallellisera dina uppgifter med `Modin` och [dask](https://dask.org) `import pandas as pd` `import modin.pandas as pd` .

## <a name="dataframe-out-of-memory-error"></a>Dataframe: slut på minnes fel 

Vanligt vis uppstår ett *slut på minnes* fel när dataframe expanderar över tillgängligt RAM-minne på datorn. Det här konceptet gäller även för ett distribuerat ramverk som `Modin` eller `Dask` .  Det innebär att din åtgärd försöker läsa in dataframe i minnet på varje nod i klustret, men det finns inte tillräckligt med RAM-minne.

En lösning är att öka ditt RAM-minne för att passa dataframe i minnet. Vi rekommenderar din beräknings storlek och bearbetnings kraft innehåller två gånger RAM-minnets storlek. Så om din dataframe är 10 GB använder du ett beräknings mål med minst 20 GB RAM-minne för att säkerställa att dataframe kan anpassas i minnet och bearbetas. 

För flera virtuella processorer, vCPU, Tänk på att du vill att en partition ska passa in i RAM-minnet som varje vCPU kan ha på datorn. Det innebär att om du har 16 GB RAM 4-virtuella processorer vill du ha ungefär 2 GB dataframes per vCPU.

### <a name="local-vs-remote"></a>Lokal vs-fjärr

Du kanske märker att vissa Pandas dataframe-kommandon fungerar snabbare när du arbetar på din lokala dator och en virtuell dator som du har tillAzure Machine Learning. Den lokala datorn har vanligt vis en aktive rad växlings fil, vilket gör att du kan läsa in mer än vad som passar i det fysiska minnet, det vill säga att hård disken används som en utökning av RAM-minnet. Azure Machine Learning virtuella datorer körs utan växlings fil, kan därför bara läsa in så mycket data som tillgängligt fysiskt RAM-minne. 

För beräknings intensiva jobb rekommenderar vi att du väljer en större virtuell dator för att förbättra bearbetnings hastigheten.

Läs mer om de [tillgängliga VM-serierna och storlekarna](concept-compute-target.md#supported-vm-series-and-sizes) för Azure Machine Learning. 

För RAM-specifikationer, se motsvarande sidor för VM-serien, till exempel [Dv2-Dsv2-serien](../virtual-machines/dv2-dsv2-series-memory.md) eller [NC-serien](../virtual-machines/nc-series.md).

### <a name="minimize-cpu-workloads"></a>Minimera CPU-arbetsbelastningar

Om du inte kan lägga till mer RAM-minne på datorn kan du använda följande tekniker för att minimera CPU-arbetsbelastningar och optimera bearbetnings tider. De här rekommendationerna gäller både enkla och distribuerade system.

Teknik | Beskrivning
----|----
Komprimering | Använd en annan representation för dina data, på ett sätt som använder mindre minne och inte märkbart påverkar resultatet av beräkningen.<br><br>*Exempel:* I stället för att lagra poster som en sträng med cirka 10 byte eller mer per post, lagrar du dem som booleska, sanna eller falska, som du kan lagra på 1 byte.
Segmentering | Läs in data i minnet i del mängder (segment), bearbeta data en delmängd vid tiden eller flera del mängder parallellt. Den här metoden fungerar bäst om du behöver bearbeta alla data, men behöver inte läsa in alla data i minnet samtidigt. <br><br>*Exempel:* I stället för att bearbeta data på hela året samtidigt kan du läsa in och bearbeta data en månad i taget.
Indexering | Använd och Använd ett index, en sammanfattning som talar om var du hittar de data du bryr dig om. Indexering är användbart när du bara behöver använda en delmängd av data, i stället för den fullständiga uppsättningen<br><br>*Exempel:* Om du har ett fullständigt års värde för försäljnings data sorterade per månad, kan ett index hjälpa dig att snabbt söka efter önskad månad som du vill bearbeta.

## <a name="scale-data-processing"></a>Skalnings data bearbetning

Om de tidigare rekommendationerna inte räcker och du inte kan få en virtuell dator som passar dina data, kan du 

* Använd ett ramverk som `Spark` eller `Dask` för att bearbeta data "slut på minne". I det här alternativet läses dataframe in i RAM-partitionen genom partition och bearbetning, med det slutliga resultatet som samlas in i slutet.  

* Skala ut till ett kluster med hjälp av ett distribuerat ramverk. I det här alternativet delas data bearbetnings belastningen upp och bearbetas på flera processorer som fungerar parallellt, med det slutliga resultatet som samlas in i slutet.

### <a name="recommended-distributed-frameworks"></a>Rekommenderade distribuerade ramverk

Följande tabell rekommenderar distribuerade ramverk som är integrerade med Azure Machine Learning baserat på din kod inställning eller data storlek.

Upplevelse eller data storlek | Rekommendation
------|------
Om du är bekant med `Pandas`| `Modin` eller `Dask` dataframe
Om du föredrar `Spark` | `PySpark`
För data som är mindre än 1 GB | `Pandas` lokalt **eller** en beräknings instans för fjärrAzure Machine Learning
För data som är större än 10 GB| Flytta till ett kluster med hjälp av `Ray` , `Dask` eller `Spark`

## <a name="next-steps"></a>Nästa steg

* [Alternativ för data inmatning med Azure Machine Learning](concept-data-ingestion.md).
* [Skapa och registrera data uppsättningar](how-to-create-register-datasets.md).
