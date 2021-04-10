---
title: Lär dig om enheter som har identifierats av alla företags sensorer
description: Använd enhets inventeringen i den lokala hanterings konsolen för att få en omfattande visning av enhets information från anslutna sensorer. Använd verktygen för att importera, exportera och filtrera för att hantera den här informationen.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: 0ae59123b59cfb54cba2a2ee9bdeefb411c8793b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782188"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Undersök alla identifieringar av företags sensor i enhets inventeringen

Du kan visa enhets information från anslutna sensorer genom att använda *enhets inventeringen* i den lokala hanterings konsolen. Den här funktionen ger dig en heltäckande bild av all nätverksinformation. Använd verktygen för att importera, exportera och filtrera för att hantera den här informationen. Statusinformation om de anslutna sensor versionerna visas också.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Skärm bild av enhetens inventerings data tabell.":::

I följande tabell beskrivs tabell kolumnerna i enhets inventeringen.

| Parameter | Beskrivning |
|--|--|
| **Aviseringar som inte godkänts** | Antalet ohanterade aviseringar som är associerade med den här enheten. |
| **Affär senhet** | Affär senheten som innehåller den här enheten. |
| **Region** | Den region som innehåller den här enheten. |
| **Webbplats** | Den plats som innehåller den här enheten. |
| **Zon** | Den zon som innehåller den här enheten. |
| **Enhet** | Azure Defender för IoT-sensorn som skyddar den här enheten. |
| **Namn** | Namnet på den här enheten som Defender för IoT identifierade den. |
| **Typ** | Typ av enhet, till exempel PLC eller HMI. |
| **Leverantör** | Namnet på enhetens leverantör, enligt vad som definieras i MAC-adressen. |
| **Operativsystem** | Enhetens operativ system. |
| **Inbyggd programvara** | Enhetens inbyggda program vara. |
| **IP-adress** | Enhetens IP-adress. |
| **LOKALT** | Enhetens VLAN. |
| **MAC-adress** | Enhetens MAC-adress. |
| **Protokoll** | De protokoll som enheten använder. |
| **Aviseringar som inte godkänts** | Antalet ohanterade aviseringar som är associerade med den här enheten. |
| **Har behörighet** | Enhetens status:<br />- **True**: enheten har auktoriserats.<br />- **Falskt**: enheten har inte auktoriserats. |
| **Kallas skanner** | Anger om den här enheten utför genomsökning – som aktiviteter i nätverket. |
| **Är programmerings enhet** | Om detta är en programmerings enhet:<br />- **True**: enheten utför programmerings aktiviteter för PLCs, RTUs och kontrollanter, som är relevanta för teknik stationer.<br />- **Falskt**: enheten är ingen programmerings enhet. |
| **Grupper** | Grupper där enheten ingår. |
| **Senaste aktivitet** | Den senaste aktivitet som enheten utförde. |
| **Discovered** | När den här enheten först setts i nätverket. |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Integrera data i Enterprise Device Inventory

Med funktionerna för data integrering kan du förbättra data i enhets inventeringen med information från andra företags resurser. Dessa källor omfattar CMDBs, DNS, brand väggar och webb-API: er.

Du kan använda den här informationen för att lära dig. Exempel:

- Datum för enhets inköp och garanti datum från sista och samma garanti

- Användare som ansvarar för varje enhet

- Biljett har öppnats för enheter

- Senaste datumet då den inbyggda program varan uppgraderades

- Enheter som tillåter åtkomst till Internet

- Enheter som kör aktiva antivirus program

- Användare som är inloggade på enheter

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Data tabell på enhets inventerings skärmen.":::

Du kan integrera data med antingen:

- Lägga till den manuellt

- Köra anpassade skript som Defender för IoT tillhandahåller

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagram över företags data integratorn.":::

Du kan arbeta med Defender-teknisk support för IoT för att konfigurera systemet för att ta emot webb-API-frågor.

Så här lägger du till data manuellt:

1. På sido menyn väljer du **enhets inventering** och väljer sedan :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Redigera enhetens inventerings inställningar.":::

