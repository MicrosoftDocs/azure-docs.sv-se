---
title: Arbeta med enhetsmeddelanden
description: Meddelanden innehåller information om nätverks aktivitet som kräver din uppmärksamhet, tillsammans med rekommendationer för att hantera den här aktiviteten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cac8d609272be1d9f34b7e0d6404e0a0ea524df7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523574"
---
# <a name="work-with-device-notifications"></a>Arbeta med enhetsmeddelanden

Meddelanden innehåller information om nätverks aktivitet som kräver din uppmärksamhet, tillsammans med rekommendationer för att hantera den här aktiviteten. Du kan till exempel få ett meddelande om:

- En inaktiv enhet. Om enheten inte längre är en del av nätverket kan du ta bort den. Om enheten är inaktiv, till exempel eftersom den av misstag inte är frånkopplad från nätverket, måste du ansluta enheten igen och stänga meddelandet.

- En IP-adress har identifierats på en enhet som för närvarande identifieras av en MAC-adress. Svara genom att auktorisera enhetens IP-adress.

Att svara på meddelanden förbättrar informationen i enhets kartan, enhets inventeringen och frågor och rapporter om Data utvinning. Det ger också insikter om legitima nätverks ändringar och potentiella nätverks konfigurationer.

För att få åtkomst till meddelanden:

- Välj **Systeminställningar** och välj sedan **data förbättring**.

## <a name="notifications-vs-alerts"></a>Meddelanden kontra aviseringar

Förutom att ta emot meddelanden om nätverks aktivitet kan du få *aviseringar*. Meddelanden innehåller information om nätverks ändringar eller olösta enhets egenskaper som inte utgör ett hot. Aviseringar ger information om nätverks avvikelser och ändringar som kan utgöra ett hot mot nätverket.

Så här visar du meddelanden:

1. Välj **enhets karta** i konsolens vänstra meny fönster.

2. Välj **meddelande** ikonen. Det röda talet ovanför ikonen visar antalet meddelanden.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Meddelande ikon.":::

   I fönstret **meddelanden** visas alla meddelanden om att sensorn har identifierat.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Meddelanden.":::

## <a name="filter-the-notifications-window"></a>Filtrera fönstret meddelanden

Använd Sök filter för att visa meddelanden som intresserar dig.

| Filtrera efter | Beskrivning |
|--|--|
| Filtrera efter typ | Visa meddelanden som avser ett bestämt intresse områden. Du kan till exempel endast visa meddelanden för inaktiva enheter. |
| Filtrera efter datum intervall | Visa meddelanden som avser ett angivet tidsintervall. Visa till exempel meddelanden som skickats under den senaste veckan. |
| Sök efter detaljerad information | Sök efter vissa meddelanden. |

## <a name="notification-events-and-responses"></a>Meddelande händelser och svar

I följande tabell beskrivs de typer av meddelande händelser som du kan få, tillsammans med alternativen för att hantera dem. Du kan uppdatera enhets informationen med ett Rekommenderat värde eller stänga meddelandet. När du stänger ett meddelande uppdateras inte enhets informationen med den rekommenderade informationen. Om trafiken identifieras igen kommer meddelandet att skickas igen.

