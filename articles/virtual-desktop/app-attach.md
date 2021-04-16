---
title: Konfigurera Windows Virtual Desktop MSIX-appen bifoga PowerShell-skript – Azure
description: Så här skapar du PowerShell-skript för MSIX-app bifoga för Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d1ca4a843c6731cde7ed70d65fc230a21ef6e7c4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389442"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>Skapa PowerShell-skript för att koppla MSIX-appen

I det här avsnittet får du lära dig hur du ställer in PowerShell-skript för att koppla MSIX-appar.

## <a name="install-certificates"></a>Installera certifikat

Du måste installera certifikat på alla sessionsvärdar i värdpoolen som ska vara värd för apparna från msix-appens anslutningspaket.

Om din app använder ett certifikat som inte är offentligt betrott eller som har själv signerats gör du så här för att installera det:

1. Högerklicka på paketet och välj **Egenskaper.**
2. I fönstret som visas väljer du **fliken Digitala signaturer.** Det bör bara finnas ett objekt i listan på fliken, som du ser i följande bild. Markera objektet för att markera objektet och välj sedan **Information.**
3. När fönstret med information om den digitala signaturen visas väljer **du fliken** Allmänt och sedan **Visa certifikat** och sedan **Installera certifikat.**
4. När installationsprogrammet öppnas väljer **du lokal dator** som lagringsplats och väljer sedan **Nästa.**
5. Om installationsprogrammet frågar dig om du vill tillåta att appen gör ändringar på enheten väljer du **Ja.**
6. Välj **Placera alla certifikat i följande arkiv** och välj sedan **Bläddra.**
7. När fönstret välj certifikatarkiv visas väljer du **Betrodda personer** och sedan **OK.**
8. Välj **Nästa** och **Slutför**.

## <a name="enable-microsoft-hyper-v"></a>Aktivera Microsoft Hyper-V

Microsoft Hyper-V måste vara aktiverat eftersom `Mount-VHD` kommandot krävs för att mellanfasa och behövs för att `Dismount-VHD` destage.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Den här ändringen kräver att du startar om den virtuella datorn.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Förbereda PowerShell-skript för BIFOGA MSIX-app

MSIX-app attach har fyra distinkta faser som måste utföras i följande ordning:

1. Fas
2. Registrera dig
3. Avregistrera
4. Destage

Varje fas skapar ett PowerShell-skript. Exempelskript för varje fas finns [här.](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)

### <a name="stage-powershell-script"></a>Mellan olika PowerShell-skript

Innan du uppdaterar PowerShell-skripten kontrollerar du att volymens GUID finns på den virtuella hårddisken. Så här hämtar du volymens GUID:

1.  Öppna nätverksresursen där den virtuella hårddisken finns i den virtuella datorn där du ska köra skriptet.

2.  Högerklicka på den virtuella hårddisken och välj **Montera**. Den virtuella hårddisken monteras på en enhetsbeteckning.

3.  När du har monterat den **virtuella Utforskaren** öppnas fönstret. Avbilda den överordnade mappen och uppdatera **$parentFolder** variabeln

    >[!NOTE]
    >Om du inte ser en överordnad mapp innebär det att MSIX inte har expanderats korrekt. Gör om föregående avsnitt och försök igen.

4.  Öppna den överordnade mappen. Om det är korrekt expanderat visas en mapp med samma namn som paketet. Uppdatera **$packageName** så att den matchar namnet på den här mappen.

    Till exempel `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Öppna en kommandotolk och ange **mountvol**. Det här kommandot visar en lista över volymer och deras GUID. Kopiera GUID för volymen där enhetsbeteckningen matchar den enhet som du monterade den virtuella hårddisken till i steg 2.

    Om du till exempel har monterat den virtuella hårddisken på enhet C i det här exempelutdata för mountvol-kommandot bör du kopiera värdet `C:\` ovan:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Uppdatera **variabel$ $volumeGuid** volymens GUID som du nyss kopierade.

7. Öppna en PowerShell-administratörsfråga och uppdatera följande PowerShell-skript med de variabler som gäller för din miljö.

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

Kör registerskriptet genom att köra följande PowerShell-cmdlets med platshållarvärdena ersatta med värden som gäller för din miljö.

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

För det här skriptet ersätter du **platshållaren för $packageName** med namnet på det paket som du testar.

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

För det här skriptet ersätter du **platshållaren för $packageName** med namnet på det paket som du testar. I en produktionsdistribution är det bäst att köra detta vid avstängning.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Konfigurera simuleringsskript för MSIX-appens anslutningsagent

När du har skapat skripten kan användarna köra dem manuellt eller konfigurera dem så att de körs automatiskt som start-, inloggnings-, utloggnings- och avstängningsskript. Mer information om dessa typer av skript finns i Använda skript för [start, avstängning,](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)inloggning och utloggning i grupprincip .

Vart och ett av dessa automatiska skript kör en fas i appens bifogande skript:

- Startskriptet kör fasskriptet.
- Inloggningsskriptet kör registerskriptet.
- Utloggningsskriptet kör avregistreringsskriptet.
- Avstängningsskriptet kör destageskriptet.

## <a name="use-packages-offline"></a>Använda paket offline

Om du använder paket från [Microsoft Store för företag](https://businessstore.microsoft.com/) eller [Microsoft Store för utbildning](https://educationstore.microsoft.com/) i nätverket eller på enheter som inte är anslutna till Internet måste du hämta paketlicenserna från Microsoft Store och installera dem på enheten för att kunna köra appen. Om enheten är online och kan ansluta till Microsoft Store för företag bör nödvändiga licenser laddas ned automatiskt, men om du är offline måste du konfigurera licenserna manuellt.

Om du vill installera licensfilerna måste du använda ett PowerShell-skript som anropar MDM_EnterpriseModernAppManagement_StoreLicenses02_01 i WMI-bryggprovidern.

Så här ställer du in licenser för offlineanvändning:

1. Ladda ned appaketet, licenser och nödvändiga ramverk från Microsoft Store för företag. Du behöver både kodade och okodade licensfiler. Detaljerade nedladdningsanvisningar finns [här.](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)
2. Uppdatera följande variabler i skriptet för steg 3:
      1. `$contentID` är ContentID-värdet från den okodade licensfilen (.xml). Du kan öppna licensfilen i valfri textredigerare.
      2. `$licenseBlob` är hela strängen för licensbloben i den kodade licensfilen (.bin). Du kan öppna den kodade licensfilen i valfri textredigerare.
3. Kör följande skript från en Admin PowerShell-kommandotolk. En bra plats för att utföra licensinstallationen är i slutet av mellanlagringsskriptet [](#stage-powershell-script) som också måste köras från en administratörsuppmaning.

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

Den här funktionen stöds inte för närvarande, men du kan ställa frågor till communityn [på Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för Windows Virtual Desktop på Windows Virtual Desktop [feedbackhubben](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
