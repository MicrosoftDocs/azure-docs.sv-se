---
title: Distribution av hälso integrering – Azure Deployment Manager
description: Beskriver hur du distribuerar en tjänst i flera regioner med Azure Deployment Manager. Den visar en säker distributions metod för att kontrol lera stabiliteten för din distribution innan den distribueras till alla regioner.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: 8e13f18ef9c2ec71a351d8a80d72d600bf8e9715
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607413"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introducera distribution av hälso integration till Azure Deployment Manager (offentlig för hands version)

Med [Azure Deployment Manager](./deployment-manager-overview.md) kan du utföra stegvisa distributioner av Azure Resource Manager-resurser. Resurserna distribueras enligt region på ett ordnat sätt. Den integrerade hälso kontrollen av Azure Deployment Manager kan övervaka distributioner och automatiskt stoppa problematiska distributioner, så att du kan felsöka och minska storleken på påverkan. Den här funktionen kan minska tillgängligheten för tjänsten som orsakas av regressioner i uppdateringar.

## <a name="health-monitoring-providers"></a>Leverantörer av hälso övervakning

För att kunna göra hälso integration så enkel som möjligt har Microsoft arbetat med några av de främsta hälso övervaknings företagen för tjänsten för att tillhandahålla en enkel kopierings-och Inklistrings lösning för att integrera hälso kontroller med dina distributioner. Om du inte redan använder en hälso övervakning är dessa bra lösningar som börjar med:

