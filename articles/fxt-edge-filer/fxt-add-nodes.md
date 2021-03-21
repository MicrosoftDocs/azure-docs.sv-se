---
title: 'Självstudie: lägga till noder i ett Azure FXT Edge-kluster'
description: Lär dig hur du lägger till klusternoder till lagrings-cachen i Azure FXT Edge och aktiverar funktionen för hög tillgänglighet (HA).
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 85ad78eeb095b427b1a6334f57c351e926022dff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021885"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Självstudie: lägga till klusternoder i ett Azure FXT Edge-kluster

Ett nytt Azure FXT Edge-kluster skapas med bara en nod. Du bör lägga till minst två noder och aktivera hög tillgänglighet innan du gör en annan konfiguration.

I den här självstudien beskrivs hur du lägger till klusternoder och aktiverar funktionen hög tillgänglighet (HA).

I den här kursen lär du dig:

> [!div class="checklist"]
>
> * Så här lägger du till noder i FXT-klustret
> * Så här aktiverar du HA

Stegen i den här självstudien tar cirka 45 minuter att slutföra.

Innan du börjar den här självstudien kan du använda de noder som du vill lägga till och [Ange de ursprungliga lösen orden](fxt-node-password.md).

## <a name="1-load-the-cluster-nodes-page"></a>1. Läs in sidan klusternoder

Öppna klustrets kontroll panel i en webbläsare och logga in som administratör. (Detaljerade instruktioner finns i översikts artikeln under [Öppna sidan Inställningar](fxt-cluster-create.md#open-the-settings-pages).)

Fliken **instrument panel** visas på kontroll panelen när den öppnas. 

![Instrument panel för kontroll panelen (första fliken)](media/fxt-cluster-config/dashboard-1-node.png)

Den här bilden visar instrument panelen för ett nyskapat kluster, med en enda nod.

## <a name="2-locate-the-node-to-add"></a>2. Leta upp noden som ska läggas till

Om du vill lägga till noder klickar du på fliken **Inställningar** och väljer sidan **FXT noder** i **kluster** avsnittet.

![Fliken Inställningar på kontroll panelen (andra fliken) med > FXT-noder för kluster som har lästs in](media/fxt-cluster-config/settings-fxt-nodes.png)

I **FXT-noderna** visas alla otilldelade FXT-noder (de flesta data Center har bara några. Hitta de FXT-noder som du vill lägga till i klustret.

> [!Tip]
> Om du inte hittar den nod som du vill använda i listan över **frånkopplade** , kontrollerar du att den uppfyller följande krav:
>
> * Den är påslagen och har fått en [rot lösen ords uppsättning](fxt-node-password.md).
> * Den är ansluten till ett nätverk som du har åtkomst till. Om du använder VLAN måste det finnas i samma VLAN som klustret.
> * Den kan identifieras med Bonjour-protokollet.
>
>   Vissa brand Väggs inställningar blockerar TCP/UDP-portarna som används av Bonjour, vilket förhindrar att operativ systemet FXT identifierar noderna automatiskt.
>
> Om den nod som du vill lägga till inte finns med i listan kan du prova följande lösningar:
>
> * Klicka på knappen **manuell identifiering** för att hitta den via IP-adress.
>
> * Tilldela temporära IP-adresser manuellt. Detta är sällsynt men kan behövas om du använder taggade VLAN och noderna inte finns i rätt nätverk, eller om nätverket inte tillåter självtilldelade IP-adresser. Följ anvisningarna i den äldre versionen av det här dokumentet om du vill [Ange en statisk IP-adress manuellt](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Nodens namn, IP-adress, program varu version och status för berättigande visas i listan. Normalt säger kolumnen **status** "vill du ansluta" eller beskriver ett system-eller maskin varu problem som gör att noden inte är tillgänglig för att ansluta till klustret.

Kolumnen **åtgärder** innehåller knappar som låter dig lägga till noden i klustret eller uppdatera dess program vara. Uppdaterings knappen installerar automatiskt den program varu version som matchar noderna som redan finns i klustret.

Alla noder i ett kluster måste använda samma version av operativ systemet, men du behöver inte uppdatera program vara innan du lägger till en nod. När du klickar på knappen **Tillåt att ansluta** , kontrollerar kluster kopplings processen automatiskt och installerar den OS-programvara som matchar versionen i klustret.

Om du vill veta mer om alternativen på den här sidan kan du läsa [ **cluster**  >  **FXT-noder**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) i guiden kluster konfiguration.

## <a name="3-click-the-allow-to-join-button"></a>3. Klicka på knappen "Tillåt att ansluta"

Klicka på knappen **Tillåt anslutning** _ i kolumnen _ *åtgärder** för den nod som du vill lägga till.

När du klickar på knappen kan nodens status ändras när dess program vara uppdateras i förberedelse för att lägga till den i klustret.

Bilden nedan visar en nod som håller på att ansluta till klustret (förmodligen hämtas en OS-uppdatering innan den läggs till). Inga knappar visas i kolumnen **åtgärder** för noder som håller på att läggas till i klustret.

![en rad i tabellen Node som visar en nods namn, IP-adress, program varu version, meddelandet "tillåten att ansluta" och en tom sista kolumn](media/fxt-cluster-config/node-join-in-process.png)

Efter en liten stund ska den nya noden visas i listan över klusternoder överst på **FXT** -nodens inställnings sida.

Upprepa processen för att lägga till de andra noderna i klustret. Du behöver inte vänta på att en nod ska sluta ansluta till klustret innan du påbörjar en ny.

## <a name="enable-high-availability"></a>Aktivera hög tillgänglighet

När du har lagt till en andra nod i klustret kan du se ett varnings meddelande på kontroll panelens instrument panel att funktionen hög tillgänglighet inte har kon figurer ATS.

Hög tillgänglighet (HA) gör det möjligt för klusternoderna att kompensera för varandra om den ena slutar. HA är inte aktiverat som standard.

![Fliken instrument panel med meddelandet "klustret har fler än en nod, men HA inte Aktiver ATS..." i villkors tabellen](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note]
> Aktivera inte HA förrän du har minst tre noder i klustret.

Följ den här proceduren för att aktivera HA:

1. Läs in sidan med **hög tillgänglighet** i **kluster** -avsnittet på fliken **Inställningar** .

   ![Sidan HA konfiguration (kluster > hög tillgänglighet). Kryss rutan "Aktivera HA" visas längst upp och knappen Skicka visas längst ned.](media/fxt-cluster-config/enable-ha.png)

2. Klicka på rutan **Aktivera ha** och klicka på knappen **Skicka** .

En avisering visas på **instrument panelen** för att bekräfta att ha Aktiver ATS.

![Instrument panels tabell som visar meddelandet "HA är fullständigt konfigurerat"](media/fxt-cluster-config/ha-configured-alert.png)

## <a name="next-steps"></a>Nästa steg

När du har lagt till alla noder i klustret fortsätter du installationen genom att konfigurera klustrets långsiktiga lagring.

> [!div class="nextstepaction"]
> [Lägg till Server dels lagring och konfigurera det virtuella namn området](fxt-add-storage.md)