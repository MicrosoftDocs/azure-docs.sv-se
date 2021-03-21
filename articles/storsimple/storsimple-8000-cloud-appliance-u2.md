---
title: StorSimple Cloud Appliance Uppdatering 3| Microsoft Docs
description: Lär dig hur du skapar, distribuerar och hanterar en StorSimple-molninstallation i ett virtuellt Microsoft Azure-nätverk. (Gäller StorSimple uppdatering 3 och senare).
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: d78051c1a5af82a986152c8244d25b68dd65d552
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968850"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Distribuera och hantera en StorSimple Cloud Appliance-installation i Azure (Uppdatering 3 eller senare)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

StorSimple 8000 Series Cloud Appliance är en ytterligare funktion som medföljer din Microsoft Azure StorSimple-lösning. StorSimple-molninstallationen körs på en virtuell dator i ett virtuellt Microsoft Azure-nätverk och du kan använda den för att säkerhetskopiera och klona data från värdar.

Den här artikeln beskriver steg för steg hur du distribuerar och hanterar en StorSimple Cloud Appliance-installation i Azure. När du har läst den här artikeln, kommer du att:

* Förstå hur molninstallationen skiljer sig från den fysiska enheten.
* Kunna skapa och konfigurera molninstallationen.
* Kunna ansluta till molninstallationen.
* Förstå hur du arbetar med molninstallationen.

Den här självstudiekursen gäller för alla StorSimple Cloud Appliance-installationer som kör uppdatering 3 eller senare.

#### <a name="cloud-appliance-model-comparison"></a>Jämförelse av molninstallationsmodeller

StorSimple-molninstallationen finns i två modeller, standardmodellen 8010 (kallades tidigare 1100) och premiummodellen 8020 (introducerades i Update 2). Följande tabell innehåller en jämförelse av de två modellerna.

