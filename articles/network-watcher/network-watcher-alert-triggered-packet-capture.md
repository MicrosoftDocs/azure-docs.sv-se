---
title: Använd paket fångst för proaktiv nätverks övervakning med aviseringar – Azure Functions
titleSuffix: Azure Network Watcher
description: Den här artikeln beskriver hur du skapar en avisering som utlöses av en paket insamling med Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 8e2af73be0fc887b132f523133159472ce1d1f98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570982"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Använda paket fångst för proaktiv nätverks övervakning med aviseringar och Azure Functions

Network Watcher Packet Capture skapar infångnings-sessioner för att spåra trafik in och ut ur virtuella datorer. Infångnings filen kan ha ett filter som har definierats för att endast spåra den trafik som du vill övervaka. Dessa data lagras sedan i en lagrings-BLOB eller lokalt på gäst datorn.

Den här funktionen kan startas via fjärr anslutning från andra automatiserings scenarier som Azure Functions. Med paket fångst får du möjlighet att köra proaktiva avbildningar baserat på definierade nätverks avvikelser. Andra användnings områden är insamling av nätverks statistik, hämtning av information om nätverks intrång, fel sökning av klient-server-kommunikation med mera.

Resurser som distribueras i Azure kör 24/7. Du och din personal kan inte aktivt övervaka status för alla resurser 24/7. Vad händer till exempel om ett problem uppstår vid 2 AM?

Genom att använda Network Watcher, aviseringar och funktioner i Azure-eko systemet kan du proaktivt reagera på data och verktyg för att lösa problem i nätverket.

![Diagrammet visar Network Watcher tillägget på en virtuell dator som flödar till ett T C P-segment som skickats > 100-fel, som flödar till Azure Functions, som flödar till Network Watcher som flödar tillbaka till Network Watcher-tillägget.][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Azure PowerShell](/powershell/azure/install-Az-ps).
* En befintlig instans av Network Watcher. Om du inte redan har en, [skapar du en instans av Network Watcher](network-watcher-create.md).
* En befintlig virtuell dator i samma region som Network Watcher med tillägget [Windows-tillägget](../virtual-machines/extensions/network-watcher-windows.md) eller [Linux-tillägget för virtuella datorer](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="scenario"></a>Scenario

I det här exemplet skickar den virtuella datorn fler TCP-segment än vanligt och du vill bli aviserad. TCP-segment används som exempel här, men du kan använda eventuella varnings villkor.

När du är aviserad vill du ta emot data på paket nivå för att förstå varför kommunikationen har ökat. Sedan kan du vidta åtgärder för att returnera den virtuella datorn till vanlig kommunikation.

Det här scenariot förutsätter att du har en befintlig instans av Network Watcher och en resurs grupp med en giltig virtuell dator.

Följande lista är en översikt över arbets flödet som äger rum:

1. En avisering utlöses på den virtuella datorn.
1. Aviseringen anropar Azure-funktionen via en webhook.
1. Din Azure-funktion bearbetar aviseringen och startar en Network Watcher Packet Capture-session.
1. Paket fångsten körs på den virtuella datorn och samlar in trafik.
1. Paket insamlings filen överförs till ett lagrings konto för granskning och diagnostisering.

För att automatisera den här processen skapar vi och ansluter en avisering på den virtuella datorn så att den utlöses när incidenten inträffar. Vi skapar även en funktion för att anropa Network Watcher.

Det här scenariot gör följande:

* Skapar en Azure-funktion som startar en paket fångst.
* Skapar en varnings regel på en virtuell dator och konfigurerar aviserings regeln för att anropa Azure-funktionen.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Det första steget är att skapa en Azure-funktion för att bearbeta aviseringen och skapa en paket fångst.

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs**  >  **beräknings**  >  **Funktionsapp**.

    ![Skapa en funktionsapp][1-1]

2. Ange följande värden på bladet **Funktionsapp** och välj sedan **OK** för att skapa appen:

    |**Inställning** | **Värde** | **Information** |
    |---|---|---|
    |**Appens namn**|PacketCaptureExample|Namnet på Function-appen.|
    |**Prenumeration**|[Din prenumeration] Den prenumeration som du vill skapa Function-appen för.||
    |**Resursgrupp**|PacketCaptureRG|Resurs gruppen som innehåller Function-appen.|
    |**Värdplan**|Förbrukningsplan| Typ av plan som din Function-app använder. Alternativen är förbruknings-eller Azure App Services planer. |
    |**Plats**|Central US| Den region där du vill skapa Function-appen.|
    |**Lagrings konto**|automatiskt skapade texter| Det lagrings konto som Azure Functions behöver för allmän lagring.|

3. På bladet **PacketCaptureExample Function-appar** väljer **du**  >  **anpassad funktion**  > **+** .

4. Välj **HttpTrigger-PowerShell** och ange sedan återstående information. Slutligen, för att skapa funktionen, väljer du **skapa**.

    |**Inställning** | **Värde** | **Information** |
    |---|---|---|
    |**Scenario**|Experimentell|Typ av scenario|
    |**Namnge din funktion**|AlertPacketCapturePowerShell|Namnet på funktionen|
    |**Auktoriseringsnivå**|Funktion|Behörighets nivå för funktionen|

![Functions-exempel][functions1]

> [!NOTE]
> PowerShell-mallen är experimentell och har inte fullständig support.

Anpassningar krävs för det här exemplet och beskrivs i följande steg.

### <a name="add-modules"></a>Lägga till moduler

Om du vill använda Network Watcher PowerShell-cmdlets laddar du upp den senaste PowerShell-modulen till Function-appen.

1. Kör följande PowerShell-kommando på den lokala datorn med de senaste Azure PowerShell-modulerna installerade:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    I det här exemplet får du den lokala sökvägen till dina Azure PowerShell-moduler. Dessa mappar används i ett senare steg. Modulerna som används i det här scenariot är:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell-mappar][functions5]

