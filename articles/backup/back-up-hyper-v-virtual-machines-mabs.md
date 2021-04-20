---
title: Back up Hyper-V virtual machines with MABS
description: Den här artikeln innehåller procedurer för att backa upp och återställa virtuella datorer med Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b4de791269161b477fc07d6539feaa975fdd72ad
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740006"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Back up Hyper-V virtual machines with Azure Backup Server

I den här artikeln förklaras hur du serverar virtuella Hyper-V-datorer med Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Scenarier som stöds

MABS kan servera virtuella datorer som körs på Hyper-V-värdservrar i följande scenarier:

- **Virtuella datorer med lokal eller direkt lagring** – Säkerhetskopiera virtuella datorer som finns på fristående Hyper-V-värdservrar med lokal eller direktansluten lagring. Exempel: en hårddisk, en storage area network (SAN)-enhet eller en NAS-enhet (Network Attached Storage). MABS-skyddsagenten måste vara installerad på alla värdar.

- **Virtuella datorer som finns på ett kluster med CSV-lagring** – Säkerhetskopiera virtuella datorer som finns på ett Hyper-V-kluster med lagring på klusterdelade volymer (CSV). MABS-skyddsagenten installeras på varje klusternod.

## <a name="host-versus-guest-backup"></a>Värd- och gästsäkerhetskopiering

MABS kan göra en säkerhetskopiering på värd- eller gästnivå av virtuella Hyper-V-datorer. På värdnivå installeras MABS-skyddsagenten på Hyper-V-värdservern eller klustret och skyddar hela de virtuella datorerna och datafilerna som körs på värden.   På gästnivå installeras agenten på varje virtuell dator och skyddar den arbetsbelastning som finns på den datorn.

Det för- och nackdelar med båda metoderna:

- Säkerhetskopieringar på värdnivå är flexibla eftersom de fungerar oavsett vilken typ av operativsystem som körs på gästdatorerna och inte kräver installation av MABS-skyddsagenten på varje virtuell dator. Om du distribuerar säkerhetskopiering på värdnivå kan du återställa en hel virtuell dator eller filer och mappar (återställning på objektnivå).

- Säkerhetskopiering på gästnivå är användbart om du vill skydda specifika arbetsbelastningar som körs på en virtuell dator. På värdnivå kan du återställa en hel virtuell dator eller specifika filer, men det ger inte återställning i kontexten för ett specifikt program. Om du till exempel vill återställa specifika SharePoint-objekt från en säkerhetskopierad virtuell dator bör du göra säkerhetskopiering på gästnivå av den virtuella datorn. Använd säkerhetskopiering på gästnivå om du vill skydda data som lagras på genomströmningsdiskar. Genomströmning gör att den virtuella datorn kan komma åt lagringsenheten direkt och lagrar inte virtuella volymdata i en VHD-fil.

## <a name="how-the-backup-process-works"></a>Så här fungerar säkerhetskopieringsprocessen

MABS utför säkerhetskopiering med VSS på följande sätt. Stegen i den här beskrivningen är numrerade för tydlighetens skull.

1. DEN MABS-blockbaserade synkroniseringsmotorn gör en inledande kopia av den skyddade virtuella datorn och ser till att kopian av den virtuella datorn är fullständig och konsekvent.

2. När den första kopian har gjorts och verifierats använder MABS Hyper-V VSS-skrivaren för att samla in säkerhetskopior. VSS-skrivaren tillhandahåller en data konsekvent uppsättning diskblock som synkroniseras med MABS-servern. Den här metoden ger fördelen med en "fullständig säkerhetskopiering" med MABS-servern, samtidigt som säkerhetskopieringsdata som måste överföras i nätverket minimeras.

