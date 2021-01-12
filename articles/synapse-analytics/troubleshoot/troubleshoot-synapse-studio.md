---
title: Felsöka Synapse Studio
description: Felsöka Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116185"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Fel sökning av Azure Synapse Studio

Den här fel söknings guiden innehåller instruktioner om vilken information som ska tillhandahållas när du öppnar ett support ärende om problem med nätverks anslutningen. Med rätt information kan vi eventuellt lösa problemet snabbare.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Anslutnings problem för Server lös SQL-adresspool

### <a name="symptom-1"></a>Symptom 1

Alternativet "Server lös SQL-pool" är nedtonat i list rutan "Anslut till".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Symptom 2

Om du kör frågan med "Server lös SQL-pool" får du fel meddelandet "Det gick inte att upprätta en anslutning till servern".

![symptom 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

> [!NOTE] 
>    Följande fel söknings steg är till för krom Edge och Chrome. Du kan använda andra webbläsare (till exempel FireFox) med samma fel söknings steg, men fönstret "utvecklarverktyg" kan ha en annan layout än skärm bilderna i den här TSG. Om möjligt ska du inte använda klassisk gräns för fel sökning, eftersom det kan visa felaktig information i vissa situationer.

Öppna panelen diagnostisk information och välj knappen "Ladda ned diagnostik". Behåll den nedladdade informationen för fel rapportering. Du kan i stället kopiera "sessions-ID" och koppla det när du öppnar support ärendet.

![diagnostik-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Starta fel sökningen genom att försöka utföra åtgärden igen i Azure Synapse Studio.

- För symptom 1 väljer du knappen Uppdatera till höger om List rutan "Använd databas" på fliken "SQL-skript" och kontrollerar om du kan se "Server lös SQL-pool".
- Prova att köra frågan igen för att se om den körs korrekt, för symptom 2.

Om problemet fortfarande finns trycker du på F12 i webbläsaren för att öppna "Utvecklarverktyg" (DevTools).

I fönstret Utvecklarverktyg växlar du till panelen nätverk. Välj knappen "Rensa" i verktygsfältet på panelen "nätverk" om det behövs.
Kontrol lera att "inaktivera cache" i "Network"-panelen är markerat.

Försök utföra åtgärden igen i Azure Synapse Studio. Du kan se nya objekt som visas i listan "nätverk" i "Utvecklarverktyg". Notera din aktuella system tid för att tillhandahålla support ärendet.

![nätverks panel 1](media/troubleshooting-synapse-studio/network-panel.png)

Hitta objektet vars URL-kolumn matchar följande mönster:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Där [*A*] är namnet på din arbets yta och "-OnDemand" kan vara "-sqlod" och var [*B*] ska vara ett databas namn, till exempel "Master". Det får finnas högst två objekt med samma URL-värde men olika metod värden. ALTERNATIV och POST. Kontrol lera om dessa två objekt har "200" eller "20x" under kolumnen status, där "x" kan vara valfri siffra.

Om någon av dem har något annat än "20x" och:

- Status börjar med "(misslyckad)", antingen utvidgar kolumnen "status" eller hovra över status texten för att se hela texten. Ta med texten och/eller skärm bilden när du öppnar support ärendet.

    ![status text](media/troubleshooting-synapse-studio/status-text.png)

    - Om du ser ERR_NAME_NOT_RESOLVED och du har skapat din arbets yta inom 10 minuter, vänta i 10 minuter och försök att se om problemet fortfarande finns.
    - Om du ser ERR_INTERNET_DISCONNECTED eller ERR_NETWORK_CHANGED kan det bero på att din DATORs nätverks anslutning har problem. Kontrol lera nätverks anslutningen och försök igen.
    - Om du ser ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR eller andra felkoder som innehåller "SSL", kan det tyda på att den lokala SSL-konfigurationen har problem eller att nätverks administratören har blockerat åtkomst till serverns SQL-pool Server. Öppna ett support ärende och bifoga felkoden i beskrivningen.
    - Om du ser ERR_NETWORK_ACCESS_DENIED kan du behöva kontrol lera med administratören om den lokala brand Väggs principen har blockerat åtkomst till antingen *. database.windows.net-domän eller Fjärrport 1443.
    - Du kan också prova samma åtgärd direkt på en annan dator och/eller nätverks miljö för att utesluta ett problem med nätverks konfigurationen på din dator.

- Status är "40x", "50 gånger" eller andra siffror, Välj objekt för att se information. Du bör se objekt informationen till höger. Hitta avsnittet "svars huvud". kontrol lera sedan om ett objekt med namnet "åtkomst kontroll-Allow-Origin" finns. I så fall, kontrol lera om det har något av följande värden:

    - `*` (enkel asterisk)
    - https://web.azuresynapse.net/ (eller annat värde som texten i webbläsarens Adress fält börjar med)

Om svars huvudet innehåller ett av ovanstående värden, innebär det att vi redan borde ha samlat in information om problemet. Du kan öppna ett support ärende om det behövs och eventuellt bifoga skärm bilden med objekt detaljerna.

Om du inte kan se rubriken eller om rubriken inte har något av värdena som anges ovan, bifoga en skärm bild av objekt detaljerna när du öppnar biljetten.

 
![objekt information](media/troubleshooting-synapse-studio/item-details.png)
 
Om stegen ovan inte löser problemet kan du behöva öppna ett support ärende. När du skickar in ditt support ärende ska du ta med "sessions-ID" eller "diagnostikinformation" som hämtades i början av den här guiden.

När du rapporterar problemet kan du eventuellt ta en skärm bild av din "konsol"-flik i "Utvecklarverktyg" och koppla den till support ärendet. Rulla innehållet och ta mer än en skärm bild om det behövs för att avbilda hela meddelandet.

![konsol för utvecklarverktyg](media/troubleshooting-synapse-studio/developer-tool-console.png)

Om du kopplar skärm bilder anger du tiden (eller ett uppskattat tidsintervall) för när du tog skärm bilderna. Det hjälper oss när du tittar på problemet.

Vissa webbläsare har stöd för att Visa tidsstämplar på fliken "konsol". För krom Edge/Chrome öppnar du dialog rutan "Inställningar" i "Utvecklarverktyg" och kontrollerar "Visa tidsstämplar" på fliken "Preferences".

![konsol inställningar för utvecklarverktyg](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![Visa tidstämpel](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Nästa steg
Om föregående steg inte hjälper till att lösa problemet [skapar du ett support ärende](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)