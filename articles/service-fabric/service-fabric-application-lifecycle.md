---
title: Programmets livs cykel i Service Fabric
description: Beskriver hur du utvecklar, distribuerar, testar, uppgraderar, underhåller och tar bort Service Fabric program.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: ae0c79cdaafc8fc016d463a01046f0a02121330a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785743"
---
# <a name="service-fabric-application-lifecycle"></a>Livscykeln för Service Fabric-program
Precis som med andra plattformar går ett program på Azure Service Fabric vanligt vis igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. Service Fabric ger förstklassig support för hela program livs cykeln för moln program, från utveckling genom distribution, daglig hantering och underhåll av eventuell inaktive ring. Tjänste modellen gör det möjligt för flera olika roller att delta oberoende av programmets livs cykel. Den här artikeln innehåller en översikt över API: erna och hur de används av de olika rollerna under faserna i Service Fabric programmets livs cykel.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Tjänst modell roller
Tjänst modell rollerna är:

* **Tjänste utvecklare**: utvecklar modulära och allmänna tjänster som kan återanvändas och användas i flera program av samma typ eller olika typer. Till exempel kan en Queue Service användas för att skapa ett biljett program (supportavdelningen) eller ett e-handelsprogram (Shopping vagn).
* **Programutvecklare**: skapar program genom att integrera en samling tjänster för att uppfylla vissa specifika krav eller scenarier. Till exempel kan en e-handelswebbplats integrera "JSON-tillstånds lös Front-End tjänst," "" auktions tillstånds känslig tjänst "och" kö tillstånds känslig tjänst "för att bygga en auktions lösning.
* **Program administratör**: fatta beslut om program konfigurationen (fylla i parametrarna för konfigurations mal len), distribution (mappning till tillgängliga resurser) och tjänst kvalitet. En program administratör bestämmer till exempel språk språket (engelska för USA eller japanska för Japan, till exempel) för programmet. Ett annat distribuerat program kan ha olika inställningar.
* **Operator**: distribuerar program baserat på program konfigurationen och kraven som anges av program administratören. Till exempel kan en operatör etablera och distribuera programmet och se till att det körs i Azure. Operatörer övervakar program hälso-och prestanda information och upprätthåller den fysiska infrastrukturen vid behov.

## <a name="develop"></a>Utveckla
1. En *service utvecklare* utvecklar olika typer av tjänster med hjälp av [Reliable Actors](service-fabric-reliable-actors-introduction.md) -eller [Reliable Services](service-fabric-reliable-services-introduction.md) programmerings modellen.
2. En *tjänsteutvecklare* beskriver i sin form de utvecklade tjänst typerna i en tjänst manifest fil bestående av en eller flera kod-, konfigurations-och data paket.
3. En *programutvecklare* skapar sedan ett program med olika tjänst typer.
4. En *programutvecklare* beskriver till exempel program typen i ett program manifest genom att referera till tjänst manifesten för komponent tjänsterna och på lämpligt sätt åsidosätta och parameterstyrda olika konfigurations-och distributions inställningar för komponent tjänsterna.

Se [Kom igång med Reliable Actors](service-fabric-reliable-actors-get-started.md) och [kom igång med Reliable Services](service-fabric-reliable-services-quick-start.md) till exempel.

