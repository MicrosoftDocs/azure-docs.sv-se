---
title: 'Kör Python-skript: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen kör Python-skript i Azure Machine Learning designer för att köra python-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 12/02/2020
ms.openlocfilehash: d1e4ffa525c5628d0b6c9a3ca67f3e069c44e823
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679191"
---
# <a name="execute-python-script-module"></a>Köra Python-skript modul

Den här artikeln beskriver skript modulen kör python i Azure Machine Learning designer.

Använd den här modulen för att köra python-kod. Mer information om arkitekturen och design principerna för python finns i så här [kör du python-kod i Azure Machine Learning designer](../how-to-designer-python.md).

Med python kan du utföra uppgifter som befintliga moduler inte stöder, till exempel:

+ Visualisera data med hjälp av `matplotlib` .
+ Använd python-bibliotek för att räkna upp data uppsättningar och modeller i din arbets yta.
+ Läsning, inläsning och manipulering av data från källor som modulen [Importera data](./import-data.md) inte stöder.
+ Kör din egen djup inlärnings kod. 

## <a name="supported-python-packages"></a>Python-paket som stöds

Azure Machine Learning använder Anaconda-distributionen av python, som innehåller många vanliga verktyg för data bearbetning. Vi kommer att uppdatera Anaconda-versionen automatiskt. Den aktuella versionen är:
 -  Anaconda 4.5 + distribution för python 3,6 

