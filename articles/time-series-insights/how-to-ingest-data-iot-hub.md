---
title: Så här lägger du till en händelse källa för IoT Hub – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du lägger till en händelse källa för IoT Hub i din Azure Time Series Insight-miljö.
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
ms.openlocfilehash: 85773ec66dbe567afcfd1afca1d14aa0119fb8f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464028"
---
# <a name="add-an-iot-hub-event-source-to-your-azure-time-series-insight-environment"></a>Lägg till en händelse källa för IoT Hub i din Azure Time Series Insight-miljö

Den här artikeln beskriver hur du använder Azure Portal för att lägga till en händelse källa som läser data från Azure IoT Hub till din Azure Time Series Insights miljö.

> [!NOTE]
> Anvisningarna i den här artikeln gäller både för Azure Time Series Insights gen 1-och Azure Time Series Insight gen 2-miljöer.

## <a name="prerequisites"></a>Förutsättningar

* Skapa en [Azure Time Series Insightss miljö](./tutorial-set-up-environment.md).
* Skapa en [IoT-hubb med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* IoT-hubben måste ha aktiva meddelande händelser som skickas i.
* Skapa en dedikerad konsument grupp i IoT Hub för Azure Time Series Insight-miljön att använda. Varje händelse källa för Azure Time Series Insight måste ha en egen dedikerad konsument grupp som inte delas med någon annan konsument. Om flera läsare förbrukar händelser från samma konsument grupp, kommer alla läsare att kunna uppvisa problem. Mer information finns i [Azure IoT Hub Developer Guide](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägga till en konsument grupp i IoT Hub

Program använder konsument grupper för att hämta data från Azure IoT Hub. Om du vill läsa data på ett tillförlitligt sätt från din IoT-hubb ger du en dedikerad konsument grupp som endast används av den här Azure Time Series Insight-miljön.

Så här lägger du till en ny konsument grupp i IoT Hub:

1. Leta upp och öppna din IoT Hub i [Azure Portal](https://portal.azure.com).

1. Under **Inställningar** väljer du **inbyggda slut punkter** och väljer sedan **händelse** slut punkten.

   [![På sidan build-in-slutpunkter väljer du knappen händelser](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. Under **konsument grupper** anger du ett unikt namn på konsument gruppen. Använd samma namn i din Azure Time Series Insight-miljö när du skapar en ny händelse källa.

1. Välj **Spara**.

## <a name="add-a-new-event-source"></a>Lägg till en ny händelse källa

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla resurser** i menyn till vänster. Välj din Azure Time Series Insight-miljö.

1. Under **Inställningar** väljer du **händelse källor** och väljer sedan **Lägg till**.

   [![Välj händelse källor och välj sedan knappen Lägg till](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. I fönstret **ny händelse källa** , för **händelse källans namn**, anger du ett namn som är unikt för den här Azure Time Series Insight-miljön. Ange till exempel **händelse-Stream**.

1. För **källa** väljer du **IoT Hub**.

1. Välj ett värde för **alternativet importera**:

   * Om du redan har en IoT-hubb i en av dina prenumerationer väljer du **använd IoT Hub från tillgängliga prenumerationer**. Det här alternativet är den enklaste metoden.

     [![Välj alternativ i fönstret ny händelse källa](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

   * I följande tabell beskrivs de egenskaper som krävs för alternativet **använd IoT Hub från tillgängliga prenumerationer** :

       [![Fönstret ny händelse källa – egenskaper som anges i alternativet Använd IoT Hub från tillgängliga prenumerationer](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumeration | Prenumerationen på den önskade IoT-hubben tillhör. |
       | Namn på IoT Hub | Namnet på den valda IoT-hubben. |
       | Princip namn för IoT Hub | Välj principen för delad åtkomst. Du kan hitta principen för delad åtkomst på fliken Inställningar i IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Den delade åtkomst principen för din händelse källa *måste* ha behörighet för **tjänst anslutning** . |
       | Princip nyckel för IoT Hub | Nyckeln är förifylld. |

   * Om IoT Hub är externt för dina prenumerationer, eller om du vill välja avancerade alternativ, väljer du **ange IoT Hub inställningar manuellt**.

      I följande tabell beskrivs de egenskaper som krävs för att **ange IoT Hub inställningar manuellt**:

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumerations-ID:t | Prenumerationen på den önskade IoT-hubben tillhör. |
       | Resursgrupp | Resurs grupps namnet där IoT Hub skapades. |
       | Namn på IoT Hub | Namnet på din IoT Hub. När du har skapat din IoT-hubb angav du ett namn för IoT Hub. |
       | Princip namn för IoT Hub | Principen för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken Inställningar i IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Den delade åtkomst principen för din händelse källa *måste* ha behörighet för **tjänst anslutning** . |
       | Princip nyckel för IoT Hub | Den delade åtkomst nyckeln som används för att autentisera åtkomsten till Azure Service Bus-namnrymden. Ange den primära eller sekundära nyckeln här. |

   * Båda alternativen delar följande konfigurations alternativ:

       | Egenskap | Beskrivning |
       | --- | --- |
       | Konsument grupp för IoT Hub | Konsument gruppen som läser händelser från IoT Hub. Vi rekommenderar starkt att du använder en dedikerad konsument grupp för din händelse källa. |
       | Händelseserialiseringsformat | JSON är för närvarande det enda tillgängliga serialiserings formatet. Händelse meddelandena måste vara i detta format eller så kan inga data läsas. |
       | Egenskapsnamn för tidsstämpel | För att fastställa det här värdet måste du förstå meddelande formatet för de meddelande data som skickas till IoT Hub. Det här värdet är **namnet** på den aktuella händelse egenskapen i de meddelande data som du vill använda som händelsens tidsstämpel. Värdet är Skift läges känsligt. Om värdet är tomt används **tids perioden för händelsen** i händelse källan som händelsens tidsstämpel. |

1. Lägg till det dedikerade Azure Time Series Insight-användarnamnet som du har lagt till i IoT-hubben.

1. Välj **Skapa**.

1. När du har skapat händelse källan börjar insikter i Azure Time Series automatiskt att strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg

* [Definiera data åtkomst principer](./concepts-access-policies.md) för att skydda data.

* [Skicka händelser](time-series-insights-send-events.md) till händelse källan.

* Få åtkomst till din miljö i [Azure Time Series Insight Explorer](https://insights.timeseries.azure.com).
