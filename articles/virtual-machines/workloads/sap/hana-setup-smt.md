---
title: Konfigurera SMT-Server för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Konfigurera SMT-Server för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 990274f34dac0234c2c2d521ca6a260f999671bb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674525"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Konfigurera SMT-Server för SUSE Linux
Stora instanser av SAP HANA har ingen direkt anslutning till Internet. Det är inte enkelt att registrera en sådan enhet med operativ system leverantören och att ladda ned och tillämpa uppdateringar. En lösning för SUSE Linux är att konfigurera en SMT-server på en virtuell Azure-dator. Var värd för den virtuella datorn i ett virtuellt Azure-nätverk, som är ansluten till den stora HANA-instansen. Med en sådan SMT-Server kan den stora instans enheten HANA registrera och hämta uppdateringar. 

Mer dokumentation om SUSE finns i [prenumerations hanterings verktyget för SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Krav för att installera en SMT-server som uppfyller uppgiften för HANA-stora instanser är:

- Ett virtuellt Azure-nätverk som är anslutet till den stora ExpressRoute-kretsen i HANA.
- Ett SUSE-konto som är associerat med en organisation. Organisationen bör ha en giltig SUSE-prenumeration.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Installera SMT-server på en virtuell Azure-dator

Logga först in på [SUSE kund Center](https://scc.suse.com/).

Gå till **organisationens**  >  **organisations uppgifter**. I det avsnittet bör du hitta de autentiseringsuppgifter som krävs för att konfigurera SMT-servern.

Installera sedan en SUSE Linux-dator i det virtuella Azure-nätverket. Om du vill distribuera den virtuella datorn tar du en SLES 12 SP2 galleri bild av Azure (Välj BYOS SUSE image). I distributions processen definierar du inte ett DNS-namn och använder inte statiska IP-adresser.

![Skärm bild av distribution av virtuell dator för SMT-Server](./media/hana-installation/image3_vm_deployment.png)

Den distribuerade virtuella datorn är mindre och fick den interna IP-adressen i det virtuella Azure-nätverket 10.34.1.4. Namnet på den virtuella datorn är *smtserver*. Efter installationen kontrol leras anslutningen till den stora instans enheten eller enheterna i HANA. Beroende på hur du har ordnat namn matchningen kan du behöva konfigurera en upplösning av de HANA-stora instans enheterna i osv/värdar för den virtuella Azure-datorn. 

Lägg till en disk till den virtuella datorn. Du kan använda den här disken för att hålla kvar uppdateringarna och själva start disken kan vara för liten. Här monteras disken på/SRV/www/htdocs, som du ser i följande skärm bild. En 100 GB-disk bör vara tillräckligt.

![Skärm bild som visar den extra disken i fönstret SparaTillFil.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Logga in på en stor instans enhet eller enheter i HANA, Behåll/etc/hosts och kontrol lera om du kan komma åt den virtuella Azure-dator som ska köra SMT-servern över nätverket.

Efter den här kontrollen loggar du in på den virtuella Azure-dator som ska köra SMT-servern. Om du använder SparaTillFil för att logga in på den virtuella datorn, kör du den här sekvensen av kommandon i bash-fönstret:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Starta om bash för att aktivera inställningarna. Starta sedan YAST.

Anslut din virtuella dator (smtserver) till SUSE-platsen.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

När den virtuella datorn är ansluten till SUSE-platsen installerar du SMT-paketen. Använd följande SparaTillFil-kommando för att installera SMT-paketen.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Du kan också använda verktyget YAST för att installera SMT-paketen. Gå till **program varu underhåll** i YaST och Sök efter SMT. Välj **SMT**, som växlar automatiskt till YaST2-SMT.

![Skärm bild av SMT i YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Godkänn valet av installation på smtserver. När installationen är klar går du till konfiguration av SMT-servern. Ange organisationens autentiseringsuppgifter från det SUSE-kund Center som du hämtade tidigare. Ange även värd namnet för den virtuella Azure-datorn som URL för SMT-Server. I den här demonstrationen är https: \/ /smtserver.

![Skärm bild av konfiguration av SMT-Server](./media/hana-installation/image6_configuration_of_smtserver1.png)

Testa nu om anslutningen till SUSE kund Center fungerar. Som du ser i följande skärm bild, i det här fallet fungerade det.

![Skärm bild av test anslutning till SUSE kund Center](./media/hana-installation/image7_test_connect.png)

Ange ett databas lösen ord när SMT-installationen startar. Eftersom det är en ny installation bör du definiera det lösen ordet som visas i följande skärm bild.

![Skärm bild av definiera lösen ord för databas](./media/hana-installation/image8_define_db_passwd.PNG)

Nästa steg är att skapa ett certifikat.

![Skärm bild som visar hur du skapar ett certifikat för SMT-Server](./media/hana-installation/image9_certificate_creation.PNG)

I slutet av konfigurationen kan det ta några minuter att köra kontrollen av synkroniseringen. Efter installationen och konfigurationen av SMT-servern bör du hitta katalogen lagrings platsen under monterings punkten/SRV/www/htdocs/. Det finns även vissa under kataloger under lagrings platsen. 

Starta om SMT-servern och dess relaterade tjänster med dessa kommandon.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Ladda ned paket till SMT-Server

När alla tjänster har startats om väljer du lämpliga paket i SMT-hantering med hjälp av YAST. Valet av paket är beroende av operativ Systems avbildningen på servern HANA stor instans. Valet av paket är inte beroende av SLES-versionen eller versionen av den virtuella datorn som kör SMT-servern. Följande skärm bild visar ett exempel på urvals skärmen.

![Skärm bild av val av paket](./media/hana-installation/image10_select_packages.PNG)

Starta sedan den första kopian av Välj paket till den SMT-server som du ställer in. Den här kopian utlöses i gränssnittet med hjälp av kommandot SMT-Mirror.

![Skärm bild av hämtning av paket till SMT-Server](./media/hana-installation/image11_download_packages.PNG)

Paketen bör kopieras till katalogerna som skapats under monterings punkten/SRV/www/htdocs. Den här processen kan ta en timme eller mer, beroende på hur många paket du väljer. När den här processen är klar går du till installations programmet för SMT-klienten. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurera SMT-klienten på HANA stora instans enheter

Klienten eller klienterna i det här fallet är de många HANA-instanser som är stora. Installations programmet för SMT-servern kopierade skriptet clientSetup4SMT.sh till den virtuella Azure-datorn. Kopiera skriptet till den stora instans av HANA som du vill ansluta till din SMT-Server. Starta skriptet med alternativet-h och ge namnet på din SMT-server som parameter. I det här exemplet är namnet *smtserver*.

![Skärm bild av konfiguration av SMT-klienten](./media/hana-installation/image12_configure_client.PNG)

Det är möjligt att belastningen på certifikatet från servern lyckas, men registreringen Miss lyckas, som visas på följande skärm bild.

![Skärm bild av fel vid klient registrering](./media/hana-installation/image13_registration_failed.PNG)

Om registreringen Miss lyckas, se [SUSE Support-dokument](https://www.suse.com/de-de/support/kb/doc/?id=7006024)och kör stegen som beskrivs där.

> [!IMPORTANT] 
> För Server namnet anger du namnet på den virtuella datorn (i det här fallet *smtserver*) utan det fullständigt kvalificerade domän namnet. 

När du har kört de här stegen kör du följande kommando på enheten HANA-stor instans:

```
SUSEConnect –cleanup
```

> [!Note] 
> Vänta några minuter efter det steget. Om du kör clientSetup4SMT.sh omedelbart kan du få ett fel meddelande.

Om du stöter på ett problem som du behöver åtgärda baserat på stegen i SUSE-artikeln startar du om clientSetup4SMT.sh på volymen HANA stor instans. Nu bör det slutföras.

![Skärm bild av lyckad klient registrering](./media/hana-installation/image14_finish_client_config.PNG)

Du konfigurerade SMT-klienten i den stora instans enheten HANA för att ansluta till den SMT-server som du installerade på den virtuella Azure-datorn. Du kan nu ta "zypper upp" eller "zypper in" om du vill installera operativ system uppdateringar på HANA-stora instanser eller installera ytterligare paket. Du kan bara hämta uppdateringar som du har hämtat tidigare på SMT-servern.

## <a name="next-steps"></a>Nästa steg
- [Hana-installation på HLI](hana-example-installation.md).











