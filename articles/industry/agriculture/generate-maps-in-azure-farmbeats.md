---
title: Generera kartor
description: Den här artikeln beskriver hur du genererar kartor i Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: d0176fd337abd37f97a6dbe5cf7c68ccf94114d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173872"
---
# <a name="generate-maps"></a>Generera kartor

Med hjälp av Azure FarmBeats kan du generera följande Maps med hjälp av satellit-bilder och indata från sensorer. Maps hjälper dig att se den geografiska platsen för Server gruppen och identifiera lämplig plats för dina enheter.

  - **Sensor placerings karta**: ger rekommendationer om hur många sensorer som ska användas och var de ska placeras i en grupp.
  - **Karta över satellit** index: visar vegetations indexet och vatten indexet för en Server grupp.
  - **Jord-fukt termisk karta**: visar jord fuktighets distribution av fusing satellit data och sensor data.

## <a name="sensor-placement-map"></a>Sensor placerings karta

En FarmBeats sensor placerings karta hjälper dig med placeringen av jord fukts sensorer. Kart resultatet består av en lista över koordinater för sensor distribution. Indata från dessa sensorer används tillsammans med satellit-bilder för att skapa jordens vatten termisk karta.

Den här kartan härleds genom att segmentera Canopy som visas under flera datum under året. Även Bare jordmån och byggnader är en del av Canopy. Du kan ta bort sensorer som inte behövs på platsen. Den här kartan är till för vägledning och du kan ändra position och siffror något beroende på din anpassade kunskap. Att lägga till sensorer regress inte termisk karta resultat, men det är möjligt att minska risken för termisk karta om sensor numret minskar.

## <a name="before-you-begin"></a>Innan du börjar

Uppfylla följande krav innan du försöker skapa en sensor placerings karta:

- Server gruppens storlek måste vara större än en Acre.
- Antalet moln fria kontroll scener måste vara större än sex för det valda datum intervallet.
- Minst sex moln fria kontroll scener måste ha en normaliserad differens vegetations index (NDVI) som är större än eller lika med 0,3.

    > [!NOTE]
    > Kontroll bara tillåter två samtidiga trådar per användare. Därför hämtas jobben i kö och kan ta längre tid att slutföra.

### <a name="dependencies-on-sentinel"></a>Beroenden för kontroll

Följande beroenden gäller kontroll:

- Vi är beroende av Sentinel-prestanda för nedladdning av satellit avbildningar. Kontrol lera prestanda status och underhålls [aktiviteter](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)för Sentinel.
- Kontroll bara tillåter två samtidiga [hämtnings trådar](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) per användare.
- Generering av precisions kartor påverkas av [kontroll täckning och]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)besöks frekvens.

## <a name="create-a-sensor-placement-map"></a>Skapa en sensor placerings karta
I det här avsnittet beskrivs procedurerna för att skapa sensor placerings kartor.

> [!NOTE]
> Du kan initiera en sensor placerings karta från sidan **kartor** eller från List rutan **generera precisions kartor** på sidan **Server grupps information** .

