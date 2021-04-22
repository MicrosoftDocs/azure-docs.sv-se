---
title: Back up Azure VMware Solution VMs with Azure Backup Server
description: Konfigurera din Azure VMware Solution för att konfigurerar virtuella datorer med hjälp av Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 92affbf883215f0d051115fb64e9e7bf7a1430b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871785"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Back up Azure VMware Solution VMs with Azure Backup Server

I den här artikeln kommer vi att äldsta till att eftersom virtuella VMware-datorer (VM) körs på Azure VMware Solution med Azure Backup Server. Börja med att gå igenom [Konfigurera Microsoft Azure Backup Server för Azure VMware Solution](set-up-backup-server-for-azure-vmware-solution.md).

Sedan går vi igenom alla nödvändiga procedurer för att:

> [!div class="checklist"] 
> * Konfigurera en säker kanal så att Azure Backup Server kan kommunicera med VMware-servrar via HTTPS. 
> * Lägg till autentiseringsuppgifterna för kontot i Azure Backup Server. 
> * Lägg till vCenter i Azure Backup Server. 
> * Konfigurera en skyddsgrupp som innehåller de virtuella VMware-datorer som du vill säkerhetskopiera, ange inställningar för säkerhetskopiering och schemalägg säkerhetskopieringen.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter-servern

Som standard kommunicerar Azure Backup Server med VMware-servrar via HTTPS. Om du vill konfigurera HTTPS-anslutningen laddar du ned VMware-certifikatutfärdarcertifikatet (CA) och importerar det på Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurera certifikatet

1. I webbläsaren på den virtuella Azure Backup Server anger du url:en för vSphere-webbklienten.

   > [!NOTE] 
   > Om sidan VMware **Komma igång** visas kontrollerar du proxyinställningarna för anslutningen och webbläsaren och försöker igen.

1. På sidan VMware **Komma igång** du Hämta **betrodda rotcertifikatutfärdarcertifikat.**

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere-webbklient":::

1. Spara filen **download.zip** på Azure Backup Server datorn och extrahera innehållet till mappen **certs,** som innehåller:

   - Rotcertifikatfil med ett tillägg som börjar med en numrerad sekvens som .0 och .1.
   - CRL-fil med ett tillägg som börjar med en sekvens som .r0 eller .r1.

1. I mappen **certs** högerklickar du på rotcertifikatfilen och väljer **Byt** namn för att ändra tillägget till **.crt**.

   Filikonen ändras till en som representerar ett rotcertifikat.

1. Högerklicka på rotcertifikatet och välj **Installera certifikat.**