2. I dialog rutan **enhets lager inställningar** väljer du **Lägg till anpassad kolumn**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Lägg till en anpassad kolumn i inventeringen.":::

3. I dialog rutan **Lägg till anpassad kolumn** lägger du till det nya kolumn namnet (upp till 250 teckens UTF), väljer **manuellt** och väljer **Spara**. Det nya objektet visas i dialog rutan **enhets inventerings inställningar** .

4. I det övre högra hörnet av **enhets inventerings** fönstret väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: och väljer **Exportera alla enhets inventering**. CSV-filen skapas.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="Den exporterade CSV-filen.":::

5. Lägg till informationen i den nya kolumnen manuellt och spara filen.

6. I det övre högra hörnet av **enhets inventerings** fönstret väljer :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: du, väljer **Importera manuella indata-kolumner** och bläddrar till CSV-filen. Nya data visas i **enhets inventerings** tabellen.

Så här integrerar du data från andra företags enheter:

1. I det övre högra hörnet av **enhets inventerings** fönstret väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: och väljer **Exportera alla enhets inventering**.

2. I dialog rutan **enhets lager inställningar** väljer du **Lägg till anpassad kolumn**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Lägg till en anpassad kolumn i inventeringen.":::

3. I dialog rutan **Lägg till anpassad kolumn** lägger du till det nya kolumn namnet (upp till 250 teckens UTF) och väljer sedan **automatiskt**. Alternativen för att **Ladda upp skript** och **testa skript** visas.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Lägg automatiskt till anpassade kolumner.":::

4. Ladda upp och testa skriptet som du fick från [Microsoft Support](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="retrieve-information-from-the-device-inventory"></a>Hämta information från enhets inventeringen

Du kan hämta en stor mängd enhets information som identifieras av hanterade sensorer och integrera den informationen med partner system. Du kan till exempel hämta sensor, zon, plats-ID, IP-adress, MAC-adress, inbyggd program vara, protokoll och leverantörs information. Filtrera information som du hämtar baserat på:

- Auktoriserade och oauktoriserade enheter.

- Enheter som är kopplade till vissa platser.

- Enheter som är kopplade till vissa zoner.

- Enheter som är kopplade till vissa sensorer.

Arbeta med Defender-kommandon i IoT API för att hämta och integrera den här informationen. Mer information finns i [Defender för API: er för IoT API-sensor och Management Console](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtrera enhets inventeringen

Du kan filtrera enhets inventeringen för att visa intresse kolumner. Du kan till exempel Visa information om PLC-enheter.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Skärm bild av enhets inventeringen.":::

Filtret rensas när du lämnar fönstret.

Om du vill använda samma filter flera gånger kan du spara ett filter eller en kombination av filter som du behöver. Du kan öppna ett vänster fönster och Visa de filter som du har sparat:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Enhets inventerings skärmen.":::

Så här filtrerar du enhets inventeringen:

1. I den kolumn som du vill filtrera väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: .

2. Välj filter typ i dialog rutan **filter** :

   - **Lika med**: det exakta värde enligt vilket du vill filtrera kolumnen. Om du till exempel filtrerar kolumnen protokoll efter **lika med** och `value=ICMP` , kommer kolumnen att visa enheter som bara använder ICMP-protokollet.

   - **Contains**: det värde som finns bland andra värden i kolumnen. Om du till exempel filtrerar kolumnen protokoll enligt **contains** och, visar `value=ICMP` kolumnen enheter som använder ICMP-protokollet som en del av listan över protokoll som enheten använder.

3. Om du vill ordna kolumn informationen enligt alfabetisk ordning väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Ordna ordningen genom att välja :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: pilarna och :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Om du vill spara ett nytt filter definierar du filtret och väljer **Spara som**.

5. Om du vill ändra filter definitionerna ändrar du definitionerna och väljer **Spara ändringar**.

## <a name="next-steps"></a>Nästa steg

[Undersök identifieringar av sensorer i en enhetsinventering](how-to-investigate-sensor-detections-in-a-device-inventory.md)
