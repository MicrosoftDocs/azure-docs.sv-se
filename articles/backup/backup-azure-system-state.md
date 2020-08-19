---
title: Säkerhetskopiera Windows system State till Azure
description: Lär dig hur du säkerhetskopierar system tillstånd för Windows Server-datorer till Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: a56e500cc0330a6406b4465ab5baeafa39b544aa
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263052"
---
# <a name="back-up-windows-system-state-to-azure"></a>Säkerhetskopiera Windows system State till Azure

Den här artikeln förklarar hur du säkerhetskopierar ditt Windows Server-systemtillstånd till Azure. Det är avsett att vägleda dig genom grunderna.

Om du vill veta mer om Azure Backup läser du den här [översikten](backup-overview.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) som ger dig åtkomst till Azure-tjänsten.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Ange lagringsredundans för valvet

När du skapar ett Recovery Services-valv ska du alltid kontrollera att lagringsredundansen är konfigurerad på det sätt som du vill.

1. Välj det nya valvet i fönstret **Recovery Services valv** .

    ![Välj det nya valvet i listan över Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    När du väljer valvet begränsas fönstret **Recovery Services valv** och fönstret inställningar (*som har namnet på valvet överst*) och informations fönstret för valvet öppet.

    ![Visa lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. I fönstret nytt valv inställningar använder du den lodräta bilden för att rulla ned till avsnittet hantera och väljer infrastruktur för **säkerhets kopiering**.
    Fönstret infrastruktur för säkerhets kopiering öppnas.
3. I fönstret infrastruktur för säkerhets kopiering väljer du **säkerhets kopierings konfiguration** för att öppna fönstret **säkerhets kopierings konfiguration** .

    ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Välj lämpligt alternativ för lagringsreplikering för valvet.

    ![Alternativ för lagrings konfiguration](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Valvet använder geo-redundant lagring som standard. Om du använder Azure som primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda **geo-redundant** lagring. Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy.md) och [lokalt redundant](../storage/common/storage-redundancy.md) i denna [översikt av lagringsredundans](../storage/common/storage-redundancy.md).

Nu när du har skapat ett valv konfigurerar du det för att säkerhetskopiera Windows system tillstånd.

## <a name="configure-the-vault"></a>Konfigurera valvet

1. I fönstret Recovery Services valv (för det valv som du nyss skapade) går du till avsnittet Komma igång, väljer **säkerhets kopiering**och väljer sedan **säkerhets kopierings mål**i rutan **komma igång med säkerhets kopia** .

    ![Öppna inställningar för säkerhetskopiering](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Fönstret **säkerhets kopierings mål** öppnas.

    ![Öppna mål rutan för säkerhets kopiering](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Från listrutan **Var körs din arbetsbelastning?** väljer du **Lokalt**.

    Du väljer **lokalt** eftersom din Windows Server-eller Windows-dator är en fysisk dator som inte är i Azure.

3. Från menyn **vad vill du säkerhetskopiera? väljer du** **system tillstånd**och väljer **OK**.

    ![Konfigurera filer och mappar](./media/backup-azure-system-state/backup-goal-system-state.png)

    När du klickar på OK visas en bock bredvid **säkerhets kopierings mål**och fönstret **Förbered infrastruktur** öppnas.

    ![När säkerhetskopieringsmålet har konfigurerats går du vidare och förbereder infrastrukturen](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. I fönstret **Förbered infrastruktur** väljer du **Ladda ned agent för Windows Server eller Windows-klient**.

    ![Förbered infrastruktur](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Om du använder Windows Server Essentials väljer du att ladda ned agenten för Windows Server Essentials. En popup-meny visas och du uppmanas att köra eller spara MARSAgentInstaller.exe.

    ![Dialogrutan MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. I popup-menyn Hämta väljer du **Spara**.

    Som standard sparas filen **MARSagentinstaller.exe** i mappen för nedladdningar. När installations programmet har slutförts visas ett popup-fönster där du tillfrågas om du vill köra installations programmet eller öppna mappen.

    ![MARS-installationsprogrammet har slutförts](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Du behöver inte installera agenten än. Du kan installera agenten när du har hämtat valvautentiseringsuppgifterna.

6. I fönstret **Förbered infrastruktur** väljer du **Hämta**.

    ![hämta autentiseringsuppgifter för valvet](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Valvets autentiseringsuppgifter laddas ned till mappen **hämtade filer** . När autentiseringsuppgifterna för valvet har hämtats visas ett popup-fönster där du tillfrågas om du vill öppna eller spara autentiseringsuppgifterna. Välj **Spara**. Om du av misstag väljer **Öppna**, låt dialog rutan som försöker öppna autentiseringsuppgifterna för valvet Miss lyckas. Du kommer inte att kunna öppna autentiseringsuppgifterna för valvet. Fortsätt till nästa steg. Autentiseringsuppgifterna för valvet finns i mappen **hämtade filer** .

    ![valvautentiseringsuppgifterna har hämtats](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > Autentiseringsuppgifterna för valvet måste sparas på en plats som är lokal på den Windows Server där du tänker använda agenten.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

> [!NOTE]
> Aktivering av säkerhetskopiering via Azure Portal är inte tillgängligt ännu. Använd Microsoft Azure Recovery Services-agenten för att säkerhetskopiera Windows Server System tillstånd.
>

1. Leta upp och dubbelklicka på **MARSagentinstaller.exe** från mappen för nedladdade filer (eller en annan lagringsplats).

    Installationsprogrammet visar ett antal meddelanden när Recovery Services-agenten extraheras, installeras och registreras.

    ![Autentiseringsuppgifter för att köra Recovery Services-agentinstallationsprogrammet](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Slutför installationsguiden för Microsoft Azure Recovery Services Agent. För att slutföra guiden måste du:

   * Välja en plats för installationen och cachelagringsmappen.
   * Ange information om proxyservern om du använder en proxyserver för att ansluta till Internet.
   * Ange ditt användarnamn och lösenord om du använder en autentiserad proxyserver.
   * Ange autentiseringsuppgifterna som du hämtat för valvet.
   * Spara krypteringslösenfrasen på en säker plats.

     > [!NOTE]
     > Om du tappar bort eller glömmer lösenfrasen kan Microsoft inte hjälpa dig att återställa dina säkerhetskopierade data. Spara filen på en säker plats. Det krävs för att återställa en säkerhetskopia.
     >
     >

Nu installeras agenten och datorn registreras i valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.

## <a name="back-up-windows-server-system-state"></a>Säkerhetskopiera Windows Server System-tillstånd

Den första säkerhets kopieringen innehåller två uppgifter:

* Schemalägg säkerhetskopieringen
* Säkerhetskopiera system tillstånd för första gången

För att slutföra den första säkerhetskopieringen använder du Microsoft Azure Recovery Services-agenten.

> [!NOTE]
> Du kan säkerhetskopiera system tillstånd på Windows Server 2008 R2 via Windows Server 2016. System tillstånds säkerhets kopiering stöds inte på klient-SKU: er. System tillstånd visas inte som ett alternativ för Windows-klienter eller Windows Server 2008 SP2-datorer.
>
>

### <a name="to-schedule-the-backup-job"></a>Så här schemalägger du säkerhetskopieringsjobbet

1. Öppna Microsoft Azure Recovery Services-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Starta Azure Recovery Services-agenten](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. I Recovery Services agent väljer du **Schemalägg säkerhets kopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. På sidan **komma igång** i guiden Schemalägg säkerhets kopiering väljer du **Nästa**.

4. På sidan **Välj objekt som ska säkerhets kopie ras väljer du** **Lägg till objekt**.

5. Välj **system tillstånd** och välj sedan **OK**.

6. Välj **Nästa**.

7. Välj säkerhets kopierings frekvens och bevarande princip för säkerhets kopiering av system tillstånd på efterföljande sidor.

8. På sidan bekräftelse granskar du informationen och väljer sedan **Slutför**.

9. När du har skapat säkerhets kopierings schemat väljer du **Stäng**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Säkerhetskopiera Windows Server System State för första gången

1. Se till att det inte finns några väntande uppdateringar för Windows Server som kräver en omstart.

2. I Recovery Services agent väljer du **Säkerhetskopiera nu** för att slutföra den inledande dirigeringen över nätverket.

    ![Säkerhetskopiera Windows Server nu](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Välj **system tillstånd** på skärmen **Välj säkerhets kopierings objekt** som visas och välj **Nästa**.

4. Gå igenom inställningarna på sidan Bekräftelse som guiden Säkerhetskopiera nu ska använda för att säkerhetskopiera datorn. Välj sedan **säkerhetskopiera**.

5. Stäng guiden genom att välja **Stäng** . Om du stänger guiden innan säkerhetskopieringen är klar fortsätter guiden att köras i bakgrunden.
    > [!NOTE]
    > MARS-agenten utlöses `SFC /verifyonly` som en del av förincheckningarna före varje säkerhets kopiering av system tillstånd. Detta görs för att säkerställa att filer som säkerhets kopie ras som en del av system tillstånd har rätt versioner som motsvarar Windows-versionen. Läs mer om system fils Checker (SFC) i [den här artikeln](/windows-server/administration/windows-commands/sfc).
    >

När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

  ![IR slutfört](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Har du några frågor?

Om du har frågor kan du [skicka oss feedback](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Nästa steg

* Få mer information om hur du [säkerhetskopierar Windows-datorer](backup-windows-with-mars-agent.md).
* Nu när du har säkerhetskopierat system tillstånd för Windows Server kan du [Hantera dina valv och servrar](backup-azure-manage-windows-server.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
