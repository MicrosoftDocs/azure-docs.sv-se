---
title: Installera Azure AD Connect med hjälp av en befintlig ADSync-databas | Microsoft Docs
description: I det här avsnittet beskrivs hur du använder en befintlig ADSync-databas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8324b82a05d7e78772e0b0b6de3a9bfaa183411
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91265399"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Installera Azure AD Connect med en befintlig ADSync-databas
Azure AD Connect kräver en SQL Server databas för att lagra data. Du kan antingen använda standard SQL Server 2012 Express-LocalDB som installerats med Azure AD Connect eller använda din egen fullständiga version av SQL. Tidigare skapades en ny databas med namnet ADSync alltid när du installerade Azure AD Connect. Med Azure AD Connect version 1.1.613.0 (eller senare) har du möjlighet att installera Azure AD Connect genom att peka den på en befintlig ADSync-databas.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Fördelar med att använda en befintlig ADSync-databas
Genom att peka på en befintlig ADSync-databas:

- Med undantag för information om autentiseringsuppgifter, återställs konfigurationen för synkronisering som lagras i ADSync-databasen (inklusive anpassade regler för synkronisering, anslutningar, filtrering och valfria funktioner) automatiskt och används under installationen. Autentiseringsuppgifter som används av Azure AD Connect för att synkronisera ändringar med lokala AD och Azure AD är krypterade och kan bara användas av den tidigare Azure AD Connect servern.
- Alla identitets data (kopplade till anslutnings utrymmen och metaversum) och synkronisera cookies som lagras i ADSync-databasen återställs också. Den nyinstallerade Azure AD Connect servern kan fortsätta synkronisera från där den tidigare Azure AD Connect servern slutade, i stället för att behöva utföra en fullständig synkronisering.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>De scenarier där du använder en befintlig ADSync-databas är fördelaktig
De här fördelarna är användbara i följande scenarier:


- Du har en befintlig Azure AD Connect-distribution. Din befintliga Azure AD Connect-Server fungerar inte längre, men den SQL Server som innehåller ADSync-databasen fungerar fortfarande. Du kan installera en ny Azure AD Connect Server och peka den på den befintliga ADSync-databasen. 
- Du har en befintlig Azure AD Connect-distribution. SQL-servern som innehåller ADSync-databasen fungerar inte längre. Men du har en aktuell säkerhets kopiering av-databasen. Du kan först återställa ADSync-databasen till en ny SQL Server. Sedan kan du installera en ny Azure AD Connect Server och peka den på den återställda ADSync-databasen.
- Du har en befintlig Azure AD Connect-distribution som använder LocalDB. På grund av gränsen på 10 GB från LocalDB skulle du vilja migrera till fullständig SQL. Du kan säkerhetskopiera ADSync-databasen från LocalDB och återställa den till en SQL-Server. Sedan kan du installera om en ny Azure AD Connect-Server och peka den på den återställda ADSync-databasen.
- Du försöker konfigurera en fristående server och vill se till att konfigurationen matchar den aktuella aktiva servern. Du kan säkerhetskopiera ADSync-databasen och återställa den till en annan SQL Server. Sedan kan du installera om en ny Azure AD Connect-Server och peka den på den återställda ADSync-databasen.

## <a name="prerequisite-information"></a>Nödvändig information

Viktiga anteckningar för att anteckna innan du fortsätter:

- Se till att granska kraven för att installera Azure AD Connect vid maskin vara och krav, samt konto och behörigheter som krävs för att installera Azure AD Connect. De behörigheter som krävs för att installera Azure AD Connect att använda läget Använd befintlig databas är samma som anpassad installation.
- Det går bara att distribuera Azure AD Connect mot en befintlig ADSync-databas med fullständig SQL. Det stöds inte med SQL Express-LocalDB. Om du har en befintlig ADSync-databas i LocalDB som du vill använda måste du först säkerhetskopiera ADSync Database (LocalDB) och återställa den till fullständig SQL. Sedan kan du Distribuera Azure AD Connect mot den återställda databasen med den här metoden.
- Den version av Azure AD Connect som används för installation måste uppfylla följande kriterier:
    - 1.1.613.0 eller senare, och
    - Samma eller högre än den version av Azure AD Connect som senast användes med ADSync-databasen. Om den Azure AD Connect-version som används för installation är högre än den version som senast användes med ADSync-databasen kan en fullständig synkronisering krävas.  Fullständig synkronisering krävs om det finns ändringar i schema-eller synkroniseringsregeln mellan de två versionerna. 
