---
title: Guide för att skapa en dator med Windows-mall | Microsoft Docs
description: Allmänna steg för att förbereda en dator med Windows-mall i labb tjänster.  Dessa steg omfattar inställning av Windows Update schema, installation av OneDrive och installation av Office.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647910"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guide för att konfigurera en Windows-mall i Azure Lab Services

Om du konfigurerar en Windows 10-mall för Azure Lab Services finns här några metod tips och tips att tänka på. Konfigurations stegen nedan är valfria.  Dessa förberedande steg kan dock hjälpa dig att göra eleverna mer produktiva, minimera tids avbrotten i klassen och se till att de använder den senaste tekniken.

>[!IMPORTANT]
>Den här artikeln innehåller PowerShell-kodfragment för att effektivisera ändrings processen för dator mal len.  För alla PowerShell-skript som visas ska du köra dem i Windows PowerShell med administratörs behörighet. I Windows 10 är det ett snabbt sätt att göra det genom att högerklicka på Start-menyn och välja "Windows PowerShell (admin)".

## <a name="install-and-configure-onedrive"></a>Installera och konfigurera OneDrive

För att skydda student data från att förloras om en virtuell dator återställs rekommenderar vi eleverna att säkerhetskopiera data till molnet.  Microsoft OneDrive kan hjälpa studenter att skydda sina data.  

### <a name="install-onedrive"></a>Installera OneDrive

