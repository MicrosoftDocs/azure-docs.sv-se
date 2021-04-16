---
title: Hantera och övervaka MARS-agentsäkerhetskopior
description: Lär dig hur du hanterar och övervakar säkerhetskopieringar Microsoft Azure Recovery Services-agenten (MARS) med hjälp av Azure Backup tjänsten.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4306f01d608542f7453b32b32a1a6894c2379159
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515030"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Hantera Microsoft Azure Recovery Services-agentsäkerhetskopior (MARS) med hjälp av Azure Backup tjänsten

Den här artikeln beskriver hur du hanterar filer och mappar som säkerhetskopieras med Microsoft Azure Recovery Services-agenten.

## <a name="modify-a-backup-policy"></a>Ändra en säkerhetskopieringspolicy

När du ändrar säkerhetskopieringspolicyn kan du lägga till nya objekt, ta bort befintliga objekt från säkerhetskopian eller undanta filer från att säkerhetskopieras med undantagsinställningar.

- **Lägg till objekt** använder endast det här alternativet för att lägga till nya objekt som ska bakbacka. Om du vill ta bort befintliga objekt använder **du alternativet Ta bort** objekt eller **Undantagsinställningar.**  
- **Ta bort objekt** använder det här alternativet för att ta bort objekt från att säkerhetskopieras.
  - Använd **Undantagsinställningar** för att ta bort alla objekt i en volym i stället för **Ta bort objekt.**
  - Om du rensar alla val i en volym bevaras gamla säkerhetskopior av objekten enligt kvarhållningsinställningarna vid tidpunkten för den senaste säkerhetskopieringen, utan ändringsomfång.
  - Om du markerar de här objekten leder det till en första fullständig säkerhetskopiering och nya principändringar tillämpas inte på gamla säkerhetskopior.
  - Om du avmarkerar hela volymen behålls tidigare säkerhetskopiering utan något omfång för ändring av kvarhållningsprincipen.
- **Undantagsinställningar** använder det här alternativet för att undanta vissa objekt från att säkerhetskopieras.

### <a name="add-new-items-to-existing-policy"></a>Lägga till nya objekt i en befintlig princip

1. I **Åtgärder väljer** du **Schemalägg säkerhetskopiering.**

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)

2. På **fliken Välj principobjekt** väljer du Ändra **säkerhetskopieringsschema för dina filer och mappar och** väljer **Nästa.**

    ![Välj principobjekt](./media/backup-azure-manage-mars/select-policy-items.png)

