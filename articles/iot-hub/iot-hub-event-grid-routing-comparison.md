---
title: Jämför Event Grid, routning för IoT Hub | Microsoft Docs
description: IoT Hub erbjuder sin egen routing-tjänst för meddelanden, men integrerar även med Event Grid för händelse publicering. Jämför de två funktionerna.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom: fasttrack-edit
ms.openlocfilehash: 37b411792ea1a3e21e0f26df0c7905eb8d46310e
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897713"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Jämför meddelanderoutning och Event Grid för IoT Hub

Azure IoT Hub ger möjlighet att strömma data från dina anslutna enheter och integrera dessa data i dina företags program. IoT Hub erbjuder två metoder för att integrera IoT-händelser i andra Azure-tjänster eller affärs program. Den här artikeln beskriver de två funktioner som tillhandahåller den här funktionen, så att du kan välja vilket alternativ som passar bäst för ditt scenario.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub meddelanderoutning](iot-hub-devguide-messages-d2c.md)**: den här IoT Hub funktionen gör det möjligt för användare att dirigera meddelanden från enheten till molnet till tjänstens slut punkter som Azure Storage behållare, Event Hubs, Service Bus köer och Service Bus ämnen. Routning tillhandahåller också en fråge funktion för att filtrera data innan de dirigeras till slut punkterna. Förutom telemetri-data kan du även skicka [icke-telemetri-händelser](iot-hub-devguide-messages-d2c.md#non-telemetry-events) som kan användas för att utlösa åtgärder. 

**IoT Hub integration med event Grid**: Azure Event Grid är en helt hanterad tjänst för händelse dirigering som använder en publicerings prenumerations modell. IoT Hub och Event Grid samar beta för att [integrera IoT Hub-händelser i Azure och icke-Azure-tjänster](iot-hub-event-grid.md), i nära real tid. IoT Hub publicerar både [enhets händelser](iot-hub-event-grid.md#event-types) och telemetri händelser.

## <a name="differences"></a>Skillnader

Både meddelanderoutning och Event Grid aktivera aviserings konfiguration, finns det några viktiga skillnader mellan de två. Mer information finns i följande tabell:

| Funktion | IoT Hub meddelanderoutning | IoT Hub integration med Event Grid |
| ------- | --------------- | ---------- |
| **Enhets meddelanden och händelser** | Ja, meddelanderoutning kan användas för telemetridata, enhets dubbla ändringar, livs cykel händelser för enheter och digitala dubbla ändrings händelser. | Ja, Event Grid kan användas för telemetridata och enhets händelser som enhet skapad/Deleted/ansluten/frånkopplad. Det går inte att använda Event Grid för enhets dubbla ändrings händelser och digitala dubbla ändrings händelser. |
| **Ordna profiler** | Ja, beställningen av händelser upprätthålls.  | Nej, ordningen på händelser är inte garanterad. | 
| **Filtrering** | Omfattande filtrering av egenskaper för meddelande program, meddelande system egenskaper, meddelande text, enhetens dubbla Taggar och enhetens dubbla egenskaper. Filtrering tillämpas inte på digitala dubbla ändrings händelser. Exempel finns i syntax för meddelanderoutning för [meddelanden](iot-hub-devguide-routing-query-syntax.md). | Filtrering baserat på händelse typ, ämnes typ och attribut i varje händelse. Exempel finns i [förstå filtrerings händelser i Event Grid prenumerationer](../event-grid/event-filtering.md). När du prenumererar på telemetri-händelser kan du använda ytterligare filter för data för att filtrera på meddelande egenskaper, meddelande text och enhet i IoT Hub, innan du publicerar till Event Grid. Se [Filtrera händelser](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Slutpunkter** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Service Bus-kö</li> <li>Service Bus-avsnitt</li></ul><br>Betalda IoT Hub SKU: er (S1, S2 och S3) är begränsade till 10 anpassade slut punkter. 100 vägar kan skapas per IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Anpassade ämnen</li> <li>Queue Storage</li> <li>Power Automate</li> <li>Tjänster från tredje part via Webhooks</li></ul><br>500 slut punkter per IoT Hub stöds. Den senaste listan över slut punkter finns i [Event Grid händelse hanterare](../event-grid/overview.md#event-handlers). |
| **Kostnad** | Det finns ingen separat avgift för meddelanderoutning. Endast ingress av telemetri till IoT Hub debiteras. Om du till exempel har ett meddelande dirigerat till tre olika slut punkter debiteras du bara för ett meddelande. | Det kostar inget att IoT Hub. Event Grid erbjuder de första 100 000 åtgärderna per månad kostnads fritt och sedan $0,60 per miljon åtgärder efteråt. |

## <a name="similarities"></a>Likheter

IoT Hub meddelanderoutning och Event Grid har liknande likheter, varav vissa beskrivs i följande tabell:

| Funktion | IoT Hub meddelanderoutning | IoT Hub integration med Event Grid |
| ------- | --------------- | ---------- |
| **Maximal meddelande storlek** | 256 KB, enhet till moln | 256 KB, enhet till moln |
| **Tillförlitlighet** | Hög: levererar varje meddelande till slut punkten minst en gång för varje väg. Förfaller alla meddelanden som inte har levererats inom en timme. | Hög: levererar varje meddelande till webhooken minst en gång för varje prenumeration. Förfaller alla händelser som inte levereras inom 24 timmar. | 
| **Skalbarhet** | Hög: optimerad för att stödja miljon tals samtidigt anslutna enheter som skickar miljarder meddelanden. | Hög: möjlighet att dirigera 10 000 000 händelser per sekund per region. |
| **Svarstid** | Låg: nära real tid. | Låg: nära real tid. |
| **Skicka till flera slut punkter** | Ja, skicka ett enda meddelande till flera slut punkter. | Ja, skicka ett enda meddelande till flera slut punkter.  
| **Säkerhet** | IoT Hub ger identitets-och revocable åtkomst kontroll per enhet. Mer information finns i [IoT Hub åtkomst kontroll](iot-hub-devguide-security.md). | Event Grid tillhandahåller verifiering vid tre punkter: händelse prenumerationer, händelse publicering och leverans av webhook-händelser. Mer information finns i [Event Grid säkerhet och autentisering](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Så här väljer du

IoT Hub meddelanderoutning och IoT Hub-integration med Event Grid utföra olika åtgärder för att uppnå liknande resultat. Båda tar med information från din IoT Hub-lösning och skickar den så att andra tjänster kan reagera. Hur vill du bestämma vilken av dem som ska användas? Tänk på följande frågor för att hjälpa dig med ditt beslut: 

* **Vilken typ av data skickar du till slut punkterna?**

   Använd IoT Hub meddelanderoutning när du måste skicka telemetridata till andra tjänster. Med meddelanderoutning kan du också skicka frågor till meddelande program och system egenskaper, meddelande text, enhets dubbla Taggar och enhetens dubbla egenskaper.

   IoT Hub-integrering med Event Grid fungerar med händelser som inträffar i IoT Hubs tjänsten. Dessa IoT Hub händelser inkluderar telemetridata, enheten har skapats, tagits bort, anslutits och kopplats från. När du prenumererar på telemetri-händelser kan du använda ytterligare filter för data för att filtrera på meddelande egenskaper, meddelande text och enhet i IoT Hub, innan du publicerar till Event Grid. Se [Filtrera händelser](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Vilka slut punkter behöver få den här informationen?**

   IoT Hub meddelanderoutning stöder ett begränsat antal unika slut punkter och slut punkts typer, men du kan bygga kopplingar för att dirigera om data och händelser till ytterligare slut punkter. En fullständig lista över slut punkter som stöds finns i tabellen i föregående avsnitt. 

   IoT Hub-integrering med Event Grid stöder 500-slutpunkter per IoT Hub och en större mängd olika slut punkts typer. Den integreras internt med Azure Functions, Logic Apps, lagrings-och Service Bus köer och fungerar också med Webhooks för att utöka sändningen av data utanför Azure-tjänstens eko system och företags program från tredje part.

* **Spelar det någon roll om dina data kommer i ordning?**

   IoT Hub meddelanderoutning är den ordning i vilken meddelanden skickas, så att de tas emot på samma sätt.

   Event Grid garanterar inte att slut punkterna tar emot händelser i samma ordning som de har inträffat. I de fall där den absoluta ordningen av meddelanden är betydande och/eller där en konsument behöver en tillförlitlig unik identifierare för meddelanden, rekommenderar vi att du använder meddelanderoutning. 

## <a name="next-steps"></a>Nästa steg

* Läs mer om [IoT Hub](iot-hub-devguide-messages-d2c.md) meddelanderoutning och [IoT Hub slut punkter](iot-hub-devguide-endpoints.md).
* Läs mer om [Azure Event Grid](../event-grid/overview.md).
* Information om hur du skapar meddelande vägar finns i avsnittet [Process IoT Hub enhet-till-moln-meddelanden med hjälp av väg](../iot-hub/tutorial-routing.md) guiden.
* Testa Event Grid-integrering genom att [skicka e-postaviseringar om Azure IoT Hub-händelser med hjälp av Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).
