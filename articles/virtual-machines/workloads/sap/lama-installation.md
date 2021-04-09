---
title: SAP LaMa-anslutning för Azure | Microsoft Docs
description: Hantera SAP-system på Azure med SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: 4772fdae06f23430d829fa411068b7af7a85b3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668715"
---
# <a name="sap-lama-connector-for-azure"></a>SAP LaMa-anslutning för Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Allmän support instruktion: öppna alltid en incident med SAP på komponenten BC-VCM-LVM-HYPERV om du behöver stöd för SAP LaMa eller Azure-anslutningen.

SAP LaMa används av många kunder för att hantera och övervaka sina SAP-landskap. Sedan SAP LaMa 3,0 SP05 levereras den med en anslutning till Azure som standard. Du kan använda den här anslutningen för att frigöra och starta virtuella datorer, kopiera och flytta hanterade diskar och ta bort hanterade diskar. Med de här grundläggande åtgärderna kan du flytta, kopiera, klona och uppdatera SAP-system med hjälp av SAP LaMa.

I den här guiden beskrivs hur du konfigurerar Azure Connector for SAP LaMa, skapar virtuella datorer som kan användas för att installera anpassningsbara SAP-system och hur du konfigurerar dem.

> [!NOTE]
> Anslutningen är bara tillgänglig i SAP LaMa Enterprise Edition

## <a name="resources"></a>Resurser

Följande SAP-anteckningar är relaterade till ämnet i SAP LaMa på Azure:

| Antecknings nummer | Rubrik |
| --- | --- |
| [2343511] |Microsoft Azure koppling för SAP landskaps hantering (LaMa) |
| [2350235] |SAP landskaps hantering 3,0-Enterprise Edition |

