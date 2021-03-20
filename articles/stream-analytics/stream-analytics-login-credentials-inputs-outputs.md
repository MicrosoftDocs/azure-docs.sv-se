---
title: Rotera inloggnings uppgifter i Azure Stream Analytics jobb
description: I den här artikeln beskrivs hur du uppdaterar autentiseringsuppgifterna för indata och utgående mottagare i Azure Stream Analytics jobb.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: fd6c072f9783e8ff5d4d5e465b513c2e530bfd63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015240"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Rotera inloggnings uppgifter för indata och utdata för ett Stream Analytics jobb

När du återskapar autentiseringsuppgifter för indata eller utdata för ett Stream Analytics jobb bör du uppdatera jobbet med nya autentiseringsuppgifter. Du måste stoppa jobbet innan du uppdaterar autentiseringsuppgifterna, du kan inte ersätta autentiseringsuppgifterna medan jobbet körs. För att minska fördröjningen mellan att stoppa och starta om jobbet, Stream Analytics har stöd för att återuppta ett jobb från dess senaste utdata. I det här avsnittet beskrivs hur du roterar inloggnings uppgifterna och startar om jobbet med nya autentiseringsuppgifter.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Återskapa nya autentiseringsuppgifter och uppdatera jobbet med de nya autentiseringsuppgifterna 

I det här avsnittet vägleder vi dig genom att återskapa autentiseringsuppgifter för Blob Storage, Event Hubs, SQL Database och Table Storage. 

### <a name="blob-storagetable-storage"></a>Blob Storage/Table Storage
1. Logga in på Azure Portal > bläddra det lagrings konto som du använde som indata/utdata för Stream Analytics jobbet.    
2. I avsnittet Inställningar öppnar du **åtkomst nycklar**. Mellan de två standard nycklarna (KEY1, key2) väljer du den som inte används av jobbet och återskapar den:  
   ![Återskapa nycklar för lagrings kontot](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Kopiera den nya skapade nyckeln.    
4. Från Azure Portal bläddrar du Stream Analytics jobb > väljer **Avbryt** och väntar på att jobbet ska stoppas.    
5. Leta reda på de indata/utdata för BLOB/tabell-lagring som du vill uppdatera autentiseringsuppgifterna för.    
6. Leta upp fältet **lagrings konto nyckel** och klistra in den nya nyckeln > Klicka på **Spara**.    
7. Ett anslutnings test startar automatiskt när du sparar ändringarna, du kan visa det från fliken meddelanden. Det finns två aviseringar – en motsvarar att spara uppdateringen och andra motsvarar att testa anslutningen:  
   ![Meddelanden efter redigering av nyckeln](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Fortsätt att [starta jobbet från avsnittet senaste avbruten tid](#start-your-job-from-the-last-stopped-time) .

### <a name="event-hubs"></a>Händelsehubbar

1. Logga in på Azure Portal > bläddra i Händelsehubben som du använde som indata/utdata för Stream Analytics jobbet.    
2. I avsnittet Inställningar öppnar du **principer för delad åtkomst** och väljer önskad åtkomst princip. Mellan **primär nyckel** och **sekundär nyckel** väljer du den som inte används av jobbet och återskapar det:  
   ![Återskapa nycklar för Event Hubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Kopiera den nya skapade nyckeln.    
4. Från Azure Portal bläddrar du Stream Analytics jobb > väljer **Avbryt** och väntar på att jobbet ska stoppas.    
5. Leta reda på de indata/utdata för Event Hub som du vill uppdatera autentiseringsuppgifterna för.    
6. Leta reda på fältet **princip nyckel för händelsehubben** och klistra in den nya nyckeln > Klicka på **Spara**.    
7. Ett anslutnings test startar automatiskt när du sparar ändringarna, se till att det har slutförts.    
8. Fortsätt att [starta jobbet från avsnittet senaste avbruten tid](#start-your-job-from-the-last-stopped-time) .

### <a name="sql-database"></a>SQL Database

Du måste ansluta till SQL Database för att uppdatera inloggnings uppgifterna för en befintlig användare. Du kan uppdatera autentiseringsuppgifterna genom att använda Azure Portal eller ett verktyg på klient sidan, till exempel SQL Server Management Studio. Det här avsnittet visar processen för att uppdatera autentiseringsuppgifter med hjälp av Azure Portal.

1. Logga in på Azure Portal > bläddra i SQL-databasen som du använde som utdata för Stream Analytics jobbet.    
2. Från **data Utforskaren**, logga in/Anslut till databasen > Välj typ av auktorisering som **SQL server-autentisering** > typ i **inloggnings** -och **lösen ords** informationen > Välj **OK**.  
   ![Återskapa autentiseringsuppgifter för SQL Database](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. På fliken fråga ändrar du lösen ordet för en av dina användares genom att köra följande fråga (Ersätt `<user_name>` med ditt användar namn och `<new_password>` med ditt nya lösen ord):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Anteckna det nya lösen ordet.    
5. Från Azure Portal bläddrar du Stream Analytics jobb > väljer **Avbryt** och väntar på att jobbet ska stoppas.    
6. Leta upp SQL Database utdata som du vill rotera autentiseringsuppgifterna för. Uppdatera lösen ordet och spara ändringarna.    
7. Ett anslutnings test startar automatiskt när du sparar ändringarna, se till att det har slutförts.    
8. Fortsätt att [starta jobbet från avsnittet senaste avbruten tid](#start-your-job-from-the-last-stopped-time) .

### <a name="power-bi"></a>Power BI
1. Logga in på Azure Portal > bläddra i Stream Analytics jobb > Välj **stoppa** och vänta tills jobbet stoppas.    
2. Leta upp Power BI utdata som du vill förnya autentiseringsuppgifterna för > Klicka på alternativet **förnya auktorisering** (du bör se ett meddelande om att det är klart) > **Spara** ändringarna.    
3. Ett anslutnings test startar automatiskt när du sparar ändringarna, se till att det har slutförts.    
4. Fortsätt att [starta jobbet från avsnittet senaste avbruten tid](#start-your-job-from-the-last-stopped-time) .

## <a name="start-your-job-from-the-last-stopped-time"></a>Starta jobbet från den senaste stopp tiden

1. Gå till fönstret för jobb **översikt** > Välj **Starta** för att starta jobbet.    
2. Välj **när sista stopped** > Klicka på **Starta**. Observera att alternativet "när det senast stoppades" bara visas om du tidigare körde jobbet och hade vissa utdata genererade. Jobbet startas om utifrån det senaste värdet för utdata.
   ![Starta Stream Analytics jobbet](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)