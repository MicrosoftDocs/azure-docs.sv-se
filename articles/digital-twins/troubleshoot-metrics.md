---
title: Visa mått med Azure Monitor
titleSuffix: Azure Digital Twins
description: Se hur du visar Azure Digitals dubbla mått i Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63817c8a65678579ce535a3c2e667e4eb0971a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434074"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Felsöka Azure Digitals dubbla: mått

Måtten som beskrivs i den här artikeln ger dig information om statusen för Azure Digitals dubbla resurser i din Azure-prenumeration. Azure Digitals dubbla mått hjälper dig att utvärdera den övergripande hälso statusen för Azure Digital-tjänster och de resurser som är anslutna till den. Den här användaren får hjälp att se vad som händer med Azures digitala dubbla, och hjälper till med rotor Saks analys av problem utan att behöva kontakta Azure-supporten.

Mått är aktiverat som standard. Du kan visa Azure Digitals dubbla mått från [Azure Portal](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Så här visar du Azure Digitals dubbla mått

1. Skapa en digital Azure-instans. Du hittar anvisningar om hur du konfigurerar en digital Azure-instans i [*anvisningar: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md).

2. Hitta din Azure Digital-instansen i [Azure Portal](https://portal.azure.com) (du kan öppna sidan genom att skriva dess namn i portalens Sök fält). 

    Från instansens meny väljer du **mått**.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Skärm bild som visar mått sidan för Azure Digitals dubbla":::

    Den här sidan visar måtten för din Azure Digital-instansen. Du kan också skapa anpassade vyer av dina mått genom att välja de som du vill se i listan.
    
3. Du kan välja att skicka Mät data till en Event Hubs-slutpunkt eller ett Azure Storage konto genom att välja **diagnostikinställningar** på menyn och sedan **lägga till diagnostikinställningar**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

    Mer information om den här processen finns i [*fel sökning: Konfigurera diagnostik*](troubleshoot-diagnostics.md).

4. Du kan välja att konfigurera aviseringar för dina mått data genom att välja **aviseringar** på menyn och sedan **+ ny varnings regel**.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Skärm bild som visar sidan aviseringar och knapp att lägga till":::

    Mer information om den här processen finns i [*fel sökning: Konfigurera aviseringar*](troubleshoot-alerts.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure Digital har mått och hur man använder dem

Azure Digitals dubbla innehåller flera mått för att ge dig en översikt över hälso tillståndet för din instans och dess tillhör ande resurser. Du kan också kombinera information från flera mått för att måla en större bild av instansens tillstånd. 

I följande tabeller beskrivs de mått som spåras av varje Azure Digital-instansen och hur varje mått relaterar till den övergripande statusen för din instans.

#### <a name="metrics-for-tracking-service-limits"></a>Mått för spårning av tjänst begränsningar

Du kan konfigurera dessa mått för att spåra när du närmar dig en [publicerad tjänst gräns](reference-service-limits.md#functional-limits) för någon aspekt av din lösning. 

Använd [aviserings](troubleshoot-alerts.md) funktionen i Azure Monitor om du vill konfigurera detta. Du kan definiera tröskelvärden för dessa mått så att du får en avisering när ett mått når en viss procent andel av den publicerade gränsen.

| Metric | Mått visnings namn | Enhet | Sammansättningstyp| Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| TwinCount | Antal dubbla (för hands version) | Antal | Totalt | Totalt antal dubbla i Azure Digitals-instansen. Använd det här måttet för att avgöra om du närmar dig [tjänst gränsen](reference-service-limits.md#functional-limits) för högsta antal tillåtna gränser per instans. |  Inget |
| ModelCount | Modell antal (för hands version) | Antal | Totalt | Totalt antal modeller i Azure Digitals dubbla instansen. Använd det här måttet för att avgöra om du närmar dig [tjänst gränsen](reference-service-limits.md#functional-limits) för högsta antal modeller som tillåts per instans. | Inget |

#### <a name="api-request-metrics"></a>API-begärans mått

Mät värden med API-begär Anden:

| Metric | Mått visnings namn | Enhet | Sammansättningstyp| Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API-begäranden | Antal | Totalt | Antalet API-begäranden som gjorts för digitala dubbla, Läs-, Skriv-, borttagnings-och fråge åtgärder. |  Anspråksautentisering <br>Reparation <br>Protokollhanterare <br>Status kod, <br>Status kod klass, <br>Status text |
| ApiRequestsFailureRate | Frekvens för misslyckade API-begäranden | Procent | Genomsnitt | Procent andelen API-begäranden som tjänsten tar emot för din instans och som ger ett internt fel (500) svars kod för digitala dubbla Läs-, Skriv-, borttagnings-och fråge åtgärder. | Anspråksautentisering <br>Reparation <br>Protokollhanterare <br>Status kod, <br>Status kod klass, <br>Status text
| ApiRequestsLatency | Svars tid för API-begäranden | Millisekunder | Genomsnitt | Svars tid för API-begäranden. Detta avser tiden från det att begäran tas emot av Azure Digitals, ända tills tjänsten skickar ett lyckat/misslyckat resultat för digitala dubbla Läs-, Skriv-, borttagnings-och fråge åtgärder. | Anspråksautentisering <br>Reparation <br>Protokoll |

#### <a name="billing-metrics"></a>Fakturerings mått

Mått som måste utföras med fakturering:

| Metric | Mått visnings namn | Enhet | Sammansättningstyp| Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Fakturerings-API-åtgärder | Antal | Totalt | Fakturerings mått för antalet API-begäranden som gjorts mot tjänsten Azure Digitals dubbla. | Meter ID |
| BillingMessagesProcessed | Behandlade fakturerings meddelanden | Antal | Totalt | Fakturerings mått för antalet meddelanden som skickas ut från Azure Digitals dubbla till externa slut punkter.<br><br>För att kunna betraktas som ett enda meddelande i fakturerings syfte får en nytto last inte vara större än 1 KB. Nytto laster som är större än detta räknas som ytterligare meddelanden i steg om 1 KB (så att ett meddelande mellan 1 och 2 KB räknas som två meddelanden, mellan 2 och 3 KB är 3 meddelanden och så vidare).<br>Den här begränsningen gäller även för svar, så ett anrop som returnerar 1,5 KB i svars texten faktureras till exempel som två åtgärder. | Meter ID |
| BillingQueryUnits | Fakturerings frågans enheter | Antal | Totalt | Antalet fråge enheter, internt beräknade mått på användning av tjänst resurser som används för att köra frågor. Det finns också ett hjälp-API som är tillgängligt för att mäta fråge enheter: [QueryChargeHelper-klass](/dotnet/api/azure.digitaltwins.core.querychargehelper) | Meter ID |

Mer information om hur Azure Digital-erbjudanden faktureras finns i [*priser för Azure Digitals dubbla priser*](https://azure.microsoft.com/pricing/details/digital-twins/).

#### <a name="ingress-metrics"></a>Ingresss mått

Mått som måste utföras med data ingress:

| Metric | Mått visnings namn | Enhet | Sammansättningstyp| Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Ingress-händelser | Antal | Totalt | Antalet inkommande telemetri-händelser till Azure Digitals dubbla. | Resultat |
| IngressEventsFailureRate | Händelse frekvens vid inkommande händelser | Procent | Genomsnitt | Procent andelen inkommande telemetri för vilka tjänsten returnerar ett internt fel (500) svars kod. | Resultat |
| IngressEventsLatency | Ingress händelse fördröjning | Millisekunder | Genomsnitt | Tiden från när en händelse kommer till när den är redo att tas ut av Azure Digitals, då tjänsten skickar ett lyckat/misslyckat resultat. | Resultat |

#### <a name="routing-metrics"></a>Routnings mått

Mått som måste utföras med Routning:

| Metric | Mått visnings namn | Enhet | Sammansättningstyp| Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Vidarebefordrade meddelanden | Antal | Totalt | Antalet meddelanden som dirigerats till en slut punkt i Azure-tjänsten som Händelsehubben, Service Bus eller Event Grid. | Slut punkts typ, <br>Resultat |
| RoutingFailureRate | Antal misslyckade vägar | Procent | Genomsnitt | Procent andelen händelser som resulterar i ett fel när de dirigeras från Azure Digitals dubbla till en slut punkt för Azure-tjänster som Händelsehubben, Service Bus eller Event Grid. | Slut punkts typ, <br>Resultat |
| RoutingLatency | Svars tid för routning | Millisekunder | Genomsnitt | Tiden som förflutit mellan en händelse som skickas från Azure Digitals till när den ansluts till Azure-tjänsten för slut punkter som Händelsehubben, Service Bus eller Event Grid. | Slut punkts typ, <br>Resultat |

## <a name="dimensions"></a>Dimensioner

Dimensionerna hjälper till att identifiera mer information om måtten. Några av måtten för routning tillhandahåller information per slut punkt. I tabellen nedan visas möjliga värden för dessa dimensioner.

| Dimension | Värden |
| --- | --- |
| Autentisering | Arbeta |
| Åtgärd (för API-begäranden) | Microsoft. DigitalTwins/DigitalTwins/Delete, <br>Microsoft. DigitalTwins/DigitalTwins/Write, <br>Microsoft. DigitalTwins/DigitalTwins/Read, <br>Microsoft. DigitalTwins/eventroutes/Read, <br>Microsoft. DigitalTwins/eventroutes/Write, <br>Microsoft. DigitalTwins/eventroutes/Delete, <br>Microsoft. DigitalTwins/modeller/läsa, <br>Microsoft. DigitalTwins/Models/Write, <br>Microsoft. DigitalTwins/Models/Delete, <br>Microsoft. DigitalTwins/fråga/åtgärd |
| Typ av slutpunkt | Event Grid <br>Händelsehubben, <br>Service Bus |
| Protokoll | HTTPS |
| Resultat | Resultatet <br>Fel |
| Statuskod | 200, 404, 500 och så vidare. |
| Status kod klass | 2xx, 4xx, 5xx och så vidare. |
| Status text | Internt Server fel, hittades inte och så vidare. |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar inspelade mått för Azure Digitals finns i [*fel sökning: Konfigurera diagnostik*](troubleshoot-diagnostics.md).
