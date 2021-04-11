---
title: Självstudie för att överföra data till resurser med Azure Stack Edge Pro | Microsoft Docs
description: I den här självstudien får du lära dig hur du lägger till och ansluter till resurser på din Azure Stack Edge Pro-enhet, så att Azure Stack Edge Pro kan överföra data till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: 8c9ad00a8910562e1a3a53af5120030bc482c927
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060218"
---
# <a name="tutorial-transfer-data-with-azure-stack-edge-pro"></a>Självstudie: överföra data med Azure Stack Edge Pro

I den här självstudien beskrivs hur du lägger till och ansluter till resurser på din Azure Stack Edge Pro-enhet. När du har lagt till resurserna kan Azure Stack Edge Pro överföra data till Azure.

Den här proceduren kan ta upp till 10 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resursen

 
## <a name="prerequisites"></a>Förutsättningar

Innan du lägger till resurser i Azure Stack Edge Pro, se till att:

- Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Pro](azure-stack-edge-deploy-install.md).

- Du har aktiverat den fysiska enheten enligt beskrivningen i [ansluta, konfigurera och aktivera Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Lägga till en resurs

Om du vill skapa en resurs, gör du så här:

1. I [Azure Portal](https://portal.azure.com/)väljer du din Azure Stack Edge-resurs och går sedan till **översikten**. Enheten bör vara online. Välj **Cloud Storage Gateway**.

   ![Enhet online](./media/azure-stack-edge-deploy-add-shares/device-online-1.png)

2. Välj **+ Lägg till resurs** i enhetens kommando fält.

   ![Lägga till en resurs](./media/azure-stack-edge-deploy-add-shares/select-add-share-1.png)

3. I fönstret **Lägg till resurs** gör du följande:

    a. Ange ett unikt namn på resursen i rutan **Namn**.  
    Resursnamnet får bara ha gemena bokstäver, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken och börja med en bokstav eller en siffra. Bindestreck måste föregås och följas av en bokstav eller ett numeriskt värde.
    
    b. Välj en **typ** för resursen.  
    Typen kan vara **SMB** eller **NFS**, med SMB som standard. SMB är standard för Windows-klienter och NFS används för Linux-klienter.  
    Beroende på om du väljer SMB- eller NFS-resurser varierar resten av alternativen något. 

    c. Ange ett lagringskonto där resursen ska placeras. 

    > [!IMPORTANT]
    > Kontrol lera att Azure Storages kontot som du använder inte har oföränderlighets-principer inställda på det om du använder det med en Azure Stack Edge Pro-eller Data Box Gateway-enhet. Mer information finns i [Ange och hantera oföränderlighets-principer för Blob Storage](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    d. Välj **Blockblob**, **Sidblob** eller **Filer** i listrutan **Lagringstjänst**.  
    Vilken typ av tjänst som du väljer beror på vilket format som du vill att dina data ska använda i Azure. I det här exemplet, eftersom vi vill lagra data som block blobbar i Azure väljer du **blockera BLOB**. Om du väljer **Page BLOB** ser du till att dina data är 512 byte justerade. Till exempel är en VHDX alltid justerad för 512 byte.

    e. Skapa en ny BLOB-behållare eller Använd en befintlig i list rutan. Om du skapar en BLOB-behållare anger du ett behållar namn. En container skapas på lagringskontot med det nyligen skapade resursnamnet om det inte redan finns en.
   
    f. Beroende på om du har skapat en SMB-resurs eller en NFS-resurs, gör du något av följande: 
     
    - **SMB-resurs**: Välj **Skapa ny** eller **Använd befintlig** i **lokal användare för behörighet**. Om du skapar en ny lokal användare, anger du ett användarnamn och ett lösenord och bekräftar därefter lösenordet. Denna åtgärd tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren för att modifiera dem.

        Om du markerar kryss rutan **Tillåt endast Läs åtgärder** för den här resurs informationen kan du ange skrivskyddade användare.

        ![Lägga till SMB-resurs](./media/azure-stack-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS-resurs**: Ange IP-adresserna för tillåtna klienter som har åtkomst till resursen.

        ![Lägga till NFS-resurs](./media/azure-stack-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Klicka på **Skapa** för att skapa resursen.
    
    Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna **uppdateras delnings panelen så** att den återspeglar den nya resursen.
    

## <a name="connect-to-the-share"></a>Ansluta till resursen

Du kan nu ansluta till en eller flera av de resurser som du skapade i det sista steget. Beroende på om du har en SMB- eller en NFS-resurs kan stegen variera.

### <a name="connect-to-an-smb-share"></a>Ansluta till en SMB-resurs

På din Windows Server-klient som är ansluten till din Azure Stack Edge Pro-enhet ansluter du till en SMB-resurs genom att ange följande kommandon:


1. I ett kommandofönster skriver du:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Ange lösenordet för resursen när du uppmanas att göra det.  
   Exempel på utdata från det här kommandot visas här.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Välj Windows + R på tangentbordet.

4. I fönstret **Kör** anger du `\\<device IP address>` och väljer sedan **OK**.  
   Utforskaren öppnas. Du bör nu kunna se de resurser du har skapat som mappar. Dubbelklicka på en resurs (mapp) i Utforskaren för att visa innehållet.
 
    ![Ansluta till SMB-resurs](./media/azure-stack-edge-deploy-add-shares/connect-to-share2.png)

    Data skrivs till dessa resurser då de genereras och enheten skickar data till molnet.

### <a name="connect-to-an-nfs-share"></a>Ansluta till en NFS-resurs

Gör följande på Linux-klienten som är ansluten till din Azure Stack Edge Pro-enhet:

1. Kontrollera att klienten har NFSv4-klienten installerad. Om du vill installera NFS-klienten använder du följande kommando:

   `sudo apt-get install nfs-common`

    Mer information finns i [Installera NFSv4-klienten](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. När NFS-klienten har installerats monterar du NFS-resursen som du skapade i Azure Stack Edge Pro-enhet med hjälp av följande kommando:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > `sync`Om du använder alternativet när du monterar resurser förbättras överförings takten för stora filer.
    > Innan du monterar filresurserna kontrollerar du om de kataloger som kommer att fungera som monteringspunkter på den lokala datorn redan har skapats. Dessa kataloger får inte innehålla några filer eller undermappar.

    I följande exempel visas hur du ansluter via NFS till en resurs på din Azure Stack Edge Pro-enhet. Enhetens IP-adress är `10.10.10.60`. `mylinuxshare2`-resursen är monterad på den virtuella Ubuntu-datorn. Resursens monteringspunkt är `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Följande varningar gäller för den här versionen:
> - När en fil har skapats i resursen stöds inte namnbyte av filen. 
> - När en fil tas bort från en resurs försvinner inte posten i lagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om följande Azure Stack Edge Pro-ämnen:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resurs

Om du vill lära dig hur du omvandlar dina data med hjälp av Azure Stack Edge Pro går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Transformera data med Azure Stack Edge Pro](./azure-stack-edge-deploy-configure-compute.md)