---
title: Aktivera Uppdateringshantering via en Azure Resource Manager-mall
description: Den här artikeln beskriver hur du använder en Azure Resource Manager-mall för att aktivera Uppdateringshantering.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 95ef52acedc9171ba86110a665d08ea97c59bfbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575817"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Aktivera Uppdateringshantering via en Azure Resource Manager-mall

Du kan använda en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) för att aktivera funktionen Azure Automation uppdateringshantering i resurs gruppen. Den här artikeln innehåller en exempel mall som automatiserar följande:

* Automatiserar skapandet av en Azure Monitor Log Analytics-arbetsyta.
* Automatiserar skapandet av ett Azure Automation-konto.
* Länkar Automation-kontot till Log Analytics-arbetsytan.
* Lägger till exempel på Automation-runbooks i kontot.
* Aktiverar funktionen Uppdateringshantering.

Mallen aktiverar inte Uppdateringshantering på en eller flera virtuella Azure-eller icke-Azure-datorer.

Om du redan har en Log Analytics arbets yta och ett Automation-konto som har distribuerats i en region som stöds i din prenumeration är de inte länkade. Med den här mallen skapas länken och distribuerar Uppdateringshantering.

>[!NOTE]
>Det går inte att skapa Automation-kör som-kontot när du använder en ARM-mall. Om du vill skapa ett Kör som-konto manuellt från portalen eller med PowerShell, se [skapa kör som-konto](../create-run-as-account.md).

När du har slutfört de här stegen måste du [Konfigurera diagnostikinställningar](../automation-manage-send-joblogs-log-analytics.md) för ditt Automation-konto så att Runbook-jobbets status och jobb strömmar skickas till den länkade Log Analytics-arbetsytan.

## <a name="api-versions"></a>API-versioner

I följande tabell visas API-versionen för de resurser som används i det här exemplet.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| [Arbetsyta](/azure/templates/microsoft.operationalinsights/workspaces) | arbetsytor | 2020-03-01 – för hands version |
| [Automation-konto](/azure/templates/microsoft.automation/automationaccounts) | automatisering | 2020-01-13 – för hands version |
| [Länkade arbets ytor tjänster](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | arbetsytor | 2020-03-01 – för hands version |
| [Lösningar](/azure/templates/microsoft.operationsmanagement/solutions) | lösningar | 2015-11-01 – för hands version |

## <a name="before-using-the-template"></a>Innan du använder mallen

JSON-mallen har kon figurer ATS för att uppmana dig att:

* Namnet på arbets ytan.
* Regionen som arbets ytan ska skapas i.
* Namnet på Automation-kontot.
* Regionen som du skapar Automation-kontot i.

Följande parametrar i mallen anges med ett standardvärde för Log Analytics arbets ytan:

* *SKU: n* är som standard den pris nivå per GB som lanserades i pris sättnings modellen april 2018.
* *dataRetention* är som standard 30 dagar.

>[!WARNING]
>Om du vill skapa eller konfigurera en Log Analytics arbets yta i en prenumeration som har valt att använda pris sättnings modellen från april 2018 är den enda giltiga Log Analytics pris nivån *PerGB2018*.
>

JSON-mallen anger ett standardvärde för de andra parametrarna som sannolikt används som standard konfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [distribuera resurser med ARM-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

Om du är nybörjare på Azure Automation och Azure Monitor är det viktigt att du förstår följande konfigurations information. De kan hjälpa dig att undvika fel när du försöker skapa, konfigurera och använda en Log Analytics arbets yta som är länkad till det nya Automation-kontot.

* Granska [Ytterligare information](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) för att helt förstå konfigurations alternativ för arbets ytor, till exempel åtkomst kontrol läge, pris nivå, kvarhållning och kapacitets reservations nivå.

* Granska [mappningar för arbets ytor](../how-to/region-mappings.md) för att ange de regioner som stöds infogade eller i en parameter fil. Endast vissa regioner stöds för att länka en Log Analytics-arbetsyta och ett Automation-konto i din prenumeration.

* Om du inte har använt Azure Monitor loggar och inte har distribuerat en arbets yta redan, bör du gå igenom [rikt linjerna för design av arbets ytor](../../azure-monitor/logs/design-logs-deployment.md). Det hjälper dig att lära dig mer om åtkomst kontroll och förstå de design implementerings strategier som vi rekommenderar för din organisation.

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

2. Redigera mallen så att den uppfyller dina krav. Överväg att skapa en [Resource Manager-parameter fil](../../azure-resource-manager/templates/parameter-files.md) i stället för att skicka parametrar som infogade värden.

3. Spara filen i en lokal mapp som **deployUMSolutiontemplate.jspå**.

4. Nu är det dags att distribuera den här mallen. Du kan använda antingen PowerShell eller Azure CLI. När du uppmanas att ange ett namn på en arbets yta och ett Automation-konto anger du ett namn som är globalt unikt för alla Azure-prenumerationer.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Det kan ta några minuter att slutföra distributionen. När det är klart visas ett meddelande som liknar följande som innehåller resultatet:

    ![Exempel på resultat när distributionen är klar](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Öppna det Automation-konto som du skapade i Azure Portal.

3. I den vänstra rutan väljer du **Runbooks**. På sidan **Runbooks** är listade tre självstudier som skapats med Automation-kontot.

    ![Självstudie-Runbooks som skapats med Automation-konto](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Välj **länkad arbets yta** i det vänstra fönstret. På sidan **länkad arbets yta** visas Log Analytics arbets ytan du angav tidigare länkad till ditt Automation-konto.

    ![Automation-konto som är länkat till arbets ytan Log Analytics](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. Välj **hantering av uppdateringar** i den vänstra rutan. På sidan **hantering av uppdateringar** visas sidan utvärdering utan någon information på grund av att den inte är aktive rad, och datorerna har inte kon figurer ATS för hantering.

    ![Vy över Uppdateringshantering-funktions bedömning](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort **uppdaterings** lösningen i arbets ytan Log Analytics, tar bort länken till Automation-kontot från arbets ytan och tar sedan bort Automation-kontot och arbets ytan.

## <a name="next-steps"></a>Nästa steg

* Om du vill använda Uppdateringshantering för virtuella datorer läser du [Hantera uppdateringar och korrigeringar för dina virtuella datorer](manage-updates-for-vm.md).

* Om du inte längre vill använda Uppdateringshantering och vill ta bort det, se anvisningar i [ta bort uppdateringshantering-funktionen](remove-feature.md).

* Om du vill ta bort virtuella datorer från Uppdateringshantering, se [ta bort virtuella datorer från uppdateringshantering](remove-vms.md).