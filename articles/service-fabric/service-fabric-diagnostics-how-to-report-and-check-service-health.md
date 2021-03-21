---
title: Rapportera och kontrol lera hälsa med Azure Service Fabric
description: Lär dig hur du skickar hälso rapporter från Service koden och hur du kontrollerar hälso tillståndet för din tjänst genom att använda de hälso övervaknings verktyg som Azure Service Fabric tillhandahåller.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 59c8202b03bf1be2be5a68b75a1d7c7404b2213d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998251"
---
# <a name="report-and-check-service-health"></a>Rapportera och kontrollera hälsan hos tjänster
När dina tjänster drabbas av problem kan din möjlighet att svara på och åtgärda incidenter och avbrott bero på din möjlighet att snabbt upptäcka problemen. Om du rapporterar problem och fel i Azure Service Fabric Health Manager från Service koden kan du använda standard verktyg för hälso övervakning som Service Fabric visar för att kontrol lera hälso statusen.

Det finns tre sätt som du kan rapportera hälsan från tjänsten:

* Använd [partitioner](/dotnet/api/system.fabric.istatefulservicepartition) eller [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) -objekt.  
  Du kan använda- `Partition` och- `CodePackageActivationContext` objekten för att rapportera hälso tillståndet för element som ingår i den aktuella kontexten. Kod som körs som en del av en replik kan till exempel endast rapportera hälso tillståndet på den repliken, den partition som den tillhör och det program som den är en del av.
* Använd `FabricClient`.   
  Du kan använda `FabricClient` för att rapportera hälso tillstånd från Service koden om klustret inte är [säkert](service-fabric-cluster-security.md) eller om tjänsten körs med administratörs behörighet. De flesta verkliga scenarier använder inte oskyddade kluster eller ger administratörs behörighet. Med `FabricClient` kan du rapportera hälsa för alla entiteter som ingår i klustret. Vi rekommenderar dock bara service code att skicka rapporter som är relaterade till sitt eget hälso tillstånd.
* Använd REST-API: erna i klustret, programmet, det distribuerade programmet, tjänsten, tjänst paketet, partitionen, repliken eller nodens nivåer. Detta kan användas för att rapportera hälsa i en behållare.

Den här artikeln vägleder dig genom ett exempel som rapporterar hälso tillstånd från Service koden. Exemplet visar också hur de verktyg som tillhandahålls av Service Fabric kan användas för att kontrol lera hälso statusen. Den här artikeln är avsedd att vara en snabb introduktion till hälso övervaknings funktionerna i Service Fabric. Mer detaljerad information finns i serien med djupgående artiklar om hälsa som börjar med länken i slutet av den här artikeln.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande installerat:

* Visual Studio 2015 eller Visual Studio 2019
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Så här skapar du ett lokalt säkert dev-kluster
* Öppna PowerShell med administratörs behörighet och kör följande kommandon:

