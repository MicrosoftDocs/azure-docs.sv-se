---
title: Skydda ett kluster i Windows med hjälp av certifikat
description: Säker kommunikation i ett fristående eller lokalt kluster i Azure Service Fabric, samt mellan klienter och klustret.
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: d75c644be47ea44f6a8a6ccac91b785af0132833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791045"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Skydda ett fristående kluster i Windows med hjälp av X. 509-certifikat
Den här artikeln beskriver hur du skyddar kommunikationen mellan de olika noderna i det fristående Windows-klustret. Det beskriver också hur du autentiserar klienter som ansluter till det här klustret med hjälp av X. 509-certifikat. Autentisering garanterar att endast behöriga användare kan komma åt klustret och distribuerade program och utföra hanterings uppgifter. Certifikat säkerhet ska vara aktiverat på klustret när klustret skapas.  

Mer information om kluster säkerhet, till exempel nod-till-nod-säkerhet, klient-till-nod-säkerhet och rollbaserad åtkomst kontroll finns i [kluster säkerhets scenarier](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Vilka certifikat behöver du?
Börja med genom att [Ladda ned Service Fabric för Windows Server-paketet](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) till en av noderna i klustret. I det hämtade paketet hittar du en ClusterConfig.X509.MultiMachine.jspå filen. Öppna filen och granska säkerhets avsnittet i avsnittet Egenskaper:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

I det här avsnittet beskrivs de certifikat som du behöver för att skydda ditt fristående Windows-kluster. Ange värdet för ClusterCredentialType till _X509_ om du anger ett kluster certifikat. Om du anger ett Server certifikat för externa anslutningar anger du ServerCredentialType till _X509_. Även om det inte är obligatoriskt rekommenderar vi att du har båda dessa certifikat för ett korrekt skyddat kluster. Om du anger dessa värden till *X509* måste du också ange motsvarande certifikat eller Service Fabric genererar ett undantag. I vissa fall kanske du bara vill ange _ClientCertificateThumbprints_ eller _ReverseProxyCertificate_. I dessa scenarier behöver du inte ange _ClusterCredentialType_ eller _ServerCredentialType_ till _X509_.


> [!NOTE]
> Ett [tumavtryck](https://en.wikipedia.org/wiki/Public_key_fingerprint) är den primära identiteten för ett certifikat. Information om hur du tar reda på tumavtrycket för de certifikat som du skapar finns i [Hämta ett tumavtryck för ett certifikat](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
> 
> 

I följande tabell visas de certifikat som du behöver i kluster konfigurationen:

| **CertificateInformation-inställning** | **Beskrivning** |
| --- | --- |
| ClusterCertificate |Rekommenderas för en test miljö. Detta certifikat krävs för att skydda kommunikationen mellan noderna i ett kluster. Du kan använda två olika certifikat, en primär och en sekundär, för uppgradering. Ange tumavtrycket för det primära certifikatet i avsnittet tumavtryck och det sekundära i ThumbprintSecondary-variablerna. |
| ClusterCertificateCommonNames |Rekommenderas för en produktions miljö. Detta certifikat krävs för att skydda kommunikationen mellan noderna i ett kluster. Du kan använda ett eller två kluster certifikats gemensamma namn. CertificateIssuerThumbprint motsvarar tumavtrycket för utfärdaren av det här certifikatet. Om mer än ett certifikat med samma egna namn används kan du ange flera utfärdare tumavtrycken.|
| ClusterCertificateIssuerStores |Rekommenderas för en produktions miljö. Det här certifikatet motsvarar utfärdaren av kluster certifikatet. Du kan ange utfärdarens nätverks namn och motsvarande Arkiv namn under det här avsnittet i stället för att ange utfärdarens tumavtryck under ClusterCertificateCommonNames.  Detta gör det enkelt att förnya kluster utfärdarens certifikat. Flera utfärdare kan anges om fler än ett kluster certifikat används. En tom IssuerCommonName tillåter alla certifikat i motsvarande butiker som anges under X509StoreNames.|
| ServerCertificate |Rekommenderas för en test miljö. Det här certifikatet presenteras för klienten när det försöker ansluta till det här klustret. För enkelhetens skull kan du välja att använda samma certifikat för ClusterCertificate och ServerCertificate. Du kan använda två olika Server certifikat, en primär och en sekundär, för uppgradering. Ange tumavtrycket för det primära certifikatet i avsnittet tumavtryck och det sekundära i ThumbprintSecondary-variablerna. |
| ServerCertificateCommonNames |Rekommenderas för en produktions miljö. Det här certifikatet presenteras för klienten när det försöker ansluta till det här klustret. CertificateIssuerThumbprint motsvarar tumavtrycket för utfärdaren av det här certifikatet. Om mer än ett certifikat med samma egna namn används kan du ange flera utfärdare tumavtrycken. För enkelhetens skull kan du välja att använda samma certifikat för ClusterCertificateCommonNames och ServerCertificateCommonNames. Du kan använda ett eller två vanliga Server certifikat namn. |
| ServerCertificateIssuerStores |Rekommenderas för en produktions miljö. Det här certifikatet motsvarar utfärdaren av Server certifikatet. Du kan ange utfärdarens nätverks namn och motsvarande Arkiv namn under det här avsnittet i stället för att ange utfärdarens tumavtryck under ServerCertificateCommonNames.  Detta gör det enkelt att förnya Server utfärdarens certifikat. Flera utfärdare kan anges om fler än ett Server certifikat används. En tom IssuerCommonName tillåter alla certifikat i motsvarande butiker som anges under X509StoreNames.|
| ClientCertificateThumbprints |Installera den här uppsättningen certifikat på de autentiserade klienterna. Du kan ha ett antal olika klient certifikat installerade på de datorer som du vill tillåta åtkomst till klustret. Ange tumavtrycket för varje certifikat i den CertificateThumbprint-variabeln. Om du anger IsAdmin till *True*, kan klienten med det här certifikatet som är installerad på den utföra administratörs hanterings aktiviteter i klustret. Om IsAdmin är *false* kan klienten med det här certifikatet endast utföra åtgärderna för användar åtkomst behörighet, vanligt vis skrivskyddad. Mer information om roller finns i [Service Fabric rollbaserad åtkomst kontroll](service-fabric-cluster-security.md#service-fabric-role-based-access-control). |
| ClientCertificateCommonNames |Ange ett eget namn för det första klient certifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för utfärdaren av det här certifikatet. Mer information om vanliga namn och utfärdaren finns i [arbeta med certifikat](/dotnet/framework/wcf/feature-details/working-with-certificates). |
| ClientCertificateIssuerStores |Rekommenderas för en produktions miljö. Det här certifikatet motsvarar utfärdaren av klient certifikatet (både administratörs-och icke-admin-roller). Du kan ange utfärdarens nätverks namn och motsvarande Arkiv namn under det här avsnittet i stället för att ange utfärdarens tumavtryck under ClientCertificateCommonNames.  Detta gör det enkelt att förnya klient utfärdarens certifikat. Flera utfärdare kan anges om fler än ett klient certifikat används. En tom IssuerCommonName tillåter alla certifikat i motsvarande butiker som anges under X509StoreNames.|
| ReverseProxyCertificate |Rekommenderas för en test miljö. Det här valfria certifikatet kan anges om du vill skydda den [omvända proxyn](service-fabric-reverseproxy.md). Kontrol lera att reverseProxyEndpointPort har angetts i nodeTypes om du använder det här certifikatet. |
| ReverseProxyCertificateCommonNames |Rekommenderas för en produktions miljö. Det här valfria certifikatet kan anges om du vill skydda den [omvända proxyn](service-fabric-reverseproxy.md). Kontrol lera att reverseProxyEndpointPort har angetts i nodeTypes om du använder det här certifikatet. |

Här är ett exempel på en kluster konfiguration där klustret, servern och klient certifikat har angetts. För kluster/Server/reverseProxy certifikat kan inte tumavtrycket och det egna namnet konfigureras tillsammans för samma certifikat typ.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Certifikat förnyelse
När du använder ett certifikats eget namn i stället för ett tumavtryck kräver inte certifikat förnyelse en uppgradering av kluster konfigurationen. Se till att den nya listan över tumavtryck är överlappande med den gamla listan för utfärdares tumavtryck för uppgraderingar. Du måste först göra en konfigurations uppgradering med den nya utfärdarens tumavtrycken och sedan installera de nya certifikaten (både kluster-/Server certifikat och utfärdarens certifikat) i arkivet. Behåll det gamla utfärdarcertifikatet i certifikat arkivet i minst två timmar efter att du har installerat det nya utfärdarens certifikat.
Om du använder utfärdare av utfärdare måste ingen konfigurations uppgradering utföras för förnyelse av certifikat utfärdare. Installera det nya utfärdarens certifikat med ett senare förfallo datum i motsvarande certifikat Arkiv och ta bort det gamla utfärdarcertifikatet efter några timmar.

## <a name="acquire-the-x509-certificates"></a>Hämta X. 509-certifikaten
För att skydda kommunikationen i klustret måste du först skaffa X. 509-certifikat för klusternoderna. För att begränsa anslutning till det här klustret till auktoriserade datorer/användare måste du dessutom hämta och installera certifikat för klient datorerna.

För kluster som kör produktions arbets belastningar använder du ett [certifikat för certifikat utfärdare (ca)](https://en.wikipedia.org/wiki/Certificate_authority)-signerat X. 509-certifikat för att skydda klustret. Mer information om hur du hämtar dessa certifikat finns i [så här hämtar du ett certifikat](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf). 

Det finns ett antal egenskaper som certifikatet måste ha för att fungera korrekt:

* Certifikatets Provider måste vara **Microsoft Enhanced RSA och AES Cryptographic Provider**

* När du skapar en RSA-nyckel kontrollerar du att nyckeln är **2048 bitar**.

* Tillägget för nyckel användning har värdet **digital signatur, nyckel chiffrering (a0)**

* Det utökade nyckel användnings tillägget har värden för **serverautentisering** (OID: 1.3.6.1.5.5.7.3.1) och **klientautentisering** (OID: 1.3.6.1.5.5.7.3.2)

För kluster som du använder i test syfte kan du välja att använda ett självsignerat certifikat.

Mer information finns i [vanliga frågor och svar om certifikat](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions).

## <a name="optional-create-a-self-signed-certificate"></a>Valfritt: skapa ett självsignerat certifikat
Ett sätt att skapa ett självsignerat certifikat som kan skyddas korrekt är att använda CertSetup.ps1-skriptet i mappen Service Fabric SDK i katalogen C:\Program\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Redigera den här filen om du vill ändra standard namnet för certifikatet. (Leta efter värdet CN = ServiceFabricDevClusterCert.) Kör det här skriptet som `.\CertSetup.ps1 -Install` .

Exportera certifikatet till en PFX-fil med ett skyddat lösen ord. Hämta först tumavtrycket för certifikatet. 
1. Från **Start** -menyn, kör **Hantera dator certifikat**. 

2. Gå till den **lokala personliga** -mappen och leta upp det certifikat som du skapade. 

3. Dubbelklicka på certifikatet för att öppna det, Välj fliken **information** och rulla ned till fältet **tumavtryck** . 

4. Ta bort blank stegen och kopiera tumavtrycket till följande PowerShell-kommando. 

5. Ändra `String` värdet till ett lämpligt säkert lösen ord för att skydda det och kör följande i PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Om du vill se information om ett certifikat som är installerat på datorn kör du följande PowerShell-kommando:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Alternativt, om du har en Azure-prenumeration, följer du stegen i [skapa ett Service Fabric kluster med Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Installera certifikaten
När du har certifikat kan du installera dem på klusternoderna. Dina noder måste ha det senaste Windows PowerShell 3. x installerat på dem. Upprepa de här stegen på varje nod för både kluster-och Server certifikat och eventuella sekundära certifikat.

1. Kopiera. pfx-filen eller filerna till noden.

2. Öppna ett PowerShell-fönster som administratör och ange följande kommandon. Ersätt *$PSWD* med det lösen ord som du använde för att skapa det här certifikatet. Ersätt *$PfxFilePath* med den fullständiga sökvägen till. pfx som kopieras till den här noden.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Ange nu åtkomst kontroll för det här certifikatet så att Service Fabric processen, som körs under nätverks tjänst kontot, kan använda det genom att köra följande skript. Ange tumavtrycket för certifikatet och **nätverks tjänsten** för tjänst kontot. Du kan kontrol lera att åtkomst kontrol listorna på certifikatet är korrekta genom att öppna certifikatet i **Starta**  >  **Hantera dator certifikat** och titta på **alla aktiviteter**  >  **hantera privata nycklar**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Upprepa föregående steg för varje server certifikat. Du kan också använda de här stegen för att installera klient certifikat på de datorer som du vill tillåta åtkomst till klustret.

## <a name="create-the-secure-cluster"></a>Skapa ett säkert kluster
När du har konfigurerat säkerhets avsnittet i ClusterConfig.X509.MultiMachine.jspå filen kan du gå vidare till avsnittet [skapa kluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) för att konfigurera noderna och skapa det fristående klustret. Kom ihåg att använda ClusterConfig.X509.MultiMachine.jspå filen när du skapar klustret. Kommandot kan till exempel se ut så här:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

När du har konfigurerat det fristående fristående Windows-klustret och har konfigurerat de autentiserade klienterna för att ansluta till den, följer du stegen i avsnittet [Anslut till ett kluster med hjälp av PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) för att ansluta till den. Exempel:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Du kan sedan köra andra PowerShell-kommandon för att arbeta med det här klustret. Du kan till exempel köra [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) för att visa en lista över noder i det här säkra klustret.


Om du vill ta bort klustret ansluter du till noden i klustret där du laddade ned Service Fabric-paketet, öppnar en kommando rad och går till mappen Package. Kör nu följande kommando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Felaktig certifikat konfiguration kan förhindra att klustret kommer igång under distributionen. Om du vill själv diagnostisera säkerhets problem kan du titta i Loggboken Group- **program och tjänst loggar**  >  **Microsoft-Service Fabric**.
> 
> 
