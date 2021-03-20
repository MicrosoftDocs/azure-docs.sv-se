---
title: Självstudie – resurs onboarding
description: Med resurs onboarding via anpassade providers kan du ändra och utöka befintliga Azure-resurser.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75649941"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Självstudie: resurs onboarding med Azure-anpassade leverantörer

I den här självstudien distribuerar du till Azure en anpassad resurs leverantör som utökar Azure Resource Manager-API: t med resurs typen Microsoft. CustomProviders/associationer. I självstudien visas hur du utökar befintliga resurser som ligger utanför resurs gruppen där den anpassade Provider-instansen finns. I den här självstudien drivs den anpassade resurs leverantören av en Azure Logic-app, men du kan använda valfri offentlig API-slutpunkt.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du känna till följande:

* Funktionerna i [Azure-anpassade providers](overview.md).
* Grundläggande information om [resurs onboarding med anpassade providers](concepts-resource-onboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Kom igång med resurs onboarding

I den här självstudien finns det två delar som måste distribueras: den anpassade providern och associationen. Om du vill göra processen enklare kan du välja att använda en enda mall som distribuerar båda.

Mallen kommer att använda dessa resurser:

* Microsoft. CustomProviders/resourceProviders
* Microsoft. Logic/arbets flöden
* Microsoft. CustomProviders/associationer

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Distribuera infrastrukturen för anpassad Provider

Den första delen av mallen distribuerar den anpassade providerns infrastruktur. Den här infrastrukturen definierar resultatet av kopplings resursen. Om du inte är bekant med anpassade providers kan du läsa [grunderna för anpassad Provider](overview.md).

Nu ska vi distribuera infrastrukturen för anpassad Provider. Kopiera, Spara och distribuera föregående mall, eller följ med och distribuera infrastrukturen med hjälp av Azure Portal.

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Sök efter **mallar** i **alla tjänster** eller med hjälp av huvud Sök rutan:

   ![Sök efter mallar](media/tutorial-resource-onboarding/templates.png)

3. Välj **Lägg till** i fönstret **mallar** :

   ![Välj Lägg till](media/tutorial-resource-onboarding/templatesadd.png)

4. Under **Allmänt** anger du ett **namn** och en **Beskrivning** för den nya mallen:

   ![Mallens namn och beskrivning](media/tutorial-resource-onboarding/templatesdescription.png)

5. Skapa Resource Manager-mallen genom att kopiera i JSON-mallen från avsnittet "kom igång med resurs onboarding" i den här artikeln:

   ![Skapa en Resource Manager-mall](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Välj **Lägg till** för att skapa mallen. Om den nya mallen inte visas väljer du **Uppdatera**.

7. Välj den nya mallen och välj sedan **distribuera**:

   ![Välj den nya mallen och välj sedan distribuera](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Ange inställningarna för de obligatoriska fälten och välj sedan prenumerationen och resurs gruppen. Du kan lämna rutan **anpassad resurs leverantörs-ID** tom.

   | Inställningsnamn | Obligatoriskt? | Beskrivning |
   | ------------ | -------- | ----------- |
   | Plats | Ja | Platsen för resurserna i mallen. |
   | Namn på Logic app | Inga | Namnet på Logic-appen. |
   | Namn på anpassad resurs leverantör | Inga | Namnet på den anpassade resurs leverantören. |
   | ID för anpassad resurs leverantör | Inga | En befintlig anpassad resurs leverantör som stöder Associations resursen. Om du anger ett värde här hoppas Logic app och distributionen av den anpassade providern över. |
   | Kopplings namn | Inga | Namnet på Associations resursen. |

   Exempel parametrar:

   ![Ange mallparametrar](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Gå till distributionen och vänta tills den är klar. Du bör se något som liknar följande skärm bild. Du bör se den nya Associations resursen som utdata:

   ![Lyckad distribution](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Här är resurs gruppen, där **Visa dolda typer** har valts:

   ![Distribution av anpassad Provider](media/tutorial-resource-onboarding/showhidden.png)

10. Utforska fliken **tidigare körnings historik** för att se anropen för associationen Create:

    ![Körnings historik för Logic app](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Distribuera ytterligare associationer

När du har konfigurerat den anpassade providern av infrastrukturen kan du enkelt distribuera fler associationer. Resurs gruppen för ytterligare associationer behöver inte vara samma som den resurs grupp där du har distribuerat den anpassade providern för infrastrukturen. Om du vill skapa en Association måste du ha Microsoft. CustomProviders/resourceproviders/Write-behörigheter för det angivna anpassade resurs-ID: t.

1. Gå till den anpassade providern **Microsoft. CustomProviders/resourceProviders** Resource i resurs gruppen för den tidigare distributionen. Du måste markera kryss rutan **Visa dolda typer** :

   ![Gå till resursen](media/tutorial-resource-onboarding/showhidden.png)

2. Kopiera egenskapen resurs-ID för den anpassade providern.

3. Sök efter **mallar** i **alla tjänster** eller med hjälp av huvud Sök rutan:

   ![Sök efter mallar](media/tutorial-resource-onboarding/templates.png)

4. Välj den mall som skapats tidigare och välj sedan **distribuera**:

   ![Välj den mall som skapats tidigare och välj sedan distribuera](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Ange inställningarna för de obligatoriska fälten och välj sedan prenumerationen och en annan resurs grupp. För inställningen **anpassad resurs leverantörs-ID** anger du det resurs-ID som du kopierade från den anpassade provider som du distribuerade tidigare.

6. Gå till distributionen och vänta tills den är klar. Den ska nu bara distribuera den nya Associations resursen:

   ![Ny Association resurs](media/tutorial-resource-onboarding/createdassociationresource.png)

Om du vill kan du gå tillbaka till **körnings historiken** för Logic app och se att ett annat anrop gjordes till Logic-appen. Du kan uppdatera Logic app för att utöka ytterligare funktioner för varje skapad Association.

## <a name="getting-help"></a>Få hjälp

Om du har frågor om Azure-anpassade leverantörer kan du prova att be dem om [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kanske redan har besvarats. kontrol lera först innan du publicerar. Lägg till taggen `azure-custom-providers` för att få ett snabbt svar!

