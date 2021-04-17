---
title: Var du vill spara & skriva experimentfiler
titleSuffix: Azure Machine Learning
description: Lär dig var du sparar indata- och utdatafiler för att förhindra lagringsbegränsningar och svarstider för experiment.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 59955b291ce706a77d0dd5ab052809fe725166d9
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387895"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Var du sparar och skriver filer för Azure Machine Learning experiment


I den här artikeln får du lära dig var du sparar indatafiler och var du skriver utdatafiler från dina experiment för att förhindra lagringsgränsfel och experimentsvarstid.

När träningen startas på ett [beräkningsmål](concept-compute-target.md)är de isolerade från externa miljöer. Syftet med den här designen är att säkerställa reproducerbarhet och portabilitet för experimentet. Om du kör samma skript två gånger, på samma eller ett annat beräkningsmål, får du samma resultat. Med den här designen kan du hantera beräkningsmål som tillståndslösa beräkningsresurser, där var och en inte har någon tillhörighet till de jobb som körs när de har slutförts.

## <a name="where-to-save-input-files"></a>Var du sparar indatafiler

Innan du kan initiera ett experiment på ett beräkningsmål eller din lokala dator måste du se till att de nödvändiga filerna är tillgängliga för beräkningsmålet, till exempel beroendefiler och datafiler som koden måste köra.

Azure Machine Learning kör träningsskript genom att kopiera hela källkatalogen. Om du har känsliga data som du inte vill ladda upp använder du en [.ignore-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i källkatalogen. I stället kan du komma åt dina data med [hjälp av ett datalager](/python/api/azureml-core/azureml.data).

Lagringsgränsen för experimentögonblicksbilder är 300 MB och/eller 2 000 filer.

Därför rekommenderar vi:

* **Lagra dina filer i en Azure Machine Learning [datauppsättning](/python/api/azureml-core/azureml.data).** Detta förhindrar problem med experimentsvarstid och har fördelarna med att komma åt data från ett fjärrbearbetningsmål, vilket innebär att autentisering och montering hanteras av Azure Machine Learning. Läs mer om hur du anger en datauppsättning som indatakälla i ditt träningsskript med [Träna med datauppsättningar](how-to-train-with-datasets.md).

* **Om du bara behöver ett** par datafiler och beroendeskript och inte kan använda ett datalager placerar du filerna i samma mappkatalog som träningsskriptet. Ange den här mappen som `source_directory` din direkt i träningsskriptet eller i koden som anropar träningsskriptet.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Lagringsgränser för ögonblicksbilder av experiment

För experiment skapar Azure Machine Learning automatiskt en ögonblicksbild av koden baserat på den katalog som du föreslår när du konfigurerar körningen. Detta har en total gräns på 300 MB och/eller 2 000 filer. Om du överskrider den här gränsen visas följande fel:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Lös problemet genom att lagra experimentfilerna i ett datalager. Om du inte kan använda ett datalager erbjuder tabellen nedan möjliga alternativa lösningar.

&nbsp;Experimentbeskrivning|Lösning för lagringsgräns
---|---
Färre än 2 000 & kan inte använda ett datalager| Åsidosätt storleksgränsen för ögonblicksbilder med <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Det kan ta flera minuter beroende på antalet och storleken på filerna.
Måste använda en specifik skriptkatalog| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Pipeline|Använd en annan underkatalog för varje steg
Jupyter Notebooks| Skapa en `.amlignore` fil eller flytta notebook-filen till en ny, tom underkatalog och kör koden igen.

## <a name="where-to-write-files"></a>Var du skriver filer

På grund av isoleringen av träningsexperiment bevaras inte nödvändigtvis de ändringar av filer som sker under körningar utanför din miljö. Om skriptet ändrar filerna som är lokala för beräkning bevaras inte ändringarna för nästa experimentkörning och de sprids inte tillbaka till klientdatorn automatiskt. De ändringar som görs under den första experimentkörningen påverkar därför inte och bör inte påverka dem i den andra.

När du skriver ändringar rekommenderar vi att du skriver filer till lagring via en Azure Machine Learning med [ett OutputFileDatasetConfig-objekt.](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) Se [hur du skapar en OutputFileDatasetConfig](how-to-train-with-datasets.md#where-to-write-training-output).

Annars skriver du filer till `./outputs` mappen `./logs` och/eller .

>[!Important]
> Två mappar, *utdata och* *loggar*, behandlas särskilt av Azure Machine Learning. När du skriver filer till mapparna och under träningen laddas filerna automatiskt upp till din körningshistorik, så att du har åtkomst till dem när `./outputs` `./logs` körningen är klar.

* **För utdata som statusmeddelanden eller bedömningsresultat skriver** du filer till mappen så att de bevaras `./outputs` som artefakter i körningshistoriken. Ha i åtanke antalet och storleken på filer som skrivs till den här mappen eftersom svarstiden kan uppstå när innehållet laddas upp till körningshistoriken. Om svarstiden är ett problem rekommenderas att du skriver filer till ett datalager.

* **Om du vill spara den skrivna filen som loggar i körningshistoriken skriver** du filer till `./logs` mappen. Loggarna laddas upp i realtid, så den här metoden är lämplig för direktuppspelning av uppdateringar från en fjärrkörning.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att komma åt data från lagring](how-to-access-data.md).

* Läs mer om [att skapa beräkningsmål för modellträning och -distribution](how-to-create-attach-compute-studio.md)