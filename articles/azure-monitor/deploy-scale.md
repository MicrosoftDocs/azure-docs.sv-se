---
title: Distribuera Azure Monitor i skala med Azure Policy
description: Distribuera Azure Monitor funktioner i skala med Azure Policy.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: cc55cd17a547b9c63f2c26479d5797fae016d8d7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044076"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Distribuera Azure Monitor i skala med Azure Policy
Vissa Azure Monitor funktioner konfigureras en gång eller ett begränsat antal gånger, men andra måste upprepas för varje resurs som du vill övervaka. I den här artikeln beskrivs metoder för att använda Azure Policy för att implementera Azure Monitor i skala för att säkerställa att övervakningen är konsekvent och korrekt konfigurerad för alla dina Azure-resurser.

Du måste till exempel skapa en diagnostisk inställning för alla befintliga Azure-resurser och för varje ny resurs som du skapar. Du måste också ha installerat och konfigurerat en agent varje gången du skapar en virtuell dator. Du kan använda metoder som PowerShell eller CLI för att utföra dessa åtgärder eftersom dessa metoder är tillgängliga för alla funktioner i Azure Monitor. Med hjälp av Azure Policy kan du ha logik på plats som automatiskt utför lämplig konfiguration varje gången du skapar eller ändrar en resurs.


## <a name="azure-policy"></a>Azure Policy
Det här avsnittet innehåller en kort introduktion till [Azure policy](../governance/policy/overview.md) som gör att du kan utvärdera och upprätthålla organisations standarder i hela Azure-prenumerationen eller hanterings gruppen med minimal ansträngning. Mer information finns i [Azure policy-dokumentationen](../governance/policy/overview.md) .

Med Azure Policy kan du ange konfigurations krav för alla resurser som skapas och antingen identifiera resurser som inte är kompatibla, blockera resurserna från att skapas eller lägga till konfigurationen som krävs. Det fungerar genom att avlyssna anrop för att skapa en ny resurs eller ändra en befintlig resurs. Den kan svara på sådana effekter som att neka begäran om den inte överensstämmer med de egenskaper som förväntas i en princip definition, flagga den för inkompatibilitet eller distribuera en relaterad resurs. Du kan reparera befintliga resurser med en **deployIfNotExists** eller **ändra** princip definition.

