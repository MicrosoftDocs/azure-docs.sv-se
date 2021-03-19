---
title: Utföra åtgärder för data
description: Konvertera, hantera och ändra data utdata och format i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84710379"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Utföra data åtgärder i Azure Logic Apps

Den här artikeln visar hur du kan arbeta med data i dina Logi Kap par genom att lägga till åtgärder för dessa uppgifter och mer:

* Skapa tabeller från matriser.
* Skapa matriser från andra matriser baserat på ett villkor.
* Skapa användarvänliga tokens från JavaScript Object Notation (JSON) objekt egenskaper så att du enkelt kan använda dessa egenskaper i ditt arbets flöde.

Om du inte hittar den åtgärd som du vill ha här kan du prova att bläddra bland många olika [data behandlings funktioner](../logic-apps/workflow-definition-language-functions-reference.md) som Azure Logic Apps tillhandahåller.

Dessa tabeller sammanfattar de data åtgärder som du kan använda och organiseras baserat på de käll data typer som åtgärderna fungerar på, men varje beskrivning visas i alfabetisk ordning.

**Mat ris åtgärder** 

Med de här åtgärderna kan du arbeta med data i matriser.

| Åtgärd | Beskrivning |
|--------|-------------|
| [**Skapa CSV-tabell**](#create-csv-table-action) | Skapa en kommaavgränsad tabell (CSV) från en matris. |
| [**Skapa HTML-tabell**](#create-html-table-action) | Skapa en HTML-tabell från en matris. |
| [**Filtrera matris**](#filter-array-action) | Skapa en mat ris del uppsättning från en matris baserat på det angivna filtret eller villkoret. |
| [**Ansluta**](#join-action) | Skapa en sträng från alla objekt i en matris och avgränsa varje objekt med det angivna specialtecknet. |
| [**Välj**](#select-action) | Skapa en matris från de angivna egenskaperna för alla objekt i en annan matris. |
||| 

**JSON-åtgärder**

De här åtgärderna hjälper dig att arbeta med data i JavaScript Object Notation-format (JSON).

| Åtgärd | Beskrivning |
|--------|-------------|
| [**Compose**](#compose-action) | Skapa ett meddelande eller en sträng från flera indata som kan ha olika data typer. Du kan sedan använda den här strängen som ett enda indata, i stället för att upprepade gånger ange samma indata. Du kan till exempel skapa ett enda JSON-meddelande från olika indata. |
| [**Parsa JSON**](#parse-json-action) | Skapa användarvänliga datatoken för egenskaper i JSON-innehåll så att du enkelt kan använda egenskaperna i dina Logic Apps. |
|||

Information om hur du skapar mer komplexa JSON-transformeringar finns i [utföra avancerade JSON-omvandlingar med flytande mallar](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic-app där du behöver åtgärden för att arbeta med data

  Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i din Logic app 

  Data åtgärder är bara tillgängliga som åtgärder, så innan du kan använda de här åtgärderna startar du din Logic app med en utlösare och inkluderar alla andra åtgärder som krävs för att skapa de utdata du vill.

<a name="compose-action"></a>

## <a name="compose-action"></a>Skriv åtgärd

Om du vill skapa ett enstaka utdata, till exempel ett JSON-objekt från flera indata, kan du använda åtgärden **Skriv** . Dina indata kan ha olika typer, till exempel heltal, booleska värden, matriser, JSON-objekt och annan inbyggd typ som Azure Logic Apps stöder, till exempel Binary och XML. Du kan sedan använda utdata i åtgärder som följer efter åtgärden **Skriv** . Åtgärden **Skriv** kan också spara dig från att upprepade gånger ange samma indata när du skapar din Logic Apps-arbetsflöde.

Du kan till exempel skapa ett JSON-meddelande från flera variabler, till exempel String-variabler som lagrar användares förnamn och efter namn, samt en heltals variabel som lagrar personers åldrar. Här accepterar **Skriv** åtgärden följande indata:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

och skapar följande utdata:

`{"age":35,"fullName":"Owens,Sophie"}`

Följ dessa steg med hjälp av Logic Apps designer för att prova ett exempel. Om du föredrar att arbeta i kodvyn kan du Kopiera exemplet **skapa** och **initiera variabel** åtgärds definitioner från den här artikeln till din egen logiska Apps underliggande arbets flödes definition: [exempel på data åtgärds kod – Skriv](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure Portal och en Logic-app med en **upprepnings** utlösare och flera **initiera variabel** åtgärder. Dessa åtgärder ställs in för att skapa två String-variabler och en heltals variabel. När du senare testar din Logic-app kan du köra appen manuellt utan att vänta på att utlösaren ska starta.

   ![Startar exempel Logic app för "Skriv"-åtgärd](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. Gör något av följande i din Logic-app där du vill skapa utdata: 

   * Välj **nytt steg** för att lägga till en åtgärd under det sista steget.

     ![Välj "nytt steg" för åtgärden "Skriv"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du musen över den anslutande pilen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** går du till rutan Sök och anger `compose` som ditt filter. I listan åtgärder väljer du åtgärden **Skriv** .

   ![Välj åtgärden "Skriv"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. I rutan **indata** anger du de indata som du vill använda för att skapa utdata.

   I det här exemplet visas listan med dynamiskt innehåll när du klickar i rutan **indata** , så att du kan välja de tidigare skapade variablerna:

   ![Välj indata som ska användas för åtgärden "Skriv"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Här är den **färdiga exempel åtgärden** : 

   ![Färdiga exempel för "skapa"-åtgärd](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Mer information om den här åtgärden i den underliggande arbets flödes definitionen finns i [åtgärden Skriv](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Testa din Logic app

För att bekräfta om **Skriv** åtgärden skapar förväntade resultat, skicka ett meddelande som innehåller utdata från åtgärden **Skriv** .

1. I din Logic app lägger du till en åtgärd som kan skicka resultatet från åtgärden **Skriv** .

1. I den åtgärden klickar du på var du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas går du till åtgärden **Skriv** och väljer **utdata**.

   I det här exemplet används åtgärden **skicka e-post** och innehåller fälten **utdata** i e-postmeddelandets brödtext och ämne:

   !["Output"-fält för åtgärden "Skriv"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Kör nu din Logic-app manuellt. I verktygsfältet designer väljer du **Kör**.

   Utifrån e-postkopplingen som du använde är följande resultat som du får:

   ![E-post med åtgärds resultat för "Skriv"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Åtgärd för att skapa CSV-tabell

Använd åtgärden **Skapa CSV-tabell** för att skapa en kommaavgränsad tabell (CSV) som innehåller egenskaper och värden från JavaScript Object Notation (JSON)-objekt i en matris. Du kan sedan använda den resulterande tabellen i åtgärder som följer åtgärden **Skapa CSV-tabell** .

Om du föredrar att arbeta i kodvyn kan du Kopiera exemplet **Skapa CSV-tabell** och **initiera variabel** åtgärds definitioner från den här artikeln i din egen Logic Apps underliggande arbets flödes definition: [exempel på data åtgärds kod – skapa CSV-tabell](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure Portal och en Logi Kap par med en **upprepnings** utlösare och en **initiera variabel** åtgärd. Åtgärden ställs in för att skapa en variabel vars ursprungliga värde är en matris som har vissa egenskaper och värden i JSON-format. När du senare testar din Logic-app kan du köra appen manuellt utan att vänta på att utlösaren ska starta.

   ![Startar exempel på Logic app för åtgärden "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Gör något av följande i din Logic-app där du vill skapa en CSV-tabell: 

   * Välj **nytt steg** för att lägga till en åtgärd under det sista steget.

     ![Välj "nytt steg" för åtgärden "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du musen över den anslutande pilen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** går du till rutan Sök och anger `create csv table` som ditt filter. I listan åtgärder väljer du åtgärden **Skapa CSV-tabell** .

   ![Välj åtgärden "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. I rutan **från** anger du den matris eller det uttryck som du vill använda för att skapa tabellen.

   I det här exemplet visas den dynamiska innehålls listan så att du kan välja den tidigare skapade variabeln när du klickar i rutan **från** :

   ![Välj mat ris utdata för att skapa CSV-tabell](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Om du vill skapa användarvänliga token för egenskaperna i JSON-objekt så att du kan välja dessa egenskaper som indata använder du [parse JSON](#parse-json-action) innan du anropar åtgärden **Skapa CSV-tabell** .

   Här är det färdiga exemplet **Skapa CSV-tabell** åtgärd: 

   ![Det färdiga exemplet för åtgärden "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

### <a name="customize-table-format"></a>Anpassa tabell format

Som standard är egenskapen **columns** inställd på att automatiskt skapa tabell kolumner baserat på mat ris objekt. Följ dessa steg om du vill ange anpassade rubriker och värden:

1. Öppna listan **kolumner** och välj **anpassad**.

1. I egenskapen **rubrik** anger du den anpassade rubrik text som ska användas i stället.

1. I egenskapen **Value** anger du det anpassade värde som ska användas i stället.

Om du vill returnera värden från matrisen kan du använda [ `item()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#item) med åtgärden **Skapa CSV-tabell** . `For_each`Du kan använda [ `items()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#items)i en slinga.

Anta till exempel att du vill att tabell kolumner som bara har egenskaps värden och inte egenskaps namnen från en matris. Om du bara vill returnera dessa värden följer du de här stegen för att arbeta i design läge eller i kodvyn. Här är resultatet som det här exemplet returnerar:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Arbeta i design läge

Behåll kolumnen **rubrik** tom i åtgärden. På varje rad i kolumnen **värde** kan du referera till varje mat ris egenskap som du vill ha. Varje rad under **värde** returnerar alla värden för den angivna mat ris egenskapen och blir en kolumn i tabellen.

1. Under **värde**, på varje rad som du vill ha, klickar du i redigerings rutan så att listan med dynamiskt innehåll visas.

1. I listan med dynamiskt innehåll väljer du **uttryck**.

1. I uttrycks redigeraren anger du det här uttrycket som anger det värde för mat ris egenskapen som du vill ha och väljer **OK**.

   `item()?['<array-property-name>']`

   Exempel:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Referens "Beskrivning" för "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Upprepa föregående steg för varje mat ris egenskap som du vill ha. När du är klar ser din åtgärd ut som i det här exemplet:

   ![funktionen "Item ()" i "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Du löser uttryck i fler beskrivande versioner genom att växla till kodvyn och tillbaka till design läge och sedan öppna den minimerade åtgärden igen:

   Åtgärden **Skapa CSV-tabell** visas nu som i det här exemplet:

   !["Skapa CSV-tabell"-matchade uttryck, inga rubriker](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Arbeta i kodvyn

I åtgärdens JSON-definition, i `columns` matrisen, anger du `header` egenskapen till en tom sträng. Referera till varje `value` mat ris egenskap som du vill använda för varje egenskap.

1. I verktygsfältet designer väljer du **kodvyn**.

1. I kod redigeraren i åtgärdens `columns` matris lägger du till `header` egenskapen Tom och det här `value` uttrycket för varje kolumn med mat ris värden som du vill ha:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Exempel:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Växla tillbaka till designvyn och öppna den minimerade åtgärden igen.

   Åtgärden **Skapa CSV-tabell** visas nu som i det här exemplet, och uttrycken har matchats till fler beskrivande versioner:

   !["Skapa CSV-tabell"-matchade uttryck och inga rubriker](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Mer information om den här åtgärden i den underliggande arbets flödes definitionen finns i [tabell åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testa din Logic app

Du kan kontrol lera om åtgärden **Skapa CSV-tabell** skapar förväntade resultat genom att skicka ett meddelande som innehåller utdata från åtgärden **Skapa CSV-tabell** .

1. I din Logic app lägger du till en åtgärd som kan skicka resultatet från åtgärden **Skapa CSV-tabell** .

1. I den åtgärden klickar du på var du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas går du till åtgärden **Skapa CSV-tabell** och väljer **utdata**. 

   I det här exemplet används Office 365 Outlook för att **skicka en e-** poståtgärd och innehåller fältet **utdata** i e-postmeddelandets brödtext:

   !["Output"-fält för åtgärden "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Kör nu din Logic-app manuellt. I verktygsfältet designer väljer du **Kör**.

   Utifrån e-postkopplingen som du använde är följande resultat som du får:

   ![E-post med åtgärds resultat för "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Åtgärden skapa HTML-tabell

Använd åtgärden **skapa HTML-tabell** om du vill skapa en HTML-tabell som innehåller egenskaper och värden från JavaScript Object Notation (JSON)-objekt i en matris. Du kan sedan använda den resulterande tabellen i åtgärder som följer åtgärden **skapa HTML-tabell** .

Om du föredrar att arbeta i kodvyn kan du Kopiera exemplet **skapa HTML-tabell** och **initiera variabel** åtgärds definitioner från den här artikeln i din egen Logic Apps underliggande arbets flödes definition: [exempel på data ÅTGÄRDs kod – skapa HTML-tabell](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure Portal och en Logi Kap par med en **upprepnings** utlösare och en **initiera variabel** åtgärd. Åtgärden ställs in för att skapa en variabel vars ursprungliga värde är en matris som har vissa egenskaper och värden i JSON-format. När du senare testar din Logic-app kan du köra appen manuellt utan att vänta på att utlösaren ska starta.

   ![Startar exempel Logic app för "skapa HTML-tabell"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Gör något av följande i din Logic-app där du vill skapa en HTML-tabell:

   * Välj **nytt steg** för att lägga till en åtgärd under det sista steget.

     ![Välj "nytt steg" för åtgärden "skapa HTML-tabell"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du musen över den anslutande pilen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** går du till rutan Sök och anger `create html table` som ditt filter. I listan åtgärder väljer du åtgärden **skapa HTML-tabell** .

   ![Välj åtgärden "skapa HTML-tabell"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. I rutan **från** anger du den matris eller det uttryck som du vill använda för att skapa tabellen.

   I det här exemplet visas den dynamiska innehålls listan så att du kan välja den tidigare skapade variabeln när du klickar i rutan **från** :

   ![Välj mat ris utdata för att skapa HTML-tabell](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Om du vill skapa användarvänliga token för egenskaperna i JSON-objekt så att du kan välja dessa egenskaper som indata använder du [parse JSON](#parse-json-action) innan du anropar åtgärden **skapa HTML-tabell** .

   Här är det färdiga exemplet **skapa HTML-tabell** åtgärd:

   ![Färdiga exempel för "skapa HTML-tabell"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

### <a name="customize-table-format"></a>Anpassa tabell format

Som standard är egenskapen **columns** inställd på att automatiskt skapa tabell kolumner baserat på mat ris objekt. Följ dessa steg om du vill ange anpassade rubriker och värden:

1. Öppna listan **kolumner** och välj **anpassad**.

1. I egenskapen **rubrik** anger du den anpassade rubrik text som ska användas i stället.

1. I egenskapen **Value** anger du det anpassade värde som ska användas i stället.

Om du vill returnera värden från matrisen kan du använda [ `item()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#item) med åtgärden **skapa HTML-tabell** . `For_each`Du kan använda [ `items()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#items)i en slinga.

Anta till exempel att du vill att tabell kolumner som bara har egenskaps värden och inte egenskaps namnen från en matris. Om du bara vill returnera dessa värden följer du de här stegen för att arbeta i design läge eller i kodvyn. Här är resultatet som det här exemplet returnerar:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Arbeta i design läge

Behåll kolumnen **rubrik** tom i åtgärden. På varje rad i kolumnen **värde** kan du referera till varje mat ris egenskap som du vill ha. Varje rad under **värde** returnerar alla värden för den angivna egenskapen och blir en kolumn i tabellen.

1. Under **värde**, på varje rad som du vill ha, klickar du i redigerings rutan så att listan med dynamiskt innehåll visas.

1. I listan med dynamiskt innehåll väljer du **uttryck**.

1. I uttrycks redigeraren anger du det här uttrycket som anger det värde för mat ris egenskapen som du vill ha och väljer **OK**.

   `item()?['<array-property-name>']`

   Exempel:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Referens egenskap i åtgärden "skapa HTML-tabell"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Upprepa föregående steg för varje mat ris egenskap som du vill ha. När du är klar ser din åtgärd ut som i det här exemplet:

   ![funktionen "Item ()" i "skapa HTML-tabell"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Du löser uttryck i fler beskrivande versioner genom att växla till kodvyn och tillbaka till design läge och sedan öppna den minimerade åtgärden igen:

   Åtgärden **skapa HTML-tabell** visas nu som i det här exemplet:

   !["Skapa HTML-tabell"-matchade uttryck, inga rubriker](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Arbeta i kodvyn

I åtgärdens JSON-definition, i `columns` matrisen, anger du `header` egenskapen till en tom sträng. Referera till varje `value` mat ris egenskap som du vill använda för varje egenskap.

1. I verktygsfältet designer väljer du **kodvyn**.

1. I kod redigeraren i åtgärdens `columns` matris lägger du till `header` egenskapen Tom och det här `value` uttrycket för varje kolumn med mat ris värden som du vill ha:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Exempel:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Växla tillbaka till designvyn och öppna den minimerade åtgärden igen.

   Åtgärden **skapa HTML-tabell** visas nu som i det här exemplet, och uttrycken har matchats till fler beskrivande versioner:

   !["Skapa HTML-tabell"-matchade uttryck och inga rubriker](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Mer information om den här åtgärden i den underliggande arbets flödes definitionen finns i [tabell åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testa din Logic app

För att bekräfta om åtgärden **skapa HTML-tabell** skapar förväntade resultat, skicka ett meddelande till dig själv som innehåller utdata från åtgärden **skapa HTML-tabell** .

1. I din Logic app lägger du till en åtgärd som kan skicka resultatet från åtgärden **skapa HTML-tabell** .

1. I den åtgärden klickar du på var du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas, under åtgärden **skapa HTML-tabell** , väljer du **utdata**. 

   I det här exemplet används Office 365 Outlook för att **skicka en e-** poståtgärd och innehåller fältet **utdata** i e-postmeddelandets brödtext:

   !["Utdata"-fält för "skapa HTML-tabell"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > När du inkluderar utdata i HTML-tabellen i en e-poståtgärd, se till att du ställer in egenskapen **är HTML** till **Ja** i alternativ för e-poståtgärdens avancerade alternativ. På så sätt formaterar e-poståtgärden HTML-tabellen korrekt.

1. Kör nu din Logic-app manuellt. I verktygsfältet designer väljer du **Kör**.

   Utifrån e-postkopplingen som du använde är följande resultat som du får:

   ![E-post med resultaten "skapa HTML-tabell"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Filtrera mat ris åtgärd

Om du vill skapa en mindre matris som har objekt som uppfyller vissa villkor från en befintlig matris använder du åtgärden **filtrera matris** . Du kan sedan använda den filtrerade matrisen i åtgärder som följer efter åtgärden **filtrera matris** .

> [!NOTE]
> All filter text som du använder i villkoret är Skift läges känslig. Den här åtgärden kan inte heller ändra formatet eller komponenter för objekt i matrisen. 
> 
> För att åtgärder ska kunna använda mat ris utdata från åtgärden **filtrera matris** , måste dessa åtgärder acceptera matriser som indata, eller så kanske du måste transformera utdata-matrisen till ett annat kompatibelt format.
> 
> Om du anropar en HTTP-slutpunkt och får ett JSON-svar använder du åtgärden **parsa JSON** för att bearbeta JSON-svaret. 
> Annars kan åtgärden **filtrera matris** bara läsa svars texten och inte JSON-nyttolastens struktur.

Om du föredrar att arbeta i kodvyn kan du kopiera exempel **filter mat ris** och **initiera variabel** åtgärds definitioner från den här artikeln till din egen Logic Apps underliggande arbets flödes definition: [data åtgärds kod exempel – filter mat ris](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure Portal och en Logi Kap par med en **upprepnings** utlösare och en **initiera variabel** åtgärd. Åtgärden ställs in för att skapa en variabel vars ursprungliga värde är en matris som har några exempel-heltal. När du senare testar din Logic-app kan du köra appen manuellt utan att vänta på att utlösaren ska starta.

   > [!NOTE]
   > Även om det här exemplet använder en enkel heltals mat ris, är den här åtgärden särskilt användbar för JSON-objekts matriser där du kan filtrera efter objekt egenskaper och värden.

   ![Startar exempel Logic app för åtgärden "Filtrera matris"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. Gör något av följande i din Logic-app där du vill skapa den filtrerade matrisen: 

   * Välj **nytt steg** för att lägga till en åtgärd under det sista steget.

     ![Välj "nytt steg" för "Filtrera matris"-åtgärd](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du musen över den anslutande pilen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger `filter array` du som filter. I listan åtgärder väljer du åtgärden **filtrera matris** .

   ![Välj åtgärden "Filtrera matris"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. I rutan **från** anger du den matris eller det uttryck som du vill filtrera.

   I det här exemplet visas den dynamiska innehålls listan så att du kan välja den tidigare skapade variabeln när du klickar i rutan **från** :

   ![Välj mat ris utdata för att skapa filtrerad matris](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. För villkoret anger du de mat ris objekt som ska jämföras, väljer jämförelse operator och anger jämförelse värdet.

   I det här exemplet används `item()` funktionen för att komma åt varje objekt i matrisen medan **filter mat ris** åtgärden söker efter mat ris objekt vars värde är större än ett:

   ![Det färdiga exemplet för åtgärden "Filtrera matris"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Mer information om den här åtgärden i den underliggande arbets flödes definitionen finns i [fråga åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testa din Logic app

Om du vill bekräfta att **filter mat ris** åtgärd skapar förväntade resultat kan du skicka ett meddelande som innehåller utdata från åtgärden **filtrera matris** .

1. I din Logic app lägger du till en åtgärd som kan skicka resultatet från åtgärden **filtrera matris** .

1. I den åtgärden klickar du på var du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **uttryck**. Om du vill hämta mat ris utdata från åtgärden **filtrera matris** anger du det här uttrycket som innehåller åtgärds namnet för **filter mat ris** :

   `@actionBody('Filter_array')`

   I det här exemplet används Office 365 Outlook för att **skicka en e-post** och innehåller utdata från actionBody-uttrycket **(' Filter_array ')** i e-postmeddelandets brödtext:

   ![Åtgärds utdata från åtgärden "Filtrera matris"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Kör nu din Logic-app manuellt. I verktygsfältet designer väljer du **Kör**.

   Utifrån e-postkopplingen som du använde är följande resultat som du får:

   ![E-post med åtgärds resultat för "Filtrera matris"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Kopplings åtgärd

Om du vill skapa en sträng som innehåller alla objekt från en matris och åtskiljer dessa objekt med ett särskilt avgränsnings tecken, använder du **kopplings** åtgärden. Du kan sedan använda strängen i åtgärder som följer efter **kopplings** åtgärden.

Om du föredrar att arbeta i kodvyn kan du Kopiera exemplet **Anslut** till och **initiera variabel** definitioner för variabler från den här artikeln till din egen Logic Apps underliggande arbets flödes definition: [exempel på data åtgärds kod – Anslut](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure Portal och en Logi Kap par med en **upprepnings** utlösare och en **initiera variabel** åtgärd. Den här åtgärden ställs in för att skapa en variabel vars ursprungliga värde är en matris som har några exempel-heltal. När du testar din Logic-app senare kan du köra appen manuellt utan att vänta på att utlösaren ska starta.

   ![Startar exempel Logic app för "koppla"-åtgärd](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. Gör något av följande i din Logic-app där du vill skapa strängen från en matris:

   * Välj **nytt steg** för att lägga till en åtgärd under det sista steget.

     ![SSelect "nytt steg" för "koppla"-åtgärd](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du musen över den anslutande pilen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger `join` du som filter. I listan åtgärder väljer du den här åtgärden: **Anslut**

   ![Välj kopplings åtgärd](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. I rutan **från** anger du den matris som innehåller de objekt som du vill ansluta till som en sträng.

   I det här exemplet klickar du på den dynamiska innehålls listan som visas så att du kan välja den tidigare skapade variabeln i rutan **från** :  

   ![Välj mat ris utdata för att skapa strängen](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. I rutan **delta med** anger du det önskade tecknen för att avgränsa varje mat ris objekt. 

   I det här exemplet används kolon (:) som avgränsare.

   ![Ange avgränsnings tecknet](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Mer information om den här åtgärden i den underliggande arbets flödes definitionen finns i [kopplings åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testa din Logic app

Du kan kontrol lera om **kopplings** åtgärden skapar förväntade resultat genom att skicka ett meddelande som innehåller utdata från **kopplings** åtgärden.

1. I din Logic app lägger du till en åtgärd som kan skicka resultatet från **kopplings** åtgärden.

1. I den åtgärden klickar du på var du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **utdata** under **kopplings** åtgärden. 

   I det här exemplet används Office 365 Outlook för att **skicka en e-** poståtgärd och innehåller fältet **utdata** i e-postmeddelandets brödtext:

   !["Output"-fält för åtgärden "koppla"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Kör nu din Logic-app manuellt. I verktygsfältet designer väljer du **Kör**.

   Utifrån e-postkopplingen som du använde är följande resultat som du får:

   ![E-post med åtgärds resultat för "koppling"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Parsa JSON-åtgärd

Om du vill referera till eller komma åt egenskaper i JavaScript Object Notation-innehåll (JSON) kan du skapa användarvänliga fält eller tokens för dessa egenskaper med hjälp av åtgärden **parsa JSON** . På så sätt kan du välja dessa egenskaper från listan med dynamiskt innehåll när du anger indata för din Logic app. För den här åtgärden kan du antingen ange ett JSON-schema eller generera ett JSON-schema från exempel-JSON-innehållet eller nytto lasten.

Om du föredrar att arbeta i kodvyn kan du Kopiera exemplet **parsa JSON** och **initiera variabel** åtgärds definitioner från den här artikeln till din egen Logic Apps underliggande arbets flödes definition: [exempel på data åtgärds kod – parsa JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure Portal och en Logi Kap par med en **upprepnings** utlösare och en **initiera variabel** åtgärd. Åtgärden ställs in för att skapa en variabel vars ursprungliga värde är ett JSON-objekt som har egenskaper och värden. När du senare testar din Logic-app kan du köra appen manuellt utan att vänta på att utlösaren ska starta.

   ![Startar exempel Logic app för åtgärden "parsa JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. Gör något av följande i din Logic-app där du vill parsa JSON-innehållet:

   * Välj **nytt steg** för att lägga till en åtgärd under det sista steget.

     ![Välj "nytt steg" för "parsa JSON"-åtgärd](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du musen över den anslutande pilen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger `parse json` du som filter. I listan åtgärder väljer du åtgärden **parsa JSON** .

   ![Välj åtgärden parsa JSON](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. I rutan **innehåll** anger du det JSON-innehåll som du vill tolka.

   I det här exemplet visas den dynamiska innehålls listan så att du kan välja den tidigare skapade variabeln när du klickar i **innehålls** rutan:

   ![Välj JSON-objekt för att parsa JSON-åtgärd](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Ange det JSON-schema som beskriver det JSON-innehåll som du parsar.

   I det här exemplet är JSON-schemat:

   ![Ange JSON-schema för det JSON-objekt som du vill parsa](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Om du inte har schemat kan du generera schemat från JSON-innehållet eller *nytto lasten* som du tolkar. 
   
   1. I åtgärden **parsa JSON** väljer **du Använd exempel nytto last för att generera schemat**.

   1. Under **Ange eller klistra in en exempel-JSON-nyttolast**, anger du JSON-innehållet och väljer sedan **färdig**.

      ![Ange JSON-innehållet för att generera schemat](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Mer information om den här åtgärden i den underliggande arbets flödes definitionen finns i [parsa JSON-åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testa din Logic app

Du kan kontrol lera om åtgärden **parsa JSON** skapar förväntade resultat genom att skicka dig själv ett meddelande som innehåller utdata från åtgärden **parsa JSON** .

1. I din Logic app lägger du till en åtgärd som kan skicka resultatet från åtgärden **parsa JSON** .

1. I den åtgärden klickar du på var du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas under åtgärden **parsa JSON** kan du nu välja egenskaper från det parsade JSON-innehållet.

   I det här exemplet används Office 365 Outlook för att **skicka en e-** poståtgärd och innehåller fälten **förnamn**, **efter namn** och **e-post** i e-postmeddelandets brödtext:

   ![JSON-egenskaper i åtgärden "Skicka ett e-postmeddelande"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Här är den färdiga e-poståtgärden:

   ![Färdiga exempel för e-poståtgärd](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Kör nu din Logic-app manuellt. I verktygsfältet designer väljer du **Kör**. 

   Utifrån e-postkopplingen som du använde är följande resultat som du får:

   ![E-post med åtgärds resultat för "parsa JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Välj åtgärd

Om du vill skapa en matris som har JSON-objekt som skapats från värden i en befintlig matris använder du åtgärden **Select** . Du kan till exempel skapa ett JSON-objekt för varje värde i en heltals mat ris genom att ange de egenskaper som varje JSON-objekt måste ha och hur du mappar värdena i käll matrisen till dessa egenskaper. Även om du kan ändra komponenterna i dessa JSON-objekt har den utgående matrisen alltid samma antal objekt som käll mat ris.

> [!NOTE]
> Åtgärder för att använda mat ris utdata från **Select** -åtgärden måste acceptera matriser som indata, eller så kanske du måste transformera den utgående matrisen till ett annat kompatibelt format. 

Om du föredrar att arbeta i kodvyn kan du Kopiera exemplet **Välj** och initiera definitioner av **variabel** åtgärder från den här artikeln i din egen Logic Apps underliggande arbets flödes definition: [exempel på data åtgärds kod – Välj](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure Portal och en Logi Kap par med en **upprepnings** utlösare och en **initiera variabel** åtgärd. Åtgärden ställs in för att skapa en variabel vars ursprungliga värde är en matris som har några exempel-heltal. När du senare testar din Logic-app kan du köra appen manuellt utan att vänta på att utlösaren ska starta.

   ![Startar exempel Logic app för åtgärden "Välj"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. Gör något av följande i din Logic-app där du vill skapa matrisen: 

   * Välj **nytt steg** för att lägga till en åtgärd under det sista steget.

     ![Välj "nytt steg" för "Välj"-åtgärd](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du musen över den anslutande pilen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** väljer du **inbyggt**. I rutan Sök anger `select` du som filter. I listan åtgärder väljer du åtgärden **Välj** .

   ![Välj åtgärden "Välj"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. I rutan **från** anger du den käll mat ris som du vill använda.

   I det här exemplet visas den dynamiska innehålls listan så att du kan välja den tidigare skapade variabeln när du klickar i rutan **från** :

   ![Välj käll mat ris för Välj åtgärd](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Ange det egenskaps namn som du vill tilldela varje värde i käll mat ris i rutan till vänster i **Map** -kolumnen. I kolumnen till höger anger du ett uttryck som representerar värdet som du vill tilldela egenskapen.

   I det här exemplet anges "Product_ID" som egenskaps namn för att tilldela varje värde i heltals mat ris med hjälp av `item()` funktionen i ett uttryck som har åtkomst till varje mat ris objekt. 

   ![Ange JSON-objektets egenskap och värden för att skapa matris](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Här är den färdiga åtgärden:

   ![Färdiga exempel för åtgärden "Välj"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Mer information om den här åtgärden i den underliggande arbets flödes definitionen finns i [Välj åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testa din Logic app

För att bekräfta om **Select** -åtgärden skapar förväntade resultat kan du skicka ett meddelande som innehåller utdata från **Select** -åtgärden.

1. I din Logic app lägger du till en åtgärd som kan skicka resultatet från **Select** -åtgärden.

1. I den åtgärden klickar du på var du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **uttryck**. Om du vill hämta mat ris resultatet från **Select** -åtgärden anger du det här uttrycket som innehåller namnet på **Select** -åtgärden:

   `@actionBody('Select')`

   I det här exemplet används Office 365 Outlook för att **skicka en e-** poståtgärd och inkludera utdata från `@actionBody('Select')` uttrycket i e-postmeddelandets brödtext:

   ![Åtgärds utdata från "Välj"-åtgärd](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Kör nu din Logic-app manuellt. I verktygsfältet designer väljer du **Kör**.

   Utifrån e-postkopplingen som du använde är följande resultat som du får:

   ![E-postmeddelande med åtgärds resultat för "Välj"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps anslutningar](../connectors/apis-list.md)