1. I guiden **Importera certifikat** väljer du **Lokal dator** som mål för certifikatet och väljer **Nästa.**

   ![Välkomstsida för guiden](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Bekräfta att du vill tillåta ändringar på datorn om du tillfrågas.

1. Välj **Placera alla certifikat i följande arkiv och** välj Bläddra **för** att välja certifikatarkivet.

   ![Certifikatlagring](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Välj **betrodda rotcertifikatutfärdare** som målmapp och välj **OK.**

1. Granska inställningarna och välj Slutför **för att** börja importera certifikatet.

   ![Kontrollera att certifikatet finns i rätt mapp](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. När certifikatimporten har bekräftats loggar du in på vCenter-servern för att bekräfta att anslutningen är säker.

### <a name="enable-tls-12-on-azure-backup-server"></a>Aktivera TLS 1.2 på Azure Backup Server

VMware 6.7 och framåt hade TLS aktiverat som kommunikationsprotokoll. 

1. Kopiera följande registerinställningar och klistra in dem i Anteckningar. Spara sedan filen som TLS. REG utan filnamnstillägget .txt.

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

1. Högerklicka på TLS. REG-fil och välj **Sammanfoga** eller **Öppna** för att lägga till inställningarna i registret.


## <a name="add-the-account-on-azure-backup-server"></a>Lägg till kontot på Azure Backup Server

1. Öppna Azure Backup Server och välj Hanteringsproduktionsservrar Hantera   >    >  **VMware** i Azure Backup Server-konsolen.

   ![Azure Backup Server konsolen](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. I dialogrutan **Hantera autentiseringsuppgifter** väljer du Lägg **till**.

   ![I dialogrutan Hantera autentiseringsuppgifter väljer du Lägg till.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. I dialogrutan **Lägg till autentiseringsuppgifter** anger du ett namn och en beskrivning för den nya autentiseringsbeskrivningen. Ange det användarnamn och lösenord som du definierade på VMware-servern.

   > [!NOTE] 
   > Om VMware-servern och Azure Backup Server inte finns i samma domän anger du domänen i **rutan** Användarnamn.

   ![Azure Backup Server dialogrutan Lägg till autentiseringsuppgifter](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Välj **Lägg till** för att lägga till nya autentiseringsuppgifter.

   ![Skärmbild som visar dialogrutan Azure Backup Server Hantera autentiseringsuppgifter med nya autentiseringsuppgifter som visas.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Lägg till vCenter-servern i Azure Backup Server

1. I Azure Backup Server väljer du **Hanteringsproduktionsservrar**  >  **Lägg**  >  **till**.

   ![Guiden För att öppna produktionsserverns tillägg](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Välj **VMware-servrar** och välj **Nästa.**

   ![Guiden För tillägg av produktionsserver](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Ange IP-adressen för vCenter.

   ![Ange VMware-server](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. I **rutan SSL-port** anger du den port som används för att kommunicera med vCenter.

   > [!TIP] 
   > Port 443 är standardporten, men du kan ändra den om din vCenter lyssnar på en annan port.

1. I rutan **Ange autentiseringsuppgifter** väljer du de autentiseringsuppgifter som du skapade i föregående avsnitt.

1. Välj **Lägg** till för att lägga till vCenter i serverlistan och välj **Nästa.**

   ![Lägg till VMware-server och autentiseringsuppgifter](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. På sidan **Sammanfattning** väljer du Lägg **till för** att lägga till vCenter i Azure Backup Server.

   Den nya servern läggs till omedelbart. vCenter behöver ingen agent.

   ![Lägga till VMware-server i Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Granska **inställningarna** på sidan Slutför och välj sedan **Stäng.**

   ![Sidan Slutför](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Du ser vCenter-servern under **Produktionsserver** med:
   - Skriv som **VMware Server** 
   - Agentstatus som **OK** 
   
      Om du ser **Agentstatus** som **Okänd** väljer du **Uppdatera**.

## <a name="configure-a-protection-group"></a>Konfigurera en skyddsgrupp

Skyddsgrupper samlar in flera virtuella datorer och tillämpar samma inställningar för datalagring och säkerhetskopiering på alla virtuella datorer i gruppen.

1. I Azure Backup Server väljer du **Skydd**  >  **Ny.**

   ![Öppna guiden Skapa ny skyddsgrupp](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. På **välkomstsidan i guiden Skapa ny** skyddsgrupp väljer du **Nästa.**

   ![Dialogrutan Skapa ny skyddsgrupp](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. På sidan **Välj typ av skyddsgrupp** väljer **du Servrar** och sedan **Nästa.** Sidan **Välj gruppmedlemmar** visas.

1. På sidan **Välj gruppmedlemmar** väljer du de virtuella datorer (eller VM-mappar) som du vill bladet och väljer sedan **Nästa.**

   > [!NOTE]
   > När du väljer en mapp eller virtuella datorer väljs även mappar i mappen för säkerhetskopiering. Du kan avmarkera mappar eller virtuella datorer som du inte vill bladet. Om en virtuell dator eller mapp redan säkerhetskopieras kan du inte välja den, vilket säkerställer att duplicerade återställningspunkter inte skapas för en virtuell dator.

   ![Välj gruppmedlemmar](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. På sidan **Välj dataskyddsmetod** anger du ett namn för skyddsgruppen och skyddsinställningarna. 

1. Ange det kortsiktiga skyddet till **Disk**, aktivera onlineskydd och välj sedan **Nästa.**

   ![Välj dataskyddsmetod](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Ange hur länge du vill att data ska säkerhetskopieras till disk.

   - **Kvarhållningsintervall:** Antalet dagar som diskåterställningspunkter behålls.
   - **Fullständig snabbsäkerhetskopiering:** Hur ofta diskåterställningspunkter tas. Om du vill ändra tidpunkter eller datum när kortsiktig säkerhetskopiering sker väljer du **Ändra**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Ange dina kortsiktiga mål för diskbaserat skydd":::

1. På sidan **Granska Disklagring granska** diskutrymmet som angetts för vm-säkerhetskopiorna.

   - De rekommenderade diskallokeringarna baseras på det kvarhållningsintervall som du har angett, typen av arbetsbelastning och storleken på skyddade data. Gör de ändringar som krävs och välj **sedan Nästa.**
   - **Datastorlek:** Storleken på data i skyddsgruppen.
   - **Diskutrymme:** Rekommenderad mängd diskutrymme för skyddsgruppen. Om du vill ändra den här inställningen väljer du utrymme som är lätt större än den mängd som du uppskattar att varje datakälla växer.
   - **Information om lagringspool:** Visar status för lagringspoolen, vilket inkluderar total och återstående diskstorlek.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Granska diskutrymmet i lagringspoolen":::

   > [!NOTE]
   > I vissa scenarier är datastorleken som rapporteras högre än den faktiska VM-storleken. Vi är medvetna om problemet och undersöker det för närvarande.

1. På sidan **Välj metod för skapande av** replik anger du hur du vill göra den första säkerhetskopieringen och väljer **Nästa.**

   - Standardvärdet är **Automatiskt över nätverket** och **Nu**. Om du använder standardinställningen anger du en tid med låg belastning. Om du väljer **Senare** anger du en dag och en tidpunkt.
   - För stora mängder data eller mindre optimala nätverksförhållanden bör du överväga att replikera data offline med hjälp av flyttbara medier.

   ![Välj metod för att skapa repliker](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. För **Alternativ för konsekvenskontroll** väljer du hur och när konsekvenskontrollerna ska automatiseras och väljer **Nästa.**

   - Du kan köra konsekvenskontroller när replikdata blir inkonsekventa eller enligt ett fast schema.
   - Om du inte vill konfigurera automatiska konsekvenskontroller kan du köra en manuell kontroll genom att högerklicka på skyddsgruppen **Utför konsekvenskontroll.**

1. På sidan **Ange onlineskyddsdata** väljer du de virtuella datorer eller VM-mappar som du vill bladet och väljer sedan **Nästa.** 

   > [!TIP]
   > Du kan välja medlemmar individuellt eller välja **Välj alla för** att välja alla medlemmar.

   ![Ange data för onlineskydd](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. På sidan Ange schema för onlinesäkerhetskopiering anger du hur ofta du vill säkerhetskopiera data från lokal lagring till Azure.  

   - Molnåterställningspunkter för att data ska genereras enligt schemat. 
   - När återställningspunkten har genererats överförs den till Recovery Services-valvet i Azure.

   ![Ange schema för onlinesäkerhetskopiering](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. På sidan **Ange princip för onlinebevarande** anger du hur länge du vill behålla återställningspunkterna som skapats från säkerhetskopiorna till Azure.

   - Det finns ingen tidsgräns för hur länge du kan behålla data i Azure.
   - Den enda gränsen är att du inte kan ha fler än 9 999 återställningspunkter per skyddad instans. I det här exemplet är den skyddade instansen VMware-servern.

   ![Ange princip för onlinebevarande](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Granska **inställningarna på** sidan Sammanfattning och välj sedan **Skapa grupp.**

   ![Sammanfattning av skyddsgruppsmedlem och inställning](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Övervaka med Azure Backup Server konsolen

När du har konfigurerat skyddsgruppen för att säkerhetskopiera Azure VMware Solution virtuella datorer kan du övervaka status för säkerhetskopieringsjobbet och aviseringen med hjälp av Azure Backup Server konsolen. Det här är vad du kan övervaka.

- I **aktivitetsområdet** Övervakning:
   - Under **Aviseringar** kan du övervaka fel, varningar och allmän information.  Du kan visa aktiva och inaktiva aviseringar och konfigurera e-postmeddelanden.
   - Under **Jobb** kan du visa jobb som startats av Azure Backup Server för en specifik skyddad datakälla eller skyddsgrupp. Du kan följa jobbförloppet eller kontrollera resurser som förbrukas av jobb.
- I **aktivitetsområdet** Skydd kan du kontrollera status för volymer och resurser i skyddsgruppen. Du kan också kontrollera konfigurationsinställningar som återställningsinställningar, diskallokering och schema för säkerhetskopiering.
- I **aktivitetsområdet** Hantering kan du visa flikarna  **Diskar, Online** och Agenter för att kontrollera status för diskar i lagringspoolen, registrering till Azure och distribuerad DPM-agentstatus.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Övervaka status för säkerhetskopieringsjobb i Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Återställa virtuella VMware-datorer

I Azure Backup Server Administratörskonsol finns det två sätt att hitta återställningsbara data. Du kan söka eller bläddra. När du återställer data kanske du inte vill återställa data eller en virtuell dator till samma plats. Därför stöder Azure Backup Server återställningsalternativ för säkerhetskopiering av virtuella VMware-datorer:

- **Återställning av ursprunglig plats (OLR): Använd** OLR för att återställa en skyddad virtuell dator till dess ursprungliga plats. Du kan bara återställa en virtuell dator till dess ursprungliga plats om inga diskar har lagts till eller tagits bort sedan säkerhetskopieringen gjordes. Om diskar har lagts till eller tagits bort måste du använda alternativ platsåterställning.
- **Återställning av alternativ plats (ALR):** Använd när den ursprungliga virtuella datorn saknas eller om du inte vill störa den ursprungliga virtuella datorn. Ange platsen för en ESXi-värd, resurspool, mapp och lagringsdatalager och sökväg. För att skilja den återställda virtuella datorn från den ursprungliga virtuella Azure Backup Server lägger *till "-Återställd"* i namnet på den virtuella datorn.
- **Återställning av enskild filplats (ILR):** Om den skyddade virtuella datorn är en virtuell Windows Server-dator kan enskilda filer eller mappar i den virtuella datorn återställas med hjälp av ilr-funktionen i Azure Backup Server. Information om hur du återställer enskilda filer finns i proceduren senare i den här artikeln. Återställning av en enskild fil från en virtuell dator är endast tillgängligt för virtuella Windows-datorer och diskåterställningspunkter.

### <a name="restore-a-recovery-point"></a>Återställa en återställningspunkt

1. I Azure Backup Server Administratörskonsol väljer du **vyn** Återställning. 

1. Använd **bläddringsfönstret** och bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visar fönstret **Återställningspunkter för de tillgängliga återställningspunkterna.

   ![Tillgängliga återställningspunkter](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. I fönstret **Återställningspunkter** för väljer du ett datum då en återställningspunkt togs. Kalenderdatum i fetstil har tillgängliga återställningspunkter. Alternativt kan du högerklicka på den virtuella datorn och välja **Visa alla återställningspunkter** och sedan välja återställningspunkten i listan.

   > [!NOTE] 
   > För kortsiktigt skydd väljer du en diskbaserad återställningspunkt för snabbare återställning. När de kortsiktiga återställningspunkterna har upphört att gälla visas endast **onlineåterställningspunkter** som ska återställas.

1. Innan du återställer från en onlineåterställningspunkt måste du se till att mellanlagringsplatsen innehåller tillräckligt med ledigt utrymme för den fullständiga okomprimerade storleken på den virtuella dator som du vill återställa. Mellanlagringsplatsen kan visas eller ändras genom att köra **guiden Konfigurera prenumerationsinställningar.**

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Azure Backup Server för återställningsmapp":::

1. Välj **Återställ** för att öppna **återställningsguiden.**

   ![Återställningsguiden, sidan Granska val av återställning](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Välj **Nästa** för att gå till **skärmen Ange återställningsalternativ.** Välj **Nästa** igen för att gå till **skärmen Välj återställningstyp.** 

   > [!NOTE]
   > VMware-arbetsbelastningar stöder inte nätverksbandbreddsbegränsning.

1. På sidan **Välj återställningstyp** återställer du antingen till den ursprungliga instansen eller en ny plats.

   - Om du **väljer Återställ till ursprunglig** instans behöver du inte göra fler val i guiden. Data för den ursprungliga instansen används.
   - Om du väljer Återställ **som** virtuell dator  på en värd anger du informationen för **ESXi-värden,**  **resurspoolen,** mappen och sökvägen på skärmen Ange **mål.**

   ![Sidan Välj återställningstyp](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. På sidan **Sammanfattning** granskar du inställningarna och väljer Återställ **för** att starta återställningsprocessen. 

   Skärmen **Återställningsstatus** visar återställningsåtgärdens förlopp.

### <a name="restore-an-individual-file-from-a-vm"></a>Återställa en enskild fil från en virtuell dator

Du kan återställa enskilda filer från en skyddad återställningspunkt för virtuella datorer. Den här funktionen är endast tillgänglig för virtuella Windows Server-datorer. Att återställa enskilda filer liknar att återställa hela den virtuella datorn, förutom att du bläddrar till VMDK och hittar de filer du vill ha innan du startar återställningsprocessen. 

> [!NOTE]
> Återställning av en enskild fil från en virtuell dator är endast tillgängligt för virtuella Windows-datorer och diskåterställningspunkter.

1. I Azure Backup Server Administratörskonsol du **vyn** Återställning.

1. Använd fönstret **Bläddra** och bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visar fönstret **Återställningspunkter för de tillgängliga återställningspunkterna.

   ![Tillgängliga återställningspunkter](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. I fönstret **Återställningspunkter för** använder du kalendern för att välja det datum som innehåller de önskade återställningspunkterna. Datumen kan ha fler än en återställningspunkt beroende på hur säkerhetskopieringspolicyn har konfigurerats. 

1. När du har valt den dag då återställningspunkten togs kontrollerar du att du har valt rätt **återställningstid.** 

   > [!NOTE]
   > Om det valda datumet har flera återställningspunkter väljer du återställningspunkten genom att välja den **i** listrutan Återställningstid. 

   När du har valt återställningspunkt visas listan över återställningsbara objekt i **fönstret** Sökväg.

1. Du hittar de filer som du  vill återställa genom att dubbelklicka på objektet i kolumnen **Återställningsbart** objekt i fönstret Sökväg för att öppna det. Välj sedan den eller de mappar som du vill återställa. Om du vill markera  flera objekt markerar du Ctrl-tangenten medan du markerar varje objekt. Använd fönstret **Sökväg** för att söka i listan över filer eller mappar som visas i **kolumnen Återställningsbart** objekt.
    
   > [!NOTE]
   > **Söklistan** nedan söker inte i undermappar. Om du vill söka igenom undermappar dubbelklickar du på mappen . Använd knappen **Upp** för att flytta från en underordnad mapp till den överordnade mappen. Du kan välja flera objekt (filer och mappar), men de måste finnas i samma överordnade mapp. Du kan inte återställa objekt från flera mappar i samma återställningsjobb.

   ![Granska val av återställning](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. När du har valt objekt för återställning går du till Administratörskonsol och väljer Återställ **för** att öppna **återställningsguiden.** I **återställningsguiden** visar skärmen **Granska återställningsval** de markerade objekt som ska återställas.

1. På skärmen **Ange återställningsalternativ** gör du något av följande:

   - Välj **Ändra** för att aktivera begränsning av nätverksbandbredd. I dialogrutan **Begränsning** väljer du Aktivera **begränsning av nätverksbandbredd för** att aktivera det. När det är aktiverat konfigurerar **du Inställningar** och **Arbetsschema.**
   - Välj **Nästa för** att lämna nätverksbegränsning inaktiverat.

1. På skärmen **Välj återställningstyp** väljer du **Nästa.** Du kan bara återställa dina filer eller mappar till en nätverksmapp.

1. På skärmen **Ange mål** väljer du **Bläddra för** att hitta en nätverksplats för dina filer eller mappar. Azure Backup Server skapar en mapp där alla återställda objekt kopieras. Mappnamnet har prefixet MABS_day-month-year. När du väljer en plats för de återställda filerna eller mappen anges informationen för den platsen.

   ![Ange plats för att återställa filer](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. På skärmen **Ange återställningsalternativ** väljer du vilken säkerhetsinställning som ska användas. Du kan välja att ändra begränsningen för nätverksbandbredden, men begränsningen är inaktiverad som standard. **San-återställning och** **-meddelande** är inte heller aktiverade.

1. På skärmen **Sammanfattning** granskar du inställningarna och väljer Återställ **för** att starta återställningsprocessen. Skärmen **Återställningsstatus** visar förloppet för återställningsåtgärden.

## <a name="next-steps"></a>Nästa steg

Nu när du har gått in på hur du Azure VMware Solution virtuella datorer med Azure Backup Server kanske du vill lära dig mer om: 

- [Felsökning när du säkerhetskopiera i Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
- [Livscykelhantering för virtuella Azure VMware Solution datorer](lifecycle-management-of-azure-vmware-solution-vms.md)
