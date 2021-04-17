---
title: Offlinesäkerhetskopiering med hjälp av Azure Data Box
description: Lär dig hur du kan använda Azure Data Box för att seeda stora inledande säkerhetskopierade data offline från MARS-agenten till ett Recovery Services-valv.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 78adc479ce5733e208d2334d30d7b88e4edf8d6b
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576099"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup offlinesäkerhetskopiering med hjälp av Azure Data Box

Du kan använda [Azure Data Box för att](../databox/data-box-overview.md) seeda dina stora första MARS Microsoft Azure säkerhetskopior (Recovery Services) offline (utan att använda nätverk) till ett Recovery Services-valv. Den här processen sparar tid och nätverksbandbredd som annars skulle förbrukas och flytta stora mängder säkerhetskopierade data online över ett nätverk med lång svarstid. Den här förbättringen är för närvarande i förhandsversion. Offlinesäkerhetskopiering Azure Data Box två distinkta fördelar jämfört med [säkerhetskopiering offline baserat på Azure Import/Export-tjänsten:](./backup-azure-backup-import-export.md)

- Du behöver inte köpa egna Azure-kompatibla diskar och anslutningsappar. Azure Data Box de diskar som är associerade med den valda [Data Box-enhet SKU:n](https://azure.microsoft.com/services/databox/data/).
- Azure Backup (MARS-agenten) kan skriva säkerhetskopierade data direkt till de SKU:er som Azure Data Box. Den här funktionen eliminerar behovet av att etablera en mellanlagringsplats för dina första säkerhetskopieringsdata. Du behöver inte heller verktyg för att formatera och kopiera dessa data till diskarna.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box med MARS-agenten

Den här artikeln förklarar hur du kan använda Azure Data Box för att seeda stora inledande säkerhetskopierade data offline från MARS-agenten till ett Recovery Services-valv.

## <a name="supported-platforms"></a>Plattformar som stöds

Processen för att seeda data från MARS-agenten med hjälp Azure Data Box stöds på följande Windows-SKU:er.

| **Operativsystem**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Arbetsstation**                        |                                                              |
| Windows 10 64-bitars                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64-bitars                    | Enterprise, Pro                                             |
| Windows 8 64-bitars                      | Enterprise, Pro                                             |
| Windows 7 64-bitars                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64-bitars            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64-bitars            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64-bitars         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64-bitars            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64-bitars    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64-bitars | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64-bitars    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64-bitars     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64-bitars        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Storlek och stöd för säkerhetskopiering av data Data Box-enhet SKU:er

| Storlek på säkerhetskopierade data (efter komprimering av MARS)* per server | SKU Azure Data Box stöds                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Azure Data Box disk](../databox/data-box-disk-overview.md) |
| >7,2 TB och <=80 TB**                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

