---
title: Publicera och hantera sensorer i Defender för IoT-portalen
description: Lär dig att publicera, Visa och hantera sensorer i Defender för IoT-portalen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a763d8b65049cd9f301379c2c038a1d799114653
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97841890"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Publicera och hantera sensorer i Defender för IoT-portalen

Den här artikeln beskriver hur du hanterar, visar och hanterar sensorer i Defender for IoT-portalen.

## <a name="onboard-sensors"></a>Registrera sensorer

Du registrerar en sensor genom att registrera den med Azure Defender för IoT och hämta en sensor aktiverings fil.

### <a name="register-the-sensor"></a>Registrera sensorn

Så här registrerar du dig:

1. Gå till **välkomst** sidan i Defender för IoT-portalen.
1. Välj **onboard-sensor**.
1. Skapa ett sensor namn. Vi rekommenderar att du inkluderar IP-adressen för sensorn som du installerade som en del av namnet eller använder ett enkelt identifierbart namn. På så sätt kan du lättare spåra och konsekvent namnge mellan registrerings namnet i Azure Defender för IoT-portalen och IP-adressen för den distribuerade sensorn som visas i sensor konsolen.
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

I Defender för IoT-portalen kan du visa grundläggande information om inbyggda sensorer. 

1. Välj **platser och sensorer**.
1. På sidan **platser och sensorer** använder du filter-och sökverktyg för att hitta sensor information som du behöver.

Den tillgängliga informationen innehåller:

- Hur många sensorer som har publicerats
- Antalet sensorer som är anslutna till molnet och som hanteras lokalt
- Hubben kopplad till en onboardd sensor
- Information som lagts till om en sensor, till exempel namnet som har tilldelats sensorn under onboarding, zonen som är kopplad till sensorn eller annan beskrivande information som läggs till med Taggar

## <a name="manage-onboarded-sensors"></a>Hantera inbyggda sensorer

Du kan använda Defender för IoT-portalen för hanterings uppgifter som rör sensorer.

### <a name="export"></a>Exportera

Om du vill exportera information om registrerad sensor väljer du **export** ikonen överst på sidan **platser och sensorer** .

### <a name="edit"></a>Redigera

Använd redigerings verktygen för **platser och sensorer** för att lägga till och redigera plats namn, zon och taggar.

### <a name="delete"></a>Ta bort

Om du tar bort en molnbaserad sensor skickas information inte till IoT Hub. Ta bort lokalt anslutna sensorer när du inte längre arbetar med dem.

Så här tar du bort en sensor:

1. Välj ellipsen (**...**) för den sensor som du vill ta bort. 
1. Bekräfta borttagningen.

### <a name="reactivate"></a>Återaktivera

Du kanske vill uppdatera det läge som sensorn hanterar i. Exempel:

- **Arbeta i Cloud-anslutet läge i stället för lokalt hanterat läge**: om du vill göra detta uppdaterar du aktiverings filen för din lokalt anslutna sensor med en aktiverings fil för en molnbaserad sensor. Efter återaktivering visas sensor identifieringar i både sensorn och Defender för IoT-portalen. När återaktiveringens filen har laddats upp skickas nyligen identifierad aviserings information till Azure.

- **Arbeta i lokalt anslutet läge i stället för läget för moln anslutning**: om du vill göra detta uppdaterar du aktiverings filen för en molnbaserad sensor med en aktiverings fil för en lokalt hanterad sensor. Efter återaktivering visas sensor identifierings information endast i sensorn.

- **Koppla sensorn till en ny IoT-hubb**: om du vill göra detta omregistrerar du sedan och laddar upp en ny aktiverings fil.

Så här återaktiverar du en sensor:

1. Gå till **platser och sensorer** -sidan på-portalen för Defender för IoT.

2. Välj den sensor som du vill ladda upp en ny aktiverings fil för.

3. Ta bort sensorn.

4. Publicera sensorn igen från **onboarding** -sidan i det nya läget eller med en ny IoT-hubb.

5. Hämta aktiverings filen från sidan **Hämta aktiverings fil** .

6. Logga in på konsolen Defender för IoT-sensorn.

7. I sensor konsolen väljer du **Systeminställningar** och väljer sedan **Återaktivering**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Ladda upp aktiverings filen för att återaktivera sensorn.":::

8. Välj **överför** och välj den fil som du sparade.

9. Välj **Aktivera**. 

## <a name="see-also"></a>Se även

[Aktivera och konfigurera din sensor](how-to-activate-and-set-up-your-sensor.md)
