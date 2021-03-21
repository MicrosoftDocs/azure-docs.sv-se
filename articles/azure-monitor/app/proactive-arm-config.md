---
title: Regel inställningar för smart identifiering – Azure Application insikter
description: Automatisera hantering och konfiguration av regler för smart identifiering av Azure Application Insights med Azure Resource Manager mallar
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: e3a7b71cd8975957754ba014ecc700484c27a6d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726129"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Hantera Application Insights regler för smart identifiering med Azure Resource Manager-mallar

Regler för smart identifiering i Application Insights kan hanteras och konfigureras med [Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md).
Den här metoden kan användas när du distribuerar nya Application Insights-resurser med Azure Resource Manager Automation, eller för att ändra inställningarna för befintliga resurser.

## <a name="smart-detection-rule-configuration"></a>Konfiguration av Smart identifierings regel

Du kan konfigurera följande inställningar för en regel för smart identifiering:
- Om regeln är aktive rad (Standardvärdet är **True**.)
- Om e-postmeddelanden ska skickas till användare som är kopplade till prenumerationens Rolls [övervaknings läsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) och [övervaknings deltagar](../../role-based-access-control/built-in-roles.md#monitoring-contributor) roller när en identifiering hittas (Standardvärdet är **True**.)
- Ytterligare e-postmottagare som bör få ett meddelande när en identifiering hittas.
    -  E-postkonfiguration är inte tillgänglig för regler för smart identifiering som är markerade som för _hands version_.

För att tillåta konfigurering av regel inställningarna via Azure Resource Manager är konfigurationen av Smart identifierings regeln nu tillgänglig som en inre resurs i Application Insights resurs med namnet **ProactiveDetectionConfigs**.
För maximal flexibilitet kan varje smart identifierings regel konfigureras med unika meddelande inställningar.

## <a name="examples"></a>Exempel

Nedan visas några exempel på hur du konfigurerar inställningarna för smart detekterings regler med hjälp av Azure Resource Manager mallar.
Alla exempel refererar till en Application Insights resurs med namnet _"Mina program"_ och till regeln för smart identifiering av långa beroende varaktigheter, som är internt med namnet _"longdependencyduration"_.
Se till att ersätta Application Insights resurs namn och ange det interna namnet för den aktuella Smart detekterings regeln. I tabellen nedan finns en lista över motsvarande interna Azure Resource Manager namn för varje smart identifierings regel.

### <a name="disable-a-smart-detection-rule"></a>Inaktivera en regel för smart identifiering

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Inaktivera sändning av e-postaviseringar för en smart identifierings regel

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Lägg till ytterligare e-postmottagare för en smart identifierings regel

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Namn för smart identifierings regel

Nedan visas en tabell med namn på Smart identifierings regler som visas i portalen, tillsammans med deras interna namn, som ska användas i Azure Resource Manager-mallen.

> [!NOTE]
> Regler för smart identifiering som marker ATS som _förhands granskning_ stöder inte e-postmeddelanden. Därför kan du bara ange egenskapen _Enabled_ för dessa regler. 

| Azure Portal regel namn | Internt namn
|:---|:---|
| Långsam sid inläsnings tid | slowpageloadtime |
| Långsam Server svars tid | slowserverresponsetime |
| Lång beroende varaktighet | longdependencyduration |
| Försämring av Server svars tid | degradationinserverresponsetime |
| Försämring i beroende varaktighet | degradationindependencyduration |
| Nedbrytning i allvarlighets grad för spårning (för hands version) | extension_traceseveritydetector |
| Onormal ökning av undantags volym (för hands version) | extension_exceptionchangeextension |
| Potentiell minnes läcka upptäcktes (förhands granskning) | extension_memoryleakextension |
| Eventuellt säkerhets problem upptäcktes (för hands version) | extension_securityextensionspackage |
| Onormal ökning av daglig data volym (förhands granskning) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Varnings regel för fel avvikelser

Den här Azure Resource Manager mallen visar hur du konfigurerar en varnings regel för fel avvikelser med allvarlighets graden 2.

> [!NOTE]
> Fel avvikelser är en global tjänst därför skapas en regel plats på den globala platsen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Den här Azure Resource Manager mallen är unik för varnings regeln för fel avvikelser och skiljer sig från de andra klassiska reglerna för smart identifiering som beskrivs i den här artikeln. Om du vill hantera fel avvikelser manuellt görs detta i Azure Monitor aviseringar medan alla andra regler för smart identifiering hanteras i fönstret för smart identifiering i användar gränssnittet.

## <a name="next-steps"></a>Nästa steg

Läs mer om att identifiera automatiskt:

- [Felavvikelser](./proactive-failure-diagnostics.md)
- [Minnes läckor](./proactive-potential-memory-leak.md)
- [Prestandaavvikelser](./proactive-performance-diagnostics.md)

