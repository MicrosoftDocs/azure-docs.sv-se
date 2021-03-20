---
title: 'ML Studio (klassisk): köra Python-skript – Azure'
description: Lär dig hur du använder modulen kör Python-skript för att använda python-kod i Machine Learning Studio (klassiska) experiment och webb tjänster.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 374589212b230e6b3ce0abcadbad8aa3eb7271fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519991"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Köra python Machine Learning-skript i Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Python är ett värdefullt verktyg i verktyget Chest av många data forskare. Den används i alla stadier av vanliga Machine Learning-arbetsflöden, inklusive data utforskning, funktions extrahering, modell utbildning och validering och distribution.

I den här artikeln beskrivs hur du kan använda den köra Python-skript-modulen för att använda python-kod i dina Azure Machine Learning Studio (klassiska) experiment och webb tjänster.

## <a name="using-the-execute-python-script-module"></a>Använda modulen kör Python-skript

Det primära gränssnittet till python i Studio (klassisk) är genom [execute Python-skript][execute-python-script] -modulen. Den accepterar upp till tre indata och genererar upp till två utdata, på samma sätt som [Kör R-skript][execute-r-script] -modulen. Python-koden anges i parameter rutan via en särskilt namngiven ingångs punkt funktion som kallas `azureml_main` .