*Typiska komprimeringsfrekvenser varierar mellan 10 % och 20 %. <br>
**Om du förväntar dig att ha mer än 80 TB inledande säkerhetskopierade data för en enskild MARS-server kontaktar du [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Initiala säkerhetskopierade data från en enskild server måste finnas i en enda Azure Data Box-instans eller Azure Data Box-disk och kan inte delas mellan flera enheter av samma eller olika SKU:er. Men en Azure Data Box enhet kan innehålla inledande säkerhetskopior från flera servrar.

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-subscription-and-required-permissions"></a>Azure-prenumeration och nödvändiga behörigheter

- Processen kräver en Azure-prenumeration.
- Processen kräver att användaren som har angetts för att utföra säkerhetskopieringspolicyn offline är ägare till Azure-prenumerationen.
- Jobbet Data Box-enhet Recovery Services-valvet (som data behöver seedas till) måste finnas i samma prenumerationer.
- Vi rekommenderar att mållagringskontot som är associerat med Azure Data Box jobbet och Recovery Services-valvet finns i samma region. Detta är dock inte nödvändigt.

### <a name="get-azure-powershell-370"></a>Hämta Azure PowerShell 3.7.0

*Detta är den viktigaste förutsättningen för processen*. Innan du installerar Azure PowerShell, version 3.7.0, utför du följande kontroller.

#### <a name="step-1-check-the-powershell-version"></a>Steg 1: Kontrollera PowerShell-versionen

1. Öppna Windows PowerShell och kör följande kommando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Om utdata visar en version som är högre än 3.7.0 gör du "Steg 2". Annars går du vidare till "Steg 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Steg 2: Avinstallera PowerShell-versionen

Avinstallera den aktuella versionen av PowerShell.

1. Ta bort de beroende modulerna genom att köra följande kommando i PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Kör följande kommando för att säkerställa att alla beroende moduler tas bort:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Steg 3: Installera PowerShell version 3.7.0

När du har kontrollerat att det inte finns några AzureRM-moduler installerar du version 3.7.0 med någon av följande metoder:

- Använd den här länken från [GitHub.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

Eller så kan du:

- Kör följande kommando i PowerShell-fönstret:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell också ha installerats med hjälp av en msi-fil. Om du vill ta bort den avinstallerar du den **med hjälp av alternativet** Avinstallera program i Kontrollpanelen.

### <a name="order-and-receive-the-data-box-device"></a>Beställa och ta emot Data Box-enhet enhet

Processen för offlinesäkerhetskopiering med MARS Azure Data Box kräver att Data Box-enhet enheter är i tillståndet Levererad innan du utlöser offlinesäkerhetskopiering med hjälp av MARS-agenten. Information om hur du beställer den lämpligaste SKU:n för dina behov finns i Storlek på [säkerhetskopieringsdata och Data Box-enhet SKU:er.](#backup-data-size-and-supported-data-box-skus) Följ stegen i [Självstudie: Beställa en Azure Data Box disk för](../databox/data-box-disk-deploy-ordered.md) att beställa och ta emot Data Box-enhet enheter.

> [!IMPORTANT]
> Välj inte *BlobStorage* som **Konto.** MARS-agenten kräver ett konto som stöder sidblobar, som inte stöds när *BlobStorage* har valts. Välj **Storage V2 (generell användning v2)** som Typ av konto **när** du skapar mållagringskontot för Azure Data Box jobb.

![Välj typ av konto i instansinformation](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Installera och konfigurera MARS-agenten

1. Se till att du avinstallerar alla tidigare installationer av MARS-agenten.
1. Hämta den senaste MARS-agenten från [den här webbplatsen.](https://aka.ms/azurebackup_agent)
1. Kör *MARSAgentInstaller.exe* och gör *bara stegen* för att installera och registrera [agenten](./install-mars-agent.md#install-and-register-the-agent) i Recovery Services-valvet där du vill att dina säkerhetskopior ska lagras.

   > [!NOTE]
   > Recovery Services-valvet måste finnas i samma prenumeration som Azure Data Box jobbet.

   När agenten har registrerats i Recovery Services-valvet följer du stegen i nästa avsnitt.

## <a name="set-up-azure-data-box-devices"></a>Konfigurera Azure Data Box enheter

Beroende på vilken Azure Data Box SKU som du har beställt följer du stegen i de avsnitt som följer. Stegen visar hur du ställer in och förbereder Data Box-enhet för MARS-agenten för att identifiera och överföra de första säkerhetskopierade data.

### <a name="set-up-azure-data-box-disks"></a>Konfigurera Azure Data Box diskar

Om du har beställt en Azure Data Box diskar (upp till 8 TB vardera) följer du stegen här för att packa [upp,](../databox/data-box-disk-deploy-set-up.md)ansluta och låsa upp Data Box-enhet disk .

>[!NOTE]
>Det är möjligt att servern med MARS-agenten inte har någon USB-port. I så fall kan du ansluta din Azure Data Box till en annan server eller klient och exponera enhetens rot som en nätverksresurs.

### <a name="set-up-azure-data-box"></a>Konfigurera Azure Data Box

Om du har Azure Data Box en instans (upp till 100 TB) följer du stegen här för att [konfigurera din Data Box-enhet instans](../databox/data-box-deploy-set-up.md).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Montera din Azure Data Box instans som ett lokalt system

MARS-agenten fungerar i kontexten Lokalt system, så den kräver att samma behörighetsnivå anges för monteringssökvägen där Azure Data Box-instansen är ansluten.

För att säkerställa att du kan Data Box-enhet en enhet som ett lokalt system med hjälp av NFS-protokollet:

1. Aktivera klienten för NFS-funktionen på Den Windows-server där MARS-agenten är installerad. Ange den alternativa *källans WIM:D:\Sources\Install.wim:4*.
1. Ladda ned PsExec från [sysinternals-sidan](/sysinternals/downloads/psexec) till servern med MARS-agenten installerad.
1. Öppna en upphöjd kommandotolk och kör följande kommando med den katalog som *innehållerPSExec.exe* som den aktuella katalogen.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Kommandofönstret som öppnas på grund av föregående kommando finns i kontexten Lokalt system. Använd det här kommandofönstret för att köra stegen för att montera Azure-sidblobresursen som en nätverksenhet på Windows-servern.
1. Följ stegen i Anslut [till Data Box-enhet](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) för att ansluta servern med MARS-agenten till Data Box-enhet via NFS. Kör följande kommando i kommandotolken Lokalt system för att montera Azure-sidans blobbresurs.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   När resursen har monterats kontrollerar du om du har åtkomst till X: från servern. Om det går fortsätter du med nästa avsnitt i den här artikeln.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Överföra inledande säkerhetskopierade data till Azure Data Box enheter

1. Öppna **Microsoft Azure Backup** på servern.
1. I fönstret **Åtgärder** väljer du **Schemalägg säkerhetskopiering.**

    ![Välj Schemalägg säkerhetskopiering](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Följ stegen i guiden **Schemalägg säkerhetskopiering.**

1. Lägg till objekt genom att välja **knappen Lägg till** objekt. Behåll den totala storleken på objekten inom de [storleksgränser som stöds av den SKU Azure Data Box](#backup-data-size-and-supported-data-box-skus) som du har beställt och tagit emot.

    ![Lägga till objekt i säkerhetskopiering](./media/offline-backup-azure-data-box/add-items.png)

1. Välj lämpligt säkerhetskopieringsschema och bevarandeprincip för **filer och mappar och** **systemtillstånd.** Systemtillståndet gäller endast för Windows-servrar och inte för Windows-klienter.
1. På sidan **Välj inledande säkerhetskopieringstyp (filer** och mappar) i guiden väljer du alternativet Överför **med Microsoft Azure Data Box-enhet diskar** och väljer **Nästa.**

    ![Välj typ av första säkerhetskopiering](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Logga in på Azure när du tillfrågas med hjälp av autentiseringsuppgifterna som har ägaråtkomst för Azure-prenumerationen. När du har gjort det bör du se en sida som liknar den här.

    ![Skapa resurser och tillämpa nödvändiga behörigheter](./media/offline-backup-azure-data-box/creating-resources.png)

   MARS-agenten hämtar sedan Data Box-enhet jobb i prenumerationen som har tillståndet Levererad.

    ![Hämta Data Box-enhet jobb för prenumerations-ID](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Välj rätt Data Box-enhet ordning som du har packat upp, anslutit och låst upp din Data Box-enhet disk. Välj **Nästa**.

    ![Välj Data Box-enhet beställningar](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Välj **Identifiera enhet** på Data Box-enhet för **enhetsidentifiering.** Den här åtgärden gör att MARS-agenten söker efter lokalt Azure Data Box diskar och identifierar dem.

    ![Data Box-enhet enhetsidentifiering](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Om du har anslutit Azure Data Box-instansen som en nätverksresurs (på grund av otillgängliga USB-portar eller eftersom du har beställt och monterat enheten på 100 TB Data Box-enhet), misslyckas identifieringen först. Du får möjlighet att ange nätverkssökvägen till den Data Box-enhet enheten.

    ![Ange nätverkssökvägen](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Ange nätverkssökvägen till rotkatalogen för den Azure Data Box disken. Den här katalogen måste innehålla en katalog med namnet *PageBlob*.
    >
    >![Rotkatalog för Azure Data Box disk](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Om diskens sökväg till exempel är och disk1 innehåller en katalog med namnet PageBlob är sökvägen som du anger på `\\mydomain\myserver\disk1\` MARS-agentens guidesida   `\\mydomain\myserver\disk1\` .
    >
    >Om du [ställer in en Azure Data Box 100 TB-enhet](#set-up-azure-data-box-devices)anger du som `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` nätverkssökväg till enheten.

1. Välj **Nästa** och välj **Slutför på** nästa sida för att spara säkerhetskopierings- och kvarhållningsprincipen med konfigurationen av offlinesäkerhetskopiering med hjälp av Azure Data Box.

   Följande sida bekräftar att principen har sparats.

    ![Principen har sparats](./media/offline-backup-azure-data-box/policy-saved.png)

1. Välj **Stäng** på föregående sida.

1. Välj **Back Up Now** (Back Up **Now) i** rutan Åtgärder i MARS-agentkonsolen. Välj **Brytning** på guidesidan.

    ![Guiden Vad gör du nu?](./media/offline-backup-azure-data-box/backup-now.png)

MARS-agenten börjar eftersom den data som du har valt Azure Data Box enheten. Den här processen kan ta från flera timmar till några dagar. Hur lång tid det tar beror på antalet filer och anslutningshastigheten mellan servern med MARS-agenten och Azure Data Box disk.

När säkerhetskopieringen av data är klar visas en sida på MARS-agenten som liknar den här.

![Säkerhetskopieringsförloppet visas](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Steg efter säkerhetskopiering

I det här avsnittet beskrivs de steg som ska vidtas när säkerhetskopieringen av data till Azure Data Box Disk lyckas.

- Följ stegen i den här artikeln för [att skicka Azure Data Box disk till Azure](../databox/data-box-disk-deploy-picked-up.md). Om du använde en Azure Data Box enhet på 100 TB följer du dessa steg för [att Azure Data Box till Azure](../databox/data-box-deploy-picked-up.md).

- [Övervaka Data Box-enhet jobb](../databox/data-box-disk-deploy-upload-verify.md) i Azure Portal. När Azure Data Box är klart flyttar MARS-agenten automatiskt data från lagringskontot till Recovery Services-valvet vid tidpunkten för nästa schemalagda säkerhetskopiering. Det markerar sedan säkerhetskopieringsjobbet *som Slutfört* jobb om en återställningspunkt har skapats.

    >[!NOTE]
    >MARS-agenten utlöser säkerhetskopieringar vid de tidpunkter som är schemalagda när principen skapas. De här jobben flaggar "Väntar Azure Data Box att jobbet ska slutföras" tills jobbet har slutförts.

- När MARS-agenten har skapat en återställningspunkt som motsvarar den första säkerhetskopieringen kan du ta bort lagringskontot eller specifikt innehåll som är associerat Azure Data Box jobbet.

## <a name="troubleshooting"></a>Felsökning

Agenten Microsoft Azure Recovery Services (MARS) skapar ett Azure Active Directory (Azure AD)-program åt dig i din klientorganisation. Det här programmet kräver ett certifikat för autentisering som skapas och laddas upp när du konfigurerar en offline-seeding-princip. Vi använder Azure PowerShell för att skapa och ladda upp certifikatet till Azure AD-programmet.

### <a name="problem"></a>Problem

När du konfigurerar offlinesäkerhetskopiering kan du få problem på grund av en bugg i Azure PowerShell-cmdleten. Du kanske inte kan lägga till flera certifikat i samma Azure AD-program som skapats av MAB-agenten. Det här problemet påverkar dig om du har konfigurerat en offline-seeding-princip för samma eller en annan server.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Kontrollera om problemet orsakas av den här specifika rotorsaken

Gör något av följande för att se om problemet är samma som det som beskrevs tidigare.

#### <a name="step-1-of-verification"></a>Steg 1 av verifieringen

Kontrollera om följande felmeddelande visas i MAB-konsolen när du konfigurerade offlinesäkerhetskopiering.

![Det går inte att skapa en offlinesäkerhetskopieringsprincip för det aktuella Azure-kontot](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Steg 2 av verifieringen

1. Öppna **temp-mappen** i installationssökvägen. Standardsökvägen för temp-mappen *är C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Leta efter *CBUICurr-filen* och öppna filen.

1. I *filen CBUICurr* bläddrar du till den sista raden och kontrollerar om problemet är detsamma som i det här felmeddelandet: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Lösning

Lös problemet genom att utföra följande steg och försök igen med principkonfigurationen.

#### <a name="step-1-of-workaround"></a>Steg 1 av lösningen

Logga in på PowerShell som visas i MAB-användargränssnittet med ett annat konto med administratörsåtkomst för prenumerationen där Data Box-enhet skapas.

#### <a name="step-2-of-workaround"></a>Steg 2 i lösningen

Om ingen annan server har offline-seeding konfigurerat och ingen annan server är beroende av `AzureOfflineBackup_<Azure User Id>` programmet tar du bort det här programmet. Välj **Azure Portal**  >  **Azure Active Directory**  >  **Appregistreringar**.

>[!NOTE]
> Kontrollera om programmet inte `AzureOfflineBackup_<Azure User Id>` har någon annan offline-seeding konfigurerad och även om ingen annan server är beroende av det här programmet. Gå till  >  **Inställningsnycklar** under **avsnittet Offentliga** nycklar. Inga andra offentliga nycklar får läggas till. Se följande skärmbild som referens.
>
>![Offentliga nycklar](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Steg 3

Utför följande åtgärder från den server som du försöker konfigurera för säkerhetskopiering offline.

1. Gå till **fliken Hantera datorcertifikatprogram**  >  **Personligt** och leta efter certifikatet med namnet `CB_AzureADCertforOfflineSeeding_<Timestamp>` .

2. Välj certifikatet, högerklicka på Alla **aktiviteter och** välj **Exportera utan** en privat nyckel i CER-format.

3. Gå till offlinesäkerhetskopieringsprogrammet i Azure som nämns i steg 2. Välj **Inställningar Nycklar**  >  **Ladda** upp offentlig  >  **nyckel.** Ladda upp certifikatet som du exporterade i föregående steg.

    ![Ladda upp offentlig nyckel](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Öppna registret på servern genom att ange **regedit** i körningsfönstret.

5. Gå till *registerpostenComputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Högerklicka på **CloudBackupProvider** och lägg till ett nytt strängvärde med namnet `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Hämta Azure-användar-ID:t genom att utföra någon av följande åtgärder:
    >
    >- Kör kommandot från azure-anslutna `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` PowerShell.
    > - Gå till registersökvägen `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` med namnet *CurrentUserId*.

6. Högerklicka på strängen som lades till i föregående steg och välj **Ändra**. I värdet anger du tumavtrycket för certifikatet som du exporterade i steg 2. Välj **OK**.

7. Dubbelklicka på certifikatet för att hämta värdet för tumavtrycket. Välj fliken **Information** och rulla nedåt tills du ser tumavtrycksfältet. Välj **Tumavtryck** och kopiera värdet.

    ![Tumavtrycksfält för certifikat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Frågor

Om du har frågor eller förtydliganden om eventuella problem kan du kontakta [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
