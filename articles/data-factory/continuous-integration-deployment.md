---
title: Kontinuerlig integrering och leverans i Azure Data Factory
description: Lär dig hur du använder kontinuerlig integrering och leverans för att flytta Data Factory pipelines från en miljö (utveckling, testning, produktion) till en annan.
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2176e74c46dc395672701555624aceb1b9563358
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592123"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Kontinuerlig integrering och leverans i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Översikt

Kontinuerlig integrering är en metod för att testa varje ändring av kodbasen automatiskt och så tidigt som möjligt. Kontinuerlig leverans följer testerna som utförs vid den kontinuerliga integreringen och skickar ändringarna till ett mellanlagrings- eller produktionssystem.

I Azure Data Factory innebär kontinuerlig integrering och leverans (CI/CD) flytt av Data Factory-pipelines från en miljö (utveckling, testning, produktion) till en annan. Azure Data Factory använder [Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md) för att lagra konfigurationen av dina olika ADF-enheter (pipelines, data uppsättningar, data flöden osv.). Det finns två föreslagna metoder för att flytta en data fabrik till en annan miljö:

-    Automatiserad distribution med hjälp av Data Factorys integrering med [Azure-pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)
-    Ladda upp en Resource Manager-mall manuellt med hjälp av Data Factory UX-integrering med Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-livscykel

Nedan visas ett exempel på en översikt över CI/CD-livscykeln i en Azure-datafabrik som är konfigurerad med Azure databaser git. Mer information om hur du konfigurerar en git-lagringsplats finns i [käll kontroll i Azure Data Factory](source-control.md).

1.  En utvecklings data fabrik skapas och konfigureras med Azure databaser git. Alla utvecklare måste ha behörighet att redigera Data Factory resurser som pipelines och data uppsättningar.

