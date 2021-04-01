---
title: Använda Storage Explorer med Azure File Storage | Microsoft Docs
description: Lär dig att använda Storage Explorer för att arbeta med filresurser och filer.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 84f6473c25a5be11eeda7cd2b311d93a7226a78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96488399"
---
# <a name="using-storage-explorer-with-azure-file-storage"></a>Använd Storage Explorer med Azure File Storage

Azure File Storage är en tjänst som erbjuder filresurser i molnet med hjälp av SMB-standardprotokollet (Server Message Block). Både SMB 2.1 och SMB 3.0 stöds. Med Azure File Storage kan du snabbt och utan kostsamma omskrivningar migrera äldre program som är beroende av filresurser till Azure. Du kan använda File Storage om du vill exponera data offentligt eller lagra programdata privat. I den här artikeln lär du dig att använda Storage Explorer för att arbeta med filresurser och filer.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

- [Hämta och installera Storage Explorer](https://www.storageexplorer.com/)

- [Ansluta till ett Azure Storage-konto eller-tjänst](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>Skapa en filresurs

Alla filer måste finnas i en filresurs, vilket är en logisk gruppering av filer. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer.

Följande steg visar hur du skapar en filresurs i Storage Explorer.

1. Öppna Storage Explorer.

1. Expandera lagringskontot som du vill skapa filresursen i från den vänstra rutan

1. Högerklicka på **Filresurser** och välj **Skapa filresurs** från snabbmenyn.

    ![Skapa en filresurs](media/vs-azure-tools-storage-explorer-files/image1.png)

1. En textruta visas nedanför mappen **Filresurser**. Ange namnet på filresursen. Se avsnittet om [regler för namngivning av resurser](./storage/blobs/storage-quickstart-blobs-dotnet.md) där det finns en lista över regler och begränsningar för namngivning av filresurser.

    ![Namnge resursen](media/vs-azure-tools-storage-explorer-files/image2.png)

1. Tryck på **Retur** när du är klar så att filresursen skapas eller på **Esc** om du vill avbryta. När en filresurs har skapats visas den i mappen **Filresurser** för det valda lagringskontot.

    ![Den nya resursen](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>Visa innehållet i en filresurs

Filresurser innehåller filer och mappar (som kan också innehålla filer).

Följande steg illustrerar hur du kan visa innehållet i en filresurs i Storage Explorer: +

1. Öppna Storage Explorer.

1. Expandera lagringskontot som innehåller den filresurs som du vill visa i den vänstra rutan.

1. Expandera lagringskontots **filresurser**.

1. Högerklicka på den filresurs som du vill visa och välj **Öppna** från snabbmenyn. Du kan även dubbelklicka på den filresurs som du vill visa.

    ![Öppna resursen](media/vs-azure-tools-storage-explorer-files/image4.png)

1. I huvudfönstret visas innehållet i filresursen.
    
    ![Skärm bild av huvud fönstret för en fil resurs i Storage Explorer visar innehållet i resursen.](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>Ta bort en filresurs

Filresurser kan enkelt skapas och tas bort efter behov. (Information om hur du tar bort enskilda filer finns i avsnittet [Hantera filer i en filresurs](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container).)

Följande steg visar hur du raderar en filresurs i Storage Explorer:

1. Öppna Storage Explorer.

1. Expandera lagringskontot som innehåller den filresurs som du vill visa i den vänstra rutan.

1. Expandera lagringskontots **filresurser**.

1. Högerklicka på den filresurs som du vill ta bort och välj **Ta bort** från snabbmenyn. Du kan även trycka **Ta bort** om du vill ta bort den markerade filresursen.

    ![Ta bort](media/vs-azure-tools-storage-explorer-files/image6.png)

1. Välj **Ja** i bekräftelsedialogen.
    
    ![Bekräftelsedialog](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>Kopiera en filresurs

Med Storage Explorer kan du kopiera en filresurs till Urklipp och klistra in filresursen på ett annat lagringskonto. (Information om hur du kopierar enskilda filer finns i avsnittet [Hantera filer i en filresurs](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container).)

Följande steg illustrerar hur du kopierar en filresurs från ett lagringskonto till ett annat.

1. Öppna Storage Explorer.

1. Expandera lagringskontot som innehåller den filresurs som du vill kopiera i den vänstra rutan.

1. Expandera lagringskontots **filresurser**.

1. Högerklicka på den filresurs som du vill kopiera och välj **Kopiera filresurs** från snabbmenyn.

    ![Kopiera filresurs](media/vs-azure-tools-storage-explorer-files/image8.png)

1. Högerklicka på det önskade mållagringskonto där du vill klistra in filresursen och välj sedan **Paste File Share** (Klistra in filresurs) från snabbmenyn.

    ![Klistra in filresurs](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>Hämta SAS:en för en filresurs

En [signatur för delad åtkomst (Shared Access Signature, SAS)](./storage/common/storage-sas-overview.md) ger delegerad åtkomst till resurser på ditt lagringskonto. Det innebär att du kan ge en klient begränsad behörighet till objekt på ditt lagringskonto under en angiven tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela nycklarna för åtkomst till ditt konto.

Följande steg visar hur du skapar en SAS för en filresurs: +

1. Öppna Storage Explorer.

1. Gå till vänster ruta och expandera det lagringskonto som innehåller filresursen som du vill hämta en SAS för.

1. Expandera lagringskontots **filresurser**.

1. Högerklicka på den önskade filresursen och välj **Hämta signatur för delad åtkomst** på snabbmenyn.

    ![Hämta signatur för delad åtkomst](media/vs-azure-tools-storage-explorer-files/image10.png)

1. I dialogrutan **Signatur för delad åtkomst** anger du princip, start- och förfallodatum, tidszon och åtkomstnivåer som du vill använda för resursen.

    ![SAS-dialog](media/vs-azure-tools-storage-explorer-files/image11.png)

1. När du är klar med att ange SAS-alternativen väljer du **Skapa**.

1. En andra dialog för **Signatur för delad åtkomst** visas sedan. Här finns en lista över filresursen tillsammans med URL:en och frågesträngar som du kan använda för att få åtkomst till lagringsresursen. Välj **Kopiera** bredvid den URL som du vill kopiera till Urklipp.
    
    ![Andra SAS-dialog](media/vs-azure-tools-storage-explorer-files/image12.png)

1. När du är klar väljer du **Stäng**.

## <a name="manage-access-policies-for-a-file-share"></a>Hantera åtkomstprinciper för en filresurs

Följande steg illustrerar hur du hanterar (lägger till och tar bort) åtkomstprinciper för en filresurs: +. Åtkomstprinciperna används för att skapa SAS-URL: er som kan användas för att komma åt Storage File-resursen under en angiven tidsperiod.

1. Öppna Storage Explorer.

1. Gå till vänster ruta och expandera det lagringskonto som innehåller den filresurs som du vill hantera åtkomstprinciper för.

1. Expandera lagringskontots **filresurser**.

1. Välj den önskade filresursen och **Hantera åtkomstprinciper** från snabbmenyn.

    ![Snabbmeny för hantering av åtkomstprinciper](media/vs-azure-tools-storage-explorer-files/image13.png)

1. I dialogen **Åtkomstprinciper** visas en lista över alla åtkomstprinciper som redan har skapats för den valda filresursen.
    
    ![Åtkomstprinciper](media/vs-azure-tools-storage-explorer-files/image14.png)

1. Gör följande beroende på åtkomstprincipens hanteringsuppgift:
    
    - **Lägg till en ny åtkomstprincip** – Välj **Lägg till**. När åtkomstprinciepn har skapats visas den som nyligen tillagd i dialogen **Åtkomstprinciper** (med standardinställningarna).

    - **Redigera en åtkomstprincip** – Gör önskade redigeringar och välj **Spara**.

    - **Ta bort en åtkomstprincip** – Välj **Ta bort** bredvid den åtkomstprincip som du vill ta bort.

1. Skapa en ny SAS-URL med hjälp av den åtkomstprincip som du skapade tidigare:
    
    ![Hämta SAS](media/vs-azure-tools-storage-explorer-files/image15.png)
    
    ![SAS-namn och -egenskaper](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>Hantera filer i en filresurs

När du har skapat en filresurs kan du överföra en fil till den filresursen, hämta en fil till den lokala datorn, öppna en fil på den lokala datorn och mycket mer.

Följande steg illustrerar hur du hanterar filer (och mappar) i en filresurs.

1.  Öppna Storage Explorer.

1.  Expandera lagringskontot som innehåller den filresurs som du vill hantera i den vänstra rutan.

1.  Expandera lagringskontots **filresurser**.

1.  Dubbelklicka på den filresurs som du vill visa.

1.  I huvudfönstret visas innehållet i filresursen.

    ![Skärm bild av huvud fönstret för fil resursen myazurefileshare i Storage Explorer, som visar innehållet i resursen med den första mappen som har valts.](media/vs-azure-tools-storage-explorer-files/image17.png)

1.  I huvudfönstret visas innehållet i filresursen.

1.  Gör följande beroende på vilken aktivitet du vill utföra:

    - **Överföra filer till en filresurs**

        a.  Gå till verktygsfältet i huvudfönstret och välj **Överför**, och sedan **Överför filer** i den nedrullningsbara menyn.

        ![Ladda upp filer](media/vs-azure-tools-storage-explorer-files/image18.png)
        
        b. I dialogen **Överför filer** dialogrutan klickar du på knappen med tre punkter (**...**) på höger sida av textrutan **Filer** och markerar den eller de filer du vill överföra.

        ![Lägga till filer](media/vs-azure-tools-storage-explorer-files/image19.png)

        c. Välj **Överför**.

    - **Överföra en mapp till en filresurs**
        
        a. Gå till verktygsfältet i huvudfönstret och klicka på **Överför**, och sedan på **Överför mapp** i den nedrullningsbara menyn.

        ![Menyn för mappöverföring](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. I dialogen **Överför mapp** klickar du på knappen med tre punkter (**...**) på höger sida av textrutan **Mapp** och väljer den mapp vars innehåll du vill överföra.

        c. Du kan även ange en målmapp som den markerade mappens innehåll ska överföras till. Om målmappen inte finns skapas den.

        d. Välj **Överför**.

    - **Hämta en fil till din lokala dator**
        
        a. Markera den fil du vill hämta.
        
        b. Gå till verktygsfältet i huvudfönstret och klicka på **Hämta**.
        
        c. I dialogrutan för att **ange var du vill spara den hämtade filen** anger du den plats där du vill spara den fil som hämtas och det namn som du vill ge den.

        d. Välj **Spara**.

    - **Öppna en fil på den lokala datorn**
        
        a.  Markera den fil som du vill öppna.
        
        b.  Gå till verktygsfältet i huvudfönstret och välj **Öppna**.
        
        c.  Filen kommer att hämtas och öppnas med det program som är kopplat till filens underliggande filtyp.

    - **Kopiera en fil till Urklipp**

        a. Markera den fil som du vill kopiera.

        b. Gå till verktygsfältet i huvudfönstret och klicka på **Kopiera**.

        c. Navigera till en annan filresurs i den vänstra rutan och dubbelklicka på den för att visa den i huvudfönstret.

        d. Gå till verktygsfältet i huvudfönstret och klicka på **Klistra in** för att skapa en kopia av filen.

    - **Ta bort en fil**

        a. Markera den fil som du vill ta bort.

        b. Gå till verktygsfältet i huvudfönstret och klicka på **Ta bort**.

        c. Välj **Ja** i bekräftelsedialogen.

## <a name="next-steps"></a>Nästa steg

- Visa [viktig information och videor för den senaste Storage Explorer-versionen](https://www.storageexplorer.com/).

- Läs mer om hur du [skapar program med Azure-blobbar, tabeller köer och filer](https://azure.microsoft.com/documentation/services/storage/).