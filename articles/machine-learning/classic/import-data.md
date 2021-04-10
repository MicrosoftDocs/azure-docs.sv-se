---
title: 'ML Studio (klassisk): importera utbildnings data – Azure'
description: Så här importerar du data till Azure Machine Learning Studio (klassisk) från olika data källor. Lär dig mer om vilka data typer och data format som stöds.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: db5f3cc4b9530c4aeac40786756b36cc0ac98728
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520382"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importera dina utbildnings data till Azure Machine Learning Studio (klassisk) från olika data källor

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Om du vill använda dina egna data i Machine Learning Studio (klassisk) för att utveckla och träna en förutsägelse analys lösning kan du använda data från: 

* **Lokal fil** – Läs in lokala data i förväg från hård disken för att skapa en data uppsättnings modul i din arbets yta
* **Online-datakällor** – Använd modulen [Importera data][import-data] för att få åtkomst till data från en av flera onlinekällor medan experimentet körs
* **Machine Learning Studio (klassisk) experiment** -Använd data som sparats som en data uppsättning i Machine Learning Studio (klassisk)
* [**SQL Server databas**](use-data-from-an-on-premises-sql-server.md) – Använd data från en SQL Server databas utan att behöva kopiera data manuellt

> [!NOTE]
> Det finns ett antal exempel data uppsättningar som är tillgängliga i Machine Learning Studio (klassisk) som du kan använda för tränings data. Information om dessa finns i [använda exempel data uppsättningar i Azure Machine Learning Studio (klassisk)](use-sample-datasets.md).

## <a name="prepare-data"></a>Förbereda data

Machine Learning Studio (klassisk) är utformad för att fungera med rektangulära eller tabell data, till exempel text data som är avgränsade eller strukturerade data från en databas, men i vissa fall kan icke-rektangulära data användas.

Det är bäst om dina data är relativt rena innan du importerar dem till Studio (klassisk). Till exempel vill du kunna ta hand om problem som strängar som inte är citerade.

Det finns dock moduler som är tillgängliga i Studio (klassisk) som möjliggör viss manipulering av data i experimentet när du har importerat dina data. Beroende på vilka Machine Learning-algoritmer du kommer att använda kan du behöva bestämma hur du ska hantera problem med data struktur, till exempel saknade värden och sparse-data, och det finns moduler som kan hjälpa dig med det. Titta i avsnittet **datatransformering** i modulen modul för moduler som utför dessa funktioner.

Du kan när som helst i experimentet Visa eller hämta de data som skapas av en modul genom att klicka på utdataporten. Beroende på modulen kan det finnas olika tillgängliga nedladdnings alternativ, eller så kan du visualisera data i webbläsaren i Studio (klassisk).

## <a name="supported-data-formats-and-data-types"></a>Data format och data typer som stöds

Du kan importera ett antal data typer till experimentet, beroende på vilken mekanism du använder för att importera data och var de kommer från:

* Oformaterad text (. txt)
* Kommaavgränsade värden (CSV) med ett sidhuvud (. csv) eller utan (.nh.csv)
* Tabbavgränsade värden (TSV) med ett sidhuvud (. tsv) eller utan (. NH. tsv)
* Excel-fil
* Azure-tabell
* Hive-tabell
* SQL Database-tabell
* OData-värden
* SVMLight-data (. SVMLight) (se [SVMLight-definitionen](http://svmlight.joachims.org/) för format information)
* ARFF-data (Attribute Relations fil format) (. arff) (se [arff-definitionen](https://www.cs.waikato.ac.nz/ml/weka/arff.html) för format information)
* Zip-fil (. zip)
* R-objekt eller arbetsytefil (. RData

Om du importerar data i ett format, till exempel ARFF som innehåller metadata, använder Studio (klassisk) dessa metadata för att definiera rubriken och data typen för varje kolumn.

Om du importerar data, till exempel TSV-eller CSV-format som inte innehåller dessa metadata, härleds data typen för varje kolumn genom att data samplas. Om data inte heller har kolumn rubriker, tillhandahåller Studio (klassisk) standard namn.

Du kan uttryckligen ange eller ändra rubriker och data typer för kolumner med hjälp av modulen [Redigera metadata][edit-metadata] .

Följande data typer kan identifieras av Studio (klassisk):

* Sträng
* Integer
* Double
* Boolesk
* DateTime
* TimeSpan

Studio använder en intern datatyp som kallas ***data tabell*** för att skicka data mellan moduler. Du kan uttryckligen konvertera dina data till data tabell format med hjälp av modulen [konvertera till data uppsättning][convert-to-dataset] .

Alla moduler som accepterar andra format än data tabeller kommer att konvertera data till data tabellen tyst innan de skickas till nästa modul.

Vid behov kan du konvertera data tabell format tillbaka till CSV-, TSV-, ARFF-eller SVMLight-format med andra konverterings moduler.
Titta i avsnittet **data format konverteringar** i modulen modul för moduler som utför dessa funktioner.

## <a name="data-capacities"></a>Data kapaciteter

Moduler i Machine Learning Studio (klassisk) har stöd för data uppsättningar på upp till 10 GB kompakta numeriska data för vanliga användnings fall. Om en modul hämtar indata från mer än ett ställe är värdet 10 GB summan av alla indata. Du kan prova större data uppsättningar genom att använda frågor från Hive eller Azure SQL Database, eller så kan du använda för bearbetning av antal inlärnings data innan du importerar data.  

Följande typer av data kan expanderas till större datauppsättningar under funktionsnormalisering och är begränsade till mindre än 10 GB:

* Utspridda
* Kategoriska
* Strängar
* Binära data

Följande moduler är begränsade till datauppsättningar som är mindre än 10 GB:

* Moduler för rekommenderare
* Modulen SMOTE (Synthetic Minority Oversampling Technique)
* Skriptmoduler: R, Python, SQL
* Moduler där den utgående datastorleken kan vara större än den inkommande datastorleken, till exempel kopplings- eller funktions-hashning
* Korsvalidering, hyperparametrar för justeringsmodeller, ordningstalsregression och ”en eller alla”-multiklasser, om antalet iterationer är mycket stort

För data uppsättningar som är större än ett par GB laddar du upp data till Azure Storage eller Azure SQL Database eller använder Azure HDInsight, i stället för att ladda upp direkt från en lokal fil.

Du kan hitta information om bilddata i modulen [Importera avbildningar](/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) .

## <a name="import-from-a-local-file"></a>Importera från en lokal fil

Du kan ladda upp en datafil från hård disken och använda den som utbildnings data i Studio (klassisk). När du importerar en datafil skapar du en data uppsättnings modul som är redo att användas i experiment i din arbets yta.

Om du vill importera data från en lokal hård disk gör du följande:

1. Klicka på **+ ny** längst ned i Studio-fönstret (klassisk).
2. Välj **data uppsättning** och **från lokal fil**.
3. I dialog rutan **Ladda upp en ny data uppsättning** bläddrar du till den fil som du vill ladda upp.
4. Ange ett namn, identifiera data typen och ange en beskrivning om du vill. En beskrivning rekommenderas – den gör att du kan registrera egenskaper för de data som du vill komma ihåg när du använder data i framtiden.
5. Kryss rutan **det här är den nya versionen av en befintlig data uppsättning** gör det möjligt att uppdatera en befintlig data uppsättning med nya data. Det gör du genom att klicka på den här kryss rutan och sedan ange namnet på en befintlig data uppsättning.

![Ladda upp en ny data uppsättning](./media/import-data/upload-dataset-from-local-file.png)

Uppladdnings tiden beror på storleken på dina data och anslutnings hastigheten till tjänsten. Om du vet att filen tar lång tid kan du göra andra saker i Studio (klassisk) medan du väntar. Men om du stänger webbläsaren innan data överföringen är klar orsakar överföringen att Miss lyckas.

När dina data har laddats upp lagras de i en data uppsättnings modul och är tillgängliga för alla experiment i din arbets yta.

När du redigerar ett experiment kan du hitta de data uppsättningar som du har laddat upp i listan **mina data uppsättningar** under listan **sparade data uppsättningar** i modulen modul. Du kan dra och släppa data uppsättningen till experiment arbets ytan när du vill använda data uppsättningen för ytterligare analys och maskin inlärning.

## <a name="import-from-online-data-sources"></a>Importera från data källor online

Med hjälp av modulen [Importera data][import-data] kan experimentet importera data från olika data källor online medan experimentet körs.

> [!NOTE]
> Den här artikeln innehåller allmän information om modulen [Importera data][import-data] . Mer detaljerad information om vilka typer av data som du kan komma åt, format, parametrar och svar på vanliga frågor finns i avsnittet modulreferens i modulen [Importera data][import-data] .

Med hjälp av modulen [Importera data][import-data] kan du komma åt data från en av flera data källor online medan experimentet körs:

* En webb adress med HTTP
* Hadoop med HiveQL
* Azure Blob Storage
* Azure-tabell
* Azure SQL Database. SQL-hanterad instans eller SQL Server
* En datafeed-Provider, OData för närvarande
* Azure Cosmos DB

Eftersom dessa inlärnings data används medan experimentet körs, är det bara tillgängligt i det experimentet. I jämförelse är data som har lagrats i en data uppsättnings modul tillgänglig för alla experiment i din arbets yta.

Om du vill komma åt online-datakällor i ditt Studio (klassiska) experiment lägger du till modulen [Importera data][import-data] i experimentet. Välj sedan **guiden för att starta import data** under **Egenskaper** för steg-för-steg-instruktioner för att välja och konfigurera data källan. Alternativt kan du manuellt välja **data källa** under **Egenskaper** och ange de parametrar som krävs för att få åtkomst till data.

Online-datakällor som stöds finns i tabellen nedan. Den här tabellen sammanfattar också de fil format som stöds och parametrar som används för att komma åt data.

> [!IMPORTANT]
> För närvarande kan modulen [Importera data][import-data] och [Exportera data][export-data] bara läsa och skriva data från Azure Storage som skapats med den klassiska distributions modellen. Med andra ord stöds inte den nya konto typen för Azure Blob Storage som erbjuder snabb åtkomst nivå eller låg frekvent åtkomst nivå ännu.
>
> I allmänhet bör alla Azure Storage-konton som du har skapat innan det här tjänst alternativet bli tillgängliga inte påverkas.
> Om du behöver skapa ett nytt konto väljer du **klassiskt** för distributions modellen eller använder Resource Manager och väljer **Allmänt syfte** i stället för **Blob Storage** för **konto typ**.
>
> Mer information finns i [Azure Blob Storage: frekvent och låg frekvent lagrings nivå](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Online-datakällor som stöds
Modulen Azure Machine Learning Studio (klassisk) **Importera data** stöder följande data Källor:

| Datakälla | Beskrivning | Parametrar |
| --- | --- | --- |
| Webb adress via HTTP |Läser data i kommaavgränsade värden (CSV), Tabbavgränsade värden (TSV), attribut-ARFF (File-relation File Format) och stöder Vector Machines (SVM-Light)-format från alla webb adresser som använder HTTP |<b>URL</b>: anger det fullständiga namnet på filen, inklusive webbplatsens URL och fil namnet, med valfritt tillägg. <br/><br/><b>Data format</b>: anger ett av de data format som stöds: CSV, TSV, arff eller SVM-Light. Om data har en rubrik rad används den för att tilldela kolumn namn. |
| Hadoop/HDFS |Läser data från distribuerad lagring i Hadoop. Du anger de data du vill använda med HiveQL, ett SQL-liknande frågespråk. HiveQL kan också användas för att aggregera data och utföra data filtrering innan du lägger till data i Studio (klassisk). |<b>Hive-databas fråga</b>: anger den Hive-fråga som används för att generera data.<br/><br/><b>HCatalog-Server-URI</b> : Ange namnet på klustret med formatet *&lt; kluster namn &gt; . azurehdinsight.net.*<br/><br/><b>Hadoop-användar konto namn</b>: anger det Hadoop-användarnamn som används för att etablera klustret.<br/><br/><b>Hadoop-kontots lösen ord</b> : anger de autentiseringsuppgifter som används för att konfigurera klustret. Mer information finns i [skapa Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).<br/><br/><b>Plats för utgående data</b>: anger om data lagras i ett Hadoop-distribuerat fil system (HDFS) eller i Azure. <br/><ul>Om du lagrar utdata i HDFS anger du Server-URI för HDFS. (Se till att använda HDInsight-klustrets namn utan HTTPS://-prefixet). <br/><br/>Om du lagrar dina utdata i Azure måste du ange namnet på Azure Storage-kontot, lagrings åtkomst nyckeln och namnet på lagrings behållaren.</ul> |
| SQL-databas |Läser data som lagras i Azure SQL Database, SQL-hanterad instans eller i en SQL Server databas som körs på en virtuell Azure-dator. |<b>Databas server namn</b>: anger namnet på den server där databasen körs.<br/><ul>Vid Azure SQL Database anger du det Server namn som genereras. Vanligt vis har den formatet *&lt; generated_identifier &gt; . Database.Windows.net.* <br/><br/>Om det finns en SQL Server på en virtuell Azure-dator anger du *TCP: &lt; virtuell dator DNS-namn &gt; , 1433*</ul><br/><b>Databas namn </b>: anger namnet på databasen på servern. <br/><br/><b>Serverns användar konto namn</b>: anger ett användar namn för ett konto som har åtkomst behörighet för databasen. <br/><br/><b>Lösen ord för serverns användar konto</b>: anger lösen ordet för användar kontot.<br/><br/><b>Databas fråga</b>: Ange en SQL-instruktion som beskriver de data som du vill läsa. |
| Lokal SQL-databas |Läser data som lagras i en SQL-databas. |<b>Datagateway</b>: anger namnet på den data Management Gateway som är installerad på en dator där den kan komma åt din SQL Server-databas. Information om hur du konfigurerar gatewayen finns i [utföra avancerad analys med Azure Machine Learning Studio (klassisk) med hjälp av data från en SQL-Server](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Databas server namn</b>: anger namnet på den server där databasen körs.<br/><br/><b>Databas namn </b>: anger namnet på databasen på servern. <br/><br/><b>Serverns användar konto namn</b>: anger ett användar namn för ett konto som har åtkomst behörighet för databasen. <br/><br/><b>Användar namn och lösen ord</b>: Klicka på <b>Ange värden</b> för att ange dina autentiseringsuppgifter för databasen. Du kan använda Windows-integrerad autentisering eller SQL Server autentisering beroende på hur din SQL Server har kon figurer ATS.<br/><br/><b>Databas fråga</b>: Ange en SQL-instruktion som beskriver de data som du vill läsa. |
| Azure-tabell |Läser data från Table service i Azure Storage.<br/><br/>Om du läser stora mängder data som inte används ofta använder du Azure Table service. Det ger en flexibel, icke-relationell (NoSQL), massivt skalbar, billig och hög tillgänglig lagrings lösning. |Alternativen i **Importera data** ändras beroende på om du kommer åt offentlig information eller ett privat lagrings konto som kräver inloggnings uppgifter. Detta bestäms av <b>autentiseringstypen</b> som kan ha värdet "PublicOrSAS" eller "Account", som var och en har en egen uppsättning parametrar. <br/><br/><b>Offentlig eller signatur för delad åtkomst (SAS)</b>: parametrarna är:<br/><br/><ul><b>Tabell-URI</b>: anger den offentliga eller SAS-URL: en för tabellen.<br/><br/><b>Anger vilka rader som ska genomsökas efter egenskaps namn</b>: värdena är <i>TopN</i> för att skanna det angivna antalet rader eller <i>ScanAll</i> för att hämta alla rader i tabellen. <br/><br/>Om data är homogena och förutsägbara rekommenderar vi att du väljer *TopN* och anger ett tal för N. För stora tabeller kan detta resultera i snabbare läsnings tider.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som varierar beroende på tabellens djup och placering väljer du alternativet *ScanAll* för att söka igenom alla rader. Detta säkerställer integriteten hos den resulterande egenskapen och metadata-konverteringen.<br/><br/></ul><b>Privat lagrings konto</b>: parametrarna är: <br/><br/><ul><b>Konto namn</b>: anger namnet på det konto som innehåller tabellen som ska läsas.<br/><br/><b>Konto nyckel</b>: anger den lagrings nyckel som är kopplad till kontot.<br/><br/><b>Tabell namn</b> : anger namnet på den tabell som innehåller de data som ska läsas.<br/><br/><b>Rader som ska genomsökas efter egenskaps namn</b>: värdena är <i>TopN</i> för att skanna det angivna antalet rader eller <i>ScanAll</i> för att hämta alla rader i tabellen.<br/><br/>Om data är homogena och förutsägbara rekommenderar vi att du väljer *TopN* och anger ett tal för N. För stora tabeller kan detta resultera i snabbare läsnings tider.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som varierar beroende på tabellens djup och placering väljer du alternativet *ScanAll* för att söka igenom alla rader. Detta säkerställer integriteten hos den resulterande egenskapen och metadata-konverteringen.<br/><br/> |
| Azure Blob Storage |Läser data som lagras i Blob Service i Azure Storage, inklusive bilder, ostrukturerad text eller binära data.<br/><br/>Du kan använda Blob Service för att visa data offentligt eller för att lagra program data privat. Du kan komma åt dina data var som helst genom att använda HTTP-eller HTTPS-anslutningar. |Alternativen i modulen **Importera data** varierar beroende på om du kommer åt offentlig information eller ett privat lagrings konto som kräver inloggnings uppgifter. Detta bestäms av autentiseringstypen som kan ha ett värde av <b>typen</b> "PublicOrSAS" eller "Account".<br/><br/><b>Offentlig eller signatur för delad åtkomst (SAS)</b>: parametrarna är:<br/><br/><ul><b>URI</b>: anger den offentliga eller SAS-URL: en för Storage-blobben.<br/><br/><b>Fil format</b>: anger formatet för data i BLOB service. De format som stöds är CSV, TSV och ARFF.<br/><br/></ul><b>Privat lagrings konto</b>: parametrarna är: <br/><br/><ul><b>Konto namn</b>: anger namnet på det konto som innehåller den blob som du vill läsa.<br/><br/><b>Konto nyckel</b>: anger den lagrings nyckel som är kopplad till kontot.<br/><br/><b>Sökväg till behållare, katalog eller BLOB </b> : anger namnet på blobben som innehåller de data som ska läsas.<br/><br/><b>BLOB-fil format</b>: anger formatet för data i Blob-tjänsten. De data format som stöds är CSV, TSV, ARFF, CSV med en angiven kodning och Excel. <br/><br/><ul>Om formatet är CSV eller TSV, måste du ange om filen innehåller en rubrik rad.<br/><br/>Du kan använda Excel-alternativet för att läsa data från Excel-arbetsböcker. I <i>Excel data format</i> -alternativet anger du om data finns i ett kalkyl blads område i Excel eller i en Excel-tabell. I alternativet <i>Excel-blad eller inbäddad tabell </i>anger du namnet på bladet eller tabellen som du vill läsa från.</ul><br/> |
| Datafeed-Provider |Läser data från en feed som stöds. För närvarande stöds endast OData-formatet (Open data Protocol). |<b>Data innehålls typ</b>: anger OData-formatet.<br/><br/><b>Käll-URL</b>: anger den fullständiga URL: en för datafeeden. <br/>Till exempel läser följande URL från exempel databasen Northwind: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importera från ett annat experiment

Det kommer att finnas tillfällen när du vill göra ett mellanliggande resultat från ett experiment och använda det som en del av ett annat experiment. Det gör du genom att spara modulen som en data uppsättning:

1. Klicka på utdata från den modul som du vill spara som en data uppsättning.
2. Klicka på **Spara som data uppsättning**.
3. När du uppmanas till det anger du ett namn och en beskrivning som gör det enkelt att identifiera data uppsättningen.
4. Klicka på **bock markeringen** .

När Spara-funktionen är klar är data uppsättningen tillgänglig för användning i alla experiment på arbets ytan. Du hittar den i listan med **sparade data uppsättningar** i modulen modul.

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure Machine Learning Studio webb tjänster som använder moduler för data import och data export](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[export-data]: /azure/machine-learning/studio-module-reference/export-data


<!-- Module References -->
[convert-to-dataset]: /azure/machine-learning/studio-module-reference/convert-to-dataset
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[import-data]: /azure/machine-learning/studio-module-reference/import-data