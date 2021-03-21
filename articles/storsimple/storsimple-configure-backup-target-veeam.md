---
title: StorSimple 8000-serien som säkerhets kopierings mål med Veeam | Microsoft Docs
description: Lär dig mer om StorSimple för säkerhets kopiering med Veeam och metod tips för integrering av båda lösningarna.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: matd
ms.openlocfilehash: bf28265de2b297dade545695c9369b8074eeb72c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94962560"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple som ett säkerhets kopierings mål med Veeam

## <a name="overview"></a>Översikt

Azure StorSimple är en hybrid lösning för moln lagring från Microsoft. StorSimple tar itu med den exponentiella data tillväxten genom att använda ett Azure Storage konto som en förlängning av den lokala lösningen och automatiskt skikta data på den lokala lagrings platsen och moln lagringen.

I den här artikeln diskuterar vi StorSimple-integrering med Veeam och bästa praxis för att integrera båda lösningarna. Vi ger också rekommendationer om hur du konfigurerar Veeam för bästa integrering med StorSimple. Vi går över till Veeam metod tips, säkerhets kopierings arkitekter och administratörer för bästa möjliga sätt att konfigurera Veeam för att uppfylla individuella säkerhets kopierings krav och service nivå avtal (service avtal).

Även om vi illustrerar konfigurations steg och viktiga begrepp, är den här artikeln av ingen en steg-för-steg-konfiguration eller installations guide. Vi antar att de grundläggande komponenterna och infrastrukturen är i fungerande ordning och är redo att stödja de begrepp som vi beskriver.

### <a name="who-should-read-this"></a>Vem bör läsa detta?

Informationen i den här artikeln är mest användbar för säkerhets kopiering av administratörer, lagrings administratörer och lagrings arkitekter som har kunskap om lagring, Windows Server 2012 R2, Ethernet, moln tjänster och Veeam.

### <a name="supported-versions"></a>Versioner som stöds