| ![Azure Deployment Manager Health Monitor-Provider Azure Monitor](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![Azure Deployment Manager Health Monitor-Provider Datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Azure Deployment Manager Health Monitor-Provider Site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Azure Deployment Manager Health Monitor-Provider Wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Monitor är Microsofts kompletta modell för att kontrol lera om det finns moln intern & hybrid övervakning och analys. |Datadog, den ledande plattformen för övervakning och analys för moderna moln miljöer. Se [hur Datadog integreras med Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, den allt-i-ett-privata och offentliga moln tjänster, övervaknings lösning. Se [hur Site24x7 integreras med Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, övervaknings-och analys plattformen för program miljöer med flera moln. Se [hur Wavefront integreras med Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Hur tjänstens hälsa fastställs

[Hälso övervaknings leverantörer](#health-monitoring-providers) erbjuder flera mekanismer för att övervaka tjänster och meddela dig om eventuella problem med tjänstens hälsa. [Azure Monitor](../../azure-monitor/overview.md) är ett exempel på ett sådant erbjudande. Azure Monitor kan användas för att skapa aviseringar när vissa tröskelvärden överskrids. Det kan till exempel vara minnes-och CPU-användning utöver förväntade nivåer när du distribuerar en ny uppdatering för tjänsten. När du får ett meddelande kan du vidta åtgärder.

Dessa hälso leverantörer erbjuder vanligt vis REST-API: er så att statusen för tjänstens övervakare kan undersökas program mässigt. REST-API: erna kan antingen komma tillbaka med en enkel felfritt/skadad signal (som bestäms av HTTP-svarskod) och/eller med detaljerad information om de signaler den tar emot.

Med det nya *healthCheck* -steget i Azure Deployment Manager kan du deklarera http-koder som indikerar en felfri tjänst eller, för mer komplexa rest-resultat, och du kan även ange reguljära uttryck som, om de matchar, visar ett felfritt svar.

Flödet för att få installations programmet med Azure Deployment Managers hälso kontroller:

1. Skapa hälso övervakare via en hälso tjänst leverantör som du väljer.
1. Skapa en eller flera healthCheck-steg som en del av distributionen av Azure Deployment Manager. Fyll i healthCheck-stegen med följande information:

    1. URI: n för REST API för dina hälso övervakare (enligt definitionen av hälso tjänst leverantören).
    1. Autentiseringsinformation. För närvarande stöds inte autentisering med API-nyckel format. För Azure Monitor ska autentiseringstypen anges som – "RolloutIdentity" eftersom den användare som tilldelats hanterad identitet som används för distribution av Azure Deployment Manager är i Azure Monitor.
    1. [HTTP-statuskod](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) eller reguljära uttryck som definierar ett felfritt svar. Observera att du kan ange reguljära uttryck som alla måste matcha för att svaret ska anses vara felfritt, eller så kan du ange uttryck som måste matchas för att svaret ska anses vara felfritt. Båda metoderna stöds.

    Följande JSON är ett exempel på hur du integrerar Azure Monitor med Azure Deployment Manager som utnyttjar RolloutIdentity och fastställer hälso kontroll om en distribution fortsätter om det inte finns några aviseringar. Den enda Azure Monitor-API: n som stöds [– Hämta alla](/rest/api/monitor/alertsmanagement/alerts/getall).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
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
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    Följande JSON är ett exempel på alla andra hälso övervaknings leverantörer:

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

1. Anropa healthCheck-stegen vid lämplig tidpunkt i distributionen av Azure Deployment Manager. I följande exempel anropas ett hälso kontroll steg i **postDeploymentSteps** av **stepGroup2**.

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

Information om ett exempel finns i [Självstudier: använda hälso kontroll i Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Faser av en hälso kontroll

I det här läget vet Azure Deployment Manager att du kan fråga efter hälso tillståndet för tjänsten och i vilka faser i distributionen du ska göra det. Azure Deployment Manager tillåter dock också djupgående konfiguration av tiden för de här kontrollerna. Ett healthCheck-steg körs i tre sekventiella faser som alla har konfigurerbara varaktigheter:

1. Vänta

    1. När en distributions åtgärd har slutförts kan virtuella datorer startas om, konfigureras om baserat på nya data eller till och med startas för första gången. Det tar också tid för tjänster att börja sända hälso signaler som ska aggregeras av hälso övervaknings leverantören i något användbart. Under den här tumultuous-processen kan det vara klokt att kontrol lera tjänstens hälso tillstånd eftersom uppdateringen ännu inte har nått ett stabilt tillstånd. Tjänsten kan vara svängning mellan felfria och felaktiga tillstånd när resurserna är lösta.
    1. Under vänte fasen övervakas inte tjänstens hälsa. Detta används för att tillåta att de distribuerade resurserna är i gång innan hälso kontroll processen påbörjas.
1. Elastiska

    1. Eftersom det är omöjligt att känna till i samtliga fall hur lång tid det tar innan det blir stabilt, kan den elastiska fasen för en flexibel tids period mellan när resurserna är potentiellt instabila och när de krävs för att upprätthålla ett felfritt stabilt tillstånd.
    1. När den elastiska fasen börjar påbörjar Azure Deployment Manager avsökningen av den angivna REST-slutpunkten för tjänstens hälsa regelbundet. Avsöknings intervallet kan konfigureras.
    1. Om hälso övervakaren kommer tillbaka med signaler som anger att tjänsten inte är felfri, ignoreras dessa signaler, den elastiska fasen fortsätter och avsökningen fortsätter.
    1. Så snart hälso övervakaren kommer tillbaka med signaler som indikerar att tjänsten är felfri, slutar den elastiska fasen och HealthyState-fasen börjar.
    1. Den varaktighet som anges för den elastiska fasen är alltså den längsta tid som kan användas för att söka efter tjänstens hälsa innan ett felfritt svar anses vara obligatoriskt.
1. HealthyState

    1. Under HealthyState-fasen avsöker service hälsan hela tiden med samma intervall som den elastiska fasen.
    1. Tjänsten förväntas upprätthålla felfria signaler från hälso övervaknings leverantören för hela den angivna tiden.
    1. Om ett svar som inte är felfritt har identifierats stoppar Azure Deployment Manager hela distributionen och returnerar REST-svaret som innehåller felaktiga tjänst signaler.
    1. När HealthyState-varaktigheten har upphört är healthCheck slutförd och distributionen fortsätter till nästa steg.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du integrerar hälso övervakning i Azure Deployment Manager. Fortsätt till nästa artikel om du vill lära dig hur du distribuerar med Deployment Manager.

> [!div class="nextstepaction"]
> [Självstudie: integrera hälso kontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)