3. På **fliken Ändra eller stoppa schemalägg säkerhetskopiering** väljer du Gör ändringar i **säkerhetskopieringsobjekt eller tider** och väljer **Nästa.**

    ![Ändra eller schemalägga säkerhetskopiering](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. På **fliken Välj objekt att säkerhetskopiera** väljer du Lägg till **objekt** för att lägga till de objekt som du vill säkerhetskopiera.

    ![Ändra eller schemalägga säkerhetskopiering för att lägga till objekt](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. I **fönstret Välj** objekt väljer du filer eller mappar som du vill lägga till och väljer **OK.**

    ![Välj objekten](./media/backup-azure-manage-mars/select-item.png)

6. Slutför åtgärden genom att utföra nästa **steg** och välja Slutför.

### <a name="add-exclusion-rules-to-existing-policy"></a>Lägga till undantagsregler i befintlig princip

Du kan lägga till undantagsregler för att hoppa över filer och mappar som du inte vill ska säkerhetskopieras. Du kan göra detta när du definierar en ny princip eller ändrar en befintlig princip.

1. I fönstret Åtgärder väljer du **Schemalägg säkerhetskopiering.** Gå till **Välj objekt att säkerhetskopiera** och välj **Undantagsinställningar.**

    ![Undantagsinställningar](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. I **Undantagsinställningar** väljer du **Lägg till undantag.**

    ![Lägg till undantag](./media/backup-azure-manage-mars/add-exclusion.png)

3. Från **Välj objekt att undanta** bläddrar du bland filerna och mapparna och väljer objekt som du vill undanta och väljer **OK.**

    ![Välj de objekt som ska undantas](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Som standard **undantas alla undermappar** i de valda mapparna. Du kan ändra detta genom att välja **Ja** eller **Nej.** Du kan redigera och ange vilka filtyper som ska undantas enligt nedan:

    ![Välj undermappstyper](./media/backup-azure-manage-mars/subfolders-type.png)

5. Slutför åtgärden genom att utföra nästa **steg** och välja Slutför.

### <a name="remove-items-from-existing-policy"></a>Ta bort objekt från befintlig princip

1. I fönstret Åtgärder väljer du **Schemalägg säkerhetskopiering.** Gå till **Välj objekt för att säkerhetskopiera**. I listan väljer du de filer och mappar som du vill ta bort från säkerhetskopieringsschemat och väljer Ta **bort objekt.**

    ![Välj de objekt som ska tas bort](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Var försiktig när du tar bort en volym helt från principen.  Om du behöver lägga till den igen behandlas den som en ny volym. Nästa schemalagda säkerhetskopiering utför en första säkerhetskopiering (fullständig säkerhetskopiering) i stället för inkrementell säkerhetskopiering. Om du tillfälligt behöver ta bort och lägga till objekt senare rekommenderar  vi att du använder **Undantagsinställningar** i stället för Ta bort objekt för att säkerställa inkrementell säkerhetskopiering i stället för fullständig säkerhetskopiering.

2. Slutför åtgärden genom att slutföra **nästa steg** och välja Slutför.

## <a name="stop-protecting-files-and-folder-backup"></a>Sluta skydda säkerhetskopiering av filer och mappar

Det finns två sätt att sluta skydda säkerhetskopiering av filer och mappar:

- **Stoppa skyddet och behåll säkerhetskopierade data**.
  - Det här alternativet stoppar alla framtida säkerhetskopieringsjobb från att skyddas.
  - Azure Backup fortsätter att behålla alla befintliga återställningspunkter.  
  - Du kommer att kunna återställa säkerhetskopierade data för återställningspunkter som inte har visats.
  - Om du väljer att återuppta skyddet kan du använda alternativet *Återaktivera schema för säkerhetskopiering.* Därefter bevaras data baserat på den nya bevarandeprincipen.
- **Stoppa skyddet och ta bort säkerhetskopierade data**.
  - Det här alternativet stoppar alla framtida säkerhetskopieringsjobb från att skydda dina data och tar bort alla återställningspunkter.
  - Du får ett e-postmeddelande om att säkerhetskopieringsdata tas bort med meddelandet *Dina data för det här säkerhetskopieringsobjektet har tagits bort. Dessa data är* tillfälligt tillgängliga i 14 dagar. Därefter tas de bort permanent och rekommenderad åtgärd Återaktivera skydd för säkerhetskopieringsobjektet inom *14 dagar* för att återställa dina data.
  - Återuppta skyddet genom att återaktivera skyddet inom 14 dagar från borttagningsåtgärden.

### <a name="stop-protection-and-retain-backup-data"></a>Stoppa skyddet och behåll säkerhetskopierade data

1. Öppna MARS-hanteringskonsolen, gå till **åtgärdsfönstret** och **välj Schemalägg säkerhetskopiering.**

    ![Välj schemalägg säkerhetskopiering](./media/backup-azure-manage-mars/mars-actions.png)
1. På sidan **Välj principobjekt** väljer du **Ändra ett säkerhetskopieringsschema för dina filer och mappar** och väljer **Nästa.**

    ![Välj principobjekt](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. På sidan **Ändra eller stoppa en schemalagd säkerhetskopiering** väljer du Sluta använda det här säkerhetskopieringsschemat, men behåller de lagrade säkerhetskopiorna **tills ett schema aktiveras igen.** Välj **Nästa**.

    ![Stoppa en schemalagd säkerhetskopiering.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. I **Pausa schemalagd säkerhetskopiering** granskar du informationen och väljer **Slutför.**

    ![Pausa en schemalagd säkerhetskopiering.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. I **Ändra säkerhetskopieringsförloppet** kontrollerar du att pausen för din schemasäkerhetskopiering har statusen Lyckades och **väljer** Stäng för att slutföra.

### <a name="stop-protection-and-delete-backup-data"></a>Stoppa skyddet och ta bort säkerhetskopierade data

1. Öppna MARS-hanteringskonsolen, gå till **fönstret Åtgärder** och välj **Schemalägg säkerhetskopiering.**
2. På sidan **Ändra eller stoppa en schemalagd säkerhetskopiering** väljer du Sluta använda det här **säkerhetskopieringsschemat och tar bort alla lagrade säkerhetskopior.** Välj **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhetskopiering.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. På sidan **Stoppa en schemalagd säkerhetskopiering** väljer du **Slutför**.

    ![Stoppa en schemalagd säkerhetskopiering och välj Slutför](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Du uppmanas att ange en PIN-kod för säkerhet (personligt id-nummer) som du måste generera manuellt. Det gör du genom att först logga in på Azure Portal.
5. Gå till **Inställningar för Recovery Services-valvEgenskaper**  >    >  .
6. Under **Pin-kod för** säkerhet väljer du **Generera.** Kopiera PIN-koden. PIN-koden är endast giltig i fem minuter.
7. Klistra in PIN-koden i hanteringskonsolen och välj **sedan OK.**

    ![Generera en PIN-kod för säkerhet.](./media/backup-azure-delete-vault/security-pin.png)

8. På sidan **Ändra säkerhetskopieringsförloppet** visas följande meddelande: *Borttagna säkerhetskopieringsdata sparas i 14 dagar. Därefter tas säkerhetskopierade data bort permanent.*  

    ![Ändra säkerhetskopieringsförloppet](./media/backup-azure-delete-vault/deleted-backup-data.png)

När du har tagit bort de lokala säkerhetskopieringsobjekten följer du nästa steg från portalen.

## <a name="re-enable-protection"></a>Återaktivera skydd

Om du har stoppat skyddet samtidigt som du behåller data och valt att återuppta skyddet kan du återaktivera säkerhetskopieringsschemat med hjälp av ändra säkerhetskopieringspolicyn.

1. På **Åtgärder väljer** du **Schemalägg säkerhetskopiering.**
1. Välj **Återaktivera säkerhetskopieringsschema. Du kan också ändra säkerhetskopieringsobjekt eller tider** och välja **Nästa.**<br>

    ![Återaktivera schemat för säkerhetskopiering](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. I **Välj objekt att säkerhetskopiera** väljer du **Nästa.**

    ![Välj objekt som ska säkerhetskopieras](./media/backup-azure-manage-mars/re-enable-next.png)
1. I **Ange schema för säkerhetskopiering** anger du säkerhetskopieringsschemat och väljer **Nästa.**
1. I **Välj kvarhållningsprincip** anger du kvarhållningstid och väljer **Nästa.**
1. Slutligen granskar **du principinformationen** på skärmen Bekräftelse och väljer **Slutför.**

## <a name="re-generate-passphrase"></a>Generera lösenfrasen på ett annat sätt

En lösenfras används för att kryptera och dekryptera data vid backning eller återställning av din lokala eller lokala dator med HJÄLP av MARS-agenten till eller från Azure. Om du har förlorat eller glömt lösenfrasen kan du återskapa lösenfrasen (förutsatt att datorn fortfarande är registrerad med Recovery Services-valvet och säkerhetskopieringen har konfigurerats) genom att följa dessa steg:

1. Från MARS-agentkonsolen går du till **Åtgärdsfönstret**  >  **Ändra egenskaper** >. Gå sedan till **fliken Kryptering.**<br>
1. Markera **kryssrutan Ändra lösenfras.**<br>
1. Ange en ny lösenfras eller välj **Generera lösenfras.**
1. Välj **Bläddra** för att spara den nya lösenfrasen.

    ![Generera lösenfras.](./media/backup-azure-manage-mars/passphrase.png)

1. Välj **OK för** att tillämpa ändringarna.  Om [säkerhetsfunktionen är](./backup-azure-security-feature.md#enable-security-features) aktiverad på Azure Portal för Recovery Services-valvet uppmanas du att ange pin-koden för säkerhet. Följ stegen i den här artikeln för att få [PIN-koden.](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)<br>
1. Klistra in säkerhets-PIN-koden från portalen och **välj OK** för att tillämpa ändringarna.<br>

    ![Klistra in säkerhets-PIN-koden](./media/backup-azure-manage-mars/passphrase2.png)
1. Se till att lösenfrasen sparas på ett säkert sätt på en annan plats (förutom källdatorn), helst i Azure Key Vault. Håll reda på alla lösenfraser om du har flera datorer som säkerhetskopieras med MARS-agenterna.

## <a name="managing-backup-data-for-unavailable-machines"></a>Hantera säkerhetskopierade data för otillgängliga datorer

I det här avsnittet beskrivs ett scenario där din källdator som skyddades med MARS inte längre är tillgänglig eftersom den har tagits bort, skadats, infekterats av skadlig kod/utpressningstrojaner eller inaktiverats.

För dessa datorer ser Azure Backup-tjänsten till att den senaste återställningspunkten inte upphör att gälla (det vill säga att den inte rensas) enligt de kvarhållningsregler som anges i säkerhetskopieringsprincipen. Därför kan du återställa datorn på ett säkert sätt.  Tänk på följande scenarier som du kan utföra på säkerhetskopierade data:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Scenario 1: Källdatorn är inte tillgänglig och du behöver inte längre behålla säkerhetskopierade data

- Du kan ta bort säkerhetskopierade data från Azure Portal med hjälp av stegen i den [här artikeln.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Scenario 2: Källdatorn är inte tillgänglig och du måste behålla säkerhetskopierade data

Hantering av säkerhetskopieringspolicyn för MARS sker via MARS-konsolen och inte via portalen. Om du behöver utöka kvarhållningsinställningarna för befintliga återställningspunkter innan de upphör att gälla måste du återställa datorn, installera MARS-konsolen och utöka principen.

- Utför följande steg för att återställa datorn:
  1. [Återställa den virtuella datorn till en alternativ måldator](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Återskapa måldatorn med samma värdnamn som källdatorn
  1. Installera agenten och omregistrera till samma valv och med samma lösenfras
  1. Starta MARS-klienten för att utöka kvarhållningstiden enligt dina krav
- Den nyligen återställda datorn, som skyddas med MARS, fortsätter att göra säkerhetskopior.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Konfigurera antivirus för MARS-agenten

Vi rekommenderar följande konfiguration för ditt antivirusprogram för att undvika konflikter med MARS-agentens drift.

1. **Lägg till sökvägs** exkluderingar: Undvik försämring av prestanda och möjliga konflikter genom att utesluta följande sökvägar från realtidsövervakning av antivirusprogram:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` och undermappar
    1. **Scratch-mapp:** Om den scratch-mappen inte finns på standardplatsen lägger du även till den i undantagen.  [Här finns anvisningar för att](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-) fastställa platsen för den nya mappen.
1. **Lägg till binära undantag:** Undvik försämring av säkerhetskopierings- och konsolaktiviteter genom att undanta processer för följande binärfiler från realtidsövervakning av antivirusprogram:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Även om det räcker att exkludera dessa sökvägar för de flesta antivirusprogram kan vissa fortfarande störa MARS-agentåtgärder. Om du får oväntade fel avinstallerar du antivirusprogrammet tillfälligt och övervakar om problemet försvinner. Om detta löser problemet kan du kontakta leverantören av antivirusprogram för att få hjälp med korrekt konfiguration av produkten.

## <a name="next-steps"></a>Nästa steg

- Information om vilka scenarier och begränsningar som stöds finns i [supportmatrisen för MARS-agenten.](./backup-support-matrix-mars-agent.md)
- Läs mer om [kvarhållningsbeteendet för säkerhetskopieringspolicyer på begäran.](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)
- Vanliga frågor och svar finns i Vanliga frågor [och svar om MARS-agenten.](backup-azure-file-folder-backup-faq.yml)
