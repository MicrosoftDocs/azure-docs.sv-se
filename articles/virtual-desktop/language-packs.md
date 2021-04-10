---
title: Installera språk paket på virtuella Windows 10-datorer i Windows Virtual Desktop – Azure
description: Så här installerar du språk paket för Windows 10-virtuella datorer med flera sessioner i Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fc7892db2ca11ab7970835f8979360961ee01104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463348"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Lägga till språk paket i en Windows 10-avbildning för flera sessioner

Windows Virtual Desktop är en tjänst som användarna kan distribuera när som helst, var som helst. Därför är det viktigt att användarna kan anpassa vilket språk deras Windows 10 Enterprise-avbildning av flera sessioner visas i.

Det finns två sätt som du kan använda för att hantera användarnas språk behov:

- Bygg dedikerade värdar för pooler med en anpassad avbildning för varje språk.
- Ha användare med olika språk-och lokaliserings krav i samma adresspool, men anpassa avbildningarna för att se till att de kan välja det språk de behöver.

Den sistnämnda metoden är mycket mer effektiv och kostnads effektiv. Det är dock upp till dig att bestämma vilken metod som passar bäst för dina behov. I den här artikeln får du lära dig hur du anpassar språk för dina avbildningar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att anpassa dina Windows 10 Enterprise multi-session-avbildningar för att lägga till flera språk:

- En virtuell Azure-dator (VM) med Windows 10 Enterprise multi-session, version 1903 eller senare

