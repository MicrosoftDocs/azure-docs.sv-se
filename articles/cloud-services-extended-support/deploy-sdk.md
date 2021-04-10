---
title: Distribuera Cloud Services (utökad support) – SDK
description: Distribuera Cloud Services (utökad support) med Azure SDK
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d36bae57a9e1609e053326cf7288b5b1bc470cef
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166895"
---
# <a name="deploy-cloud-services-extended-support-by-using-the-azure-sdk"></a>Distribuera Cloud Services (utökad support) med Azure SDK

Den här artikeln visar hur du använder [Azure SDK](https://azure.microsoft.com/downloads/) för att distribuera en Cloud Services-instans (utökad support) som har flera roller (webb roll och arbets roll) och tillägget fjärr skrivbord. Cloud Services (utökad support) är en distributions modell av Azure-Cloud Services som baseras på Azure Resource Manager.

> [!IMPORTANT]
> Cloud Services (utökad support) är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar den inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Innan du börjar

Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support) och skapa associerade resurser.

## <a name="deploy-cloud-services-extended-support"></a>Distribuera Cloud Services (utökad support)
1. Installera [Azure Compute SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute/43.0.0-preview) och initiera klienten med hjälp av en standardmekanism för autentisering.

    ```csharp
        public class CustomLoginCredentials : ServiceClientCredentials
    {
        private string AuthenticationToken { get; set; }
        public override void InitializeServiceClient<T>(ServiceClient<T> client)
           {
               var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantID}");
               var credential = new ClientCredential(clientId: "{clientID}", clientSecret: "{clientSecret}");
               var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
               if (result == null) throw new InvalidOperationException("Failed to obtain the JWT token");
               AuthenticationToken = result.Result.AccessToken;
           }
        public override async Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
           {
                if (request == null) throw new ArgumentNullException("request");
                if (AuthenticationToken == null) throw new InvalidOperationException("Token Provider Cannot Be Null");
                request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", AuthenticationToken);
                request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                //request.Version = new Version(apiVersion);
                await base.ProcessHttpRequestAsync(request, cancellationToken);
            }
    
        var creds = new CustomLoginCredentials();
        m_subId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
        ResourceManagementClient m_ResourcesClient = new ResourceManagementClient(creds);
        NetworkManagementClient m_NrpClient = new NetworkManagementClient(creds);
        ComputeManagementClient m_CrpClient = new ComputeManagementClient(creds);
        StorageManagementClient m_SrpClient = new StorageManagementClient(creds);
        m_ResourcesClient.SubscriptionId = m_subId;
        m_NrpClient.SubscriptionId = m_subId;
        m_CrpClient.SubscriptionId = m_subId;
        m_SrpClient.SubscriptionId = m_subId;
    ```

2. Skapa en ny resurs grupp genom att installera Azure Resource Manager NuGet-paketet.

    ```csharp 
    var resourceGroups = m_ResourcesClient.ResourceGroups;
    var m_location = “East US”;
    var resourceGroupName = "ContosoRG";//provide existing resource group name, if created already
    var resourceGroup = new ResourceGroup(m_location); 
    resourceGroup = await resourceGroups.CreateOrUpdateAsync(resourceGroupName, resourceGroup);
    ```

