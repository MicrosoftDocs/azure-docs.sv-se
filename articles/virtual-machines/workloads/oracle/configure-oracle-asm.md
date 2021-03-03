---
title: Konfigurera Oracle ASM på en virtuell Azure Linux-dator | Microsoft Docs
description: Få snabbt till gång till Oracle ASM i Azure-miljön.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: b180a0f148db5222a288958724ad248acd18b565
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673862"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Konfigurera Oracle ASM på en virtuell Azure Linux-dator  

Med virtuella Azure-datorer får du en fullständigt konfigurerbar och flexibel datormiljö. Den här självstudien omfattar grundläggande distribution av virtuella Azure-datorer tillsammans med installation och konfiguration av hantering av Oracle (automatisk lagring).  Lär dig att:

> [!div class="checklist"]
> * Skapa och Anslut till en Oracle Database virtuell dator
> * Installera och konfigurera Oracle-automatiserad lagrings hantering
> * Installera och konfigurera infrastruktur för Oracle-rutnät
> * Initiera en Oracle ASM-installation
> * Skapa en Oracle DB som hanteras av ASM


Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Förbereda miljön

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group) . En Azure-resurs grupp är en logisk behållare där Azure-resurser distribueras och hanteras. I det här exemplet är en resurs grupp med namnet *myResourceGroup* i regionen *östra* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator baserat på Oracle Database avbildningen och konfigurera den så att den använder Oracle ASM använder du kommandot [AZ VM Create](/cli/azure/vm) . 

I följande exempel skapas en virtuell dator med namnet myVM som är en Standard_DS2_v2 storlek med fyra anslutna data diskar på 50 GB vardera. Om de inte redan finns på standard platsen för nycklar skapas även SSH-nycklar.  Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

När du har skapat den virtuella datorn visar Azure CLI information som liknar följande exempel. Anteckna värdet för `publicIpAddress` . Du använder den här adressen för att få åtkomst till den virtuella datorn.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Anslut till VM:en

Använd följande kommando för att skapa en SSH-session med den virtuella datorn och konfigurera ytterligare inställningar. Ersätt IP-adressen med `publicIpAddress` värdet för den virtuella datorn.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installera Oracle ASM

Slutför följande steg för att installera Oracle ASM. 