3. MABS-skyddsagenten på en server som kör Hyper-V använder befintliga Hyper-V API:er för att avgöra om en skyddad virtuell dator också stöder VSS.

   - Om en virtuell dator uppfyller kraven för onlinesäkerhetskopiering och har komponenten för integrering av Hyper-V-tjänster installerad kommer Hyper-V VSS-skrivaren rekursivt att vidarebefordra VSS-begäran till alla VSS-medvetna processer på den virtuella datorn. Den här åtgärden utförs utan att MABS-skyddsagenten installeras på den virtuella datorn. Denna rekursiva VSS-begäran gör att Hyper-V VSS-skrivaren kan kontrollera att diskskrivningsåtgärderna synkroniseras så att en VSS-ögonblicksbild tas utan dataförlust.

     Komponenten för Hyper-V-integreringstjänsterna anropar Hyper-V VSS-skrivaren i VSS-tjänsterna (Volume Shadow Copy) på virtuella datorer för att säkerställa att deras programdata är i ett enhetligt tillstånd.

   - Om den virtuella datorn inte uppfyller kraven för onlinesäkerhetskopiering använder MABS automatiskt Hyper-V-API:erna för att pausa den virtuella datorn innan de samlar in datafiler.

4. När den första baslinjekopian av den virtuella datorn synkroniseras med MABS-servern, avbildas alla ändringar som görs i de virtuella datorresurserna i en ny återställningspunkt. Återställningspunkten representerar det konsekventa tillståndet hos den virtuella datorn vid en viss tidpunkt. Registreringar av återställningspunkter kan ske minst en gång per dag. När en ny återställningspunkt skapas använder MABS replikering på blocknivå tillsammans med Hyper-V VSS-skrivaren för att avgöra vilka block som har ändrats på servern som kör Hyper-V efter att den senaste återställningspunkten har skapats. Dessa datablock överförs sedan till MABS-servern och tillämpas på repliken av skyddade data.

5. MABS-servern använder VSS på de volymer som är värdar för återställningsdata så att flera skuggkopior är tillgängliga. Var och en av dessa skuggkopior möjliggör separat återställning. VSS-återställningspunkter lagras på MABS-servern. Den tillfälliga kopia som görs på servern som kör Hyper-V lagras bara under MABS-synkroniseringen.

>[!NOTE]
>
>Från och med Windows Server 2016 har virtuella Hyper-V-hårddiskar inbyggd ändringsspårning som kallas motståndskraftig ändringsspårning (RCT). MABS använder RCT (intern ändringsspårning i Hyper-V), vilket minskar behovet av tidskrävande konsekvenskontroller i scenarier som vm-krascher. RCT ger bättre flexibilitet än den ändringsspårning som tillhandahålls av säkerhetskopiering med VSS-ögonblicksbild. MABS V3 optimerar nätverks- och lagringsförbrukningen ytterligare genom att endast överföra ändrade data under konsekvenskontroller.

## <a name="backup-prerequisites"></a>Förutsättningar för säkerhetskopiering

Det här är förutsättningarna för att servera virtuella Hyper-V-datorer med MABS:

|Förutsättning|Information|
|------------|-------|
|Krav för MABS|– Om du vill utföra återställning på objektnivå för virtuella datorer (återställa filer, mappar, volymer) måste du installera Hyper-V-rollen på MABS-servern.  Om du bara vill återställa den virtuella datorn och inte objektnivå krävs inte rollen.<br />– Du kan skydda upp till 800 virtuella datorer på 100 GB vardera på en MABS-server och tillåta flera MABS-servrar som stöder större kluster.<br />– MABS undantar sidfilen från inkrementella säkerhetskopieringar för att förbättra prestanda vid säkerhetskopiering av virtuella datorer.<br />– MABS kan hanteraren hanteraren för en Hyper-V-server eller ett hyper-V-kluster i samma domän som MABS-servern, eller i en underordnad eller betrodd domän. Om du vill servera Hyper-V i en arbetsgrupp eller en obetrott domän måste du konfigurera autentisering. För en enskild Hyper-V-server kan du använda NTLM- eller certifikatautentisering. För ett kluster kan du endast använda certifikatautentisering.<br />-   Du kan inte använda säkerhetskopiering på värdnivå för att säkerhetskopiera data på virtuella datorer på direktlagringsdiskar. I det här scenariot rekommenderar vi att du använder säkerhetskopiering på värdnivå för att säkerhetskopiera VHD-filer och säkerhetskopiering på gästnivå för att säkerhetskopiera andra data som inte visas på värden.<br />   – Du kan komplicera virtuella datorer som lagras på deduplicerade volymer.|
|Förutsättningar för Hyper-V VM|– Versionen av integrationskomponenter som körs på den virtuella datorn ska vara samma som versionen av Hyper-V-värden. <br />-   För varje säkerhetskopiering av virtuella datorer måste du frigöra utrymme på volymen där VHD-filerna finns så att Hyper-V har tillräckligt med utrymme för differentieringsdiskar (AVHD:er) under säkerhetskopieringen. Utrymmet måste minst vara lika med beräkningen **ursprunglig hårddiskstorlek\* omsättningshastighet tidslucka för \*säkerhetskopiering**. Om du använder flera säkerhetskopieringar i ett kluster behöver du tillräckligt mycket lagringskapacitet för att få plats med AVHD:erna för var och en av de virtuella datorerna baserat på den här beräkningen.<br />– Om du vill domänkontrollanter på Hyper-V-värdservrar som kör Windows Server 2012 R2 ska den virtuella datorn ha en angiven SCSI-styrenhet, även om den inte är ansluten till något. (I Windows Server 2012 R2-säkerhetskopiering monterar Hyper-V-värden en ny virtuell hårddisk på den virtuella datorn och demonterar den senare. Endast SCSI-styrenheten har stöd för detta och krävs därför för onlinesäkerhetskopiering av den virtuella datorn.  Utan den här inställningen utfärdas händelse-ID 10103 när du försöker brygga den virtuella datorn.)|
|Förutsättningar för Linux|– Du kan backa upp virtuella Linux-datorer med MABS. Endast filkonsekventa ögonblicksbilder stöds.|
|Säkerhetskopiera virtuella datorer med CSV-lagring|-   För CSV-lagring installerar du maskinvaruprovidern Volume Shadow Copy Services (VSS) på Hyper-V-servern. Kontakta din SAN-leverantör (Storage Area Network) för VSS-maskinvaruprovidern.<br />– Om en enskild nod oväntat stängs av i ett CSV-kluster utför MABS en konsekvenskontroll mot de virtuella datorer som kördes på noden.<br />-   Om du behöver starta om en Hyper-V-server som har BitLocker-diskkryptering aktiverat för CSV-klustret måste du utföra en konsekvenskontroll för virtuella Hyper-V-datorer.|
|Säkerhetskopiera virtuella datorer med SMB-lagring|– Aktivera automatisk montering på den server som kör Hyper-V för att aktivera skydd av virtuella datorer.<br />   -   Inaktivera TCP Chimney-avlastning.<br />-   Kontrollera att alla Hyper-V:s machine$-konton har fullständig behörighet till de aktuella SMB-filresurserna.<br />– Kontrollera att sökvägen för alla komponenter i den virtuella datorn under återställningen till en alternativ plats är mindre än 260 tecken. Annars kan återställningen lyckas, men Hyper-V kan inte montera den virtuella datorn.<br />– Följande scenarier stöds inte:<br />     Distributioner där vissa komponenter i den virtuella datorn finns på lokala volymer och vissa komponenter finns på fjärrvolymer; en IPv4- eller IPv6-adress för lagringsplatsens filserver och återställning av en virtuell dator till en dator som använder SMB-fjärrresurser.<br />- Du måste aktivera VSS-agenttjänsten för filserver på varje SMB-server – Lägg till den i Lägg till roller och funktioner Välj serverroller Fil- och lagringstjänster Filtjänster Filtjänst filserver  >    >    >    >    >  **VSS-agenttjänst**.|

## <a name="back-up-virtual-machines"></a>Säkerhetskopiera virtuella datorer