Gör så här:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.
2. Välj **skapa kartor** och välj **sensor placering** på den nedrullningsbara menyn.

    ![Välj sensor placering](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. När du har valt **sensor placering** visas **sensor placerings** fönstret.

    ![Sensor placerings fönster](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Välj en Server grupp på den nedrullningsbara menyn **Server grupp** .
   Om du vill söka efter och välja Server gruppen kan du antingen bläddra i list rutan eller ange namnet på Server gruppen i text rutan.
5. Om du vill generera en karta för det senaste året väljer du **rekommenderat**.
6. Om du vill generera en karta för ett anpassat datum intervall väljer du alternativet **Välj datum intervall**. Ange start-och slutdatum för vilket du vill generera sensor placerings kartan.
7. Välj **generera kartor**.
 Ett bekräftelse meddelande med jobb information visas.

  Information om jobb status finns i avsnittet **Visa jobb**. Om jobb *statusen visas visas* ett detaljerat fel meddelande i knapp beskrivningen för statusen *misslyckades* . I det här fallet upprepar du föregående steg och försöker igen.

  Om problemet kvarstår kan du läsa avsnittet [fel sökning](troubleshoot-azure-farmbeats.md) eller kontakta [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-a-sensor-placement-map"></a>Visa och ladda ned en sensor placerings karta

Gör så här:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.

    ![Välj kartor från den vänstra navigerings menyn](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **filter** i det vänstra fönstrets navigerings fönster.
  **Filter** fönstret visar Sök villkor.

    ![Filter fönster](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Välj **typ**-, **datum**-och **namn** värden på de nedrullningsbara menyerna. Välj sedan **Använd** för att söka efter den karta som du vill visa.
  Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.
4. Bläddra igenom listan över Maps som är tillgängliga genom att använda navigerings fälten i slutet av sidan.
5. Välj den karta som du vill visa. Ett popup-fönster visar förhands granskningen för den valda kartan.
6. Välj **Ladda ned** och ladda ned den interjson-fil med sensor koordinater.

    ![För hands version av sensor placerings karta](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Karta över satellit index

I följande avsnitt beskrivs de procedurer som ingår i att skapa och visa en satellit index karta.

> [!NOTE]
> Du kan initiera en satellit index karta från sidan **kartor** eller från List menyn **generera precisions kartor** på sidan **Server grupps information** .

FarmBeats ger dig möjlighet att generera NDVI, förbättrat vegetations index (EVI) och (normaliserad differens vatten index) för Server grupper. Dessa index hjälper till att fastställa hur grödan för närvarande växer eller har vuxit tidigare och de representativa vatten nivåerna i marken.


> [!NOTE]
> Det krävs en kontroll avbildning för de dagar då kartan genereras.


## <a name="create-a-satellite-indices-map"></a>Skapa en satellit index karta

Gör så här:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.
2. Välj **skapa kartor** och välj **satellit index** på den nedrullningsbara menyn.

    ![Välj satellit index på den nedrullningsbara menyn](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. När du har valt **satellit index** visas fönstret **satellit index** .

    ![Satellit index-fönstret](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Välj en Server grupp på den nedrullningsbara menyn.
   Om du vill söka efter och välja din server grupp kan du antingen bläddra i list rutan eller ange namnet på Server gruppen.   
5. Om du vill generera en karta för den senaste veckan väljer du **den här veckan**.
6. Om du vill generera en karta för ett anpassat datum intervall väljer du alternativet **Välj datum intervall**. Ange start-och slutdatum för vilket du vill generera kartan för satellit index.
7. Välj **generera kartor**.
    Ett bekräftelse meddelande med jobb information visas.

    ![Bekräftelse meddelande för satellit index karta](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Information om jobb status finns i avsnittet **Visa jobb**. Om jobb *statusen visas visas* ett detaljerat fel meddelande i knapp beskrivningen för statusen *misslyckades* . I det här fallet upprepar du föregående steg och försöker igen.

    Om problemet kvarstår kan du läsa avsnittet [fel sökning](troubleshoot-azure-farmbeats.md) eller kontakta [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-a-map"></a>Visa och ladda ned en karta

Gör så här:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.

    ![Välj kartor](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **filter** i det vänstra fönstrets navigerings fönster. **Filter** fönstret visar Sök villkor.

    ![Filter fönstret visar Sök villkor](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Välj **typ**-, **datum**-och **namn** värden på de nedrullningsbara menyerna. Välj sedan **Använd** för att söka efter den karta som du vill visa.
  Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.

4. Bläddra igenom listan över Maps som är tillgängliga genom att använda navigerings fälten i slutet av sidan.
5. För varje kombination av namn och **datum** för **Server gruppen** är följande tre Maps tillgängliga:
    - NDVI
    - EVI
    - NDWI
6. Välj den karta som du vill visa. Ett popup-fönster visar förhands granskningen för den valda kartan.
7. Välj **Ladda ned** på den nedrullningsbara menyn för att välja nedladdnings formatet. Kartan laddas ned och lagras i din lokala mapp på din dator.

    ![Förhands granskning av den valda satellit index kartan](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Jord-fukt termisk karta

Jord fukt är det vatten som hålls i utrymmet mellan jord partiklar.Jord-fuktens termisk karta hjälper dig att förstå jord fukts data i djupet med hög upplösning i Server gruppen. För att kunna generera en korrekt och användbar jord fukt termisk karta krävs en enhetlig distribution av sensorer. Alla sensorer måste vara från samma provider. Olika leverantörer har skillnader i hur jord fukten mäts tillsammans med skillnader i kalibreringen. Termisk karta genereras för ett visst djup genom att använda sensorer som distribueras på det djupet.

### <a name="before-you-begin"></a>Innan du börjar

Uppfylla följande krav innan du försöker skapa en jord-fukt termisk karta:

- Minst tre jord fuktighets sensorer måste distribueras. Försök inte att skapa en jord Fukts termisk karta innan sensorer distribueras och är kopplade till Server gruppen.
- Att skapa en jord Fukts termisk karta påverkas av kontrollens Sök vägs täckning, moln höljet och moln skugga. Minst en indikator för en moln fri indikator måste vara tillgänglig under de senaste 120 dagarna, från den dag då jordens vatten termisk karta begärdes.
- Minst hälften av sensorer som distribuerats i Server gruppen måste vara online och ha data strömning till Datahub.
- Termisk karta måste genereras med hjälp av sensor mått från samma provider.


## <a name="create-a-soil-moisture-heatmap"></a>Skapa en jord-fukt termisk karta

Gör så här:

1. På Start sidan går du till **kartor** från den vänstra navigerings menyn för att visa sidan **kartor** .
2. Välj **skapa kartor** och välj **jord fukt** på den nedrullningsbara menyn.

    ![Välj jord fukt på den nedrullningsbara menyn](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. När du har valt **jord fukt** visas fönstret **jord fukt** .

    ![Jord Fukts fönster](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Välj en Server grupp på den nedrullningsbara menyn **Server grupp** .
   Om du vill söka efter och välja din server grupp, kan du antingen bläddra i list rutan eller ange namnet på Server gruppen på list menyn **Välj grupp** .
5. På den Välj den nedrullningsbara menyn **Välj jord fuktighets sensor** väljer du det djup som du vill generera kartan för.
Om du vill hitta sensor måttet går du till **sensorer** och väljer en jord fuktighets sensor. I avsnittet **sensor egenskaper** använder du värdet i **måttnamn**.
6. Välj ett av alternativen om du vill generera en karta för **idag** eller **den här veckan**.
7. Om du vill generera en karta för ett anpassat datum intervall väljer du alternativet **Välj datum intervall**. Ange start-och slutdatum för vilken du vill skapa jordens fukt termisk karta.
8. Välj **generera kartor**.
 Ett bekräftelse meddelande med jobb information visas.

   ![Bekräftelse meddelande för jord Fukts karta](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Information om jobb status finns i avsnittet **Visa jobb**. Om jobb *statusen visas visas* ett detaljerat fel meddelande i knapp beskrivningen för statusen *misslyckades* . I det här fallet upprepar du föregående steg och försöker igen.

    Om problemet kvarstår kan du läsa avsnittet [fel sökning](troubleshoot-azure-farmbeats.md) eller kontakta [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-a-map"></a>Visa och ladda ned en karta

Gör så här:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.

    ![Gå till Maps](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **filter** i det vänstra fönstrets navigerings fönster. **Filter** fönstret visar var du kan söka efter kartor.

    ![Välj filter i det vänstra navigerings fältet](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Välj **typ**-, **datum**-och **namn** värden på de nedrullningsbara menyerna. Välj sedan **Använd** för att söka efter den karta som du vill visa. Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.
4. Välj **sorterings** ikonen bredvid tabell rubrikerna för att sortera efter **Server grupp**, **datum**, **skapad**, **jobb-ID** och **Jobbtyp**.
5. Bläddra igenom listan över Maps som är tillgängliga genom att använda navigerings knapparna i slutet av sidan.
6. Välj den karta som du vill visa. Ett popup-fönster visar förhands granskningen för den valda kartan.
7. Välj **Ladda ned** på den nedrullningsbara menyn för att välja nedladdnings formatet. Kartan laddas ned och lagras i den angivna mappen.

    ![För hands version av jord fukt termisk karta](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
