---
title: Lägg till en Event Hubs händelse källa – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du lägger till en händelse källa för Azure-Event Hubs i din Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: ee66e68216933c410092865a1cdb781476a944c6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461142"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Lägg till händelse källan för Event Hub i Azure Time Series Insightss miljön

Den här artikeln beskriver hur du använder Azure Portal för att lägga till en händelse källa som läser data från Azure Event Hubs till din Azure Time Series Insights miljö.

> [!NOTE]
> Stegen som beskrivs i den här artikeln gäller både Azure Time Series Insights gen 1-och Azure Time Series Insights gen 2-miljöer.

## <a name="prerequisites"></a>Förutsättningar

- Skapa en Azure Time Series Insights-miljö enligt beskrivningen i [skapa en Azure Time Series Insights-miljö](./tutorial-set-up-environment.md).
- Skapa en händelsehubb. Läs [skapa ett Event Hubs-namnområde och en Event Hub med hjälp av Azure Portal](../event-hubs/event-hubs-create.md).
- Event Hub måste ha aktiva meddelande händelser skickade till den. Lär dig hur du [skickar händelser till Azure Event Hubs med hjälp av .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Skapa en dedikerad konsument grupp i händelsehubben som Azure Time Series Insightss miljön kan använda. Varje Azure Time Series Insights händelse källa måste ha en egen dedikerad konsument grupp som inte delas med någon annan konsument. Om flera läsare förbrukar händelser från samma konsument grupp, kommer alla läsare att kunna uppvisa problem. Det finns en gräns på 20 konsument grupper per Event Hub. Mer information finns i [programmerings guiden för Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Lägga till en konsument grupp till händelsehubben

Program använder konsument grupper för att hämta data från Azure Event Hubs. Om du vill läsa data på ett tillförlitligt sätt från händelsehubben kan du ange en dedikerad konsument grupp som endast används av den här Azure Time Series Insightss miljön.

Så här lägger du till en ny konsument grupp i händelsehubben:

1. I [Azure Portal](https://portal.azure.com)letar du reda på och öppnar Event Hub-instansen från fönstret **Översikt** i Event Hub-namnområdet. Välj **entiteter > Event Hubs** eller Sök efter din instans under **namn**.

    [![Öppna ditt Event Hub-namnområde](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. I Event Hub-instansen väljer du **entiteter > konsument grupper**. Välj sedan **+ konsument grupp** för att lägga till en ny konsument grupp.

   [![Händelsehubben – Lägg till en konsument grupp](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Annars väljer du en befintlig konsument grupp och går vidare till nästa avsnitt.

1. På sidan **konsument grupper** anger du ett nytt unikt värde för **namn**.  Använd samma namn när du skapar en ny händelse källa i Azure Time Series Insightss miljön.

1. Välj **Skapa**.

## <a name="add-a-new-event-source"></a>Lägg till en ny händelse källa

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Leta upp din befintliga Azure Time Series Insights-miljö. Välj **alla resurser** på den vänstra menyn och välj sedan din Azure Time Series Insightss miljö.

1. Välj **händelse källor** och välj sedan **Lägg till**.

   [![Under händelse källor väljer du knappen Lägg till](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Ange ett värde för **händelse källans namn** som är unikt för den här Azure Time Series Insightss miljön, till exempel `Contoso-TSI-Gen 1-Event-Hub-ES` .

1. För **källa** väljer du **händelsehubben**.

1. Välj lämpliga värden för **alternativet importera**:

   - Om du har en befintlig händelsehubben i en av dina prenumerationer väljer du **Använd händelsehubben från tillgängliga prenumerationer**. Det här alternativet är den enklaste metoden.

     [![Välj ett import alternativ för händelse källa](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - I följande tabell beskrivs de nödvändiga egenskaperna för alternativet **Använd händelsehubben från tillgängliga prenumerationer** :

       [![Information om prenumeration och händelsehubben](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumeration | Prenumerationen på den önskade Event Hub-instansen och namn området tillhör. |
       | Namnområde för händelsehubb | Event Hub-namnområdet som den önskade Event Hub-instansen tillhör. |
       | Namn på händelsehubb | Namnet på den önskade Event Hub-instansen. |
       | Princip värde för Event Hub | Välj önskad princip för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken **Konfigurera konfiguration** av händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Den delade åtkomst principen för din händelse källa *måste* ha **Läs** behörighet. |
       | Princip nyckel för Event Hub | Fylls i automatiskt från det valda värdet för Event Hub-principen. |

   - Om händelsehubben är extern för dina prenumerationer, eller om du vill välja avancerade alternativ, väljer du **Ange inställningar för Event Hub manuellt**.

       I följande tabell beskrivs de egenskaper som krävs för alternativet **Tillhandahåll Event Hub-inställningar manuellt** :

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumerations-ID:t | Prenumerationen på den önskade Event Hub-instansen och namn området tillhör. |
       | Resursgrupp | Resurs gruppen som den önskade Event Hub-instansen och namn området tillhör. |
       | Namnområde för händelsehubb | Event Hub-namnområdet som den önskade Event Hub-instansen tillhör. |
       | Namn på händelsehubb | Namnet på den önskade Event Hub-instansen. |
       | Princip värde för Event Hub | Välj önskad princip för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken **Konfigurera konfiguration** av händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Den delade åtkomst principen för din händelse källa *måste* ha **Läs** behörighet. |
       | Princip nyckel för Event Hub | Den delade åtkomst nyckeln som används för att autentisera åtkomsten till Service Bus-namnrymden. Ange den primära eller sekundära nyckeln här. |

   - Båda alternativen delar följande konfigurations alternativ:

       | Egenskap | Beskrivning |
       | --- | --- |
       | Konsumentgrupp för händelsehubb | Konsument gruppen som läser händelser från händelsehubben. Vi rekommenderar starkt att du använder en dedikerad konsument grupp för din händelse källa. |
       | Händelseserialiseringsformat | JSON är för närvarande det enda tillgängliga serialiserings formatet. Händelse meddelanden måste ha det här formatet eller så går det inte att läsa data. |
       | Egenskapsnamn för tidsstämpel | För att fastställa det här värdet måste du förstå meddelande formatet för de meddelande data som skickas till händelsehubben. Det här värdet är **namnet** på den aktuella händelse egenskapen i de meddelande data som du vill använda som händelsens tidsstämpel. Värdet är Skift läges känsligt. Om värdet är tomt används **tids perioden för händelsen** i händelse källan som händelsens tidsstämpel. |

1. Lägg till det dedikerade Azure Time Series Insights konsument grupp namn som du har lagt till i händelsehubben.

1. Välj **Skapa**.

   När händelse källan har skapats börjar Azure Time Series Insights automatiskt strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg

- [Definiera data åtkomst principer](./concepts-access-policies.md) för att skydda data.

- [Skicka händelser](time-series-insights-send-events.md) till händelse källan.

- Få åtkomst till din miljö i [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com).
