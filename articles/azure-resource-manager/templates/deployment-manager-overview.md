---
title: Säker distribution över regioner – Azure Deployment Manager
description: Lär dig hur du distribuerar en tjänst över flera regioner med Azure Deployment Manager och om säker distributions praxis.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 63553b0bbca031faa44e0d88480fcc08950a3e2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627507"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Aktivera distributions metoder för säker distribution med Azure Deployment Manager (offentlig för hands version)

Om du vill distribuera tjänsten över flera regioner och se till att den körs som förväntat i varje region kan du använda Azure Deployment Manager för att samordna en mellanlagrad distribution av tjänsten. Precis som för alla Azure-distributioner definierar du resurserna för din tjänst i [Resource Manager-mallar](template-syntax.md). När du har skapat mallarna använder du Deployment Manager för att beskriva topologin för tjänsten och hur den ska distribueras.

Deployment Manager är en funktion i Resource Manager. Den utökar dina funktioner under distributionen. Använd Deployment Manager när du har en komplex tjänst som måste distribueras till flera regioner. Genom att mellanlagra distribution av tjänsten kan du upptäcka potentiella problem innan den har distribuerats till alla regioner. Om du inte behöver de extra försiktighets åtgärderna för en mellanlagrad distribution använder du standard [distributions alternativen](deploy-portal.md) för Resource Manager. Deployment Manager integreras sömlöst med alla befintliga verktyg från tredje part som stöder Resource Manager-distributioner, t. ex. kontinuerlig integrering och kontinuerlig leverans (CI/CD).

