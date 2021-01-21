---
title: Importera enhetsinformation
description: Defender för IoT-sensorer övervakar och analyserar speglad trafik. I dessa fall kanske du vill importera data för att utöka informationen på enheter som redan har identifierats.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb805f60ba9feb0ae2d1483b2ab2df4e03639d8
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625422"
---
# <a name="import-device-information-to-a-sensor"></a>Importera enhets information till en sensor

En Azure Defender för IoT-sensor övervakar och analyserar speglad trafik. I vissa fall, på grund av företagsspecifika nätverks konfigurations principer, kan viss information inte skickas.

I dessa fall kanske du vill importera data för att utöka informationen på enheter som redan har identifierats. Det finns två alternativ för att importera information till sensorer:

- **Importera från kartan**: uppdatera enhets namn, typ, grupp eller Purdue lager till kartan.

- **Importera från import inställningar**: importera enhetens operativ system, IP-adress, korrigerings nivå eller tillstånds status.

## <a name="import-from-the-map"></a>Importera från kartan

I det här avsnittet beskrivs hur du importerar enhets namn, typer, grupper eller Purdue lager till enhets kartan. Du gör detta från kartan.

Här är import kraven:

- **Namn**: kan vara upp till 30 tecken.

- **Typ** -eller **Purdue lager**: Använd de alternativ som visas i dialog rutan **enhets egenskaper** . (Högerklicka på enheten och välj **Visa egenskaper**.)

- **Enhets grupp**: skapa en ny grupp på högst 30 tecken. 

> [!NOTE]
> För att undvika konflikter ska du inte importera de data som du exporterade från en sensor till en annan sensor.

För att importera:

1. På menyn på sidan väljer du **enheter**.

2. I det övre högra hörnet i fönstret **enheter** väljer du :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Skärm bild av enhets fönstret.":::

3. Välj **Exportera enheter**. En omfattande mängd information visas i den exporterade filen. Den här informationen inkluderar protokoll som enheten använder och status för enhets auktorisering.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informationen i den exporterade filen.":::

4. I CSV-filen ändrar du endast enhets namn, typ, grupp och Purdue lager. Spara sedan filen. 

   Använd standarder för versaler som visas i den exporterade filen. Till exempel, för Purdue-skiktet, använder du alla versaler i första bokstaven.

5. Från List menyn **Importera/exportera** i **enhets** fönstret väljer du **Importera enheter**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importera enheter via enhets fönstret.":::

6. Välj **Importera enheter** och välj den CSV-fil som du vill importera. Import status meddelanden visas på skärmen tills dialog rutan **Importera enheter** stängs.

## <a name="import-from-import-settings"></a>Importera från import inställningar

I det här avsnittet beskrivs hur du importerar enhetens IP-adress, operativ system, korrigerings nivå eller behörighets status till enhets kartan. Det gör du i dialog rutan **Importera inställningar** .

Så här importerar du IP-adress, operativ system och korrigerings nivå:

