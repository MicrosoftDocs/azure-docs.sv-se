---
title: Åtgärdsregler för Azure Monitor aviseringar
description: Förstå vilka åtgärdsregler i Azure Monitor är och hur du konfigurerar och hanterar dem.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4f54ee7d21d52386bd18921aec33cabe02046852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772567"
---
# <a name="action-rules-preview"></a>Åtgärdsregler (förhandsversion)

Med åtgärdsregler kan du lägga till eller ignorera åtgärdsgrupper för dina uttjade aviseringar. En enda regel kan omfatta olika omfång för målresurser, till exempel alla aviseringar för en specifik resurs (till exempel en specifik virtuell dator) eller alla aviseringar som utsätts på en resurs i en prenumeration. Du kan också lägga till olika filter för att kontrollera vilka aviseringar som omfattas av en regel och definiera ett schema för den, till exempel för att den endast ska tillämpas utanför arbetstid eller under ett planerat underhåll.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Varför och när ska du använda åtgärdsregler?

### <a name="suppression-of-alerts"></a>Undertryckning av aviseringar

Det finns många scenarier där det är användbart att ignorera de meddelanden som aviseringar genererar. De här scenarierna sträcker sig från undertryckning under ett planerat underhåll till undertryckning under icke-arbetstid. Teamet som ansvarar för  **ContosoVM** vill till exempel ignorera aviseringsmeddelanden för den kommande helg eftersom **ContosoVM** genomgår planerat underhåll.

Även om teamet kan inaktivera varje aviseringsregel som konfigureras på **ContosoVM** manuellt (och aktivera den igen efter underhåll), är det inte en enkel process. Åtgärdsregler hjälper dig att definiera aviseringsundertryckning i stor skala med möjlighet att flexibelt konfigurera undertryckningsperioden. I föregående exempel kan teamet definiera en åtgärdsregel på **ContosoVM** som ignorerar alla aviseringsmeddelanden under helgerna.

### <a name="actions-at-scale"></a>Åtgärder i stor skala

Även om aviseringsregler hjälper dig att definiera den åtgärdsgrupp som utlöses när aviseringen genereras, har kunderna ofta en gemensam åtgärdsgrupp i åtgärdsomfånget. Ett team som ansvarar för resursgruppen **ContosoRG definierar** förmodligen samma åtgärdsgrupp för alla aviseringsregler som definierats i **ContosoRG.**

Åtgärdsregler hjälper dig att förenkla den här processen. Genom att definiera åtgärder i stor skala kan en åtgärdsgrupp utlösas för alla aviseringar som genereras i det konfigurerade omfånget. I föregående exempel kan teamet definiera en åtgärdsregel på **ContosoRG** som utlöser samma åtgärdsgrupp för alla aviseringar som genereras i den.

> [!NOTE]
> Åtgärdsregler gäller inte för Azure Service Health aviseringar.

## <a name="configuring-an-action-rule"></a>Konfigurera en åtgärdsregel

### <a name="portal"></a>[Portal](#tab/portal)

Du kan komma åt funktionen genom att **välja Hantera åtgärder** från **landningssidan** aviseringar i Azure Monitor. Välj sedan **Åtgärdsregler (förhandsversion)**. Du kan komma åt reglerna genom att **välja Åtgärdsregler (förhandsversion)** på instrumentpanelen på landningssidan för aviseringar.

![Åtgärdsregler från Azure Monitor landningssidan](media/alerts-action-rules/action-rules-landing-page.png)

Välj **+ Ny åtgärdsregel.**

![Skärmbild som visar sidan Hantera åtgärder med knappen Ny åtgärdsregel markerad.](media/alerts-action-rules/action-rules-new-rule.png)

Du kan också skapa en åtgärdsregel när du konfigurerar en aviseringsregel.

![Skärmbild som visar sidan Skapa regel med knappen Skapa åtgärdsregel markerad.](media/alerts-action-rules/action-rules-alert-rule.png)

Nu bör du se flödessidan för att skapa åtgärdsregler. Konfigurera följande element:

