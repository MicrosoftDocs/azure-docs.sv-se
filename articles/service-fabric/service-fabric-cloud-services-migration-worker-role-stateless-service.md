---
title: Konvertera Azure Cloud Services-appar till Service Fabric
description: Den här guiden jämför Cloud Services Web-och Worker-roller och Service Fabric tillstånds lösa tjänster för att hjälpa till att migrera från Cloud Services till Service Fabric.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: cf593f793aabf2a0650684ed8d02fe02d756ec2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575745"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guide för att konvertera webb-och arbets roller till Service Fabric tillstånds lösa tjänster
I den här artikeln beskrivs hur du migrerar Cloud Services Web-och Worker-roller till Service Fabric tillstånds lösa tjänster. Det här är den enklaste migreringen från Cloud Services till Service Fabric för program vars övergripande arkitektur kommer att stanna på samma sätt.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Cloud Service-projekt för att Service Fabric program projekt
 Ett moln tjänst projekt och ett Service Fabric-programprojekt har en liknande struktur och båda representerar distributions enheten för ditt program, det vill säga de definierar det kompletta paketet som distribueras för att köra programmet. Ett moln tjänst projekt innehåller en eller flera webb-eller arbets roller. På samma sätt innehåller ett Service Fabric program projekt en eller flera tjänster. 

Skillnaden är att Cloud Service-projektet Couples program distributionen med en VM-distribution och därför innehåller konfigurations inställningar för virtuella datorer, medan Service Fabric programprojektet definierar ett program som ska distribueras till en uppsättning befintliga virtuella datorer i ett Service Fabric-kluster. Själva Service Fabric-klustret distribueras bara en gång, antingen via en Resource Manager-mall eller via Azure Portal och flera Service Fabric program kan distribueras till den.

![Jämförelse av Service Fabric och Cloud Services projekt][3]

## <a name="worker-role-to-stateless-service"></a>Arbets roll för tillstånds lös tjänst
En arbets roll representerar en tillstånds lös arbets belastning, vilket innebär att varje instans av arbets belastningen är identisk och begär Anden kan skickas till vilken instans som helst. Varje instans förväntas inte komma ihåg den tidigare begäran. Tillstånd att arbets belastningen fungerar på hanteras av ett externt tillstånds lager, till exempel Azure Table Storage eller Azure Cosmos DB. I Service Fabric representeras den här typen av arbets belastning av en tillstånds lös tjänst. Den enklaste metoden för att migrera en arbets roll till Service Fabric kan göras genom att konvertera arbets roll kod till en tillstånds lös tjänst.

![Arbets roll för tillstånds lös tjänst][4]

## <a name="web-role-to-stateless-service"></a>Webb roll för tillstånds lös tjänst
På samma sätt som arbets rollen representerar en webbroll även en tillstånds lös arbets belastning, och därför kan den också mappas till en Service Fabric tillstånds lös tjänst. Till skillnad från webb roller stöder Service Fabric dock inte IIS. För att migrera ett webb program från en webbroll till en tillstånds lös tjänst måste du först byta till ett webb ramverk som kan vara egen värd och som inte är beroende av IIS eller system. Web, till exempel ASP.NET Core 1.

| **Program** | **Stöds** | **Sökväg för migrering** |
| --- | --- | --- |
| ASP.NET webb formulär |Inga |Konvertera till ASP.NET Core 1 MVC |
| ASP.NET MVC |Med migrering |Uppgradera till ASP.NET Core 1 MVC |
| ASP.NET Web API |Med migrering |Använd egen server eller ASP.NET Core 1 |
| ASP.NET Core 1 |Ja |Ej tillämpligt |

## <a name="entry-point-api-and-lifecycle"></a>API för start punkt och livs cykel
Arbets rollen och Service Fabric tjänst-API: er erbjuder liknande start punkter: 

