---
title: Återställa filer och mappar från virtuell Azure-säkerhetskopiering
description: I den här artikeln lär du dig hur du återställer filer och mappar från en återställnings punkt för en virtuell Azure-dator.
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: references_regions
ms.openlocfilehash: c5a027773a55347b71c1e6a66d24d7fdb99220d0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725517"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Återställa filer från en säkerhetskopia av en virtuell Azure-dator

Azure Backup ger möjlighet att återställa [virtuella Azure-datorer (VM) och diskar](./backup-azure-arm-restore-vms.md) från virtuella Azure-säkerhetskopieringar, även kallade återställnings punkter. Den här artikeln förklarar hur du återställer filer och mappar från en virtuell Azure-säkerhetskopiering. Det går bara att återställa filer och mappar för virtuella Azure-datorer som distribueras med Resource Manager-modellen och skyddas till ett Recovery Services-valv.


> [!NOTE]
> Den här funktionen är tillgänglig för virtuella Azure-datorer som distribueras med Resource Manager-modellen och skyddas till ett Recovery Services-valv.
> Fil återställning från en krypterad VM-säkerhetskopiering stöds inte.
>

![Arbets flöde för återställning av filmapp](./media/backup-azure-restore-files-from-vm/file-recovery-1.png)

## <a name="step-1-generate-and-download-script-to-browse-and-recover-files"></a>Steg 1: generera och ladda ned skript för att bläddra och återställa filer

Om du vill återställa filer eller mappar från återställnings punkten går du till den virtuella datorn och utför följande steg:

1. Logga in på [Azure Portal](https://portal.Azure.com) och välj **virtuella datorer** i det vänstra fönstret. I listan över virtuella datorer väljer du den virtuella datorn för att öppna den virtuella datorns instrument panel.

2. I den virtuella datorns meny väljer du **säkerhets kopiering** för att öppna instrument panelen för säkerhets kopiering.

    ![Öppna Recovery Services valv säkerhets kopierings objekt](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. På instrument panelen för säkerhets kopierings menyn väljer du **fil återställning**.

    ![Välj fil återställning](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Menyn **fil återställning** öppnas.

    ![Fil återställnings meny](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. I list rutan **Välj återställnings punkt** väljer du den återställnings punkt som innehåller de filer som du vill använda. Som standard är den senaste återställnings punkten redan vald.

5. Välj **Hämta körbar fil** (för virtuella Windows Azure-datorer) eller **Ladda ned skript** (för virtuella Linux Azure-datorer genereras ett Python-skript) för att ladda ned program varan som används för att kopiera filer från återställnings punkten.

    ![Ladda ned körbar fil](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure laddar ned den körbara filen eller skriptet till den lokala datorn.

    ![Hämta meddelandet för den körbara filen eller skriptet](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Om du vill köra den körbara filen eller skriptet som administratör rekommenderar vi att du sparar den nedladdade filen på din dator.

6. Den körbara filen eller skriptet är lösenordsskyddat och kräver ett lösen ord. I **fil återställnings** menyn väljer du knappen Kopiera för att läsa in lösen ordet i minnet.

    ![Genererat lösen ord](./media/backup-azure-restore-files-from-vm/generated-pswd.png)


## <a name="step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script"></a>Steg 2: kontrol lera att datorn uppfyller kraven innan du kör skriptet

När skriptet har hämtats kontrollerar du att du har rätt dator för att köra det här skriptet. Den virtuella datorn där du planerar att köra skriptet bör inte ha någon av följande konfigurationer som inte stöds. Om så är fallet väljer du en annan dator helst från samma region som uppfyller kraven.  

### <a name="dynamic-disks"></a>Dynamiska diskar

Du kan inte köra det körbara skriptet på den virtuella datorn med någon av följande egenskaper:

- Volymer som sträcker sig över flera diskar (utsträckta och stripade volymer).
- Feltoleranta volymer (speglade volymer och RAID-5-volymer) på dynamiska diskar.

### <a name="windows-storage-spaces"></a>Windows Storage Spaces

Det går inte att köra den nedladdade körbara filen på den virtuella datorn som är konfigurerad för Windows lagrings utrymmen.

### <a name="virtual-machine-backups-having-large-disks"></a>Säkerhets kopiering av virtuella datorer med stora diskar

Om den säkerhetskopierade datorn har ett stort antal diskar (>16) eller stora diskar (> 4 TB var) rekommenderar vi inte att du kör skriptet på samma dator för återställning, eftersom det kommer att ha en betydande inverkan på den virtuella datorn. I stället rekommenderar vi att du bara har en separat virtuell dator för fil återställning (virtuella Azure VM D2v3-datorer) och sedan stänger ned den när det inte behövs. 

Se krav för att återställa filer från säkerhetskopierade virtuella datorer med stor disk:<br>
[Windows OS](#for-backed-up-vms-with-large-disks-windows)<br>
[Linux-operativsystem](#for-backed-up-vms-with-large-disks-linux)


## <a name="step-3-os-requirements-to-successfully-run-the-script"></a>Steg 3: operativ system krav för att köra skriptet

Den virtuella dator som du vill köra det nedladdade skriptet på måste uppfylla följande krav.

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
| .NET | 4.6.2 och över |
| TLS | 1,2 bör stödjas  |

## <a name="step-4-access-requirements-to-successfully-run-the-script"></a>Steg 4: åtkomst krav för att köra skriptet

Om du kör skriptet på en dator med begränsad åtkomst kontrollerar du att det finns åtkomst till:

- `download.microsoft.com`
- URL: er för återställnings tjänsten (GEO-NAME refererar till den region där Recovery Services valvet finns)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.com` (För offentliga Azure-regioner)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.cn` (För Azure Kina-21Vianet)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.us` (För Azure amerikanska myndigheter)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.de` (För Azure Germany)
- Utgående portar 53 (DNS), 443, 3260

> [!NOTE]
>
> Skript filen som du laddade ned i steg 1 [ovan](#step-1-generate-and-download-script-to-browse-and-recover-files) kommer att ha **geo-namnet** i namnet på filen. Använd detta **geo-namn** för att fylla i URL: en. Det nedladdade skript namnet kommer att börja med: \' VMName- \' \_ \' attributnamnet \' _ \' GUID \' .<br><br>
> Om exempelvis skriptets fil namn är *ContosoVM_wcus_12345678*, är **geo-namnet** *wcus* och URL: en skulle vara:<br> <https://pod01-rec2.wcus.backup.windowsazure.com>
>

För Linux kräver skriptet "Open-iSCSI"-och ' lshw '-komponenter för att ansluta till återställnings punkten. Om komponenterna inte finns på den dator där skriptet körs, ställer skriptet efter behörighet att installera komponenterna. Ange medgivande för att installera de nödvändiga komponenterna.

Åtkomst till `download.microsoft.com` krävs för att ladda ned komponenter som används för att skapa en säker kanal mellan den dator där skriptet körs och data i återställnings punkten.


## <a name="step-5-running-the-script-and-identifying-volumes"></a>Steg 5: köra skriptet och identifiera volymer

### <a name="for-windows"></a>För Windows

När du har uppfyllt alla krav som anges i steg 2, steg 3 och steg 4 kopierar du skriptet från den hämtade platsen (vanligt vis mappen Hämtade filer), högerklickar på den körbara filen eller skriptet och kör den med administratörs behörighet. När du uppmanas till det anger du lösen ordet eller klistrar in lösen ordet från minnet och trycker på RETUR. När du har angett ett giltigt lösen ord ansluter skriptet till återställnings punkten.

  ![Körbara utdata](./media/backup-azure-restore-files-from-vm/executable-output.png)


När du kör den körbara filen monterar operativ systemet de nya volymerna och tilldelar enhets beteckningar. Du kan använda Utforskaren eller Utforskaren för att bläddra bland enheterna. De enhets beteckningar som tilldelats volymerna får inte ha samma bokstäver som den ursprungliga virtuella datorn. Volym namnet bevaras dock. Om volymen på den ursprungliga virtuella datorn exempelvis var "datadisk (E: `\` )" kan den volymen anslutas på den lokala datorn som "data disk (" valfri bokstav ": `\` ). Bläddra igenom alla volymer som anges i skriptets utdata tills du hittar dina filer eller mappar.  

   ![Anslutna återställnings volymer](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-backed-up-vms-with-large-disks-windows"></a>För säkerhetskopierade virtuella datorer med stora diskar (Windows)

Om fil återställnings processen låser sig efter att du kört fil återställnings skriptet (till exempel om diskarna aldrig monteras eller om de är monterade men volymerna inte visas) utför du följande steg:
  
1. Se till att operativ systemet är WS 2012 eller högre.
2. Se till att register nycklarna är inställda enligt rekommendationerna nedan i återställnings servern och var noga med att starta om servern. Antalet bredvid GUID kan vara mellan 0001-0005. I följande exempel är det 0004. Navigera genom sökvägen till register nyckeln tills avsnittet parametrar.

    ![Register nyckel ändringar](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

### <a name="for-linux"></a>För Linux

För Linux-datorer genereras ett Python-skript. Hämta skriptet och kopiera det till den relevanta/kompatibla Linux-servern. Du kan behöva ändra behörigheterna för att köra den med ```chmod +x <python file name>``` . Kör sedan python-filen med ```./<python file name>``` .


I Linux monteras volymerna för återställnings punkten på den mapp där skriptet körs. De anslutna diskarna, volymerna och motsvarande monterings Sök vägar visas i enlighet med detta. Dessa monterings Sök vägar visas för användare som har åtkomst till rot nivå. Bläddra igenom de volymer som anges i utdata för skriptet.

  ![Fil återställnings meny för Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)


#### <a name="for-backed-up-vms-with-large-disks-linux"></a>För säkerhetskopierade virtuella datorer med stora diskar (Linux) * *

Om fil återställnings processen låser sig efter att du kört fil återställnings skriptet (till exempel om diskarna aldrig monteras eller om de är monterade men volymerna inte visas) utför du följande steg:

1. I filen/etc/iSCSI/iscsid.conf ändrar du inställningen från:
    - `node.conn[0].timeo.noop_out_timeout = 5`  att `node.conn[0].timeo.noop_out_timeout = 120`
2. Kör skriptet igen när du har gjort ändringarna ovan. Om det finns tillfälliga haverier, se till att det finns ett mellanrum på 20 till 30 minuter mellan återförsök för att undvika efterföljande burst-överföring av begär Anden som påverkar mål förberedelsen. Intervallet mellan omkörningar ser till att målet är klart för anslutning från skriptet.
3. Efter fil återställning kontrollerar du att du går tillbaka till portalen och väljer **demontera diskar** för återställnings punkter där du inte kunde montera volymerna. I stort sett rensar det här steget eventuella befintliga processer/sessioner och ökar chansen att återställa.


#### <a name="lvmraid-arrays-for-linux-vms"></a>LVM/RAID-matriser (för virtuella Linux-datorer)

I Linux används Logical Volume Manager (LVM) och/eller programvaru-RAID-matriser för att hantera logiska volymer över flera diskar. Om den skyddade virtuella Linux-datorn använder LVM-och/eller RAID-matriser kan du inte köra skriptet på samma virtuella dator.<br>
Kör i stället skriptet på en annan dator med ett kompatibelt operativ system och som stöder fil systemet på den skyddade virtuella datorn.<br>
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

Se till att volym grupperna som motsvarar skriptets volymer är aktiva. Följande kommando används för att visa aktiva volym grupper. Kontrol lera om skriptets relaterade volym grupper finns i den här listan.

```bash
vgdisplay -a
```  

Annars aktiverar du volym gruppen med hjälp av följande kommando.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>Lista logiska volymer i volym grupper

När vi hämtar den unika, aktiva listan över VGs som är relaterade till skriptet, kan de logiska volymer som finns i dessa volym grupper listas med hjälp av följande kommando.

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
> Använd inte "Mount-a". Det här kommandot monterar alla enheter som beskrivs i '/etc/fstab '. Detta kan betyda att dubbla enheter kan monteras. Data kan omdirigeras till enheter som skapats av ett skript, vilket inte bevarar data, och detta kan leda till data förlust.

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

## <a name="step-6-closing-the-connection"></a>Steg 6: stänger anslutningen

När du har identifierat filerna och kopierat dem till en lokal lagrings plats, ta bort (eller demontera) de ytterligare enheterna. Om du vill demontera enheterna går du till **fil återställnings** menyn i Azure Portal och väljer **demontera diskar**.

![Demontera diskar](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

När diskarna har demonterats får du ett meddelande. Det kan ta några minuter innan anslutningen har uppdaterats så att du kan ta bort diskarna.

I Linux tar operativ systemet inte bort motsvarande monterings Sök vägar automatiskt när anslutningen till återställnings punkten har brutits. Monterings Sök vägarna finns som "föräldralösa" volymer och är synliga, men genererar ett fel när du öppnar/skriver filerna. De kan tas bort manuellt. Skriptet, vid körning, identifierar eventuella sådana volymer som är befintliga från alla tidigare återställnings punkter och rensar dem vid medgivande.

> [!NOTE]
> Kontrol lera att anslutningen är stängd när de filer som krävs har återställts. Detta är viktigt, särskilt i scenariot där den dator där skriptet körs också har kon figurer ATS för säkerhets kopiering. Om anslutningen fortfarande är öppen kan den efterföljande säkerhets kopieringen Miss förväntar sig med felet "UserErrorUnableToOpenMount". Detta beror på att de monterade enheterna/volymerna antas vara tillgängliga och när de kan komma åt dem kan Miss lyckas eftersom den underliggande lagringen, det vill säga, inte är tillgänglig. Om du rensar anslutningen tas dessa enheter/volymer bort, så de kommer inte att vara tillgängliga under säkerhets kopieringen.

## <a name="security"></a>Säkerhet

I det här avsnittet beskrivs de olika säkerhets åtgärder som vidtagits för att genomföra fil återställning från virtuella Azure-säkerhetskopieringar.

### <a name="feature-flow"></a>Funktions flöde

Den här funktionen har skapats för att komma åt VM-data utan att behöva återställa hela VM-eller VM-diskar och med det minsta antalet steg. Åtkomst till VM-data tillhandahålls av ett skript (som monterar återställnings volymen när den körs enligt nedan) och den utgör hörnen för alla säkerhets implementeringar:

  ![Säkerhets funktions flöde](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Säkerhets implementeringar

#### <a name="select-recovery-point-who-can-generate-script"></a>Välj återställnings punkt (som kan generera skript)

Skriptet ger åtkomst till VM-data, så det är viktigt att reglera vem som kan generera det på den första platsen. Du måste logga in på Azure Portal och vara [Azure RBAC-auktoriserad](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) för att kunna generera skriptet.

Fil återställning måste ha samma behörighets nivå som krävs för återställning av virtuella datorer och diskar. Med andra ord kan endast behöriga användare se de virtuella dator data som kan generera skriptet.

Det genererade skriptet är signerat med det officiella Microsoft-certifikatet för tjänsten Azure Backup. Manipulering av skriptet innebär att signaturen är bruten och att försök att köra skriptet är markerat som en potentiell risk av operativ systemet.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montera återställnings volym (som kan köra skript)

Endast en administratör kan köra skriptet och det ska köras i förhöjd läge. Skriptet kör bara en i förväg genererad uppsättning steg och accepterar inte inmatade från externa källor.

För att köra skriptet krävs ett lösen ord som bara visas för den auktoriserade användaren vid tidpunkten för generering av skript i Azure Portal eller PowerShell/CLI. Detta är att se till att den auktoriserade användare som laddar ned skriptet också är ansvarig för att köra skriptet.

#### <a name="browse-files-and-folders"></a>Bläddra bland filer och mappar

För att bläddra bland filer och mappar använder skriptet iSCSI-initieraren på datorn och ansluter till återställnings punkten som är konfigurerad som ett iSCSI-mål. Här kan du tänka på scenarier där en försöker imitera/manipulera antingen/alla komponenter.

Vi använder en metod för ömsesidig CHAP-autentisering så att varje komponent autentiserar den andra. Det innebär att det är mycket svårt för en falsk initierare att ansluta till iSCSI-målet och att ett förfalskat mål måste vara anslutet till den dator där skriptet körs.

Data flödet mellan återställnings tjänsten och datorn skyddas genom att skapa en säker TLS-tunnel över TCP ([TLS 1,2 bör stödjas](#step-3-os-requirements-to-successfully-run-the-script) på den dator där skriptet körs).

Alla fil Access Control List (ACL) som finns i den överordnade/säkerhetskopierade virtuella datorn bevaras även i det monterade fil systemet.

Skriptet ger skrivskyddad åtkomst till en återställnings punkt och är bara giltig i 12 timmar. Om du vill ta bort åtkomsten tidigare loggar du in på Azure Portal/PowerShell/CLI och utför **demontera diskar** för den specifika återställnings punkten. Skriptet kommer att ogiltig förklaras.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [återställer filer via PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- Lär dig hur du [återställer filer via Azure CLI](./tutorial-restore-files.md)
- När den virtuella datorn har återställts kan du läsa mer om hur du [hanterar säkerhets kopior](./backup-azure-manage-vms.md)
