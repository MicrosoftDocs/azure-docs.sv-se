---
title: Skapa och använda en Azure Files resurs på virtuella Windows-datorer
description: Skapa och använda en Azure Files resurs i Azure Portal. Anslut den till en virtuell Windows-dator, anslut till Files-resursen och ladda upp en fil till Files-resursen.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5a3c664f6c6c0532ef915357cfbcbc8228202502
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718305"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Snabbstart: Skapa och hantera Azure Files resurs med virtuella Windows-datorer

Artikeln visar de grundläggande stegen för att skapa och använda en Azure Files-resurs. I den här snabbstarten fokuserar vi på att snabbt konfigurera en Azure Files-resurs så att du kan se hur tjänsten fungerar. Om du behöver mer detaljerade instruktioner för att skapa och använda Azure-filresurser i din egen miljö, kan du läsa mer i [Använda en Azure-filresurs med Windows](storage-how-to-use-files-windows.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Förbered din miljö

I den här snabbstarten konfigurerar du följande objekt:

- Skapa ett Azure-lagringskonto och en Azure-filresurs
- En virtuell Windows Server 2016 Datacenter-dator

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Innan du kan arbeta med en Azure-filresurs måste du skapa ett Azure-lagringskonto. Ett v2-lagringskonto för generell användning ger åtkomst till alla Azure Storage-tjänster: blobar, filer, köer och tabeller. Snabbstarten skapar ett v2-lagringskonto för generell användning, men stegen för att skapa alla typer av lagringskonton liknar dessa. Ett lagringskonto kan innehålla ett obegränsat antal resurser. En resurs kan lagra ett obegränsat antal filer, upp till kapacitetsbegränsningen för lagringskontot.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Därefter skapar du en filresurs.

1. När distributionen av Azure-lagringskontot är klar väljer du **Gå till resurs**.
1. Välj **Filresurser** i fönstret lagringskonto.

    ![Välj Filresurser.](./media/storage-files-quick-create-use-windows/click-files.png)

1. Välj **+ Filresurs**.

    ![Välj + filresurs för att skapa en ny filresurs.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Ge den nya filresursen *namnet qsfileshare,* ange "1" för **Kvot,** lämna **Transaktionsoptimerad** markerad och välj **Skapa.** Kvoten kan vara högst 5 TiB (100 TiB med stora filresurser aktiverade), men du behöver bara 1 GiB för den här snabbstarten.
1. Skapa en ny txt-fil med namnet *qsTestFile* på den lokala datorn.
1. Välj den nya filresursen och välj sedan **Överför** på filresursens plats.

    ![Ladda upp en fil.](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Bläddra till platsen där du skapade din txt-fil > välj *qsTestFile.txt* > välj **Överför**.

Nu har du skapat ett Azure Storage-konto och en filresurs med en fil i Azure. Därefter ska du skapa den virtuella Azure-datorn i Windows Server 2016 Datacenter som representerar den lokala servern i den här snabbstarten.

### <a name="deploy-a-vm"></a>Distribuera en virtuell dator

1. Expandera sedan menyn till vänster om portalen och välj **Skapa en resurs** i det övre vänstra hörnet i Azure-portalen.
1. I sökrutan ovanför listan över virtuella **Azure Marketplace** du efter och väljer **Windows Server 2016 Datacenter.**
1. På fliken **Grundinställningar** under **Projektinformation** väljer du den resursgrupp som du skapade för snabbstarten.

   ![Ange grundläggande information om den virtuella datorn på portalbladet.](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Under **Instansinformation** ger du den virtuella datorn namnet *qsVM*.
1. Lämna standardinställningar för **Region**, **Tillgänglighetsalternativ**, **Avbildning** och **Storlek**.
1. Under **Administratörskonto** lägger du till **ett användarnamn** och anger ett lösenord **för** den virtuella datorn.
1. Under **Regler för inkommande portar** väljer du **Tillåt valda portar** och sedan **RDP (3389)** och **HTTP** från listrutan.
1. Välj **Granska + skapa**.
1. Välj **Skapa**. Det tar några minuter att skapa en ny virtuell dator.

1. När distributionen av den virtuella datorn är klar väljer du **Gå till resurs**.

Nu har du skapat en ny virtuell dator och anslutit en datadisk. Nu måste du ansluta till den virtuella datorn.

### <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

1. Välj **Anslut** på sidan för den virtuella datorns egenskaper.

   ![Ansluta till en virtuell Azure-dator från portalen](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta med **IP-adress** över **portnummer** *3389*. Välj sedan **Hämta RDP-filen**.
1. Öppna den nedladdade RDP-filen och välj **Anslut** när du tillfrågas.
1. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange användarnamnet som *localhost\användarnamn* där &lt;användarnamn&gt; är det användarnamn som du skapade för den virtuella datorn. Ange det lösenord som du skapade för den virtuella datorn och välj sedan **OK**.

   ![Fler alternativ](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** för att skapa anslutningen.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mappa Azure-filresursen till en Windows-enhet

1. I Azure-portalen går du till filresursen *qsfileshare* och väljer **Anslut**.
1. Välj en enhetsbeteckning och kopiera sedan innehållet i den andra rutan och klistra in det **i Anteckningar.**

   :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Skärmbild som visar innehållet i rutan som du ska kopiera och klistra in i Anteckningar." lightbox="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png":::

1. Öppna **PowerShell** på den virtuella datorn och klistra in innehållet i **Anteckningar och** tryck sedan på Retur för att köra kommandot. Den bör mappa enheten.

## <a name="create-a-share-snapshot"></a>Skapa en ögonblicksbild av en resurs

Nu när du har mappat enheten kan du skapa en ögonblicksbild.

1. I portalen navigerar du till filresursen, väljer **Ögonblicksbilder och** sedan + Lägg **till ögonblicksbild.**

   ![Välj ögonblicksbilder under avsnittet Åtgärder och välj sedan Lägg till ögonblicksbild.](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. I den virtuella datorn öppnar du *qstestfile.txt* och skriver ”den här filen har ändrats” > Spara och stäng filen.
1. Skapa en annan ögonblicksbild.

## <a name="browse-a-share-snapshot"></a>Bläddra i en resursögonblicksbild

1. På filresursen väljer du **Ögonblicksbilder.**
1. På **bladet Ögonblicksbilder** väljer du den första ögonblicksbilden i listan.

   ![Vald ögonblicksbild i listan med tidsstämplar](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Öppna ögonblicksbilden och välj *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Återställa från en ögonblicksbild

1. Från bladet för filresursens ögonblicksbild högerklickar du på *qsTestFile* och väljer knappen **Återställ**.

    :::image type="content" source="media/storage-files-quick-create-use-windows/restore-share-snapshot.png" alt-text="Skärmbild av bladet för ögonblicksbild, qstestfile har valts, återställning är markerat.":::

1. Välj **Skriv över originalfilen**.

   ![Skärmbild av popup-fönster för återställning, överskrivning av ursprunglig fil har valts.](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Öppna filen i den virtuella datorn. Den oförändrade versionen har återställts.

## <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild

1. På filresursen väljer du **Ögonblicksbilder.**
1. På **bladet Ögonblicksbilder** väljer du den sista ögonblicksbilden i listan och väljer Ta **bort**.

   ![Skärmbild av bladet ögonblicksbilder, den senaste ögonblicksbilden vald, knappen Ta bort markerad.](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Använda en resursögonblicksbild i Windows

Precis som med lokala VSS-ögonblicksbilder, kan du visa ögonblicksbilder från din monterade Azure-filresurs med hjälp av fliken Tidigare versioner.

1. Leta upp den monterade resursen i Utforskaren.

   ![Monterad resurs i Utforskaren](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Välj *qsTestFile.txt* och > högerklicka och välj **Egenskaper** i menyn.

   ![Snabbmeny för en vald katalog](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Välj **Tidigare versioner** för att visa en lista över resursögonblicksbilder för den här katalogen.

1. Välj **Öppna** för att öppna ögonblicksbilden.

   ![Fliken Tidigare versioner](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Återställa från en tidigare version

1. Välj **Återställ**. Åtgärden kopierar innehållet i hela katalogen rekursivt vid tidpunkten då resursögonblicksbilden skapades till den ursprungliga platsen.

   ![Återställningsknapp i varningsmeddelande](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)
    
    > [!NOTE]
    > Om filen inte har ändrats visas ingen tidigare version för filen eftersom filen är samma version som ögonblicksbilden. Detta stämmer överens med hur detta fungerar på en Windows-filserver.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda en Azure-filresurs i Windows](storage-how-to-use-files-windows.md)
