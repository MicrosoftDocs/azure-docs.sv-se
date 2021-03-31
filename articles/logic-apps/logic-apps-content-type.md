---
title: Hantera innehållstyper
description: Lär dig hur du hanterar olika innehålls typer i arbets flöden under design tid och kör tid i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 04642e69adba82cf33b933829d72c51109ad2ee1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578201"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Hantera innehålls typer i Azure Logic Apps

Olika innehålls typer kan flöda via en Logic app, till exempel JSON, XML, flata filer och binära data. Även om Logic Apps stöder alla innehålls typer, har vissa inbyggda support och kräver inte data omvandling eller konvertering i dina Logi Kap par. Andra typer kan kräva data omvandling eller omvandling vid behov. I den här artikeln beskrivs hur Logic Apps hanterar innehålls typer och hur du korrekt kan omvandla eller konvertera dessa typer vid behov.

För att fastställa lämpligt sätt för att hantera innehålls typer Logic Apps förlitar `Content-Type` sig på huvud värde i HTTP-anrop, till exempel:

* [Application/JSON](#application-json) (intern typ)
* [text/plain](#text-plain) (intern typ)
* [program/XML och program/oktett-Stream](#application-xml-octet-stream)
* [Andra innehålls typer](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps lagrar och hanterar en begäran med innehålls typen *program/JSON* som ett JavaScript-objekt (JavaScript-notation). Som standard kan du parsa JSON-innehåll utan data typs byte. Om du vill parsa en begäran som har ett huvud med innehålls typen "Application/JSON" kan du använda ett uttryck. I det här exemplet returneras värdet `dog` från `animal-type` matrisen utan databyte: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Om du arbetar med JSON-data som inte anger något sidhuvud kan du manuellt omvandla dessa data till JSON med hjälp av [JSON ()-funktionen](../logic-apps/workflow-definition-language-functions-reference.md#json), till exempel: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Skapa token för JSON-egenskaper

Logic Apps ger dig möjlighet att generera användarvänliga token som representerar egenskaperna i JSON-innehåll så att du kan referera till och använda dessa egenskaper enklare i din Logic app-arbetsflöde.

* **Begär utlösare**

  När du använder den här utlösaren i Logic App Designer kan du ange ett JSON-schema som beskriver den nytto last som du förväntar dig att ta emot. 
  Designern parsar JSON-innehåll med hjälp av det här schemat och genererar användarvänliga token som representerar egenskaperna i ditt JSON-innehåll. 
  Du kan sedan enkelt referera till och använda dessa egenskaper i din Logic app-arbetsflöde. 
  
  Om du inte har ett schema kan du generera schemat. 
  
  1. I utlösaren för begäran väljer **du Använd exempel nytto last för att generera schemat**.  
  
  2. Under **Ange eller klistra in en exempel-JSON-nyttolast**, anger du ett exempel på en nytto last och väljer sedan **färdig**. Exempel: 

     ![Skärm bild som visar åtgärden "när en HTTP-begäran tas emot" med ett exempel på en JSON-nyttolast.](./media/logic-apps-content-type/request-trigger.png)

     Det genererade schemat visas nu i utlösaren.

     ![Tillhandahåll exempel-JSON-nyttolast](./media/logic-apps-content-type/generated-schema.png)

     Här är den underliggande definitionen för din begär ande utlösare i kodvyn:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. Se till att du inkluderar ett `Content-Type` sidhuvud och ange värdet för rubriken i din begäran `application/json` .

* **Parsa JSON-åtgärd**

  När du använder den här åtgärden i Logic App Designer kan du parsa JSON-utdata och generera användarvänliga token som representerar egenskaperna i ditt JSON-innehåll. 
  Du kan sedan enkelt referera till och använda dessa egenskaper i din Logic app-arbetsflöde. Precis som i utlösaren för förfrågningar kan du ange eller generera ett JSON-schema som beskriver det JSON-innehåll som du vill parsa. 
  På så sätt kan du enklare använda data från Azure Service Bus, Azure Cosmos DB och så vidare.

  ![Parsa JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/ren

När din Logic-app tar emot HTTP-meddelanden som har `Content-Type` sidhuvudet inställt på `text/plain` , lagrar din Logic app dessa meddelanden i RAW form. Om du inkluderar dessa meddelanden i efterföljande åtgärder utan databyte, så fortsätter begär Anden med `Content-Type` sidhuvudet inställt på `text/plain` . 

Om du till exempel arbetar med en platt fil kan du få en HTTP-begäran med `Content-Type` sidhuvudet inställt på `text/plain` innehålls typ:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Om du sedan skickar den här begäran i en senare åtgärd som brödtext för en annan begäran, till exempel `@body('flatfile')` , innehåller den andra förfrågan även en `Content-Type` rubrik som är inställd på `text/plain` . Om du arbetar med data som är oformaterad text men inte har angett något sidhuvud, kan du manuellt omvandla dessa data till text med hjälp av [funktionen String ()](../logic-apps/workflow-definition-language-functions-reference.md#string) , till exempel följande uttryck: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>program/XML och program/oktett-Stream

Logic Apps bevarar alltid `Content-Type` i en mottagen HTTP-begäran eller-svar. Så om din Logi Kap par tar emot innehåll med `Content-Type` set till `application/octet-stream` och du inkluderar det innehållet i en senare åtgärd utan databyte, har den utgående begäran också `Content-Type` angetts till `application/octet-stream` . På så sätt kan Logic Apps garantera att data inte går förlorade vid förflyttning i arbets flödet. Åtgärds tillstånd, eller indata och utdata, lagras dock i ett JSON-objekt medan statusen flyttas genom arbets flödet. 

## <a name="converter-functions"></a>Omvandlare-funktioner

För att bevara vissa data typer, Logic Apps konverterar innehåll till en binär Base64-kodad sträng med lämpliga metadata som bevarar både `$content` nytto lasten och `$content-type` , som automatiskt konverteras. 

I den här listan beskrivs hur Logic Apps konverterar innehåll när du använder dessa [funktioner](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Skickar data till `application/json`
* `xml()`: Skickar data till `application/xml`
* `binary()`: Skickar data till `application/octet-stream`
* `string()`: Skickar data till `text/plain`
* `base64()`: Konverterar innehåll till en Base64-kodad sträng
* `base64toString()`: Konverterar en Base64-kodad sträng till `text/plain`
* `base64toBinary()`: Konverterar en Base64-kodad sträng till `application/octet-stream`
* `dataUri()`: Konverterar en sträng till en data-URI
* `dataUriToBinary()`: Konverterar en data-URI till en binär sträng
* `dataUriToString()`: Konverterar en data-URI till en sträng

Om du till exempel får en HTTP-begäran som `Content-Type` är inställd på `application/xml` , till exempel det här innehållet:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Du kan omvandla det här innehållet med hjälp av `@xml(triggerBody())` uttrycket med- `xml()` och- `triggerBody()` funktionerna och sedan använda det här innehållet senare. Du kan också använda `@xpath(xml(triggerBody()), '/CustomerName')` uttrycket med-och- `xpath()` `xml()` funktionerna. 

## <a name="other-content-types"></a>Andra innehålls typer

Logic Apps fungerar med och stöder andra typer av innehåll, men kan kräva att du hämtar meddelande texten manuellt genom att avkoda `$content` variabeln.

Anta till exempel att din Logic app utlöses av en begäran med `application/x-www-url-formencoded` innehålls typen. För att bevara alla data `$content` har variabeln i begär ande texten en nytto last som är kodad som en Base64-sträng:

`CustomerName=Frank&Address=123+Avenue`

Eftersom begäran inte är oformaterad text eller JSON, lagras begäran i åtgärden enligt följande:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps innehåller inbyggda funktioner för att hantera formulär data, till exempel: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Eller så kan du manuellt komma åt data med hjälp av ett uttryck som till exempel:

`@string(body('formdataAction'))` 

Om du vill att den utgående begäran ska ha samma `application/x-www-url-formencoded` innehålls typs huvud, kan du lägga till begäran i åtgärdens brödtext utan att behöva ange något genom att använda ett uttryck som `@body('formdataAction')` . Den här metoden fungerar dock bara när texten är den enda parametern i `body` indatamängden. Om du försöker använda `@body('formdataAction')` uttrycket i en `application/json` begäran får du ett körnings fel eftersom bröd texten skickas som kodad.
