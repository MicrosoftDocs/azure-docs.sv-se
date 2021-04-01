---
title: Testa din app i Azure | Microsoft Docs
description: Lär dig hur du skapar en fil resurs i ett labb och monterar den på din lokala dator och en virtuell dator i labbet. distribuera sedan Skriv bord/webb program till fil resursen och testa dem.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2dbbf349da4e352fe20a22db03cc9063d801990
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282253"
---
# <a name="test-your-app-in-azure"></a>Testa appen i Azure 
Den här artikeln innehåller steg för att testa ditt program i Azure med DevTest Labs. Först skapar du en fil resurs i ett labb och monterar den som en enhet på din lokala utvecklings dator och en virtuell dator i ett labb. Sedan använder du Visual Studio 2019 för att distribuera din app till fil resursen så att du kan köra appen på den virtuella datorn i labbet.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar 
1. [Skapa en Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en och logga in på [Azure Portal](https://portal.azure.com).
2. Följ anvisningarna i [den här artikeln](devtest-lab-create-lab.md) för att skapa ett labb med hjälp av Azure DevTest Labs. Fäst labbet på instrument panelen så att du enkelt kan hitta den nästa gången du loggar in. Med Azure DevTest Labs kan du snabbt skapa resurser i Azure genom att minimera spill och kontrol lera kostnaden. Mer information om DevTest Labs finns i [Översikt](devtest-lab-overview.md). 
3. Skapa ett Azure Storage-konto i Labbets resurs grupp genom att följa anvisningarna i artikeln [skapa ett lagrings konto](../storage/common/storage-account-create.md) . På sidan **skapa lagrings konto** väljer du **Använd befintlig** för **resurs grupp** och väljer sedan **Labbets resurs grupp**. 
4. Skapa en fil resurs i Azure Storage genom att följa anvisningarna i artikeln [skapa en fil resurs i Azure Files](../storage/files/storage-how-to-create-file-share.md) . 

## <a name="mount-the-file-share-on-your-local-machine"></a>Montera fil resursen på den lokala datorn
1. Använd skriptet från [montera avsnittet Azure-filresurs](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) för att [använda en Azure-filresurs med Windows](../storage/files/storage-how-to-use-files-windows.md) -artikel på den lokala datorn. 
2. Använd sedan `net use` kommandot för att montera fil resursen på din dator. Här är exempel kommandot: Ange ditt Azure Storage-namn och fil resurs namn innan du kör kommandot. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Skapa en virtuell dator i labbet
1. På sidan **fil resurs** väljer du **resurs gruppen** på menyn för dynamiska länkar överst. Du ser sidan **resurs grupp** . 
    
    ![Välj resurs grupp från menyn för dynamiska länkar](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. På sidan **resurs grupp** väljer du det **labb** du skapade i DevTest Labs.

    ![Välja labbet](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. På **DevTest Lab** -sidan för ditt labb väljer du **+ Lägg till** i verktygsfältet. 

    ![Knappen Lägg till för labbet](media/test-app-in-azure/add-button-in-lab.png)
4. På sidan **Välj en bassida** söker du efter **smalldisk** och väljer **[Smalldisk] Windows Server 2016 Data Center**. 

    ![Välj liten disk Windows Server](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. På sidan **virtuell dator** anger du namnet på den **virtuella datorn**, **användar namn**, **lösen ord** och väljer **skapa**.    
    
    ![Sidan Skapa virtuell dator](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Montera fil resursen på den virtuella datorn
1. När den virtuella datorn har skapats väljer du den **virtuella datorn** i listan.    

    ![Välj den virtuella labb datorn](media/test-app-in-azure/select-lab-vm.png)
2. Välj **Anslut** i verktygsfältet för att ansluta till den virtuella datorn. 
3. [Installera Azure PowerShell](/powershell/azure/install-az-ps).
4. Följ anvisningarna i avsnittet montera fil resursen. 

## <a name="publish-your-app-from-visual-studio"></a>Publicera din app från Visual Studio
I det här avsnittet ska du publicera din app från Visual Studio till en virtuell test dator i molnet.

1. Skapa ett skriv bord/webb program med hjälp av Visual Studio 2019.
2. Skapa din app.
3. Om du vill publicera din app högerklickar du på projektet i **Solution Explorer** och väljer **publicera**. 
4. I **publicerings guiden** anger du den **enhet** som är mappad till fil resursen.

    **Desktop-app:**

    ![Skrivbordsapp](media/test-app-in-azure/desktop-app.png)

    **Webbapp:**

    ![Webbapp](media/test-app-in-azure/web-app.png)

1. Klicka på **Nästa** för att slutföra publicerings arbets flödet och välj **Slutför**. När du har slutfört stegen i guiden skapar Visual Studio ditt program och publicerar det till fil resursen. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testa appen på den virtuella test datorn i labbet

1. Gå till den virtuella dator sidan för din virtuella dator i labbet. 
2. Välj **Starta** i verktygsfältet för att starta den virtuella datorn om den är i stoppat läge. Du kan konfigurera principer för automatisk start och automatisk avstängning för den virtuella datorn för att undvika att starta och stoppa varje gång. 
3. Välj **Anslut**.

    ![Sidan virtuell dator](media/test-app-in-azure/virtual-machine-page.png)
4. Starta **Utforskaren** på den virtuella datorn och välj **den här datorn** för att hitta fil resursen.

    ![Hitta resurs på virtuell dator](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Om du inte kan hitta fil resursen på den virtuella datorn eller på den lokala datorn kan du av någon anledning montera den igen genom att köra `net use` kommandot. Du hittar `net use` kommandot i guiden **Anslut** i **fil resursen** i Azure Portal.
1. Öppna fil resursen och bekräfta att du ser appen du distribuerade från Visual Studio. 

    ![Öppen resurs på virtuell dator](media/test-app-in-azure/open-file-share.png)

    Nu kan du komma åt och testa din app i den virtuella test datorn som du skapade i Azure.

## <a name="next-steps"></a>Nästa steg
I följande artiklar finns information om hur du använder virtuella datorer i ett labb. 

- [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)
- [Starta om en virtuell labb dator](devtest-lab-restart-vm.md)
- [Ändra storlek på en virtuell labb dator](devtest-lab-resize-vm.md)
