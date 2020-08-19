---
title: Självstudie – Säkerhetskopiera Windows Server till Azure
description: Den här självstudiekursen förklarar hur du säkerhetskopierar lokala Windows-servrar till ett Recovery Services-valv.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d2990b5950cf8812367c3a59c6cace39e4085e2a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261913"
---
# <a name="back-up-windows-server-to-azure"></a>Säkerhetskopiera Windows Server till Azure

Du kan använda Azure Backup för att skydda Windows Server från skador, attacker och katastrofer. Azure Backup innehåller ett enkelt verktyg som kallas MARS-agenten (Microsoft Azure Recovery Services). MARS-agenten installeras på en Windows Server för att skydda filer och mappar samt information om serverkonfigurationen via Systemtillstånd för Windows Server. I den här självstudiekursen beskrivs hur du kan använda MARS-agenten för att säkerhetskopiera Windows Server till Azure. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
>
> * Ladda ned och installera MARS-agenten
> * Konfigurera tidpunkter för säkerhetskopiering och kvarhållningsschema för serverns säkerhetskopior
> * Utföra en säkerhets kopiering på begäran

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på <https://portal.azure.com>.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Innan du kan säkerhetskopiera Windows Server måste du skapa en plats för de säkerhetskopior, eller återställningspunkter, som ska lagras. Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en container i Azure som lagrar säkerhetskopior från Windows Server. Följ stegen nedan för att skapa ett Recovery Services-valv i Azure-portalen.