3. Skapa ett lagrings konto och en behållare där du lagrar Service Pack-filerna (. cspkg) och tjänst konfigurations filen (. cscfg). Installera [Azure Storage NuGet-paketet](https://www.nuget.org/packages/Azure.Storage.Common/). Det här steget är valfritt om du använder ett befintligt lagrings konto. Lagrings kontots namn måste vara unikt.

    ```csharp
    string storageAccountName = “ContosoSAS”
    var stoInput = new StorageAccountCreateParameters
       {
            Location = m_location,
            Kind = Microsoft.Azure.Management.Storage.Models.Kind.StorageV2,
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku(SkuName.StandardRAGRS),
        };
            StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.Create(rgName,
            storageAccountName, stoInput);
            bool created = false;
            while (!created)
               {
                    Thread.Sleep(600);
                    var stos = m_SrpClient.StorageAccounts.ListByResourceGroup(rgName);
                    created =
                    stos.Any(
                        t =>
                        StringComparer.OrdinalIgnoreCase.Equals(t.Name, storageAccountName));
                }
        
    StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.GetProperties(rgName, storageAccountName);.
    var accountKeyResult = m_SrpClient.StorageAccounts.ListKeysWithHttpMessagesAsync(rgName, storageAccountName).Result;
    CloudStorageAccount storageAccount = new CloudStorageAccount(new StorageCredentials(storageAccountName, accountKeyResult.Body.Keys.FirstOrDefault(). Value), useHttps: true);
        
    var blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExistsAsync().Wait();
    sharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddDays(-1);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddDays(2);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
    ```

4. Ladda upp Service Pack-filen (. cspkg) till lagrings kontot. Paket-URL: en kan vara en SAS-URI (signatur för delad åtkomst) från valfritt lagrings konto.

    ```csharp
    CloudBlockBlob cspkgblockBlob = container.GetBlockBlobReference(“ContosoApp.cspkg”);
    cspkgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cspkg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string cspkgsasContainerToken = cspkgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cspkgSASUrl = cspkgblockBlob.Uri + cspkgsasContainerToken;
    ```

5. Överför tjänst konfigurations filen (. cscfg) till lagrings kontot. Ange tjänst konfiguration som antingen sträng XML eller URL-format.

    ```csharp
    CloudBlockBlob cscfgblockBlob = container.GetBlockBlobReference(“ContosoApp.cscfg”);
    cscfgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cscfg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasCscfgContainerToken = cscfgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cscfgSASUrl = cscfgblockBlob.Uri + sasCscfgContainerToken;
    ```

6. Skapa ett virtuellt nätverk och ett undernät. Installera [Azure Network NuGet-paketet](https://www.nuget.org/packages/Azure.ResourceManager.Network/). Det här steget är valfritt om du använder ett befintligt nätverk och undernät.

    ```csharp
    VirtualNetwork vnet = new VirtualNetwork(name: vnetName) 
       { 
            AddressSpace = new AddressSpace 
               { 
                    AddressPrefixes = new List<string> { "10.0.0.0/16" } 
               }, 
                    Subnets = new List<Subnet> 
                    { 
                        new Subnet(name: subnetName) 
                        { 
                            AddressPrefix = "10.0.0.0/24" 
                        } 
                    }, 
                    Location = m_location 
               }; 
    m_NrpClient.VirtualNetworks.CreateOrUpdate(resourceGroupName, “ContosoVNet”, vnet);
    ```

7. Skapa en offentlig IP-adress och ange egenskapen DNS-etikett för den offentliga IP-adressen. Cloud Services (utökad support) stöder bara [Basic] ( https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU offentliga IP-adresser. Standard-SKU offentliga IP-adresser fungerar inte med Cloud Services.
Om du använder en statisk IP-adress måste du referera till den som en Reserverad IP i tjänst konfigurations filen (. cscfg)

    ```csharp
    PublicIPAddress publicIPAddressParams = new PublicIPAddress(name: “ContosIp”) 
       { 
            Location = m_location, 
            PublicIPAllocationMethod = IPAllocationMethod.Dynamic, 
            DnsSettings = new PublicIPAddressDnsSettings() 
               { 
                    DomainNameLabel = “contosoappdns” 
               } 
       }; 
    PublicIPAddress publicIpAddress = m_NrpClient.PublicIPAddresses.CreateOrUpdate(resourceGroupName, publicIPAddressName, publicIPAddressParams);
    ```

8. Skapa ett nätverks profil objekt och koppla den offentliga IP-adressen till belastningsutjämnarens klient del. Azure-plattformen skapar automatiskt en klassisk "SKU"-belastnings Utjämnings resurs i samma prenumeration som moln tjänst resursen. Belastnings Utjämnings resursen är en skrivskyddad resurs i ARM. Alla uppdateringar av resursen stöds endast via Cloud Service-distributionspaket (. cscfg &. csdef)

    ```csharp
    LoadBalancerFrontendIPConfiguration feipConfiguration = new LoadBalancerFrontendIPConfiguration() 
        { 
            Name = “ContosoFe”, 
            Properties = new LoadBalancerFrontendIPConfigurationProperties() 
                { 
                PublicIPAddress = new CM.SubResource() 
                    { 
                        Id = $"/subscriptions/{m_subId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{publicIPAddressName}", 
                    } 
                } 
        }; 
    
    CloudServiceNetworkProfile cloudServiceNetworkProfile = new CloudServiceNetworkProfile() 
        { 
            LoadBalancerConfigurations = new List<LoadBalancerConfiguration>() 
                { 
                    new LoadBalancerConfiguration() 
                       { 
                        Name  = 'ContosoLB', 
                        Properties = new LoadBalancerConfigurationProperties() 
                            { 
                            FrontendIPConfigurations = new List<LoadBalancerFrontendIPConfiguration>() 
                                { 
                                feipConfig 
                                } 
                            } 
                        } 
                } 
        }; 
    
    ```

9. Skapa ett nyckelvalv. Det här nyckel valvet används för att lagra certifikat som är associerade med Cloud Services-roller (utökade stöd). Nyckel valvet måste finnas i samma region och prenumeration som Cloud Services-instansen (utökad support) och ha ett unikt namn. Mer information finns i [använda certifikat med Azure Cloud Services (utökad support)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US”
    ```

10. Uppdatera nyckel valvets åtkomst princip och bevilja certifikat behörigheter till ditt användar konto.

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg'      -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete
    ```

    Du kan också ange åtkomst principen via objekt-ID (som du kan hämta genom att köra `Get-AzADUser` ).

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -     ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates          create,get,list,delete
    ```

11. I det här exemplet ska vi lägga till ett självsignerat certifikat i ett nyckel valv. Certifikatets tumavtryck måste läggas till i tjänst konfigurations filen (. cscfg) för distribution på Cloud Services-roller (utökad support).

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -       SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -      CertificatePolicy $Policy
    ```

12. Skapa ett operativ system profil objekt. OS-profilen anger de certifikat som är associerade med Cloud Services-roller (utökade stöd). Här är det samma certifikat som vi skapade i föregående steg.

    ```csharp
    CloudServiceOsProfile cloudServiceOsProfile = 
        new CloudServiceOsProfile 
           { 
                Secrets = new List<CloudServiceVaultSecretGroup> 
                   { 
                        New CloudServiceVaultSecretGroup { 
                        SourceVault = <sourceVault>, 
                        VaultCertificates = <vaultCertificates> 
                        } 
                   } 
           };
    ```

13. Skapa ett roll profil objekt. En roll profil definierar rollspecifika egenskaper för en SKU, till exempel namn, kapacitet och nivå. 

    I det här exemplet definierar vi två roller: ContosoFrontend och ContosoBackend. Roll profil informationen bör matcha den roll konfiguration som definierats i tjänst konfigurations filen (. cscfg) och tjänst definitions filen (. csdef).

    ```csharp
    CloudServiceRoleProfile cloudServiceRoleProfile = new CloudServiceRoleProfile()
       {
            Roles = new List<CloudServiceRoleProfileProperties>();
            
                // foreach role in cloudService
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoFrontend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoBackend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                    }
                    }
    ```

14. Valfritt Skapa ett tilläggs profil objekt som du vill lägga till i din Cloud Services (utökad support) instans. I det här exemplet lägger vi till ett RDP-tillägg.

    ```csharp
    string rdpExtensionPublicConfig = "<PublicConfig>" +
        "<UserName>adminRdpTest</UserName>" +
        "<Expiration>2021-10-27T23:59:59</Expiration>" +
        "</PublicConfig>";
        string rdpExtensionPrivateConfig = "<PrivateConfig>" +
        "<Password>VsmrdpTest!</Password>" +
        "</PrivateConfig>";
    
        Extension rdpExtension = new Extension
                {
                    Name = name,
                    Properties = new CloudServiceExtensionProperties
                    {
                        Publisher = "Microsoft.Windows.Azure.Extensions",
                        Type = "RDP",
                        TypeHandlerVersion = "1.2.1",,
                        AutoUpgradeMinorVersion = true,
                        Settings = rdpExtensionPublicConfig,
                        ProtectedSettings = rdpExtensionPrivateConfig,
                        RolesAppliedTo = [“*”],
                    }
                };
                        
    CloudServiceExtensionProfile cloudServiceExtensionProfile = new CloudServiceExtensionProfile
        {
            Extensions = rdpExtension
        };
    ```

15. Skapa distributionen av Cloud Services-instansen (utökad support).

    ```csharp
    CloudService cloudService = new CloudService
        {
            Properties = new CloudServiceProperties
                {
                    RoleProfile = cloudServiceRoleProfile
                    Configuration = < Add Cscfg xml content here>,
                    // ConfigurationUrl = <Add your configuration URL here>,
                    PackageUrl = <Add cspkg SAS url here>,
                    ExtensionProfile = cloudServiceExtensionProfile,
                    OsProfile= cloudServiceOsProfile,
                    NetworkProfile = cloudServiceNetworkProfile,
                    UpgradeMode = 'Auto'
                },
                    Location = m_location
                };
    
    CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
    ```

## <a name="next-steps"></a>Nästa steg
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera Cloud Services (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), en [mall](deploy-template.md)eller [Visual Studio](deploy-visual-studio.md).
- Besök [exempel lagrings platsen för Cloud Services (utökad support)](https://github.com/Azure-Samples/cloud-services-extended-support)
