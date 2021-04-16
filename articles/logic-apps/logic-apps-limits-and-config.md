---
title: Gränser och konfigurering
description: Tjänstbegränsningar, till exempel varaktighet, dataflöde och kapacitet, plus konfigurationsvärden, till exempel IP-adresser som tillåts för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/16/2021
ms.openlocfilehash: 9887350842749809dd4c3708acc71a08f416e54f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565890"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Information om begränsningar och konfiguration för Azure Logic Apps

I den här artikeln beskrivs begränsningar och konfigurationsinformation för att skapa och köra automatiserade arbetsflöden med Azure Logic Apps. Mer Power Automate finns [i Gränser och konfiguration i Power Automate](/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Definitionsgränser för logikapp

Här är gränserna för en enskild logikappsdefinition:

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Åtgärder per arbetsflöde | 500 | Om du vill utöka den här gränsen kan du lägga till kapslade arbetsflöden efter behov. |
| Tillåtet kapslingsdjup för åtgärder | 8 | Om du vill utöka den här gränsen kan du lägga till kapslade arbetsflöden efter behov. |
| Arbetsflöden per region och prenumeration | 1 000 | |
| Utlösare per arbetsflöde | 10 | När du arbetar i kodvyn, inte designern |
| Gräns för switch-omfångsfall | 25 | |
| Variabler per arbetsflöde | 250 | |
| Namn för `action` eller `trigger` | 80 tecken | |
| Tecken per uttryck | 8 192 | |
| Längden på `description` | 256 tecken | |
| Maximalt antal `parameters` | 50 | |
| Maximalt antal `outputs` | 10 | |
| Maximal storlek för `trackedProperties` | 16 000 tecken |
| Infogade kodåtgärder – Maximalt antal kodtecken | 1 024 tecken | Om du vill utöka den här gränsen till 100 000 tecken skapar du dina logikappar med resurstypen **Logikapp (förhandsversion),** antingen med hjälp av [Azure Portal](create-stateful-stateless-workflows-azure-portal.md) eller med hjälp av Visual Studio Code och [Azure Logic Apps-tillägget **(förhandsversion).**](create-stateful-stateless-workflows-visual-studio-code.md) |
| Infogade kodåtgärder – Maximal varaktighet för kod som körs | 5 sekunder | Om du vill utöka den här gränsen till 15 sekunder skapar du dina logikappar med resurstypen Logikapp **(förhandsversion),** antingen med hjälp av [Azure Portal](create-stateful-stateless-workflows-azure-portal.md) eller med hjälp av Visual Studio Code och [ **Azure Logic Apps-tillägget (förhandsversion).**](create-stateful-stateless-workflows-visual-studio-code.md) |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Gränser för körningens varaktighet och kvarhållningshistorik

Här är gränserna för en enskild logikappkörning:

| Name | Gräns för flera innehavare | Gräns för integreringstjänstmiljö | Kommentarer |
|------|--------------------|---------------------------------------|-------|
| Körningens varaktighet | 90 dagar | 366 dagar | Körningens varaktighet beräknas med starttiden för en körning och den gräns [](#change-duration) som anges i arbetsflödesinställningen, Kvarhållning av körningshistorik i dagar vid den starttiden. <p><p>Information om hur du ändrar standardgränsen finns i [Ändra körningens varaktighet och kvarhållning av historik i lagringen.](#change-duration) |
| Kvarhållning av körningshistorik i lagring | 90 dagar | 366 dagar | Om en körnings varaktighet överskrider kvarhållningsgränsen för den aktuella körningshistoriken tas körningen bort från körningshistoriken i lagringen. Oavsett om körningen slutförs eller tidsgränsen går ut beräknas kvarhållningen av körningshistoriken alltid med hjälp av körningens starttid och den aktuella gränsen som anges i arbetsflödesinställningen, Kvarhållning av körningshistorik [**i dagar**](#change-retention). Oavsett föregående gräns används den aktuella gränsen alltid för beräkning av kvarhållning. <p><p>Mer information om hur du ändrar standardgränsen finns i Ändra [kvarhållning av varaktighet och körningshistorik i lagring.](#change-retention) Om du vill öka maxgränsen [kontaktar du Logic Apps team för](mailto://logicappspm@microsoft.com) att få hjälp med dina krav. |
| Minsta upprepningsintervall | 1 sekund | 1 sekund ||
| Maximalt upprepningsintervall | 500 dagar | 500 dagar ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Ändra körningens varaktighet och kvarhållning av historik i lagring

Samma inställning styr det maximala antalet dagar som ett arbetsflöde kan köras och för att behålla körningshistoriken i lagringen. Följ dessa steg om du vill ändra standardgränsen eller den aktuella gränsen för dessa egenskaper.

* För logikappar i Azure med flera innehavare är standardgränsen på 90 dagar samma som den högsta gränsen. Du kan bara minska det här värdet.

* För logikappar i en integreringstjänstmiljö kan du minska eller öka standardgränsen på 90 dagar.

Anta till exempel att du minskar kvarhållningsgränsen från 90 dagar till 30 dagar. En 60-dagars gammal körning tas bort från körningshistoriken. Om du ökar kvarhållningsperioden från 30 dagar till 60 dagar finns en 20-dagars körning kvar i körningshistoriken i ytterligare 40 dagar.

> [!IMPORTANT]
> Om en körnings varaktighet överskrider kvarhållningsgränsen för den aktuella körningshistoriken tas körningen bort från körningshistoriken i lagringen. För att undvika att körningshistoriken  går förlorade kontrollerar du att kvarhållningsgränsen alltid är längre än körningens längsta möjliga varaktighet.

1. I [sökrutan Azure Portal](https://portal.azure.com) du och väljer **Logikappar**.

1. Leta upp och välj din logikapp. Öppna logikappen i Logikappdesignern.

1. På logikappens meny väljer du **Arbetsflödesinställningar.**

1. Under **Körningsalternativ går** du till listan **Kvarhållning av körningshistorik i** dagar och väljer **Anpassad.**

1. Dra skjutreglaget för att ändra antalet dagar som du vill ha.

1. När du är klar går du till verktygsfältet **Arbetsflödesinställningar** och väljer **Spara**.

Om du skapar en Azure Resource Manager mall för logikappen visas den här inställningen som en egenskap i arbetsflödets resursdefinition, som beskrivs i mallreferensen [för Microsoft.Logic-arbetsflöden:](/azure/templates/microsoft.logic/workflows)

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Gränser för samtidighet, loopning och debatching

Här är gränserna för en enskild logikappkörning:

### <a name="loops"></a>Loopar

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Foreach-matrisobjekt | 100 000 | Den här gränsen beskriver det maximala antalet matrisobjekt som en "för varje"-loop kan bearbeta. <p><p>Om du vill filtrera större matriser kan du använda [frågeåtgärden](logic-apps-perform-data-operations.md#filter-array-action). |
| Foreach-samtidighet | Med samtidighet av: 20 <p><p>Med samtidighet på: <p><p>– Standard: 20 <br>- Min: 1 <br>– Max: 50 | Den här gränsen är det maximala antalet "för varje" loop-iterationer som kan köras samtidigt eller parallellt. <p><p>Information om hur du ändrar den här gränsen [finns i Ändra "för varje" samtidighetsgräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) eller [Kör "för varje" loopar sekventiellt](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Until-iterationer | – Standard: 60 <br>- Min: 1 <br>– Max: 5 000 | Det maximala antalet cykler som en "Until"-loop kan ha under en logikappkörning. <p><p>Om du vill ändra den här gränsen väljer du Ändra gränser i loopformen "Until" och anger värdet för **egenskapen** Antal. |
| Tills tidsgränsen är över | – Standard: PT1H (1 timme) | Den tid som "Until"-loopen kan köras innan den avslutas och anges i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601). Timeoutvärdet utvärderas för varje loopcykel. Om någon åtgärd i loopen tar längre tid än tidsgränsen stoppas inte den aktuella cykeln. Nästa cykel startar dock inte eftersom begränsningsvillkoret inte uppfylls. <p><p>Om du vill ändra den här gränsen går du till loopformen "Until" och väljer Ändra gränser och anger värdet för **egenskapen Timeout.** |
||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>Samtidighet och debatching

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Utlösar samtidighet | Med samtidighet av: Obegränsat <p><p>Med samtidighet på, som du inte kan ångra när du har aktivera: <p><p>– Standard: 25 <br>- Min: 1 <br>– Max: 100 | Den här gränsen är det maximala antalet logikappinstanser som kan köras samtidigt eller parallellt. <p><p>**Obs!** När samtidighet är aktiverat minskas SplitOn-gränsen till 100 objekt för [debatching-matriser](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Information om hur du ändrar den här gränsen [finns i Ändra samtidighetsgräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) för utlösare eller Utlösa instanser [sekventiellt](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximalt antal väntande körningar | Med samtidighet av: <p><p>- Min: 1 <br>– Max: 50 <p><p>Med samtidighet på: <p><p>– Min: 10 plus antalet samtidiga körningar (utlösar samtidighet) <br>– Max: 100 | Den här gränsen är det maximala antalet logikappinstanser som kan vänta på att köras när logikappen redan kör det maximala antalet samtidiga instanser. <p><p>Information om hur du ändrar den här gränsen finns [i Ändra gränsen för väntande körningar.](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs) |
| SplitOn-objekt | Med samtidighet av: 100 000 <p><p>Med samtidighet på: 100 | För utlösare som returnerar en matris kan du ange ett uttryck som använder en SplitOn-egenskap som delar upp eller [debatchar](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) matrisobjekt i flera arbetsflödesinstanser för bearbetning, i stället för att använda en Foreach-loop. Det här uttrycket refererar till matrisen som ska användas för att skapa och köra en arbetsflödesinstans för varje matrisobjekt. <p><p>**Obs!** När samtidighet är aktiverat minskas SplitOn-gränsen till 100 objekt. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Här är gränserna för en enskild logikappsdefinition:

### <a name="multi-tenant-logic-apps-service"></a>Tjänst för Logic Apps klientorganisation

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Åtgärd: Körningar per rullande intervall på 5 minuter | – 100 000 körningar (standard) <p><p>– 300 000 körningar (max i läget för högt dataflöde)  | Information om hur du höjer standardgränsen till maxgränsen för logikappen finns i [Kör i läget för högt dataflöde](#run-high-throughput-mode), som är i förhandsversion. Eller så kan du [distribuera arbetsbelastningen över mer än en logikapp](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) efter behov. |
| Åtgärd: Samtidiga utgående anrop | ~ 2 500 | Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
| Körningsslutpunkt: Samtidiga inkommande anrop | ~1 000 | Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
| Körningsslutpunkt: Läsa anrop per 5 minuter  | 60 000 | Den här gränsen gäller för anrop som hämtar råa indata och utdata från en logikapps körningshistorik. Du kan distribuera arbetsbelastningen över mer än en app efter behov. |
| Körningsslutpunkt: Anropa anrop per 5 minuter | 45 000 | Du kan distribuera arbetsbelastningen över fler än en app efter behov. |
| Innehållsgenomflöde per 5 minuter | 600 MB | Du kan distribuera arbetsbelastningen över fler än en app efter behov. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Kör i läget för högt dataflöde

För en enda logikappdefinition har antalet åtgärder som körs var femte minut en [standardgräns.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Om du vill höja [](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) standardgränsen till maxgränsen för logikappen, som är tre gånger standardgränsen, kan du aktivera läget för högt dataflöde, som är i förhandsversion. Eller så kan du [distribuera arbetsbelastningen över mer än en logikapp](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) efter behov.

1. I Azure Portal väljer du Arbetsflödesinställningar under **Inställningar** på **logikappmenyn.**

1. Under **Körningsalternativ**  >  **Högt dataflöde** ändrar du inställningen till **På**.

   ![Skärmbild som visar logikappmenyn i Azure Portal med "Arbetsflödesinställningar" och "Högt dataflöde" inställt på "På".](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Om du vill aktivera den här inställningen i en ARM-mall för att distribuera logikappen lägger du till objektet i objektet för logikappens resursdefinition med egenskapen `properties` `runtimeConfiguration` inställd på `operationOptions` `OptimizedForHighThroughput` :

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Mer information om resursdefinitionen för logikappen finns i [Översikt: Automatisera](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)distribution för Azure Logic Apps med hjälp av Azure Resource Manager mallar .

### <a name="integration-service-environment-ise"></a>Integration Service Environment (ISE)

* [Ise-SKU för](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)utvecklare: Ger upp till 500 körningar per minut, men tänk på följande:

  * Se till att du endast använder denna SKU för utforskning, experiment, utveckling eller testning – inte för produktions- eller prestandatestning. Denna SKU har inget serviceavtal (SLA), uppskalningskapacitet eller redundans under återanvändning, vilket innebär att du kan uppleva fördröjningar eller driftstopp.

  * Uppdateringar av backend kan tillfälligt avbryta tjänsten.

* [Premium ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)I följande tabell beskrivs den här SKU:ns dataflödesgränser, men om du vill överskrida dessa gränser vid normal bearbetning eller köra belastningstestning som kan överskrida dessa gränser kontaktar du [Logic Apps-teamet](mailto://logicappsemail@microsoft.com) för att få hjälp med dina krav.

  | Name | Gräns | Kommentarer |
  |------|-------|-------|
  | Körningsgräns för basenhet | Systembegränsning när infrastrukturkapaciteten når 80 % | Tillhandahåller ~4 000 åtgärdskörningar per minut, vilket är ~160 miljoner åtgärdskörningar per månad |
  | Körningsgräns för skalningsenhet | Systembegränsning när infrastrukturkapaciteten når 80 % | Varje skalningsenhet kan ge ~2 000 ytterligare åtgärdskörningar per minut, vilket är cirka 80 miljoner fler åtgärdskörningar per månad |
  | Maximalt antal skalningsenheter som du kan lägga till | 10 | |
  ||||

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Gatewaygränser

Azure Logic Apps stöder skrivåtgärder, inklusive infogningar och uppdateringar, via gatewayen. Dessa åtgärder har dock begränsningar [för nyttolastens storlek.](/data-integration/gateway/service-gateway-onprem#considerations)

<a name="http-limits"></a>

## <a name="http-limits"></a>HTTP-gränser

Här är gränserna för ett enskilt inkommande eller utgående anrop:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Timeout-varaktighet

Vissa anslutningsåtgärder gör asynkrona anrop eller lyssnar efter webhook-begäranden, så tidsgränsen för dessa åtgärder kan vara längre än dessa gränser. Mer information finns i teknisk information för den specifika anslutningsappen och [arbetsflödesutlösare och åtgärder.](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

| Name | Logic Apps (flera klient) | Logic Apps (förhandsversion) | Integreringstjänstmiljö | Kommentarer |
|------|---------------------------|----------------------|---------------------------------|-------|
| Utgående begäran | 120 sekunder <br>(2 minuter) | 230 sekunder <br>(3,9 minuter) | 240 sekunder <br>(4 minuter) | Exempel på utgående begäranden är anrop som görs av HTTP-utlösaren eller åtgärden. Mer information om förhandsversionen finns i [Azure Logic Apps Preview](logic-apps-overview-preview.md). <p><p>**Tips:** Använd ett asynkront avsökningsmönster [eller en](../logic-apps/logic-apps-create-api-app.md#async-pattern) [until-loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)för åtgärder som körs längre. Om du vill komma runt tidsgränsgränser när [](logic-apps-http-endpoint.md)du anropar en annan logikapp som har en anropsbar slutpunkt kan du använda den inbyggda **Azure Logic Apps-åtgärden** i stället, som du hittar i anslutningsväljaren under Inbyggd . |
| Inkommande begäran | 120 sekunder <br>(2 minuter) | 230 sekunder <br>(3,9 minuter) | 240 sekunder <br>(4 minuter) | Exempel på inkommande begäranden är anrop som tas emot av begärandeutlösaren, HTTP Webhook-utlösaren och HTTP Webhook-åtgärden. Mer information om förhandsversionen finns i [Azure Logic Apps Preview](logic-apps-overview-preview.md). <p><p>**Obs!** För att den ursprungliga anroparen ska få svaret måste alla steg i svaret slutföras inom gränsen om du inte anropar en annan logikapp som ett kapslat arbetsflöde. Mer information finns i [Anropa, utlösa eller kapsla logikappar.](../logic-apps/logic-apps-http-endpoint.md) |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Meddelandestorlek

| Name | Gräns för flera innehavare | Gräns för integreringstjänstmiljö | Kommentarer |
|------|--------------------|---------------------------------------|-------|
| Meddelandestorlek | 100 MB | 200 MB | Information om hur du kan komma runt den här gränsen [finns i Hantera stora meddelanden med segmentering.](../logic-apps/logic-apps-handle-large-messages.md) Vissa anslutningsappar och API:er kanske inte stöder segmentering eller ens standardgränsen. <p><p>– Anslutningsappar som AS2, X12 och EDIFACT har sina egna [B2B-meddelandegränser.](#b2b-protocol-limits) <br>– ISE-anslutningsappar använder ISE-gränsen, inte gränserna för icke-ISE-anslutning. |
| Meddelandestorlek med segmentering | 1 GB | 5 GB | Den här gränsen gäller för åtgärder som antingen har inbyggt stöd för segmentering eller låter dig aktivera segmentering i deras körningskonfiguration. <p><p>Om du använder en ISE stöder Logic Apps-motorn den här gränsen, men anslutningsappar har egna segmentgränser upp till motorgränsen. Se till exempel [API-referensen för Azure Blob Storage-anslutningsappen.](/connectors/azureblob/) Mer information om segmentering finns i [Hantera stora meddelanden med segmentering.](../logic-apps/logic-apps-handle-large-messages.md) |
|||||

#### <a name="character-limits"></a>Teckenbegränsningar

| Name | Gräns | Kommentarer |
|------|-------|-------|
| Gräns för uttrycksutvärdering | 131 072 tecken | Uttrycken `@concat()` , , får inte vara längre än den här `@base64()` `@string()` gränsen. |
| Teckengräns för begärande-URL | 16 384 tecken | |
||||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Återförsöksprincip

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Antal återförsök | 90 | Standardvärdet är 4. Om du vill ändra standardvärdet använder du [principparametern retry](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximal tid innan nytt försök | 1 dag | Om du vill ändra standardvärdet använder du [principparametern retry](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimal tid innan nytt försök | 5 sekunder | Om du vill ändra standardvärdet använder du [principparametern retry](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Autentiseringsgränser

Här är gränserna för en logikapp som börjar med en begärandeutlösare och aktiverar [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) för auktorisering av inkommande anrop till begärandeutlösaren:

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Azure AD-auktoriseringsprinciper | 5 | |
| Anspråk per auktoriseringsprincip | 10 | |
| Anspråksvärde – maximalt antal tecken | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Gränser för anpassade anslutningsappar

Här är gränserna för anpassade anslutningsappar som du kan skapa från webb-API:er.

| Name | Gräns för flera innehavare | Gräns för integreringstjänstmiljö | Kommentarer |
|------|--------------------|---------------------------------------|-------|
| Antal anpassade anslutningsappar | 1 000 per Azure-prenumeration | 1 000 per Azure-prenumeration ||
| Antal begäranden per minut för en anpassad anslutningsapp | 500 begäranden per minut per anslutning | 2 000 begäranden per minut per *anpassad anslutningsapp* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Hanterade identiteter

| Name | Gräns |
|------|-------|
| Hanterade identiteter per logikapp | Antingen den system tilldelade identiteten eller en användar tilldelad identitet |
| Antal logikappar som har en hanterad identitet i en Azure-prenumeration per region | 1 000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Gränser för integrationskonto

Varje Azure-prenumeration har följande begränsningar för integrationskontot:

* Ett [integrationskonto](../logic-apps/logic-apps-pricing.md#integration-accounts) på kostnadsfri nivå per Azure-region. Den här nivån är endast tillgänglig för offentliga regioner i Azure, till exempel USA, västra eller Sydostasien, men inte [för Azure China 21Vianet](/azure/china/overview-operations) [eller Azure Government](../azure-government/documentation-government-welcome.md).

* 1 000 totala integrationskonton, inklusive integrationskonton i alla [integrationstjänstmiljöer (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) för både [Developer- och Premium-SKU:er.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Varje ISE, oavsett om det är Developer eller [Premium,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)kan använda ett enda integrationskonto utan extra kostnad, även om den inkluderade kontotypen varierar beroende på ISE SKU. Du kan skapa fler integrationskonton för din ISE upp till den totala gränsen för en [extra kostnad:](logic-apps-pricing.md#fixed-pricing)

  | ISE SKU | Gränser för integrationskonto |
  |---------|----------------------------|
  | **Premium** | 20 konton totalt, inklusive ett Standard-konto utan extra kostnad. Med den här SKU:n kan du bara ha [standardkonton.](../logic-apps/logic-apps-pricing.md#integration-accounts) Inga kostnadsfria eller grundläggande konton tillåts. |
  | **Utvecklare** | 20 totalt antal konton, inklusive ett [kostnadsfritt](../logic-apps/logic-apps-pricing.md#integration-accounts) konto (begränsat till 1). Med den här SKU:n kan du ha en kombination av: <p>– Ett kostnadsfritt konto och upp till 19 [standardkonton.](../logic-apps/logic-apps-pricing.md#integration-accounts) <br>– Inget kostnadsfritt konto och upp till 20 Standard-konton. <p>Inga grundläggande eller ytterligare kostnadsfria konton tillåts. <p><p>**Viktigt!** Använd [Developer SKU för](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) att experimentera, utveckla och testa, men inte för produktions- eller prestandatestning. |
  |||

Information om hur priser och fakturering fungerar för ISE:er finns Logic Apps [prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prisinformation finns i [Logic Apps priser.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefaktgränser per integrationskonto

Här är gränserna för antalet artefakter för varje integrationskontonivå. Prisinformation finns i [Logic Apps priser.](https://azure.microsoft.com/pricing/details/logic-apps/) Information om hur priser och fakturering fungerar för integrationskonton finns i [Logic Apps prismodellen](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Använd endast den kostnadsfria nivån för undersökande scenarier, inte produktionsscenarier. Den här nivån begränsar dataflöde och användning och har inget serviceavtal (SLA).

| Artefakt | Kostnadsfri | Grundläggande | Standard |
|----------|------|-------|----------|
| EDI-handelspartner | 10 | 1 | 1 000 |
| EDI-handelspartner | 25 | 2 | 1 000 |
| Maps | 25 | 500 | 1 000 |
| Scheman | 25 | 500 | 1 000 |
| Sammansättningar | 10 | 25 | 1 000 |
| Certifikat | 25 | 2 | 1 000 |
| Batch-konfigurationer | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Kapacitetsbegränsningar för artefakter

| Artefakt | Gräns | Kommentarer |
| -------- | ----- | ----- |
| Sammansättning | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du ett [Azure Storage-konto och en blobcontainer.](../logic-apps/logic-apps-enterprise-integration-schemas.md) |
| Karta (XSLT-fil) | 8 MB | Om du vill ladda upp filer som är större än 2 MB [använder du Azure Logic Apps REST API – Maps](/rest/api/logic/maps/createorupdate). <p><p>**Obs!** Mängden data eller poster som en karta kan bearbeta baseras på meddelandestorleken och åtgärdens tidsgränsgränser i Azure Logic Apps. Om du till exempel använder en [](#http-limits)HTTP-åtgärd, baserat på HTTP-meddelandestorlek och tidsgränsgränser , kan en karta bearbeta data upp till storleksgränsen för HTTP-meddelanden om åtgärden slutförs inom HTTP-tidsgränsen. |
| Schema | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du ett [Azure Storage-konto och en blobcontainer.](../logic-apps/logic-apps-enterprise-integration-schemas.md) |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Dataflödesbegränsningar

| Körningsslutpunkt | Kostnadsfri | Grundläggande | Standard | Kommentarer |
|------------------|------|-------|----------|-------|
| Läs anrop per 5 minuter | 3 000 | 30,000 | 60 000 | Den här gränsen gäller för anrop som hämtar råa indata och utdata från en logikapps körningshistorik. Du kan distribuera arbetsbelastningen över fler än ett konto efter behov. |
| Anropa anrop per 5 minuter | 3 000 | 30,000 | 45 000 | Du kan distribuera arbetsbelastningen över fler än ett konto efter behov. |
| Spåra anrop per 5 minuter | 3 000 | 30,000 | 45 000 | Du kan distribuera arbetsbelastningen över fler än ett konto efter behov. |
| Blockera samtidiga anrop | ~1 000 | ~1 000 | ~1 000 | Samma för alla SKU:er. Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Meddelandestorlek för B2B-protokoll (AS2, X12, EDIFACT)

Här är de storleksgränser för meddelanden som gäller för B2B-protokoll:

| Name | Gräns för flera innehavare | Gräns för integreringstjänstmiljö | Kommentarer |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1 – 25 MB | v2 – 200 MB <br>v1 – 25 MB | Gäller för avkodning och kodning |
| X12 | 50 MB | 50 MB | Gäller för avkodning och kodning |
| EDIFACT | 50 MB | 50 MB | Gäller för avkodning och kodning |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Inaktivera eller ta bort logikappar

När du inaktiverar en logikapp instansieras inga nya körningar. Alla pågående och väntande körningar fortsätter tills de har slutförts, vilket kan ta tid att slutföra.

När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Brandväggskonfiguration: IP-adresser och tjänsttaggar

När logikappen behöver kommunicera via en brandvägg som begränsar trafiken till specifika  IP-adresser måste brandväggen tillåta åtkomst för både [de inkommande](#inbound) och utgående IP-adresserna som används av Logic Apps-tjänsten eller körningen i Azure-regionen där logikappen finns. [](#outbound) *Alla* logikappar i samma region använder samma IP-adressintervall.

För att till exempel stödja anrop som logikappar i regionen USA, västra skickar eller tar emot via inbyggda utlösare och åtgärder, till exempel  [HTTP-utlösaren](../connectors/connectors-native-http.md)eller åtgärden , måste brandväggen tillåta åtkomst för alla inkommande IP-adresser och utgående IP-adresser för Logic Apps-tjänsten som finns i regionen USA, västra. 

Om logikappen även använder hanterade anslutningsappar, till exempel Office 365 Outlook-anslutningsappen eller SQL-anslutningsappen, eller använder anpassade anslutningsappar, måste brandväggen även tillåta åtkomst för alla utgående [IP-adresser](#outbound) för hanterade anslutningsappar i logikappens Azure-region. [](../connectors/apis-list.md#managed-api-connectors) [](/connectors/custom-connectors/)  Om du använder anpassade anslutningsappar som har åtkomst till lokala resurser via den lokala [datagatewayresursen](logic-apps-gateway-connection.md)i Azure måste du dessutom konfigurera gatewayinstallationen så att den tillåter åtkomst för motsvarande utgående *[IP-adresser](#outbound)* för hanterade anslutningar.

Mer information om hur du ställer in kommunikationsinställningar på gatewayen finns i följande avsnitt:

* [Justera kommunikationsinställningar för den lokala datagatewayen](/data-integration/gateway/service-gateway-communication)
* [Konfigurera proxyinställningar för den lokala datagatewayen](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Överväganden för BRANDVÄGGS-IP-konfiguration

Innan du ställer in brandväggen med IP-adresser bör du läsa dessa överväganden:

* Om du använder [Power Automate](/power-automate/getting-started)går vissa åtgärder, till exempel **HTTP** och **HTTP + OpenAPI,** direkt via Azure Logic Apps-tjänsten och kommer från DE IP-adresser som anges här. Mer information om IP-adresser som används av Power Automate finns [i Gränser och konfiguration för Power Automate](/flow/limits-and-config#ip-address-configuration).

* För [Azure China 21Vianet](/azure/china/)är fasta eller reserverade IP-adresser inte tillgängliga för anpassade anslutningsappar och för hanterade anslutningsappar, till exempel Azure Storage, SQL Server, Office 365 Outlook och så vidare. [](../logic-apps/custom-connector-overview.md) [](../connectors/apis-list.md#managed-api-connectors)

* Om dina logikappar körs i en [integrationstjänstmiljö (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md)kontrollerar du att du [även öppnar dessa portar.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)

* För att förenkla eventuella säkerhetsregler som du vill skapa [](../virtual-network/service-tags-overview.md) kan du välja att använda tjänsttaggar i stället för att ange IP-adressprefix för varje region. De här taggarna fungerar i de regioner Logic Apps tjänsten är tillgänglig:

  * **LogicAppsManagement:** Representerar de inkommande IP-adressprefixen för Logic Apps tjänsten.

  * **LogicApps:** Representerar de utgående IP-adressprefixen för Logic Apps tjänsten.

  * **AzureConnectors:** Representerar IP-adressprefix för hanterade anslutningsappar som gör inkommande webhook-återanrop till Logic Apps-tjänsten och utgående anrop till sina respektive tjänster, till exempel Azure Storage eller Azure Event Hubs.

* Om logikapparna har problem med att komma åt Azure Storage-konton som använder [brandväggar](../storage/common/storage-network-security.md)och brandväggsregler har du olika [andra alternativ för att aktivera åtkomst.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

  Logikappar kan till exempel inte direkt komma åt lagringskonton som använder brandväggsregler och som finns i samma region. Men om du tillåter utgående IP-adresser för hanterade anslutningsappar i din region kan dina logikappar komma åt [lagringskonton](../logic-apps/logic-apps-limits-and-config.md#outbound)som finns i en annan region förutom när du använder Azure Table Storage- eller Azure Queue Storage-anslutningsappar. Om du vill komma Table Storage eller Queue Storage kan du använda HTTP-utlösaren och åtgärderna i stället. Andra alternativ finns i Åtkomst [till lagringskonton bakom brandväggar.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Inkommande IP-adresser

I det här avsnittet visas de inkommande IP-adresserna Azure Logic Apps tjänsten. Om du har Azure Government kan du [Azure Government - Inkommande IP-adresser.](#azure-government-inbound)

> [!TIP]
> För att minska komplexiteten när du skapar säkerhetsregler [](../virtual-network/service-tags-overview.md)kan du välja att använda tjänsttaggen **LogicAppsManagement** i stället för att ange inkommande IP-Logic Apps-adressprefix för varje region. Du kan också använda **tjänsttaggen AzureConnectors** för hanterade anslutningsappar som gör inkommande webhook-återanrop till Logic Apps-tjänsten i stället för att ange IP-adressprefix för inkommande hanterade anslutningar för varje region. De här taggarna fungerar i de regioner Logic Apps tjänsten är tillgänglig.
>
> Följande anslutningsappar gör inkommande webhook-återanrop till Logic Apps tjänsten:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign Demo, Adobe Sign Preview, Adobe Sign Stage, Azure Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign Demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, Shifts for Microsoft Teams, Teamwork Projects, Typeform
>
> \***SAP:** Returanroparen beror på om distributionsmiljön är Azure eller ISE för flera innehavare. I miljön för flera innehavare gör den lokala datagatewayen anropet tillbaka till Logic Apps tjänsten. I en ISE gör SAP-anslutningsappen anropet tillbaka till Logic Apps tjänsten.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Azure för flera innehavare – inkommande IP-adresser

| Region för flera innehavare | IP-adress |
|---------------------|----|
| Australien, östra | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australien, sydöstra | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brasilien, södra | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Brasilien, sydöstra | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Kanada, centrala | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada, östra | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indien, centrala | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Central US | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Asien, östra | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| East US | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| USA, östra 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Frankrike, centrala | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Frankrike, södra | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Tyskland, norra | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Tyskland, västra centrala | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Japan, östra | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japan, västra | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Sydkorea, centrala | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Sydkorea, södra | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA, norra centrala | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa, norra | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Mellanöstern | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Sydafrika, norra | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Sydafrika, västra | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA, södra centrala | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indien, södra | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sydostasien | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Schweiz, norra | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Schweiz, västra | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| Förenade Arabemiraten, centrala | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Förenade Arabemiraten, norra | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Storbritannien, södra | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Storbritannien, västra | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA, västra centrala | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa, västra | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Indien, västra | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA, västra | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA, västra 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government – Inkommande IP-adresser

| Azure Government region | IP-adress |
|-------------------------|----|
| US Gov, Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov, Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov, Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| USA DoD, centrala | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Utgående IP-adresser

I det här avsnittet visas de utgående IP-adresserna för Azure Logic Apps-tjänsten och hanterade anslutningsappar. Om du har Azure Government kan du [Azure Government – utgående IP-adresser.](#azure-government-outbound)

> [!TIP]
> För att minska komplexiteten när du skapar säkerhetsregler kan du välja att använda tjänsttaggen **LogicApps** [i](../virtual-network/service-tags-overview.md)stället för att ange utgående IP Logic Apps-adressprefix för varje region. Alternativt kan du också använda **tjänsttaggen AzureConnectors** för hanterade anslutningsappar som gör utgående anrop till sina respektive tjänster, till exempel Azure Storage eller Azure Event Hubs, i stället för att ange IP-adressprefix för utgående hanterad anslutningsapp för varje region. De här taggarna fungerar i de regioner Logic Apps tjänsten är tillgänglig.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Azure för flera innehavare – utgående IP-adresser

| Region för flera innehavare | Logic Apps IP | IP för hanterade anslutningsappar |
|---------------------|---------------|-----------------------|
| Australien, östra | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Australien, sydöstra | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Brasilien, södra | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Brasilien, sydöstra | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 | 23.97.120.109, 23.97.121.26 |
| Kanada, centrala | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Kanada, östra | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| Indien, centrala | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| Central US | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Asien, östra | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| East US | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| USA, östra 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| Frankrike, centrala | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Frankrike, södra | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Tyskland, norra | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Tyskland, västra centrala | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Japan, östra | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Japan, västra | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Sydkorea, centrala | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Sydkorea, södra | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| USA, norra centrala | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Europa, norra | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Mellanöstern | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Sydafrika, norra | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Sydafrika, västra | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| USA, södra centrala | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Indien, södra | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Sydostasien | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Schweiz, norra | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| Schweiz, västra | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| Förenade Arabemiraten, centrala | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Förenade Arabemiraten, norra | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Storbritannien, södra | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Storbritannien, västra | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| USA, västra centrala | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| Europa, västra | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Indien, västra | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| USA, västra | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| USA, västra 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government – utgående IP-adresser

| Region | Logic Apps IP | IP för hanterade anslutningsappar |
|--------|---------------|-----------------------|
| USA DoD, centrala | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| US Gov, Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| US Gov, Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| US Gov, Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Nästa steg

* Lär dig hur [du skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Lär dig mer [om vanliga exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