1. Konfigurera [MABS-servern och](backup-azure-microsoft-azure-backup.md) [lagringen](backup-mabs-add-storage.md). Använd följande riktlinjer för lagringskapacitet när du konfigurerar lagringen.
   - Genomsnittlig storlek för virtuell dator – 100 GB
   - Antal virtuella datorer per MABS-server – 800
   - Total storlek på 800 virtuella datorer – 80 TB
   - Utrymme som krävs för lagring av säkerhetskopior – 80 TB

2. Konfigurera MABS-skyddsagenten på Hyper-V-servern eller Hyper-V-klusternoderna.

3. I MABS-administratörskonsolen väljer **du**  >  **Skapa skyddsgrupp för** att öppna guiden Skapa ny **skyddsgrupp.**

4. På sidan **Välj gruppmedlemmar** väljer du de virtuella datorer som du vill skydda (från de Hyper-V-värdservrar som de virtuella datorerna finns på). Vi rekommenderar att du placerar alla virtuella datorer som har samma skyddsprincip i samma skyddsgrupp. Du får en effektivare användning av diskutrymmet om du aktiverar samplacering. Med samplacering kan du hitta data i olika skyddsgrupper på samma disk- eller bandlagring. Flera datakällor får då en enda replik och återställningspunktvolym.

5. På sidan **Välj dataskyddsmetod** anger du ett skyddsgruppnamn. Välj **Jag vill ha kortvarigt skydd med disk** och välj **Jag vill ha ett onlineskydd** om du vill säkerhetskopiera data till Azure med tjänsten Azure Backup.

6. I **Ange Short-Term mål**  >  **kvarhållningsintervall** anger du hur länge du vill behålla diskdata. I **Synkroniseringsfrekvens** anger du hur ofta inkrementella säkerhetskopieringar av data ska köras. Du kan också välja att aktivera **Precis innan en återställningspunkt** i stället för att välja ett intervall för inkrementell säkerhetskopiering. När den här inställningen är aktiverad kör MABS en fullständig snabbsäkerhetskopiering precis före varje schemalagd återställningspunkt.

    > [!NOTE]
    >
    >Om du skyddar programarbetsbelastningarna skapas återställningspunkter i enlighet med synkroniseringsfrekvensen under förutsättning att programmet har stöd för inkrementella säkerhetskopior. Om den inte gör det kör MABS en fullständig snabbsäkerhetskopiering i stället för en inkrementell säkerhetskopiering och skapar återställningspunkter i enlighet med schemat för snabbsäkerhetskopiering.<br></br>Säkerhetskopieringen säkerhetskopierar inte kontrollpunkterna som är associerade med virtuella datorer.

7. På sidan **Granska diskallokering granskar** du lagringspoolens allokerade diskutrymme för skyddsgruppen.

   **Total datastorlek** är storleken på de data som du vill storlekar på och Diskutrymme som ska etableras på **MABS** är det utrymme som MABS rekommenderar för skyddsgruppen. MABS väljer den perfekta säkerhetskopieringsvolymen baserat på inställningarna. Du kan dock redigera valen av säkerhetskopieringsvolym under **Disk allocation details (Diskallokeringsdetaljer)**. Välj önskad lagringsplats för arbetsbelastningarna i den nedrullningsbara menyn. Redigeringarna ändrar värdena för **Totalt lagringsutrymme** och **Ledigt lagringsutrymme** i fönstret **Tillgängligt disklagringsutrymme**. Underetableerat utrymme är mängden lagringsutrymme som MABS föreslår att du lägger till i volymen för att fortsätta med säkerhetskopieringar smidigt i framtiden.

8. På sidan **Välj metod för skapande av replik** anger du hur den första replikeringen av data i skyddsgruppen ska utföras. Om du väljer **Replikera automatiskt över nätverket** rekommenderar vi att du väljer en tid med låg belastning. För stora mängder data eller mindre än optimala nätverksförhållanden kan du välja Manuellt **,** vilket kräver att data replikeras offline med hjälp av flyttbara medier.