Läs även [SAP-hjälp portalen för SAP Lama](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Allmänna anmärkningar

* Se till att aktivera *Automatisk monterings punkt-skapande* i installations programmet-> inställningar-> motor  
  Om SAP LaMa monterar volymer med hjälp av de anpassningsbara SAP-tilläggen på en virtuell dator måste monterings punkten finnas om den här inställningen inte är aktive rad.

* Använd separata undernät och Använd inte dynamiska IP-adresser för att förhindra IP-adressen "stjäla" när du distribuerar nya virtuella datorer och SAP-instanser är inte för beredda  
  Om du använder dynamisk IP-adressallokering i under nätet, som också används av SAP LaMa, kan det hända att ett SAP-system med SAP-LaMa inte kan förberedas. Om ett SAP-system är unför berett är IP-adresserna inte reserverade och kan allokeras till andra virtuella datorer.

* Om du loggar in på hanterade värdar, se till att inte blockera fil system från att demonteras  
  Om du loggar in på en virtuell Linux-dator och ändrar arbets katalogen till en katalog i en monterings punkt, till exempel/usr/sap/AH1/ASCS00/exe, går det inte att demontera volymen och en återaktivering eller avförberedelse Miss lyckas.

* Se till att inaktivera CLOUD_NETCONFIG_MANAGE på SUSE SLES Linux Virtual Machines. Mer information finns i [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Konfigurera Azure Connector för SAP-LaMa

Azure-anslutaren levereras från och med SAP LaMa 3,0 SP05. Vi rekommenderar att du alltid installerar det senaste support paketet och korrigeringen för SAP LaMa 3,0.

Azure-anslutaren använder Azure Resource Manager API för att hantera dina Azure-resurser. SAP LaMa kan använda ett huvud namn för tjänsten eller en hanterad identitet för att autentisera mot detta API. Om din SAP-LaMa körs på en virtuell Azure-dator rekommenderar vi att du använder en hanterad identitet enligt beskrivningen i kapitlet, [använder en hanterad identitet för att få åtkomst till Azure-API: et](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Om du vill använda ett huvud namn för tjänsten följer du stegen i kapitlet [Använd ett tjänst objekt för att få åtkomst till Azure-API: et](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Använd ett huvud namn för tjänsten för att få åtkomst till Azure-API: et

Azure-anslutaren kan använda ett huvud namn för tjänsten för att auktorisera mot Microsoft Azure. Följ dessa steg om du vill skapa ett tjänst huvud namn för SAP landskaps hantering (LaMa).

1. Gå till https://portal.azure.com
1. Öppna bladet Azure Active Directory
1. Klicka på Appregistreringar
1. Klicka på ny registrering
1. Ange ett namn och klicka på registrera
1. Välj den nya appen och klicka på certifikat & hemligheter på fliken Inställningar
1. Skapa en ny klient hemlighet, ange en beskrivning för en ny nyckel, Välj när hemligheten ska gå ut och klicka på Spara
1. Skriv ned värdet. Den används som lösen ord för tjänstens huvud namn
1. Anteckna program-ID: t. Den används som användar namn för tjänstens huvud namn

Tjänstens huvud namn har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvud namn behörighet att komma åt dem.

1. Gå till https://portal.azure.com
1. Öppna bladet resurs grupper
1. Välj den resurs grupp som du vill använda
1. Klicka på åtkomst kontroll (IAM)
1. Klicka på Lägg till roll tilldelning
1. Välj roll deltagare
1. Ange namnet på det program som du skapade ovan
1. Klicka på Spara
1. Upprepa steg 3 till 8 för alla resurs grupper som du vill använda i SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Använd en hanterad identitet för att få åtkomst till Azure-API: et

För att kunna använda en hanterad identitet, måste din SAP LaMa-instans köras på en virtuell Azure-dator som har en system-eller användare som tilldelats identiteten. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../../../active-directory/managed-identities-azure-resources/overview.md) och [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

Den hanterade identiteten har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge IT-behörighet för att få åtkomst till dem.

1. Gå till https://portal.azure.com
1. Öppna bladet resurs grupper
1. Välj den resurs grupp som du vill använda
1. Klicka på åtkomst kontroll (IAM)
1. Klicka på Lägg till > Lägg till roll tilldelning
1. Välj roll deltagare
1. Välj virtuell dator för tilldela till
1. Välj den virtuella dator där din SAP LaMa-instans körs
1. Klicka på Spara
1. Upprepa stegen för alla resurs grupper som du vill använda i SAP LaMa

I din SAP LaMa Azure Connector-konfiguration väljer du Använd hanterad identitet för att aktivera användningen av den hanterade identiteten. Om du vill använda en tilldelad system identitet, måste du lämna fältet användar namn tomt. Om du vill använda en tilldelad identitet anger du användarens tilldelade identitets-ID i fältet användar namn.

### <a name="create-a-new-connector-in-sap-lama"></a>Skapa en ny anslutning i SAP LaMa

Öppna webbplatsen SAP LaMa och gå till infrastruktur. Gå till fliken Cloud Managers och klicka på Lägg till. Välj Microsoft Azure Molnadapter och klicka på Nästa. Ange följande information:

* Etikett: Välj ett namn för anslutnings instansen
* Användar namn: program-ID för tjänstens huvud namn eller ID för den användare som tilldelats den virtuella datorns identitet. Mer information finns i [använda en system-eller användare som tilldelats identiteter]
* Lösen ord: tjänstens huvud nyckel/lösen ord. Du kan lämna fältet tomt om du använder en system-eller användare som tilldelats identiteten.
* URL: Behåll standard `https://management.azure.com/`
* Övervaknings intervall (sekunder): måste vara minst 300
* Använd hanterad identitet: SAP LaMa kan använda en system-eller användare som tilldelats identitet för att autentisera mot Azure-API: et. Se kapitlet [använda en hanterad identitet för att få åtkomst till Azure-API: et](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) i den här hand boken.
* Prenumerations-ID: ID för Azure-prenumeration
* Azure Active Directory klient-ID: ID för Active Directory klienten
* Proxyvärd: proxyserverns värdnamn om SAP LaMa behöver en proxyserver för att ansluta till Internet
* Proxyserver: proxyserverns TCP-port
* Ändra lagrings typ för att spara kostnader: Aktivera den här inställningen om Azure-kortet ska ändra lagrings typen för Managed Disks att spara kostnaderna när diskarna inte används. För data diskar som refereras till i en SAP-instans konfiguration kommer kortet att ändra disk typen till standard lagring under en instans, avförberedas och tillbaka till den ursprungliga lagrings typen under en instans förbereds. Om du stoppar en virtuell dator i SAP-LaMa, kommer kortet att ändra lagrings typen för alla anslutna diskar, inklusive OS-disken till standard lagring. Om du startar en virtuell dator i SAP-LaMa, kommer kortet att ändra lagrings typen tillbaka till den ursprungliga lagrings typen.

Verifiera dina inaktuella indatatyper genom att klicka på testa konfiguration. Du bör se

Anslutningen lyckades: anslutningen till Microsoft Cloud lyckades. 7 resurs grupper hittades (endast 10 grupper har begärts)

längst ned på webbplatsen.

## <a name="provision-a-new-adaptive-sap-system"></a>Etablera ett nytt anpassat SAP-system

Du kan distribuera en ny virtuell dator manuellt eller använda en av Azure-mallarna i [snabb starts databasen](https://github.com/Azure/azure-quickstart-templates). Den innehåller mallar för [SAP NETWEAVER ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver Application servers](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)och- [databasen](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Du kan också använda mallarna för att etablera nya värdar som en del av system kopiering/kloning osv.

Vi rekommenderar att du använder ett separat undernät för alla virtuella datorer som du vill hantera med SAP LaMa och inte använder dynamiska IP-adresser för att förhindra IP-adressen "stjäla" när du distribuerar nya virtuella datorer och SAP-instanser är inte för beredda.

> [!NOTE]
> Ta om möjligt bort alla tillägg för virtuella datorer eftersom de kan orsaka långa körningar för att koppla från diskar från en virtuell dator.

Se till att användar \<hanasid> -ADM, \<sapsid> adm och grupp sapsys finns på mål datorn med samma ID och GID eller Använd LDAP. Aktivera och starta NFS-servern på de virtuella datorer som ska användas för att köra SAP NetWeaver (A) SCS.

### <a name="manual-deployment"></a>Manuell distribution

SAP LaMa kommunicerar med den virtuella datorn med hjälp av SAP-värd agenten. Om du distribuerar de virtuella datorerna manuellt eller inte använder Azure Resource Manager-mallen från snabb starts databasen, måste du installera den senaste SAP-värdservern och de anpassningsbara SAP-tilläggen. Mer information om de korrigerings nivåer som krävs för Azure finns i SAP anmärkning [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Manuell distribution av en virtuell Linux-dator

Skapa en ny virtuell dator med ett av de operativ system som stöds i SAP NOTE [2343511]. Lägg till ytterligare IP-konfigurationer för SAP-instanserna. Varje instans måste ha minst på IP-adress och måste installeras med ett virtuellt värdnamn.

SAP NetWeaver ASCS-instansen behöver diskar för/sapmnt/ \<SAPSID> ,/usr/SAP/ \<SAPSID> ,/usr/SAP/trans och/usr/SAP/ \<sapsid> adm. Det behövs inga ytterligare diskar för program servrarna för SAP-NetWeaver. Allt som är relaterat till SAP-instansen måste lagras i ASCS och exporteras via NFS. Annars är det för närvarande inte möjligt att lägga till ytterligare program servrar med SAP LaMa.

![SAP NetWeaver-ASCS på Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Manuell distribution för SAP HANA

Skapa en ny virtuell dator med ett av de operativ system som stöds för SAP HANA enligt listan i SAP anmärkning [2343511]. Lägg till ytterligare en IP-konfiguration för SAP HANA och en per HANA-klient.

SAP HANA behöver diskar för/Hana/Shared,/Hana/backup,/Hana/data och/Hana/log

![SAP HANA på Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Manuell distribution för Oracle Database på Linux

Skapa en ny virtuell dator med ett av de operativ system som stöds för Oracle-databaser enligt vad som anges i SAP anmärkning [2343511]. Lägg till ytterligare en IP-konfiguration för Oracle-databasen.

Oracle-databasen behöver diskar för/Oracle,/Home/oraod1 och/Home/Oracle

![Diagram som visar en Oracle-databas på Linux och de diskar som behövs.](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Manuell distribution för Microsoft SQL Server

Skapa en ny virtuell dator med ett av de operativ system som stöds för Microsoft SQL Server enligt listan i SAP anmärkning [2343511]. Lägg till ytterligare en IP-konfiguration för SQL Server-instansen.

Den SQL Server databas servern behöver diskar för databas data och loggfiler och diskar för c:\usr\sap.

![Oracle Database på Linux](media/lama/sap-lama-db-sql.png)

Se till att installera en Microsoft ODBC-drivrutin som stöds för SQL Server på en virtuell dator som du vill använda för att flytta en SAP-NetWeaver program server till eller som ett mål för system kopiering/kloning.

SAP LaMa kan inte flytta SQL Server sig själv, så att en virtuell dator som du vill använda för att flytta en databas instans till eller som ett mål behov för system kopiering/kloning SQL Server förinstallerad.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Distribuera en virtuell dator med en Azure-mall

Hämta följande senaste tillgängliga arkiv från [SAP Software Marketplace](https://support.sap.com/swdc) för operativ systemet på de virtuella datorerna:

1. SAPCAR 7,21
1. SAP-VÄRDA GENT 7,21
1. SAP ADAPTIV-TILLÄGG 1,0 EXT

Hämta även följande komponenter från [Microsoft Download Center](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 Redistributable Package (x64) (endast Windows)
1. Microsoft ODBC Driver för SQL Server (endast SQL Server)

Komponenterna krävs för att distribuera mallen. Det enklaste sättet att göra dem tillgängliga för mallen är att överföra dem till ett Azure Storage-konto och skapa en signatur för delad åtkomst (SAS).

Mallarna har följande parametrar:

* sapSystemId: SAP-systemets ID. Den används för att skapa en disklayouten (till exempel/usr/SAP/ \<sapsid> ).

* computerName: dator namnet för den nya virtuella datorn. Den här parametern används också av SAP LaMa. När du använder den här mallen för att etablera en ny virtuell dator som en del av en system kopiering väntar SAP LaMa tills värden med det här dator namnet kan nås.

* osType: den typ av operativ system som du vill distribuera.

* dbType: databasens typ. Den här parametern används för att avgöra hur många ytterligare IP-konfigurationer som måste läggas till och hur disklayouten ska se ut.

* sapSystemSize: storleken på det SAP-system som du vill distribuera. Den används för att fastställa instans typ och storlek för den virtuella datorn.

* adminUsername: användar namn för den virtuella datorn.

* adminPassword: lösen ordet för den virtuella datorn. Du kan också ange en offentlig nyckel för SSH.

* sshKeyData: Offentlig SSH-nyckel för de virtuella datorerna. Stöds endast för Linux-operativsystem.

* subnetId: ID för det undernät som du vill använda.

* deployEmptyTarget: du kan distribuera ett tomt mål om du vill använda den virtuella datorn som mål för en instans, flytta eller liknande. I det här fallet är inga ytterligare diskar eller IP-konfigurationer kopplade.

* sapcarLocation: platsen för det sapcar-program som matchar det operativ system som du distribuerar. sapcar används för att extrahera de Arkiv du anger i andra parametrar.

* sapHostAgentArchiveLocation: platsen för SAP host agent-arkivet. SAP-värd agent distribueras som en del av den här mallen.

* sapacExtLocation: platsen för de anpassningsbara SAP-tilläggen. SAP NOTE [2343511] visar den lägsta korrigerings nivå som krävs för Azure.

* vcRedistLocation: platsen för den VC-körning som krävs för att installera de anpassningsbara SAP-tilläggen. Den här parametern krävs endast för Windows.

* odbcDriverLocation: platsen för den ODBC-drivrutin som du vill installera. Endast Microsoft ODBC-drivrutin för SQL Server stöds.

* sapadmPassword: lösen ordet för sapadm-användaren.

* sapadmId: Linux-användar-ID: t för sapadm-användaren. Krävs inte för Windows.

* sapsysGid: Linux-grupp-ID: t för sapsys-gruppen. Krävs inte för Windows.

* _artifactsLocation: bas-URI, där artefakter som krävs av den här mallen finns. När mallen distribueras med hjälp av de medföljande skripten, används en privat plats i prenumerationen och det här värdet skapas automatiskt. Krävs endast om du inte distribuerar mallen från GitHub.

* _artifactsLocationSasToken: sasToken krävs för att komma åt _artifactsLocation. När mallen distribueras med hjälp av de medföljande skripten skapas en sasToken automatiskt. Krävs endast om du inte distribuerar mallen från GitHub.

### <a name="sap-hana"></a>SAP HANA

I exemplen nedan förutsätter vi att du installerar SAP HANA med system-ID HN1 och SAP NetWeaver-systemet med system-ID AH1. De virtuella värdarna är HN1-dB för HANA-instansen ah1-dB för den HANA-klient som används av SAP NetWeaver-systemet, ah1-ASCs för SAP NetWeaver ASCS och ah1-di-0 för den första SAP NetWeaver-programservern.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Installera SAP NetWeaver-ASCS för SAP HANA med Azure Managed Disks

Innan du startar SAP Software Provisioning Manager (SWPM) måste du montera IP-adressen för det virtuella värd namnet för ASCS. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med hjälp av sapacext måste du montera om IP-adressen efter en omstart.

![Linux-logotyp.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows-logotyp.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Kör SWPM och Använd *ah1-ASCs* för *värd namnet för ASCs-instansen*.

![Linux-logotyp.][Logo_Linux] Linux  
Lägg till följande profil parameter i SAP host agent-profilen som finns på/usr/SAP/hostctrl/exe/host_profile. Mer information finns i SAP NOTE [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Installera SAP NetWeaver ASCS för SAP HANA på Azure NetAppFiles (ANF) BETA

> [!NOTE]
> Den här funktionen är eller GA ännu. Mer information hittar du i SAP NOTE [2815988] (endast synlig för Preview-kunder).
Öppna en SAP-incident på komponenten BC-VCM-LVM-HYPERV och begär att ansluta till LaMa Storage adapter för Azure NetApp Files för hands version

ANF tillhandahåller NFS för Azure. I samband med SAP LaMa fören klar skapandet av instanserna av ABAP Central Services (ASCS) och den efterföljande installationen av program servrar. Tidigare var ASCS-instansen att fungera som NFS-server även och parametern acosprep/nfs_paths måste läggas till i host_profile för SAP-Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF är för närvarande tillgängligt i följande regioner:

Östra Australien, centrala USA, östra USA, östra USA 2, norra Europa, södra centrala USA, Västeuropa och västra USA 2.

#### <a name="network-requirements"></a>Nätverks krav

ANF kräver ett delegerat undernät som måste vara en del av samma VNET som SAP-servrarna. Här är ett exempel på en sådan konfiguration.
Den här skärmen visar skapandet av VNET och det första under nätet:

![SAP LaMa skapa ett virtuellt nätverk för Azure ANF ](media/lama/sap-lama-createvn-50.png)

Nästa steg skapar det delegerade under nätet för Microsoft. NetApp/Volumes.

![SAP LaMa Lägg till delegerat undernät ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa-lista över undernät ](media/lama/sap-lama-subnets.png)

Nu måste ett NetApp-konto skapas i Azure Portal:

![SAP LaMa skapa NetApp-konto ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP LaMa NetApp-konto har skapats ](media/lama/sap-lama-netappaccount.png)

I NetApp-kontot anger kapacitets gruppen storlek och typ av diskar för varje pool:

![SAP LaMa skapa NetApp Capacity pool ](media/lama/sap-lama-capacitypool-50.png)

![SAP LaMa NetApp-pool skapades ](media/lama/sap-lama-capacitypool-list.png)

NFS-volymerna kan nu definieras. Eftersom det kommer att finnas volymer för flera system i en pool ska du välja ett namn schema som du själv förklarar. Genom att lägga till SID kan du gruppera relaterade volymer tillsammans. Följande monteringar krävs för ASCS och as-instansen: */sapmnt/ \<SID\>*, */usr/SAP/ \<SID\>* och */Home/ \<sid\> ADM*. Du behöver eventuellt */usr/SAP/trans* för den centrala transport katalogen, som minst används av alla system i ett landskap.

> [!NOTE]
> Under BETA fasen måste namnet på volymerna vara unika i prenumerationen.

![SAP LaMa skapa en volym 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa skapa en volym 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP-LaMa skapa en volym 3 ](media/lama/sap-lama-createvolume3-80.png)

De här stegen måste också upprepas för de andra volymerna.

![SAP LaMa-lista över skapade volymer ](media/lama/sap-lama-volumes.png)

Nu måste dessa volymer monteras på de system där den första installationen med SAP-SWPM kommer att utföras.

Först måste du skapa monterings punkterna. I det här fallet är SID-AN1 så att följande kommandon måste utföras:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Sedan kommer ANF-volymerna att monteras med följande kommandon:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Monterings kommandon kan också härledas från portalen. De lokala monterings punkterna måste justeras.

Använd kommandot DF-h för att verifiera.

![SAP LaMa-monterings punkter OS-nivå ](media/lama/sap-lama-mounts.png)

Nu måste installationen med SWPM utföras.

Samma steg måste utföras för minst en som instans.

När installationen är klar måste systemet identifieras i SAP LaMa.

Monterings punkterna bör se ut så här för ASCS och som-instansen:

![SAP LaMa-monterings punkter i LaMa ](media/lama/sap-lama-ascs.png) (detta är ett exempel. IP-adresserna och export Sök vägen skiljer sig från de som används tidigare)


#### <a name="install-sap-hana"></a>Installera SAP HANA

Om du installerar SAP HANA med hjälp av kommando rads verktyget hdblcm använder du parameter--hostname för att ange ett virtuellt värdnamn. Du måste lägga till IP-adressen för databasens virtuella värdnamn i ett nätverks gränssnitt. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med hjälp av sapacext måste du montera om IP-adressen efter en omstart.

Lägg till ett annat virtuellt värdnamn och en IP-adress för det namn som används av program servrarna för att ansluta till HANA-klienten.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Kör databas instans installationen av SWPM på den virtuella program Server datorn, inte på den virtuella datorn HANA. Använd *ah1-DB* för *databas värden* i dialog *databasen för SAP-system*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Installera SAP NetWeaver Application Server för SAP HANA

Innan du startar SAP Software Provisioning Manager (SWPM) måste du montera IP-adressen för program serverns virtuella värdnamn. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med hjälp av sapacext måste du montera om IP-adressen efter en omstart.

![Linux-logotyp.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows-logotyp.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Vi rekommenderar att du använder SAP NetWeaver Profile parameter databaser/HDB/hdb_use_ident för att ange den identitet som används för att hitta nyckeln i HDB-userstore. Du kan lägga till den här parametern manuellt efter installationen av databas instansen med SWPM eller köra SWPM med

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Om du ställer in den manuellt måste du också skapa nya HDB userstore-poster.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Använd *ah1-di-0* som *värd namn för Pas-instansen* i dialog rutan *primär program Server instans*.

#### <a name="post-installation-steps-for-sap-hana"></a>Steg efter installationen för SAP HANA

Se till att säkerhetskopiera SYSTEMDB och alla klient databaser innan du försöker göra en klient kopia, flytta klient organisationen eller skapa en systemreplikering.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

I exemplen nedan förutsätter vi att du installerar SAP NetWeaver-systemet med system-ID AS1. De virtuella värdarna är AS1-dB för SQL Server-instansen som används av SAP NetWeaver-systemet, AS1-ASCs för SAP NetWeaver ASCS och AS1-di-0 för den första SAP NetWeaver-programservern.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Installera SAP NetWeaver-ASCS för SQL Server

Innan du startar SAP Software Provisioning Manager (SWPM) måste du montera IP-adressen för det virtuella värd namnet för ASCS. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med hjälp av sapacext måste du montera om IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Kör SWPM och Använd *AS1-ASCs* för *värd namnet för ASCs-instansen*.

#### <a name="install-sql-server"></a>Installera SQL Server

Du måste lägga till IP-adressen för databasens virtuella värdnamn i ett nätverks gränssnitt. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med hjälp av sapacext måste du montera om IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Kör databas instans installationen av SWPM på den virtuella SQL Server-datorn. Använd SAPINST_USE_HOSTNAME =*AS1-DB* för att åsidosätta det värdnamn som används för att ansluta till SQL Server. Om du har distribuerat den virtuella datorn med hjälp av Azure Resource Manager-mallen, se till att ange den katalog som används för datafilerna i databasen för att *C:\sql\data* och databas logg filen till *C:\sql\log*.

Se till att användare *NT instans\system* har åtkomst till SQL Server och har Server rollen *sysadmin*. Mer information finns i SAP NOTE [1877727] och [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Installera SAP NetWeaver program Server

Innan du startar SAP Software Provisioning Manager (SWPM) måste du montera IP-adressen för program serverns virtuella värdnamn. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med hjälp av sapacext måste du montera om IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Använd *AS1-di-0* som *värd namn för Pas-instansen* i dialog rutan *primär program Server instans*.

## <a name="troubleshooting"></a>Felsökning

### <a name="errors-and-warnings-during-discover"></a>Fel och varningar under identifiering

* SELECT-behörighet nekades
  * Utforskaren [ODBC SQL Server-drivrutin] [SQL Server] SELECT-behörighet nekades för objektet log_shipping_primary_databases, databasen msdb, schema ' dbo '. [SOAPFaultException]  
  SELECT-behörighet nekades för objektet log_shipping_primary_databases, databasen msdb, schema ' dbo '.
  * Lösning  
    Kontrol lera att *NT instans\system* har åtkomst till SQL Server. Se SAP NOTE [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Fel och varningar för instans verifiering

* Ett undantag uppstod vid verifiering av HDB-userstore  
  * Se logg visaren  
    com. SAP. NW. lm. ACI. Monitor. API. Validation. RuntimeValidationException: undantag i verifiering med ID ' RuntimeHDBConnectionValidator ' (verifiering: ' VALIDATION_HDB_USERSTORE '): det gick inte att hämta hdbuserstore  
    HANA-userstore finns inte på rätt plats
  * Lösning  
    Kontrol lera att/usr/sap/AH1/hdbclient/install/installation.ini är korrekt

### <a name="errors-and-warnings-during-a-system-copy"></a>Fel och varningar under en system kopiering

* Ett fel uppstod när system etablerings steget verifierades
  * Orsakad av: com. SAP. NW. lm. lm. ACI. Engine. Base. API. util. Exception. HAOperationException anropar/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u system-p Hook-R ' |/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u system-p Hook-R
  * Lösning  
    Gör en säkerhets kopia av alla databaser i källan HANA-system

* System kopierings steget *början* av databas instansen
  * Värd agent åtgärden "000D3A282BC91EE8A1D76CF1F92E2944" misslyckades (OperationException. FaultCode: "127", meddelande: "kommandot kunde inte utföras. : [Microsoft] [ODBC SQL Server driv rutin] [SQL Server] användaren har inte behörighet att ändra databasen ' AS2 ', databasen finns inte eller så är databasen inte i ett tillstånd som tillåter åtkomst kontroller. ')
  * Lösning  
    Kontrol lera att *NT instans\system* har åtkomst till SQL Server. Se SAP NOTE [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Fel och varningar under en kloning av systemet

* Ett fel uppstod vid försök att registrera instans agenten i steget *tvångs register och start instans agenten* för program Server eller ASCS
  * Ett fel uppstod vid försök att registrera instans agenten. (RemoteException: det gick inte att läsa in instans data från profilen \\ as1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0: det går inte att komma åt profilen \\ AS1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0: ingen sådan fil eller katalog.)
  * Lösning  
   Kontrol lera att sapmnt-resursen på ASCS/SCS har fullständig åtkomst till SAP_AS1_GlobalAdmin

* Fel i steget *Aktivera start skydd för kloning*
  * Det gick inte att öppna filen ' \\ as1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0 ' orsak: ingen sådan fil eller katalog
  * Lösning  
    Dator kontot för program servern behöver skriv åtkomst till profilen

### <a name="errors-and-warnings-during-create-system-replication"></a>Fel och varningar vid skapande av systemreplikering

* Undantag vid klickning på Skapa systemreplikering
  * Orsakad av: com. SAP. NW. lm. lm. ACI. Engine. Base. API. util. Exception. HAOperationException anropar/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u system-p Hook-R ' |/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u system-p Hook-R
  * Lösning  
    Testa om sapacext kan köras som `<hanasid`>ADM

* Fel när fullständig kopia inte är aktive rad i Storage-steget
  * Ett fel uppstod vid rapportering av ett context Attribute-meddelande för sökvägen IStorageCopyData. storageVolumeCopyList: 1 och Field targetStorageSystemId
  * Lösning  
    Ignorera varningar i steg och försök igen. Det här problemet åtgärdas i ett nytt support paket/korrigering av SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Fel och varningar vid omsökning

* Sökvägen '/usr/sap/AH1 ' tillåts inte för NFS-återexporter.
  * Se SAP NOTE [2628497] för mer information.
  * Lösning  
    Lägg till ASCS-export till ASCS HostAgent-profilen. Se SAP NOTE [2628497]

* Funktionen har inte implementerats vid omlokalisering av ASCS
  * Kommandoutdata: exportfs: värd:/usr/SAP/AX1: funktionen har inte implementerats
  * Lösning  
    Kontrol lera att NFS-servertjänsten är aktive rad på den omplacerade virtuella mål datorn

### <a name="errors-and-warnings-during-application-server-installation"></a>Fel och varningar vid installation av program Server

* Fel vid körning av SAPinst-steg: getProfileDir
  * FEL: (senaste fel rapporterat av steget: infångad ESAPinstException i modul-anrop: validator för steg | NW_DI | IND | IND | IND | IND | 0 | 0 | NW_GetSidFromProfiles | IND | IND | IND | IND | getSid | 0 | NW_readProfileDir | IND | IND | IND | IND | readProfile | 0 | getProfileDir rapporterade ett fel: Node \\ \as1-ascs\sapmnt\AS1\SYS\profile finns inte. Lös problemet genom att starta SAPinst i interaktivt läge
  * Lösning  
    Kontrol lera att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i installations guiden för program Server

* Fel vid körning av SAPinst-steg: askUnicode
  * FEL: (senaste fel rapporterat av steget: infångad ESAPinstException i modul-anrop: validator för steg | NW_DI | IND | IND | IND | IND | 0 | 0 | NW_GetSidFromProfiles | IND | IND | IND | IND | getSid | 0 | NW_getUnicode | IND | IND | IND | IND | Unicode | 0 | askUnicode rapporterade ett fel: starta SAPinst i interaktivt läge för att lösa problemet)
  * Lösning  
    Om du använder en tidigare SAP-kernel kan SWPM inte avgöra om systemet är ett Unicode-system som inte längre använder meddelande servern för ASCS. Se SAP NOTE [2445033] för mer information.  
    Det här problemet åtgärdas i ett nytt support paket/korrigering av SAP LaMa.  
    Ange profil parametern OS_UNICODE = UC i standard profilen för ditt SAP-system för att undvika det här problemet.

* Fel vid körning av SAPinst-steg: dCheckGivenServer
  * Fel vid körning av SAPinst-steg: dCheckGivenServer "version =" 1.0 "-fel: (senaste fel rapporterat av steget: \<p> installationen avbröts av användaren. \</p>
  * Lösning  
    Kontrol lera att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i installations guiden för program Server

* Fel vid körning av SAPinst-steg: checkClient
  * Fel vid körning av SAPinst-steg: checkClient "version =" 1.0 "-fel: (senaste fel rapporterat av steget: \<p> installationen avbröts av användaren. \</p>)
  * Lösning  
    Kontrol lera att Microsoft ODBC-drivrutinen för SQL Server är installerad på den virtuella datorn där du vill installera program servern

* Fel vid körning av SAPinst-steg: copyScripts
  * Senaste fel rapporterat av steget: system anropet misslyckades. INFORMATION: fel 13 (0x0000000d) (behörighet nekas) vid körning av system anropet ' fopenU ' med parametern ( \\ \ AS1-ASCs/sapmnt/AS1/sys/exe/UC/NTAMD64/strdbs. cmd, w), rad (494) i filen (\ bas/bas/749_REL/bc_749_REL/src/ins/sapinst/impl/src/syslib/filesystem/syxxcfstrm2.cpp), stack spårning:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: anropa ()  
  iaxxcfile. cpp: 183: iastring CIaOsFileConnect:: callMemberFunction (iastring CONST& Name, args_t CONST& argument)  
  iaxxcfile. cpp: 1849: iastring CIaOsFileConnect:: newFileStream (args_t CONST& _args)  
  iaxxbfile. cpp: 773: CIaOsFile:: newFileStream_impl (4)  
  syxxcfile. cpp: 233: CSyFileImpl:: OpenStream (ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 29: CSyFileStreamImpl:: CSyFileStreamImpl (CSyFileStream *, iastring, ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 265: CSyFileStreamImpl:: Open ()  
  syxxcfstrm2. cpp: 58: CSyFileStream2Impl:: CSyFileStream2Impl (const CSyPath & \\ \ AW1-ASCs/sapmnt/AW1/sys/exe/UC/NTAMD64/strdbs. cmd, 0x4)  
  syxxcfstrm2. cpp: 456: CSyFileStream2Impl:: Open ()
  * Lösning  
    Kontrol lera att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i installations guiden för program Server

* Fel vid körning av SAPinst-steg: askPasswords
  * Senaste fel rapporterat av steget: system anropet misslyckades. INFORMATION: fel 5 (0x00000005) (åtkomst nekas.) vid körning av system anropet ' NetValidatePasswordPolicy ' med parametern (...), rad (359) i filen (\ bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), stack spårning:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: anropa ()  
  CSiStepExecute. cpp: 764: CSiStepExecute:: invokeDialog ()  
  DarkModeGuiEngine. cpp: 56: DarkModeGuiEngine:: showDialogCalledByJs ()  
  DarkModeDialog. cpp: 85: DarkModeDialog:: Submit ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: anropa ()  
  iaxxcaccount. cpp: 107: iastring CIaOsAccountConnect:: callMemberFunction (iastring CONST& Name, args_t CONST& argument)  
  iaxxcaccount. cpp: 1186: iastring CIaOsAccountConnect:: validatePasswordPolicy (args_t CONST& _args)  
  iaxxbaccount. cpp: 430: CIaOsAccount:: validatePasswordPolicy_impl ()  
  synxcaccmg. cpp: 297: ISyAccountMgt::P asswordValidationMessage CSyAccountMgtImpl:: validatePasswordPolicy (saponazure, * * * * *) CONST)
  * Lösning  
    Se till att lägga till en värd regel i steg *isolering* för att tillåta kommunikation från den virtuella datorn till domänkontrollanten

## <a name="next-steps"></a>Nästa steg
* [Användarguide för SAP HANA på Azure][hana-ops-guide]
* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
