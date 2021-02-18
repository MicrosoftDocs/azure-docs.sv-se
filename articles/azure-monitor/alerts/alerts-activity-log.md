---
title: Skapa, Visa och hantera aktivitets logg aviseringar i Azure Monitor
description: Skapa aktivitets logg aviseringar med hjälp av Azure Portal, en Azure Resource Manager mall och Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 83023cca6b034ee0e9acddfa081f09eb47b9fb1e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625870"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor  

## <a name="overview"></a>Översikt

Aktivitets logg aviseringar är de aviseringar som aktive ras när en ny aktivitets logg händelse inträffar som matchar de villkor som anges i aviseringen.

Dessa aviseringar är för Azure-resurser och kan skapas med hjälp av en Azure Resource Manager mall. De kan också skapas, uppdateras eller tas bort i Azure Portal. Normalt skapar du aktivitets logg aviseringar för att ta emot meddelanden när vissa ändringar sker i resurser i din Azure-prenumeration. Aviseringar är ofta begränsade till specifika resurs grupper eller resurser. Du kanske till exempel vill bli meddelad när en virtuell dator i exempel resurs gruppen **myProductionResourceGroup** tas bort. Eller så kanske du vill bli meddelad om eventuella nya roller har tilldelats till en användare i din prenumeration.

> [!IMPORTANT]
> Aviseringar om service Health-aviseringar kan inte skapas via gränssnittet för skapande av aktivitets logg aviseringar. Om du vill veta mer om hur du skapar och använder meddelanden om tjänst hälsa läser du [ta emot aktivitets logg aviseringar för meddelanden om tjänst hälsa](../../service-health/alerts-activity-log-service-notifications-portal.md).

Kontrol lera följande när du skapar varnings regler:

- Prenumerationen i omfånget skiljer sig inte från prenumerationen där aviseringen skapades.
- Kriterierna måste vara nivå, status, anropare, resurs grupp, resurs-ID eller händelse kategori för resurs typ som aviseringen har kon figurer ATS för.
- Det finns inget "anyOf"-villkor eller kapslade villkor i aviserings konfigurationens JSON. I princip tillåts endast ett "allOf"-villkor utan ytterligare "allOf"-eller "anyOf"-villkor.
- När kategorin är "administrativ" måste du ange minst ett av de föregående kriterierna i aviseringen. Du får inte skapa en avisering som aktive ras varje gång en händelse skapas i aktivitets loggarna.
- Det går inte att skapa aviseringar för händelser i aviserings kategorin för aktivitets loggen.

## <a name="azure-portal"></a>Azure Portal

Du kan använda Azure Portal för att skapa och ändra varnings regler för aktivitets loggen. Upplevelsen är integrerad med en Azure aktivitets logg för att säkerställa sömlös skapande av aviseringar för specifika händelser av intresse.

### <a name="create-with-the-azure-portal"></a>Skapa med Azure Portal

Använd följande procedur.