1. Hämta filen [devices_info_2.2.8 och up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) från [Hjälp Center](https://cyberx-labs.zendesk.com/hc/en-us) och ange informationen på följande sätt:

   - **IP-adress**: Ange ENHETens IP-adress.

   - **Operativ system**: Välj i den nedrullningsbara listan.

   - **Senaste uppdatering**: Använd formatet åååå-mm-dd.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Sidan alternativ.":::

2. På menyn på sidan väljer du **Importera inställningar**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importera dina inställningar.":::

3. Om du vill överföra den nödvändiga konfigurationen går du till avsnittet **enhets information** och väljer **Lägg till** och överför den CSV-fil som du har för berett.

Så här importerar du statusen för auktorisering:

1. Hämta och spara [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) -filen från Defender för IoT Help Center. Kontrol lera att du har sparat filen som en CSV-fil.

2. Ange informationen som:

   - **IP-adress**: ENHETens IP-adress.

   - **Namn**: namnet på den auktoriserade enheten. Kontrol lera att namnen är korrekta. Namn som anges för enheterna i den importerade listan överskrivna namn som visas i enhets kartan.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Excel-filer med importerad enhets lista.":::

3. På menyn på sidan väljer du **Importera inställningar**.

4. I avsnittet **auktoriserade enheter** väljer du **Lägg till** och överför CSV-filen som du sparade.

När informationen importeras får du aviseringar om oauktoriserade enheter för alla enheter som inte visas i listan.

## <a name="import-device-information-to-the-sensor"></a>Importera enhets information till sensorn

Sensorn övervakar och analyserar speglad trafik. I vissa fall, på grund av företagsspecifika nätverks konfigurations principer, kan viss information inte skickas.

I dessa fall kanske du vill importera data för att utöka enhets information på enheter som redan har identifierats. Det finns två alternativ för att importera information till sensorer:

- **Importera från kartan**: uppdatera enhets namn, typ, grupp eller Purdue lager till kartan.

- **Importera från import inställningar**: importera enhetens operativ system, IP-adress, korrigerings nivå eller tillstånds status.

### <a name="import-from-the-map"></a>Importera från kartan

I det här avsnittet beskrivs hur du importerar enhets namn, typer, grupper eller Purdue lager till enhets kartan. Du gör detta från kartan.

Här är import kraven:

- **Namn**: kan vara upp till 30 tecken.

- **Typ** -eller **Purdue lager**: Använd de alternativ som visas i dialog rutan **enhets egenskaper** . (Högerklicka på enheten och välj **Visa egenskaper**.)

- **Enhets grupp**: skapa en ny grupp på högst 30 tecken. 

> [!NOTE]
> För att undvika konflikter ska du inte importera de data som du exporterade från en sensor till en annan sensor.

För att importera:

1. På menyn på sidan väljer du **enheter**.

2. I det övre högra hörnet i fönstret **enheter** väljer du :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Fönstret att välja din enhet från.":::

3. Välj **Exportera enheter**. En omfattande mängd information visas i den exporterade filen. Exempel är protokoll som enheten använder och status för enhets auktorisering.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informationen i den exporterade filen.":::

4. I CSV-filen ändrar du endast enhets namn, typ, grupp och Purdue lager. Spara sedan filen. 

   Använd standarder för versaler som visas i den exporterade filen. Till exempel, för Purdue-skiktet, använder du alla versaler i första bokstaven.

5. Från List menyn **Importera/exportera** i **enhets** fönstret väljer du **Importera enheter**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importera dina enheter.":::

6. Välj **Importera enheter** och välj den CSV-fil som du vill importera. Import status meddelanden visas på skärmen tills dialog rutan **Importera enheter** stängs.

### <a name="import-from-import-settings"></a>Importera från import inställningar 

I det här avsnittet beskrivs hur du importerar enhetens IP-adress, operativ system, korrigerings nivå eller behörighets status till enhets kartan. Det gör du i dialog rutan **Importera inställningar** .

Så här importerar du IP-adress, operativ system och korrigerings nivå:

1. Hämta filen [devices_info_2.2.8 och up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) från [Hjälp Center](https://cyberx-labs.zendesk.com/hc/en-us) och ange informationen på följande sätt:

   - **IP-adress**: ENHETens IP-adress.

   - **Operativ system**: Välj i den nedrullningsbara listan.

   - **Senaste uppdatering**: Använd formatet åååå-mm-dd.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Innehållet på skärmen.":::

2. På menyn på sidan väljer du **Importera inställningar**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Fyll i fönstret import inställningar.":::

3. Om du vill överföra den konfiguration som krävs går du till avsnittet **enhets information** och väljer **Lägg till** och överför den CSV-fil som du har för berett.

Så här importerar du statusen för auktorisering:

1. Hämta och spara [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) -filen från Defender för IoT Help Center. Kontrol lera att du har sparat filen som en CSV-fil.

2. Ange informationen som:

   - **IP-adress**: ENHETens IP-adress.

   - **Namn**: namnet på den auktoriserade enheten. Kontrol lera att namnen är korrekta. Namn som anges för enheterna i den importerade listan överskrivna namn som visas i enhets kartan.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Importera listan till enhets kartan.":::

3. På menyn på sidan väljer du **Importera inställningar**.

4. I avsnittet **auktoriserade enheter** väljer du **Lägg till** och överför CSV-filen som du sparade.

När informationen importeras får du aviseringar om oauktoriserade enheter för alla enheter som inte visas i listan.

## <a name="see-also"></a>Se även

[Styr vilken trafik som övervakas](how-to-control-what-traffic-is-monitored.md)

[Undersök identifieringar av sensorer i en enhetsinventering](how-to-investigate-sensor-detections-in-a-device-inventory.md)
