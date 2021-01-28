---
title: Överföra data med Azure Data Box Gateway | Microsoft Docs
description: Lär dig hur du lägger till och ansluter till resurser på din Azure Data Box Gateway. Data Box Gateway enheten kan överföra data till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 0c0ef6157ebf70c896fbac5ff692246e4fad2c14
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937212"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Självstudie: överföra data med Azure Data Box Gateway


## <a name="introduction"></a>Introduktion

I den här artikeln beskrivs hur du lägger till och ansluter till resurser på din Data Box Gateway. När du har lagt till resurserna kan enheten överföra data till Azure.

Den här proceduren kan ta upp till 10 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Lägga till en resurs
> * Ansluta till resurs

## <a name="prerequisites"></a>Förutsättningar

Kontrollera följande innan du lägger till resurser till din Data Box Gateway:

- Du har etablerat en virtuell enhet och anslutit till den enligt beskrivningen i [tillhandahålla en data Box gateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md) eller [etablera en data Box gateway i VMware](data-box-gateway-deploy-provision-vmware.md).

- Du har aktiverat den virtuella enheten som beskrivs i [Anslut och aktiverar din Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- Enheten är redo för att du ska skapa resurser och överföra data.

## <a name="add-a-share"></a>Lägga till en resurs

Gör så här om du vill skapa en resurs:

1. I [Azure Portal](https://portal.azure.com/)väljer du din data Box gateway resurs och går sedan till **Översikt**. Enheten bör vara online. Välj **+ Lägg till resurs** i enhetens kommando fält.
   
   ![Lägga till en resurs](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Gör följande i **Lägg till resurs**:

    1. Ange ett unikt namn på resursen. Resursnamnen får bara innehålla gemener, siffror och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.
    
    2. Välj en **typ** för resursen. Typen kan vara SMB eller NFS, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

    3. Ange ett lagringskonto där resursen ska placeras. En container skapas på lagringskontot med det nyligen skapade resursnamnet om det inte redan finns en. Om containern redan finns används den befintliga containern.
       > [!IMPORTANT]
       > Kontrol lera att Azure Storage-kontot som du använder inte har oföränderlighets-principer inställda på det om du använder det med en Data Box Gateway-enhet. Mer information finns i [Ange och hantera oföränderlighets-principer för Blob Storage](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    4. Välj **lagringstjänst** – blockblob, sidblob eller filer. Vilken typ av tjänst som väljs beror på vilket format du vill använda för data som lagras i Azure. I det här fallet vill vi till exempel att data ska lagras som blob-block i Azure, därför väljer vi Blockblob. Om du väljer Sidblob måste du kontrollera att dina data är justerade för 512 byte. Till exempel är en VHDX alltid justerad för 512 byte.
   
    5. Det här steget beror på om du skapar en SMB- eller en NFS-resurs.
     
    - **SMB-resurs** – **under alla lokala privilegier användare** väljer du **Skapa ny** eller **Använd befintlig**. Om du skapar en ny lokal användare anger du ett **användar namn** och **lösen ord** och **bekräftar sedan lösen ordet**. Den här åtgärden tilldelar behörighet till den lokala användaren. Ändring av behörigheter på resurs nivå stöds inte för närvarande.
    
        ![Lägga till SMB-resurs](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Om du markerar kryss rutan **Tillåt endast Läs åtgärder** för den här resurs informationen kan du ange skrivskyddade användare.
        
    - **NFS-resurs** – ange IP-adresserna för de tillåtna klienter som har åtkomst till resursen.

        ![Lägga till NFS-resurs](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Klicka på **Skapa** för att skapa resursen.
    
    Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna **uppdateras delnings panelen så** att den återspeglar den nya resursen.
    
    ![Panelen uppdaterade resurser](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Ansluta till resursen

Du kan nu ansluta till en eller flera av de resurser som du skapade i det sista steget. Beroende på om du har en SMB- eller en NFS-resurs kan stegen variera.

### <a name="connect-to-an-smb-share"></a>Ansluta till en SMB-resurs

På en Windows Server-klient som är ansluten till en Data Box Gateway ansluter du till en SMB-resurs genom att ange följande kommandon:


1. I ett kommandofönster skriver du:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ange lösenordet för resursen när du tillfrågas. Exempel på utdata från det här kommandot visas här.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Välj Windows + R på tangent bordet. 
3. I fönstret **Kör** anger du `\\<device IP address>` och väljer sedan **OK**. Utforskaren öppnas. Du bör nu kunna se de resurser du har skapat som mappar. Dubbelklicka på en resurs (mapp) i Utforskaren för att visa innehållet.
 
    ![Ansluta till SMB-resurs](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Data skrivs till dessa resurser då de genereras och enheten skickar data till molnet.

### <a name="connect-to-an-nfs-share"></a>Ansluta till en NFS-resurs

Gör följande på Linux-klienten som är ansluten till din enhet:

1. Kontrollera att klienten har NFSv4-klienten installerad. Om du vill installera NFS-klienten använder du följande kommando:

   `sudo apt-get install nfs-common`

    Mer information finns i [Installera NFSv4-klienten](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen som du har skapat på Data Box Gateway-enheten:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Innan du monterar ska du kontrollera att de kataloger som fungerar som monteringspunkter på den lokala datorn redan har skapats och att de inte innehåller några filer eller undermappar.

    I följande exempel visas hur du ansluter via NFS till en resurs på Gateway-enhet. Den virtuella enhetens IP-adress är `10.10.10.60`, resursen `mylinuxshare2` är monterad på ubuntuVM, och monteringspunkten är `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Följande varningar gäller för den här versionen:
> - När en fil har skapats i resurserna stöds inte namnbytet för filen.
> - När en fil tas bort från en resurs försvinner inte posten i lagringskontot.
> - Om du använder `rsync` för att kopiera data stöds inte alternativet `rsync -a`.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Gateway-ämnen som att:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resurs


Gå vidare till nästa självstudie och lär dig hur du administrerar Data Box Gateway.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera en Data Box Gateway](https://aka.ms/dbg-docs)