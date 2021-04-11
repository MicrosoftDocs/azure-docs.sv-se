---
title: CI/CD för Azure våren Cloud
description: CI/CD för Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 331ef39facb9f7cf8f069f2a238be325f53de2d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878549"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD för Azure våren Cloud

Med kontinuerlig integrering och kontinuerliga leverans verktyg kan du snabbt distribuera uppdateringar till befintliga program med minimal ansträngning och risk. Azure DevOps hjälper dig att organisera och kontrol lera dessa viktiga jobb. För närvarande erbjuder Azure våren Cloud inget angivet Azure DevOps-plugin-program.  Du kan dock integrera dina våren Cloud-program med DevOps med hjälp av en [Azure CLI-uppgift](/azure/devops/pipelines/tasks/deploy/azure-cli).

Den här artikeln visar hur du använder en Azure CLI-aktivitet med Azure våren Cloud för att integrera med Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Skapa en Azure Resource Manager tjänst anslutning

Läs [den här artikeln](/azure/devops/pipelines/library/connect-to-azure) för att lära dig hur du skapar en Azure Resource Manager tjänst anslutning till ditt Azure DevOps-projekt. Se till att välja samma prenumeration som du använder för din Azure våren Cloud Service-instans.

## <a name="azure-cli-task-templates"></a>Mallar för Azure CLI-aktiviteter
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Distribuera artefakter

Du kan bygga och distribuera dina projekt med en serie `tasks` . Det här kodfragmentet definierar variabler, en .NET Core-uppgift för att bygga programmet och en Azure CLI-aktivitet för att distribuera *. zip* -filen.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --assign-endpoint
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Distribuera artefakter

Du kan bygga och distribuera dina projekt med en serie `tasks` . Det här kodfragmentet definierar först en maven-uppgift för att bygga programmet, följt av en andra aktivitet som distribuerar JAR-filen med Azures moln för Azure CLI-tillägget.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Distribuera från källa

Det går att distribuera direkt till Azure utan ett separat build-steg.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>Nästa steg

* [Snabb start: Distribuera ditt första Azure våren Cloud-program](spring-cloud-quickstart.md)