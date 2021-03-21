---
title: Få åtkomst till data uppsättningar med python-klient bibliotek – team data science process
description: Installera och Använd python-klient biblioteket för att komma åt och hantera Azure Machine Learning data på ett säkert sätt från en lokal python-miljö.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 497b8f5598cf7aa7720f47863d465f5e29789b07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321949"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Åtkomst till datauppsättningar med Python med hjälp av Python-klientbiblioteket i Azure Machine Learning
Förhands granskningen av Microsoft Azure Machine Learning python-klientprogrammet kan ge säker åtkomst till dina Azure Machine Learning data uppsättningar från en lokal python-miljö och möjliggör skapande och hantering av data uppsättningar i en arbets yta.

Det här avsnittet innehåller anvisningar om hur du:

* Installera Machine Learning python-klient biblioteket
* få åtkomst till och ladda upp data uppsättningar, inklusive instruktioner om hur du får åtkomst till Azure Machine Learning data uppsättningar från din lokala python-miljö
* få åtkomst till mellanliggande data uppsättningar från experiment
* Använd python-klient biblioteket för att räkna upp data uppsättningar, få åtkomst till metadata, läsa innehållet i en data uppsättning, skapa nya data uppsättningar och uppdatera befintliga data uppsättningar

## <a name="prerequisites"></a><a name="prerequisites"></a>Förutsättningar
Python-klient biblioteket har testats i följande miljöer:

* Windows, Mac och Linux
* Python 2,7, 3,3 och 3,4

Det har ett beroende av följande paket:

* autentiseringsbegäran
* python – dateutil
* Pandas

