---
title: Installera TmaxSoft OpenFrame på Azure Virtual Machines
description: Lär dig hur du konfigurerar en miljö med öppna ramar i Azure som passar för utveckling, demonstrationer, testning eller produktions arbets belastningar.
services: virtual-machines
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 2ca5f0311102c27c1233098a18ff16701b99ea2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950798"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Installera TmaxSoft OpenFrame på Azure

Lär dig hur du konfigurerar en miljö med öppna ramar i Azure som passar för utveckling, demonstrationer, testning eller produktions arbets belastningar. Den här självstudien vägleder dig genom varje steg.

OpenFrame innehåller flera komponenter som skapar stordator-emuleringsklienten på Azure. Exempel: OpenFrame onlinetjänster ersätter stordator mellanprogram, till exempel IBM Customer information Control system (CICS) och OpenFrame batch med dess TJES-komponent, ersätter IBM-stordatorens del system (JES).

OpenFrame fungerar med valfri Relations databas, inklusive Oracle Database, Microsoft SQL Server, IBM DB2 och MySQL. Den här installationen av OpenFrame använder Relations databasen TmaxSoft Tibero. Både OpenFrame och Tibero körs på ett Linux-operativsystem. Den här självstudien installerar CentOS 7,3, men du kan använda andra Linux-distributioner som stöds. Program servern för OpenFrame och Tibero-databasen är installerad på en virtuell dator (VM).

I själv studie kursen lär du dig hur du installerar OpenFrame Suite-komponenter. Vissa måste installeras separat.

Huvuds huvuds RAM komponenter:

- Installations paket som krävs.
- Tibero-databas.
- Open Database Connectivity (ODBC) används av program i OpenFrame för att kommunicera med Tibero-databasen.
- OpenFrame Base, mellanprogram som hanterar hela systemet.
- OpenFrame batch, den lösning som ersätter stordatorens batch-system.
- TACF, en tjänstmall som styr användar åtkomsten till system och resurser.
- Prosorterar ett sorterings verktyg för batch-transaktioner.
- OFCOBOL, en kompilator som tolkar stordator programmets COBOL-program.
- OFASM, en-kompilerare som tolkar stordator programmets sammansatta program.
- OpenFrame Server Type C (OSC), den lösning som ersätter stordator programmets mellan-och IBM-CICS.
- Java Enterprise User Solution (JEUS), en webb program server som är certifierad för Java Enterprise Edition 6.
- OFGW, komponenten OpenFrame gateway som tillhandahåller en 3270-lyssnare.
- OFManager är en lösning som ger openframes drift-och hanterings funktioner i webb miljön.

Andra nödvändiga komponenter för OpenFrame:

- OSI, den lösning som ersätter mellanliggande stordator-och IMS-DOMÄNKONTROLLANTen.
- TJES, den lösning som tillhandahåller stordatorens JES-miljö.
- OFTSAM, den lösning som aktiverar (V) SAM-filer som ska användas i det öppna systemet.
- OFHiDB, den lösning som ersätter stordatorens IMS-databas.
- OFPLI, en-kompilerare som tolkar stordator-/I program.
- PROTRIEVE är en lösning som kör stordator-Easytrieve.
- OFMiner är en lösning som analyserar stordatorernas till gångar och sedan migrerar dem till Azure.

## <a name="architecture"></a>Arkitektur

Följande figur ger en översikt över de arkitektur komponenter för OpenFrame 7,0 som är installerade i den här självstudien:

![Openlist-komponenter](media/openframe-02.png)

## <a name="azure-system-requirements"></a>System krav för Azure

I följande tabell visas kraven för installationen på Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Krav</th><th>Beskrivning</th></tr>
</thead>
<tbody>
<tr><td>Linux-distributioner som stöds i Azure
</td>
<td>
Linux x86 2,6 (32-bitars, 64-bitars)<br/>
Red Hat 7. x<br/>
CentOS 7. x<br/>
</td>
</tr>
<tr><td>Maskinvara
</td>
<td>Kärnor: 2 (minimum)<br/>
Minne: 4 GB (minst)<br/>
Växlings utrymme: 1 GB (minimum)<br/>
Hård disk: 100 GB (minst)<br/>
</td>
</tr>
<tr><td>Valfri program vara för Windows-användare
</td>
<td>SparaTillFil: används i den här guiden för att konfigurera VM-funktioner<br/>
WinSCP: en populär SFTP-klient och FTP-klient som du kan använda<br/>
Sol förmörkelse för Windows: en utvecklings plattform som stöds av TmaxSoft<br/>
(Microsoft Visual Studio stöds inte för tillfället)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Förutsättningar

