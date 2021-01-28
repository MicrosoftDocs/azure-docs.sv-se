---
title: Migrera en Azure Monitor Application Insights klassisk resurs till en arbets yta-baserad resurs | Microsoft Docs
description: Lär dig mer om de steg som krävs för att uppgradera din Azure Monitor Application Insights Classic-resurs till den nya arbets ytans-baserade modellen.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5316bf5b919fe8b24ea1dd601214df62aa034f37
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945103"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Migrera till arbets ytans baserade Application Insights resurser

Den här guiden vägleder dig genom processen att migrera en klassisk Application Insights-resurs till en arbets yta-baserad resurs. Arbets ytans resurser stöder fullständig integrering mellan Application Insights och Log Analytics. Arbets ytans resurser skickar Application Insights telemetri till en gemensam Log Analytics arbets yta, vilket ger dig till gång till [de senaste funktionerna i Azure Monitor](#new-capabilities) samtidigt som program-, infrastruktur-och plattforms loggar hålls på en enda konsol IDE rad plats.

Arbets ytans resurser möjliggör gemensam Azure-rollbaserad åtkomst kontroll (Azure RBAC) i dina resurser och eliminerar behovet av frågor mellan appar/arbets ytor.

**Arbets ytans baserade resurser är för närvarande tillgängliga i alla kommersiella regioner och Azure amerikanska myndigheter**

## <a name="new-capabilities"></a>Nya funktioner

Med arbets ytans baserade Application Insights kan du dra nytta av alla de senaste funktionerna i Azure Monitor och Log Analytics inklusive:

* [Kundhanterade nycklar (CMK)](../platform/customer-managed-keys.md) tillhandahåller kryptering i vila för dina data med krypterings nycklar som bara du har åtkomst till.
* Med [Azures privata länk](../platform/private-link-security.md) kan du på ett säkert sätt länka Azure PaaS-tjänster till ditt virtuella nätverk med hjälp av privata slut punkter.
* [Ta med din egen lagring (BYOS) för profiler och Snapshot debugger](./profiler-bring-your-own-storage.md) ger dig fullständig kontroll över principen för kryptering vid vila, livs längds hanterings principen och nätverks åtkomst för alla data som är kopplade till Application Insights Profiler och Snapshot debugger. 
* Med [kapacitet reservations nivåer](../platform/manage-cost-storage.md#pricing-model) kan du spara så mycket som 25% jämfört med priset betala per användning. 
* Snabbare data inmatning via Log Analytics strömning.

## <a name="migration-process"></a>Migreringsprocessen

När du migrerar till en arbets yta-baserad resurs överförs inga data från den klassiska resursens lagring till den nya arbets ytan-baserade lagringen. Om du väljer att migrera kommer i stället att ändra den plats där nya data skrivs till en Log Analytics-arbetsyta samtidigt som åtkomsten till dina klassiska resurs data bevaras. 

Dina klassiska resurs data behålls och omfattas av inställningarna för kvarhållning på den klassiska Application Insights-resursen. Alla nya data inmatnings flyttning kommer att omfattas av [inställningarna för kvarhållning](../platform/manage-cost-storage.md#change-the-data-retention-period) av den associerade Log Analytics-arbetsytan, som också har stöd för [olika bevarande inställningar efter datatyp](../platform/manage-cost-storage.md#retention-by-data-type).
Migreringsprocessen är **permanent och kan inte ångras**. När du migrerar en resurs till arbets ytans baserade Application Insights är det alltid en arbets ytans-baserade resurs. När du migrerar kan du dock ändra mål arbets ytan så ofta som det behövs. 

> [!NOTE]
> Data inmatning och kvarhållning för arbets ytans baserade Application Insights resurser [faktureras via arbets ytan Log Analytics](../platform/manage-cost-storage.md) där data finns. Om du har valt data kvarhållning som är större än 90 dagar på data som matas in i den klassiska Application Insights-resursen före migreringen, kommer datakvarhållning fortsätta att debiteras genom den Application Insights resursen. [Läs mer]( ./pricing.md#workspace-based-application-insights) om fakturering för arbets ytans baserade Application Insights-resurser.

Om du inte behöver migrera en befintlig resurs och istället vill skapa en ny arbets yta-baserad Application Insights resurs använder du [guiden skapa arbets yta](create-workspace-resource.md).

## <a name="pre-requisites"></a>Förutsättningar 

- En Log Analytics-arbetsyta med åtkomst kontrol läget inställt på **`use resource or workspace permissions`** inställningen. 

    - Arbets ytans baserade Application Insights resurser är inte kompatibla med arbets ytor som har angetts till den dedikerade **`workspace based permissions`** inställningen. Om du vill veta mer om åtkomst kontroll för Log Analytics arbets yta läser du avsnittet [Log Analytics konfigurera åtkomst kontroll läge](../platform/manage-access.md#configure-access-control-mode)

    - Om du inte redan har en befintlig Log Analytics arbets yta kan du [läsa dokumentationen för att skapa Log Analytics arbets ytor](../learn/quick-create-workspace.md).
    
- Kontinuerlig export stöds inte för arbets ytans resurser och måste inaktive ras.
När migreringen är klar kan du använda [diagnostikinställningar](../platform/diagnostic-settings.md) för att konfigurera data arkivering till ett lagrings konto eller strömma till Azure Event Hub.  

- Kontrol lera de aktuella inställningarna för kvarhållning under **allmän**  >  **användning och uppskattade kostnader**  >  **data lagring** för din Log Analytics-arbetsyta. Den här inställningen påverkar hur länge alla nya inmatade data lagras när du migrerar din Application Insights-resurs. Om du för närvarande lagrar Application Insights data som är längre än standardvärdet 90 dagar och vill behålla denna större kvarhållningsperiod, kan du behöva justera inställningarna för kvarhållning av arbets yta.

## <a name="migrate-your-resource"></a>Migrera din resurs

Det här avsnittet beskriver hur du migrerar en klassisk Application Insights-resurs till den nya arbets ytans baserade resurs typ.

1. Från Application Insights resurs väljer du **Egenskaper** under **Konfigurera** rubrik i den vänstra meny raden.

    ![Egenskaper markerade i röd ruta](./media/convert-classic-resource/properties.png)

2. Välj **`Migrate to Workspace-based`**.
    
     ![Knappen migrera resurs](./media/convert-classic-resource/migrate.png)

3. Välj den Log Analytics arbets yta där du vill att all framtida inmatnings Application Insights telemetri ska lagras.

     ![Guiden Migrera användar gränssnitt med alternativ för att välja targe-arbetsyta](./media/convert-classic-resource/migration.png)
    

När din resurs har migrerats visas motsvarande information om arbets ytan i **översikts** fönstret:

![Namn på arbetsyta](./media/create-workspace-resource/workspace-name.png)

Om du klickar på den blå länk texten tas du till den associerade Log Analytics arbets ytan där du kan dra nytta av den nya frågan i enhetlig arbets yta.

## <a name="understanding-log-queries"></a>Förstå logg frågor

Vi ger fortfarande fullständig bakåtkompatibilitet för dina Application Insights klassiska resurs frågor, arbets böcker och loggbaserade aviseringar inom Application Insightss upplevelsen. 

Om du vill skriva frågor mot den [nya arbetsytebaserade tabell strukturen/schemat](apm-tables.md)måste du först gå till din Log Analytics-arbetsyta. 

När du frågar direkt från Log Analytics gränssnittet i din arbets yta ser du bara de data som inhämtas efter migreringen. Om du vill se både dina klassiska Application Insights data + nya data som matas in efter migrering i en enhetlig fråga använder du vyn loggar (analys) från den migrerade Application Insights resursen.

## <a name="programmatic-resource-migration"></a>Migrering av program mässig resurs

### <a name="azure-cli"></a>Azure CLI

För att få åtkomst till för hands versionen Application Insights Azure CLI-kommandon måste du först köra:

```azurecli
 az extension add -n application-insights
```

Om du inte kör `az extension add` kommandot visas ett fel meddelande som säger: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nu kan du köra följande för att skapa din Application Insights-resurs:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Exempel

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Fullständig Azure CLI-dokumentation för det här kommandot finns i [Azure CLI-dokumentationen](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update).

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights`PowerShell-kommandot stöder för närvarande inte migrering av en klassisk Application Insights resurs till arbets ytans baserade. Om du vill skapa en arbets yta baserad resurs med PowerShell kan du använda Azure Resource Manager-mallarna nedan och distribuera med PowerShell.

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

#### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parameter fil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Ändra den associerade arbets ytan

När en arbets yta-baserad Application Insights resurs har skapats kan du ändra den associerade Log Analytics-arbetsytan.

I fönstret Application Insights resurs väljer du **Egenskaper**  >  **ändra arbets yta**  >  **Log Analytics arbets ytor**.

## <a name="troubleshooting"></a>Felsökning

### <a name="access-mode"></a>Åtkomstläge

**Fel meddelande:** *den valda arbets ytan har kon figurer ATS med arbets ytans-baserat åtkomst läge. Vissa APM-funktioner kan påverkas. Välj en annan arbets yta eller Tillåt resurs-baserad åtkomst i inställningar för arbets ytan. Du kan åsidosätta det här felet med CLI.* 

För att din arbets yta-baserade Application Insights resurs ska fungera korrekt måste du ändra åtkomst kontrol läget för mål Log Analytics arbets ytan till inställningen **resurs eller arbets ytans behörigheter** . Den här inställningen finns i användar gränssnittet för Log Analytics arbets yta under **Egenskaper**  >  **åtkomst kontrol läge**. Detaljerade anvisningar finns i [Log Analytics konfigurera åtkomst kontroll läges guide](../platform/manage-access.md#configure-access-control-mode). Om ditt åtkomst kontroll läge är inställt på inställningen exklusiv **Kräv arbets ytans behörighet** , är migreringen via portalens migrering fortfarande blockerad.

Om du inte kan ändra åtkomst kontrol läget av säkerhets skäl för din aktuella mål arbets yta, rekommenderar vi att du skapar en ny Log Analytics arbets yta som ska användas för migreringen. 

### <a name="continuous-export"></a>Löpande export

**Fel meddelande:** *kontinuerlig export måste inaktive ras innan du fortsätter. Efter migreringen använder du diagnostiska inställningar för export.* 

Äldre funktioner för kontinuerlig export stöds inte för arbets ytans resurser. Innan du migrerar måste du inaktivera kontinuerlig export.

1. Från vyn Application Insights resurs, under **Konfigurera** rubrik väljer du **löpande export**.

    ![Meny alternativ för kontinuerlig export](./media/convert-classic-resource/continuous-export.png)

2. Välj **Inaktivera**.

    ![Knappen för att inaktivera kontinuerlig export](./media/convert-classic-resource/disable.png)

- När du har valt inaktivera kan du gå tillbaka till migreringens användar gränssnitt. Om sidan Redigera kontinuerlig export visar att dina inställningar inte sparas kan du välja OK för den här frågan eftersom den inte gäller för att inaktivera/aktivera kontinuerlig export.

- När du har migrerat Application Insights-resursen till arbets ytans baserade, kan du använda diagnostikinställningar för att ersätta de funktioner som kontinuerliga exporter använder för att tillhandahålla. Välj **diagnostikinställningar**  >  **Lägg till diagnostisk inställning** inifrån din Application Insights-resurs. Du kan välja alla tabeller eller en delmängd av tabeller för att arkivera till ett lagrings konto eller strömma till en Azure Event Hub. Detaljerad vägledning om diagnostikinställningar finns i [vägledningen för Azure Monitor Diagnostic-inställningar](../platform/diagnostic-settings.md).

### <a name="retention-settings"></a>Inställningar för kvarhållning

**Varnings meddelande:** *dina anpassade Application Insights inställningarna för kvarhållning gäller inte för data som skickas till arbets ytan. Du måste konfigurera om den separat.*

Du behöver inte göra några ändringar innan du migrerar, men det här meddelandet är att varna dig om att dina nuvarande Application Insights kvarhållning inte har ställts in på standard perioden 90-dagars kvarhållning. Det här varnings meddelandet innebär att du kanske vill ändra inställningarna för kvarhållning för Log Analytics arbets ytan innan du migrerar och börjar mata in nya data. 

Du kan kontrol lera de aktuella inställningarna för kvarhållning för Log Analytics under **allmän**  >  **användning och uppskattade kostnader**  >  **data kvarhållning** i Log Analytics användar gränssnittet. Den här inställningen påverkar hur länge alla nya inmatade data lagras när du migrerar din Application Insights-resurs.

## <a name="next-steps"></a>Nästa steg

* [Utforska mått](../platform/metrics-charts.md)
* [Skriv analysfrågor](../log-query/log-query-overview.md)
