---
title: Offlinesäkerhetskopiering för DPM och Azure Backup Server
description: Med Azure Backup kan du skicka data från nätverket med Azure import/export-tjänsten. I den här artikeln beskrivs arbets flödet offline-säkerhetskopiering för DPM och Azure Backup Server.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98701821"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>Arbets flöde för offline-säkerhetskopiering för DPM och Azure Backup Server (MABS)

>[!IMPORTANT]
> De här stegen gäller för DPM 2019 UR1 (eller senare) och MABS v3-UR1 (eller senare).

System Center Data Protection Manager och Azure Backup Server (MABS) integreras med Azure Backup och använder flera inbyggda effektivitets vinster som sparar nätverks-och lagrings kostnader under de första fullständiga säkerhets kopiorna av data till Azure. De första fullständiga säkerhets kopieringarna överför ofta stora mängder data och kräver mer nätverks bandbredd jämfört med efterföljande säkerhets kopieringar som bara överför delta/steg. Azure Backup komprimerar de första säkerhets kopiorna. Genom processen för dirigering av dirigering kan Azure Backup använda diskar för att ladda upp komprimerade första säkerhets kopierings data offline till Azure.

Offline-seeding-processen för Azure Backup är nära integrerad med [Azure import/export-tjänsten](../import-export/storage-import-export-service.md). Du kan använda den här tjänsten för att överföra data till Azure med hjälp av diskar. Om du har terabyte (TBs) av inledande säkerhets kopierings data som behöver överföras via ett nätverk med hög latens och låg bandbredd, kan du använda arbets flödet offline-seeding för att leverera den första säkerhets kopian på en eller flera hård diskar till ett Azure-datacenter. Den här artikeln innehåller en översikt och ytterligare steg som slutför det här arbets flödet för System Center Data Protection Manager (DPM) och Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Processen för säkerhets kopiering offline för Microsoft Azure Recovery Services (MARS) Agent skiljer sig från DPM och MABS. Information om hur du använder säkerhets kopiering offline med MARS-agenten finns i [arbets flöde för offline-säkerhetskopiering i Azure Backup](backup-azure-backup-import-export.md). Offline-säkerhetskopiering stöds inte för säkerhets kopiering av system tillstånd som gjorts med hjälp av Azure Backup agenten.
>
> MABS UR1-uppdateringen ger också förhands granskningen för säkerhets kopiering offline med Azure Data Box i MABS. Kontakta [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) för att få mer information.

## <a name="overview"></a>Översikt

Med funktionen offline-seeding i Azure Backup och tjänsten Azure import/export är det enkelt att ladda upp data offline till Azure med hjälp av diskar. Säkerhets kopierings processen offline omfattar följande steg:

> [!div class="checklist"]
>
> * Säkerhetskopierade data skrivs till en mellanlagringsplats i stället för att skickas över nätverket.
> * Data på mellanlagringsplatsen skrivs sedan till en eller flera SATA-diskar med hjälp av *AzureOfflineBackupDiskPrep* -verktyget.
> * Ett Azure import-jobb skapas automatiskt av verktyget.
> * SATA-enheterna skickas sedan till närmaste Azure-datacenter.
> * När överföringen av säkerhets kopierings data till Azure är färdig Azure Backup kopierar säkerhetskopierade data till säkerhets kopierings valvet och de stegvisa säkerhets kopiorna schemaläggs.

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att följande krav är uppfyllda innan du startar arbets flödet offline-säkerhetskopiering:

* Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) har skapats. Skapa en genom att följa stegen i [skapa en Recovery Services Vault](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)självstudie – säkerhets kopiering-Windows-Server-till-Azure # Create-a-Recovery-Services-valv).
* Se till att endast den [senaste versionen av Microsoft Azure Recovery Services-agenten](https://aka.ms/azurebackup_agent) är installerad på SC DPM eller Mabs och är registrerad på Recovery Services-valvet.
* Samlad uppdatering 1 installeras på SC DPM 2019 eller MABS v3.

  > [!NOTE]
  > Med DPM 2019 UR1 och MABS v3-UR1, autentiseras offline-dirigeringen med hjälp av Azure Active Directory.

* På DPM-eller MABS-servern kontrollerar du att Microsoft Edge eller Internet Explorer 11 är installerat och att Java Script är aktiverat.
* Skapa ett Azure Storage-konto i samma prenumeration som Recovery Services-valvet.
* Kontrol lera att du har de [behörigheter som krävs](../active-directory/develop/howto-create-service-principal-portal.md) för att skapa Azure Active Directory-programmet. Arbets flödet offline-säkerhetskopiering skapar ett Azure Active Directory-program i prenumerationen som är kopplad till Azure Storage kontot. Målet med programmet är att tillhandahålla Azure Backup med säker och begränsad åtkomst till Azure import-tjänsten som krävs för arbets flödet offline-säkerhetskopiering.
* Registrera Microsoft. ImportExport-resurs leverantören med prenumerationen som innehåller det Azure Storage kontot. Så här registrerar du resurs leverantören:
    1. I huvud menyn väljer du **prenumerationer**.
    2. Om du prenumererar på flera prenumerationer väljer du den prenumeration som du använder för säkerhets kopiering offline. Om du bara använder en prenumeration visas din prenumeration.
    3. I menyn prenumeration väljer du **resurs leverantörer** för att visa listan över leverantörer.
    4. I listan över providers bläddrar du ned till Microsoft. ImportExport. Om statusen är NotRegistered väljer du **Registrera**.

       ![Resurs leverantören registreras](./media/backup-azure-backup-server-import-export/register-import-export.png)

* En mellanlagringsplats, som kan vara en nätverks resurs eller ytterligare en enhet på datorn, intern eller extern, med tillräckligt disk utrymme för att lagra din ursprungliga kopia skapas. Om du till exempel vill säkerhetskopiera en fil server med 500 GB kontrollerar du att mellanlagringsområdet är minst 500 GB. (En mindre mängd används på grund av komprimering.)
* För diskar som har skickats till Azure, se till att endast 2,5-tums SSD-eller 2,5-tums eller 3,5-tums inbyggda SATA II/III-hårddiskar används. Du kan använda hård diskar upp till 10 TB. Läs [dokumentationen för Azure import/export-tjänsten](../import-export/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen enheter som stöds av tjänsten.
* SATA-enheterna måste vara anslutna till en dator (kallas *kopierings dator*) från den plats där kopian av säkerhets kopierings data från mellanlagringsplatsen till SATA-enheterna görs. Se till att BitLocker är aktiverat på kopierings datorn.

## <a name="workflow"></a>Arbetsflöde

Informationen i det här avsnittet hjälper dig att slutföra arbets flödet för offline-säkerhetskopiering så att dina data kan skickas till ett Azure-datacenter och laddas upp till Azure Storage. Om du har frågor om import tjänsten eller någon del av processen går du till [översikts dokumentationen för import tjänsten](../import-export/storage-import-export-service.md) som refereras tidigare.

## <a name="initiate-offline-backup"></a>Starta säkerhets kopiering offline

1. När du skapar en ny skydds grupp med onlineskydd eller lägger till onlineskydd i befintlig skydds grupp visas följande skärm. Om du vill välja inledande metod för replikering online väljer du **överför med min egen disk** och väljer **Nästa**.

    ![Sidan importera](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Azures inloggnings sida öppnas. Logga in med ditt Azure-användarkonto, som har *ägar* rollen behörighet för Azure-prenumerationen.

    ![Azures inloggnings sida](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. Ange indata på sidan **Använd din egen disk** .

   ![Indata för att använda din egen disk](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   Beskrivningen av indata är följande:

   * **Mellanlagringsplats**: den tillfälliga lagrings plats som den första säkerhets kopian skrivs till. Mellanlagrings platsen kan vara en nätverks resurs eller en lokal dator. Om kopierings datorn och käll datorn skiljer sig anger du sökvägen till den fullständiga nätverks platsen för mellanlagringsplatsen.
   * **Azure Resource Manager lagrings konto**: namnet på resurs hanterarens typ lagrings konto (generell användning v1 eller generell användning v2) i valfri Azure-prenumeration.
   * **Azure Storage behållare**: namnet på den mål-Blob Storage-behållare i Azure Storage-kontot där säkerhetskopierade data har importer ATS.
   * **ID för Azure-prenumeration**: ID för Azure-prenumerationen där Azure Storage-kontot skapas.
   * **Namn på Azure import-jobb**: det unika namnet som Azure import service och Azure Backup spåra överföringen av data som skickas på diskar till Azure.

    Spara **mellanlagringsplatsen** och namn informationen för **Azure import-jobbet** som du har angett. Det krävs att du förbereder diskarna.

4. Slutför arbets flödet för att skapa eller uppdatera skyddet. Och om du vill starta säkerhets kopian offline högerklickar du på **skydds gruppen** och väljer sedan alternativet för att **skapa återställnings punkt** . Sedan väljer du alternativet **onlineskydd** .

   ![Skapa återställnings punkt](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. Övervaka jobbet för att skapa replik online i fönstret övervakning. Jobbet bör slutföras med varningen som *väntar på att Azure import-jobbet ska* slutföras.

   ![Slutför återställnings punkt](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. När åtgärden har slutförts är mellanlagringsplatsen redo att användas för disk förberedelse.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbered SATA-enheter och leverera till Azure

*AzureOfflineBackupDiskPrep* -verktyget förbereder de SATA-enheter som skickas till närmaste Azure-datacenter. Det här verktyget är tillgängligt i installations katalogen för Azure Backup Agent (i följande sökväg): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Gå till katalogen och kopiera **AzureOfflineBackupDiskPrep** -katalogen till en annan dator där SATA-enheterna är anslutna. På datorn med de anslutna SATA-enheterna ser du till att:

   * Kopierings datorn kan komma åt mellanlagringsplatsen för arbets flödet offline-dirigering med hjälp av samma nätverks Sök väg som tillhandahölls i arbets flödet i avsnittet "Starta säkerhets kopiering offline".
   * BitLocker är aktiverat på kopierings datorn.
   * Azure PowerShell 3.7.0 har installerats på kopierings datorn (krävs inte om du kör AzureOfflineBackupDiskPrep-verktyget på DPM-eller MABS-servern).
   * De senaste kompatibla webbläsarna (Microsoft Edge eller Internet Explorer 11) är installerade och Java Script är aktiverat.
   * Kopierings datorn har åtkomst till Azure Portal. Vid behov kan kopierings datorn vara samma som käll datorn.

     > [!IMPORTANT]
     > Om käll datorn är en virtuell dator är det obligatoriskt att använda en annan fysisk server eller klient dator som kopierings dator.

1. Öppna en kommando tolk med förhöjd behörighet på kopierings datorn med katalogen *AzureOfflineBackupDiskPrep* Utility som den aktuella katalogen. Kör följande kommando:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Parameter | Beskrivning |
    | --- | --- |
    | s: &lt; *sökväg för mellanlagringsplats*&gt; |Den här obligatoriska indatatypen används för att ange sökvägen till den mellanlagringsplats som du angav i arbets flödet i avsnittet "påbörja säkerhets kopiering offline". |
    | p: &lt; *sökväg till PublishSettingsFile*&gt; |Den här alternativa indatan används för att ange sökvägen till filen för Azure Publish-inställningar. |

    När du kör kommandot begär verktyget valet av Azure import-jobb som motsvarar de enheter som måste förberedas. Om bara ett enda import jobb är associerat med den angivna mellanlagringsplatsen visas en skärm som liknar den som följer.

      ![Disk förberedelse konsol](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Ange enhets beteckningen utan avslutande kolon för den monterade disk som du vill förbereda för överföring till Azure.
1. Ange en bekräftelse för enhetens formatering vid uppmaning.
1. Du uppmanas att logga in på din Azure-prenumeration. Skriv in dina inloggningsuppgifter.

    ![Azure-inloggnings skärm](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopierade data. Du kan behöva koppla ytterligare diskar när du uppmanas att göra det om den angivna disken inte har tillräckligt med utrymme för säkerhets kopierings data. <br/>

    I slutet av lyckad körning av verktyget har kommando tolken tre delar av informationen:
    * En eller flera diskar som du har angett är för beredda för leverans till Azure.
    * Du får en bekräftelse på att ditt import jobb har skapats. Import jobbet använder det namn som du har angett.
    * Verktyget visar leverans adressen för Azure-datacentret.

     ![Förberedelse av Azure-disk slutförd](./media/backup-azure-backup-server-import-export/console.png)

1. I slutet av kommando körningen visas även alternativet att uppdatera leverans information.

1. Leverera diskarna till den adress som verktyget tillhandahåller och behåll spårnings numret för framtida bruk.

   > [!IMPORTANT]
   > Det går inte att ha samma spårnings nummer på två Azure import-jobb. Se till att enheter som förbereds av verktyget under ett enda Azure import-jobb levereras tillsammans i ett enda paket och att det finns ett enda unikt spårnings nummer för paketet. Kombinera inte enheter som har bearbetats som en del av olika Azure import-jobb i ett enda paket.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Uppdatera skeppnings information för Azure import-jobbet

I följande procedur uppdateras leverans informationen för Azure import-jobbet. Den här informationen innehåller information om:

* namnet på den operatör som levererar diskarna till Azure
* returnera leverans information för dina diskar

   1. Logga in på din Azure-prenumeration.
   2. I huvud menyn väljer du **alla tjänster** och i dialog rutan alla tjänster skriver du import. När du ser **import/export-jobb** väljer du det.
       ![Ange leverans information](./media/backup-azure-backup-server-import-export/search-import-job.png)

       Listan över **import/export-jobb** -menyn öppnas och listan över alla import/export-jobb i den valda prenumerationen visas.

   3. Om du har flera prenumerationer måste du välja den prenumeration som används för att importera säkerhets kopierings data. Välj sedan det nyskapade import jobbet för att öppna dess information.

       ![Granska skeppnings information](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. På menyn Inställningar för import jobbet väljer du **Hantera skeppnings information** och anger information om retur leveranser.

       ![Lagra skeppnings information](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. När du har spårnings numret från transport företaget väljer du banderollen på sidan Översikt för Azure import jobb och anger följande information:

      > [!IMPORTANT]
      > Se till att informationen som operatör och spårningsnummer uppdateras inom två veckor efter det att importjobb skapats i Azure. Om du inte verifierar informationen inom två veckor kan det leda till att jobbet tas bort och att enheterna inte bearbetas.

      ![Jobb översikt](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![Spårnings information](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tid för att bearbeta enheterna

Hur lång tid det tar att bearbeta ett Azure-importerat jobb varierar. Process tiden beror på faktorer som leverans tid, Jobbtyp, typ och storlek för de data som kopieras och storleken på de diskar som tillhandahålls. Tjänsten Azure import/export har inget service avtal. När diskarna har tagits emot strävar tjänsten efter att slutföra säkerhets kopieringen av säkerhets kopian till ditt Azure Storage-konto på 7 till 10 dagar. I nästa avsnitt beskrivs hur du kan övervaka statusen för Azure import-jobbet.

### <a name="monitor-azure-import-job-status"></a>Övervaka status för Azure import-jobb

Du kan övervaka statusen för ditt import jobb från Azure Portal genom att gå till sidan för **import/export-jobb** och välja ditt jobb. Mer information om import jobbens status finns i artikeln om export i [lagrings import](../import-export/storage-import-export-service.md) .

### <a name="complete-the-workflow"></a>Slutför arbets flödet

När import jobbet är klart är de första säkerhets kopierings data tillgängliga i ditt lagrings konto. Vid nästa schemalagda säkerhets kopiering Azure Backup kopierar innehållet i data från lagrings kontot till Recovery Services-valvet.

Vid tidpunkten för nästa schemalagda körning av repliken av online-replikering utför Data Protection Manager stegvis säkerhets kopiering över den första säkerhets kopian.

## <a name="next-steps"></a>Nästa steg

* Om du har frågor om Azures arbets flöde för import/export-tjänsten kan du läsa mer i [använda tjänsten Microsoft Azure import/export för att överföra data till Blob Storage](../import-export/storage-import-export-service.md).
