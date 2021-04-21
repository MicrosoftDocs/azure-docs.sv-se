---
title: Skapa, visa och hantera aktivitetsloggaviseringar i Azure Monitor
description: Skapa aktivitetsloggaviseringar med hjälp av Azure Portal, en Azure Resource Manager mall och Azure PowerShell.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 647378d7e93ab383441b363315a84cea8a5ab773
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772549"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Skapa, visa och hantera aktivitetsloggaviseringar med hjälp av Azure Monitor  

## <a name="overview"></a>Översikt

Aktivitetsloggaviseringar är de aviseringar som aktiveras när en ny aktivitetslogghändelse inträffar som matchar de villkor som anges i aviseringen.

De här aviseringarna gäller för Azure-resurser och kan skapas med hjälp av en Azure Resource Manager mall. De kan också skapas, uppdateras eller tas bort i Azure Portal. Normalt skapar du aktivitetsloggaviseringar för att få meddelanden när specifika ändringar sker i resurser i din Azure-prenumeration. Aviseringar är ofta begränsade till vissa resursgrupper eller resurser. Du kanske till exempel vill få ett meddelande när en virtuell dator i exempelresursgruppen **myProductionResourceGroup** tas bort. Eller så kanske du vill få ett meddelande om några nya roller har tilldelats till en användare i din prenumeration.

> [!IMPORTANT]
> Aviseringar om service health-meddelanden kan inte skapas via gränssnittet för skapande av aktivitetsloggaviseringar. Mer information om hur du skapar och använder service health-aviseringar finns i [Ta emot aktivitetsloggaviseringar för service health-meddelanden.](../../service-health/alerts-activity-log-service-notifications-portal.md)

När du skapar aviseringsregler ser du till att följande:

- Prenumerationen i omfånget skiljer sig inte från prenumerationen där aviseringen skapas.
- Kriterierna måste vara den nivå, status, anropare, resursgrupp, resurs-ID eller resurstypshändelsekategori som aviseringen har konfigurerats för.
- Endast ett "allOf"-villkor tillåts.
- "AnyOf" kan användas för att tillåta flera villkor över flera fält (till exempel om antingen fälten "status" eller "subStatus" är lika med ett visst värde). Observera att användningen av "AnyOf" för närvarande är begränsad till att skapa aviseringsregeln med hjälp av en ARM-malldistribution.
- "ContainsAny" kan användas för att tillåta flera värden i samma fält (till exempel om "åtgärd" är lika med "delete" eller "modify"). Observera att användningen av ContainsAny för närvarande är begränsad till att skapa aviseringsregeln med hjälp av en ARM-malldistribution.
- När kategorin är "administrativ" måste du ange minst ett av föregående villkor i aviseringen. Du får inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.
- Aviseringar kan inte skapas för händelser i aviseringskategorin för aktivitetsloggen.

## <a name="azure-portal"></a>Azure Portal

Du kan använda Azure Portal för att skapa och ändra aviseringsregler för aktivitetsloggen. Upplevelsen är integrerad med en Azure-aktivitetslogg för att säkerställa sömlös aviseringsgenerering för specifika händelser av intresse.

### <a name="create-with-the-azure-portal"></a>Skapa med Azure Portal

Använd följande procedur.

