---
title: Säkerhetskopiera virtuella datorer i Azure VMware-lösningen med Azure Backup Server
description: Konfigurera din Azure VMware-lösning för att säkerhetskopiera virtuella datorer med hjälp av Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 163065556b7dbc979d68613744ea827c209d9fda
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618924"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Säkerhetskopiera virtuella datorer i Azure VMware-lösningen med Azure Backup Server

I den här artikeln ska vi säkerhetskopiera virtuella VMware-datorer (VM: ar) som körs på Azure VMware-lösning med Azure Backup Server. Börja med att gå igenom [konfigurera Microsoft Azure Backup Server för Azure VMware-lösning](set-up-backup-server-for-azure-vmware-solution.md).

Sedan kommer vi att gå igenom alla nödvändiga procedurer för att:

> [!div class="checklist"] 
> * Konfigurera en säker kanal så att Azure Backup Server kan kommunicera med VMware-servrar via HTTPS. 
> * Lägg till kontoautentiseringsuppgifter till Azure Backup Server. 
> * Lägg till vCenter i Azure Backup Server. 
> * Konfigurera en skydds grupp som innehåller de virtuella VMware-datorer som du vill säkerhetskopiera, ange inställningar för säkerhets kopiering och Schemalägg säkerhets kopieringen.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter-servern

Azure Backup Server kommunicerar som standard med VMware-servrar via HTTPS. Om du vill konfigurera HTTPS-anslutningen laddar du ned VMware Certificate Authority-certifikatet (CA) och importerar det på Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurera certifikatet

1. I webbläsaren, på Azure Backup Server dator, anger du webb adressen för webb klienten vSphere.

   > [!NOTE] 
   > Om sidan VMware **komma igång** inte visas kontrollerar du inställningarna för anslutning och webbläsare och försöker igen.

1. På sidan VMware **komma igång** väljer du **Hämta betrodda certifikat från rot certifikat utfärdare**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere-webbklient":::

1. Spara **download.zip** -filen på Azure Backup Server datorn och extrahera sedan innehållet till mappen **certifikat** , som innehåller:

   - Rot certifikat fil med ett tillägg som börjar med en numrerad sekvens som. 0 och. 1.
   - CRL-fil med ett tillägg som börjar med en sekvens som. R0 eller. R1.

1. I mappen **certifikat** högerklickar du på rot certifikat filen och väljer **Byt namn** för att ändra tillägget till **. CRT**.

   Fil ikonen ändras till en som representerar ett rot certifikat.

1. Högerklicka på rot certifikatet och välj **Installera certifikat**.

