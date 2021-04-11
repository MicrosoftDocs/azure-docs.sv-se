---
title: Förbereda och anpassa en huvud-VHD-avbildning – Azure
description: Hur du förbereder, anpassar och laddar upp en Windows-huvudavbildning av ett virtuellt skriv bord till Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 654b5b293397af9737813d759a7c784d90629942
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445813"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Förbereda och anpassa en VHD-huvudavbildning

Den här artikeln beskriver hur du förbereder en avbildning av en virtuell hård disk (VHD) för uppladdning till Azure, inklusive hur du skapar virtuella datorer (VM) och installerar program vara på dem. De här anvisningarna gäller för en Windows-konfiguration med en virtuell dator som kan användas med din organisations befintliga processer.

>[!IMPORTANT]
>Vi rekommenderar att du använder en avbildning från Azures avbildnings Galleri. Men om du behöver använda en anpassad avbildning kontrollerar du att du inte redan har installerat Windows Virtual Desktop-agenten på den virtuella datorn. Om du använder en anpassad avbildning med Windows Virtual Desktop-agenten kan det orsaka problem med avbildningen, till exempel blockera registrering och förhindra anslutningar till användarsessionen.  

## <a name="create-a-vm"></a>Skapa en virtuell dator

Windows 10 Enterprise multi-session är tillgängligt i Azures avbildnings Galleri. Det finns två alternativ för att anpassa avbildningen.

