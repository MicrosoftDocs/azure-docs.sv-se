---
title: Köra ett skript när en Azure Service Fabric-tjänst startar
description: Lär dig hur du konfigurerar en princip för en Service Fabric tjänst för konfigurations start punkt och kör ett skript när tjänsten startas.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012535"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Köra skript för start av tjänster som ett lokalt konto för användaren eller systemet
Innan en körbar Service Fabric-tjänst startar kan det vara nödvändigt att köra vissa konfigurations-eller konfigurations arbeten.  Du kan till exempel konfigurera miljövariabler. Du kan ange ett skript som ska köras innan den körbara tjänsten startar i tjänst manifestet för tjänsten. Genom att konfigurera en RunAs-princip för start punkten för tjänst konfigurationen kan du ändra vilket konto som den körbara filen för installations programmet körs under.  Med en separat installations start punkt kan du köra hög privilegie rad konfiguration under en kort tids period, så att tjänst värd filen inte behöver köras med hög behörighet under längre tid.

Installations start punkten (**SetupEntryPoint** i [tjänst manifestet](service-fabric-application-and-service-manifests.md)) är en privilegie rad start punkt som standard körs med samma autentiseringsuppgifter som Service Fabric (vanligt vis kontot *NetworkService* ) före någon annan start punkt. Den körbara filen som anges av **EntryPoint** är vanligt vis den tids krävande tjänst värden. Den körbara **Start punkten** körs när den körbara filen **SetupEntryPoint** har slutförts. Den resulterande processen övervakas och startas om och börjar igen med **SetupEntryPoint** om den skulle stängas av eller kraschar. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurera tjänstens konfigurationsstartpunkt
Följande är ett enkelt tjänst manifest exempel för en tillstånds lös tjänst som anger ett installations skript *MySetup.bat* i **SetupEntryPoint** för tjänsten.  **Argument** används för att skicka argument till skriptet när det körs.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurera principen för en start punkt för tjänst konfigurationen
Som standard körs den körbara filen för konfigurations platsen för tjänsten med samma autentiseringsuppgifter som Service Fabric (vanligt vis kontot *NetworkService* ).  I applikations manifestet kan du ändra säkerhets behörigheterna för att köra start skriptet under ett lokalt system konto eller ett administratörs konto.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurera principen med hjälp av ett lokalt system konto
Följande program manifest exempel visar hur du konfigurerar start punkten för tjänst konfigurationen som ska köras under användar administratörs konto (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
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

Skapa först avsnittet **huvud** namn med ett användar namn, till exempel SetupAdminUser. SetupAdminUser-användarkontot är medlem i system gruppen Administratörer.

Sedan, under avsnittet **service manifest import** , konfigurerar du en princip för att tillämpa det här huvudobjektet på **SetupEntryPoint**. Den här principen anger Service Fabric att när **MySetup.bat** -filen körs bör den köras som SetupAdminUser (med administratörs behörighet). Eftersom du *inte* har tillämpat en princip på huvud start punkten körs koden i **MyServiceHost.exe** under systemets **NetworkService** -konto. Detta är standard kontot som alla tjänst start punkter körs som.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurera principen med hjälp av lokala system konton
Det är ofta bättre att köra start skriptet med ett lokalt system konto i stället för ett administratörs konto. Att köra RunAs-principen som medlem i gruppen administratörer fungerar vanligt vis inte bra eftersom datorerna har användar Access Control (UAC) aktiverat som standard. I sådana fall är rekommendationen att köra SetupEntryPoint som LocalSystem, i stället för en lokal användare som har lagts till i gruppen Administratörer. I följande exempel visas inställning av SetupEntryPoint som ska köras som LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> För Linux-kluster, för att köra en tjänst eller konfigurations start punkten som **rot**, kan du ange  **den** startenhet som **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Kör ett skript från start punkten för installationen
Lägg nu till ett start skript i projektet som ska köras under administratörs behörighet. 

I Visual Studio högerklickar du på tjänst projektet och lägger till en ny fil med namnet *MySetup.bat*.

Se sedan till att *MySetup.bat* -filen ingår i tjänst paketet. Som standard är det inte. Markera filen, högerklicka för att hämta snabb menyn och välj **Egenskaper**. I dialog rutan Egenskaper ser du till att **Kopiera till utdata-katalogen** är inställt på **Kopiera om nyare**. Se följande skärmbild.

![Visual Studio-CopyToOutput för SetupEntryPoint-kommando fil][image1]

Redigera *MySetup.bat* -filen och Lägg till följande kommandon ange en system miljö variabel och mata ut en textfil:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Därefter skapar och distribuerar du lösningen till ett lokalt utvecklings kluster. När tjänsten har startats, som visas i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), kan du se att MySetup.bat-filen lyckades på två sätt. Öppna en PowerShell-kommandotolk och skriv:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Anteckna sedan namnet på noden där tjänsten distribuerades och startades i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Till exempel nod 2. Gå sedan till arbets katalogen för program instansen för att hitta out.txt-filen som visar värdet för **TestVariable**. Om den här tjänsten exempelvis har distribuerats till nod 2 kan du gå till den här sökvägen för **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Kör PowerShell-kommandon från en installations start punkt
Om du vill köra PowerShell från **SetupEntryPoint** -punkten kan du köra **PowerShell.exe** i en batch-fil som pekar på en PowerShell-fil. Lägg först till en PowerShell-fil till tjänst projektet, till exempel **MySetup.ps1**. Kom ihåg att ange alternativet *Kopiera om en senare* egenskap så att filen också ingår i tjänst paketet. I följande exempel visas en batch-fil som startar en PowerShell-fil som kallas MySetup.ps1, som anger en system miljö variabel som kallas **TestVariable**.

MySetup.bat att starta en PowerShell-fil:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

I PowerShell-filen lägger du till följande för att ange en system miljö variabel:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> När kommando filen körs tittar den som standard på programmappen som heter **arbete** för filer. I det här fallet vill vi när MySetup.bat körs, vill vi att det ska hitta MySetup.ps1-filen i samma mapp, vilket är mappen program **kod paket** . Om du vill ändra den här mappen ställer du in arbetsmappen:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Felsöka ett start skript lokalt med omdirigering av konsol
Ibland är det användbart för fel söknings syfte att se konsolens utdata från att köra ett installations skript. Du kan ange en princip för omdirigering av konsol på installations start punkten i tjänst manifestet, som skriver utdata till en fil. Filens utdata skrivs till programmappen som heter **log** på den klusternod där programmet distribueras och körs. 

> [!WARNING]
> Använd aldrig konsolens omdirigerings policy i ett program som distribueras i produktion eftersom detta kan påverka programmets redundans. Använd *endast* detta för lokal utveckling och fel sökning.  
> 
> 

Följande tjänst manifest exempel visar hur du ställer in omdirigering av konsolen med ett FileRetentionCount-värde:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Om du nu ändrar MySetup.ps1-filen för att skriva ett **eko** kommando, skrivs detta till utdatafilen för fel söknings syfte:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> När du har felsöker skriptet tar du omedelbart bort den här konsolens omdirigerings princip.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Lär dig mer om program-och tjänst säkerhet](service-fabric-application-and-service-security.md)
* [Förstå program modellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
