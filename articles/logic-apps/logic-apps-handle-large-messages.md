---
title: Hantera stora meddelanden med hjälp av segment
description: Lär dig hur du hanterar stora meddelande storlekar med hjälp av segment i automatiserade uppgifter och arbets flöden som du skapar med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: DavidCBerry13
ms.author: daberry
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 1b23c92ec70b80a6cd08fc42a05ffec1e5b43b31
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656775"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Hantera stora meddelanden med segment i Azure Logic Apps

Logic Apps begränsar meddelande innehållet till en maximal storlek vid hantering av meddelanden. Den här gränsen bidrar till att minska de kostnader som skapas genom att lagra och bearbeta stora meddelanden. Om du vill hantera meddelanden som är större än den här gränsen kan Logic Apps *segmentera* ett stort meddelande i mindre meddelanden. På så sätt kan du fortfarande överföra stora filer med Logic Apps under vissa förhållanden. Vid kommunikation med andra tjänster via kopplingar eller HTTP kan Logic Apps använda stora meddelanden men *bara* i segment. Det här villkoret innebär att anslutningar måste också ha stöd för segment, eller det underliggande HTTP-meddelande utbytet mellan Logic Apps och dessa tjänster måste använda segment.

Den här artikeln visar hur du kan konfigurera segment för åtgärder som hanterar meddelanden som är större än gränsen. Logic app-utlösare stöder inte segment hantering på grund av den ökade omkostnaderna för att utväxla flera meddelanden. 

## <a name="what-makes-messages-large"></a>Vad gör meddelanden "stora"?

