---
title: Metodtips för Azure Service Fabric-säkerhet
description: Bästa praxis och design överväganden för att hålla Azure Service Fabric-kluster och-program säkra.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b7af0a4c26a47644973e936eb37e221853d74c03
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784671"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric-säkerhet 

Mer information om [metod tips för Azure-säkerhet](../security/index.yml)finns i [metod tips för Azure Service Fabric Security](../security/fundamentals/service-fabric-best-practices.md)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](../key-vault/index.yml) är den rekommenderade tjänsten för hemligheter-hantering för Azure Service Fabric-program och-kluster.
> [!NOTE]
> Om certifikat/hemligheter från en Key Vault distribueras till en skalnings uppsättning för virtuella datorer som en virtuell dators skal uppsättnings hemlighet, måste Key Vault och den virtuella datorns skalnings uppsättning vara samordnad.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Skapa certifikat utfärdare som utfärdats Service Fabric certifikat

Ett Azure Key Vault certifikat kan antingen skapas eller importeras till en Key Vault. När ett Key Vault certifikat skapas, skapas den privata nyckeln inuti Key Vault och exponeras aldrig för certifikat ägaren. Här är några sätt att skapa ett certifikat i Key Vault:

- Skapa ett självsignerat certifikat för att skapa ett offentligt privat nyckel par och associera det med ett certifikat. Certifikatet kommer att signeras av en egen nyckel. 
- Skapa ett nytt certifikat manuellt om du vill skapa ett offentligt privat privat nyckel par och generera en X. 509-certifikat signerings förfrågan. Signerings förfrågan kan signeras av din registrerings utfärdare eller certifikat utfärdare. Det signerade x509-certifikatet kan slås samman med det väntande nyckel paret för att slutföra KV-certifikatet i Key Vault. Även om den här metoden kräver fler steg ger den bättre säkerhet eftersom den privata nyckeln skapas i och är begränsad till Key Vault. Detta beskrivs i diagrammet nedan. 

Läs mer om hur du [skapar metoder för att skapa certifikat för Azure-certifikats valv](../key-vault/certificates/create-certificate.md) .

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Distribuera Key Vault certifikat till Service Fabric skalnings uppsättningar för virtuella kluster datorer

Om du vill distribuera certifikat från ett Samplacerat nyckel valv till en skalnings uppsättning för virtuella datorer använder du [osProfile](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)för skalnings uppsättning för virtuella datorer. Här följer egenskaper för Resource Manager-mall:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Valvet måste vara aktiverat för distribution av Resource Manager-mallar.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Använda en Access Control lista (ACL) för ditt certifikat för ditt Service Fabric-kluster

[Tillägg för skalnings uppsättning för virtuella datorer](/cli/azure/vmss/extension) Publisher Microsoft. Azure. ServiceFabric används för att konfigurera nodernas säkerhet.
Använd följande egenskaper för Resource Manager-mall för att tillämpa en ACL för dina certifikat för dina Service Fabric kluster processer:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Skydda ett Service Fabric kluster certifikat per eget namn

Om du vill skydda ditt Service Fabric kluster med certifikat `Common Name` använder du Resource Manager- [certificateCommonNames](/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)på följande sätt:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service Fabric kluster kommer att använda det första giltiga certifikatet som hittas i värdens certifikat arkiv. I Windows kommer det här certifikatet att vara det senaste förfallo datumet som matchar ditt eget namn och utfärdare tumavtryck.

Azure-domäner, till exempel * \<YOUR SUBDOMAIN\> . cloudapp.Azure.com eller \<YOUR SUBDOMAIN\> . trafficmanager.net, ägs av Microsoft. Certifikat utfärdare kommer inte att utfärda certifikat för domäner till obehöriga användare. De flesta användare behöver köpa en domän från en registrator eller vara behörig domän administratör för en certifikat utfärdare för att utfärda ett certifikat med samma namn.

Mer information om hur du konfigurerar DNS-tjänsten för att matcha din domän med en Microsoft-IP-adress finns i så här konfigurerar du [Azure DNS som värd för din domän](../dns/dns-delegate-domain-azure-dns.md).

