---
title: Självstudie – Utöka Windows-filservrar med Azure File Sync | Microsoft Docs
description: Lär dig hur du utökar Windows-filservrar med Azure File Sync, från början till slut.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1d2a0d79a5cdd53f8376c088fc986c20908575eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91329472"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Självstudie: Utöka Windows-filservrar med Azure File Sync

I artikeln beskrivs de grundläggande stegen för att utöka lagrings kapaciteten för en Windows-Server med hjälp av Azure File Sync. Även om självstudien har funktioner för Windows Server som en virtuell Azure-dator, gör du vanligt vis den här processen för dina lokala servrar. Instruktioner för hur du distribuerar Azure File Sync i din egen miljö finns i artikeln [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Distribuera tjänsten för synkronisering av lagring
> * Förbereda Windows Server för användning med Azure File Sync
> * Installera Azure File Sync-agenten
> * Registrera Windows Server med tjänsten för synkronisering av lagring
> * Skapa en synkroniseringsgrupp och en molnslutpunkt
> * Skapa en serverslutpunkt

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Förbered din miljö

För den här självstudien behöver du göra följande innan du kan distribuera Azure File Sync:

- Skapa ett Azure Storage-konto och en filresurs
- Konfigurera en virtuell Windows Server 2016 Datacenter-dator
- Förbereda den virtuella Windows Server-datorn för Azure File Sync

### <a name="create-a-folder-and-txt-file"></a>Skapa en mapp och en .txt-fil

På en lokal dator skapar du en mapp med namnet _FilesToSync_ och lägger till en textfil med namnet _mytestdoc.txt_. Du laddar upp den filen till filresursen senare i den här självstudien.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Skapa en filresurs

När du har distribuerat ett Azure Storage-konto skapar du en filresurs.

1. I Azure-portalen väljer du **Gå till resurs**.
1. Välj **Filer** i lagringskontots fönster.

    ![Välj Filer](./media/storage-sync-files-extend-servers/click-files.png)

1. Välj **+ fil resurs**.

    ![Välj knappen för att lägga till filresurs](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Ge den nya filresursen namnet _afsfileshare_. Ange ”1” för **Kvot** och välj sedan **Skapa**. Kvoten kan vara högst 5 TiB men du behöver bara 1 GB för den här kursen.

    ![Ange ett namn och en kvot för den nya filresursen](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Välj den nya filresursen. På filresursplatsen väljer du **Ladda upp**.

    ![Ladda upp en fil](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Bläddra till mappen _FilesToSync_, där du har skapat .txt-filen, välj _mytestdoc.txt_ och välj **Ladda upp**.

    ![Bläddra i filresursen](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Nu har du skapat ett lagringskonto och en filresurs med en fil i det. Nu ska du distribuera en virtuell Azure-dator i Windows Server 2016 Datacenter som representerar den lokala servern i den här självstudien.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Distribuera en virtuell dator och ansluta en datadisk

1. Gå till Azure-portalen och expandera menyn till vänster. Välj **Skapa en resurs** i det övre vänstra hörnet.
1. I sökrutan ovanför listan över **Azure Marketplace**-resurser söker du efter **Windows Server 2016 Datacenter** och väljer det bland resultaten. Välj **Skapa**.
1. Gå till fliken **grundläggande** . Under **projekt information** väljer du den resurs grupp som du skapade för den här självstudien.

   ![Ange grundläggande information om din virtuella datorer på portalbladet](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Under **Instansinformation** anger du ett namn på virtuell dator. Ett exempel kan vara _myVM_.
1. Ändra inte standardinställningarna för **Region**, **Tillgänglighetsalternativ**, **Avbildning** och **Storlek**.
1. Under **Administratörskonto**, ange ett **användarnamn** och **lösenord** för den virtuella datorn.
1. Under **Regler för inkommande portar** väljer du **Tillåt valda portar** och sedan **RDP (3389)** och **HTTP** från den nedrullningsbara menyn.

1. Innan du skapar en virtuell dator måste du skapa en datadisk.

   1. Välj **Nästa:Diskar**.

      ![Lägga till datadiskar](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. På fliken **Diskar** och sedan under **Diskalternativ** lämnar du kvar standardinställningarna.
   1. Under **DATADISKAR** väljer du **Skapa och anslut en ny disk**.

   1. Använd standardinställningarna förutom för **Storlek (GiB)**, som du kan ändra till **1 GB** för den här självstudien.

      ![Datadiskinformation](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Välj **OK**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

   Du kan välja ikonen **Meddelanden** om du vill se **distributionsförloppet**. Det kan ta några minuter att skapa en ny virtuell dator.

1. När distributionen av den virtuella datorn är klar väljer du **Gå till resurs**.

   ![Gå till resurs](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Nu har du skapat en ny virtuell dator och anslutit en datadisk. Nu kommer du att ansluta till den virtuella datorn.

### <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

1. I Azure-portalen väljer du **Anslut** på sidan för den virtuella datorns egenskaper.

   ![Ansluta till en virtuell Azure-dator från portalen](./media/storage-sync-files-extend-servers/connect-vm.png)

1. På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta via **IP-adress** via port 3389. Välj **Hämta RDP-fil**.

   ![Ladda ned RDP-filen](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Öppna den nedladdade RDP-filen och välj **Anslut** när du tillfrågas.
1. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange användarnamnet som *localhost\användarnamn* och ange det lösenord som du skapade för den virtuella datorn. Välj sedan **OK**.

   ![Fler alternativ](./media/storage-sync-files-extend-servers/local-host2.png)

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** för att skapa anslutningen.

### <a name="prepare-the-windows-server"></a>Förbereda Windows-servern

För Windows Server 2016 Datacenter-servern inaktiverar du Förbättrad säkerhetskonfiguration i Internet Explorer. Det här steget krävs bara för inledande serverregistrering. Du kan aktivera det igen när servern har registrerats.

På den virtuella Windows Server 2016 Datacenter-datorn öppnas Serverhanteraren automatiskt.  Om Serverhanteraren inte öppnas som standard kan du söka efter den i Start-menyn.

1. I **Serverhanteraren** väljer du **Lokal server**.

   ![”Lokal server” till vänster i användargränssnittet för Serverhanteraren](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. I fönsterrutan **Egenskaper** väljer du länken för **Förbättrad säkerhetskonfiguration i Internet Explorer**.  

    ![Fönstret ”Förbättrad säkerhetskonfiguration i Internet Explorer” i användargränssnittet i Serverhanteraren](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. I dialogrutan **Förbättrad säkerhetskonfiguration i Internet Explorer** väljer du **Av** för **Administratörer** och **Användare**.

    ![Popup-fönstret Förbättrad säkerhetskonfiguration i Internet Explorer med ”Av” valt](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Nu kan du lägga till datadisken på den virtuella datorn.

### <a name="add-the-data-disk"></a>Lägga till datadisken

1. När du är på den virtuella **Windows Server 2016 Datacenter**-datorn väljer du **Filer och lagringstjänster** > **Volymer** > **Diskar**.

    ![Datadisk](media/storage-sync-files-extend-servers/your-disk.png)

1. Högerklicka på 1 GB-disken med namnet **Msft Virtual Disk** och välj **Ny volym**.
1. Slutför guiden. Använd standardinställningarna och anteckna den tilldelade enhetsbeteckningen.
1. Välj **Skapa**.
1. Välj **Stäng**.

   Nu har du tagit disken online och skapat en volym. Öppna Utforskaren på den virtuella Windows Server-datorn för att bekräfta förekomsten av den nyligen tillagda datadisken.

1. I Utforskaren på den virtuella datorn expanderar du **Den här datorn** och öppnar den nya enheten. I det här exemplet är det F:-enheten.
1. Högerklicka och välj **ny**  >  **mapp**. Namnge mappen _FilesToSync_.
1. Öppna mappen **FilesToSync**.
1. Högerklicka och välj **nytt**  >  **text dokument**. Namnge textfilen _MyTestFile_.

    ![Lägga till en ny textfil](media/storage-sync-files-extend-servers/new-file.png)

1. Stäng **Utforskaren** och **Serverhanteraren**.

### <a name="download-the-azure-powershell-module"></a>Ladda ned Azure PowerShell-modulen

På den virtuella Windows Server 2016 Datacenter-datorn installerar du sedan Azure PowerShell-modulen på servern.

1. På den virtuella datorn öppnar du ett PowerShell-fönster med förhöjd behörighet.
1. Kör följande kommando:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Om du har en NuGet-version som är äldre än 2.8.5.201 uppmanas du att ladda ned och installera den senaste versionen av NuGet.

   Som standard konfigureras inte PowerShell-galleriet som en betrodd lagringsplats för PowerShellGet. Första gången du använder PSGallery visas följande meddelande:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Svara **Ja** eller **Ja till alla** för att fortsätta med installationen.

Modulen `Az` är en sammanslagen modul för Azure PowerShell-cmdletar. När du installerar den laddar den ned alla tillgängliga Azure Resource Manager-moduler och gör dess cmdletar tillgängliga för användning.

Du har nu konfigurerat miljön för den här självstudien. Du är redo att distribuera tjänsten för synkronisering av lagring.

## <a name="deploy-the-service"></a>Distribuera tjänsten

För att distribuera Azure File Sync placerar först en resurs för **tjänsten för synkronisering av lagring** i en resursgrupp för din valda prenumeration. Tjänsten för synkronisering av lagring ärver åtkomstbehörigheter från dess prenumeration och resursgrupp.

1. I Azure-portalen väljer du **Skapa en resurs** och sök sedan efter **Azure File Sync**.
1. Välj **Azure File Sync** i sökresultatet.
1. Välj **Skapa** för att öppna fliken **Distribuera synkronisering av lagring**.

   ![Distribuera synkronisering av lagring](media/storage-sync-files-extend-servers/afs-info.png)

   I fönstret som öppnas anger du följande information:

   | Värde | Beskrivning |
   | ----- | ----- |
   | **Namn** | Ett unikt namn (per prenumeration) för tjänsten för synkronisering av lagring.<br><br>Använd _afssyncservice02_ för den här självstudien. |
   | **Prenumeration** | Den Azure-prenumeration som du använder för den här kursen. |
   | **Resursgrupp** | Den resursgrupp som innehåller tjänsten för synkronisering av lagring.<br><br>Använd _afsresgroup101918_ för den här självstudien. |
   | **Plats** | East US |

1. När du är klar väljer du **Skapa** för att distribuera **tjänsten för synkronisering av lagring**.
1. Välj fliken **Meddelanden** > **Gå till resurs**.

## <a name="install-the-agent"></a>Installera agenten

Azure File Sync-agenten är ett nedladdningsbart paket som möjliggör att Windows Server kan synkroniseras med en Azure-filresurs.

1. På den virtuella **Windows Server 2016 Datacenter**-datorn öppnar du **Internet Explorer**.
1. Gå till [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Rulla ned till avsnittet **Azure File Sync-agent** och välj **Ladda ned**.

   ![Nedladdning av synkroniseringsagent](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Markera kryssrutan för **StorageSyncAgent_V3_WS2016.EXE** och välj **Nästa**.

   ![Välja agent](media/storage-sync-files-extend-servers/select-agent.png)

1. Välj **Tillåt när**  >  **körningen** är  >  **öppen**.
1. Stäng PowerShell-fönstret, om du inte redan har gjort det.
1. Acceptera standardinställningarna i **konfigurationsguiden för lagringssynkroniseringsagenten**.
1. Välj **installera**.
1. Välj **Slutför**.

Du har distribuerat Azure-synkroniseringstjänsten och installerat agenten på den virtuella Windows Server 2016 Datacenter-datorn. Nu behöver du registrera den virtuella datorn med tjänsten för synkronisering av lagring.

## <a name="register-windows-server"></a>Registrera Windows Server

När du registrerar Windows-servern med en tjänst för synkronisering av lagring etableras en förtroenderelation mellan din server (eller ditt kluster) och tjänsten för synkronisering av lagring. En server kan bara registreras till en tjänst för synkronisering av lagring. Den kan synkronisera med andra servrar och Azure-filresurser som är associerade med den tjänsten för synkronisering av lagring.

Användargränssnittet för serverregistrering bör öppnas automatiskt när du har installerat Azure File Sync-agenten. Om det inte öppnas kan du öppna det manuellt från dess filplats: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. När användargränssnittet för serverregistrering öppnas på den virtuella datorn väljer du **OK**.
1. Välj **Logga in** för att börja.
1. Logga in med autentiseringsuppgifterna för Azure-kontot och välj **Logga in**.
1. Ange följande information:

   ![Skärmbild av användargränssnittet för serverregistrering](media/storage-sync-files-extend-servers/signin.png)

   | Värde | Beskrivning |
   | ----- | ----- |
   | **Azure Subscription (Azure-prenumeration)** | Den prenumeration som innehåller tjänsten för synkronisering av lagring för den här kursen. |
   | **Resursgrupp** | Den resursgrupp som innehåller tjänsten för synkronisering av lagring. Använd _afsresgroup101918_ för den här självstudien. |
   | **Storage Sync Service (Tjänst för synkronisering av lagring)** | Namnet på tjänsten för synkronisering av lagring. Använd _afssyncservice02_ för den här självstudien. |

1. Välj **Registrera** för att slutföra serverregistreringen.
1. Som en del av registreringsprocessen uppmanas du att logga in en gång till. Logga in och välj **Nästa**.
1. Välj **OK**.

## <a name="create-a-sync-group"></a>Skapa en synkroniseringsgrupp

En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. En synkroniseringsgrupp måste innehålla en molnslutpunkt, som representerar en Azure-filresurs. En synkroniseringsgrupp måste även innehålla en eller flera serverslutpunkter. En serverslutpunkt representerar en sökväg på en registrerad servern. Så här skapar du en synkroniseringsgrupp:

1. I [Azure-portalen](https://portal.azure.com/) väljer du **+ Synkroniseringsgrupp** från tjänsten för synkronisering av lagring. Använd *afssyncservice02* för den här självstudien.

   ![Skapa en ny synkroniseringsgrupp i Azure-portalen](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Ange följande information för att skapa en synkroniseringsgrupp med en molnslutpunkt:

   | Värde | Beskrivning |
   | ----- | ----- |
   | **Sync-gruppens namn** | Det här namnet måste vara unikt i tjänsten för synkronisering av lagring men kan vara vilket namn som helst som är logiskt för dig. Använd *afssyncgroup* för den här självstudien.|
   | **Prenumeration** | Prenumerationen där du har distribuerat tjänsten för synkronisering av lagring för den här kursen. |
   | **Lagringskonto** | Välj **Välj lagringskonto**. I det fönster som visas väljer du det lagringskonto som har den Azure-filresurs du har skapade. Använd *afsstoracct101918* för den här självstudien. |
   | **Azure-filresurs** | Namnet på den Azure-filresurs som du skapade. Använd *afsfileshare* för den här självstudien. |

1. Välj **Skapa**.

Om du väljer din synkroniseringsgrupp kan du se att du nu har en **molnslutpunkt**.

## <a name="add-a-server-endpoint"></a>Lägga till en serverslutpunkt

En serverslutpunkt representerar en specifik plats på en registrerad server. Det kan till exempel vara en mapp på en servervolym. Så här lägger du till en serverslutpunkt:

1. Markera den nya synkroniseringsgruppen och välj sedan **Lägg till serverslutpunkt**.

   ![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. I fönstret **Lägg till serverslutpunkt** anger du följande information för att skapa en serverslutpunkt:

   | Värde | Beskrivning |
   | ----- | ----- |
   | **Registrerad Server** | Namnet på den server som du skapade. Använd *afsvm101918* för den här självstudien. |
   | **Sökväg** | Windows Server-sökvägen till den enhet som du skapade. Använd *f:\filestosync* i den här självstudien. |
   | **Molnnivåindelning** | Lämna det inaktiverat för den här kursen. |
   | **Ledigt utrymme på volym** | Lämna det tomt för den här kursen. |

1. Välj **Skapa**.

Dina filer är nu synkroniserade i Azure-filresursen och Windows Server.

![Azure Storage har synkroniserats](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som du skapade i den här självstudien tar du först bort slut punkterna från tjänsten för synkronisering av lagring. Avregistrera sedan servern med lagrings tjänsten för synkronisering, ta bort Sync-grupperna och ta bort synkroniseringstjänsten.

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig de grundläggande stegen för att utöka lagrings kapaciteten för en Windows-Server med hjälp av Azure File Sync. En mer grundlig titt på planering för en Azure File Sync distribution finns i:

> [!div class="nextstepaction"]
> [Planera för Azure File Sync-distribution](./storage-sync-files-planning.md)