Det första alternativet är att etablera en virtuell dator (VM) i Azure genom att följa anvisningarna i [skapa en virtuell dator från en hanterad avbildning](../virtual-machines/windows/create-vm-generalized-managed.md)och sedan gå vidare till [förberedelse och installation av program vara](set-up-customize-master-image.md#software-preparation-and-installation).

Det andra alternativet är att skapa avbildningen lokalt genom att ladda ned avbildningen, etablering av en virtuell Hyper-V-dator och anpassa den efter dina behov, som vi tar upp i följande avsnitt.

### <a name="local-image-creation"></a>Skapa lokal avbildning

När du har laddat ned avbildningen till en lokal plats öppnar du **Hyper-V Manager** för att skapa en virtuell dator med den virtuella hård disken som du kopierade. Följande instruktioner är en enkel version, men du hittar mer detaljerad information i [skapa en virtuell dator i Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Så här skapar du en virtuell dator med den kopierade virtuella hård disken:

1. Öppna **guiden Ny virtuell dator**.

2. På sidan Ange generation väljer du **generation 1**.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av sidan Ange generation. Alternativet "generation 1" är markerat.](media/a41174fd41302a181e46385e1e701975.png)

3. Under kontroll punkts typ inaktiverar du kontroll punkter genom att avmarkera kryss rutan.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av avsnittet kontroll punkts typ på sidan kontroll punkter.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Du kan också köra följande cmdlet i PowerShell för att inaktivera kontroll punkter.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Fast disk

Om du skapar en virtuell dator från en befintlig virtuell hård disk skapas en dynamisk disk som standard. Den kan ändras till en fast disk genom att välja **Redigera disk...** som visas i följande bild. Mer detaljerad information finns i [förbereda en virtuell Windows-VHD eller VHDX att ladda upp till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!div class="mx-imgBorder"]
> ![En skärm bild av alternativet Redigera disk.](media/35772414b5a0f81f06f54065561d1414.png)

Du kan också köra följande PowerShell-cmdlet för att ändra disken till en fast disk.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Förberedelse av program vara och installation

Det här avsnittet beskriver hur du förbereder och installerar FSLogix och Windows Defender, samt några grundläggande konfigurations alternativ för appar och din avbildnings register.

Om du installerar Microsoft 365 appar för företag och OneDrive på den virtuella datorn går du till [installera Office på en huvud-VHD-avbildning](install-office-on-wvd-master-image.md) och följer anvisningarna där du kan installera apparna. När du är klar går du tillbaka till den här artikeln.

Om dina användare behöver åtkomst till vissa LOB-program rekommenderar vi att du installerar dem när du har slutfört det här avsnittets instruktioner.

### <a name="set-up-user-profile-container-fslogix"></a>Konfigurera användar profil behållare (FSLogix)

Om du vill inkludera FSLogix-behållaren som en del av avbildningen följer du anvisningarna i [skapa en profil behållare för en adresspool med hjälp av en fil resurs](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Du kan testa funktionerna i FSLogix-behållaren med [den här snabb](/fslogix/configure-cloud-cache-tutorial/)starten.

### <a name="configure-windows-defender"></a>Konfigurera Windows Defender

Om Windows Defender är konfigurerat på den virtuella datorn kontrollerar du att det har kon figurer ATS för att inte genomsöka hela innehållet i VHD-och VHDX-filer under bilagan.

Den här konfigurationen tar bara bort skanning av VHD-och VHDX-filer under den bifogade filen, men påverkar inte genomsökningen i real tid.

Mer detaljerad information om hur du konfigurerar Windows Defender på Windows Server finns i [Konfigurera Windows Defender Antivirus-undantag i Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Mer information om hur du konfigurerar Windows Defender för att undanta vissa filer från genomsökning finns i [Konfigurera och validera undantag baserat på fil namns tillägg och mappens plats](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Inaktivera automatiska uppdateringar

Så här inaktiverar du automatiska uppdateringar via lokal grupprincip:

1. Öppna **redigerare för lokalt grupprincipobjekt \\ administrativa mallar \\ Windows-komponenter \\ Windows Update**.
2. Högerklicka på **Konfigurera automatisk uppdatering** och Ställ in den på **inaktive rad**.

Du kan också köra följande kommando i en kommando tolk för att inaktivera automatiska uppdateringar.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Ange startlayout för Windows 10-datorer (valfritt)

Kör det här kommandot för att ange en startlayout för Windows 10-datorer.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Konfigurera omdirigering av tidszon

Omdirigering av tidszon kan verkställas på grupprincip nivå eftersom alla virtuella datorer i en pool är en del av samma säkerhets grupp.

Omdirigera tids zoner:

1. Öppna **konsolen Grupprinciphantering** på Active Directory-servern.
2. Expandera din domän och grupprincip objekt.
3. Högerklicka på grupprincip- **objektet** som du skapade för grup princip inställningarna och välj **Redigera**.
4. I **redigeraren Grupprinciphantering** navigerar du till **dator konfigurations**  >  **principer**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Fjärrskrivbordstjänster**  >  **värd** enhet för fjärrskrivbordssession  >  **och omdirigering av resurser**.
5. Aktivera inställningen **Tillåt omdirigering** av tidszon.

Du kan också köra det här kommandot på huvud avbildningen för att omdirigera tids zoner:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Inaktivera Storage Sense

Vi rekommenderar att du inaktiverar Storage Sense för Windows Virtual Desktop-fjärrskrivbordssession som använder Windows 10 Enterprise eller Windows 10 Enterprise multi-session. Du kan inaktivera Storage Sense i menyn Inställningar under **lagring**, som du ser på följande skärm bild:

> [!div class="mx-imgBorder"]
> ![En skärm bild av lagrings menyn under Inställningar. Alternativet "Storage Sense" är inaktiverat.](media/storagesense.png)

Du kan också ändra inställningen med registret genom att köra följande kommando:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Ta med ytterligare språk stöd

Den här artikeln beskriver inte hur du konfigurerar språk och regional support. Mer information finns i följande artiklar:

- [Lägga till språk i Windows-avbildningar](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Funktioner på begäran](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Språk-och regions funktioner på begäran (franska)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Andra program och register konfiguration

I det här avsnittet beskrivs program-och operativ system konfiguration. All konfiguration i det här avsnittet görs via register poster som kan utföras av kommando rads-och regedit-verktyg.

>[!NOTE]
>Du kan implementera bästa praxis i konfigurationen med antingen grupprincip objekt (GPO) eller register import. Administratören kan välja något av alternativen baserat på deras organisations krav.

För feedback Hub-insamling av telemetridata i Windows 10 Enterprise multi-session kör du följande kommando:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Kör följande kommando för att åtgärda Watson-krascher:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Ange följande kommandon i Registereditorn för att åtgärda stöd för 5 k-matchning. Du måste köra kommandona innan du kan aktivera stacken sida vid sida.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Förbered avbildningen för uppladdning till Azure

När du har avslutat konfigurationen och installerat alla program följer du anvisningarna i [förbereda en Windows-VHD eller VHDX att ladda upp till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) för att förbereda avbildningen.

När du har förberedat avbildningen för uppladdning ser du till att den virtuella datorn är i läget avstängd eller inte allokerad.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Ladda upp huvud avbildningen till ett lagrings konto i Azure

Det här avsnittet gäller endast när huvud avbildningen har skapats lokalt.

Följande anvisningar visar hur du överför huvud avbildningen till ett Azure Storage-konto. Om du inte redan har ett Azure Storage-konto följer du anvisningarna i [den här artikeln](/azure/developer/javascript/tutorial-vscode-static-website-node-03) för att skapa ett.

1. Konvertera VM-avbildningen (VHD) till Fixed om du inte redan gjort det. Om du inte konverterar bilden till Fixed kan du inte skapa bilden.

2. Ladda upp den virtuella hård disken till en BLOB-behållare i ditt lagrings konto. Du kan ladda upp snabbt med [Storage Explorer-verktyget](https://azure.microsoft.com/features/storage-explorer/). Mer information om Storage Explorer-verktyget finns i [den här artikeln](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows).

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av Microsoft Azure Storage Explorer verktygs fönstret Sök. Kryss rutan "Ladda upp VHD-eller VHDX-filer som Page blobbar (rekommenderas)" är markerad.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Gå sedan till Azure Portal i webbläsaren och Sök efter "images". Din sökning ska leda dig till sidan **Skapa avbildning** , som du ser på följande skärm bild:

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av sidan Skapa avbildning i Azure Portal, fyllt med exempel värden för bilden.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. När du har skapat avbildningen bör du se ett meddelande som liknar det i följande skärm bild:

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av meddelandet "en avbildning har skapats".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har en avbildning kan du skapa eller uppdatera värdar för pooler. Mer information om hur du skapar och uppdaterar värdbaserade pooler finns i följande artiklar:

- [Skapa en värdpool med en Azure Resource Manager-mall](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Självstudie: skapa en värdbaserad pool med Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Skapa en värdpool med PowerShell](create-host-pools-powershell.md)
- [Skapa en profilcontainer för en värdpool med hjälp av en filresurs](create-host-pools-user-profile.md)
- [Konfigurera metoden för belastningsutjämning för Windows Virtual Desktop](configure-host-pool-load-balancing.md)

Om du har påträffat ett anslutnings problem när du har för berett eller anpassat din VHD-avbildning kan du läsa mer i [fel söknings guiden](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) .
