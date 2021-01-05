---
title: Anslut till Azure Resource Manager på din Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du ansluter till Azure Resource Manager som körs på din Azure Stack Edge Pro-GPU med Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 6bf0da8716233178889d47ec3d57e9b29bc2658f
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763225"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Anslut till Azure Resource Manager på din Azure Stack Edge Pro-enhet

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Azure Resource Manager har ett hanteringslager som hjälper dig att skapa, uppdatera och ta bort resurser i din Azure-prenumeration. Azure Stack Edge Pro-enheten stöder samma Azure Resource Manager-API: er för att skapa, uppdatera och ta bort virtuella datorer i en lokal prenumeration. Med det här stödet kan du hantera enheten på ett sätt som är konsekvent med molnet. 

I den här självstudien beskrivs hur du ansluter till lokala API: er på din Azure Stack Edge Pro-enhet via Azure Resource Manager med Azure PowerShell.

## <a name="about-azure-resource-manager"></a>Om Azure Resource Manager

Azure Resource Manager tillhandahåller ett konsekvent hanterings lager för att anropa API: et för Azure Stack Edge Pro-enheten och utföra åtgärder som att skapa, uppdatera och ta bort virtuella datorer. Arkitekturen i Azure Resource Manager beskrivs i följande diagram.

![Diagram för Azure Resource Manager](media/azure-stack-edge-j-series-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Slut punkter för Azure Stack Edge Pro-enhet

I följande tabell sammanfattas olika slut punkter som exponeras på din enhet, de protokoll som stöds och portarna för att få åtkomst till dessa slut punkter. I artikeln hittar du referenser till dessa slut punkter.

| # | Slutpunkt | Protokoll som stöds | Port som används | Används för |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Så här ansluter du till Azure Resource Manager för Automation |
| 2. | Security Token Service | https | 443 | Autentisera via åtkomst och uppdatera tokens |
| 3. | Blob | https | 443 | Ansluta till Blob Storage via REST |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Ansluter till Azure Resource Manager-arbetsflöde

Processen för att ansluta till lokala API: er för enheten med hjälp av Azure Resource Manager kräver följande steg:

| Aktivitets # | Du kommer att göra detta steg... | .. på den här platsen. |
| --- | --- | --- |
| 1. | [Konfigurera din Azure Stack Edge Pro-enhet](#step-1-configure-azure-stack-edge-pro-device) | Lokalt webb gränssnitt |
| 2. | [Skapa och installera certifikat](#step-2-create-and-install-certificates) | Windows-klient/lokalt webb gränssnitt |
| 3. | [Granska och konfigurera förutsättningarna](#step-3-install-powershell-on-the-client) | Windows-klient |
| 4. | [Konfigurera Azure PowerShell på klienten](#step-4-set-up-azure-powershell-on-the-client) | Windows-klient |
| 5. | [Ändra värd fil för slut punkts namn matchning](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows-klient eller DNS-Server |
| 6. | [Kontrol lera att slut punktens namn är löst](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows-klient |
| 7. | [Använd Azure PowerShell-cmdletar för att verifiera anslutningen till Azure Resource Manager](#step-7-set-azure-resource-manager-environment) | Windows-klient |

I följande avsnitt beskrivs var och en av stegen ovan för att ansluta till Azure Resource Manager.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att klienten som används för att ansluta till enheten via Azure Resource Manager använder TLS 1,2. Mer information finns i [Konfigurera TLS 1,2 på Windows-klienten som har åtkomst till Azure Stack Edge Pro-enhet](azure-stack-edge-j-series-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>Steg 1: Konfigurera Azure Stack Edge Pro-enhet 

Utför följande steg i det lokala webb gränssnittet för din Azure Stack Edge Pro-enhet.

1. Slutför nätverks inställningarna för din Azure Stack Edge Pro-enhet. 

    ![Sidan nätverks inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Anteckna IP-adressen för enheten. Du kommer att använda den här IP-adressen senare.

2. Konfigurera enhets namnet och DNS-domänen från **enhets** sidan. Anteckna enhets namnet och DNS-domänen som du kommer att använda senare.

    ![Sidan enhet för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > Enhetens namn, DNS-domän kommer att användas för att forma de slut punkter som visas.
    > Använd Azure Resource Manager-och blob-slutpunkterna från **enhets** sidan i det lokala webb gränssnittet.


## <a name="step-2-create-and-install-certificates"></a>Steg 2: skapa och installera certifikat

Certifikaten garanterar att kommunikationen är betrodd. På din Azure Stack Edge Pro-enhet genereras självsignerade installationer, blob-och Azure Resource Manager-certifikat automatiskt. Du kan också ta med en egen signerad blob och Azure Resource Manager certifikat.

När du använder ett signerat certifikat måste du också ha motsvarande signerings kedja av certifikatet. För signerings kedjan, Azure Resource Manager och blob-certifikaten på enheten behöver du även motsvarande certifikat på klient datorn för att autentisera och kommunicera med enheten.

Om du vill ansluta till Azure Resource Manager måste du skapa eller hämta signerings kedjan och slut punkts certifikat, importera certifikaten på Windows-klienten och slutligen Ladda upp certifikaten på enheten.

### <a name="create-certificates-optional"></a>Skapa certifikat (valfritt)

I test-och utvecklings syfte kan du använda Windows PowerShell för att skapa certifikat i det lokala systemet. Följ dessa rikt linjer när du skapar certifikat för klienten:

1. Du måste först skapa ett rot certifikat för signerings kedjan. Mer information finns i steg för att [skapa signerings kedjans certifikat](azure-stack-edge-j-series-manage-certificates.md#create-signing-chain-certificate).

2. Du kan sedan skapa slut punkts certifikat för blobben och Azure Resource Manager. Du kan hämta dessa slut punkter från **enhets** sidan i det lokala webb gränssnittet. Se stegen för att [skapa slut punkts certifikat](azure-stack-edge-j-series-manage-certificates.md#create-signed-endpoint-certificates).

3. För alla dessa certifikat ser du till att ämnes namnet och det alternativa ämnes namnet överensstämmer med följande rikt linjer:

    |Typ |Ämnes namn (SN)  |Alternativt namn för certifikat mottagare (SAN)  |Exempel på ämnes namn |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Flera SAN-certifikat för båda slut punkterna|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

Mer information om certifikat finns i [Hantera certifikat](azure-stack-edge-j-series-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Ladda upp certifikat på enheten

De certifikat som du skapade i föregående steg kommer att finnas i det personliga arkivet på klienten. Dessa certifikat måste exporteras till klienten i lämpliga format filer som sedan kan överföras till din enhet.

1. Rot certifikatet måste exporteras som en fil med fil namns tillägget *. cer* . Detaljerade anvisningar finns i [Exportera certifikat som en CER-fil](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-der-format).

2. Slut punkts certifikaten måste exporteras som *PFX* -filer med privata nycklar. Detaljerade anvisningar finns i [Exportera certifikat som. pfx-fil med privata nycklar](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. Rot-och slut punkts certifikaten laddas sedan upp på enheten med alternativet **+ Lägg till certifikat** på sidan **certifikat** i det lokala webb gränssnittet. Följ stegen i [Ladda upp certifikat](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)för att ladda upp certifikaten.


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Importera certifikat på klienten som kör Azure PowerShell

Windows-klienten där du ska anropa Azure Resource Manager API: er måste upprätta förtroende med enheten. Därför måste de certifikat som du skapade i föregående steg importeras på Windows-klienten till lämpligt certifikat arkiv.

1. Rot certifikatet som du exporterade som DER-format med *. cer* -tillägget bör nu importeras till certifikat utfärdare för betrodda rot certifikat i klient systemet. Detaljerade anvisningar finns i [Importera certifikat till arkivet Betrodda rot certifikat utfärdare.](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format)

2. Slut punkts certifikaten som du exporterade som *. pfx* måste exporteras som *. cer*. Den här *. cer* -filen importeras sedan i det **personliga** certifikat arkivet på datorn. Detaljerade anvisningar finns i [Importera certifikat till det personliga arkivet](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>Steg 3: installera PowerShell på klienten 

Windows-klienten måste uppfylla följande krav:

1. Kör PowerShell version 5,0. Du måste ha PowerShell version 5,0 eller senare. Kör följande cmdlet för att kontrol lera versionen av PowerShell på systemet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Jämför **huvud** versionen och se till att den är 5,0 eller senare.

    Läs informationen om att [uppgradera befintliga Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) om du har en inaktuell version.

    Om du inte \' har PowerShell 5,0 följer du [installationen av Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6).

    Ett exempel på utdata visas nedan.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. Du kan komma åt PowerShell-galleriet.

    Kör PowerShell som administratör. Kontrol lera om PSGallery har registrerats som en lagrings plats.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Ett exempel på utdata visas nedan.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Om din lagrings plats inte är betrodd eller om du behöver mer information, se [verifiera PowerShell-galleriet hjälpmedel](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Steg 4: Konfigurera Azure PowerShell på klienten 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Du kommer att installera Azure PowerShell-moduler på klienten som ska fungera med din enhet.

    a. Kör PowerShell som administratör. Du behöver åtkomst till PowerShell-galleriet. 


    b. Kör följande kommando för att installera nödvändiga Azure PowerShell-moduler från PowerShell-galleriet:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Kontrol lera att du har version 2.5.0 av Azure-RM-modulen som körs i slutet av installationen. 
    Om du har en befintlig version av Azure-RM-modul som inte matchar den version som krävs avinstallerar du med hjälp av följande kommando:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Du måste nu installera den version som krävs.
   

Ett exempel på utdata visas nedan som anger att modulerna AzureRM version 2.5.0 har installerats.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Steg 5: ändra värd filen för slut punkts namn matchning 

Nu ska du lägga till den Azure-enhetliga VIP som du definierade i det lokala webb gränssnittet för enheten för att:

- Värd filen på klienten eller,
- DNS-serverkonfigurationen

> [!IMPORTANT]
> Vi rekommenderar att du ändrar DNS-serverkonfigurationen för slut punkts namn matchning.

Utför följande steg på den Windows-klient som du använder för att ansluta till enheten:

1. Starta **anteckningar** som administratör och öppna sedan **hosts** -filen som finns på C:\WINDOWS\SYSTEM32\DRIVERS\ETC.

    ![Windows Explorer Hosts-fil](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)

2. Lägg till följande poster i **hosts** -filen och Ersätt med lämpliga värden för din enhet: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > Posten i hosts-filen måste matcha exakt den som angavs för att ansluta till Azure Resource Manager i ett senare steg. Kontrol lera att DNS-domännamnet här är allt i gemener.

    Du sparade enhetens IP-adress från det lokala webb gränssnittet i ett tidigare steg.

    Inloggningen. \<appliance name\> .\<DNS domain\> posten är slut punkten för Security Token Service (STS). STS ansvarar för att skapa, verifiera, förnya och annullera säkerhetstoken. Säkerhetstokentjänst används för att skapa en åtkomsttoken och en uppdaterad token som används för kontinuerlig kommunikation mellan enheten och klienten.

3. Använd följande bild som referens. Spara **värdens** fil.

    ![Hosts-fil i anteckningar](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Steg 6: kontrol lera slut punktens namn matchning på klienten

Kontrol lera att slut punkts namnet har matchats på klienten som du använder för att ansluta till Azure-konsekvent VIP.

1. Du kan använda kommando rads verktyget ping.exe för att kontrol lera att slut punkts namnet är löst. Med en IP-adress returnerar Ping-kommandot TCP/IP-värdnamnet för datorn som du \' spårar.

    Lägg till `-a` växeln på kommando raden som visas i exemplet nedan. Om värd namnet är återaktiverat returneras även denna potentiellt värdefulla information i svaret.

    ![Ping i kommando tolken](media/azure-stack-edge-j-series-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Steg 7: Ange Azure Resource Manager miljö

Ange Azure Resource Managers miljö och kontrol lera att enheten för klient kommunikation via Azure Resource Manager fungerar bra. Utför följande steg för den här verifieringen:


1. Använd cmdleten `Add-AzureRmEnvironment` för att se till att kommunikationen via Azure Resource Manager fungerar korrekt och att API-anropen går via den port som är dedikerad för Azure Resource Manager, 443.

    Cmdleten `Add-AzureRmEnvironment` lägger till slutpunkter och metadata så att Azure Resource Manager-cmdletar kan ansluta till en ny instans av Azure Resource Manager. 


    > [!IMPORTANT]
    > Den Azure Resource Manager slut punkts-URL som du anger i följande cmdlet är Skift läges känslig. Se till att slut punkts-URL: en är i gemener och matchar det du angav i hosts-filen. Om ärendet inte matchar visas ett fel meddelande.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Ett exempel på utdata visas nedan:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Konfigurera miljön som Azure Stack Edge Pro och porten som ska användas för anrop till Azure Resource Manager som 443. Du definierar miljön på två sätt:

    - Konfigurera miljön. Ange följande kommando:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Mer information finns på [set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0).

    - Definiera miljö inlinjen för varje cmdlet som du kör. Detta säkerställer att alla API-anrop går igenom rätt miljö. Som standard går anropen via Azures offentliga, men du vill att de ska gå igenom den miljö som du har angett för Azure Stack Edge Pro-enhet.

    - Se mer information om [hur du växlar AzureRM-miljöer](#switch-environments).

2. Anropa lokala enhets-API:er för att autentisera anslutningarna till Azure Resource Manager. 

    1. Dessa autentiseringsuppgifter är för ett lokalt dator konto och används enbart för API-åtkomst.

    2. Du kan ansluta via `login-AzureRMAccount` eller via `Connect-AzureRMAccount` kommando. 

        1. Logga in genom att skriva följande kommando. Klient-ID: t i den här instansen är hårdkodat-c0257de7-538f-415c-993a-1b87a031879d. Använd följande användar namn och lösen ord.

            - **Användar namn**  -  *EdgeArmUser*

            - **Lösen ord**  -  [Ange lösen ordet för Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md) och Använd det här lösen ordet för att logga in. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Ett annat sätt att logga in är att använda `login-AzureRmAccount` cmdleten. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` -TenantId c0257de7-538f-415c-993a-1b87a031879d 

            Här är ett exempel på utdata från kommandot. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> Anslutningen till Azure Resource Manager upphör att gälla var 1,5: e timme eller om din Azure Stack Edge Pro-enhet startas om. Om detta händer kommer alla cmdletar som du kör att returnera fel meddelanden till den inverkan som du inte är ansluten till Azure längre. Du måste logga in igen.

## <a name="switch-environments"></a>Växla miljöer

Kör `Disconnect-AzureRmAccount` kommando för att växla till en annan `AzureRmEnvironment` . 

Om du använder `Set-AzureRmEnvironment` och `Login-AzureRmAccount` utan att använda `Disconnect-AzureRmAccount` omväxlas miljön faktiskt inte.  

I följande exempel visas hur du växlar mellan två miljöer `AzDBE1` och `AzDBE2` .

Börja med att lista alla befintliga miljöer på klienten.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Sedan kan du hämta vilken miljö du för närvarande är ansluten till via din Azure Resource Manager.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Du bör nu koppla bort från den aktuella miljön innan du växlar till den andra miljön.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Logga in på den andra miljön. Exemplet på utdata visas nedan.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Kör denna cmdlet för att bekräfta vilken miljö som du är ansluten till.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Du har nu bytt till den avsedda miljön.

## <a name="next-steps"></a>Nästa steg

[Distribuera virtuella datorer på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).