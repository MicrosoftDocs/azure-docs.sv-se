---
title: Övervaka B2B-meddelanden med hjälp av Azure Monitor
description: Felsök AS2-, X12-och EDIFACT-meddelanden genom att konfigurera Azure Monitor loggar och samla in diagnostikdata för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 765c15897bd5d435503d3bef07e76a93b148971c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100596865"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Konfigurera Azure Monitor-loggar och samla in diagnostikdata för B2B-meddelanden i Azure Logic Apps

När du har konfigurerat B2B-kommunikation mellan handels partner i ditt integrations konto kan dessa partners utbyta meddelanden med hjälp av protokoll som AS2, X12 och EDIFACT. För att kontrol lera att den här kommunikationen fungerar som du förväntar dig kan du konfigurera [Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md) för ditt integrations konto. [Azure Monitor](../azure-monitor/overview.md) hjälper dig att övervaka molnet och lokala miljöer så att du enklare kan underhålla deras tillgänglighet och prestanda. Genom att använda Azure Monitor loggar kan du registrera och lagra data om körnings data och händelser, t. ex. utlösa händelser, köra händelser och åtgärds händelser i en [Log Analytics arbets yta](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). För meddelanden samlar loggning även in information som:

* Antal meddelanden och status
* Bekräftelse status
* Korrelationer mellan meddelanden och bekräftelser
* Detaljerade fel beskrivningar för fel

Med Azure Monitor kan du skapa [logg frågor](../azure-monitor/logs/log-query-overview.md) som hjälper dig att hitta och granska den här informationen. Du kan också [använda dessa diagnostikdata med andra Azure-tjänster](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), till exempel Azure Storage och Azure Event Hubs.

