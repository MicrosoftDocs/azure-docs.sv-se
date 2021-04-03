---
title: Felsöka fel i designer-modul
titleSuffix: Azure Machine Learning
description: Lär dig hur du kan läsa och felsöka automatiserade felkoder för moduler i Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 11/25/2020
ms.openlocfilehash: b917e3fc93c59de85c5236c18e31d7bbc9d891f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98065481"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>Undantag och fel koder för designern

I den här artikeln beskrivs fel meddelandena och undantags koderna i Azure Machine Learning designer som hjälper dig att felsöka dina pipelines i Machine Learning.

Du hittar fel meddelandet i designern genom att följa dessa steg:  

- Välj modulen misslyckades, gå till fliken **utdata + loggar** . du hittar den detaljerade loggen i **70_driver_log.txt** -filen under kategorin **azureml-logs** .

- Om du vill ha ett detaljerat modulnamn kan du kontrol lera det i error_info.jsunder **module_statistics** kategori.

Nedan visas felkoder för moduler i designern.

## <a name="error-0001"></a>Fel 0001  
 Undantag uppstår om en eller flera angivna kolumner med data uppsättningen inte kunde hittas.  

 Du får det här felet om ett kolumn val görs för en modul, men den eller de markerade kolumnerna finns inte i inmatnings data uppsättningen. Det här felet kan inträffa om du har angett ett kolumn namn manuellt eller om kolumn väljaren har angett en föreslagen kolumn som inte fanns i data uppsättningen när du körde pipelinen.  

**Lösning:** Gå tillbaka i modulen för att lösa detta undantag och kontrol lera att kolumn namn eller namn är korrekta och att alla refererade kolumner finns.  

|Undantags meddelanden|
|------------------------|
|Det gick inte att hitta en eller flera angivna kolumner.|
|Det gick inte att hitta kolumnen med namnet eller indexet {column_id}.|
|Kolumnen med namnet eller indexet {column_id} finns inte i {arg_name_missing_column}.|
|Kolumnen med namnet eller indexet {column_id} finns inte i {arg_name_missing_column}, men finns i {arg_name_has_column}.|
|Det gick inte att hitta kolumner med namnet eller indexet {column_names}.|
|Det finns inga kolumner med namnet eller indexet {column_names} i {arg_name_missing_column}.|
|Det finns inga kolumner med namnet eller indexet {column_names} i {arg_name_missing_column}, men det finns i {arg_name_has_column}.|


## <a name="error-0002"></a>Fel 0002  
 Undantag uppstår om en eller flera parametrar inte kunde parsas eller konverteras från en angiven typ till obligatorisk av typ av mål.  

 Det här felet uppstår i Azure Machine Learning när du anger en parameter som inmatad och värdetyp skiljer sig från den förväntade typen, och implicit konvertering kan inte utföras.  

**Lösning:** Kontrol lera modulens krav och Bestäm vilken Värdetyp som krävs (sträng, heltal, dubbelt osv.)  

|Undantags meddelanden|
|------------------------|
|Det gick inte att parsa parametern.|
|Det gick inte att parsa parametern {arg_name_or_column}.|
|Det gick inte att konvertera parametern {arg_name_or_column} till {to_type}.|
|Det gick inte att konvertera parametern {arg_name_or_column} från {from_type} till {to_type}.|
|Det gick inte att konvertera parameter värde {arg_name_or_column} {arg_value} från {from_type} till {to_type}.|
|Det gick inte att konvertera värdet {arg_value} i kolumnen {arg_name_or_column} från {from_type} till {to_type} med användningen av formatet {fmt}.|


## <a name="error-0003"></a>Fel 0003  
 Undantag uppstår om en eller flera indata är null eller tomma.  

 Du får det här felet i Azure Machine Learning om några indata eller parametrar till en modul är null eller tomma.  Det här felet kan inträffa, till exempel när du inte har angett något värde för en parameter. Det kan också inträffa om du väljer en data uppsättning som saknar värden eller en tom data uppsättning.  

**Lösning:**

+ Öppna den modul som skapade undantaget och kontrol lera att alla indata har angetts. Se till att alla nödvändiga indata har angetts. 
+ Se till att data som läses in från Azure Storage är tillgängliga och att konto namnet eller nyckeln inte har ändrats.  
+ Kontrol lera indata för saknade värden eller null-värden.
+ Om du använder en fråga på en data källa kontrollerar du att data returneras i det format som du förväntar dig. 
+ Kontrol lera om det finns skrivfel eller andra ändringar i data specifikationen.
  
|Undantags meddelanden|
|------------------------|
|En eller flera indata är null eller tomma.|
|Indatamängden {Name} är null eller tomt.|


## <a name="error-0004"></a>Fel 0004  
 Undantaget inträffar om parametern är mindre än eller lika med det angivna värdet.  

 Du får det här felet i Azure Machine Learning om parametern i meddelandet är under ett begränsat värde som krävs för att modulen ska bearbeta data.  

**Lösning:** Gå tillbaka i modulen som utlöser undantaget och ändra parametern så att den är större än det angivna värdet.  

|Undantags meddelanden|
|------------------------|
|Parametern måste vara större än det giltiga värdet.|
|Värdet för parametern {arg_name} måste vara större än {lower_boundary}.|
|Parametern {arg_name} har värdet {actual_value} som måste vara större än {lower_boundary}.|


## <a name="error-0005"></a>Fel 0005  
 Undantaget inträffar om parametern är mindre än ett angivet värde.  

 Du får det här felet i Azure Machine Learning om parametern i meddelandet är under eller lika med ett begränsat värde som krävs för att modulen ska bearbeta data.  

**Lösning:** Gå tillbaka i modulen som utlöser undantaget och ändra parametern så att den är större än eller lika med det angivna värdet.  

|Undantags meddelanden|
|------------------------|
|Parametern måste vara större än eller lika med ett begränsat värde.|
|Värdet för parametern {arg_name} måste vara större än eller lika med {lower_boundary}.|
|Parametern {arg_name} har värdet {Value} som ska vara större än eller lika med {lower_boundary}.|


## <a name="error-0006"></a>Fel 0006  
 Undantaget inträffar om parametern är större än eller lika med det angivna värdet.  

 Du får det här felet i Azure Machine Learning om parametern i meddelandet är större än eller lika med ett begränsat värde som krävs för att modulen ska bearbeta data.  

**Lösning:** Gå tillbaka i modulen som utlöser undantaget och ändra parametern så att den är mindre än det angivna värdet.  

