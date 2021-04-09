---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 45b2ac73ffcb4b777572f9ad62d8aae0e8277e1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560236"
---
> [!NOTE]
> När du gör ändringar i StorSimple-adaptern för SharePoint RBS-konfigurationen måste du vara inloggad med ett användar konto som tillhör gruppen domän administratörer. Dessutom måste du komma åt konfigurations sidan från en webbläsare som körs på samma värd som central administration.
> 
> 

#### <a name="to-configure-rbs"></a>Konfigurera RBS
1. Öppna sidan Central administration av SharePoint och bläddra till **Systeminställningar**. 
2. I avsnittet **Azure-StorSimple** klickar du på **Konfigurera StorSimple-kort**.
   
    ![Konfigurera StorSimple-kortet](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. På sidan **Konfigurera StorSimple-kort** :
   
   1. Kontrol lera att kryss rutan **Aktivera redigerings Sök väg** är markerad.
   2. I text rutan anger du sökvägen till Universal Naming Convention (UNC) för BLOB Store.
      
      > [!NOTE]
      > BLOB Store-volymen måste vara värd för en iSCSI-volym som kon figurer ATS på StorSimple-enheten.

   3. Klicka på knappen **Aktivera** under var och en av innehålls databaserna som du vill konfigurera för Fjärrlagring.
      
      > [!NOTE]
      > BLOB-arkivet måste delas och nås av alla frontend-servrar (Web front-end) och det användar konto som har kon figurer ATS för SharePoint-servergruppen måste ha åtkomst till resursen.
      
      ![Aktivera RBS-providern](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      När du aktiverar eller inaktiverar RBS visas även följande meddelande.
      
      ![Konfigurera StorSimple-Adapter aktivera inaktivera](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Klicka på knappen **Uppdatera** för att tillämpa konfigurationen. När du klickar på knappen **Uppdatera** uppdateras RBS-konfigurationens status på alla WFE-servrar och hela Server gruppen är RBS-aktiverad. Följande meddelande visas.
      
      ![Kort konfigurations meddelande](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Om du konfigurerar RBS för en SharePoint-grupp med ett mycket stort antal databaser (större än 200) kan webb sidan för Central administration av SharePoint vara timeout. Om detta inträffar uppdaterar du sidan. Detta påverkar inte konfigurations processen.

4. Verifiera konfigurationen:
   
   1. Logga in på webbplatsen för Central administration av SharePoint och bläddra till sidan **Konfigurera StorSimple-kort** .
   2. Kontrol lera konfigurations informationen för att se till att de matchar de inställningar som du har angett. 
5. Kontrol lera att RBS fungerar korrekt:
   
   1. Ladda upp ett dokument till SharePoint. 
   2. Bläddra till den UNC-sökväg som du har konfigurerat. Se till att katalog strukturen för RBS har skapats och att den innehåller det överförda objektet.
6. Valfritt Du kan använda Microsoft RBS `Migrate()` PowerShell-cmdleten som ingår i SharePoint för att migrera befintligt BLOB-innehåll till StorSimple-enheten. Mer information finns i [Migrera innehåll till eller från RBS i SharePoint 2013][6] eller [Migrera innehåll till eller från RBS (SharePoint Foundation 2010)][7].
7. Valfritt I test installationer kan du kontrol lera att BLOBarna har flyttats ut från innehålls databasen på följande sätt: 
   
   1. Starta SQL Management Studio.
   2. Kör frågan ListBlobsInDB_2010. SQL eller ListBlobsInDB_2013. SQL enligt följande.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Om RBS har kon figurer ATS korrekt ska ett NULL-värde visas i kolumnen SizeOfContentInDB för alla objekt som har laddats upp och utlösts med RBS.
8. Valfritt När du har konfigurerat RBS och flyttat allt BLOB-innehåll till StorSimple-enheten kan du flytta innehålls databasen till enheten. Om du väljer att flytta innehålls databasen, rekommenderar vi att du konfigurerar lagring av innehålls databaser på enheten som en primär volym. Använd sedan etablerade SQL Server metod tips för att migrera innehålls databasen till StorSimple-enheten. 
   
   > [!NOTE]
   > Det går bara att flytta innehålls databasen till enheten i StorSimple 8000-serien (den stöds inte för serien 5000 eller 7000).
   
   Om du lagrar BLOBBAR och innehålls databasen i separata volymer på StorSimple-enheten, rekommenderar vi att du konfigurerar dem i samma volym behållare. Detta säkerställer att de säkerhets kopie ras tillsammans.
   
   > [!WARNING]
   > Om du inte har aktiverat RBS rekommenderar vi inte att du flyttar innehålls databasen till StorSimple-enheten. Det här är en testad konfiguration.
   
9. Gå till nästa steg: [Konfigurera skräp insamling](#configure-garbage-collection).

[6]: /SharePoint/administration/migrate-content-into-or-out-of-rbs
[7]: /previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)