1. I **guiden Importera certifikat** väljer du **lokal dator** som mål för certifikatet och väljer **Nästa**.

   ![Välkomst sida för guiden](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Bekräfta att du vill tillåta ändringar på datorn om du uppmanas att göra det.

1. Välj **Placera alla certifikat i följande Arkiv** och välj **Bläddra** för att välja certifikat arkivet.

   ![Certifikat lagring](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Välj **betrodda rot certifikat utfärdare** som målmapp och välj **OK**.

1. Granska inställningarna och välj **Slutför** för att börja importera certifikatet.

   ![Verifiera att certifikatet finns i rätt mapp](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. När certifikat importen har bekräftats loggar du in på vCenter-servern för att bekräfta att anslutningen är säker.

### <a name="enable-tls-12-on-azure-backup-server"></a>Aktivera TLS 1,2 på Azure Backup Server

VMware 6,7 och tidigare hade TLS aktiverat som kommunikations protokoll. 

1. Kopiera följande register inställningar och klistra in dem i anteckningar. Spara sedan filen som TLS. REG utan tillägget. txt.

   ```
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Högerklicka på TLS. REG-filen och välj **sammanfoga** eller **Öppna** för att lägga till inställningarna i registret.


## <a name="add-the-account-on-azure-backup-server"></a>Lägg till kontot på Azure Backup Server

1. Öppna Azure Backup Server och välj **hanterings**  >  **produktions servrar** i Azure Backup Server-konsolen  >  **Hantera VMware**.

   ![Azure Backup Server-konsol](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. I dialog rutan **Hantera autentiseringsuppgifter** väljer du **Lägg till**.

   ![I dialog rutan Hantera autentiseringsuppgifter väljer du Lägg till.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. I dialog rutan **Lägg till autentiseringsuppgift** anger du ett namn och en beskrivning för den nya autentiseringsuppgiften. Ange det användar namn och lösen ord som du har definierat på VMware-servern.

   > [!NOTE] 
   > Om VMware-servern och Azure Backup Server inte finns i samma domän, anger du domänen i rutan **användar namn** .

   ![Dialog rutan Azure Backup Server Lägg till autentiseringsuppgift](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Välj **Lägg** till för att lägga till den nya autentiseringsuppgiften.

   ![Skärm bild som visar dialog rutan Azure Backup Server Hantera autentiseringsuppgifter med nya autentiseringsuppgifter som visas.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Lägg till vCenter-servern i Azure Backup Server

1. I Azure Backup Server-konsolen väljer du **hanterings**  >  **produktions servrar**  >  **Lägg till**.

   ![Öppna guiden för att lägga till produktions server](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Välj **VMware-servrar** och välj **Nästa**.

   ![Guiden för att lägga till produktions server](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Ange IP-adressen för vCenter.

   ![Ange VMware-Server](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. I rutan **SSL-port** anger du den port som används för att kommunicera med vCenter.

   > [!TIP] 
   > Port 443 är standard porten, men du kan ändra den om vCenter lyssnar på en annan port.

1. I rutan **ange autentiseringsuppgifter** väljer du de autentiseringsuppgifter som du skapade i föregående avsnitt.

1. Välj **Lägg** till för att lägga till vCenter i listan servrar och välj **Nästa**.

   ![Lägg till VMware-Server och autentiseringsuppgift](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. På sidan **Sammanfattning** väljer du **Lägg** till för att lägga till vCenter till Azure Backup Server.

   Den nya servern läggs till omedelbart. vCenter behöver ingen agent.

   ![Lägg till VMware-Server i Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. På sidan **Slutför** granskar du inställningarna och väljer sedan **Stäng**.

   ![Sidan Slutför](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Du ser vCenter-servern som visas under **produktions server** med:
   - Typ som **VMware-Server** 
   - Agent status som **OK** 
   
      Om du ser **agent status** som **okänd** väljer du **Uppdatera**.

## <a name="configure-a-protection-group"></a>Konfigurera en skydds grupp

Skydds grupper samlar in flera virtuella datorer och tillämpar samma data lagrings-och säkerhets kopierings inställningar för alla virtuella datorer i gruppen.

1. I Azure Backup Server-konsolen väljer du **skydd**  >  **nytt**.

   ![Öppna guiden Skapa ny skydds grupp](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. På Välkomst sidan **Skapa ny skydds grupp** väljer du **Nästa**.

   ![Dialog rutan skapa ny skydds grupp guide](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Välj **servrar** på sidan **Välj typ av skydds grupp** och välj sedan **Nästa**. Sidan **Välj grupp medlemmar** visas.

1. På sidan **Välj grupp medlemmar** väljer du de virtuella datorer (eller VM-mappar) som du vill säkerhetskopiera och väljer sedan **Nästa**.

   > [!NOTE]
   > När du väljer en mapp eller virtuella datorer väljs även mappar i mappen för säkerhets kopiering. Du kan avmarkera mappar och virtuella datorer som du inte vill säkerhetskopiera. Om en virtuell dator eller mapp redan säkerhets kopie ras kan du inte välja den, vilket garanterar att dubbla återställnings punkter inte skapas för en virtuell dator.

   ![Välj grupp medlemmar](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. På sidan **Välj data skydds metod** anger du ett namn på skydds gruppen och skydds inställningarna. 

1. Ange kortvarigt skydd till **disk**, aktivera onlineskydd och välj sedan **Nästa**.

   ![Välj dataskyddsmetod](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Ange hur länge du vill behålla data som säkerhets kopie ras till disk.

   - **Kvarhållningsintervall**: antalet dagar som disk återställnings punkter behålls.
   - **Snabb och fullständig säkerhets kopiering**: hur ofta disk återställnings punkter tas. Om du vill ändra de tidpunkter eller datum då kortsiktiga säkerhets kopieringar sker väljer du **ändra**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Ange kortsiktiga mål för disk-baserat skydd":::

1. På sidan **granska disklagring allokering** granskar du disk utrymmet för säkerhets kopiorna för virtuella datorer.

   - De rekommenderade disk tilldelningarna baseras på det kvarhållningsintervall du angav, typ av arbets belastning och storleken på skyddade data. Gör nödvändiga ändringar och välj sedan **Nästa**.
   - **Data storlek:** Storlek på data i skydds gruppen.
   - **Disk utrymme:** Rekommenderad mängd disk utrymme för skydds gruppen. Om du vill ändra den här inställningen väljer du utrymme som är ljust större än det belopp du uppskattar varje data källa växer.
   - **Information om lagringspool:** Visar status för lagringspoolen, som innehåller total och återstående disk storlek.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Granska det disk utrymme som tilldelats i lagringspoolen":::

   > [!NOTE]
   > I vissa fall är data storleken som rapporteras större än den faktiska storleken på den virtuella datorn. Vi är medvetna om problemet och undersöker det just nu.

1. På sidan **Välj metod för skapande av replik** anger du hur du vill att den första säkerhets kopieringen ska utföras och väljer **sedan nästa**.

   - Standardvärdet sker **automatiskt över nätverket** och **nu**. Om du använder standardvärdet anger du en tid med låg belastning. Om du väljer **senare** anger du en dag och en tidpunkt.
   - Överväg att replikera data offline med hjälp av flyttbara media för stora mängder data eller mindre än optimala nätverks förhållanden.

   ![Välj metod för skapande av replik](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. För **alternativ för konsekvens kontroll** väljer du hur och när du vill automatisera konsekvens kontroller och väljer **Nästa**.

   - Du kan köra konsekvens kontroller när replik data blir inkonsekventa eller enligt ett angivet schema.
   - Om du inte vill konfigurera automatiska konsekvens kontroller kan du köra en manuell kontroll genom att högerklicka på skydds gruppen **utför konsekvens kontroll**.

1. På sidan **Ange online skydds data** väljer du de virtuella datorer eller VM-mappar som du vill säkerhetskopiera och väljer sedan **Nästa**. 

   > [!TIP]
   > Du kan välja medlemmarna individuellt eller välja **alla** för att välja alla medlemmar.

   ![Ange skydds data online](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. På sidan **Ange schema för onlinesäkerhetskopiering** anger du hur ofta du vill säkerhetskopiera data från lokal lagring till Azure. 

   - Moln återställnings punkter för data som genereras enligt schemat. 
   - När återställnings punkten har genererats överförs den till Recovery Services valvet i Azure.

   ![Ange schemat för onlinesäkerhetskopiering](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. På sidan **Ange princip för kvarhållning av online** anger du hur länge du vill behålla återställnings punkterna som skapats från säkerhets kopieringarna till Azure.

   - Det finns ingen tids gräns för hur länge du kan lagra data i Azure.
   - Den enda begränsningen är att du inte kan ha mer än 9 999 återställnings punkter per skyddad instans. I det här exemplet är den skyddade instansen VMware-servern.

   ![Ange bevarande princip online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. På sidan **Sammanfattning** granskar du inställningarna och väljer sedan **Skapa grupp**.

   ![Skydds grupp medlem och inställnings Sammanfattning](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Övervaka med Azure Backup Server-konsolen

När du har konfigurerat skydds gruppen för säkerhets kopiering av virtuella datorer i Azure VMware-lösningen kan du övervaka säkerhets kopierings jobbets status och avisering med hjälp av Azure Backup Server-konsolen. Det här kan du övervaka.

- I **övervaknings** aktivitets fältet:
   - Under **aviseringar** kan du övervaka fel, varningar och allmän information.  Du kan visa aktiva och inaktiva aviseringar och konfigurera e-postaviseringar.
   - Under **jobb** kan du Visa jobb som startats av Azure Backup Server för en bestämd skyddad data källa eller skydds grupp. Du kan följa jobb förloppet eller kontrol lera resurser som förbrukas av jobb.
- I aktivitets avsnittet **skydd** kan du kontrol lera statusen för volymer och resurser i skydds gruppen. Du kan också kontrol lera konfigurations inställningar, till exempel återställnings inställningar, diskallokering och schema för säkerhets kopiering.
- I aktivitets avsnittet **hantering** kan du Visa flikarna **diskar, online** och **agenter** för att kontrol lera statusen för diskarna i lagringspoolen, registrera till Azure och distribuera statusen för DPM-agenten.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Övervaka status för säkerhets kopierings jobb i Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Återställa virtuella VMware-datorer

I Azure Backup Server Administratörskonsol finns det två sätt att hitta återställnings bara data. Du kan söka eller bläddra. När du återställer data kanske du inte vill återställa data eller en virtuell dator till samma plats. Därför stöder Azure Backup Server tre återställnings alternativ för säkerhets kopiering av virtuella VMware-datorer:

- **Återställning av ursprunglig plats (OLR)**: Använd OLR för att återställa en skyddad virtuell dator till dess ursprungliga plats. Du kan bara återställa en virtuell dator till dess ursprungliga plats om inga diskar har lagts till eller tagits bort sedan säkerhets kopieringen genomfördes. Om diskarna har lagts till eller tagits bort måste du använda alternativ plats återställning.
- **Alternativ plats återställning (återställning till)**: Använd när den ursprungliga virtuella datorn saknas eller om du inte vill störa den ursprungliga virtuella datorn. Ange platsen för en ESXi-värd, resurspool, mapp och lagrings plats och sökväg för lagring. För att hjälpa till att skilja den återställda virtuella datorn från den ursprungliga virtuella datorn lägger Azure Backup Server till *"-återställd"* till namnet på den virtuella datorn.
- **Individuell fil lokaliserings återställning (ILR)**: om den skyddade virtuella datorn är en virtuell Windows Server-dator kan enskilda filer eller mappar i den virtuella datorn återställas med hjälp av ILR-funktionen i Azure Backup Server. Om du vill återställa enskilda filer, se proceduren senare i den här artikeln. Att återställa en enskild fil från en virtuell dator är bara tillgängligt för virtuella Windows-datorer och disk återställnings punkter.

### <a name="restore-a-recovery-point"></a>Återställa en återställnings punkt

1. Välj vyn **återställning** i Azure Backup Server administratörskonsol. 

1. Använd fönstret **Bläddra** , bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visas tillgängliga återställnings punkter i rutan * * återställnings punkter för.

   ![Tillgängliga återställnings punkter](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. I fönstret **återställnings punkter för** väljer du ett datum när en återställnings punkt skulle utföras. Kalender datum i fetstil har tillgängliga återställnings punkter. Alternativt kan du högerklicka på den virtuella datorn och välja **Visa alla återställnings punkter** och sedan välja återställnings punkten i listan.

   > [!NOTE] 
   > För kortsiktigt skydd väljer du en diskbaserad återställnings punkt för snabbare återställning. När kortsiktiga återställnings punkter går ut ser du bara återställnings punkter **online** att återställa.

1. Innan du återställer från en onlineåterställningspunkt ser du till att mellanlagringsplatsen innehåller tillräckligt med ledigt utrymme för att kunna ta del av den fulla okomprimerade storleken på den virtuella dator som du vill återställa. Du kan visa eller ändra mellanlagringsplatsen genom att köra **guiden Konfigurera prenumerations inställningar**.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Azure Backup Server inställningar för återställnings mapp":::

1. Välj **Återställ** för att öppna **återställnings guiden**.

   ![Återställnings guiden, sidan Granska val av återställning](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Välj **Nästa** för att gå till skärmen **Ange återställnings alternativ** . Välj **Nästa** igen för att gå till skärmen **Välj återställnings typ** . 

   > [!NOTE]
   > VMware-arbetsbelastningar stöder inte aktivering av begränsning av nätverks bandbredd.

1. På sidan **Välj återställnings typ** återställer du antingen till den ursprungliga instansen eller till en ny plats.

   - Om du väljer **Återställ till ursprunglig instans** behöver du inte göra några fler val i guiden. Data för den ursprungliga instansen används.
   - Om du väljer **Återställ som virtuell dator på en värd** anger du informationen för **ESXi-värden**, **resurspool**, **mappar** och **sökväg** på skärmen **Ange mål** .

   ![Sidan Välj återställnings typ](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. På sidan **Sammanfattning** granskar du inställningarna och väljer **Återställ** för att starta återställnings processen. 

   Skärmen **återställnings status** visar förloppet för återställnings åtgärden.

### <a name="restore-an-individual-file-from-a-vm"></a>Återställa en enskild fil från en virtuell dator

Du kan återställa enskilda filer från en skyddad VM-återställnings punkt. Den här funktionen är endast tillgänglig för virtuella Windows Server-datorer. Att återställa enskilda filer liknar att återställa hela den virtuella datorn, förutom att du bläddrar till VMDK och letar upp de filer som du vill ha innan du påbörjar återställnings processen. 

> [!NOTE]
> Att återställa en enskild fil från en virtuell dator är bara tillgängligt för virtuella Windows-datorer och disk återställnings punkter.

1. Välj vyn **återställning** i Azure Backup Server administratörskonsol.

1. Använd fönstret **Bläddra** , bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visas tillgängliga återställnings punkter i rutan * * återställnings punkter för.

   ![Tillgängliga återställnings punkter](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. I rutan **återställnings punkter för** använder du kalendern för att välja det datum som innehåller de önskade återställnings punkterna. Beroende på hur säkerhets kopierings principen har kon figurer ATS kan datum ha fler än en återställnings punkt. 

1. När du har valt den dag då återställnings punkten togs, se till att du väljer rätt **återställnings tid**. 

   > [!NOTE]
   > Om det valda datumet har flera återställnings punkter väljer du återställnings punkten genom att välja den i list rutan **återställnings tid** . 

   När du har valt återställnings punkten visas listan över återställnings bara objekt i **Sök vägs** fönstret.

1. Du hittar de filer som du vill återställa genom att dubbelklicka på objektet i kolumnen **återställnings Bart objekt** i rutan **sökväg** för att öppna det. Välj sedan den fil eller de mappar som du vill återställa. Om du vill markera flera objekt väljer du **CTRL** -tangenten när du markerar varje objekt. Använd **Sök vägs** fönstret för att söka i listan över filer eller mappar som visas i kolumnen **återställnings Bart objekt** .
    
   > [!NOTE]
   > **Sök listan nedan** söker inte i undermappar. Om du vill söka i undermappar dubbelklickar du på mappen. Använd **upp** -knappen för att flytta från en underordnad mapp till den överordnade mappen. Du kan välja flera objekt (filer och mappar), men de måste finnas i samma överordnade mapp. Du kan inte återställa objekt från flera mappar i samma återställnings jobb.

   ![Granska val av återställning](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. När du har valt objekten för återställning går du till menyfliksområdet Administratörskonsol verktyg och väljer **Återställ** för att öppna **återställnings guiden**. I **återställnings guiden** visar sidan **Granska val av återställning** de valda objekten som ska återställas.

1. Gör något av följande på skärmen **Ange återställnings alternativ** :

   - Välj **ändra** om du vill aktivera begränsning av nätverks bandbredd. I dialog rutan **begränsa** väljer du **Aktivera begränsning av nätverks bandbredds användning** för att aktivera det. När du har aktiverat konfigurerar du **inställningarna** och **arbets schemat**.
   - Välj **Nästa** för att lämna nätverks begränsning inaktiverat.

1. På skärmen **Välj återställnings typ** väljer du **Nästa**. Du kan bara återställa dina filer eller mappar till en nätverksmapp.

1. På skärmen **Ange mål** väljer du **Bläddra** för att hitta en nätverks plats för dina filer eller mappar. Azure Backup Server skapar en mapp där alla återställda objekt kopieras. Namnet på mappen har prefixet MABS_day-månad-år. När du väljer en plats för de återställda filerna eller mappen tillhandahålls information om den platsen.

   ![Ange plats för att återställa filer](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. På skärmen **Ange återställnings alternativ** väljer du vilken säkerhets inställning som ska användas. Du kan välja att ändra nätverks bandbreddens användnings begränsning, men begränsningen är inaktive rad som standard. Dessutom aktive ras inte **San-återställning** och **meddelanden** .

1. På sidan **Sammanfattning** granskar du inställningarna och väljer **Återställ** för att starta återställnings processen. Skärmen **återställnings status** visar förloppet för återställnings åtgärden.

## <a name="next-steps"></a>Nästa steg

Nu när du har använt säkerhets kopieringen av dina virtuella datorer i Azure VMware-lösningen med Azure Backup Server kanske du vill lära dig mer om: 

- [Fel sökning när du konfigurerar säkerhets kopieringar i Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md).
- [Livs cykel hantering av virtuella datorer i Azure VMware-lösningen](lifecycle-management-of-azure-vmware-solution-vms.md).
