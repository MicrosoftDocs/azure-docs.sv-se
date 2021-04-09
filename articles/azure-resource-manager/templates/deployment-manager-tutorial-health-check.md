---
title: Använd hälso kontroll för Azure Deployment Manager
description: Använd hälso kontroll för att på ett säkert sätt Distribuera Azure-resurser med Azure Deployment Manager.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 12d246a493ff9ee9e20868da32d633d51939e66c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626634"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Självstudie: använda hälso kontroll i Azure Deployment Manager (offentlig för hands version)

Lär dig hur du integrerar hälso kontroll i [Azure Deployment Manager](./deployment-manager-overview.md). Den här självstudien baseras på själv studie kursen [använda Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md) . Du måste slutföra den här kursen innan du fortsätter med den här.

I distributions mal len som används i [använda Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md)använde du ett wait-steg. I den här självstudien ersätter du steget vänta med en hälso kontroll.

> [!IMPORTANT]
> Om din prenumeration har marker ATS för Kanarie för att testa nya Azure-funktioner kan du bara använda Azure Deployment Manager för att distribuera till Kanarie regionerna.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en tjänst Simulator för hälso kontroll
> * Ändra distributions mal len
> * Distribuera topologin
> * Distribuera distributionen med felaktig status
> * Verifiera distribution av distributionen
> * Distribuera distributionen med felfri status
> * Verifiera distribution av distributionen
> * Rensa resurser

Ytterligare resurser:

