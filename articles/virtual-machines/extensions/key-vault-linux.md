---
title: Azure Key Vault VM-tillägg för Linux
description: Distribuera en agent som utför automatisk uppdatering av Key Vault certifikat på virtuella datorer med ett tillägg för virtuell dator.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 9032bfca30ead56c91d7904e18b76753cf3b6dfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582178"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault tillägg för virtuell dator för Linux

Key Vault VM-tillägget ger automatisk uppdatering av certifikat som lagras i ett Azure Key Vault. Mer specifikt övervakar tillägget en lista över observerade certifikat som lagras i nyckel valv.  Vid identifiering av en ändring, hämtar tillägget och installerar motsvarande certifikat. Key Vault VM-tillägget publiceras och stöds av Microsoft, för närvarande på virtuella Linux-datorer. Det här dokumentet innehåller information om plattformar, konfigurationer och distributions alternativ som stöds för Key Vault VM-tillägget för Linux. 

### <a name="operating-system"></a>Operativsystem

Key Vault VM-tillägget stöder dessa Linux-distributioner:

- Ubuntu – 1804
- SUSE-15 

> [!NOTE]
> För att få utökade säkerhetsfunktioner kan du förbereda uppgraderingen av Ubuntu-1604-och Debian-9-system eftersom dessa versioner når sitt slut för den angivna support perioden.
> 

### <a name="supported-certificate-content-types"></a>Innehålls typer för certifikat som stöds

- PKCS-#12
- PEM


## <a name="prerequisities"></a>Prerequisities
  - Key Vault instans med certifikat. Se [skapa en Key Vault](../../key-vault/general/quick-create-portal.md)
  - VM/VMSS måste ha tilldelats en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md)
  - Åtkomst principen för Key Vault måste anges med hemligheter `get` och `list` behörighet för VM/VMSS-hanterad identitet för att hämta en hemlig del av certifikatet. Se [hur du autentiserar till Key Vault](../../key-vault/general/authentication.md) och [tilldelar en Key Vault åtkomst princip](../../key-vault/general/assign-access-policy-cli.md).
  -  VMSS ska ha följande identitets inställning: ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
 - AKV-tillägget ska ha den här inställningen: `
                 "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `
## <a name="key-vault-vm-extension-version"></a>Version för Key Vault VM-tillägg
* Ubuntu-18,04-och SUSE-15-användare kan välja att uppgradera sin version av nyckel valvets VM-tillägg till en `V2.0` fullständig hämtnings funktion för certifikat kedjan. Utfärdarens certifikat (mellanliggande och rot) läggs till i bladet PEM i filen.

* Om du vill uppgradera till `v2.0` måste du ta bort `v1.0` först och sedan installera `v2.0` .
```
  az vm extension delete --name KeyVaultForLinux --resource-group ${resourceGroup} --vm-name ${vmName}
  az vm extension set -n "KeyVaultForLinux" --publisher Microsoft.Azure.KeyVault --resource-group "${resourceGroup}" --vm-name "${vmName}" –settings .\akvvm.json –version 2.0
```  
  Flaggan--version 2,0 är valfri eftersom den senaste versionen ska installeras som standard.   

* Om den virtuella datorn har certifikat som hämtats med v 1.0, tas inte de hämtade certifikaten bort om du tar bort v 1.0 AKVVM-tillägget.  När du har installerat v 2.0 kommer de befintliga certifikaten inte att ändras.  Du måste ta bort certifikatfiler eller förnya certifikatet för att hämta PEM-filen med fullständig kedja på den virtuella datorn.




## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Key Vault VM-tillägget. Tillägget kräver inte skyddade inställningar. alla dess inställningar betraktas som information utan att säkerheten påverkas. Tillägget kräver en lista över övervakade hemligheter, avsöknings frekvens och mål certifikat arkivet. Specifikt:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> URL: er för dina observerade certifikat bör ha formatet `https://myVaultName.vault.azure.net/secrets/myCertName` .
> 
> Detta beror på att `/secrets` sökvägen returnerar det fullständiga certifikatet, inklusive den privata nyckeln, medan `/certificates` sökvägen inte fungerar. Mer information om certifikat hittar du här: [Key Vault certifikat](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> Egenskapen "authenticationSettings" **krävs** bara för virtuella datorer med **användare tilldelade identiteter**.
> Den anger identitet som ska användas för autentisering till Key Vault.


### <a name="property-values"></a>Egenskaps värden

| Name | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| utgivare | Microsoft.Azure.KeyVault | sträng |
| typ | KeyVaultForLinux | sträng |
| typeHandlerVersion | 2.0 | int |
| pollingIntervalInS | 3600 | sträng |
| Certifikat Arkiv | Den ignoreras i Linux | sträng |
| linkOnRenewal | falskt | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | sträng |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | sträng mat ris
| msiEndpoint | http://169.254.169.254/metadata/identity | sträng |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | sträng |


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver uppdatering av certifikat. Tillägget kan distribueras till enskilda virtuella datorer eller skalnings uppsättningar för virtuella datorer. Schemat och konfigurationen är gemensamma för båda typerna av mallar. 

JSON-konfigurationen för ett tillägg för virtuell dator måste kapslas i den virtuella datorns resurs fragment, särskilt `"resources": []` objekt för mallen för den virtuella datorn och i händelse av skalnings uppsättning för virtuella datorer under `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` objekt.

 > [!NOTE]
> VM-tillägget kräver att system-eller användarens hanterade identitet tilldelas till autentisering till Key Vault.  Se [hur du autentiserar till Key Vault och tilldelar en Key Vault åtkomst princip.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Tillägg för beroende ordning
Key Vault VM-tillägget stöder tilläggs sortering om det är konfigurerat. Som standard rapporterar tillägget att det har startats så snart det har börjat avsökningen. Den kan dock konfigureras att vänta tills den har laddat ned den fullständiga listan över certifikat innan en lyckad start har rapporter ATS. Om andra tillägg är beroende av att den fullständiga uppsättningen certifikat installeras innan de startar, så tillåter de här tilläggen att tillägget deklarerar ett beroende av Key Vault-tillägget. Detta förhindrar att de här tilläggen startar förrän alla certifikat som de är beroende av har installerats. Tillägget gör om den inledande hämtningen på obestämd tid och förblir i ett `Transitioning` tillstånd.

Aktivera detta genom att göra följande:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```
> Lägg Att använda den här funktionen är inte kompatibel med en ARM-mall som skapar en systemtilldelad identitet och uppdaterar en Key Vault åtkomst princip med den identiteten. Detta leder till ett död läge eftersom valv åtkomst principen inte kan uppdateras förrän alla tillägg har startats. I stället bör du använda en *enda användare som tilldelats MSI-identitet* och för hands ACL dina valv med den identiteten innan du distribuerar.

## <a name="azure-powershell-deployment"></a>Azure PowerShell distribution
> [!WARNING]
> PowerShell-klienter lägger ofta `\` till `"` i settings.jspå vilket gör att akvvm_service Miss lyckas med felet: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Azure PowerShell kan användas för att distribuera Key Vault VM-tillägget till en befintlig virtuell dator eller skalnings uppsättning för virtuella datorer. 

* Så här distribuerar du tillägget på en virtuell dator:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "2.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Distribuera tillägget på en skalnings uppsättning för virtuella datorer:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "2.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Key Vault VM-tillägget till en befintlig virtuell dator eller skalnings uppsättning för virtuella datorer. 
 
* Så här distribuerar du tillägget på en virtuell dator:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --version 2.0 `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Distribuera tillägget på en skalnings uppsättning för virtuella datorer:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vmss-name "<vmssName>" `
        --version 2.0 `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
Observera följande begränsningar/krav:
- Key Vault begränsningar:
  - Det måste finnas vid tidpunkten för distributionen 
  - Key Vault åtkomst princip måste anges för VM/VMSS-identitet med hjälp av en hanterad identitet. Se [hur du autentiserar till Key Vault](../../key-vault/general/authentication.md) och [tilldelar en Key Vault åtkomst princip](../../key-vault/general/assign-access-policy-cli.md).

### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* Finns det en gräns för hur många observedCertificates du kan konfigurera?
  Nej, Key Vault VM-tillägget har inte någon gräns för antalet observedCertificates.


### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och genom att använda Azure PowerShell. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
#### <a name="logs-and-configuration"></a>Loggar och konfiguration

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>Använda symlink

Symboliska länkar och symlinks är i princip avancerade genvägar. Om du vill undvika att övervaka mappen och hämta det senaste certifikatet automatiskt kan du använda den här symlink `([VaultName].[CertificateName])` för att hämta den senaste versionen av certifikat på Linux.

### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* Finns det en gräns för hur många observedCertificates du kan konfigurera?
  Nej, Key Vault VM-tillägget har inte någon gräns för antalet observedCertificates.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
