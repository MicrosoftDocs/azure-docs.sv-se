---
title: Installera en huvud mål server för återställning av virtuella Linux-datorer med Azure Site Recovery
description: Lär dig hur du konfigurerar en Linux-huvud mål server för återställning efter fel till en lokal plats under haveri beredskap för virtuella VMware-datorer till Azure med hjälp av Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: mayg
ms.openlocfilehash: 9e1008f7acbfe0685b7a171176c7dc54592d1491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019250"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Konfigurera en Linux-huvudmålserver för återställning efter fel
När du har växlat över dina virtuella datorer till Azure kan du återställa de virtuella datorerna till den lokala platsen. Om du vill återställa den virtuella datorn från Azure till den lokala platsen måste du återställa den virtuella datorn från Azure. För den här processen behöver du en lokal huvud mål server för att ta emot trafiken. 

Om den skyddade virtuella datorn är en virtuell Windows-dator behöver du ett Windows huvud mål. För en virtuell Linux-dator behöver du ett Linux-huvud mål. Läs följande steg för att lära dig hur du skapar och installerar ett Linux-huvud mål.

> [!IMPORTANT]
> Från och med lanseringen av 9.10.0 huvud mål servern kan den senaste huvud mål servern bara installeras på en Ubuntu 16,04-Server. Nya installationer är inte tillåtna på CentOS 6.6-servrar. Du kan dock fortsätta att uppgradera dina gamla huvud mål servrar med hjälp av 9.10.0-versionen.
> Huvud mål servern på LVM stöds inte.

## <a name="overview"></a>Översikt
Den här artikeln innehåller anvisningar för hur du installerar ett Linux-huvud mål.

