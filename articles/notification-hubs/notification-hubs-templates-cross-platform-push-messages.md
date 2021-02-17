---
title: Azure Notification Hubs-mallar
description: Lär dig mer om att använda mallar för Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c4c1c247d8fb248c5e6d548dd04af1c3d08a4e76
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588328"
---
# <a name="notification-hubs-templates"></a>Notification Hubs mallar

Mallar gör det möjligt för ett klient program att ange det exakta formatet för de meddelanden som ska tas emot. Med hjälp av mallar kan en app inse flera olika fördelar, inklusive följande:

- En plattforms oberoende oberoende Server del
- Anpassade meddelanden
- Klient versions oberoende
- Enkel lokalisering

Det här avsnittet innehåller två djupgående exempel på hur du använder mallar för att skicka oberoende meddelanden som riktar sig mot alla dina enheter på olika plattformar och för att anpassa sändnings aviseringen till varje enhet.

## <a name="using-templates-cross-platform"></a>Använda mallar mellan plattformar

Standard sättet att skicka push-meddelanden är att skicka för varje meddelande som ska skickas en speciell nytto Last till Platform Notification Services (WNS, APN). Om du till exempel vill skicka en avisering till APN är nytto lasten ett JSON-objekt av följande format:

```json
{"aps": {"alert" : "Hello!" }}
```

För att skicka ett liknande popup-meddelande i ett Windows Store-program är XML-nyttolasten följande:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Du kan skapa liknande nytto laster för MPNS-(Windows Phone) och FCM-plattformar (Android).

Det här kravet tvingar appens Server del att producera olika nytto laster för varje plattform och gör det effektivt för Server delen som ansvarar för en del av presentations lagret i appen. Vissa problem innefattar lokalisering och grafiska layouter (särskilt för Windows Store-appar som innehåller aviseringar för olika typer av paneler).

Med funktionen Notification Hubs mall kan du skapa särskilda registreringar, till exempel mall registreringar, som inkluderar, förutom uppsättningen taggar, en mall. Med funktionen Notification Hubs mall kan en klient app associera enheter med mallar oavsett om du arbetar med installationer (standard) eller registreringar. Med föregående nytto Last exempel är den enda plattforms oberoende informationen det faktiska varnings meddelandet (Hej!). En mall är en uppsättning instruktioner för Notification Hub om hur du formaterar ett plattforms oberoende meddelande för registreringen av den aktuella klient appen. I föregående exempel är det plattforms oberoende meddelandet en enda egenskap: `message = Hello!` .

Följande bild illustrerar processen:

![Diagram som visar processen för att använda mallar mellan plattformar](./media/notification-hubs-templates/notification-hubs-hello.png)

Mallen för iOS client app-registreringen är följande:

```json
{"aps": {"alert": "$(message)"}}
```

Motsvarande mall för Windows Store-klientens app är:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Observera att det faktiska meddelandet ersätts med uttrycket $ (Message). Det här uttrycket instruerar Notification Hub när det skickar ett meddelande till den här specifika registreringen för att bygga ett meddelande som följer och växlar i det gemensamma värdet.

Om du arbetar med installations modellen, innehåller installations "mallar"-nyckeln en JSON-uppsättning med flera mallar. Om du arbetar med registrerings modellen kan klient programmet skapa flera registreringar för att använda flera mallar. till exempel en mall för aviserings meddelanden och en mall för panel uppdateringar. Klient program kan också blanda interna registreringar (registreringar utan mall) och mall-registreringar.

Notification Hub skickar ett meddelande för varje mall utan att fundera över om de tillhör samma klient program. Det här beteendet kan användas för att översätta plattforms oberoende meddelanden till fler meddelanden. Samma plattforms oberoende meddelande till Notification Hub kan till exempel enkelt översättas i en popup-avisering och en uppdatering av en panel, utan att Server delen måste vara medveten om den. Vissa plattformar (till exempel iOS) kan komprimera flera meddelanden till samma enhet om de skickas under en kort tids period.

