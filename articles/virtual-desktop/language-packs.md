---
title: Installera språkpaket på Windows 10 virtuella datorer i Windows Virtual Desktop – Azure
description: Så här installerar du språkpaket för Windows 10 virtuella datorer med flera sessioner i Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: db1ac3f5de507a5cfdbfec7216afea9a0f4ac541
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515047"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Lägga till språkpaket i en Windows 10 en flersessionsavbildning

Windows Virtual Desktop är en tjänst som användarna kan distribuera när som helst, var som helst. Det är därför det är viktigt att användarna kan anpassa vilket språk deras Windows 10 Enterprise visar i en flersessionsbild.

Det finns två sätt att tillgodose språkbehoven för dina användare:

- Skapa dedikerade värdpooler med en anpassad avbildning för varje språk.
- Ha användare med olika språk- och lokaliseringskrav i samma värdpool, men anpassa sina avbildningar så att de kan välja det språk de behöver.

Den senare metoden är mycket mer effektiv och kostnadseffektiv. Det är dock upp till dig att bestämma vilken metod som bäst passar dina behov. Den här artikeln visar hur du anpassar språk för dina bilder.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande saker för att anpassa dina Windows 10 Enterprise flersessionsavbildningar för att lägga till flera språk:

- En virtuell Azure-dator (VM) Windows 10 Enterprise flera sessioner, version 1903 eller senare

