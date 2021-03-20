---
title: Säkerhets kopiering offline med Azure Data Box för DPM och MABS
description: Du kan använda Azure Data Box för att dirigera inledande säkerhets kopierings data offline från DPM och MABS.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 1cfd9131099ad6a8ccd3d43e93f3d97641514f03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752557"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Seeding offline med Azure Data Box för DPM och MABS (för hands version)

> [!NOTE]
> Den här funktionen gäller för Data Protection Manager (DPM) 2019 UR2 och senare.<br><br>
> Den här funktionen är för närvarande en för hands version för Microsoft Azure Backup Server (MABS). Om du är intresse rad av att använda Azure Data Box för att dirigera Seed med MABS kan du kontakta oss på [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

Den här artikeln förklarar hur du kan använda Azure Data Box för att dirigera inledande säkerhets kopierings data offline från DPM och MABS till ett Azure Recovery Services-valv.

Du kan använda [Azure Data Box](../databox/data-box-overview.md) för att dirigera dina stora inledande DPM/Mabs-säkerhetskopieringar offline (utan att använda nätverket) till ett Recovery Services-valv. Den här processen sparar tid och nätverks bandbredd som annars skulle konsumeras för att flytta stora mängder säkerhetskopierade data online över ett nätverk med hög latens. Den här funktionen finns för närvarande som en förhandsversion.

Offline-säkerhetskopiering som baseras på Azure Data Box ger två distinkta fördelar jämfört med [offline-säkerhetskopiering baserat på Azure import/export-tjänsten](backup-azure-backup-server-import-export.md):

- Du behöver inte skaffa dina egna Azure-kompatibla diskar och anslutningar. Azure Data Box levererar de diskar som är associerade med den valda [data Box-enhet SKU: n](https://azure.microsoft.com/services/databox/data/).

- Azure Backup (MARS-agent) kan skriva säkerhets kopierings data direkt till de SKU: er som stöds av Azure Data Box. Den här funktionen eliminerar behovet av att etablera en mellanlagringsplats för dina första säkerhets kopierings data. Du behöver inte heller något verktyg för att formatera och kopiera dessa data till diskarna.

## <a name="supported-platforms"></a>Plattformar som stöds

Följande plattformar stöds:

- Windows Server 2019 64 bitar (standard, data Center, Essentials)
- Windows Server 2016 64 bitar (standard, data Center, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Säkerhetskopiera data storlek och Data Box-enhet SKU: er som stöds

Följande Data Box-enhet SKU: er stöds:

| Säkerhets kopierings data storlek (efter komprimering per MARS) \* per server | Azure Data Box SKU som stöds |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| > 7,2 TB och <= 80 TB\*\* | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

\*Vanliga komprimerings hastigheter varierar mellan 10-20% <br>
\*\*Kontakta [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) om du förväntar dig att ha mer än 80 TB första säkerhets kopierings data för en enskild data källa.

> [!IMPORTANT]
> Första säkerhets kopierings data från en enda data källa måste finnas i en enskild Azure Data Box eller Azure Data Box disk och kan inte delas mellan flera enheter av samma eller olika SKU: er. En Azure Data Box kan dock innehålla första säkerhets kopieringar från flera data källor.

## <a name="before-you-begin"></a>Innan du börjar

MARS-agenten som körs på DPM/MABS bör uppgraderas till den [senaste versionen](https://aka.ms/azurebackup_agent) (2.0.9171.0 eller senare).

Kontrollera följande:

### <a name="azure-subscription-and-required-permissions"></a>Azure-prenumeration och nödvändiga behörigheter

- En giltig Azure-prenumeration.
- Användaren som ska utföra säkerhets kopierings principen offline måste vara ägare till Azure-prenumerationen.
- Data Box-enhet-jobbet och Recovery Services-valvet som data måste dirigeras till måste vara tillgängliga i samma prenumerationer.
    > [!NOTE]
    > Vi rekommenderar att mål lagrings kontot och Recovery Services valvet befinner sig i samma region. Detta är dock inte obligatoriskt.

### <a name="order-and-receive-the-data-box-device"></a>Beställ och ta emot den Data Box-enhet enheten

Se till att de nödvändiga Data Box-enhet enheterna är i ett *levererat* tillstånd innan du utlöser säkerhets kopiering offline. Se [säkerhets kopierings data storlek och stöd för data Box-enhet SKU: er](#backup-data-size-and-supported-data-box-skus) för att beställa den lämpligaste SKU: n för ditt krav. Följ stegen i [den här artikeln](../databox/data-box-disk-deploy-ordered.md) för att beställa och ta emot dina data Box-enhet enheter.

> [!IMPORTANT]
> Välj inte *BlobStorage* för **konto typen**. DPM/MABS-servern kräver ett konto som stöder sid-blobar som inte stöds när *BlobStorage* har valts. Välj  **Storage v2 (generell användning v2)** som **konto typ** när du skapar mål lagrings kontot för ditt Azure Data Box-jobb.

![Konfigurera Azure Data Center](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Konfigurera Azure Data Box enheter

När du har fått Azure Data Box enheten, beroende på vilken Azure Data Box SKU du har beställt, utför du stegen i lämpliga avsnitt nedan för att konfigurera och förbereda de Data Box-enhet enheterna för DPM/MABS-servern för att identifiera och överföra de första säkerhets kopierings data.

### <a name="setup-azure-data-box-disk"></a>Installations Azure Data Box disk

Om du har ordnat en eller flera Azure Data Box diskar (upp till 8 TB var och en) följer du stegen som beskrivs [här](../databox/data-box-disk-deploy-set-up.md) för att packa upp, ansluta och låsa upp data Box-enhet disken.

> [!NOTE]
> Det är möjligt att DPM/MABS-servern inte har någon USB-port. I ett sådant scenario kan du ansluta Azure Data Box-disken till en annan server/klient och exponera enhetens rot som en nätverks resurs.

## <a name="setup-azure-data-box"></a>Installations Azure Data Box

Om du beställde en Azure Data Box (upp till 100 TB) följer du stegen som beskrivs [här](../databox/data-box-deploy-set-up.md) för att konfigurera din data Box-enhet.

### <a name="mount-your-azure-data-box-as-local-system"></a>Montera Azure Data Box som lokalt system

DPM/MABS-servern fungerar i system kontexten och kräver att samma behörighets nivå anges för monterings Sök vägen där Azure Data Box är ansluten. Följ stegen nedan för att se till att du kan montera din Data Box-enhet-enhet som lokalt system med hjälp av NFS-protokollet.

1. Aktivera funktionen klient för NFS på DPM/MABS-servern.
Ange alternativ källa: *WIM: D: \Sources\Install.wim: 4*
2. Ladda ned **PsExec** från [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) till DPM/Mabs-servern.
3. Öppna en kommando tolk med förhöjd behörighet och kör följande kommando med katalogen som innehåller *PSExec.exe* som aktuell katalog.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. Kommando fönstret som öppnas som ett resultat av kommandot ovan är i det lokala systemets kontext. Använd det här kommando fönstret för att köra steg för att montera Azure-sidans BLOB-resurs som en nätverks enhet på Windows Server.
5. Följ stegen [här](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) för att ansluta din DPM/Mabs-server till den data Box-enhet enheten via NFS och kör följande kommando i kommando tolken för den lokala datorn för att montera Azure Page blobs-resursen:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. När du har monterat kontrollerar du om du kan komma åt X: från servern. Om du kan kan du fortsätta med nästa avsnitt i den här artikeln.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Överför första säkerhets kopierings data till Azure Data Box enheter

1. På DPM/MABS-servern följer du stegen för att [skapa en ny skydds grupp](/system-center/dpm/create-dpm-protection-groups). Om du lägger till ett onlineskydd-skydd i den befintliga skydds gruppen högerklickar du på den befintliga skydds gruppen och väljer **Lägg till onlineskydd** och börja från **steg 8**.
2. På sidan **Välj grupp medlemmar** anger du de datorer och källor som du vill säkerhetskopiera.
3. På sidan **Välj data skydds metod** anger du hur du vill hantera kortsiktig och långsiktig säkerhets kopiering. Se till att du väljer **Jag vill ha onlineskydd.**

   ![Skapa ny skydds grupp](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. På sidan **Välj kortsiktiga mål** anger du hur du vill säkerhetskopiera till kortsiktig lagring på disk.
5. På sidan **Granska diskallokering** granskar du allokerat disk utrymme för lagringspoolen för skydds gruppen.
6. På sidan **Välj metod för skapande av replik** väljer du **automatiskt över nätverket.**
7. På sidan **Välj alternativ för konsekvens kontroll** väljer du hur du vill automatisera konsekvens kontroller.
8. På sidan **Ange online skydds data** väljer du den medlem som du vill aktivera onlineskydd.

    ![Ange skydds data online](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. På sidan **Ange schema för onlinesäkerhetskopiering** anger du hur ofta stegvisa säkerhets kopieringar ska ske i Azure.
10. På sidan **Ange bevarande princip för online** anger du hur återställnings punkterna som skapas från säkerhets kopiorna per dag/vecka/månad/år ska behållas i Azure.
11. På skärmen **Välj replikering online** i guiden väljer du alternativ **överföring med Microsoft-ägda diskar** och väljer **sedan nästa**.

    ![Välj inledande replikering online](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > Alternativet att välja **överföring med Microsofts ägda diskar** är inte tillgängligt för Mabs v3 eftersom funktionen är en för hands version. Kontakta oss på [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) om du vill använda den här funktionen för Mabs v3.

12. Logga in i Azure när du uppmanas att använda de användarautentiseringsuppgifter som har ägar åtkomst till Azure-prenumerationen. Efter en lyckad inloggning visas följande skärm:

    ![Efter lyckad inloggning](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     DPM/MABS-servern hämtar sedan de Data Box-enhet jobben i prenumerationen som har statusen *levererad* .

     > [!NOTE]
     > Det tar längre tid än vanligt att logga in för första gången. Modulen Azure PowerShell installeras i bakgrunden, och även Azure AD-programmet är registrerat.
     >
     >  - Följande PowerShell-moduler är installerade:<br>
          -AzureRM. Profile     *5.8.3*<br>
          -AzureRM. Resources   *6.7.3*<br>
          – AzureRM. Storage     *5.2.0*<br>
          – Azure. Storage       *4.6.1*<br>
     >  - Azure AD-programmet registreras som *AzureOfflineBackup_ \<object GUID of the user>*.

13. Välj rätt data Box ordning som du har packat upp, anslutit och olåst Data Box-enhet disken. Välj **Nästa**.

    ![Välj data rutan](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. På skärmen **identifiera datarutan** anger du sökvägen till data Box-enhet enheten och väljer sedan **identifiera enhet**.

    ![Ange Nätverks Sök väg](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Ange Nätverks Sök vägen till rot katalogen på den Azure Data Box disken. Katalogen måste innehålla en katalog med namnet *PageBlob* som visas nedan:
    >
    > ![USB-enhet](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Om till exempel sökvägen till disken är `\\mydomain\myserver\disk1\` och *Disk1* innehåller en katalog med namnet *PageBlob*, är sökvägen som ska anges i guiden DPM/Mabs Server `\\mydomain\myserver\disk1\` .
    > Om du [installerar en Azure Data Box 100 TB-enhet](./offline-backup-azure-data-box.md#set-up-azure-data-box)anger du följande som nätverks Sök väg till enheten `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. Välj **Nästa**. På sidan **Sammanfattning** granskar du inställningarna och väljer **Skapa grupp**.

    ![Identifiera data](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    Följande skärm bild visar att skydds gruppen har skapats.

    ![Skydds grupp har skapats](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Välj **Stäng** på skärmen ovan.

    Med detta sker den inledande replikeringen av data till DPM/MABS-disken. När skyddet är klart visar grupp status skydds status som **OK** på sidan **skydd** .

17. För att initiera säkerhets kopiering offline till din Azure Data Box enhet högerklickar du på **skydds gruppen** och väljer sedan alternativet för att **skapa återställnings punkt** . Sedan väljer du alternativet **onlineskydd** .

    ![Skapa återställnings punkt](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Återställnings punkt](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   DPM/MABS-servern kommer att börja säkerhetskopiera de data som du har valt för den Azure Data Box enheten. Detta kan ta från flera timmar till några dagar, beroende på storleken på data och anslutnings hastigheten mellan DPM/MABS-servern och Azure Data Box Disk.

   Du kan övervaka jobbets status i fönstret **övervakning** . När säkerhets kopieringen av data har slutförts visas en skärm som liknar den som visas nedan:

   ![Administratörs konsol](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Steg efter säkerhets kopiering

Följ dessa steg när säkerhets kopieringen av data till Azure Data Box Disk har slutförts.

- Följ stegen i [den här artikeln](../databox/data-box-disk-deploy-picked-up.md) för att leverera Azure Data Box-disken till Azure. Om du använde en Azure Data Box 100-TB-enhet följer du [stegen](../databox/data-box-deploy-picked-up.md) nedan för att leverera Azure Data Box till Azure.
- [Övervaka data Box-enhet jobb](../databox/data-box-disk-deploy-upload-verify.md) i Azure Portal. När Azure Data Box jobbet är *klart* flyttar DPM/Mabs-servern automatiskt data från lagrings kontot till Recovery Services-valvet vid nästa schemalagda säkerhets kopiering. Då markeras säkerhets kopierings jobbet som *jobbet slutfört* om en återställnings punkt har skapats.

  > [!NOTE]
  > DPM/MABS-servern utlöser säkerhets kopieringarna vid de tidpunkter som har schemalagts under skapande av skydds grupper. Dessa jobb flaggar dock *väntar på att Azure Data Box jobbet ska slutföras* tills jobbet är klart.

- När DPM/MABS-servern har skapat en återställnings punkt som motsvarar den första säkerhets kopieringen kan du ta bort lagrings kontot (eller det angivna innehållet) som är kopplat till Azure Data Box jobbet.

## <a name="troubleshooting"></a>Felsökning

Microsoft Azure Backup-agenten (monoklonal) på DPM-servern skapar ett Azure AD-program åt dig i din klient organisation. Det här programmet kräver ett certifikat för autentisering som skapas och laddas upp när du konfigurerar Dirigerings principen offline.

Vi använder Azure PowerShell för att skapa och ladda upp certifikatet till Azure AD-programmet.

### <a name="issue"></a>Problem

Vid konfigurering av säkerhets kopiering offline, på grund av ett känt kod fel i Azure PowerShell cmdleten, kan du inte lägga till flera certifikat i samma Azure AD-program som skapats av monoklonal-agenten. Detta kommer att påverka dig om du har konfigurerat en seeding-princip offline för samma eller en annan server.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Kontrol lera om problemet orsakas av denna grundläggande orsak

Utför något av följande steg för att säkerställa att felet beror på [problemet](#issue) ovan:

#### <a name="step-1"></a>Steg 1

Kontrol lera om följande fel meddelande visas i DPM/MABS-konsolen vid konfigurering av säkerhets kopiering offline:

![Azure Recovery Services-agent](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Steg 2

1. Öppna **Temp** -mappen i installations Sök vägen (standard Sök vägen för temp är *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Leta efter *CBUICurr* -filen och öppna filen.
2. Bläddra till den sista raden i *CBUICurr* -filen och kontrol lera om problemet beror på att det inte går att skapa ett Azure AD-programautentiseringsuppgifter på kundens konto. Undantag: uppdatering av befintlig autentiseringsuppgift med KeyId \<some guid> tillåts inte.

### <a name="workaround"></a>Lösning

Lös problemet genom att utföra följande steg och försöka utföra princip konfigurationen igen.

1. Logga in på Azures inloggnings sida som visas på DPM/MABS-serverns användar gränssnitt med ett annat konto med administratörs behörighet för den prenumeration som ska användas för att skapa Data Box-enhet jobb.
2. Om ingen annan server har offline-dirigering konfigurerad och ingen annan server är beroende av `AzureOfflineBackup_<Azure User Id>` programmet, tar du bort det här programmet från **Azure Portal > Azure Active Directory > Appregistreringar**.

   > [!NOTE]
   > Kontrol lera om programmet `AzureOfflineBackup_<Azure User Id>` inte har någon annan offlineadressbok konfigurerad och att ingen annan server är beroende av det här programmet. Gå till **inställningar > nycklar** under avsnittet offentliga nycklar. Inga andra **offentliga nycklar** har lagts till. Se följande skärm bild för referens:
   >
   > ![Offentliga nycklar](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Steg 3

Utför följande åtgärder från den DPM/MABS-server som du försöker konfigurera säkerhets kopiering offline:

1. Öppna fliken **hantera datorns certifikat program**  >  **personliga** och leta efter certifikatet med namnet `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. Välj certifikatet ovan, högerklicka på **alla aktiviteter** och **Exportera** utan privat nyckel i. cer-format.
3. Gå till säkerhets kopierings programmet för Azure offline som anges i **punkt 2**. I **Inställningar**  >  **nycklar**  >  **Ladda upp offentlig nyckel** laddar du upp certifikatet som exporterades i steget ovan.

   ![Ladda upp offentliga nycklar](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. På-servern öppnar du registret genom att skriva **regedit** i **körnings** fönstret.
5. Gå till registret *Computer\HKEY \_ lokala \_ MACHINE\SOFTWARE\Microsoft\Windows Azure-Backup\Config\CloudBackupProvider*. Högerklicka på **CloudBackupProvider** och Lägg till ett nytt sträng värde med namnet `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Hämta användar-ID: t för Azure genom att utföra någon av följande åtgärder:
    >
    >- Kör kommandot från Azure-anslutna PowerShell `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` .
    > - Navigera till register Sök vägen `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` med namnet *CurrentUserId*.

6. Högerklicka på den sträng som du har lagt till i steget ovan och välj **ändra**. I värdet anger du tumavtrycket för det certifikat som du exporterade i **punkt 2** och väljer **OK**.
7. Hämta värdet för tumavtryck genom att dubbelklicka på certifikatet och sedan välja **information**  och rulla nedåt tills du ser fältet tumavtryck. Välj **tumavtryck** och kopiera värdet.

   ![Tumavtryck-värde](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Nästa steg

- [Offline-seeding med egen disk (med Azure import/export-tjänsten)](backup-azure-backup-server-import-export.md)