Meddelanden är "stora" baserat på tjänsten som hanterar dessa meddelanden. Den exakta storleks begränsningen för stora meddelanden skiljer sig åt mellan Logic Apps och anslutningar. Både Logic Apps och kopplingar kan inte direkt använda stora meddelanden, som måste vara segmenterade. För storleks gränsen för Logic Apps meddelande, se [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md).
För varje kopplings gräns för meddelande storlek, se [kopplingens aktuella tekniska information](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Segment hantering av meddelanden för Logic Apps

Logic Apps kan inte direkt använda utdata från segmenterade meddelanden som är större än gränsen för meddelande storlek. Endast åtgärder som stöder segment åtkomst kan komma åt meddelande innehållet i dessa utdata. Det innebär att en åtgärd som hanterar stora meddelanden måste uppfylla *antingen* följande kriterier:

* Inbyggt stöd för segmentering när åtgärden tillhör en koppling. 
* Har segment stöd aktiverat i den åtgärdens körnings konfiguration. 

Annars får du ett körnings fel när du försöker komma åt stora innehålls utdata. Information om hur du aktiverar segment finns i [Konfigurera segment stöd](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Segment hantering av meddelanden för anslutningar

Tjänster som kommunicerar med Logic Apps kan ha egna storleks gränser för meddelanden. Dessa gränser är ofta mindre än Logic Apps gränsen. Om du till exempel antar att en koppling har stöd för segment kan en koppling ta ett meddelande om 30 MB som stor, medan Logic Apps inte. För att uppfylla den här kopplingens gräns kan Logic Apps dela upp ett meddelande som är större än 30 MB i mindre segment.

För kopplingar som stöder segment, är det underliggande segment protokollet osynligt för slutanvändare. Men alla kopplingar stöder inte segment koppling, så dessa kopplingar genererar körnings fel när inkommande meddelanden överskrider kopplingens storleks gränser.

> [!NOTE]
> För åtgärder som använder segment kan du inte skicka utlösaren eller använda uttryck som `@triggerBody()?['Content']` i dessa åtgärder. I stället kan du prova att använda [åtgärden **Skriv**](../logic-apps/logic-apps-perform-data-operations.md#compose-action) eller [skapa en variabel](../logic-apps/logic-apps-create-variables-store-values.md) för att hantera innehållet i text-eller JSON-filinnehållet. Om utlösaren innehåller andra innehålls typer, till exempel mediefiler, måste du utföra andra åtgärder för att hantera det innehållet.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Konfigurera segment över HTTP

I allmänna HTTP-scenarier kan du dela upp stora innehålls hämtningar och överföringar över HTTP, så att din Logic app och en slut punkt kan utbyta stora meddelanden. Du måste dock segmentera meddelanden på det sätt som Logic Apps förväntar sig. 

Om en slut punkt har aktiverat segment för nedladdningar eller uppladdningar, segmenteras HTTP-åtgärder i din Logic-app automatiskt stora meddelanden. Annars måste du konfigurera segment stöd för slut punkten. Om du inte äger eller styr slut punkten eller kopplingen kanske du inte har möjlighet att konfigurera segment.

Om en HTTP-åtgärd inte redan aktiverar segment, måste du också konfigurera segment i åtgärdens `runTimeConfiguration` egenskap. Du kan ställa in den här egenskapen i åtgärden, antingen direkt i kodvyn enligt beskrivningen senare eller i Logic Apps designer som beskrivs här:

1. I http-åtgärdens övre högra hörn väljer du knappen med tre punkter (**...**) och väljer sedan **Inställningar**.

   ![Öppna menyn Inställningar på åtgärden.](./media/logic-apps-handle-large-messages/http-settings.png)

2. Under **innehålls överföring** anger du **Tillåt segment** till **på**.

   ![Aktivera segment](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Fortsätt med följande avsnitt om du vill fortsätta att konfigurera segment för hämtningar eller uppladdningar.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Hämta innehåll i segment

Många slut punkter skickar automatiskt stora meddelanden i segment när de hämtas via en HTTP GET-begäran. Om du vill hämta segmenterade meddelanden från en slut punkt över HTTP måste slut punkten ha stöd för partiella innehålls begär Anden eller *segmenterade hämtningar*. När din Logi Kap par skickar en HTTP GET-begäran till en slut punkt för nedladdning av innehåll, och slut punkten svarar med status koden "206", innehåller svaret segment innehåll. Logic Apps kan inte kontrol lera om en slut punkt stöder partiella begär Anden. Men när din Logic-app får det första "206"-svaret skickar din Logic-app automatiskt flera begär Anden för att ladda ned allt innehåll.

Om du vill kontrol lera om en slut punkt har stöd för partiellt innehåll skickar du en HEAD-begäran. Den här begäran hjälper dig att avgöra om svaret innehåller `Accept-Ranges` rubriken. På så sätt kan du *föreslå* det här alternativet genom att ange `Range` rubriken i HTTP GET-begäran om slut punkten stöder segment hämtningar men inte skickar segmenterat innehåll. 

De här stegen beskriver den detaljerade processen Logic Apps använder för att ladda ned segmenterat innehåll från en slut punkt till din Logic app:

1. Din Logic-App skickar en HTTP GET-begäran till slut punkten.

   Rubriken för begäran kan alternativt innehålla ett `Range` fält som beskriver ett byte-intervall för att begära innehålls segment.

2. Slut punkten svarar med status koden "206" och HTTP-meddelandets text.

    Information om innehållet i det här segmentet visas i svarets `Content-Range` rubrik, inklusive information som hjälper Logic Apps att fastställa början och slutet för segmentet, plus den totala storleken på hela innehållet innan segmentning.

3. Din Logic-App skickar automatiskt uppföljning av HTTP GET-begäranden.

    Din Logic App skickar uppföljnings begär anden tills hela innehållet hämtas.

Denna åtgärds definition visar till exempel en HTTP GET-begäran som anger `Range` sidhuvudet. Rubriken *föreslår* att slut punkten ska svara med segmenterat innehåll:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

GET-begäran anger "Range"-rubriken till "byte = 0-1023", vilket är det antal byte. Om slut punkten stöder begär Anden om partiellt innehåll, svarar slut punkten med ett innehålls segment från det begärda intervallet. Baserat på slut punkten kan det exakta formatet för rubrik fältet "Range" variera.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Ladda upp innehåll i segment

Om du vill överföra segment innehåll från en HTTP-åtgärd måste åtgärden ha aktiverat segment stöd via åtgärdens `runtimeConfiguration` egenskap. Den här inställningen tillåter åtgärden att starta segment protokollet. Din Logi Kap par kan sedan skicka ett första POST-eller skicka-meddelande till mål slut punkten. När slut punkten svarar med en föreslagen segment storlek följer din Logic app upp genom att skicka HTTP-begäranden som innehåller innehålls segmenten.

De här stegen beskriver den detaljerade processen Logic Apps använder för att ladda upp segmenterat innehåll från din Logic app till en slut punkt:

1. Din Logi Kap par skickar en initial HTTP POST eller en skicka begäran med en tom meddelande text. Begär ande huvudet innehåller den här informationen om det innehåll som din Logic app vill överföra i segment:

   | Fält för Logic Apps begär ande huvud | Värde | Typ | Beskrivning |
   |---------------------------------|-------|------|-------------|
   | **x-MS-Transfer-Mode** | segment vis | Sträng | Anger att innehållet har laddats upp i segment |
   | **x-MS-Content-Length** | <*innehålls längd*> | Integer | Hela innehålls storleken i byte innan segmentning |
   ||||

2. Slut punkten svarar med status koden 200 och denna valfria information:

   | Rubrik fält för slut punkts svar | Typ | Obligatorisk | Beskrivning |
   |--------------------------------|------|----------|-------------|
   | **x-MS-segment-storlek** | Integer | Nej | Den föreslagna segment storleken i byte |
   | **Plats** | Sträng | Yes | Den URL-plats dit meddelanden om HTTP-KORRIGERINGarna ska skickas |
   ||||

3. Din Logi Kap par skapar och skickar uppföljning av HTTP-meddelanden – var och en med den här informationen:

   * Ett innehålls segment baserat på **x-MS-segment-storlek** eller viss internt Beräknad storlek tills alla innehålls summor **x-MS-Content-Length** överförs sekventiellt

   * Den här rubrik informationen om innehålls segmentet som skickas i varje KORRIGERINGs meddelande:

     | Fält för Logic Apps begär ande huvud | Värde | Typ | Beskrivning |
     |---------------------------------|-------|------|-------------|
     | **Innehålls intervall** | <*intervall*> | Sträng | Byte-intervallet för det aktuella innehålls segmentet, inklusive startvärdet, slut värde och total innehålls storlek, till exempel: "byte = 0-1023/10100" |
     | **Innehålls typ** | <*innehålls typ*> | Sträng | Typ av segmenterat innehåll |
     | **Innehålls längd** | <*innehålls längd*> | Sträng | Längden på storleken i byte för det aktuella segmentet |
     |||||

4. Efter varje PATCH-begäran bekräftar slut punkten kvittot för varje segment genom att svara med status koden "200" och följande svarshuvuden:

   | Rubrik fält för slut punkts svar | Typ | Obligatorisk | Beskrivning |
   |--------------------------------|------|----------|-------------|
   | **Intervall** | Sträng | Yes | Byte-intervallet för innehåll som har tagits emot av slut punkten, till exempel: "byte = 0-1023" |   
   | **x-MS-segment-storlek** | Integer | Nej | Den föreslagna segment storleken i byte |
   ||||

Denna åtgärds definition visar till exempel en HTTP POST-begäran om att överföra segment innehåll till en slut punkt. I åtgärdens `runTimeConfiguration` egenskap `contentTransfer` anges egenskapen `transferMode` till `chunked` :

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