Mer information om hur du installerar Oracle ASM finns i [Oracle ASMLib Downloads for Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Du måste logga in som rot för att kunna fortsätta med ASM-installationen:

   ```bash
   sudo su -
   ```
   
2. Kör följande ytterligare kommandon för att installera Oracle ASM-komponenter:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Kontrol lera att Oracle ASM är installerat:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Utdata från det här kommandot ska visa följande komponenter:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM kräver vissa användare och roller för att fungera korrekt. Följande kommandon skapar de nödvändiga användar kontona och grupperna: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Verifiera att användare och grupper har skapats korrekt:

   ```bash
   id grid
   ```

    Utdata från det här kommandot ska visa följande användare och grupper:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Skapa en mapp för användar *rutnät* och ändra ägare:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Konfigurera Oracle ASM

I den här självstudien är standard användaren *rutnät* och standard gruppen är *asmadmin*. Se till att *Oracle* -användaren är en del av asmadmin-gruppen. Utför följande steg för att konfigurera din Oracle ASM-installation:

1. Konfigurationen av driv rutinen för Oracle ASM-biblioteket omfattar att definiera standard användaren (rutnät) och standard gruppen (asmadmin) samt att konfigurera enheten så att den startar vid start (Välj y) och för att söka efter diskar vid start (Välj y). Du måste besvara prompterna från följande kommando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Utdata från det här kommandot bör se ut ungefär så här, och det går inte att svara på frågor som ska besvaras.

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Visa disk konfigurationen:

   ```bash
   cat /proc/partitions
   ```

   Utdata från det här kommandot bör se ut ungefär som i följande lista över tillgängliga diskar

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Formatera disk */dev/SDC* genom att köra följande kommando och besvara prompterna med:
   - *n* för ny partition
   - *p* för primär partition
   - *1* om du vill välja den första partitionen
   - Tryck på `enter` den första standard cylindern
   - Tryck på `enter` den sista standard cylindern
   - Tryck på *w* för att skriva ändringarna i partitionstabellen  

   ```bash
   fdisk /dev/sdc
   ```
   
   Med de svar som anges ovan bör utdata för `fdisk` kommandot se ut så här:

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Upprepa föregående `fdisk` kommando för `/dev/sdd` , `/dev/sde` och `/dev/sdf` .

5. Kontrol lera disk konfigurationen:

   ```bash
   cat /proc/partitions
   ```

   Kommandots utdata bör se ut så här:

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Kontrol lera status för Oracle ASM-tjänsten och starta Oracle ASM-tjänsten:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Kommandots utdata bör se ut så här:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Skapa Oracle ASM-diskar:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   Kommandots utdata bör se ut så här:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Visa lista över Oracle ASM-diskar:

   ```bash
   service oracleasm listdisks
   ```

   Kommandots utdata bör inte visa följande Oracle ASM-diskar:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Ändra lösen orden för rot-, Oracle-och Grid-användarna. **Anteckna dessa nya lösen ord** när du använder dem senare under installationen.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Ändra behörighet för mappen:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Ladda ned och Förbered infrastrukturen för Oracle-rutnät

Slutför följande steg för att ladda ned och förbereda Oracle Grid Infrastructure-programmet:

1. Hämta Oracle Grid-infrastruktur från [sidan för Oracle ASM-hämtning](https://www.oracle.com/database/technologies/oracle19c-linux-downloads.html). 

   Under nedladdningen **Oracle Database 12C Release 1 Grid Infrastructure (12.1.0.2.0) för Linux x86-64** laddar du ned de två. zip-filerna.

2. När du har hämtat zip-filerna till klient datorn kan du använda Secure Copy Protocol (SCP) för att kopiera filerna till den virtuella datorn:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Använda SSH tillbaka till din Oracle VM i Azure för att flytta. zip-filerna till mappen/opt. Ändra sedan ägaren till filerna:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Zippa upp filerna. (Installera Linux-uppackning-verktyget om det inte redan är installerat.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Ändra behörighet:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Uppdatera konfigurerat växlings utrymme. Komponenter i Oracle-rutnätet kräver minst 6,8 GB växlings utrymme för att installera rutnätet. Standard växlings fil storleken för Oracle Linux avbildningar i Azure är bara 2048 MB. Du måste öka `ResourceDisk.SwapSizeMB` `/etc/waagent.conf` filen och starta om WALinuxAgent-tjänsten för att de uppdaterade inställningarna ska börja gälla. Eftersom det är en skrivskyddad fil måste du ändra fil behörigheter för att aktivera skriv åtkomst.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Sök efter `ResourceDisk.SwapSizeMB` och ändra värdet till **8192**. Du måste klicka `insert` för att ange infognings läge, skriva in värdet **8192** och sedan på `esc` för att återgå till kommando läge. Skriv och tryck på om du vill skriva ändringarna och avsluta filen `:wq` `enter` .
   
   > [!NOTE]
   > Vi rekommenderar starkt att du alltid använder `WALinuxAgent` för att konfigurera växlings utrymme så att det alltid skapas på den lokala tillfälliga disken (temporär disk) för bästa prestanda. Mer information finns i [så här lägger du till en växlings fil i Linux Azure Virtual Machines](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Förbered din lokala klient och virtuella dator för att köra begäran om X11
Att konfigurera Oracle ASM kräver ett grafiskt gränssnitt för att slutföra installationen och konfigurationen. Vi använder begäran om x11-protokollet för att under lätta installationen. Om du använder ett klient system (Mac eller Linux) som redan har begäran om x11-funktioner aktiverade och konfigurerade – kan du hoppa över konfigurationen och installationen är exklusiv för Windows-datorer. 

1. [Hämta filen SparaTillFil](https://www.putty.org/) och [Hämta Xming](https://xming.en.softonic.com/) till din Windows-dator. Du måste slutföra installationen av båda dessa program med standardvärdena innan du fortsätter.

2. När du har installerat SparaTillFil kan du öppna en kommando tolk, ändra till mappen SparaTillFil (till exempel C:\Program Files\PuTTY) och köra för `puttygen.exe` att generera en nyckel.

3. I SparaTillFil-nyckel Generator:
   
   1. Generera en nyckel genom att välja `Generate` knappen.
   2. Kopiera innehållet i nyckeln (Ctrl + C).
   3. Välj knappen `Save private key`.
   4. Ignorera varningen om att skydda nyckeln med en lösen fras och välj sedan `OK` .

   ![Skärm bild av en nyckels generator för SparaTillFil](./media/oracle-asm/puttykeygen.png)

4. Kör följande kommandon i den virtuella datorn:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Skapa en fil som heter `authorized_keys`. Klistra in innehållet i nyckeln i den här filen och spara sedan filen.

   > [!NOTE]
   > Nyckeln måste innehålla strängen `ssh-rsa` . Dessutom måste innehållet i nyckeln vara en enskild textrad.
   >  

6. Starta SparaTillFil på klient systemet. I rutan **kategori** går du till **Connection**  >  **SSH**  >  **auth**. I rutan **privat nyckel fil för autentisering** , bläddra till den nyckel som du skapade tidigare.

   ![Skärm bild av alternativen för SSH-autentisering](./media/oracle-asm/setprivatekey.png)

7. I fönstret **kategori** går du till **anslutnings**-  >  **SSH**-  >  **begäran om x11**. Markera kryss rutan **aktivera vidarebefordran av begäran om x11** .

   ![Skärm bild av alternativen för vidarebefordring av SSH-begäran om X11](./media/oracle-asm/enablex11.png)

8. I fönstret **kategori** går du till **session**. Ange din Oracle ASM VM `<publicIPaddress>` i dialog rutan värdnamn, Fyll i ett nytt `Saved Session` namn och klicka sedan på på `Save` .  När du har sparat `open` det klickar du på för att ansluta till din virtuella Oracle ASM-dator.  Första gången du ansluter får du en varning om att fjärrdatorn inte är cachelagrad i registret. Klicka på för `yes` att lägga till den och fortsätta.

   ![Skärm bild av alternativen för SparaTillFil-sessioner](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installera Oracle Grid-infrastruktur

Slutför följande steg för att installera Oracle Grid-infrastrukturen:

1. Logga in som **rutnät**. (Du bör kunna logga in utan att behöva ange ett lösen ord.) 

   > [!NOTE]
   > Om du kör Windows kontrollerar du att du har startat Xming innan du påbörjar installationen.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   12C version 1 av Oracle Grid Infrastructure öppnas. (Det kan ta några minuter innan installations programmet startar.)

2. På sidan **Välj installations alternativ** väljer du **Installera och konfigurera Oracle Grid-infrastruktur för en fristående server**.

   ![Skärm bild av installations alternativ sidan Välj installations alternativ](./media/oracle-asm/install01.png)

3. På sidan **Välj produkt språk** kontrollerar du att **engelska** eller språket som du vill ha är markerat.  Klicka på `next`.

4. På sidan **skapa ASM-disk grupp** :
   - Ange ett namn för disk gruppen.
   - Under **redundans** väljer du **extern**.
   - Under **storlek på allokeringsenhet** väljer du **4**.
   - Under **Lägg till diskar** väljer du **ORCLASMSP**.
   - Klicka på `next`.

5. På sidan **Ange ASM-lösenord** väljer du alternativet **Använd samma lösen ord för dessa konton** och anger ett lösen ord.

   ![Skärm bild av installations programmet anger du sidan för ASM-lösenord](./media/oracle-asm/install04.png)

6. På sidan **Ange hanterings alternativ** har du möjlighet att konfigurera fyr Kants moln kontroll. Vi hoppar över det här alternativet `next` . Fortsätt genom att klicka. 

7. Använd standardinställningarna på sidan **privilegierade operativ system grupper** . Klicka `next` om du vill fortsätta.

8. På sidan **Ange installations plats** använder du standardinställningarna. Klicka `next` om du vill fortsätta.

9. På sidan **skapa inventering** ändrar du lager katalogen till `/u01/app/grid/oraInventory` . Klicka `next` om du vill fortsätta.

   ![Skärm bild av installations programmets inventerings sida](./media/oracle-asm/install08.png)

10. Markera kryss rutan **Kör konfigurations skript automatiskt** på sidan **konfiguration av rot skript** . Välj sedan alternativet **Använd autentiseringsuppgifter för rot användare** och ange lösen ordet för rot användaren.

    ![Skärm bild av installations programmets konfigurations sida för rot skript körning](./media/oracle-asm/install09.png)

11. På sidan **utför nödvändiga kontroller Miss söker** den aktuella installationen med fel. Detta är ett förväntat beteende. Välj `Fix & Check Again`.

12. I dialog rutan **korrigerings skript** klickar du på `OK` .

13. Granska de valda inställningarna på sidan **Sammanfattning** och klicka sedan på `Install` .

    ![Skärm bild av installations programmets sammanfattnings sida](./media/oracle-asm/install12.png)

14. En varnings dialog ruta visas som informerar dig om att konfigurations skript måste köras som en privilegie rad användare. Klicka `Yes` om du vill fortsätta.

15. På sidan **Slutför** klickar du på `Close` för att slutföra installationen.

## <a name="set-up-your-oracle-asm-installation"></a>Konfigurera din Oracle ASM-installation

Utför följande steg för att konfigurera din Oracle ASM-installation:

1. Se till att du fortfarande är inloggad som **rutnät** från din begäran om x11-session. Du kan behöva trycka `enter` för att återskapa terminalen. Starta sedan Oracle-konfigurations assistenten för automatisk lagrings hantering:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Konfigurations assistenten för Oracle ASM öppnas.

2. I dialog rutan **Konfigurera ASM: disk grupper** klickar du på `Create` knappen och sedan på `Show Advanced Options` .

3. I dialog rutan **skapa disk grupp** :

   - Ange disk gruppens namn **data**.
   - Under **Välj medlems diskar** väljer du **ORCL_DATA** och **ORCL_DATA1**.
   - Under **storlek på allokeringsenhet** väljer du **4**.
   - `ok`Skapa disk gruppen genom att klicka på.
   - `ok`Stäng bekräftelse fönstret genom att klicka på.

   ![Skärm bild av dialog rutan skapa disk grupp](./media/oracle-asm/asm02.png)

4. I dialog rutan **Konfigurera ASM: disk grupper** klickar du på `Create` knappen och sedan på `Show Advanced Options` .

5. I dialog rutan **skapa disk grupp** :

   - Ange disk grupp namnet **FRA**.
   - Välj **extern (ingen)** under **redundans**.
   - Under **Välj medlems diskar** väljer du **ORCL_FRA**.
   - Under **storlek på allokeringsenhet** väljer du **4**.
   - `ok`Skapa disk gruppen genom att klicka på.
   - `ok`Stäng bekräftelse fönstret genom att klicka på.

   ![Skärm bild av dialog rutan skapa disk grupp och markerar alternativet externt (ingen).](./media/oracle-asm/asm04.png)

6. Välj **Avsluta** för att stänga konfigurations assistenten för ASM.

   ![Skärm bild av dialog rutan konfigurera ASM: disk grupper med knappen Avsluta](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Skapa databasen

Oracle Database-programvaran är redan installerad på Azure Marketplace-avbildningen. Utför följande steg för att skapa en databas:

1. Växla användare till Oracle superanvändare och initiera lyssnaren för loggning:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Databas konfigurations assistenten öppnas.

2. På sidan **databas åtgärd** klickar du på `Create Database` .

3. På sidan **skapande läge** :

   - Ange ett namn för databasen.
   - För **lagrings typ** ser du till att **Automatisk lagrings hantering (ASM)** är markerat.
   - Använd standard platsen för den föreslagna ASM-platsen för **databasfiler**.
   - För **snabb återställnings område** använder du standard platsen som rekommenderas av ASM.
   - Ange ett **Administratörs lösen ord** och **Bekräfta lösen ordet**.
   - Se till `create as container database` har valts.
   - Ange ett `pluggable database name` värde.

4. På sidan **Sammanfattning** granskar du de valda inställningarna och klickar sedan på `Finish` för att skapa databasen.

   ![Skärmbild av sammanfattningssidan](./media/oracle-asm/createdb03.png)

5. Databasen har skapats. På sidan **Slutför** har du möjlighet att låsa upp ytterligare konton för att använda den här databasen och ändra lösen orden. Om du vill göra det väljer du **lösen ords hantering** – annars klickar du på `close` .

## <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Du har konfigurerat Oracle-automatiserad lagrings hantering på Oracle DB avbildningen från Azure Marketplace.  När du inte längre behöver den här virtuella datorn kan du använda följande kommando för att ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudie: Konfigurera Oracle-DataGuard](configure-oracle-dataguard.md)

[Självstudie: Konfigurera Oracle-GoldenGate](Configure-oracle-golden-gate.md)

Granska [arkitekten Oracle DB](oracle-design.md)
