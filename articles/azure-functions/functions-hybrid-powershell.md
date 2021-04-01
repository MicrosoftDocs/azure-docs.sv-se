---
title: Hantera lokala lokala resurser med hjälp av PowerShell-funktioner
description: Lär dig hur du konfigurerar Hybridanslutningar i Azure Relay för att ansluta en PowerShell Function-app till lokala resurser, som sedan kan användas för att fjärrhantera den lokala resursen.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 5e01ffd8e17fda9113c7ec0fdb2c7f436b39c810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936914"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hantera hybrid miljöer med PowerShell i Azure Functions och App Service Hybridanslutningar

Funktionen Azure App Service Hybridanslutningar ger åtkomst till resurser i andra nätverk. Du kan lära dig mer om den här funktionen i [hybridanslutningar](../app-service/app-service-hybrid-connections.md) -dokumentationen. Den här artikeln beskriver hur du använder den här funktionen för att köra PowerShell-funktioner som riktar sig mot en lokal server. Den här servern kan sedan användas för att hantera alla resurser i den lokala miljön från en Azure PowerShell-funktion.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Konfigurera en lokal server för PowerShell-fjärrkommunikation

Följande skript aktiverar PowerShell-fjärrkommunikation och skapar en ny brand Väggs regel och en WinRM https-lyssnare. I test syfte används ett självsignerat certifikat. I en produktions miljö rekommenderar vi att du använder ett signerat certifikat.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Skapa en PowerShell Function-app i portalen

App Service Hybridanslutningar-funktionen är bara tillgänglig i de grundläggande, standard-och isolerade pris planerna. När du skapar Function-appen med PowerShell skapar eller väljer du någon av dessa planer.

1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

1. På den **nya** sidan väljer du **Compute**  >  **Funktionsapp**.

1. På sidan **grundläggande** inställningar använder du funktionen appinställningar som anges i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Funktionsappens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.  |
    |**Publicera**| Kod | Alternativ för att publicera kodfiler eller en Docker-container. |
    | **Körningsstack** | Önskat språk | Välj PowerShell Core. |
    |**Version**| Versionsnummer | Välj den version av den installerade körnings miljön.  |
    |**Region**| Önskad region | Välj en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner kommer åt. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Skapa en Function-app – grundläggande information." border="true":::

