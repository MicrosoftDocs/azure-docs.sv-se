---
title: Uppgradera Azure-IoT Hub | Microsoft Docs
description: Ändra pris-och skalnings nivå för IoT Hub för att få fler funktioner för meddelande hantering och enhets hantering.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "61440241"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Uppgradera din IoT-hubb

När din IoT-lösning växer är Azure IoT Hub redo för att hjälpa dig att skala upp. Azure IoT Hub erbjuder två nivåer, Basic (B) och standard (S), för att hantera kunder som vill använda olika funktioner. Inom varje nivå finns tre storlekar (1, 2 och 3) som avgör hur många meddelanden som kan skickas varje dag.

När du har fler enheter och behöver fler funktioner finns det tre sätt att justera IoT-hubben så att den passar dina behov:

* Lägg till enheter i IoT Hub. Till exempel tillåter varje ytterligare enhet i en B1 IoT Hub ytterligare 400 000 meddelanden per dag.

* Ändra storleken på IoT Hub. Du kan till exempel migrera från B1-nivån till B2-nivån för att öka antalet meddelanden som varje enhet kan stödja per dag.

* Uppgradera till en högre nivå. Du kan till exempel uppgradera från B1-nivån till S1-nivån för att få åtkomst till avancerade funktioner med samma meddelande kapacitet.

Dessa ändringar kan uppstå utan att avbryta befintliga åtgärder.

Om du vill nedgradera IoT-hubben kan du ta bort enheter och minska storleken på IoT Hub, men du kan inte nedgradera till en lägre nivå. Du kan till exempel flytta från S2-nivån till S1-nivån, men inte från S2-nivån till B1-nivån. Det går bara att välja en typ av [IoT Hub-utgåva](https://azure.microsoft.com/pricing/details/iot-hub/) inom en nivå per IoT Hub. Du kan till exempel skapa en IoT Hub med flera enheter S1, men inte med en blandning av enheter från olika versioner, till exempel S1 och B3, eller S1 och S2.

De här exemplen är avsedda att hjälpa dig att förstå hur du anpassar din IoT Hub när din lösning ändras. För detaljerad information om varje nivås funktioner bör du alltid hänvisa till [Azure IoT Hub prissättning](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Uppgradera din befintliga IoT Hub

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till din IoT-hubb.

2. Välj **pris och skala**.

   ![Prissättning och skala](./media/iot-hub-upgrade/pricing-scale.png)

3. Om du vill ändra nivån för navet väljer du **pris-och skalnings nivå**. Välj den nya nivån och klicka sedan på **Välj**.

   ![Pris- och skalnivå](./media/iot-hub-upgrade/select-tier.png)

4. Om du vill ändra antalet enheter i hubben anger du ett nytt värde under **IoT Hub enheter**.

5. Välj **Spara** för att spara ändringarna.

Din IoT Hub är nu justerad och dina konfigurationer är oförändrade.

Den maximala gränsen för partition för Basic-nivå IoT Hub och standard nivån IoT Hub är 32. De flesta IoT-hubbar behöver bara 4 partitioner. Du väljer en partitions gräns när IoT Hub skapas och relaterar enhets-till-moln-meddelanden till antalet samtidiga läsare av dessa meddelanden. Värdet förblir oförändrat när du migrerar från Basic-nivån till standard nivån.

## <a name="next-steps"></a>Nästa steg

Få mer information om [hur du väljer rätt IoT Hub nivå](iot-hub-scaling.md).