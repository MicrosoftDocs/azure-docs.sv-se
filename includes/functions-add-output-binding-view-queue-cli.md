---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90606276"
---
Du kan visa kön i [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) eller i  [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Du kan också visa kön i Azure CLI enligt beskrivningen i följande steg:

1. Öppna funktions projektets *local.setting.jspå* fil och kopiera värdet för anslutnings strängen. Kör följande kommando i ett terminalfönster-eller kommando fönster för att skapa en miljö variabel med namnet `AZURE_STORAGE_CONNECTION_STRING` , och klistra in din aktuella anslutnings sträng i stället för  `<MY_CONNECTION_STRING>` . (Denna miljö variabel innebär att du inte behöver ange anslutnings strängen för varje efterföljande kommando med hjälp av `--connection-string` argumentet.)

    # <a name="bash"></a>[bash](#tab/bash)
    
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
    
1. Valfritt Använd [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) kommandot för att Visa lagrings köer i ditt konto. Utdata från det här kommandot ska innehålla en kö med namnet `outqueue` , som skapades när funktionen skrev sitt första meddelande till kön.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Använd [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) kommandot för att läsa meddelandet från den här kön, vilket bör vara det första namnet du använde när du testar funktionen tidigare. Kommandot läser och tar bort det första meddelandet från kön. 

    # <a name="bash"></a>[bash](#tab/bash)
    
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

    Det här skriptet använder certutil för att avkoda den base64-kodade meddelande samlingen från en lokal Temp-fil. Om det inte finns några utdata kan du försöka ta bort `> NUL` från skriptet för att sluta ignorera certutil-utdata om det uppstår ett fel. 
    
    ---
    
    Eftersom meddelande texten lagras base64- [kodat](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)måste meddelandet avkodas innan det visas. När du `az storage message get` har kört meddelandet tas meddelandet bort från kön. Om det bara finns ett meddelande i `outqueue` , kommer du inte att få ett meddelande när du kör det här kommandot en gång i stället. ett fel visas i stället.
