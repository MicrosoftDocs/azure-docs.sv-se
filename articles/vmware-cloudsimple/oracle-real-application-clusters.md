---
title: Azure VMware-lösning av CloudSimple – optimera ditt CloudSimple privata moln för Oracle RAC
description: Beskriver hur du distribuerar ett nytt kluster och optimerar en virtuell dator för installationen och konfigurationen av Oracle Real Application Clusters (RAC)
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3959aae5f490af10c6747cfa67d9960e0c4a203f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899277"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimera ditt CloudSimple privata moln för att installera Oracle RAC

Du kan distribuera Oracle Real Application Clusters (RAC) i din CloudSimple privata moln miljö. I den här guiden beskrivs hur du distribuerar ett nytt kluster och optimerar en virtuell dator för Oracle RAC-lösningen. När du har slutfört stegen i det här avsnittet kan du installera och konfigurera Oracle RAC.

## <a name="storage-policy"></a>Lagrings princip

Lyckad implementering av Oracle RAC kräver ett tillräckligt antal noder i klustret.  I virtuellt San lagrings princip tillämpas felen på tolerera (FTT) på data diskar som används för att lagra databasen, logga och gör om diskar.  Antalet noder som krävs för att effektivt tolerera felen är 2N + 1 där N är värdet för FTT.

Exempel: om det önskade FTT är 2, måste det totala antalet noder i klustret vara 2 * 2 + 1 = 5.

## <a name="overview-of-deployment"></a>Översikt över distribution

I följande avsnitt beskrivs hur du konfigurerar din CloudSimple privata moln miljö för Oracle RAC.

1. Metod tips för disk konfiguration
2. Distribuera CloudSimple Private Cloud vSphere-kluster
3. Konfigurera nätverk för Oracle RAC
4. Konfigurera virtuellt San Storage policies
5. Skapa virtuella Oracle-datorer och skapa delade VM-diskar
6. Konfigurera tillhörighets regler för virtuell dator till värd

## <a name="best-practices-for-disk-configuration"></a>Metod tips för disk konfiguration

Virtuella Oracle RAC-datorer har flera diskar som används för en speciell funktion.  Delade diskar monteras på alla virtuella datorer som används av Oracle RAC-kluster.  Installations diskarna för operativ system och program vara monteras bara på de enskilda virtuella datorerna.  

![Översikt över virtuella Oracle RAC-datorer](media/oracle-vm-disks-overview.png)

I följande exempel används diskarna som definierats i tabellen nedan.

| Disk                                      | Syfte                                       | Delad disk |
|-------------------------------------------|-----------------------------------------------|-------------|
| Operativsystem                                        | Operativsystemdisk                         | Inga          |
| STÖDRASTRET                                      | Installations plats för Oracle Grid-programvara     | Inga          |
| DATABASE                                  | Installations plats för Oracle Database-programvara | Inga          |
| ORAHOME                                   | Bas plats för binärfiler för Oracle-databasen    | Inga          |
| DATA1, DATA2, DATA3, DATA4                | Disk där Oracle-databasfiler lagras   | Ja         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Gör om logg diskar                                | Ja         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Röstnings diskar                                  | Ja         |
| FRA1, FRA2                                | Snabba återställnings områden diskar                      | Ja         |

