---
title: Hantering av Azure Stack Edge Pro GPU-resurs | Microsoft Docs
description: Beskriver hur du använder Azure Portal för att hantera resurser på din Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b79cb1b195d35c1e25dd750476c0dacb296f8010
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102638823"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Använd Azure Portal för att hantera resurser på din Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du hanterar resurser i Azure Stack Edge Pro. Du kan hantera Azure Stack Edge Pro via Azure Portal eller via det lokala webb gränssnittet. Använd Azure Portal för att lägga till, ta bort, uppdatera resurser eller synkronisera lagrings nyckeln för lagrings kontot som är kopplat till resurserna. Den här artikeln gäller Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter.

## <a name="about-shares"></a>Om resurser

Om du vill överföra data till Azure måste du skapa resurser på Azure Stack Edge Pro. De resurser som du lägger till i Azure Stack Edge Pro-enheten kan vara lokala resurser eller resurser som skickar data till molnet.

 - **Lokala resurser**: Använd dessa resurser när du vill att data ska bearbetas lokalt på enheten.
 - **Resurser**: Använd de här resurserna när du vill att enhets data ska skickas automatiskt till ditt lagrings konto i molnet. Alla moln funktioner, till exempel **lagrings nycklar** för **uppdatering** och synkronisering, gäller för resurserna.


## <a name="add-a-share"></a>Lägga till en resurs

Skapa en resurs genom att utföra stegen nedan på Azure-portalen.

1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **Cloud Storage Gateway-> resurser**. Välj **+ Lägg till resurs** i kommando fältet.

    ![Välj Lägg till resurs](media/azure-stack-edge-gpu-manage-shares/add-share-1.png)

2. I **Lägg till resurs** anger du inställningar för resursen. Ange ett unikt namn på resursen.
    
    Resursnamn får bara innehålla siffror, gemener och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.

3. Välj en **typ** för resursen. Typen kan vara **SMB** eller **NFS**, med SMB som standard. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

4. Ange ett **Lagringskonto** där resursen ska placeras. En container skapas på lagringskontot med resursnamnet om containern inte redan finns. Om containern redan finns används den befintliga containern.

5. I list rutan väljer du **lagrings tjänsten** från Block Blob, Page BLOB eller Files. Vilken typ av tjänst som väljs beror på vilket format du vill använda för data som lagras i Azure. I den här instansen vill vi till exempel att data ska finnas som block-blobbar i Azure, och därför väljer vi **Block-Blob**. Om du väljer **Page BLOB** måste du se till att dina data är 512 byte justerade. Använd **Page BLOB** för virtuella hård diskar eller VHDX som alltid är 512 byte-justerade.

6. Det här steget beror på om du skapar en SMB- eller en NFS-resurs.
    - **Om du skapar en SMB-resurs** – I fältet för **lokal användare med fullständig behörighet** väljer du mellan **Skapa ny** eller **Använd befintlig**. Om du skapar en ny lokal användare anger du **användarnamn**, **lösenord** och sedan bekräfta lösenord. Detta tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.

        ![Lägga till SMB-resurs](media/azure-stack-edge-gpu-manage-shares/add-smb-share.png)

        Om du markerar alternativet för att endast tillåta läsåtgärder för dessa resursdata kan du ange skrivskyddade användare.
    - **Om du skapar en NFS-resurs** – Du måste ange **IP-adresserna för de tillåtna klienter** som har åtkomst till resursen.

        ![Lägga till NFS-resurs](media/azure-stack-edge-gpu-manage-shares/add-nfs-share.png)

7. Använd den lokala monterings punkten för att enkelt få åtkomst till resurserna från Edge Compute-moduler. Välj **Använd resurs med Edge Compute** så att resursen monteras automatiskt när den har skapats. När det här alternativet är markerat kan Edge-modulen också använda beräkningen med den lokala monterings punkten.

8. Klicka på **skapa** för att skapa resursen. Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna **uppdateras delnings bladet** så att det återspeglar den nya resursen.

## <a name="add-a-local-share"></a>Lägg till en lokal resurs

1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **Cloud Storage Gateway-> resurser**. Välj **+ Lägg till resurs** i kommando fältet.

    ![Välj Lägg till resurs 2](media/azure-stack-edge-gpu-manage-shares/add-local-share-1.png)

2. I **Lägg till resurs** anger du inställningar för resursen. Ange ett unikt namn på resursen.
    
    Resurs namn får bara innehålla siffror, gemener och versaler och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.

3. Välj en **typ** för resursen. Typen kan vara **SMB** eller **NFS**, med SMB som standard. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

   > [!IMPORTANT]
   > Kontrol lera att Azure Storages kontot som du använder inte har oföränderlighets-principer inställda på det om du använder det med en Azure Stack Edge Pro-eller Data Box Gateway-enhet. Mer information finns i [Ange och hantera oföränderlighets-principer för Blob Storage](../storage/blobs/storage-blob-immutability-policies-manage.md).

