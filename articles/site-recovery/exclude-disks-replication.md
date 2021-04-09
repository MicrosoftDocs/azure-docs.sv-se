---
title: Undanta diskar från replikering med Azure Site Recovery
description: Så här undantar du diskar från replikering till Azure med Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008267"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Exkludera diskar från haveri beredskap

Den här artikeln beskriver hur du undantar diskar från replikering under haveri beredskap från lokala datorer till Azure med [Azure Site Recovery](site-recovery-overview.md). Du kan exkludera diskar från replikeringen av flera orsaker:

- Så att viktiga data som är överslagna på den uteslutna disken inte replikeras.
- För att optimera förbrukad replikeringstrafik eller resurser på mål sidan.
- För att spara lagrings-och nätverks resurser genom att inte replikera data som du inte behöver.
- Virtuella Azure-datorer har nått Site Recovery gränserna för replikering.


## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan undanta diskar från replikeringen som sammanfattas i tabellen.

**Azure till Azure** | **VMware till Azure** | **Hyper-V till Azure** | **Fysisk server till Azure**
--- | --- | --- | ---
Ja | Ja | Ja | Ja

## <a name="exclude-limitations"></a>Uteslut begränsningar

**Begränsning** | **Virtuella Azure-datorer** | **VMwares virtuella datorer** | **Hyper-V:s virtuella datorer**
--- | --- | ---
**Disk typer** | Du kan undanta grundläggande diskar från replikering.<br/><br/> Du kan inte undanta operativ system diskar eller dynamiska diskar. Temporära diskar undantas som standard. | Du kan undanta grundläggande diskar från replikering.<br/><br/> Du kan inte undanta operativ system diskar eller dynamiska diskar. | Du kan undanta grundläggande diskar från replikering.<br/><br/> Du kan inte undanta operativsystemdiskar. Vi rekommenderar att du inte undantar dynamiska diskar. Site Recovery kan inte identifiera vilken VHS som är grundläggande eller dynamisk i den virtuella gäst datorn. Om alla beroende dynamiska volym diskar inte undantas blir den skyddade dynamiska disken en felaktig disk på en virtuell dator med redundans och data på disken är inte tillgängliga.
**Replikera disk** | Du kan inte utesluta en disk som replikeras.<br/><br/> Inaktivera och återaktivera replikering för den virtuella datorn. |  Du kan inte utesluta en disk som replikeras. |  Du kan inte utesluta en disk som replikeras.
**Mobilitets tjänst (VMware)** | Inte relevanta | Du kan endast utesluta diskar på virtuella datorer som har mobilitets tjänsten installerad.<br/><br/> Det innebär att du måste installera mobilitets tjänsten manuellt på de virtuella datorer som du vill exkludera diskar för. Du kan inte använda metoden för push-installation eftersom den bara installerar mobilitets tjänsten när replikering har Aktiver ATS. | Inte relevant.
**Lägg till/ta bort** | Du kan lägga till hanterade diskar på replikerings-aktiverade virtuella Azure-datorer med hanterade diskar. Du kan inte ta bort diskar på replikerings-aktiverade virtuella Azure-datorer. | Du kan inte lägga till eller ta bort diskar när replikering har Aktiver ATS. Inaktivera och återaktivera replikering för att lägga till en disk. | Du kan inte lägga till eller ta bort diskar när replikering har Aktiver ATS. Inaktivera och aktivera sedan replikering igen.
**Redundans** | Om en app behöver en disk som du har exkluderat, efter redundansväxlingen måste du skapa disken manuellt så att den replikerade appen kan köras.<br/><br/> Du kan också skapa disken vid redundansväxling av den virtuella datorn genom att integrera Azure Automation i en återställnings plan. | Om du undantar en disk som en app behöver skapar du den manuellt i Azure efter redundansväxlingen. | Om du undantar en disk som en app behöver skapar du den manuellt i Azure efter redundansväxlingen.
**Lokal återställning efter fel – diskar som skapats manuellt** | Inte relevanta | **Virtuella Windows-datorer**: diskar som skapats manuellt i Azure kunde inte återställas. Om du till exempel växlar över tre diskar och skapar två diskar direkt på en virtuell Azure-dator, återställs bara de tre diskar som växlades över sedan tillbaka.<br/><br/> **Virtuella Linux-datorer**: diskar som skapats manuellt i Azure återställs inte. Om du till exempel växlar över tre diskar och skapar två diskar på en virtuell Azure-dator återställs inte alla fem. Du kan inte undanta diskar som har skapats manuellt från återställningen. | Diskar som skapats manuellt i Azure kunde inte återställas. Om du till exempel växlar över tre diskar och skapar två diskar direkt på en virtuell Azure-dator kommer endast tre diskar som växlades över att växlas tillbaka.
**Lokal återställning efter fel – exkluderade diskar** | Inte relevanta | Om du växlar tillbaka till den ursprungliga datorn inkluderar inte disk konfigurationen för återställning av virtuella datorer de exkluderade diskarna. Diskar som har uteslutits från VMware till Azure-replikering är inte tillgängliga på den virtuella datorn för återställning efter fel. | När återställningen är till den ursprungliga Hyper-V-platsen, är disk konfigurationen för den virtuella datorn densamma som för den ursprungliga käll dator disken. Diskar som har uteslutits från Hyper-V-platsen till Azure-replikering är tillgängliga på den virtuella datorn för återställning efter fel.



