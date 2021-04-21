---
title: Hantera sensorer i Defender for IoT-portalen
description: Lär dig hur du registrerar, visar och hanterar sensorer i Defender for IoT-portalen.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f407a65f60a1b969f17ebe00be39a888a09ec83d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752722"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>Hantera sensorer i Defender for IoT-portalen

Den här artikeln beskriver hur du registrerar, visar och hanterar sensorer i [Defender for IoT-portalen.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

## <a name="onboard-sensors"></a>Registrera sensorer

Du registrerar en sensor genom att registrera den med Azure Defender for IoT och ladda ned en sensoraktiveringsfil.

### <a name="register-the-sensor"></a>Registrera sensorn

Så här registrerar du dig:

1. Gå till **välkomstsidan** i [Defender for IoT-portalen.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)
1. Välj **Publicera sensor.**
1. Skapa ett sensornamn. Vi rekommenderar att du inkluderar IP-adressen för sensorn som du installerade som en del av namnet eller använder ett lätt identifierbart namn. Detta säkerställer enklare spårning och konsekvent namngivning mellan registreringsnamnet i Azure [Defender for IoT-portalen](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) och IP-adressen för den distribuerade sensorn som visas i sensorkonsolen.
1. Associera sensorn med en Azure-prenumeration.
1. Välj ett sensoranslutningsläge med hjälp av **den molnanslutna** växlingsknappen. Om växlingsknappen är på är sensorn molnansluten. Om växlingsknappen är inaktiverad hanteras sensorn lokalt.

   - **Molnanslutna sensorer:** Information som sensorn identifierar visas i sensorkonsolen. Aviseringsinformation levereras via en IoT-hubb och kan delas med andra Azure-tjänster, till exempel Azure Sentinel. Dessutom kan hotinformationspaket push-skickas från Azure Defender for IoT-portalen till sensorer. Om sensorn inte är molnansluten måste du ladda ned hotinformationspaket och sedan ladda upp dem till dina företagssensorer. Om du vill tillåta att Defender for IoT push-erar paket till sensorer aktiverar du växlingsknappen **Automatiska uppdateringar av** hotinformation. Mer information finns i [Hotinformationsforskning och paket.](how-to-work-with-threat-intelligence-packages.md)
   Välj en IoT-hubb som fungerar som en gateway mellan den här sensorn och Azure Defender for IoT portalen. Definiera ett platsnamn och en zon. Du kan också lägga till beskrivande taggar. Platsnamn, zon och taggar är beskrivande poster på [sidan Platser och sensorer.](#view-onboarded-sensors)

   - **Lokalt hanterade sensorer:** Information som sensorerna identifierar visas i sensorkonsolen. Om du arbetar i ett trådlöst nätverk och vill ha en enhetlig vy över all information som identifieras av flera lokalt hanterade sensorer kan du arbeta med den lokala hanteringskonsolen.

   För molnanslutna sensorer är namnet som definierats under onboarding det namn som visas i sensorkonsolen. Du kan inte ändra det här namnet direkt från konsolen. För lokalt hanterade sensorer lagras namnet som tillämpas under registrering i Azure, men kan uppdateras i sensorkonsolen.

### <a name="download-the-sensor-activation-file"></a>Ladda ned sensoraktiveringsfilen

Sensoraktiveringsfilen innehåller instruktioner om sensorns hanteringsläge. Du laddar ned en unik aktiveringsfil för varje sensor som du distribuerar. En användare som loggar in på sensorkonsolen för första gången laddar upp aktiveringsfilen till sensorn.

Så här laddar du ned en aktiveringsfil:

1. På sidan **Publicera sensor väljer** du ladda ned **aktiveringsfilen**.
1. Gör filen tillgänglig för den användare som loggar in på sensorkonsolen för första gången.

## <a name="view-onboarded-sensors"></a>Visa inbyggda sensorer

I [Defender for IoT-portalen](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)kan du visa viktig driftsinformation om inbyggda sensorer.

1. Välj **Platser och sensorer.** Sidan visar hur många sensorer som har introducerats, antalet sensorer som är molnanslutna och lokalt hanterade, samt:

- det sensornamn som tilldelades under registrering.
- anslutningstyp (molnansluten eller lokalt hanterad)
- zonen som är associerad med sensorn.
- Den installerade sensorversionen
- Status för sensoranslutningen till molnet.
- Senaste gången sensorn identifierades som ansluter till molnet.

## <a name="manage-onboarded-sensors"></a>Hantera inbyggda sensorer

Använd [Defender for IoT-portalen för](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) hanteringsuppgifter relaterade till sensorer.

Inbyggda sensorer kan visas på **sidan Platser och** sensorer. Du kan också redigera sensorinformation från den här sidan.

### <a name="export-sensor-details"></a>Exportera sensorinformation

Om du vill exportera inbyggd sensorinformation väljer **du ikonen** Exportera överst på sidan **Platser och** sensorer.

### <a name="edit-sensor-zone-details"></a>Redigera information om sensorzon

Använd **redigeringsalternativen Platser och** sensorer för att redigera sensornamnet och zonen.

Så här redigerar du:

1. Välj **ellipsen** (**...**) för den sensor som du vill redigera.
1. Välj **Redigera**.
1. Uppdatera sensorzonen eller skapa en ny zon.

### <a name="delete-a-sensor"></a>Ta bort en sensor

Om du tar bort en molnansluten sensor skickas inte information till IoT-hubben. Ta bort lokalt anslutna sensorer när du inte längre arbetar med dem.

Så här tar du bort en sensor:

1. Välj ellipsen (**...**) för den sensor som du vill ta bort.
1. Bekräfta borttagningen.

### <a name="reactivate-a-sensor"></a>Återaktivera en sensor 

Du kan behöva återaktivera sensorn eftersom du vill:

- **Arbeta i molnanslutet** läge i stället för lokalt hanterat läge: Efter återaktiveringen visas sensoridentifieringarna i sensorn och nyligen identifierad aviseringsinformation levereras via IoT-hubben. Den här informationen kan delas med andra Azure-tjänster, till exempel Azure Sentinel.

- **Arbeta i lokalt hanterat läge i stället** för molnanslutet läge: Efter återaktiveringen visas sensoridentifieringsinformation endast i sensorn.

- **Associera sensorn med en ny IoT-hubb:** Om du vill göra det registrerar du sensorn på nytt med en ny hubb och laddar sedan ned en ny aktiveringsfil.

Så här återaktiverar du en sensor:

1. Gå till **sidan Webbplatser och** sensorer på Defender for [IoT-portalen](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Välj den sensor som du vill ladda upp en ny aktiveringsfil för.

3. Ta bort sensorn.

4. Publicera sensorn igen i det nya läget eller med en ny IoT-hubb genom att välja Publicera en **sensor** Komma igång sidan.

5. Ladda ned aktiveringsfilen.

1. Logga in på Defender for IoT-sensorkonsolen.

7. I sensorkonsolen väljer **du Systeminställningar** och sedan **Återaktivering.**

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Ladda upp aktiveringsfilen för att återaktivera sensorn.":::

8. Välj **Ladda** upp och välj den fil som du sparade från sidan Publicera sensor.

9. Välj **Aktivera.**

## <a name="next-steps"></a>Nästa steg

[Aktivera och konfigurera sensor](how-to-activate-and-set-up-your-sensor.md)