4. Använd den lokala monterings punkten för att enkelt få åtkomst till resurserna från Edge Compute-moduler. Välj **Använd resurs med Edge Compute** så att Edge-modulen kan använda beräkningen med den lokala monterings punkten.

5. Välj **Konfigurera som lokala resurser i Edge**. Data i lokala resurser förblir lokalt på enheten. Du kan bearbeta dessa data lokalt.

6. I fältet **all behörighet lokal användare** väljer du från **Skapa ny** eller **Använd befintlig**.

7. Välj **Skapa**. 

    ![Skapa lokal resurs](media/azure-stack-edge-gpu-manage-shares/add-local-share-2.png)

    Du ser ett meddelande om att delningen håller på att skapas. När resursen har skapats med de angivna inställningarna **uppdateras delnings bladet** så att det återspeglar den nya resursen.

    ![Visa blad för uppdaterings resurser](media/azure-stack-edge-gpu-manage-shares/add-local-share-3.png)
    
    Välj resursen för att visa den lokala monterings punkt för Edge Compute-modulerna för den här resursen.

    ![Visa information om lokala resurser](media/azure-stack-edge-gpu-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Montera en resurs

Om du har skapat en resurs innan du konfigurerade Compute på din Azure Stack Edge Pro-enhet måste du montera resursen. Utför följande steg för att montera en resurs.


1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **Cloud Storage Gateway-> resurser**. Välj den resurs som du vill montera från listan över resurser. I kolumnen **används för beräkning** visas statusen som **inaktive rad** för den valda resursen.

    ![Välja resurs](media/azure-stack-edge-gpu-manage-shares/mount-share-1.png)

2. Välj **montera**.

    ![Välj montera](media/azure-stack-edge-gpu-manage-shares/mount-share-2.png)

3. Välj **Ja** när du uppmanas att bekräfta. Detta kommer att montera resursen.

    ![Bekräfta montering](media/azure-stack-edge-gpu-manage-shares/mount-share-3.png)

4. När resursen har monterats går du till listan över resurser. Du ser att den **använda för beräknings** kolumnen visar resurs status som **aktive rad**.

    ![Resurs monterad](media/azure-stack-edge-gpu-manage-shares/mount-share-4.png)

5. Välj resursen igen om du vill visa den lokala monterings punkt för resursen. Edge Compute module använder den här lokala monterings punkt för resursen.

    ![Lokala monterings punkt för resursen](media/azure-stack-edge-gpu-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Demontera en resurs

Utför följande steg i Azure Portal för att demontera en resurs.

1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **Cloud Storage Gateway-> resurser**. I listan över resurser väljer du den resurs som du vill demontera. Du vill se till att resursen som du demonterar inte används av några moduler. Om resursen används av en modul visas problem med motsvarande modul.

    ![Välj resurs 2](media/azure-stack-edge-gpu-manage-shares/unmount-share-1.png)

2.  Välj **demontera**.

    ![Välj demontera](media/azure-stack-edge-gpu-manage-shares/unmount-share-2.png)

3. Välj **Ja** när du uppmanas att bekräfta. Detta kommer att demontera resursen.

    ![Bekräfta demontering](media/azure-stack-edge-gpu-manage-shares/unmount-share-3.png)

4. När resursen har demonterats går du till listan över resurser. Nu visas resurs status som **inaktive rad** i **beräknings** kolumnen.

    ![Dela demontera](media/azure-stack-edge-gpu-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Ta bort en resurs

Utför följande steg på Azure-portalen om du vill ta bort en resurs.

1. Välj från listan över resurser, och klicka på den resurs som du vill ta bort.

    ![Välj resurs 3](media/azure-stack-edge-gpu-manage-shares/delete-share-1.png)

2. Klicka på **Ta bort**.

    ![Klicka på Ta bort](media/azure-stack-edge-gpu-manage-shares/delete-share-2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

    ![Bekräfta borttagning](media/azure-stack-edge-gpu-manage-shares/delete-share-3.png)

Listan över resurser som uppdateras för att återspegla borttagningen.

## <a name="refresh-shares"></a>Uppdatera resurser

Med uppdaterings funktionen kan du uppdatera innehållet i en resurs. När du uppdaterar en resurs initieras en sökning för att hitta alla Azure-objekt, inklusive blobar och filer som har lagts till i molnet sedan den senaste uppdateringen. De här ytterligare filerna hämtas sedan för att uppdatera innehållet i resursen på enheten.

> [!IMPORTANT]
> - Du kan inte uppdatera lokala resurser.
> - Behörigheter och åtkomst kontrol listor (ACL: er) bevaras inte i en uppdaterings åtgärd. 

Utför följande steg på Azure-portalen om du vill uppdatera en resurs.

1.  Gå till **Resurser** i Azure-portalen. Välj och klicka på den resurs som du vill uppdatera.

    ![Välj resurs 4](media/azure-stack-edge-gpu-manage-shares/refresh-share-1.png)

2.  Klicka på **Uppdatera**. 

    ![Klicka på Uppdatera](media/azure-stack-edge-gpu-manage-shares/refresh-share-2.png)
 
3.  Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Ett jobb startas för att uppdatera innehållet i den lokala resursen.

    ![Bekräfta uppdatering](media/azure-stack-edge-gpu-manage-shares/refresh-share-3.png)
 
4.   Medan uppdateringen pågår nedtonas uppdateringsalternativet i snabbmenyn. Klicka på meddelandet för jobbet för att visa jobbstatus för uppdateringen.

5.   Tiden för att uppdatera bestäms av antalet filer i Azure-containern samt filerna på enheten. När uppdateringen är klar uppdateras tidsstämpeln för resursen. Även om uppdateringen delvis har misslyckats anses åtgärden ha lyckats och tidsstämpeln uppdateras. Uppdaterings fel loggarna uppdateras också.

![Uppdaterad tidsstämpel](media/azure-stack-edge-gpu-manage-shares/refresh-share-4.png)
 
Om ett fel uppstår genereras en avisering. Aviseringen innehåller detaljerad information om orsaken och rekommendationen för att åtgärda problemet. Aviseringen innehåller också en länk till en fil med en fullständig sammanfattning av felen, inklusive de filer som inte kunde uppdateras eller tas bort.

## <a name="sync-pinned-files"></a>Synkronisera fästa filer 

Om du vill synkronisera fästa filer automatiskt utför du följande steg i Azure Portal:
 
1. Välj ett befintligt Azure Storage-konto. 

2. Gå till **behållare** och välj **+ container** för att skapa en behållare. Namnge den här behållaren som *newcontainer*. Ange **offentlig åtkomst nivå** till behållare.

    ![Automatiserad synkronisering för fästa filer 1](media/azure-stack-edge-gpu-manage-shares/image-1.png)

3. Välj behållarens namn och ange följande metadata:  

    - Name = "fästa" 
    - Värde = "true" 

    ![Automatiserad synkronisering för fästa filer 2](media/azure-stack-edge-gpu-manage-shares/image-2.png)
 
4. Skapa en ny resurs på enheten. Mappa den till den fästa behållaren genom att välja alternativet befintlig behållare. Markera resursen som skrivskyddad. Skapa en ny användare och ange användar namnet och motsvarande lösen ord för den här resursen.  

    ![Automatiserad synkronisering för fästa filer 3](media/azure-stack-edge-gpu-manage-shares/image-3.png)
 
5. Från Azure Portal bläddrar du till den behållare som du skapade. Ladda upp filen som du vill fästa i newcontainer som har de metadata som har angetts till fäst. 

6. Välj **Uppdatera data** i Azure Portal för att enheten ska kunna hämta fäst principen för den specifika Azure Storage containern.  

    ![Automatiserad synkronisering för fästa filer 4](media/azure-stack-edge-gpu-manage-shares/image-4.png)
 
7. Öppna den nya resurs som skapades på enheten. Filen som överfördes till lagrings kontot laddas nu ned till den lokala resursen. 

    När enheten är frånkopplad eller Återansluten, utlöses uppdateringen. Uppdateringen kommer bara att ta bort de filer som har ändrats. 


## <a name="sync-storage-keys"></a>Synkronisera lagringsnycklar

Om dina lagringskontonycklar har roterats måste du synkronisera åtkomstnycklarna för lagring. Synkroniseringen hjälper enheten att hämta de senaste nycklarna för ditt lagringskonto.

Utför följande steg i Azure-portalen för att synkronisera din lagringsåtkomstnyckel.

1. Gå till **Översikt** i din resurs. Välj från listan över resurser och klicka på en resurs som är associerad med det lagringskonto som du vill synkronisera.

    ![Välj Dela med relevant lagrings konto](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-1.png)

2. Klicka på **Synkronisera lagringsnyckel**. Klicka på **Ja** när du uppmanas att bekräfta.

     ![Välj synkronisera lagrings nyckel](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-2.png)

3. Avsluta dialogrutan när synkroniseringen är klar.

>[!NOTE]
> Du behöver bara utföra åtgärden en gång för ett givet lagringskonto. Du behöver inte upprepa åtgärden för alla resurser som är associerade med samma lagringskonto.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](azure-stack-edge-gpu-manage-users.md).