Azure Deployment Manager är i för hands version. Hjälp oss att förbättra funktionen genom att ge [feedback](https://aka.ms/admfeedback).

Om du vill använda Deployment Manager måste du skapa fyra filer:

* Topology-mall.
* Distributions mal len.
* Parameter fil för topologi.
* Parameter fil för distribution.

Du distribuerar topologin innan du distribuerar distributions mal len.

Ytterligare resurser:

* [Referens för Azure Deployment Manager REST API](/rest/api/deploymentmanager/).
* [Självstudie: Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).
* [Självstudie: Använd hälso kontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
* [Exempel på Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identitet och åtkomst

Med Deployment Manager utför en [användardefinierad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) distributions åtgärderna. Du skapar den här identiteten innan du påbörjar distributionen. Den måste ha åtkomst till prenumerationen som du distribuerar tjänsten till och tillräcklig behörighet för att slutföra distributionen. Information om vilka åtgärder som beviljas via roller finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Identiteten måste finnas på samma plats som distributionen.

## <a name="topology-template"></a>Topology-mall

Topologi mal len beskriver de Azure-resurser som utgör din tjänst och var de ska distribueras. Följande bild visar topologin för en exempel tjänst:

![Hierarki från service Topology till tjänster till tjänst enheter](./media/deployment-manager-overview/service-topology.png)

Topologi mal len innehåller följande resurser:

* Artefakt källa – där dina Resource Manager-mallar och parametrar lagras.
* Service Topology – pekar på artefakt källa.
  * Tjänster – anger plats och ID för Azure-prenumeration.
    * Tjänst enheter – anger resurs grupp, distributions läge och sökväg till mall-och parametriserad filer.

För att förstå vad som händer på varje nivå är det bra att se vilka värden du anger.

![Värden för varje nivå](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Artefakt källa för mallar

I din Topology-mall skapar du en artefakt källa som innehåller filerna för mallar och parametrar. Artefakt källan är ett sätt att hämta filer för distribution. Du ser en annan artefakt källa för binärfiler senare i den här artikeln.

I följande exempel visas det allmänna formatet för artefakt källan.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
  "properties": {
    "sourceType": "AzureStorage",
    "artifactRoot": "<root-folder-for-templates>",
    "authentication": {
      "type": "SAS",
      "properties": {
        "sasUri": "<SAS-URI-for-storage-container>"
      }
    }
  }
}
```

Mer information finns i [referens för artifactSources-mallen](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Tjänsttopologi

I följande exempel visas det allmänna formatet för service Topology-resursen. Du anger resurs-ID för den artefakt källa som innehåller mallarna och parametervärdena. Tjänste sto pol Ogin inkluderar alla tjänst resurser. Kontrol lera att artefakt källan är tillgänglig eftersom tjänstens topologi är beroende av den.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Mer information finns i [referens för serviceTopologies-mallen](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Tjänster

I följande exempel visas tjänst resursens allmänna format. I varje tjänst anger du plats och ID för Azure-prenumeration som ska användas för att distribuera tjänsten. Om du vill distribuera till flera regioner definierar du en tjänst för varje region. Tjänsten är beroende av tjänst sto pol Ogin.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
  "dependsOn": [
      "<service-topology>"
  ],
  "properties": {
    "targetSubscriptionId": "<subscription-ID>",
    "targetLocation": "<location-of-deployed-service>"
  },
  "resources": [
    {
      "type": "serviceUnits",
            ...
        }
    ]
}
```

Mer information finns i [referens för Services-mall](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Tjänstenheter

I följande exempel visas det allmänna formatet för tjänst enhets resursen. I varje tjänst enhet anger du resurs gruppen, [distributions läget](deployment-modes.md) som ska användas för distribution och sökvägen till mallen och parameter filen. Om du anger en relativ sökväg för mallen och parametrarna skapas den fullständiga sökvägen från rotmappen i artefakter källan. Du kan ange en absolut sökväg för mallen och parametrarna, men du förlorar möjligheten att enkelt version av dina versioner. Tjänst enheten är beroende av tjänsten.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
  "dependsOn": [
    "<service>"
  ],
  "tags": {
    "serviceType": "Service West US Web App"
  },
  "properties": {
    "targetResourceGroup": "<resource-group-name>",
    "deploymentMode": "Incremental",
    "artifacts": {
      "templateArtifactSourceRelativePath": "<relative-path-to-template>",
      "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
    }
  }
}
```

Varje mall bör innehålla de relaterade resurser som du vill distribuera i ett enda steg. En tjänst enhet kan till exempel ha en mall som distribuerar alla resurser för tjänstens klient del.

Mer information finns i [referens för serviceUnits-mallen](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Distributions mal len

I distributions mal len beskrivs de steg som ska utföras när du distribuerar tjänsten. Du anger den topologi som ska användas och definierar ordningen för distribution av tjänst enheter. Den innehåller en artefakt källa för att lagra binärfiler för distributionen. I din distributions mall definierar du följande hierarki:

* Artefakt källa.
* Aktivitets.
* Introduktion.
  * Steg grupper.
    * Distributions åtgärder.

Följande bild visar hierarkin för distributions mal len:

![Hierarki från distribution till steg](./media/deployment-manager-overview/Rollout.png)

Varje distribution kan ha många steg grupper. Varje steg grupp har en distributions åtgärd som pekar på en tjänst enhet i tjänst sto pol Ogin.

### <a name="artifact-source-for-binaries"></a>Artefakt källa för binärfiler

I distributions mal len skapar du en artefakt källa för de binärfiler som du behöver distribuera till tjänsten. Den här artefakt källan liknar [artefakt källan för mallar](#artifact-source-for-templates), förutom att den innehåller skript, webb sidor, kompilerad kod eller andra filer som behövs för din tjänst.

### <a name="steps"></a>Steg

Du kan definiera ett steg för att utföra antingen före eller efter distributionen. För närvarande är endast `wait` steget och `healthCheck` steget tillgängligt.

`wait`Steget pausar distributionen innan du fortsätter. Du kan kontrol lera att tjänsten körs som förväntat innan du distribuerar nästa tjänst enhet. I följande exempel visas det allmänna formatet för ett `wait` steg.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
    "name": "waitStep",
    "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Egenskapen duration använder [ISO 8601-standarden](https://en.wikipedia.org/wiki/ISO_8601#Durations). I föregående exempel anges vänta en minut.

Mer information om hälso kontroller finns i [Introduktion till distribution av hälso integrering i Azure Deployment Manager](./deployment-manager-health-check.md) och [självstudie: Använd hälso kontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Mer information finns i [referens för metod mal len](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Distributioner

Kontrol lera att artefakt källan är tillgänglig eftersom distributionen är beroende av den. Distributionen definierar steg grupper för varje tjänst enhet som distribueras. Du kan definiera åtgärder som ska vidtas före eller efter distributionen. Du kan till exempel ange att distributionen ska vänta efter att tjänste enheten har distribuerats. Du kan definiera ordningen för steg grupperna.

Identity-objektet anger den [användar tilldelnings hanterade identitet](#identity-and-access) som utför distributions åtgärderna.

I följande exempel visas det allmänna formatet för distributionen.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
  "Identity": {
    "type": "userAssigned",
    "identityIds": [
      "<managed-identity-ID>"
    ]
  },
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "buildVersion": "1.0.0.0",
    "artifactSourceId": "<artifact-source-ID>",
    "targetServiceTopologyId": "<service-topology-ID>",
    "stepGroups": [
      {
        "name": "stepGroup1",
        "dependsOnStepGroups": ["<step-group-name>"],
        "preDeploymentSteps": ["<step-ID>"],
        "deploymentTargetId":
          "<service-unit-ID>",
        "postDeploymentSteps": ["<step-ID>"]
      },
            ...
        ]
    }
}
```

Mer information finns i referens för distributions [mal len](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Parameter fil

Du skapar två parameter-filer. En parameter fil används vid distribution av tjänstens topologi och den andra används för distribution av distributionen. Det finns vissa värden som du måste kontrol lera är desamma i båda parametervärdena.

## <a name="containerroot-variable"></a>containerRoot-variabel

Med versions distributioner ändras sökvägen till dina artefakter med varje ny version. Första gången du kör en distribution kan sökvägen vara `https://<base-uri-blob-container>/binaries/1.0.0.0` . Den andra gången det kan vara `https://<base-uri-blob-container>/binaries/1.0.0.1` . Deployment Manager gör det enklare att få rätt rot Sök väg för den aktuella distributionen med hjälp av `$containerRoot` variabeln. Det här värdet ändras med varje version och är inte känt före distributionen.

Använd `$containerRoot` variabeln i parameter filen för mallen som distribuerar Azure-resurserna. Vid distributions tillfället ersätts den här variabeln med de faktiska värdena från distributionen.

Under distributionen skapar du till exempel en artefakt källa för de binära artefakterna.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
  "properties": {
    "sourceType": "AzureStorage",
      "artifactRoot": "[parameters('binaryArtifactRoot')]",
      "authentication" :
        {
          "type": "SAS",
          "properties": {
            "sasUri": "[parameters('artifactSourceSASLocation')]"
          }
        }
  }
},
```

Lägg märke `artifactRoot` till `sasUri` egenskaperna och. Artefakt roten kan vara inställd på ett värde som `binaries/1.0.0.0` . SAS-URI: n är URI: n till din lagrings behållare med SAS-token för åtkomst. Deployment Manager konstruerar automatiskt värdet för `$containerRoot` variabeln. Den kombinerar dessa värden i formatet `<container>/<artifactRoot>` .

Din mall och parameter fil måste känna till rätt sökväg för att hämta versioner av binärfilerna. Om du till exempel vill distribuera filer för en webbapp skapar du följande parameter fil med `$containerRoot` variabeln. Du måste använda två omvända snedstreck ( `\\` ) för sökvägen eftersom det första är ett escape-tecken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Använd sedan den parametern i mallen:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
  ],
  "tags": {
    "displayName": "WebAppMSDeploy"
  },
  "properties": {
    "packageUri": "[parameters('deployPackageURI')]"
  }
}
```

Du hanterar versions distributioner genom att skapa nya mappar och skicka den rot Sök vägen under distributionen. Sökvägen flödar till mallen som distribuerar resurserna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Deployment Manager. Fortsätt till nästa artikel om du vill lära dig hur du distribuerar med Deployment Manager.

> [!div class="nextstepaction"]
> [Självstudie: använda Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md)
>
> [Snabb start: prova Azure Deployment Manager på bara några minuter](https://github.com/Azure-Samples/adm-quickstart)
