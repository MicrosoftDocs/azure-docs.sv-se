---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 921127ffdd35007cc3fa2eaaa95cdb3fac8bbe15
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782288"
---
Du kan visa kön i [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) eller i  [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Du kan också visa kön i Azure CLI enligt beskrivningen i följande steg:

1. Öppna funktionsprojektets namnlocal.setting.js *filen* och kopiera värdet för anslutningssträngen. Kör följande kommando i en terminal eller ett kommandofönster för att skapa en miljövariabel med namnet och klistra `AZURE_STORAGE_CONNECTION_STRING` in din specifika anslutningssträng i stället för  `<MY_CONNECTION_STRING>` . (Den här miljövariabeln innebär att du inte behöver ange anslutningssträngen till varje efterföljande kommando med `--connection-string` argumentet .)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Valfritt) Använd kommandot [`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list) för att visa Storage-köerna i ditt konto. Utdata från det här kommandot ska innehålla en kö med namnet `outqueue` , som skapades när funktionen skrev sitt första meddelande till kön.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Använd kommandot [`az storage message get`](/cli/azure/storage/message#az_storage_message_get) för att läsa meddelandet från den här kön, vilket bör vara det förnamn som du använde när du testade funktionen tidigare. Kommandot läser och tar bort det första meddelandet från kön. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Det här skriptet använder certutil för att avkoda base64-kodad meddelandesamling från en lokal temp-fil. Om det inte finns några utdata provar du att ta bort från skriptet för att sluta ignorera `> NUL` certutil-utdata om det uppstår ett fel. 
    
    ---
    
    Eftersom meddelandetexten lagras [base64-kodad](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)måste meddelandet avkodas innan det visas. När du `az storage message get` har kört tas meddelandet bort från kön. Om det bara fanns ett meddelande i hämtar du inte ett meddelande när du kör det här kommandot `outqueue` en andra gång och får i stället ett fel.