| **Start punkt** | **Arbets roll** | **Service Fabric tjänst** |
| --- | --- | --- |
| Bearbetar |`Run()` |`RunAsync()` |
| VM-start |`OnStart()` |Ej tillämpligt |
| Stoppa virtuell dator |`OnStop()` |Ej tillämpligt |
| Öppna lyssnare för klient begär Anden |Ej tillämpligt |<ul><li> `CreateServiceInstanceListener()` för tillstånds lös</li><li>`CreateServiceReplicaListener()` för tillstånds känslig</li></ul> |

### <a name="worker-role"></a>Arbets roll
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Service Fabric tillstånds lös tjänst
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Båda har en primär "kör"-åsidosättning som börjar bearbetas. Service Fabric Services kombinerar `Run` , `Start` och `Stop` till en enda start punkt, `RunAsync` . Tjänsten ska börja fungera när `RunAsync` den startas och ska sluta fungera när `RunAsync` metodens CancellationToken är signalerad. 

Det finns flera viktiga skillnader mellan livs cykeln och livs längden för arbets roller och Service Fabric tjänster:

* **Livs cykel:** Den största skillnaden är att en arbets roll är en virtuell dator och att dess livs cykel är kopplad till den virtuella datorn, vilket inkluderar händelser för när den virtuella datorn startar och stoppas. En Service Fabric tjänst har en livs cykel som är separat från VM-livscykeln, så den inkluderar inte händelser för när den virtuella värddatorn eller datorn startar och stoppas, eftersom de inte är relaterade.
* **Livs längd:** En arbets roll instans återanvänds om `Run` metoden avslutas. `RunAsync`Metoden i en Service Fabric-tjänst kan dock köras för att slutföras och tjänst instansen håller på att vara igång. 

Service Fabric tillhandahåller en valfri start punkt för kommunikations konfiguration för tjänster som lyssnar efter klient begär Anden. Både RunAsync och kommunikations start punkten är valfria åsidosättningar i Service Fabric Services – din tjänst kan välja att bara lyssna på klient begär Anden eller köra en bearbetnings slinga eller båda – som är orsaken till att RunAsync-metoden tillåts avsluta utan att starta om tjänst instansen, eftersom den kan fortsätta att lyssna efter klient begär Anden.

## <a name="application-api-and-environment"></a>Program-API och miljö
API: et för Cloud Servicess miljön innehåller information och funktioner för den aktuella VM-instansen samt information om andra roll instanser för virtuella datorer. Service Fabric innehåller information som rör dess körning och viss information om noden som en tjänst körs för tillfället. 

