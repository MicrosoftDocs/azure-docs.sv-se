---
title: Aktivera Uppdateringshantering via en Azure Resource Manager-mall
description: Den här artikeln beskriver hur du använder en Azure Resource Manager för att aktivera Uppdateringshantering.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 687c3d49f98fe6832d23dc1529a9761d862e0666
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830888"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Aktivera Uppdateringshantering via en Azure Resource Manager-mall

Du kan använda en [Azure Resource Manager för](../../azure-resource-manager/templates/template-syntax.md) att aktivera Azure Automation Uppdateringshantering i resursgruppen. Den här artikeln innehåller en exempelmall som automatiserar följande:

* Automatiserar skapandet av en Azure Monitor Log Analytics-arbetsyta.
* Automatiserar skapandet av ett Azure Automation konto.
* Länkar Automation-kontot till Log Analytics-arbetsytan.
* Lägger till Exempel på Automation-runbooks till kontot.
* Aktiverar Uppdateringshantering funktion.

Mallen automatiserar inte aktiveringen av Uppdateringshantering på en eller flera virtuella Azure-datorer eller andra virtuella datorer.

Om du redan har en Log Analytics-arbetsyta och ett Automation-konto distribuerat i en region som stöds i din prenumeration är de inte länkade. Med hjälp av den här mallen skapas länken och distribueras Uppdateringshantering.

>[!NOTE]
>Det går inte att skapa Automation Kör som-kontot när du använder en ARM-mall. Information om hur du skapar ett Kör som-konto manuellt från portalen eller med PowerShell finns [i Skapa Kör som-konto.](../create-run-as-account.md)

När du har slutfört de [](../automation-manage-send-joblogs-log-analytics.md) här stegen måste du konfigurera diagnostikinställningar för ditt Automation-konto för att skicka status för Runbook-jobb och jobbströmmar till den länkade Log Analytics-arbetsytan.

## <a name="api-versions"></a>API-versioner