1. Välj **Nästa: Värd**. Ange följande inställningar på sidan **värd** .

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../storage/common/storage-account-create.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får bara innehålla siffror och gemena bokstäver. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operativsystem**| Önskat operativ system | Ett operativ system är i förväg valt för dig baserat på ditt val av körnings stack, men du kan ändra inställningen om det behövs. |
    | **[Plantyp](../azure-functions/functions-scale.md)** | **App Service-plan** | Välj **App Service-plan**. När du använder en App Service-plan måste du hantera [funktionsappens skalning](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Skapa en funktion app-hosting." border="true":::

1. Välj **Nästa: övervakning**. Ange följande inställningar på sidan **övervakning** .

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Standardvärde | Skapar en Application Insights resurs av samma *app-namn* i den närmaste region som stöds. Genom att utöka den här inställningen eller välja **Skapa ny** kan du ändra Application Insights namn eller välja en annan region i ett [Azure-geografiskt](https://azure.microsoft.com/global-infrastructure/geographies/) område där du vill lagra dina data. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Skapa en funktion app-Monitoring." border="true":::

1. Välj **Granska + skapa** för att granska konfigurations valen för appen.

1. På sidan **Granska + skapa** granskar du inställningarna och väljer sedan **skapa** för att etablera och distribuera Function-appen.

1. Välj **aviserings** ikonen i det övre högra hörnet i portalen och titta efter ett meddelande om att **distributionen har slutförts** .

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst vid instrument panelen**. Genom att fästa blir det enklare att återgå till den här funktions program resursen från instrument panelen.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Skapa en hybrid anslutning för Function-appen

Hybrid anslutningar konfigureras från avsnittet nätverk i Function-appen:

1. Under **Inställningar** i Function-appen som du nyss skapade väljer du **nätverk**. 
1. Välj **Konfigurera dina hybrid anslutningar slut punkter**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="Konfigurera hybrid anslutnings slut punkter." border="true":::

1. Välj **Lägg till hybrid anslutning**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Lägg till en hybrid anslutning." border="true":::

1. Ange information om hybrid anslutningen som visas direkt efter följande skärm bild. Du kan välja att göra **slut punkts värd** inställningen matcha värd namnet för den lokala servern för att göra det lättare att komma ihåg servern senare när du kör fjärrkommandon. Porten matchar standard porten för Windows Remote Management-tjänsten som definierades på servern tidigare.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Lägg till hybrid anslutning." border="true":::

    | Inställning      | Föreslaget värde  |
    | ------------ | ---------------- |
    | **Namn på Hybrid anslutning** | ContosoHybridOnPremisesServer |
    | **Slut punkts värd** | finance1 |
    | **Slut punkts port** | 5986 |
    | **Service Bus-namnrymd** | Skapa Ny |
    | **Plats** | Välj en tillgänglig plats |
    | **Namn** | contosopowershellhybrid | 

1. Välj **OK** för att skapa hybrid anslutningen.

## <a name="download-and-install-the-hybrid-connection"></a>Ladda ned och installera hybrid anslutningen

1. Välj **Hämta anslutnings hanteraren** för att spara *. msi* -filen lokalt på datorn.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Hämta installations programmet." border="true":::

1. Kopiera *MSI* -filen från den lokala datorn till den lokala servern.
1. Kör installations programmet för Hybridanslutningshanteraren för att installera tjänsten på den lokala servern.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Installera hybrid anslutningen." border="true":::

1. Öppna hybrid anslutningen från portalen och kopiera sedan Gateway-anslutningssträngen till Urklipp.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Kopiera hybrid anslutnings strängen." border="true":::

1. Öppna Hybridanslutningshanteraren gränssnittet på den lokala servern.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Öppna användar gränssnittet hybrid anslutning." border="true":::

1. Välj **ange manuellt** och klistra in anslutnings strängen från Urklipp.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Klistra in hybrid anslutningen." border="true":::

1. Starta om Hybridanslutningshanteraren från PowerShell om den inte visas som ansluten.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Skapa en app-inställning för lösen ordet för ett administratörs konto

1. Under **Inställningar** för din Function-app väljer du **konfiguration**. 
1. Välj **+ ny program inställning**.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Konfigurera ett lösen ord för administratörs kontot." border="true":::

1. Ge inställningen namnet **ContosoUserPassword** och ange lösen ordet. Välj **OK**.
1. Välj **Spara** för att lagra lösen ordet i Function-programmet.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Spara lösen ordet för administratörs kontot." border="true":::

## <a name="create-a-function-http-trigger"></a>Skapa en funktion HTTP-utlösare

1. I din Function-app väljer du **Functions** och väljer sedan **+ Lägg till**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Skapa en ny HTTP-utlösare." border="true":::

1. Välj mallen för **http-utlösare** .

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Välj mallen för HTTP-utlösare." border="true":::

1. Ge den nya funktionen ett namn och välj **skapa funktion**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Namnge och skapa den nya funktionen HTTP-utlösare." border="true":::

## <a name="test-the-function"></a>Testa funktionen

1. I den nya funktionen väljer du **kod + test**. Ersätt PowerShell-koden från mallen med följande kod:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

1. Välj **Spara**.

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Ändra PowerShell-koden och spara funktionen HTTP-utlösare." border="true":::

 1. Välj **test** och välj sedan **Kör** för att testa funktionen. Granska loggarna för att kontrol lera att testet lyckades.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="Testa HTTP trigger funktion." border="true":::

## <a name="managing-other-systems-on-premises"></a>Hantera andra system lokalt

Du kan använda den anslutna lokala servern för att ansluta till andra servrar och hanterings system i den lokala miljön. På så sätt kan du hantera data Center åtgärder från Azure med hjälp av PowerShell-funktionerna. Följande skript registrerar en PowerShell-klientsession som körs under de angivna autentiseringsuppgifterna. Autentiseringsuppgifterna måste vara för en administratör på fjärrservrarna. Du kan sedan använda den här konfigurationen för att få åtkomst till andra slut punkter på den lokala servern eller data centret.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Ersätt följande variabler i skriptet med tillämpliga värden från din miljö:
* $HybridEndpoint
* $RemoteServer

I de två föregående scenarierna kan du ansluta och hantera dina lokala miljöer med hjälp av PowerShell i Azure Functions och Hybridanslutningar. Vi rekommenderar att du lär dig mer om [hybridanslutningar](../app-service/app-service-hybrid-connections.md) och [PowerShell i functions](./functions-reference-powershell.md).

Du kan också använda virtuella Azure- [nätverk](./functions-create-vnet.md) för att ansluta till din lokala miljö via Azure Functions.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Lär dig mer om att arbeta med PowerShell-funktioner](functions-reference-powershell.md)