1. Välj **funktions program inställningar**  >  **gå till App Service Editor**.

    ![Funktionsappinställningar][functions2]

1. Högerklicka på mappen **AlertPacketCapturePowershell** och skapa sedan en mapp med namnet **azuremodules**. 

4. Skapa en undermapp för varje modul som du behöver.

    ![Mapp och undermappar][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Högerklicka på undermappen **AZ. Network** och välj sedan **Ladda upp filer**. 

6. Gå till dina Azure-moduler. I mappen lokal **AZ. Network** väljer du alla filer i mappen. Välj sedan **OK**. 

7. Upprepa de här stegen för **AZ. Accounts** och **AZ. Resources**.

    ![Ladda upp filer][functions6]

1. När du är klar ska varje mapp ha PowerShell-modulens filer från den lokala datorn.

    ![PowerShell-filer][functions7]

### <a name="authentication"></a>Autentisering

Om du vill använda PowerShell-cmdletar måste du autentisera dig. Du konfigurerar autentisering i Function-appen. Om du vill konfigurera autentisering måste du konfigurera miljövariabler och ladda upp en krypterad nyckel fil till Function-appen.

> [!NOTE]
> Det här scenariot ger bara ett exempel på hur du implementerar autentisering med Azure Functions. Det finns andra sätt att göra detta på.

#### <a name="encrypted-credentials"></a>Krypterade autentiseringsuppgifter

Följande PowerShell-skript skapar en nyckel fil med namnet **PassEncryptKey. Key**. Den innehåller också en krypterad version av lösen ordet som anges. Det här lösen ordet är samma lösen ord som definieras för det Azure Active Directory-program som används för autentisering.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

I App Service Editor av Function-appen skapar du en mapp med namnet **nycklar** under **AlertPacketCapturePowerShell**. Ladda sedan upp filen **PassEncryptKey. Key** som du skapade i föregående PowerShell-exempel.

![Functions-nyckel][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Hämta värden för miljövariabler

Det slutliga kravet är att ställa in de miljövariabler som krävs för att få åtkomst till värdena för autentisering. I följande lista visas de miljövariabler som skapas:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Klient-ID är program-ID för ett program i Azure Active Directory.

1. Om du inte redan har ett program att använda kan du köra följande exempel för att skapa ett program.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Lösen ordet som du använder när du skapar programmet bör vara samma lösen ord som du skapade tidigare när du sparade nyckel filen.

1. I Azure Portal väljer du **prenumerationer**. Välj den prenumeration som du vill använda och välj sedan **åtkomst kontroll (IAM)**.

    ![Functions IAM][functions9]

1. Välj det konto som du vill använda och välj sedan **Egenskaper**. Kopiera program-ID: t.

    ![Programmets ID för functions][functions10]

#### <a name="azuretenant"></a>AzureTenant

Hämta klient-ID: t genom att köra följande PowerShell-exempel:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Värdet för AzureCredPassword-miljövariabeln är det värde som du får från att köra följande PowerShell-exempel. Det här exemplet är samma som visas i avsnittet föregående **krypterade autentiseringsuppgifter** . Värdet som behövs är resultatet av `$Encryptedpassword` variabeln.  Detta är det lösen ord för tjänstens huvud namn som du krypterade med hjälp av PowerShell-skriptet.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Lagra miljövariablerna

1. Gå till Function-appen. Välj sedan **funktions program inställningar**  >  **Konfigurera appinställningar**.

    ![Konfigurera appinställningar][functions11]

1. Lägg till miljövariablerna och deras värden i appens inställningar och välj sedan **Spara**.

    ![Appinställningar][functions12]

### <a name="add-powershell-to-the-function"></a>Lägg till PowerShell i funktionen

Det är nu dags att göra anrop till Network Watcher inifrån Azure-funktionen. Beroende på kraven kan implementeringen av den här funktionen variera. Det allmänna flödet av koden är dock följande:

1. Kör indataparametrar.
2. Fråga om befintliga paket insamlingar för att kontrol lera gränser och lösa namn konflikter.
3. Skapa en paket avbildning med lämpliga parametrar.
4. Avsöknings paket med jämna mellanrum tills det är klart.
5. Meddela användaren att insamlings sessionen för paketet är slutförd.

Följande exempel är PowerShell-kod som kan användas i-funktionen. Det finns värden som måste ersättas för **subscriptionId**, **resourceGroupName** och **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Hämta funktions webb adress 
1. När du har skapat din funktion konfigurerar du aviseringen så att den anropar den URL som är kopplad till funktionen. Om du vill hämta det här värdet kopierar du funktions-URL: en från Function-appen.

    ![Hitta funktions webb adressen][functions13]

2. Kopiera funktions webb adressen för din Function-app.

    ![Kopiera funktions webb adress][2]

Om du behöver anpassade egenskaper i nytto lasten för webhook POST-begäran, se [Konfigurera en webhook på en Azure Metric-avisering](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurera en avisering på en virtuell dator

Aviseringar kan konfigureras för att meddela individer när ett speciellt mått överskrider ett tröskelvärde som har tilldelats till den. I det här exemplet är aviseringen på de TCP-segment som skickas, men aviseringen kan utlösas för många andra mått. I det här exemplet konfigureras en avisering för att anropa en webhook för att anropa funktionen.

### <a name="create-the-alert-rule"></a>Skapa aviserings regeln

Gå till en befintlig virtuell dator och Lägg sedan till en varnings regel. Mer detaljerad dokumentation om hur du konfigurerar aviseringar finns i [skapa aviseringar i Azure Monitor för Azure-tjänster – Azure Portal](../azure-monitor/alerts/alerts-classic-portal.md). Ange följande värden på bladet **aviserings regel** och välj sedan **OK**.

  |**Inställning** | **Värde** | **Information** |
  |---|---|---|
  |**Namn**|TCP_Segments_Sent_Exceeded|Aviserings regelns namn.|
  |**Beskrivning**|TCP skickade segment överskred tröskeln|Beskrivning av varnings regeln.|
  |**Mått**|TCP skickade segment| Måttet som ska användas för att utlösa aviseringen. |
  |**Condition**|Större än| Villkoret som ska användas när måttet utvärderas.|
  |**Tröskelvärde**|100| Värdet för måttet som utlöser aviseringen. Värdet måste anges till ett giltigt värde för din miljö.|
  |**Period**|Under de senaste fem minuterna| Anger den period i vilken du vill söka efter tröskelvärdet för måttet.|
  |**Webhook**|[webhook-URL från Function app]| Webhook-URL: en från Function-appen som skapades i föregående steg.|

> [!NOTE]
> Måttet TCP-segment är inte aktiverat som standard. Läs mer om hur du aktiverar ytterligare mått genom att besöka [Aktivera övervakning och diagnostik](../azure-monitor/overview.md).

## <a name="review-the-results"></a>Granska resultaten

När kriteriet för aviserings utlösare skapas skapas en paket insamling. Gå till Network Watcher och välj **paket fångst**. På den här sidan kan du välja länken paket insamlings fil för att ladda ned paket fångsten.

![Visa paket fångst][functions14]

Om infångstfilen lagras lokalt kan du hämta den genom att logga in på den virtuella datorn.

Anvisningar om hur du laddar ned filer från Azure Storage-konton finns i [komma igång med Azure Blob Storage med hjälp av .net](../storage/blobs/storage-quickstart-blobs-dotnet.md). Ett annat verktyg som du kan använda är [Storage Explorer](https://storageexplorer.com/).

När din avbildning har hämtats kan du Visa den med hjälp av alla verktyg som kan läsa en **Cap** -fil. Följande är länkar till två av dessa verktyg:

- [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visar dina paket fångster genom att besöka [insamlings analys av paket med wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png