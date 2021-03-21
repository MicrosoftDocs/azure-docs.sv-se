---
title: Hantera resurser i Azure Data Box Gateway | Microsoft Docs
description: Beskriver hur du använder Azure-portalen för att hantera resurser i Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 8a6bfb1c6aa268914e6dd1157115067b15bfa404
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786918"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-gateway"></a>Använda Azur-portalen för att hantera resurser i Azure Data Box Gateway 

Den här artikeln beskriver hur du hanterar resurser i din Azure Data Box Gateway. Du kan hantera Azure Data Box Gateway via Azure-portalen eller via det lokala webbgränssnittet. Använd Azure-portalen för att lägga till, ta bort och uppdatera resurser eller för att synkronisera lagringsnyckeln för det lagringskonto som är associerat med resurserna.

## <a name="about-shares"></a>Om resurser

Om du vill överföra data till Azure måste du skapa resurser på din Azure Data Box-gateway. De resurser som du lägger till på Data Box Gateway-enheten blir molnresurser. Data från dessa resurser överförs automatiskt till molnet. Alla funktioner i molnet, som uppdatering och synkronisering av lagringsnycklar, gäller för dessa resurser. Använd molnresurserna när du vill att enhetsdata ska skickas automatiskt till ditt lagringskonto i molnet.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ta bort en resurs
> * Uppdatera resurser
> * Synkronisera lagringsnyckel


## <a name="add-a-share"></a>Lägga till en resurs

Skapa en resurs genom att utföra stegen nedan på Azure-portalen.

1. I Azure Portal går du till Data Box Gateway resurs och navigerar sedan till **Översikt**. Klicka på **+ Lägg till resurs** i kommandofältet.
2. I **Lägg till resurs** anger du inställningar för resursen. Ange ett unikt namn på resursen. 

    ![Klicka på Lägg till resurs](media/data-box-gateway-manage-shares/add-share-1.png)

    Resursnamn får bara innehålla siffror, gemener och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.

3. Välj en **typ** för resursen. Typen kan vara **SMB** eller **NFS**, med SMB som standard. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

4. Ange ett **Lagringskonto** där resursen ska placeras. En container skapas på lagringskontot med resursnamnet om containern inte redan finns. Om containern redan finns används den befintliga containern.  

5. Välj **lagringstjänst** – blockblob, sidblob eller filer. Vilken typ av tjänst som väljs beror på vilket format du vill använda för data som lagras i Azure. I det här fallet vill vi till exempel att data ska lagras som blob-block i Azure, därför väljer vi **Blockblob**. Om du väljer **Sidblob** måste du kontrollera att dina data är justerade för 512 byte. Till exempel är en VHDX alltid justerad för 512 byte.

   > [!IMPORTANT]
   > Kontrol lera att Azure Storage-kontot som du använder inte har oföränderlighets-principer inställda på det om du använder det med en Data Box Gateway-enhet. Mer information finns i [Ange och hantera oföränderlighets-principer för Blob Storage](../storage/blobs/storage-blob-immutability-policies-manage.md).

6. Det här steget beror på om du skapar en SMB- eller en NFS-resurs.
    - **Om du skapar en SMB-resurs** – I fältet för **lokal användare med fullständig behörighet** väljer du mellan **Skapa ny** eller **Använd befintlig**. Om du skapar en ny lokal användare anger du **användarnamn**, **lösenord** och sedan bekräfta lösenord. Detta tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.

        ![Lägga till SMB-resurs](media/data-box-gateway-manage-shares/add-share-2.png)

        Om du markerar alternativet för att endast tillåta läsåtgärder för dessa resursdata kan du ange skrivskyddade användare.
    - **Om du skapar en NFS-resurs** – Du måste ange **IP-adresserna för de tillåtna klienter** som har åtkomst till resursen.

        ![Lägga till NFS-resurs](media/data-box-gateway-manage-shares/add-share-3.png)

7. Klicka på **skapa** för att skapa resursen. Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna **uppdateras delnings bladet** så att det återspeglar den nya resursen.
 
