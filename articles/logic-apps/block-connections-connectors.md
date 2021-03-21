---
title: Blockera anslutningar för vissa API-kopplingar
description: Begränsa skapandet och användningen av API-anslutningar i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 02d9852f6615c3926a02294e0e7eca50f2fbe9a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310040"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Blockera anslutningar som skapats av anslutningar i Azure Logic Apps

Om din organisation inte tillåter anslutning till begränsade eller ej godkända resurser genom att använda sina anslutningar i Azure Logic Apps kan du blockera möjligheten att skapa och använda dessa anslutningar i Logic app-arbetsflöden. Med [Azure policy](../governance/policy/overview.md)kan du definiera och genomdriva [principer](../governance/policy/overview.md#policy-definition) som förhindrar att du skapar eller använder anslutningar för anslutningar som du vill blockera. Av säkerhets skäl kan du till exempel vilja blockera anslutningar till vissa sociala media-plattformar eller andra tjänster och system.

I det här avsnittet visas hur du konfigurerar en princip som blockerar vissa anslutningar med hjälp av Azure Portal, men du kan skapa princip definitioner på andra sätt, till exempel via Azure REST API, Azure PowerShell, Azure CLI och Azure Resource Manager mallar. Mer information finns i [Självstudier: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

* Referens-ID för den koppling som du vill blockera. Mer information finns i [hitta referens-ID för koppling](#connector-reference-ID).

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Hitta referens-ID för koppling

Om du redan har en logisk app med den anslutning som du vill blockera följer du [stegen för Azure Portal](#connector-ID-portal). I annat fall gör du så här:

1. Gå till [listan med Logic Apps-kopplingar](/connectors/connector-reference/connector-reference-logicapps-connectors).

1. Hitta referens sidan för den koppling som du vill blockera.

   Om du till exempel vill blockera Instagram-anslutaren, som är föråldrad, går du till den här sidan:

   `https://docs.microsoft.com/connectors/instagram/`

1. Från sidans URL kopierar du och sparar referens-ID: t för kopplingen i slutet utan snedstreck ( `/` ), till exempel `instagram` .

   Senare, när du skapar en princip definition, använder du detta ID i definitionens villkors instruktion, till exempel:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure Portal

1. Leta upp och öppna din Logic app i [Azure Portal](https://portal.azure.com).

1. På menyn Logic app väljer du **vyn logiskt program kod** så att du kan visa din Logic Apps JSON-definition.

   ![Öppna "Logic app Code View" för att hitta kopplings-ID](./media/block-connections-connectors/code-view-connector-id.png)

1. Hitta `parameters` objektet som innehåller `$connections` objektet, vilket innehåller ett `{connection-name}` objekt för varje anslutning i din Logic app och anger information om anslutningen:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   För Instagram-anslutaren hittar du till exempel `instagram` objektet, som identifierar en Instagram-anslutning:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. För den anslutning som du vill blockera hittar du `id` egenskapen och värdet, som följer det här formatet: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Här är till exempel `id` egenskap och värde för en Instagram-anslutning:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. Från `id` egenskap svärdet kopierar du och sparar kopplings referens-ID: t i slutet, till exempel `instagram` .

   Senare, när du skapar en princip definition, använder du detta ID i definitionens villkors instruktion, till exempel:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Skapa en princip för att blockera skapande av anslutningar

Följ dessa steg om du vill blockera skapandet av en anslutning helt i en Logic app:

1. Logga in på [Azure-portalen](https://portal.azure.com). I rutan Portal sökning anger `policy` du och väljer **princip**.

   ![I Azure Portal, leta upp och välj "princip"](./media/block-connections-connectors/find-select-azure-policy.png)

1. Välj **definitioner**    >  **+ princip definition** under redigering i menyn princip.

   ![Välj "definitioner" > "+ princip definition"](./media/block-connections-connectors/add-new-policy-definition.png)

1. Under **princip definition** anger du informationen för princip definitionen, baserat på de egenskaper som beskrivs i exemplet:

   ![Skärm bild som visar egenskaperna för "princip definition".](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Definitions plats** | Ja | <*Azure-prenumeration-namn*> | Azure-prenumerationen som ska användas för princip definitionen <p><p>1. om du vill hitta din prenumeration väljer du knappen med tre punkter (**...**). <br>2. i **prenumerations** listan letar du reda på och väljer din prenumeration. <br>3. Välj **Välj** när du är klar. |
   | **Namn** | Ja | <*princip-definition-namn*> | Namnet som ska användas för princip definitionen |
   | **Beskrivning** | Inga | <*princip-definition-namn*> | En beskrivning av princip definitionen |
   | **Kategori** | Ja | **Logic Apps** | Namnet på en befintlig kategori eller ny kategori för princip definitionen |
   | **Principframtvingande** | Ja | **Aktiverad** | Den här inställningen anger om du vill aktivera eller inaktivera princip definitionen när du sparar ditt arbete. |
   ||||

1. Under **princip regel** fylls JSON-redigerings rutan i förväg med en princip definitions mall. Ersätt den här mallen med din [princip definition](../governance/policy/concepts/definition-structure.md) baserat på de egenskaper som beskrivs i tabellen nedan och genom att följa den här syntaxen:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | `mode` | `All` | Det läge som avgör de resurs typer som principen utvärderar. <p><p>Det här scenariot anges `mode` till `All` , som tillämpar principen på Azures resurs grupper, prenumerationer och alla resurs typer. <p><p>Mer information finns i [princip definition struktur-läge](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Villkoret som avgör när princip regeln ska genomdrivas <p><p>I det här scenariot `{condition-to-evaluate}` avgör om `api.id` värdet i `Microsoft.Web/connections/api.id` matchar på `*managedApis/{connector-name}` , vilket anger ett jokertecken (*). <p><p>Mer information finns i [princip definitions struktur – princip regel](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `field` | `Microsoft.Web/connections/api.id` | `field`Värdet som ska jämföras mot villkoret <p><p>I det här scenariot `field` använder [*aliaset alias*](../governance/policy/concepts/definition-structure.md#aliases), `Microsoft.Web/connections/api.id` för att komma åt värdet i egenskapen koppling `api.id` . |
   | `like` | `*managedApis/{connector-name}` | Den logiska operator och det värde som ska användas för att jämföra `field` värdet <p><p>I det här scenariot ser `like` operatorn och jokertecknet (*) till att regeln fungerar oavsett region och strängen, `*managedApis/{connector-name}` är det värde som ska matchas där `{connector-name}` är ID: t för den koppling som du vill blockera. <p><p>Anta till exempel att du vill blockera skapande av anslutningar till plattformar eller databaser för sociala medier: <p><p>Twitter `twitter` <br>Instagram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>– SQL Server eller Azure SQL: `sql` <p><p>Du hittar dessa kopplings-ID: n i [hitta referens-ID för Connector](#connector-reference-ID) tidigare i det här avsnittet. |
   | `then` | `{effect-to-apply}` | Den påverkan som ska gälla när `if` villkoret uppfylls <p><p>I det här scenariot `{effect-to-apply}` är att blockera och neka en begäran eller åtgärd som inte följer principen. <p><p>Mer information finns i [princip definitions struktur – princip regel](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect`Är att blockera begäran, som är att skapa den angivna anslutningen <p><p>Mer information finns i [förstå Azure policys effekter-neka](../governance/policy/concepts/effects.md#deny). |
   ||||

   Anta till exempel att du vill blockera skapande av anslutningar med Instagram-anslutningen. Här är princip definitionen som du kan använda:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Så här visas rutan **princip regel** :

   ![Skärm bild som visar rutan "princip regel" med ett exempel på en princip regel.](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   För flera kopplingar kan du lägga till fler villkor, till exempel:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. När du är klar väljer du **Spara**. När du har sparat princip definitionen genererar Azure Policy och lägger till fler egenskaps värden i princip definitionen.

1. Sedan kan du [skapa en princip tilldelning](#create-policy-assignment)genom att ange den princip definition där du vill tvinga principen.

Mer information om definitioner av Azure-principer finns i följande avsnitt:

* [Princip struktur definition](../governance/policy/concepts/definition-structure.md)
* [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)
* [Azure Policy inbyggda princip definitioner för Azure Logic Apps](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>Skapa en princip för att blockera användning av anslutningar

När du skapar en anslutning i en Logic app, finns anslutningen som separat Azure-resurs. Om du bara tar bort Logic-appen tas inte anslutningen bort automatiskt och fortsätter att finnas tills den tagits bort. Du kanske har ett scenario där anslutningen redan finns eller där du måste skapa anslutningen för användning utanför en Logic app. Du kan fortfarande blockera möjligheten att använda en befintlig anslutning i en Logic app genom att skapa en princip som förhindrar att du sparar Logi Kap par som har begränsad eller ej godkänd anslutning.

1. Logga in på [Azure-portalen](https://portal.azure.com). I rutan Portal sökning anger `policy` du och väljer **princip**.

   ![I Azure Portal, leta upp och välj "princip"](./media/block-connections-connectors/find-select-azure-policy.png)

1. Välj **definitioner**    >  **+ princip definition** under redigering i menyn princip.

   ![Välj "definitioner" > "+ princip definition"](./media/block-connections-connectors/add-new-policy-definition.png)

1. Under **princip definition** anger du informationen för princip definitionen baserat på de egenskaper som beskrivs i exemplet och fortsätter med Instagram som exempel:

   ![Egenskaper för princip definition](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Definitions plats** | Ja | <*Azure-prenumeration-namn*> | Azure-prenumerationen som ska användas för princip definitionen <p><p>1. om du vill hitta din prenumeration väljer du knappen med tre punkter (**...**). <br>2. i **prenumerations** listan letar du reda på och väljer din prenumeration. <br>3. Välj **Välj** när du är klar. |
   | **Namn** | Ja | <*princip-definition-namn*> | Namnet som ska användas för princip definitionen |
   | **Beskrivning** | Inga | <*princip-definition-namn*> | En beskrivning av princip definitionen |
   | **Kategori** | Ja | **Logic Apps** | Namnet på en befintlig kategori eller ny kategori för princip definitionen |
   | **Principframtvingande** | Ja | **Aktiverad** | Den här inställningen anger om du vill aktivera eller inaktivera princip definitionen när du sparar ditt arbete. |
   ||||

1. Under **princip regel** fylls JSON-redigerings rutan i förväg med en princip definitions mall. Ersätt den här mallen med din [princip definition](../governance/policy/concepts/definition-structure.md) baserat på de egenskaper som beskrivs i tabellen nedan och genom att följa den här syntaxen:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | `mode` | `All` | Det läge som avgör de resurs typer som principen utvärderar. <p><p>Det här scenariot anges `mode` till `All` , som tillämpar principen på Azures resurs grupper, prenumerationer och alla resurs typer. <p><p>Mer information finns i [princip definition struktur-läge](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Villkoret som avgör när princip regeln ska genomdrivas <p><p>I det här scenariot `{condition-to-evaluate}` avgör om strängen utdata från `[string(field('Microsoft.Logic/workflows/parameters'))]` , innehåller strängen, `{connector-name}` . <p><p>Mer information finns i [princip definitions struktur – princip regel](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | Värdet som ska jämföras mot villkoret <p><p>I det här scenariot `value` är strängen utdata från `[string(field('Microsoft.Logic/workflows/parameters'))]` , som konverterar `$connectors` objektet i `Microsoft.Logic/workflows/parameters` objektet till en sträng. |
   | `contains` | `{connector-name}` | Den logiska operatorn och det värde som ska användas för att jämföra med `value` egenskapen <p><p>I det här scenariot kontrollerar `contains` operatorn att regeln fungerar oavsett var den `{connector-name}` visas, där strängen, `{connector-name}` är ID: t för den koppling som du vill begränsa eller blockera. <p><p>Anta till exempel att du vill blockera användning av anslutningar till sociala medie plattformar eller databaser: <p><p>Twitter `twitter` <br>Instagram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>– SQL Server eller Azure SQL: `sql` <p><p>Du hittar dessa kopplings-ID: n i [hitta referens-ID för Connector](#connector-reference-ID) tidigare i det här avsnittet. |
   | `then` | `{effect-to-apply}` | Den påverkan som ska gälla när `if` villkoret uppfylls <p><p>I det här scenariot `{effect-to-apply}` är att blockera och redundansväxla en begäran eller åtgärd som inte följer principen. <p><p>Mer information finns i [princip definitions struktur – princip regel](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect`Är att `deny` eller blockera begäran om att spara en Logic app som använder den angivna anslutningen <p><p>Mer information finns i [förstå Azure policys effekter-neka](../governance/policy/concepts/effects.md#deny). |
   ||||

   Anta till exempel att du vill blockera att spara Logic Apps som använder Instagram-anslutningar. Här är princip definitionen som du kan använda:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Så här visas rutan **princip regel** :

   ![Regel för princip definition](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. När du är klar väljer du **Spara**. När du har sparat princip definitionen genererar Azure Policy och lägger till fler egenskaps värden i princip definitionen.

1. Sedan kan du [skapa en princip tilldelning](#create-policy-assignment)genom att ange den princip definition där du vill tvinga principen.

Mer information om definitioner av Azure-principer finns i följande avsnitt:

* [Princip struktur definition](../governance/policy/concepts/definition-structure.md)
* [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)
* [Azure Policy inbyggda princip definitioner för Azure Logic Apps](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Skapa princip tilldelning

Därefter måste du tilldela den princip definition där du vill genomdriva principen, till exempel till en enda resurs grupp, flera resurs grupper, Azure Active Directory (Azure AD) eller Azure-prenumeration. För den här uppgiften följer du stegen nedan för att skapa en princip tilldelning:

1. Om du loggat ut loggar du in på [Azure Portal](https://portal.azure.com)igen. I rutan Portal sökning anger `policy` du och väljer **princip**.

   ![I Azure Portal, leta upp och välj "princip"](./media/block-connections-connectors/find-select-azure-policy.png)

1. I menyn **princip** , under **redigering**, väljer du **tilldelningar**  >  **tilldela princip**.

   ![Välj tilldelningar > tilldela](./media/block-connections-connectors/add-new-policy-assignment.png)

1. Under **grunderna** anger du den här informationen för princip tilldelningen:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Omfång** | Ja | De resurser där du vill genomdriva princip tilldelningen. <p><p>1. Klicka på knappen med tre punkter (**...**) bredvid rutan **omfång** . <br>2. Välj Azure-prenumerationen i listan **prenumeration** . <br>3. du kan också välja resurs gruppen från listan **resurs grupp** . <br>4. När du är klar väljer du **Välj**. |
   | **Undantag** | Inga | Alla Azure-resurser som ska undantas från princip tilldelningen. <p><p>1. Klicka på knappen med tre punkter (**...**) bredvid rutan **undantag** . <br>2. Välj resurs > **Lägg till i det valda omfånget** i **resurs** listan. <br>3. När du är klar väljer du **Spara**. |
   | **Principdefinition** | Ja | Namnet på den princip definition som du vill tilldela och tillämpa. Det här exemplet fortsätter med exempel Instagram-principen "blockera Instagram-anslutningar". <p><p>1. Klicka på knappen med tre punkter (**...**) bredvid rutan **princip definition** . <br>2 **. Sök efter** och välj princip definitionen med hjälp av **typ** filtret eller sökrutan. <br>3. Välj **Välj** när du är klar. |
   | **Namn på tilldelning** | Ja | Namnet som ska användas för princip tilldelningen, om det skiljer sig från princip definitionen |
   | **Tilldelnings-ID** | Ja | Automatiskt genererat ID för princip tilldelningen |
   | **Beskrivning** | Inga | En beskrivning av princip tilldelningen |
   | **Principframtvingande** | Ja | Inställningen som aktiverar eller inaktiverar princip tilldelningen |
   | **Tilldelad av** | Inga | Namnet på den person som skapade och tillämpade princip tilldelningen |
   ||||

   Om du till exempel vill tilldela principen till en Azure-resurs grupp med hjälp av Instagram-exemplet:

   ![Egenskaper för princip tilldelning](./media/block-connections-connectors/policy-assignment-basics.png)

1. När du är klar väljer du **Granska + skapa**.

   När du har skapat en princip kan du behöva vänta upp till 15 minuter innan principen börjar gälla. Ändringar kan också ha liknande fördröjda effekter.

1. När principen träder i funktion kan du [testa principen](#test-policy).

Mer information finns i [snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser](../governance/policy/assign-policy-portal.md).

<a name="test-policy"></a>

## <a name="test-the-policy"></a>Testa principen

Om du vill testa principen börjar du skapa en anslutning med hjälp av den nu begränsade anslutningen i Logic App Designer. Om du fortsätter med Instagram-exemplet visas ett fel meddelande om att din Logic app inte kunde skapa anslutningen när du loggar in på Instagram:

![Anslutnings problem på grund av tillämpad princip](./media/block-connections-connectors/connection-failure-message.png)

Meddelandet innehåller följande information:

| Beskrivning | Innehåll |
|-------------|---------|
| Orsak till det här problemet | `"Resource 'instagram' was disallowed by policy."` |
| Namn på tilldelning | `"Block Instagram connections"` |
| Tilldelnings-ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| ID för princip definition | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure policy](../governance/policy/overview.md)