1. Välj **Alla tjänster** på menyn till vänster och skriv **Recovery Services** i tjänstlistan. Välj **Recovery Services valv**.

   ![Öppna Recovery Services valv](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. På menyn **Recovery Services valv** väljer du **Lägg till**.

   ![Ange information för valvet](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. På menyn **Recovery Services-valt**:

    * Skriv *myRecoveryServicesVault* i **Namn**.
    * Aktuellt prenumerations-ID visas i **Prenumeration**.
    * För **Resursgrupp** väljer du **Använd befintlig** och sedan *myResourceGroup*. Om *myResourceGroup* inte finns väljer du **Skapa ny** och skriver *myResourceGroup*.
    * Från den nedrullningsbara menyn **Plats** väljer du *Europa, västra*.
    * Välj **skapa** för att skapa Recovery Services-valvet.

När valvet har skapats visas det i listan över Recovery Services-valv.

## <a name="download-recovery-services-agent"></a>Ladda ned Recovery Services-agenten

MARS-agenten (Microsoft Azure Recovery Services) skapar en koppling mellan Windows Server och Recovery Services-valvet. Följande procedur beskriver hur du laddar ned agenten till servern.

1. Från listan med Recovery Services-valv väljer du **myRecoveryServicesVault** för att öppna valvets instrumentpanel.

   ![Välj valvet för att öppna instrument panelen](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. På instrument panelen för valv väljer du **säkerhets kopiering**.

3. På **Säkerhetskopieringsmål**-menyn:

   * för **var körs din arbets belastning?**, Välj **lokalt**
   * för **Vad vill du säkerhetskopiera?**, välj **Filer och mappar** och **Systemtillstånd**.

   ![Menyn säkerhetskopiera mål](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Välj **Förbered infrastruktur** för att öppna menyn **Förbered infrastruktur** .

5. På menyn **Förbered infrastruktur** väljer du **Ladda ned agent för Windows Server eller Windows-klient** för att ladda ned *MARSAgentInstaller.exe*.

    ![Ladda ned agent för Windows Server eller Windows-klient](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Installationsprogrammet öppnar en separat webbläsare och laddar ned **MARSAgentInstaller.exe**.

6. Innan du kör den nedladdade filen väljer du **Hämta** och spara filen med autentiseringsuppgifter för **valvet** i menyn Förbered infrastruktur. Autentiseringsuppgifter krävs för att koppla MARS-agenten till Recovery Services-valvet.

    ![Ladda ned autentiseringsuppgifter för valvet](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

1. Leta upp och dubbelklicka på **MARSagentinstaller.exe** som du laddade ned.
2. **Installationsguide för Microsoft Azure Recovery Services-agenten** visas. När du går igenom guiden anger du följande information när du uppmanas till det och väljer **Registrera**.
   * Plats för installationen och cachelagringsmappen.
   * Information om proxyservern om du använder en proxyserver för att ansluta till Internet.
   * Ditt användarnamn och lösenord om du använder en autentiserad proxyserver.

     ![Installations guide för Microsoft Azure Recovery Services agent](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. I slutet av guiden väljer du **Fortsätt till registrering** och anger den fil för **valv** som du laddade ned i föregående procedur.

4. Ange vid uppmaningen en krypteringslösenfrasen för att kryptera säkerhetskopiorna från Windows Server. Spara lösen frasen på en säker plats eftersom Microsoft inte kan återställa lösen frasen om den går förlorad.

5. Välj **Slutför**.

## <a name="configure-backup-and-retention"></a>Konfigurera säkerhetskopiering och kvarhållning

Du använder Microsoft Azure Recovery Services-agenten för att schemalägga när säkerhetskopiering till Azure sker på Windows Server. Utför följande steg på den server där du laddade ned agenten.

1. Öppna Microsoft Azure Recovery Services-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

2. I Recovery Services agent-konsolen väljer du **Schemalägg säkerhets kopiering** under **Åtgärds fönstret**.

    ![Schemalägg säkerhets kopiering](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Välj **Nästa** för att navigera till sidan **Välj objekt som ska säkerhets kopie ras** .

4. Välj **Lägg till objekt** och i dialog rutan som öppnas väljer du **system tillstånd** och filer eller mappar som du vill säkerhetskopiera. Välj sedan **OK**.

5. Välj **Nästa**.

6. På sidan **Ange schema för säkerhets kopiering (system tillstånd)** anger du den tid på dagen eller vecka då säkerhets kopieringar måste utlösas för system tillstånd och välj **Nästa**.

7. På sidan **Välj bevarande princip (system tillstånd)** väljer du bevarande principen för säkerhets kopian för system tillstånd och väljer **Nästa**.

8. Välj på samma sätt säkerhetskopieringsschema och kvarhållningsprincip för valda filer och mappar.

9. På sidan **Välj första säkerhets kopierings typ** väljer du **automatiskt över nätverket**och väljer **sedan nästa**.

10. På sidan **bekräftelse** granskar du informationen och väljer **Slutför**.

11. När du har skapat säkerhets kopierings schemat väljer du **Stäng**.

## <a name="perform-an-on-demand-backup"></a>Utföra en säkerhetskopiering på begäran

Du har upprättat schemat när säkerhets kopierings jobb körs. Men du har inte säkerhetskopierat servern. Det är en bra metod för haveri beredskap att köra en säkerhets kopiering på begäran för att säkerställa data återhämtning för servern.

1. I Microsoft Azure Recovery Services agent-konsolen väljer du **Säkerhetskopiera nu**.

    ![Säkerhetskopiera nu](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. I guiden **Säkerhetskopiera nu** väljer du en från **filer och mappar** eller **system tillstånd** som du vill säkerhetskopiera och väljer **Nästa**
3. Gå igenom de inställningar på sidan **Bekräftelse** som guiden **Säkerhetskopiera nu** ska använda för att säkerhetskopiera servern. Välj sedan **säkerhetskopiera**.
4. Stäng guiden genom att välja **Stäng** . Om du stänger guiden innan säkerhetskopieringen är klar fortsätter guiden att köras i bakgrunden.
5. När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i rutan **Jobb** i MARS-agentkonsolen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Azure Portal för att:

> [!div class="checklist"]
>
> * skapar ett Recovery Services-valv
> * Ladda ned Microsoft Azure Recovery Services-agenten
> * Installera agenten
> * Konfigurera säkerhetskopiering för Windows Server
> * Utföra en säkerhetskopiering på begäran

Fortsätt till nästa självstudiekurs för att återställa filer från Azure till Windows Server

> [!div class="nextstepaction"]
> [Återställa filer från Azure till Windows Server](./tutorial-backup-restore-files-windows-server.md)