## <a name="typical-scenarios"></a>Typiska scenarier

Exempel på data omsättning som är bra kandidater för undantag är skrivningar till en växlings fil (pagefile.sys) och skriver till tempdb-filen för Microsoft SQL Server. Beroende på arbets belastningen och underlag rings systemet kan växlings-och tempdb-filerna registrera en stor mängd omsättning. Att replikera den här typen av data till Azure är resurs intensiv.

- För att optimera replikering för en virtuell dator med en enda virtuell disk som innehåller både operativ systemet och växlings filen, kan du:
    1. Dela upp den enda virtuella hårddisken på två virtuella diskar. En virtuell disk har operativsystemet och den andra har växlingsfilen.
    2. Undanta växlingsfilen från replikering.

- För att optimera replikering för en disk som innehåller både Microsoft SQL Server tempdb-filen och system databas filen, kunde du:
    1. Förvara systemdatabasen och tempdb på två olika diskar.
    2. Undanta tempdb-disken från replikeringen.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exempel 1: Undanta en SQL Server tempdb-disk

Nu ska vi titta på hur du hanterar disk undantag, redundans och redundans för en källa SQL Server Windows VM-* * SalesDB * * *, för vilken vi vill undanta tempdb. 

### <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

Vi har de här diskarna på den virtuella käll datorn för Windows-SalesDB.

**Disknamn** | **Gäst operativ system disk** | **Enhetsbeteckning** | **Disk data typ**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operativ system disk.
DB-Disk1| Disk1 | D:\ | SQL system Database och User Databas1.
DB-Disk2 (disken har undantagits från skydd) | Disk2 | E:\ | Temporära filer.
DB-Disk3 (disken har undantagits från skydd) | Disk3 | F:\ | SQL tempdb-databas.<br/><br/> Mappsökväg – F:\MSSQL\Data\. Anteckna sökvägen till mappen före redundansväxlingen.
DB-Disk4 | Disk4 |G:\ | Användardatabas2

1. Vi aktiverar replikering för den virtuella SalesDB-datorn.
2. Vi undantar disk2 och Disk3 från replikering eftersom data omsättning på dessa diskar är tillfälligt. 


### <a name="handle-disks-during-failover"></a>Hantera diskar under redundans

Eftersom diskar inte replikeras när du växlar över till Azure finns inte diskarna på den virtuella Azure-datorn som skapades efter redundansväxlingen. Den virtuella Azure-datorn har diskarna sammanfattas i den här tabellen.

**Gäst operativ system disk** | **Enhetsbeteckning** | **Disk data typ**
--- | --- | ---
Disk0 | C:\ | Operativ system disk.
Disk1 | E:\ | Tillfällig lagring<br/><br/>Azure lägger till den här disken. Eftersom disk2 och Disk3 uteslöts från replikering, E: är den första enhets beteckningen från listan över tillgängliga. Azure tilldelar E: till den tillfälliga lagringsvolymen. Andra enhets beteckningar för replikerade diskar är desamma.
Disk2 | D:\ | SQL-systemdatabas och användardatabas1
Disk3 | G:\ | Användardatabas2

I vårt exempel har SQL tempdb-disken, eftersom Disk3, exkluderats från replikering och inte är tillgänglig på den virtuella Azure-datorn, och SQL-tjänsten är i ett stoppat tillstånd och den behöver F:\MSSQL\Data-sökvägen. Du kan skapa den här sökvägen på ett par olika sätt: 

- Lägg till en ny disk efter redundansväxlingen och tilldela tempdb-mappsökvägen.
- Använda den befintliga tillfälliga lagringsdisken för tempdb-mappsökvägen.

