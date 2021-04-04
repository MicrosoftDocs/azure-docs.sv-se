---
title: Konfigurera Windows Virtual Desktop MSIX-appen bifoga PowerShell-skript – Azure
description: Så här skapar du PowerShell-skript för MSIX-appen Anslut för Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5e45c51735e0b7ab4b263d3f3047b5848c82439d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185775"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>Skapa PowerShell-skript för MSIX app attach (för hands version)

> [!IMPORTANT]
> MSIX app Attach är för närvarande en offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det här avsnittet beskriver hur du konfigurerar PowerShell-skript för MSIX app Attach.

>[!IMPORTANT]
>Innan du börjar ska du fylla i och skicka [det här formuläret](https://aka.ms/enablemsixappattach) för att aktivera MSIX app Attach i din prenumeration. Om du inte har en godkänd begäran fungerar inte MSIX-appens koppling. Godkännande av förfrågningar kan ta upp till 24 timmar under arbets dagar. Du får ett e-postmeddelande när din begäran har godkänts och slutförts.

## <a name="install-certificates"></a>Installera certifikat

Du måste installera certifikat på alla värddatorer i den modempool som ska vara värd för åtkomst punkterna från MSIX-appen bifoga paket.

Om din app använder ett certifikat som inte är offentligt betrott eller själv signerat, så gör du så här för att installera det:

1. Högerklicka på paketet och välj **Egenskaper**.
2. I fönstret som visas väljer du fliken **digitala signaturer** . Det får bara finnas ett objekt i listan på fliken, som du ser i följande bild. Markera objektet för att markera objektet och välj sedan **information**.
3. När fönstret information om digital signatur visas väljer du fliken **Allmänt** och sedan **Visa certifikat** och väljer sedan **Installera certifikat**.
4. När installations programmet öppnas väljer du **lokal dator** som lagrings plats och väljer sedan **Nästa**.
5. Om du tillfrågas om du vill tillåta att appen gör ändringar på enheten väljer du **Ja**.
6. Välj **Placera alla certifikat i följande Arkiv** och välj **Bläddra**.
7. När fönstret Välj certifikat Arkiv visas väljer du **Betrodda personer** och väljer sedan **OK**.
8. Välj **Nästa** och **Slutför**.

## <a name="enable-microsoft-hyper-v"></a>Aktivera Microsoft Hyper-V

Microsoft Hyper-V måste aktive ras eftersom `Mount-VHD` kommandot krävs för att mellanlagra och `Dismount-VHD` det krävs för att kunna avmellanlagra.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Den här ändringen kräver att du startar om den virtuella datorn.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Förbereda PowerShell-skript för MSIX app Attach

MSIX app Attach har fyra distinkta faser som måste utföras i följande ordning:

1. Fas
2. Registrera dig
3. Avregistrera
4. Destage

Varje fas skapar ett PowerShell-skript. Exempel skript för varje fas finns [här](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-powershell-script"></a>Steg-PowerShell-skript

Innan du uppdaterar PowerShell-skripten ser du till att du har volymens GUID för volymen på den virtuella hård disken. Så här hämtar du volymens GUID:

1.  Öppna nätverks resursen där den virtuella hård disken finns i den virtuella datorn där du kör skriptet.

2.  Högerklicka på den virtuella hård disken och välj **montera**. Detta kommer att montera den virtuella hård disken till en enhets beteckning.

3.  När du har monterat den virtuella hård disken **öppnas fönstret Utforskaren** . Avbilda den överordnade mappen och uppdatera variabeln **$parentFolder**

    >[!NOTE]
    >Om du inte ser en överordnad mapp betyder det att MSIX inte expanderades korrekt. Gör om föregående avsnitt och försök igen.

4.  Öppna den överordnade mappen. Om den är korrekt expanderad visas en mapp med samma namn som paketet. Uppdatera variabeln **$PackageName** så att den matchar namnet på den här mappen.

    Till exempel `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Öppna en kommando tolk och ange **mountvol**. Det här kommandot visar en lista över volymer och deras GUID. Kopiera GUID för volymen där enhets beteckningen matchar den enhet som du monterade din virtuella hård disk till i steg 2.

    Exempel: i det här exemplet på utdata för kommandot mountvol, om du har monterat din virtuella hård disk till enhet C, ska du kopiera värdet ovan `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Uppdatera variabeln **$volumeGuid** med volym-GUID som du nyss kopierade.

7. Öppna en admin PowerShell-prompt och uppdatera följande PowerShell-skript med de variabler som gäller för din miljö.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Registrera PowerShell-skript

Kör register skriptet genom att köra följande PowerShell-cmdletar med plats hållarnas värden ersatta med värden som gäller för din miljö.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>Avregistrera PowerShell-skript

I det här skriptet ersätter du plats hållaren för **$PackageName** med namnet på det paket som du testar.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Destage PowerShell-skript

I det här skriptet ersätter du plats hållaren för **$PackageName** med namnet på det paket som du testar. I en produktions distribution är det bäst att köra detta vid avstängning.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Konfigurera simulerings skript för MSIX-appens kopplings agent

När du har skapat skripten kan användarna köra dem manuellt eller ställa in dem för att köra automatiskt som start, inloggning, utloggning och avstängnings skript. Mer information om dessa typer av skript finns i [använda skript för start, avstängning, inloggning och utloggning i Grupprincip](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Vart och ett av dessa automatiska skript kör en fas i appen bifoga skript:

- Start skriptet kör Stadium skriptet.
- Inloggnings skriptet kör register skriptet.
- Utloggnings skriptet kör Avregistrerings skriptet.
- Avstängnings skriptet kör destage-skriptet.

## <a name="use-packages-offline"></a>Använda paket offline

Om du använder paket från [Microsoft Store för företag](https://businessstore.microsoft.com/) eller [Microsoft Store för utbildning](https://educationstore.microsoft.com/) i nätverket eller på enheter som inte är anslutna till Internet, måste du hämta paket licenser från Microsoft Store och installera dem på enheten för att kunna köra appen. Om enheten är online och kan ansluta till Microsoft Store för företag, bör de licenser som krävs hämtas automatiskt, men om du är offline måste du konfigurera licenserna manuellt.

Om du vill installera licensfiler måste du använda ett PowerShell-skript som anropar MDM_EnterpriseModernAppManagement_StoreLicenses02_01-klassen i WMI-providern.

Så här konfigurerar du licenserna för offline-användning:

1. Ladda ned programpaketet, licenserna och de ramverk som krävs från Microsoft Store för företag. Du behöver både kodade och kodade licensfiler. Detaljerade instruktioner för hämtning hittar du [här](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Uppdatera följande variabler i skriptet för steg 3:
      1. `$contentID` är ContentID-värdet från den kodade licens filen (. xml). Du kan öppna licens filen i valfri text redigerare.
      2. `$licenseBlob` är hela strängen för licens-bloben i den kodade licens filen (. bin). Du kan öppna den kodade licens filen i valfri text redigerare.
3. Kör följande skript från en admin PowerShell-prompt. En bra plats för att utföra licens installationen är i slutet av det [mellanlagrings skript](#stage-powershell-script) som måste köras från en administratörs prompt.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Nästa steg

Den här funktionen stöds inte för närvarande, men du kan ställa frågor till communityn på den [virtuella Windows-datorn TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för virtuella Windows-datorer i [hubben Windows Virtual Desktop feedback](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
