---
title: Aktivera Azure Monitor på Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du aktiverar Azure Monitor på en Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 199ec8e2f1e8eb74d971286a4fc6180eb8b72f2a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595968"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Aktivera Azure Monitor på din Azure Stack Edge Pro GPU-enhet

Övervaknings behållare på din Azure Stack Edge Pro GPU-enhet är kritisk, särskilt när du kör flera beräknings program. Med Azure Monitor kan du samla in behållar loggar och minnes-och processor mått från det Kubernetes-kluster som körs på enheten.

I den här artikeln beskrivs de steg som krävs för att aktivera Azure Monitor på enheten och samla in behållar loggar i Log Analytics arbets yta. Azure Monitor Metrics Store stöds för närvarande inte med din Azure Stack Edge Pro GPU-enhet. 


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du:

- En Azure Stack Edge Pro-enhet. Kontrol lera att enheten har Aktiver ATS enligt stegen i [Självstudier: Aktivera enheten](azure-stack-edge-gpu-deploy-activate.md).
- Du har slutfört **Konfigurera beräknings** steget enligt [självstudien: Konfigurera compute på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-configure-compute.md) på enheten. Enheten bör ha en tillhör ande IoT Hub resurs, en IoT-enhet och en IoT Edge enhet.


## <a name="create-log-analytics-workspace"></a>Skapa Log Analytics-arbetsyta

Utför följande steg för att skapa en Log Analytics-arbetsyta. En Log Analytics-arbetsyta är en logisk lagrings enhet där loggdata samlas in och lagras.

1. I Azure Portal väljer du **+ skapa en resurs** och söker efter **Log Analytics arbets yta** och väljer sedan **skapa**. 
1. Konfigurera följande inställningar på **arbets ytan skapa Log Analytics**. Acceptera resten som standard.

    1. På fliken **grundläggande** anger du prenumeration, resurs grupp, namn och region för arbets ytan. 

        ![Fliken grundläggande för Log Analytics arbets yta](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. På fliken **pris nivå** accepterar du standard planen för **betala per** användning.

        ![Fliken prissättning för Log Analytics arbets yta](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. På fliken **Granska + skapa** granskar du informationen för din arbets yta och väljer **skapa**.

        ![Granska + skapa för Log Analytics arbets yta](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

Mer information finns i de detaljerade stegen i [skapa en Log Analytics arbets yta via Azure Portal](../azure-monitor/logs/quick-create-workspace.md).



## <a name="enable-container-insights"></a>Aktivera container Insights

Utför följande steg för att aktivera behållar insikter på din arbets yta. 

1. Följ de detaljerade stegen i [så här lägger du till Azure Monitor containers-lösningen](../azure-monitor/containers/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution). Använd följande mallfil `containerSolution.json` :

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. Hämta resurs-ID och plats. Gå till `Your Log Analytics workspace > General > Properties`. Kopiera följande information:

    - **resurs-ID**, vilket är det fullständigt kvalificerade Azure-resurs-ID: t för Azure Log Analytics-arbetsytan. 
    - **plats**, som är Azure-regionen.

    ![Egenskaper för Log Analytics arbets yta](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Använd följande parameter fil `containerSolutionParams.json` . Ersätt `workspaceResourceId` med resurs-ID: t och `workspaceRegion` platsen som du kopierade i föregående steg.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Här är ett exempel på utdata från en Log Analytics arbets yta med behållar insikter aktiverade:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Konfigurera Azure Monitor på din enhet

1. Gå till den nyskapade Log Analytics resursen och kopiera **arbetsyte-ID: t** och **primär nyckeln** (arbets ytans nyckel).

    ![Hantering av agenter i Log Analytics-arbetsyta](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Spara informationen som du kommer att använda i ett senare steg.

1. [Anslut till enhetens PowerShell-gränssnitt](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. Använd Log Analytics-arbetsytans ID och arbets ytans nyckel med följande cmdlet:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. När Azure Monitor har Aktiver ATS bör du se loggar i arbets ytan Log Analytics. Om du vill visa status för det Kubernetes-kluster som har distribuerats på enheten går du till **Azure Monitor > insights > behållare**. För miljö alternativet väljer du **alla**. 

    ![Mått i Log Analytics-arbetsyta](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [övervakar Kubernetes-arbetsbelastningar via Kubernetes-instrumentpanelen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- Lär dig [Hantera aviseringar om enhets händelser](azure-stack-edge-gpu-manage-device-event-alert-notifications.md). 