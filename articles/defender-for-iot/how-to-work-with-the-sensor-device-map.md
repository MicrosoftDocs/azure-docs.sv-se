---
title: Arbeta med sensor enhets kartan
description: Enhets kartan ger en grafisk representation av nätverks enheter som har identifierats. Använd kartan för att analysera och hantera enhets information, nätverks segment och generera rapporter.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: dd0f28411dccab1a1dd36fbd01d2d1acbcc689e0
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843859"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Undersök sensor identifieringar i enhets kartan

Enhets kartan ger en grafisk representation av nätverks enheter som har identifierats. Använd kartan för att:

  - Hämta, analysera och hantera enhets information.

  - Analysera nätverks segment, till exempel specifika grupper av intresse-eller Purdue lager.

  - Generera rapporter, till exempel exportera enhets information och sammanfattningar.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Skärm bild av enhets kartan.":::

För att få åtkomst till kartan:

  - Välj **enhets karta** från huvud skärmen i konsolen.

## <a name="map-search-and-layout-tools"></a>Verktyg för kart sökning och layout

Följande verktyg används för att arbeta i kartan.

Användar rollen avgör vilka verktyg som är tillgängliga i enhets kartans fönster. Mer information om användar roller finns i [skapa och hantera användare](how-to-create-and-manage-users.md) .

| Symbol | Beskrivning |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Sök efter IP-adress eller MAC-adress för en speciell enhet. Ange IP-adressen eller MAC-adressen i text rutan. Kartan visar den enhet som du sökte efter med enheter som är anslutna till den. |
| Grupp markering och filter <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Skärm bild av gruppens högdagrar och filter."::: | Filtrera eller markera kartan baserat på standard och anpassade enhets grupper. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | DEN döljer vyn, för att aktivera en fokuserad vy på enhets Visa enheter och gruppera IT-enheter.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Behåll den aktuella enhets ordningen i kartan. Om du till exempel drar enheter till nya platser på kartan finns enheterna kvar på dessa platser när kartan avslutas. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Anpassa till skärm |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | – Visa Purdue-skiktet som identifieras för den här enheten, inklusive automatisk, process kontroll, övervakare och företag <br /> – Visa anslutningar mellan enheter.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Visa eller Dölj mellan broadcast och multicast. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Filtrera enheterna på kartan enligt den tidpunkt de senast kommunicerar med andra enheter. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="ansökningar" border="false"::: | Visa meddelanden om en enhet. Till exempel om en ny IP-adress har identifierats för en enhet med hjälp av en befintlig MAC-adress |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Export" border="false"::: | Exportera/importera enhets information. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="egenskaperna" border="false"::: | Visa grundläggande enhets egenskaper för valda enheter. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Zooma in" border="false"::: eller :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="ut" border="false"::: | Zooma in eller ut ur enheter i kartan. |

## <a name="view-ot-elements-only"></a>Visa endast objekt med element

Som standard aggregeras IT-enheter automatiskt efter undernät, så att kart visningen fokuserar på Nätverks-och ICS-nätverk. Presentationen av IT-nätverkets nätverks element minimeras till ett minimum, vilket minskar det totala antalet enheter som visas på kartan och ger en tydlig bild av nätverks elementen objekt och ICS.

Varje undernät presenteras som en enskild enhet på enhets kartan, inklusive en interaktiv och utöknings möjlighet att titta på information om ett IT-undernät och tillbaka.

Figuren nedan visar ett minimerat IT-undernät med 27-nätverks element.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet.png" alt-text="minimerat IT-undernät med 27 IT-nätverks element":::

Så här aktiverar du funktionen för att dölja IT-nätverk:

- I fönstret systeminställning kontrollerar du att funktionen IT-nätverk är aktive rad.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="System inställnings fönster":::

Expandera ett IT-undernät:

1. Om du vill skilja mellan IT-och nätverksbaserade nätverk väljer du **undernät** på skärmen system inställningar.

   > [!NOTE]
   > Vi rekommenderar att du namnger varje undernät med meningsfulla namn på användaren enkelt kan identifiera för att skilja mellan IT-nätverk.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Konfiguration av undernät":::

2. I fönstret Redigera undernäts konfiguration rensar du ICS-undernätet för varje undernät som du vill definiera som ett IT-undernät. IT-undernätet visas minimerat i enhets kartan med meddelanden om ICS-enheter, till exempel en kontrollant eller PLC, i IT-nätverk.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Redigera undernät-konfiguration":::

