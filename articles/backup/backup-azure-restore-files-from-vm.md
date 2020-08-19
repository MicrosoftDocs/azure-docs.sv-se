---
title: Återställa filer och mappar från virtuell Azure-säkerhetskopiering
description: I den här artikeln lär du dig hur du återställer filer och mappar från en återställnings punkt för en virtuell Azure-dator.
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: 01235e116ca93f9c73e698e4d72ae0cb561824d5
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88262678"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Återställa filer från säkerhets kopiering av virtuella Azure-datorer

Azure Backup ger möjlighet att återställa [virtuella Azure-datorer (VM) och diskar](./backup-azure-arm-restore-vms.md) från virtuella Azure-säkerhetskopieringar, även kallade återställnings punkter. Den här artikeln förklarar hur du återställer filer och mappar från en virtuell Azure-säkerhetskopiering. Det går bara att återställa filer och mappar för virtuella Azure-datorer som distribueras med Resource Manager-modellen och skyddas till ett Recovery Services-valv.

> [!NOTE]
> Den här funktionen är tillgänglig för virtuella Azure-datorer som distribueras med Resource Manager-modellen och skyddas till ett Recovery Services-valv.
> Fil återställning från en krypterad VM-säkerhetskopiering stöds inte.
>

## <a name="mount-the-volume-and-copy-files"></a>Montera volymen och kopiera filer

Om du vill återställa filer eller mappar från återställnings punkten går du till den virtuella datorn och väljer önskad återställnings punkt.

