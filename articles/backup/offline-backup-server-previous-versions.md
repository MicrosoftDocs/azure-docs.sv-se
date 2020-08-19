---
title: Säkerhets kopiering offline för Data Protection Manager (DPM) och Microsoft Azure Backup-Server (MABS)-tidigare versioner
description: Med Azure Backup kan du skicka data från nätverket med Azure import/export-tjänsten. I den här artikeln beskrivs arbets flödet offline-säkerhetskopiering för tidigare versioner av DPM och Azure Backup Server.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: e986baaf6ac2943bd210761ff2194eacdee5984a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261930"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>Arbets flöde för offline-säkerhetskopiering för DPM och Azure Backup Server (tidigare versioner)

>[!IMPORTANT]
>De här stegen gäller för DPM 2019 RTM och tidigare versioner och MABS v3 RTM och tidigare versioner.

Azure Backup har flera inbyggda effektivitets vinster som sparar kostnader för nätverk och lagring under de första fullständiga säkerhets kopieringarna av data till Azure. De första fullständiga säkerhets kopieringarna överför ofta stora mängder data och kräver mer nätverks bandbredd jämfört med efterföljande säkerhets kopieringar som bara överför delta/steg. Azure Backup komprimerar de första säkerhets kopiorna. Genom processen för dirigering av dirigering kan Azure Backup använda diskar för att ladda upp komprimerade första säkerhets kopierings data offline till Azure.

Offline-seeding-processen för Azure Backup är nära integrerad med [Azure import/export-tjänsten](../storage/common/storage-import-export-service.md). Du kan använda den här tjänsten för att överföra data till Azure med hjälp av diskar. Om du har terabyte (TBs) av inledande säkerhets kopierings data som behöver överföras via ett nätverk med hög latens och låg bandbredd, kan du använda arbets flödet offline-seeding för att leverera den första säkerhets kopian på en eller flera hård diskar till ett Azure-datacenter. Den här artikeln innehåller en översikt och ytterligare steg som slutför det här arbets flödet för System Center Data Protection Manager (DPM) och Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Processen för säkerhets kopiering offline för Microsoft Azure Recovery Services (MARS) Agent skiljer sig från DPM och MABS. Information om hur du använder säkerhets kopiering offline med MARS-agenten finns i [arbets flöde för offline-säkerhetskopiering i Azure Backup](backup-azure-backup-import-export.md). Offline-säkerhetskopiering stöds inte för säkerhets kopiering av system tillstånd som gjorts med hjälp av Azure Backup agenten.
>

## <a name="overview"></a>Översikt

Med funktionen offline-seeding i Azure Backup och tjänsten Azure import/export är det enkelt att ladda upp data offline till Azure med hjälp av diskar. Säkerhets kopierings processen offline omfattar följande steg:

> [!div class="checklist"]
>
> * Säkerhetskopierade data skrivs till en mellanlagringsplats i stället för att skickas över nätverket.
> * Data på mellanlagringsplatsen skrivs sedan till en eller flera SATA-diskar med hjälp av *AzureOfflineBackupDiskPrep* -verktyget.
> * Ett Azure import-jobb skapas automatiskt av verktyget.
> * SATA-enheterna skickas sedan till närmaste Azure-datacenter.
> * När överföringen av säkerhets kopierings data till Azure är färdig Azure Backup kopierar säkerhetskopierade data till säkerhets kopierings valvet och de stegvisa säkerhets kopiorna schemaläggs.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Offline-säkerhetskopiering stöds för alla distributions modeller av Azure Backup som säkerhetskopierar data från lokala nätverk till Microsoft-molnet. Dessa modeller är:

> [!div class="checklist"]
>
> * Säkerhets kopiering av filer och mappar med MARS-agenten eller Azure Backup-agenten.
> * Säkerhets kopiering av alla arbets belastningar och filer med DPM.
> * Säkerhets kopiering av alla arbets belastningar och filer med MABS.

>[!NOTE]
>Azure CSP-prenumerationer stöds inte för användning med offline-seeding för DPM 2019 RTM och tidigare versioner, och MABS v3 RTM och tidigare versioner. Online-säkerhetskopieringar över nätverket stöds fortfarande.

## <a name="prerequisites"></a>Krav

Kontrol lera att följande krav är uppfyllda innan du startar arbets flödet offline-säkerhetskopiering:

* Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) har skapats. Följ stegen i [skapa ett Recovery Services-valv](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) för att skapa en.
* En Azure Backup-Agent eller MABS eller DPM har installerats på antingen Windows Server eller en Windows-klient, efter vad som är tillämpligt och datorn har registrerats med Recovery Services-valvet. Se till att endast den [senaste versionen av Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) används.
* [Hämta filen Azure Publish Settings](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) på den dator som du planerar att säkerhetskopiera dina data från. Den prenumeration som du hämtar publicerings inställnings filen från kan skilja sig från den prenumeration som innehåller Recovery Services valvet. Om din prenumeration är på suveräna Azure-moln kan du använda följande länkar för att ladda ned filen med Azures publicerings inställningar.

    | Region för suveräna moln | Fil länk för Azure Publish-inställningar |
    | --- | --- |
    | USA | [Länk](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kina | [Länk](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Ett Azure Storage-konto med distributions modellen Resource Manager har skapats i den prenumeration som du laddade ned filen med publicerings inställningar för. I lagrings kontot skapar du en ny BLOB-behållare som ska användas som mål.

  ![Skapa ett lagrings konto med Resource Manager-utveckling](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* En mellanlagringsplats, som kan vara en nätverks resurs eller ytterligare en enhet på datorn, intern eller extern, med tillräckligt disk utrymme för att lagra din ursprungliga kopia skapas. Om du till exempel vill säkerhetskopiera en fil server med 500 GB kontrollerar du att mellanlagringsområdet är minst 500 GB. (En mindre mängd används på grund av komprimering.)
* För diskar som har skickats till Azure, se till att endast 2,5-tums SSD-eller 2,5-tums eller 3,5-tums inbyggda SATA II/III-hårddiskar används. Du kan använda hård diskar upp till 10 TB. Läs [dokumentationen för Azure import/export-tjänsten](../storage/common/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen enheter som stöds av tjänsten.
* SATA-enheterna måste vara anslutna till en dator (kallas *kopierings dator*) från den plats där kopian av säkerhets kopierings data från mellanlagringsplatsen till SATA-enheterna görs. Se till att BitLocker är aktiverat på kopierings datorn.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Förbered servern för säkerhets kopiering av offline-process

>[!NOTE]
> Om du inte hittar de verktyg som visas, till exempel *AzureOfflineBackupCertGen.exe*, i din installation av mars-agenten skriver du till AskAzureBackupTeam@microsoft.com för att få åtkomst till dem.

* Öppna en kommando tolk med förhöjd behörighet på servern och kör följande kommando:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Verktyget skapar en Azure offline-säkerhetskopiering Active Directory program om det inte finns något.

    Om det redan finns ett program, ber den här körbara filen dig att ladda upp certifikatet manuellt till programmet i klient organisationen. Följ stegen i [det här avsnittet](#manually-upload-an-offline-backup-certificate) för att ladda upp certifikatet manuellt till programmet.

* Verktyget *AzureOfflineBackupCertGen.exe* genererar en *OfflineApplicationParams.xml* -fil. Kopiera den här filen till servern med MABS eller DPM.
* Installera den [senaste mars-agenten](https://aka.ms/azurebackup_agent) på DPM-instansen eller Azure Backup servern.
* Registrera servern på Azure.
* Kör följande kommando:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* Föregående-kommando skapar filen `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>Ladda upp ett offline-certifikat för säkerhets kopiering manuellt

Följ dessa steg om du vill ladda upp offline backup-certifikatet manuellt till ett tidigare skapat Azure Active Directory program som är avsett för offline-säkerhetskopiering.

1. Logga in på Azure-portalen.
1. Gå till **Azure Active Directory**  >  **Appregistreringar**.
1. På fliken **ägda program** letar du reda på ett program med formatet visnings namn `AzureOfflineBackup _<Azure User Id` .

    ![Fliken hitta program på ägda program](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. Välj programmet. Under **Hantera** i det vänstra fönstret, går du till **certifikat & hemligheter**.
1. Sök efter befintliga certifikat eller offentliga nycklar. Om det inte finns någon, kan du ta bort programmet genom att välja knappen **ta bort** på programmets **översikts** sida. Sedan kan du göra om stegen för att [förbereda servern för säkerhets kopierings processen offline](#prepare-the-server-for-the-offline-backup-process) och hoppa över följande steg. Annars fortsätter du att följa de här stegen från DPM-instansen eller Azure Backup server där du vill konfigurera säkerhets kopiering offline.
1. Från **Start** – **Kör**skriver du *Certlm. msc*. I fönstret **certifikat – lokal dator** väljer du fliken **certifikat – lokal dator**  >  **Personal** . Sök efter certifikatet med namnet `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
1. Välj certifikatet, högerklicka på **alla uppgifter**och välj sedan **Exportera**, utan privat nyckel, i. cer-format.
1. Gå till Azure offline Backup-programmet i Azure Portal.
1. Välj **Hantera**  >  **certifikat & hemligheter**  >  **Ladda upp certifikat**. Överför certifikatet som exporterades i föregående steg.

    ![Ladda upp certifikatet](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. På-servern öppnar du registret genom att skriva in **regedit** i körnings fönstret.
1. Gå till register posten *dator \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider*.
1. Högerklicka på **CloudBackupProvider**och Lägg till ett nytt sträng värde med namnet `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Gör något av följande för att hitta användar-ID: t för Azure:
    >
    >* Kör kommandot från Azure-anslutna PowerShell `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` .
    >* Gå till register Sök vägen `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` .

1. Högerklicka på den sträng som du lade till i föregående steg och välj **ändra**. I värdet anger du tumavtrycket för det certifikat som du exporterade i steg 7. Välj sedan **OK**.
1. Om du vill hämta värdet för tumavtrycket dubbelklickar du på certifikatet. Välj fliken **information** och rulla nedåt tills du ser fältet tumavtryck. Välj **tumavtryck**och kopiera värdet.

    ![Kopiera värde från fältet tumavtryck](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. Fortsätt till [arbets flödes](#workflow) avsnittet om du vill fortsätta med säkerhets kopierings processen offline.

## <a name="workflow"></a>Arbetsflöde

Informationen i det här avsnittet hjälper dig att slutföra arbets flödet offline-säkerhetskopiering så att dina data kan skickas till ett Azure-datacenter och laddas upp till Azure Storage. Om du har frågor om import tjänsten eller någon del av processen går du till [översikts dokumentationen för import tjänsten](../storage/common/storage-import-export-service.md) som refereras tidigare.

### <a name="initiate-offline-backup"></a>Starta säkerhets kopiering offline

1. När du schemalägger en säkerhets kopia ser du följande sida i Windows Server, en Windows-klient eller DPM.

    ![Sidan importera](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    Här är motsvarande sida i DPM. <br/>

    ![Sidan importera DPM och Azure Backup Server](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    De rutor som du fyller i är:

   * **Mellanlagringsplats**: den tillfälliga lagrings plats som den första säkerhets kopian skrivs till. Mellanlagrings platsen kan vara en nätverks resurs eller en lokal dator. Om kopierings datorn och käll datorn skiljer sig anger du sökvägen till den fullständiga nätverks platsen för mellanlagringsplatsen.
   * **Publicerings inställningar för Azure**: den lokala sökvägen till filen med publicerings inställningar.
   * **Azure-importens jobbnamn**: det unika namn som Azures import-/export tjänst och Azure Backup spåra överföringen av data som skickas på diskar till Azure.
   * **ID för Azure-prenumeration**: ID för Azure-prenumerationen för prenumerationen från varifrån du laddade ned filen för Azure Publish-inställningar.
   * **Azure Storage konto**: namnet på det lagrings konto i Azure-prenumerationen som är associerat med Azure Publish Settings-filen.
   * **Azure Storage behållare**: namnet på mål lagrings-bloben i Azure Storage-kontot där säkerhetskopierade data importeras.

   Spara **mellanlagringsplatsen** och namn informationen för **Azure import-jobbet** som du har angett. Det krävs att du förbereder diskarna.

1. Slutför arbets flödet. Om du vill starta säkerhets kopiering offline väljer du **Säkerhetskopiera nu** på Azure Backup Agent hanterings konsol. Den första säkerhets kopian skrivs till mellanlagringsområdet som en del av det här steget.

    ![Säkerhetskopiera nu](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    Slutför motsvarande arbets flöde i DPM eller Azure Backup Server genom att högerklicka på **skydds gruppen**. Välj alternativet för att **skapa återställnings punkt** . Välj sedan alternativet **onlineskydd** .

    ![DPM och MABS Säkerhetskopiera nu](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    När åtgärden har slutförts är mellanlagringsplatsen redo att användas för disk förberedelse.

    ![Säkerhets kopierings förlopp](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbered SATA-enheter och leverera till Azure

Verktyget *AzureOfflineBackupDiskPrep* används för att förbereda SATA-enheter som skickas till närmaste Azure-datacenter. Det här verktyget är tillgängligt i installations katalogen för Recovery Services agenten på följande sökväg:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Gå till katalogen och kopiera *AzureOfflineBackupDiskPrep* -katalogen till en kopierings dator där de SATA-enheter som ska förberedas är anslutna. Se till att:

   * Kopierings datorn kan komma åt mellanlagringsplatsen för arbets flödet offline-dirigering med hjälp av samma nätverks Sök väg som tillhandahölls i arbets flödet i avsnittet "Starta säkerhets kopiering offline".
   * BitLocker är aktiverat på kopierings datorn.
   * Kopierings datorn har åtkomst till Azure Portal. Vid behov kan kopierings datorn vara samma som käll datorn.

     > [!IMPORTANT]
     > Om käll datorn är en virtuell dator är det obligatoriskt att använda en annan fysisk server eller klient dator som kopierings dator.

1. Öppna en kommando tolk med förhöjd behörighet på kopierings datorn med katalogen *AzureOfflineBackupDiskPrep* Utility som den aktuella katalogen. Kör följande kommando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parameter | Beskrivning |
    | --- | --- |
    | s: &lt; *sökväg för mellanlagringsplats*&gt; |Den här obligatoriska indatatypen används för att ange sökvägen till den mellanlagringsplats som du angav i arbets flödet i avsnittet "påbörja säkerhets kopiering offline". |
    | p: &lt; *sökväg till PublishSettingsFile*&gt; |Den här alternativa indatan används för att ange sökvägen till filen för Azure Publish-inställningar som du angav i arbets flödet i avsnittet "påbörja säkerhets kopiering offline". |

    > [!NOTE]
    > &lt;Värdet för sökvägen till AzurePublishSettingFile &gt; är obligatoriskt när kopierings datorn och käll datorn skiljer sig åt.
    >
    >

    När du kör kommandot begär verktyget valet av Azure import-jobb som motsvarar de enheter som måste förberedas. Om bara ett enda import jobb är associerat med den angivna mellanlagringsplatsen visas en sida som den här.

    ![Inmatade Azure disk Preparation-verktyg](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Ange enhets beteckningen utan avslutande kolon för den monterade disk som du vill förbereda för överföring till Azure. När du uppmanas till det anger du en bekräftelse av enhetens formatering.

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopierade data. Du kan behöva koppla ytterligare diskar när du uppmanas att göra det om disken inte har tillräckligt med utrymme för säkerhets kopierings data. <br/>

    När verktyget har slutförts förbereds en eller flera diskar som du har angett för leverans till Azure. Ett import jobb med det namn som du angav under arbets flödet i avsnittet "initiera offline-säkerhetskopiering" skapas också i Azure. Slutligen visar verktyget leverans adressen till det Azure-datacenter där diskarna måste skickas.

    ![Förberedelse av Azure-disk avslutad](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. I slutet av kommando körningen visas även alternativet att uppdatera leverans information.

    ![Uppdatera leverans information, alternativ](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. Du kan ange information direkt. Verktyget vägleder dig genom processen som omfattar en serie indata. Om du inte har information som spårnings nummer eller annan information som är relaterad till leverans kan du avsluta sessionen. De steg som krävs för att uppdatera leverans informationen finns i den här artikeln.

1. Leverera diskarna till den adress som verktyget tillhandahåller. Behåll spårnings numret för framtida bruk.

   > [!IMPORTANT]
   > Det går inte att ha samma spårnings nummer på två Azure import-jobb. Se till att enheter som förbereds av verktyget under ett enda Azure import-jobb levereras tillsammans i ett enda paket och att det finns ett enda unikt spårnings nummer för paketet. Kombinera inte enheter som har bearbetats som en del av olika Azure import-jobb i ett enda paket.

1. När du har information om spårnings numret går du till käll datorn, som väntar på slut för ande av import jobb. Kör följande kommando i en upphöjd kommando tolk med *AzureOfflineBackupDiskPrep* Utility-katalogen som den aktuella katalogen.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Du kan också köra följande kommando från en annan dator, t. ex. kopierings datorn, med *AzureOfflineBackupDiskPrep* -katalogen som den aktuella katalogen.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parameter | Beskrivning |
    | --- | --- |
    | ä | Detta obligatoriska indata används för att uppdatera skeppnings information för ett Azure-import jobb. |
    | s: &lt; *sökväg för mellanlagringsplats*&gt; | Detta obligatoriska indata används när kommandot inte körs på käll datorn. Den används för att ange sökvägen till den mellanlagringsplats som du angav i arbets flödet i avsnittet "påbörja säkerhets kopiering offline". |
    | p: &lt; *sökväg till PublishSettingsFile*&gt; | Detta obligatoriska indata används när kommandot inte körs på käll datorn. Den används för att ange sökvägen till filen för Azure Publish-inställningar som du angav i arbets flödet i avsnittet "påbörja säkerhets kopiering offline". |

    Verktyget identifierar automatiskt det import jobb som käll datorn väntar på eller de import jobb som är kopplade till mellanlagringsplatsen när kommandot körs på en annan dator. Det ger dig möjlighet att uppdatera skeppnings information via en serie med indata.

    ![Ange leverans information](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. När du har angett alla indata kan du läsa informationen noga och bekräfta leverans informationen du angav genom att skriva **Ja**.

    ![Granska skeppnings information](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. När leverans informationen har uppdaterats tillhandahåller verktyget en lokal plats där leverans informationen du angav lagras.

    ![Lagra leverans information](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > Se till att enheterna når Azure-datacentret inom två veckor från att tillhandahålla skeppnings information med hjälp av *AzureOfflineBackupDiskPrep* -verktyget. Om du inte gör det kan det leda till att enheterna inte bearbetas.

När du har slutfört de föregående stegen är Azure-datacenter redo att ta emot enheterna och bearbeta dem ytterligare för att överföra säkerhets kopierings data från enheterna till det klassiska Azure Storage-konto som du skapade.

### <a name="time-to-process-the-drives"></a>Tid för att bearbeta enheterna

Hur lång tid det tar att bearbeta ett Azure-importerat jobb varierar. Process tiden beror på faktorer som leverans tid, Jobbtyp, typ och storlek för de data som kopieras och storleken på de diskar som tillhandahålls. Tjänsten Azure import/export har inget service avtal. När diskarna har tagits emot strävar tjänsten efter att slutföra säkerhets kopieringen av säkerhets kopian till ditt Azure Storage-konto på 7 till 10 dagar. I nästa avsnitt beskrivs hur du kan övervaka statusen för Azure import-jobbet.

### <a name="monitor-azure-import-job-status"></a>Övervaka status för Azure import-jobb

Medan dina enheter överförs eller på Azure-datacenter för att kopieras till lagrings kontot, visar Azure Backup agenten eller DPM eller MABS-konsolen på käll datorn följande jobb status för schemalagda säkerhets kopieringar:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Kontrol lera status för import jobb:

1. Öppna en kommando tolk med förhöjd behörighet på käll datorn och kör följande kommando:

     `AzureOfflineBackupDiskPrep.exe u:`

1. I utdata visas import jobbets aktuella status.

    ![Kontrol lera status för import jobb](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Mer information om de olika tillstånden för Azures import jobb finns i [Visa status för Azure import/export-jobb](../storage/common/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Slutför arbets flödet

När import jobbet är klart är de första säkerhets kopierings data tillgängliga i ditt lagrings konto. Vid nästa schemalagda säkerhets kopiering Azure Backup kopierar innehållet i data från lagrings kontot till Recovery Services-valvet.

   ![Kopiera data till Recovery Services Vault](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

Vid nästa schemalagda säkerhets kopiering Azure Backup utföra stegvis säkerhets kopiering över den första säkerhets kopian.

## <a name="next-steps"></a>Nästa steg

* Om du har frågor om Azures arbets flöde för import/export-tjänsten kan du läsa mer i [använda tjänsten Microsoft Azure import/export för att överföra data till Blob Storage](../storage/common/storage-import-export-service.md).