## <a name="deploy"></a>Distribuera
1. En *program administratör* försvanrar program typen till ett särskilt program som ska distribueras till ett Service Fabric-kluster genom att ange lämpliga parametrar för **ApplicationType** -elementet i applikations manifestet.
2. En *operatör* överför programpaketet till kluster avbildnings arkivet med hjälp av [metoden **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [cmdleten **copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). Programpaketet innehåller applikations manifestet och samlingen av tjänst paket. Service Fabric distribuerar program från programpaketet som lagras i avbildnings arkivet, som kan vara ett Azure-Blob-arkiv eller Service Fabric system tjänsten.
3. *Operatören* etablerar sedan program typen i mål klustret från det överförda programpaketet med hjälp av [metoden **ProvisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [cmdleten **register-ServiceFabricApplicationType**](/powershell/module/servicefabric/register-servicefabricapplicationtype)eller [åtgärden **tillhandahåll ett program** rest](/rest/api/servicefabric/provision-an-application).
4. Efter etableringen av programmet startar en *operatör* programmet med de parametrar som tillhandahålls av *program administratören* med hjälp av [metoden **CreateApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [cmdleten **New-ServiceFabricApplication**](/powershell/module/servicefabric/new-servicefabricapplication)eller [åtgärden **skapa program** rest](/rest/api/servicefabric/create-an-application).
5. När programmet har distribuerats använder en *operatör* [ **CreateServiceAsync** -metoden](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [cmdleten **New-ServiceFabricService**](/powershell/module/servicefabric/new-servicefabricservice)eller [åtgärden **skapa tjänst** rest](/rest/api/servicefabric/create-a-service) för att skapa nya tjänst instanser för programmet baserat på tillgängliga tjänst typer.
6. Programmet körs nu i Service Fabric klustret.

Mer information finns i [distribuera ett program](service-fabric-deploy-remove-applications.md) till exempel.

## <a name="test"></a>Testa
1. När du har distribuerat till det lokala utvecklings klustret eller ett test kluster kör en *tjänste utvecklare* det inbyggda test scenariot för redundansväxling med hjälp av [**FailoverTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) -och [**FailoverTestScenario**](/dotnet/api/system.fabric.testability.scenario.failovertestscenario) -klasserna eller [cmdleten **Invoke-ServiceFabricFailoverTestScenario**](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario). Test scenariot för redundans kör en angiven tjänst via viktiga över gångar och redundans för att säkerställa att den fortfarande är tillgänglig och fungerar.
2. *Tjänste utvecklaren* kör sedan det inbyggda test scenariot för kaos med hjälp [**av ChaosTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) -och [**ChaosTestScenario**](/dotnet/api/system.fabric.testability.scenario.chaostestscenario) -klasserna eller [cmdleten **Invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario). Test scenariot för kaos inducerar slumpmässigt flera noder, kod paket och replik fel i klustret.
3. *Tjänste utvecklaren* [testar tjänst-till-tjänst-kommunikation](service-fabric-testability-scenarios-service-communication.md) genom att redigera test scenarier som flyttar primära repliker runt klustret.

Mer information finns i [Introduktion till fel analys tjänsten](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Uppgradera
1. En *service utvecklare* uppdaterar komponent tjänsterna för det instansierade programmet och/eller åtgärdar buggar och innehåller en ny version av tjänst manifestet.
2. En *programutvecklare* åsidosätter och parameterizes konfigurations-och distributions inställningarna för de konsekventa tjänsterna och ger en ny version av applikations manifestet. Programutvecklaren införlivar sedan de nya versionerna av tjänst manifesten i programmet och tillhandahåller en ny version av program typen i ett uppdaterat programpaket.
3. En *program administratör* införlivar den nya versionen av program typen i mål programmet genom att uppdatera lämpliga parametrar.
4. En *operatör* överför det uppdaterade programpaketet till kluster avbildnings lagret med hjälp av [metoden **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [cmdleten **copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). Programpaketet innehåller applikations manifestet och samlingen av tjänst paket.
5. En *operatör* etablerar den nya versionen av programmet i mål klustret med hjälp av metoden [ **ProvisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [cmdleten **register-ServiceFabricApplicationType**](/powershell/module/servicefabric/register-servicefabricapplicationtype)eller [åtgärden **tillhandahåll ett program** rest](/rest/api/servicefabric/provision-an-application).
6. En *operatör* uppgraderar mål programmet till den nya versionen med hjälp av [metoden **UpgradeApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [cmdleten **Start-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/start-servicefabricapplicationupgrade)eller [ **uppgraderar en Application** rest-åtgärd](/rest/api/servicefabric/upgrade-an-application).
7. En *operatör* kontrollerar förloppet för uppgraderingen med [ **GetApplicationUpgradeProgressAsync** -metoden](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Get-ServiceFabricApplicationUpgrade** -cmdlet](/powershell/module/servicefabric/get-servicefabricapplicationupgrade): en eller [åtgärden **Hämta program uppgraderings förlopp** rest](/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Om det behövs ändrar *operatören* och återanvänder parametrarna för den aktuella program uppgraderingen med hjälp av [ **UpdateApplicationUpgradeAsync** -metoden](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [cmdleten **Update-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/update-servicefabricapplicationupgrade)eller [ **uppdaterings åtgärden uppdatera program uppgradering**](/rest/api/servicefabric/update-an-application-upgrade).
9. Vid behov återställer *operatorn* den aktuella program uppgraderingen med hjälp av metoden [ **RollbackApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [cmdleten **Start-ServiceFabricApplicationRollback**](/powershell/module/servicefabric/start-servicefabricapplicationrollback)eller [ **återställnings** åtgärden för att återställa program](/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric uppgraderar mål programmet som körs i klustret utan att förlora tillgängligheten för någon av dess komponent tjänster.

I [själv studie kursen om program uppgradering](service-fabric-application-upgrade-tutorial.md) finns exempel.

## <a name="maintain"></a>Underhåll
1. För uppgraderingar och uppdateringar av operativ system Service Fabric gränssnitt med Azure-infrastrukturen för att garantera tillgängligheten för alla program som körs i klustret.
2. För uppgraderingar och korrigeringar till Service Fabrics plattformen Service Fabric uppgraderingar utan att förlora tillgängligheten för något av de program som körs i klustret.
3. En *program administratör* godkänner tillägg eller borttagning av noder från ett kluster efter analys av historiska kapacitets användnings data och prognostiserad framtida efter frågan.
4. En *operator* lägger till och tar bort noder som anges av *program administratören*.
5. När nya noder läggs till i eller befintliga noder tas bort från klustret, kan Service Fabric automatiskt belastningsutjämna de program som körs på alla noder i klustret för att uppnå optimala prestanda.

## <a name="remove"></a>Ta bort
1. En *operatör* kan ta bort en specifik instans av en tjänst som körs i klustret utan att ta bort hela programmet med [ **DeleteServiceAsync** -metoden](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [cmdleten **Remove-ServiceFabricService**](/powershell/module/servicefabric/remove-servicefabricservice)eller [åtgärden **ta bort tjänst** rest](/rest/api/servicefabric/delete-a-service).  
2. En *operatör* kan också ta bort en program instans och alla dess tjänster med hjälp av [metoden **DeleteApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [cmdleten **Remove-ServiceFabricApplication**](/powershell/module/servicefabric/remove-servicefabricapplication)eller [rest-åtgärden **ta bort program**](/rest/api/servicefabric/delete-an-application).
3. När programmet och tjänsterna har stoppats kan *operatören* avetablera program typen med hjälp av metoden [ **UnprovisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **unregister-ServiceFabricApplicationType-** cmdleten](/powershell/module/servicefabric/unregister-servicefabricapplicationtype)eller [ **avetablera en program** rest-åtgärd](/rest/api/servicefabric/unprovision-an-application). Avetablering av program typen tar inte bort programpaketet från avbildnings arkiv. Du måste ta bort programpaketet manuellt.
4. En *operator* tar bort programpaketet från avbildnings Arkiv med hjälp av [ **RemoveApplicationPackage** -metoden](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [cmdleten **Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage).

Mer information finns i [distribuera ett program](service-fabric-deploy-remove-applications.md) till exempel.

## <a name="next-steps"></a>Nästa steg
Mer information om att utveckla, testa och hantera Service Fabric program och tjänster finns i:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)
* [Programuppgradering](service-fabric-application-upgrade.md)
* [Översikt över testare](service-fabric-testability-overview.md)