Om du vill konfigurera loggning för ditt integrations konto [installerar du Logic Apps B2B-lösningen](#install-b2b-solution) i Azure Portal. Den här lösningen tillhandahåller sammanställd information för B2B-meddelande händelser. Om du vill aktivera loggning och skapa frågor för den här informationen konfigurerar du [Azure Monitor loggar](#set-up-resource-logs).

Den här artikeln visar hur du aktiverar Azure Monitor loggning för ditt integrations konto.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

* En Log Analytics-arbetsyta. Om du inte har en Log Analytics arbets yta, lär du dig [hur du skapar en Log Analytics arbets yta](../azure-monitor/logs/quick-create-workspace.md).

* En Logic-app som har kon figurer ATS med Azure Monitor loggning och skickar informationen till en Log Analytics arbets yta. Lär dig [hur du konfigurerar Azure Monitor loggar för din Logic app](../logic-apps/monitor-logic-apps.md).

* Ett integrations konto som är länkat till din Logic app. Lär dig [hur du länkar ditt integrations konto till din Logic app](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Installera Logic Apps B2B lösning

Innan Azure Monitor loggar kan spåra B2B-meddelanden för din Logic app lägger du till **Logic Apps B2B** -lösningen i din Log Analytics-arbetsyta.

1. I sökrutan [Azure Portal](https://portal.azure.com)anger du `log analytics workspaces` och väljer **Log Analytics arbets ytor**.

   ![Välj Log Analytics arbets ytor](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. Under **Log Analytics arbets ytor** väljer du din arbets yta.

   ![Välj din Log Analytics arbets yta](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. I fönstret Översikt, under **Kom igång med Log Analytics**  >  **Konfigurera övervaknings lösningar**, väljer du **Visa lösningar**.

   ![I översikts fönstret väljer du "Visa lösningar"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. I översikts fönstret väljer du **Lägg till**.

   ![Lägg till ny lösning i översikts fönstret](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. När **Marketplace** öppnas i rutan Sök anger du `logic apps b2b` och väljer **Logic Apps B2B**.

   ![Från Marketplace väljer du "Logic Apps hantering"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. I fönstret lösnings Beskrivning väljer du **skapa**.

   ![Välj "skapa" för att lägga till "Logic Apps B2B"-lösning](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Granska och bekräfta Log Analytics arbets ytan där du vill installera lösningen och välj sedan **skapa** igen.

   ![Välj "skapa" för "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   När Azure har distribuerat lösningen till den Azure-resurs grupp som innehåller din Log Analytics arbets yta, visas lösningen i arbets ytans sammanfattnings fönster. När B2B-meddelanden bearbetas uppdateras antalet meddelanden i den här rutan.

   ![Fönstret Sammanfattning av arbets yta](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Konfigurera Azure Monitor loggar

Du kan aktivera Azure Monitor loggning direkt från ditt integrations konto.

1. I [Azure Portal](https://portal.azure.com)letar du reda på och väljer ditt integrations konto.

   ![Hitta och välj ditt integrations konto](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. På integrations kontots meny, under **övervakning**, väljer du **diagnostikinställningar**. Välj **Lägg till diagnostikinställning**.

   ![Under "övervakning" väljer du inställningar för diagnostik](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Följ de här stegen för att skapa inställningen:

   1. Ange ett namn för inställningen.

   1. Välj **Skicka till Log Analytics**.

   1. För **prenumeration** väljer du den Azure-prenumeration som är kopplad till din Log Analytics-arbetsyta.

   1. För **Log Analytics arbets yta** väljer du den arbets yta som du vill använda.

   1. Under **loggen** väljer du kategorin **IntegrationAccountTrackingEvents** som anger den händelse kategori som du vill registrera.

   1. När du är klar väljer du **Spara**.

   Exempel: 

   ![Konfigurera Azure Monitor loggar för att samla in diagnostikdata](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Visa meddelande status

När din Logic App körs kan du Visa status och information om dessa meddelanden i din Log Analytics-arbetsyta.

1. Leta upp och öppna arbets ytan Log Analytics i rutan [Azure Portal](https://portal.azure.com) Sök.

1. På arbets ytans meny väljer du **Sammanfattning av arbets yta**  >  **Logic Apps B2B**.

   ![Fönstret Sammanfattning av arbets yta](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Om Logic Apps B2B panelen inte omedelbart visar resultat efter en körning, försök att välja **Uppdatera** eller vänta en stund innan du försöker igen.

   Som standard visar panelen **Logic Apps B2B** data baserat på en enda dag. Om du vill ändra data omfånget till ett annat intervall väljer du kontrollen omfång överst på sidan:

   ![Ändra intervall](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. När instrument panelen för meddelande status visas kan du Visa mer information om en viss meddelande typ, som visar data baserat på en enda dag. Välj panelen för **AS2**, **X12** eller **EDIFACT**.

   ![Visa status för meddelanden](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   En lista med meddelanden visas för din valda panel. Till exempel kan en lista över AS2-meddelanden se ut så här:

   ![Status och information för AS2-meddelanden](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Mer information om egenskaperna för varje meddelande typ finns i följande beskrivningar av meddelande egenskaper:

   * [Egenskaper för AS2-meddelande](#as2-message-properties)
   * [Egenskaper för X12-meddelande](#x12-message-properties)
   * [Egenskaper för EDIFACT-meddelande](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/logs/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Egenskaps beskrivningar och namn format för AS2-, X12-och EDIFACT-meddelanden

Här är egenskaps beskrivningar och namn format för hämtade meddelandefiler för varje meddelande typ.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Beskrivningar av AS2 meddelande egenskaper

Här är egenskaps beskrivningarna för varje AS2-meddelande.

| Egenskap | Beskrivning |
|----------|-------------|
| **Avsändare** | Gäst partnern som anges i **ta emot inställningar** eller värd partnern som anges i **Skicka inställningar** för ett AS2-avtal |
| **Mottagare** | Värd partnern som anges i **ta emot inställningar** eller gäst partnern som anges i **Skicka inställningar** för ett AS2-avtal |
| **Logikapp** | Den Logic app där AS2-åtgärderna har kon figurer ATS |
| **Status** | Status för AS2-meddelande <br>Lyckades = tar emot eller skickade ett giltigt AS2-meddelande. Ingen MDN har kon figurer ATS. <br>Lyckades = tar emot eller skickade ett giltigt AS2-meddelande. MDN konfigureras och tas emot, eller så skickas MDN. <br>Misslyckades = ett ogiltigt AS2-meddelande togs emot. Ingen MDN har kon figurer ATS. <br>Väntar = tar emot eller skickade ett giltigt AS2-meddelande. MDN har ställts in och MDN förväntas. |
| **Följ** | Status för MDN-meddelande <br>Accepterad = mottagen eller skickad positiv MDN. <br>Väntar = väntar på att ta emot eller skicka en MDN. <br>Avvisad = ta emot eller skicka ett negativt MDN. <br>Krävs inte = MDN har inte ställts in i avtalet. |
| **Riktning** | AS2 meddelande riktning |
| **Spårnings-ID** | Det ID som motsvarar alla utlösare och åtgärder i en Logic app |
| **Meddelande-ID** | AS2 meddelande-ID från AS2-meddelandehuvuden |
| **Tidsstämpel** | Tiden då AS2-åtgärden bearbetade meddelandet |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Beskrivningar av X12 meddelande egenskaper

Här är egenskaps beskrivningarna för varje X12-meddelande.

| Egenskap | Beskrivning |
|----------|-------------|
| **Avsändare** | Gäst partnern som anges i **ta emot inställningar** eller värd partnern som anges i **Skicka inställningar** för ett X12-avtal |
| **Mottagare** | Värd partnern som anges i **ta emot inställningar** eller gäst partnern som anges i **Skicka inställningar** för ett X12-avtal |
| **Logikapp** | Den Logic app där X12-åtgärderna har kon figurer ATS |
| **Status** | Status för X12-meddelande <br>Lyckades = tar emot eller skickade ett giltigt X12-meddelande. Ingen funktions ack har kon figurer ATS. <br>Lyckades = tar emot eller skickade ett giltigt X12-meddelande. Funktions ack har kon figurer ATS och tagits emot, eller en funktionell ack skickas. <br>Misslyckades = mottog eller skickade ett ogiltigt X12-meddelande. <br>Väntar = tar emot eller skickade ett giltigt X12-meddelande. Funktions ack har kon figurer ATS och en funktionell ack förväntas. |
| **Följ** | Status för funktionell ack (997) <br>Accepterad = mottagen eller skickade en positiv funktionell ack. <br>Nekad = mottagen eller skickad negativ funktionell ack. <br>Väntar = en funktions ack förväntas men tas inte emot. <br>Väntar = genererar en funktionell ack men kan inte skicka till partner. <br>Inte obligatoriskt = funktions ack har inte kon figurer ATS. |
| **Riktning** | X12 meddelande riktning |
| **Spårnings-ID** | Det ID som motsvarar alla utlösare och åtgärder i en Logic app |
| **Typ av meddelande** | Meddelande typen EDI-X12 |
| **ICN** | Utbytes kontroll numret för X12-meddelandet |
| **TSCN** | Kontroll nummer för transaktions uppsättning för X12-meddelandet |
| **Tidsstämpel** | Tiden då X12-åtgärden bearbetade meddelandet |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Beskrivningar av EDIFACT meddelande egenskaper

Här är egenskaps beskrivningarna för varje EDIFACT-meddelande.

| Egenskap | Beskrivning |
|----------|-------------|
| **Avsändare** | Gäst partnern som anges i **ta emot inställningar** eller värd partnern som anges i **Skicka inställningar** för ett EDIFACT-avtal |
| **Mottagare** | Värd partnern som anges i **ta emot inställningar** eller gäst partnern som anges i **Skicka inställningar** för ett EDIFACT-avtal |
| **Logikapp** | Den Logic app där EDIFACT-åtgärderna har kon figurer ATS |
| **Status** | Status för EDIFACT-meddelande <br>Lyckades = tar emot eller skickade ett giltigt EDIFACT-meddelande. Ingen funktions ack har kon figurer ATS. <br>Lyckades = tar emot eller skickade ett giltigt EDIFACT-meddelande. Funktions ack har kon figurer ATS och tagits emot, eller en funktionell ack skickas. <br>Misslyckades = mottaget eller skickat ett ogiltigt EDIFACT-meddelande <br>Väntar = tar emot eller skickade ett giltigt EDIFACT-meddelande. Funktions ack har kon figurer ATS och en funktionell ack förväntas. |
| **Följ** | Status för funktionell ack (CONTRL) <br>Accepterad = mottagen eller skickade en positiv funktionell ack. <br>Nekad = mottagen eller skickad negativ funktionell ack. <br>Väntar = en funktions ack förväntas men tas inte emot. <br>Väntar = genererar en funktionell ack men kan inte skicka till partner. <br>Inte obligatoriskt = funktions ack har inte kon figurer ATS. |
| **Riktning** | EDIFACT meddelande riktning |
| **Spårnings-ID** | Det ID som motsvarar alla utlösare och åtgärder i en Logic app |
| **Typ av meddelande** | Meddelande typen EDIFACT |
| **ICN** | Utbytes kontroll numret för EDIFACT-meddelandet |
| **TSCN** | Kontroll nummer för transaktions uppsättning för EDIFACT-meddelandet |
| **Tidsstämpel** | Tiden då EDIFACT-åtgärden bearbetade meddelandet |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Nästa steg

* [Skapa frågor om övervakning och spårning](../logic-apps/create-monitoring-tracking-queries.md)
