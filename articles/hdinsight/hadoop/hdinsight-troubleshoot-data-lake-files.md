---
title: Det gick inte att komma åt Data Lake Storage-filer i Azure HDInsight
description: Det gick inte att komma åt Data Lake Storage-filer i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.openlocfilehash: f4c5a23b604334952730fcc4cf1fcb3fcbed6237
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944389"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Det gick inte att komma åt Data Lake Storage-filer i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue-acl-verification-failed"></a>Problem: ACL-verifieringen misslyckades

Du får ett fel meddelande som liknar:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Orsak

Användaren kan ha återkallat behörigheter för tjänstens huvud namn (SP) på filer/mappar.

### <a name="resolution"></a>Lösning

1. Kontrol lera att alternativet SP har x-behörighet för att gå längs sökvägen. Mer information finns i [behörigheter](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). Exempel `dfs` kommando för att kontrol lera åtkomsten till filer/mappar i data Lake lagrings konto:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Ställ in de behörigheter som krävs för att få åtkomst till sökvägen baserat på den Läs-/skriv åtgärd som utförs. Se här för behörigheter som krävs för olika fil Systems åtgärder.

---

## <a name="issue-service-principal-certificate-expiry"></a>Problem: förfallo datum för tjänstens huvud namn

Du får ett fel meddelande som liknar:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Orsak

Certifikatet som angetts för tjänstens huvud namn kan ha upphört att gälla.

1. SSH till huvudnoden. Kontrol lera åtkomst till lagrings kontot med hjälp av följande `dfs` kommando:

    ```
    hdfs dfs -ls /
    ```

1. Bekräfta att fel meddelandet liknar följande utdata:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Hämta en av webb adresserna från `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls` .

1. Kör följande spiral-kommando för att hämta OAuth-token.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. Utdata för ett giltigt huvud namn för tjänsten ska vara något som liknar:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Om tjänstens huvud certifikat har upphört att gälla ser utdata ut ungefär så här:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Eventuella andra Azure Active Directory relaterade fel eller problem med certifikat kan identifieras genom att pinga Gateway-URL: en för att hämta OAuth-token.

1. Om du får följande fel meddelande när du försöker få åtkomst till ADLS från HDI-klustret. Kontrol lera att certifikatet har gått ut genom att följa stegen ovan.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Lösning

Skapa ett nytt certifikat eller tilldela ett befintligt certifikat med hjälp av följande PowerShell-skript:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Om du vill tilldela ett befintligt certifikat, skapar du ett certifikat, har. pfx-filen och lösen ordet klart. Koppla certifikatet till tjänstens huvud namn som klustret skapades med med hjälp av AppId Ready.

Kör PowerShell-kommandot när du har ersatt parametrarna med de faktiska värdena.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]