* [Referens för Azure Deployment Manager REST API](/rest/api/deploymentmanager/).
* [Ett exempel på en Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* Slutför [Använd Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Installera artefakterna

Om du inte redan har hämtat exemplen som används i den nödvändiga självstudien kan du ladda ned [mallarna och artefakterna](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) och zippa upp den lokalt. Kör sedan PowerShell-skriptet från avsnittet krav vägledning [Förbered artefakterna](./deployment-manager-tutorial.md#prepare-the-artifacts). Skriptet skapar en resurs grupp, skapar en lagrings behållare, skapar en BLOB-behållare, laddar ned de hämtade filerna och skapar sedan en SAS-token.

* Gör en kopia av URL: en med SAS-token. URL-adressen krävs för att fylla i ett fält i de två parametervärdena: Topology Parameters File och distribution Parameters File.
* Öppna _CreateADMServiceTopology.Parameters.jspå_ och uppdatera värdena för `projectName` och `artifactSourceSASLocation` .
* Öppna _CreateADMRollout.Parameters.jspå_ och uppdatera värdena för `projectName` och `artifactSourceSASLocation` .

## <a name="create-a-health-check-service-simulator"></a>Skapa en tjänst Simulator för hälso kontroll

I produktion använder du vanligt vis en eller flera övervaknings leverantörer. För att kunna göra hälso integration så enkel som möjligt har Microsoft arbetat med några av de främsta hälso övervaknings företagen för tjänsten för att tillhandahålla en enkel kopierings-och Inklistrings lösning för att integrera hälso kontroller med dina distributioner. En lista över dessa företag finns i [hälso övervaknings leverantörer](./deployment-manager-health-check.md#health-monitoring-providers). I den här självstudien skapar du en [Azure-funktion](../../azure-functions/index.yml) för att simulera en tjänst för hälso övervakning. Den här funktionen tar en status kod och returnerar samma kod. Din Azure Deployment Manager-mall använder status koden för att avgöra hur du ska fortsätta med distributionen.

Följande två filer används för att distribuera Azure-funktionen. Du behöver inte hämta de här filerna för att gå igenom självstudien.

* En Resource Manager-mall som finns på [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json) . Du distribuerar den här mallen för att skapa en Azure-funktion.
* En zip-fil med käll koden för Azure Function [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip) . Detta zip-namn anropas av Resource Manager-mallen.

Om du vill distribuera Azure-funktionen väljer du **prova** att öppna Azure Cloud Shell och klistrar in följande skript i Shell-fönstret. Om du vill klistra in koden högerklickar du på Shell-fönstret och väljer **Klistra in**.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Verifiera och testa Azure-funktionen:

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Öppna resurs gruppen. Standard namnet är projekt namnet med **RG** tillagt.
1. Välj app service från resurs gruppen. Standard namnet på App Service är projekt namnet med **webapp** APPEND.
1. Expandera **funktioner** och välj sedan **HttpTrigger1**.

    ![Azure Deployment Manager hälso kontroll Azure-funktion](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Välj **&lt; /> Hämta funktions webb adress**.
1. Välj **Kopiera** för att kopiera webb adressen till Urklipp. URL: en liknar:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Ersätt `{healthStatus}` i URL: en med en status kod. I den här självstudien använder du fel för att testa fel scenariot *och använda antingen* *felfri* eller *Varning* för att testa det felfria scenariot. Skapa två URL: er, en med *felaktig* status och den andra med *felfri* status. Exempel:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Du behöver båda URL: erna för att slutföra den här självstudien.

1. Om du vill testa hälso övervaknings simulatorn öppnar du de URL: er som du skapade i föregående steg. Resultatet av statusen för felaktig status ser ut ungefär så här:

    ```Output
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Ändra distributions mal len

Syftet med det här avsnittet är att visa hur du inkluderar ett hälso kontroll steg i distributions mal len.

1. Öppna _CreateADMRollout.js_ som du skapade i [använda Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md). Den här JSON-filen ingår i nedladdningen.  Se [krav](#prerequisites).
1. Lägg till två fler parametrar:

    ```json
    "healthCheckUrl": {
      "type": "string",
      "metadata": {
        "description": "Specifies the health check URL."
      }
    },
    "healthCheckAuthAPIKey": {
      "type": "string",
      "metadata": {
          "description": "Specifies the health check Azure Function function authorization key."
      }
    }
    ```

1. Ersätt resurs definitionen för väntande steg med en hälso kontroll av resurs definitionen för ett hälso steg:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    Baserat på definitionen fortsätter distributionen om hälso tillståndet är antingen *felfri* eller *Varning*.

1. Uppdatera `dependsOn` installations definitionen för att inkludera det nyligen definierade hälso kontrollen:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Uppdatera `stepGroups` för att inkludera hälso kontroll steget. `healthCheckStep`Kallas för `postDeploymentSteps` `stepGroup2` . `stepGroup3` och `stepGroup4` distribueras bara om statusen felfri är antingen *felfri* eller *Varning*.

    ```json
    "stepGroups": [
      {
        "name": "stepGroup1",
          "preDeploymentSteps": [],
          "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
          "postDeploymentSteps": []
        },
        {
          "name": "stepGroup2",
          "dependsOnStepGroups": ["stepGroup1"],
          "preDeploymentSteps": [],
          "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
          "postDeploymentSteps": [
            {
              "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
            }
          ]
        },
        {
          "name": "stepGroup3",
          "dependsOnStepGroups": ["stepGroup2"],
          "preDeploymentSteps": [],
          "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
           "postDeploymentSteps": []
        },
        {
          "name": "stepGroup4",
          "dependsOnStepGroups": ["stepGroup3"],
          "preDeploymentSteps": [],
          "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
          "postDeploymentSteps": []
        }
    ]
    ```

    Om du jämför `stepGroup3` avsnittet innan och efter det har ändrats, är det här avsnittet nu beroende av `stepGroup2` . Detta är nödvändigt när `stepGroup3` och de efterföljande steg grupperna beror på resultatet av hälso övervakningen.

    Följande skärm bild illustrerar de ändrade områdena och hur hälso kontroll steget används:

    ![Mall för hälso kontroll av Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Distribuera topologin

Kör följande PowerShell-skript för att distribuera topologin. Du behöver samma _CreateADMServiceTopology.jspå_ och _CreateADMServiceTopology.Parameters.js_ som du använde i [använda Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Kontrollera att tjänsttopologin och de angivna resurserna har skapats på Azure-portalen:

![Azure Deployment Manager-självstudie om resurser för en distribuerad tjänsttopologi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

**Visa dolda typer** måste väljas för att resurserna ska visas.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Distribuera distributionen med statusen ohälsosamt

Använd den felaktiga status-URL som du skapade i [skapa en tjänst Simulator för hälso kontroll](#create-a-health-check-service-simulator). Du behöver den ändrade _CreateADMServiceTopology.jspå_ och samma _CreateADMServiceTopology.Parameters.js_ som du använde i [använda Azure Deployment Manager med Resource Manager-mallar](./deployment-manager-tutorial.md).

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` är ett asynkront anrop. Meddelandet lyckades innebär att distributionen har startats. Om du vill kontrol lera distributionen använder du `Get-AZDeploymentManagerRollout` .  Se nästa procedur.

Använd följande PowerShell-skript för att kontrol lera installations förloppet:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

Följande exempel på utdata visar att distributionen misslyckades på grund av en felaktig status:

```Output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

När distributionen är klar visas en ytterligare resurs grupp som skapats för västra USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Distribuera distributionen med felfri status

Upprepa det här avsnittet om du vill distribuera om distributionen med URL: en för felfri status. När distributionen är klar visas en mer resurs grupp som skapats för USA, östra.

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Bläddra till de nya webb programmen under de nya resurs grupper som skapats av distributions distributionen.
1. Öppna webbprogrammet i en webbläsare. Kontrol lera platsen och versionen i _index.html_ -filen.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
1. Använd fältet **Filtrera efter namn** för att visa resursgrupperna som skapats i den här självstudien.

    * **&lt; projectName>RG**: innehåller Deployment Manager-resurser.
    * **&lt; ProjectName>ServiceWUSrg**: innehåller de resurser som definierats av ServiceWUS.
    * **&lt; ProjectName>ServiceEUSrg**: innehåller de resurser som definierats av ServiceEUS.
    * Resursgruppen för den användardefinierade hanterade identiteten.
1. Välj resursgruppens namn.
1. Välj **ta bort resurs grupp** på den översta menyn.
1. Upprepa de två sista stegen för att ta bort andra resursgrupper som skapats av den här självstudien.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder funktionen för hälso kontroll i Azure Deployment Manager. Mer information finns i [dokumentationen om Azure Resource Manager](../index.yml).