3. Om du vill expandera IT-nätverket på kartan högerklickar du på det i fönstret enheter och väljer **expandera nätverk**.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Expandera din vy över nätverket.":::

4. En bekräftelse ruta visas där du får ett meddelande om att layoutändringar inte kan återställas.

5. Välj **OK**. Elementen för IT-undernätet visas på kartan.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Komprimera ett IT-undernät:

1.  Välj **enheter** i den vänstra rutan.

2. I fönstret enheter väljer du ikonen Dölj. Talet i rött anger hur många expanderade undernät som visas på kartan.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Enhets fönster":::

3. Välj de undernät som du vill minimera eller Välj **Dölj alla**. Det valda under nätet visas minimerat på kartan.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Dölj alla":::

Ikonen komprimera uppdateras med det uppdaterade antalet expanderade under nät.

## <a name="view-or-highlight-device-groups"></a>Visa eller markera enhets grupper

Du kan anpassa kart visningen baserat på enhets grupper. Till exempel grupper av enheter som är associerade med ett angivet VLAN eller undernät. Fördefinierade grupper är tillgängliga och anpassade grupper kan skapas.

Visa grupper efter:

  - **Markering:** Markera de enheter som tillhör en speciell grupp i blått.

  - **Filtrering:** Visa endast enheter på kartan som tillhör en speciell grupp.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Standardvy för porten":::

Följande fördefinierade grupper är tillgängliga:

| Gruppnamn | Beskrivning |
|--|--|
| **Kända program eller icke-standrad portar (standard)** | Enheter som använder reserverade portar, till exempel TCP. Enheter som använder andra portar än standard portar eller portar som inte har tilldelats ett alias. |
| **Överliggande protokoll (standard)** | Enheter som hanterar den andra trafiken. |
| **Auktorisering (standard)** | Enheter som identifierades i nätverket under inlärnings processen eller som officiellt har lagts till i nätverket |
| **Enhets inventerings filter** | Enheter grupperade enligt de filter som sparas i enhets inventerings tabellen. |
| **Avsöknings intervall** | Enheter grupperade efter avsöknings intervall. Avsöknings intervallen genereras automatiskt enligt cykliska kanaler eller punkter. Till exempel 15,0 sekunder, 3,0 sekunder, 1,5 sekunder eller intervall. Genom att granska den här informationen kan du lära dig om system avsökers för snabbt eller långsamt. |
| **Programmeringsspråk** | Teknik stationer och programmerade styrenheter |
| **Undernät** | Enheter som tillhör ett speciellt undernät. |
| **LOKALT** | Enheter som är associerade med ett angivet VLAN-ID. |
| **Anslutning mellan undernät** | Enheter som är associerade med anslutning mellan undernät. |
| **Fästa aviseringar** | Enheter som användaren har fäst en avisering för. |
| **Simuleringar av attack vektor** | Sårbara enheter identifierade i angrepps vektor rapporter. För att kunna visa dessa enheter på kartan markerar du kryss rutan **Visa i enhets karta** när du genererar angrepps vektorn. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Lägga till simuleringar för attack vektor"::: |
| **Senast observerad** | Enheter grupperade efter den tidsram som senast setts, t. ex. en timme, sex timmar, en dag, sju dagar. |
| **Inte i Active Directory** | Alla icke-PLC-enheter som inte kommunicerar med Active Directory. |

För att markera eller filtrera enheter:

1. Välj **enhets karta** på menyn på sidan.

2. Välj filter ikonen. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Meny":::

3. I fönstret grupper väljer du den grupp som du vill markera eller filtrera enheter.

4. Välj **Markera** eller **filtrera**.

## <a name="define-custom-groups"></a>Definiera anpassade grupper

Förutom att Visa fördefinierade grupper kan du definiera anpassade grupper. Grupperna visas i rapporterna enhets karta, enhets inventering och data utvinning.

> [!NOTE]
> Du kan också skapa grupper från enhets inventeringen.

Så här skapar du en grupp:

1. Välj **enheter** på menyn på sidan. Enhets kartan visas.

2. Välj :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="grupp inställning"::: för att Visa grupp inställningarna.

3. Välj :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="grupper"::: för att skapa en ny anpassad grupp.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Skapa en anpassad grupp skärm":::

4. Lägg till namnet på gruppen och Använd upp till 30 tecken.

5. Välj relevanta enheter enligt följande:

   - Lägg till enheterna från den här menyn genom att välja dem i listan (Välj på pilknappen)<br /> Eller: <br /> 
   - Lägg till enheterna från den här menyn genom att kopiera dem från en vald grupp (Välj på pilknappen)