|Undantags meddelanden|
|------------------------|
|Parameter matchnings fel. En av parametrarna måste vara mindre än en annan.|
|Värdet för parametern {arg_name} måste vara mindre än värdet för parametern {upper_boundary_parameter_name}.|
|Parametern {arg_name} har värdet {Value} som ska vara mindre än {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Fel 0007  
 Ett undantags fel uppstår om parametern är större än ett angivet värde.  

 Du får det här felet i Azure Machine Learning om du i egenskaperna för modulen har angett ett värde som är större än vad som tillåts. Du kan till exempel ange en data som ligger utanför intervallet för de datum som stöds, eller så kan du ange att fem kolumner ska användas när endast tre kolumner är tillgängliga. 

 Du kan också se det här felet om du anger två uppsättningar med data som behöver matcha på något sätt. Om du till exempel byter namn på kolumner och anger kolumnerna efter index, måste antalet namn som du anger matcha antalet kolumn index. Ett annat exempel kan vara en matematik åtgärd som använder två kolumner, där kolumnerna måste ha samma antal rader. 

**Lösning:**

 + Öppna modulen i fråga och granska eventuella numeriska egenskaps inställningar.
 + Se till att alla parameter värden ligger inom det tillåtna värde intervallet för den egenskapen.
 + Om modulen kräver flera indata kontrollerar du att indata är av samma storlek.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Kontrol lera om data uppsättningen eller data källan har ändrats. Ibland Miss kan ett värde som fungerade med en tidigare version av data efter antalet kolumner, kolumnens data typer eller data storleken ändrats.  

|Undantags meddelanden|
|------------------------|
|Parameter matchnings fel. En av parametrarna måste vara mindre än eller lika med en annan.|
|Värdet för parametern {arg_name} måste vara mindre eller lika med värdet för parametern {upper_boundary_parameter_name}.|
|Parametern {arg_name} har värdet {actual_value} som ska vara mindre än eller lika med {upper_boundary}.|
|Värdet för parametern {arg_name} {actual_value} måste vara mindre än eller lika med parametern {upper_boundary_parameter_name} värde {upper_boundary}.|
|Värdet för parametern {arg_name} {actual_value} måste vara mindre än eller lika med {upper_boundary_meaning} värde {upper_boundary}.|


## <a name="error-0008"></a>Fel 0008  
 Undantaget inträffar om parametern inte är i intervallet.  

 Du får det här felet i Azure Machine Learning om parametern i meddelandet ligger utanför de gränser som krävs för att modulen ska bearbeta data.  

 Det här felet visas till exempel om du försöker använda [Lägg till rader](add-rows.md) för att kombinera två data uppsättningar som har ett annat antal kolumner.  

**Lösning:** Gå tillbaka i modulen som utlöser undantaget och ändra parametern så att den ligger inom det angivna intervallet.  

|Undantags meddelanden|
|------------------------|
|Parametervärdet är inte inom det angivna intervallet.|
|Värdet för parametern {arg_name} är inte inom intervallet.|
|Värdet för parametern {arg_name} måste vara inom intervallet [{lower_boundary}, {upper_boundary}].|
|Värdet för parametern {arg_name} är inte inom intervallet. pga|


## <a name="error-0009"></a>Fel 0009  
 Undantag inträffar när namnet på Azure Storage-kontot eller container namnet har angetts felaktigt.  

Det här felet uppstår i Azure Machine Learning designer när du anger parametrar för ett Azure Storage-konto, men det går inte att matcha namnet eller lösen ordet. Fel på lösen ord eller konto namn kan bero på många orsaker:

 + Kontot har fel typ. Vissa nya konto typer stöds inte för användning med Machine Learning designer. Mer information finns i [Importera data](import-data.md) .
 + Du har angett ett felaktigt konto namn
 + Kontot finns inte längre
 + Lösen ordet för lagrings kontot är felaktigt eller har ändrats
 + Du angav inte behållar namnet, eller så finns inte behållaren
 + Du angav inte fullständigt sökvägen till filen (sökväg till blobben)
   

**Lösning:**

Sådana problem uppstår ofta när du försöker ange konto namn, lösen ord eller container Sök väg manuellt. Vi rekommenderar att du använder den nya guiden för modulen [Importera data](import-data.md) som hjälper dig att söka efter och kontrol lera namn.

Kontrol lera också om kontot, behållaren eller blobben har tagits bort. Använd ett annat Azure Storage-verktyg för att kontrol lera att konto namnet och lösen ordet har angetts korrekt och att behållaren finns. 

Vissa nyare konto typer stöds inte av Azure Machine Learning. Till exempel kan de nya lagrings typerna "frekvent" eller "kall" inte användas för Machine Learning. Både klassiska lagrings konton och lagrings konton som skapats som "allmänna ändamål" fungerar bra.

Om den fullständiga sökvägen till en BLOB har angetts kontrollerar du att sökvägen har angetts som **container/blobname** och att både behållaren och bloben finns i kontot.  

 Sökvägen får inte innehålla något inledande snedstreck. Till exempel är fel **/container/BLOB** och ska anges som **container/BLOB**.  


|Undantags meddelanden|
|------------------------|
|Namnet på Azure Storage-kontot eller containern är felaktigt.|
|Namnet på Azure Storage-kontot {account_name} eller container namnet {container_name} är felaktigt. ett behållar namn för format containern/blobben förväntades.|


## <a name="error-0010"></a>Fel 0010  
 Undantag uppstår om indata-datauppsättningar har kolumn namn som ska matcha men inte.  

 Det här felet visas i Azure Machine Learning om kolumn indexet i meddelandet har olika kolumn namn i de två data uppsättningarna.  

**Lösning:** Använd [Redigera metadata](edit-metadata.md) eller ändra den ursprungliga data uppsättningen till samma kolumn namn för det angivna kolumn indexet.  

|Undantags meddelanden|
|------------------------|
|Kolumner med motsvarande index i indata-datauppsättningar har olika namn.|
|Kolumn namnen är inte desamma för kolumnen {col_index} (noll-based) för indata-datauppsättningar ({DataSet1} och {DataSet2}).|


## <a name="error-0011"></a>Fel 0011  
 Undantag inträffar om det skickade kolumn uppsättnings argumentet inte gäller för någon av data mängds kolumnerna.  

 Det här felet visas i Azure Machine Learning om det angivna kolumn urvalet inte matchar någon av kolumnerna i den angivna data uppsättningen.  

 Du kan också få det här felet om du inte har valt en kolumn och minst en kolumn krävs för att modulen ska fungera.  

**Lösning:** Ändra kolumn valet i modulen så att den kommer att gälla för kolumnerna i data uppsättningen.  

 Om modulen kräver att du väljer en viss kolumn, till exempel en etikett kolumn, kontrollerar du att den högra kolumnen är markerad.  

 Om olämpliga kolumner är markerade tar du bort dem och kör pipelinen igen.  

|Undantags meddelanden|
|------------------------|
|Angiven kolumn uppsättning gäller inte för någon av data mängds kolumnerna.|
|Den angivna kolumn uppsättningen {column_set} gäller inte för någon av data mängds kolumnerna.|


## <a name="error-0012"></a>Fel 0012  
 Undantag uppstår om det inte gick att skapa en instans av klassen med den angivna argument uppsättningen.  

**Lösning:** Det här felet är inte åtgärds bara av användaren och kommer att bli inaktuell i en framtida version.  

|Undantags meddelanden|
|------------------------|
|Modell som inte är tränad, träna modellen först.|
|Modell som inte är tränad ({arg_name}), Använd tränad modell.|


## <a name="error-0013"></a>Fel 0013  
 Undantag uppstår om den som har skickats till modulen är en ogiltig typ.  

 Felet uppstår när en utbildad modell inte är kompatibel med den anslutna bedömnings modulen. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Lösning:**

Ta reda på vilken typ av elev som produceras av modulen utbildning och Bestäm vilken bedömnings modul som är lämplig för din elev. 

Om modellen tränas med någon av de specialiserade inlärnings modulerna ansluter du bara den tränade modellen till motsvarande anpassade bedömnings modell. 


|Modell typ|Modul för utbildning| Bedömnings modul|
|----|----|----|
|alla klassificerare|[Träningsmodell](train-model.md) |[Poängmodell](score-model.md)|
|Regressions modell|[Träningsmodell](train-model.md) |[Poängmodell](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Undantags meddelanden|
|------------------------|
|En elev av en ogiltig typ har skickats.|
|Eleven {arg_name} har en ogiltig typ.|
|Eleven {arg_name} har den ogiltiga typen {learner_type}.|
|En elev av en ogiltig typ har skickats. Undantags meddelande: {exception_message}|


## <a name="error-0014"></a>Fel 0014  
 Undantag uppstår om antalet unika kolumn värden är större än tillåtet.  

 Det här felet uppstår när en kolumn innehåller för många unika värden, t. ex. en ID-kolumn eller text kolumn. Du kanske ser det här felet om du anger att en kolumn ska hanteras som kategoriska-data, men det finns för många unika värden i kolumnen för att tillåta bearbetning. Du kan också se det här felet om det uppstår ett matchnings fel mellan antalet unika värden i två indata.   

Felet för unika värden är större än vad som tillåts om **båda** uppfyller följande villkor:

- Mer än 97% instanser av en kolumn är unika värden, vilket innebär att nästan alla kategorier skiljer sig från varandra.
- En kolumn har över 1000 unika värden.

**Lösning:**

Öppna den modul som genererade felet och identifiera de kolumner som används som indata. För vissa moduler kan du högerklicka på data uppsättningen och välja **visualisera** för att hämta statistik för enskilda kolumner, inklusive antalet unika värden och deras distribution.

För kolumner som du tänker använda för gruppering eller kategorisering, vidta åtgärder för att minska antalet unika värden i kolumner. Du kan minska på olika sätt beroende på kolumnens datatyp. 

För ID-kolumner som inte är meningsfulla funktioner under träna en modell kan du använda [Redigera metadata](../algorithm-module-reference/edit-metadata.md) för att markera kolumnen som **klar** och den kommer inte att användas vid inlärningen av en modell. 

För text kolumner kan du använda [funktionen hashing](../algorithm-module-reference/feature-hashing.md) eller [extrahera N-gram-funktioner från text-modulen](../algorithm-module-reference/extract-n-gram-features-from-text.md) för att Förbearbeta text kolumner.
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Det går inte att hitta någon lösning som matchar ditt scenario? Du kan ge feedback om det här avsnittet som innehåller namnet på den modul som orsakade felet och data typen och kolumnens kardinalitet. Vi kommer att använda informationen för att tillhandahålla mer riktade fel söknings steg för vanliga scenarier.   

|Undantags meddelanden|
|------------------------|
|Mängden unika kolumn värden är större än vad som tillåts.|
|Antalet unika värden i kolumnen: {column_name} är större än tillåtet.|
|Antalet unika värden i kolumnen: {column_name} överskrider antalet tupler {begränsning}.|


## <a name="error-0015"></a>Fel 0015  
 Undantag uppstår om databas anslutningen har misslyckats.  

 Du får det här felet om du anger ett felaktigt SQL-kontonamn, ett lösen ord, en databas server eller ett databas namn, eller om det inte går att upprätta en anslutning till databasen på grund av problem med databasen eller servern.  

**Lösning:** Kontrol lera att konto namnet, lösen ordet, databas servern och databasen har angetts korrekt och att det angivna kontot har rätt behörighets nivå. Kontrol lera att databasen är tillgänglig för närvarande.  

|Undantags meddelanden|
|------------------------|
|Det gick inte att ansluta till databasen.|
|Det gick inte att ansluta till databasen: {connection_str}.|


## <a name="error-0016"></a>Fel 0016  
 Undantag uppstår om indata-datauppsättningar som skickas till modulen ska ha kompatibla kolumn typer men inte.  

 Du får det här felet i Azure Machine Learning om kolumn typerna som skickas i två eller flera data uppsättningar inte är kompatibla med varandra.  

**Lösning:** Använd [Redigera metadata](edit-metadata.md) eller ändra den ursprungliga indata-datauppsättningen<!--, or use [Convert to Dataset](convert-to-dataset.md)--> för att säkerställa att kolumn typerna är kompatibla.  

|Undantags meddelanden|
|------------------------|
|Kolumner med motsvarande index i indata-datauppsättningar har inkompatibla typer.|
|Kolumnerna {first_col_names} är inkompatibla mellan tåg-och test data.|
|Kolumnerna {first_col_names} och {second_col_names} är inkompatibla.|
|Kolumn element typerna är inte kompatibla för kolumnen {first_col_names} (nollbaserat) för indata-datauppsättningar ({first_dataset_names} respektive {second_dataset_names}).|


## <a name="error-0017"></a>Fel 0017  
 Undantag uppstår om en markerad kolumn använder en datatyp som inte stöds av den aktuella modulen.  

 Du kan till exempel få det här felet i Azure Machine Learning om ditt kolumn val innehåller en kolumn med en datatyp som inte kan bearbetas av modulen, till exempel en sträng kolumn för en matematik åtgärd, eller en score-kolumn där en kategoriska Feature-kolumn krävs.  

**Lösning:**
 1. Identifiera den kolumn som är problemet.
 2. Granska kraven för modulen.
 3. Ändra kolumnen så att den uppfyller kraven. Du kan behöva använda flera av följande moduler för att göra ändringar, beroende på kolumn och konvertering som du försöker:
    + Använd [Redigera metadata](edit-metadata.md) om du vill ändra data typen för kolumner eller ändra kolumn användningen från funktion till numerisk, kategoriska till icke-kategoriska, och så vidare.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Som sista utväg kan du behöva ändra den ursprungliga indata-datauppsättningen.

> [!TIP]
> Det går inte att hitta någon lösning som matchar ditt scenario? Du kan ge feedback om det här avsnittet som innehåller namnet på den modul som orsakade felet och data typen och kolumnens kardinalitet. Vi kommer att använda informationen för att tillhandahålla mer riktade fel söknings steg för vanliga scenarier. 

|Undantags meddelanden|
|------------------------|
|Det går inte att bearbeta kolumnen av den aktuella typen. Typen stöds inte av modulen.|
|Det går inte att bearbeta kolumnen av typen {col_type}. Typen stöds inte av modulen.|
|Det går inte att bearbeta kolumnen {col_name} av typen {col_type}. Typen stöds inte av modulen.|
|Det går inte att bearbeta kolumnen {col_name} av typen {col_type}. Typen stöds inte av modulen. Parameter namn: {arg_name}.|


## <a name="error-0018"></a>Fel 0018  
 Undantaget inträffar om indata-datauppsättningen är ogiltig.  

**Lösning:** Det här felet i Azure Machine Learning kan visas i många sammanhang, så det finns ingen enskild lösning. I allmänhet anger felet att data som angetts som indata till en modul har fel antal kolumner eller att data typen inte överensstämmer med kraven för modulen. Exempel:  

-   Modulen kräver en etikett kolumn, men ingen kolumn har marker ATS som en etikett eller så har du inte valt en etikett kolumn ännu.  
  
-   Modulen kräver att data är kategoriska men dina data är numeriska.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Data har fel format.  
  
-   Importerade data innehåller ogiltiga tecken, felaktiga värden eller out-of-Range-värden.  
-   Kolumnen är tom eller innehåller för många värden som saknas.  

 För att fastställa kraven och hur dina data kan se igenom hjälp avsnittet för den modul som ska använda data uppsättningen som indata.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Undantags meddelanden|
|------------------------|
|Data uppsättningen är ogiltig.|
|{DataSet1} innehåller ogiltiga data.|
|{DataSet1} och {DataSet2} ska vara konsekventa columnwise.|
|{DataSet1} innehåller ogiltiga data, {orsak}.|
|{DataSet1} innehåller {invalid_data_category}. {troubleshoot_hint}|
|{DataSet1} är inte giltigt, {orsak}. {troubleshoot_hint}|


## <a name="error-0019"></a>Fel 0019  
 Undantag uppstår om kolumnen förväntas innehålla sorterade värden, men inte.  

 Du får det här felet i Azure Machine Learning om de angivna kolumn värdena är i fel ordning.  

**Lösning:** Sortera kolumnvärdena genom att manuellt ändra data uppsättningen för indata och köra modulen igen.  

|Undantags meddelanden|
|------------------------|
|Värdena i kolumnen sorteras inte.|
|Värdena i kolumnen {col_index} sorteras inte.|
|Värdena i kolumnen {col_index} för data uppsättningen {dataset} har inte sorterats.|
|Värdena i argumentet {arg_name} har inte sorterats i ordningen {sorting_order}.|


## <a name="error-0020"></a>Fel 0020  
 Undantag uppstår om antalet kolumner i några av data uppsättningarna som skickats till modulen är för litet.  

 Du får det här felet i Azure Machine Learning om inte tillräckligt många kolumner har valts för en modul.  

**Lösning:** Gå tillbaka i modulen och se till att kolumn väljaren har rätt antal valda kolumner.  

|Undantags meddelanden|
|------------------------|
|Antalet kolumner i indata-datauppsättningen är mindre än det tillåtna minimivärdet.|
|Antalet kolumner i indata-datamängden {arg_name} är mindre än det tillåtna minimivärdet.|
|Antalet kolumner i indata-datauppsättningen är mindre än det tillåtna minimivärdet {required_columns_count} kolumn (er).|
|Antalet kolumner i indata-datamängden {arg_name} är mindre än det tillåtna minimivärdet på {required_columns_count} kolumn (er).|


## <a name="error-0021"></a>Fel 0021  
 Undantag uppstår om antalet rader i några av data uppsättningarna som skickats till modulen är för litet.  

 Det här felet visas i Azure Machine Learning när det inte finns tillräckligt många rader i data uppsättningen för att utföra den angivna åtgärden. Du kan till exempel se det här felet om indata-datauppsättningen är tom, eller om du försöker utföra en åtgärd som kräver att ett visst minsta antal rader är giltigt. Sådana åtgärder kan omfatta (men är inte begränsade till) gruppering eller klassificering baserat på statistiska metoder, vissa typer av diskretisering och inlärning med antal.  

**Lösning:**

 + Öppna den modul som returnerade felet och kontrol lera indata-och modul egenskaper. 
 + Kontrol lera att indata-datauppsättningen inte är tom och att det finns tillräckligt med rader med data som uppfyller de krav som beskrivs i modulens hjälp.  
 + Om dina data har lästs in från en extern källa, se till att data källan är tillgänglig och att det inte finns något fel eller någon ändring i data definitionen som skulle göra att import processen får färre rader.
 + Om du utför en åtgärd på data strömmen för den modul som kan påverka typen av data eller antalet värden, t. ex. rensnings-, delnings-eller kopplings åtgärder, kontrollerar du utdata för dessa åtgärder för att avgöra hur många rader som returneras.  

|Undantags meddelanden|
|------------------------|
|Antalet rader i indata-datauppsättningen är mindre än det tillåtna minimivärdet.|
|Antalet rader i indata-datauppsättningen är mindre än det tillåtna minimivärdet på {required_rows_count} rad (er).|
|Antalet rader i indata-datauppsättningen är mindre än det tillåtna minimivärdet på {required_rows_count} rad (er). pga|
|Antalet rader i indata-datamängden {arg_name} är mindre än det tillåtna minimivärdet på {required_rows_count} rad (er).|
|Antalet rader i indata-datamängden {arg_name} är {actual_rows_count}, vilket är mindre än det tillåtna minimivärdet på {required_rows_count} rad (er).|
|Antalet {row_type} rader i indata-datamängden {arg_name} är {actual_rows_count}, vilket är mindre än det tillåtna minimivärdet på {required_rows_count} rad (er).|


## <a name="error-0022"></a>Fel 0022  
 Undantag uppstår om antalet markerade kolumner i indata-datamängd inte motsvarar det förväntade antalet.  

 Det här felet i Azure Machine Learning kan inträffa när den underordnade modulen eller åtgärden kräver ett särskilt antal kolumner eller indata, och du har angett för få eller för många kolumner eller indata. Exempel:  

-   Du anger en enskild etikett kolumn eller nyckel kolumn och har valt flera kolumner av misstag.  
  
-   Du byter namn på kolumner, men tillhandahöll fler eller färre namn än det finns kolumner.  
  
-   Antalet kolumner i källan eller målet har ändrats eller stämmer inte överens med antalet kolumner som används av modulen.  
  
-   Du har angett en kommaavgränsad lista med värden för indata, men antalet värden matchar inte, eller så stöds inte flera indata.  

**Lösning:** Gå tillbaka i modulen och markera kolumn markeringen för att se till att rätt antal kolumner är markerat. Verifiera utdata från överordnade moduler och kraven för efterföljande åtgärder.  

 Om du använde något av alternativen för kolumn val som kan markera flera kolumner (kolumn index, alla funktioner, alla numeriska osv.), verifierar du det exakta antalet kolumner som returneras av markeringen.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Kontrol lera att de överordnade kolumnernas antal eller typ inte har ändrats.  

 Om du använder en rekommendations data uppsättning för att träna en modell måste du komma ihåg att rekommendationen förväntar sig ett begränsat antal kolumner, som motsvarar användar objekt par eller användar objekts rankning. Ta bort ytterligare kolumner innan du tränar data uppsättningarna modell eller data delnings rekommendation. Mer information finns i [dela data](split-data.md).  

|Undantags meddelanden|
|------------------------|
|Antalet markerade kolumner i indata-datamängden är inte lika med det förväntade talet.|
|Antalet markerade kolumner i indata-datamängden är inte lika med {expected_col_count}.|
|Kolumn urvals mönstret {selection_pattern_friendly_name} innehåller antalet markerade kolumner i indata-datamängden som inte är lika med {expected_col_count}.|
|Kolumn markerings mönstret {selection_pattern_friendly_name} förväntas ge {expected_col_count} kolumner markerade i indata-datauppsättningen, men {selected_col_count} kolumn (er) har faktiskt angetts.|


## <a name="error-0023"></a>Fel 0023  

Undantag inträffar om mål kolumnen i data uppsättningen dataset inte är giltig för den aktuella utbildaren.  

Det här felet uppstår i Azure Machine Learning om mål kolumnen (som valts i modulen Module) inte är av en giltig datatyp, innehåller alla saknade värden eller inte kategoriska som förväntat.  

**Lösning:** Gå tillbaka till modulens inmatade för att kontrol lera innehållet i kolumnen etikett/mål. Kontrol lera att det inte har alla värden som saknas. Om modulen förväntar sig att mål kolumnen ska vara kategoriska kontrollerar du att det finns fler än ett distinkt värde i kolumnen Target.  

|Undantags meddelanden|
|------------------------|
|Den angivna data uppsättningen innehåller en mål kolumn som inte stöds.|
|Den angivna data uppsättningen innehåller en mål kolumn ({column_index}) som inte stöds.|
|Den angivna data uppsättningen innehåller en mål kolumn {column_index} som inte stöds för en mer information av typen {learner_type}.|


## <a name="error-0024"></a>Fel 0024  
Undantag uppstår om data uppsättningen inte innehåller en etikett kolumn.  

 Det här felet uppstår i Azure Machine Learning när modulen kräver en etikett kolumn och data uppsättningen inte har en etikett kolumn. Till exempel kräver utvärderingen av en resultat uppsättning vanligt vis att det finns en etikett kolumn för att beräkna noggrannhets mått.  

Det kan också hända att en etikett kolumn finns i data uppsättningen, men inte identifieras korrekt av Azure Machine Learning.

**Lösning:**

+ Öppna den modul som genererade felet och ta reda på om en etikett kolumn finns. Namnet eller data typen för kolumnen spelar ingen roll, så länge kolumnen innehåller ett enda resultat (eller beroende variabel) som du försöker förutsäga. Om du inte är säker på vilken kolumn som har etiketten söker du efter ett generiskt namn, till exempel  *klass* eller *mål*. 
+  Om data uppsättningen inte innehåller någon etikett kolumn, är det möjligt att kolumnen etikett har tagits bort direkt eller av misstag. Det kan också vara att data uppsättningen inte är utdata från en underordnad poängsättnings-modul.
+ Om du uttryckligen vill markera kolumnen som etikett-kolumn, lägger du till modulen [Redigera metadata](edit-metadata.md) och ansluter data uppsättningen. Markera endast kolumnen etikett och välj **etikett** i list rutan **fält** . 
+ Om fel kolumn har valts som etikett kan du välja **Rensa etikett** från **fälten** för att åtgärda metadata för kolumnen. 
  
|Undantags meddelanden|
|------------------------|
|Det finns ingen etikett kolumn i data uppsättningen.|
|Det finns ingen etikett kolumn i {dataset_name}.|


## <a name="error-0025"></a>Fel 0,025  
 Undantag uppstår om data uppsättningen inte innehåller någon Scores-kolumn.  

 Det här felet uppstår i Azure Machine Learning om indatamängden till evaluate-modellen inte innehåller giltiga Poäng kolumner. Användaren försöker till exempel utvärdera en data uppsättning innan den beskrevs med en korrekt tränad modell, eller så släpptes kolumnen poäng direkt. Detta undantag uppstår även om poängen i de två data uppsättningarna är inkompatibla. Du kan till exempel försöka jämföra noggrannheten för en linjär modellerings regressor med en binär klassificerare.  

**Lösning:** Gå tillbaka till indatamängden för utvärdera modellen och Undersök om den innehåller en eller flera Poäng kolumner. Om inte, har data uppsättningen inte betyget eller så har inga poäng kolumner släppts i en överordnad modul.  

|Undantags meddelanden|
|------------------------|
|Det finns ingen Poäng kolumn i data uppsättningen.|
|Det finns ingen Poäng kolumn i {dataset_name}.|
|Det finns ingen Poäng kolumn i {dataset_name} som skapas av en {learner_type}. Poäng data uppsättningen med rätt typ av lära.|


## <a name="error-0026"></a>Fel 0026  
 Undantag uppstår om kolumner med samma namn inte är tillåtna.  

 Det här felet i Azure Machine Learning uppstår om flera kolumner har samma namn. Ett sätt som du kan få det här felet är om data uppsättningen inte har någon rubrik rad och kolumn namn tilldelas automatiskt: Col0, Col1 osv.  

**Lösning:** Om kolumner har samma namn, infogar du en modul för att [Redigera metadata](edit-metadata.md) mellan indata-datauppsättningen och modulen. Använd kolumn väljaren i [Redigera metadata](edit-metadata.md) för att välja kolumner att byta namn på, skriva de nya namnen i text rutan **nya kolumn namn** .  

|Undantags meddelanden|
|------------------------|
|Samma kolumn namn anges i argument. Samma kolumn namn tillåts inte i modulen.|
|Samma kolumn namn i argumenten {arg_name_1} och {arg_name_2} är inte tillåtna. Ange olika namn.|


## <a name="error-0027"></a>Fel 0027  
 Undantag inträffar om två objekt måste ha samma storlek men inte.  

 Detta är ett vanligt fel i Azure Machine Learning och kan orsakas av många villkor.  

**Lösning:** Det finns ingen enskild lösning. Du kan dock söka efter villkor som följande:  

-   Om du byter namn på kolumner, se till att varje lista (kolumnerna och listan med nya namn) har samma antal objekt.  
  
-   Om du kopplar ihop eller sammanfogar två data uppsättningar, se till att de har samma schema.  
  
-   Om du ansluter två data uppsättningar som har flera kolumner måste du kontrol lera att nyckel kolumnerna har samma datatyp och välja alternativet **Tillåt dubbletter och bevara kolumn ordning i markeringen**.  

|Undantags meddelanden|
|------------------------|
|Storleken på överförda objekt är inkonsekvent.|
|Storleken på {friendly_name1} är inkonsekvent med storleken {friendly_name2}.|


## <a name="error-0028"></a>Fel 0028  
 Ett undantag inträffar i fallet när kolumn uppsättningen innehåller dubbletter av kolumn namn och det inte är tillåtet.  

 Det här felet uppstår i Azure Machine Learning när kolumn namn är duplicerade; Detta är inte unikt.  

**Lösning:** Om några kolumner har samma namn, lägger du till en instans av [Redigera metadata](edit-metadata.md) mellan indata-datauppsättningen och modulen som upphöjer felet. Använd kolumn väljaren i [Redigera metadata](edit-metadata.md) för att välja kolumner att byta namn på och Skriv de nya kolumn namnen i text rutan **nya kolumn namn** . Om du byter namn på flera kolumner kontrollerar du att värdena som du skriver i de **nya kolumn namnen** är unika.  

|Undantags meddelanden|
|------------------------|
|Kolumn uppsättningen innehåller ett eller flera kolumn namn.|
|Namnet {duplicated_name} är en dubblett.|
|Namnet {duplicated_name} är dubblerat i {arg_name}.|
|Namnet {duplicated_name} är en dubblett. Information: {information}|


## <a name="error-0029"></a>Fel 0029  
 Undantag inträffar i fall när ogiltig URI skickas.  

 Det här felet uppstår i Azure Machine Learning om ogiltig URI skickas.  Du får det här felet om något av följande villkor är uppfyllt:  

-   Den offentliga eller SAS-URI: n som tillhandahölls för Azure Blob Storage för läsning eller skrivning innehåller ett fel.  
  
-   Tids perioden för SAS har upphört att gälla.  
  
-   Webb adressen via HTTP-källan representerar en fil eller en loopback-URI.  
  
-   Webb adressen via HTTP innehåller en felaktigt formaterad URL.  
  
-   URL: en kan inte matchas av fjär källan.  

**Lösning:** Gå tillbaka i modulen och kontrol lera formatet för URI: n. Om data källan är en webb adress via HTTP kontrollerar du att den avsedda källan inte är en fil eller en loopback-URI (localhost).  

|Undantags meddelanden|
|------------------------|
|Ogiltig URI har skickats.|
|URI: n {invalid_url} är ogiltig.|


## <a name="error-0030"></a>Fel 0030  
 Undantag uppstår i fallet när det inte går att hämta en fil.  

 Detta undantag i Azure Machine Learning inträffar när det inte går att hämta en fil. Du får det här undantaget när ett försök att läsa från en HTTP-källa misslyckades efter tre (3) nya försök.  

**Lösning:** Kontrol lera att URI: n till HTTP-källan är korrekt och att platsen är tillgänglig via Internet.  

|Undantags meddelanden|
|------------------------|
|Det går inte att hämta en fil.|
|Ett fel uppstod när filen skulle hämtas: {file_url}.|


## <a name="error-0031"></a>Fel 0031  
 Undantaget inträffar om antalet kolumner i kolumn uppsättningen är mindre än vad som behövs.  

 Det här felet uppstår i Azure Machine Learning om antalet valda kolumner är mindre än vad som behövs.  Du får det här felet om det minsta antalet kolumner som krävs inte är markerat.  

**Lösning:** Lägg till ytterligare kolumner i kolumn urvalet med hjälp av **kolumn väljaren**.  

|Undantags meddelanden|
|------------------------|
|Antalet kolumner i kolumn uppsättningen är mindre än vad som krävs.|
|Minst {required_columns_count} kolumn (er) måste anges för indataargumentet {arg_name}.|
|Minst {required_columns_count} kolumn (er) måste anges för indataargumentet {arg_name}. Det faktiska antalet angivna kolumner är {input_columns_count}.|


## <a name="error-0032"></a>Fel 0032  
 Undantag inträffar om argumentet inte är ett tal.  

 Du får det här felet i Azure Machine Learning om argumentet är Double eller NaN.  

**Lösning:** Ändra det angivna argumentet så att det använder ett giltigt värde.  

|Undantags meddelanden|
|------------------------|
|Argumentet är inte ett tal.|
|{arg_name} är inte ett tal.|


## <a name="error-0033"></a>Fel 0033  
 Undantaget inträffar om argumentet är oändligt.  

 Det här felet uppstår i Azure Machine Learning om argumentet är oändligt. Du får det här felet om argumentet är antingen `double.NegativeInfinity` eller `double.PositiveInfinity` .  

**Lösning:** Ändra det angivna argumentet till ett giltigt värde.  

|Undantags meddelanden|
|------------------------|
|Argumentet måste vara ändligt.|
|{arg_name} är inte begränsad.|
|Kolumnen {column_name} innehåller oändliga värden.|


## <a name="error-0034"></a>Fel 0034  
 Undantag uppstår om det finns fler än en klassificering för ett angivet användar objekt par.  

 Det här felet i Azure Machine Learning uppstår i rekommendationen om ett användar objekt par har fler än ett klassificerings värde.  

**Lösning:** Se till att paret User-item har ett enda klassificerings värde.  

|Undantags meddelanden|
|------------------------|
|Det finns fler än en klassificering för värdena i data uppsättningen.|
|Fler än en klassificering för användaren {user} och objektet {Item} i data tabellen för klassificerings förutsägelse.|
|Fler än en klassificering för användaren {user} och objektet {Item} i {DataSet}.|


## <a name="error-0035"></a>Fel 0035  
 Undantag uppstår om inga funktioner har angetts för en angiven användare eller ett objekt.  

 Det här felet uppstår i Azure Machine Learning du försöker använda en rekommendations modell för att få en bedömning, men det går inte att hitta någon funktions vektor.  

**Lösning:**

Matchbox-rekommendationen har vissa krav som måste uppfyllas när du använder antingen objekt funktioner eller användar funktioner.  Det här felet indikerar att en funktions vektor saknas för en användare eller ett objekt som du har angett som indata. Se till att en Vector med funktioner är tillgänglig i data för varje användare eller objekt.  

 Om du t. ex. har tränat en rekommendations modell med funktioner som användarens ålder, plats eller inkomst, men nu vill skapa Poäng för nya användare som inte setts under utbildningen, måste du tillhandahålla en viss uppsättning funktioner (t. ex. ålder, plats och inkomst värden) för de nya användarna för att kunna göra lämpliga förutsägelser för dem. 

 Om du inte har några funktioner för dessa användare bör du överväga att skapa lämpliga funktioner i funktions teknik.  Om du till exempel inte har enskilda användares ålder eller inkomst värden kan du generera ungefärliga värden som ska användas för en grupp av användare. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Upplösningen är inte tillämplig för ditt ärende? Du är välkommen att skicka feedback om den här artikeln och ange information om scenariot, inklusive modulen och antalet rader i kolumnen. Vi kommer att använda den här informationen för att tillhandahålla mer detaljerade fel söknings steg i framtiden.

|Undantags meddelanden|
|------------------------|
|Inga funktioner har angetts för en obligatorisk användare eller ett objekt.|
|Funktioner för {required_feature_name} krävs men har inte angetts.|


## <a name="error-0036"></a>Fel 0036  
 Undantag uppstår om flera funktions vektorer har angetts för en specifik användare eller ett objekt.  

 Det här felet uppstår i Azure Machine Learning om en funktions vektor har definierats mer än en gång.  

**Lösning:** Se till att funktions vektorn inte har definierats mer än en gång.  

|Undantags meddelanden|
|------------------------|
|Duplicera funktions definition för en användare eller ett objekt.|


## <a name="error-0037"></a>Fel 0037  
 Undantag uppstår om flera etikett kolumner har angetts och bara ett tillåts.  

 Det här felet uppstår i Azure Machine Learning om fler än en kolumn har valts som den nya etikett kolumnen. De flesta övervakade Learning-algoritmer kräver att en kolumn markeras som mål eller etikett.  

**Lösning:** Se till att välja en enskild kolumn som den nya etikett kolumnen.  

|Undantags meddelanden|
|------------------------|
|Flera etikett kolumner har angetts.|
|Flera etikett kolumner har angetts i {dataset_name}.|


## <a name="error-0039"></a>Fel 0039  
 Ett undantag uppstår om en åtgärd har misslyckats.  

 Det här felet uppstår i Azure Machine Learning när en intern åtgärd inte kan slutföras.  

**Lösning:** Det här felet orsakas av många villkor och det finns ingen speciell påföljd.  
 Följande tabell innehåller allmänna meddelanden för det här felet, som följs av en speciell Beskrivning av villkoret. 

 Om det inte finns någon information kan du [Skicka feedback från Microsoft Q&en fråge sida](/answers/topics/azure-machine-learning-studio-classic.html) och ange information om de moduler som genererade felet och relaterade villkor.

|Undantags meddelanden|
|------------------------|
|Åtgärden misslyckades.|
|Ett fel uppstod när åtgärden slutfördes: {failed_operation}.|
|Ett fel uppstod när åtgärden slutfördes: {failed_operation}. Orsak: {orsak}.|


## <a name="error-0042"></a>Fel 0042  
 Ett undantag inträffar när det inte går att konvertera kolumnen till en annan typ.  

 Det här felet uppstår i Azure Machine Learning när det inte går att konvertera kolumnen till den angivna typen.  Du får det här felet om en modul kräver en viss datatyp, till exempel DateTime, text, flytt ALS nummer eller heltal, men det går inte att konvertera en befintlig kolumn till den typ som krävs.  

Du kan till exempel välja en kolumn och försöka konvertera den till en numerisk datatyp som används i en matematik åtgärd och få det här felet om kolumnen innehöll ogiltiga data. 

Ett annat skäl till att du kan få det här felet om du försöker använda en kolumn som innehåller flytt ALS nummer eller många unika värden som en kategoriska kolumn. 

**Lösning:**

+ Öppna hjälp sidan för den modul som genererade felet och kontrol lera data typs kraven.
+ Granska data typerna för kolumnerna i data uppsättningen för indata.
+ Granska data som har sitt ursprung i så kallade schema mindre data källor.
+ Kontrol lera data uppsättningen för saknade värden eller specialtecken som kan blockera konverteringen till önskad datatyp. 
    + Numeriska data typer bör vara konsekventa: Sök efter flytt ALS nummer i en kolumn med heltal.
    + Sök efter text strängar eller NA-värden i en tal kolumn. 
    + Booleska värden kan konverteras till en lämplig representation beroende på den data typ som krävs.
    + Undersök text kolumner för icke-Unicode-tecken, TABB-tecken eller kontroll tecken
    + Datetime-data bör vara konsekventa för att undvika modellerings fel, men rensning kan vara komplicerat på grund av många format. Överväg att använda <!--the [Execute R Script](execute-r-script.md) or -->Utför rensning genom att [köra Python-skript](execute-python-script.md) moduler.  
+ Om det behövs ändrar du värdena i indata-datauppsättningen så att kolumnen kan konverteras korrekt. Ändringen kan omfatta diskretisering, trunkering eller avrundning, Eli minering av avvikande eller Imputation av värden som saknas. I följande artiklar finns några vanliga data omvandlings scenarier i Machine Learning:
    + [Rensa data som saknas](clean-missing-data.md)
    + [Normalisera Data](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Är du klar med upplösning eller ej tillämpligt på ditt fall? Du är välkommen att skicka feedback om den här artikeln och ange information om scenariot, inklusive modulen och data typen för kolumnen. Vi kommer att använda den här informationen för att tillhandahålla mer detaljerade fel söknings steg i framtiden.  

|Undantags meddelanden|
|------------------------|
|Tillåts inte konvertering.|
|Det gick inte att konvertera kolumnen av typen {Type1} till kolumnen av typen {TYPE2}.|
|Det gick inte att konvertera kolumnen {col_name1} av typen {Type1} till kolumnen av typen {TYPE2}.|
|Det gick inte att konvertera kolumnen {col_name1} av typen {Type1} till kolumnen {col_name2} av typen {TYPE2}.|


## <a name="error-0044"></a>Fel 0044  
 Ett undantag inträffar när det inte går att härleda element typen för kolumnen från befintliga värden.  

 Det här felet uppstår i Azure Machine Learning när det inte går att härleda typen av en kolumn eller kolumner i en data uppsättning. Detta inträffar vanligt vis när två eller flera data uppsättningar sammanfogas med olika element typer. Om Azure Machine Learning inte kan fastställa en gemensam typ som kan representera alla värden i en kolumn eller kolumner utan att information går förlorad, genereras det här felet.  

**Lösning:** Se till att alla värden i en kolumn i båda data uppsättningarna kombineras antingen av samma typ (numeriska, booleska, kategoriska, sträng, datum osv.) eller kan tvingas till samma typ.  

|Undantags meddelanden|
|------------------------|
|Det går inte att härleda element typen för kolumnen.|
|Det går inte att härleda element typen för kolumnen {column_name}--alla element är null-referenser.|
|Det går inte att härleda element typen för kolumnen {column_name} för data uppsättningen {dataset_name}--alla element är null-referenser.|


## <a name="error-0045"></a>Fel 0045  
 Ett undantag inträffar när det inte går att skapa en kolumn på grund av blandade element typer i källan.  

 Det här felet i Azure Machine Learning skapas när element typerna för två data uppsättningar som kombineras är olika.  

**Lösning:** Se till att alla värden i en specifik kolumn i båda data uppsättningarna kombineras av samma typ (numeriska, booleska, kategoriska, sträng, datum osv.).  

|Undantags meddelanden|
|------------------------|
|Det går inte att skapa en kolumn med blandade element typer.|
|Det går inte att skapa kolumnen med ID {column_id} för blandade element typer:<br />Typ av data [{row_1}, {column_id}] är {type_1}. <br />Typ av data [{row_2}, {column_id}] är {type_2}.|
|Det går inte att skapa kolumnen med ID {column_id} för blandade element typer:<br />Typen i segmentet {chunk_id_1} är {type_1}. <br />Typen i segmentet {chunk_id_2} är {type_2} med segment storleken: {chunk_size}.|


## <a name="error-0046"></a>Fel 0046  
 Undantag inträffar när det inte går att skapa katalogen på den angivna sökvägen.  

 Det här felet uppstår i Azure Machine Learning när det inte går att skapa en katalog på den angivna sökvägen. Du får det här felet om någon del av sökvägen till utdata-katalogen för en Hive-fråga är felaktig eller inte går att komma åt.  

**Lösning:** Gå tillbaka i modulen och kontrol lera att katalog Sök vägen är korrekt formaterad och att den är tillgänglig med de aktuella autentiseringsuppgifterna.  

|Undantags meddelanden|
|------------------------|
|Ange en giltig utmatnings katalog.|
|Det går inte att skapa katalogen: {Path}. Ange en giltig sökväg.|


## <a name="error-0047"></a>Fel 0047  
 Undantag uppstår om antalet funktions kolumner i några av de data uppsättningar som skickats till modulen är för litet.  

 Det här felet i Azure Machine Learning uppstår om data uppsättningen för indata till träning inte innehåller det minsta antalet kolumner som krävs av algoritmen. Vanligt vis är antingen data uppsättningen tom eller innehåller bara utbildnings kolumner.  

**Lösning:** Gå tillbaka till indata-datauppsättningen för att se till att det finns en eller flera andra kolumner förutom kolumnen etikett.  

|Undantags meddelanden|
|------------------------|
|Antalet funktions kolumner i indata-datauppsättningen är mindre än det tillåtna minimivärdet.|
|Antalet funktions kolumner i indata-datauppsättningen är mindre än det tillåtna minimivärdet {required_columns_count} kolumn (er).|
|Antalet funktions kolumner i indata-datamängden {arg_name} är mindre än det tillåtna minimivärdet {required_columns_count} kolumn (er).|


## <a name="error-0048"></a>Fel 0048  
 Undantag uppstår i fallet när det inte går att öppna en fil.  

 Det här felet uppstår i Azure Machine Learning när det inte går att öppna en fil för läsning eller skrivning. Du kan få det här felet av följande anledningar:  

-   Behållaren eller filen (BLOB) finns inte  
  
-   Åtkomst nivån för filen eller behållaren tillåter inte åtkomst till filen  
  
-   Filen är för stor för att kunna läsas eller fel format  

**Lösning:** Gå tillbaka till modulen och filen som du försöker läsa.  

 Kontrol lera att namnen på behållaren och filen är korrekta.  

 Använd den klassiska Azure-portalen eller ett Azure Storage-verktyg för att kontrol lera att du har behörighet att komma åt filen.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Undantags meddelanden|
|------------------------|
|Det gick inte att öppna en fil.|
|Ett fel uppstod när filen öppnades: {file_name}.|
|Ett fel uppstod när filen öppnades: {file_name}. Lagrings undantags meddelande: {exception}.|


## <a name="error-0049"></a>Fel 0049  
 Undantag uppstår i fallet när det inte går att parsa en fil.  

 Det här felet uppstår i Azure Machine Learning när det inte går att parsa en fil. Du får det här felet om fil formatet som valts i modulen [Importera data](import-data.md) inte matchar det faktiska formatet för filen, eller om filen innehåller ett okänt.  

**Lösning:** Gå tillbaka till modulen och korrigera fil formats valet om den inte matchar fil formatet. Om möjligt bör du kontrol lera att filen inte innehåller några ogiltiga tecken.  

|Undantags meddelanden|
|------------------------|
|Det går inte att parsa en fil.|
|Fel vid parsning av {file_format}-filen.|
|Fel vid parsning av {file_format}-filen: {file_name}.|
|Fel vid parsning av {file_format}-filen. Orsak: {failure_reason}.|
|Fel vid parsning av {file_format}-filen: {file_name}. Orsak: {failure_reason}.|


## <a name="error-0052"></a>Fel 0052  
 Undantag uppstår om nyckeln för Azure Storage-kontot har angetts felaktigt.  

 Det här felet uppstår i Azure Machine Learning om nyckeln som används för att komma åt Azure Storage-kontot är felaktig. Du kan till exempel se det här felet om Azure Storage-nyckeln trunkerades när den kopierades och klistrades in, eller om fel nyckel användes.  

 Mer information om hur du hämtar nyckeln för ett Azure Storage-konto finns i [Visa, kopiera och återskapa lagrings åtkomst nycklar](../../storage/common/storage-account-create.md).  

**Lösning:** Gå tillbaka modulen och kontrol lera att Azure Storage-nyckeln är korrekt för kontot. Kopiera nyckeln igen från den klassiska Azure-portalen om det behövs.  

|Undantags meddelanden|
|------------------------|
|Azure Storage-kontots nyckel är felaktig.|


## <a name="error-0053"></a>Fel 0053  
 Undantag inträffar i fallet när det inte finns några användar funktioner eller objekt för matchbox-rekommendationer.  

 Det här felet i Azure Machine Learning skapas när det inte går att hitta en funktions vektor.  

**Lösning:** Se till att det finns en funktions vektor i indata-datauppsättningen.  

|Undantags meddelanden|
|------------------------|
|Användar funktioner eller/och objekt krävs, men har inte angetts.|


## <a name="error-0056"></a>Fel 0056  
 Undantag uppstår om de kolumner som du har valt för en åtgärd strider mot kraven.  

 Det här felet uppstår Azure Machine Learning när du väljer kolumner för en åtgärd som kräver att kolumnen är av en viss datatyp. 

 Det här felet kan också inträffa om kolumnen har rätt datatyp, men modulen som du använder kräver att kolumnen också är markerad som en funktions-, etikett-eller kategoriska-kolumn.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Lösning:**

1.  Granska data typen för de kolumner som för närvarande är markerade. 

2. Kontrol lera om de markerade kolumnerna är kategoriska, Label eller Feature columns.  
  
3.  Granska hjälp avsnittet för modulen där du gjorde kolumn urvalet för att avgöra om det finns särskilda krav för data typen eller kolumn användningen.  
  
3.  Använd [Redigera metadata](edit-metadata.md) om du vill ändra kolumn typen för den här åtgärdens varaktighet. Se till att ändra kolumn typen tillbaka till det ursprungliga värdet med hjälp av en annan instans av [Redigera metadata](edit-metadata.md), om du behöver den för efterföljande åtgärder.  

|Undantags meddelanden|
|------------------------|
|En eller flera valda kolumner fanns inte i en tillåten kategori.|
|Kolumnen med namnet {col_name} är inte i en tillåten kategori.|


## <a name="error-0057"></a>Fel 0057  
 Ett undantag uppstår vid försök att skapa en fil eller BLOB som redan finns.  

 Detta undantag uppstår när du använder modulen [Exportera data](export-data.md) eller annan modul för att spara resultaten av en pipeline i Azure Machine Learning till Azure Blob Storage, men du försöker skapa en fil eller BLOB som redan finns.   

**Lösning:**

 Du får bara det här felet om du tidigare har ställt in egenskaps **skrivnings läget för Azure Blob Storage** på **fel**. Enligt design genererar den här modulen ett fel om du försöker skriva en data uppsättning till en blob som redan finns.

 - Öppna modulens egenskaper och ändra egenskapen **Azure Blob Storage Skriv läge** till **Skriv över**.
 - Alternativt kan du ange namnet på en annan målfil eller fil och se till att du anger en blob som inte redan finns.  

|Undantags meddelanden|
|------------------------|
|Filen eller blobben finns redan.|
|Filen eller blobben {file_path} finns redan.|


## <a name="error-0058"></a>Fel 0058  
 Det här felet uppstår i Azure Machine Learning om data uppsättningen inte innehåller den förväntade etikett kolumnen.  

 Detta undantag kan också inträffa när den angivna etikett kolumnen inte matchar data eller datatyp som förväntas av en elev eller har fel värden. Detta undantag skapas till exempel när du använder en kolumn med Real värdes etiketter när du tränar en binär klassificerare.  

**Lösning:** Upplösningen beror på den eller de utbildare som du använder och data typerna för kolumnerna i data uppsättningen. Kontrol lera först kraven för Machine Learning-algoritmen eller modulen utbildning.  

 Gå tillbaka till indata-datauppsättningen. Kontrol lera att kolumnen du förväntar dig att behandlas som etikett har rätt datatyp för den modell som du skapar.  

 Kontrol lera indata för saknade värden och eliminera eller ersätt dem om det behövs.  

 Om det behövs lägger du till modulen [Redigera metadata](edit-metadata.md) och kontrollerar att kolumnen etikett är markerad som en etikett.  

|Undantags meddelanden|
|------------------------|
|Etikett kolumn värden och de resultat kolumn värden som visas är inte jämförbara.|
|Etikett kolumnen är inte som förväntat i {dataset_name}.|
|Etikett kolumnen är inte som förväntat i {dataset_name}, {orsak}.|
|Etikett kolumnen {column_name} förväntas inte i {dataset_name}.|
|Etikett kolumnen {column_name} förväntas inte i {dataset_name}, {orsak}.|


## <a name="error-0059"></a>Fel 0059  
 Undantag uppstår om ett kolumn index som har angetts i en kolumn väljare inte kan parsas.  

 Det här felet uppstår i Azure Machine Learning om ett kolumn index som anges när du använder kolumn väljaren inte kan parsas.  Det här felet visas när kolumn indexet har ett ogiltigt format som inte går att parsa.  

**Lösning:** Ändra kolumn indexet så att det använder ett giltigt index värde.  

|Undantags meddelanden|
|------------------------|
|Det gick inte att parsa ett eller flera angivna kolumn index eller index intervall.|
|Det gick inte att parsa kolumn indexet eller intervallet {column_index_or_range}.|


## <a name="error-0060"></a>Fel 0060  
 Undantag inträffar när ett kolumn intervall utanför intervallet anges i en kolumn väljare.  

 Det här felet uppstår i Azure Machine Learning när ett kolumn intervall utanför intervallet anges i kolumn väljaren. Det här felet visas om kolumn intervallet i kolumn väljaren inte motsvarar kolumnerna i data uppsättningen.  

**Lösning:** Ändra kolumn intervallet i kolumn väljaren så att det motsvarar kolumnerna i data uppsättningen.  

|Undantags meddelanden|
|------------------------|
|Det angivna kolumn index intervallet är ogiltigt eller utanför intervallet.|
|Kolumn intervallet {column_range} är ogiltigt eller utanför intervallet.|


## <a name="error-0061"></a>Fel 0061  
 Ett undantag uppstår vid försök att lägga till en rad i en DataTable som har ett annat antal kolumner än tabellen.  

 Det här felet uppstår i Azure Machine Learning när du försöker lägga till en rad i en data uppsättning som har ett annat antal kolumner än data uppsättningen.  Du får det här felet om den rad som läggs till i data uppsättningen har ett annat antal kolumner från indata-datauppsättningen.  Det går inte att lägga till raden i data uppsättningen om antalet kolumner är olika.  

**Lösning:** Ändra indata-datauppsättningen till att ha samma antal kolumner som den tillagda raden, eller ändra raden som lagts till i har samma antal kolumner som data uppsättningen.  

|Undantags meddelanden|
|------------------------|
|Alla tabeller måste ha samma antal kolumner.|
|Kolumner i segmentet {chunk_id_1} skiljer sig åt från segment {chunk_id_2} med segment storlek: {chunk_size}.|
|Kolumn antal i filen {filename_1} (Count = {column_count_1}) skiljer sig åt från filen {filename_2} (Count = {column_count_2}).|


## <a name="error-0062"></a>Fel 0062  
 Ett undantag uppstår vid försök att jämföra två modeller med olika typer av information.  

 Det här felet i Azure Machine Learning skapas när utvärderings måtten för två olika Poäng data uppsättningar inte kan jämföras. I det här fallet är det inte möjligt att jämföra effektiviteten hos de modeller som används för att producera de två poäng data uppsättningarna.  

**Lösning:** Kontrol lera att resultat resultaten produceras av samma typ av Machine Learning-modell (binära klassificering, regression, klassificering av flera klasser, rekommendation, klustring, avvikelse identifiering osv.) Alla modeller som du jämför måste ha samma elev typ.  

|Undantags meddelanden|
|------------------------|
|Alla modeller måste ha samma elev typ.|
|Fick en inkompatibel typ av information: {actual_learner_type}. Förväntade typer av information är: {expected_learner_type_list}.|


## <a name="error-0064"></a>Fel 0064  
 Undantag uppstår om namnet eller lagrings nyckeln för Azure Storage-kontot har angetts felaktigt.  

 Det här felet uppstår i Azure Machine Learning om namnet eller lagrings nyckeln för Azure Storage-kontot har angetts felaktigt. Du får det här felet om du anger ett felaktigt konto namn eller lösen ord för lagrings kontot. Detta kan inträffa om du anger konto namnet eller lösen ordet manuellt. Det kan också inträffa om kontot har tagits bort.  

**Lösning:** Kontrol lera att konto namnet och lösen ordet har angetts korrekt och att kontot finns.  

|Undantags meddelanden|
|------------------------|
|Namnet på Azure Storage-kontot eller lagrings nyckeln är felaktigt.|
|Namnet på Azure Storage-kontot {account_name} eller lagrings nyckeln för konto namnet är felaktigt.|


## <a name="error-0065"></a>Fel 0065  
 Ett undantag uppstår om ett Azure-Blob-namn har angetts felaktigt.  

 Det här felet uppstår i Azure Machine Learning om Azure Blob-namnet har angetts felaktigt.  Du får ett fel meddelande om:  

-   Det går inte att hitta blobben i den angivna behållaren.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Endast behållaren angavs som källa i en begäran om att [Importera data](import-data.md) när formatet var Excel eller CSV med kodning; sammanfogning av innehållet i alla blobar i en behållare tillåts inte med dessa format.  
  
-   En SAS-URI innehåller inte namnet på en giltig blob.  

**Lösning:** När du går tillbaka till modulen utlöses undantaget. Kontrol lera att den angivna blobben finns i behållaren i lagrings kontot och att du kan se blobben i den behörigheten. Kontrol lera att indatafilen har formatet **ContainerName/filename** om du har Excel eller CSV med kodnings format. Kontrol lera att en SAS-URI innehåller namnet på en giltig blob.  

|Undantags meddelanden|
|------------------------|
|BLOB-namnet för Azure Storage är felaktigt.|
|Azure Storage BLOB-namnet {blob_name} är felaktigt.|
|BLOB-namnet för Azure Storage med prefixet {blob_name_prefix} finns inte.|
|Det gick inte att hitta några Azure Storage-blobbar under behållaren {container_name}.|
|Det gick inte att hitta några Azure Storage-blobbar med sökvägen {blob_wildcard_path} för jokertecken.|


## <a name="error-0066"></a>Fel 0066  
 Ett undantag uppstår om en resurs inte kunde överföras till en Azure-blob.  

 Det här felet uppstår i Azure Machine Learning om en resurs inte kunde överföras till en Azure-blob.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Båda sparas i samma Azure Storage-konto som kontot som innehåller indatafilen.  

**Lösning:** Gå tillbaka i modulen. Kontrol lera att namnet på Azure-kontot, lagrings nyckeln och behållaren är korrekta och att kontot har behörighet att skriva till behållaren.  

|Undantags meddelanden|
|------------------------|
|Det gick inte att överföra resursen till Azure Storage.|
|Det gick inte att överföra filen {source_path} till Azure Storage som {dest_path}.|


## <a name="error-0067"></a>Fel 0067  
 Undantag uppstår om en data uppsättning har ett annat antal kolumner än förväntat.  

 Det här felet uppstår i Azure Machine Learning om en data uppsättning har ett annat antal kolumner än förväntat.  Du får det här felet när antalet kolumner i data uppsättningen skiljer sig från antalet kolumner som modulen förväntar sig under körningen.  

**Lösning:** Ändra indata-datauppsättningen eller parametrarna.  

|Undantags meddelanden|
|------------------------|
|Ett oväntat antal kolumner i DataTable.|
|Ett oväntat antal kolumner i data mängden {dataset_name}.|
|{Expected_column_count} kolumn (er) förväntades men {actual_column_count} kolumner hittades i stället.|
|I indata-datauppsättningen {dataset_name} förväntades {expected_column_count} kolumn (er), men {actual_column_count} kolumn (er) hittades i stället.|


## <a name="error-0068"></a>Fel 0068  
 Undantag uppstår om det angivna Hive-skriptet inte är korrekt.  

 Det här felet uppstår i Azure Machine Learning om det finns syntaxfel i ett Hive-skript eller om Hive-tolken påträffar ett fel vid körning av frågan eller skriptet.  

**Lösning:**

Fel meddelandet från Hive rapporteras normalt tillbaka i fel loggen så att du kan vidta åtgärder baserat på det specifika felet. 

+ Öppna modulen och undersök frågan efter misstag.  
+ Kontrol lera att frågan fungerar korrekt utanför Azure Machine Learning genom att logga in på Hive-konsolen för ditt Hadoop-kluster och köra frågan.  
+ Försök att placera kommentarer i Hive-skriptet på en separat rad i stället för att blanda körbara program satser och kommentarer på en enda rad.  

### <a name="resources"></a>Resurser

Se följande artiklar för hjälp med Hive-frågor för Machine Learning:

+ [Skapa Hive-tabeller och läsa in data från Azure Blob Storage](../team-data-science-process/move-hive-tables.md)
+ [Utforska data i tabeller med Hive-frågor](../team-data-science-process/explore-data-hive-tables.md)
+ [Skapa funktioner för data i ett Hadoop-kluster med hjälp av Hive-frågor](../team-data-science-process/create-features-hive.md)
+ [Hive för SQL-användare lathund Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Undantags meddelanden|
|------------------------|
|Hive-skriptet är felaktigt.|


## <a name="error-0069"></a>Fel 0069  
 Undantag uppstår om det angivna SQL-skriptet inte är korrekt.  

 Det här felet uppstår i Azure Machine Learning om det angivna SQL-skriptet har syntaxfel, eller om kolumnerna eller tabellen som anges i skriptet inte är giltiga. 

 Du får det här felet om SQL-motorn påträffar ett fel när frågan eller skriptet körs. SQL-felmeddelande rapporteras normalt igen i fel loggen så att du kan vidta åtgärder baserat på det specifika felet.  

**Lösning:** Gå tillbaka i modulen och granska SQL-frågan för misstag.  

 Kontrol lera att frågan fungerar korrekt utanför Azure ML genom att logga in på databas servern direkt och köra frågan.  

 Om det finns ett SQL-genererat meddelande som rapporter ATS av ett modul undantag, vidta åtgärder baserat på det rapporterade felet. Fel meddelandena innehåller till exempel ibland speciell vägledning om det troliga felet:
+ *Ingen sådan kolumn eller saknad databas*, vilket indikerar att du har angett ett kolumn namn som fel. Om du är säker på att kolumn namnet är korrekt kan du prova att använda hakparenteser eller citat tecken för att omsluta kolumn-ID: n.
+ *SQL Logic-fel \<SQL keyword\> nära*, vilket indikerar att du kan ha ett syntaxfel före det angivna nyckelordet

  
|Undantags meddelanden|
|------------------------|
|SQL-skriptet är felaktigt.|
|SQL-frågan {sql_query} är felaktig.|
|SQL-frågan {sql_query} är felaktig. Undantags meddelande: {exception}.|


## <a name="error-0070"></a>Fel 0070  
 Ett undantag uppstår vid försök att komma åt en Azure-tabell som inte finns.  

 Det här felet uppstår Azure Machine Learning när du försöker komma åt en Azure-tabell som inte finns. Du får det här felet om du anger en tabell i Azure Storage, som inte finns när du läser från eller skriver till Azure Table Storage. Detta kan inträffa om du skriver in namnet på den önskade tabellen eller om du har ett matchnings fel mellan mål namnet och lagrings typen. Du kan till exempel läsa från en tabell men ange namnet på en BLOB i stället.  

**Lösning:** Gå tillbaka i modulen för att kontrol lera att namnet på tabellen är korrekt.  

|Undantags meddelanden|
|------------------------|
|Azure-tabellen finns inte.|
|Azure-tabellen {table_name} finns inte.|


## <a name="error-0072"></a>Fel 0072  
 Undantag inträffar om tids gränsen för anslutningen uppnåddes.  

 Det här felet uppstår i Azure Machine Learning när tids gränsen för anslutningen uppnåddes. Du får det här felet om det finns problem med anslutningen till data källan eller målet, t. ex. långsam Internet anslutning, eller om data uppsättningen är stor och/eller om SQL-frågan för att läsa i data utför komplicerad bearbetning.  

**Lösning:** Ta reda på om det finns problem med långsamma anslutningar till Azure Storage eller Internet.  

|Undantags meddelanden|
|------------------------|
|Tids gränsen för anslutningen har inträffat.|


## <a name="error-0073"></a>Fel 0073  
 Undantag uppstår om ett fel inträffar när en kolumn konverteras till en annan typ.  

 Det här felet uppstår i Azure Machine Learning när det inte går att konvertera kolumnen till en annan typ.  Det här felet visas om en modul kräver en viss typ och det inte går att konvertera kolumnen till den nya typen.  

**Lösning:** Ändra indata-datauppsättningen så att kolumnen kan konverteras baserat på det inre undantaget.  

|Undantags meddelanden|
|------------------------|
|Det gick inte att konvertera kolumnen.|
|Det gick inte att konvertera kolumnen till {target_type}.|


## <a name="error-0075"></a>Fel 0075  
Undantag inträffar när en ogiltig diskretisering-funktion används vid quantizing av en data uppsättning.  

Det här felet uppstår i Azure Machine Learning när du försöker att använda bin-data med en metod som inte stöds, eller när parameter kombinationerna är ogiltiga.  

**Lösning:**

Fel hantering för den här händelsen introducerades i en tidigare version av Azure Machine Learning som tillåter mer anpassning av diskretisering-metoder. För närvarande är alla diskretisering-metoder baserade på ett val från en nedrullningsbar listruta, så tekniskt sett bör det inte längre vara möjligt att få det här felet.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Undantags meddelanden|
|------------------------|
|Ogiltig diskretisering-funktion används.|


## <a name="error-0077"></a>Fel 0077  
 Undantag inträffar när ett okänt skrivnings läge för BLOB-filen har överförts.  

 Det här felet uppstår i Azure Machine Learning om ett ogiltigt argument skickas i specifikationerna för en BLOB-fil eller källa.  

**Lösning:** I nästan alla moduler som importerar eller exporterar data till och från Azure Blob Storage, tilldelas parameter värden som styr skrivnings läget med hjälp av en nedrullningsbar listruta. Därför är det inte möjligt att skicka ett ogiltigt värde och det här felet bör inte visas. Det här felet kommer att vara inaktuellt i en senare version.  

|Undantags meddelanden|
|------------------------|
|BLOB-Write-läget stöds inte.|
|BLOB-Write-läget stöds inte: {blob_write_mode}.|


## <a name="error-0078"></a>Fel 0078  
 Undantag inträffar när HTTP-alternativet för [Importera data](import-data.md) får en 3xx-status kod som anger omdirigering.  

 Det här felet uppstår i Azure Machine Learning när HTTP-alternativet för [Importera data](import-data.md) får en 3xx (301, 302, 304 osv.) som anger omdirigering. Du får det här felet om du försöker ansluta till en HTTP-källa som omdirigerar webbläsaren till en annan sida. Av säkerhets skäl är det inte tillåtet att omdirigera webbplatser som data källor för Azure Machine Learning.  

**Lösning:** Om webbplatsen är en betrodd webbplats anger du den omdirigerade URL: en direkt.  

|Undantags meddelanden|
|------------------------|
|Http-omdirigering tillåts inte.|


## <a name="error-0079"></a>Fel 0079  
 Undantag uppstår om namn på Azure Storage container anges felaktigt.  

 Felet i Azure Machine Learning uppstår om namnet på Azure Storage-behållaren är felaktigt angivet. Du får det här felet om du inte har angett både behållaren och blob-namnet (fil) med hjälp **av sökvägen till bloben som börjar med container** -alternativet när du skriver till Azure Blob Storage.  

**Lösning:** Gå tillbaka till modulen [Exportera data](export-data.md) och kontrol lera att den angivna sökvägen till blobben innehåller både behållaren och fil namnet i formatet **container/filename**.  

|Undantags meddelanden|
|------------------------|
|Namnet på Azure Storage-behållaren är felaktigt.|
|Namnet på Azure Storage-behållaren {container_name} är felaktigt. ett behållar namn för format containern/blobben förväntades.|


## <a name="error-0080"></a>Fel 0080  
 Undantag inträffar när kolumnen med alla värden som saknas inte tillåts av modulen.  

 Det här felet i Azure Machine Learning skapas när en eller flera av kolumnerna som används av modulen innehåller alla värden som saknas. Om en modul exempelvis beräknar sammanställd statistik för varje kolumn, kan den inte användas i en kolumn som inte innehåller några data. I sådana fall stoppas modul körningen med detta undantag.  

**Lösning:** Gå tillbaka till indata-datauppsättningen och ta bort alla kolumner som innehåller alla saknade värden.  

|Undantags meddelanden|
|------------------------|
|Kolumner med värden som saknas är inte tillåtna.|
|Kolumnen {col_index_or_name} har alla värden som saknas.|


## <a name="error-0081"></a>Fel 0081  
 Undantag uppstår i PCA-modul om antalet dimensioner som ska minskas till är lika med antalet funktions kolumner i indata-datauppsättningen som innehåller minst en kolumn för sparse-funktioner.  

 Det här felet i Azure Machine Learning skapas om följande villkor uppfylls: (a) data uppsättningen för indata innehåller minst en null-optimerad kolumn och (b) det slutgiltiga antalet dimensioner som krävs är samma som antalet ingångs dimensioner.  

**Lösning:** Överväg att minska antalet dimensioner i utdatan till färre än antalet dimensioner i indata. Detta är vanligt i program av PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Undantags meddelanden|
|------------------------|
|För data uppsättning som innehåller null-optimerade funktions kolumner, ska de dimensioner som ska minskas till vara mindre än antalet funktions kolumner.|


## <a name="error-0082"></a>Fel 0082  
 Ett undantag inträffar när en modell inte kan avserialiseras.  

 Det här felet uppstår i Azure Machine Learning när en sparad Machine Learning-modell eller-transformering inte kan läsas in med en nyare version av Azure Machine Learning runtime som ett resultat av en brytande ändring.  

**Lösning:** Den utbildnings pipelinen som skapade modellen eller omvandlingen måste köras igen och modellen eller transformeringen måste sparas på nytt.  

|Undantags meddelanden|
|------------------------|
|Det gick inte att deserialisera modellen eftersom den förmodligen serialiseras med ett äldre format för serialisering. Omträna och spara om modellen.|


## <a name="error-0083"></a>Fel 0083  
 Ett undantag uppstår om data uppsättningen som används för träning inte kan användas för en konkret typ av elev.  

 Det här felet i Azure Machine Learning skapas när data uppsättningen är inkompatibel med den elev som tränas. Data uppsättningen kan till exempel innehålla minst ett värde som saknas i varje rad, och därför skulle hela data uppsättningen hoppas över under träningen. I andra fall förväntas inte några Machine Learning-algoritmer, till exempel avvikelse identifiering, etiketter visas och kan utlösa detta undantag om det finns etiketter i data uppsättningen.  

**Lösning:** Läs dokumentationen för den handare som används för att kontrol lera kraven för data uppsättningen för indata. Granska kolumnerna för att se att alla obligatoriska kolumner finns.  

|Undantags meddelanden|
|------------------------|
|Data uppsättningen som används för träning är ogiltig.|
|{data_name} innehåller ogiltiga data för träning.|
|{data_name} innehåller ogiltiga data för träning. Elev typ: {learner_type}.|
|{data_name} innehåller ogiltiga data för träning. Elev typ: {learner_type}. Orsak: {orsak}.|
|Det gick inte att tillämpa åtgärden {action_name} på tränings data {data_name}. Orsak: {orsak}.|


## <a name="error-0084"></a>Fel 0084  
 Undantag inträffar när resultat som skapats från ett R-skript utvärderas. Detta stöds för närvarande inte.  

 Felet i Azure Machine Learning uppstår om du försöker använda en av modulerna för att utvärdera en modell med utdata från ett R-skript som innehåller resultat.  

**Lösning:**

|Undantags meddelanden|
|------------------------|
|Det finns för närvarande inte stöd för att utvärdera poäng som skapats av en anpassad modell.|


## <a name="error-0085"></a>Fel 0085  
 Undantag inträffar när skript utvärderingen Miss lyckas med ett fel.  

 Det här felet uppstår Azure Machine Learning när du kör anpassade skript som innehåller syntaxfel.  

**Lösning:** Granska koden i ett externt redigerings program och Sök efter fel.  

|Undantags meddelanden|
|------------------------|
|Fel vid utvärdering av skript.|
|Följande fel uppstod under skript utvärderingen. mer information finns i utgående loggen:<br />----------Start av fel meddelande från {script_language}-tolken----------<br />meddelande<br />----------Fel meddelandets slut från {script_language}-tolken----------|


## <a name="error-0090"></a>Fel 0090  
 Undantag inträffar när det inte går att skapa Hive-tabell.  

 Det här felet uppstår i Azure Machine Learning när du använder [export data](export-data.md) eller ett annat alternativ för att spara data i ett HDInsight-kluster och det inte går att skapa den angivna Hive-tabellen.  

**Lösning:** Kontrol lera namnet på det Azure Storage-konto som är associerat med klustret och kontrol lera att du använder samma konto i egenskaperna för modulen.  

|Undantags meddelanden|
|------------------------|
|Det gick inte att skapa Hive-tabellen. För ett HDInsight-kluster ser du till att namnet på det Azure Storage-konto som är associerat med klustret är detsamma som det som skickas genom parametern modul.|
|Det gick inte att skapa Hive-tabellen {table_name}. För ett HDInsight-kluster ser du till att namnet på det Azure Storage-konto som är associerat med klustret är detsamma som det som skickas genom parametern modul.|
|Det gick inte att skapa Hive-tabellen {table_name}. För ett HDInsight-kluster kontrollerar du att namnet på Azure Storage-kontot som är associerat med klustret är {cluster_name}.|


## <a name="error-0102"></a>Fel 0102  
 Genereras när en ZIP-fil inte kan extraheras.  

 Det här felet uppstår i Azure Machine Learning när du importerar ett zippat paket med fil namns tillägget. zip, men paketet är inte en zip-fil, eller så använder filen inte ett zip-format som stöds.  

**Lösning:** Kontrol lera att den valda filen är en giltig. zip-fil och att den har komprimerats med hjälp av någon av de algoritmer som stöds.  

 Om du får det här felet när du importerar data uppsättningar i komprimerat format måste du kontrol lera att alla filer som finns i filen använder något av de fil format som stöds och att de är i Unicode-format.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Försök att läsa de önskade filerna i en ny komprimerad zippad mapp och försök att lägga till den anpassade modulen igen.  

|Undantags meddelanden|
|------------------------|
|Den angivna ZIP-filen har inte rätt format.|


## <a name="error-0105"></a>Fel 0105  
 Det här felet visas när en modul definitions fil innehåller en parameter typ som inte stöds  
  
 Det här felet i Azure Machine Learning skapas när du skapar en XML-definition för en anpassad modul och typen av parameter eller argument i definitionen inte matchar en typ som stöds.  
  
**Lösning:** Se till att type-egenskapen för ett **arg** -element i XML-definitions filen för anpassad modul är en typ som stöds.  
  
|Undantags meddelanden|  
|------------------------|  
|Parameter typen stöds inte.|  
|Parameter typen som inte stöds har {0} angetts.|  


## <a name="error-0107"></a>Fel 0107  
 Genereras när en modul definitions fil definierar en utdatatyp som inte stöds  
  
 Det här felet i Azure Machine Learning skapas när typen för en utdataport i en XML-definition för en anpassad modul inte matchar en typ som stöds.  
  
**Lösning:** Kontrol lera att typ egenskapen för ett utdata-element i XML-definitions filen för den anpassade modulen är en typ som stöds.  
  
|Undantags meddelanden|  
|------------------------|  
|Utdatatypen stöds inte.|  
|Utdatatypen {output_type} som inte stöds har angetts.|  


## <a name="error-0125"></a>Fel 0125  
 Utlöstes när schemat för flera data uppsättningar inte matchar.  

**Lösning:**

|Undantags meddelanden|
|------------------------|
|Data uppsättningens schema stämmer inte överens.|


## <a name="error-0127"></a>Fel 0127  
 Bild punkts storleken överskrider den tillåtna gränsen  

 Det här felet uppstår om du läser bilder från en avbildnings data uppsättning för klassificering och avbildningarna är större än vad modellen kan hantera.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Undantags meddelanden|
|------------------------|
|Bild punkts storleken överskrider den tillåtna gränsen.|
|Bild punkts storleken i filen {file_path} överskrider den tillåtna gränsen: {size_limit}.|


## <a name="error-0128"></a>Fel 0128  
 Antalet villkorsstyrda sannolikheter för kategoriska-kolumner överskrider gränsen.  

**Lösning:**

|Undantags meddelanden|
|------------------------|
|Antalet villkorsstyrda sannolikheter för kategoriska-kolumner överskrider gränsen.|
|Antalet villkorsstyrda sannolikheter för kategoriska-kolumner överskrider gränsen. Kolumnerna {column_name_or_index_1} och {column_name_or_index_2} är det problematiska paret.|


## <a name="error-0129"></a>Fel 0129  
 Antalet kolumner i data uppsättningen överskrider den tillåtna gränsen.  

**Lösning:**

|Undantags meddelanden|
|------------------------|
|Antalet kolumner i data uppsättningen överskrider den tillåtna gränsen.|
|Antalet kolumner i data uppsättningen i {dataset_name} överskrider tillåtet.|
|Antalet kolumner i data uppsättningen i {dataset_name} överskrider den tillåtna gränsen på {component_name}.|
|Antalet kolumner i data mängden i {dataset_name} överskrider tillåtet {limit_columns_count}-gräns på {component_name}.|


## <a name="error-0134"></a>Fel 0134
Ett undantag inträffar när etikett kolumnen saknas eller har otillräckligt antal etiketterade rader.  

Det här felet uppstår när modulen kräver en etikett kolumn, men du inte inkluderade någon i kolumn urvalet, eller så saknas kolumnen etikett för många värden.

Felet kan också uppstå när en tidigare åtgärd ändrar data uppsättningen så att otillräckliga rader är tillgängliga för en underordnad åtgärd. Anta till exempel att du använder ett uttryck i modulen **partition och exempel** för att dela upp en data uppsättning efter värden. Om inga matchningar hittas för ditt uttryck skulle en av de data uppsättningar som skapas från partitionen vara tom.

Lösning: 

 Om du lägger till en etikett kolumn i kolumn valet men inte känns igen, använder du modulen [Redigera metadata](edit-metadata.md) för att markera den som en etikett kolumn.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Sedan kan du använda modulen [Rensa data som saknas](clean-missing-data.md) för att ta bort rader med värden som saknas i kolumnen etikett. 

 Kontrol lera dina indata-datauppsättningar för att se till att de innehåller giltiga data, och tillräckligt många rader för att uppfylla kraven för åtgärden. Många algoritmer genererar ett fel meddelande om de kräver vissa minsta antal rader med data, men data innehåller bara några rader eller bara en rubrik.

|Undantags meddelanden|
|------------------------|
|Ett undantag inträffar när etikett kolumnen saknas eller har otillräckligt antal etiketterade rader.|
|Ett undantag inträffar när etikett kolumnen saknas eller innehåller färre än {required_rows_count} etiketterade rader.|
|Undantaget inträffar när etikett kolumnen i data uppsättningen {dataset_name} saknas eller innehåller färre än {required_rows_count} etiketterade rader.|


## <a name="error-0138"></a>Fel 0138  
 Minnet har tömts, det går inte att slutföra körningen av modulen. Nedsampling av data uppsättningen kan hjälpa till att minska problemet.  

 Felet uppstår när modulen som kör kräver mer minne än vad som är tillgängligt i Azure-behållaren. Detta kan inträffa om du arbetar med en stor data uppsättning och den aktuella åtgärden inte ryms i minnet.  

**Lösning:** Om du försöker läsa en stor data uppsättning och åtgärden inte kan slutföras kan det hjälpa att göra en nedsampling av data uppsättningen.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Undantags meddelanden|
|------------------------|
|Minnet har tömts, det går inte att slutföra körningen av modulen.|
|Minnet har tömts, det går inte att slutföra körningen av modulen. Information: {information}|


## <a name="error-0141"></a>Fel 0141  
 Undantag uppstår om antalet markerade numeriska kolumner och unika värden i kolumnerna kategoriska och String är för litet.  

 Det här felet uppstår i Azure Machine Learning när det inte finns tillräckligt många unika värden i den markerade kolumnen för att utföra åtgärden.  

**Lösning:** Vissa åtgärder utför statistiska åtgärder på funktions-och kategoriska kolumner, och om det inte finns tillräckligt med värden kan åtgärden Miss lyckas eller returnera ett ogiltigt resultat. Kontrol lera din data uppsättning för att se hur många värden som finns i kolumnerna Feature och Label, och Fastställ om åtgärden du försöker utföra är statistiskt giltig.  

 Om käll data uppsättningen är giltig kan du också kontrol lera om någon överordnad data behandling eller metadata har ändrat data och tagit bort vissa värden.  

 Om överordnade åtgärder innefattar delning, sampling eller omsampling, verifierar du att utdata innehåller det förväntade antalet rader och värden.  

|Undantags meddelanden|
|------------------------|
|Antalet markerade numeriska kolumner och unika värden i kolumnerna kategoriska och String är för litet.|
|Det totala antalet markerade numeriska kolumner och unika värden i kolumnerna kategoriska och String (för närvarande {actual_num}) bör vara minst {lower_boundary}.|


## <a name="error-0154"></a>Fel 0154  
 Undantag inträffar när användaren försöker ansluta data till nyckel kolumner med inkompatibel kolumn typ.

|Undantags meddelanden|
|------------------------|
|Nyckel kolumn element typerna är inte kompatibla.|
|Nyckel kolumn element typerna är inte kompatibla. (left: {keys_left}; höger: {keys_right})|


## <a name="error-0155"></a>Fel 0155  
 Undantag inträffar när kolumn namn för data mängden inte är sträng.

|Undantags meddelanden|
|------------------------|
|Kolumn namnet dataframe måste vara en sträng typ. Kolumn namn är inte sträng.|
|Kolumn namnet dataframe måste vara en sträng typ. Kolumn namnen {column_names} är inte sträng.|


## <a name="error-0156"></a>Fel 0156  
 Ett undantag inträffar när det inte gick att läsa data från Azure SQL Database.

|Undantags meddelanden|
|------------------------|
|Det gick inte att läsa data från Azure SQL Database.|
|Det gick inte att läsa data från Azure SQL Database: {detailed_message} DB: {database_server_name}: {database_name} fråga: {sql_statement}|


## <a name="error-0157"></a>Fel 0157  
 Data lagret hittades inte.

|Undantags meddelanden|
|------------------------|
|Data lager informationen är ogiltig.|
|Data lager informationen är ogiltig. Det gick inte att hämta AzureML-datalagret {datastore_name} i arbets ytan {workspace_name}.|


## <a name="error-0158"></a>Fel 0158
 Genereras när en omvandlings katalog är ogiltig.

|Undantags meddelanden|
|------------------------------------------------------------|
|Angivet TransformationDirectory är ogiltigt.|
|TransformationDirectory {arg_name} är ogiltigt. Orsak: {orsak}. Kör ett nytt inlärnings experiment som genererar Transformations filen. Om övnings experimentet har tagits bort måste du återskapa och spara transformeringsfilen.|
|TransformationDirectory {arg_name} är ogiltigt. Orsak: {orsak}. {troubleshoot_hint}|


## <a name="error-0159"></a>Fel 0159
 Undantag uppstår om modul modell katalogen är ogiltig. 

|Undantags meddelanden|
|------------------------------------------------------------|
|Angivet ModelDirectory är ogiltigt.|
|ModelDirectory {arg_name} är ogiltigt.|
|ModelDirectory {arg_name} är ogiltigt. Orsak: {orsak}.|
|ModelDirectory {arg_name} är ogiltigt. Orsak: {orsak}. {troubleshoot_hint}|


## <a name="error-1000"></a>Fel 1000  
Internt biblioteks undantag.  

Det här felet tillhandahålls för att samla in andra fel som inte hanteras av en intern motor. Därför kan orsaken till det här felet vara olika beroende på vilken modul som orsakade felet.  

För att få mer hjälp rekommenderar vi att du skickar det detaljerade meddelandet som medföljer felet till [Azure Machine Learning-forumet](/answers/topics/azure-machine-learning.html), tillsammans med en beskrivning av scenariot, inklusive data som används som indata. Den här feedbacken hjälper oss att prioritera fel och identifiera de viktigaste problemen för ytterligare arbete.  

|Undantags meddelanden|
|------------------------|
|Biblioteks undantag.|
|Biblioteks undantag: {exception}.|
|Okänt biblioteks undantag: {exception}. {customer_support_guidance}.|


## <a name="execute-python-script-module"></a>Köra Python-skript modul

Sök **i azureml_main** i **70_driver_logs** av **execute python-skriptfil** och du kan se vilken rad som inträffade i fel. Till exempel "File"/tmp/tmp01_ID/user_script. py ", rad 17 i azureml_main" anger att felet inträffade på 17-linjen i python-skriptet.