- ADSync-databasen som används bör innehålla ett synkroniseringstillstånd som är relativt nyligen. Den senaste synkroniseringen med den befintliga ADSync-databasen ska ligga inom de tre senaste veckorna.
- När du installerar Azure AD Connect med hjälp av metoden Använd befintlig databas, bevaras inte inloggnings metoden som kon figurer ATS på den tidigare Azure AD Connect servern. Dessutom kan du inte konfigurera inloggnings metoden under installationen. Du kan bara konfigurera inloggnings metoden när installationen är klar.
- Du kan inte ha flera Azure AD Connect-servrar delar samma ADSync-databas. Med metoden Använd befintlig databas kan du återanvända en befintlig ADSync-databas med en ny Azure AD Connect-Server. Den stöder inte delning.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Steg för att installera Azure AD Connect med läget "Använd befintlig databas"
1.  Ladda ned Azure AD Connect Installer (AzureADConnect.MSI) till Windows Server. Dubbelklicka på installations programmet för Azure AD Connect för att börja installera Azure AD Connect.
2.  När MSI-installationen är klar startar Azure AD Connect-guiden med installationsläget Express. Stäng fönstret genom att klicka på ikonen Avsluta.
![Skärm bild som visar sidan "Välkommen till Azure A D Connect" med "Express inställningar" på den vänstra menyn markerad.](./media/how-to-connect-install-existing-database/db1.png)
3.  Starta en ny kommandotolk eller PowerShell-session. Navigera till mappen C:\Program\Microsoft Azure Active Directory Connect. Kör kommandot .\AzureADConnect.exe /useexistingdatabase för att starta Azure AD Connect-guiden i läget ”Använd befintlig databas”.

> [!NOTE]
> Använd bara växeln **/UseExistingDatabase** när databasen redan innehåller data från en tidigare Azure AD Connect-installation. När du till exempel flyttar från en lokal databas till en fullständig SQL Server databas eller när Azure AD Connect servern har återskapats och du återställde en SQL-säkerhetskopia av ADSync-databasen från en tidigare installation av Azure AD Connect. Om databasen är tom, det vill säga att den inte innehåller några data från en tidigare Azure AD Connect installation, hoppar du över det här steget.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Du välkomnas med Välkommen till Azure AD Connect-skärmen. När du godkänt licensvillkoren och sekretesspolicyn klickar du på **Fortsätt**.
   ![Skärm bild som visar sidan "Välkommen till Azure A D Connect"](./media/how-to-connect-install-existing-database/db3.png)
1. På skärmen **Installera nödvändiga komponenter** är alternativet **Använd en befintlig SQL-server** aktiverat. Ange namnet på den SQL-server som är värd för ADSync-databasen. Om SQL-motorinstansen som används som värd för ADSync-databasen inte är standardinstansen på SQL-servern, måste du ange instansnamnet för SQL-motorn. Om SQL-surfning inte är aktiverat, måste du dessutom också ange portnumret för SQL-motorinstansen. Exempel:         
   ![Skärm bild som visar sidan "installera nödvändiga komponenter".](./media/how-to-connect-install-existing-database/db4.png)           

1. På skärmen **Anslut till Azure AD**, måste du ange autentiseringsuppgifter för en global administratör i Azure AD-katalogen. Det rekommenderas att använda ett konto i standarddomänen onmicrosoft.com. Det här kontot används bara för att skapa ett tjänstkonto i Azure AD och används inte när guiden har slutförts.
   ![Anslut](./media/how-to-connect-install-existing-database/db5.png)
 
1. På skärmen **Anslut dina kataloger** har den befintliga AD-skog som konfigurerats för katalogsynkronisering angetts med en röd kryssikon bredvid sig. Om du vill synkronisera ändringar från en lokal AD-skog krävs ett AD DS-konto. Azure AD Connect-guiden kan inte hämta autentiseringsuppgifter för det AD DS-konto som lagrats i ADSync-databasen eftersom autentiseringsuppgifterna krypterats och endast kan dekrypteras av den tidigare Azure AD Connect-servern. Klicka på **Ändra autentiseringsuppgifter** för att ange AD DS-kontot för AD-skogen.
   ![Kataloger](./media/how-to-connect-install-existing-database/db6.png)
 
