---
title: Gränser och konfigurering
description: Tjänst begränsningar, till exempel varaktighet, data flöde och kapacitet, plus konfigurations värden, till exempel IP-adresser som ska tillåtas, för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/05/2021
ms.openlocfilehash: 2debf7d350f4f1fde5e86a60ad03a6858bc02743
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490343"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Information om begränsningar och konfiguration för Azure Logic Apps

I den här artikeln beskrivs begränsningar och konfigurations information för att skapa och köra automatiserade arbets flöden med Azure Logic Apps. För automatisk energi förbrukning, se [gränser och konfiguration i energi spar läge](/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Definitions gränser för Logic app

Här är gränserna för en enda Logic app-definition:

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Åtgärder per arbetsflöde | 500 | Om du vill utöka den här gränsen kan du lägga till kapslade arbets flöden efter behov. |
| Tillåtet kapslings djup för åtgärder | 8 | Om du vill utöka den här gränsen kan du lägga till kapslade arbets flöden efter behov. |
| Arbets flöden per region per prenumeration | 1 000 | |
| Utlösare per arbets flöde | 10 | När du arbetar i kodvyn, inte i designern |
| Gräns för växel omfattnings fall | 25 | |
| Variabler per arbets flöde | 250 | |
| Namn för `action` eller `trigger` | 80 tecken | |
| Tecken per uttryck | 8 192 | |
| Längd på `description` | 256 tecken | |
| Maximalt antal `parameters` | 50 | |
| Maximalt antal `outputs` | 10 | |
| Maximal storlek för `trackedProperties` | 16 000 tecken |
| Infogad kod åtgärd-maximalt antal kod tecken | 1 024 tecken | Om du vill utöka den här gränsen till 100 000 tecken skapar du Logi Kap par med resurs typen **Logic app (förhands granskning)** , antingen [genom att använda Azure Portal](create-stateful-stateless-workflows-azure-portal.md) eller [genom att använda Visual Studio Code och tillägget **Azure Logic Apps (förhands granskning)**](create-stateful-stateless-workflows-visual-studio-code.md). |
| Infogad kod åtgärd-maximal varaktighet för körning av kod | 5 sekunder | Om du vill utöka den här gränsen till en 15 sekunder, skapar du Logi Kap par med resurs typen **Logic app (förhands granskning)** , antingen [genom att använda Azure Portal](create-stateful-stateless-workflows-azure-portal.md) eller [genom att använda Visual Studio Code och tillägget **Azure Logic Apps (förhands granskning)**](create-stateful-stateless-workflows-visual-studio-code.md). |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Körnings gränser för varaktighet och kvarhållning

Här följer gränserna för en enda Logic app-körning:

| Name | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Kommentarer |
|------|--------------------|---------------------------------------|-------|
| Körningens varaktighet | 90 dagar | 366 dagar | Körningens varaktighet beräknas med hjälp av start tiden för körning och den gräns som anges i arbets flödes inställningen. [**Kör historik kvarhållning i dagar**](#change-duration) vid den Start tiden. <p><p>Om du vill ändra standard gränsen, se [ändra körnings tid och historik kvarhållning i lagring](#change-duration). |
| Kvarhållning av körnings historik i lagring | 90 dagar | 366 dagar | Om körnings tiden överskrider den aktuella gränsen för körnings historik, tas körningen bort från körnings historiken i lagringen. Oavsett om körningen har slutförts eller om tids gränsen överskrids beräknas alltid körnings historiken med hjälp av körningens start tid och den aktuella gränsen som anges i arbets flödes inställningen, [**körning av historik i dagar**](#change-retention). Oavsett föregående gräns används alltid den aktuella gränsen för att beräkna kvarhållning. <p><p>Om du vill ändra standard gränsen och mer information, se [ändra varaktighet och körnings historik i lagring](#change-retention). Om du vill öka Max gränsen [kontaktar du Logic Appss teamet](mailto://logicappspm@microsoft.com) för att få hjälp med dina krav. |
| Lägsta upprepnings intervall | 1 sekund | 1 sekund ||
| Högsta upprepnings intervall | 500 dagar | 500 dagar ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Ändra körnings tid och historik kvarhållning i lagring

Samma inställning styr det maximala antalet dagar som ett arbets flöde kan köras och för att bevara körnings historiken i lagringen. Följ dessa steg om du vill ändra standard eller aktuell gräns för dessa egenskaper.

* För logi Kap par i Azure med flera innehavare, är standard gränsen på 90 samma som den maximala gränsen. Du kan bara minska det här värdet.

* För Logic Apps i en integrerings tjänst miljö kan du minska eller öka standard gränsen på 90 dagar.

Anta till exempel att du minskar gränsen för kvarhållning från 90 dagar till 30 dagar. En 60-dagars-gammal-körning tas bort från körnings historiken. Om du ökar kvarhållningsperioden från 30 dagar till 60 dagar, stannar en 20 dagars gammal körning i körnings historiken för ytterligare 40 dagar.

> [!IMPORTANT]
> Om körnings tiden överskrider den aktuella gränsen för körnings historik, tas körningen bort från körnings historiken i lagringen. För att undvika att köra historiken för körning kontrollerar du att kvarhållningsintervallet *alltid* är mer än körningens längsta möjliga varaktighet.

1. I sökrutan [Azure Portal](https://portal.azure.com) söker du efter **och väljer Logi** Kap par.

1. Hitta och välj din Logic app. Öppna din Logic app i Logic App Designer.

1. På menyn Logic Apps väljer du **arbets flödes inställningar**.

1. Under **körnings alternativ**, från listan **körnings historik för kvarhållning i dagar** , väljer du **anpassad**.

1. Dra skjutreglaget för att ändra antalet dagar som du vill ha.

1. När du är klar väljer du **Spara** i verktygsfältet **arbets flödes inställningar** .

Om du genererar en Azure Resource Manager mall för din Logic app visas den här inställningen som en egenskap i arbets flödets resurs definition, som beskrivs i [mallen Microsoft. Logic-arbetsflöden referens](/azure/templates/microsoft.logic/workflows):

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

## <a name="concurrency-looping-and-debatching-limits"></a>Samtidighets gränser, slingor och avbatchorder

Här följer gränserna för en enda Logic app-körning:

### <a name="loops"></a>Loopar

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Förgrunds mat ris objekt | 100 000 | Den här gränsen beskriver det maximala antalet mat ris objekt som en "for each"-loop kan bearbeta. <p><p>Du kan använda [åtgärden fråga](logic-apps-perform-data-operations.md#filter-array-action)för att filtrera större matriser. |
| Samtidighets samtidighet | Med samtidigering: 20 <p><p>Med samtidighet på: <p><p>-Standard: 20 <br>-Min: 1 <br>– Max: 50 | Den här gränsen är det högsta antalet iterationer som kan köras samtidigt eller parallellt. <p><p>Om du vill ändra den här gränsen läser du [ändra "för varje" samtidighets gräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) eller [Kör "för varje" slingor i följd](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Until-iterationer | -Standard: 60 <br>-Min: 1 <br>– Max: 5 000 | Det maximala antalet cykler som en "till"-loop kan ha under en Logic app-körning. <p><p>Om du vill ändra den här gränsen väljer du **ändra gränser** i formen "till" och anger värdet för egenskapen **Count** . |
| Tills timeout | -Standard: PT1H (1 timme) | Den längsta tid som loopen "fram" kan köras innan den avslutas och anges i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601). Timeout-värdet utvärderas för varje loop-cykel. Om en åtgärd i slingan tar längre tid än tids gränsen, stoppas inte den aktuella cykeln. Nästa cykel startar dock inte eftersom gräns villkoret inte är uppfyllt. <p><p>Om du vill ändra den här gränsen väljer du **ändra gränser** i formen "till" och anger värdet för egenskapen **timeout** . |
||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>Samtidighet och debatchering

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Utlös samtidighet | Med concurrency: obegränsat <p><p>Med samtidigitet på, som du inte kan ångra när du har aktiverat: <p><p>-Standard: 25 <br>-Min: 1 <br>– Max: 100 | Den här gränsen är det maximala antalet Logic App-instanser som kan köras samtidigt eller parallellt. <p><p>**Obs!** när samtidighet har Aktiver ATS minskas SplitOn-gränsen till 100 objekt för [debatchering av matriser](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Om du vill ändra den här gränsen, se [ändra utlösarens samtidighets gräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) eller [Utlös instansen i tur och ordning](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximalt antal väntande körningar | Med samtidigering: <p><p>-Min: 1 <br>– Max: 50 <p><p>Med samtidighet på: <p><p>-Min: 10 plus antalet samtidiga körningar (Utlös samtidighet) <br>– Max: 100 | Den här gränsen är det maximala antalet Logic App-instanser som kan vänta på att köras när din Logic app redan kör maximalt antal samtidiga instanser. <p><p>Om du vill ändra den här gränsen, se [begränsningen för ändrings väntande körning](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| SplitOn-objekt | Med samtidigering: 100 000 <p><p>Med samtidighet på: 100 | För utlösare som returnerar en matris kan du ange ett uttryck som använder en ' SplitOn '-egenskap som [delar upp eller avgruppera mat ris objekt i flera arbets flödes instanser](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) för bearbetning, i stället för att använda en "förgrunds"-slinga. Det här uttrycket refererar till matrisen som används för att skapa och köra en arbets flödes instans för varje mat ris objekt. <p><p>**Obs!** när samtidighet har Aktiver ATS minskas SplitOn-gränsen till 100 objekt. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Här är gränserna för en enda Logic app-definition:

### <a name="multi-tenant-logic-apps-service"></a>Logic Apps tjänst för flera innehavare

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Åtgärd: körningar per 5 minuters rullande intervall | – 100 000 körningar (standard) <p><p>-300 000 körningar (maximalt i läge med hög data flöde)  | För att öka standard gränsen till den maximala gränsen för din Logic app, se [köra i högt data flödes läge](#run-high-throughput-mode), som finns i för hands version. Eller så kan du [distribuera arbets belastningen i mer än en Logic app](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) vid behov. |
| Åtgärd: utgående utgående samtal | ~ 2 500 | Du kan minska antalet samtidiga förfrågningar eller minska varaktigheten om det behövs. |
| Runtime-slutpunkt: samtidiga inkommande samtal | ~ 1 000 | Du kan minska antalet samtidiga förfrågningar eller minska varaktigheten om det behövs. |
| Runtime-slutpunkt: Läs anrop per 5 minuter  | 60 000 | Den här gränsen gäller för anrop som hämtar rå data och utdata från en Logic Apps körnings historik. Du kan distribuera arbets belastningen i mer än en app vid behov. |
| Runtime-slutpunkt: anropa anrop per 5 minuter | 45 000 | Du kan distribuera arbets belastningen i mer än en app vid behov. |
| Innehålls data flöde per 5 minuter | 600 MB | Du kan distribuera arbets belastningen i mer än en app vid behov. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Kör i högt data flödes läge

För en enda Logic app-definition har antalet åtgärder som körs var 5: e minut en [standard gräns](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). För att öka standard gränsen till den [maximala gränsen](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) för din Logic app, som är tre gånger som standard gräns, kan du aktivera läget för hög data flöde, som finns i för hands version. Eller så kan du [distribuera arbets belastningen i mer än en Logic app](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) vid behov.

1. I Azure Portal väljer du **arbets flödes inställningar** under **Inställningar** på din Logic app-meny.

1. Under **körnings alternativ**  >  för **högt data flöde** ändrar du inställningen till **på**.

   ![Skärm bild som visar Logic app-menyn i Azure Portal med "arbets flödes inställningar" och "högt data flöde" inställt på "on".](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Om du vill aktivera den här inställningen i en ARM-mall för att distribuera din Logic app, `properties` lägger du till `runtimeConfiguration` objektet med `operationOptions` egenskapen inställt på i objekt för din Logic Apps resurs definition `OptimizedForHighThroughput` :

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

Mer information om din resurs definition för Logic app finns i [Översikt: Automatisera distribution av Azure Logic Apps med hjälp av Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

### <a name="integration-service-environment-ise"></a>Integrerings tjänst miljö (ISE)

* [Developer ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): ger upp till 500 körningar per minut, men tänk på följande:

  * Se till att du endast använder den här SKU: n för utforskning, experiment, utveckling eller testning – inte för produktion eller prestanda testning. Denna SKU har inget service avtal (SLA), skalnings kapacitet eller redundans under återvinning, vilket innebär att du kan uppleva fördröjningar eller drift stopp.

  * Server dels uppdateringar kan tillfälligt avbryta tjänsten.

* [Premium ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): följande tabell beskriver de data flödes gränser som finns i den här SKU: n, men för att överskrida dessa gränser vid normal bearbetning, eller köra belastnings testning som kan gå över dessa gränser, [Kontakta Logic Apps-teamet](mailto://logicappsemail@microsoft.com) om du behöver hjälp med dina krav.

  | Name | Gräns | Kommentarer |
  |------|-------|-------|
  | Bas enhets körnings gräns | Systemet begränsas när infrastruktur kapaciteten når 80% | Innehåller ~ 4 000 åtgärds körningar per minut, vilket är ~ 160 000 000 åtgärds körningar per månad |
  | Skalnings enhetens körnings gräns | Systemet begränsas när infrastruktur kapaciteten når 80% | Varje skalnings enhet kan ge ~ 2 000 ytterligare åtgärds körningar per minut, vilket är ~ 80 000 000 fler åtgärds körningar per månad |
  | Högsta antal skalnings enheter som du kan lägga till | 10 | |
  ||||

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Gatewaygränser

Azure Logic Apps stöder Skriv åtgärder, inklusive infogningar och uppdateringar via gatewayen. Dessa åtgärder har dock [gränser för deras nytto Last storlek](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="http-limits"></a>

## <a name="http-limits"></a>HTTP-gränser

Här är gränserna för ett enskilt inkommande eller utgående samtal:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Varaktighet för timeout

Vissa kopplings åtgärder gör asynkrona anrop eller lyssnar efter webhook-begäranden, så tids gränsen för dessa åtgärder kan vara längre än dessa gränser. Mer information finns i teknisk information för den aktuella anslutningen och även för [arbets flödes utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name | Logic Apps (flera innehavare) | Logic Apps (för hands version) | Integrerings tjänst miljö | Kommentarer |
|------|---------------------------|----------------------|---------------------------------|-------|
| Utgående begäran | 120 sekunder <br>(2 minuter) | 230 sekunder <br>(3,9 minuter) | 240 sekunder <br>(4 minuter) | Exempel på utgående begär Anden är anrop gjorda av HTTP-utlösaren eller åtgärden. Mer information om för hands versionen finns Azure Logic Apps för [hands](logic-apps-overview-preview.md)version. <p><p>**Tips**: Använd ett [asynkront avsöknings mönster](../logic-apps/logic-apps-create-api-app.md#async-pattern) eller en [until-slinga](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)för längre drift åtgärder. För att undvika tids gränser när du anropar en annan Logic app som har en [anropad slut punkt](logic-apps-http-endpoint.md)kan du använda den inbyggda Azure Logic Apps åtgärden i stället, som du hittar i anslutnings väljaren under **inbyggd**. |
| Inkommande begäran | 120 sekunder <br>(2 minuter) | 230 sekunder <br>(3,9 minuter) | 240 sekunder <br>(4 minuter) | Exempel på inkommande förfrågningar inkluderar anrop som tagits emot av utlösaren för begäran, HTTP-webhook-utlösare och HTTP webhook-åtgärd. Mer information om för hands versionen finns Azure Logic Apps för [hands](logic-apps-overview-preview.md)version. <p><p>**Obs!** för att den ursprungliga anroparen ska få svaret måste alla steg i svaret slutföras inom gränsen, om du inte anropar en annan Logic app som ett kapslat arbets flöde. Mer information finns i [anropa, Utlös ande eller kapsla Logic Apps](../logic-apps/logic-apps-http-endpoint.md). |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Meddelandestorlek

| Name | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Kommentarer |
|------|--------------------|---------------------------------------|-------|
| Meddelandestorlek | 100 MB | 200 MB | För att undvika den här gränsen, se [hantera stora meddelanden med segment](../logic-apps/logic-apps-handle-large-messages.md). Vissa anslutningar och API: er kanske inte stöder segment koppling eller till och med standard gränsen. <p><p>– Kopplingar som AS2, X12 och EDIFACT har egna [gränser för B2B-meddelanden](#b2b-protocol-limits). <br>– ISE-kopplingar använder ISE-gränsen, inte deras gränser som inte är ISE-anslutningsprogrammet. |
| Meddelande storlek med segment | 1 GB | 5 GB | Den här gränsen gäller för åtgärder som antingen har inbyggt stöd för segmentering eller som låter dig aktivera segment i körnings konfigurationen. <p><p>Om du använder en ISE stöder Logic Apps motor den här gränsen, men kopplingarna har sina egna segment gränser upp till motor gränsen, till exempel, se [Azure-Blob Storage Connectors API-referens](/connectors/azureblob/). Mer information om segment finns i [hantera stora meddelanden med segment](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Character-gränser

| Name | Gräns | Kommentarer |
|------|-------|-------|
| Gräns för uttrycksutvärdering | 131 072 tecken | , `@concat()` `@base64()` , `@string()` Uttryck får inte vara längre än den här gränsen. |
| Tecken gräns för begär ande URL | 16 384 tecken | |
||||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Återförsöksprincip

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Antal återförsök | 90 | Standardvärdet är 4. Om du vill ändra standardvärdet använder du [princip parametern för att försöka igen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximal tid innan nytt försök | 1 dag | Om du vill ändra standardvärdet använder du [princip parametern för att försöka igen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimal tid innan nytt försök | 5 sekunder | Om du vill ändra standardvärdet använder du [princip parametern för att försöka igen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Autentiserings gränser

Här följer gränserna för en Logic app som börjar med en begär ande utlösare och aktiverar [Azure Active Directory öppna autentisering](../active-directory/develop/index.yml) (Azure AD OAuth) för att auktorisera inkommande anrop till begär ande utlösare:

| Name | Gräns | Kommentarer |
| ---- | ----- | ----- |
| Auktoriseringsprinciper för Azure AD | 5 | |
| Anspråk per auktoriseringsprincip | 10 | |
| Anspråk värde – maximalt antal tecken | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Gränser för anpassade anslutningsappar

Här är gränserna för anpassade anslutningar som du kan skapa från webb-API: er.

| Name | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Kommentarer |
|------|--------------------|---------------------------------------|-------|
| Antal anpassade anslutningar | 1 000 per Azure-prenumeration | 1 000 per Azure-prenumeration ||
| Antal begär Anden per minut för en anpassad anslutning | 500 förfrågningar per minut per anslutning | 2 000 förfrågningar per minut per *anpassad anslutning* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Hanterade identiteter

| Name | Gräns |
|------|-------|
| Hanterade identiteter per Logic app | Antingen en tilldelad identitet eller en användardefinierad identitet |
| Antal Logic Apps som har en hanterad identitet i en Azure-prenumeration per region | 1 000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Gränser för integrationskonto

Varje Azure-prenumeration har följande gränser för integrations kontot:

* Ett integrations konto på [kostnads fri nivå](../logic-apps/logic-apps-pricing.md#integration-accounts) per Azure-region. Den här nivån är endast tillgänglig för offentliga regioner i Azure, till exempel västra USA eller Sydostasien, men inte för [Azure Kina 21Vianet](/azure/china/overview-operations) eller [Azure Government](../azure-government/documentation-government-welcome.md).

* 1 000 sammanlagt integrerings konton, inklusive integrations konton i alla [integrerings tjänst miljöer (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) i både [Developer-och Premium-SKU: er](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Varje ISE, vare sig [utvecklare eller Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), kan använda ett enda integrations konto utan extra kostnad, även om den inkluderade konto typen varierar av ISE SKU. Du kan skapa fler integrations konton för din ISE upp till den totala gränsen för [ytterligare kostnader](logic-apps-pricing.md#fixed-pricing):

  | ISE SKU | Gränser för integrationskonto |
  |---------|----------------------------|
  | **Premium** | 20 totala konton, inklusive ett standard konto utan extra kostnad. Med den här SKU: n kan du bara ha [standard](../logic-apps/logic-apps-pricing.md#integration-accounts) konton. Inga kostnads fria eller grundläggande konton är tillåtna. |
  | **Utvecklare** | 20 totala konton, inklusive ett [kostnads fritt](../logic-apps/logic-apps-pricing.md#integration-accounts) konto (begränsat till 1). Med den här SKU: n kan du antingen kombinera: <p>– Ett kostnads fritt konto och upp till 19 [standard](../logic-apps/logic-apps-pricing.md#integration-accounts) konton. <br>– Inget kostnads fritt konto och upp till 20 standard konton. <p>Inga grundläggande eller ytterligare kostnads fria konton är tillåtna. <p><p>**Viktigt**: Använd [SKU: n för utvecklare](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) för experimentering, utveckling och testning, men inte för produktion eller prestanda testning. |
  |||

Information om hur priser och fakturering fungerar för ISEs finns i [pris modellen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefakt gränser per integrations konto

Här följer gränserna för antalet artefakter för varje integrations konto nivå. Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/). Information om hur priser och fakturering fungerar för integrations konton finns i [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Använd endast den kostnads fria nivån för exempel scenarier, inte produktions scenarier. Den här nivån begränsar data flödet och användningen och har inget service nivå avtal (SLA).

| Artefakt | Kostnadsfri | Grundläggande | Standard |
|----------|------|-------|----------|
| EDI handels avtal | 10 | 1 | 1 000 |
| EDI-handels partner | 25 | 2 | 1 000 |
| Maps | 25 | 500 | 1 000 |
| Scheman | 25 | 500 | 1 000 |
| Sammansättningar | 10 | 25 | 1 000 |
| Certifikat | 25 | 2 | 1 000 |
| Batch-konfigurationer | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Artefakt kapacitets gränser

| Artefakt | Gräns | Kommentarer |
| -------- | ----- | ----- |
| Sammansättning | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du ett [Azure Storage-konto och en BLOB-behållare](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mappa (XSLT-fil) | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du [Azure Logic Apps REST API-Maps](/rest/api/logic/maps/createorupdate). <p><p>**Obs!** den mängd data eller poster som en karta kan bearbeta, baseras på meddelandets storlek och tids gräns gränser för åtgärder i Azure Logic Apps. Om du till exempel använder en HTTP-åtgärd, baserat på [http-meddelandets storlek och tids gränser](#http-limits), kan en karta bearbeta data upp till storleks gränsen för HTTP-meddelanden om åtgärden slutförs inom tids gränsen för http-timeout. |
| Schema | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du ett [Azure Storage-konto och en BLOB-behållare](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Dataflödesbegränsningar

| Runtime-slutpunkt | Kostnadsfri | Grundläggande | Standard | Kommentarer |
|------------------|------|-------|----------|-------|
| Läs anrop per 5 minuter | 3 000 | 30 000 | 60 000 | Den här gränsen gäller för anrop som hämtar rå data och utdata från en Logic Apps körnings historik. Du kan distribuera arbets belastningen i mer än ett konto vid behov. |
| Anropa anrop per 5 minuter | 3 000 | 30 000 | 45 000 | Du kan distribuera arbets belastningen i mer än ett konto vid behov. |
| Spåra anrop per 5 minuter | 3 000 | 30 000 | 45 000 | Du kan distribuera arbets belastningen i mer än ett konto vid behov. |
| Blockerar samtidiga anrop | ~ 1 000 | ~ 1 000 | ~ 1 000 | Samma för alla SKU: er. Du kan minska antalet samtidiga förfrågningar eller minska varaktigheten om det behövs. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B-protokoll (AS2, X12, EDIFACT) meddelande storlek

Här är de meddelande storleks gränser som gäller för B2B-protokoll:

| Name | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Kommentarer |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1-25 MB | v2 – 200 MB <br>v1-25 MB | Gäller för avkoda och koda |
| X12 | 50 MB | 50 MB | Gäller för avkoda och koda |
| EDIFACT | 50 MB | 50 MB | Gäller för avkoda och koda |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Inaktivera eller ta bort Logic Apps

När du inaktiverar en Logic app instansieras inga nya körningar. Alla pågående och väntande körningar fortsätter tills de har slutförts, vilket kan ta lång tid att slutföra.

När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Brand Väggs konfiguration: IP-adresser och service märken

När din Logi Kap par behöver kommunicera via en brand vägg som begränsar trafik till vissa IP-adresser, måste brand väggen tillåta åtkomst  för både [inkommande](#inbound) och [utgående](#outbound) ip-adresser som används av Logic Apps tjänst eller körning i den Azure-region där din Logic app finns. *Alla* Logic Apps i samma region använder samma IP-adressintervall.

Om du till exempel vill stödja samtal som Logic Apps i regionen Västra USA skickar eller tar emot via inbyggda utlösare och åtgärder, till exempel [http-utlösare eller åtgärd](../connectors/connectors-native-http.md), måste brand väggen tillåta åtkomst för *alla* Logic Apps tjänstens inkommande IP-adresser *och* utgående IP-adresser som finns i regionen USA, västra.

Om din Logic app även använder [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), till exempel Office 365 Outlook Connector eller SQL-anslutning, eller använder [anpassade anslutningar](/connectors/custom-connectors/), måste brand väggen också tillåta åtkomst för *alla* [utgående IP-adresser för hanterad anslutning](#outbound) i din Logic app Azure-region. Om du använder anpassade anslutnings program som har åtkomst till lokala resurser via den [lokala datagateway-resursen i Azure](logic-apps-gateway-connection.md)måste du konfigurera gateway-installationen för att tillåta åtkomst för motsvarande *hanterade anslutningar [utgående IP-adresser](#outbound)*.

Mer information om hur du konfigurerar kommunikations inställningar på gatewayen finns i följande avsnitt:

* [Justera kommunikationsinställningar för den lokala datagatewayen](/data-integration/gateway/service-gateway-communication)
* [Konfigurera proxyinställningar för den lokala datagatewayen](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Överväganden vid IP-konfiguration för brand vägg

Innan du konfigurerar brand väggen med IP-adresser bör du gå igenom följande överväganden:

* Om du använder [Power](/power-automate/getting-started)automate, går vissa åtgärder, till exempel **http** och **http + openapi**, direkt via Azure Logic Apps tjänsten och kommer från de IP-adresser som visas här. Mer information om de IP-adresser som används av Power automatisering finns i [gränser och konfiguration för energi automatisering](/flow/limits-and-config#ip-address-configuration).

* För [Azure Kina 21Vianet](/azure/china/)är fasta eller reserverade IP-adresser inte tillgängliga för [anpassade anslutningar](../logic-apps/custom-connector-overview.md) och för [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), till exempel Azure Storage, SQL Server, Office 365 Outlook och så vidare.

* Om dina Logi Kap par körs i en [integrerings tjänst miljö (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md)ser du till att du [även öppnar dessa portar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* För att hjälpa dig att förenkla de säkerhets regler som du vill skapa, kan du välja att använda [service märken](../virtual-network/service-tags-overview.md) istället för att ange IP-adressprefix för varje region. Dessa taggar fungerar i de regioner där Logic Appss tjänsten är tillgänglig:

  * **LogicAppsManagement**: representerar de inkommande IP-adressprefix för Logic Appss tjänsten.

  * **LogicApps**: representerar de utgående IP-adressprefix för Logic Appss tjänsten.

  * **AzureConnectors**: representerar IP-adressprefix för hanterade anslutningar som gör inkommande webhook-återanrop till tjänsten Logic Apps och utgående anrop till deras respektive tjänster, till exempel Azure Storage eller Azure-Event Hubs.

* Om dina Logi Kap par har problem med att komma åt Azure Storage-konton som använder [brand väggar och brand Väggs regler](../storage/common/storage-network-security.md), har du [olika alternativ för att aktivera åtkomst](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Logic Apps kan till exempel inte komma åt lagrings konton som använder brand Väggs regler och som finns i samma region. Men om du tillåter [utgående IP-adresser för hanterade anslutningar i din region](../logic-apps/logic-apps-limits-and-config.md#outbound), kan dina Logi Kap par komma åt lagrings konton som finns i en annan region, förutom när du använder Azure Table Storage-eller Azure Queue Storage-anslutningarna. Om du vill komma åt Table Storage eller Queue Storage kan du använda HTTP-utlösaren och åtgärderna i stället. Andra alternativ finns i [åtkomst till lagrings konton bakom brand väggar](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Inkommande IP-adresser

I det här avsnittet visas endast inkommande IP-adresser för den Azure Logic Apps tjänsten. Om du har Azure Government, se [Azure Government-inkommande IP-adresser](#azure-government-inbound).

> [!TIP]
> För att minska komplexiteten när du skapar säkerhets regler kan du också använda [service tag](../virtual-network/service-tags-overview.md)- **LogicAppsManagement** i stället för att ange inkommande Logic Apps IP-adressprefix för varje region. Alternativt kan du också använda **AzureConnectors** för hanterade anslutningar som gör inkommande webhook-återanrop till den Logic Apps tjänsten, i stället för att ange inkommande IP-adressprefix för hanterade anslutningar för varje region. Dessa taggar fungerar i de regioner där Logic Appss tjänsten är tillgänglig.
>
> Följande kopplingar gör inkommande webhook-återanrop till tjänsten Logic Apps:
>
> Adobe Creative Cloud, Adobe-signera, Adobe-signera demo, Adobe Sign-förhandsgranskning, Adobe Sign-fas, Azure Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign demo, Dynamics 365 for fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, tolkare, SAP *, Shift för Microsoft Teams, lag projekt, Typeform
>
> \***SAP**: retur anroparen beror på om distributions miljön är en Azure eller ISE med flera innehavare. I en miljö med flera innehavare gör den lokala datagatewayen att anropet tillbaka till Logic Apps-tjänsten. I en ISE gör SAP-anslutaren anropet tillbaka till Logic Apps-tjänsten.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Azure-inkommande IP-adresser för flera innehavare

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
| Östra Norge | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Sydafrika, norra | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Sydafrika, västra | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA, södra centrala | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indien, södra | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sydostasien | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Schweiz, norra | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Schweiz, västra | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| Förenade Arabemiraten Central | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
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

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government-inkommande IP-adresser

| Azure Government Region | IP-adress |
|-------------------------|----|
| US Gov, Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov, Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov, Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| USA DoD, centrala | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Utgående IP-adresser

I det här avsnittet visas de utgående IP-adresserna för Azure Logic Apps tjänsten och hanterade anslutningar. Om du har Azure Government, se [Azure Government utgående IP-adresser](#azure-government-outbound).

> [!TIP]
> För att minska komplexiteten när du skapar säkerhets regler kan du också använda [service tag](../virtual-network/service-tags-overview.md)- **LogicApps** i stället för att ange utgående Logic Apps IP-adressprefix för varje region. Du kan också använda **AzureConnectors** service tag för hanterade anslutningar som gör utgående anrop till sina respektive tjänster, till exempel Azure Storage eller Azure Event Hubs, i stället för att ange utgående IP-adressprefix för hanterade anslutningar för varje region. Dessa taggar fungerar i de regioner där Logic Appss tjänsten är tillgänglig.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Azure-utgående IP-adresser för flera innehavare

| Region för flera innehavare | Logic Apps IP | IP för hanterade anslutningar |
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
| Östra Norge | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Sydafrika, norra | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Sydafrika, västra | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| USA, södra centrala | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Indien, södra | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Sydostasien | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Schweiz, norra | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| Schweiz, västra | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| Förenade Arabemiraten Central | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Förenade Arabemiraten, norra | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Storbritannien, södra | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Storbritannien, västra | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| USA, västra centrala | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| Europa, västra | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Indien, västra | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| USA, västra | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| USA, västra 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government utgående IP-adresser

| Region | Logic Apps IP | IP för hanterade anslutningar |
|--------|---------------|-----------------------|
| USA DoD, centrala | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| US Gov, Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| US Gov, Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| US Gov, Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Lär dig mer om [Vanliga exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