![Nytt flöde för skapande av åtgärdsregel](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Omfång

Välj först omfånget (Azure-prenumeration, resursgrupp eller målresurs). Du kan också välja en kombination av omfång inom en enda prenumeration.

![Åtgärdsregelomfång](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria&quot;></a>Filtervillkor

Om du vill kan du definiera filter så att regeln gäller för en specifik delmängd av aviseringarna, eller för specifika händelser för varje avisering (till exempel endast &quot;Uttad&quot; eller endast &quot;Löst").

De tillgängliga filtren är:

* **Allvarlighetsgrad**  
Den här regeln gäller endast för aviseringar med de valda allvarlighetsgraderna.  
Allvarlighetsgrad **= "Sev1"** innebär till exempel att regeln endast gäller för aviseringar med allvarlighetsgrad Sev1.
* **Övervaka tjänst**  
Den här regeln gäller endast för aviseringar som kommer från de valda övervakningstjänster.  
Övervaka tjänst **= "Azure Backup"** innebär till exempel att regeln endast gäller för säkerhetskopieringsaviseringar (som kommer från Azure Backup).
* **Resurstyp**  
Den här regeln gäller endast för aviseringar för de valda resurstyperna.  
Resurstyp **= "Virtual Machines"** innebär till exempel att regeln endast gäller för aviseringar på virtuella datorer.
* **Aviseringsregel-ID**  
Den här regeln gäller endast för aviseringar som kommer från en specifik aviseringsregel. Värdet ska vara det Resource Manager ID:t för aviseringsregeln.  
Till exempel, aviseringsregel-ID **= "/subscriptions/SubId1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/API-Latency"** innebär att den här regeln endast gäller för aviseringar som kommer från måttaviseringsregeln "API-Latency".  
_Obs! Du kan få rätt aviseringsregel-ID genom att visa dina aviseringsregler från CLI eller genom att öppna en specifik aviseringsregel i portalen, klicka på "Egenskaper" och kopiera värdet "Resurs-ID"._
* **Övervaka villkor**  
Den här regeln gäller endast för aviseringshändelser med det angivna övervakningsvillkoret – antingen **"Fired"** eller **"Resolved"**.
* **Beskrivning**  
Den här regeln gäller endast för aviseringar som innehåller en specifik sträng i fältet för aviseringsbeskrivning. Fältet innehåller beskrivningen av aviseringsregeln.  
Beskrivningen **innehåller till exempel "prod"** innebär att regeln endast matchar aviseringar som innehåller strängen "prod" i beskrivningen.
* **Aviseringskontext (nyttolast)**  
Den här regeln gäller endast för aviseringar som innehåller något av ett eller flera specifika värden i fälten för aviseringskontext.  
Till exempel innehåller aviseringskontext **(nyttolast) "Computer-01"** att regeln endast gäller för aviseringar vars nyttolast innehåller strängen "Computer-01".

> [!NOTE]
> Varje filter kan innehålla upp till fem värden.  
> Ett filter på övervakningstjänsten kan till exempel innehålla upp till fem övervakningstjänstnamn.




Om du anger flera filter i en regel gäller alla. Om du till exempel anger resurstyp **= "Virtual Machines"** och allvarlighetsgrad **= "Sev0"** gäller regeln endast för Sev0-aviseringar på virtuella datorer.

![Filter för åtgärdsregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Undertryckning eller åtgärdsgruppskonfiguration

Konfigurera sedan åtgärdsregeln för antingen aviseringsundertryckning eller stöd för åtgärdsgrupp. Du kan inte välja båda. Konfigurationen fungerar på alla aviseringsinstanser som matchar det tidigare definierade omfånget och filtren.

#### <a name="suppression"></a>Undertryckning

Om du väljer **undertryckning** konfigurerar du varaktigheten för undertryckning av åtgärder och meddelanden. Välj något av följande alternativ:
* **Från och med nu (alltid):** Undertrycker alla meddelanden på obestämd tid.
* **Vid en schemalagd tidpunkt:** Undertrycker meddelanden inom en avgränsad varaktighet.
* **Med en upprepning:** Undertrycker meddelanden enligt ett återkommande schema per dag, vecka eller månad.

![Undertryckning av åtgärdsregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Åtgärdsgrupp

Om du väljer **Åtgärdsgrupp** i växlingsknappen lägger du antingen till en befintlig åtgärdsgrupp eller skapar en ny.

> [!NOTE]
> Du kan bara associera en åtgärdsgrupp med en åtgärdsregel.

![Lägg till eller skapa en ny åtgärdsregel genom att välja Åtgärdsgrupp](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Information om åtgärdsregel

Konfigurera sedan följande information för åtgärdsregeln:
* Name
* Resursgrupp som den har sparats i
* Description

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan skapa åtgärdsregler med Azure CLI med kommandot [az monitor action-rule create.](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create)  Referensen `az monitor action-rule` är bara en av många Azure [CLI-referenser för Azure Monitor](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Förbered din miljö

1. [Installera Azure CLI](/cli/azure/install-azure-cli)

   Om du vill kan du också använda Azure Cloud Shell för att slutföra stegen i den här artikeln.  Azure Cloud Shell är en interaktiv gränssnittsmiljö som du använder via webbläsaren.  Börja Cloud Shell med någon av följande metoder:

   - Öppna Cloud Shell genom att gå till [https://shell.azure.com](https://shell.azure.com)

   - Välj knappen **Cloud Shell** på menyraden i det övre högra hörnet i [Azure Portal](https://portal.azure.com)

1. Logga in.

   Om du använder en lokal installation av CLI loggar du in med kommandot [az login.](/cli/azure/reference-index#az_login)  Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

    ```azurecli
    az login
    ```

1. Installera `alertsmanagement` tillägget

   Kommandot `az monitor action-rule` är en experimentell utökning av Grundläggande Azure CLI. Läs mer om tilläggsreferenser i [Använda tillägg med Azure CLI.](/cli/azure/azure-cli-extensions-overview?)

   ```azurecli
   az extension add --name alertsmanagement
   ```

   Följande varning förväntas.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Skapa åtgärdsregler med Azure CLI

Mer information om obligatoriska och valfria parametrar finns i Azure CLI-referensinnehållet för [az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) create.

Skapa en åtgärdsregel för att förhindra meddelanden i en resursgrupp.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Skapa en åtgärdsregel för att förhindra meddelanden för alla Sev4-aviseringar på alla virtuella datorer i prenumerationen varje helg.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Exempelscenarier

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Undertryckning av aviseringar baserat på allvarlighetsgrad

Contoso vill ignorera meddelanden för alla Sev4-aviseringar på alla virtuella datorer i prenumerationen **ContosoSub** varje helg.

**Lösning:** Skapa en åtgärdsregel med:
* Omfång = **ContosoSub**
* Filter
    * Allvarlighetsgrad = **Sev4**
    * Resurstyp = **Virtual Machines**
* Undertryckning med upprepning inställt på varje vecka och **lördag** och **söndag** markerat

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Undertryckning av aviseringar baserat på aviseringskontext (nyttolast)

Contoso vill ignorera meddelanden för alla loggaviseringar som genereras för **Dator-01** i **ContosoSub** på obestämd tid eftersom de går igenom underhåll.

**Lösning:** Skapa en åtgärdsregel med:
* Omfång = **ContosoSub**
* Filter
    * Övervaka tjänst = **Log Analytics**
    * Aviseringskontext (nyttolast) **innehåller Dator-01**
* Undertryckning **inställt på Från nu (Alltid)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Åtgärdsgrupp definierad i en resursgrupp

Contoso har definierat [en måttavisering på prenumerationsnivå.](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) Men de vill definiera de åtgärder som utlöses specifikt för aviseringar som genereras från resursgruppen **ContosoRG**.

**Lösning:** Skapa en åtgärdsregel med:
* Omfång = **ContosoRG**
* Inga filter
* Åtgärdsgrupp inställd på **ContosoActionGroup**

> [!NOTE]
> *Åtgärdsgrupper som definierats i åtgärdsregler och aviseringsregler fungerar oberoende av varandra, utan deduplicering.* Om en åtgärdsgrupp har definierats för aviseringsregeln i scenariot som beskrivs ovan utlöses den tillsammans med den åtgärdsgrupp som definierats i åtgärdsregeln.

## <a name="managing-your-action-rules"></a>Hantera åtgärdsregler

### <a name="portal"></a>[Portal](#tab/portal)

Du kan visa och hantera dina åtgärdsregler från listvyn:

![Listvy över åtgärdsregler](media/alerts-action-rules/action-rules-list-view.png)

Härifrån kan du aktivera, inaktivera eller ta bort åtgärdsregler i stor skala genom att markera kryssrutan bredvid dem. När du väljer en åtgärdsregel öppnas dess konfigurationssida. Sidan hjälper dig att uppdatera åtgärdsregelns definition och aktivera eller inaktivera den.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan visa och hantera dina åtgärdsregler med kommandot [az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor) från Azure CLI.

Innan du hanterar åtgärdsregler med Azure CLI förbereder du din miljö med hjälp av anvisningarna i [Konfigurera en åtgärdsregel](#configuring-an-action-rule).

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Bästa praxis

Logga aviseringar som du skapar med [alternativet](./alerts-unified-log.md) för antal resultat genererar en enda aviseringsinstans med hjälp av hela sökresultatet (som kan sträcka sig över flera datorer). I det här scenariot, om en åtgärdsregel använder filtret Aviseringskontext **(nyttolast),** agerar den på aviseringsinstansen så länge det finns en matchning. I scenario 2, som beskrevs tidigare, ignoreras hela meddelandet om sökresultaten för den genererade loggaviseringen innehåller både **Dator-01** och **Dator-02.** Det finns inget meddelande som genereras **för Dator-02** alls.

![Diagram som visar åtgärdsregler och logga aviseringar med en enda aviseringsinstans markerad.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Om du vill använda logga aviseringar med åtgärdsregler på bästa sätt skapar du logga aviseringar med [måttmåttsalternativet.](./alerts-unified-log.md) Separata aviseringsinstanser genereras av det här alternativet baserat på dess definierade gruppfält. I scenario 2 genereras sedan separata aviseringsinstanser för **Dator-01** och **Dator-02.** På grund av åtgärdsregeln som beskrivs i scenariot ignoreras bara meddelandet för **Dator-01.** Meddelandet för **Dator-02** fortsätter att branda som vanligt.

![Åtgärdsregler och logga aviseringar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Vanliga frågor

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>När jag konfigurerar en åtgärdsregel vill jag se alla möjliga överlappande åtgärdsregler så att jag undviker dubbla meddelanden. Går det att göra det?

När du har definierat ett omfång när du konfigurerar en åtgärdsregel kan du se en lista över åtgärdsregler som överlappar samma omfång (om det finns några). Den här överlappningen kan vara något av följande alternativ:

* En exakt matchning: Till exempel den åtgärdsregel som du definierar och den överlappande åtgärdsregeln finns i samma prenumeration.
* En delmängd: Till exempel är den åtgärdsregel som du definierar i en prenumeration och den överlappande åtgärdsregeln finns i en resursgrupp i prenumerationen.
* En supermängd: Till exempel är den åtgärdsregel som du definierar i en resursgrupp och den överlappande åtgärdsregeln finns i prenumerationen som innehåller resursgruppen.
* En skärningspunkt: Den åtgärdsregel som du definierar är till exempel på **VM1** och **VM2,** och den överlappande åtgärdsregeln finns på **VM2** och **VM3.**

![Skärmbild som visar sidan Ny åtgärdsregel med de överlappande åtgärdsreglerna som visas i åtgärdsreglerna som definierats i samma omfångsfönster.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>När jag konfigurerar en aviseringsregel, är det möjligt att veta om det redan finns åtgärdsregler som kan fungera enligt den aviseringsregel som jag definierar?

När du har definierat målresursen för din aviseringsregel kan du se en lista över åtgärdsregler som agerar på samma omfång (om det finns någon) genom att välja Visa **konfigurerade** åtgärder i **avsnittet** Åtgärder. Den här listan fylls i baserat på följande scenarier för omfånget:

* En exakt matchning: Till exempel den aviseringsregel som du definierar och åtgärdsregeln finns i samma prenumeration.
* En delmängd: Till exempel är den aviseringsregel som du definierar i en prenumeration och åtgärdsregeln finns i en resursgrupp i prenumerationen.
* En supermängd: Till exempel är den aviseringsregel som du definierar i en resursgrupp och åtgärdsregeln finns i prenumerationen som innehåller resursgruppen.
* En skärningspunkt: Till exempel är den aviseringsregel som du definierar på **VM1** och **VM2,** och åtgärdsregeln finns på **VM2** och **VM3.**

![Överlappande åtgärdsregler](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Kan jag se aviseringar som har ignorerats av en åtgärdsregel?

På sidan [med aviseringslistan](./alerts-managing-alert-instances.md)kan du välja ytterligare en kolumn med namnet **Undertryckningsstatus.** Om meddelandet för en aviseringsinstans utelämnades skulle det visa den statusen i listan.

![Undertryckta aviseringsinstanser](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Vad händer om det finns en åtgärdsregel med en åtgärdsgrupp och en annan med aktiv undertryckning i samma omfång?

Undertryckning har alltid företräde inom samma omfång.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Vad händer om jag har en resurs som omfattas av två åtgärdsregler? Får jag ett eller två meddelanden? Till exempel **VM2** i följande scenario:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

För varje avisering på VM1 och VM3 utlöses åtgärdsgruppen AG1 en gång. För varje avisering **på VM2** utlöses åtgärdsgruppen AG1 två gånger, eftersom åtgärdsregler inte deduplicera åtgärder.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Vad händer om jag har en resurs som övervakas i två separata åtgärdsregler och en anropar åtgärd medan en annan för undertryckning? Till exempel **VM2** i följande scenario:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

För varje avisering på VM1 utlöses åtgärdsgruppen AG1 en gång. Åtgärder och meddelanden för varje avisering på VM2 och VM3 ignoreras.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Vad händer om jag har en aviseringsregel och en åtgärdsregel som definierats för samma resurs som anropar olika åtgärdsgrupper? Till exempel **VM1** i följande scenario:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

För varje avisering på VM1 utlöses åtgärdsgruppen AG1 en gång. När aviseringsregeln "rule1" utlöses utlöses även AG2. Åtgärdsgrupper som definierats i åtgärdsregler och aviseringsregler fungerar oberoende av varandra, utan deduplicering.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om aviseringar i Azure](./alerts-overview.md)