| Meddelande händelse typer | Beskrivning | Svar |
|--|--|--|
| Ny IP identifierades | En ny IP-adress är kopplad till enheten. Fem scenarier kan identifieras: <br /><br /> En ytterligare IP-adress var kopplad till en enhet. Den här enheten är också kopplad till en befintlig MAC-adress.<br /><br /> En ny IP-adress har identifierats för en enhet som använder en befintlig MAC-adress. För närvarande kommunicerar enheten inte med hjälp av en IP-adress.<br /> <br /> En ny IP-adress har identifierats för en enhet som använder ett NetBIOS-namn. <br /><br /> En IP-adress identifierades som hanterings gränssnitt för en enhet som är associerad med en MAC-adress. <br /><br /> En ny IP-adress har identifierats för en enhet som använder en virtuell IP-adress. | **Ange ytterligare IP-adress till enhet** (slå samman enheter) <br /> <br />**Ersätt befintlig IP** <br /> <br /> **Stäng**<br /> Ta bort meddelandet. |
| Inaktiva enheter | Trafiken identifierades inte på en enhet i mer än 60 dagar. | **Ta bort** <br /> Om enheten inte är en del av nätverket tar du bort den. <br /><br />**Stäng** <br /> Ta bort meddelandet om enheten ingår i nätverket. Om enheten är inaktiv (till exempel eftersom den av misstag inte är frånkopplad från nätverket), Stäng meddelandet och Återanslut enheten. |
| Nya enheter | Ett undernät innehåller en enhet som inte är definierad i ett ICS-undernät. <br /><br /> Varje undernät som innehåller minst en enhets enhet kan definieras som ett ICS-undernät. Detta hjälper till att skilja mellan olika enheter och IT-enheter på kartan. | **Ange som ICS-undernät** <br /> <br /> **Stäng** <br />Ta bort meddelandet om enheten inte är en del av under nätet. |
| Inga undernät har kon figurer ATS | Inga undernät har kon figurer ATS i nätverket. <br /><br /> Konfigurera undernät för bättre representation i kartan och möjlighet att skilja mellan olika enheter och IT-enheter. | **Öppna undernät-konfiguration** och konfigurera undernät. <br /><br />**Stäng** <br /> Ta bort meddelandet. |
| Ändringar i operativ system | Ett eller flera nya operativ system har associerats med enheten. | Välj namnet på det nya operativ system som du vill associera med enheten.<br /><br /> **Stäng** <br /> Ta bort meddelandet. |
| Nya undernät | Nya undernät identifierades. | **Learn**<br />Lägg automatiskt till under nätet.<br />**Öppna under näts konfiguration**<br />Lägg till all information om undernät som saknas.<br />**Stäng**<br />Ta bort meddelandet. |
| Ändringar av enhets typ | En ny enhets typ har associerats med enheten. | **Ange som {...}**<br />Koppla den nya typen till enheten.<br />**Stäng**<br />Ta bort meddelandet. |

## <a name="respond-to-many-notifications-simultaneously"></a>Svara på många meddelanden samtidigt

Du kan behöva hantera flera meddelanden samtidigt. Exempel:

- Om det gjorde en operativ Systems uppgradering till en stor uppsättning nätverks servrar kan du instruera sensorn att lära sig nya server versioner för alla uppgraderade servrar. 

- Om en grupp av enheter i en viss rad har fasats ut och inte längre är aktiv kan du instruera sensorn att ta bort dessa enheter från-konsolen.

Du kan instruera sensorn att tillämpa nyligen identifierad information på flera enheter eller ignorera den.   

Visa meddelanden och hantera meddelanden:

1. Använd alternativet **Filtrera efter typ, datum intervall** eller alternativet **Välj alla** . Avmarkera aviseringar efter behov.

2. Instruera sensorn att tillämpa nyligen identifierad information på valda enheter genom att välja **Lär dig**. Eller så instruerar du sensorn att ignorera nyligen identifierad information genom att välja **Stäng**. Antalet meddelanden som du samtidigt kan lära dig och stänga, tillsammans med antalet meddelanden som du måste hantera individuellt, visas.

**Nya IP-adresser** och **inga undernät** konfigurerade händelser kan inte hanteras samtidigt. De kräver manuell bekräftelse.

## <a name="improve-device-os-classification-data-enhancement"></a>Förbättra enhetens OS-klassificering: data förbättring 

Sensorn identifierar kontinuerligt nya enheter automatiskt. Den identifierar också ändringar till tidigare identifierade enheter, inklusive operativ system typer.

Under vissa omständigheter kan konflikter identifieras i identifierade operativ system. Detta kan inträffa eftersom du har en OS-version som refererar till antingen Skriv bords-eller server system. Om det inträffar får du ett meddelande med valfria system klassificeringar.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Förbättra data.":::

Undersök rekommendationerna för att utöka OS-klassificeringen. Den här informationen visas i enhets inventeringen, rapporter om Data utvinning och andra skärmar. Det kan också förbättra precisionen för aviseringar, hot och risk analyser.

När du godkänner en rekommendation kommer operativ systemets typ information att uppdateras i sensorn.

## <a name="see-also"></a>Se även

[Visa aviseringar](how-to-view-alerts.md)