## <a name="using-templates-for-personalization"></a>Använda mallar för anpassning

En annan fördel med att använda mallar är möjligheten att använda Notification Hubs för att utföra anpassningar av meddelanden per registrering. Överväg till exempel en väder-app som visar en panel med väder förhållanden på en speciell plats. En användare kan välja mellan Celsius eller Fahrenheit grader, och en enda eller fem dagars prognos. Med hjälp av mallar kan varje klient programs installation registreras för det format som krävs (1 – dag Celsius, 1 dag Fahrenheit, 5 dagar Celsius, 5 dagar Fahrenheit) och har Server delen skicka ett enda meddelande som innehåller all information som krävs för att fylla i mallarna (till exempel en prognos på fem dagar med Celsius och Fahrenheit grader).

Mallen för en dags prognos med Celsius-temperaturer är följande:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Meddelandet som skickas till Notification Hub innehåller alla följande egenskaper:

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

Genom att använda det här mönstret skickar Server delen bara ett enda meddelande utan att behöva lagra vissa anpassnings alternativ för användarna i appen. Följande bild illustrerar det här scenariot:

![Diagram som visar hur Server delen bara skickar ett enda meddelande till varje plattform.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Så här registrerar du mallar

Om du vill registrera dig för mallar med hjälp av installations modellen (önskad) eller registrerings modellen, se [registrerings hantering](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Språk för mall uttryck

Mallar är begränsade till XML-eller JSON-dokument format. Du kan också bara placera uttryck på specifika platser. till exempel Node-attribut eller värden för XML, sträng egenskaps värden för JSON.

Följande tabell visar det språk som tillåts i mallar:

| Uttryck       | Beskrivning |
| ---------------- | --- |
| $ (prop)          | Referens till en händelse egenskap med angivet namn. Egenskaps namn är inte Skift läges känsliga. Det här uttrycket matchar egenskapens text värde eller till en tom sträng om egenskapen inte finns. |
| $ (prop, n)       | Som ovan, men texten är explicit beskuren med n tecken, till exempel $ (rubrik, 20) klipp innehåll i egenskapen title med 20 tecken. |
| . (prop, n)       | Som ovan, men texten suffixs med tre punkter när den klipps ut. Den totala storleken på den urklippta strängen och suffixet innehåller inte fler än n tecken. . (rubrik, 20) med inmatad egenskap "det här är rubrik raden" resulterar i **Detta är rubriken...** |
| % (prop)          | Liknar $ (Name) förutom att utdata är URI-kodad. |
| # (prop)          | Används i JSON-mallar (till exempel för iOS-och Android-mallar).<br><br>Den här funktionen fungerar exakt på samma sätt som $ (prop) som tidigare angavs, förutom när den används i JSON-mallar (till exempel Apple-mallar). I det här fallet, om den här funktionen inte omges av "{", "}" (till exempel "myJsonProperty": "# (Name)") och den utvärderas till ett tal i JavaScript-format, t. ex. regexp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, är utdata-JSON ett tal.<br><br>Till exempel "Badge:" # (namn) "blir" BADGE ": 40 (och inte" 40 "). |
| ' text ' eller ' text ' | En literal. Litteraler innehåller godtycklig text omgiven av enkla eller dubbla citat tecken. |
| Uttr1 + Expr2    | Sammanfognings operatorn kopplar ihop två uttryck i en enda sträng. |

Uttrycken kan vara något av de föregående formulären.

När du använder sammanfogning måste hela uttrycket omges av `{}` . Till exempel `{$(prop) + ‘ - ’ + $(prop2)}`.

Följande mall är till exempel inte en giltig XML-mall:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Som förklaras tidigare, när du använder sammanfogning, måste uttryck omges av klammerparenteser. Exempel:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Azure Notification Hubs](notification-hubs-push-notification-overview.md)