| **Miljö aktivitet** | **Molntjänster** | **Service Fabric** |
| --- | --- | --- |
| Konfigurations inställningar och ändrings meddelande |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokal lagring |`RoleEnvironment` |`CodePackageActivationContext` |
| Slut punkts information |`RoleInstance` <ul><li>Aktuell instans: `RoleEnvironment.CurrentRoleInstance`</li><li>Andra roller och instanser: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` för aktuell Node-adress</li><li>`FabricClient` och `ServicePartitionResolver` för tjänst slut punkts identifiering</li> |
| Miljö emulering |`RoleEnvironment.IsEmulated` |Ej tillämpligt |
| Samtidig ändrings händelse |`RoleEnvironment` |Ej tillämpligt |

## <a name="configuration-settings"></a>Konfigurationsinställningar
Konfigurations inställningar i Cloud Services anges för en virtuell dator roll och tillämpas på alla instanser av den virtuella dator rollen. De här inställningarna är nyckel/värde-par som anges i ServiceConfiguration. *. cscfg-filer och kan nås direkt via RoleEnvironment. I Service Fabric gäller inställningarna individuellt för varje tjänst och för varje program, i stället för till en virtuell dator, eftersom en virtuell dator kan vara värd för flera tjänster och program. En tjänst består av tre paket:

* **Kod:** innehåller tjänstens körbara filer, binärfiler, DLL-filer och andra filer som en tjänst behöver köra.
* **Config:** alla konfigurationsfiler och inställningar för en tjänst.
* **Data:** statiska datafiler som är associerade med tjänsten.

Vart och ett av dessa paket kan vara oberoende versions hantering och uppgraderas. Precis som med Cloud Services kan ett konfigurations paket nås via programmering via ett API och händelser är tillgängliga för att meddela tjänsten om en ändring av konfigurations paket. En Settings.xml-fil kan användas för nyckel värdes konfiguration och programmatisk åtkomst som liknar avsnittet appinställningar i en App.config-fil. Men till skillnad från Cloud Services kan ett Service Fabric konfigurations paket innehålla konfigurationsfiler i alla format, oavsett om det är XML, JSON, YAML eller ett anpassat binärformat. 

### <a name="accessing-configuration"></a>Åtkomst till konfiguration
#### <a name="cloud-services"></a>Cloud Services
Konfigurations inställningar från ServiceConfiguration. *. cscfg kan nås via `RoleEnvironment` . De här inställningarna är globalt tillgängliga för alla roll instanser i samma moln tjänst distribution.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Varje tjänst har sitt eget konfigurations paket. Det finns ingen inbyggd mekanism för globala konfigurations inställningar som är tillgängliga för alla program i ett kluster. När du använder Service Fabricens särskilda Settings.xml konfigurations fil i ett konfigurations paket kan värden i Settings.xml skrivas över på program nivå, vilket gör det möjligt att konfigurera konfigurations inställningar på program nivå.

Konfigurations inställningarna är åtkomst i varje tjänst instans via tjänstens `CodePackageActivationContext` .

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Konfigurations uppdaterings händelser
#### <a name="cloud-services"></a>Cloud Services
`RoleEnvironment.Changed`Händelsen används för att meddela alla roll instanser när en ändring sker i miljön, till exempel en konfigurations ändring. Detta används för att använda konfigurations uppdateringar utan återvinning av roll instanser eller omstart av en arbets process.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Var och en av de tre paket typerna i en service kod, konfiguration och data-har händelser som meddelar en tjänst instans när ett paket uppdateras, läggs till eller tas bort. En tjänst kan innehålla flera paket av varje typ. En tjänst kan till exempel ha flera konfigurations paket, varje individuellt versions-och uppgraderings Bart. 

Dessa händelser är tillgängliga för att förbruka ändringar i tjänst paket utan att starta om tjänst instansen.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Start åtgärder
Start åtgärder är åtgärder som vidtas innan ett program startar. En start åtgärd används vanligt vis för att köra installations skript med utökade privilegier. Både Cloud Services och Service Fabric stöd för start åtgärder. Den största skillnaden är Cloud Services att en start aktivitet är kopplad till en virtuell dator eftersom den är en del av en roll instans, men i Service Fabric en start aktivitet är kopplad till en tjänst som inte är kopplad till någon viss virtuell dator.

| Service Fabric | Cloud Services |
| --- | --- |
| Konfigurations plats |Service definition. csdef |
| Behörigheter |"begränsad" eller "förhöjd" |
| Sekvensering |"enkel", "bakgrund", "förgrund" |

### <a name="cloud-services"></a>Cloud Services
I Cloud Services en start punkt konfigureras per roll i service definition. csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
I Service Fabric en start punkt konfigureras per tjänst i ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>En anteckning om utvecklings miljö
Både Cloud Services och Service Fabric är integrerade med Visual Studio med projektmallar och stöd för fel sökning, konfiguration och distribution av både lokalt och till Azure. Både Cloud Services och Service Fabric tillhandahåller också en lokal utvecklings körnings miljö. Skillnaden är att medan Cloud Service Development runtime emulerar den Azure-miljö som den körs på, Service Fabric inte använder en emulator – den använder den fullständiga Service Fabric Runtime. Den Service Fabric miljö som du kör på den lokala utvecklings datorn är samma miljö som körs i produktion.

## <a name="next-steps"></a>Nästa steg
Läs mer om Service Fabric Reliable Services och de grundläggande skillnaderna mellan Cloud Services och Service Fabric program arkitektur för att förstå hur du kan dra nytta av den fullständiga uppsättningen Service Fabric funktioner.

* [Komma igång med Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Konceptuell guide till skillnaderna mellan Cloud Services och Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