1.  En utvecklare [skapar en funktions gren](source-control.md#creating-feature-branches) som gör en ändring. De gör det möjligt att felsöka sina pipeliner med de senaste ändringarna. Mer information om hur du felsöker en pipeline-körning finns i [iterativ utveckling och fel sökning med Azure Data Factory](iterative-development-debugging.md).

1.  När en utvecklare är nöjd med sina ändringar, skapar de en pull-begäran från sina funktions grenar till huvud-eller samarbets grenen för att få sina ändringar granskade av peer-datorer.

1.  När en pull-begäran har godkänts och ändringar slås samman i huvud grenen publiceras ändringarna i utvecklings fabriken.

1.  När teamet är redo att distribuera ändringarna i en test-eller UAT-fabrik (User Accepting testing), går teamet till sina Azure-pipeliner och distribuerar den önskade versionen av utvecklings fabriken till UAT. Den här distributionen äger rum som en del av en Azure pipeline-uppgift och använder parametrarna för Resource Manager-mallar för att tillämpa lämplig konfiguration.

1.  När ändringarna har verifierats i test fabriken distribuerar du till produktions fabriken genom att använda nästa uppgift för pipelines-versionen.

> [!NOTE]
> Endast utvecklings fabriken är kopplad till en git-lagringsplats. Test-och produktions faktorerna bör inte ha en git-lagringsplats kopplad till sig och bör bara uppdateras via en Azure DevOps-pipeline eller via en resurs hanterings mall.

I bilden nedan beskrivs de olika stegen i den här livs cykeln.

![Diagram över kontinuerlig integrering med Azure-pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatisera kontinuerlig integrering med hjälp av Azure pipelines-versioner

Följande är en guide för att konfigurera en Azure pipelines-lansering som automatiserar distributionen av en data fabrik till flera miljöer.

### <a name="requirements"></a>Krav

-   En Azure-prenumeration som är länkad till Visual Studio Team Foundation Server eller Azure-databaser som använder [Azure Resource Manager tjänstens slut punkt](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   En data fabrik som kon figurer ATS med Azure databaser git-integrering.

-   Ett [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som innehåller hemligheterna för varje miljö.

### <a name="set-up-an-azure-pipelines-release"></a>Konfigurera en version av Azure pipelines

1.  Öppna det projekt som har kon figurer ATS med din data fabrik i [Azure-DevOps](https://dev.azure.com/).

1.  Välj **pipelines** på vänster sida av sidan och välj sedan **versioner**.

    ![Välj pipeliner, versioner](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Välj **ny pipeline** eller, om du har befintliga pipeliner, väljer du **ny** och sedan **ny versions pipeline**.

1.  Välj den **tomma jobb** mal len.

    ![Välj tomt jobb](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  I rutan **scen namn** anger du namnet på din miljö.

1.  Välj **Lägg till artefakt** och välj sedan den git-lagringsplats som kon figurer ATS med utvecklings data fabriken. Välj [publicerings grenen](source-control.md#configure-publishing-settings) för lagrings platsen för **standard grenen**. Som standard är publicerings grenen `adf_publish` . Välj **senaste från standard gren** för **standard versionen**.

    ![Lägg till en artefakt](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Lägg till en Azure Resource Manager distributions uppgift:

    a.  I vyn fas väljer du **Visa fas aktiviteter**.

    ![Vyn fas](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Skapa en ny uppgift. Sök efter **distribution av arm-mall** och välj sedan **Lägg till**.

    c.  I distributions aktiviteten väljer du prenumeration, resurs grupp och plats för mål data fabriken. Ange autentiseringsuppgifter om det behövs.

    d.  I listan **åtgärd** väljer du **skapa eller uppdatera resurs grupp**.

    e.  Välj knappen med tre punkter (**...**) bredvid rutan **mall** . Bläddra till den Azure Resource Manager-mall som genereras i publicerings grenen för den konfigurerade git-lagringsplatsen. Leta efter filen `ARMTemplateForFactory.json` i <FactoryName> mappen i adf_publish grenen.

    f.  Välj **...** bredvid rutan **mallparametrar** för att välja parameter filen. Leta efter filen `ARMTemplateParametersForFactory.json` i <FactoryName> mappen i adf_publish grenen.

    ex.  Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parameter värden för mål data fabriken. För autentiseringsuppgifter som kommer från Azure Key Vault anger du hemlighetens namn mellan dubbla citat tecken. Om t. ex. hemlighetens namn är cred1 anger du **"$ (cred1)"** för det här värdet.

    h. Välj **stegvis** för **distributions läget**.

    > [!WARNING]
    > I fullständigt distributions läge **raderas** resurser som finns i resurs gruppen men inte har angetts i den nya Resource Manager-mallen. Mer information finns i [Azure Resource Manager distributions lägen](../azure-resource-manager/templates/deployment-modes.md)

    ![Data Factory Prod. distribution](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Spara versions pipelinen.

1. Om du vill utlösa en version väljer du **Skapa version**. För att automatisera skapandet av versioner, se [Azure DevOps release triggers](/azure/devops/pipelines/release/triggers)

   ![Välj Skapa version](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> I CI/CD-scenarier måste integrerings körnings typen (IR) i olika miljöer vara densamma. Om du till exempel har en lokal IR-anslutning i utvecklings miljön, måste samma IR också vara av typen egen värd i andra miljöer, till exempel test och produktion. Om du däremot delar integrerings körningar över flera steg, måste du konfigurera integration runtime som länkad egen värd i alla miljöer, till exempel utveckling, testning och produktion.

### <a name="get-secrets-from-azure-key-vault"></a>Hämta hemligheter från Azure Key Vault

Om du har hemligheter som ska skickas i en Azure Resource Manager-mall rekommenderar vi att du använder Azure Key Vault med Azure pipelines-versionen.

Det finns två sätt att hantera hemligheter:

1.  Lägg till filen hemligheter till Parameters. Mer information finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](../azure-resource-manager/templates/key-vault-parameter.md).

    Skapa en kopia av parameter filen som överförs till publicerings grenen. Ange värdena för de parametrar som du vill hämta från Key Vault med det här formatet:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    När du använder den här metoden hämtas hemligheten automatiskt från nyckel valvet.

    Parameter filen måste också finnas i publicerings grenen.

1. Lägg till en [Azure Key Vault aktivitet](/azure/devops/pipelines/tasks/deploy/azure-key-vault) före Azure Resource Manager distributions aktivitet som beskrivs i föregående avsnitt:

    1.  På fliken **aktiviteter** skapar du en ny uppgift. Sök efter **Azure Key Vault** och Lägg till det.

    1.  I Key Vault aktiviteten väljer du den prenumeration där du skapade nyckel valvet. Ange autentiseringsuppgifter om det behövs och välj sedan nyckel valvet.

    ![Lägg till en Key Vault uppgift](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Bevilja behörighet till Azure pipelines-agenten

Azure Key Vault aktiviteten kan Miss Miss kan ett fel meddelande om nekad åtkomst om rätt behörigheter inte har angetts. Hämta loggarna för versionen och leta upp den. ps1-fil som innehåller kommandot för att ge behörighet till Azure pipelines-agenten. Du kan köra kommandot direkt. Du kan också kopiera ägar-ID: t från filen och lägga till åtkomst principen manuellt i Azure Portal. `Get` och `List` är de lägsta behörigheter som krävs.

### <a name="updating-active-triggers"></a>Uppdaterar aktiva utlösare

Distributionen kan inte utföras om du försöker uppdatera aktiva utlösare. Om du vill uppdatera aktiva utlösare måste du stoppa dem manuellt och sedan starta om dem efter distributionen. Du kan göra detta med hjälp av en Azure PowerShell uppgift:

1.  Lägg till en **Azure PowerShell** aktivitet på fliken **aktiviteter** i versionen. Välj aktivitet version 4. *. 

1.  Välj den prenumeration som din fabrik är i.

1.  Välj **skript fil Sök väg** som skript typ. Detta kräver att du sparar ditt PowerShell-skript i din lagrings plats. Följande PowerShell-skript kan användas för att stoppa utlösare:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Du kan slutföra liknande steg (med `Start-AzDataFactoryV2Trigger` funktionen) för att starta om utlösarna efter distributionen.

Data Factory-teamet har angett ett [skript före och efter distribution](#script) som finns längst ned i den här artikeln. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Befordra en Resource Manager-mall manuellt för varje miljö

1. I listan **arm-mall** väljer du **Exportera arm** -mall för att exportera Resource Manager-mallen för din data fabrik i utvecklings miljön.

   ![Exportera en Resource Manager-mall](media/continuous-integration-deployment/continuous-integration-image1.png)

1. I test-och produktions data fabrikerna väljer du **Importera arm-mall**. Den här åtgärden tar dig till Azure Portal, där du kan importera den exporterade mallen. Välj **skapa en egen mall i redigeraren** för att öppna Principeditorn i Resource Manager.

   ![Bygg en egen mall](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Välj **Läs in fil** och välj sedan den genererade Resource Manager-mallen. Detta är **arm_template.jspå** filen som finns i. zip-filen som exporterades i steg 1.

   ![Redigera mall](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. I avsnittet inställningar anger du konfigurations värden, t. ex. länkade tjänst uppgifter. När du är klar väljer du **köp** för att distribuera Resource Manager-mallen.

   ![Avsnittet Inställningar](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Använda anpassade parametrar med Resource Manager-mallen

Om din utvecklings fabrik har en kopplad git-lagringsplats, kan du åsidosätta standardmallarna för Resource Manager för Resource Manager-mallen som genereras genom att publicera eller exportera mallen. Du kanske vill åsidosätta standard parameter konfigurationen för Resource Manager i följande scenarier:

* Du använder automatiserad CI/CD och du vill ändra vissa egenskaper under distributionen av Resource Manager, men egenskaperna är inte parameterstyrda som standard.
* Fabriken är så stor att Resource Manager-standardmallen är ogiltig eftersom den har fler än det högsta tillåtna antalet parametrar (256).

    Det finns tre alternativ för att hantera den anpassade parametern 256-gränsen:    
  
    * Använd den anpassade parameter filen och ta bort egenskaper som inte behöver Parameterisering, t. ex., egenskaper som kan behålla ett standardvärde och därmed minska antalet parametrar.
    * Omfaktas logik i data flödet för att minska parametrar, till exempel har alla pipeline-parametrar samma värde. du kan bara använda globala parametrar i stället.
    * Dela en data fabrik i flera data flöden.

Om du vill åsidosätta standard parameter konfigurationen för Resource Manager går du till **Hantera** hubben och väljer **arm-mall** i avsnittet "käll kontroll". Under avsnittet **konfiguration av arm-parameter** klickar du på **Redigera** ikon i "redigera parameter konfiguration" för att öppna Resource Manager-parameterns konfigurations kod redigerare.

![Hantera anpassade parametrar](media/author-management-hub/management-hub-custom-parameters.png)

> [!NOTE]
> **Konfiguration av arm-parameter** är bara aktiverat i git-läge. För närvarande är den inaktive rad i läget "Live mode" eller "Data Factory".

När du skapar en anpassad Resource Manager-parameter konfigureras en fil med namnet **arm-template-parameters-definition.jspå** i rotmappen i git-grenen. Du måste använda det exakta fil namnet.

![Fil för anpassade parametrar](media/continuous-integration-deployment/custom-parameters.png)

När du publicerar från samarbets grenen kommer Data Factory att läsa den här filen och använda dess konfiguration för att generera vilka egenskaper som får parametrar. Om ingen fil hittas används standard mal len.

När du exporterar en Resource Manager-mall Data Factory läser filen från den gren som du för närvarande arbetar på, inte samarbets grenen. Du kan skapa eller redigera filen från en privat gren, där du kan testa dina ändringar genom att välja **Exportera arm-mall** i användar gränssnittet. Sedan kan du slå samman filen till samarbets grenen.

> [!NOTE]
> En anpassad Resource Manager-parameter har inte ändra parameterns gräns för ARM-mall på 256. Du kan välja och minska antalet parameter egenskaper.

### <a name="custom-parameter-syntax"></a>Anpassad parameter-syntax

Nedan följer några rikt linjer som du följer när du skapar den anpassade parameter filen **arm-template-parameters-definition.jspå**. Filen består av ett avsnitt för varje entitetstyp: utlösare, pipeline, länkad tjänst, data uppsättning, integration Runtime och data flöde.

* Ange sökvägen till egenskapen under den relevanta entitetstypen.
* Om du anger ett egenskaps namn för att ange att `*` du vill Parameterisera alla egenskaper under den (enbart till den första nivån, inte rekursivt). Du kan också ange undantag för den här konfigurationen.
* Att ange värdet för en egenskap som en sträng anger att du vill Parameterisera egenskapen. Använd formatet `<action>:<name>:<stype>`.
   *  `<action>` kan vara något av följande tecken:
      * `=` betyder att det aktuella värdet ska vara standardvärdet för parametern.
      * `-` innebär att inte behålla standardvärdet för parametern.
      * `|` är ett specialfall för hemligheter från Azure Key Vault för anslutnings strängar eller nycklar.
   * `<name>` är namnet på parametern. Om det är tomt tar det med namnet på egenskapen. Om värdet börjar med ett `-` Character förkortas namnet. Till exempel `AzureStorage1_properties_typeProperties_connectionString` skulle kortas till `AzureStorage1_connectionString` .
   * `<stype>` är typen av parameter. Om `<stype>` är tomt är standard typen `string` . Värden som stöds:,,,, `string` `securestring` `int` `bool` `object` `secureobject` och `array` .
* Att ange en matris i definitions filen anger att den matchande egenskapen i mallen är en matris. Data Factory itererar igenom alla objekt i matrisen med hjälp av definitionen som anges i integration runtime-objektet i matrisen. Det andra objektet, en sträng, blir namnet på egenskapen, som används som namn för parametern för varje iteration.
* En definition kan inte vara unik för en resurs instans. Alla definitioner gäller för alla resurser av den typen.
* Som standard är alla säkra strängar, som Key Vault hemligheter och säkra strängar, som anslutnings strängar, nycklar och tokens, parameterstyrda.
 
### <a name="sample-parameterization-template"></a>Exempel på Parameterisering-mall

Här är ett exempel på hur en resurs hanterings parameter konfiguration kan se ut så här:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Här är en förklaring av hur föregående mall skapas, uppdelat efter resurs typ.

#### <a name="pipelines"></a>Pipelines
    
* Alla egenskaper i sökvägen `activities/typeProperties/waitTimeInSeconds` är parameterstyrda. Alla aktiviteter i en pipeline som har en kod nivå egenskap med namnet `waitTimeInSeconds` (till exempel `Wait` aktiviteten) är parameterstyrda som ett tal med ett standard namn. Men det finns inget standardvärde i Resource Manager-mallen. Det är en obligatorisk Indatatyp under distributionen av Resource Manager.
* På samma sätt är en egenskap `headers` som kallas (t. ex. i en `Web` aktivitet) parameterstyrda med typen `object` (JObject). Det har ett standardvärde, vilket är samma värde som käll fabriken.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alla egenskaper under sökvägen `typeProperties` är parameterstyrda med respektive standardvärden. Det finns till exempel två egenskaper under `IntegrationRuntimes` typ egenskaper: `computeProperties` och `ssisProperties` . Båda egenskaps typerna skapas med deras respektive standardvärden och typer (objekt).

#### <a name="triggers"></a>Utlösare

* Under `typeProperties` , har två egenskaper parametriserade. Det första är `maxConcurrency` , som har angetts att ha ett standardvärde och är av typen `string` . Den har standard parameter namnet `<entityName>_properties_typeProperties_maxConcurrency` .
* `recurrence`Egenskapen är också parametriserad. Under den här nivån anges alla egenskaper på den nivån som parameterstyrda som strängar, med standardvärden och parameter namn. Ett undantag är `interval` egenskapen, som är parameterstyrda som typ `int` . Parameter namnet har suffix `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . På samma sätt `freq` är egenskapen en sträng och är parameterstyrda som en sträng. `freq`Egenskapen är dock parameterstyrda utan ett standardvärde. Namnet är kortare och suffixet. Till exempel `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Länkade tjänster är unika. Eftersom länkade tjänster och data uppsättningar har en mängd olika typer, kan du ange en typ bestämd anpassning. I det här exemplet `AzureDataLakeStore` används en speciell mall för alla länkade tjänster av typen. En annan mall används för alla andra (via `*` ).
* `connectionString`Egenskapen är parameterstyrda som ett `securestring` värde. Det har inget standardvärde. Det kommer att ha ett förkortat parameter namn med suffix `connectionString` .
* Egenskapen `secretAccessKey` inträffar som en (till `AzureKeyVaultSecret` exempel i en länkad Amazon S3-tjänst). Den är automatiskt parameterstyrda som en Azure Key Vault hemlighet och hämtas från det konfigurerade nyckel valvet. Du kan också Parameterisera själva nyckel valvet.

#### <a name="datasets"></a>Datauppsättningar

* Även om typ specifik anpassning är tillgänglig för data uppsättningar kan du ange konfiguration utan att uttryckligen ha en \* -nivå-konfiguration. I föregående exempel är alla data uppsättnings egenskaper under `typeProperties` parameterstyrda.

> [!NOTE]
> **Azure-aviseringar och matriser**  som kon figurer ATS för en pipeline stöds inte för närvarande som parametrar för arm-distributioner. Om du vill tillämpa aviseringar och matriser på nytt i en ny miljö ska du följa [Data Factory övervakning, varningar och matriser.](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics)
> 

### <a name="default-parameterization-template"></a>Standard Parameterisering-mall

Nedan visas den aktuella standard Parameterisering-mallen. Om du bara behöver lägga till några få parametrar kan det vara en bra idé att redigera mallen direkt eftersom du inte förlorar den befintliga Parameterisering-strukturen.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Exempel: parametriserade ett befintligt Azure Databricks interaktivt kluster-ID

I följande exempel visas hur du lägger till ett enda värde i standard mal len Parameterisering. Vi vill bara lägga till ett befintligt Azure Databricks interaktiva kluster-ID: t för en länkad Databricks-tjänst till parameter filen. Observera att filen är samma som föregående fil, förutom att du kan lägga till `existingClusterId` under fältet egenskaper i `Microsoft.DataFactory/factories/linkedServices` .

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Länkade Resource Manager-mallar

Om du har konfigurerat CI/CD för dina data fabriker kan du överskrida gränserna för Azure Resource Manager mal len när fabriken växer större. Till exempel är en gräns det maximala antalet resurser i en Resource Manager-mall. För att kunna hantera stora fabriker samtidigt som du skapar en fullständig Resource Manager-mall för en fabrik, skapar Data Factory nu länkade Resource Manager-mallar. Med den här funktionen delas hela fabriks nytto lasten upp i flera filer så att du inte begränsas av gränserna.

Om du har konfigurerat git skapas och sparas de länkade mallarna tillsammans med de fullständiga Resource Manager-mallarna i adf_publish grenen i en ny mapp med namnet linkedTemplates:

![Mapp för länkade Resource Manager-mallar](media/continuous-integration-deployment/linked-resource-manager-templates.png)

De länkade Resource Manager-mallarna består vanligt vis av en huvud mal len och en uppsättning underordnade mallar som är länkade till huvud servern. Den överordnade mallen kallas ArmTemplate_master.jspå och underordnade mallar namnges med mönstret ArmTemplate_0.jspå, ArmTemplate_1.jspå och så vidare. 

Om du vill använda länkade mallar i stället för den fullständiga Resource Manager-mallen uppdaterar du CI/CD-aktiviteten så att den pekar på ArmTemplate_master.jsi stället för ArmTemplateForFactory.jspå (fullständig Resource Manager-mall). Resource Manager kräver också att du överför de länkade mallarna till ett lagrings konto så att Azure kan komma åt dem under distributionen. Mer information finns i [distribuera länkade Resource Manager-mallar med VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

Kom ihåg att lägga till Data Factory skript i CI/CD-pipeline innan och efter distributions aktiviteten.

Om du inte har git konfigurerat kan du komma åt de länkade mallarna via **export arm-mallen** i listan **arm-mall** .

## <a name="hotfix-production-environment"></a>Snabb korrigerings produktions miljö

Om du distribuerar en fabrik till produktion och inser att det finns en bugg som måste åtgärdas direkt, men du inte kan distribuera den aktuella samarbets grenen, kan du behöva distribuera en snabb korrigering. Den här metoden kallas snabb korrigerings teknik eller QFE.

1.    I Azure DevOps går du till den version som distribuerades till produktionen. Hitta den senaste incheckning som har distribuerats.

2.    Hämta genomförande-ID: t för samarbets grenen från bekräftelse meddelandet.

3.    Skapa en ny gren för snabb korrigeringar från det genomförandet.

4.    Gå till Azure Data Factory UX och växla till snabb korrigerings grenen.

5.    Åtgärda felet genom att använda Azure Data Factory UX. Testa dina ändringar.

6.    När korrigeringen har verifierats väljer du **Exportera arm-mall** för att hämta mallen för snabb korrigeringar för Resource Manager.

7.    Kontrol lera den här versionen manuellt i adf_publish grenen.

8.    Om du har konfigurerat din versions pipeline så att den automatiskt utlöses baserat på adf_publish incheckningar startar en ny version automatiskt. Annars måste du köa en version manuellt.

9.    Distribuera snabb korrigerings versionen till test-och produktions fabrikerna. Den här versionen innehåller föregående produktions nytto Last plus den korrigering som du gjorde i steg 5.

10.   Lägg till ändringarna från snabb korrigeringen till utvecklings grenen så att senare versioner inte tar med samma fel.

Se videon under en djupgående video genom gång om hur du kan åtgärda dina miljöer. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="exposure-control-and-feature-flags"></a>Exponerings kontroll och funktions flaggor

När du arbetar med ett team finns det instanser där du kan slå samman ändringar, men vill inte att de ska köras i utökade miljöer som till exempel PROD och frågor och svar. För att hantera det här scenariot rekommenderar ADF-teamet [DevOps-begreppet att använda funktions flaggor](/azure/devops/migrate/phase-features-with-feature-flags). I ADF kan du kombinera [globala parametrar](author-global-parameters.md) och [if villkor-aktiviteten](control-flow-if-condition-activity.md) för att dölja uppsättningar av logik baserat på dessa miljö flaggor.

Information om hur du konfigurerar en funktions flagga finns i följande video kurs:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="best-practices-for-cicd"></a>Metod tips för CI/CD

Om du använder git-integrering med din data fabrik och har en CI/CD-pipeline som flyttar dina ändringar från utveckling till test och sedan till produktion, rekommenderar vi följande metod tips:

-   **Git-integrering**. Konfigurera endast din utvecklings data fabrik med git-integrering. Ändringar av test och produktion distribueras via CI/CD och kräver inte git-integrering.

-   **Skript för för-och efter distribution**. Innan du utför distributions steget i Resource Manager i CI/CD måste du slutföra vissa åtgärder, t. ex. stoppa och starta om utlösare och rensning. Vi rekommenderar att du använder PowerShell-skript före och efter distributions aktiviteten. Mer information finns i [Uppdatera aktiva utlösare](#updating-active-triggers). Data Factory-teamet har [angett ett skript](#script) som ska användas längst ned på den här sidan.

-   **Integrerings körningar och delning**. Integrerings körningar ändras inte ofta och liknar varandra i alla steg i CI/CD. Så Data Factory förväntar dig att du har samma namn och typ av integration runtime i alla stadier av CI/CD. Om du vill dela integrerings körningar i alla faser bör du överväga att använda en ternär fabrik som bara innehåller de delade integrerings körningarna. Du kan använda den här delade fabriken i alla dina miljöer som en länkad integration runtime-typ.

-   **Distribution av hanterad privat slut punkt**. Om det redan finns en privat slut punkt i en fabrik och du försöker distribuera en ARM-mall som innehåller en privat slut punkt med samma namn, men med ändrade egenskaper, kommer distributionen att Miss Förslut. Med andra ord kan du distribuera en privat slut punkt så länge den har samma egenskaper som den som redan finns i fabriken. Om någon egenskap skiljer sig mellan miljöer kan du åsidosätta den genom att parametriserade den egenskapen och ange respektive värde under distributionen.

-   **Key Vault**. När du använder länkade tjänster vars anslutnings information lagras i Azure Key Vault rekommenderar vi att du håller separata nyckel valv för olika miljöer. Du kan också konfigurera separata behörighets nivåer för varje nyckel valv. Till exempel kanske du inte vill att dina team medlemmar ska ha behörighet till produktions hemligheter. Om du följer den här metoden rekommenderar vi att du behåller samma hemliga namn i alla steg. Om du behåller samma hemliga namn behöver du inte Parameterisera varje anslutnings sträng i CI/CD-miljöer eftersom det enda som ändras är nyckel valvets namn, som är en separat parameter.

-  **Resurs namn** På grund av begränsningar i ARM-mallar kan problem i distributionen uppstå om dina resurser innehåller blank steg i namnet. Azure Data Factorys teamet rekommenderar att du använder "_" eller "-" tecken i stället för utrymme för resurser. Till exempel är Pipeline_1 ett hellre namn över "pipelining 1".

## <a name="unsupported-features"></a>Funktioner som inte stöds

- Enligt design tillåter Data Factory inte körsbär-plockning av incheckningar eller selektiv publicering av resurser. Publiceringar tar med alla ändringar som gjorts i data fabriken.

    - Data Factory-entiteter är beroende av varandra. Utlösare är exempelvis beroende av pipeliner och pipeliner beror på data uppsättningar och andra pipeliner. Selektiv publicering av en del av resurser kan leda till oväntade beteenden och fel.
    - Vid sällsynta tillfällen när du behöver selektiv publicering bör du överväga att använda en snabb korrigering. Mer information finns i [snabb korrigerings produktions miljö](#hotfix-production-environment).

- Azure Data Factorys teamet rekommenderar inte att du tilldelar Azure RBAC-kontroller till enskilda entiteter (pipelines, data uppsättningar osv.) i en data fabrik. Om en utvecklare till exempel har åtkomst till en pipeline eller en datamängd ska utvecklaren kunna ha åtkomst till alla pipelines eller datamängder i datafabriken. Om du tycker att du behöver implementera många Azure-roller i en data fabrik tittar du på att distribuera en andra data fabrik.

-   Du kan inte publicera från privata grenar.

-   Du kan för närvarande inte vara värd för projekt på Bitbucket.

-   Du kan för närvarande inte exportera och importera aviseringar och matriser som parametrar. 

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Exempel skript för för-och efter distribution

Följande exempel skript kan användas för att stoppa utlösare före distribution och starta om dem efteråt. Skriptet innehåller också kod för att ta bort resurser som har tagits bort. Spara skriptet i en Azure DevOps git-lagringsplats och referera till den via en Azure PowerShell aktivitet som använder version 4. *.

När du kör ett skript för för distribution måste du ange en variant av följande parametrar i fältet **skript argument** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


När du kör ett skript efter distribution måste du ange en variant av följande parametrar i fältet **skript argument** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell-uppgift](media/continuous-integration-deployment/continuous-integration-image11.png)

Här är det skript som kan användas för för-och-distribution. IT-konton för borttagna resurser och resurs referenser.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
