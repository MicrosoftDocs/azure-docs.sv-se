---
title: Kontrollverktyget för ArrInspector
description: Användar handbok för ArrInspector-verktyget
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 300e0ff26d643ae0263d21e604cb26da37a18841
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723833"
---
# <a name="the-arrinspector-inspection-tool"></a>Kontrollverktyget för ArrInspector

ArrInspector är ett webbaserat verktyg som används för att inspektera en pågående Azure-fjärrrendering-session. Den är avsedd att användas för fel söknings syfte, för att kontrol lera strukturen på den scen som återges, Visa logg meddelanden och övervaka Live-prestanda på servern.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Ansluter till ArrInspector

När du har fått värd namnet (slutar på `mixedreality.azure.com` ) för din arr-Server ansluter du med [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Den här funktionen skapar en `StartArrInspector.html` på den enhet där programmet körs. Om du vill starta ArrInspector öppnar du filen med en webbläsare (Edge, Firefox eller Chrome) på en dator. Filen är endast giltig i 24 timmar.

Om appen som anropar `ConnectToArrInspectorAsync` redan körs på en dator:

* Om du använder unions integreringen kan den startas automatiskt åt dig.
* Annars hittar du filen i *User Folders \\ LocalAppData \\ [your_app] \\ AC \\ Temp*.

Om appen körs på en HoloLens:

1. Få åtkomst till HoloLens med hjälp av [Windows Device Portal](/windows/mixed-reality/using-the-windows-device-portal).
1. Gå till *System > Utforskaren*.
1. Navigera till *localappdata för \\ användare \\ [your_app] \\ AC- \\ Temp*.
1. Spara *StartArrInspector.html* på din dator.
1. Öppna *StartArrInspector.html* för att läsa in sessionens ArrInspector.

## <a name="the-performance-panel"></a>Panelen prestanda

![Panelen prestanda](./media/performance-panel.png)

Den här panelen visar diagram över alla prestanda värden per ram som exponeras av servern. Värdena omfattar för närvarande tidsram, FPS, processor-och minnes användning, minnes statistik som total RAM-användning, antal objekt, osv.

Om du vill visualisera en av dessa parametrar klickar du på knappen **Lägg till ny** och väljer en av de tillgängliga värdena som visas i dialog rutan. Den här åtgärden lägger till ett nytt rullnings diagram på panelen och spårar värdena i real tid. Till höger kan du se det *lägsta*, *högsta* och *aktuella* värdet.

Du kan panorera grafen genom att dra dess innehåll med musen, men det är dock bara möjligt att panorera vågrätt när ArrInspector är i pausat läge.

Du kan zooma genom att hålla CTRL samtidigt som du drar. Horisontell zoomning kan också styras med skjutreglaget längst ned.

Det lodräta intervallet är som standard beräknat baserat på de värden som visas för närvarande, och de minsta och högsta värdena visas i text rutorna till höger. När värdena anges manuellt, antingen genom att skriva dem direkt i text rutan eller genom panorering/zoomning, kommer diagrammet att använda dessa värden. Om du vill återställa den automatiska lodräta ram, klickar du på ikonen i det övre högra hörnet.

![lodrätt intervall](./media/vertical-range.png)

## <a name="the-log-panel"></a>Panelen logg

![Logg panel](./media/log-panel.png)

Logg panelen visar en lista över logg meddelanden som genereras på Server sidan. Vid anslutning visar den upp till 200 tidigare logg meddelanden och kommer att skriva ut nya.

Du kan filtrera listan baserat på logg typen `[Error/Warning/Info/Debug]` med knapparna längst upp.
![Logg filter knappar](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Hämtnings panelen för tids data

![Fångst av tids data](./media/timing-data-capture.png)

Den här panelen används för att avbilda tids information från servern och ladda ned den. Filen använder JSON- [formatet Chrome-spårning](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Om du vill granska data öppnar du Chrome på URL: en `Chrome://tracing` och drar och släpper den nedladdade filen på sidan. Tid mätnings data samlas in kontinuerligt i en ring-buffert med fast storlek. Vid avskrivning innehåller insamlingen bara information om den omedelbara tiden, vilket innebär några sekunder till några minuter.

## <a name="the-scene-inspection-panel"></a>Kontroll panelen för scenen

![Kontroll panel för scen](./media/scene-inspection-panel.png)

Den här panelen visar strukturen för den renderade scenen. Objektets hierarki är till vänster. innehållet i det markerade objektet är till höger. Panelen är skrivskyddad och uppdateras i real tid.

## <a name="the-vm-debug-information-panel"></a>Panelen för fel söknings information för virtuell dator

![Fel söknings informations panel för virtuell dator](./media/state-debugger-panel.png)

Den här panelen innehåller vissa fel söknings funktioner.

### <a name="restart-service"></a>Starta om tjänsten

Med knappen **starta om tjänsten** startar du om körningen på den virtuella datorn som arrInspector är ansluten till. Alla anslutna klienter kommer att kopplas från och arrInspector-sidan måste läsas in för att ansluta till den omstartade tjänsten.

### <a name="collect-debug-information"></a>Samla in felsöknings information

Knappen **samla in fel söknings information för virtuell dator** öppnar en dialog ruta där du kan utlösa arr-instansen för att samla in felsöknings information på den virtuella datorn:

![Dialog rutan fel söknings information för virtuell dator](./media/state-debugger-dialog.png)

Felsöknings information hjälper Azure-fjärrrendering-teamet att analysera eventuella problem som inträffar i en ARR-instans som körs. Dialog rutan innehåller ett textfält som ger ytterligare information, till exempel steg för att återskapa ett problem.

När du klickar på knappen **börja samla in** stängs dialog rutan och samlings processen börjar. Det kan ta några minuter att samla in informationen på den virtuella datorn.

![Insamling av virtuella dator fel söknings information pågår](./media/state-debugger-panel-in-progress.png)

När samlingen är färdig visas ett meddelande i fönstret ArrInspector. Det här meddelandet innehåller ett ID som identifierar den aktuella samlingen. Se till att spara det här ID: t för att skicka det till Azure-fjärrrendering-teamet.

![Informations insamling för VM-felsökning lyckades](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Du kan inte ladda ned eller på annat sätt komma åt VM-felsökning. Endast Azure-fjärråter givnings teamet har åtkomst till de insamlade data. Du måste kontakta oss och skicka samlings-ID: t tillsammans för att vi ska kunna undersöka problemet som du ser.

## <a name="pause-mode"></a>Paus läge

I det övre högra hörnet kan du med en växel pausa Live-uppdateringen av panelerna. Det här läget kan vara användbart för att noggrant inspektera ett särskilt tillstånd.

![Paus läge](./media/pause-mode.png)

När du aktiverar direkt uppdatering igen återställs alla paneler.