9. På sidan **Alternativ för konsekvenskontroll** väljer du hur du vill automatisera konsekvenskontroller. Du kan aktivera att en kontroll endast körs när replikdata blir inkonsekvent, eller enligt ett schema. Om du inte vill konfigurera automatiska konsekvenskontroller kan du när som helst köra en manuell kontroll genom att högerklicka på skyddsgruppen och välja **Utför konsekvenskontroll**.

    När du har skapat skyddsgruppen utförs inledande replikering av data i enlighet med den metod som du har valt. Efter den inledande replikeringen utförs varje säkerhetskopiering i enlighet med inställningarna för skyddsgruppen. Tänk på följande om du behöver återställa säkerhetskopierade data:

## <a name="back-up-replica-virtual-machines"></a>Säkerhetskopiera virtuella replikdatorer

Om MABS körs på Windows Server 2012 R2 eller högre kan du kopier virtuella replikdatorer. Detta är användbart av flera skäl:

**Det minskar säkerhetskopieringens inverkan på arbetsbelastningen som körs** – När du gör en säkerhetskopia av en virtuell dator krävs vissa extra resurser när en ögonblicksbild skapas. Genom att avlasta säkerhetskopieringsprocessen till en sekundär fjärrplats påverkas inte längre den arbetsbelastning som körs av säkerhetskopieringen. Detta gäller endast för distributioner där säkerhetskopian lagras på en fjärrplats. Du kan till exempel göra dagliga säkerhetskopior och lagra data lokalt för att säkerställa snabba återställningstider, och samtidigt göra månadsvisa eller kvartalsvisa säkerhetskopior från virtuella replikdatorer som fjärrlagras för långsiktig kvarhållning.

**Sparar bandbredd** – I en typisk distribution för fjärranslutna avdelningskontor/huvudkontor behöver du en lämplig mängd etablerad bandbredd för att överföra säkerhetskopieringsdata mellan olika platser. Om du skapar en replikerings- och redundansstrategi utöver din säkerhetskopieringsstrategi kan du minska mängden redundanta data som skickas över nätverket. Genom att säkerhetskopiera den virtuella replikdatorns data i stället för den primära, sparar du arbetet med att skicka säkerhetskopierade data över nätverket.

**Aktiverar värdleverantörens säkerhetskopiering** – Du kan använda ett värdbaserat datacenter som en replikplats utan behov av ett sekundärt datacenter. I det här fallet kräver värd-SLA konsekvent säkerhetskopiering av virtuella replikdatorer.

En virtuell replikdator är inaktiverad till dess att redundansväxling initieras, och VSS kan inte garantera en programkonsekvent säkerhetskopiering för en virtuell replikdator. Säkerhetskopieringen av en virtuell replikdator kommer därför endast att krascha konsekvent. Om kraschkonsekvens inte kan garanteras misslyckas säkerhetskopieringen. Detta kan inträffa i ett antal situationer:

- Den virtuella replikdatorn är inte felfri och är i ett kritiskt tillstånd.

- Den virtuella replikdatorn omsynkroniseras (den är i något av tillstånden Omsynkronisering pågår eller Omsynkronisering krävs).

- Den första replikeringen mellan den primära och den sekundära platsen pågår eller väntar på den virtuella datorn.

- .hrl-loggar tillämpas på den virtuella replikdatorn, eller en tidigare åtgärd för att tillämpa HRL-loggarna på den virtuella disken misslyckades, eller avbröts eller avbröts.

- Migrering eller redundans av den virtuella replikdatorn pågår

## <a name="recover-backed-up-virtual-machines"></a>Återställa säkerhetskopierade virtuella datorer

När du kan återställa en säkerhetskopierade virtuell dator använder du guiden Återställning för att välja den virtuella datorn och specifik återställningspunkt. Så här öppnar du guiden Återställning och återställer en virtuell dator:

