---
title: Skapa ett kluster med hjälp av certifikatets nätverks namn
description: Lär dig hur du skapar ett Service Fabric kluster med hjälp av certifikatets egna namn från en mall.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c852b40d35f936753d3c16420159676da239b6c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86246443"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Distribuera ett Service Fabric kluster som använder certifikatets delade namn i stället för tumavtryck
Inga två certifikat kan ha samma tumavtryck, vilket gör det svårt att förnya kluster certifikat eller hantering. Flera certifikat kan dock ha samma egna namn eller ämne.  Ett kluster som använder Certifikatets gemensamma namn gör certifikat hanteringen mycket enklare. I den här artikeln beskrivs hur du distribuerar ett Service Fabric-kluster för att använda certifikatets egna namn i stället för certifikatets tumavtryck.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Hämta ett certifikat
Börja med att hämta ett certifikat från en [certifikat utfärdare (ca)](https://wikipedia.org/wiki/Certificate_authority).  Certifikatets egna namn bör vara för den anpassade domän som du äger och som köps av en domän registrator. Till exempel "azureservicefabricbestpractices.com"; de som inte är anställda i Microsoft kan inte etablera certifikat för MS-domäner, så du kan inte använda DNS-namnen på dina LB-eller Traffic Manager som vanliga namn för ditt certifikat, och du måste etablera en [Azure DNS zon](../dns/dns-delegate-domain-azure-dns.md) om din anpassade domän ska kunna matchas i Azure. Du ska också deklarera din anpassade domän som du äger som ditt klusters "managementEndpoint" om du vill att portalen ska återspegla det anpassade domän Ali Aset för klustret.

I test syfte kan du få ett CA-signerat certifikat från en kostnads fri eller öppen certifikat utfärdare.

> [!NOTE]
> Självsignerade certifikat, inklusive de som genereras när du distribuerar ett Service Fabric kluster i Azure Portal, stöds inte. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Ladda upp certifikatet till ett nyckel valv
I Azure distribueras ett Service Fabric-kluster på en skal uppsättning för virtuella datorer.  Ladda upp certifikatet till ett nyckel valv.  När klustret distribueras installerar certifikatet på den skalnings uppsättning för virtuella datorer som klustret körs på.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Hämta och uppdatera en exempel mall
I den här artikeln används mallen [5-nods säkra kluster exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) och mallparametrar. Ladda ned *azuredeploy.js* och *azuredeploy.parameters.jspå* filer till datorn.

### <a name="update-parameters-file"></a>Uppdatera parameter fil
Öppna först *azuredeploy.parameters.js* filen i en text redigerare och Lägg till följande parameter värde:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Ange sedan *certificateCommonName*-, *SourceVaultValue*-och *certificateUrlValue* -parametervärdena för de som returneras av föregående skript:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Uppdatera mallfilen
Öppna sedan *azuredeploy.js* filen i en text redigerare och gör tre uppdateringar till att ge stöd för certifikatets egna namn.

1. I avsnittet **parametrar** lägger du till en *certificateCommonName* -parameter:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Överväg också att ta bort *certificateThumbprint*, men det kanske inte längre behövs.

2. Ange värdet för variabeln *sfrpApiVersion* till "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. I **Microsoft. Compute/virtualMachineScaleSets** -resursen uppdaterar du tillägget för den virtuella datorn så att det använder det egna namnet i certifikat inställningarna i stället för tumavtrycket.  I **virtualMachineProfile** -> **extensionProfile** -> **tillägg** -> **Egenskaper** -> **Inställningar** -> **certifikat**, Lägg till 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    och ta bort `"thumbprint": "[parameters('certificateThumbprint')]",` .

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. Uppdatera API-versionen till "2018-02-01" i resursen **Microsoft. ServiceFabric/Clusters** .  Lägg också till en **certificateCommonNames** -inställning med en **commonNames** -egenskap och ta bort **certifikat** inställningen (med egenskapen tumavtryck) som i följande exempel:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > I fältet certificateIssuerThumbprint kan du ange förväntade utfärdare för certifikat med ett angivet ämnes namn. Det här fältet accepterar en kommaavgränsad uppräkning av SHA1-tumavtrycken. Obs! det här är en förstärkning av certifikat valideringen – om utfärdaren inte har angetts eller är tom godkänns certifikatet för autentisering om dess kedja kan skapas och slutar i en rot som är betrodd av verifieraren. Om utfärdaren anges godkänns certifikatet om tumavtrycket för dess direkta utfärdare matchar något av de värden som anges i det här fältet, oavsett om roten är betrodd eller inte. Observera att en PKI kan använda olika certifikat utfärdare för att utfärda certifikat för samma ämne, och därför är det viktigt att ange alla förväntade Issuer-tumavtrycken för ett specifikt ämne.
   >
   > Att ange utfärdaren anses vara bästa praxis. även om du utelämnar det fortsätter det att fungera för certifikat som går samman till en betrodd rot – det här beteendet har begränsningar och kan gå ut i en snar framtid. Observera också att kluster som distribueras i Azure och som skyddas med X509-certifikat som utfärdats av en privat PKI och som deklarerats av ämnet kanske inte kan verifieras av Azure Service Fabric-tjänsten (för kluster-till-tjänst-kommunikation), om PKI: ns certifikat policy inte kan identifieras, är tillgänglig och tillgänglig. 

## <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Distribuera om den uppdaterade mallen när du har gjort ändringarna.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* Lär dig hur du [förnyar ett kluster certifikat](service-fabric-cluster-rollover-cert-cn.md)
* [Uppdatera och hantera kluster certifikat](service-fabric-cluster-security-update-certs-azure.md)
* Förenkla certifikat hanteringen genom [att ändra klustret från certifikatets tumavtryck till eget namn](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
