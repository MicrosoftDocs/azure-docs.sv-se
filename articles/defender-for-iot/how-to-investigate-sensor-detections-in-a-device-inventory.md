---
title: Få insikt i enheter som identifieras av en speciell sensor
description: Enhets inventeringen visar ett stort antal enhets egenskaper som en sensor identifierar.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 4daec83f44a545d7837a7e73e847f56b1f5770e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782103"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Undersök identifieringar av sensorer i en enhetsinventering

Enhets inventeringen visar ett stort antal enhets egenskaper som en sensor identifierar. Alternativen är tillgängliga för att:

 - Filtrera informationen enkelt.

 - Exportera information till en CSV-fil.

 - Importera information om Windows-registret.

 - Skapa grupper för visning i enhets kartan.

## <a name="view-device-attributes-in-the-device-inventory"></a>Visa enhetens attribut i enhets inventeringen

Följande attribut visas i enhets inventerings tabellen.

| Parameter | Beskrivning |
|--|--|
| Name | Enhetens namn som sensorn upptäckte den eller som anges av användaren. |
| Typ | Typ av enhet som bestäms av sensorn eller som anges av användaren. |
| Leverantör | Namnet på enhetens leverantör, enligt vad som definieras i MAC-adressen. |
| Operativsystem | Enhetens operativ system, om det upptäcks. |
| Version av inbyggd programvara | Enhetens inbyggda program vara, om den upptäcks. |
| IP-adress | IP-adressen för enheten som definierats. |
| LOKALT | Enhetens VLAN. Mer information om hur du instruerar sensorn att identifiera VLAN finns i [definiera VLAN-namn](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (How-to-define-Management-Console-Network-Settings. MD # define-VLAN-Names). |
| MAC-adress | Enhetens MAC-adress. |
| Protokoll | De protokoll som enheten använder. |
| Aviseringar som inte godkänts | Antalet aviseringar som inte har godkänts och som är kopplade till den här enheten. |
| Har behörighet | Den behörighets status som definierats av användaren:<br />- **True**: enheten har auktoriserats.<br />- **Falskt**: enheten har inte auktoriserats. |
| Kallas skanner | Definieras som en enhet för nätverks genomsökning av användaren. |
| Är programmerings enhet | Definieras som en auktoriserad programmerings enhet av användaren. <br />- **True**: enheten utför programmerings aktiviteter för PLCs, RTUs och kontrollanter, som är relevanta för teknik stationer. <br />- **Falskt**: enheten är ingen programmerings enhet. |
| Grupper | De grupper som enheten ingår i. |
| Senaste aktivitet | Den senaste aktivitet som enheten utförde. |
| Discovered | När den här enheten först setts i nätverket. |

Så här visar du enhets inventeringen:

1. Välj **enheter** i den vänstra rutan. Fönstret **enheter** öppnas till höger.

2. I fönstret **enheter** väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

För att dölja och Visa kolumner, anpassa tabellen enhets inventering:

1. På den övre högra menyn i enhets inventeringen väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Skärmen Inställningar för enhets inventering.":::

2. I fönstret **enhets lager inställningar** väljer du de kolumner som du vill visa i enhets inventerings tabellen.

3. Ändra placeringen av kolumnerna i tabellen med hjälp av pilarna.

4. Välj **Spara**. Fönstret **enhets lager inställningar** stängs och de nya inställningarna visas i tabellen.

### <a name="create-temporary-device-inventory-filters"></a>Skapa temporära enhets lager filter

Du kan ange ett filter som definierar vilken information som visas i tabellen. Du kan till exempel bestämma att du bara vill visa information om PLC-enheten.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Enheter inlärning.":::

Filtret sparas inte när du lämnar lagret.

### <a name="save-device-inventory-filters"></a>Spara enhets inventerings filter

Du kan spara ett filter eller en kombination av filter som du behöver och tillämpa dem igen i enhets inventeringen. Skapa bredare filter baserat på en viss enhets typ eller mer smala filter baserat på en specifik typ och ett särskilt protokoll.

De filter du sparar sparas också som enhets mappnings grupper. Den här funktionen ger ytterligare en nivå av granularitet för att Visa nätverks enheter på kartan.

Så här skapar du filter:

1. I den kolumn som du vill filtrera väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. Välj filter typ i dialog rutan **filter** :

   - **Lika med**: det exakta värde enligt vilket du vill filtrera kolumnen. Om du till exempel filtrerar kolumnen protokoll efter **lika med** och `value=ICMP` , kommer kolumnen att visa enheter som bara använder ICMP-protokollet.

   - **Contains**: det värde som finns bland andra värden i kolumnen. Om du till exempel filtrerar kolumnen protokoll enligt **contains** och, visar `value=ICMP` kolumnen enheter som använder ICMP-protokollet som en del av listan över protokoll som enheten använder.

3. Om du vill ordna kolumn informationen enligt alfabetisk ordning väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Ordna ordningen genom att välja :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: pilarna och :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Om du vill spara ett nytt filter definierar du filtret och väljer **Spara som**.

5. Om du vill ändra filter definitionerna ändrar du definitionerna och väljer **Spara ändringar**.

Så här visar du filter:

- Öppna det vänstra fönstret och Visa de filter som du har sparat:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Visa filtren i det vänstra fönstret.":::

### <a name="view-filtered-information-as-a-map-group"></a>Visa filtrerad information som en kart grupp

När du växlar till vyn karta är de filtrerade enheterna markerade och filtrerade. Den filter grupp som du sparade visas på sido menyn under filter gruppen **enhets inventering** .

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Visa filter när du är i Map-vyn.":::

## <a name="learn-windows-registry-details"></a>Lär dig mer om registret i Windows

Förutom Learning-enheter kan du identifiera Microsoft Windows-arbetsstationer och-servrar. Enheterna visas också i enhets inventeringen. När du har lärt enheterna kan du utöka enhets inventeringen med detaljerad Windows-information, till exempel:

- Windows-version installerad

- Installerade program

- Information om korrigerings nivå

- Öppna portar

- Mer robust information om OS-versioner

Det finns två alternativ för att hämta den här informationen:

- Aktiv avsökning genom att använda schemalagda WMI-sökningar. 

- Lokal undersökning genom att distribuera och köra ett skript på enheten. Att arbeta med lokala skript kringgår riskerna med att köra WMI-avsökning på en slut punkt. Det är också användbart för reglerade nätverk med vattenfall och envägs element.

Den här artikeln beskriver hur du lokalt undersökar registret i Windows-slutpunkten med ett skript. Den här informationen används för att generera aviseringar, meddelanden, rapporter om Data utvinning, riskbedömningar och angrepps vektor rapporter.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Skärm bild för Data utvinning.":::

Du kan undersöka följande Windows-operativ system:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Innan du börjar

Om du vill arbeta med skriptet måste du uppfylla följande krav:

- Administratörs behörighet krävs för att köra skriptet på enheten.

- Sensorn bör redan ha lärt Windows-enheten. Det innebär att om enheten redan finns kommer skriptet att hämta sin information.

- En sensor övervakar nätverket som Windows-datorn är ansluten till.

### <a name="acquire-the-script"></a>Hämta skriptet

[Kontakta kund support](mailto:support.microsoft.com)om du vill ta emot skriptet.

### <a name="deploy-the-script"></a>Distribuera skriptet

Du kan distribuera skriptet en gång eller schemalägga pågående frågor med hjälp av standard metoder och verktyg för automatisk distribution.

### <a name="about-the-script"></a>Om skriptet

- Skriptet körs som ett verktyg och inte ett installerat program. Körningen av skriptet påverkar inte slut punkten.

- Filerna som skriptet genererar stannar kvar på den lokala enheten tills du tar bort dem.

- Filerna som skriptet genererar finns bredvid varandra. Avgränsa dem inte.

- Om du kör skriptet igen på samma plats skrivs filerna över.

Kör skriptet så här:  

1. Kopiera skriptet till en lokal enhet och packa upp det. Följande filer visas:

    - start.bat

    - settings.jspå

    - data. bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Vy över filerna i Utforskaren.":::

2. Kör `run.bat` filen.

3. När registret har avsökats visas filen CX-Snapshot med register informationen.

4. Fil namnet anger system namn och datum och tid för ögonblicks bilden. Ett exempel på ett fil namn är `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Importera enhets information

Information som lärts på varje slut punkt ska importeras till sensorn.

Filer som genereras från frågorna kan placeras i en mapp som du kan komma åt från sensorer. Använd standard metoder och automatiserade verktyg för att flytta filerna från varje Windows-slutpunkt till den plats där du importerar dem till sensorn.

Uppdatera inte fil namn.

För att importera:

1. Välj **import inställningar** i dialog rutan **Importera Windows-konfiguration** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importera dina Windows-konfigurationer.":::

2. Välj **Lägg till** och välj sedan alla filer (Ctrl + A).

3. Välj **Stäng**. Enhetens register information importeras. Om det uppstår problem med att ladda upp en av filerna kommer du att bli informerad om vilken fil uppladdning som misslyckades.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="Överföringen av de tillagda filerna lyckades.":::

## <a name="export-device-inventory-information"></a>Exportera information om enhets inventering

Du kan exportera enhets inventerings information till en Excel-fil.

Så här exporterar du en CSV-fil:

- På den övre högra menyn i enhets inventeringen väljer du :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . CSV-rapporten genereras och hämtas.

## <a name="see-also"></a>Se även

[Undersök alla identifieringar av företagssensorer i en enhetsinventering](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Arbeta med vyer för webbplats översikt](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