1. I MABS-administratörskonsolen skriver du namnet på den virtuella datorn eller expanderar listan över skyddade objekt, navigerar till Alla skyddade **HyperV-data** och väljer den virtuella dator som du vill återställa.

2. I fönstret **Återställningspunkter för** väljer du ett datum i kalendern för att se tillgängliga återställningspunkter. Välj den återställningspunkt som du vill använda på menyn **Sökväg** i återställningsguiden.

3. På menyn **Åtgärder** väljer du **Återställ för** att öppna återställningsguiden.

    Den virtuella dator och återställningspunkt som du har valt visas på skärmen **Granska val av återställning**. Välj **Nästa**.

4. På skärmen **Välj återställningstyp** väljer du var du vill återställa data och väljer sedan **Nästa.**

    - **Återställ till den ursprungliga instansen:** När du återställer till den ursprungliga instansen tas den ursprungliga virtuella hårddisken och alla associerade kontrollpunkter bort. MABS återställer den virtuella hårddisken och andra konfigurationsfiler till den ursprungliga platsen med hjälp av Hyper-V VSS-skrivaren. I slutet av återställningsprocessen är de virtuella datorerna fortfarande högtillgängliga.
        Resursgruppen måste vara tillgänglig för återställning. Om den inte är tillgänglig återställer du till en annan plats och gör sedan den virtuella datorn högtillgänglig.

    - **Återställ som virtuell dator till** valfri värd: MABS stöder återställning av alternativ plats, vilket ger sömlös återställning av en skyddad virtuell Hyper-V-dator till en annan Hyper-V-värd, oberoende av processorarkitekturen. Virtuella Hyper-V-datorer som återställs till en klusternod har inte hög tillgänglig. Om du väljer det här alternativet visas ytterligare en skärm i Återställningsguiden där du kan identifiera mål och målsökväg.
    
        >[!NOTE]
        >Om du väljer den ursprungliga värden är beteendet detsamma som **Återställ till den ursprungliga instansen**. Den ursprungliga virtuella hårddisken och alla associerade kontrollpunkter tas bort.

    - Kopiera **till** en nätverksmapp: MABS stöder återställning på objektnivå (ILR), vilket gör att du kan återställa filer, mappar, volymer och virtuella hårddiskar (VHD) på objektnivå från en säkerhetskopiering på värdnivå av virtuella Hyper-V-datorer till en nätverksresurs eller en volym på en MABS-skyddad server. MABS-skyddsagenten behöver inte installeras i gästen för att utföra återställning på objektnivå. Om du väljer det här alternativet visas ytterligare en skärm i Återställningsguiden där du kan identifiera mål och målsökväg.

5. I **Ange återställningsalternativ konfigurerar** du återställningsalternativen och väljer **Nästa:**

    - Om du återställer en virtuell dator med låg bandbredd väljer du Ändra **för** att aktivera **Begränsning av nätverksbandbredd.** Du kan ange hur mycket bandbredd som du vill göra tillgänglig och den tidpunkt då bandbredd är tillgänglig när du har aktiverat alternativet för begränsning.
    - Välj **Aktivera SAN-baserad återställning med hjälp av ögonblicksbilder** av maskinvara om du har konfigurerat nätverket.
    - Välj **Send an e-mail when the recovery complete (Skicka ett e-postmeddelande när återställningen har slutförts)** och ange de e-postadresserna om du vill att e-postmeddelandena ska skickas när återställningen är klar.

6. Kontrollera att alla uppgifter i fönstret Sammanfattning är korrekta. Om informationen inte är korrekt, eller om du vill göra en ändring, väljer du **Tillbaka**. Om du är nöjd med inställningarna väljer du **Återställ för** att starta återställningsprocessen.

7. Skärmen **Återställningsstatus** innehåller information om återställningsjobbet.

## <a name="next-steps"></a>Nästa steg

[Återställa data från Azure Backup Server](./backup-azure-alternate-dpm-server.md)