## <a name="delete-a-share"></a>Ta bort en resurs

Utför följande steg på Azure-portalen om du vill ta bort en resurs.

1. Välj från listan över resurser, och klicka på den resurs som du vill ta bort.

    ![Välja resurs](media/data-box-gateway-manage-shares/delete-1.png)

2. Klicka på **Ta bort**. 

    ![Klicka på Ta bort](media/data-box-gateway-manage-shares/delete-2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

    ![Bekräfta borttagning](media/data-box-gateway-manage-shares/delete-3.png)

Listan över resurser uppdateras för att återspegla borttagningen.


## <a name="refresh-shares"></a>Uppdatera resurser

Med uppdateringsfunktionen kan du uppdatera innehållet i en lokal resurs. När du uppdaterar en resurs initieras en sökning för att hitta alla Azure-objekt, inklusive blobar och filer som har lagts till i molnet sedan den senaste uppdateringen. De nya filerna används sedan för att uppdatera innehållet i den lokala resursen på enheten. 

> [!NOTE]
> Behörigheter och åtkomst kontrol listor (ACL: er) bevaras inte i en uppdaterings åtgärd. 

Utför följande steg på Azure-portalen om du vill uppdatera en resurs.

1. Gå till **Resurser** i Azure-portalen. Välj och klicka på den resurs som du vill uppdatera.

   ![Välj resurs 2](media/data-box-gateway-manage-shares/refresh-1.png)

2. Klicka på **Uppdatera**. 

   ![Klicka på Uppdatera](media/data-box-gateway-manage-shares/refresh-2.png)
 
3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Ett jobb startas för att uppdatera innehållet i den lokala resursen. 

   ![Bekräfta uppdatering](media/data-box-gateway-manage-shares/refresh-3.png)
 
4.   Medan uppdateringen pågår nedtonas uppdateringsalternativet i snabbmenyn. Klicka på meddelandet för jobbet för att visa jobbstatus för uppdateringen.

5. Tiden för att uppdatera bestäms av antalet filer i Azure-containern samt filerna på enheten. När uppdateringen är klar uppdateras tidsstämpeln för resursen. Även om uppdateringen delvis har misslyckats anses åtgärden ha lyckats och tidsstämpeln uppdateras. 

   ![Uppdaterad tidsstämpel](media/data-box-gateway-manage-shares/refresh-4.png)
 
Om ett fel uppstår genereras en avisering. Aviseringen innehåller detaljerad information om orsaken och rekommendationen för att åtgärda problemet. Aviseringen innehåller också en länk till en fil med en fullständig sammanfattning av felen, inklusive de filer som inte kunde uppdateras eller tas bort.

>[!IMPORTANT]
> I den här versionen ska du inte uppdatera mer än en enskild resurs åt gången.

## <a name="sync-storage-keys"></a>Synkronisera lagringsnycklar

Om dina lagringskontonycklar har roterats måste du synkronisera åtkomstnycklarna för lagring. Synkroniseringen hjälper enheten att hämta de senaste nycklarna för ditt lagringskonto.

Utför följande steg i Azure-portalen för att synkronisera din lagringsåtkomstnyckel.

1. Gå till **Översikt** i din resurs. 
2. I listan över resurser väljer du och klickar på en resurs som är kopplad till det lagrings konto som du behöver synkronisera. Klicka på **Synkronisera lagrings nyckel**. 

     ![Synkronisera lagringsnyckel](media/data-box-gateway-manage-shares/sync-storage-key-1.png)

3. Klicka på **Ja** när du uppmanas att bekräfta. Avsluta dialogrutan när synkroniseringen är klar.

     ![Synkronisera lagrings nyckel 2](media/data-box-gateway-manage-shares/sync-storage-key-2.png)

>[!NOTE]
> Du behöver bara utföra åtgärden en gång för ett givet lagringskonto. Du behöver inte upprepa åtgärden för alla resurser som är associerade med samma lagringskonto.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](data-box-gateway-manage-users.md).