![Köra Python-skript modul](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Exempel på python-kod i rutan modul parameter](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Indataparametrar

Indata till python-modulen visas som Pandas DataFrames. `azureml_main`Funktionen accepterar upp till två valfria Pandas-DataFrames som parametrar.

Mappningen mellan indataporter och funktions parametrar är positions:

- Den första anslutna Indataporten mappas till funktionens första parameter.
- Den andra indatamängden (om ansluten) mappas till funktionens andra parameter.
- Den tredje indatamängden används för att [Importera ytterligare python-moduler](#import-modules).

Mer detaljerad semantik för hur indataportarna mappas till parametrarna i `azureml_main` funktionen visas nedan.

![Tabell med indataports-konfigurationer och resulterande python-signatur](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Retur värden

`azureml_main`Funktionen måste returnera en enskild Pandas DataFrame-paketerad i en python- [sekvens](https://docs.python.org/2/c-api/sequence.html) , till exempel en tupel, en lista eller en numpy-matris. Det första elementet i den här sekvensen returneras till den första utdataporten för modulen. Den andra utdataporten för modulen används för [visualiseringar](#visualizations) och kräver inte ett retur värde. Det här schemat visas nedan.

![Mappa indataportar till parametrar och retur värde till utgående port](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Översättning av data typer för indata och utdata

Studio-datauppsättningar är inte samma som Panda DataFrames. Det innebär att indata-datauppsättningar i Studio (klassisk) konverteras till Pandas-DataFrame och utdata-DataFrames konverteras tillbaka till Studio (klassiska) data uppsättningar. Under den här konverterings processen utförs även följande översättningar:

 **Data typen python** | **Översättnings procedur för Studio** |
| --- | --- |
| Strängar och siffror| Översatt som är |
| Pandas ' NA ' | Översatt till saknat värde |
| Index vektorer | Som inte stöds |
| Kolumn namn som inte är sträng | Anrop `str` för kolumn namn |
| Duplicera kolumn namn | Lägg till numeriskt suffix: (1), (2), (3) och så vidare.

**Alla indata-ramar i python-funktionen har alltid ett 64-bitars numeriskt index från 0 till antalet rader minus 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Befintliga Python-skript moduler importeras

Server delen som används för att köra python baseras på [Anaconda](https://www.anaconda.com/distribution/), en mycket använda vetenskaplig python-distribution. Den levereras med nära 200 av de vanligaste python-paketen som används i datainriktade arbets belastningar. Studio (klassisk) stöder för närvarande inte användning av paket hanterings system som pip eller Conda för att installera och hantera externa bibliotek.  Om du tycker att du behöver inkludera ytterligare bibliotek använder du följande scenario som vägledning.

Ett vanligt användnings fall är att införliva befintliga Python-skript i Studio (klassiska) experiment. [Execute Python-skript][execute-python-script] module accepterar en zip-fil som innehåller python-moduler på den tredje Indataporten. Filen zippas inte av körnings ramverket vid körning och innehållet läggs till i biblioteks Sök vägen för python-tolken. `azureml_main`Start punkts funktionen kan sedan importera dessa moduler direkt. 

Anta till exempel att filen Hello.py innehåller en enkel "Hello, World"-funktion.

![Användardefinierad funktion i Hello.py-fil](./media/execute-python-scripts/figure4.png)

Därefter skapar vi en fil Hello.zip som innehåller Hello.py:

![Zip-fil som innehåller användardefinierad python-kod](./media/execute-python-scripts/figure5.png)

Överför zip-filen som en data uppsättning till Studio (klassisk). Skapa och kör sedan ett experiment som använder python-koden i Hello.zip-filen genom att koppla den till den tredje Indataporten för modulen **Kör python** som visas i följande bild.

![Exempel på experiment med Hello.zip som ininformation i en EXECUTE Python-skript-modul](./media/execute-python-scripts/figure6a.png)

![Användardefinierad python-kod som laddats upp som en zip-fil](./media/execute-python-scripts/figure6b.png)

Modulens utdata visar att zip-filen har packats upp och att funktionen `print_hello` har körts.

![Modulens utdata som visar användardefinierad funktion](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Åtkomst till Azure Storage blobbar

Du kan komma åt data som lagras i ett Azure Blob Storage-konto med följande steg:

1. Ladda ned [Azure Blob Storage-paketet för python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokalt.
1. Överför zip-filen till din Studio (klassiska) arbets yta som en data uppsättning.
1. Skapa ditt BlobService-objekt med `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Inaktivera **säker överföring som krävs** på fliken lagrings **konfigurations** inställning

![Inaktivera säker överföring som krävs i Azure Portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Drift av python-skript

[Körningen av python-skriptfilerna][execute-python-script] som används i ett bedömnings experiment anropas när den publiceras som en webb tjänst. Bilden nedan visar till exempel ett poängsättnings experiment som innehåller koden för att utvärdera ett enda python-uttryck.

![Studio-arbetsyta för en webb tjänst](./media/execute-python-scripts/figure3a.png)

![Pandas-uttryck för python](./media/execute-python-scripts/python-script-with-python-pandas.png)

En webb tjänst som skapats från det här experimentet gör följande:

1. Ta ett python-uttryck som inmatade värden (som en sträng)
1. Skicka python-uttrycket till Python-tolken
1. Returnerar en tabell som innehåller både uttrycket och det utvärderade resultatet.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Arbeta med visualiseringar

Observationer som skapats med MatplotLib kan returneras av [Kör python-skriptet][execute-python-script]. Ritytor omdirigeras dock inte automatiskt till bilder när de använder R. Användaren måste därför uttryckligen Spara alla områden i PNG-filer.

Om du vill generera bilder från MatplotLib måste du utföra följande steg:

1. Växla Server delen till "aggregering" från standard-qt-baserad åter givning.
1. Skapa ett nytt figur objekt.
1. Hämta axeln och generera alla ritytor till den.
1. Spara bilden i en PNG-fil.

Den här processen illustreras i följande bilder som skapar en punkt ritnings mat ris med hjälp av funktionen scatter_matrix i Pandas.

![Kod för att spara MatplotLib siffror till bilder](./media/execute-python-scripts/figure-v1-8.png)

![Klicka på visualisera i en köra python-skriptfil för att Visa siffrorna](./media/execute-python-scripts/figure-v2-9a.png)

![Visualisera ritytor för ett exempel experiment med python-kod](./media/execute-python-scripts/figure-v2-9b.png)

Det är möjligt att returnera flera siffror genom att spara dem i olika bilder. Studio (klassisk) körning hämtar alla bilder och sammanfogar dem för visualisering.

## <a name="advanced-examples"></a>Avancerade exempel

Anaconda-miljön som installeras i Studio (klassisk) innehåller vanliga paket som NumPy, SciPy och scikits-lär. Dessa paket kan användas effektivt för data bearbetning i en maskin inlärnings pipeline.

Följande experiment och skript visar till exempel användningen av Ensemble-lär i Scikits-Learn för att beräkna prioritets resultat för en data uppsättning. Poängen kan användas för att utföra ett övervakat funktions val innan de matas in i en annan modell.

Här är python-funktionen som används för att beräkna prioritets poängen och ordna funktionerna utifrån poängen:

![Funktion för att rangordna funktioner efter Poäng](./media/execute-python-scripts/figure8.png)

Följande experiment beräknar och returnerar prioritets poängen för funktionerna i data uppsättningen "Pima indiska diabetes" i Azure Machine Learning Studio (klassisk):

![Försök att rangordna funktioner i Pima indiska diabetes-datauppsättningen med python](./media/execute-python-scripts/figure9a.png)

![Visualisering av utdata från modulen kör Python-skript](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Begränsningar

[Execute Python-skript][execute-python-script] modulen har för närvarande följande begränsningar:

### <a name="sandboxed-execution"></a>Sandbox-körning

Python runtime är för närvarande begränsat och tillåter inte åtkomst till nätverket eller det lokala fil systemet på ett beständigt sätt. Alla filer som sparas lokalt isoleras och tas bort när modulen har slutförts. Python-koden kan inte komma åt de flesta kataloger på den dator som den körs på, undantaget är den aktuella katalogen och dess under kataloger.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Brist på avancerad utveckling och fel söknings support

Python-modulen stöder för närvarande inte IDE-funktioner som IntelliSense och fel sökning. Även om modulen Miss lyckas vid körning är den fullständiga python stack-spårningen tillgänglig. Men den måste visas i utmatnings loggen för modulen. Vi rekommenderar för närvarande att du utvecklar och felsöker Python-skript i en miljö som IPython och sedan importerar koden till modulen.

### <a name="single-data-frame-output"></a>Utdata för en data ram

Start punkten för python får bara returnera en enskild data ram som utdata. För närvarande går det inte att returnera godtyckliga python-objekt, till exempel utbildade modeller direkt tillbaka till Studio-körningsmiljön (klassisk). Precis som [Kör R-skript][execute-r-script], som har samma begränsning, är det möjligt i många fall att Pickle objekt till en byte mat ris och sedan returnera det inuti en data ram.

### <a name="inability-to-customize-python-installation"></a>Det går inte att anpassa python-installationen

För närvarande är det enda sättet att lägga till anpassade python-moduler via mekanismen för zip-filer som beskrivs ovan. Även om det är möjligt för små moduler är det besvärligt för stora moduler (särskilt moduler med inbyggda dll: er) eller ett stort antal moduler.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Python Developer Center](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: /azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script