Publicera kommentarer eller frågor i slutet av den här artikeln eller på [sidan Microsoft Q&en fråga för Azure Recovery Services](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Förutsättningar

* Om du vill välja den värd som ska användas för att distribuera huvud målet kontrollerar du om återställning efter fel återställningen kommer till en befintlig lokal virtuell dator eller till en ny virtuell dator. 
    * För en befintlig virtuell dator ska värden för huvud målet ha åtkomst till den virtuella datorns data lager.
    * Om den lokala virtuella datorn inte finns (i händelse av en alternativ plats återställning) skapas en virtuell dator för återställning efter fel på samma värd som huvud mål servern. Du kan välja valfri ESXi-värd för att installera huvud målet.
* Huvud målet bör finnas i ett nätverk som kan kommunicera med processervern och konfigurations servern.
* Huvud mål serverns version måste vara lika med eller tidigare än versioner av processervern och konfigurations servern. Om versionen av konfigurations servern till exempel är 9,4 kan huvud målets version vara 9,4 eller 9,3, men inte 9,5.
* Huvud målet kan bara vara en virtuell VMware-dator och inte en fysisk server.

> [!NOTE]
> Se till att du inte aktiverar Storage-vMotion på alla hanterings komponenter, till exempel huvud mål. Om huvud målet flyttas efter en lyckad återaktivering går det inte att koppla från de virtuella dator diskarna (VMDK: er). I detta fall Miss lyckas failback.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Storleks rikt linjer för att skapa en huvud mål Server

Skapa huvud målet enligt följande storleks rikt linjer:
- **RAM-minne**: 6 GB eller mer
- **OS-disk storlek**: 100 GB eller mer (för att installera OS)
- **Ytterligare disk storlek för kvarhållning av enhet**: 1 TB
- **Processor kärnor**: 4 kärnor eller mer
- **Kernel**: 4,16. *

## <a name="deploy-the-master-target-server"></a>Distribuera huvud mål servern

### <a name="install-ubuntu-16042-minimal"></a>Installera Ubuntu 16.04.2 minimal

Vidta följande steg för att installera Ubuntu 16.04.2 64-bitars operativ system.

1.   Gå till [nedladdnings länken](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), Välj den närmaste speglingen och ladda ned en Ubuntu 16.04.2 med minimal 64-bitars ISO.
Behåll en Ubuntu 16.04.2 minimal 64-bitars ISO i DVD-enheten och starta systemet.

1.  Välj **engelska** som önskat språk och välj sedan **RETUR**.
    
    ![Välj ett språk](./media/vmware-azure-install-linux-master-target/image1.png)
1. Välj **Installera Ubuntu Server** och välj sedan **RETUR**.

    ![Välj Installera Ubuntu-Server](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Välj **engelska** som önskat språk och välj sedan **RETUR**.

    ![Välj engelska som önskat språk](./media/vmware-azure-install-linux-master-target/image3.png)

1. Välj lämpligt alternativ i listan alternativ för **tidszon** och välj sedan **RETUR**.

    ![Välj rätt tidszon](./media/vmware-azure-install-linux-master-target/image4.png)

1. Välj **Nej** (standard alternativet) och välj sedan **RETUR**.

     ![Konfigurera tangent bordet](./media/vmware-azure-install-linux-master-target/image5.png)
1. Välj **engelska (US)** som ursprungsland/region för tangent bordet och välj sedan **RETUR**.

1. Välj **engelska (US)** som tangentbordslayout och välj sedan **RETUR**.

1. Ange värd namnet för servern i rutan **värdnamn** och välj sedan **Fortsätt**.

1. Om du vill skapa ett användar konto anger du användar namnet och väljer sedan **Fortsätt**.

      ![Skapa ett användarkonto](./media/vmware-azure-install-linux-master-target/image9.png)

1. Ange lösen ordet för det nya användar kontot och välj sedan **Fortsätt**.

1.  Bekräfta lösen ordet för den nya användaren och välj sedan **Fortsätt**.

    ![Bekräfta lösen orden](./media/vmware-azure-install-linux-master-target/image11.png)

1.  I nästa val för att kryptera din arbets katalog väljer du **Nej** (standard alternativet) och väljer sedan **RETUR**.

1. Om tids zonen som visas är korrekt väljer du **Ja** (standard alternativet) och väljer sedan **RETUR**. Om du vill konfigurera om din tidszon väljer du **Nej**.

1. I alternativ för partitionerings metod väljer du **guidad-Använd hel disk** och väljer sedan **RETUR**.

     ![Välj alternativet partitionerings metod](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Välj lämplig disk i alternativen **Välj disk till partition** och välj sedan **RETUR**.

    ![Välj disken](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Välj **Ja** om du vill skriva ändringarna på disk och välj sedan **RETUR**.

    ![Välj standard alternativet](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  I valet konfigurera proxy väljer du standard alternativet, väljer **Fortsätt** och väljer sedan **RETUR**.
     
     ![Skärm bild som visar var du väljer Fortsätt och välj sedan Retur.](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Välj alternativet **inga automatiska uppdateringar** i valet för att hantera uppgraderingar på systemet och välj sedan **RETUR**.

     ![Välj hur du vill hantera uppgraderingar](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Eftersom Azure Site Recovery huvud mål servern kräver en särskilt version av Ubuntu, måste du se till att kernel-uppgraderingar är inaktiverade för den virtuella datorn. Om de är aktiverade kommer alla vanliga uppgraderingar att orsaka fel på huvud mål servern. Se till att du väljer alternativet **inga automatiska uppdateringar** .

1.  Välj standard alternativ. Om du vill använda openSSH för SSH Connect väljer du alternativet **openSSH Server** och väljer sedan **Fortsätt**.

    ![Välj program vara](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Välj **Ja** i valet för att installera GRUB-startläsaren och välj sedan **RETUR**.
     
    ![GRUB start installations program](./media/vmware-azure-install-linux-master-target/image20.png)


1. Välj lämplig enhet för start inläsnings installationen (helst **/dev/SDA**) och välj sedan **RETUR**.
     
    ![Välj lämplig enhet](./media/vmware-azure-install-linux-master-target/image21.png)

1. Välj **Fortsätt** och välj sedan **RETUR** för att slutföra installationen.

    ![Slutför installationen](./media/vmware-azure-install-linux-master-target/image22.png)

1. När installationen har slutförts loggar du in på den virtuella datorn med de nya autentiseringsuppgifterna för användaren. (Mer information hittar du i **steg 10** .)

1. Använd stegen som beskrivs i följande skärm bild för att ange lösen ordet för rot användaren. Logga in som rot användare.

    ![Ange rot användarens lösen ord](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Konfigurera datorn som en huvud mål Server

För att hämta ID för varje SCSI-hårddisk på en virtuell Linux-dator, **disken. Parametern Enableuuid är = TRUE** måste vara aktive rad. Utför följande steg för att aktivera den här parametern:

1. Stäng av den virtuella datorn.

2. Högerklicka på posten för den virtuella datorn i den vänstra rutan och välj sedan **Redigera inställningar**.

3. Välj fliken **alternativ** .

4. Välj **Avancerad** allmän i den vänstra rutan  >  och välj sedan knappen **konfigurations parametrar** på den nedre högra delen av skärmen.

    ![Öppna konfigurations parameter](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Alternativet **konfigurations parametrar** är inte tillgängligt när datorn körs. Om du vill aktivera den här fliken stänger du den virtuella datorn.

5. Se om en rad med **disk. Enableuuid är** finns redan.

   - Om värdet finns och är inställt på **falskt**, ändra värdet till **Sant**. (Värdena är inte Skift läges känsliga.)

   - Om värdet finns och är inställt på **Sant** väljer du **Avbryt**.

   - Om värdet inte finns väljer du **Lägg till rad**.

   - I kolumnen namn, Lägg till **disk. Enableuuid är** och Ställ in värdet på **Sant**.

     ![Kontrollerar om disken. Enableuuid är finns redan](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Inaktivera kernel-uppgraderingar

Azure Site Recovery huvud mål servern kräver en speciell version av Ubuntu, se till att kernel-uppgraderingar är inaktiverade för den virtuella datorn. Om kernel-uppgraderingar är aktiverade kan det orsaka att huvud mål servern inte fungerar.

#### <a name="download-and-install-additional-packages"></a>Hämta och installera ytterligare paket

> [!NOTE]
> Kontrol lera att du har Internet anslutning för att ladda ned och installera ytterligare paket. Om du inte har någon Internet anslutning måste du hitta dessa deb-paket manuellt och installera dem.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Hämta installations programmet för

Om huvud målet har Internet anslutning kan du hämta installations programmet genom att följa stegen nedan. Annars kan du kopiera installations programmet från processervern och sedan installera det.

#### <a name="download-the-master-target-installation-packages"></a>Hämta installations paket för huvud mål

[Hämta de senaste installations bitarna för Linux Master Target](https://aka.ms/latestlinuxmobsvc).

Om du vill ladda ned det med Linux skriver du:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Kontrol lera att du laddar ned och zippa installations programmet i din Hem Katalog. Om du packar upp på **/usr/local** Miss lyckas installationen.


#### <a name="access-the-installer-from-the-process-server"></a>Få åtkomst till installations programmet från processervern

1. På processervern går du till **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Kopiera den nödvändiga installations filen från processervern och spara den som **latestlinuxmobsvc. tar. gz** i din Hem Katalog.


### <a name="apply-custom-configuration-changes"></a>Använd anpassade konfigurations ändringar

Använd följande steg som rot användare om du vill använda anpassade konfigurations ändringar:

1. Kör följande kommando för att untar binärfilen.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Skärm bild av kommandot som ska köras](./media/vmware-azure-install-linux-master-target/image16.png)

2. Kör följande kommando för att ge behörighet.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Kör skriptet med hjälp av följande kommando.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Kör bara skriptet en gång på servern. Stäng sedan av servern. Starta om servern när du har lagt till en disk enligt beskrivningen i nästa avsnitt.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Lägg till en lagrings disk till den virtuella Linux-huvudmål datorn

Använd följande steg för att skapa en lagrings disk:

1. Koppla en ny 1 TB-disk till den virtuella Linux-huvudmål datorn och starta sedan datorn.

2. Använd kommandot **multipath-lla** för att lära dig om Multipath-ID: t för lagrings disken: **multipath-lla**

    ![Multipath-ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formatera enheten och skapa sedan ett fil system på den nya enheten: **mkfs. ext4/dev/mapper/ \<Retention disk's multipath id>**.
    
    ![Filsystem](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. När du har skapat fil systemet monterar du lagrings disken.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Skapa **fstab** -posten för att montera lagrings enheten varje gång systemet startar.
    
    `vi /etc/fstab`
    
    Välj **Infoga** för att börja redigera filen. Skapa en ny rad och infoga sedan följande text. Redigera disk-multipath-ID: t baserat på det markerade multipath-ID: t från föregående kommando.

    **/dev/mapper/ \<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Välj **ESC** och skriv **: Wq** (Skriv och avsluta) för att stänga redigerings fönstret.

### <a name="install-the-master-target"></a>Installera huvud målet

> [!IMPORTANT]
> Huvud mål serverns version måste vara lika med eller tidigare än versioner av processervern och konfigurations servern. Om det här villkoret inte är uppfyllt lyckas skydd, men replikeringen Miss lyckas.


> [!NOTE]
> Innan du installerar huvud mål servern kontrollerar du att **/etc/hosts** -filen på den virtuella datorn innehåller poster som mappar det lokala värd namnet till IP-adresserna som är associerade med alla nätverkskort.

1. Kör följande kommando för att installera huvud målet.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    ```

2. Kopiera lösen frasen från **C:\Programdata\Microsoft Azure Site Recovery\private\connection.Passphrase** på konfigurations servern. Spara den som **passphrase.txt** i samma lokala katalog genom att köra följande kommando:

    `echo <passphrase> >passphrase.txt`

    Exempel: 

    `echo itUx70I47uxDuUVY >passphrase.txt`
    

3. Anteckna konfigurations serverns IP-adress. Kör följande kommando för att registrera servern med konfigurations servern.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exempel: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Vänta tills skriptet har slutförts. Om huvud mål servern registreras visas huvud målet på sidan **Site Recovery infrastruktur** i portalen.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Installera huvud målet med hjälp av interaktiv installation

1. Kör följande kommando för att installera huvud målet. Välj **huvud mål** för agent rollen.

    ```
    ./install
    ```

2. Välj standard platsen för installation och välj sedan **RETUR** för att fortsätta.

    ![Välja en standard plats för installation av huvud mål](./media/vmware-azure-install-linux-master-target/image17.png)

När installationen är slutförd registrerar du konfigurations servern med hjälp av kommando raden.

1. Notera konfigurations serverns IP-adress. Du behöver det i nästa steg.

2. Kör följande kommando för att registrera servern med konfigurations servern.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh
    ```

     Vänta tills skriptet har slutförts. Om huvud mål servern har registrerats visas huvud målet på sidan **Site Recovery infrastruktur** i portalen.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Installera VMware-verktyg/open-vm – tools på huvud mål servern

Du måste installera VMware-verktyg eller öppna-VM-verktyg på huvud målet så att det kan identifiera data lager. Om verktygen inte är installerade visas inte skärmen för att skydda i data lager. När du har installerat VMware-verktygen måste du starta om.

### <a name="upgrade-the-master-target-server"></a>Uppgradera huvud mål servern

Kör installationsprogrammet. Den identifierar automatiskt att agenten är installerad på huvud mål servern. Om du vill uppgradera väljer du **Y**.  När installationen har slutförts kontrollerar du vilken version av huvud målet som installerats med hjälp av följande kommando:

`cat /usr/local/.vx_version`


Du ser att **versions** fältet innehåller versions numret för huvud mål servern.

## <a name="common-issues"></a>Vanliga problem

* Se till att du inte aktiverar Storage-vMotion på alla hanterings komponenter, till exempel huvud mål. Om huvud målet flyttas efter en lyckad återaktivering går det inte att koppla från de virtuella dator diskarna (VMDK: er). I detta fall Miss lyckas failback.

* Huvud målet ska inte ha några ögonblicks bilder på den virtuella datorn. Om det finns ögonblicks bilder, Miss lyckas failback.

* På grund av vissa anpassade NIC-konfigurationer inaktive ras nätverks gränssnittet under starten och huvud mål agenten kan inte initieras. Kontrol lera att följande egenskaper har angetts korrekt. Kontrol lera dessa egenskaper i Ethernet-kort filens/etc/network/interfaces.
    * automatisk eth0
    * iface eth0 inet DHCP <br>

    Starta om nätverks tjänsten med följande kommando: <br>

`sudo systemctl restart networking`


## <a name="next-steps"></a>Nästa steg
När du har slutfört installationen och registreringen av huvud målet kan du se huvud målet i avsnittet **huvud mål** i **Site Recovery infrastruktur**, under översikt över konfigurations servern.

Nu kan du fortsätta med [återskydd](vmware-azure-reprotect.md), följt av failback.