Azure Policy består av objekten i följande tabell. Se [Azure policy objekt](../governance/policy/overview.md#azure-policy-objects) för en mer detaljerad förklaring av varje.

| Objekt | Beskrivning |
|:---|:---|
| Definition av princip | Beskriver villkoren för resurs efterlevnad och den påverkan som ska vidtas om ett villkor är uppfyllt. Detta kan vara alla resurser av en viss typ eller endast resurser som matchar vissa egenskaper. Detta kan vara att helt enkelt flagga resursen för efterlevnad eller distribuera en relaterad resurs. Princip definitioner skrivs med JSON enligt beskrivningen i [Azure policy definitions struktur](../governance/policy/concepts/definition-structure.md). Effekter beskrivs i [förstå Azure policys effekter](../governance/policy/concepts/effects.md).
| Princip initiativ | En grupp princip definitioner som ska appliceras tillsammans. Du kan till exempel ha en princip definition för att skicka resurs loggar till en Log Analytics-arbetsyta och en annan för att skicka resurs loggar till Event Hub. Skapa ett initiativ som innehåller båda princip definitionerna och tillämpa initiativet på resurser i stället för de enskilda princip definitionerna. Initiativ skrivs med JSON enligt beskrivningen i [Azure policy initiativets struktur](../governance/policy/concepts/initiative-definition-structure.md). |
| Tilldelning | En princip definition eller ett initiativ börjar inte gälla förrän det har tilldelats en omfattning. Du kan till exempel tilldela en princip till en resurs grupp för att tillämpa den på alla resurser som skapats i den resursen, eller tillämpa den på en prenumeration för att tillämpa den på alla resurser i den prenumerationen.  Mer information finns i [Azure policy tilldelnings struktur](../governance/policy/concepts/assignment-structure.md). |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Inbyggda princip definitioner för Azure Monitor
Azure Policy innehåller flera fördefinierade definitioner relaterade till Azure Monitor. Du kan tilldela dessa princip definitioner till din befintliga prenumeration eller använda dem som grund för att skapa dina egna anpassade definitioner. En fullständig lista över de inbyggda politiken i kategorin **övervakning** finns i [Azure policy inbyggda princip definitioner för Azure Monitor](.//policy-reference.md).

Om du vill visa de inbyggda princip definitionerna som är relaterade till övervakning gör du följande:

1. Gå till **Azure policy** i Azure Portal.
2. Välj **definitioner**.
3. I **typ** väljer du *inbyggd* och för **kategori** och väljer *övervakning*.

  ![Skärm bild av sidan Azure Policy definitioner i Azure Portal visar en lista över princip definitioner för övervaknings kategorin och den inbyggda typen.](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Diagnostikinställningar
[Diagnostikinställningar](essentials/diagnostic-settings.md) samlar in resurs loggar och mått från Azure-resurser till flera platser, vanligt vis till en Log Analytics arbets yta som gör det möjligt att analysera data med [logg frågor](logs/log-query-overview.md) och [logg aviseringar](alerts/alerts-log.md). Använd principen för att automatiskt skapa en diagnostisk inställning varje gången du skapar en resurs.

Varje Azure-resurs har en unik uppsättning kategorier som måste visas i den diagnostiska inställningen. Därför kräver varje resurs typ en separat princip definition. Vissa resurs typer har inbyggda princip definitioner som du kan tilldela utan ändringar. För andra resurs typer måste du skapa en anpassad definition.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Inbyggda princip definitioner för Azure Monitor
Det finns två inbyggda princip definitioner för varje resurs typ, en för att skicka till Log Analytics arbets ytan och en annan till Event Hub. Om du bara behöver en plats tilldelar du den principen för resurs typen. Om du behöver båda tilldelar du båda princip definitionerna för resursen.

Följande bild visar till exempel princip definitionerna för de inbyggda diagnostikinställningar för Data Lake Analytics.

  ![Delvis skärm bild från sidan Azure Policy definitioner visar två inbyggda inställningar för inställnings principer för Data Lake Analytics.](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Anpassade princip definitioner
För resurs typer som inte har en inbyggd princip måste du skapa en anpassad princip definition. Du kan göra detta manuellt i Azure Portal genom att kopiera en befintlig inbyggd princip och sedan ändra för resurs typen. Det är mer effektivt om du skapar principen program mässigt med ett skript i PowerShell-galleriet.

Skriptet [create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) skapar principfiler för en viss resurs typ som du kan installera med hjälp av POWERSHELL eller cli. Använd följande procedur för att skapa en anpassad princip definition för diagnostiska inställningar.


1. Kontrol lera att du har [Azure PowerShell](/powershell/azure/install-az-ps) installerat.
2. Installera skriptet med följande kommando:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Kör skriptet med parametrarna för att ange vart loggarna ska skickas. Du uppmanas att ange en prenumeration och resurs typ. Om du till exempel vill skapa en princip definition som skickar till Log Analytics-arbetsyta och Händelsehubben, använder du följande kommando.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. Alternativt kan du ange en prenumeration och resurs typ i kommandot. Om du till exempel vill skapa en princip definition som skickar till Log Analytics arbets yta och Händelsehubben för Azure SQL Server-databaser, använder du följande kommando.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. Skriptet skapar separata mappar för varje princip definition, där varje innehåller tre filer som heter azurepolicy.jspå, azurepolicy.rules.jspå azurepolicy.parameters.jspå. Om du vill skapa principen manuellt i Azure Portal kan du kopiera och klistra in innehållet i azurepolicy.jspå eftersom det innehåller hela princip definitionen. Använd de andra två filerna med PowerShell eller CLI för att skapa princip definitionen från en kommando rad.

    I följande exempel visas hur du installerar princip definitionen från både PowerShell och CLI. Varje inkluderar metadata för att ange en kategori för **övervakning** för att gruppera den nya princip definitionen med de inbyggda princip definitionerna.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Förmåga
I stället för att skapa en tilldelning för varje princip definition är en gemensam strategi att skapa ett initiativ som innehåller princip definitionerna för att skapa diagnostikinställningar för varje Azure-tjänst. Skapa en tilldelning mellan initiativet och en hanterings grupp, prenumeration eller resurs grupp beroende på hur du hanterar din miljö. Den här strategin ger följande fördelar:

- Skapa en enskild tilldelning för initiativet i stället för flera tilldelningar för varje resurs typ. Använd samma initiativ för flera övervaknings grupper, prenumerationer eller resurs grupper.
- Ändra initiativet när du behöver lägga till en ny resurs typ eller mål. Dina ursprungliga krav kan till exempel vara att bara skicka data till en Log Analytics arbets yta, men senare vill du lägga till Händelsehubben. Ändra initiativet i stället för att skapa nya tilldelningar.

Se [skapa och tilldela en initiativ definition](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) för information om hur du skapar ett initiativ. Tänk på följande rekommendationer:

- Ange den **kategori** som ska **övervakas** för att gruppera den med relaterade inbyggda och anpassade princip definitioner.
- I stället för att ange informationen för arbets ytan Log Analytics och Händelsehubben för princip definitionen som ingår i initiativet använder du en gemensam initiativ parameter. På så sätt kan du enkelt ange ett gemensamt värde för alla princip definitioner och ändra värdet vid behov.

![Initiativdefinition](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Tilldelning 
Tilldela initiativet till en hanterings grupp, prenumeration eller resurs grupp för Azure, beroende på omfånget för dina resurser som ska övervakas. En [hanterings grupp](../governance/management-groups/overview.md) är särskilt användbart för en princip som är särskilt användbar om din organisation har flera prenumerationer.

![Skärm bild av inställningarna för fliken grundläggande i avsnittet Tilldela initiativ i diagnostikinställningar till Log Analytics arbets ytan i Azure Portal.](media/deploy-scale/initiative-assignment.png)

Genom att använda initiativ parametrar kan du ange arbets ytan eller någon annan information en gång för alla princip definitioner i initiativet. 

![Initiativparametrar](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Åtgärder
Initiativet kommer att gälla för varje virtuell dator när den skapas. En [reparations uppgift](../governance/policy/how-to/remediate-resources.md) distribuerar princip definitionerna i initiativet till befintliga resurser, så att du kan skapa diagnostikinställningar för alla resurser som redan har skapats. När du skapar tilldelningen med hjälp av Azure Portal har du möjlighet att skapa en reparations uppgift på samma gång. Se [Reparera icke-kompatibla resurser med Azure policy](../governance/policy/how-to/remediate-resources.md) för information om reparationen.

![Initiativ reparation](media/deploy-scale/initiative-remediation.png)


## <a name="vm-insights"></a>VM-insikter
[VM Insights](vm/vminsights-overview.md) är det primära verktyget i Azure Monitor för övervakning av virtuella datorer. Om du aktiverar VM Insights installeras både Log Analytics-agenten och beroende agenten. Använd Azure Policy för att se till att varje virtuell dator har kon figurer ATS som du skapar i stället för att utföra dessa uppgifter manuellt.

> [!NOTE]
> VM Insights innehåller en funktion som kallas för **princip täckning för VM Insights** som gör att du kan identifiera och åtgärda icke-kompatibla virtuella datorer i din miljö. Du kan använda den här funktionen i stället för att arbeta direkt med Azure Policy för virtuella Azure-datorer och för virtuella hybrid datorer som är anslutna till Azure Arc. För skalnings uppsättningar för virtuella Azure-datorer måste du skapa tilldelningen med hjälp av Azure Policy.
 

VM Insights innehåller följande inbyggda initiativ som installerar båda agenterna för att aktivera fullständig övervakning. 

|Namn |Beskrivning |
|:---|:---|
|Aktivera VM Insights | Installerar Log Analytics agent och beroende agent på virtuella Azure-datorer och hybrid virtuella datorer som är anslutna till Azure Arc. |
|Aktivera Azure Monitor för skalnings uppsättningar för virtuella datorer | Installerar den Log Analytics agenten och beroende agenten på skalnings uppsättningen för den virtuella Azure-datorn. |


### <a name="virtual-machines"></a>Virtuella datorer
I stället för att skapa tilldelningar för de här initiativen med hjälp av Azure Policy-gränssnittet innehåller VM Insights en funktion som gör att du kan kontrol lera antalet virtuella datorer i varje omfattning för att avgöra om initiativet har tillämpats. Du kan sedan konfigurera arbets ytan och skapa obligatoriska tilldelningar med det gränssnittet.

Mer information om den här processen finns i [Aktivera VM-insikter med hjälp av Azure policy](./vm/vminsights-enable-policy.md).

![Princip för VM Insights](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer
Om du vill använda Azure Policy för att aktivera övervakning av skalnings uppsättningar för virtuella datorer, tilldelar du initiativet **aktivera Azure Monitor för Virtual Machine Scale set** till en hanterings grupp, prenumeration eller resurs grupp i Azure beroende på vilka resurser som ska övervakas. En [hanterings grupp](../governance/management-groups/overview.md) är särskilt användbart för en princip som är särskilt användbar om din organisation har flera prenumerationer.

![Skärm bild av sidan tilldela initiativ i Azure Portal. Initiativ definitionen är inställd på att aktivera Azure Monitor för skalnings uppsättningar för virtuella datorer.](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

Välj arbets ytan som data ska skickas till. Den här arbets ytan måste ha *VMInsights* -lösningen installerad enligt beskrivningen i [Konfigurera Log Analytics arbets yta för VM-insikter](vm/vminsights-configure-workspace.md).

![Välj arbetsyta](media/deploy-scale/virtual-machine-scale-set-workspace.png)

Skapa en reparations uppgift om du har en befintlig virtuell dators skalnings uppsättning som måste tilldelas den här principen.

![Reparations uppgift](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Log Analytics-agent
Du kan ha scenarier där du vill installera Log Analytics agenten men inte beroende agenten. Det finns inget inbyggt initiativ för bara agenten, men du kan skapa egna baserat på de inbyggda princip definitionerna som tillhandahålls av VM Insights.

> [!NOTE]
> Det kan inte finnas någon anledning att distribuera beroende agenten på egen hand eftersom den kräver att den Log Analytics agenten levererar sina data till Azure Monitor.


|Namn |Beskrivning |
|-----|------------|
|Granska Log Analytics agent distribution – VM-avbildningen (OS) har inte listats |Rapporterar virtuella datorer som icke-kompatibla om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |
|Distribuera Log Analytics agent för virtuella Linux-datorer |Distribuera Log Analytics agent för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |
|Distribuera Log Analytics agent för virtuella Windows-datorer |Distribuera Log Analytics agent för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |
| [För hands version]: Log Analytics-agenten ska installeras på dina Linux Azure Arc-datorer |Rapporterar hybrid Azure Arc-datorer som icke-kompatibla för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |
| [För hands version]: Log Analytics agent ska installeras på dina Windows Azure Arc-datorer |Rapporterar hybrid Azure Arc-datorer som inkompatibla för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |
| [För hands version]: Distribuera Log Analytics agent till Linux Azure Arc-datorer |Distribuera Log Analytics agent för Linux hybrid Azure Arc-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |
| [För hands version]: Distribuera Log Analytics agent till Windows Azure Arc-datorer |Distribuera Log Analytics agent för Windows hybrid Azure Arc-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |
|Granska beroende agent distribution i virtuell dator skalnings uppsättningar – VM-avbildning (OS) har inte listats |Rapporterar skalnings uppsättning för virtuella datorer som icke-kompatibel om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |
|Granska Log Analytics agent distribution i Virtual Machine Scale Sets – VM-avbildning (OS) har inte listats |Rapporterar skalnings uppsättning för virtuella datorer som icke-kompatibel om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |
|Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Linux-datorer |Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |
|Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Windows-datorer |Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure policy](../governance/policy/overview.md).
- Läs mer om [diagnostikinställningar](essentials/diagnostic-settings.md).