![Kommandon som visar hur du skapar ett säkert dev-kluster](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Så här distribuerar du ett program och kontrollerar dess hälsa
1. Öppna Visual Studio som administratör.
1. Skapa ett projekt med hjälp av mallen för **tillstånds känsliga tjänster** .
   
    ![Skapa ett Service Fabric program med tillstånds känslig tjänst](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Tryck på **F5** för att köra programmet i fel söknings läge. Programmet distribueras till det lokala klustret.
1. När programmet har körts högerklickar du på ikonen lokal kluster hanterare i meddelande fältet och väljer **Hantera lokalt kluster** på snabb menyn för att öppna Service Fabric Explorer.
   
    ![Öppna Service Fabric Explorer från meddelande fältet](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Program hälsan bör visas som i den här avbildningen. För närvarande bör programmet vara felfritt utan fel.
   
    ![Felfritt program i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Du kan också kontrol lera hälsan med hjälp av PowerShell. Du kan använda ```Get-ServiceFabricApplicationHealth``` för att kontrol lera hälso tillståndet för ett program och du kan använda ```Get-ServiceFabricServiceHealth``` för att kontrol lera tjänstens hälsa. Hälso rapporten för samma program i PowerShell finns i den här avbildningen.
   
    ![Felfritt program i PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Lägga till anpassade hälso händelser i Service koden
Service Fabric-projektmallar i Visual Studio innehåller exempel kod. Följande steg visar hur du kan rapportera anpassade hälso händelser från Service koden. Sådana rapporter visas automatiskt i standard verktyg för övervakning av hälso tillstånd som Service Fabric tillhandahåller, till exempel Service Fabric Explorer, Azure Portal Health View och PowerShell.

1. Öppna programmet som du skapade tidigare i Visual Studio, eller skapa ett nytt program med hjälp av Visual Studio **-mallen tillstånds känslig tjänst** .
1. Öppna filen Stateful1. CS och hitta `myDictionary.TryGetValueAsync` anropet i- `RunAsync` metoden. Du kan se att den här metoden returnerar en `result` som innehåller det aktuella värdet för räknaren eftersom nyckel logiken i det här programmet är att köra ett antal som körs. Om det här programmet var ett verkligt program, och om bristen på ett haveri resultat, skulle du vilja flagga den händelsen.
1. Om du vill rapportera en hälso händelse när bristen på resultat representerar ett fel, lägger du till följande steg.
   
    a. Lägg till `System.Fabric.Health` namn området i filen Stateful1. cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Lägg till följande kod efter `myDictionary.TryGetValueAsync` anropet
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Vi rapporterar replik hälsan eftersom det rapporteras från en tillstånds känslig tjänst. `HealthInformation`Parametern lagrar information om det hälso problem som rapporteras.
   
    Om du har skapat en tillstånds lös tjänst använder du följande kod
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Om tjänsten körs med administratörs behörighet eller om klustret inte är [säkert](service-fabric-cluster-security.md), kan du även använda `FabricClient` för att rapportera hälsan som visas i följande steg.  
   
    a. Skapa `FabricClient` instansen efter `var myDictionary` deklarationen.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Lägg till följande kod efter `myDictionary.TryGetValueAsync` anropet.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Låt oss simulera felet och se det visas i hälso övervaknings verktygen. För att simulera felen kommenterar du den första raden i hälso rapporterings koden som du lade till tidigare. När du har kommenterat ut den första raden kommer koden att se ut som i följande exempel.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Den här koden utlöses när hälso rapporten `RunAsync` körs varje tid. När du har gjort ändringen trycker du på **F5** för att köra programmet.
1. När programmet har körts öppnar du Service Fabric Explorer för att kontrol lera hälso tillståndet för programmet. Den här gången visar Service Fabric Explorer att programmet inte är felfritt. Programmet visas som ohälsosamt eftersom felet som rapporterades från den kod som vi lade till tidigare.
   
    ![Skadat program i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Om du väljer den primära repliken i trädvyn för Service Fabric Explorer, ser du att **hälso tillståndet** indikerar ett fel. Service Fabric Explorer visar också hälso rapport information som har lagts till i `HealthInformation` parametern i koden. Du kan se samma hälso rapporter i PowerShell och Azure Portal.
   
    ![Replik hälsa i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Den här rapporten finns kvar i Health Manager tills den ersätts av en annan rapport eller tills repliken har tagits bort. Eftersom vi inte angav `TimeToLive` för den här hälso rapporten i `HealthInformation` objektet upphör rapporten aldrig att gälla.

Vi rekommenderar att hälsa rapporteras på den mest detaljerade nivån, som i det här fallet är repliken. Du kan också rapportera hälsa på `Partition` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Om du vill rapportera hälsa på `Application` , `DeployedApplication` , och `DeployedServicePackage` , använder du  `CodePackageActivationContext` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Nästa steg
* [Djupgående Service Fabric hälsa](service-fabric-health-introduction.md)
* [REST API för repor ting service Health](/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API för rapportering av program hälsa](/rest/api/servicefabric/report-the-health-of-an-application)