-   Veeam 9 och senare versioner
-   [StorSimple Update 3 och senare versioner](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Varför StorSimple som ett mål för säkerhets kopiering?

StorSimple är ett bra alternativ för ett säkerhets kopierings mål eftersom:

-   Den tillhandahåller standard, lokal lagring för säkerhets kopierings program som ska användas som ett snabbt säkerhets kopierings mål, utan några ändringar. Du kan också använda StorSimple för en snabb återställning av de senaste säkerhets kopieringarna.
-   Dess moln nivå integreras sömlöst med ett moln lagrings konto i Azure för att kunna använda kostnads effektiv Azure Storage.
-   Det tillhandahåller automatiskt lagring på annan plats för haveri beredskap.


## <a name="key-concepts"></a>Viktiga begrepp

Precis som med vilken lagrings lösning som helst är det viktigt att en noggrann utvärdering av lösningens lagrings prestanda, service avtal, ändrings takt och kapacitets tillväxt måste lyckas. Huvud idén är att genom att introducera en moln nivå, spelar dina åtkomst tider och data flöden till molnet en grundläggande roll i StorSimple för att utföra sitt arbete.

StorSimple är utformad för att tillhandahålla lagring till program som fungerar med en väldefinierad arbets uppsättning data (frekventa data). I den här modellen lagras den aktiva data uppsättningen på de lokala nivåerna, och återstående icke-arbetsminne/kall/arkiverad data uppsättning för data skiktas i molnet. Den här modellen visas i följande figur. Den nästan platta gröna linjen representerar de data som lagras på de lokala nivåerna på StorSimple-enheten. Den röda linjen representerar den totala mängden data som lagras i StorSimple-lösningen på alla nivåer. Utrymmet mellan den platta gröna linjen och den exponentiella röda kurvan representerar den totala mängden data som lagras i molnet.

**StorSimple-skiktning** 
 ![ Diagram för StorSimple-skiktning](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Med den här arkitekturen i åtanke kommer du att se att StorSimple passar utmärkt för att hantera säkerhets kopierings mål. Du kan använda StorSimple för att:

-   Utför dina mest frekventa återställningar från den lokala arbets uppsättningen med data.
-   Använd molnet för haveri beredskap på annan plats och äldre data, där återställningar är mindre frekventa.

## <a name="storsimple-benefits"></a>StorSimple-förmåner

StorSimple tillhandahåller en lokal lösning som är sömlöst integrerad med Microsoft Azure, genom att dra nytta av sömlös åtkomst till lokal lagring och moln lagring.

StorSimple använder automatisk nivåering mellan den lokala enheten, som har SSD-lagring (solid-state Device) och en seriellt ansluten SCSI (SAS) och Azure Storage. Automatisk nivå lagring bevarar data som används ofta, på SSD-och SAS-nivåerna. Den flyttar data som används sällan till Azure Storage.

StorSimple erbjuder följande förmåner:

-   Unika deduplicerings-och komprimerings algoritmer som använder molnet för att uppnå oöverträffade Deduplicerings nivåer
-   Hög tillgänglighet
-   Geo-replikering med hjälp av Azure geo-replikering
-   Azure-integrering
-   Data kryptering i molnet
-   Förbättrad katastrof återställning och efterlevnad

Även om StorSimple presenterar två huvudsakliga distributions scenarier (primärt säkerhets kopierings mål och sekundärt säkerhets kopierings mål) är det grundläggande en enkel, block lagrings enhet. StorSimple utför all komprimering och deduplicering. Den skickar och hämtar sömlöst data mellan molnet och programmet och fil systemet.

Mer information om StorSimple finns i [StorSimple 8000-serien: hybrid moln lagrings lösning](storsimple-overview.md). Du kan också granska de [tekniska specifikationerna för StorSimple 8000-serien](./storsimple-8000-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Det går bara att använda en StorSimple-enhet som ett säkerhets kopierings mål för StorSimple 8000 Update 3 och senare versioner.

## <a name="architecture-overview"></a>Översikt över arkitekturen

I följande tabeller visas inledande vägledning för enhets modell-till-arkitektur.

**StorSimple-kapaciteter för lokal lagring och moln lagring**

| Lagringskapacitet | 8100 | 8600 |
|---|---|---|
| Lokal lagringskapacitet | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Kapacitet för moln lagring | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Lagrings storleken förutsätter ingen deduplicering eller komprimering.

**StorSimple-kapacitet för primära och sekundära säkerhets kopieringar**

| Säkerhets kopierings scenario  | Lokal lagringskapacitet  | Kapacitet för moln lagring  |
|---|---|---|
| Primär säkerhets kopia  | Senaste säkerhets kopior som lagrats på lokal lagring för snabb återställning för att uppfylla återställnings punkt mål (jobb) | Säkerhets kopierings historiken passar i moln kapaciteten |
| Sekundär säkerhets kopiering | Sekundär kopia av säkerhets kopierings data kan lagras i moln kapaciteten  | Ej tillämpligt  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple som primärt säkerhets kopierings mål

I det här scenariot presenteras StorSimple-volymer till säkerhets kopierings programmet som den enda lagrings platsen för säkerhets kopior. Följande bild visar en lösnings arkitektur där alla säkerhets kopior använder StorSimple-skiktade volymer för säkerhets kopiering och återställning.

![StorSimple som primärt logiskt schema för säkerhets kopierings mål](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logiska steg för säkerhets kopiering av primära mål

1.  Säkerhets kopierings servern kontaktar mål säkerhets kopierings agenten och säkerhets kopierings agenten skickar data till säkerhets kopierings servern.
2.  Säkerhets kopierings servern skriver data till StorSimple-skiktade volymer.
3.  Säkerhets kopierings servern uppdaterar katalog databasen och slutför sedan säkerhets kopierings jobbet.
4.  Ett ögonblicks bild skript utlöser StorSimple Cloud Snapshot Manager (start eller Delete).
5.  Säkerhets kopierings servern tar bort utgångna säkerhets kopior baserat på en bevarande princip.

### <a name="primary-target-restore-logical-steps"></a>Primärt mål för återställning av logiska steg

1.  Säkerhets kopierings servern börjar återställa lämpliga data från lagrings platsen.
2.  Säkerhets kopierings agenten tar emot data från säkerhets kopierings servern.
3.  Säkerhets kopierings servern Slutför återställnings jobbet.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple som sekundärt säkerhets kopierings mål

I det här scenariot används StorSimple-volymer främst för långsiktig kvarhållning eller arkivering.

Följande bild visar en arkitektur där de första säkerhets kopieringarna och återställningarna riktas mot en hög prestanda volym. Dessa säkerhets kopior kopieras och arkiveras till en StorSimple-nivå volym enligt ett angivet schema.

Det är viktigt att du ändrar storlek på hög prestanda volymen så att den kan hantera kapacitets-och prestanda krav för bevarande principen.

![StorSimple som ett sekundärt mål logiskt diagram för säkerhets kopiering](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logiska steg för säkerhets kopiering av sekundärt mål

1.  Säkerhets kopierings servern kontaktar mål säkerhets kopierings agenten och säkerhets kopierings agenten skickar data till säkerhets kopierings servern.
2.  Säkerhets kopierings servern skriver data till lagring med höga prestanda.
3.  Säkerhets kopierings servern uppdaterar katalog databasen och slutför sedan säkerhets kopierings jobbet.
4.  Säkerhets kopierings servern kopierar säkerhets kopior till StorSimple baserat på en bevarande princip.
5.  Ett ögonblicks bild skript utlöser StorSimple Cloud Snapshot Manager (start eller Delete).
6.  Säkerhets kopierings servern tar bort utgångna säkerhets kopior baserat på en bevarande princip.

### <a name="secondary-target-restore-logical-steps"></a>Logiska steg för återställning av sekundärt mål

1.  Säkerhets kopierings servern börjar återställa lämpliga data från lagrings platsen.
2.  Säkerhets kopierings agenten tar emot data från säkerhets kopierings servern.
3.  Säkerhets kopierings servern Slutför återställnings jobbet.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Att distribuera lösningen kräver tre steg:

1. Förbered nätverks infrastrukturen.
2. Distribuera din StorSimple-enhet som ett säkerhets kopierings mål.
3. Distribuera Veeam.

Varje steg beskrivs i detalj i följande avsnitt.

### <a name="set-up-the-network"></a>Konfigurera nätverket

Eftersom StorSimple är en lösning som är integrerad med Azure-molnet kräver StorSimple en aktiv och fungerande anslutning till Azure-molnet. Den här anslutningen används för åtgärder som moln ögonblicks bilder, data hantering och metadata-överföring och för att lagra äldre, mindre använda data i Azure Cloud Storage.

För att lösningen ska fungera optimalt rekommenderar vi att du följer de här nätverks rekommendationerna:

-   Länken som ansluter din StorSimple-nivå till Azure måste uppfylla dina krav på bandbredd. Uppnå detta genom att tillämpa den nödvändiga QoS-nivån (Quality of Service) på dina infrastruktur växlar för att matcha ditt RTO-service avtal (Recovery Time mål).
-   Maximal fördröjning för Azure Blob Storage-åtkomst ska vara cirka 80 ms.

### <a name="deploy-storsimple"></a>Distribuera StorSimple

Steg-för-steg-StorSimple distributions vägledning finns i [distribuera din lokala StorSimple-enhet](./storsimple-8000-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Distribuera Veeam

Metod tips för Veeam-installation finns i [Veeam Backup & Replication Practice Practices](https://bp.veeam.expert/)och läsa användar handboken på [Veeam Help Center (teknisk dokumentation)](https://www.veeam.com/documentation-guides-datasheets.html).

## <a name="set-up-the-solution"></a>Konfigurera lösningen

I det här avsnittet demonstreras några konfigurations exempel. Följande exempel och rekommendationer visar den mest grundläggande och grundläggande implementeringen. Den här implementeringen kanske inte tillämpas direkt på dina särskilda säkerhets kopierings krav.

### <a name="set-up-storsimple"></a>Konfigurera StorSimple

| StorSimple distributions uppgifter  | Ytterligare kommentarer |
|---|---|
| Distribuera din lokala StorSimple-enhet. | Versioner som stöds: Update 3 och senare versioner. |
| Aktivera säkerhets kopierings målet. | Använd de här kommandona för att aktivera eller inaktivera säkerhets kopieringens mål läge och hämta status. Mer information finns i fjärrans [luta till en StorSimple-enhet](./storsimple-8000-remote-connect.md).</br> Aktivera säkerhets kopierings läge: `Set-HCSBackupApplianceMode -enable` . </br> Så här stänger du av säkerhets kopierings läge: `Set-HCSBackupApplianceMode -disable` . </br> Så här hämtar du det aktuella läget för inställningarna för säkerhets kopierings läge: `Get-HCSBackupApplianceMode` . |
| Skapa en gemensam volym behållare för din volym som lagrar säkerhetskopierade data. Alla data i en volym behållare har deduplicerats. | StorSimple volym behållare definierar Deduplicerings domäner.  |
| Skapa StorSimple-volymer. | Skapa volymer med storlekar så nära den förväntade användningen som möjligt, eftersom volym storleken påverkar varaktigheten för moln ögonblicks bilder. Information om hur du ändrar storlek på en volym finns i om [bevarande principer](#retention-policies).</br> </br> Använd StorSimple-skiktade volymer och markera kryss rutan **Använd den här volymen för lagrings data** som inte används mindre ofta. </br> Det finns inte stöd för att använda lokalt fästa volymer. |
| Skapa en unik säkerhets kopierings princip för StorSimple för alla säkerhets kopierings mål volymer. | En princip för StorSimple-säkerhetskopiering definierar volym konsekvens gruppen. |
| Inaktivera schemat när ögonblicks bilderna upphör att gälla. | Ögonblicks bilder utlöses som en åtgärd efter bearbetning. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurera lagring för värd säkerhets kopierings Server

Konfigurera lagring av värd reserv Server enligt följande rikt linjer:  

- Använd inte utsträckta volymer (som skapats av Windows Disk Management). Utsträckta volymer stöds inte.
- Formatera dina volymer med NTFS-storlek för allokeringsenheten av 64 KB.
- Mappa StorSimple-volymerna direkt till Veeam-servern.
    - Använd iSCSI för fysiska servrar.


## <a name="best-practices-for-storsimple-and-veeam"></a>Metod tips för StorSimple och Veeam

Konfigurera din lösning enligt rikt linjerna i följande avsnitt.

### <a name="operating-system-best-practices"></a>Metod tips för operativ system

- Inaktivera Windows Server-kryptering och deduplicering för NTFS-filsystemet.
- Inaktivera Windows Server-defragmentering på StorSimple-volymerna.
- Inaktivera indexering av Windows Server på StorSimple-volymer.
- Kör en virus genomsökning på käll värden (inte mot StorSimple-volymerna).
- Inaktivera standard underhåll av [Windows Server](/windows/win32/w8cookbook/automatic-maintenance) i aktivitets hanteraren. Gör detta på något av följande sätt:
  - Inaktivera underhålls Configurator i Schemaläggaren i Windows.
  - Hämta [PsExec](/sysinternals/downloads/psexec) från Windows Sysinternals. När du har hämtat PsExec kör du Windows PowerShell som administratör och skriver:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Metod tips för StorSimple

-   Se till att StorSimple-enheten har uppdaterats till [uppdatering 3 eller senare](./index.yml).
-   Isolera iSCSI-och moln trafik. Använd dedikerade iSCSI-anslutningar för trafik mellan StorSimple och säkerhets kopierings servern.
-   Se till att din StorSimple-enhet är ett dedikerat säkerhets kopierings mål. Blandade arbets belastningar stöds inte eftersom de påverkar din RTO och återställnings punkt.

### <a name="veeam-best-practices"></a>Metod tips för Veeam

-   Veeam-databasen ska vara lokal på servern och inte finnas på en StorSimple-volym.
-   För haveri beredskap säkerhetskopierar du Veeam-databasen på en StorSimple-volym.
-   Vi stöder Veeam fullständiga och stegvisa säkerhets kopieringar för den här lösningen. Vi rekommenderar att du inte använder syntetiska och differentiella säkerhets kopior.
-   Säkerhetskopierade datafiler får bara innehålla data för ett speciellt jobb. Till exempel tillåts inga medie tillägg över olika jobb.
-   Inaktivera jobb verifiering. Vid behov bör verifieringen schemaläggas efter det senaste säkerhets kopierings jobbet. Det är viktigt att förstå att det här jobbet påverkar säkerhets kopierings fönstret.
-   Aktivera förallokering av media.
-   Se till att parallell bearbetning är aktiverat.
-   Stäng av komprimering.
-   Inaktivera deduplicering på säkerhets kopierings jobbet.
-   Ställ in optimering till **LAN-mål**.
-   Aktivera **skapa en aktiv fullständig säkerhets kopiering** (varannan vecka).
-   På lagrings platsen för säkerhets kopian ställer du in **Använd filer för säkerhets kopiering per virtuell dator**.
-   Ange **Använd flera överförings strömmar per jobb** till **8** (högst 16 tillåts). Justera det här numret upp eller ned baserat på processor belastningen på StorSimple-enheten.

## <a name="retention-policies"></a>Principer för kvarhållning

En av de vanligaste bevarande princip typerna för säkerhets kopiering är en princip för farfar, Pappa, och son (GFS). I en GFS-princip utförs en stegvis säkerhets kopiering varje dag och fullständiga säkerhets kopieringar görs varje vecka och varje månad. Den här principen resulterar i sex StorSimple-skiktade volymer: en volym innehåller de fullständiga vecko-, månads-och årliga fullständiga säkerhets kopiorna. de andra fem volymerna lagrar dagliga stegvisa säkerhets kopieringar.

I följande exempel använder vi en GFS rotation. Exemplet förutsätter följande:

-   Icke-deduplicerade eller komprimerade data används.
-   Fullständiga säkerhets kopieringar är 1 TiB.
-   Dagliga stegvisa säkerhets kopieringar är 500 GiB.
-   Fyra vecko Visa säkerhets kopior sparas i en månad.
-   Tolv månatliga säkerhets kopieringar sparas i ett år.
-   En årlig säkerhets kopia sparas i 10 år.

Baserat på föregående antaganden skapar du en 26-TiB StorSimple-nivå volym för de månatliga och årliga fullständiga säkerhets kopiorna. Skapa en 5-TiB StorSimple-nivå volym för var och en av de stegvisa dagliga säkerhets kopieringarna.

| Kvarhållning av säkerhets kopierings typ | Storlek (TiB) | GFS-multiplikator\* | Total kapacitet (TiB)  |
|---|---|---|---|
| Veckovis fullständig | 1 | 4  | 4 |
| Daglig stegvis | 0,5 | 20 (cykler är lika många veckor per månad) | 12 (2 för ytterligare kvot) |
| Månatlig fullständig | 1 | 12 | 12 |
| Varje år fullständig | 1  | 10 | 10 |
| GFS-krav |   | 38 |   |
| Ytterligare kvot  | 4  |   | 42 totalt GFS-krav  |

\* GFS-multiplikatorn är antalet kopior som du måste skydda och behålla för att uppfylla kraven för säkerhets kopierings principen.

## <a name="set-up-veeam-storage"></a>Konfigurera Veeam-lagring

### <a name="to-set-up-veeam-storage"></a>Konfigurera Veeam-lagring

1.  I Veeam för säkerhets kopiering och replikering i **databas verktyg** går du till **säkerhets kopierings infrastruktur**. Högerklicka på **säkerhets kopierings databaser** och välj sedan **Lägg till säkerhets kopierings lagring**.

    ![Skärm bild som visar Veeam-hanteringskonsolen och markerar alternativet Lägg till säkerhets kopierings lagring.](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  I dialog rutan **ny säkerhets kopierings databas** anger du ett namn och en beskrivning för lagrings platsen. Välj **Nästa**.

    ![Veeam hanterings konsol, namn och beskrivnings sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  För typen väljer du **Microsoft Windows Server**. Välj Veeam-servern. Välj **Nästa**.

    ![Hanterings konsol för Veeam väljer du typ av lagrings plats för säkerhets kopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Bläddra och välj volymen om du vill ange **plats**. Markera kryss rutan **Begränsa maximalt antal samtidiga aktiviteter till:** och Ställ in värdet på **4**. Detta säkerställer att bara fyra virtuella diskar bearbetas samtidigt medan varje virtuell dator (VM) bearbetas. Välj knappen **Avancerat** .

    ![Hanterings konsol för Veeam väljer du volym](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  I dialog rutan **Inställningar för kompatibilitetsinställningar** markerar du kryss rutan **Använd säkerhets kopiering per virtuell dator** .

    ![Veeam hanterings konsol, kompatibilitetsinställningar för lagring](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  I dialog rutan **ny säkerhets kopierings databas** markerar du kryss rutan **Aktivera vPower NFS-tjänsten på monterings servern (rekommenderas)** . Välj **Nästa**.

    ![Skärm bild som visar hanterings konsolen för Veeam där du kan lägga till en ny lagrings plats för säkerhets kopior.](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Granska inställningarna och välj sedan **Nästa**.

    ![Veeam Management Console, sidan säkerhets kopierings databas](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    En lagrings plats läggs till i Veeam-servern.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurera StorSimple som ett primärt säkerhets kopierings mål

> [!IMPORTANT]
> Data återställning från en säkerhets kopia som har flyttats till molnet sker i moln hastighet.

Följande bild visar mappningen av en typisk volym till ett säkerhets kopierings jobb. I det här fallet mappar alla vecko Visa säkerhets kopior till den fullständiga lördag-disken och de stegvisa säkerhets kopiorna mappar till Monday-Friday stegvisa diskar. Alla säkerhets kopior och återställningar är från en StorSimple-nivå volym.

![Logiskt diagram för konfiguration av primär säkerhets kopierings mål](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple som primär säkerhets kopierings mål GFS schema exempel

Här är ett exempel på ett GFS rotations schema för fyra veckor, varje månad och varje år:

| Typ av frekvens/säkerhets kopiering | Fullständig | Stegvis (dagar 1-5)  |   
|---|---|---|
| Varje vecka (veckor 1-4) | Lördag | Monday-Friday |
| Varje månad  | Lördag  |   |
| Varje år | Lördag  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Tilldela StorSimple-volymer till ett säkerhets kopierings jobb med Veeam

Skapa ett dagligt jobb med din primära Veeam StorSimple-volym för primärt scenario för säkerhets kopierings mål. För ett mål scenario för sekundär säkerhets kopiering skapar du ett dagligt jobb genom att använda direktansluten lagring (DAS), nätverksansluten lagring (NAS) eller bara en bunt av diskar (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Så här tilldelar du StorSimple-volymer till ett säkerhets kopierings jobb för Veeam

1.  I Veeam-konsolen för säkerhets kopiering och replikering väljer du **säkerhets kopiering & replikering**. Högerklicka på **säkerhets kopiering** och välj sedan **VMware** eller **Hyper-V**, beroende på din miljö.

    ![Hanterings konsol för Veeam, nytt säkerhets kopierings jobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  I dialog rutan **nytt säkerhets kopierings jobb** anger du ett namn och en beskrivning för det dagliga säkerhets kopierings jobbet.

    ![Skärm bild av Veeam-hanteringskonsolen där du lägger till namn och beskrivning.](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Välj en virtuell dator som du vill säkerhetskopiera till.

    ![Skärm bild som visar hanterings konsolen för Veeam där du väljer den virtuella datorn.](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Välj de värden som du vill använda för **Backup proxy** och **säkerhets kopierings lagring**. Välj ett värde för **återställnings punkter som ska behållas på disken** enligt RTO-definitionerna för miljön på lokalt ansluten lagrings plats. Välj **Avancerat**.

    ![Veeam hanterings konsol, sidan nytt säkerhets kopierings jobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. I dialog rutan **Avancerade inställningar** väljer du **stegvis** på fliken **säkerhets kopiering** . Se till att kryss rutan **skapa syntetiska fullständiga säkerhets kopieringar med jämna mellanrum** är avmarkerad. Markera kryss rutan **skapa aktiva fullständiga säkerhets kopieringar regelbundet** . Under **aktiv fullständig säkerhets kopiering** markerar du kryss rutan **veckovis på valda dagar** för lördag.

    ![Skärm bild som visar hanterings konsolen för Veeam, särskilt den nya sidan Avancerade inställningar för säkerhets kopierings jobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. På fliken **Storage** ser du till att kryss rutan **Aktivera inline datadeduplicering** är avmarkerad. Markera kryss rutan **Uteslut växling av fil block** och markera kryss rutan **Uteslut borttagna Filblock** . Ange **ingen** **komprimerings nivå** . För balanserade prestanda och deduplicering ställer du in **lagrings optimering** till **LAN-mål**. Välj **OK**.

    ![Veeam Management Console, sidan Avancerade inställningar för säkerhets kopierings jobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Information om deduplicering och komprimerings inställningar i Veeam finns i [data komprimering och deduplicering](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  I dialog rutan **Redigera säkerhets kopierings jobb** kan du markera kryss rutan **Aktivera program medveten bearbetning** (valfritt).

    ![Hanterings konsol för Veeam, nya säkerhets kopierings jobb sidan gäst bearbetning](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Ställ in schemat så att det körs en gång per dag, i taget som du kan ange.

    ![Veeam hanterings konsol, sida för ny säkerhets kopierings jobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurera StorSimple som ett sekundärt säkerhets kopierings mål

> [!NOTE]
> Data återställs från en säkerhets kopia som har flyttats till molnet i moln hastighet.

I den här modellen måste du ha ett lagrings medium (förutom StorSimple) för att fungera som en tillfällig cache. Du kan till exempel använda en redundant matris av oberoende diskar (RAID)-volym för att hantera utrymme, indata/utdata (I/O) och bandbredd. Vi rekommenderar att du använder RAID 5, 50 och 10.

Följande bild visar typisk kortsiktig kvarhållning lokalt (till Server) volymer och lagrings volymer med långsiktig kvarhållning. I det här scenariot körs alla säkerhets kopieringar på den lokala (till servern) RAID-volymen. Dessa säkerhets kopior dupliceras regelbundet och arkiveras i en Arkiv volym. Det är viktigt att ändra storlek på din lokala RAID-volym (Server) så att den kan hantera dina kortsiktiga kapacitets-och prestanda krav för kvarhållning.

![Logiskt diagram för StorSimple-mål för sekundär säkerhets kopiering](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple som ett sekundärt GFS exempel på säkerhets kopierings mål

I följande tabell visas hur du konfigurerar säkerhets kopieringar som ska köras på lokala och StorSimple diskar. Den innehåller individuella och totala kapacitets krav.

| Typ av säkerhets kopiering och kvarhållning | Konfigurerat lagring | Storlek (TiB) | GFS-multiplikator | Total kapacitet \* (TIB) |
|---|---|---|---|---|
| Vecka 1 (fullständig och stegvis) |Lokal disk (kortsiktig)| 1 | 1 | 1 |
| StorSimple veckor 2-4 |StorSimple disk (lång sikt) | 1 | 4 | 4 |
| Månatlig fullständig |StorSimple disk (lång sikt) | 1 | 12 | 12 |
| Varje år fullständig |StorSimple disk (lång sikt) | 1 | 1 | 1 |
|Storleks krav för GFS-volymer |  |  |  | arton|

\* Total kapacitet innehåller 17-TiB av StorSimple-diskar och 1-TiB för lokal RAID-volym.


### <a name="gfs-example-schedule"></a>Schema för GFS-exempel

GFS rotation, varje vecka, månads vis och årligt schema

| Vecka | Fullständig | Stegvis dag 1 | Stegvis dag 2 | Stegvis dag 3 | Stegvis dag 4 | Stegvis dag 5 |
|---|---|---|---|---|---|---|
| Vecka 1 | Lokal RAID-volym  | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym |
| Vecka 2 | StorSimple veckor 2-4 |   |   |   |   |   |
| Vecka 3 | StorSimple veckor 2-4 |   |   |   |   |   |
| Vecka 4 | StorSimple veckor 2-4 |   |   |   |   |   |
| Varje månad | StorSimple varje månad |   |   |   |   |   |
| Varje år | StorSimple varje år  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Tilldela StorSimple-volymer till ett Veeam kopierings jobb

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Så här tilldelar du StorSimple-volymer till ett Veeam kopierings jobb

1.  I Veeam-konsolen för säkerhets kopiering och replikering väljer du **säkerhets kopiering & replikering**. Högerklicka på **säkerhets kopiering** och välj sedan **VMware** eller **Hyper-V**, beroende på din miljö.

    ![Skärm bild som visar Veeam-hanteringskonsolen med de VMware-och Hyper-V-alternativ som du kan välja.](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  I dialog rutan **nytt säkerhets kopierings jobb** anger du ett namn och en beskrivning för jobbet.

    ![Skärm bild som visar hanterings konsolen för Veeam där du anger namn och beskrivning för jobbet.](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Välj de virtuella datorer som du vill bearbeta. Välj från säkerhets kopieringar och välj sedan den dagliga säkerhets kopia som du skapade tidigare.

    ![Veeam Management Console, ny säkerhets kopierings jobb sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Uteslut objekt från säkerhets kopierings jobbet om det behövs.

5.  Välj lagrings plats för säkerhets kopia och ange ett värde för **återställnings punkter som ska behållas**. Se till att markera kryss rutan **Behåll följande återställnings punkter för arkivering** . Definiera säkerhets kopierings frekvensen och välj sedan **Avancerat**.

    ![Skärm bild som visar var du definierar säkerhets kopierings frekvensen.](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Ange följande avancerade inställningar:

    * På fliken **Underhåll** inaktiverar du skada på lagrings nivå.

    ![Skärm bild som visar fliken Underhåll i hanterings konsolen för Veeam.](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * På fliken **lagring** kontrollerar du att deduplicering och komprimering är inaktiverat.

    ![Veeam Management Console, nya säkerhets kopierings jobb sidan Avancerade inställningar](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Ange att data överföringen är direkt.

8.  Definiera schemat för säkerhets kopierings fönstret enligt dina behov och slutför sedan guiden.

Mer information finns i [skapa säkerhets kopierings jobb](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>StorSimple moln ögonblicks bilder

StorSimple moln ögonblicks bilder skyddar de data som finns i din StorSimple-enhet. Att skapa en moln ögonblicks bild motsvarar att leverera lokala säkerhets kopierings band till en annan plats. Om du använder Azure Geo-redundant lagring, motsvarar att skapa en moln ögonblicks bild för att leverera säkerhets kopierings band till flera platser. Om du behöver återställa en enhet efter en katastrof kan du få en annan StorSimple-enhet online och göra en redundansväxling. Efter redundansväxlingen skulle du kunna komma åt data (i moln hastighet) från den senaste moln ögonblicks bilden.

I följande avsnitt beskrivs hur du skapar ett kort skript för att starta och ta bort StorSimple moln ögonblicks bilder under säkerhets kopiering efter bearbetning.

> [!NOTE]
> Ögonblicks bilder som manuellt eller program mässigt skapas följer inte StorSimple för ögonblicks bilder. De här ögonblicks bilderna måste tas bort manuellt eller program mässigt tas bort.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starta och ta bort moln ögonblicks bilder med hjälp av ett skript

> [!NOTE]
> Kontrol lera noggrant efterlevnaden och data kvarhållning innan du tar bort en StorSimple-ögonblicksbild. Mer information om hur du kör ett skript efter säkerhets kopiering finns i Veeam-dokumentationen.


### <a name="backup-lifecycle"></a>Säkerhets kopierings livs cykel

![Schema för säkerhets kopierings livs cykel](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Krav

-   Servern som kör skriptet måste ha åtkomst till Azures moln resurser.
-   Användar kontot måste ha de behörigheter som krävs.
-   En säkerhets kopierings princip för StorSimple med associerade StorSimple-volymer måste ställas in men inte aktive ras.
-   Du behöver StorSimple resurs namn, registrerings nyckel, enhets namn och säkerhets kopierings princip-ID.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Starta eller ta bort en moln ögonblicks bild

1. [Installera Azure PowerShell](/powershell/azure/).
2. Hämta och konfigurera [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-skript.
3. Kör PowerShell som administratör på den server som kör skriptet. Se till att du kör skriptet med `-WhatIf $true` för att se vilka ändringar som görs i skriptet. Skicka när verifieringen är klar `-WhatIf $false` . Kör kommandot nedan:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Om du vill lägga till skriptet i säkerhets kopierings jobbet redigerar du Veeam-jobbet avancerade alternativ.

    ![Veeam säkerhets kopiering fliken Avancerade inställningar skript](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Vi rekommenderar att du kör StorSimple för att köra säkerhets kopierings principen för moln ögonblicks bilder som ett bearbetnings skript efter ditt dagliga säkerhets kopierings jobb. Mer information om hur du säkerhetskopierar och återställer din säkerhets kopierings program miljö för att hjälpa dig att uppfylla dina återställnings-och RTO finns i säkerhets kopierings arkitekten.

## <a name="storsimple-as-a-restore-source"></a>StorSimple som återställnings källa

Återställningar från en StorSimple-enhet fungerar som återställning från valfri block lagrings enhet. Återställningar av data som är i nivå av molnet sker i moln hastighet. För lokala data sker återställningarna vid enhetens lokala disk hastighet.

Med Veeam får du snabb, detaljerad återställning på filnivå via StorSimple via de inbyggda Explorer-vyerna i Veeam-konsolen. Använd Veeam-Utforskare för att återställa enskilda objekt, t. ex. e-postmeddelanden, Active Directory objekt och SharePoint-objekt från säkerhets kopior. Återställningen kan göras utan avbrott i den lokala datorn. Du kan också göra en tidpunkts återställning för Azure SQL Database och Oracle-databaser. Veeam och StorSimple gör processen för återställning på objekt nivå från Azure snabbt och enkelt. Information om hur du utför en återställning finns i Veeam-dokumentationen:

- För [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- För [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- För [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- För [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- För [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple redundans och haveri beredskap

> [!NOTE]
> För säkerhets kopierings mål scenarier stöds inte StorSimple Cloud Appliance som ett återställnings mål.

En katastrof kan orsakas av olika faktorer. I följande tabell visas vanliga scenarier för haveri beredskap.

| Scenario | Påverkan | Återställa | Kommentarer |
|---|---|---|---|
| StorSimple enhets problem | Säkerhets kopierings-och återställnings åtgärder avbryts. | Ersätt den felande enheten och utför [StorSimple redundans och haveri beredskap](./storsimple-8000-device-failover-disaster-recovery.md). | Om du behöver utföra en återställning efter återställning av enheten hämtas fullständiga data arbets uppsättningar från molnet till den nya enheten. Alla åtgärder är i moln hastighet. Processen för att genomsöka index och genomsöka kataloger kan orsaka att alla säkerhets kopierings uppsättningar genomsöks och hämtas från moln nivån till den lokala enhets nivån, vilket kan vara en tids krävande process. |
| Veeam Server-problem | Säkerhets kopierings-och återställnings åtgärder avbryts. | Återskapa säkerhets kopierings servern och utför databas återställningen enligt beskrivningen i [Veeam Help Center (teknisk dokumentation)](https://www.veeam.com/documentation-guides-datasheets.html).  | Du måste återskapa eller återställa Veeam-servern på återställnings platsen för haveri beredskap. Återställ databasen till den senaste punkten. Om den återställda Veeam-databasen inte är synkroniserad med dina senaste säkerhets kopierings jobb krävs indexering och katalogering. Den här processen för att indexera och genomsöka kataloger kan orsaka att alla säkerhets kopierings uppsättningar genomsöks och hämtas från moln nivån till den lokala enhets nivån. Detta gör det ytterligare tids krävande. |
| Plats haveri som leder till förlust av både säkerhets kopierings servern och StorSimple | Säkerhets kopierings-och återställnings åtgärder avbryts. | Återställ StorSimple först och återställ sedan Veeam. | Återställ StorSimple först och återställ sedan Veeam. Om du behöver utföra en återställning efter enhets återställningen hämtas fullständiga data arbets uppsättningar från molnet till den nya enheten. Alla åtgärder är i moln hastighet. |


## <a name="references"></a>Referenser

Följande dokument refereras till i den här artikeln:

- [StorSimple Multipath I/O-installation](./storsimple-8000-configure-mpio-windows-server.md)
- [Lagrings scenarier: tunn allokering](/windows-hardware/drivers/storage/thin-provisioning)
- [Använda GPT-enheter](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Konfigurera skugg kopior för delade mappar](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [återställer från en säkerhets kopia](./storsimple-8000-restore-from-backup-set-u2.md).
- Läs mer om hur du utför [redundans och haveri beredskap](./storsimple-8000-device-failover-disaster-recovery.md).