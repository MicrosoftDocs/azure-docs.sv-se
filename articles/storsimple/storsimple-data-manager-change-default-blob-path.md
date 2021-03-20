---
title: Ändra BLOB-sökväg från standard
description: Lär dig hur du konfigurerar en Azure-funktion för att byta namn på en standard Sök väg för BLOB-filer och flytta Blobbarna till en annan plats.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011446"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Ändra en blobsökväg från standardsökvägen

När StorSimple Data Managers tjänsten transformerar data placeras omvandlade blobbar som standard i en lagrings behållare som anges under skapandet av mål lagrings platsen. När Blobbarna kommer till den här platsen kanske du vill flytta dessa blobbar till en annan plats. Den här artikeln beskriver hur du konfigurerar en Azure-funktion för att byta namn på en standard Sök väg för BLOB-filer och därmed flytta blobarna till en annan plats.

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har en korrekt konfigurerad jobb definition i StorSimple Data Managers tjänsten.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Utför följande steg för att skapa en Azure-funktion:

1. Gå till [Azure-portalen](https://portal.azure.com/).

2. Klicka på **+ Skapa en resurs**. I rutan **Sök** skriver du **Funktionsapp** och trycker på **RETUR**. Välj och klicka på **Function app** i listan med appar som visas.

    ![Skriv "Funktionsapp" i sökrutan](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klicka på **Skapa**.

    ![Knappen Funktionsapp fönstret "skapa"](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Utför följande steg på bladet **Funktionsapp** konfiguration:

    1. Ange ett unikt **namn för appen**.
    2. I list rutan väljer du **prenumerationen**. Den här prenumerationen ska vara samma som den som är associerad med din StorSimple Data Manager-tjänst.
    3. Välj **Skapa ny** resurs grupp.
    4. Välj **förbruknings plan** för List rutan **värd plan** .
    5. Ange en plats där din funktion körs. Du vill ha samma region där StorSimple Data Manager-tjänsten och lagrings kontot som är kopplat till jobb definitionen finns.
    6. Välj ett befintligt lagringskonto eller skapa ett nytt lagringskonto. Ett lagrings konto används internt för funktionen.

        ![Ange nya Funktionsapp konfigurations data](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klicka på **Skapa**. Function-appen skapas.
     
        ![Funktionsapp skapats](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Välj **Functions** och klicka på **+ ny funktion**.

    ![Klicka på + ny funktion](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Välj **C#** som språk. I matrisen med mall paneler väljer du **C#** på panelen **QueueTrigger-csharp** .

7. I **kön utlösare**:

    1. Ange ett **namn** för din funktion.
    2. I rutan **Könamn** skriver du namnet på din data omvandlings jobb definition.
    3. Under **lagrings konto anslutning** klickar du på **ny**. I listan över lagrings konton väljer du det konto som är associerat med din jobb definition. Anteckna anslutnings namnet (markerat). Namnet krävs senare i Azure-funktionen.

        ![Skapa en ny C#-funktion](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klicka på **Skapa**. **Funktionen** skapas.

     
10. I fönstret funktion kör du _. CSX_ -filen.

    ![Skapa en ny C#-funktion 2](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Utför följande steg.

    1. Klistra in följande kod:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Ersätt **STORAGE_CONNECTIONNAME** på rad 11 med din lagrings konto anslutning (se steg 7c).

        ![Kopiera namn på lagrings anslutning](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Spara** funktionen.

        ![Spara funktion](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Slutför funktionen genom att lägga till en eller flera filer på följande sätt:

    1. Klicka på **Visa filer**.

       ![Länken "Visa filer"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klicka på **+ Lägg till**.
        
        ![Lägg till ny funktion Lägg till fil](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Skriv **project.jspå** och tryck sedan på **RETUR**. I **project.js** filen klistrar du in följande kod:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Klicka på **Spara**.

        ![Ny funktion projekt-JSON](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Du har skapat en Azure-funktion. Den här funktionen utlöses varje gång en ny BLOB genereras av data omvandlings jobbet.

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager användar gränssnitt för att transformera dina data](storsimple-data-manager-ui.md)