#### <a name="add-a-new-disk-after-failover"></a>Lägg till en ny disk efter redundans

1. Notera sökvägarna till SQL tempdb.mdf och tempdb.ldf före redundansväxlingen.
2. Från Azure Portal lägger du till en ny disk till den virtuella Azure-datorn. Disken ska ha samma storlek (eller större) som källa för SQL tempdb-disken (Disk3).
3. Logga in på den virtuella Azure-datorn.
4. Initiera och formatera den nytillagda disken från diskhanteringskonsolen (diskmgmt.msc).
5. Tilldela samma enhets beteckning som användes av SQL tempdb-disken (F:)
6. Skapa en tempdb-mapp på volym F: (F:\MSSQL\Data).
7. Starta SQL-tjänsten från tjänstkonsolen.

#### <a name="use-an-existing-temporary-storage-disk"></a>Använd en befintlig tillfällig lagrings disk 

1. Öppna en kommandotolk.
2. Kör SQL Server i återställningsläge från kommandotolken.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Kör följande sqlcmd för att ändra tempdb-sökvägen till en ny sökväg.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Stoppa Microsoft SQL Server-tjänsten.

    ```console
    Net stop MSSQLSERVER
    ```

5. Starta Microsoft SQL Server-tjänsten.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>Virtuella VMware-datorer: diskar under återställning efter fel till den ursprungliga platsen

Nu ska vi se hur du hanterar diskar på virtuella VMware-datorer när du växlar tillbaka till den ursprungliga lokala platsen.

- **Diskar som skapats i Azure**: eftersom vårt exempel använder en virtuell Windows-dator replikeras diskar som du skapar manuellt i Azure tillbaka till din plats när du växlar tillbaka eller återaktiverar en virtuell dator.
- **Tillfällig lagrings disk i Azure**: den tillfälliga lagrings disken replikeras inte tillbaka till lokala värdar.
- **Exkluderade diskar**: diskar som uteslöts från VMware till Azure-replikering är inte tillgängliga på den lokala virtuella datorn efter återställning efter fel.

Innan du återställer de virtuella VMware-datorerna till den ursprungliga platsen, är inställningarna för Azure VM-disken följande.

**Gäst operativ system disk** | **Enhetsbeteckning** | **Disk data typ**
--- | --- | ---
Disk0 | C:\ | Operativ system disk.
Disk1 | E:\ | Tillfällig lagring.
Disk2 | D:\ | SQL system Database och User Databas1.
Disk3 | G:\ | Användarens Databas2.

Efter återställningen har den virtuella VMware-datorn på den ursprungliga platsen de diskar som sammanfattas i tabellen.

**Gäst operativ system disk** | **Enhetsbeteckning** | **Disk data typ**
--- | --- | ---
Disk0 | C:\ | Operativ system disk.
Disk1 | D:\ | SQL system Database och User Databas1.
Disk2 | G:\ | Användarens Databas2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Virtuella Hyper-V-datorer: diskar under återställning efter fel till den ursprungliga platsen

Nu ska vi se hur du hanterar diskar på virtuella Hyper-V-datorer när du växlar tillbaka till den ursprungliga lokala platsen.

- **Diskar som skapats i Azure**: diskar som du skapar manuellt i Azure replikeras inte tillbaka till din plats när du växlar tillbaka eller återaktiverar en virtuell dator.
- **Tillfällig lagrings disk i Azure**: den tillfälliga lagrings disken replikeras inte tillbaka till lokala värdar.
- **Exkluderade diskar**: efter återställning efter fel är konfigurationen av den virtuella dator disken densamma som den ursprungliga disk konfigurationen för virtuella datorer. Diskar som har uteslutits från replikering från Hyper-V till Azure finns på den virtuella datorn för återställning efter fel.

Innan du växlar tillbaka de virtuella Hyper-V-datorerna till den ursprungliga platsen, är inställningarna för Azure VM-disken följande.

**Gäst operativ system disk** | **Enhetsbeteckning** | **Disk data typ**
--- | --- | ---
Disk0 | C:\ | Operativ system disk.
Disk1 | E:\ | Tillfällig lagring.
Disk2 | D:\ | SQL system Database och User Databas1.
Disk3 | G:\ | Användarens Databas2.

Efter planerad redundansväxling (failback) från Azure till lokal Hyper-V har den virtuella Hyper-V-datorn på den ursprungliga platsen diskarna sammanfattas i tabellen.

