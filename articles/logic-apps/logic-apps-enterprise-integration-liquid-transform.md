---
title: Konvertera JSON och XML med flytande mallar
description: Transformera JSON och XML med hjälp av flytande mallar som Maps i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0362c9ed4f736474dbd49e1bfaf1373e0f48acd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992717"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Transformera JSON och XML med flytande mallar som Maps i Azure Logic Apps

När du vill utföra grundläggande JSON-omvandlingar i dina Logi Kap par kan du använda inbyggda [data åtgärder](../logic-apps/logic-apps-perform-data-operations.md) som att **skapa** eller **parsa JSON**. För avancerad och komplex JSON till JSON-omvandlingar som har element som iterationer, kontroll flöden och variabler skapar du och använder mallar som beskriver dessa omvandlingar med hjälp av mallen för [flytande](https://shopify.github.io/liquid/) öppen källkod. Du kan också [utföra andra transformeringar](#other-transformations), till exempel JSON till text, XML till JSON och XML till text.

Innan du kan utföra en flytande omvandling i din Logic app måste du först skapa en flytande mall som definierar den mappning som du vill använda. Sedan [laddar du upp mallen som en karta](../logic-apps/logic-apps-enterprise-integration-maps.md) i ditt [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). När du lägger till **transformerings-JSON till JSON-vätske** åtgärden i din Logic app, kan du välja den flytande mallen som kartan för den åtgärd som ska användas.

Den här artikeln visar hur du utför dessa uppgifter:

* Skapa en flytande mall.
* Lägg till mallen i ditt integrations konto.
* Lägg till åtgärden flytande omvandling i din Logic app.
* Välj mallen som den karta som du vill använda.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Grundläggande kunskaper om [flytande mallar språk](https://shopify.github.io/liquid/)

  > [!NOTE]
  > **Transformerings-JSON till JSON-vätske** åtgärd följer [DotLiquid-implementeringen för vätska](https://github.com/dotliquid/dotliquid), vilket skiljer sig i vissa fall från [Shopify-implementeringen för flytande](https://shopify.github.io/liquid). Mer information finns i [överväganden om flytande mallar](#liquid-template-considerations).

## <a name="create-the-template"></a>Skapa mallen

1. Skapa den flytande mall som du använder som karta för JSON-omvandlingen. Du kan använda vilket redigerings verktyg som helst.

   I det här exemplet skapar du exempel mal len flytande som beskrivs i det här avsnittet:

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. Spara mallen med hjälp av `.liquid` tillägget. I det här exemplet används `SimpleJsonToJsonTemplate.liquid`.

## <a name="upload-the-template"></a>Ladda upp mallen

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. I rutan Azure Portal Sök anger `integration accounts` du och väljer **integrations konton**.

   ![Hitta "integrations konton"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Sök efter och välj ditt integrations konto.

   ![Välj integrations konto](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. I **översikts** fönstret under **komponenter** väljer du **Maps**.

    ![Välj panelen kartor](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. I fönstret **Maps** väljer du **Lägg till** och anger den här informationen för kartan:

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | `JsonToJsonTemplate` | Namnet på kartan, som är "JsonToJsonTemplate" i det här exemplet |
   | **Kart typ** | **flytande** | Kartans typ. För JSON till JSON-transformering måste du välja **flytande**. |
   | **Karta** | `SimpleJsonToJsonTemplate.liquid` | En befintlig flytande mall eller kart fil som ska användas för omvandling, som är "SimpleJsonToJsonTemplate. flytande" i det här exemplet. Du kan använda fil väljaren för att hitta den här filen. För mappnings storleks gränser, se [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   |||

   ![Lägg till flytande mall](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Lägg till flytande omvandlings åtgärd

1. I Azure Portal följer du de här stegen för att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Lägg till [utlösaren för begäran](../connectors/connectors-native-reqres.md#add-request) till din Logic app i Logic App Designer.

1. Under utlösaren väljer du **nytt steg**. I rutan Sök anger `liquid` du som filter och väljer den här åtgärden: **transformera JSON till JSON-flytande**

   ![Sök och välj flytande åtgärd](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Öppna **kart** listan och välj din flytande mall, som är "JsonToJsonTemplate" i det här exemplet.

   ![Välj karta](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Om Maps-listan är tom är den mest sannolika att din Logic app inte är länkad till ditt integrations konto. 
   Följ dessa steg om du vill länka din Logic app till det integrations konto som har den flytande mallen eller kartan:

   1. På din Logic app-meny väljer du **arbets flödes inställningar**.

   1. I listan **Välj ett integrations konto** väljer du ditt integrations konto och väljer **Spara**.

      ![Länka Logic app till integrations konto](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Lägg nu till egenskapen **innehåll** i den här åtgärden. Öppna listan **Lägg till ny parameter** och välj **innehåll**.

   ![Lägg till egenskapen content i åtgärden](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Ange **innehålls** egenskap svärdet genom att klicka inuti **innehålls** rutan så att listan med dynamiskt innehåll visas. Välj **Body** -token, som representerar bröd textens utdata från utlösaren.

   ![Välj "Body"-token för egenskap svärdet "content"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   När du är klar ser villkoret ut som i det här exemplet:

   ![Åtgärden transformera JSON till JSON har utförts](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Testa din Logic app

Använd [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg för att skicka JSON-indata till din Logic app. De transformerade JSON-utdata från din Logic app ser ut som i det här exemplet:

![Exempel på utdata](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Överväganden för flytande mallar

* Flytande mallar följer [fil storleks gränserna för Maps](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) i Azure Logic Apps.

* **Transformerings-JSON till JSON-vätske** åtgärd följer [DotLiquid-implementeringen för vätska](https://github.com/dotliquid/dotliquid). Den här implementeringen är en port till .NET Framework från [Shopify-implementeringen för vätska](https://shopify.github.io/liquid/) och skiljer sig i [vissa fall](https://github.com/dotliquid/dotliquid/issues).

  Här är de kända skillnaderna:

  * **Transformerings-JSON till JSON-vätske** åtgärd matar internt ut en sträng, som kan innehålla JSON, XML, HTML och så vidare. Den flytande åtgärden anger bara att den förväntade text utmatningen från flytande mall är en JSON-sträng. Åtgärden instruerar din Logi Kap par att parsa inmatade objekt som JSON-objekt och tillämpar en omslutning så att vätskor kan tolka JSON-strukturen. Efter omvandlingen instruerar åtgärden din Logic app att parsa text resultatet från flytande tillbaka till JSON.

    DotLiquid förstår inte internt JSON, så se till att du undantar omvänt snedstreck ( `\` ) och andra reserverade JSON-tecken.

  * Om din mall använder [vätske filter](https://shopify.github.io/liquid/basics/introduction/#filters), se till att du följer [namngivnings konventionerna DotLiquid och C#](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing), som använder *menings hölje*. För alla flytande transformeringar kontrollerar du att filter namnen i mallen också använder menings höljen. Annars fungerar inte filtren.

    Om du t. ex. använder `replace` filtret används `Replace` , inte `replace` . Samma regel gäller om du provar exempel på [DotLiquid online](http://dotliquidmarkup.org/try-online). Mer information finns i [Shopify flytande filter](https://shopify.dev/docs/themes/liquid/reference/filters) och [filter för DotLiquid-vätskor](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters). Shopify-specifikationen innehåller exempel för varje filter, så för jämförelse kan du prova de här exemplen på [DotLiquid-try online](http://dotliquidmarkup.org/try-online).

  * `json`Filtret från Shopify-tilläggs filtren är för närvarande [inte implementerat i DotLiquid](https://github.com/dotliquid/dotliquid/issues/384). Normalt kan du använda det här filtret för att förbereda text utdata för JSON-sträng tolkning, men i stället måste du använda `Replace` filtret i stället.

  * Standard `Replace` filtret i DotLiquid- [implementeringen](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) använder [regex-matchning (reguljärt uttryck)](/dotnet/standard/base-types/regular-expression-language-quick-reference), medan [Shopify-implementeringen](https://shopify.github.io/liquid/filters/replace/) använder [enkel sträng matchning](https://github.com/Shopify/liquid/issues/202). Båda implementeringarna verkar fungera på samma sätt tills du använder ett RegEx-reserverat tecken eller ett escape-tecken i matchnings parametern.

    Om du till exempel vill kringgå det RegEx-reserverade omvänt snedstrecket ( `\` ) escape-tecken, använder `| Replace: '\\', '\\'` du och inte `| Replace: '\', '\\'` . I de här exemplen visas hur `Replace` filtret beter sig annorlunda när du försöker kringgå omvänt snedstreck. Medan den här versionen fungerar:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    Med det här resultatet:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Den här versionen Miss lyckas:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    Med det här felet:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    Mer information finns i [replace standard filter använder regex-mönster matchning...](https://github.com/dotliquid/dotliquid/issues/385).

  * `Sort`Filtret i DotLiquid- [implementeringen](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) sorterar objekt i en matris eller samling efter egenskap, men med följande skillnader:<p>

    * Följande [Shopify beteende för sort_natural](https://shopify.github.io/liquid/filters/sort_natural/), inte [Shopify sorterings beteende](https://shopify.github.io/liquid/filters/sort/).

    * Sorterar endast i sträng alfanumerisk ordning. Mer information finns i [numerisk sortering](https://github.com/Shopify/liquid/issues/980).

    * Använder *SKIFT* läges okänslig ordning, inte Skift läges känslig ordning. Mer information finns i [sorterings filtret följer inte Skift läges beteendet i Shopify-specifikationen]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Andra omvandlingar med flytande

Flytande är inte begränsad till endast JSON-omvandlingar. Du kan också använda vätska för att utföra andra transformeringar, till exempel:

* [JSON till text](#json-text)
* [XML till JSON](#xml-json)
* [XML till text](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>Transformera JSON till text

Här är den flytande mall som används för det här exemplet:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Här är exempel på indata och utdata:

![Exempel på utdata-JSON till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>Transformera XML till JSON

Här är den flytande mall som används för det här exemplet:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor`Slingan är en anpassad upprepnings funktion för XML-indata så att du kan skapa JSON-nyttolaster som undviker ett avslutande kommatecken. `where`Villkoret för den här anpassade loopen använder dessutom XML-elementets namn för jämförelse, i stället för elementets värde som andra vätske filter. Mer information finns i [djupet om insamlings princip – samling av saker](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Här är exempel på indata och utdata:

![Exempel på utdata-XML till JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>Transformera XML till text

Här är den flytande mall som används för det här exemplet:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Här är exempel på indata och utdata:

![Exempel på utdata-XML till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Nästa steg

* [Shopify – flytande språk och exempel](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid – prova online](http://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub-problem](https://github.com/dotliquid/dotliquid/issues/)
* Läs mer om [Maps](../logic-apps/logic-apps-enterprise-integration-maps.md)
