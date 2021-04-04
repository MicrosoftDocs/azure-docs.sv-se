---
title: Säkerhets kopiering offline med hjälp av Azure Data Box
description: Lär dig hur du kan använda Azure Data Box för att dirigera stora första säkerhets kopierings data offline från MARS-agenten till ett Recovery Services-valv.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752591"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup offline-säkerhetskopiering med Azure Data Box

Du kan använda [Azure Data Box](../databox/data-box-overview.md) för att dirigera dina säkerhets kopieringar av stora inledande Microsoft Azure Recovery Services (mars) offline (utan att använda nätverket) till ett Recovery Services-valv. Den här processen sparar tid och nätverks bandbredd som annars skulle konsumeras för att flytta stora mängder säkerhetskopierade data online över ett nätverk med hög latens. Den här förbättringen är för närvarande en för hands version. Offline-säkerhetskopiering som baseras på Azure Data Box ger två distinkta fördelar jämfört med [offline-säkerhetskopiering baserat på Azure import/export-tjänsten](./backup-azure-backup-import-export.md):

- Du behöver inte skaffa dina egna Azure-kompatibla diskar och anslutningar. Azure Data Box levererar de diskar som är associerade med den valda [data Box-enhet SKU: n](https://azure.microsoft.com/services/databox/data/).
- Azure Backup (MARS-agent) kan skriva säkerhets kopierings data direkt till de SKU: er som stöds av Azure Data Box. Den här funktionen eliminerar behovet av att etablera en mellanlagringsplats för dina första säkerhets kopierings data. Du behöver inte heller något verktyg för att formatera och kopiera dessa data till diskarna.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box med MARS-agenten

Den här artikeln förklarar hur du kan använda Azure Data Box för att dirigera stora inledande säkerhets kopierings data offline från MARS-agenten till ett Recovery Services-valv.

## <a name="supported-platforms"></a>Plattformar som stöds

Processen för att dirigera data från MARS-agenten med hjälp av Azure Data Box stöds i följande Windows SKU: er.

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
| Windows Server 2008 SP2 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Säkerhetskopiera data storlek och Data Box-enhet SKU: er som stöds

| Säkerhets kopierings data storlek (efter MARS) * per server | Azure Data Box SKU som stöds                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Azure Data Box disk](../databox/data-box-disk-overview.md) |
| >7,2 TB och <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* Vanliga komprimerings hastigheter varierar mellan 10% och 20%. <br>
* * Om du förväntar dig att ha mer än 80 TB första säkerhets kopierings data för en enskild MARS-server kontaktar du [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Första säkerhets kopierings data från en enskild server måste finnas i en enskild Azure Data Box instans eller Azure Data Box disk och kan inte delas mellan flera enheter av samma eller olika SKU: er. Men en Azure Data Box enhet kan innehålla första säkerhets kopieringar från flera servrar.

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-subscription-and-required-permissions"></a>Azure-prenumeration och nödvändiga behörigheter

- Processen kräver en Azure-prenumeration.
- Processen kräver att den användare som är utsedd för att utföra säkerhets kopierings principen offline är en ägare av Azure-prenumerationen.
- Data Box-enhet-jobbet och Recovery Services-valvet (som data måste dirigeras till) måste finnas i samma prenumerationer.
- Vi rekommenderar att mål lagrings kontot som är kopplat till Azure Data Box-jobbet och Recovery Services-valvet finns i samma region. Detta är dock inte nödvändigt.

### <a name="get-azure-powershell-370"></a>Hämta Azure PowerShell 3.7.0

*Detta är den viktigaste förutsättningen för processen*. Innan du installerar Azure PowerShell version 3.7.0 utför du följande kontroller.

#### <a name="step-1-check-the-powershell-version"></a>Steg 1: kontrol lera PowerShell-versionen

1. Öppna Windows PowerShell och kör följande kommando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Om utdata visar en högre version än 3.7.0 gör du "steg 2". Annars går du vidare till "steg 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Steg 2: Avinstallera PowerShell-versionen

Avinstallera den aktuella versionen av PowerShell.

1. Ta bort de beroende modulerna genom att köra följande kommando i PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Kör följande kommando för att kontrol lera att alla beroende moduler har tagits bort:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Steg 3: installera PowerShell-versionen 3.7.0

När du har kontrollerat att det inte finns några AzureRM-moduler installerar du version 3.7.0 med någon av följande metoder:

- Använd [den här länken](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)från GitHub.

Eller så kan du:

- Kör följande kommando i PowerShell-fönstret:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell kan också ha installerats med hjälp av en MSI-fil. Ta bort den genom att avinstallera den med alternativet **Avinstallera program** på kontroll panelen.

### <a name="order-and-receive-the-data-box-device"></a>Beställ och ta emot den Data Box-enhet enheten

Offline-säkerhetskopiering med MARS och Azure Data Box kräver att Data Box-enhet enheterna är i ett levererat tillstånd innan du aktiverar säkerhets kopiering offline med hjälp av MARS-agenten. För att beställa den lämpligaste SKU: n för ditt krav, se [säkerhets kopierings data storlek och data Box-enhet SKU: er som stöds](#backup-data-size-and-supported-data-box-skus). Följ stegen i [Självstudier: Beställ en Azure Data Box disk](../databox/data-box-disk-deploy-ordered.md) för att beställa och ta emot data Box-enhet enheter.

> [!IMPORTANT]
> Välj inte *BlobStorage* för **konto typen**. MARS-agenten kräver ett konto som stöder Page blobbar, vilket inte stöds när *BlobStorage* har valts. Välj **Storage v2 (generell användning v2)** som **konto typ** när du skapar mål lagrings kontot för ditt Azure Data Box-jobb.

![Välj konto typ i instans information](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Installera och konfigurera MARS-agenten

1. Se till att avinstallera alla tidigare installationer av MARS-agenten.
1. Ladda ned den senaste MARS-agenten från [den här webbplatsen](https://aka.ms/azurebackup_agent).
1. Kör *MARSAgentInstaller.exe* och utför *bara* stegen för att [Installera och registrera agenten](./install-mars-agent.md#install-and-register-the-agent) på det Recovery Services-valv där du vill att dina säkerhets kopior ska lagras.

   > [!NOTE]
   > Recovery Services-valvet måste finnas i samma prenumeration som Azure Data Boxs jobbet.

   När agenten har registrerats i Recovery Services-valvet följer du stegen i nästa avsnitt.

## <a name="set-up-azure-data-box-devices"></a>Konfigurera Azure Data Box enheter

Beroende på Azure Data Box SKU: n som du beställde, utför du stegen i lämpliga avsnitt som följer. Stegen visar hur du konfigurerar och förbereder Data Box-enhet enheter för MARS-agenten för att identifiera och överföra de första säkerhets kopierings data.

### <a name="set-up-azure-data-box-disks"></a>Konfigurera Azure Data Box diskar

Om du har ordnat en eller flera Azure Data Box diskar (upp till 8 TB var och en) följer du stegen som beskrivs här för att [packa upp, ansluta och låsa upp data Box-enhet disken](../databox/data-box-disk-deploy-set-up.md).

>[!NOTE]
>Det är möjligt att servern med MARS-agenten inte har någon USB-port. I så fall kan du ansluta Azure Data Box-disken till en annan server eller klient och exponera enhetens rot som en nätverks resurs.

### <a name="set-up-azure-data-box"></a>Konfigurera Azure Data Box

Om du beställde en Azure Data Box instans (upp till 100 TB) följer du stegen här [för att konfigurera din data Box-enhet-instans](../databox/data-box-deploy-set-up.md).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Montera Azure Data Box-instansen som ett lokalt system

MARS-agenten fungerar i det lokala systemets kontext, så den kräver att samma behörighets nivå anges för monterings Sök vägen där Azure Data Box-instansen är ansluten.

För att säkerställa att du kan montera din Data Box-enhet-enhet som ett lokalt system med hjälp av NFS-protokollet:

1. Aktivera klienten för NFS-funktionen på Windows-servern som har MARS-agenten installerad. Ange alternativ källa *WIM: D: \Sources\Install.wim: 4*.
1. Hämta PsExec från [Sysinternals](/sysinternals/downloads/psexec) -sidan till servern med mars-agenten installerad.
1. Öppna en kommando tolk med förhöjd behörighet och kör följande kommando med katalogen som innehåller *PSExec.exe* som den aktuella katalogen.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Kommando fönstret som öppnas på grund av föregående kommando finns i det lokala systemets kontext. Använd det här kommando fönstret för att utföra stegen för att montera Azure-sidans BLOB-resurs som en nätverks enhet på Windows Server.
1. Följ stegen i [Anslut till data Box-enhet](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) för att ansluta servern med mars-agenten till data Box-enhet-enheten via NFS. Kör följande kommando i kommando tolken för den lokala datorn för att montera Azure Page blobs-resursen.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   När resursen har monterats kontrollerar du om du kan komma åt X: från servern. Om du kan kan du fortsätta med nästa avsnitt i den här artikeln.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Överför första säkerhets kopierings data till Azure Data Box enheter

1. Öppna programmet **Microsoft Azure Backup** på servern.
1. I fönstret **åtgärder** väljer du **Schemalägg säkerhets kopiering**.

    ![Välj Schemalägg säkerhets kopiering](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Följ stegen i **guiden Schemalägg säkerhets kopiering**.

1. Lägg till objekt genom att välja knappen **Lägg till objekt** . Behåll den totala storleken för objekten inom [storleks gränserna som stöds av den Azure Data Box SKU](#backup-data-size-and-supported-data-box-skus) som du beställde och mottog.

    ![Lägg till objekt i säkerhets kopian](./media/offline-backup-azure-data-box/add-items.png)

1. Välj lämpligt schema för säkerhets kopiering och bevarande princip för **filer och mappar** och **system tillstånd**. System tillstånd gäller endast för Windows-servrar och inte för Windows-klienter.
1. På sidan **Välj första säkerhets kopierings typ (filer och mappar)** i guiden väljer du alternativet **överför med Microsoft Azure Data Box-enhet diskar** och väljer **sedan nästa**.

    ![Välj första säkerhets kopierings typ](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Logga in på Azure när du uppmanas att använda de användarautentiseringsuppgifter som har ägar åtkomst till Azure-prenumerationen. När du har lyckats med detta bör du se en sida som liknar den här.

    ![Skapa resurser och tillämpa nödvändiga behörigheter](./media/offline-backup-azure-data-box/creating-resources.png)

   MARS-agenten hämtar sedan de Data Box-enhet jobben i prenumerationen som har statusen levererad.

    ![Hämta Data Box-enhet jobb för prenumerations-ID](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Välj rätt Data Box-enhet ordning som du har packat upp, anslutit och olåst Data Box-enhet disken. Välj **Nästa**.

    ![Välj Data Box-enhets order](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Välj **identifiera enhet** på sidan **data Box-enhet enhets identifiering** . Den här åtgärden gör att MARS-agenten söker efter lokalt anslutna Azure Data Box diskar och identifierar dem.

    ![Identifiering av Data Box-enhet enhet](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Om du anslöt Azure Data Box-instansen som en nätverks resurs (på grund av otillgängligheten hos USB-portar eller eftersom du beställde och monterat den 100-TB Data Box-enhet enheten) Miss lyckas identifieringen vid första. Du har fått möjlighet att ange nätverks Sök vägen till Data Box-enhet enheten.

    ![Ange Nätverks Sök vägen](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Ange Nätverks Sök vägen till rot katalogen på den Azure Data Box disken. Katalogen måste innehålla en katalog med namnet *PageBlob*.
    >
    >![Azure Data Box diskens rot Katalog](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Om till exempel sökvägen till disken är `\\mydomain\myserver\disk1\` och *Disk1* innehåller en katalog med namnet *PageBlob*, är den sökväg som du anger på sidan för guiden mars agent `\\mydomain\myserver\disk1\` .
    >
    >Om du [konfigurerar en Azure Data Box 100-TB-enhet](#set-up-azure-data-box-devices)anger du `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` som nätverks Sök väg till enheten.

1. Välj **Nästa** och välj **Slutför** på nästa sida för att spara säkerhets kopierings-och bevarande principen med konfigurationen av offline-säkerhetskopiering med hjälp av Azure Data box.

   Följande sida bekräftar att principen har sparats.

    ![Principen har sparats](./media/offline-backup-azure-data-box/policy-saved.png)

1. Välj **Stäng** på föregående sida.

1. Välj **Säkerhetskopiera nu** i fönstret **åtgärder** i mars agent-konsolen. Välj **säkerhetskopiera** på Guide sidan.

    ![Guiden Säkerhetskopiera nu](./media/offline-backup-azure-data-box/backup-now.png)

MARS-agenten börjar säkerhetskopiera de data som du har valt för den Azure Data Box enheten. Den här processen kan ta flera timmar till några dagar. Hur lång tid det tar beror på antalet filer och anslutnings hastigheten mellan servern med MARS-agenten och den Azure Data Box disken.

När säkerhets kopieringen av data har avslut ATS visas en sida på MARS-agenten som liknar den här.

![Säkerhets kopierings förlopp visas](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Steg efter säkerhets kopiering

I det här avsnittet beskrivs de steg som du vidtar när säkerhets kopieringen av data till Azure Data Box Disk har slutförts.

- Följ stegen i den här artikeln för att [leverera Azure Data Box-disken till Azure](../databox/data-box-disk-deploy-picked-up.md). Om du använde en Azure Data Box 100-TB-enhet följer du stegen nedan för att [leverera Azure Data Box-enheten till Azure](../databox/data-box-deploy-picked-up.md).

- [Övervaka data Box-enhet jobb](../databox/data-box-disk-deploy-upload-verify.md) i Azure Portal. När Azure Data Box jobbet är klart flyttar MARS-agenten automatiskt data från lagrings kontot till Recovery Services-valvet vid nästa schemalagda säkerhets kopiering. Sedan markeras säkerhets kopierings jobbet som *jobbet slutfört* om en återställnings punkt har skapats.

    >[!NOTE]
    >MARS-agenten utlöser säkerhets kopieringar vid schemalagda tidpunkter när principen skapas. Jobb flaggan "väntar på att Azure Data Box jobb ska slutföras" tills jobbet har slutförts.

- När MARS-agenten har skapat en återställnings punkt som motsvarar den första säkerhets kopieringen kan du ta bort lagrings kontot eller det angivna innehållet som är kopplat till Azure Data Box jobbet.

## <a name="troubleshooting"></a>Felsökning

Microsoft Azure Recovery Services (MARS)-agenten skapar ett Azure Active Directory (Azure AD)-program åt dig i din klient. Det här programmet kräver ett certifikat för autentisering som skapas och laddas upp när du konfigurerar en seeding-princip offline. Vi använder Azure PowerShell för att skapa och ladda upp certifikatet till Azure AD-programmet.

### <a name="problem"></a>Problem

När du konfigurerar säkerhets kopiering offline kan det uppstå problem på grund av ett fel i Azure PowerShell-cmdleten. Du kanske inte kan lägga till flera certifikat i samma Azure AD-program som skapats av monoklonal-agenten. Det här problemet påverkar dig om du har konfigurerat en seeding-princip offline för samma eller en annan server.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Kontrol lera om problemet orsakas av denna grundläggande orsak

Gör något av följande för att se om problemet är detsamma som det tidigare beskrivits.

#### <a name="step-1-of-verification"></a>Steg 1 i verifieringen

Kontrol lera om följande fel meddelande visas i "monoklonal-konsolen" när du konfigurerade offline-säkerhetskopiering.

![Det gick inte att skapa en princip för offline-säkerhetskopiering för det aktuella Azure-kontot](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Steg 2 av verifieringen

1. Öppna **Temp** -mappen i installations Sök vägen. Standard Sök vägen för temp är *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Leta efter filen *CBUICurr* och öppna filen.

1. Bläddra till den sista raden i *CBUICurr* -filen och kontrol lera om problemet är detsamma som i det här fel meddelandet: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Lösning

Som en lösning för att lösa det här problemet utför du följande steg och försöker sedan utföra princip konfigurationen igen.

#### <a name="step-1-of-workaround"></a>Steg 1 i lösningen

Logga in på PowerShell som visas i användar gränssnittet för användar gränssnittet genom att använda ett annat konto med administratörs åtkomst för den prenumeration som ska användas för att Data Box-enhet jobbet skapas.

#### <a name="step-2-of-workaround"></a>Steg 2 i lösningen

Om ingen annan server har offline-dirigering konfigurerad och ingen annan server är beroende av `AzureOfflineBackup_<Azure User Id>` programmet, tar du bort det här programmet. Välj **Azure Portal**  >  **Azure Active Directory**  >  **Appregistreringar**.

>[!NOTE]
> Kontrol lera om `AzureOfflineBackup_<Azure User Id>` programmet inte har någon annan offline-dirigering konfigurerad och om ingen annan server är beroende av det här programmet. Gå till **Inställningar**  >  **nycklar** under avsnittet **offentliga nycklar** . Inga andra offentliga nycklar har lagts till. Se följande skärm bild för referens.
>
>![Offentliga nycklar](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Steg 3

Utför följande åtgärder från den server som du försöker konfigurera för säkerhets kopiering offline.

1. Gå till fliken **hantera datorns certifikat program**  >  **personliga** och Sök efter certifikatet med namnet `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. Välj certifikatet, högerklicka på **alla uppgifter** och välj **Exportera** utan privat nyckel i. cer-format.

3. Gå till säkerhets kopierings programmet för Azure offline som nämns i steg 2. Välj **Inställningar**  >  **nycklar**  >  **Ladda upp offentlig nyckel**. Överför certifikatet som du exporterade i föregående steg.

    ![Ladda upp offentlig nyckel](./media/offline-backup-azure-data-box/upload-public-key.png)

4. På-servern öppnar du registret genom att skriva in **regedit** i körnings fönstret.

5. Gå till registret *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Högerklicka på **CloudBackupProvider** och Lägg till ett nytt sträng värde med namnet `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Hämta användar-ID: t för Azure genom att utföra någon av följande åtgärder:
    >
    >- Kör kommandot från Azure-anslutna PowerShell `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` .
    > - Gå till register Sök vägen `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` med namnet *CurrentUserId*.

6. Högerklicka på den sträng som du lade till i föregående steg och välj **ändra**. I värdet anger du tumavtrycket för det certifikat som du exporterade i steg 2. Välj **OK**.

7. Om du vill hämta värdet för tumavtrycket dubbelklickar du på certifikatet. Välj fliken **information** och rulla nedåt tills du ser fältet tumavtryck. Välj **tumavtryck** och kopiera värdet.

    ![Tumavtryck för certifikat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Frågor

Kontakta om du har frågor eller vill veta mer om eventuella problem [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