I följande tabell visas API-versionen för de resurser som används i det här exemplet.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| [Arbetsyta](/azure/templates/microsoft.operationalinsights/workspaces) | arbetsytor | 2020-03-01-preview |
| [Automation-konto](/azure/templates/microsoft.automation/automationaccounts) | automatisering | 2020-01-13-preview |
| [Länkade tjänster för arbetsytan](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | arbetsytor | 2020-03-01-preview |
| [Lösningar](/azure/templates/microsoft.operationsmanagement/solutions) | lösningar | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Innan du använder mallen

JSON-mallen är konfigurerad för att fråga efter:

* Namnet på arbetsytan.
* Den region där arbetsytan ska skapas.
* Namnet på Automation-kontot.
* Regionen där Automation-kontot ska skapas.

Följande parametrar i mallen anges med ett standardvärde för Log Analytics-arbetsytan:

* *SKU* är som standard prisnivån per GB som släpptes i prismodellen för april 2018.
* *dataRetention* är som standard 30 dagar.

>[!WARNING]
>Om du vill skapa eller konfigurera en Log Analytics-arbetsyta i en prenumeration som har valt prismodellen för april 2018 är den enda giltiga Log Analytics-prisnivån *PerGB2018*.
>

JSON-mallen anger ett standardvärde för de andra parametrarna som troligen skulle användas som en standardkonfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns [i Distribuera resurser med ARM-mallar och Azure CLI.](../../azure-resource-manager/templates/deploy-cli.md)

Om du inte har Azure Automation och Azure Monitor är det viktigt att du förstår följande konfigurationsinformation. De kan hjälpa dig att undvika fel när du försöker skapa, konfigurera och använda en Log Analytics-arbetsyta som är länkad till ditt nya Automation-konto.

* Granska [ytterligare information för](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) att få en fullständig förståelse för konfigurationsalternativen för arbetsytan, till exempel åtkomstkontrollläge, prisnivå, kvarhållning och kapacitetsreservationsnivå.

* Granska [arbetsytemappningar](../how-to/region-mappings.md) för att ange vilka regioner som stöds, infogade eller i en parameterfil. Endast vissa regioner stöds för att länka en Log Analytics-arbetsyta och ett Automation-konto i din prenumeration.

* Om du inte har Azure Monitor och inte redan har distribuerat en arbetsyta bör du läsa designvägledningen [för arbetsytan.](../../azure-monitor/logs/design-logs-deployment.md) Den hjälper dig att lära dig mer om åtkomstkontroll och förstå de strategier för designimplementering som vi rekommenderar för din organisation.

## <a name="deploy-template"></a>Distribuera mallen

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                    "description": "Workspace name"
                }
            },
            "sku": {
                "type": "string",
                "allowedValues": [
                    "pergb2018",
                    "Free",
                    "Standalone",
                    "PerNode",
                    "Standard",
                    "Premium"
                ],
                "defaultValue": "pergb2018",
                "metadata": {
                    "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
                }
            },
            "dataRetention": {
                "type": "int",
                "defaultValue": 30,
                "minValue": 7,
                "maxValue": 730,
                "metadata": {
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
                "metadata": {
                    "description": "Specifies the location in which to create the workspace."
                }
            },
            "automationAccountName": {
                "type": "string",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "automationAccountLocation": {
                "type": "string",
                "metadata": {
                    "description": "Specifies the location in which to create the Automation account."
                }
            },
            "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
                }
            }
        },
        "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
          }
        },
        "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
                "name": "[variables('Updates').name]",
                "type": "Microsoft.OperationsManagement/solutions",
                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                },
                "plan": {
                    "name": "[variables('Updates').name]",
                    "publisher": "Microsoft",
                    "promotionCode": "",
                    "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                }
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Redigera mallen så att den uppfyller dina krav. Överväg att skapa [Resource Manager en parameterfil i](../../azure-resource-manager/templates/parameter-files.md) stället för att skicka parametrar som infogade värden.

3. Spara den här filen i en lokal mappdeployUMSolutiontemplate.js **på**.

4. Nu är det dags att distribuera den här mallen. Du kan använda antingen PowerShell eller Azure CLI. När du uppmanas att ange en arbetsyta och Ett Automation-kontonamn anger du ett namn som är globalt unikt för alla Azure-prenumerationer.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Det kan ta några minuter att slutföra distributionen. När den är klar visas ett meddelande som liknar följande som innehåller resultatet:

    ![Exempelresultat när distributionen är klar](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I Azure Portal du det Automation-konto som du skapade.

3. Välj **Runbooks** i det vänstra fönstret. På sidan **Runbooks** visas tre självstudie-runbooks som skapats med Automation-kontot.

    ![Självstudie om runbooks som skapats med Automation-konto](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Välj Länkad arbetsyta i **den vänstra rutan.** På sidan **Länkad arbetsyta** visas Log Analytics-arbetsytan som du angav tidigare länkad till ditt Automation-konto.

    ![Automation-konto som är länkat till Log Analytics-arbetsytan](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. Välj Uppdateringshantering i den **vänstra rutan.** På sidan **Uppdateringshantering** visas utvärderingssidan utan någon information på grund av att den precis har aktiverats, och datorer har inte konfigurerats för hantering.

    ![Uppdateringshantering för funktionsutvärdering](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver  dem tar du bort uppdateringslösningen på Log Analytics-arbetsytan, tar bort länken till Automation-kontot från arbetsytan och tar sedan bort Automation-kontot och arbetsytan.

## <a name="next-steps"></a>Nästa steg

* Information om Uppdateringshantering för virtuella datorer finns [i Hantera uppdateringar och korrigeringar för dina virtuella datorer.](manage-updates-for-vm.md)

* Om du inte längre vill använda Uppdateringshantering och vill ta bort den kan du läsa anvisningarna i [Ta bort Uppdateringshantering funktionen](remove-feature.md).

* Information om hur du tar bort virtuella Uppdateringshantering datorer finns [i Ta bort virtuella datorer från Uppdateringshantering](remove-vms.md).