> [!NOTE]
> När du har delegerat domän namn servrarna till Azure DNS Zone namnservrar, lägger du till följande två poster i din DNS-zon:
> - En "A"-post för domän APEX som inte är en `Alias record set` till alla IP-adresser som din anpassade domän kommer att matcha.
> - En C-post för Microsoft-underordnade domäner som du har angett som inte är en `Alias record set` . Du kan till exempel använda din Traffic Manager eller Load Balancer DNS-namn.

Uppdatera portalen för att visa ett anpassat DNS-namn för Service Fabric klustret `"managementEndpoint"` genom att uppdatera följande mall egenskaper för Service Fabric Cluster Resource Manager:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Kryptera Service Fabric paketets hemliga värden

Vanliga värden som är krypterade i Service Fabric-paket är Azure Container Registry (ACR) autentiseringsuppgifter, miljövariabler, inställningar och lagrings konto nycklar för Azure Volume plugin.

Så [här konfigurerar du ett krypterings certifikat och krypterar hemligheter på Windows-kluster](./service-fabric-application-secret-management-windows.md):

Generera ett självsignerat certifikat för kryptering av din hemlighet:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Använd anvisningarna i [distribuera Key Vault certifikat för att Service Fabric skalnings uppsättningar för virtuella datorer](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) för att distribuera Key Vault-certifikat till Service Fabric klustrets Virtual Machine Scale Sets.

Kryptera din hemlighet med följande PowerShell-kommando och uppdatera sedan ditt Service Fabric program manifest med det krypterade värdet:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Så [här konfigurerar du ett krypterings certifikat och krypterar hemligheter på Linux-kluster](./service-fabric-application-secret-management-linux.md):

Generera ett självsignerat certifikat för kryptering av dina hemligheter:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Använd anvisningarna i [distribuera Key Vault certifikat för att Service Fabric skalnings uppsättningar för virtuella datorer i klustret](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) till Service Fabric klustrets Virtual Machine Scale Sets.

Kryptera din hemlighet med följande kommandon och uppdatera sedan Service Fabric program manifestet med det krypterade värdet:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