| Enhetsmodell | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maximal kapacitet** |30 TB |64 TB |
| **Azure VM** |Standard_A3 (4 kärnor, 7 GB minne)| Standard_DS3 (4 kärnor, 14 GB minne)|
| **Regional tillgänglighet** |Alla Azure-regioner |Azure-regioner som har stöd för Premium Storage och virtuella Azure-datorer, DS3<br></br>Använd [listan](https://azure.microsoft.com/regions/services/) för att se om både **virtuella datorer > DS-serien** och **lagring > disklagring** är tillgängligt i din region. |
| **Lagringstyp** |Använder Azure Standardlagring för lokala diskar<br></br> Lär dig hur du [skapar ett Standardlagringskonto](../storage/common/storage-account-create.md) |Använder Azure Premium Storage för lokala diskar<sup>2</sup> <br></br> |
| **Riktlinjer för arbetsbelastning** |Hämtning av filer från säkerhetskopior på objektnivå |Utvecklings- och testscenarier i molnet <br></br>Arbetsbelastningar med kortare svarstider och högre prestanda<br></br>Sekundär enhet för katastrofåterställning |

<sup>1</sup> *tidigare* kallades 1100.

<sup>2</sup> *både 8010 och 8020 använder Azure standard Storage för moln nivån. Skillnaden finns endast på den lokala nivån i enheten*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Så här skiljer sig molninstallationen från den fysiska enheten

StorSimple-installationen är en programvaruversion av StorSimple som körs på en enskild nod på en virtuell Microsoft Azure-dator. Molninstallationen har stöd för scenarier för haveriberedskap där den fysiska enheten inte är tillgänglig. Molninstallationen är lämpad att användas för hämtning på objektnivå från säkerhetskopior, för lokal haveriberedskap samt för molnbaserade utvecklings- och testscenarier.

#### <a name="differences-from-the-physical-device"></a>Hur den skiljer sig från den fysiska enheten

I följande tabell beskrivs några huvudsakliga skillnader mellan StorSimple-molninstallationen och den fysiska StorSimple-enheten.

|  | Fysisk enhet | Molninstallation |
| --- | --- | --- |
| **Plats** |Finns i datacentret. |Körs i Azure. |
| **Nätverksgränssnitt** |Har sex nätverksgränssnitt: DATA 0 till DATA 5. |Har bara ett nätverksgränssnitt: DATA 0. |
| **Registrering** |Registreras i det inledande konfigurationssteget. |Registreringen är en separat åtgärd. |
| **Krypterings nyckel för tjänst data** |Återskapa på den fysiska enheten och uppdatera sedan molninstallationen med den nya nyckeln. |Det går inte att återskapa från molninstallationen. |
| **Volymtyper som stöds** |Har stöd både för lokalt fixerade och nivåindelade volymer. |Har stöd endast för nivåindelade volymer. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Krav för molninstallationen

I följande avsnitt beskrivs konfigurationskraven för StorSimple-molninstallationen. Gå igenom säkerhetsövervägandena för användningen av en molninstallation innan du distribuerar molninstallationen.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Krav för Azure

Innan du etablerar molninstallationen måste du göra följande förberedelser i Azure-miljön:

* Se till att du har en fysisk StorSimple 8000 series-enhet (modell 8100 eller 8600) distribuerad och körandes i ditt datacenter. Registrera enheten med samma StorSimple Device Manager-tjänst som du vill skapa en StorSimple Cloud Appliance-installation för.
* [Konfigurera ett virtuellt nätverk i Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) för molninstallationen. Om du använder Premiumlagring, måste du skapa ett virtuellt nätverk i en Azure-region som har stöd för Premiumlagring. Premium Storage-regioner är regioner som motsvarar raden för disklagring i [listan över Azure-tjänster baserat på region](https://azure.microsoft.com/regions/services/).
* Vi rekommenderar att du använder DNS-standardservern från Azure i stället för att ange ett eget DNS-servernamn. Om DNS-servernamnet inte är giltigt eller om DNS-servern inte kan matcha IP-adresser korrekt går det inte att skapa molninstallationen.
* Punkt-till-plats och plats-till-plats går att välja, men är inget krav. Om du vill kan du konfigurera dessa alternativ för mer avancerade scenarier.
* Du kan skapa [Azure Virtual Machines](../virtual-machines/windows/quick-create-portal.md) (värdservrar) i det virtuella nätverket som kan använda de volymer som exponeras av molninstallationen. Servrarna måste uppfylla följande krav:

  * Vara virtuella Windows eller Linux-datorer som har installerad programvara med iSCSI-initierare.
  * Köras i samma virtuella nätverk som molninstallationen.
  * Kunna ansluta till iSCSI-målet för molninstallationen via molninstallationens interna IP-adress.
  * Kontrollera att du har konfigurerat stöd för iSCSI och molntrafik på samma virtuella nätverk.

#### <a name="storsimple-requirements"></a>Krav för StorSimple

Gör följande uppdateringar i StorSimple Device Manager-tjänsten innan du skapar en molninstallation:

* Lägg till [åtkomstkontrollposter](storsimple-8000-manage-acrs.md) för de virtuella datorer som ska vara värdservrar för molninstallationen.
* Använd ett [lagringskonto](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) i samma region som molninstallationen. Lagringskonton i olika regioner kan resultera i sämre prestanda. Du kan använda ett Standard- eller Premium Storage-konto med molninstallationen. Mer information om hur du skapar ett [Standardlagringskonto](../storage/common/storage-account-create.md).
* Använd ett annat lagringskonto när du skapar molninstallationen än det som används för dina data. Om samma lagringskonto används kan det resultera i sämre prestanda.

Kontrollera att du har följande information innan du börjar:

* Ditt Azure Portal-konto med autentiseringsuppgifter.
* En kopia av krypteringsnyckeln för tjänstdata från den fysiska enheten som är registrerad i StorSimple Device Manager-tjänsten.

## <a name="create-and-configure-the-cloud-appliance"></a>Skapa och konfigurera molninstallationen

Kontrollera att [kraven för molninstallationen](#prerequisites-for-the-cloud-appliance) är uppfyllda innan du genomför de här procedurerna.

Du skapar en StorSimple-molninstallation genom att följa stegen nedan.

### <a name="step-1-create-a-cloud-appliance"></a>Steg 1: Skapa en molninstallation

Du skapar StorSimple-molninstallationen genom att följa stegen nedan.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Om det inte går att skapa molninstallationen i det här steget kan det bero på att du inte har någon Internetanslutning. Om du behöver mer information går du till [Felsöka problem med Internetanslutning](#troubleshoot-internet-connectivity-errors) när du skapar en molninstallation.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>Steg 2: Konfigurera och registrera molninstallationen

Kontrollera att du har en kopia av krypteringsnyckeln för tjänstdata innan du påbörjar den här proceduren. Krypteringsnyckeln för tjänstdata skapades när du registrerade din första fysiska StorSimple-enhet med StorSimple Device Manager-tjänsten. Du uppmanades att spara den på en säker plats. Om du inte har en kopia av krypteringsnyckeln för tjänstdata ska du kontakta Microsoft Support för att få hjälp.

Konfigurera och registrera StorSimple-molninstallationen genom att utföra stegen nedan.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Steg 3: (Valfritt) ändra konfigurationsinställningarna för enheten

I följande avsnitt beskrivs de enhetskonfigurationsinställningar som krävs för StorSimple-molninstallationen om du vill använda CHAP, StorSimple Snapshot Manager eller ändra administratörslösenordet för enheten.

#### <a name="configure-the-chap-initiator"></a>Konfigurera CHAP-initieraren

Den här parametern innehåller de autentiseringsuppgifter som molninstallationen (målet) förväntar sig från initierarna (servrarna) som försöker få åtkomst till volymerna. Initierarna skapar ett CHAP-användarnamn och ett CHAP-lösenord för att identifiera sig själva på din enhet under den här autentiseringen. Detaljerade anvisningar finns i [Konfigurera CHAP för din enhet](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Konfigurera CHAP-målet

Den här parametern innehåller de autentiseringsuppgifter som molninstallationen använder när en CHAP-aktiverad initierare begär ömsesidig eller dubbelriktad autentisering. Molninstallationen använder ett omvänt CHAP-användarnamn och lösenord för att identifiera sig för initieraren under den här autentiseringsprocessen.

> [!NOTE]
> Inställningarna för CHAP-målet är globala inställningar. När de här inställningarna tillämpas använder alla volymer som är anslutna till molninstallationen CHAP-autentisering.

Detaljerade anvisningar finns i [Konfigurera CHAP för din enhet](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Konfigurera lösenordet för StorSimple Snapshot Manager

Programvaran StorSimple Manager finns på din Windows-värd och ger administratörer möjlighet att hantera säkerhetskopiering av din StorSimple-enhet i form av lokala och molnbaserade ögonblicksbilder.

> [!NOTE]
> För molninstallationen är din Windows-värd en virtuell Azure-dator.

När du konfigurerar en enhet i StorSimple Snapshot Manager uppmanas du att ange StorSimple-enhetens IP-adress och lösenord för att autentisera lagringsenheten. Detaljerade anvisningar finns i [Konfigurera lösenordet för StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Ändra enhetens administratörslösenord

Om du använder Windows PowerShell-gränssnittet för att komma åt molninstallationen måste du ange ett administratörslösenord för enheten. För att skydda dina data måste du ändra det här lösenordet innan molninstallationen kan användas. Detaljerade anvisningar finns i [Konfigurera administratörslösenord för enheten](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-cloud-appliance"></a>Fjärransluta till molninstallationen

Fjärråtkomst till molninstallationen via Windows PowerShell-gränssnittet är inte aktiverat som standard. Du måste aktivera fjärrhantering i molninstallationen först, och sedan på klienten som används för att komma åt molninstallationen.

Följande tvåstegsprocedur beskriver hur du fjärransluter till molninstallationen.

### <a name="step-1-configure-remote-management"></a>Steg 1: Konfigurera fjärrhantering

Du konfigurerar fjärrhantering för StorSimple-molninstallationen genom att följa stegen nedan.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>Steg 2: Fjärransluta till molninstallationen

När du har aktiverat fjärrhantering för molninstallationen använder du Windows PowerShell-fjärrkommunikation för att ansluta till installationen från en annan virtuell dator i samma virtuella nätverk. Du kan till exempel ansluta från den värd-VM som du konfigurerade och använde för att ansluta iSCSI. I de flesta distributioner öppnar du en offentlig slutpunkt för att komma åt din virtuella värddator som du kan använda för att komma åt molninstallationen.

> [!WARNING]
> **För ökad säkerhet rekommenderar vi starkt att du använder HTTPS för att ansluta till slutpunkterna och sedan tar bort slutpunkterna när din PowerShell-fjärrsession är slutförd.**

Du måste följa procedurerna i [Fjärransluta till din StorSimple-enhet](storsimple-8000-remote-connect.md) när du konfigurerar fjärrstyrning för molninstallationen.

## <a name="connect-directly-to-the-cloud-appliance"></a>Ansluta direkt till molninstallationen

Du kan också ansluta direkt till molninstallationen. Om du vill ansluta direkt till molninstallationen från en annan dator utanför det virtuella nätverket eller utanför Microsoft Azure-miljön måste du skapa ytterligare slutpunkter.

Du skapar en offentlig slutpunkt för molninstallationen genom att följa stegen nedan.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

Vi rekommenderar att du ansluter från en annan virtuell dator i samma virtuella nätverk eftersom den här metoden minimerar antalet offentliga slutpunkter i ditt virtuella nätverk. I detta fall ansluter du den virtuella datorn via en fjärrskrivbordssession och konfigurerar sedan den virtuella datorn på samma sätt som andra Windows-klienter i ett lokalt nätverk. Du behöver inte lägga till det offentliga portnumret eftersom porten redan är känd.

## <a name="get-private-ip-for-the-cloud-appliance"></a>Få en privat IP-adress för molninstallationen

Du behöver en intern eller privat IP-adress för molninstallationen för att den ska kunna ansluta till värdservern i samma virtuella nätverk. Du får en privat IP-adress för molninstallationen genom att följa stegen nedan

1. Gå till den underliggande virtuella datorn för din molninstallation. Den virtuella datorn har samma namn som din molninstallation. Gå till **Alla resurser**, ange namnet på molninstallationen och prenumerationen och ange sedan typen som ”virtuell dator”. Markera och klicka på den virtuella dator som motsvarar molninstallationen i listan över virtuella datorer som visas.

     ![Välj virtuell dator för din molninstallation](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. Gå till **Inställningar > Nätverk**. Du ser den privata IP-adressen för molninstallationen i det högra fönstret. Anteckna det.

    ![Få en privat IP-adress för din molninstallation](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>Arbeta med StorSimple-molninstallationen

Nu när du har skapat och konfigurerat StorSimple-molninstallationen är det dags att börja arbeta med den. Du kan arbeta med volymcontainer, volymer och säkerhetskopieringspolicyer i en molninstallation på samma sätt som på en fysisk StorSimple-enhet. Den enda skillnaden är att du måste vara noga med att välja molninstallationen från enhetslistan. Stegvisa anvisningar som beskriver de olika hanteringsaktiviteterna för molninstallationen finns i [Use the StorSimple Device Manager service to manage a cloud appliance](storsimple-8000-manager-service-administration.md) (Hantera en molninstallation med hjälp av StorSimple Device Manager-tjänsten).

I följande avsnitt beskrivs några av de skillnader som du kommer att upptäcka när du arbetar med molninstallationen.

### <a name="maintain-a-storsimple-cloud-appliance"></a>Underhålla en StorSimple-molninstallation

Eftersom molninstallationen är en helt programvarubaserad enhet kräver den minimalt med underhåll jämfört med den fysiska enheten.

Du kan inte uppdatera en molninstallation. Använd den senaste versionen av programvaran för att skapa en ny molninstallation.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Lagringskonton för en molninstallation

Lagringskonton skapas för användning av StorSimple Device Manager-tjänsten, av molninstallationen och av den fysiska enheten. Vi rekommenderar att du använder en regionsidentifierare i det egna namnet när du skapar dina lagringskonton. På så sätt blir det lättare att säkerställa att regionen är konsekvent mellan alla systemkomponenter. För att undvika prestandaproblem med en molninstallation är det viktigt att alla komponenter finns i samma region.

Stegvisa anvisningar finns i [Lägg till ett lagringskonto](storsimple-8000-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-cloud-appliance"></a>Inaktivera en StorSimple-molninstallation

När du inaktiverar en molninstallation tas den virtuella datorn och resursen som skapades när installationen etablerades bort. När molninstallationen har inaktiverats kan den inte återställas till sitt tidigare tillstånd. Stoppa eller ta bort klienter och värdar som är beroende av molninstallationen innan du inaktiverar den.

Följande åtgärder utförs när du inaktiverar en molninstallation:

* Molninstallationen tas bort.
* Operativsystemdisken och datadiskarna som skapades för molninstallationen tas bort.
* Den värdbaserade tjänsten och virtuella nätverk som skapades under etableringen bevaras. Om du inte använder dem, bör du ta bort dem manuellt.
* Ögonblicksbilder av molndata som har skapats för molninstallationen sparas.

Stegvisa anvisningar finns i [Inaktivera och ta bort din virtuella StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).

När molninstallationen visas som inaktiverad på bladet för StorSimple Device Manager-tjänsten kan du ta bort den från enhetslistan på bladet **Enheter**.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Starta, stoppa och starta om en molninstallation
Till skillnad från den fysiska StorSimple-enheten finns det ingen strömbrytare på en StorSimple-molninstallation. Det kan dock finnas tillfällen när du behöver stoppa och starta om molninstallationen.

Det enklaste sättet att starta, stoppa och starta om en molninstallation är via bladet för Virtual Machines-tjänsten. Gå till tjänsten Virtual Machines. I listan med virtuella datorer letar du upp den virtuella dator som är kopplad till din molninstallation (samma namn) och klickar på namnet på den virtuella datorn. På bladet för den virtuella datorn visas molninstallationens status som **Körs** eftersom molninstallationen startas som standard när den har skapats. Du kan starta, stoppa och starta om en virtuell dator när som helst.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Återställa till fabriksinställningarna
Om du bestämmer dig för att börja om med molninstallationen inaktiverar du den och tar bort den och skapar sedan en ny.

## <a name="fail-over-to-the-cloud-appliance"></a>Växla över till molninstallationen
Haveriberedskap är ett av de scenarier som StorSimple Cloud Appliance utvecklades för. Under dessa scenarier kan det hända att den fysiska StorSimple-enheten eller hela datacentret inte tillgängliga. Som tur är kan du använda molninstallationen för att återställa åtgärder på en annan plats. Under en katastrofåterställning byter volymcontainrarna från källenheten ägarskap och överförs till molninstallationen.

Följande krav måste vara uppfyllda för att katastrofåterställningen ska lyckas:

* Molninstallationen har skapats och konfigurerats.
* Alla volymer i volymcontainern är offline.
* Den volymcontainer som du redundansväxlar har en tillhörande ögonblicksbild av molndata.

> [!NOTE]
> * När du använder en molninstallation som en sekundär enhet för katastrofåterställning är det viktigt att komma ihåg att 8010 har 30 TB standardlagring, och 8020 64 TB Premium Storage. 8020-molninstallationer med högre kapacitet kan passa bättre i ett katastrofåterställningsscenario.

Stegvisa anvisningar finns i avsnittet [Växla över till en molninstallation](storsimple-8000-device-failover-cloud-appliance.md).

## <a name="delete-the-cloud-appliance"></a>Ta bort molninstallationen
Om du tidigare har konfigurerat och använt en StorSimple-molninstallation men inte vill fortsätta att ackumulera beräkningskostnader för dess användning, måste du stoppa molninstallationen. Om du stoppar molninstallationen avallokeras den virtuella datorn. Om du stoppar molninstallationen ackumuleras inte längre kostnader för dess användning mot din prenumeration. Lagringskostnaderna för operativsystem- och datadiskarna fortsätter dock att ackumuleras.

Om du vill stoppa alla kostnader måste du ta bort molninstallationen. Om du vill ta bort de säkerhetskopior som har skapats av molninstallationen kan du inaktivera eller ta bort enheten. Mer information finns i [Inaktivera och ta bort en StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Felsöka problem med Internetanslutning
Om det inte finns någon Internetanslutning när molninstallationen skapas, så misslyckas åtgärden. Du kan felsöka problem med Internetanslutningen genom att utföra följande steg i Azure Portal:

1. [Skapa en virtuell Windows-dator i Azure-portalen](../virtual-machines/windows/quick-create-portal.md). Den virtuella datorn måste använda samma lagringskonto, virtuella nätverk och undernät som används av molninstallationen. Om det finns en befintlig Windows Server-värd i Azure som använder samma lagringskonto, virtuella nätverk och undernät, kan du även använda den för att felsöka Internetanslutningen.
2. Logga in via fjärrinloggning på den virtuella datorn som skapades i föregående steg.
3. Öppna ett kommandofönster i den virtuella datorn (Win + R och skriv sedan `cmd`).
4. Kör följande cmd i prompten.

    `nslookup windows.net`
5. Om `nslookup` misslyckas hindrar problemet med Internetanslutningen molninstallationen från att registrera sig för StorSimple Manager-tjänsten.
6. Gör nödvändiga ändringar i ditt virtuella nätverk så att molninstallationen kan komma åt Azure-webbplatser, till exempel _windows.net_.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hanterar en molninstallation med hjälp av StorSimple Device Manager-tjänsten](storsimple-8000-manager-service-administration.md).
* Förstå hur du [återställer en StorSimple-volym från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md).