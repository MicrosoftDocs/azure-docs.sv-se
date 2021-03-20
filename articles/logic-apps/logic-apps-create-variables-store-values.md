---
title: Skapa och hantera variabler för att lagra och skicka värden
description: Lär dig hur du lagrar, hanterar, använder och överför värden med hjälp av variabler i automatiserade uppgifter och arbets flöden som du skapar med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91333910"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Lagra och hantera värden med variabler i Azure Logic Apps

Den här artikeln visar hur du skapar och arbetar med variabler som du använder för att lagra värden i din Logic app. Variabler kan till exempel hjälpa dig att spåra hur många gånger en slinga körs. Om du vill iterera över en matris eller kontrol lera en matris för ett särskilt objekt, kan du använda en variabel för att referera till index numret för varje mat ris element.

Du kan skapa variabler för data typer som till exempel Integer, Float, Boolean, String, array och Object. När du har skapat en variabel kan du utföra andra uppgifter, till exempel:

* Hämta eller referera till variabelns värde.
* Öka eller minska variabeln med ett konstant värde, även kallat *öka* och *minska*.
* Tilldela variabeln ett annat värde.
* Infoga eller *Lägg till* variabelns värde som det sista objektet i en sträng eller matris.

Variabler finns och är globala endast i den Logic App-instans som skapar dem. De bevaras även i alla upprepningar i en Logic App-instans. När du refererar till en variabel använder du variabelns namn som token, inte åtgärdens namn, vilket är det vanligaste sättet att referera till en åtgärds utdata.

> [!IMPORTANT]
> Som standard körs cykler i en "for each"-loop parallellt. När du använder variabler i loopar kan du köra loopen [sekventiellt](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) så att variabler returnerar förutsägbara resultat.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic app där du vill skapa variabeln

  Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i din Logic app

  Innan du kan lägga till åtgärder för att skapa och arbeta med variabler måste din Logic app starta med en utlösare.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Initiera variabel

Du kan skapa en variabel och deklarera dess datatyp och initialt värde – alla inom en åtgärd i din Logic app. Du kan bara deklarera variabler på global nivå, inte inom omfattningar, villkor och slingor.

