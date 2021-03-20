---
title: Azure Service Fabric-distribution med FabricClient
description: 'Använd FabricClient-API: er för att distribuera och ta bort program i Service Fabric.'
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 343a37c983b1d64a4b1986913d9d6fd648a113fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785557"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Distribuera och ta bort program med FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

När en [program typ har paketerats][10]är den redo för distribution till ett Azure Service Fabric-kluster. Distributionen omfattar följande tre steg:

1. Ladda upp programpaketet till avbildnings arkivet
2. Registrera program typen
3. Ta bort programpaketet från avbildnings arkivet
4. Skapa program instansen

När du har distribuerat ett program och kört en instans i klustret kan du ta bort program instansen och dess program typ. Ta bort ett program från klustret helt genom att följa dessa steg:

1. Ta bort (eller ta bort) den program instans som körs
2. Avregistrera program typen om du inte längre behöver den

Om du använder Visual Studio för att distribuera och felsöka program i ditt lokala utvecklings kluster hanteras alla föregående steg automatiskt via ett PowerShell-skript.  Det här skriptet finns i mappen *scripts* i programprojektet. Den här artikeln innehåller en bakgrund för vad skriptet gör så att du kan utföra samma åtgärder utanför Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Anslut till klustret
Anslut till klustret genom att skapa en [FabricClient](/dotnet/api/system.fabric.fabricclient) -instans innan du kör något av kod exemplen i den här artikeln. Exempel på att ansluta till ett lokalt utvecklings kluster eller ett fjärran slutet kluster eller ett kluster som skyddas med hjälp av Azure Active Directory, X509-certifikat eller Windows Active Directory finns i [ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Kör följande exempel för att ansluta till det lokala utvecklings klustret:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Ladda upp programpaketet
Anta att du skapar och paketerar ett program med namnet mina *program* i Visual Studio. Som standard är program typs namnet som anges i ApplicationManifest.xml "MyApplicationType".  Programpaketet som innehåller det nödvändiga applikations manifestet, tjänst manifesten och kod/config/data paket finns i *C:\Users \& lt; username &gt; \Documents\Visual Studio 2019 \ Projects\MyApplication\MyApplication\pkg\Debug*.

När du överför programpaketet placeras det på en plats som är tillgänglig för de interna Service Fabric-komponenterna. Service Fabric verifierar programpaketet under registreringen av programpaketet. Men om du vill verifiera programpaketet lokalt (dvs innan du laddar upp) använder du cmdleten [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API överför programpaketet till klustrets avbildnings arkiv. 

Om programpaketet är stort och/eller har många filer kan du [Komprimera det](service-fabric-package-apps.md#compress-a-package) och kopiera det till avbildnings lagret med hjälp av PowerShell. Komprimeringen minskar storleken och antalet filer.

Se [förstå anslutnings strängen för avbildnings arkivet](service-fabric-image-store-connection-string.md) för kompletterande information om avbildnings lagret och anslutnings strängen för avbildnings arkivet.

## <a name="register-the-application-package"></a>Registrera programpaketet
Den program typ och version som har deklarerats i applikations manifestet blir tillgängliga när programpaketet registreras. Systemet läser paketet som laddades upp i föregående steg, verifierar paketet, bearbetar paket innehållet och kopierar det bearbetade paketet till en intern system plats.  

[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) -API: t registrerar program typen i klustret och gör den tillgänglig för distribution.

[GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) -API: et innehåller information om alla program typer som har registrerats korrekt. Du kan använda det här API: et för att fastställa när registreringen är färdig.

## <a name="remove-an-application-package-from-the-image-store"></a>Ta bort ett program paket från avbildnings arkivet
Vi rekommenderar att du tar bort applikations paketet när programmet har registrerats.  Om du tar bort program paket från avbildnings arkivet frigörs system resurser.  Att behålla oanvända programpaket använder disk lagring och leder till problem med program prestanda. Ta bort programpaketet från avbildnings arkivet med [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) -API: et.

## <a name="create-an-application-instance"></a>Skapa en program instans
Du kan skapa en instans av ett program från vilken program typ som helst som har registrerats med hjälp av [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) -API: et. Namnet på varje program måste börja med schemat *"Fabric:"* och måste vara unikt för varje program instans (inom ett kluster). Alla standard tjänster som definieras i program manifestet för mål program typen skapas också.

Flera program instanser kan skapas för en specifik version av en registrerad program typ. Varje program instans körs i isolering med en egen arbets katalog och en uppsättning processer.

Om du vill se vilka namngivna program och tjänster som körs i klustret kör du [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) -och [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) -API: erna.

## <a name="create-a-service-instance"></a>Skapa en tjänst instans
Du kan skapa en instans av en tjänst från en tjänst typ med [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) -API: et.  Om tjänsten deklareras som en standard tjänst i applikations manifestet instansieras tjänsten när programmet instansieras.  Anrop av [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) -API: et för en tjänst som redan är instansierad returnerar ett undantag av typen FabricException. Undantaget kommer att innehålla en felkod med värdet FabricErrorCode. ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Ta bort en tjänst instans
När en tjänst instans inte längre behövs kan du ta bort den från den aktiva program instansen genom att anropa [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) -API: et.  

> [!WARNING]
> Den här åtgärden kan inte ångras och tjänst tillstånd kan inte återställas.

## <a name="remove-an-application-instance"></a>Ta bort en program instans
När en program instans inte längre behövs kan du ta bort den permanent med namnet med hjälp av [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) -API: et. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) tar automatiskt bort alla tjänster som tillhör programmet, och tar permanent bort alla tjänst tillstånd.

> [!WARNING]
> Den här åtgärden kan inte ångras och det går inte att återställa program tillstånd.

## <a name="unregister-an-application-type"></a>Avregistrera en program typ
När en viss version av en program typ inte längre behövs bör du avregistrera den specifika versionen av program typen med [unregister-ServiceFabricApplicationType-](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API: et. Att avregistrera oanvända versioner av program typer frigör lagrings utrymme som används av avbildnings arkivet. En version av en program typ kan avregistreras så länge inga program instansieras mot den versionen av program typen. Program typen kan inte heller ha inga väntande program uppgraderingar som refererar till den versionen av program typen.

## <a name="troubleshooting"></a>Felsökning
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage ber om en ImageStoreConnectionString
Den Service Fabric SDK-miljön bör redan ha rätt standardinställningar. Men om det behövs ska ImageStoreConnectionString för alla kommandon matcha det värde som Service Fabric-klustret använder. Du hittar ImageStoreConnectionString i kluster manifestet som hämtats med hjälp av kommandona [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) och Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Cmdleten **Get-ImageStoreConnectionStringFromClusterManifest** , som är en del av modulen Service Fabric SDK PowerShell, används för att hämta anslutnings strängen för avbildnings lagret.  Importera SDK-modulen genom att köra:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


ImageStoreConnectionString finns i kluster manifestet:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Se [förstå anslutnings strängen för avbildnings arkivet](service-fabric-image-store-connection-string.md) för kompletterande information om avbildnings lagret och anslutnings strängen för avbildnings arkivet.

### <a name="deploy-large-application-package"></a>Distribuera stort programpaket
Problem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API nådde tids gränsen för ett stort programpaket (GB GB).
Prova:
- Ange en större tids gräns för metoden [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) med `timeout` parameter. Som standard är tids gränsen 30 minuter.
- Kontrol lera nätverks anslutningen mellan käll datorn och klustret. Om anslutningen är långsam bör du överväga att använda en dator med en bättre nätverks anslutning.
Om klient datorn finns i en annan region än klustret, bör du överväga att använda en klient dator i en närmare eller samma region som klustret.
- Kontrol lera om du träffar extern begränsning. Om till exempel bild arkivet har kon figurer ATS för att använda Azure Storage kan uppladdning vara begränsad.

Problem: uppladdnings paketet har slutförts, men [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API-timeout. Pröva
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar det till avbildnings arkivet.
Komprimeringen minskar storleken och antalet filer, vilket i sin tur minskar mängden trafik och arbete som Service Fabric måste utföra. Uppladdnings åtgärden kan vara långsammare (särskilt om du tar med komprimerings tiden), men registrerar och avregistrerar program typen går snabbare.
- Ange en större tids gräns för [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) -API med `timeout` parameter.

### <a name="deploy-application-package-with-many-files"></a>Distribuera programpaket med många filer
Problem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) nådde tids gränsen för ett programpaket med många filer (ordning på tusental).
Prova:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar det till avbildnings arkivet. Komprimeringen minskar antalet filer.
- Ange en större tids gräns för [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) med `timeout` parameter.

## <a name="code-example"></a>Kodexempel
I följande exempel kopieras ett programpaket till avbildnings arkivet och program typen etableras. Sedan skapar exemplet en program instans och skapar en tjänst instans. Slutligen tar exemplet bort program instansen, avetablerar program typen och tar bort programpaketet från avbildnings arkivet.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Nästa steg
[Service Fabric program uppgradering](service-fabric-application-upgrade.md)

[Introduktion till Service Fabric hälsa](service-fabric-health-introduction.md)

[Diagnostisera och felsöka en Service Fabric-tjänst](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellera ett program i Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