Vi rekommenderar att du använder en python-distribution som [Anaconda](https://www.anaconda.com/) eller [Canopy](https://store.enthought.com/downloads/), som medföljer python, ipython och de tre paket som anges ovan installerade. Även om IPython inte är absolut nödvändigt är det en bra miljö för att manipulera och visualisera data interaktivt.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Så här installerar du Azure Machine Learning python-klient biblioteket
Installera Azure Machine Learning python-klient biblioteket för att slutföra de aktiviteter som beskrivs i det här avsnittet. Det här biblioteket är tillgängligt från [python-paketets index](https://pypi.python.org/pypi/azureml). Om du vill installera den i python-miljön kör du följande kommando från din lokala python-miljö:

```console
pip install azureml
```

Du kan också hämta och installera från källorna på [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

```console
python setup.py install
```

Om du har git installerat på datorn kan du använda PIP för att installera direkt från git-lagringsplatsen:

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Använd kodfragment för att komma åt data uppsättningar
Med python-klient biblioteket får du program mässig åtkomst till dina befintliga data uppsättningar från experiment som har körts.

Från Azure Machine Learning Studio-webbgränssnittet (klassisk) kan du generera kodfragment som innehåller all nödvändig information för att ladda ned och deserialisera data uppsättningar som Pandas DataFrame-objekt på den lokala datorn.

### <a name="security-for-data-access"></a><a name="security"></a>Säkerhet för data åtkomst
Kodfragmenten som tillhandahålls av Azure Machine Learning Studio (klassisk) för användning med python-klient biblioteket innehåller ditt arbetsyte-ID och autentiseringstoken. Dessa ger fullständig åtkomst till din arbets yta och måste skyddas, till exempel ett lösen ord.

Av säkerhets skäl är funktionen för kodfragment bara tillgänglig för användare som har rollen som **ägare** till arbets ytan. Din roll visas i Azure Machine Learning Studio (klassisk) på sidan **användare** under **Inställningar**.

![Skärm bilden visar inställningarna på sidan användare i Azure Machine Learning Studio.][security]

Om rollen inte har angetts som **ägare** kan du antingen begära att bli inbjuden till en ägare eller be ägaren av arbets ytan att förse dig med kodfragmentet.

Du kan välja något av följande alternativ för att hämta autentiseringstoken:

* Fråga efter en token från en ägare. Ägare kan komma åt sina autentiseringstoken från inställnings sidan för deras arbets yta i Azure Machine Learning Studio (klassisk). Välj **Inställningar** i den vänstra rutan och klicka på **autentiseringstoken för att** se de primära och sekundära tokens. Även om antingen primär-eller sekundär token-token kan användas i kodfragmentet, rekommenderar vi att ägarna bara delar de sekundära tokens.

   ![Autentiseringstoken](./media/python-data-access/ml-python-access-settings-tokens.png)

* Be att bli befordrad till ägarens roll: en aktuell ägare av arbets ytan måste först ta bort dig från arbets ytan och sedan bjuda in dig till den som ägare.

När utvecklare har fått arbets ytans ID och autentiseringstoken kan de komma åt arbets ytan med hjälp av kodfragmentet oavsett deras roll.

Tokens hanteras på sidan **AUTHORIZATION tokens** under **Inställningar**. Du kan återskapa dem, men den här proceduren återkallar åtkomsten till föregående token.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Få åtkomst till data uppsättningar från ett lokalt python-program
1. I Machine Learning Studio (klassisk) klickar du på **data uppsättningar** i navigerings fältet till vänster.
2. Välj den data uppsättning som du vill ha åtkomst till. Du kan välja någon av data uppsättningarna från listan **mina data uppsättningar** eller från listan **samples** .
3. Klicka på **generera data åtkomst kod** i det nedre verktygsfältet. Om data har ett format som inte är kompatibelt med python-klient biblioteket är den här knappen inaktive rad.
   
    ![Skärm bild som visar data uppsättningar med koden generera DATA åtkomst.][datasets]
4. Välj kodfragmentet i fönstret som visas och kopiera det till Urklipp.
   
    ![Knappen generera kod för data åtkomst][dataset-access-code]
5. Klistra in koden i den bärbara datorn i ditt lokala python-program.
   
    ![Klistra in kod i antecknings boken][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Få åtkomst till mellanliggande data uppsättningar från Machine Learning experiment
När ett experiment körs i Machine Learning Studio (klassisk) är det möjligt att komma åt mellanliggande data uppsättningar från utdata-noderna i moduler. Mellanliggande data uppsättningar är data som har skapats och använts för mellanliggande steg när ett modell verktyg har körts.

Mellanliggande data uppsättningar kan nås så länge data formatet är kompatibelt med python-klient biblioteket.

Följande format stöds (konstanter för dessa format finns i `azureml.DataTypeIds` klassen):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Du kan bestämma formatet genom att hovra över nodens utdata. Den visas tillsammans med nodnamnet i en knapp beskrivning.

Några av modulerna, t. ex. modulen [Split][split] , utdata till ett format med namnet `Dataset` , som inte stöds av python-klient biblioteket.

![Data uppsättnings format][dataset-format]

Du måste använda en Conversion-modul, till exempel [konvertera till CSV][convert-to-csv], för att få utdata i ett format som stöds.

![GenericCSV-format][csv-format]

Följande steg visar ett exempel som skapar ett experiment, kör det och får åtkomst till mellanliggande data uppsättning.

1. Skapa ett nytt experiment.
2. Infoga en **data uppsättnings modul för binärdata med vuxen räknings inkomst** .
3. Infoga en [delad][split] modul och Anslut dess indata till utdata för datauppsättnings modulen.
4. Infoga en [Convert to CSV][convert-to-csv] -modul och Anslut dess indata till någon av utdata för [delade][split] moduler.
5. Spara experimentet, kör det och vänta tills jobbet har slutförts.
6. Klicka på noden utdata i modulen [konvertera till CSV][convert-to-csv] .
7. När snabb menyn visas väljer du **generera kod för data åtkomst**.
   
    ![Snabbmeny][experiment]
8. Välj kodfragmentet och kopiera det till Urklipp från fönstret som visas.
   
    ![Generera åtkomst kod från snabb menyn][intermediate-dataset-access-code]
9. Klistra in koden i din bärbara dator.
   
    ![Klistra in kod i Notebook][ipython-intermediate-dataset]
10. Du kan visualisera data med matplotlib. Detta visas i ett histogram för kolumnen ålder:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Använd Machine Learning python-klient biblioteket för att få åtkomst till, läsa, skapa och hantera data uppsättningar
### <a name="workspace"></a>Arbetsyta
Arbets ytan är start punkten för python-klient biblioteket. Ange `Workspace` en klass med arbetsyte-ID och autentiseringstoken för att skapa en instans:

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>Räkna upp data uppsättningar
Räkna upp alla data uppsättningar på en specifik arbets yta:

```python
for ds in ws.datasets:
    print(ds.name)
```

Räkna upp enbart skapade data uppsättningar:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

Räkna upp bara exempel data uppsättningar:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

Du kan komma åt en data uppsättning efter namn (vilket är Skift läges känsligt):

```python
ds = ws.datasets['my dataset name']
```

Eller så kan du komma åt det genom att indexera:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>Metadata
Data uppsättningar har metadata, förutom innehåll. (Mellanliggande data uppsättningar är ett undantag till den här regeln och har inga metadata.)

Vissa metadata-värden tilldelas av användaren vid skapande tillfället:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Andra värden tilldelas av Azure ML:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

Se `SourceDataset` klassen för mer information om tillgängliga metadata.

### <a name="read-contents"></a>Läsa innehåll
Kodfragmenten som tillhandahålls av Machine Learning Studio (klassisk) hämtar och deserialiserar data uppsättningen automatiskt till ett Pandas DataFrame-objekt. Detta görs med `to_dataframe` metoden:

```python
frame = ds.to_dataframe()
```

Om du hellre vill ladda ned rå data och utföra deserialiseringen själv, är det ett alternativ. För närvarande är detta det enda alternativet för format som "ARFF", vilket python-klient biblioteket inte kan deserialisera.

Så här läser du innehållet som text:

```python
text_data = ds.read_as_text()
```

Så här läser du innehållet som binär:

```python
binary_data = ds.read_as_binary()
```

Du kan också bara öppna en ström till innehållet:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>Skapa en ny data uppsättning
Med python-klient biblioteket kan du ladda upp data uppsättningar från python-programmet. Dessa data uppsättningar är sedan tillgängliga för användning i din arbets yta.

Om du har dina data i en Pandas-DataFrame använder du följande kod:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Om dina data redan är serialiserade kan du använda:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Python-klient biblioteket kan serialisera en Pandas-DataFrame till följande format (konstanter för dessa är i `azureml.DataTypeIds` klassen):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Uppdatera en befintlig data uppsättning
Om du försöker överföra en ny data uppsättning med ett namn som matchar en befintlig data uppsättning bör du få ett konflikt fel.

Om du vill uppdatera en befintlig data uppsättning måste du först hämta en referens till den befintliga data uppsättningen:

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Använd sedan `update_from_dataframe` för att serialisera och ersätta innehållet i data uppsättningen på Azure:

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Ange ett värde för den valfria parametern om du vill serialisera data till ett annat format `data_type_id` .

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Du kan också ange en ny beskrivning genom att ange ett värde för `description` parametern.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

Alternativt kan du ange ett nytt namn genom att ange ett värde för `name` parametern. Från och med nu hämtar du data uppsättningen med det nya namnet. Följande kod uppdaterar data, namn och beskrivning.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

`data_type_id`Parametrarna och `name` `description` är valfria och standardvärdet för deras tidigare värde. `dataframe`Parametern krävs alltid.

Om dina data redan är serialiserade använder du `update_from_raw_data` i stället för `update_from_dataframe` . Om du bara skickar i `raw_data` stället för  `dataframe` fungerar det på ett liknande sätt.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: /azure/machine-learning/studio-module-reference/convert-to-csv
[split]: /azure/machine-learning/studio-module-reference/split-data