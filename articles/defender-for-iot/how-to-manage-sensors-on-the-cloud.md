---
title: Publicera och hantera sensorer och prenumerationer i Defender for IoT-portalen
description: Lär dig att publicera, Visa och hantera sensorer i Defender för IoT-portalen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/18/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 63b3b450e289b40aa9acbfb0d5170e8eb57f9e58
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733269"
---
# <a name="onboard-and-manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>Publicera och hantera sensorer och prenumerationer i Defender for IoT-portalen

Den här artikeln beskriver hur du hanterar, visar och hanterar sensorer i [Defender for IoT-portalen](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Registrera sensorer

Du registrerar en sensor genom att registrera den med Azure Defender för IoT och hämta en sensor aktiverings fil.

### <a name="register-the-sensor"></a>Registrera sensorn

Så här registrerar du dig:

1. Gå till **välkomst** sidan i [Defender för IoT-portalen](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Välj **onboard-sensor**.
1. Skapa ett sensor namn. Vi rekommenderar att du inkluderar IP-adressen för sensorn som du installerade som en del av namnet eller använder ett enkelt identifierbart namn. På så sätt kan du lättare spåra och konsekvent namnge mellan registrerings namnet i Azure [Defender för IoT-portalen](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) och IP-adressen för den distribuerade sensorn som visas i sensor konsolen.
1. Koppla sensorn till en Azure-prenumeration.
1. Välj ett sensor hanterings läge med hjälp av den **anslutna molnet** . Om växlings listen är på är sensorn ansluten till molnet. Om växlingen är avstängd hanteras sensorn lokalt.

   - **Moln anslutna sensorer**: information som sensorn identifierar visas i sensor konsolen. Aviserings information levereras via en IoT-hubb och kan delas med andra Azure-tjänster, till exempel Azure Sentinel.

   - **Lokalt hanterade sensorer**: information som sensorer identifierar visas i sensor konsolen. Om du arbetar i ett Air-gapped nätverk och vill ha en enhetlig vy över all information som identifieras av flera lokalt hanterade sensorer, kan du arbeta med den lokala hanterings konsolen.

   För moln anslutna sensorer är namnet som definierats under onboarding det namn som visas i sensor konsolen. Du kan inte ändra namnet från konsolen direkt. För lokalt hanterade sensorer kommer det namn som används vid onboarding att lagras i Azure och kan uppdateras i sensor konsolen.

1. Välj en IoT-hubb som ska fungera som en gateway mellan den här sensorn och Azure Defender för IoT.
1. Om sensorn är ansluten till molnet associerar du den med en IoT-hubb och definierar sedan ett plats namn och en zon. Du kan också lägga till beskrivande taggar. Plats namn, zon och taggar är beskrivande poster på [sidan platser och sensorer](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Hämta sensor aktiverings filen

Sensor aktiverings filen innehåller instruktioner om hanterings läget för sensorn. Du hämtar en unik aktiverings fil för varje sensor som du distribuerar. En användare som loggar in på sensor konsolen för första gången överför aktiverings filen till sensorn.

Så här hämtar du en aktiverings fil:

1. På sidan **onboard sensor** väljer du **Hämta aktiverings fil**.
1. Gör filen tillgänglig för den användare som loggar in på sensor konsolen för första gången.

## <a name="view-onboarded-sensors"></a>Visa inbyggda sensorer

I [Defender för IoT-portalen](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)kan du visa grundläggande information om inbyggda sensorer.

1. Välj **platser och sensorer**.
1. Använd filter-och sökverktyg för att hitta information om sensor-och hot information som du behöver.

- Hur många sensorer som har publicerats
- Antalet sensorer som är anslutna till molnet och som hanteras lokalt
- Hubben kopplad till en onboardd sensor
- Information som lagts till om en sensor, till exempel namnet som har tilldelats sensorn under onboarding, zonen som är kopplad till sensorn eller annan beskrivande information som läggs till med Taggar

## <a name="manage-onboarded-sensors"></a>Hantera inbyggda sensorer

Använd [Defender för IoT-portalen](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) för hanterings uppgifter som rör sensorer.

Inbyggda sensorer kan visas på sidan **platser och sensorer** . Du kan också redigera sensor information från den här sidan.

### <a name="export-sensor-details"></a>Exportera sensor information

Om du vill exportera information om registrerad sensor väljer du **export** ikonen överst på sidan **platser och sensorer** .

### <a name="edit-sensor-zone-details"></a>Redigera sensor zons information

Använd redigerings alternativen **platser och sensorer** för att redigera sensorns namn och zon.

Redigera:

1. Välj **ellipsen** (**...**) för den sensor som du vill redigera.
1. Välj **Redigera**.
1. Uppdatera sensor zonen eller skapa en ny zon.

### <a name="delete-a-sensor"></a>Ta bort en sensor

Om du tar bort en molnbaserad sensor skickas information inte till IoT Hub. Ta bort lokalt anslutna sensorer när du inte längre arbetar med dem.

Så här tar du bort en sensor:

1. Välj ellipsen (**...**) för den sensor som du vill ta bort.
1. Bekräfta borttagningen.

### <a name="reactivate-a-sensor"></a>Återaktivera en sensor 

Du kan behöva återaktivera sensorn eftersom du vill:

- **Arbeta i Cloud-anslutet läge i stället för lokalt hanterat läge**: efter återaktivering visas sensor identifieringar i sensorn och nyligen identifierad aviserings information levereras via IoT Hub. Den här informationen kan delas med andra Azure-tjänster, till exempel Azure Sentinel.

- **Arbeta i lokalt hanterat läge i stället för läget för moln anslutning**: efter återaktivering visas sensor identifierings information endast i sensorn.

- **Koppla sensorn till en ny IoT-hubb**: om du vill göra detta omregistrerar du sensorn med en ny hubb och hämtar sedan en ny aktiverings fil.

Så här återaktiverar du en sensor:

1. Gå till **platser och sensorer** -sidan på- [portalen för Defender för IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Välj den sensor som du vill ladda upp en ny aktiverings fil för.

3. Ta bort sensorn.

4. Publicera sensorn igen i det nya läget eller med en ny IoT-hubb genom att välja **publicera en sensor** från komma igång sidan.

5. Hämta aktiverings filen.

1. Logga in på konsolen Defender för IoT-sensorn.

7. I sensor konsolen väljer du **Systeminställningar** och väljer sedan **Återaktivering**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Ladda upp aktiverings filen för att återaktivera sensorn.":::

8. Välj **överför** och välj filen som du sparade från den inbyggda sensor sidan.

9. Välj **Aktivera**.

## <a name="offboard-a-subscription"></a>Avpublicera en prenumeration

Prenumerationer hanteras per månad. När du avpublicera en prenumeration debiteras du för den prenumerationen fram till slutet av månaden. 

Avinstallera alla sensorer som är associerade med prenumerationen innan du offboarding prenumerationen. Mer information om hur du tar bort en sensor finns i [ta bort en sensor](#delete-a-sensor). 

Så här avpublicera du en prenumeration:

1. Gå till sidan med **priser** .
1. Välj prenumerationen och välj sedan ikonen **ta bort** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. I popup-fönstret för bekräftelse markerar du kryss rutan för att bekräfta att du har tagit bort alla sensorer som är associerade med prenumerationen.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Markera kryss rutan och välj avpublicera för att avpublicera sensorn.":::

1. Välj knappen **avpublicera** . 

Den lokala miljön påverkas inte, men du bör avinstallera sensorn från den lokala miljön eller omtilldela sensorn till en annan prenumeration, så att inga relaterade data flödar till den lokala hanterings konsolen. 

## <a name="see-also"></a>Se även

[Aktivera och konfigurera sensor](how-to-activate-and-set-up-your-sensor.md)