1. I [Azure Portal](https://portal.azure.com) eller Visual Studio öppnar du din Logic app i Logic Apps designer.

   I det här exemplet används Azure Portal och en Logic-app med en befintlig utlösare.

1. I din Logic app, under steget där du vill lägga till en variabel, följer du något av följande steg: 

   * Välj **nytt steg** för att lägga till en åtgärd under det sista steget.

     ![Skärm bild som visar åtgärden "nytt steg" som valts på sidan "Logic App Designer".](./media/logic-apps-create-variables-store-values/add-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du musen över den anslutande pilen så att plus tecknet ( **+** ) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** går du till rutan Sök och anger `variables` som ditt filter. I listan åtgärder väljer du **initiera variabel**.

   ![Välj åtgärd](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Ange den här informationen om din variabel enligt beskrivningen nedan:

   | Egenskap | Krävs | Värde |  Beskrivning |
   |----------|----------|-------|--------------|
   | **Namn** | Ja | <*variabel namn*> | Namnet på variabeln som ska ökas |
   | **Typ** | Ja | <*variabel typ*> | Data typen för variabeln |
   | **Värde** | Inga | <*Start värde*> | Det inledande värdet för variabeln <p><p>**Tips**: du kan även ange det här värdet som bästa praxis så att du alltid vet startvärdet för din variabel. |
   |||||

   Exempel:

   ![Initiera variabel](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Fortsätt nu med att lägga till de åtgärder som du vill använda. När du är klar väljer du **Spara** i verktygsfältet designer.

Om du växlar från designern till kodvyn visas här hur åtgärden **initiera variabeln** visas i din Logic app-definition, som är i JavaScript Object Notation (JSON)-format:

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> Även om åtgärden **initiera variabel** har ett `variables` avsnitt som är strukturerat som en matris, kan åtgärden bara skapa en variabel i taget. Varje ny variabel kräver en enskild **initierande variabel** åtgärd.

Här följer några exempel på några andra variabel typer:

*Sträng variabel*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Boolesk variabel*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Matris med heltal*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Matris med strängar*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Hämta variabelns värde

Om du vill hämta eller referera till en variabels innehåll kan du också använda [funktionen variabler ()](../logic-apps/workflow-definition-language-functions-reference.md#variables) i Logic App Designer och kodvyn. När du refererar till en variabel använder du variabelns namn som token, inte åtgärdens namn, vilket är det vanligaste sättet att referera till en åtgärds utdata.

Till exempel hämtar det här uttrycket de objekt från array-variabeln som [skapades tidigare i den här artikeln](#append-value) med hjälp av `variables()` funktionen. `string()`Funktionen returnerar variabelns innehåll i sträng format:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Increment-variabel 

Om du vill öka eller *öka* en variabel med ett konstant värde lägger du till åtgärden **öka variabeln** i din Logic app. Den här åtgärden fungerar endast med heltal och float-variabler.

1. I Logic App Designer, under det steg där du vill öka en befintlig variabel, väljer du **nytt steg**. 

   Till exempel har den här Logic-appen redan en utlösare och en åtgärd som skapade en variabel. Lägg därför till en ny åtgärd i följande steg:

   ![Lägg till åtgärd](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen så att plus tecknet (+) visas. Välj plus tecknet och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "öka variabel" som filter. I listan åtgärder väljer du **öka variabel**.

   ![Välj åtgärden "öka variabel"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Ange den här informationen för att öka din variabel:

   | Egenskap | Krävs | Värde |  Beskrivning |
   |----------|----------|-------|--------------|
   | **Namn** | Ja | <*variabel namn*> | Namnet på variabeln som ska ökas |
   | **Värde** | Inga | <*öka värde*> | Det värde som används för att öka variabeln. Standardvärdet är ett. <p><p>**Tips**: du kan även ange det här värdet som bästa praxis så att du alltid känner till det exakta värdet för att öka variabeln. |
   ||||

   Exempel:

   ![Exempel på värde för ökning](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. När du är klar väljer du **Spara** i verktygsfältet designer.

Om du växlar från designern till kodvyn visas här hur den **stegvisa variabel** åtgärden visas i din Logic app-definition, som är i JSON-format:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Exempel: skapa loop-räknare

Variabler används ofta för att räkna antalet gånger som en slinga körs. Det här exemplet visar hur du skapar och använder variabler för den här aktiviteten genom att skapa en slinga som räknar de bifogade filerna i ett e-postmeddelande.

1. Skapa en tom Logic-app i Azure Portal. Lägg till en utlösare som söker efter ny e-post och bifogade filer.

   I det här exemplet används Office 365 Outlook-utlösare för **när ett nytt e-postmeddelande tas emot**. Du kan konfigurera den här utlösaren så att den endast utlöses när e-postmeddelandet har bilagor. Du kan dock använda en koppling som söker efter nya e-postmeddelanden med bifogade filer, till exempel Outlook.com-anslutningsprogrammet.

1. I utlösaren kan du söka efter bilagor och skicka dessa bilagor till din Logic app-arbetsflöde genom att välja **Ja** för följande egenskaper:

   * **Has Attachment** (Innehåller bifogad fil)
   * **Inkludera bifogade filer**

   ![Sök efter och inkludera bifogade filer](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Lägg till [åtgärden **initiera variabel**](#create-variable). Skapa en heltals variabel med namnet `Count` som har ett start värde som är noll.

   ![Lägg till åtgärd för "initiera variabel"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Om du vill gå igenom varje bilaga lägger du till en *för varje* slinga.

   1. Under åtgärden **initiera variabel** väljer du **nytt steg**.

   1. Under **Välj en åtgärd** väljer du **inbyggt**. I rutan Sök anger `for each` du som Sök filter och väljer **för varje**.

      ![Lägg till en "för varje"-slinga](./media/logic-apps-create-variables-store-values/add-loop.png)

1. I slingan klickar du i rutan **Välj utdata från föregående steg** . När listan med dynamiskt innehåll visas väljer du **bifogade filer**.

   ![Välj ”Bifogade filer”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Egenskapen **Attachments** överför en matris, som innehåller e-postbilagor från utlösaren i slingan.

1. I **för varje** slinga väljer du **Lägg till en åtgärd**.

   ![Välj Lägg till en åtgärd](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. I rutan Sök anger du "öka variabel" som filter. I listan åtgärder väljer du **öka variabel**.

   > [!NOTE]
   > Se till att den **ökande variabel** åtgärden visas i slingan. Om åtgärden visas utanför loopen drar du åtgärden till slingan.

1. I den **stegvisa variabel** åtgärden, i listan **namn** , väljer du variabeln **Count** .

   ![Välj variabeln "antal"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. Under slingan lägger du till alla åtgärder som skickar dig antalet bilagor. I din åtgärd inkluderar du värdet från variabeln **Count** , till exempel:

   ![Lägg till en åtgärd som skickar resultat](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. Om din Logic app inte är aktive rad väljer du **Översikt** på din Logic app-meny. Välj **Aktivera** i verktygsfältet.

1. I verktygsfältet Logic App Designer väljer du **Kör**. Det här steget startar din Logic app manuellt.

1. Skicka ett e-postmeddelande med en eller flera bilagor till e-postkontot som du använde i det här exemplet.

   Det här steget utlöses av Logic Apps-utlösaren, som skapar och kör en instans för din Logic app-arbetsflöde. Därför skickar Logic-appen ett meddelande eller e-postmeddelande som visar antalet bifogade filer i e-postmeddelandet som du har skickat.

Om du växlar från designern till kodvyn visas här hur **för varje** slinga tillsammans med åtgärden **öka variabeln** i din Logic app-definition, som är i JSON-format.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Minska variabel

Om du vill minska eller *minska* en variabel med ett konstant värde följer du stegen för att [öka en variabel](#increment-value) , förutom att du hittar och väljer åtgärden **minska variabeln** i stället. Den här åtgärden fungerar endast med heltal och float-variabler.

Här följer egenskaperna för åtgärden **minska variabeln** :

| Egenskap | Krävs | Värde |  Beskrivning |
|----------|----------|-------|--------------|
| **Namn** | Ja | <*variabel namn*> | Namnet på variabeln som ska minskas | 
| **Värde** | Inga | <*öka värde*> | Värdet för decrementing variabeln. Standardvärdet är ett. <p><p>**Tips**: du kan även ange det här värdet som bästa praxis så att du alltid vet det exakta värdet för decrementing av din variabel. |
||||| 

Om du växlar från designern till kodvyn visas här hur åtgärden **minska variabeln** visas i din Logic app-definition, som är i JSON-format.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>Ange variabel

Om du vill tilldela ett annat värde till en befintlig variabel följer du stegen för att [öka en variabel](#increment-value) , förutom att du:

1. Sök efter och välj **variabel åtgärden Ställ in** i stället.

1. Ange variabel namn och värde som du vill tilldela. Både det nya värdet och variabeln måste ha samma datatyp. Värdet är obligatoriskt eftersom den här åtgärden inte har något standardvärde.

Här följer egenskaperna för åtgärden **ange variabel** :

| Egenskap | Krävs | Värde |  Beskrivning |
|----------|----------|-------|--------------|
| **Namn** | Ja | <*variabel namn*> | Namnet på variabeln som ska ändras |
| **Värde** | Ja | <*nytt – värde*> | Värdet som du vill tilldela variabeln. Båda måste ha samma datatyp. |
||||| 

> [!NOTE]
> Om du inte ökar eller decrementing variabler *kan det hända* att om du ändrar variabler i loopar kan du skapa oväntade resultat eftersom looparna körs parallellt eller samtidigt, som standard. I dessa fall kan du prova att ställa in loopen så att den körs sekventiellt. Om du till exempel vill referera till variabelvärdet i slingan och förväntar sig samma värde i början och slutet av loop-instansen, följer du dessa steg för att ändra hur loopen körs: 
>
> 1. I loopens övre högra hörn väljer du knappen med tre punkter (**...**) och väljer sedan **Inställningar**.
> 
> 2. Under **concurrency-kontroll** ändrar du **Åsidosätt standardinställningen** till **på**.
>
> 3. Dra skjutreglaget **för parallellitet** till **1**.

Om du växlar från designern till kodvyn visas här hur åtgärden **ange variabel** visas i din Logic app-definition, som är i JSON-format. Det här exemplet ändrar `Count` variabelns aktuella värde till ett annat värde.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Lägg till i variabel

För variabler som lagrar strängar eller matriser kan du infoga eller *lägga till* en variabels värde som det sista objektet i dessa strängar eller matriser. Du kan följa stegen för att [öka en variabel](#increment-value) , förutom att du följer de här stegen i stället: 

1. Sök efter och välj en av dessa åtgärder baserat på om din variabel är en sträng eller en matris: 

   * **Lägg till i sträng variabel**
   * **Lägg till i mat ris variabel** 

1. Ange det värde som ska läggas till som det sista objektet i strängen eller matrisen. Det här värdet är obligatoriskt.

Här följer egenskaperna för åtgärderna **APPEND to...** :

| Egenskap | Krävs | Värde |  Beskrivning |
|----------|----------|-------|--------------|
| **Namn** | Ja | <*variabel namn*> | Namnet på variabeln som ska ändras |
| **Värde** | Ja | <*tilläggs värde*> | Värdet som du vill lägga till, vilket kan ha vilken typ som helst |
|||||

Om du växlar från designern till kodvyn visas här hur åtgärden **Lägg till mat ris variabel** visas i din Logic app-definition, som är i JSON-format. Det här exemplet skapar en mat ris variabel och lägger till ett annat värde som det sista objektet i matrisen. Resultatet är en uppdaterad variabel som innehåller den här matrisen: `[1,2,3,"red"]`

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps anslutningar](../connectors/apis-list.md)
