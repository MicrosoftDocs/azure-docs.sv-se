---
title: Utforska data och modeller i Windows
titleSuffix: Azure Data Science Virtual Machine
description: Utföra data utforsknings-och modellerings uppgifter på Windows-Data Science Virtual Machine.
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: c44c40f2ddab53cf0beb5ecd48127d126fdb4333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101657408"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Data vetenskap med en Windows-Data Science Virtual Machine

Windows Data Science Virtual Machine (DSVM) är en kraftfull utvecklings miljö för data vetenskap där du kan utföra data utforsknings-och modellerings uppgifter. Miljön har redan byggts och paketerats med flera populära verktyg för data analys som gör det enkelt att komma igång med din analys för lokala, molnbaserade eller hybrid distributioner. 

DSVM fungerar nära Azure-tjänster. Den kan läsa och bearbeta data som redan är lagrade på Azure, i Azure Synapse (tidigare SQL DW), Azure Data Lake, Azure Storage eller Azure Cosmos DB. Det kan också dra nytta av andra analys verktyg, till exempel Azure Machine Learning.

I den här artikeln får du lära dig hur du använder din DSVM för att utföra data vetenskaps uppgifter och interagera med andra Azure-tjänster. Här följer några av de saker du kan göra på DSVM:

- Använd en Jupyter Notebook för att experimentera med dina data i en webbläsare med hjälp av python 2, python 3 och Microsoft R. (Microsoft R är en företags färdig version av R utformad för prestanda.)
- Utforska data och utveckla modeller lokalt på DSVM med hjälp av Microsoft Machine Learning Server och python.
- Administrera dina Azure-resurser med hjälp av Azure Portal eller PowerShell.
- Utöka ditt lagrings utrymme och dela storskaliga data uppsättningar/kod i hela teamet genom att skapa en Azure Files resurs som en monterad enhet på din DSVM.
- Dela kod med ditt team genom att använda GitHub. Få åtkomst till lagrings platsen med hjälp av de förinstallerade git-klienterna: git bash och git GUI.
- Få åtkomst till Azure data-och analys tjänster som Azure Blob Storage, Azure Cosmos DB, Azure Synapse (tidigare SQL DW) och Azure SQL Database.
- Bygg rapporter och en instrument panel med hjälp av Power BI Desktop instansen som är förinstallerad på DSVM och distribuera dem i molnet.

- Installera ytterligare verktyg på den virtuella datorn.   

