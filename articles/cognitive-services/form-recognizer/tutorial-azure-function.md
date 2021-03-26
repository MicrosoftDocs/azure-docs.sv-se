---
title: 'Självstudie: använda en Azure-funktion för att bearbeta lagrade dokument'
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du använder en Azure-funktion för att utlösa bearbetningen av dokument som överförs till en Azure Blob Storage-behållare.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: bf455d9401593b5c09fa295e492368a2a5bee240
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048700"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Självstudie: använda en Azure-funktion för att bearbeta lagrade dokument

Du kan använda formulär igenkänning som en del av en automatiserad data bearbetnings pipeline som skapats med Azure Functions. Den här guiden visar hur du använder en Azure-funktion för att bearbeta dokument som överförs till en Azure Blob Storage-behållare. Det här arbets flödet hämtar tabell data från lagrade dokument med hjälp av formulär tolkens layout-tjänst och sparar tabell data i en CSV-fil i Azure. Du kan sedan Visa data med hjälp av Microsoft Power BI (beskrivs inte här).

> [!div class="mx-imgBorder"]
> ![arbets flödes diagram för Azure-tjänsten](./media/tutorial-azure-function/workflow-diagram.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Storage-konto
> * Skapa ett Azure Functions-projekt
> * Extrahera layout data från överförda formulär
> * Ladda upp layoutinformation till Azure Storage

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Skapa en formulär igenkännings resurs "  target="_blank"> skapa en formulär igenkännings resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta formulärets igenkännings nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
  * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Forms igenkännings-API: et. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
  * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* Ett lokalt PDF-dokument att analysera. Du kan hämta det här [exempel dokumentet](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) så att det används.
* [Python 3.8. x](https://www.python.org/downloads/) installerat.
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) installerat.
* [Azure Functions Core tools](../../azure-functions/functions-run-local.md?tabs=windows%2ccsharp%2cbash#install-the-azure-functions-core-tools) installerat.
* Visual Studio Code med följande tillägg installerade:
  * [Azure Functions tillägg](/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Python-tillägg](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

[Skapa ett Azure Storage-konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) på Azure Portal. Välj **StorageV2** som konto typ.

I det vänstra fönstret väljer du fliken **CORS** och tar bort den befintliga CORS-principen om sådan finns.

När den har distribuerats skapar du två tomma Blob Storage-behållare, med namnet **test** och **utdata**.

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Öppna Visual Studio Code. Om du har installerat Azure Functions tillägget bör du se en Azure-logotyp i det vänstra navigerings fönstret. Välj den. Skapa ett nytt projekt och när du uppmanas att skapa en lokal mapp **coa_new** att innehålla projektet.

![Knappen Skapa funktion i VSCode](./media/tutorial-azure-function/vs-code-create-function.png)


Du uppmanas att konfigurera ett antal inställningar:
* I meddelandet **Välj ett språk** väljer du python.
* I dialog rutan **Välj en mall** väljer du Azure Blob Storage-utlösare. Ge sedan standard utlösaren ett namn.
* I **Välj inställnings** tolk väljer du att skapa nya lokala app-inställningar.
* Välj din Azure-prenumeration med det lagrings konto som du skapade. Sedan måste du ange namnet på lagrings behållaren (i det här fallet `test/{name}` )
* Välj att öppna projektet i det aktuella fönstret. 

![VSCode skapa prompt-exempel](./media/tutorial-azure-function/vs-code-prompt.png)

När du har slutfört de här stegen kommer VSCode att lägga till ett nytt Azure Function-projekt med ett *\_ \_ init \_ \_ . py* Python-skript. Det här skriptet aktive ras när en fil laddas upp till **test** lagrings behållaren, men det gör inte något.

## <a name="test-the-function"></a>Testa funktionen

Tryck på F5 för att köra funktionen Basic. VSCode kommer att uppmana dig att välja ett lagrings konto som ska finnas i gränssnittet. Välj det lagrings konto som du skapade och fortsätt.

Öppna Azure Storage Explorer och ladda upp ett PDF-exempel dokument till **test** behållaren. Kontrol lera sedan VSCode-terminalen. Skriptet ska logga att det utlöstes av PDF-uppladdningen.

![VSCode-Terminal-test](./media/tutorial-azure-function/vs-code-terminal-test.png)


Stoppa skriptet innan du fortsätter.

## <a name="add-form-processing-code"></a>Lägg till formulär bearbetnings kod

Härnäst ska du lägga till din egen kod till python-skriptet för att anropa formulär tolknings tjänsten och parsa de överförda dokumenten med hjälp av formulär tolkens [layout-API](concept-layout.md).

I VSCode navigerar du till funktionens *requirements.txt* -fil. Detta definierar beroendena för ditt skript. Lägg till följande python-paket i filen:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Öppna sedan skriptet *\_ \_ init \_ \_ . py* . Lägg till följande `import`-uttryck:

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

Du kan lämna den genererade `main` funktionen som den är. Du lägger till din anpassade kod i den här funktionen.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

Följande kodblock anropar formulär tolken och [analyserar layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) -API: et på det överförda dokumentet. Fyll i slut punkt och nyckel värden. 


# <a name="version-20"></a>[version 2,0](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[version 2,1 Preview](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.3/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Gå till Azure-portalen. Om formulär igenkännings resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i artikeln [Cognitive Services Security](../cognitive-services-security.md) .

Lägg sedan till kod för att fråga tjänsten och hämta returnerade data. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Lägg sedan till följande kod för att ansluta till Azure Storage **output** -behållaren. Fyll i dina egna värden för lagrings kontots namn och nyckel. Du kan hämta nyckeln på fliken **åtkomst nycklar** för lagrings resursen i Azure Portal.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

Följande kod tolkar det returnerade formulärets tolknings svar, skapar en. csv-fil och laddar upp den till behållaren **utdata** . 


> [!IMPORTANT]
> Du måste förmodligen redigera koden för att matcha strukturen för dina egna formulär dokument.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Slutligen överför det sista blocket kod den extraherade tabellen och text data till ditt Blob Storage-element.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>Kör funktionen

Tryck på F5 för att köra funktionen igen. Använd Azure Storage Explorer för att ladda upp ett PDF-exempel formulär till **test** lagrings behållaren. Den här åtgärden ska utlösa skriptet för körning och du bör sedan se den resulterande CSV-filen (visas som en tabell) i behållaren **utdata** .

Du kan ansluta den här behållaren till Power BI för att skapa omfattande visualiseringar av de data som den innehåller.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en Azure-funktion som skrivits i python för att automatiskt bearbeta uppladdade PDF-dokument och mata ut innehållet i ett mer användarvänligt format. Nu ska du lära dig hur du använder Power BI för att visa data.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Vad är formigenkänning?](overview.md)
* Läs mer om [layout-API: et](concept-layout.md)