**Disk namn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Disk data typ**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Operativ system disk.
DB-Disk1 | Disk1 | D:\ | SQL system Database och User Databas1.
DB-Disk2 (utesluten disk) | Disk2 | E:\ | Temporära filer.
DB-Disk3 (utesluten disk) | Disk3 | F:\ | SQL tempdb-databas<br/><br/> Mappsökväg (F:\MSSQL\Data \) .
DB-Disk4 | Disk4 | G:\ | Användardatabas2


## <a name="example-2-exclude-the-paging-file-disk"></a>Exempel 2: exkludera växlings fil disken

Nu ska vi titta på hur du hanterar disk undantag, redundans och redundans för en virtuell dator i Windows som vi vill utesluta pagefile.sys-fildisken på både D-enheten och en alternativ enhet.


### <a name="paging-file-on-the-d-drive"></a>Växlings fil på D-enheten

Vi har de här diskarna på den virtuella käll datorn.

**Disknamn** | **Gäst operativ system disk** | **Enhetsbeteckning** | **Disk data typ**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operativsystemdisk
DB-Disk1 (exkludera från replikering) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Inställningarna för fil växlings filen på den virtuella käll datorn är följande:

![Skärm bild av dialog rutan virtuellt minne med linjen D: enhet [växlings enhets volym] markerad som visar växlings filens storlek (MB) på 3000-7000.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Vi aktiverar replikering för den virtuella datorn.
2. Vi undantar DB-Disk1 från replikering.

#### <a name="disks-after-failover"></a>Diskar efter redundans

Efter redundansväxlingen har de diskar som sammanfattas i tabellen sammanfattas i den virtuella Azure-datorn.

**Disknamn** | **Antal gästoperativsystem** | **Enhetsbeteckning** | **Datatyp på disken**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operativsystemdisk
DB-Disk1 | Disk1 | D:\ | Tillfällig lagring/pagefile.sys <br/><br/> Eftersom DB-Disk1 (D:) uteslöts, D: är den första enhets beteckningen från listan över tillgängliga.<br/><br/> Azure tilldelar enhetsbeteckningen D: till den tillfälliga lagringsvolymen.<br/><br/> Eftersom D: är tillgängligt förblir inställningen för VM-växlingsfilen densamma).
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Inställningarna för växlings fil på den virtuella Azure-datorn är följande:

![Inställningar för växlingsfiler på den virtuella Azure-datorn](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Växlings fil på en annan enhet (inte D:)

Nu ska vi titta på exempel där växlings filen inte finns på D-enheten.  

Vi har de här diskarna på den virtuella käll datorn.

**Disknamn** | **Gäst operativ system disk** | **Enhetsbeteckning** | **Disk data typ**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operativsystemdisk
DB-Disk1 (exkludera från replikering) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Inställningarna för växlings filen på den lokala virtuella datorn är följande:

![Inställningar för växlingsfiler på den lokala virtuella datorn](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Vi aktiverar replikering för den virtuella datorn.
2. Vi undantar DB-Disk1 från replikering.

#### <a name="disks-after-failover"></a>Diskar efter redundans

Efter redundansväxlingen har de diskar som sammanfattas i tabellen sammanfattas i den virtuella Azure-datorn.

**Disknamn** | **Gäst-OS disknr** | **Enhetsbeteckning** | **Disk data typ**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Operativsystemdisk
DB-Disk1 | Disk1 | D:\ | Tillfällig lagring<br/><br/> Eftersom D: är den första tillgängliga enhetsbeteckningen i listan tilldelar Azure enhetsbeteckningen D: till den tillfälliga lagringsvolymen.<br/><br/> Alla replikerade diskar har kvar samma enhetsbeteckning.<br/><br/> Eftersom disken G: inte är tillgänglig använder systemet enheten C: för växlings filen.
DB-Disk2 | Disk2 | E:\ | Användardata 1
DB-Disk3 | Disk3 | F:\ | Användardata 2

Inställningarna för växlings fil på den virtuella Azure-datorn är följande:

![Skärm bild av dialog rutan virtuellt minne med C: enhets linjen markerad med inställningen för växlings fil storlek för "system Managed".](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Nästa steg

- Läs mer om rikt linjer för den tillfälliga lagrings disken:
    - [Lär dig mer om](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) att använda SSD i virtuella Azure-datorer för att lagra SQL Server tempdb och buffertpooltillägget
    - [Granska ](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) prestanda metod tips för SQL Server i virtuella Azure-datorer.
- När du har konfigurerat och fått igång distributionen kan du [läsa mer](failover-failback-overview.md) om olika typer av redundansväxlingar.