> [!NOTE]
> Ytterligare användnings kostnader gäller för många av tjänsterna för data lagring och analys som anges i den här artikeln. Mer information finns på sidan med [priser för Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Förutsättningar

* Du behöver en Azure-prenumeration. Du kan [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/free/).
* Instruktioner för hur du konfigurerar en Data Science Virtual Machine på Azure Portal finns i [skapa en virtuell dator](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Använda Jupyter Notebooks
Jupyter Notebook tillhandahåller en webbläsarbaserad IDE för data utforskning och modellering. Du kan använda python 2, python 3 eller R (både öppen källkod och Microsoft R Server) i en Jupyter Notebook.

Om du vill starta Jupyter Notebook väljer du ikonen **Jupyter Notebook** på **Start** -menyn eller på Skriv bordet. I kommando tolken DSVM kan du också köra kommandot ```jupyter notebook``` från den katalog där du har befintliga antecknings böcker eller där du vill skapa nya antecknings böcker.  

När du har startat Jupyter navigerar du till `/notebooks` katalogen till exempel antecknings böcker som förpackas i DSVM. Nu kan du:

* Välj den bärbara datorn om du vill se koden.
* Kör varje cell genom att välja SKIFT + RETUR.
* Kör hela antecknings boken genom att välja **cell**  >  **körning**.
* Skapa en ny antecknings bok genom att välja ikonen Jupyter (övre vänstra hörnet), klicka på knappen **nytt** till höger och sedan välja språket för antecknings boken (även kallat kernels).   

> [!NOTE]
> För närvarande stöds python 2,7, python 3,6, R, Julia och PySpark-kerneler i Jupyter. R-kärnan stöder programmering i både öppen källkod R och Microsoft R.   
> 
> 

När du är i antecknings boken kan du utforska dina data, bygga modellen och testa modellen med hjälp av ditt val av bibliotek.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Utforska data och utveckla modeller med Microsoft Machine Learning Server
Du kan använda språk som R och python för att utföra data analyser direkt på DSVM.

För R kan du använda en IDE-liknande RStudio som du hittar på Start-menyn eller på Skriv bordet. Du kan också använda R-verktyg för Visual Studio. Microsoft har tillhandahållit ytterligare bibliotek ovanpå CRAN R med öppen källkod för att möjliggöra skalbar analys och möjlighet att analysera data som är större än den minnes storlek som tillåts i parallell segment analys. 

För python kan du använda en IDE som Visual Studio Community Edition, som har tillägget Python Tools for Visual Studio (PTVS) förinstallerat. Som standard konfigureras bara python 3,6, rot Conda-miljön på PTVS. Gör så här för att aktivera Anaconda python 2,7:

1. Skapa anpassade miljöer för varje version genom att gå till **verktyg**  >  **python-verktyg**  >  **python-miljöer** och sedan välja **+ anpassad** i Visual Studio Community Edition.
1. Ange en beskrivning och ange sökvägen till miljöprefixet som **c:\anaconda\envs\python2** för anaconda python 2,7.
1. Välj **Använd automatisk identifiering**  >   för att spara miljön.

Mer information om hur du skapar python-miljöer finns i [PTVS-dokumentationen](/visualstudio/python/) .

Nu har du ställt in för att skapa ett nytt python-projekt. Gå till **filen**  >  **nytt**  >  **projekt**  >  **python** och välj den typ av python-program som du skapar. Du kan ställa in python-miljön för det aktuella projektet till önskad version (python 2,7 eller 3,6) genom att högerklicka på **python-miljöer** och sedan välja **Lägg till/ta bort python-miljöer**. Du hittar mer information om hur du arbetar med PTVS i [produkt dokumentationen](/visualstudio/python/).



## <a name="manage-azure-resources"></a>Hantera Azure-resurser
DSVM låter dig inte bara bygga analys lösningen lokalt på den virtuella datorn. Du kan också få åtkomst till tjänster på Azures moln plattform. Azure tillhandahåller flera beräknings-, lagrings-, data analys-och andra tjänster som du kan administrera och få åtkomst till från din DSVM.

För att administrera din Azure-prenumeration och moln resurser har du två alternativ:
+ Använd webbläsaren och gå till [Azure Portal](https://portal.azure.com).

+ Använd PowerShell-skript. Kör Azure PowerShell från en genväg på Skriv bordet eller från **Start** -menyn. Se [Microsoft Azure PowerShell-dokumentationen](../../azure-resource-manager/management/manage-resources-powershell.md) för fullständig information. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Utöka lagring med hjälp av delade fil system
Data forskare kan dela stora data uppsättningar, kod eller andra resurser i teamet. DSVM har cirka 45 GB ledigt utrymme. Om du vill utöka lagringen kan du använda Azure Files och antingen montera den på en eller flera DSVM-instanser eller komma åt den via en REST API. Du kan också använda [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) eller använda [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) för att lägga till extra dedikerade data diskar. 

> [!NOTE]
> Det maximala utrymmet på den Azure Files resursen är 5 TB. Storleks gränsen för varje fil är 1 TB. 

Du kan använda det här skriptet i Azure PowerShell för att skapa en Azure Files-resurs:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Nu när du har skapat en Azure Files-resurs kan du montera den på en virtuell dator i Azure. Vi rekommenderar att du sätter den virtuella datorn i samma Azure-datacenter som lagrings kontot, för att undvika svars tid och data överförings kostnader. Följande är de Azure PowerShell-kommandon som används för att montera enheten på DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Nu kan du komma åt den här enheten som en normal enhet på den virtuella datorn.

## <a name="share-code-in-github"></a>Dela kod i GitHub
GitHub är en kod lagrings plats där du kan hitta kod exempel och källor för olika verktyg med hjälp av tekniker som delas av Developer community. Den använder git som teknik för att spåra och lagra versioner av filerna. GitHub är också en plattform där du kan skapa din egen lagrings plats för att lagra teamets delade kod och dokumentation, implementera versions kontroll och kontrol lera vem som har åtkomst till att visa och bidra med kod. 

Besök [GitHub-hjälp sidorna](https://help.github.com/) om du vill ha mer information om hur du använder git. Du kan använda GitHub som ett av de olika sätten att samar beta med ditt team, använda kod som har utvecklats av communityn och sedan koppla kod till communityn.

DSVM har lästs in med klient verktyg på kommando raden och i det grafiska användar gränssnittet för att komma åt GitHub-lagringsplatsen. Kommando rads verktyget som fungerar med git och GitHub kallas git bash. Visual Studio är installerat på DSVM och har git-tillägg. Du kan hitta ikoner för dessa verktyg på **Start** -menyn och på Skriv bordet.

Om du vill ladda ned kod från en GitHub-lagringsplats använder du ```git clone``` kommandot. Om du till exempel vill hämta data vetenskaps databasen som publicerats av Microsoft i den aktuella katalogen kan du köra följande kommando i git bash:

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

I Visual Studio kan du utföra samma klonings åtgärd. Följande skärm bild visar hur du kommer åt git-och GitHub-verktyg i Visual Studio:

![Skärm bild av Visual Studio med GitHub-anslutningen visas](./media/vm-do-ten-things/VSGit.png)

Du hittar mer information om hur du använder Git för att arbeta med din GitHub-lagringsplats från resurser som finns på github.com. [Lathund-bladet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) är en användbar referens.

## <a name="access-azure-data-and-analytics-services"></a>Få åtkomst till Azure data-och analys tjänster
### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob Storage är en tillförlitlig, ekonomisk moln lagrings tjänst för data som är stora och små. I det här avsnittet beskrivs hur du kan flytta data till Blob Storage och komma åt data som lagras i en Azure-blob.

#### <a name="prerequisites"></a>Förutsättningar

* Skapa ditt Azure Blob Storage-konto från [Azure Portal](https://portal.azure.com).

   ![Skärm bild av processen för att skapa lagrings konton i Azure Portal](./media/vm-do-ten-things/create-azure-blob.png)

* Bekräfta att kommando rads verktyget AzCopy är förinstallerat: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . Katalogen som innehåller azcopy.exe finns redan i miljövariabeln PATH, så du kan undvika att skriva den fullständiga kommando Sök vägen när du kör det här verktyget. Mer information om AzCopy-verktyget finns i AzCopy- [dokumentationen](../../storage/common/storage-use-azcopy-v10.md).
* Starta Azure Storage Explorer-verktyget. Du kan ladda ned den från  [webb sidan Storage Explorer](https://storageexplorer.com/). 

   ![Skärm bild av Azure Storage Explorer åtkomst till ett lagrings konto](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Flytta data från en virtuell dator till en Azure-Blob: AzCopy

Om du vill flytta data mellan dina lokala filer och Blob Storage kan du använda AzCopy på kommando raden eller i PowerShell:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

Ersätt **C:\MyFolder** med sökvägen till den plats där filen lagras, **Mystorageaccount** med ditt Blob Storage- **kontonamn,** behållar namnet och **lagrings konto nyckeln** med din åtkomst nyckel för Blob Storage. Du kan hitta autentiseringsuppgifterna för ditt lagrings konto i [Azure Portal](https://portal.azure.com).

Kör kommandot AzCopy i PowerShell eller från en kommando tolk. Här är några exempel på användningen av AzCopy-kommandot:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

När du har kört AzCopy-kommandot för att kopiera till en Azure-Blob visas filen i Azure Storage Explorer.

![Skärm bild av lagrings kontot som visar den överförda CSV-filen](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Flytta data från en virtuell dator till en Azure-Blob: Azure Storage Explorer

Du kan också ladda upp data från den lokala filen på den virtuella datorn med hjälp av Azure Storage Explorer:

* Om du vill överföra data till en behållare väljer du mål behållaren och klickar på knappen **överför** . ![ Skärm bild av knappen upload i Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Välj ellipsen (**...**) till höger om rutan **filer** , Välj en eller flera filer som ska överföras från fil systemet och välj **Ladda upp** för att påbörja överföring av filerna. ![ Skärm bild av dialog rutan Ladda upp filer](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Läsa data från en Azure-Blob: python ODBC

Du kan använda BlobService-biblioteket för att läsa data direkt från en BLOB i en Jupyter Notebook eller i ett python-program.

Importera först de nödvändiga paketen:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Anslut sedan dina autentiseringsuppgifter för Blob Storage-kontot och Läs data från bloben:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Data läses som en data ram:

![Skärm bild av de första 10 raderna med data](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-and-databases"></a>Azure Synapse Analytics och databaser
Azure Synapse Analytics är ett elastiskt informations lager som en tjänst med en SQL Server upplevelse i företags klass.

Du kan etablera Azure Synapse Analytics genom att följa anvisningarna i [den här artikeln](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md). När du har etablerat Azure Synapse Analytics kan du använda [den här genom gången](../team-data-science-process/sqldw-walkthrough.md) för att utföra data uppladdning, utforskning och modellering med hjälp av data i Azure Synapse Analytics.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB är en NoSQL-databas i molnet. Du kan använda den för att arbeta med dokument som JSON och för att lagra och skicka frågor till dokumenten.

Använd följande nödvändiga steg för att komma åt Azure Cosmos DB från DSVM:

1. Azure Cosmos DB python SDK har redan installerats på DSVM. Kör ```pip install pydocumentdb --upgrade``` från en kommando tolk för att uppdatera den.
2. Skapa ett Azure Cosmos DB konto och en databas från [Azure Portal](https://portal.azure.com).
3. Hämta verktyget Azure Cosmos DB datamigrering från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) och extrahera till en valfri katalog.
4. Importera JSON-data (Volcano-data) som lagras i en [offentlig BLOB](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) till Azure Cosmos dB med följande kommando parametrar till migreringsverktyget. (Använd dtui.exe från den katalog där du installerade verktyget Azure Cosmos DB datamigrering.) Ange käll-och mål platsen med följande parametrar:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

När du har importerat data kan du gå till Jupyter och öppna antecknings boken med namnet *DocumentDBSample*. Den innehåller python-kod för att komma åt Azure Cosmos DB och göra några grundläggande frågor. Du kan lära dig mer om Azure Cosmos DB genom att gå till tjänstens [dokumentations sida](../../cosmos-db/index.yml).

## <a name="use-power-bi-reports-and-dashboards"></a>Använda Power BI rapporter och instrument paneler 
Du kan visualisera Volcano JSON-filen från föregående Azure Cosmos DB exempel i Power BI Desktop för att få visuella insikter om data. Detaljerade anvisningar finns i [Power BI artikeln](../../cosmos-db/powerbi-visualize.md). Här följer de övergripande stegen:

1. Öppna Power BI Desktop och välj **Hämta data**. Ange URL: en: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Du bör se vilka JSON-poster som importeras som en lista. Konvertera listan till en tabell så Power BI kan arbeta med den.
4. Expandera kolumnerna genom att välja ikonen Expandera (pil).
5. Observera att platsen är ett **post** fält. Expandera posten och välj bara koordinaterna. **Koordinaten** är en List kolumn.
6. Lägg till en ny kolumn för att konvertera List koordinatens kolumn till en kommaavgränsad **LatLong** -kolumn. Sammanfoga de två elementen i koordinatens List fält med hjälp av formeln ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
7. Konvertera kolumnen **höjning** till decimal och välj knapparna **Stäng** och **tillämpa** .

I stället för föregående steg kan du klistra in följande kod. Den skriver ut stegen som används i Avancerad redigerare i Power BI för att skriva data transformationer i ett frågespråk.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Nu har du data i din Power BI data modell. Din Power BI Desktop instans bör se ut så här:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Du kan börja skapa rapporter och visualiseringar med hjälp av data modellen. Du kan följa stegen i [den här Power BI artikeln](../../cosmos-db/powerbi-visualize.md#build-the-reports) för att bygga en rapport.

## <a name="scale-the-dsvm-dynamically"></a>Skala DSVM dynamiskt 
Du kan skala upp och ned DSVM för att uppfylla projektets behov. Om du inte behöver använda den virtuella datorn på kvällen eller på helger kan du stänga av den virtuella datorn från [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Du ådrar dig beräknings avgifter om du bara använder knappen Stäng för operativ systemet på den virtuella datorn. I stället bör du frigöra din DSVM med hjälp av Azure Portal eller Cloud Shell.
> 
> 

Du kan behöva hantera viss storskalig analys och behöver mer processor, minne eller disk kapacitet. I så fall kan du hitta olika VM-storlekar i förhållande till processor kärnor, GPU-baserade instanser för djup inlärning, minnes kapacitet och disk typer (inklusive solid state-hårddiskar) som uppfyller dina beräknings-och budget behov. Den fullständiga listan över virtuella datorer, tillsammans med deras beräknings priser per timme, finns på sidan med [priser för Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) .


## <a name="add-more-tools"></a>Lägg till fler verktyg
Verktyg som är inbyggda i DSVM kan hantera många vanliga data analys behov. Detta sparar tid eftersom du inte behöver installera och konfigurera dina miljöer en i taget. Det sparar också pengar, eftersom du betalar för endast resurser som du använder.

Du kan använda andra Azure data-och Analytics-tjänster som är profilerade i den här artikeln för att förbättra din analys miljö. I vissa fall kan du behöva ytterligare verktyg, inklusive vissa patentskyddade partner verktyg. Du har fullständig administrativ åtkomst på den virtuella datorn för att installera nya verktyg som du behöver. Du kan också installera ytterligare paket i python och R som inte är förinstallerade. För python kan du använda antingen ```conda``` eller ```pip``` . För r kan du använda ```install.packages()``` i r-konsolen eller använda IDE och välja **paket**  >  **installera paket**.

## <a name="deep-learning"></a>Deep learning

Förutom de ramverkbaserade exemplen kan du få en uppsättning omfattande genom gångar som har verifierats på DSVM. Med de här genom gången kan du komma igång med att utveckla djup inlärnings program i domäner som bild och text/språk förståelse.   


- [Köra neurala-nätverk i olika ramverk](https://github.com/ilkarman/DeepLearningFrameworks): den här genom gången visar hur du migrerar kod från ett ramverk till ett annat. Det visar också hur du jämför modeller och körnings prestanda i ramverk. 

- [En instruktions guide för att bygga en komplett lösning för att identifiera produkter i bilder](https://github.com/Azure/cortana-intelligence-product-detection-from-images): bild identifiering är en teknik som kan användas för att hitta och klassificera objekt i bilder. Den här tekniken har möjlighet att få enorma förmåner i många affärs domäner i real tid. Åter försäljare kan till exempel använda den här metoden för att avgöra vilken produkt en kund har hämtat från hyllan. Den här informationen i sin tur hjälper butiker att hantera produkt inventering. 

- [Djup inlärning för ljud](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): den här självstudien visar hur du tränar en djup inlärnings modell för att upptäcka ljud händelser i [data uppsättningen för urbana ljud](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Den innehåller också en översikt över hur du arbetar med ljuddata.

- [Klassificering av text dokument](https://github.com/anargyri/lstm_han): den här genom gången visar hur du skapar och tränar två neurala-nätverks arkitekturer: hierarkiskt Attention-nätverk och LSTM-nätverk. Dessa neurala-nätverk använder keras-API: et för djup inlärning för att klassificera text dokument. 

## <a name="summary"></a>Sammanfattning
I den här artikeln beskrivs några av de saker du kan göra på Microsoft Data Science Virtual Machine. Det finns många fler saker du kan göra för att göra DSVM till en effektiv analys miljö.