- Language ISO, Feature on Demand (FOD) Disk 1 och Inbox Apps ISO för os-versionen som avbildningen använder. Du kan ladda ned dem här:
     
     - Språk-ISO:
        - [Windows 10, version 1903 eller 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, version 2004 eller 20H2 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD Disk 1 ISO:
        - [Windows 10, version 1903 eller 1909 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, version 2004 eller 20H2 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Inbox Apps ISO:
        - [Windows 10, version 1903 eller 1909 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, version 2004 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, version 20H2 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Om du använder iso Lokaliserat gränssnittspaket filer (LXP) för att lokalisera dina avbildningar måste du också ladda ned lämplig LXP ISO för bästa språkupplevelse
        - Om du använder Windows 10 version 1903 eller 1909:
          - [Windows 10, version 1903 eller 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Om du använder Windows 10, version 2004 eller 20H2 använder du informationen i Lägga till språk i [Windows 10: Kända](/windows-hardware/manufacture/desktop/language-packs-known-issue) problem för att ta reda på vilken av följande LXP-ISO:er som är rätt för dig:
          - [Windows 10, version 2004 eller 20H2 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, version 2004 eller 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, version 2004 eller 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, version 2004 eller 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, version 2004 eller 20H2 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, version 2004 eller 20H2 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)
          - [Windows 10, version 2004 eller 20H2 **3C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2103C.iso)

- En Azure Files resurs eller en filresurs på en virtuell Windows-filserverdator

>[!NOTE]
>Filresursen (lagringsplatsen) måste vara tillgänglig från den virtuella Azure-dator som du planerar att använda för att skapa den anpassade avbildningen.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Skapa en innehållsdatabas för språkpaket och funktioner på begäran

Så här skapar du innehållsdatabasen för språkpaket och FOD:er och en lagringsplats för inkorgsappens paket:

1. På en virtuell Azure-dator laddar du ned Windows 10 Iso-, FOD-, och inbox-appar för Windows 10 Enterprise-session, version 1903/1909 och 2004-avbildningar från länkarna i [Krav](#prerequisites).

2. Öppna och montera ISO-filerna på den virtuella datorn.

3. Gå till språkpaketets ISO och kopiera innehållet från mapparna **LocalExperiencePacks** och **x64 \\ langpacks** och klistra sedan in innehållet i filresursen.

4. Gå till **ISO-filen fod,** kopiera allt dess innehåll och klistra in det i filresursen.
5. Gå till mappen **amd64fre** i Inbox Apps ISO och kopiera innehållet på lagringsplatsen för de inkorgsappar som du har förberett.

     >[!NOTE]
     > Om du arbetar med begränsad lagring kopierar du bara filerna för de språk som du vet att användarna behöver. Du kan dela upp filerna genom att titta på språkkoderna i deras filnamn. Den franska filen har till exempel koden "fr-FR" i sitt namn. En fullständig lista över språkkoder för alla tillgängliga språk finns i [Tillgängliga språkpaket för Windows.](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)

     >[!IMPORTANT]
     > Vissa språk kräver ytterligare teckensnitt som ingår i satellitpaket som följer olika namngivningskonventioner. Till exempel innehåller japanska teckensnittsfilnamn "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Ett exempel på japanska språkpaket med språktaggen "Jpan" i filnamnen.](media/language-pack-example.png)

6. Ange behörigheter för resursen för språkinnehållsdatabasen så att du har läsbehörighet från den virtuella dator som du ska använda för att skapa den anpassade avbildningen.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Skapa en anpassad Windows 10 Enterprise flersessionsavbildning manuellt

Så här skapar du en anpassad Windows 10 Enterprise en flersessionsavbildning manuellt:

1. Distribuera en virtuell Azure-dator och gå sedan till Azure-galleriet och välj den Windows 10 Enterprise version av en flersession som du använder.
2. När du har distribuerat den virtuella datorn ansluter du till den med RDP som lokal administratör.
3. Kontrollera att den virtuella datorn har alla de senaste Windows-uppdateringarna. Ladda ned uppdateringarna och starta om den virtuella datorn om det behövs.
4. Anslut till filresursdatabasen för språkpaket, FOD och inkorgsappar och montera den på en bokstavsenhet (till exempel enhet E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Skapa en anpassad Windows 10 Enterprise avbildning för flera sessioner automatiskt

Om du hellre vill installera språk via en automatiserad process kan du konfigurera ett skript i PowerShell. Du kan använda följande skriptexempel för att installera språkpaketen Spanska (Spanien), Franska (Frankrike) och Kinesiska (2004) och satellitpaket för Windows 10 Enterprise för flera sessioner, version 2004. Skriptet integrerar språkgränssnittspaketet och alla nödvändiga satellitpaket i bilden. Du kan dock också ändra det här skriptet för att installera andra språk. Se bara till att köra skriptet från en upphöjd PowerShell-session, annars fungerar det inte.

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

Skriptet kan ta en stund beroende på hur många språk du behöver installera.

När skriptet har körts kontrollerar du att språkpaketen har installerats korrekt genom att gå till  >    >  **Startinställningstid &**  >  **Språkspråk**. Om språkfilerna finns där är du redo.

När du har lagt till ytterligare språk i Windows-avbildningen måste även inkorgsapparna uppdateras för att stödja de tillagda språken. Detta kan göras genom att uppdatera de förinstallerade apparna med innehållet från inkorgsapparna ISO. Om du vill utföra den här uppdateringen i en frånkopplad miljö (ingen Internetåtkomst från den virtuella datorn möjligt) kan du använda följande PowerShell-skriptexempel för att automatisera processen.

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
>Inkorgsapparna som ingår i ISO är inte de senaste versionerna av de förinstallerade Windows-apparna. För att få den senaste versionen av alla appar måste du uppdatera apparna med hjälp av Windows Store-appen och utföra en manuell sökning efter uppdateringar när du har installerat ytterligare språk.

Se till att koppla från resursen när du är klar.

## <a name="finish-customizing-your-image"></a>Slutför anpassningen av avbildningen

När du har installerat språkpaketen kan du installera andra program som du vill lägga till i den anpassade avbildningen.

När du är klar med att anpassa avbildningen måste du köra systemförberedelseverktyget (sysprep).

Så här kör du sysprep:

1. Öppna en upphöjd kommandotolk och kör följande kommando för att generalisera avbildningen:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Stoppa den virtuella datorn och avbilda den sedan i en hanterad avbildning genom att följa anvisningarna i Skapa en hanterad avbildning av [en generaliserad virtuell dator i Azure](../virtual-machines/windows/capture-image-resource.md).

3. Du kan nu använda den anpassade avbildningen för att distribuera Windows Virtual Desktop en värdpool. Information om hur du distribuerar en värdpool finns [i Självstudie: Skapa en värdpool med Azure Portal](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Aktivera språk i appen för Windows-inställningar

När du har distribuerat värdpoolen måste du slutligen lägga till språket i varje användares språklista så att de kan välja önskat språk på menyn Inställningar.

För att säkerställa att användarna kan välja de språk som du har installerat loggar du in som användare och kör sedan följande PowerShell-cmdlet för att lägga till de installerade språkpaketen på språkmenyn. Du kan också konfigurera det här skriptet som en automatiserad uppgift eller ett inloggningsskript som aktiveras när användaren loggar in i sessionen.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

När en användare ändrar sina språkinställningar måste de logga ut från sin Windows Virtual Desktop-session och logga in igen för att ändringarna ska gälla. 

## <a name="next-steps"></a>Nästa steg

Om du är nyfiken på kända problem för språkpaket kan du läsa Lägga till språkpaket [i Windows 10 version 1803 och senare versioner: Kända problem.](/windows-hardware/manufacture/desktop/language-packs-known-issue)

Om du har andra frågor om Windows 10 Enterprise flera sessioner kan du läsa våra vanliga [frågor och svar.](windows-10-multisession-faq.yml)
