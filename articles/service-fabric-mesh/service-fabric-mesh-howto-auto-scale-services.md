---
title: Skala automatiskt en app som körs i Azure Service Fabric nät
description: Lär dig hur du konfigurerar principer för automatisk skalning för tjänsterna i ett Service Fabric nätprogram.
author: georgewallace
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: a707e3601bb24b2d5c2aa9402edff4a2e8803033
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625912"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Skapa principer för autoskalning för ett Service Fabric nätprogram

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

En av de största fördelarna med att distribuera program till Service Fabric nät är möjligheten att enkelt skala dina tjänster i eller ut. Detta bör användas för att hantera olika belastnings mängder på dina tjänster eller förbättra tillgängligheten. Du kan skala dina tjänster manuellt i eller ut eller konfigurera principer för automatisk skalning.

Med [automatisk skalning](service-fabric-mesh-scalability.md#autoscaling-service-instances) kan du skala upp antalet tjänst instanser dynamiskt (vågrät skalning). Automatisk skalning ger bra elastiskhet och möjliggör etablering eller borttagning av tjänst instanser baserat på processor-eller minnes användning.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Alternativ för att skapa en princip för automatisk skalning, utlösare och mekanism
En princip för automatisk skalning definieras för varje tjänst som du vill skala. Principen definieras i antingen YAML-tjänstens resurs fil eller i mallen för JSON-distribution. Varje skalnings princip består av två delar: en utlösare och en skalnings funktion.

Utlösaren definierar när en princip för automatisk skalning anropas.  Ange typen av utlösare (genomsnittlig inläsning) och måttet som ska övervakas (CPU eller minne).  De övre och nedre tröskelvärdena för inläsning anges i procent. Skalnings intervallet definierar hur ofta den angivna användningen (i sekunder) ska kontrol leras (till exempel Genomsnittlig CPU-belastning) över alla aktuella distribuerade tjänst instanser.  Mekanismen utlöses när det övervakade måttet sjunker under den nedre tröskeln eller ökar över det övre tröskelvärdet.  

Skalnings mekanismen definierar hur du utför skalnings åtgärden när principen utlöses.  Ange typen av mekanism (Lägg till/ta bort replik), antalet lägsta och högsta antal repliker (som heltal).  Antalet tjänst repliker skalas aldrig under det lägsta antalet eller över det högsta antalet.  Ange också skalnings ökningen som ett heltal, vilket är antalet repliker som ska läggas till eller tas bort i en skalnings åtgärd.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definiera en princip för automatisk skalning i en JSON-mall

I följande exempel visas en princip för automatisk skalning i en mall för JSON-distribution.  Principen för automatisk skalning deklareras i en egenskap för tjänsten som ska skalas.  I det här exemplet definieras en PROCESSORs genomsnittliga belastnings utlösare.  Mekanismen aktive ras om den genomsnittliga CPU-belastningen för alla distribuerade instanser sjunker under 0,2 (20%) eller hamnar ovanför 0,8 (80%).  CPU-belastningen kontrol leras var 60 sekund.  Skalnings mekanismen definieras för att lägga till eller ta bort instanser om principen utlöses.  Tjänst instanser läggs till eller tas bort i steg om en.  Ett minsta antal instanser av ett och maximalt antal instanser av 40 har också definierats.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definiera en princip för autoskalning i en tjänst. yaml-resurs fil
I följande exempel visas en princip för automatisk skalning i en tjänst resurs fil (YAML).  Principen för automatisk skalning deklareras som en egenskap för tjänsten som ska skalas.  I det här exemplet definieras en PROCESSORs genomsnittliga belastnings utlösare.  Mekanismen aktive ras om den genomsnittliga CPU-belastningen för alla distribuerade instanser sjunker under 0,2 (20%) eller hamnar ovanför 0,8 (80%).  CPU-belastningen kontrol leras var 60 sekund.  Skalnings mekanismen definieras för att lägga till eller ta bort instanser om principen utlöses.  Tjänst instanser läggs till eller tas bort i steg om en.  Ett minsta antal instanser av ett och maximalt antal instanser av 40 har också definierats.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skalar en tjänst manuellt](service-fabric-mesh-tutorial-template-scale-services.md)
