---
title: Skapa och Använd en Azure Files resurs på virtuella Windows-datorer
description: Skapa och Använd en Azure Files-resurs i Azure Portal. Anslut den till en virtuell Windows-dator, Anslut till fil resursen och ladda upp en fil till fil resursen.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4c5629f80c37c9f79dc9a39c4d8304acbee9679d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489582"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Snabb start: skapa och hantera Azure Files resurs med virtuella Windows-datorer

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
1. Välj **Filer** i lagringskontots fönster.

    ![Välj Filer](./media/storage-files-quick-create-use-windows/click-files.png)

1. Välj **fil resurs**.

    ![Välj knappen för att lägga till filresurs](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Ge den nya filresursen namnet *qsfileshare* > ange ”1” som **Kvot** > välj **Skapa**. Kvoten kan vara högst 5 TiB, men du behöver bara 1 GiB för den här snabbstarten.
1. Skapa en ny txt-fil med namnet *qsTestFile* på den lokala datorn.
1. Välj den nya filresursen och välj sedan **Överför** på filresursens plats.

    ![Ladda upp en fil](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Bläddra till platsen där du skapade din txt-fil > välj *qsTestFile.txt* > välj **Överför**.

Nu har du skapat ett Azure Storage-konto och en filresurs med en fil i Azure. Därefter ska du skapa den virtuella Azure-datorn i Windows Server 2016 Datacenter som representerar den lokala servern i den här snabbstarten.

### <a name="deploy-a-vm"></a>Distribuera en virtuell dator

1. Expandera sedan menyn till vänster om portalen och välj **Skapa en resurs** i det övre vänstra hörnet i Azure-portalen.
1. Sök efter och välj **Windows Server 2016 Data Center** i sökrutan ovanför **Azure Marketplace** -resurser och välj sedan **skapa**.
1. På fliken **Grundinställningar** under **Projektinformation** väljer du den resursgrupp som du skapade för snabbstarten.

   ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Under **Instansinformation** ger du den virtuella datorn namnet *qsVM*.
1. Lämna standardinställningar för **Region**, **Tillgänglighetsalternativ**, **Avbildning** och **Storlek**.
1. Under **Administratörskonto** lägger du till *VMadmin* som **Användarnamn** och anger ett **Lösenord** för den virtuella datorn.
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
1. Kopiera innehållet i den andra rutan och klistra in det i **Anteckningar**.

   ![Skärm bild som visar innehållet i rutan sekunder som ska kopieras och klistras in i anteckningar.](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. I den virtuella datorn öppnar du **Utforskaren** och väljer **Den här datorn** i fönstret. Detta ändrar menyerna i menyfliksområdet. Under menyn **Dator** väljer du **Mappa nätverksenhet**.
1. Välj enhetsbeteckningen och ange UNC-sökvägen. Om du har följt namn förslaget i den här snabb starten kopierar du *\\ qsstorageacct. File. Core. Windows. Net\qsfileshare* från **anteckningar**.

   Kontrollera att båda kryssrutorna är markerade.

   ![En skärmbild av dialogrutan "Anslut nätverksenhet"](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Välj **Slutför**.
1. I dialogrutan **Windows-säkerhet**:

   - Kopiera lagringskontonamnet som ska läggas till i AZURE\ från Anteckningar och klistra in det i dialogrutan **Windows-säkerhet** som användarnamn. Om du har följt namnförslagen i den här snabbstarten kopierar du *AZURE\qsstorageacct*.
   - Kopiera lagringskontonyckeln från Anteckningar och klistra in den i dialogrutan **Windows-säkerhet** som lösenord.

      ![UNC-sökvägen från fönstret Anslut i Azure Files](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Skapa en ögonblicksbild av en resurs

Nu när du har mappat enheten kan du skapa en ögonblicksbild.

1. I portalen går du till filresursen och väljer **Skapa ögonblicksbild**.

   ![Skapa en ögonblicksbild](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. I den virtuella datorn öppnar du *qstestfile.txt* och skriver ”den här filen har ändrats” > Spara och stäng filen.
1. Skapa en annan ögonblicksbild.

## <a name="browse-a-share-snapshot"></a>Bläddra i en resursögonblicksbild

1. Välj **Visa ögonblicksbilder** i din filresurs.
1. I fönstret **Ögonblicksbilder av filresurs** väljer du den första ögonblicksbilden i listan.

   ![Vald ögonblicksbild i listan med tidsstämplar](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. I fönstret för denna ögonblicksbild väljer du *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Återställa från en ögonblicksbild

1. Från bladet för filresursens ögonblicksbild högerklickar du på *qsTestFile* och väljer knappen **Återställ**.
1. Välj **Skriv över originalfilen**.

   ![Knapparna Ladda ned och Återställ](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Öppna filen i den virtuella datorn. Den oförändrade versionen har återställts.

## <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild

1. Välj **Visa ögonblicksbilder** i din filresurs.
1. I fönstret **Ögonblicksbilder av filresurs** väljer du den sista ögonblicksbilden i listan och klickar på **Ta bort**.

   ![Knappen Ta bort](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

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

   ![Knappen Återställ i varnings meddelandet ](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png) : om filen inte har ändrats visas inte en tidigare version av filen eftersom den är samma version som ögonblicks bilden. Detta är konsekvent med hur det fungerar på en Windows-filserver.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda en Azure-filresurs i Windows](storage-how-to-use-files-windows.md)
