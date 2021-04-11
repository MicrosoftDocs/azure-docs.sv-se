---
title: Testa en virtuell Azure-dators avbildning för Azure Marketplace
description: Lär dig hur du testar och skickar ett erbjudande för virtuella Azure-datorer på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 467b7d605b57c479d84fc995b4e0dc53b3ac5275
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558303"
---
# <a name="test-a-virtual-machine-image"></a>Testa en avbildning av en virtuell dator

Det här avsnittet beskriver stegen för att testa en virtuell dator avbildning (VM) för distribution på Azure Marketplace.

## <a name="deploy-an-azure-vm"></a>Distribuera en virtuell Azure-dator

Så här distribuerar du en virtuell dator från avbildningen av den delade avbildningen:

1. Navigera till avbildnings versionen av det delade avbildnings galleriet
1. Klicka på Skapa virtuell dator
1. Ange ett namn på en virtuell dator och välj en VM-storlek
1. Klicka på granska + skapa. När verifieringen har slutförts klickar du på skapa

> [!NOTE]
> Om du behöver skapa en virtuell dator från en VHD-fil följer du anvisningarna i följande artiklar, [förbereder en Azure Resource Manager mall](#connect-the-certification-tool-to-a-vm-image) eller [distribuerar en virtuell Azure-dator med hjälp av PowerShell](#how-to-use-powershell-to-consume-the-self-test-api).

Den här artikeln beskriver hur du testar och skickar en virtuell dator avbildning (VM) på den kommersiella Marketplace för att säkerställa att den uppfyller de senaste publicerings kraven för Azure Marketplace.

Slutför de här stegen innan du skickar ditt virtuella dator erbjudande:

- Distribuera en virtuell Azure-dator med hjälp av din generaliserade avbildning; Se [skapa en virtuell dator med en godkänd bas](azure-vm-create-using-approved-base.md) eller [skapa en virtuell dator med hjälp av en egen avbildning](azure-vm-create-using-own-image.md).
- Köra valideringar.

## <a name="run-validations"></a>Kör valideringar

Det finns två sätt att köra valideringar på den distribuerade avbildningen.

### <a name="use-certification-test-tool-for-azure-certified"></a>Använd certifierings test verktyget för Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Hämta och kör verktyget för certifierings test

Certifierings test verktyget för Azure Certified körs på en lokal Windows-dator men testar en Azure-baserad virtuell Windows-eller Linux-dator. Det certifierar att din användar avbildning av virtuella datorer kan användas med Microsoft Azure och att rikt linjerna och kraven kring att förbereda din virtuella hård disk är uppfyllda.

1. Hämta och installera det senaste [certifierings test verktyget för Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Öppna certifierings verktyget och välj sedan **Starta nytt test**.
3. På skärmen testa information anger du ett **testnamn** för test körningen.
4. Välj plattform för din virtuella dator, antingen **Windows Server** eller **Linux**. Valet av plattform påverkar de återstående alternativen.
5. Om den virtuella datorn använder den här databas tjänsten markerar du kryss rutan **test för Azure SQL Database** .

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Ansluta certifierings verktyget till en VM-avbildning

1. Välj SSH-autentiseringsläge: autentisering av lösen ord eller nyckel fil-autentisering.
2. Om du använder lösenordsbaserad autentisering anger du värden för den **virtuella datorns DNS-namn**, **användar namn** och **lösen ord**. Du kan också ändra standard-SSH-portnumret.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Visar valet av test information för virtuella datorer.":::

3. Om du använder nyckelbaserad autentisering anger du värden för den virtuella datorns DNS-namn, användar namn och plats för den privata nyckeln. Du kan också inkludera en lösen fras eller ändra standard-SSH-portnumret.
4. Ange det fullständigt kvalificerade DNS-namnet för virtuell dator (till exempel MyVMName.Cloudapp.net).
5. Ange **användar namn** och **lösen ord**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Visar valet av användar namn och lösen ord för virtuella datorer.":::

6. Välj **Nästa**.

#### <a name="run-a-certification-test"></a>Kör ett certifierings test

När du har fått parameter värden för din VM-avbildning i certifierings verktyget väljer du testa anslutning för att skapa en giltig anslutning till din virtuella dator. När en anslutning har verifierats väljer du **Nästa** för att starta testet. När testet är klart visas resultatet i en tabell. I kolumnen Status visas (pass/misslyckande/varning) för varje test. Om något av testerna inte fungerar är din avbildning inte godkänd. I det här fallet granskar du kraven och felen, gör de föreslagna ändringarna och kör testet igen.

När det automatiserade testet har slutförts, anger du ytterligare information om din VM-avbildning på de två flikarna i enkät skärmen, allmän utvärdering och automatisk distribution av kernel. Välj sedan Nästa.

På den sista skärmen kan du ange mer information, till exempel SSH Access-information för en virtuell Linux-avbildning och en förklaring till eventuella misslyckade utvärderingar om du söker efter undantag.

Välj slutligen skapa rapport för att ladda ned test resultaten och loggfilerna för de test ärenden som körts, tillsammans med dina svar till enkäten.
> [!Note]
> Några utgivare har scenarier där virtuella datorer måste låsas eftersom de har program vara, till exempel brand väggar som är installerade på den virtuella datorn. I det här fallet laddar du ned det [certifierade testverktyget](https://aka.ms/AzureCertificationTestTool) här och skickar rapporten till [supporten](https://aka.ms/marketplacepublishersupport)för partner Center.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Hur du använder PowerShell för att använda Self-Test API

### <a name="on-linux-os"></a>På Linux OS

Anropa API: t i PowerShell:

1. Använd kommandot Invoke-WebRequest för att anropa API: et.
2. Metoden är post och Content-Type är JSON, som du ser i följande kod exempel och skärmdump.
3. Ange Body-parametrarna i JSON-format.

I följande exempel visas ett PowerShell-anrop till API: et:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Här är ett exempel på hur du anropar API: et i PowerShell:

[![Skärm exempel för att anropa API: et i PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>I föregående exempel kan du hämta JSON och parsa den för att få följande information:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Den här exempel skärmen, som visar `$res.Content` , visar information om dina test resultat i JSON-format:

[![Skärm exempel för att anropa API i PowerShell med information om test resultat.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Här är ett exempel på JSON-test resultat som visas i en online-JSON-visningsprogrammet (till exempel [Code beautify](https://codebeautify.org/jsonviewer) eller [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Fler test resultat i ett JSON-visningsprogram online.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>På Windows OS

Anropa API: t i PowerShell:

1. Använd kommandot Invoke-WebRequest för att anropa API: et.
2. Metoden är post och Content-Type är JSON, som du ser i följande kod exempel och exempel skärm.
3. Skapa Body-parametrarna i JSON-format.

I det här kod exemplet visas ett PowerShell-anrop till API: et:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Följande exempel skärmar visar exempel för att anropa API: et i PowerShell:

**Med SSH-nyckel**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Anropar API: et i PowerShell med en SSH-nyckel.":::

**Med lösen ord**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Anropar API: et i PowerShell med ett lösen ord.":::

<br>I föregående exempel kan du hämta JSON och parsa den för att få följande information:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Den här skärmen, som visar `$res.Content` , visar information om dina test resultat i JSON-format:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Information om test resultat i JSON-format.":::

<br>Här är ett exempel på test resultat som visas i en online-JSON-visningsprogrammet (till exempel [Code beautify](https://codebeautify.org/jsonviewer) eller [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Test resultat i ett online-JSON-visningsprogram.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Så här använder du en sväng för att använda Self-Test API i Linux OS

I det här exemplet kommer svängen att användas för att göra ett POST-API-anrop till Azure Active Directory och den virtuella Self-Host-datorn.

1. Begär en Azure AD-token för att autentisera till egen värd-VM

   Se till att de korrekta värdena har ersatts i förfrågan om bläddring.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Här är ett exempel på svaret från begäran:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Skicka en begäran för den virtuella datorn med självtest

   Se till att Bearer-token och parametrarna ersätts med rätt värden.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Exempel svar från det virtuella dator-API-anropet för test

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Nästa steg

- Logga in på [partner Center](https://partner.microsoft.com/).
