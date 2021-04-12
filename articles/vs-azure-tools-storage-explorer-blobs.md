---
title: Hantera Azure Blob Storage-resurser med Storage Explorer | Microsoft Docs
description: Hantera Azure Blob Storage-resurser med Storage Explorer. Skapa en BLOB-behållare, Visa BLOB container innehåll, ta bort eller kopiera en BLOB-behållare med mera.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 48f00d964a87790b8d8c9b1d8eceaed26d15199e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95531975"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Hantera Azure Blob Storage-resurser med Storage Explorer

## <a name="overview"></a>Översikt

[Azure Blob Storage](./storage/blobs/storage-quickstart-blobs-dotnet.md) är en tjänst för att lagra stora mängder ostrukturerade data, till exempel text eller binära data, som kan nås från var som helst i världen via http eller https.
Du kan använda Blob Storage för att exponera data offentligt eller lagra programdata privat. I den här artikeln får du lära dig hur du använder Storage Explorer för att arbeta med BLOB-behållare och blobbar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

* [Hämta och installera Storage Explorer](https://www.storageexplorer.com)
* [Ansluta till ett Azure Storage-konto eller-tjänst](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Skapa en blobcontainer

Alla blobbar måste finnas i en BLOB-behållare, vilket bara är en logisk gruppering av blobbar. Ett konto kan innehålla ett obegränsat antal behållare, och varje behållare kan lagra ett obegränsat antal blobbar.

Följande steg visar hur du skapar en BLOB-behållare i Storage Explorer.

1. Öppna Storage Explorer.
2. I den vänstra fönsterrutan expanderar du det lagringskonto där du vill skapa blob-containern.
3. Högerklicka på **BLOB-behållare** och välj **skapa BLOB-behållare** från snabb menyn.

   ![Snabbmeny för att skapa blob-containrar][0]
4. En text ruta visas under mappen **BLOB containers** . Ange namnet på blob-containern. Se [skapa en behållare](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) för information om regler och begränsningar för namngivning av BLOB-behållare.

   ![Text ruta för att skapa BLOB-behållare][1]
5. Tryck på **Retur** när du är klar så att blob-containern skapas eller på **Esc** om du vill avbryta. När BLOB-behållaren har skapats visas den under mappen **BLOB containers** för det valda lagrings kontot.

   ![En BLOB-behållare har skapats][2]

## <a name="view-a-blob-containers-contents"></a>Visa innehållet i en BLOB-behållare

BLOB-behållare innehåller blobbar och mappar (som även kan innehålla blobbar).

Följande steg illustrerar hur du visar innehållet i en BLOB-behållare i Storage Explorer:

1. Öppna Storage Explorer.
2. I den vänstra rutan expanderar du det lagrings konto som innehåller den BLOB-behållare som du vill visa.
3. Expandera lagrings kontots **BLOB-behållare**.
4. Högerklicka på den BLOB-behållare som du vill visa och välj **Öppna BLOB container Editor** på snabb menyn.
   Du kan också dubbelklicka på den BLOB-behållare som du vill visa.

   ![Snabb meny för öppna BLOB container Editor][19]
5. I huvud fönstret visas BLOB-behållarens innehåll.

   ![Redigerare för BLOB-behållare][3]

## <a name="delete-a-blob-container"></a>Ta bort en BLOB-behållare

BLOB-behållare kan enkelt skapas och tas bort efter behov. (Information om hur du tar bort enskilda blobbar finns i avsnittet [Hantera blobbar i en BLOB-behållare](#managing-blobs-in-a-blob-container).)

Följande steg illustrerar hur du tar bort en BLOB-behållare i Storage Explorer:

1. Öppna Storage Explorer.
2. I den vänstra rutan expanderar du det lagrings konto som innehåller den BLOB-behållare som du vill visa.
3. Expandera lagrings kontots **BLOB-behållare**.
4. Högerklicka på den BLOB-behållare som du vill ta bort och – välj **ta bort** från snabb menyn.
   Du kan också trycka på **ta bort** för att ta bort den valda BLOB-behållaren.

   ![Ta bort snabb menyn för BLOB-behållare][4]
5. Välj **Ja** i bekräftelsedialogen.

   ![Bekräfta borttagning av BLOB-behållare][5]

## <a name="copy-a-blob-container"></a>Kopiera en BLOB-behållare

Med Storage Explorer kan du kopiera en BLOB-behållare till Urklipp och sedan klistra in BLOB-behållaren i ett annat lagrings konto. (Information om hur du kopierar enskilda blobbar finns i avsnittet [Hantera blobbar i en BLOB-behållare](#managing-blobs-in-a-blob-container).)

Följande steg illustrerar hur du kopierar en BLOB-behållare från ett lagrings konto till ett annat.

1. Öppna Storage Explorer.
2. I den vänstra rutan expanderar du det lagrings konto som innehåller den BLOB-behållare som du vill kopiera.
3. Expandera lagrings kontots **BLOB-behållare**.
4. Högerklicka på den BLOB-behållare som du vill kopiera och välj **Kopiera BLOB-behållare** på snabb menyn.

   ![Snabb meny för kopiering av BLOB-behållare][6]
5. Högerklicka på det önskade "mål" lagrings kontot där du vill klistra in BLOB-behållaren och – välj **Klistra in BLOB-behållare** på snabb menyn.

   ![Snabb meny för att klistra in BLOB-behållare][7]

## <a name="get-the-sas-for-a-blob-container"></a>Hämta SAS för en blobcontainer

En [signatur för delad åtkomst (Shared Access Signature, SAS)](./storage/common/storage-sas-overview.md) ger delegerad åtkomst till resurser på ditt lagringskonto.
Det innebär att du kan ge en klient begränsad behörighet till objekt på ditt lagringskonto under en angiven tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela nycklarna för åtkomst till ditt konto.

Följande steg visar hur du skapar en SAS för en BLOB-behållare:

1. Öppna Storage Explorer.
2. I den vänstra rutan expanderar du det lagrings konto som innehåller den BLOB-behållare som du vill hämta en SAS för.
3. Expandera lagrings kontots **BLOB-behållare**.
4. Högerklicka på önskad BLOB-behållare och välj **Hämta signatur för delad åtkomst** på snabb menyn.

   ![Hämta SAS-snabb menyn][8]
5. I dialogrutan **Signatur för delad åtkomst** anger du princip, start- och förfallodatum, tidszon och åtkomstnivåer som du vill använda för resursen.

   ![Hämta SAS-alternativ][9]
6. När du är klar med att ange SAS-alternativen väljer du **Skapa**.
7. En andra dialog ruta **signatur för delad åtkomst** kommer sedan att visa en lista över BLOB-behållaren tillsammans med URL: en och frågesträngar som du kan använda för att få åtkomst till lagrings resursen.
   Välj **Kopiera** bredvid den URL som du vill kopiera till Urklipp.

   ![Kopiera SAS-URL: er][10]
8. När du är klar väljer du **Stäng**.

## <a name="manage-access-policies-for-a-blob-container"></a>Hantera åtkomst principer för en BLOB-behållare

Följande steg visar hur du hanterar (lägger till och tar bort) åtkomst principer för en BLOB-behållare:

1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du lagrings kontot som innehåller BLOB-behållaren vars åtkomst principer du vill hantera.
3. Expandera lagrings kontots **BLOB-behållare**.
4. Välj önskad BLOB-behållare och välj **Hantera åtkomst principer** på snabb menyn.

   ![Snabbmeny för hantering av åtkomstprinciper][11]
5. Dialog rutan **åtkomst principer** visar en lista över alla åtkomst principer som redan har skapats för den valda BLOB-behållaren.

   ![Alternativ för åtkomst princip][12]
6. Gör följande beroende på åtkomstprincipens hanteringsuppgift:

   * **Lägg till en ny åtkomstprincip** – Välj **Lägg till**. När åtkomstprinciepn har skapats visas den som nyligen tillagd i dialogen **Åtkomstprinciper** (med standardinställningarna).
   * **Redigera en åtkomst princip** – gör önskade ändringar och välj **Spara**.
   * **Ta bort en åtkomstprincip** – Välj **Ta bort** bredvid den åtkomstprincip som du vill ta bort.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Ange offentlig åtkomst nivå för en BLOB-behållare

Som standard är varje BLOB-behållare inställd på "ingen offentlig åtkomst".

Följande steg illustrerar hur du anger en offentlig åtkomst nivå för en BLOB-behållare.

1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du lagrings kontot som innehåller BLOB-behållaren vars åtkomst principer du vill hantera.
3. Expandera lagrings kontots **BLOB-behållare**.
4. Välj önskad BLOB-behållare och-från snabb menyn – Välj **Ange offentlig åtkomst nivå**.

   ![Ange snabb meny för offentlig åtkomst nivå][13]
5. I dialog rutan **Ange offentlig åtkomst nivå för behållare** anger du önskad åtkomst nivå.

   ![Ange alternativ för offentlig åtkomst nivå][14]
6. Välj **Använd**.

## <a name="managing-blobs-in-a-blob-container"></a>Hantera blobbar i en BLOB-behållare

När du har skapat en BLOB-behållare kan du ladda upp en blob till BLOB-behållaren, ladda ned en blob till den lokala datorn, öppna en BLOB på den lokala datorn och mycket mer.

Följande steg illustrerar hur du hanterar blobbar (och mappar) i en BLOB-behållare.

1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du lagringskontot som innehåller den blobcontainer som du vill hantera.
3. Expandera lagrings kontots **BLOB-behållare**.
4. Dubbelklicka på den BLOB-behållare som du vill visa.
5. I huvud fönstret visas BLOB-behållarens innehåll.

   ![Visa BLOB-behållare][3]
6. I huvud fönstret visas BLOB-behållarens innehåll.
7. Gör följande beroende på vilken aktivitet du vill utföra:

   * **Ladda upp filer till en BLOB-behållare**

     1. Gå till verktygsfältet i huvudfönstret och välj **Överför**, och sedan **Överför filer** i den nedrullningsbara menyn.

        ![Menyn Ladda upp filer][15]
     2. I dialogen **Överför filer** dialogrutan klickar du på knappen med tre punkter (**...**) på höger sida av textrutan **Filer** och markerar den eller de filer du vill överföra.

        ![Alternativ för att ladda upp filer][16]
     3. Ange typ av **Blob-typ**. Mer information finns i [skapa en behållare](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .
     4. Du kan också ange en målmapp där de markerade filerna ska överföras. Om målmappen inte finns skapas den.
     5. Välj **Överför**.
   * **Ladda upp en mapp till en BLOB-behållare**

     1. Gå till verktygsfältet i huvudfönstret och klicka på **Överför**, och sedan på **Överför mapp** i den nedrullningsbara menyn.

        ![Menyn för mappöverföring][17]
     2. I dialogen **Överför mapp** klickar du på knappen med tre punkter (**...**) på höger sida av textrutan **Mapp** och väljer den mapp vars innehåll du vill överföra.

        ![Överför Mappalternativ][18]
     3. Ange typ av **Blob-typ**. Mer information finns i [skapa en behållare](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .
     4. Du kan även ange en målmapp som den markerade mappens innehåll ska överföras till. Om målmappen inte finns skapas den.
     5. Välj **Överför**.
   * **Ladda ned en blob till den lokala datorn**

     1. Välj den blob som du vill ladda ned.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Hämta**.
     3. I dialog rutan **Ange var du vill spara den hämtade blobben** anger du den plats där du vill att bloben ska hämtas och det namn som du vill ge den.  
     4. Välj **Spara**.
   * **Öppna en BLOB på den lokala datorn**

     1. Välj den blob som du vill öppna.
     2. Gå till verktygsfältet i huvudfönstret och välj **Öppna**.
     3. Blobben laddas ned och öppnas med det program som är associerat med blobens underliggande filtyp.
   * **Kopiera en blob till Urklipp**

     1. Välj den blob som du vill kopiera.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Kopiera**.
     3. I det vänstra fönstret navigerar du till en annan BLOB-behållare och dubbelklickar på den för att visa den i huvud fönstret.
     4. I verktygsfältet i huvud fönstret väljer du **Klistra in** för att skapa en kopia av blobben.
   * **Ta bort en blob**

     1. Välj den blob som du vill ta bort.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Ta bort**.
     3. Välj **Ja** i bekräftelsedialogen.

## <a name="next-steps"></a>Nästa steg

* Visa [viktig information och videor för den senaste Storage Explorer-versionen](https://www.storageexplorer.com).
* Läs mer om hur du [skapar program med Azure-blobbar, tabeller köer och filer](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png