![Disk konfiguration för Oracle-virtuell dator](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Konfiguration av virtuell dator

* Varje virtuell dator är konfigurerad med fyra SCSI-styrenheter.
* Typ av SCSI-styrenhet har angetts till VMware paravirtuell.
* Flera virtuella diskar (. VMDK) skapas.
* Diskar monteras på olika SCSI-styrenheter.
* Delnings typ för flera skrivare har angetts för delade kluster diskar.
* Virtuellt San lagrings princip definieras för att säkerställa hög tillgänglighet för diskar.

### <a name="operating-system-and-software-disk-configuration"></a>Konfiguration av operativ system och program disk

Varje virtuell Oracle-dator har kon figurer ATS med flera diskar för värd operativ systemet, växling, program varu installation och andra operativ system funktioner.  Diskarna delas inte mellan de virtuella datorerna.  

* Tre diskar för varje virtuell dator konfigureras som virtuella diskar och monteras på virtuella Oracle RAC-datorer.
    * OS-disk
    * Disk för att lagra Oracle-rutnät installerar filer
    * Disk för lagring av filer för Oracle Database-installation
* Diskar kan konfigureras som **tunt allokerade**.
* Varje disk monteras på den första SCSI-styrenheten (SCSI0).  
* Delning har angetts till **Ingen delning**.
* Redundans definieras på lagrings platsen med virtuellt SAN-principer.  

![Diagram som visar fysisk konfiguration för Oracle RAC OS-disken.](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Konfiguration av data disk

Data diskar används främst för att lagra databasfiler.  

* Fyra diskar konfigureras som virtuella diskar och monteras på alla virtuella Oracle RAC-datorer.
* Varje disk monteras på en annan SCSI-styrenhet.
* Varje virtuell disk konfigureras som **tjocka etablerings Eager nollställd**.  
* Delning har angetts till **multi-Writer**.  
* Diskarna måste vara konfigurerade som en enhets grupp för automatisk lagrings hantering (ASM).  
* Redundans definieras på lagrings platsen med virtuellt SAN-principer.  
* ASM-redundans är inställt på **extern** redundans.

![Konfiguration av Oracle RAC data disk Group](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Gör om logg disk konfiguration

Gör om loggfiler som används för att lagra en kopia av de ändringar som gjorts i databasen.  Loggfilerna används när data måste återställas efter eventuella problem.

* Gör om logg diskar måste konfigureras som flera disk grupper.  
* Sex diskar skapas och monteras på alla virtuella Oracle RAC-datorer.
* Diskar monteras på olika SCSI-styrenheter
* Varje virtuell disk konfigureras som **tjocka etablerings Eager nollställd**.
* Delning har angetts till **multi-Writer**.  
* Diskarna måste vara konfigurerade som två ASM-diskenheter.
* Varje ASM-diskavbildning innehåller tre diskar, som finns på olika SCSI-styrenheter.  
* ASM-redundans har angetts till **Normal** redundans.
* Fem göra om-loggfiler skapas på båda ASM recreate-logg gruppen

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Konfiguration av logg disk grupp för Oracle RAC REG](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Konfiguration av Oracle-röstning

Röstnings diskar tillhandahåller funktioner för kvorum som en ytterligare kommunikations kanal för att undvika eventuella problem med delad hjärna.

* Fem diskar skapas och monteras på alla virtuella Oracle RAC-datorer.
* Diskar monteras på en SCSI-styrenhet
* Varje virtuell disk konfigureras som **tjocka etablerings Eager nollställd**.
* Delning har angetts till **multi-Writer**.  
* Diskarna måste vara konfigurerade som en ASM-disk grupp.  
* ASM-redundans har angetts till **hög** redundans.

![Konfiguration av disk grupp för Oracle RAC-röstning](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Disk konfiguration för snabb återställnings utrymme i Oracle (valfritt)

Det snabba återställnings fältet (FRA) är fil system som hanteras av Oracle ASM-diskavbildning.  FRA tillhandahåller en delad lagrings plats för säkerhets kopierings-och återställnings filer. Oracle skapar arkiverade loggar och Flashback-loggar i snabb återställnings ytan. Oracle Recovery Manager (RMAN) kan lagra sina säkerhets kopior och avbildnings kopior i snabb återställnings fältet och använda dem när de återställer filer under medie återställning.

* Två diskar skapas och monteras på alla virtuella Oracle RAC-datorer.
* Diskar monteras på en annan SCSI-styrenhet
* Varje virtuell disk konfigureras som **tjocka etablerings Eager nollställd**.
* Delning har angetts till **multi-Writer**.  
* Diskarna måste vara konfigurerade som en ASM-disk grupp.  
* ASM-redundans är inställt på **extern** redundans.

![Diagram som visar konfigurationen av en röst grupp för Oracle RAC-röstning.](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Distribuera CloudSimple Private Cloud vSphere-kluster

Följ den här processen om du vill distribuera ett vSphere-kluster i ditt privata moln:

1. [Skapa ett privat moln](create-private-cloud.md)från CloudSimple-portalen. CloudSimple skapar en standard-vCenter-användare med namnet "cloudowner" i det nyligen skapade privata molnet. Mer information om standard användar-och behörighets modellen för privata moln finns i [Lär dig mer om behörighets modellen för privata moln](learn-private-cloud-permissions.md).  Det här steget skapar det primära hanterings klustret för ditt privata moln.

2. Från CloudSimple-portalen [expanderar du det privata molnet](expand-private-cloud.md) med ett nytt kluster.  Det här klustret kommer att användas för att distribuera Oracle RAC.  Välj antalet noder baserat på önskad fel tolerans (minst tre noder).

## <a name="set-up-networking-for-oracle-rac"></a>Konfigurera nätverk för Oracle RAC

1. I ditt privata moln [skapar du två VLAN](create-vlan-subnet.md), ett för det offentliga Oracle-nätverket och ett för det privata Oracle-nätverket och tilldelar lämpliga CIDR-exempel för undernät.
2. När VLAN har skapats skapar du de [distribuerade port grupperna i det privata molnet vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Konfigurera en [virtuell DHCP-och DNS-serverdator](dns-dhcp-setup.md) i hanterings klustret för Oracle-miljön.
4. [Konfigurera DNS-vidarebefordran på den DNS-Server](on-premises-dns-setup.md#create-a-conditional-forwarder) som är installerad i det privata molnet.

## <a name="set-up-vsan-storage-policies"></a>Konfigurera lagrings principer för virtuellt San

Virtuellt SAN-principer definierar felen för att tolerera och diska randning för de data som lagras på de virtuella dator diskarna.  Den lagrings princip som skapas måste tillämpas på de virtuella dator diskarna när den virtuella datorn skapas.

1. [Logga](./vcenter-access.md) in på vSphere-klienten för ditt privata moln.
2. Välj **principer och profiler** på den översta menyn.
3. På den vänstra menyn väljer du **lagrings principer för virtuella datorer** och väljer sedan **skapa en princip för VM-lagring**.
4. Ange ett beskrivande namn för principen och klicka på **Nästa**.
5. I avsnittet **princip struktur** väljer du **Aktivera regler för virtuellt SAN-lagring** och klickar på **Nästa**.
6. I avsnittet **virtuellt San**  >  -**tillgänglighet** väljer du **ingen** för plats katastrof tolerans. För att Miss lyckas med att tolerera väljer du alternativet **RAID-spegling** för önskad FTT.
    ![Virtuellt San-inställningar ](media/oracle-rac-storage-wizard-vsan.png) .
7. I avsnittet **Avancerat** väljer du antalet disk ränder per objekt. För objekt utrymmes reservation väljer du **tjockt tillhandahållen**. Välj **inaktivera objekt kontroll Summa**. Klicka på **Nästa**.
8. Följ anvisningarna på skärmen för att visa en lista över kompatibla virtuellt San-datalager, granska inställningarna och slutför installationen.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Skapa virtuella datorer i Oracle och skapa delade VM-diskar för Oracle

För att skapa en virtuell dator för Oracle, klona en befintlig virtuell dator eller skapa en ny.  I det här avsnittet beskrivs hur du skapar en ny virtuell dator och sedan klonar den för att skapa en andra efter att du har installerat det grundläggande operativ systemet.  När de virtuella datorerna har skapats kan du skapa en Lägg till diskar i dem.  Oracle-kluster använder delade diskar för lagring, data, loggar och gör om-loggar.

### <a name="create-vms"></a>Skapa VM:ar

1. I vCenter klickar du på ikonen **värdar och kluster** . Välj det kluster som du skapade för Oracle.
2. Högerklicka på klustret och välj **ny virtuell dator**.
3. Välj **Skapa ny virtuell dator** och klicka på **Nästa**.
4. Namnge datorn, Välj platsen för den virtuella Oracle-datorn och klicka på **Nästa**.
5. Välj kluster resursen och klicka på **Nästa**.
6. Välj virtuellt San-datalager för klustret och klicka på **Nästa**.
7. Behåll standard valet av ESXi 6,5 och klicka på **Nästa**.
8. Välj gäst operativ systemet för ISO-filen för den virtuella dator som du skapar och klicka på **Nästa**.
9. Välj den hård disk storlek som krävs för att installera operativ systemet.
10. Om du vill installera programmet på en annan enhet klickar du på **Lägg till ny enhet**.
11. Välj Nätverks alternativ och tilldela den distribuerade port gruppen som skapats för det offentliga nätverket.
12. Om du vill lägga till ytterligare nätverks gränssnitt klickar du på **Lägg till ny enhet** och väljer den distribuerade port grupp som skapats för det privata nätverket.
13. För ny DC/DVD-enhet väljer du ISO-filen för data lagret som innehåller ISO för den önskade installationen av operativ systemet. Välj den fil som du tidigare laddade upp till mappen ISO och mallar och klicka på **OK**.
14. Granska inställningarna och klicka på **OK** för att skapa den nya virtuella datorn.
15. Slå på den virtuella datorn. Installera operativ systemet och eventuella uppdateringar som krävs

När operativ systemet har installerats kan du klona en andra virtuell dator. Högerklicka på posten för den virtuella datorn och välj alternativet och klona.

### <a name="create-shared-disks-for-vms"></a>Skapa delade diskar för virtuella datorer

Oracle använder delad disk för att lagra data, logga och gör om loggfiler.  Du kan skapa en delad disk på vCenter och montera den på båda de virtuella datorerna.  För högre prestanda kan du placera data diskarna på olika SCSI-styrenheter steg nedan och se hur du skapar en delad disk på vCenter och sedan kopplar den till en virtuell dator. vCenter Flash client används för att ändra egenskaperna för den virtuella datorn.

#### <a name="create-disks-on-the-first-vm"></a>Skapa diskar på den första virtuella datorn

1. Högerklicka på en av de virtuella Oracle-datorerna i vCenter och välj **Redigera inställningar**.
2. I avsnittet ny enhet väljer du **SCSI-styrenhet** och klickar på **Lägg till**.
3. I avsnittet ny enhet väljer du **ny hård disk** och klickar på **Lägg till**.
4. Expandera egenskaperna för ny hård disk.
5. Ange storleken på hård disken.
6. Ange den virtuella dator lagrings principen som virtuellt San lagrings princip som du definierade tidigare.
7. Välj platsen som en mapp på virtuellt San data lager. Platsen hjälper till med att bläddra och koppla diskarna till en annan virtuell dator.
8. För disk etablering väljer du **tjock etablering Eager noll**.
9. Ange **multi-Writer** för delning.
10. För noden virtuell enhet väljer du den nya SCSI-styrenhet som skapades i steg 2.

    ![Skärm bild som visar de fält som behövs för att skapa diskar på den första virtuella datorn.](media/oracle-rac-new-hard-disk.png)

Upprepa steg 2 – 10 för alla nya diskar som krävs för Oracle-data, loggar och gör om loggfiler.

#### <a name="attach-disks-to-second-vm"></a>Koppla diskar till den andra virtuella datorn

1. Högerklicka på en av de virtuella Oracle-datorerna i vCenter och välj **Redigera inställningar**.
2. I avsnittet ny enhet väljer du **SCSI-styrenhet** och klickar på **Lägg till**.
3. I avsnittet ny enhet väljer du **befintlig hård disk** och klickar på **Lägg till**.
4. Bläddra till den plats där disken skapades för den första virtuella datorn och välj VMDK-filen.
5. Ange den virtuella dator lagrings principen som virtuellt San lagrings princip som du definierade tidigare.
6. För disk etablering väljer du **tjock etablering Eager noll**.
7. Ange **multi-Writer** för delning.
8. För noden virtuell enhet väljer du den nya SCSI-styrenhet som skapades i steg 2.

    ![Skapa diskar på den första virtuella datorn](media/oracle-rac-existing-hard-disk.png)

Upprepa steg 2 – 7 för alla nya diskar som krävs för Oracle-data, loggar och gör om loggfiler.

## <a name="set-up-vm-host-affinity-rules"></a>Konfigurera tillhörighets regler för virtuell dator värd

Regler för VM-till-värd-tillhörighet kontrollerar att den virtuella datorn körs på önskad värd.  Du kan definiera regler på vCenter för att se till att den virtuella Oracle-datorn körs på värden med tillräckliga resurser och uppfyller eventuella särskilda licensierings krav.

1. I CloudSimple-portalen [eskalerar du behörigheterna](escalate-private-cloud-privileges.md) för cloudowner-användaren.
2. Logga in på vSphere-klienten för ditt privata moln.
3. I vSphere-klienten väljer du det kluster där de virtuella Oracle-datorerna ska distribueras och klickar på **Konfigurera**.
4. Under Konfigurera väljer du **VM/värd grupper**.
5. Klicka på **+** .
6. Lägg till en VM-grupp. Välj **VM-grupp** som typ. Ange namnet på gruppen. Välj de virtuella datorerna och klicka sedan på **OK** för att skapa gruppen.
6. Lägg till en värd grupp. Välj **värd grupp** som typ. Ange namnet på gruppen. Välj värdarna där de virtuella datorerna ska köras och klicka sedan på **OK** för att skapa gruppen.
7. Om du vill skapa en regel klickar du på **VM/värd regler**.
8. Klicka på **+** .
9. Ange ett namn för regeln och markera **Aktivera**.
10. Välj **Virtual Machines som ska vara värd** för regel typen.
11. Välj den VM-grupp som innehåller de virtuella Oracle-datorerna.
12. Select **måste köras på värdar i den här gruppen**.
13. Välj den värd grupp som du skapade.
14. Skapa regeln genom att klicka på **OK**.

## <a name="references"></a>Referenser

* [Om virtuellt SAN-principer](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware multi-Writer-attribut för delade VMDK: er](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
