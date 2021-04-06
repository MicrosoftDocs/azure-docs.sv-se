---
title: Dirigera offline-säkerhetskopiering med tjänsten Azure import/export
description: Lär dig hur du kan använda Azure Backup för att skicka data från nätverket med Azure import/export-tjänsten. I den här artikeln förklaras dirigeringen av de första säkerhets kopierings data med hjälp av Azure import/export-tjänsten.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704371"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Arbets flöde för offline-säkerhetskopiering i Azure Backup

Azure Backup har flera inbyggda effektivitets vinster som sparar kostnader för nätverk och lagring under de första fullständiga säkerhets kopieringarna av data till Azure. De första fullständiga säkerhets kopieringarna överför ofta stora mängder data och kräver mer nätverks bandbredd jämfört med efterföljande säkerhets kopieringar som bara överför delta/steg. Genom processen för dirigering av dirigering kan Azure Backup använda diskar för att ladda upp offline-säkerhetskopierade data till Azure.

Azure Backup offline-seeding-processen är nära integrerad med [Azure import/export-tjänsten](../import-export/storage-import-export-service.md). Du kan använda den här tjänsten för att överföra första säkerhets kopierings data till Azure med hjälp av diskar. Om du har terabyte (TBs) av inledande säkerhets kopierings data som behöver överföras via ett nätverk med hög latens och låg bandbredd, kan du använda arbets flödet offline-seeding för att leverera den första säkerhets kopian på en eller flera hård diskar till ett Azure-datacenter. Följande bild ger en översikt över stegen i arbets flödet.

  ![Översikt över arbets flödes processen offline-import](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Säkerhets kopierings processen offline omfattar följande steg:

1. I stället för att skicka säkerhetskopierade data över nätverket skriver du säkerhets kopierings data till en mellanlagringsplats.
1. Använd verktyget *AzureOfflineBackupDiskPrep* för att skriva data på mellanlagringsplatsen till en eller flera SATA-diskar.
1. Som en del av det förberedande arbetet skapar verktyget *AzureOfflineBackupDiskPrep* ett Azure-importerat jobb. Skicka SATA-enheterna till närmaste Azure-datacenter och referera till import jobbet för att ansluta aktiviteterna.
1. På Azure-datacentret kopieras data på diskarna till ett Azure Storage-konto.
1. Azure Backup kopierar säkerhetskopierade data från lagrings kontot till Recovery Services-valvet och stegvisa säkerhets kopieringar schemaläggs.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Följande Azure Backup funktioner eller arbets belastningar stöder användning av offline-säkerhetskopiering för:

> [!div class="checklist"]
>
> * Säkerhets kopiering av filer och mappar med Microsoft Azure Recovery Services MARS-agenten (MARS), kallas även för Azure Backup agenten.
> * Säkerhets kopiering av alla arbets belastningar och filer med System Center Data Protection Manager (DPM).
> * Säkerhets kopiering av alla arbets belastningar och filer med Microsoft Azure Backup Server.

   > [!NOTE]
   > Offline-säkerhetskopiering stöds inte för säkerhets kopiering av system tillstånd som gjorts med hjälp av Azure Backup agenten.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Förutsättningar

  > [!NOTE]
  > Följande nödvändiga komponenter och arbets flöde gäller endast offline-säkerhetskopiering av filer och mappar med hjälp av den [senaste Azure Recovery Services-agenten](https://aka.ms/azurebackup_agent). Om du vill utföra säkerhets kopiering offline för arbets belastningar med System Center DPM eller Azure Backup Server, se [arbets flöde för offline-säkerhetskopiering för DPM och Azure Backup Server](backup-azure-backup-server-import-export.md).

Innan du startar arbets flödet offline måste du uppfylla följande krav:

* Skapa ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md). Följ stegen i [skapa ett Recovery Services valv](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)för att skapa ett valv.
* Kontrol lera att endast den [senaste versionen av Azure Backup Agent](https://aka.ms/azurebackup_agent) är installerad på Windows Server eller Windows-klienten, efter vad som är tillämpligt, och att datorn är registrerad i Recovery Services-valvet.
* Azure PowerShell 3.7.0 krävs på den dator som kör Azure Backup-agenten. Hämta och [Installera 3.7.0-versionen av Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Kontrol lera att Microsoft Edge eller Internet Explorer 11 är installerat på datorn som kör Azure Backup-agenten och att Java Script är aktiverat.
* Skapa ett Azure Storage-konto i samma prenumeration som Recovery Services-valvet.
* Kontrol lera att du har de [behörigheter som krävs](../active-directory/develop/howto-create-service-principal-portal.md) för att skapa Azure Active Directory-programmet. Arbets flödet offline-säkerhetskopiering skapar ett Azure Active Directory-program i prenumerationen som är kopplad till Azure Storage-kontot. Målet med programmet är att tillhandahålla Azure Backup med säker och begränsad åtkomst till Azure import/export-tjänsten, vilket krävs för arbets flödet offline-säkerhetskopiering.
* Registrera *Microsoft. ImportExport* -resurs leverantören med den prenumeration som innehåller Azure Storage-kontot. Så här registrerar du resurs leverantören:
    1. På huvud menyn väljer du **prenumerationer**.
    1. Om du prenumererar på flera prenumerationer väljer du den prenumeration som du planerar att använda för säkerhets kopiering offline. Om du bara använder en prenumeration visas din prenumeration.
    1. På menyn prenumeration väljer du **resurs leverantörer** för att visa listan över leverantörer.
    1. Rulla ned till *Microsoft. ImportExport* i listan över providrar. Om **statusen** är **NotRegistered** väljer du **Registrera**.

        ![Registrera resursprovidern](./media/backup-azure-backup-import-export/registerimportexport.png)

* En mellanlagringsplats, som kan vara en nätverks resurs eller ytterligare en enhet på datorn, intern eller extern, med tillräckligt disk utrymme för att lagra din ursprungliga kopia skapas. Om du till exempel vill säkerhetskopiera en fil server med 500 GB kontrollerar du att mellanlagringsområdet är minst 500 GB. (En mindre mängd används på grund av komprimering.)
* När du skickar diskar till Azure använder du bara 2,5-tums SSD-eller 2,5-tums eller 3,5-tums interna SATA II/III-hårddiskar. Du kan använda hård diskar upp till 10 TB. Läs [dokumentationen för Azure import/export-tjänsten](../import-export/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen enheter som stöds av tjänsten.
* SATA-enheterna måste vara anslutna till en dator (kallas *kopierings dator*) från den plats där kopian av säkerhets kopierings data från mellanlagringsplatsen till SATA-enheterna görs. Se till att BitLocker är aktiverat på kopierings datorn.

## <a name="workflow"></a>Arbetsflöde

I det här avsnittet beskrivs arbets flödet offline-säkerhetskopiering så att dina data kan skickas till ett Azure-datacenter och laddas upp till Azure Storage. Om du har frågor om import tjänsten eller någon del av processen kan du läsa mer i [översikts dokumentationen för Azure import/export-tjänsten](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Starta säkerhets kopiering offline

1. När du schemalägger en säkerhets kopia på Recovery Services agenten visas den här sidan.

    ![Sidan importera](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Välj alternativ **överföring med mina egna diskar**.

    > [!NOTE]
    > Använd Azure Data Box alternativet för att överföra första säkerhets kopierings data offline. Det här alternativet sparar den ansträngning som krävs för att skaffa dina egna Azure-kompatibla diskar. Den ger Microsoft-patentskyddade, säkra och tamperproof-Azure Data Box enheter som säkerhetskopierade data kan skrivas direkt till av Recovery Servicess agenten.

1. Välj **Nästa** och fyll i rutorna noggrant.

    ![Ange din disk information](./media/backup-azure-backup-import-export/your-disk-details.png)

   De rutor som du fyller i är:

    * **Mellanlagringsplats**: den tillfälliga lagrings plats som den första säkerhets kopian skrivs till. Mellanlagrings platsen kan vara en nätverks resurs eller en lokal dator. Om kopierings datorn och käll datorn skiljer sig anger du sökvägen till den fullständiga nätverks platsen för mellanlagringsplatsen.
    * **Azure Resource Manager lagrings konto**: namnet på resurs hanterarens typ lagrings konto (generell användning v1 eller generell användning v2) i valfri Azure-prenumeration.
    * **Azure Storage behållare**: namnet på den mål-Blob Storage-behållare i Azure Storage-kontot där säkerhets kopierings data importeras innan de kopieras till Recovery Services-valvet.
    * **ID för Azure-prenumeration**: ID för Azure-prenumerationen där Azure Storage-kontot skapas.
    * **Azure-importens jobbnamn**: det unika namn som Azures import-/export tjänst och Azure Backup spåra överföringen av data som skickas på diskar till Azure.
  
   När du har fyllt i rutorna väljer du **Nästa**. Spara **mellanlagringsplatsen** och namn informationen för **Azure import-jobbet** . Det krävs att du förbereder diskarna.

1. När du uppmanas till det loggar du in på din Azure-prenumeration. Du måste logga in så att Azure Backup kan skapa Azure Active Directory programmet. Ange de behörigheter som krävs för att få åtkomst till Azure import/export-tjänsten.

    ![Inloggnings sida för Azure-prenumeration](./media/backup-azure-backup-import-export/azure-login.png)

1. Slutför arbets flödet. I Azure Backup Agent-konsolen väljer du **Säkerhetskopiera nu**.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow.png)

1. På sidan **bekräftelse** i guiden väljer du **säkerhetskopiera**. Den första säkerhets kopian skrivs till mellanlagringsområdet som en del av installationen.

   ![Bekräfta att du är redo att säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    När åtgärden har slutförts är mellanlagringsplatsen redo att användas för disk förberedelse.

   ![Guide sidan Säkerhetskopiera nu](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbered SATA-enheter och leverera till Azure

*AzureOfflineBackupDiskPrep* -verktyget förbereder de SATA-enheter som skickas till närmaste Azure-datacenter. Det här verktyget finns i installations katalogen för Azure Backup-agenten på följande sökväg:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Gå till katalogen och kopiera *AzureOfflineBackupDiskPrep* -katalogen till en annan dator där SATA-enheterna är anslutna. På datorn med de anslutna SATA-enheterna kontrollerar du att:

   * Kopierings datorn kan komma åt mellanlagringsplatsen för arbets flödet offline-dirigering med hjälp av samma nätverks Sök väg som tillhandahölls i arbets flödet i avsnittet "Starta säkerhets kopiering offline".
   * BitLocker är aktiverat på kopierings datorn.
   * Azure PowerShell 3.7.0 har installerats.
   * De senaste kompatibla webbläsarna (Microsoft Edge eller Internet Explorer 11) är installerade och Java Script är aktiverat.
   * Kopierings datorn har åtkomst till Azure Portal. Vid behov kan kopierings datorn vara samma som käll datorn.

     > [!IMPORTANT]
     > Om käll datorn är en virtuell dator måste kopierings datorn vara en annan fysisk server eller klient dator från käll datorn.

1. Öppna en kommando tolk med förhöjd behörighet på kopierings datorn med katalogen *AzureOfflineBackupDiskPrep* Utility som den aktuella katalogen. Kör följande kommando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | Beskrivning |
    | --- | --- |
    | s: &lt; *sökväg för mellanlagringsplats*&gt; |Den här obligatoriska indatatypen används för att ange sökvägen till den mellanlagringsplats som du angav i arbets flödet i avsnittet "påbörja säkerhets kopiering offline". |
    | p: &lt; *sökväg till PublishSettingsFile*&gt; |Den här alternativa indatan används för att ange sökvägen till filen för Azure Publish-inställningar.  |

    När du kör kommandot begär verktyget valet av Azure import-jobb som motsvarar de enheter som måste förberedas. Om bara ett enda import jobb är associerat med den angivna mellanlagringsplatsen visas en sida som den här.

    ![Inmatade Azure disk Preparation-verktyg](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Ange enhets beteckningen utan avslutande kolon för den monterade disk som du vill förbereda för överföring till Azure.
1. Ange en bekräftelse för enhetens formatering vid uppmaning.
1. Du uppmanas att logga in på din Azure-prenumeration. Ange autentiseringsuppgifter.

    ![Inloggning för Azure-prenumeration](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopierade data. Du kan behöva koppla ytterligare diskar när du uppmanas att göra det om den angivna disken inte har tillräckligt med utrymme för säkerhets kopierings data. <br/>

    I slutet av lyckad körning av verktyget har kommando tolken tre delar av informationen:

   1. En eller flera diskar som du har angett är för beredda för leverans till Azure.
   1. Du får en bekräftelse på att ditt import jobb har skapats. Import jobbet använder det namn som du har angett.
   1. Verktyget visar leverans adressen för Azure-datacentret.

      ![Förberedelse av Azure-disk avslutad](./media/backup-azure-backup-import-export/console2.png)<br/>

1. I slutet av kommando körningen kan du uppdatera leverans informationen.

1. Leverera diskarna till den adress som verktyget tillhandahåller. Behåll spårnings numret för framtida bruk.

   > [!IMPORTANT]
   > Det går inte att ha samma spårnings nummer på två Azure import-jobb. Se till att enheter som förbereds av verktyget under ett enda Azure import-jobb levereras tillsammans i ett enda paket och att det finns ett enda unikt spårnings nummer för paketet. Kombinera inte enheter som har bearbetats som en del av separata Azure-importpriser i ett enda paket.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Uppdatera skeppnings information för Azure import-jobbet

I följande procedur uppdateras leverans informationen för Azure import-jobbet. Den här informationen innehåller information om:

* Namnet på den operatör som levererar diskarna till Azure.
* Returnera leverans information för diskarna.

1. Logga in på din Azure-prenumeration.
1. I huvud menyn väljer du **alla tjänster**. I dialog rutan **alla tjänster** anger du **Importera**. När du ser **import/export-jobb** väljer du det.

    ![Ange leverans information](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Menyn **import/export-jobb** öppnas och listan över alla import/export-jobb i den valda prenumerationen visas.

1. Om du har flera prenumerationer väljer du den prenumeration som används för att importera säkerhetskopierade data. Välj sedan det nyskapade import jobbet för att öppna dess information.

    ![Granska skeppnings information](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. På menyn **Inställningar** för import jobbet väljer du **Hantera leverans information**. Ange information om retur leveranser.

    ![Lagra leverans information](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. När du har spårnings numret från transport företaget väljer du banderollen på sidan Översikt för Azure import jobb och anger följande information.

   > [!IMPORTANT]
   > Se till att informationen som operatör och spårningsnummer uppdateras inom två veckor efter det att importjobb skapats i Azure. Om du inte verifierar informationen inom två veckor kan det leda till att jobbet tas bort och att enheterna inte bearbetas.

   ![Avisering om spårning av informations uppdatering](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Information om bärvåg och spårnings nummer](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tid för att bearbeta enheterna

Hur lång tid det tar att bearbeta ett Azure-importerat jobb varierar. Bearbetnings tiden baseras på faktorer som leverans tid, Jobbtyp, typ och storlek för de data som kopieras och storleken på de diskar som tillhandahålls. Tjänsten Azure import/export har inget service avtal. När diskarna har tagits emot strävar tjänsten efter att slutföra säkerhets kopieringen av säkerhets kopian till ditt Azure Storage-konto på 7 till 10 dagar.

### <a name="monitor-azure-import-job-status"></a>Övervaka status för Azure import-jobb

Du kan övervaka statusen för ditt import jobb från Azure Portal. Gå till sidan **import/export-jobb** och välj ditt jobb. Mer information om status för import jobben finns i [Vad är Azure import/export-tjänsten?](../import-export/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Slutför arbets flödet

När import jobbet har slutförts är de första säkerhets kopierings data tillgängliga i ditt lagrings konto. Vid nästa schemalagda säkerhets kopiering Azure Backup kopierar innehållet i data från lagrings kontot till Recovery Services-valvet.

   ![Kopiera data till Recovery Services Vault](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Vid nästa schemalagda säkerhets kopiering Azure Backup utföra en stegvis säkerhets kopiering.

### <a name="clean-up-resources"></a>Rensa resurser

När den första säkerhets kopieringen är färdig kan du på ett säkert sätt ta bort de data som importer ATS till Azure Storage-behållaren och säkerhets kopierings data på mellanlagringsplatsen.

## <a name="next-steps"></a>Nästa steg

* Om du har frågor om Azures arbets flöde för import/export-tjänsten kan du läsa mer i [använda tjänsten Microsoft Azure import/export för att överföra data till Blob Storage](../import-export/storage-import-export-service.md).