Planera på att ägna några dagar åt att sätta samman all nödvändig program vara och slutföra alla manuella processer.

Innan du börjar ska du göra följande:

- Hämta installations mediet för OpenFrame från TmaxSoft. Om du är en befintlig TmaxSoft-kund kontaktar du din TmaxSoft-representant för en licensierad kopia. Annars kan du begära en utvärderings version från [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Begär den OpenFrame-dokumentationen genom att skicka e-post till <support@tmaxsoft.com> .

- Skaffa en Azure-prenumeration om du inte redan har en. Du kan också skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Valfritt. Konfigurera en plats-till-plats-VPN-tunnel eller en hopp som begränsar åtkomsten till den virtuella Azure-datorn till de användare som tillåts i din organisation. Det här steget är inte obligatoriskt, men det är en bra metod.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Konfigurera en virtuell dator på Azure för OpenFrame och Tibero

Du kan konfigurera OpenFrame-miljön med olika distributions mönster, men i följande procedur visas hur du distribuerar OpenFrame-program servern och Tibero-databasen på en virtuell dator. I större miljöer och för stora arbets belastningar är det bästa sättet att distribuera databasen separat på den egna virtuella datorn för bättre prestanda.

**Så här skapar du en virtuell dator**

1. Gå till Azure Portal på <https://portal.azure.com> och logga in på ditt konto.

2. Klicka på **Virtuella datorer**.

    ![Resurs lista i Azure Portal](media/vm-01.png)

3. Klicka på **Lägg till**.

    ![Lägg till alternativ i Azure Portal](media/vm-02.png)

4. Till höger om **operativ system** klickar du på **mer**.

     ![Fler alternativ i Azure Portal](media/vm-03.png)

5. Klicka på **CentOS-baserad 7,3** om du vill följa den här genom gången exakt, eller så kan du välja en annan Linux-distribution som stöds.

     ![Alternativ för operativ system i Azure Portal](media/vm-04.png)

6. I **grundläggande** inställningar anger du **namn**, **användar namn**, **Autentiseringstyp**, **prenumeration** (betala per användning är betalnings sätten AWS) och **resurs grupp** (Använd en befintlig eller skapa en TmaxSoft-grupp).

7. När du är klar (inklusive det offentliga/privata nyckel paret för **Autentiseringstyp**) klickar du på **Skicka**.

> [!NOTE]
> Om du använder en offentlig SSH-nyckel för **Autentiseringstyp** kan du läsa stegen i nästa avsnitt för att generera det offentliga/privata nyckel paret och sedan återuppta stegen här.

### <a name="generate-a-publicprivate-key-pair"></a>Skapa ett offentligt/privat nyckel par

Om du använder ett Windows-operativsystem behöver du PuTTYgen för att generera ett offentligt/privat nyckel par.

Den offentliga nyckeln kan delas fritt, men den privata nyckeln bör hållas helt hemlig och ska aldrig delas med en annan part. När du har genererat nycklarna måste du klistra in den **offentliga SSH-nyckeln** i konfigurationen, och ladda upp den till den virtuella Linux-datorn. Den lagras inuti behöriga \_ nycklar i/.ssh- \~ katalogen för användar kontots Hem Katalog. Den virtuella Linux-datorn kan sedan identifiera och verifiera anslutningen när du har angett den associerade **privata SSH-nyckeln** i SSH-klienten (i vårt exempel SparaTillFil).

När du ger nya individer åtkomst till den virtuella datorn: 

- Varje ny person genererar egna offentliga/privata nycklar med PuTTYgen.
- Enskilda personer lagrar sina egna privata nycklar separat och skickar informationen om den offentliga nyckeln till administratören för den virtuella datorn.
- Administratören klistrar in innehållet i den offentliga nyckeln i filen med \~ /.ssh/Authorized- \_ nycklar.
- Den nya personen ansluter via SparaTillFil.

**Så här skapar du ett offentligt/privat nyckel par**

1.  Ladda ned PuTTYgen från <https://www.putty.org/> och installera det med alla standardinställningar.

2.  Öppna PuTTYgen genom att leta upp katalogen SparaTillFil-installation i mappen C: \\ programfiler \\ .

    ![SparaTillFil-gränssnitt](media/puttygen-01.png)

3.  Klicka på **Generera**.

    ![Skärm bild som visar dialog rutan för dialog rutan SparaTillFil-nyckel och markerar knappen generera.](media/puttygen-02.png)

4.  När du har genererat, sparar du både den offentliga och den privata nyckeln. Klistra in innehållet i den offentliga nyckeln i avsnittet **offentlig nyckel för SSH** i fönstret **skapa \> grundläggande information om virtuella datorer** (visas i steg 6 och 7 i föregående avsnitt).

    ![Dialog rutan för SparaTillFil-nyckel Generator](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurera VM-funktioner

1. I Azure Portal går du till bladet **Välj en storlek** och väljer de maskin varu inställningar för Linux Machine som du vill använda. *Minimi* kraven för att installera både Tibero och OpenFrame är 2 processorer och 4 GB RAM-minne, vilket visas i den här exempel installationen:

    ![Skapa en virtuell dator – grunder](media/create-vm-01.png)

2. Klicka på **3 inställningar** och Använd standardinställningarna för att konfigurera valfria funktioner.
3. Granska din betalnings information.

    ![Skapa virtuell dator – köp](media/create-vm-02.png)

4. Skicka in dina val. Azure börjar distribuera den virtuella datorn. Den här processen tar vanligt vis några minuter.

5. När den virtuella datorn distribueras visas instrument panelen med alla inställningar som valdes under konfigurationen. Anteckna den **offentliga IP-adressen**.

    ![Tmax på Azure-instrumentpanelen](media/create-vm-03.png)

6. Öppna PuTTY.

7. För **värdnamn** skriver du ditt användar namn och den offentliga IP-adress som du kopierade. Till exempel **username \@ publicip**.

    ![Skärm bild som visar dialog rutan SparaTillFil-konfiguration och markerar fältet värd namn (eller IP-adress).](media/putty-01.png)

8. Klicka på **anslutningens \> SSH- \> autentisering** i rutan **kategori** . Ange sökvägen till den **privata nyckel** filen.

    ![Dialog rutan SparaTillFil-konfiguration](media/putty-02.png)

9. Starta fönstret SparaTillFil genom att klicka på **Öppna** . Om det lyckas är du ansluten till din nya virtuella CentOS-dator som körs på Azure.

10. Om du vill logga in som rot användare skriver du **sudo bash**.

    ![Inloggning av rot användare i kommando fönstret](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Konfigurera miljön och paket

Nu när den virtuella datorn har skapats och du är inloggad måste du utföra några installations steg och installera de nödvändiga för installations paketen.

1. Mappa namnet **ofdemo** till den lokala IP-adressen genom att använda vi för att redigera värd filen ( `vi /etc/hosts` ). Under förutsättning att vår IP-adress är 192.168.96.148 ofdemo är detta före ändringen:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Detta är efter ändringen:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Skapa grupper och användare:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Ändra lösen ordet för användaren oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Uppdatera kernel-parametrarna i/etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Uppdatera kernel-parametrarna dynamiskt utan omstart:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Hämta de nödvändiga paketen: kontrol lera att servern är ansluten till Internet, ladda ned följande paket och installera dem sedan:

     - dos2unix
     - glibc
     - glibc. i686 glibc. x86 \_ 64
     - libaio
     - ncurses

          > [!NOTE]
          > När du har installerat ncurses-paketet skapar du följande symboliska länkar:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c + +
     - libaio-devel. x86 \_ 64
     - strace
     - ltrace
     - gdb

7. Om du installerar Java RPM gör du följande:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Installera Tibero-databasen

Tibero tillhandahåller flera viktiga funktioner i OpenFrame-miljön på Azure:

- Tibero används som OpenFrame Internal data Store för olika system funktioner.
- VSAM-filer, inklusive KSDS, RRDS och ESDS, använder Tibero-databasen internt för data lagring.
- Data lagringen för TACF lagras i Tibero.
- Katalog informationen för OpenFrame lagras i Tibero.
- Tibero-databasen kan användas som ersättning för IBM DB2 för att lagra program data.

**Så här installerar du Tibero**

1. Kontrol lera att den binära installations filen Tibero finns och granska versions numret.
2. Kopiera Tibero-programvaran till Tibero-användarkontot (oframe). Exempel:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Öppna. bash- \_ profilen i vi ( `vi .bash_profile` ) och klistra in följande i den:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Om du vill köra bash-profilen går du till kommando tolken och skriver:

    ```
    source .bash_profile
    ```

5. Generera tips filen (en konfigurations fil för Tibero) och öppna den sedan i vi. Exempel:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Ändra \$ TB \_ Home/client/config/tbdsn. TBR och Lägg 127.0.0.1 i stället oflocalhost som visas:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Skapa databasen. Följande utdata visas:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Om du vill återvinna Tibero måste du först stänga av den med `tbdown` kommandot. Exempel:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Starta Tibero med `tbboot` . Exempel:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Skapa ett tabell utrymme genom att öppna databasen med hjälp av SYS-användare (sys/Tmax) och sedan skapa nödvändiga tabell utrymmen för standard volymen och TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Skriv nu följande SQL-kommandon:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Starta Tibero och kontrol lera att Tibero-processerna körs:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Utdata:

![Tibero-utdata](media/tibero-01.png)

## <a name="install-odbc"></a>Installera ODBC

Program i OpenFrame kommunicerar med Tibero-databasen med hjälp av det ODBC-API som tillhandahålls av projektet unixODBC med öppen källkod.

Så här installerar du ODBC:

1. Kontrol lera att installations filen unixODBC-2.3.4. tjär. gz finns eller Använd `wget unixODBC-2.3.4.tar.gz` kommandot. Exempel:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Zippa upp binärfilen. Exempel:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navigera till unixODBC-2.3.4-katalogen och generera make med hjälp av kontrollens maskin information. Exempel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     UnixODBC installeras som standard i/usr/local, så `--prefix` skickar ett värde för att ändra platsen. På samma sätt installeras konfigurationsfiler i volymen/etc som standard, så `--sysconfdir` överför värdet för önskad plats.

4. Kör make: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Kopiera den körbara filen i program katalogen efter kompilering. Exempel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Använd vi för att redigera bash-profilen ( `vi ~/.bash_profile` ) och Lägg till följande:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Använd ODBC. Redigera följande filer på motsvarande sätt. Exempel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Skapa en symbolisk länk och verifiera Tibero-databas anslutningen:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Följande utdata visas:

![ODBC-utdata som visar anslutna till SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Installera OpenFrame Base

Den grundläggande program servern installeras före de enskilda tjänster som OpenFrame använder för att hantera systemet på Azure, inklusive transaktions hanterings serverns processer.

**Så här installerar du OpenFrame Base**

1. Kontrol lera att Tibero-installationen har slutförts och kontrol lera att det finns en konfigurations fil för OpenFrame- \_ Base7 \_ 0 \_ Linux \_ x86 \_ 64. bin och bas. Properties.

2. Uppdatera bash-profilen med följande Tibero information:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Kör bash-profilen:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Se till att Tibero-processerna körs. Exempel:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Grund](media/base-01.png)

     > [!IMPORTANT]
     > Kontrol lera att du startar Tibero före installationen.

5. Generera licens på [TechNet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) och Lägg till batch-, TACF-, OSC-licenser i den aktuella mappen:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Ladda ned filerna OpenFrame Base Binary och Base. Properties:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Kör installations programmet med hjälp av filen Base. Properties. Exempel:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    När det är klart är meddelandet installationen slutförd diplayed.

8. Verifiera bas katalog strukturen för OpenFrame med `ls -ltr` kommandot. Exempel:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Börja med att öppna ram-bas:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![utdata för tmboot-kommandot](media/base-02.png)

10. Kontrol lera att process statusen är klar med kommandot tmadmin i si. RDY visas i kolumnen **status** för var och en av processerna:

     ![utdata för tmadmin-kommandot](media/base-03.png)

11. Stäng av OpenFrame-bas:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Installera OpenFrame batch

Batch-bildruta består av flera komponenter som simulerar stordatorer och används för att köra batch-jobb på Azure.

**Installera batch**

1. Kontrol lera att den grundläggande installationen lyckades och kontrol lera att OpenFrame \_ Batch7 \_ 0 \_ Fix2 \_ MVS \_ Linux \_ x86 \_ 64. bin Installer-filen och batch. Properties-konfigurationsfilen finns:

2. I kommando tolken skriver `vi batch.properties` du för att redigera batch. Properties-filen med hjälp av vi.

3. Ändra parametrarna enligt följande:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Kör batch-installationen genom att skriva följande i kommando tolken:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. När installationen är klar startar du de installerade OpenFrame-paketen genom att skriva `tmboot` i kommando tolken.

    ![tmboot-utdata](media/tmboot-01.png)

6. Skriv `tmadmin` vid kommando tolken för att kontrol lera processen för OpenFrame.

    ![Tmax-administratörs skärm](media/tmadmin-01.png)

7. Kör följande kommandon:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Använd `tmdown` kommandot för att starta och stänga batch:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Installera TACF

TACF Manager är en OpenFrame service-modul som styr användar åtkomsten till system och resurser via RACF-säkerhet.

**Så här installerar du TACF**

1. Kontrol lera att OpenFrame \_ Tacf7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin Installer-filen och TACF. Properties-konfigurationsfilen finns.
2. Se till att batch-installationen lyckades och Använd sedan vi för att öppna filen TACF. Properties ( `vi tacf.properties` ).
3. Ändra parametrarna för TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. När du har slutfört TACF-installationsprogrammet använder du miljövariablerna TACF. Skriv följande i kommandotolken:

     ```
     source \~/.bash\_profile
     ```

5. Kör installations programmet för TACF. Skriv följande i kommandotolken:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Utdata ser ut ungefär så här:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. I kommando tolken skriver `tmboot` du för att starta om OpenFrame. Utdata ser ut ungefär så här:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Kontrol lera att process statusen är klar med `tmadmin` i `si` kommandot. Exempel:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     I kolumnen **status** visas RDY:

    ![RDY i kolumnen Status](media/tmboot-02.png)

8. Kör följande kommandon:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Stäng av servern med hjälp av `tmdown` kommandot. Utdata ser ut ungefär så här:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Installera prosorteringen

Prosorteringen är ett verktyg som används i batch-transaktioner för att sortera data.

**Installera prosorteringen**

1. Kontrol lera att batch-installationen lyckades och kontrol lera att installations filen för **prosort-bin-prosorten \_ 2sp3-linux64-2123-opt. tjär. gz** finns.

2. Kör installations programmet med hjälp av egenskaps filen. Skriv följande i kommandotolken:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Flytta prosorterings katalogen till Start platsen. Skriv följande i kommandotolken:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Skapa en licens under katalog och kopiera licens filen där. Exempel:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Öppna bash. Profile i vi ( `vi .bash_profile` ) och uppdatera den på följande sätt:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Om du vill köra bash-profilen skriver du följande i kommando tolken: `. .bash_profile`

7. Skapa konfigurations filen. Exempel:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Skapa den symboliska länken. Exempel:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Verifiera prosorterings installationen genom att köra `prosort -h` kommandot. Exempel:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Installera OFCOBOL

OFCOBOL är OpenFrame compiler som tolkar stordator programmets COBOL-program. 

**Så här installerar du OFCOBOL**

1. Kontrol lera att batch-/online-installationen har slutförts och kontrol lera sedan att filen OpenFrame \_ COBOL3 \_ 0 \_ 40 \_ Linux \_ x86 \_ 64. bin finns.

2. Om du vill köra installations programmet för OFCOBOL skriver du följande i kommando tolken:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Läs licens avtalet och tryck på RETUR för att fortsätta.

4. Godkänn licens avtalet. När installationen är klar visas följande:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Öppna bash-profilen i vi ( `vi .bash_profile` ) och kontrol lera att har uppdaterats med OFCOBOL-variabler.
6. Kör bash-profilen. Skriv följande i kommandotolken:

     ```
      source ~/.bash_profile
     ```

7. Kopiera OFCOBOL-licensen till den installerade mappen. Exempel:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Gå till konfigurations filen OpenFrame tjclrun. conf och öppna den i vi. Exempel:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Här är avsnittet SYSLIB före ändringen:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Här är SYSLIB-avsnittet efter ändringen:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Granska filen OpenFrame \_ COBOL \_ InstallLog. log i vi och kontrol lera att det inte finns några fel. Exempel:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Använd `ofcob --version` kommandot och granska versions numret för att verifiera installationen. Exempel:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Starta om OpenFrame med `tmdown/tmboot` kommandot.

## <a name="install-ofasm"></a>Installera OFASM

OFASM är en OpenFrame-kompilator som tolkar stordatorernas sammansatta program.

**Så här installerar du OFASM**

1. Kontrol lera att batch-/online-installationen har slutförts och kontrol lera att \_ filen OpenFrame ASM3 \_ 0 \_ Linux \_ x86 \_ 64. bin är tillgänglig.

2. Kör installations programmet. Exempel:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Läs licens avtalet och tryck på RETUR för att fortsätta.
4. Godkänn licens avtalet.
5. Kontrol lera att bash-profilen har uppdaterats med OFASM-variabler. Exempel:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Öppna konfigurations filen OpenFrame tjclrun. conf i vi och redigera den på följande sätt:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Här är avsnittet [SYSLIB] *innan* ändringen:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Här är avsnittet [SYSLIB] *efter* ändringen:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Öppna filen OpenFrame \_ ASM \_ InstallLog. log i vi och kontrol lera att det inte finns några fel. Exempel:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Starta om OpenFrame genom att utfärda något av följande kommandon:

     ```
     tmdown / tmboot
     ```

     eller

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Installera OSC

OSC är den OpenFrame-miljö som liknar IBM-CICS som stöder höghastighets OLTP-transaktioner och andra hanterings funktioner.

**Installera OSC**

1. Kontrol lera att den grundläggande installationen har slutförts och kontrol lera sedan att det finns en \_ konfigurations fil för OpenFrame OSC7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin och filen OSC. Properties.
2. Redigera följande parametrar i filen OSC. Properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Kör installations programmet med hjälp av egenskaps filen som visas:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     När du är klar visas meddelandet "installationen har slutförts".

4. Kontrol lera att bash-profilen har uppdaterats med OSC-variabler.
5. Granska filen OpenFrame \_ OSC7 \_ 0 \_ Fix2 \_ InstallLog. log. Den bör se ut ungefär så här:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Använd vi för att öppna konfigurations filen ofsys. SEQ. Exempel:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. \# \# Redigera parametrarna som visas i bas-och batch-avsnitten.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Kopiera licens filen. Exempel:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. För att starta och stänga av OSC, initiera det CICS regions delade minnet genom att skriva `osctdlinit OSCOIVP1` i kommando tolken.

10. Kör `oscboot` för att starta OSC. Utdata ser ut ungefär så här:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Om du vill kontrol lera att process statusen är klar använder du `tmadmin` kommandot i si. Alla processer bör Visa RDY i kolumnen **status** .

    ![Processer som visar RDY](media/tmadmin-02.png)

12. Stäng av OSC med `oscdown` kommandot.

## <a name="install-jeus"></a>Installera JEUS

JEUS (Java Enterprise User Solution) tillhandahåller presentations lagret i OpenFrame-webbappens Server.

Innan du installerar JEUS installerar du Apache Ant-paketet som tillhandahåller de bibliotek och kommando rads verktyg som behövs för att installera JEUS.

**Installera Apache Ant**

1. Hämta Ant binärt med `wget` kommandot. Exempel:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Använd `tar` verktyget för att extrahera den binära filen och flytta den till en lämplig plats. Exempel:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. För effektivitet skapar du en symbolisk länk:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Öppna bash-profilen i vi ( `vi .bash_profile` ) och uppdatera den med följande variabler:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Använd modifierad miljö variabel. Exempel:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Så här installerar du JEUS**

1. Expandera installations programmet med hjälp av `tar` verktyget. Exempel:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Skapa en **Jeus** -mapp ( `mkdir jeus7` ) och zippa upp binärfilen.
3. Ändra till **installations** katalogen (eller Använd parametern JEUS för din egen miljö). Exempel:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Kör `ant clean-all` innan du utför bygget. Utdata ser ut ungefär så här:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Gör en säkerhets kopia av filen domän-config-Template. Properties. Exempel:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Öppna filen Domain-config-Template. properties i vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Ändra `jeus.password=jeusadmin nodename=Tmaxsoft` till `jeus.password=tmax1234 nodename=ofdemo`

8. Kör `ant install` kommandot för att bygga JEUS.
9.  Uppdatera \_ profil filen. bash med JEUS-variablerna som visas:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Kör bash-profilen. Exempel:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Valfritt*. Skapa ett alias för enkel avstängning och start av JEUS-komponenter:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Verifiera installationen genom att starta domän administratörs servern som visas:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verifiera efter webb inloggning med hjälp av syntaxen:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     <http://192.168.92.133:9736/webadmin/login.>Inloggnings skärmen visas till exempel:
    
     ![JEUS webadmin inloggnings skärm](media/jeus-01.png)

     > [!NOTE]
     > Om du får problem med port säkerhet öppnar du Port 9736 eller inaktiverar brand väggen ( `systemctl stop firewall` ).

14. Om du vill ändra värdnamn för server1 klickar du på **lås & redigera** och klickar sedan på **server1**. I Server-fönstret ändrar du värdnamn enligt följande:

    1.  Ändra **nodnamn** till **ofdemo**.
    2.  Klicka på **OK** på höger sida av fönstret.
    3.  Klicka på **Verkställ ändringar** på den nedre vänstra sidan i fönstret och för Beskrivning anger du *hostname-ändring*.

    ![JEUS webadmin-skärmen](media/jeus-02.png)

15. Kontrol lera att konfigurationen har slutförts på bekräftelse skärmen.

    ![skärmen jeus_domain Server](media/jeus-03.png)

16. Starta den hanterade Server processen "server1" med följande kommando:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installera OFGW

OFGW är en OpenFrame-gateway som stöder kommunikation mellan 3270-termin Ale mula torn och OSI-basen och hanterar sessioner mellan termin Ale mula torn och OSI.

**Så här installerar du OFGW**

1. Kontrol lera att JEUS har installerats och kontrol lera att \_ filen OFGW7 0 \_ 1 \_ allmän. bin-installationsprogrammet finns.
2. Kör installations programmet. Exempel:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Använd följande platser för motsvarande prompter:
     -   JEUS Hem Katalog
     -   JEUS domän namn
     -   JEUS Server namn
     -   Tibero-drivrutin
     -   Tmax Node ID ofdemo

4. Godkänn resten av standardinställningarna och tryck sedan på RETUR för att avsluta installations programmet.

5. Kontrol lera att URL: en för OFGW fungerar som förväntat:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Följande skärm visas:

    ![OpenFrame-webterminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Installera OFManager

OFManager tillhandahåller drift-och hanterings funktioner för OpenFrame i webb miljön.

**Så här installerar du OFManager**

1. Kontrol lera att \_ filen OFManager7 Generic. bin Installer finns.
2. Kör installations programmet. Exempel:

     ```
     OFManager7_Generic.bin
     ```

3.  Tryck på RETUR för att fortsätta och godkänn sedan licens avtalet.
4.  Välj installationsmappen.
5.  Acceptera alla standardinställningar.
6.  Välj Tibero som databas.
7.  Tryck på RETUR för att avsluta installations programmet.
8.  Kontrol lera att URL: en för OFManager fungerar som förväntat:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Start skärmen visas:

![Tmax OpenFrame Manager inloggnings skärm](media/ofmanager-01.png)

Som slutför installationen av komponenten OpenFrame.

## <a name="next-steps"></a>Nästa steg

Om du överväger en stordator-migrering är vårt expanderande partner eko system tillgängligt för dig. Detaljerad vägledning om hur du väljer en partner lösning finns i [Platform modernisering Alliance](https://datamigration.microsoft.com/).

-   [Kom igång med Azure](../../../../index.yml)
-   [Dokumentation om Host Integration Server (hans)](/host-integration-server/)
-   [Guide för ökning och växling i Azure Virtual Data Center](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