1. I popup-fönstret kan du antingen (i) ange autentiseringsuppgifter för en företagsadministratör och låta Azure AD Connect skapa AD DS-kontot åt dig, eller (ii) skapa AD DS-kontot och ange dess autentiseringsuppgifter till Azure AD Connect. När du har valt ett alternativ och angett nödvändiga autentiseringsuppgifter klickar du på **OK** för att stänga popup-fönstret.
   ![Skärm bild som visar popup-dialog rutan "A D-skogs konto" med "Skapa nytt A D-konto" markerat.](./media/how-to-connect-install-existing-database/db7.png)
 
1. När autentiseringsuppgifterna har angetts ersätts den röda kryssikonen med en grön bockikon. Klicka på **Nästa**.
   ![Skärm bild som visar sidan "Anslut dina kataloger".](./media/how-to-connect-install-existing-database/db8.png)
 
1. På skärmen **redo att konfigurera** klickar du på **Installera**.
   ![Välkommen](./media/how-to-connect-install-existing-database/db9.png)
 
1. När installationen är klar aktiveras automatiskt Azure AD Connect-servern för mellanlagringsläge. Vi rekommenderar att du läser igenom serverkonfigurationen och väntande exporter för oväntade ändringar innan du inaktiverar mellanlagringsläget. 

## <a name="post-installation-tasks"></a>Uppgifter efter installation
När du återställer en säkerhets kopia av en databas som skapats av en version av Azure AD Connect före 1.2.65.0, väljs automatiskt en inloggnings metod för att **Konfigurera**. Medan inställningarna för hash-synkronisering och tillbakaskrivning av lösen ord återställs, måste du senare ändra inloggnings metoden så att den matchar de andra principerna som gäller för den aktiva synkroniseringstjänsten.  Om du inte utför de här stegen kan användarna förhindra att de loggar in om den här servern blir aktiv.  

Använd tabellen nedan för att kontrol lera eventuella ytterligare steg som krävs.

|Funktion|Steg|
|-----|-----|
|Hash-synkronisering av lösen ord| inställningarna för synkronisering av lösen ord och tillbakaskrivning av lösen ord återställs helt för versioner av Azure AD Connect som börjar med 1.2.65.0.  Om du återställer med en äldre version av Azure AD Connect granskar du inställningarna för synkronisering för dessa funktioner för att se till att de matchar den aktiva synkroniseringstjänsten.  Inga andra konfigurations steg bör vara nödvändiga.|
|Federation med AD FS|Azure-autentiseringar fortsätter att använda den AD FS principen som kon figurer ATS för den aktiva synkroniseringstjänsten.  Om du använder Azure AD Connect för att hantera AD FS-servergruppen kan du alternativt ändra inloggnings metoden till AD FS federationen som förberedelse för din standby-server som blir den aktiva synkroniseringsåtgärden.   Om enhets alternativ är aktiverade på den aktiva synkroniseringstjänsten konfigurerar du de alternativen på den här servern genom att köra aktiviteten "Konfigurera enhets alternativ".|
|Direkt autentisering och skriv bords Sign-On|Uppdatera inloggnings metoden för att matcha konfigurationen på din aktiva synkroniseringstjänst.  Om detta inte följs innan du befordrar servern till primär, inaktive ras direktautentisering tillsammans med sömlös enkel inloggning och klienten kan vara utelåst om du inte har ett lösen ord för hash-synkronisering som reserv inloggnings alternativ. Observera också att när du aktiverar direktautentisering i mellanlagringsplatsen installeras en ny autentiseringstjänst, som är registrerad och körs som en agent med hög tillgänglighet som accepterar inloggnings förfrågningar.|
|Federation med PingFederate|Azure-autentiseringar fortsätter att använda PingFederate-principen som kon figurer ATS för din aktiva synkroniseringstjänst.  Alternativt kan du ändra inloggnings metoden till PingFederate som förberedelse för att din vänte Server ska bli den aktiva synkroniseringsåtgärden.  Det här steget kan skjutas upp tills du behöver federera ytterligare domäner med PingFederate.|

## <a name="next-steps"></a>Nästa steg

- Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).
- Läs mer om dessa funktioner, som aktiverades med installationen: [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).
- Läs mer om [schemaläggaren och hur du utlöser synkronisering](how-to-connect-sync-feature-scheduler.md).
- Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
