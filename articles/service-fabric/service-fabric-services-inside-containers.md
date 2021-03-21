---
title: Använd Azure Service Fabric Services i Windows
description: Lär dig hur du Använd Service Fabric Reliable Services och Reliable Actors tjänster i Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e0c5f3ecf0334ad2190d8542e54388a2a25d30fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999815"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Containeranpassa Service Fabric Reliable Services och Reliable Actors i Windows

Service Fabric stöder Container Service Fabric mikrotjänster (Reliable Services och pålitliga aktörbaserade tjänster). Mer information finns i [Service Fabric-behållare](service-fabric-containers-overview.md).

Det här dokumentet innehåller rikt linjer för hur du får din tjänst att köras i en Windows-behållare.

> [!NOTE]
> För närvarande fungerar den här funktionen bara för Windows. För att köra behållare måste klustret köras på Windows Server 2016 med behållare.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Steg för att Använd ditt Service Fabric program

1. Öppna ditt Service Fabric-program i Visual Studio.

2. Lägg till klass [SFBinaryLoader. cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) i projektet. Koden i den här klassen är en hjälp program att läsa in Service Fabric körbara binärfiler i programmet när de körs i en behållare.

3. Initiera inläsaren vid program start punkten för varje kod paket som du vill Använd. Lägg till den statiska konstruktorn som visas i följande kodfragment till din program start punkt fil.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Bygg och [Paketera](service-fabric-package-apps.md#Package-App) ditt projekt. Om du vill skapa och skapa ett paket högerklickar du på programprojektet i Solution Explorer och väljer **paket** kommandot.

5. För varje kod paket du behöver Använd kör du PowerShell-skriptet [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Användningen är följande:

    Fullständigt .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Skriptet skapar en mapp med Docker-artefakter på $dockerPackageOutputDirectoryPath. Ändra de genererade Dockerfile till `expose` alla portar, kör installations skript och så vidare. utifrån dina behov.

6. Sedan måste du [bygga](service-fabric-get-started-containers.md#Build-Containers) och [pusha](service-fabric-get-started-containers.md#Push-Containers) ditt Docker container-paket till din lagrings plats.

7. Ändra ApplicationManifest.xml och ServiceManifest.xml för att lägga till behållar avbildning, lagrings information, Registerscanner och mappning från Port till värd. För att ändra manifesten, se [skapa ett program för Azure Service Fabric container](service-fabric-get-started-containers.md). Kod paket definitionen i tjänst manifestet måste ersättas med motsvarande behållar avbildning. Se till att ändra EntryPoint till en ContainerHost-typ.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Lägg till mappningen från Port till värd för din replikerare och tjänst slut punkt. Eftersom båda dessa portar tilldelas vid körning av Service Fabric har ContainerPort angetts till noll för att använda den tilldelade porten för mappning.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Information om hur du konfigurerar isolerings läget för behållare finns i [Konfigurera isolerings läge]( ./service-fabric-get-started-containers.md#configure-isolation-mode). Windows stöder två isoleringslägen för containrar: process och Hyper-V. Följande kodfragment visar hur isolerings läget anges i program manifest filen.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Som standard har Service Fabric-program åtkomst till Service Fabric runtime, i form av en slut punkt som accepterar programspecifika begär Anden. Överväg att inaktivera den här åtkomsten när programmet är värd för ej betrodd kod. Mer information finns [i rekommenderade säkerhets metoder i Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Om du vill inaktivera åtkomst till Service Fabric runtime lägger du till följande inställning i avsnittet principer i program manifestet som motsvarar det importerade tjänst manifestet, enligt följande:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Om du vill testa det här programmet måste du distribuera det till ett kluster som kör version 5,7 eller senare. För körnings versioner 6,1 eller lägre måste du redigera och uppdatera kluster inställningarna för att aktivera den här förhands gransknings funktionen. Följ stegen i den här [artikeln](service-fabric-cluster-fabric-settings.md) för att lägga till inställningen som visas nästa.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. [Distribuera](service-fabric-deploy-remove-applications.md) sedan det redigerade programpaketet till det här klustret.

Nu bör du ha ett behållar Service Fabric program som kör ditt kluster.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du kör [containrar i Service Fabric](service-fabric-get-started-containers.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).