Information om hur du hämtar och installerar OneDrive manuellt finns i hämtnings sidorna för [OneDrive](https://onedrive.live.com/about/download/) eller [OneDrive för företag](https://onedrive.live.com/about/business/) .

Du kan också använda följande PowerShell-skript.  Den hämtar och installerar den senaste versionen av OneDrive automatiskt.  När OneDrive-klienten har installerats kör du installations programmet.  I vårt exempel använder vi `/allUsers` växeln för att installera OneDrive för alla användare på datorn. Vi använder också `/silent` växeln för att installera OneDrive i tyst läge.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive-anpassningar

Det finns många [anpassningar som kan göras i OneDrive](/onedrive/use-group-policy). Nu ska vi gå igenom några av de vanligaste anpassningarna.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Flytta Windows-kända mappar i bakgrunden till OneDrive

Mappar som dokument, nedladdningar och bilder används ofta för att lagra elev-filer. För att se till att dessa mappar säkerhets kopie ras i OneDrive rekommenderar vi att du flyttar dessa mappar till OneDrive.

Om du använder en dator som inte använder Active Directory kan användare manuellt flytta dessa mappar till OneDrive när de har autentiserat till OneDrive.

1. Öppna Utforskaren
2. Högerklicka på mappen dokument, nedladdningar eller bilder.
3. Gå till egenskaper > plats.  Flytta mappen till en ny mapp i OneDrive-katalogen.

Om den virtuella datorn är ansluten till Active Directory kan du ställa in mallen så att dina studenter automatiskt kan flytta de kända mapparna till OneDrive.  

Du måste först hämta ditt organisations-ID.  Mer information finns i [hitta Microsoft 365 organisations-ID](/onedrive/find-your-office-365-tenant-id).  Du kan också hämta organisations-ID med hjälp av följande PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

När du har ditt organisations-ID anger du OneDrive för att be om att flytta kända mappar till OneDrive med hjälp av följande PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Använd OneDrive-filer på begäran

Studenter kan ha många filer i sina OneDrive-konton. För att hjälpa till att spara utrymme på datorn och minska hämtnings tiden rekommenderar vi att du gör alla filer som lagras i Students OneDrive-konto på begäran.  Filer på begäran laddas bara ned när en användare har åtkomst till filen.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Logga in användare tyst till OneDrive

OneDrive kan ställas in för att logga in automatiskt med Windows-autentiseringsuppgifter för den inloggade användaren.  Automatisk inloggning är användbart för klasser där studenten loggar in med sina skol uppgifter.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Inaktivera den självstudie som visas i slutet av OneDrive-installationen

Med den här inställningen kan du förhindra att själv studie kursen startar i en webbläsare i slutet av OneDrive-installationen.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Ange den maximala storleken för en fil som ska laddas ned automatiskt

Den här inställningen används tillsammans med obevakade inloggnings användare till OneDrive-synkroniseringsklienten med sina Windows-autentiseringsuppgifter på enheter som inte har OneDrive-filer på begäran aktiverat. Alla användare som har ett OneDrive-värde som är större än det angivna tröskelvärdet (i MB) uppmanas att välja de mappar som ska synkroniseras innan OneDrive-synkroniseringsklienten (OneDrive.exe) laddar ned filerna.  I vårt exempel är "1111-2222-3333-4444" organisations-ID: t och 0005000 anger ett tröskelvärde på 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Installera och konfigurera Microsoft 365

### <a name="install-microsoft-365"></a>Installera Microsoft 365

Om din dator behöver Office rekommenderar vi att du installerar Office via [Office Deployment Tool (ODT)](https://www.microsoft.com/download/details.aspx?id=49117). Du måste skapa en återanvändbar konfigurations fil med hjälp av [administrations centret för Microsoft 365 Apps](https://config.office.com/) för att välja vilken arkitektur, vilka funktioner du behöver från Office och hur ofta de uppdateras.

1. Gå till [Microsoft 365 Apps administrations Center](https://config.office.com/) och ladda ned din egen konfigurations fil.
2. Hämta [distributions verktyget för Office](https://www.microsoft.com/download/details.aspx?id=49117).  Den hämtade filen är `setup.exe` .
3. Kör `setup.exe /download configuration.xml` för att ladda ned Office-komponenter.
4. Kör `setup.exe /configure configuration.xml` för att installera Office-komponenter.

### <a name="change-the-microsoft-365-update-channel"></a>Ändra Microsoft 365 uppdaterings kanal

Med hjälp av konfigurations verktyget för Office kan du ange hur ofta Office ska ta emot uppdateringar. Men om du behöver ändra hur ofta Office tar emot uppdateringar efter installationen kan du ändra uppdaterings kanalens URL. Uppdaterings kanalens URL-adresser finns i [ändra Microsoft 365 Apps uppdaterings kanal för enheter i din organisation](/deployoffice/change-update-channels). Exemplet nedan visar hur du ställer in Microsoft 365 att använda månads uppdaterings kanalen.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Installera och konfigurera uppdateringar

### <a name="install-the-latest-windows-updates"></a>Installera de senaste Windows-uppdateringarna

Vi rekommenderar att du installerar de senaste Microsoft-uppdateringarna på mallen Machine i säkerhets syfte innan du publicerar mallen VM.  Det kan också undvikas att studenter stör sitt arbete när uppdateringar körs vid oväntade tidpunkter.

1. Starta **Inställningar** från Start-menyn
2. Klicka på **uppdatera** & säkerhet
3. Klicka på **Sök efter uppdateringar**
4. Uppdateringar laddas ned och installeras.

Du kan också använda PowerShell för att uppdatera mallen Machine.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Vissa uppdateringar kan kräva att datorn startas om.  Du får en uppmaning om en omstart krävs.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Installera de senaste uppdateringarna för Microsoft Store-appar

Vi rekommenderar att alla Microsoft Store appar uppdateras till de senaste versionerna.  Här följer instruktioner för att uppdatera program manuellt från Microsoft Store.  

1. Starta **Microsoft Store** -program.
2. Klicka på ellipsen (...) bredvid ditt användar foto i programmets övre hörn.
3. Välj **Ladda ned** och uppdateringar på den nedrullningsbara menyn.
4. Klicka på knappen **Hämta uppdatering** .

Du kan också använda PowerShell för att uppdatera Microsoft Store program som redan är installerade.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Stoppa automatiska Windows-uppdateringar

När du har uppdaterat Windows till den senaste versionen kan du välja att stoppa Windows-uppdateringar.  Automatiska uppdateringar kan eventuellt störa den schemalagda klass tiden.  Om din kurs är en längre igång kan du be eleverna att manuellt söka efter uppdateringar eller ställa in automatiska uppdateringar för en tid utanför de schemalagda klass timmarna.  Mer information om anpassnings alternativ för Windows Update finns i [hantera ytterligare Windows Update inställningar](/windows/deployment/update/waas-wu-settings).

Automatiska Windows-uppdateringar kan stoppas med hjälp av följande PowerShell-skript.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Installera främmande språk paket

Om du behöver fler språk installerade på den virtuella datorn kan du lägga till dem via Microsoft Store.

1. Starta Microsoft Store
2. Sök efter "språk paket"
3. Välj språk att installera

Om du redan är inloggad på mallen VM använder du genvägen "installera språk paket" ( `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` ) för att gå direkt till rätt inställnings sida.

## <a name="remove-unneeded-built-in-apps"></a>Ta bort onödiga inbyggda appar

Windows 10 innehåller många inbyggda program som kanske inte behövs för din specifika klass. För att förenkla dator avbildningen för studenter kanske du vill avinstallera vissa program från din mall maskin.  Om du vill se en lista över installerade program använder du PowerShell- `Get-AppxPackage` cmdleten.  I exemplet nedan visas alla installerade program som kan tas bort.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Använd Remove-Appx-cmdleten om du vill ta bort ett program.  Exemplet nedan visar hur du tar bort alla XBox-relaterade.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Installera vanliga undervisnings program

Installera andra appar som ofta används för att lära sig genom Windows Store-appen. Förslag innehåller program som [Microsoft whiteboard app](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)och [Minecraft Education Edition](https://education.minecraft.net/). Dessa program måste installeras manuellt via Windows Store eller via deras respektive webbplatser på mallen VM.

## <a name="conclusion"></a>Slutsats

Den här artikeln visar dig valfria steg för att förbereda din virtuella Windows-mall för en effektiv klass.  Stegen inkluderar installation av OneDrive och installation av Microsoft 365, installation av uppdateringar för Windows och installation av uppdateringar för Microsoft Store-appar.  Vi har också diskuterat hur du ställer in uppdateringar till ett schema som passar bäst för din klass.  

## <a name="next-steps"></a>Nästa steg
Se artikeln om hur du styr avstängnings beteendet i Windows för att hjälpa till med att hantera kostnader: [Guide för att kontrol lera beteendet i Windows](how-to-windows-shutdown.md)