1. I Azure Portal väljer du **övervaka**  >  **aviseringar**.
2. Välj **ny varnings regel** i det övre vänstra hörnet i **aviserings** fönstret.

     ![Ny varnings regel](media/alerts-activity-log/AlertsPreviewOption.png)

     Fönstret **Skapa regel** visas.

      ![Nya alternativ för aviserings regel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Under **definiera aviserings villkor** anger du följande information och väljer sedan **slutförd**:

   - **Aviserings mål:** Om du vill visa och välja målet för den nya aviseringen använder du **Filtrera efter prenumerations**  /  **filter efter resurs typ**. Välj den resurs eller resurs grupp i listan som visas.

     > [!NOTE]
     > 
     > Du kan bara välja [Azure Resource Manager](../../azure-resource-manager/management/overview.md) spårad resurs, resurs grupp eller en hel prenumeration för en aktivitets logg signal. 

     **Exempel vy för aviserings mål**

     ![Välja mål](media/alerts-activity-log/select-target.png)

   - Under **mål kriterier** väljer du **Lägg till kriterier**. Alla tillgängliga signaler för målet visas, vilket inkluderar de från olika kategorier av **aktivitets loggen**. Kategori namnet läggs till i **övervaknings tjänstens** namn.

   - Välj den signal i listan som visas för olika åtgärder som är möjliga för **aktivitets loggen** typ.

     Du kan välja tids linjen för logg historiken och motsvarande aviserings logik för den här mål signalen:

     **Fönstret Lägg till villkor**

     ![Lägg till villkor](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  För att få en hög kvalitets-och effektiv regel ber vi dig lägga till minst ett villkor för regler med signalen "alla administrativa". 
     > Som en del av aviserings definitionen måste du fylla i en av de nedrullningsbara List rutorna: "händelse nivå", "status" eller "initierad av" och genom att regeln är mer unik.

     - **Historik tid**: händelser som är tillgängliga för den valda åtgärden kan ritas under de senaste 6, 12 eller 24 timmarna eller under den senaste veckan.

     - **Aviserings logik**:

       - **Händelse nivå**: händelsens allvarlighets grad: _utförlig_ _information_, _Varning_, _fel_ eller _kritisk_.
       - **Status**: status för händelsen: _startade_, _misslyckades_ eller _lyckades_.
       - **Händelse som initieras av**: kallas även för anroparen. E-postadressen eller Azure Active Directory identifieraren för den användare som utförde åtgärden.

       I det här exempel diagrammet används varnings logiken:

       ![Valda villkor](media/alerts-activity-log/criteria-selected.png)

4. Under **definiera aviserings information** anger du följande information:

    - **Namn på varnings regel**: namnet på den nya varnings regeln.
    - **Beskrivning**: beskrivningen av den nya varnings regeln.
    - **Spara avisering till resurs grupp**: Välj den resurs grupp där du vill spara den nya regeln.

5. Under **Åtgärds grupp** i den nedrullningsbara menyn anger du den åtgärds grupp som du vill tilldela till den nya varnings regeln. Du kan också [skapa en ny åtgärds grupp](../platform/action-groups.md) och tilldela den till den nya regeln. Om du vill skapa en ny grupp väljer du **+ ny grupp**.

6. Om du vill aktivera reglerna efter att du har skapat dem väljer du **Ja** för alternativet **Aktivera regel vid skapande** .
7. Välj **Skapa varningsregel**.

    Den nya varnings regeln för aktivitets loggen skapas och ett bekräftelse meddelande visas i det övre högra hörnet i fönstret.

    Du kan aktivera, inaktivera, redigera eller ta bort en regel. Läs mer om hur du hanterar aktivitets logg regler.


En enkel analoghet för att förstå villkor för vilka aviserings regler som kan skapas i en aktivitets logg är att utforska eller filtrera händelser via [aktivitets loggen i Azure Portal](../essentials/activity-log.md#view-the-activity-log). På skärmen **Azure Monitor-aktivitets logg** kan du filtrera eller hitta den nödvändiga händelsen och sedan skapa en avisering med hjälp av knappen **Lägg till aktivitets logg avisering** . Följ sedan steg 4 till 7 enligt ovan.
    
 ![Lägg till avisering från aktivitets loggen](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visa och hantera i Azure Portal

1. I Azure Portal väljer du **övervaka**  >  **aviseringar**. Välj **Hantera aviserings regler** i det övre vänstra hörnet i fönstret.

    ![Skärm bild som visar aktivitets loggen med sökrutan markerad.](media/alerts-activity-log/manage-alert-rules.png)

    Listan över tillgängliga regler visas.

2. Sök efter den aktivitets logg regel som ska ändras.

    ![Aviserings regler för Sök aktivitets logg](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Du kan använda tillgängliga filter, _prenumeration_, _resurs grupp_,  _resurs_, _signal typ_ eller _status_ för att hitta den aktivitets regel som du vill redigera.

   > [!NOTE]
   > 
   > Du kan bara redigera **Beskrivning**, **mål kriterier** och **Åtgärds grupper**.

3. Välj regeln och dubbelklicka om du vill redigera regel alternativen. Gör de ändringar som krävs och välj sedan **Spara**.

   ![Hantera aviseringsregler](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Du kan aktivera, inaktivera eller ta bort en regel. Välj lämpligt alternativ överst i fönstret när du har valt regeln enligt beskrivningen i steg 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Om du vill skapa en aktivitets logg aviserings regel med hjälp av en Azure Resource Manager mall skapar du en resurs av typen `microsoft.insights/activityLogAlerts` . Fyll sedan i alla relaterade egenskaper. Här är en mall som skapar en aviserings regel för aktivitets logg:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Det tidigare JSON-exemplet kan sparas som, till exempel sampleActivityLogAlert.jsför den här genom gången och kan distribueras med hjälp av [Azure Resource Manager i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

  > [!NOTE]
  > 
  > Observera att det går att definiera aktivitets logg aviseringar på högsta nivån för prenumerationen.
  > Detta innebär att det inte finns något alternativ för att definiera aviseringar på några prenumerationer, och därför bör definitionen vara en avisering per prenumeration.

Följande fält är de alternativ som du kan använda i Azure Resource Manager mall för villkors fälten: Observera att "Resource Health", "Advisor" och "Service Health" har extra egenskaps fält för sina särskilda fält. 
1. resourceId: resurs-ID för den påverkade resursen i aktivitets logg händelsen som aviseringen ska genereras på.
2. Kategori: kategorin för i aktivitets logg händelsen. Till exempel: administrativ, ServiceHealth, ResourceHealth, autoskalning, säkerhet, rekommendation, policy.
3. anropare: e-postadressen eller Azure Active Directory identifieraren för den användare som utförde åtgärden för aktivitets logg händelsen.
4. nivå: aktivitetens nivå i aktivitets logg händelsen som aviseringen ska genereras på. Exempel: kritisk, fel, varning, information, utförligt.
5. operationName: namnet på åtgärden i aktivitets logg händelsen. Exempel: Microsoft. Resources/distributioner/Write
6. resourceGroup: namnet på resurs gruppen för den påverkade resursen i aktivitets logg händelsen.
7. resourceProvider: [Azure-resurs leverantörer och typer förklaring](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). En lista som mappar resurs leverantörer till Azure-tjänster finns i [Resource providers för Azure-tjänster](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: sträng som beskriver status för åtgärden i aktivitets händelsen. Till exempel: startad, pågår, lyckades, misslyckades, aktiv, löst
9. under status: vanligt vis HTTP-statuskod för motsvarande REST-anrop, men kan även innehålla andra strängar som beskriver en under status.   Till exempel: OK (HTTP-status kod: 200), skapad (HTTP-status kod: 201), godkänd (HTTP-status kod: 202), ingen innehålls (HTTP-status kod: 204), felaktig begäran (HTTP-status kod: 400), hittades inte (HTTP-status kod: 404), konflikt (HTTP-status kod: 409), internt Server fel (HTTP-status kod: 500), tjänsten är inte tillgänglig (HTTP-status kod: 503), Gateway-timeout (HTTP-statuskod
10. resourceType: den typ av resurs som påverkades av händelsen. Exempel: Microsoft. Resources/distributions

Exempel:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Mer information om aktivitets loggs fälten finns [här](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Det kan ta upp till 5 minuter innan den nya varnings regeln om aktivitets logg aktive ras.

## <a name="rest-api"></a>REST-API 
[Azure Monitor aktivitets logg aviserings-API](/rest/api/monitor/activitylogalerts) är ett REST API. Den är helt kompatibel med Azure Resource Manager REST API. Den kan användas via PowerShell med Resource Manager-cmdleten eller Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Distribuera Resource Manager-mallen med PowerShell
Om du vill använda PowerShell för att distribuera exempel Resource Manager-mallen som visas i avsnittet föregående [Azure Resource Manager mall](#azure-resource-manager-template) , använder du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

där sampleActivityLogAlert.parameters.jspå innehåller värdena för de parametrar som krävs för att skapa en varnings regel.

### <a name="use-activity-log-powershell-cmdlets"></a>Använda PowerShell-cmdletar för aktivitets logg

Aktivitets logg aviseringar har dedikerade PowerShell-cmdlets tillgängliga:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): skapar en ny aktivitets logg avisering eller uppdaterar en befintlig aktivitets logg avisering.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): hämtar en eller flera aktivitets loggs aviserings resurser.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): aktiverar en befintlig aktivitets logg avisering och anger dess taggar.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): inaktiverar en befintlig aktivitets logg avisering och anger dess taggar.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): tar bort en aktivitets logg avisering.

## <a name="azure-cli"></a>Azure CLI

Dedikerade Azure CLI-kommandon under uppsättningen ange [AZ övervaka aktivitet – logg aviseringen](/cli/azure/monitor/activity-log/alert) är tillgängliga för hantering av varnings regler för aktivitets loggen.

Om du vill skapa en ny varnings regel för aktivitets loggen använder du följande kommandon i den här ordningen:

1. [AZ övervaka aktivitet – logg avisering skapa](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): skapa en ny resurs för aktivitets logg aviserings regel.
1. [AZ övervaka aktivitet – logg aviserings omfattning](/cli/azure/monitor/activity-log/alert/scope): Lägg till omfattning för aviserings regeln för den skapade aktivitets loggen.
1. [AZ övervaka aktivitet – logg aviserings åtgärd – grupp](/cli/azure/monitor/activity-log/alert/action-group): Lägg till en åtgärds grupp i aktivitets loggens aviserings regel.

Om du vill hämta en resurs för varnings regler för aktivitets loggen använder du Azure CLI [-kommandot AZ övervaka aktivitet – logg avisering Visa](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Om du vill visa alla resurser för aktivitets logg aviserings regler i en resurs grupp använder du [AZ övervaka aktivitet – logg aviserings lista](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Aktivitets logg aviserings regel resurser kan tas bort med hjälp av Azure CLI [-kommandot AZ övervaka aktivitet – logg avisering ta bort](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [webhook-scheman för aktivitets loggar](./activity-log-alerts-webhook.md).
- Läs en [Översikt över aktivitets loggar](./activity-log-alerts.md).
- Läs mer om [Åtgärds grupper](../platform/action-groups.md).  
- Läs mer om [meddelanden om tjänst hälsa](../../service-health/service-notifications.md).

