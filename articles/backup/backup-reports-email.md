---
title: E-Azure Backup rapporter
description: Skapa automatiserade uppgifter för att ta emot periodiska rapporter via e-post
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 8c18d4c7a3c7a9ba343296961fa9a44614366405
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510570"
---
# <a name="email-azure-backup-reports"></a>E-Azure Backup rapporter

Med hjälp av funktionen **e-postrapport** som är tillgänglig i säkerhets kopierings rapporter kan du skapa automatiska uppgifter som tar emot regelbundna rapporter via e-post. Den här funktionen fungerar genom att distribuera en Logic-app i din Azure-miljö som frågar data från dina valda Log Analytics (LA)-arbets ytor, baserat på de indata som du anger. [Lär dig mer om Logic Apps och deras priser](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Komma igång

Utför följande steg för att konfigurera e-postuppgifter via säkerhets kopierings rapporter:

1.  Gå till **säkerhets** kopierings Center för säkerhets kopiering  >   och klicka på fliken **e-postrapport** .
2.  Skapa en uppgift genom att ange följande information:
    * **Uppgifts information** – namnet på den Logic-app som ska skapas och den prenumeration, resurs grupp och plats som den ska skapas i. Observera att Logic-appen kan skicka frågor till data över flera prenumerationer, resurs grupper och platser (som valts i avsnittet rapport filter), men skapas i kontexten för en enskild prenumeration, resurs grupp och plats.
    * **Data att exportera** – den flik som du vill exportera. Du kan antingen skapa en enda aktivitets-app per flik eller e-posta alla flikar med en enda uppgift genom att välja alternativet **alla flikar** .
    * **E-postalternativ**: e-postmeddelandets frekvens, mottagarens e-post-ID och e-postmeddelandets ämne.

    ![Fliken e-post](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  När du har klickat på **Skicka** och **Bekräfta** så skapas Logic app. Logic app och associerade API-anslutningar skapas med taggen **UsedByBackupReports: true** för enkel identifiering. Du måste utföra ett verifierings steg för en gång för att den logiska appen ska kunna köras, enligt beskrivningen i avsnittet nedan.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Auktorisera anslutningar till Azure Monitor loggar och Office 365

Logic app använder [azuremonitorlogs](https://docs.microsoft.com/connectors/azuremonitorlogs/) -anslutaren för att skicka frågor till de La arbets ytorna och använder [Office365 Outlook](https://docs.microsoft.com/connectors/office365connector/) Connector för att skicka e-post. Du måste utföra en engångs auktorisering för dessa två kopplingar. 
 
Följ stegen nedan för att utföra auktoriseringen:

1.  Navigera till **Logic Apps** i Azure Portal.
2.  Sök efter namnet på den Logic app som du har skapat och navigera till resursen.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Klicka på meny alternativet **API-anslutningar** .

    ![API-anslutningar](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Du ser två anslutningar med formatet `<location>-azuremonitorlogs` och `<location>-office365` – det vill säga _öster-azuremonitorlogs_ och _öster-Office365_.
5.  Navigera till var och en av dessa anslutningar och välj meny alternativet **Redigera API-anslutning** . På skärmen som visas väljer du **auktorisera** och sparar anslutningen när auktoriseringen är klar.

    ![Godkänna anslutning](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Om du vill testa om Logic-appen fungerar efter auktoriseringen kan du gå tillbaka till Logic-appen, öppna **Översikt** och välja **Kör utlösare** i den övre rutan för att testa om ett e-postmeddelande har genererats.

## <a name="contents-of-the-email"></a>E-postmeddelandets innehåll

* Alla diagram och diagram som visas i portalen är tillgängliga som infogat innehåll i e-postmeddelandet.
* De rutnät som visas i portalen är tillgängliga som *. csv-bilagor i e-postmeddelandet.
* Data som visas i e-postmeddelandet använder alla filter på rapport nivå som valts av användaren i rapporten när du skapar e-postuppgiften.
* Filter på fliken nivå, till exempel **namn på säkerhets kopierings instans**, **princip namn** och så vidare, tillämpas inte. Det enda undantaget till detta är rutnätet för **kvarhållning av optimeringar** på fliken **Optimize** , där filtren för **daglig**, **veckovis**, **månatlig** och **årlig** RP-kvarhållning tillämpas.
* Tidsintervallet och agg regerings typen (för diagram) baseras på användarens tidsintervall val i rapporterna. Om tidsintervallet exempelvis är de senaste 60 dagarna (översätts till vecko agg regerings typ) och e-postfrekvensen är dagligen, får mottagaren ett e-postmeddelande varje dag med diagram som sträcker sig över data som tagits under den senaste 60-dagars perioden, med data som sammanställts på en veckovis nivå.

## <a name="troubleshooting-issues"></a>Felsökning av problem

Om du inte får e-postmeddelanden som förväntat även efter en lyckad distribution av Logic-appen, kan du följa stegen nedan för att felsöka konfigurationen:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Scenario 1: få ingen e-post eller ett fel meddelande

* Det här problemet kan inträffa på grund av att Outlook API Connector inte är auktoriserat. Följ de steg som anges ovan för att auktorisera anslutningen.

* Det här problemet kan också uppstå om du har angett en felaktig e-postmottagare när du skapar Logic-appen. Du kan kontrol lera att e-postmottagaren har angetts korrekt genom att gå till Logic-appen i Azure Portal, öppna Logic Apps designer och välja e-poststeg för att se om rätt e-post-ID används.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Scenario 2: få ett fel meddelande om att det inte gick att köra Logic app för att slutföra

Så här felsöker du problemet:
1.  Navigera till Logic-appen i Azure Portal.
2.  Längst ned på **översikts** skärmen visas avsnittet **körnings historik** . Du kan öppna den senaste körningen och se vilka steg i arbets flödet som misslyckades. Möjliga orsaker kan vara:
    * **Azure Monitor logs Connector har inte auktoriserats**: åtgärda problemet genom att följa de steg som anges ovan.
    * **Fel i La-frågan**: om du har anpassat Logic-appen med dina egna frågor kan ett fel i någon av de La frågorna orsaka att Logic-appen inte fungerar. Du kan välja det relevanta steget och se det fel som orsakar att frågan körs felaktigt.

Kontakta Microsoft-supporten om problemet kvarstår.

## <a name="next-steps"></a>Nästa steg
[Läs mer om Backup-rapporter](https://docs.microsoft.com/azure/backup/configure-reports)