6. Välj **Lägg till grupp**.

### <a name="add-devices-to-a-custom-group"></a>Lägga till enheter i en anpassad grupp

Du kan lägga till enheter i en anpassad grupp eller skapa en ny anpassad grupp och enheten.

1. Högerklicka på en eller flera enheter på kartan.

2. Välj **Lägg till i grupp**.

3. Ange ett grupp namn i fältet grupp och välj +. Den nya gruppen visas. Om gruppen redan finns kommer den att läggas till i den befintliga anpassade gruppen.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Gruppnamn":::

4. Lägg till enheter i en grupp genom att upprepa steg 1-3.

## <a name="map-zoom-views"></a>Mappar med zoomnings läge

Genom att arbeta med Map-vyer kan du påskynda data utredning när du analyserar stora nätverk.

Tre enhets informations visningar kan visas:

  - [Fågel ögon-vy](#birds-eye-view)

  - [Enhets typ och anslutnings visning](#device-type-and-connection-view)

  - [Detaljerad vy](#detailed-view)

### <a name="birds-eye-view"></a>Fågel ögon-vy

Den här vyn ger en översikt över enheter som representeras på följande sätt:

  - Röda punkter visar enheter med varning (ar)

  - Stjärnmärkt punkter visar vilka enheter som marker ATS som viktiga

  - Svarta punkter visar enheter utan aviseringar

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Fågel ögon-vy":::

### <a name="device-type-and-connection-view"></a>Enhets typ och anslutnings visning 

Den här vyn visar enheter som visas som ikoner på kartan för att markera enheter med aviseringar, enhets typer och anslutna enheter.

  - Enheter med aviseringar visas med en röd ring

  - Enheter utan aviseringar visas med en grå ring

  - Enheter som visas som stjärnor har marker ATS som viktiga

Ikonen enhets typ visas med anslutna enheter.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="vyn anslutning":::

### <a name="detailed-view"></a>Detaljerad vy 

I den detaljerade vyn presenteras enheter och enhets etiketter och indikatorer med följande information:

:::image type="content" source="media/how-to-work-with-maps/device-map.png" alt-text="Detaljerad vy":::

### <a name="control-the-zoom-view"></a>Styr vyn zooma

Den kartdata som visas beror på kart zoomnings nivån. Att växla mellan kartdata görs genom att ändra zoomnings nivåerna.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Styr vyn zooma":::

### <a name="enable-simplified-zoom-views"></a>Aktivera förenklade zoom-vyer

Administratörer som vill ha säkerhetsanalytiker och RO-användare som har åtkomst till Brid-ögon-och enhets-och typ anslutnings vyer bör aktivera alternativet förenklad vy.

Så här aktiverar du förenklade kart lägen:

  - Välj **Systeminställningar** och växla sedan alternativet för **förenklad kart visning** .

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Förenkla kart visning":::

## <a name="learn-more-about-devices"></a>Läs mer om enheter

Det finns en mängd olika verktyg för att lära dig mer om enheter som utgör enhets kartan:

- [Enhets etiketter och indikatorer](#device-labels-and-indicators)

- [Snabb vyer för enhet](#device-quick-views)

- [Visa och hantera enhets egenskaper](#view-and-manage-device-properties)

- [Visa enhets typer](#view-device-types)

- [Bakplan](#backplane-properties)

- [Visa en tids linje med händelser för enheten](#view-a-timeline-of-events-for-the-device)

- [Analysera programmerings information och ändringar](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Enhets etiketter och indikatorer

Följande etiketter och indikatorer kan visas på enheter på kartan:

| Enhets etikett | Beskrivning |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP-värdnamn"::: | IP-adressens värdnamn och IP-adress eller undernäts adresser |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Antal aviseringar"::: | Antal aviseringar som är associerade med enheten |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Enhets typ ikon, till exempel Storage, PLC eller historian. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="grupperade enheter"::: | Antal enheter grupperade i ett undernät i ett IT-nätverk. I det här exemplet 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="enhets inlärnings period"::: | En enhet som upptäcktes efter inlärnings perioden och inte auktoriserats som en nätverks enhet. |
| Heldragen linje | Logisk anslutning mellan enheter |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Ny enhet"::: | Den nya enheten har identifierats efter att inlärningen är slutförd. |

### <a name="device-quick-views"></a>Snabb vyer för enhet

Åtkomst till enhets egenskaper och anslutningar från kartan.

Så här öppnar du menyn snabb egenskaper:

  - Välj :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="snabb menyerna":::snabb egenskaper på snabb menyn.

#### <a name="quick-device-properties"></a>Egenskaper för snabb enhet

Välj en enhet eller flera enheter medan snabb egenskaper-skärmen är öppen för att se de enheternas högdagrar:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Egenskaper för snabb enhet":::

#### <a name="quick-connection-properties"></a>Egenskaper för snabb anslutning

Välj en anslutning när skärmen snabb egenskaper är öppen för att se de protokoll som används i den här anslutningen och när de senast setts:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Egenskaper för snabb anslutning":::

## <a name="view-and-manage-device-properties"></a>Visa och hantera enhets egenskaper

Du kan visa enhets egenskaper för varje enhet som visas på kartan. Till exempel enhets namnet, typen eller operativ systemet eller den inbyggda program varan eller leverantören.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Visa och hantera enhets egenskaper":::

Följande information kan uppdateras manuellt. Information som anges manuellt åsidosätter information som identifieras av Defender för IoT.

  - Namn

  - Typ

  - Operativsystem

  - Purdue-skikt

  - Beskrivning

| Objekt | Beskrivning |
|--|--|
| Grundläggande information | Den grundläggande information som krävs. |
| Namn | Enhetens namn. <br /> Sensorn identifierar som standard enhets namnet som det definieras i nätverket. Till exempel ett namn som definierats i DNS-servern. <br /> Om inga sådana namn har definierats visas enhetens IP-adress i det här fältet. <br /> Du kan ändra ett enhets namn manuellt. Ge dina enheter meningsfulla namn som återspeglar deras funktioner. |
| Typ | Enhets typen som identifierades av sensorn. <br /> Mer information finns i [Visa enhets typer](#view-device-types). |
| Leverantör | Enhets leverantören. |
| Operativsystem | Enhetens operativ system. |
| Purdue-skikt | Det Purdue-skikt som identifieras av sensorn för den här enheten, inklusive: <br /> -Automatisk <br /> -Process kontroll <br /> – Övervakare <br /> – Enterprise |
| Beskrivning | Ett fritext fält. <br /> Lägg till mer information om enheten. |
| Attribut | Eventuell ytterligare information som identifierades om enheten under inlärnings perioden och som inte tillhör andra kategorier visas i avsnittet attribut. <br /> Informationen är RO. |
| Inställningar | Du kan ändra enhets inställningarna manuellt för att förhindra falska positiva identifieringar: <br /> - **Auktoriserad enhet**: under inlärnings perioden identifieras alla enheter som identifieras i nätverket som auktoriserade enheter. När en enhet identifieras efter inlärnings perioden visas den som en oauktoriserad enhet som standard. Du kan ändra den här definitionen manuellt. <br /> - **Kallas skanner**: aktivera det här alternativet om du vet att enheten är känd som skanner och det inte finns något behov av att varna dig om den. <br /> - **Programmerings enhet**: aktivera det här alternativet om du vet att enheten är känd som en programmerings enhet och det inte finns något behov av att varna dig om den. |
| Anpassade grupper | De anpassade grupperna i enhets kartan där enheten ingår. |
| Tillstånd | Enhetens säkerhet och status för auktorisering: <br /> -Statusen är `Secured` när det inte finns några aviseringar <br /> – När det finns aviseringar om enheten visas antalet aviseringar <br /> – Statusen `Unauthorized` visas för enheter som har lagts till i nätverket efter inlärnings perioden. Du kan definiera enheten manuellt som `Authorized Device` i inställningarna <br /> – Om den här enhetens adress definieras som en dynamisk adress `DHCP` läggs den till i status. |


| Nätverk | Beskrivning |
|--|--|
| Gränssnitt | Enhetens gränssnitt. Ett RO-fält. |
| Protokoll | De protokoll som används av enheten. Ett RO-fält. |
| Inbyggd programvara | Om det finns information om displaner-information kommer den inbyggda program varan inte att visas. |
| Adress | Enhetens IP-adress. |
| Nummer | Enhetens serie nummer. |
| Modulens adress | Enhets modell och fack nummer eller ID. |
| Modell | Enhetens modell nummer. |
| Version av inbyggd programvara | Versions numret för den inbyggda program varan. |

Så här visar du enhets informationen:

1. Välj **enheter** på menyn på sidan.

2. Högerklicka på en enhet och välj **Visa egenskaper**. Enhets Fönstret Egenskaper visas.

3. Välj den begärda aviseringen längst ned i det här fönstret om du vill visa detaljerad information om aviseringar för den här enheten.

### <a name="view-device-types"></a>Visa enhets typer

Enhets typen identifieras automatiskt av sensorn under enhets identifierings processen. Du kan ändra typen manuellt.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Visa enhets typer":::

Följande tabell visar alla typer i systemet:

| Kategori | Enhetstyp |
|--|--|
| DENNA | Teknik Station <br /> FOTOGRAF <br />Historian <br />HMI <br />IED <br />DCS-kontrollant <br />RTU <br />System för industriella förpackningar <br />Industriell skala <br />Industriell robot <br />Plats <br />Mätare <br />Variabel frekvens enhet  <br />Robot kontroll enhet <br />Servisk enhet <br />Pneumatisk enhet <br />Ramen |
| IT | Domänkontrollant <br />DB-server <br />Arbetsstation <br />Server <br />Terminal Station <br />Lagring <br />Mobil telefon <br />Surfplatta <br />Säkerhets kopierings Server |
| IoT | IP-kamera <br />Skrivare  <br />Håls lags klocka <br />ATM <br />Smart TV <br />Spel konsol <br />DVR <br />Dörr kontroll panel <br />HVAC <br />Termostat <br />Brand larm <br />Smart ljus <br />Smart växel <br />Brand detektor <br />IP-telefon <br />Larm system <br />Alarm siren <br />Rörelsedetektor <br />Hissen <br />Fuktighets sensor <br />Streckkodsläsare <br />Avbrotts fri ström källa <br />Person räknar system <br />Intercom <br />Turnstile |
| Nätverk | Trådlös åtkomst punkt <br />Router <br />Switch <br />Brandvägg <br />VPN Gateway <br />NTP-server <br />WiFi ananas <br />Fysisk plats <br />I/O-kort <br /> Protokoll konverterare |

Så här visar du enhets informationen:

1.  Välj **enheter** på menyn på sidan.

2. Högerklicka på en enhet och välj **Visa egenskaper**. Enhets Fönstret Egenskaper visas.

3. Välj den begärda aviseringen längst ned i det här fönstret om du vill visa detaljerad information om aviseringar för den här enheten.

### <a name="backplane-properties"></a>Egenskaper för bakplan

Om en PLC innehåller flera moduler som är åtskilda i rack och fack, kan egenskaperna variera mellan modulens kort. Om till exempel IP-adressen och MAC-adressen är desamma, kan den inbyggda program varan vara annorlunda.

Du kan använda alternativet för att använda funktionen för att granska flera styrenheter/kort och deras kapslade enheter som en enhet med en mängd olika definitioner. Varje plats i vyn bakplan representerar de underliggande enheterna – de enheter som upptäcktes bakom den.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Egenskaper för bakplan":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Disponera enhets egenskaper":::

En bakplan kan innehålla upp till 30 styrenhets kort och upp till 30 rack enheter. Det totala antalet enheter som ingår i flera nivåer kan vara upp till 200 enheter.

Fönstret bakplan visas i enhets Fönstret Egenskaper när information om displaner identifieras.

Varje plats visas med antalet underliggande enheter och den ikon som visar modultypen.

| Ikon | Modultyp |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Strömförsörjning"::: | Strömförsörjning |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="Analog I/O"::: | Analog I/O |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Kommunikations kort"::: | Kommunikations kort |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="Digital I/O"::: | Digital I/O |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="Processor"::: | Processor |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Allmänna"::: | Allmänna |

När du väljer en plats visas plats informationen:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="Välj en plats":::

Om du vill visa de underliggande enheterna bakom facket väljer du **Visa på karta**. Facket visas i enhets kartan med alla underliggande moduler och enheter som är anslutna till den.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="VISA PÅ KARTAN":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Visa en tids linje med händelser för enheten

Visa en tids linje med händelser som är associerade med en enhet.

Så här visar du tids linjen:

1. Högerklicka på en enhet från kartan.

2. Välj **Visa händelser**. Fönstret händelse tids period öppnas med information om händelser som identifierats för den valda enheten.

Mer information finns i [händelsens tids linje](#event-timeline) .

## <a name="analyze-programming-details-and-changes"></a>Analysera programmerings information och ändringar

Förbättra data utredning genom att Visa programmerings händelser som utförs på dina nätverks enheter och analysera kod ändringar. Den här informationen hjälper dig att identifiera misstänkt programmerings aktivitet, till exempel:

  - Mänskligt fel: en tekniker programmerar fel enhet.

  - Skadad programmerings automatisering: programmeringen utförs felaktigt på grund av automation-fel.

  - Hackade system: obehöriga användare har loggat in på en programmerings enhet.

Du kan visa en programmerad enhet och bläddra igenom olika programmerings ändringar som utförs på den av andra enheter.

Visa kod som har lagts till, ändrats eller tagits bort eller som inte har ändrats av programmerings enheten. Sök efter programmerings ändringar baserat på filtyper, datum eller tider av intresse.

### <a name="when-to-review-programming-activity"></a>När du ska granska programmerings aktivitet 

Du kan behöva granska programmerings aktivitet:

  - När du har läst en avisering om otillåten programmering

  - Efter en planerad uppdatering av kontrollanter

  - När en process eller dator inte fungerar som den ska (för att se vem som utförde den senaste uppdateringen och när)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Program ändrings logg":::

Med ytterligare alternativ kan du:

  - Markera händelser av intresse med en stjärna.

  - Hämta en *. txt-fil med den aktuella koden.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Om auktoriserade eller obehöriga programmerings händelser 

Obehöriga programmerings händelser utförs av enheter som inte har lärts eller definierats manuellt som programmerings enheter. Auktoriserade programmerings händelser utförs av enheter som har lösts eller definierats manuellt som programmerings enheter.

I fönstret programmerings analys visas både godkända och obehöriga programmerings händelser.

### <a name="accessing-programming-details-and-changes"></a>Åtkomst till programmerings information och ändringar

Öppna programmerings analys fönstret från:

- [Händelse tids linje](#event-timeline)

- [Obehöriga programmerings aviseringar](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Händelse tids linje

Använd händelsens tids linje för att visa en tids linje med händelser där programmerings ändringar har upptäckts.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="En vy över händelsens tids linje.":::

### <a name="unauthorized-programming-alerts"></a>Obehöriga programmerings aviseringar

Aviseringar utlöses när obehöriga programmerings enheter utför programmerings aktiviteter.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Obehöriga programmerings aviseringar":::

> [!NOTE]
> Du kan också visa grundläggande information om programmering i enhets Fönstret Egenskaper och enhets inventering. Se [information om enhets programmering: ytterligare platser](#device-programming-information-additional-locations) för mer information.

### <a name="working-in-the-programming-timeline-window"></a>Arbeta i tids linje fönstret för programmering

I det här avsnittet beskrivs hur du visar programfiler och jämför versioner. Sök efter vissa filer som har skickats till en programmerad enhet. Sök efter filer baserat på:

  - Date

  - Filtyp

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="tids linje fönster för programmering":::

|Tids linje typ för programmering | Beskrivning |
|--|--|
| Programmerad enhet | Innehåller information om den enhet som har programmerats, inklusive värdnamn och fil. |
| Senaste händelser | Visar de 50 senaste händelserna som identifierats av sensorn. <br />För att markera en händelse, Hovra över den och klicka på stjärnan. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> De senaste 50 händelserna kan visas. |
| Files | Visar de filer som identifierats för det valda datumet och fil storleken på den programmerade enheten. <br /> Som standard är det maximala antalet filer som är tillgängliga för visning per enhet 300. <br /> Som standard är den maximala fil storleken för varje fil 15 MB. |
| Fil status :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Fil etiketter anger status för filen på enheten, inklusive: <br /> **Tillagt**: filen lades till i slut punkten vid vald datum eller tid. <br /> **Uppdaterad**: filen uppdaterades vid det datum eller den tid som valts. <br /> **Borttagen**: den här filen har tagits bort. <br /> **Ingen etikett**: filen ändrades inte.   |
| Programmerings enhet | Enheten som gjorde programmerings ändringen. Flera enheter kan ha utfört programmerings ändringar på en program enhet. Värdnamn, datum eller tid för ändring och inloggad användare visas. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Visar den aktuella filen som är installerad på den programmerade enheten. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Ladda ned en textfil med den kod som visas. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Jämför den aktuella filen med filen som har identifierats på ett valt datum. |

### <a name="choose-a-file-to-review"></a>Välj en fil att granska

I det här avsnittet beskrivs hur du väljer en fil som ska granskas.

Så här väljer du en fil som ska granskas:

1. Välj en händelse i fönstret **senaste händelser**

2. Välj en fil som utgör fil fönstret. Filen visas i det aktuella fönstret.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Välj den fil som du vill arbeta med.":::

### <a name="compare-files"></a>Jämför filer

I det här avsnittet beskrivs hur du jämför programfiler.

Att jämföra:

1. Välj en händelse i fönstret senaste händelser.

2. Välj en fil i rutan fil. Filen visas i det aktuella fönstret. Du kan jämföra den här filen med andra filer.

3. Välj jämförelse indikator.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Jämför indikator":::

   I fönstret visas alla datum då den valda filen identifierades på den programbaserade enheten. Filen kan ha uppdaterats på den programmerade enheten av flera programmerings enheter.

   Antalet skillnader som identifieras visas i det övre högra hörnet i fönstret. Du kan behöva rulla nedåt för att se skillnaderna.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="Rulla ned till ditt val":::

   Talet beräknas efter intilliggande rader med ändrad text. Om till exempel åtta efterföljande rader med kod har ändrats (tas bort, uppdaterats eller lagts till), kommer detta att beräknas som en skillnad.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="Vyn tids linje för program.":::

4. Välj ett datum. Filen som identifieras på det valda datumet visas i fönstret.

5. Filen som väljs från fönstret senaste händelser/filer visas alltid till höger.

### <a name="device-programming-information-additional-locations"></a>Information om enhets programmering: ytterligare platser

Förutom att granska information i tids linjen för programmering, kan du komma åt programmerings information i enhets Fönstret Egenskaper och enhets inventeringen.

| Enhetstyp | Beskrivning |
|--|--|
| Enhetsegenskaper | Fönstret enhets egenskaper innehåller information om den senaste programmerings händelsen som identifierats på device\. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Enhetens egenskaper"::: |
| Enhets inventeringen | Enhets inventeringen visar om enheten är en programmerings device\. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Inventering av enheter"::: |

## <a name="manage-device-information-from-the-map"></a>Hantera enhets information från kartan

Sensorn uppdaterar eller påverkar inte enheter direkt i nätverket. Ändringar som görs här påverkar bara hur analyserar enheten.

### <a name="delete-devices"></a>Ta bort enheter

Du kanske vill ta bort en enhet om den information som har lärts inte är relevant. Exempel:

  - En partner entreprenör vid en teknisk arbets Station ansluter för att utföra konfigurations uppdateringar. När uppgiften har slutförts bör enheten inte längre övervakas.

  - På grund av ändringar i nätverket är vissa enheter inte längre anslutna.

Om du inte tar bort enheten fortsätter sensorn att övervaka den. Efter 60 dagar visas ett meddelande som rekommenderar att du tar bort.

Du kan få en avisering som anger att enheten inte svarar om en annan enhet försöker komma åt den. I så fall kan nätverket vara felkonfigurerat.

Enheten tas bort från enhets kartan, enhets inventeringen och data utvinnings rapporterna. Annan information, till exempel: information som lagras i widgetar kommer att behållas.

Enheten måste vara aktiv i minst 10 minuter för att ta bort den.

Så här tar du bort en enhet från enhets kartan:

1. Välj **enheter** på menyn på sidan.

2. Högerklicka på en enhet och välj **ta bort**.

### <a name="merge-devices"></a>Slå samman enheter

Under vissa omständigheter kan du behöva slå samman enheter. Detta kan krävas om sensorn identifierade separata nätverks enheter som är en unik enhet. Exempel:

  - En PLC med fyra nätverkskort

  - En bärbar dator med WIFI och fysiskt kort

Vid sammanslagning instruerar du sensorn att kombinera enhets egenskaperna för två enheter till en. När du gör det uppdateras enhets Fönstret Egenskaper och sensor rapporter med den nya informationen om enhets egenskaper.

Om du till exempel sammanfogar två enheter med en IP-adress visas båda IP-adresserna som separata gränssnitt i enhets Fönstret Egenskaper. Du kan endast sammanfoga auktoriserade enheter.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Enhets Fönstret Egenskaper":::

Händelsens tids linje presenterar sammanslagnings händelsen.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Händelse tids linjen med sammanfogade händelser.":::

Det går inte att ångra en enhets sammanslagning. Om du av misstag har sammanfogat två enheter tar du bort enheten och väntar på att sensorn ska identifiera båda igen.

Så här sammanfogar du enheter:

1. Välj två enheter och högerklicka på en av dem.

2. Välj **sammanfoga** för att sammanfoga enheterna. Det kan ta upp till 2 minuter att slutföra sammanslagningen.

3. I dialog rutan Ange attribut för sammanfogad enhet väljer du ett enhets namn.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="dialog rutan attribut":::

4. Välj **Spara**.

### <a name="authorize-and-unauthorize-devices"></a>Auktorisera och avauktorisera enheter

Under inlärnings perioden identifieras alla enheter som identifierats i nätverket som auktoriserade enheter. Den **auktoriserade** etiketten visas inte på dessa enheter i enhets kartan.

När en enhet identifieras efter inlärnings perioden visas den som en oauktoriserad enhet. Förutom att Visa oauktoriserade enheter i kartan kan du också se dem i enhets inventeringen.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Enhets inventering":::

**Ny enhet mot obehörig**

Nya enheter identifieras efter att inlärnings perioden visas med en `New` och- `Unauthorized` etikett.

Om du flyttar en enhet på kartan eller ändrar enhets egenskaperna manuellt, `New` tas etiketten bort från enhets ikonen.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Obehöriga enheter – angrepps vektorer och risk utvärderings rapporter

Oauktoriserade enheter beräknas i rapporter om riskhantering och angrepps vektorer.

- **Angrepps vektor rapporter:** Enheter som har marker ATS som otillåtna löses i angrepps vektorn som misstänkta falska enheter som kan vara ett hot mot nätverket.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Tillståndsvy dina angrepps vektor rapporter":::

- **Riskbedömnings rapporter:** Enheter som har marker ATS som otillåtna är:

  - Identifierade i riskbedömnings rapporter

Så här auktoriserar eller avauktoriserar du enheter manuellt:

1. Högerklicka på enheten på kartan och välj **unauktorisering**

### <a name="mark-devices-as-important"></a>Markera enheter som viktiga

Du kan markera betydande nätverks enheter som viktiga, till exempel affärs kritiska servrar. Dessa enheter markeras med en stjärna på kartan. Stjärnan varierar beroende på kartans zoomnings nivå.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Viktiga enheter – angrepps vektorer och risk utvärderings rapporter

Viktiga enheter beräknas när rapporter om riskbedömning och angrepps vektorer skapas.

  - Angrepps vektor rapporter enheter som marker ATS som viktiga lösas i angrepps vektorn som angrepps mål. 

  - Rapporter om riskbedömning: enheter som har marker ATS som viktiga beräknas när man tillhandahåller säkerhets poängen i riskbedömnings rapporten.

## <a name="generate-activity-reports-from-the-map"></a>Skapa aktivitets rapporter från kartan

Skapa en aktivitets rapport för en vald enhet under 1, 6, 12 eller 24 timmar. Följande information är tillgänglig:

  - Kategori: grundläggande identifierings information baserat på trafik scenarier.

  - Käll-och mål enheter

  - Data: ytterligare information har fel.

  - Datum och tid som senast lästes.

Du kan spara rapporten som en Microsoft Excel-eller Word-fil.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Senaste aktivitet":::

Så här skapar du en aktivitets rapport för en enhet:

1. Högerklicka på en enhet från kartan.

2. Välj en aktivitets rapport.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Visa en rapport över din aktivitet.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Generera angrepps vektor rapporter från kartan

Simulera en angrepps vektor rapport och lär dig om en enhet på den karta du väljer är ett sårbart attack mål.

Angrepps vektor rapporter ger en grafisk representation av en sårbarhets kedja av enheter som går att utnyttja. Dessa sårbarheter kan ge en angripare åtkomst till viktiga nätverks enheter. Attack vektor simulatorn beräknar angrepps vektorer i real tid och analyserar alla angrepps vektorer per ett särskilt mål.

Så här visar du en enhet i en attack vektor rapporter:

1. Högerklicka på en enhet från kartan.

2. Välj **simulera angrepps vektorer**. Dialog rutan angrepps vektor öppnas med den enhet som du väljer som angrepps mål.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Lägg till simulering av attack vektor":::

3. Lägg till de återstående parametrarna i dialog rutan och välj **Lägg till simulering**.

## <a name="export-device-information-from-the-map"></a>Exportera enhets information från kartan

Exportera följande enhets information från kartan.

  - Enhets information (Microsoft Excel)

  - En enhets Sammanfattning (Microsoft Excel)

  - En Word-fil med grupper (Microsoft Word)

Att exportera:

1. Välj export ikonen från kartan.

1. Välj ett export alternativ.

## <a name="see-also"></a>Se även

[Undersök sensor identifieringar i en enhets inventering](how-to-investigate-sensor-detections-in-a-device-inventory.md)