1. I Azure Portal du Övervaka   >  **aviseringar.**
2. Välj **Ny aviseringsregel** i det övre vänstra hörnet i **fönstret Aviseringar.**

     ![Ny aviseringsregel](media/alerts-activity-log/AlertsPreviewOption.png)

     Fönstret **Skapa** regel visas.

      ![Nya alternativ för aviseringsregel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Under **Definiera aviseringsvillkor** anger du följande information och väljer **Klar:**

   - **Aviseringsmål:** Om du vill visa och välja mål för den nya aviseringen använder du **Filtrera efter prenumeration** Filtrera efter  /  **resurstyp.** Välj den resurs eller resursgrupp i listan som visas.

     > [!NOTE]
     > 
     > Du kan bara [Azure Resource Manager](../../azure-resource-manager/management/overview.md) spårad resurs, resursgrupp eller en hel prenumeration för en aktivitetsloggsignal. 

     **Exempelvy för aviseringsmål**

     ![Välja mål](media/alerts-activity-log/select-target.png)

   - Under **Målvillkor väljer** du Lägg **till villkor**. Alla tillgängliga signaler för målet visas, vilket inkluderar dem från olika kategorier av **aktivitetsloggen**. Kategorinamnet läggs till i **övervakartjänstens** namn.

   - Välj signalen från listan med olika åtgärder som är möjliga för typen **Aktivitetslogg.**

     Du kan välja tidslinjen för logghistorik och motsvarande aviseringslogik för den här målsignalen:

     **Skärmen Lägg till villkor**

     ![Lägga till villkor](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  För att få en hög kvalitet och effektiva regler ber vi att lägga till minst ett ytterligare villkor i regler med signalen "Alla administrativa". 
     > Som en del av definitionen av aviseringen måste du fylla i någon av listrutan: "Händelsenivå", "Status" eller "Initierad av" och av den regeln blir mer specifik.

     - **Historiktid:** Händelser som är tillgängliga för den valda åtgärden kan ritas under de senaste 6, 12 eller 24 timmarna eller under den senaste veckan.

     - **Aviseringslogik:**

       - **Händelsenivå:** Händelsens allvarlighetsgrad: _Utförlig_, _Information,_ _Varning,_ _Fel_ eller _Kritisk._
       - **Status:** Status för händelsen: _Startade,_ _Misslyckades_ eller _Lyckades_.
       - **Händelse initierad av**: Kallas även anroparen. E-postadressen eller Azure Active Directory identifierare för den användare som utförde åtgärden.

       Det här exempel på signaldiagram har tillämpat aviseringslogiken:

       ![Valda kriterier](media/alerts-activity-log/criteria-selected.png)

4. Ange **följande information under** Definiera aviseringsinformation:

    - **Namn på aviseringsregel:** Namnet på den nya aviseringsregeln.
    - **Beskrivning**: Beskrivningen för den nya aviseringsregeln.
    - **Spara avisering till resursgrupp:** Välj den resursgrupp där du vill spara den nya regeln.

5. Under **Åtgärdsgrupp** går du till listrutan och anger den åtgärdsgrupp som du vill tilldela till den nya aviseringsregeln. Eller skapa [en ny åtgärdsgrupp och](./action-groups.md) tilldela den till den nya regeln. Om du vill skapa en ny grupp väljer **du + Ny grupp**.

6. Om du vill aktivera reglerna när du har skapat dem **väljer du Ja** för alternativet Aktivera regel **vid** skapande.
7. Välj **Skapa varningsregel**.

    Den nya aviseringsregeln för aktivitetsloggen skapas och ett bekräftelsemeddelande visas i det övre högra hörnet i fönstret.

    Du kan aktivera, inaktivera, redigera eller ta bort en regel. Läs mer om hur du hanterar aktivitetsloggregler.


En enkel liknelse för att förstå villkoren för vilka aviseringsregler som kan skapas i en aktivitetslogg är att utforska eller filtrera händelser via aktivitetsloggen [i Azure Portal](../essentials/activity-log.md#view-the-activity-log). På skärmen **Azure Monitor aktivitetslogg** kan du filtrera eller hitta den nödvändiga händelsen och sedan skapa en avisering med hjälp av knappen Lägg till **aktivitetsloggavisering.** Följ sedan steg 4 till och med 7 som tidigare visats.
    
 ![Lägga till en avisering från aktivitetsloggen](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visa och hantera i Azure Portal

1. I Azure Portal du Övervaka   >  **aviseringar.** Välj **Hantera aviseringsregler** i det övre vänstra hörnet i fönstret.

    ![Skärmbild som visar aktivitetsloggen med sökrutan markerad.](media/alerts-activity-log/manage-alert-rules.png)

    Listan över tillgängliga regler visas.

2. Sök efter aktivitetsloggregeln som ska ändras.

    ![Sök efter varningsregler för aktivitetslogg](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Du kan använda de tillgängliga _filtren_ Prenumeration, _Resursgrupp,_  _Resurs,_ _Signaltyp_ eller _Status_ för att hitta den aktivitetsregel som du vill redigera.

   > [!NOTE]
   > 
   > Du kan bara redigera **Beskrivning,** **Målvillkor** och **Åtgärdsgrupper**.

3. Välj regeln och dubbelklicka för att redigera regelalternativen. Gör de nödvändiga ändringarna och välj sedan **Spara.**

   ![Hantera aviseringsregler](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Du kan aktivera, inaktivera eller ta bort en regel. Välj lämpligt alternativ överst i fönstret när du har valt regeln enligt beskrivningen i steg 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Om du vill skapa en aviseringsregel för aktivitetsloggen Azure Resource Manager en mall skapar du en resurs av typen `microsoft.insights/activityLogAlerts` . Sedan fyller du i alla relaterade egenskaper. Här är en mall som skapar en aviseringsregel för aktivitetslogg:

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
Föregående JSON-exempel kan sparas som till exempel sampleActivityLogAlert.jspå i den här genomgången och kan distribueras med hjälp [av Azure Resource Manager i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

  > [!NOTE]
  > 
  > Observera att aktivitetsloggaviseringar på högsta nivå kan definieras är prenumeration.
  > Det innebär att det inte finns något alternativ för att definiera aviseringar för ett par prenumerationer. Därför bör definitionen vara avisering per prenumeration.

Följande fält är de alternativ som du kan använda i Azure Resource Manager-mallen för villkorsfälten: Observera att "Resource Health", "Advisor" och "Service Health" har extra egenskapsfält för sina särskilda fält. 
1. resourceId: Resurs-ID:t för den påverkade resursen i aktivitetslogghändelsen som aviseringen ska genereras för.
2. category: Kategorin för i aktivitetslogghändelsen. Exempel: Administrativ, ServiceHealth, ResourceHealth, Autoskala, Säkerhet, Rekommendation, Princip.
3. anropare: E-postadressen eller Azure Active Directory identifierare för den användare som utförde åtgärden för aktivitetslogghändelsen.
4. nivå: Nivån på aktiviteten i aktivitetslogghändelsen som aviseringen ska genereras på. Till exempel: Kritisk, Fel, Varning, Information, Utförlig.
5. operationName: Namnet på åtgärden i aktivitetslogghändelsen. Exempel: Microsoft.Resources/deployments/write
6. resourceGroup: Namnet på resursgruppen för den påverkade resursen i aktivitetslogghändelsen.
7. resourceProvider: [Förklaring av Azure-resursproviders och -typer.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0) En lista som mappar resursproviders till Azure-tjänster finns i [Resursproviders för Azure-tjänster.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)
8. status: Sträng som beskriver status för åtgärden i aktivitetshändelsen. Exempel: Startad, Pågår, Lyckades, Misslyckades, Aktiv, Löst
9. subStatus: Vanligtvis HTTP-statuskoden för motsvarande REST-anrop, men kan även innehålla andra strängar som beskriver en understatus.   Exempel: OK (HTTP-statuskod: 200), Skapad (HTTP-statuskod: 201), Godkänd (HTTP-statuskod: 202), Inget innehåll (HTTP-statuskod: 204), Felaktig begäran (HTTP-statuskod: 400), Inte Hittades (HTTP-statuskod: 404), konflikt (HTTP-statuskod: 409), internt serverfel (HTTP-statuskod: 500), Tjänsten är inte tillgänglig (HTTP-statuskod: 503), gateway-timeout (HTTP-statuskod: 504).
10. resourceType: Typen av resurs som påverkades av händelsen. Exempel: Microsoft.Resources/deployments

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
Mer information om aktivitetsloggfälten finns [här.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)



> [!NOTE]
> Det kan ta upp till 5 minuter innan den nya aktivitetsloggaviseringsregeln blir aktiv.

## <a name="rest-api"></a>REST-API 
[API:Azure Monitor aktivitetsloggaviseringar](/rest/api/monitor/activitylogalerts) är en REST API. Det är helt kompatibelt med Azure Resource Manager REST API. Den kan användas via PowerShell med hjälp av Resource Manager-cmdleten eller Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Distribuera Resource Manager med PowerShell
Om du vill använda PowerShell för att Resource Manager exempelmallen som [visas Azure Resource Manager föregående](#azure-resource-manager-template) mallavsnitt använder du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

där sampleActivityLogAlert.parameters.jspå innehåller de värden som anges för de parametrar som behövs för att skapa aviseringsregeln.

### <a name="use-activity-log-powershell-cmdlets"></a>Använda PowerShell-cmdlets för aktivitetslogg

Aktivitetsloggaviseringar har dedikerade PowerShell-cmdlets tillgängliga:

- [Set-AzActivityLogAlert:](/powershell/module/az.monitor/set-azactivitylogalert)Skapar en ny aktivitetsloggavisering eller uppdaterar en befintlig aktivitetsloggavisering.
- [Get-AzActivityLogAlert:](/powershell/module/az.monitor/get-azactivitylogalert)Hämtar en eller flera aktivitetsloggaviseringsresurser.
- [Enable-AzActivityLogAlert:](/powershell/module/az.monitor/enable-azactivitylogalert)Aktiverar en befintlig aktivitetsloggavisering och anger dess taggar.
- [Disable-AzActivityLogAlert:](/powershell/module/az.monitor/disable-azactivitylogalert)Inaktiverar en befintlig aktivitetsloggavisering och anger dess taggar.
- [Remove-AzActivityLogAlert:](/powershell/module/az.monitor/remove-azactivitylogalert)Tar bort en aktivitetsloggavisering.

## <a name="azure-cli"></a>Azure CLI

Dedikerade Azure CLI-kommandon under set [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) är tillgängliga för hantering av aviseringsregler för aktivitetslogg.

Om du vill skapa en ny aviseringsregel för aktivitetsloggen använder du följande kommandon i den här ordningen:

1. [az monitor activity-log alert create:](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_create)Skapa en ny resurs för aviseringsregel för aktivitetslogg.
1. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope): Lägg till omfång för den skapade aktivitetsloggaviseringsregeln.
1. [az monitor activity-log alert action-group:](/cli/azure/monitor/activity-log/alert/action-group)Lägg till en åtgärdsgrupp i aviseringsregeln för aktivitetsloggen.

Om du vill hämta en regelresurs för aktivitetsloggaviseringar använder du Azure [CLI-kommandot az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_show
). Om du vill visa alla resurser för aktivitetsloggaviseringsregel i en resursgrupp använder [du az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_list).
Resurser för aktivitetsloggaviseringsregel kan tas bort med hjälp av Azure [CLI-kommandot az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_delete).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer [om webhook-schema för aktivitetsloggar.](./activity-log-alerts-webhook.md)
- Läs en [översikt över aktivitetsloggar.](./activity-log-alerts.md)
- Läs mer om [åtgärdsgrupper.](./action-groups.md)  
- Läs mer om [service health-meddelanden.](../../service-health/service-notifications.md)