En fullständig lista finns i avsnittet [förinstallerade python-paket](#preinstalled-python-packages).

Om du vill installera paket som inte finns i den förinstallerade listan (till exempel *scikit-Diverse*) lägger du till följande kod i skriptet: 

```python
import os
os.system(f"pip install scikit-misc")
```

Använd följande kod för att installera paket för bättre prestanda, särskilt för härledning:
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> Om din pipeline innehåller flera köra Python-skript moduler som behöver paket som inte finns i den förinstallerade listan, installerar du paketen i varje modul.

> [!WARNING]
> Excute Python-skript module stöder inte installation av paket som är beroende av extra interna bibliotek med kommando som "apt-get", till exempel Java, PyODBC och så vidare. Detta beror på att den här modulen körs i en enkel miljö med python förinstallerat endast och med icke-administratörs behörighet.  

## <a name="access-to-registered-datasets"></a>Åtkomst till registrerade data uppsättningar

Du kan referera till följande exempel kod för att få åtkomst till [registrerade data uppsättningar](../how-to-create-register-datasets.md) på din arbets yta:

```Python
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    ws = run.experiment.workspace

    from azureml.core import Dataset
    dataset = Dataset.get_by_name(ws, name='test-register-tabular-in-designer')
    dataframe1 = dataset.to_pandas_dataframe()
     
    # If a zip file is connected to the third input port,
    # it is unzipped under "./Script Bundle". This directory is added
    # to sys.path. Therefore, if your zip file contains a Python file
    # mymodule.py you can import it using:
    # import mymodule

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
```

## <a name="upload-files"></a>Ladda upp filer
EXECUTE Python-skript module stöder överföring av filer med hjälp av [Azure Machine Learning python SDK](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#upload-file-name--path-or-stream-).

I följande exempel visas hur du laddar upp en avbildnings fil i modulen kör Python-skript:

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

När pipeline-körningen är färdig kan du förhandsgranska bilden i den högra panelen i modulen.

> [!div class="mx-imgBorder"]
> ![Förhands granskning av Uppladdad bild](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Så här konfigurerar du kör Python-skript

EXECUTE Python-skript module innehåller exempel på python-kod som du kan använda som start punkt. Ange en uppsättning indata och python-kod som ska köras i text rutan **Python-skript** för att konfigurera EXECUTE python script module.

1. Lägg till modulen **Kör Python-skript** i din pipeline.

2. Lägg till och Anslut på **Dataset1** alla data uppsättningar från designern som du vill använda för indata. Referera till den här data uppsättningen i python-skriptet som **DataFrame1**.

    Användningen av en data uppsättning är valfri. Använd den om du vill generera data med hjälp av python eller använda python-kod för att importera data direkt till modulen.

    Den här modulen stöder tillägg av en andra data uppsättning på **Dataset2**. Referera till den andra data mängden i python-skriptet som **DataFrame2**.

    Data uppsättningar som lagras i Azure Machine Learning konverteras automatiskt till Pandas data ramar när de läses in med den här modulen.

    ![Köra python-indatamängds karta](media/module/python-module.png)

4. Om du vill inkludera nya python-paket eller-kod ansluter du den zippade filen som innehåller dessa anpassade resurser till **skript paketets** port. Eller om ditt skript är större än 16 KB använder du **Skriptets paket** port för att undvika fel som *kommando raden överskrider gränsen på 16597 tecken*. 

    
    1. Paketera skriptet och andra anpassade resurser i en zip-fil.
    1. Överför zip-filen som en **fil data uppsättning** till Studio. 
    1. Dra data uppsättnings modulen från listan *data uppsättningar* i fönstret till vänster på design sidan i designern. 
    1. Anslut data uppsättnings modulen till **skript pakets** porten för **execute python-skriptfil** .
    
    Alla filer som finns i det överförda zippade arkivet kan användas under pipeline-körningen. Om arkivet innehåller en katalog struktur bevaras strukturen.
 
    > [!WARNING]
    >  Använd inte **appen** som namnet på mappen eller skriptet eftersom **appen** är ett reserverat ord för inbyggda tjänster. Men du kan använda andra namn områden som `app123` .
   
    Följande är ett exempel på ett skript paket som innehåller en python-skriptfil och en txt-fil:
      
    > [!div class="mx-imgBorder"]
    > ![Exempel på skript paket](media/module/python-script-bundle.png)  

    Följande är innehållet i `my_script.py` :

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    Följande är exempel kod som visar hur du använder filerna i skript paketet:    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. I text rutan **Python-skript** skriver eller klistrar du in giltigt Python-skript.

    > [!NOTE]
    >  Var försiktig när du skriver skriptet. Se till att det inte finns några syntaxfel, till exempel att använda variabler som inte har deklarerats eller ej importerade moduler eller funktioner. Betala extra uppmärksamhet till den förinstallerade modulen. Om du vill importera moduler som inte visas i listan installerar du motsvarande paket i skriptet, till exempel:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Text rutan **Python-skript** är förifylld med instruktioner i kommentarer och exempel kod för data åtkomst och utdata. Du måste redigera eller ersätta den här koden. Följ python-konventioner för indrag och Skift läge:

    + Skriptet måste innehålla en funktion som heter `azureml_main` som start punkt för den här modulen.
    + Start punkt funktionen måste ha två indataargument `Param<dataframe1>` och `Param<dataframe2>` även om dessa argument inte används i skriptet.
    + Zippade filer som är anslutna till den tredje Indataporten är zippade och lagras i katalogen `.\Script Bundle` , som också läggs till i python `sys.path` . 

    Om din. zip-fil innehåller `mymodule.py` importerar du den med hjälp av `import mymodule` .

    Två data uppsättningar kan returneras till designern, som måste vara en sekvens av typen `pandas.DataFrame` . Du kan skapa andra utdata i python-koden och skriva dem direkt till Azure Storage.

    > [!WARNING]
    > Vi rekommenderar **inte** att du ansluter till en databas eller andra externa lagrings enheter i **köra python-skriptfil**. Du kan använda modulen [Importera data](./import-data.md) och [Exportera data](./export-data.md)     

6. Skicka pipelinen.

    Alla data och all kod läses in i en virtuell dator och körs med den angivna python-miljön.

## <a name="results"></a>Resultat

Resultatet av eventuella beräkningar av den inbäddade python-koden måste anges som `pandas.DataFrame` , vilket automatiskt konverteras till det Azure Machine Learning data uppsättnings formatet. Du kan sedan använda resultatet med andra moduler i pipelinen.

Modulen returnerar två data uppsättningar:  
  
+ **Resultat data uppsättning 1**, som definieras av den första data ramen som returnerade Pandas i ett Python-skript.

+ **Resultat data uppsättning 2**, som definieras av den andra returnerade data ramen Pandas i ett Python-skript.

## <a name="preinstalled-python-packages"></a>Förinstallerade python-paket
De förinstallerade paketen är:
-    ADAL = = 1.2.2
-    applicationinsights = = 0.11.9
-    attr = = 19.3.0
-    Azure-common = = 1.1.25
-    Azure-core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure-Identity = = 1.3.0
-    Azure-MGMT-Authorization = = 0.60.0
-    Azure-MGMT-containerregistry = = 2.8.0
-    Azure-MGMT-nyckel valv = = 2.2.0
-    Azure-MGMT-Resource = = 8.0.1
-    Azure-MGMT-Storage = = 8.0.0
-    Azure-Storage-BLOB = = 1.5.0
-    Azure-Storage – common = = 1.4.2
-    azureml-core = = 1.1.5.5
-    azureml-nu-Native = = 14.1.0
-    azureml-nu = = 1.3.5
-    azureml-standardvärden = = 1.1.5.1
-    azureml-designer-klassisk-modules = = 0.0.118
-    azureml-designer-Core = = 0.0.31
-    azureml-designer – Internal = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml-pipeline-core = = 1.1.5
-    azureml-telemetri = = 1.1.5.3
-    backports. tempfile = = 1.0
-    backports. weakref = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    certifiera = = 2019.11.28
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    Klicka på = = 7.1.1
-    cloudpickle = = 1.3.0
-    ConfigParser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    kryptografi = = 2,8
-    cykel = = 0.10.0
-    dill = = 0.3.1.1
-    distribution = = 1.4.0
-    Docker = = 4.2.0
-    docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    kolv = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-core = = 1.3.0
-    Google-Cloud – Storage = = 1.26.0
-    Google-återupptagande-Media = = 0.5.0
-    googleapis – common-prot = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2.9
-    obalanserat-lär = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-loggning – py = = 0,2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    liac-arff = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    Mer-itertools = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    numpy = = 1.18.2
-    oauthlib = = 3.1.0
-    Pandas = = 0.25.3
-    pathspec = = 0.7.0
-    PIP = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modules = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    python-dateutil = = 2.8.1
-    pytz = = 2019.3
-    begär Anden-oauthlib = = 1.3.0
-    begär Anden = = 2.23.0
-    RSA = = 4.0
-    ruamel. yaml = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit – lära = = 0.22.2
-    scipy = = 1.4.1
-    secretstorage = = 3.1.2
-    installations verktyg = = 46.1.1. post20200323
-    sex = = 1.14.0
-    Smart-öppen = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-Client = = 0.57.0
-    werkzeug = = 0.16.1
-    Wheel = = 0.34.2

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.