När du har krypterat dina skyddade värden [anger du krypterade hemligheter i Service Fabric program](./service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application)och dekrypterar [krypterade hemligheter från Service koden](./service-fabric-application-secret-management.md#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Inkludera certifikat i Service Fabric program

För att ge programmet åtkomst till hemligheter, inkludera certifikatet genom att lägga till ett **SecretsCertificate** -element i applikations manifestet.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Autentisera Service Fabric-program till Azure-resurser med hjälp av Hanterad tjänstidentitet (MSI)

Information om hanterade identiteter för Azure-resurser finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).
Azure Service Fabric-kluster finns på Virtual Machine Scale Sets, som har stöd för [hanterad tjänstidentitet](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
Om du vill hämta en lista över tjänster som MSI kan användas för att autentisera till, se [Azure-tjänster som stöder Azure Active Directory autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).


Om du vill aktivera systemtilldelad hanterad identitet under skapandet av en skalnings uppsättning för virtuella datorer eller en befintlig skalnings uppsättning för virtuella datorer, måste du deklarera följande `"Microsoft.Compute/virtualMachinesScaleSets"` egenskap:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Se [Vad är Managed identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss.md#system-assigned-managed-identity) för mer information.

Om du har skapat en  [användardefinierad hanterad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity), måste du deklarera följande resurs i mallen för att tilldela den till den virtuella datorns skalnings uppsättning. Ersätt `\<USERASSIGNEDIDENTITYNAME\>` med namnet på den användare-tilldelade hanterade identitet som du skapade:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Innan ditt Service Fabric program kan använda en hanterad identitet måste behörigheter beviljas till de Azure-resurser som den måste autentisera med.
Följande kommandon ger åtkomst till en Azure-resurs:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

I din Service Fabric program kod [hämtar du en](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) åtkomsttoken för Azure Resource Manager genom att göra en rest som liknar följande:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Din Service Fabric-app kan sedan använda åtkomsttoken för att autentisera till Azure-resurser som stöder Active Directory.
I följande exempel visas hur du gör detta för Cosmos DB resurs:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows säkerhets bas linjer
[Vi rekommenderar att du implementerar en bransch standard konfiguration som är allmänt känd och väl testad, till exempel Microsofts säkerhets bas linjer, i stället för att skapa en bas linje själv](/windows/security/threat-protection/windows-security-baselines). ett alternativ för att konfigurera dessa på din Virtual Machine Scale Sets är att använda Azures tilläggs hanterare för önskad tillstånds konfiguration (DSC) för att konfigurera de virtuella datorerna när de är online, så att de kör produktions program varan.

## <a name="azure-firewall"></a>Azure Firewall
[Azure-brandväggen är en hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillstånds känslig brand vägg som en tjänst med inbyggd hög tillgänglighet och obegränsad moln skalbarhet.](../firewall/overview.md) Detta gör det möjligt att begränsa utgående HTTP/S-trafik till en angiven lista med fullständigt kvalificerade domän namn (FQDN), inklusive jokertecken. Den här funktionen kräver inte TLS/SSL-avslutning. Vi rekommenderar att du använder [Azure FIREWALL FQDN-Taggar](../firewall/fqdn-tags.md) för Windows-uppdateringar och aktiverar nätverks trafik till Microsoft Windows Update-slutpunkter kan flöda genom brand väggen. [Genom att distribuera Azure-brandväggen med en mall](../firewall/deploy-template.md) får du ett exempel på definitionen av resurs mal len för Microsoft. Network/azureFirewalls. Brand Väggs regler som är vanliga för att Service Fabric program är att tillåta följande för klustrets virtuella nätverk:

- * download.microsoft.com
- * servicefabric.azure.com
- *.core.windows.net

Dessa brand Väggs regler kompletterar tillåtna utgående nätverks säkerhets grupper, som innehåller ServiceFabric och lagring, som tillåtna destinationer från det virtuella nätverket.

## <a name="tls-12"></a>TLS 1.2

Microsoft [Azure rekommenderar](https://azure.microsoft.com/updates/azuretls12/) att alla kunder Slutför migreringen till lösningar som stöder TLS (Transport Layer security) 1,2 och för att se till att TLS 1,2 används som standard.

Azure-tjänster, inklusive [Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/microsoft-azure-service-fabric-6-3-refresh-release-cu1-notes/ba-p/791493), har slutfört teknik arbetet för att ta bort beroendet av TLS 1.0/1.1-protokoll och ge fullständig support till kunder som vill ha sina arbets belastningar konfigurerade att godkänna och endast initiera TLS 1,2-anslutningar.

Kunderna bör konfigurera sina Azure-värdbaserade arbets belastningar och lokala program som interagerar med Azure-tjänster för att använda TLS 1,2 som standard. Så här konfigurerar du [Service Fabric klusternoder och program](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md) för att använda en speciell TLS-version.

## <a name="windows-defender"></a>Windows Defender 

Som standard installeras Windows Defender Antivirus på Windows Server 2016. Mer information finns i [Windows Defender Antivirus på Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Användargränssnittet installeras som standard på vissa SKU:er, men det krävs inte. För att minska prestanda påverkan och resurs förbruknings omkostnader som skapas av Windows Defender, och om dina säkerhets principer tillåter att du undantar processer och sökvägar för program med öppen källkod, måste du deklarera följande mall egenskaper för den virtuella datorns skal uppsättnings tillägg för att utesluta ditt Service Fabric-kluster från genomsökningar:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Se dokumentationen för program mot skadlig kod för konfigurations regler om du inte använder Windows Defender. Windows Defender stöds inte i Linux.

## <a name="platform-isolation"></a>Plattforms isolering
Som standard beviljas Service Fabric-program åtkomst till själva Service Fabric körnings miljön, som i sin tur är i olika former: [miljövariabler](service-fabric-environment-variables-reference.md) som pekar på fil Sök vägar på värden som motsvarar program-och Fabric-filer, en kommunikation mellan processens slut punkt som godkänner programspecifika begär Anden och klient certifikatet som infrastruktur resursen förväntar sig att programmet ska använda för att autentisera sig. I den här tjänsten är det lämpligt att inaktivera den här åtkomsten till SF-körningen, om det inte uttryckligen krävs. Åtkomst till körnings miljön tas bort med hjälp av följande deklaration i avsnittet principer i applikations manifestet: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md).
* Läs mer om [Service Fabric support alternativ](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