- Språket ISO, Feature på demand (franska) disk 1 och Inbox-appar ISO av den OS-version som avbildningen använder. Du kan hämta dem här:
     
     - Språk-ISO:
        - [Windows 10, version 1903 eller 1909 språk paket ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, version 2004 eller 20H2 språk paket ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - Franska departements disk 1 ISO:
        - [Windows 10, version 1903 eller 1909 franska departements disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, version 2004 eller 20H2 DEPARTEMENTs disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Inkorg-appar ISO:
        - [Inbox-appar för Windows 10, version 1903 eller 1909 i ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, version 2004 Inbox-appar ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, version 20H2 Inbox-appar ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Om du använder ISO-filer för lokala Experience Pack (LXP) för att lokalisera dina avbildningar måste du också hämta rätt LXP ISO för bästa språk upplevelsen
        - Om du använder Windows 10, version 1903 eller 1909:
          - [Windows 10, version 1903 eller 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Om du använder Windows 10, version 2004 eller 20H2, använder du informationen i [lägga till språk i Windows 10: kända problem](/windows-hardware/manufacture/desktop/language-packs-known-issue) för att ta reda på vilka av följande LXP ISO passar dig:
          - [Windows 10, version 2004 eller 20H2 **9b** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, version 2004 eller 20H2 **9c** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, version 2004 eller 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, version 2004 eller 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, version 2004 eller 20H2 **1c** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, version 2004 eller 20H2 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)

- En Azure Files resurs eller en fil resurs på en virtuell Windows-fil Server

>[!NOTE]
>Fil resursen (databasen) måste vara tillgänglig från den virtuella Azure-dator som du planerar att använda för att skapa den anpassade avbildningen.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Skapa en innehålls lagrings plats för språk paket och funktioner på begäran

Så här skapar du innehålls lagrings platsen för språk paket och FODs och en lagrings plats för paket för Inkorgen appar:

1. På en virtuell Azure-dator laddar du ned Windows 10-appar för flera språk i ISO, FODs och Inkorgen för Windows 10 Enterprise multi-session, version 1903/1909 och 2004-avbildningar från länkarna i [krav](#prerequisites).

2. Öppna och montera ISO-filerna på den virtuella datorn.

3. Gå till språk paketet ISO och kopiera innehållet från mappen **LocalExperiencePacks** och **x64 \\ Langpacks** och klistra in innehållet i fil resursen.

4. Gå till **ISO-filen med franska**, kopiera allt innehåll och klistra in den i fil resursen.
5. Gå till mappen **amd64fre** i Inkorgen appar i ISO och kopiera innehållet i lagrings platsen för de Inbox-appar som du har för berett.

     >[!NOTE]
     > Om du arbetar med begränsad lagring kan du bara kopiera filerna för de språk som du känner till användarna behöver. Du kan se vilka filer som skiljer sig genom att titta på språk koderna i deras fil namn. Till exempel har den franska filen koden "fr-FR" i sitt namn. En fullständig lista över språk koder för alla tillgängliga språk finns i [tillgängliga språk paket för Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Vissa språk kräver ytterligare teckensnitt som ingår i satellit paket som följer olika namngivnings konventioner. Till exempel innehåller japanska teckensnitts fil namn "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Ett exempel på de japanska språk paketen med språk tag gen "Jpan" i deras fil namn.](media/language-pack-example.png)

6. Ange behörigheterna för den lokala lagrings platsen för språk innehåll så att du har Läs behörighet från den virtuella datorn som du använder för att skapa den anpassade avbildningen.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Skapa en anpassad Windows 10 Enterprise multi-session-avbildning manuellt

Så här skapar du en anpassad Windows 10 Enterprise multi-session-avbildning manuellt:

1. Distribuera en virtuell Azure-dator och gå sedan till Azure-galleriet och välj den aktuella versionen av Windows 10 Enterprise multi-session som du använder.
2. När du har distribuerat den virtuella datorn ansluter du till den med hjälp av RDP som lokal administratör.
3. Se till att den virtuella datorn har alla de senaste Windows-uppdateringarna. Hämta uppdateringarna och starta om den virtuella datorn om det behövs.
4. Anslut till språk paketet, franska och apparnas fildelnings lagring och montera den på en brev enhet (till exempel enhet E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Skapa en anpassad avbildning av Windows 10 Enterprise multi-session automatiskt

Om du hellre vill installera språk via en automatiserad process kan du konfigurera ett skript i PowerShell. Du kan använda följande skript exempel för att installera de spanska (Spanien), franska (Frankrike) och kinesiska (Kina) språk paket och satellit paket för Windows 10 Enterprise multi-session, version 2004. Skriptet integrerar språk gränssnitts paketet och alla nödvändiga satellit paket i avbildningen. Du kan dock också ändra det här skriptet för att installera andra språk. Se bara till att köra skriptet från en upphöjd PowerShell-session, annars fungerar det inte.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Skriptet kan ta ett tag beroende på hur många språk du behöver installera.

När skriptet har körts kan du kontrol lera att språk paketen har installerats korrekt genom att gå till **Start**  >  **Inställningar**  >  **tid & språk**  >  . Om språkfilerna finns där är det klart.

När du har lagt till ytterligare språk i Windows-avbildningen måste apparna i Inkorgen också uppdateras för att stödja de språk som har lagts till. Det kan du göra genom att uppdatera de förinstallerade apparna med innehållet från inkorg-apparna ISO. Om du vill utföra den här uppdateringen i en frånkopplad miljö (ingen Internet åtkomst från den virtuella datorn) kan du använda följande PowerShell-skript för att automatisera processen.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>Inkorg-appar som ingår i ISO är inte de senaste versionerna av de förinstallerade Windows-apparna. Om du vill hämta den senaste versionen av alla appar måste du uppdatera apparna med hjälp av Windows Store-appen och utföra en manuell sökning efter uppdateringar när du har installerat de nya språken.

När du är klar, se till att koppla från resursen.

## <a name="finish-customizing-your-image"></a>Slutför anpassningen av avbildningen

När du har installerat språk paketen kan du installera all annan program vara som du vill lägga till i din anpassade avbildning.

När du är klar med anpassningen av avbildningen måste du köra system förberedelse verktyget (Sysprep).

Köra Sysprep:

1. Öppna en kommando tolk med förhöjd behörighet och kör följande kommando för att generalisera avbildningen:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Stoppa den virtuella datorn och avbilda den sedan i en hanterad avbildning genom att följa anvisningarna i [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](../virtual-machines/windows/capture-image-resource.md).

3. Nu kan du använda den anpassade avbildningen för att distribuera en Windows-pool för virtuella skriv bord. Information om hur du distribuerar en adresspool finns i [Självstudier: skapa en värdbaserad pool med Azure Portal](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Aktivera språk i appen Windows-inställningar

Slutligen, efter att du har distribuerat poolen, måste du lägga till språket i varje användares språk lista så att de kan välja språk på menyn Inställningar.

För att se till att användarna kan välja de språk som du har installerat loggar du in som användaren och kör sedan följande PowerShell-cmdlet för att lägga till de installerade språk paketen på menyn språk. Du kan också ställa in det här skriptet som en automatiserad aktivitet eller ett inloggnings skript som aktive ras när användaren loggar in i sin session.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

När en användare har ändrat sina språk inställningar måste de logga ut från Windows Virtual Desktop-sessionen och logga in igen för att ändringarna ska börja gälla. 

## <a name="next-steps"></a>Nästa steg

Om du är nyfiken på kända problem med språk paket, se [lägga till språk paket i Windows 10, version 1803 och senare versioner: kända problem](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Om du har andra frågor om Windows 10 Enterprise multi-session kan du titta närmare på våra [vanliga frågor och svar](windows-10-multisession-faq.md).
