---
title: Lägg till en HTTPS-slutpunkt med Kestrel
description: I den här självstudien lär du dig hur du lägger till en HTTPS-slutpunkt i en klientwebbtjänst i ASP.NET Core med hjälp av Kestrel och hur du distribuerar programmet till ett kluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: c675f8ece8369bcfc0055343221ac82aea59dec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326243"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Självstudie: Lägga till en HTTPS-slutpunkt i en klienttjänst i webb-API:t för ASP.NET Core med hjälp av Kestrel

Den här självstudiekursen är den tredje delen i en serie.  Du får lära dig att aktivera HTTPS i en ASP.NET Core-tjänst som körs på Service Fabric. När du är färdig har du ett röstningsprogram med en HTTPS-aktiverad webbklientdel i ASP.NET Core som lyssnar på port 443. Om du inte vill skapa röstningsprogrammet manuellt i [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-deploy-app-to-party-cluster.md) kan du [ladda ned källkoden](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) till det färdiga programmet.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Definiera en HTTPS-slutpunkt i tjänsten
> * Konfigurera Kestrel för användning av HTTPS
> * Installera TLS/SSL-certifikatet på de fjärranslutna klusternoderna
> * Ge NETWORK SERVICE åtkomst till certifikatets privata nyckel
> * Öppna port 443 i Azure-lastbalanseraren
> * Distribuera programmet till ett fjärrkluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Bygga ett .NET Service Fabric-program](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Distribuera programmet till ett fjärran slutet kluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Lägga till en HTTPS-slutpunkt i en klienttjänst i ASP.NET Core
> * [Konfigurera CI/CD med hjälp av Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installera Visual Studio 2019](https://www.visualstudio.com/) version 16,5 eller senare med arbets belastningarna **Azure Development** och **ASP.net och webb utveckling** .
* [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Hämta ett certifikat eller skapa ett självsignerat utvecklingscertifikat

För produktionsprogram ska du använda ett certifikat från en [certifikatutfärdare (CA)](https://wikipedia.org/wiki/Certificate_authority). För utveckling och testning kan du skapa och använda ett självsignerat certifikat. i SDK:t för Service Fabric finns skriptet *CertSetup.ps1* som skapar ett självsignerat certifikat och importerar det till certifikatlagret i `Cert:\LocalMachine\My`. Öppna en kommandotolk som administratör och kör följande kommando för att skapa ett certifikat med ämnet ”CN = mytestcert”:

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

Om du redan har en PFX-certifikatfil kör du följande för att importera certifikatet till certifikatlagret i `Cert:\LocalMachine\My`:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>Definiera en HTTPS-slutpunkt i tjänstmanifestet

Starta Visual Studio som **administratör** och öppna röstningsprogrammet. Öppna *VotingWeb/PackageRoot/ServiceManifest.xml* i Solution Explorer. Tjänstmanifestet definierar tjänstens slutpunkter.  Leta rätt på avsnittet **Endpoints** och redigera den befintliga slutpunkten ”ServiceEndpoint”.  Ändra namnet till ”EndpointHttps”, ställ in protokollet som *https*, typen till *Input* och porten till *443*.  Spara ändringarna.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>Konfigurera Kestrel för användning av HTTPS

Öppna filen *VotingWeb/VotingWeb.cs* i Solution Explorer.  Konfigurera Kestrel att använda HTTPS och slå upp certifikatet i `Cert:\LocalMachine\My`-lagret. Lägg till följande using-uttryck:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Uppdatera `ServiceInstanceListener` så att den nya slutpunkten *EndpointHttps* används och att den lyssnar på port 443. När du konfigurerar webbvärden att använda Kestrel-server måste du konfigurera Kestrel att lyssna efter IPv6-adresser på alla nätverksgränssnitt: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(FindMatchingCertificateBySubject());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Lägg även till följande metod så att Kestrel kan hitta certifikatet i `Cert:\LocalMachine\My`-lagret med hjälp av ämnet.  

Ersätt ”&lt;your_CN_value&gt;” med ”mytestcert” om du har skapat ett självsignerat certifikat med föregående PowerShell-kommando. Använd annars CN för ditt certifikat.
Tänk på att när det gäller lokal distribution till `localhost` det är det bättre att använda "CN = localhost" för att undvika undantag för autentisering.

```csharp
private X509Certificate2 FindMatchingCertificateBySubject(string subjectCommonName)
{
    using (var store = new X509Store(StoreName.My, StoreLocation.LocalMachine))
    {
        store.Open(OpenFlags.OpenExistingOnly | OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var matchingCerts = new X509Certificate2Collection();
    
    foreach (var enumeratedCert in certCollection)
    {
      if (StringComparer.OrdinalIgnoreCase.Equals(subjectCommonName, enumeratedCert.GetNameInfo(X509NameType.SimpleName, forIssuer: false))
        && DateTime.Now < enumeratedCert.NotAfter
        && DateTime.Now >= enumeratedCert.NotBefore)
        {
          matchingCerts.Add(enumeratedCert);
        }
    }

        if (matchingCerts.Count == 0)
    {
        throw new Exception($"Could not find a match for a certificate with subject 'CN={subjectCommonName}'.");
    }
        
        return matchingCerts[0];
    }
}


```

## <a name="grant-network-service-access-to-the-certificates-private-key"></a>Bevilja nätverks tjänst åtkomst till certifikatets privata nyckel

I föregående steg importerade du certifikatet till `Cert:\LocalMachine\My`-lagret på utvecklingsdatorn.  Nu ska du uttryckligen ge kontot som kör tjänsten (nätverks tjänsten som standard) åtkomst till certifikatets privata nyckel. Du kan göra det här steget manuellt (med verktyget certlm. msc), men det är bättre att köra ett PowerShell-skript automatiskt genom att [Konfigurera ett start skript](service-fabric-run-script-at-service-startup.md) i **SetupEntryPoint** i tjänst manifestet.

>[!NOTE]
> Service Fabric stöder deklaration av slut punkts certifikat efter tumavtryck eller ämnes namn. I så fall kommer körningen att konfigurera bindningen och åtkomst kontrol listan för certifikatets privata nyckel till identiteten som tjänsten kör som. Körnings miljön övervakar också certifikatet för ändringar/förnyelser och återaktiverar sedan motsvarande privata nyckel.

### <a name="configure-the-service-setup-entry-point"></a>Konfigurera tjänstens konfigurationsstartpunkt

Öppna *VotingWeb/PackageRoot/ServiceManifest.xml* i Solution Explorer.  I avsnittet **CodePackage** lägger du till noden **SetupEntryPoint** och sedan noden **ExeHost**.  För **ExeHost** ställer du in **Program** som ”Setup.bat” och **WorkingFolder** som ”CodePackage”.  När tjänsten VotingWeb startar körs skriptet Setup.bat i mappen CodePackage innan VotingWeb.exe startar.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>Lägga till konfigurationsskript för batch och PowerShell

Om du vill köra PowerShell från punkten **SetupEntryPoint** kan du köra PowerShell.exe i en batchfil som pekar på en PowerShell-fil. Lägg först till batchfilen i tjänstens projekt.  Öppna Solution Explorer, högerklicka på **VotingWeb**, välj **Lägg till**->**Nytt objekt** och lägg till en ny fil med namnet ”Setup.bat”.  Redigera filen *Setup.bat* och lägg till följande kommando:

```cmd
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Ändra egenskaperna för filen *Setup.bat* så att **Kopiera till utdatakatalog** får värdet ”Kopiera om nyare”.

![Ange filegenskaper][image1]

Öppna Solution Explorer, högerklicka på **VotingWeb**, välj **Lägg till**->**Nytt objekt** och lägg till en ny fil med namnet ”SetCertAccess.ps1”.  Redigera filen *SetCertAccess.ps1* och lägg till följande skript:

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Ändra *SetCertAccess.ps1*-filegenskaperna så att **Kopiera till utdatakatalog** anges till ”Kopiera om nyare”.

### <a name="run-the-setup-script-as-a-local-administrator"></a>Köra konfigurationsskriptet som lokal administratör

Som standard körs den körbara filen för tjänstens konfigurationsstartpunkt med samma autentiseringsuppgifter som Service Fabric (vanligtvis kontot NetworkService). För *SetCertAccess.ps1* krävs administratörsbehörighet. Du kan ändra säkerhetsbehörigheterna i manifestet så att startskriptet körs under ett lokalt administratörskonto.

Öppna *Voting/ApplicationPackageRoot/ApplicationManifest.xml* i Solution Explorer. Skapa först avsnittet **Principals** (Huvudkonton) och lägg till en ny användare (till exempel ”SetupAdminUser”). Lägg till användarkontot SetupAdminUser i systemgruppen Administrators.
I avsnittet **ServiceManifestImport** för VotingWebPkg ska du sedan konfigurera en **RunAsPolicy** så att huvudkontot SetupAdminUser används för konfigurationsstartpunkten. Den här principen anger för Service Fabric att filen Setup.bat körs som SetupAdminUser (med administratörsbehörighet).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="run-the-application-locally"></a>Köra appen lokalt

I Solution Explorer väljer du **röstnings** programmet och anger egenskapen för **program-URL** till "https: \/ /localhost: 443".

Spara alla filer och tryck på F5 för att köra programmet lokalt.  När programmet har distribuerats öppnas en webbläsare till https: \/ /localhost: 443. Om du använder ett självsignerat certifikat visas en varning om att datorn inte har förtroende för den här webbplatsens säkerhet.  Fortsätt till webbsidan.

![Skärm bild av Service Fabric röstning exempel app som körs i ett webbläsarfönster med URL: en https://localhost/ .][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Installera certifikatet på klusternoder

Innan du distribuerar programmet till Azure ska du installera certifikatet i `Cert:\LocalMachine\My`-lagret för alla de fjärranslutna klusternoderna.  Tjänster kan flyttas till olika noder i klustret.  När klientwebbtjänsten startar på en klusternod kommer startskriptet att leta upp certifikatet och konfigurera åtkomstbehörigheter.

Exportera först certifikatet till en PFX-fil. Öppna programmet certlm. msc och navigera till **personliga** > **certifikat**.  Högerklicka på certifikatet *mytestcert* och välj **Alla aktiviteter**>**Exportera**.

![Exportera certifikatet][image4]

I exportguiden väljer du **Ja, exportera den privata nyckeln** och sedan formatet PFX (Personal Information Exchange).  Exportera filen till *C:\Users\sfuser\votingappcert.pfx*.

Sedan installerar du certifikatet på fjärrklustret med hjälp av [de här angivna PowerShell-skripten](./scripts/service-fabric-powershell-add-application-certificate.md).

> [!Warning]
> Ett självsignerat certifikat räcker för utveckling och testning. För produktionsprogram ska du använda ett certifikat från en [certifikatutfärdare (CA)](https://wikipedia.org/wiki/Certificate_authority) istället för ett självsignerat certifikat.

## <a name="open-port-443-in-the-azure-load-balancer-and-virtual-network"></a>Öppna port 443 i Azure Load Balancer och Virtual Network

Öppna port 443 i lastbalanseraren om den inte redan är öppen.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

Gör samma sak för det associerade virtuella nätverket.

```powershell
$rulename="allowAppPort$port"
$nsgname="voting-vnet-security"
$RGname="voting_RG"
$port=443

# Get the NSG resource
$nsg = Get-AzNetworkSecurityGroup -Name $nsgname -ResourceGroupName $RGname

# Add the inbound security rule.
$nsg | Add-AzNetworkSecurityRuleConfig -Name $rulename -Description "Allow app port" -Access Allow `
    -Protocol * -Direction Inbound -Priority 3891 -SourceAddressPrefix "*" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange $port

# Update the NSG.
$nsg | Set-AzNetworkSecurityGroup
```

## <a name="deploy-the-application-to-azure"></a>Distribuera appen till Azure

Spara alla filer, växla från Debug till Release och tryck på F6 för att bygga om programmet.  Högerklicka på **Voting** i Solution Explorer och välj **Publicera**. Välj klustrets slutpunkt för anslutning som du skapade i [Distribuera ett program till ett kluster](service-fabric-tutorial-deploy-app-to-party-cluster.md), eller välj ett annat kluster.  Klicka på **Publicera** så att programmet publiceras till fjärrklustret.

När programmet distribuerats öppnar du en webbläsare och går till `https://mycluster.region.cloudapp.azure.com:443` (uppdatera webbadressen med klustrets slutpunkt för anslutning). Om du använder ett självsignerat certifikat visas en varning om att datorn inte har förtroende för den här webbplatsens säkerhet.  Fortsätt till webbsidan.

![Skärm bild av Service Fabric röstning exempel app som körs i ett webbläsarfönster med URL: en https://mycluster.region.cloudapp.azure.com:443 .][image3]

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Definiera en HTTPS-slutpunkt i tjänsten
> * Konfigurera Kestrel för användning av HTTPS
> * Installera TLS/SSL-certifikatet på de fjärranslutna klusternoderna
> * Ge NETWORK SERVICE åtkomst till certifikatets privata nyckel
> * Öppna port 443 i Azure-lastbalanseraren
> * Distribuera programmet till ett fjärrkluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Konfigurera CI/CD med hjälp av Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