1. Logga in på [Azure Portal](https://portal.Azure.com) och välj **virtuella datorer**i det vänstra fönstret. I listan över virtuella datorer väljer du den virtuella datorn för att öppna den virtuella datorns instrument panel.

2. I den virtuella datorns meny väljer du **säkerhets kopiering** för att öppna instrument panelen för säkerhets kopiering.

    ![Öppna Recovery Services valv säkerhets kopierings objekt](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. På instrument panelen för säkerhets kopierings menyn väljer du **fil återställning**.

    ![Välj fil återställning](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Menyn **fil återställning** öppnas.

    ![Fil återställnings meny](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. I list rutan **Välj återställnings punkt** väljer du den återställnings punkt som innehåller de filer som du vill använda. Som standard är den senaste återställnings punkten redan vald.

5. Om du vill ladda ned program varan som används för att kopiera filer från återställnings punkten väljer du **Hämta körbar fil** (för virtuella Windows Azure-datorer) eller **Ladda ned skript** (för virtuella Linux Azure-datorer genereras ett Python-skript).

    ![Ladda ned körbar fil](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure laddar ned den körbara filen eller skriptet till den lokala datorn.

    ![Hämta meddelandet för den körbara filen eller skriptet](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Om du vill köra den körbara filen eller skriptet som administratör rekommenderar vi att du sparar den nedladdade filen på din dator.

6. Den körbara filen eller skriptet är lösenordsskyddat och kräver ett lösen ord. I **fil återställnings** menyn väljer du knappen Kopiera för att läsa in lösen ordet i minnet.

    ![Genererat lösen ord](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Kontrol lera att [du har rätt dator](#selecting-the-right-machine-to-run-the-script) för att köra skriptet. Om rätt dator är samma dator som du laddade ned skriptet på, kan du fortsätta till nedladdnings avsnittet. Från nedladdnings platsen (vanligt vis mappen *hämtade filer* ) högerklickar du på den körbara filen eller skriptet och kör det med administratörs behörighet. När du uppmanas till det anger du lösen ordet eller klistrar in lösen ordet från minnet och trycker på **RETUR**. När du har angett ett giltigt lösen ord ansluter skriptet till återställnings punkten.

    ![Körbara utdata](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. För Linux-datorer genereras ett Python-skript. En måste ladda ned skriptet och kopiera det till den relevanta/kompatibla Linux-servern. Du kan behöva ändra behörigheterna för att köra den med ```chmod +x <python file name>``` . Kör sedan python-filen med ```./<python file name>``` .

Se avsnittet [åtkomst krav](#access-requirements) för att kontrol lera att skriptet körs.

### <a name="identifying-volumes"></a>Identifiera volymer

#### <a name="for-windows"></a>För Windows

När du kör den körbara filen monterar operativ systemet de nya volymerna och tilldelar enhets beteckningar. Du kan använda Utforskaren eller Utforskaren för att bläddra bland enheterna. De enhets beteckningar som tilldelats volymerna får inte ha samma bokstäver som den ursprungliga virtuella datorn. Volym namnet bevaras dock. Om volymen på den ursprungliga virtuella datorn exempelvis var "datadisk (E: `\` )" kan den volymen anslutas på den lokala datorn som "data disk (" valfri bokstav ": `\` ). Bläddra igenom alla volymer som anges i skriptets utdata tills du hittar dina filer eller mappar.  

   ![Fil återställnings meny](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>För Linux

I Linux monteras volymerna för återställnings punkten på den mapp där skriptet körs. De anslutna diskarna, volymerna och motsvarande monterings Sök vägar visas i enlighet med detta. Dessa monterings Sök vägar visas för användare som har åtkomst till rot nivå. Bläddra igenom de volymer som anges i utdata för skriptet.

  ![Fil återställnings meny för Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Stänger anslutningen

När du har identifierat filerna och kopierat dem till en lokal lagrings plats, ta bort (eller demontera) de ytterligare enheterna. Om du vill demontera enheterna går du till **fil återställnings** menyn i Azure Portal och väljer **demontera diskar**.

![Demontera diskar](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

När diskarna har demonterats visas ett meddelande. Det kan ta några minuter innan anslutningen har uppdaterats så att du kan ta bort diskarna.

I Linux tar operativ systemet inte bort motsvarande monterings Sök vägar automatiskt när anslutningen till återställnings punkten har brutits. Monterings Sök vägarna finns som "föräldralösa" volymer och är synliga, men genererar ett fel när du öppnar/skriver filerna. De kan tas bort manuellt. Skriptet, vid körning, identifierar eventuella sådana volymer som är befintliga från alla tidigare återställnings punkter och rensar dem vid medgivande.

> [!NOTE]
> Kontrol lera att anslutningen är stängd när de filer som krävs har återställts. Detta är viktigt, särskilt i scenariot där den dator där skriptet körs också har kon figurer ATS för säkerhets kopiering. Om anslutningen fortfarande är öppen kan den efterföljande säkerhets kopieringen Miss förväntas med fel meddelandet "UserErrorUnableToOpenMount". Detta beror på att de monterade enheterna/volymerna antas vara tillgängliga och när de kan komma åt dem kan Miss lyckas eftersom den underliggande lagringen, t. ex., iSCSI-målservern inte är tillgänglig. Om du rensar anslutningen tas enheterna/volymerna bort och de kommer därför inte att vara tillgängliga under säkerhets kopieringen.

## <a name="selecting-the-right-machine-to-run-the-script"></a>Välja rätt dator för att köra skriptet

Om skriptet har hämtats, är nästa steg att kontrol lera om den dator där du planerar att köra skriptet är rätt dator. Följande är de krav som måste uppfyllas på datorn.

### <a name="original-backed-up-machine-versus-another-machine"></a>Ursprunglig säkerhetskopierad dator jämfört med en annan dator

1. Om den säkerhetskopierade datorn är en stor disk med stor disk – det vill säga antalet diskar är större än 16 diskar eller om varje disk är större än 4 TB **måste skriptet köras på en annan dator** och [kraven](#file-recovery-from-virtual-machine-backups-having-large-disks) måste vara uppfyllda.
1. Även om den säkerhetskopierade datorn inte är en stor virtuell disk, kan skriptet i [dessa scenarier](#special-configurations) inte köras på samma säkerhetskopierade virtuella dator.

### <a name="os-requirements-on-the-machine"></a>OS-krav på datorn

Datorn där skriptet måste köras måste uppfylla [de här operativ system kraven](#system-requirements).

### <a name="access-requirements-for-the-machine"></a>Åtkomst krav för datorn

Den dator där skriptet måste köras måste uppfylla [dessa åtkomst krav](#access-requirements).

## <a name="special-configurations"></a>Särskilda konfigurationer

### <a name="dynamic-disks"></a>Dynamiska diskar

Om den skyddade virtuella Azure-datorn har volymer med en eller båda av följande egenskaper kan du inte köra det körbara skriptet på samma virtuella dator.

- Volymer som sträcker sig över flera diskar (utsträckta och stripade volymer)
- Feltoleranta volymer (speglade volymer och RAID-5-volymer) på dynamiska diskar

Kör i stället det körbara skriptet på en annan dator med ett kompatibelt operativ system.

### <a name="windows-storage-spaces"></a>Windows Storage Spaces

Windows Storage Spaces är en Windows-teknik som gör det möjligt att virtualisera lagring. Med Windows lagrings utrymmen kan du gruppera bransch standard diskar i lagringspooler. Sedan använder du det tillgängliga utrymmet i dessa lagringspooler för att skapa virtuella diskar, som kallas lagrings utrymmen.

Om den skyddade virtuella Azure-datorn använder Windows lagrings utrymmen kan du inte köra det körbara skriptet på samma virtuella dator. Kör i stället det körbara skriptet på en annan dator med ett kompatibelt operativ system.

### <a name="lvmraid-arrays"></a>LVM/RAID-matriser

I Linux används Logical Volume Manager (LVM) och/eller programvaru-RAID-matriser för att hantera logiska volymer över flera diskar. Om den skyddade virtuella Linux-datorn använder LVM-och/eller RAID-matriser kan du inte köra skriptet på samma virtuella dator. Kör i stället skriptet på en annan dator med ett kompatibelt operativ system och som stöder fil systemet på den skyddade virtuella datorn.

Följande skript utdata visar diskarna LVM och/eller RAID-matriser och volymerna med partitionstypen.

   ![Menyn för Linux LVM-utdata](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Kör kommandona i följande avsnitt om du vill ta med dessa partitioner online.

#### <a name="for-lvm-partitions"></a>För LVM-partitioner

När skriptet har körts monteras LVM-partitionerna i de fysiska volym (er)/disk som anges i utdata för skriptet. Processen är att

1. Hämta en unik lista över volym grupp namn från fysiska volymer eller diskar
2. Lista de logiska volymerna i dessa volym grupper
3. Montera sedan de logiska volymerna till en önskad sökväg.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Visar volym grupp namn från fysiska volymer

Så här visar du volym grupp namnen:

```bash
pvs -o +vguuid
```

Det här kommandot visar en lista över alla fysiska volymer (inklusive de som finns innan skriptet körs), deras motsvarande volym grupp namn och volym gruppens unika användar-ID (UUID). Ett exempel på utdata från kommandot visas nedan.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

Den första kolumnen (PV) visar den fysiska volymen, de efterföljande kolumnerna visar relevanta volym grupp namn, format, attribut, storlek, ledigt utrymme och det unika ID: t för volym gruppen. Kommandoutdata visar alla fysiska volymer. Se utdata för skriptet och identifiera de volymer som är relaterade till säkerhets kopian. I exemplet ovan skulle utdata i skriptet ha visat/dev/SDF och/dev/SDD. Det *datavg_db* volym gruppen tillhör skriptet och *Appvg_new* volym gruppen tillhör datorn. Den sista idén är att se till att ett unikt volym grupp namn ska ha ett unikt ID.

###### <a name="duplicate-volume-groups"></a>Duplicera volym grupper

Det finns scenarier där volym grupp namn kan ha 2 UUID: er när skriptet har körts. Det innebär att volym grupp namnen på datorn där skriptet körs och i den säkerhetskopierade virtuella datorn är desamma. Sedan måste vi byta namn på de säkerhetskopierade virtuella dator volym grupperna. Ta en titt på exemplet nedan.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

Skriptets utdata skulle ha visat/dev/SDG,/dev/SDH,/dev/sdm2 som bifogad. Det betyder att motsvarande VG-namn är Appvg_new och rootvg. Men samma namn finns också i datorns VG-lista. Vi kan kontrol lera att ett VG-namn har två UUID: er.

Nu måste vi byta namn på VG namn för skriptbaserade volymer, till exempel:/dev/SDG,/dev/SDH,/dev/sdm2. Om du vill byta namn på volym gruppen använder du följande kommando

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Nu har vi alla VG-namn med unika ID: n.

###### <a name="active-volume-groups"></a>Aktiva volym grupper

Se till att volym grupperna som motsvarar skriptets volymer är aktiva. Kommandot nedan används för att visa aktiva volym grupper. Kontrol lera om skriptets relaterade volym grupper finns i den här listan.

```bash
vgdisplay -a
```  

Annars aktiverar du volym gruppen genom att använda kommandot nedan.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>Lista logiska volymer i volym grupper

När vi hämtar den unika, aktiva listan över VGs som är relaterade till skriptet, kan de logiska volymer som finns i dessa volym grupper listas med kommandot nedan.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

Det här kommandot visar sökvägen för varje logisk volym som "LV-sökväg".

##### <a name="mounting-logical-volumes"></a>Montera logiska volymer

Montera de logiska volymerna till valfri sökväg:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> Använd inte "Mount-a". Det här kommandot monterar alla enheter som beskrivs i '/etc/fstab '. Detta kan betyda att dubbla enheter kan monteras. Data kan omdirigeras till enheter som skapats av skript, som inte bevarar data och kan därför leda till data förlust.

#### <a name="for-raid-arrays"></a>För RAID-matriser

Följande kommando visar information om alla RAID-diskar:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Den relevanta RAID-disken visas som `/dev/mdm/<RAID array name in the protected VM>`

Använd monterings kommandot om RAID-disken har fysiska volymer:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Om RAID-disken har en annan LVM som kon figurer ATS i den, använder du den föregående proceduren för LVM-partitioner men använder volym namnet i stället för RAID-diskens namn.

## <a name="system-requirements"></a>Systemkrav

### <a name="for-windows-os"></a>För Windows OS

I följande tabell visas kompatibiliteten mellan server-och dator operativ system. När du återställer filer kan du inte återställa filer till en tidigare eller framtida version av operativ systemet. Du kan till exempel inte återställa en fil från en virtuell Windows Server 2016-dator till Windows Server 2012 eller en dator med Windows 8. Du kan återställa filer från en virtuell dator till samma serveroperativ system eller till kompatibelt klient operativ system.

|Server-OS | Kompatibelt klient operativ system  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8,1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>För Linux OS

I Linux måste operativ systemet på datorn som används för att återställa filer ha stöd för fil systemet på den skyddade virtuella datorn. När du väljer en dator för att köra skriptet, se till att datorn har ett kompatibelt operativ system och använder en av de versioner som anges i följande tabell:

|Linux-operativsystem | Versioner  |
| --------------- | ---- |
| Ubuntu | 12,04 och uppåt |
| CentOS | 6,5 och uppåt  |
| RHEL | 6,7 och uppåt |
| Debian | 7 och senare |
| Oracle Linux | 6,4 och uppåt |
| SLES | 12 och över |
| openSUSE | 42,2 och uppåt |

> [!NOTE]
> Vi har hittat några problem med att köra fil återställnings skriptet på datorer med SLES 12 SP4 OS och vi undersöker med SLES-teamet.
> För närvarande fungerar det att köra fil återställnings skriptet på datorer med SLES 12 SP2 och SP3 OS-versioner.
>

Skriptet kräver också python-och bash-komponenter för att kunna köra och ansluta säkert till återställnings punkten.

|Komponent | Version  |
| --------------- | ---- |
| bash | 4 och senare |
| python | 2.6.6 och senare  |
| TLS | 1,2 bör stödjas  |

## <a name="access-requirements"></a>Åtkomstkrav

Om du kör skriptet på en dator med begränsad åtkomst kontrollerar du att det finns åtkomst till:

- `download.microsoft.com`
- URL: er för återställnings tjänsten (geo-Name refererar till den region där Recovery Service-valvet finns)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` (För offentliga Azure-regioner)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn` (För Azure Kina-21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us` (För Azure amerikanska myndigheter)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de` (För Azure Germany)
- Utgående portar 53 (DNS), 443, 3260

> [!NOTE]
>
> - Det nedladdade skript fil namnet kommer att ha **geo-namnet** ifyllt i URL: en. Exempel: det nedladdade skript namnet börjar med \' VMName \' \_ \' \' -namnet _ \' GUID \' , som *ContosoVM_wcus_12345678*
> - URL: en skulle vara <https://pod01-rec2.wcus.backup.windowsazure.com> "
>

För Linux kräver skriptet "Open-iSCSI"-och ' lshw '-komponenter för att ansluta till återställnings punkten. Om komponenterna inte finns på den dator där skriptet körs, ställer skriptet efter behörighet att installera komponenterna. Ange medgivande för att installera de nödvändiga komponenterna.

Åtkomst till `download.microsoft.com` krävs för att ladda ned komponenter som används för att skapa en säker kanal mellan den dator där skriptet körs och data i återställnings punkten.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Fil återställning från säkerhets kopior av virtuella datorer med stora diskar

I det här avsnittet beskrivs hur du utför fil återställning från säkerhets kopior av virtuella Azure-datorer med fler än 16 diskar eller disk storleken är större än 4 TB.

Eftersom fil återställnings processen kopplar alla diskar från säkerhets kopian, när ett stort antal diskar (>16) eller stora diskar (> 4 TB varje) används, rekommenderas följande åtgärds punkter:

- Behåll en separat Restore Server (virtuella Azure VM D2v3-datorer) för fil återställning. Du kan använda det bara för fil återställning och sedan stänga ned det när det inte behövs. Det rekommenderas inte att återställas på den ursprungliga datorn eftersom den har betydande påverkan på den virtuella datorn.
- Kör skriptet en gång för att kontrol lera om fil återställningen lyckas.
- Om fil återställnings processen låser sig (diskarna aldrig monteras eller om de är monterade men volymerna inte visas) utför du följande steg.
  - Om återställnings servern är en virtuell Windows-dator:
    - Se till att operativ systemet är WS 2012 eller högre.
    - Se till att register nycklarna är inställda enligt rekommendationerna nedan i återställnings servern och var noga med att starta om servern. Antalet bredvid GUID kan vara mellan 0001-0005. I följande exempel är det 0004. Navigera genom sökvägen till register nyckeln tills avsnittet parametrar.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Om återställnings servern är en virtuell Linux-dator:
  - I filen/etc/iSCSI/iscsid.conf ändrar du inställningen från:
    - Node. ansluts [0]. tidsintervallet. noop_out_timeout = 5 till Node. ansluten [0]. tidsintervallet. noop_out_timeout = 30
- När du har gjort ändringen ovan kör du skriptet igen. Med de här ändringarna är det mycket troligt att fil återställningen kommer att lyckas.
- Varje gång användaren laddar ned ett skript initierar Azure Backup processen för att förbereda återställnings punkten för hämtning. Med stora diskar tar det lång tid att utföra den här processen. Om det finns successiva burst-anrop, kommer mål förberedelsen att ingå i en nedladdnings spiral. Därför rekommenderar vi att du laddar ned ett skript från portalen/PowerShell/CLI, väntar i 20-30 minuter (en tumregel) och kör det. Vid den här tidpunkten förväntas målet vara klart för anslutning från skript.
- Efter fil återställning kontrollerar du att du går tillbaka till portalen och väljer **demontera diskar** för återställnings punkter där du inte kunde montera volymerna. I stort sett rensar det här steget eventuella befintliga processer/sessioner och ökar chansen att återställa.

## <a name="troubleshooting"></a>Felsökning

Om du har problem när du återställer filer från de virtuella datorerna kan du läsa följande tabell för ytterligare information.

| Fel meddelande/scenario | Trolig orsak | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| Exe-utdata: *undantag inträffade vid anslutning till målet* | Skriptet kan inte komma åt återställnings punkten    | Kontrol lera om datorn uppfyller de [tidigare åtkomst kraven](#access-requirements). |  
| Exe-utdata: *målet har redan loggats in via en iSCSI-session.* | Skriptet har redan körts på samma dator och enheterna har anslutits | Volymerna för återställnings punkten har redan bifogats. De kan inte monteras med samma enhets beteckning på den ursprungliga virtuella datorn. Bläddra igenom alla tillgängliga volymer i Utforskaren för filen. |
| Exe-utdata: *det här skriptet är ogiltigt eftersom diskarna har demonterats via portalen/överskridit gränsen på 12 timmar. Ladda ned ett nytt skript från portalen.* |    Diskarna har demonterats från portalen eller så överskreds gränsen på 12 timmar | Denna specifika exe är nu ogiltig och kan inte köras. Om du vill komma åt filerna för återställnings punkten-in-Time går du till portalen för en ny exe.|
| På den dator där exe körs: de nya volymerna demonteras inte när du klickar på knappen demontera | ISCSI-initieraren på datorn svarar inte/uppdaterar anslutningen till målet och upprätthåller cachen. |  När du har klickat på **demontera**väntar du några minuter. Om de nya volymerna inte är demonterade kan du bläddra igenom alla volymer. Om du bläddrar bland alla volymer så tvingas initieraren att uppdatera anslutningen och volymen demonteras med ett fel meddelande om att disken inte är tillgänglig.|
| Exe-utdata: skriptet körs korrekt men "nya volymer anslutna" visas inte i skriptets utdata |    Detta är ett tillfälligt fel    | Volymerna har redan bifogats. Öppna Utforskaren för att bläddra. Om du använder samma dator för att köra skript varje gång, bör du överväga att starta om datorn och listan bör visas i efterföljande exe-körningar. |
| Linux-information: det går inte att Visa önskade volymer | Operativ systemet på datorn där skriptet körs kanske inte känner igen det underliggande fil systemet för den skyddade virtuella datorn | Kontrol lera om återställnings punkten är krasch-konsekvent eller fil konsekvent. Om filen är konsekvent kör du skriptet på en annan dator vars operativ system identifierar den skyddade virtuella datorns fil system. |
| Windows-information: det går inte att Visa önskade volymer | Diskarna kan ha anslutits men volymerna har inte kon figurer ATS | På skärmen disk hantering identifierar du ytterligare diskar som är relaterade till återställnings punkten. Om någon av dessa diskar är i offline-tillstånd kan du försöka att ta dem online genom att högerklicka på disken och välja **online**.|

## <a name="security"></a>Säkerhet

I det här avsnittet beskrivs de olika säkerhets åtgärder som vidtagits för att genomföra fil återställning från virtuella Azure-säkerhetskopieringar.

### <a name="feature-flow"></a>Funktions flöde

Den här funktionen har skapats för att komma åt VM-data utan att behöva återställa hela VM-eller VM-diskar och med det minsta antalet steg. Åtkomst till VM-data tillhandahålls av ett skript (som monterar återställnings volymen när den körs enligt nedan) och den utgör hörnen för alla säkerhets implementeringar:

  ![Säkerhets funktions flöde](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Säkerhets implementeringar

#### <a name="select-recovery-point-who-can-generate-script"></a>Välj återställnings punkt (som kan generera skript)

Skriptet ger åtkomst till VM-data, så det är viktigt att reglera vem som kan generera det på den första platsen. Du måste logga in i Azure Portal och vara RBAC- [auktoriserade](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) för att generera skriptet.

Fil återställning måste ha samma behörighets nivå som krävs för återställning av virtuella datorer och diskar. Med andra ord kan endast behöriga användare se de virtuella dator data som kan generera skriptet.

Det genererade skriptet är signerat med det officiella Microsoft-certifikatet för tjänsten Azure Backup. Manipulering av skriptet innebär att signaturen är bruten och att försök att köra skriptet är markerat som en potentiell risk av operativ systemet.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montera återställnings volym (som kan köra skript)

Endast en administratör kan köra skriptet och det ska köras i förhöjd läge. Skriptet kör bara en i förväg genererad uppsättning steg och accepterar inte inmatade från externa källor.

För att köra skriptet krävs ett lösen ord som bara visas för den auktoriserade användaren vid tidpunkten för generering av skript i Azure Portal eller PowerShell/CLI. Detta är att se till att den auktoriserade användare som laddar ned skriptet också är ansvarig för att köra skriptet.

#### <a name="browse-files-and-folders"></a>Bläddra bland filer och mappar

För att bläddra bland filer och mappar använder skriptet iSCSI-initieraren på datorn och ansluter till den återställnings punkt som är konfigurerad som ett iSCSI-mål. Här kan du tänka på scenarier där en försöker imitera/manipulera antingen/alla komponenter.

Vi använder en metod för ömsesidig CHAP-autentisering så att varje komponent autentiserar den andra. Det innebär att det är mycket svårt för en falsk initierare att ansluta till iSCSI-målet och att ett förfalskat mål måste vara anslutet till den dator där skriptet körs.

Data flödet mellan återställnings tjänsten och datorn skyddas genom att skapa en säker TLS-tunnel över TCP ([TLS 1,2 bör stödjas](#system-requirements) på den dator där skriptet körs).

Alla fil Access Control List (ACL) som finns i den överordnade/säkerhetskopierade virtuella datorn bevaras även i det monterade fil systemet.

Skriptet ger skrivskyddad åtkomst till en återställnings punkt och är bara giltig i 12 timmar. Om du vill ta bort åtkomsten tidigare loggar du in på Azure Portal/PowerShell/CLI och utför **demontera diskar** för den specifika återställnings punkten. Skriptet kommer att ogiltig förklaras.

## <a name="next-steps"></a>Nästa steg

- Läs [fel söknings](#troubleshooting) avsnittet om du har problem med att återställa filer
- Lär dig hur du [återställer filer via PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- Lär dig hur du [återställer filer via Azure CLI](./tutorial-restore-files.md)
- När den virtuella datorn har återställts kan du läsa mer om hur du [hanterar säkerhets kopior](./backup-azure-manage-vms.md)
