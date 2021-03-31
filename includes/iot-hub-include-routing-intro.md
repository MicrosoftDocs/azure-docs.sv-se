---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 552a40be0c069d1002ebc7ea4dafe0d6f93a5755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85128210"
---
Med [meddelanderoutning](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) kan du skicka telemetridata från dina IoT-enheter till inbyggda Event Hub-kompatibla slut punkter eller anpassade slut punkter som Blob storage, Service Bus köer, Service Bus ämnen och Event Hubs. Om du vill konfigurera en anpassad meddelanderoutning skapar du [routningslänkar](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) för att anpassa den väg som matchar ett visst villkor. Därefter dirigeras inkommande data automatiskt till slutpunkterna av IoT Hub. Om ett meddelande inte matchar någon av de definierade Dirigerings frågorna dirigeras det till standard slut punkten.

I den här självstudien i två delar får du lära dig hur du konfigurerar och använder de här anpassade Dirigerings frågorna med IoT Hub. Du dirigerar meddelanden från en IoT-enhet till en av flera slut punkter, inklusive Blob Storage och en Service Bus kö. Meddelanden till Service Bus kön hämtas av en Logic app och skickas via e-post. Meddelanden som inte har definierats med anpassad meddelanderoutning skickas till standard slut punkten och hämtas sedan Azure Stream Analytics och visas i en Power BI visualisering.

För att slutföra del 1 och 2 av den här självstudien utför du följande uppgifter:

**Del I: skapa resurser, konfigurera meddelanderoutning**
> [!div class="checklist"]
> * Skapa resurserna – en IoT-hubb, ett lagrings konto, en Service Bus kö och en simulerad enhet. Detta kan göras med hjälp av Azure Portal, en Azure Resource Manager mall, Azure CLI eller Azure PowerShell.
> * Konfigurera slut punkterna och meddelande vägarna i IoT Hub för lagrings kontot och Service Bus kön.

**Del II: skicka meddelanden till hubben, Visa dirigerade resultat**
> [!div class="checklist"]
> * Skapa en logikapp som utlöses och skickar e-post när ett meddelande läggs till i Service Bus-kön.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben för de olika dirigeringsalternativen.
> * Skapa en Power BI-visualisering för data som skickas till standardslutpunkten.
> * Visa resultatet ...
> * ...i Service Bus-kön och e-postmeddelandena.
> * ...i lagringskontot.
> * ...i Power BI-visualiseringen.

## <a name="prerequisites"></a>Förutsättningar

* I del 1 av den här självstudien:
  - Du måste ha en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* I del 2 av den här självstudien:
  - Du måste ha slutfört del 1 av den här självstudien och ha resurserna fortfarande tillgängliga.
  - Installera [Visual Studio](https://www.visualstudio.com/).
  - Få åtkomst till ett Power BI konto för att analysera standard slut punktens Stream Analytics. ([Prova Power BI utan kostnad](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Ha ett arbets-eller skol konto för att skicka e-postmeddelanden.
  - Kontrol lera att port 8883 är öppen i brand väggen. Exemplet i den här självstudien använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
