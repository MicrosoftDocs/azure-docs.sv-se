---
title: Säkerhetskopiera virtuella Hyper-V-datorer med MABS
description: Den här artikeln innehåller procedurer för att säkerhetskopiera och återställa virtuella datorer med hjälp av Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: a020559229771fff1ecc8fb512a5b2af70240cdd
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509514"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Säkerhetskopiera virtuella Hyper-V-datorer med Azure Backup Server

Den här artikeln förklarar hur du säkerhetskopierar virtuella Hyper-V-datorer med hjälp av Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Scenarier som stöds

MABS kan säkerhetskopiera virtuella datorer som körs på Hyper-V-värd servrar i följande scenarier:

- **Virtuella datorer med lokal eller direkt lagring** – Säkerhetskopiera virtuella datorer som finns på fristående Hyper-V-värdservrar med lokal eller direktansluten lagring. Till exempel: en hård disk, en storage area network-enhet (SAN) eller en nätverksansluten lagrings enhet (NAS). MABS-skyddsagenten måste vara installerad på alla värdar.

- **Virtuella datorer som finns på ett kluster med CSV-lagring** – Säkerhetskopiera virtuella datorer som finns på ett Hyper-V-kluster med lagring på klusterdelade volymer (CSV). MABS-skyddsagenten installeras på varje klusternod.

## <a name="host-versus-guest-backup"></a>Värd kontra gäst säkerhets kopiering

MABS kan göra en säkerhets kopia av virtuella Hyper-V-datorer på värd-eller gästnivå. På värdnivå är MABS-skyddsagenten installerad på Hyper-V-värdservern eller klustret och skyddar alla virtuella datorer och datafiler som körs på värden.   På gästnivå är agenten installerad på varje virtuell dator och skyddar arbets belastningen på den datorn.

Det för- och nackdelar med båda metoderna:

- Säkerhets kopiering på värdnivå är flexibla eftersom de fungerar oavsett vilken typ av operativ system som körs på gäst datorerna och inte kräver installation av MABS-skyddsagenten på varje virtuell dator. Om du distribuerar säkerhets kopiering på värdnivå kan du återställa en hel virtuell dator eller filer och mappar (återställning på objekt nivå).

- Säkerhets kopiering på gästnivå är användbart om du vill skydda vissa arbets belastningar som körs på en virtuell dator. På värdnivå kan du återställa en hel virtuell dator eller specifika filer, men den ger inte återställnings kontexten för ett särskilt program. Om du till exempel vill återställa vissa SharePoint-objekt från en säkerhets kopie rad virtuell dator bör du göra en säkerhets kopia av den virtuella datorn på gästnivå. Använd säkerhets kopiering på gästnivå om du vill skydda data som lagras på genom strömnings diskar. Genom strömning får den virtuella datorn direkt åtkomst till lagrings enheten och lagrar inte virtuella volym data i en VHD-fil.

## <a name="how-the-backup-process-works"></a>Så här fungerar säkerhets kopieringen

MABS utför säkerhets kopiering med VSS på följande sätt. Stegen i den här beskrivningen är numrerade för tydlighetens skull.

1. MABS-block-based Synchronization-motorn gör en ursprunglig kopia av den skyddade virtuella datorn och ser till att kopian av den virtuella datorn är fullständig och konsekvent.

2. När den första kopian har gjorts och verifierats använder MABS Hyper-V VSS-skrivaren för att samla in säkerhets kopior. VSS-skrivaren tillhandahåller en data enhetlig uppsättning disk block som är synkroniserade med MABS-servern. Den här metoden ger fördelen med en fullständig säkerhets kopiering med MABS-servern och minimerar de säkerhets kopierings data som måste överföras i nätverket.

3. MABS-skyddsagenten på en server där Hyper-V körs använder de befintliga Hyper-V API: erna för att avgöra om en skyddad virtuell dator också har stöd för VSS.

   - Om en virtuell dator uppfyller kraven för onlinesäkerhetskopiering och har komponenten för integrering av Hyper-V-tjänster installerad kommer Hyper-V VSS-skrivaren rekursivt att vidarebefordra VSS-begäran till alla VSS-medvetna processer på den virtuella datorn. Den här åtgärden utförs utan att MABS-skyddsagenten installeras på den virtuella datorn. Denna rekursiva VSS-begäran gör att Hyper-V VSS-skrivaren kan kontrollera att diskskrivningsåtgärderna synkroniseras så att en VSS-ögonblicksbild tas utan dataförlust.

     Komponenten för Hyper-V-integreringstjänsterna anropar Hyper-V VSS-skrivaren i VSS-tjänsterna (Volume Shadow Copy) på virtuella datorer för att säkerställa att deras programdata är i ett enhetligt tillstånd.

   - Om den virtuella datorn inte följer säkerhets kopierings kraven online använder MABS automatiskt Hyper-V-API: erna för att pausa den virtuella datorn innan de fångar in datafiler.

4. När den ursprungliga bas linje kopian av den virtuella datorn har synkroniserats med MABS-servern, fångas alla ändringar som görs i de virtuella dator resurserna på en ny återställnings punkt. Återställningspunkten representerar det konsekventa tillståndet hos den virtuella datorn vid en viss tidpunkt. Registreringar av återställningspunkter kan ske minst en gång per dag. När en ny återställnings punkt skapas använder MABS block nivå replikering tillsammans med Hyper-V VSS-skrivaren för att avgöra vilka block som har ändrats på den server där Hyper-V körs när den sista återställnings punkten skapades. Dessa data block överförs sedan till MABS-servern och tillämpas på repliken av skyddade data.

5. MABS-servern använder VSS på de volymer som är värdar för återställnings data så att flera skugg kopior är tillgängliga. Var och en av dessa skuggkopior möjliggör separat återställning. VSS-återställnings punkter lagras på MABS-servern. Den tillfälliga kopia som görs på servern som kör Hyper-V lagras bara under hela MABS-synkroniseringen.

>[!NOTE]
>
>Från och med Windows Server 2016 har Hyper-V-virtuella hård diskar inbyggd ändrings spårning som kallas elastisk ändrings spårning (RCT). MABS använder RCT (den interna ändrings spårningen i Hyper-V), vilket minskar behovet av konsekvens kontroller i scenarier som VM kraschar. RCT ger bättre flexibilitet än den ändringsspårning som tillhandahålls av säkerhetskopiering med VSS-ögonblicksbild. MABS v3 optimerar användningen av nätverks-och lagrings utrymme ytterligare genom att endast överföra ändrade data under en konsekvens kontroll.

## <a name="backup-prerequisites"></a>Förutsättningar för säkerhetskopiering

Detta är kraven för att säkerhetskopiera virtuella Hyper-V-datorer med MABS:

|Förutsättning|Information|
|------------|-------|
|MABS-krav|– Om du vill utföra återställning på objekt nivå för virtuella datorer (återställa filer, mappar, volymer) måste du installera Hyper-V-rollen på MABS-servern.  Om du bara vill återställa den virtuella datorn och inte på objekt nivå så krävs inte rollen.<br />-Du kan skydda upp till 800 virtuella datorer på 100 GB vardera på en MABS-Server och tillåta flera MABS-servrar som stöder större kluster.<br />-MABS utesluter växlings filen från stegvisa säkerhets kopieringar för att förbättra prestanda för säkerhets kopiering av virtuella datorer.<br />-MABS kan säkerhetskopiera en Hyper-V-server eller ett kluster i samma domän som MABS-servern eller i en underordnad eller betrodd domän. Om du vill säkerhetskopiera Hyper-V i en arbets grupp eller en ej betrodd domän måste du konfigurera autentisering. Du kan använda NTLM eller certifikatautentisering för en enskild Hyper-V-server. Du kan endast använda certifikatautentisering för ett kluster.<br />-   Du kan inte använda säkerhetskopiering på värdnivå för att säkerhetskopiera data på virtuella datorer på direktlagringsdiskar. I det här scenariot rekommenderar vi att du använder säkerhets kopiering på värdnivå för att säkerhetskopiera VHD-filer och säkerhets kopiering på gästnivå för att säkerhetskopiera andra data som inte är synliga på värden.<br />   -Du kan säkerhetskopiera virtuella datorer som lagras på deduplicerade volymer.|
|Förutsättningar för Hyper-V VM|-Versionen av integrations komponenterna som körs på den virtuella datorn måste vara samma som Hyper-V-värdens version. <br />-   För varje säkerhetskopiering av virtuella datorer måste du frigöra utrymme på volymen där VHD-filerna finns så att Hyper-V har tillräckligt med utrymme för differentieringsdiskar (AVHD:er) under säkerhetskopieringen. Utrymmet måste minst vara lika med beräkningen **ursprunglig hårddiskstorlek\* omsättningshastighet tidslucka för \*säkerhetskopiering**. Om du använder flera säkerhetskopieringar i ett kluster behöver du tillräckligt mycket lagringskapacitet för att få plats med AVHD:erna för var och en av de virtuella datorerna baserat på den här beräkningen.<br />– Om du vill säkerhetskopiera virtuella datorer som finns på Hyper-V-värd servrar som kör Windows Server 2012 R2, måste den virtuella datorn ha en angiven SCSI-styrenhet, även om den inte är ansluten till något. (I Windows Server 2012 R2 online backup monterar Hyper-V-värden en ny virtuell hård disk i den virtuella datorn och demonterar sedan den senare. Endast SCSI-styrenheten stöder detta och krävs därför för onlinesäkerhetskopiering av den virtuella datorn.  Utan den här inställningen kommer händelse-ID 10103 att utfärdas när du försöker säkerhetskopiera den virtuella datorn.)|
|Förutsättningar för Linux|– Du kan säkerhetskopiera virtuella Linux-datorer med MABS. Endast filkonsekventa ögonblicksbilder stöds.|
|Säkerhetskopiera virtuella datorer med CSV-lagring|-   För CSV-lagring installerar du maskinvaruprovidern Volume Shadow Copy Services (VSS) på Hyper-V-servern. Kontakta din SAN-leverantör (Storage Area Network) för VSS-maskinvaruprovidern.<br />– Om en enda nod stängs av utan förvarning i ett CSV-kluster utför MABS en konsekvens kontroll mot de virtuella datorer som kördes på noden.<br />-   Om du behöver starta om en Hyper-V-server som har BitLocker-diskkryptering aktiverat för CSV-klustret måste du utföra en konsekvenskontroll för virtuella Hyper-V-datorer.|
|Säkerhetskopiera virtuella datorer med SMB-lagring|– Aktivera automatisk montering på den server som kör Hyper-V för att aktivera skydd av virtuella datorer.<br />   -   Inaktivera TCP Chimney-avlastning.<br />-   Kontrollera att alla Hyper-V:s machine$-konton har fullständig behörighet till de aktuella SMB-filresurserna.<br />– Kontrol lera att fil Sök vägen för alla virtuella dator komponenter under återställningen till den alternativa platsen är färre än 260 tecken. Annars kan återställningen lyckas, men Hyper-V kan inte montera den virtuella datorn.<br />-Följande scenarier stöds inte:<br />     Distributioner där vissa komponenter i den virtuella datorn finns på lokala volymer och vissa komponenter finns på fjärrvolymer. en IPv4-eller IPv6-adress för lagrings platsens fil server och återställning av en virtuell dator till en dator som använder SMB-fjärrresurser.<br />-Du måste aktivera VSS-agenttjänsten för fil server på varje SMB-server – Lägg till den i **Lägg till roller och funktioner**  >  **Välj Server roller**  >  **fil-och lagrings tjänster**  >  **fil**  >  **tjänst fil tjänst**  >  **VSS-agenttjänsten**.|

## <a name="back-up-virtual-machines"></a>Säkerhetskopiera virtuella datorer

1. Konfigurera din [Mabs-Server](backup-azure-microsoft-azure-backup.md) och [lagrings utrymme](backup-mabs-add-storage.md). Använd följande riktlinjer för lagringskapacitet när du konfigurerar lagringen.
   - Genomsnittlig storlek för virtuell dator – 100 GB
   - Antal virtuella datorer per MABS-Server – 800
   - Total storlek på 800 virtuella datorer – 80 TB
   - Utrymme som krävs för lagring av säkerhetskopior – 80 TB

2. Konfigurera skydds agenten för MABS på Hyper-V-servern eller Hyper-V-klusternoderna. Om du utför säkerhets kopiering på gästnivå installerar du agenten på de virtuella datorer som du vill säkerhetskopiera på gästnivå.

3. I administratörs konsolen för Mabs väljer du **skydd**  >  **skapa skydds grupp** för att öppna guiden **Skapa ny skydds grupp** .

4. På sidan **Välj gruppmedlemmar** väljer du de virtuella datorer som du vill skydda (från de Hyper-V-värdservrar som de virtuella datorerna finns på). Vi rekommenderar att du placerar alla virtuella datorer som har samma skyddsprincip i samma skyddsgrupp. Du får en effektivare användning av diskutrymmet om du aktiverar samplacering. Med samplacering kan du hitta data i olika skyddsgrupper på samma disk- eller bandlagring. Flera datakällor får då en enda replik och återställningspunktvolym.

5. På sidan **Välj dataskyddsmetod** anger du ett skyddsgruppnamn. Välj **Jag vill ha kortvarigt skydd med disk** och välj **Jag vill ha ett onlineskydd** om du vill säkerhetskopiera data till Azure med tjänsten Azure Backup.

6. I **Ange Short-Term mål** för  >  **kvarhållning** anger du hur länge du vill behålla disk data. I **Synkroniseringsfrekvens** anger du hur ofta stegvis säkerhets kopiering av data ska köras. Du kan också välja att aktivera **Precis innan en återställningspunkt** i stället för att välja ett intervall för inkrementell säkerhetskopiering. När den här inställningen är aktive rad kommer MABS att köra en fullständig snabb säkerhets kopiering precis innan varje schemalagd återställnings punkt.

    > [!NOTE]
    >
    >Om du skyddar programarbetsbelastningarna skapas återställningspunkter i enlighet med synkroniseringsfrekvensen under förutsättning att programmet har stöd för inkrementella säkerhetskopior. Om den inte gör det kör MABS en fullständig snabb säkerhets kopiering i stället för en stegvis säkerhets kopiering och skapar återställnings punkter i enlighet med schemat för snabb säkerhets kopiering.

7. På sidan **Granska diskallokering** granskar du allokerat disk utrymme för lagringspoolen för skydds gruppen.

   **Total data storlek** är storleken på de data som du vill säkerhetskopiera och **disk utrymme som ska tillhandahållas på Mabs** är det utrymme som Mabs rekommenderar för skydds gruppen. MABS väljer den ideala säkerhets kopierings volymen baserat på inställningarna. Du kan dock redigera valen av säkerhetskopieringsvolym under **Disk allocation details (Diskallokeringsdetaljer)**. Välj önskad lagringsplats för arbetsbelastningarna i den nedrullningsbara menyn. Redigeringarna ändrar värdena för **Totalt lagringsutrymme** och **Ledigt lagringsutrymme** i fönstret **Tillgängligt disklagringsutrymme**. Underetablerat utrymme är mängden lagrings MABS som föreslår att du lägger till volymen, för att fortsätta med säkerhets kopieringar smidigt i framtiden.

8. På sidan **Välj metod för skapande av replik** anger du hur den första replikeringen av data i skyddsgruppen ska utföras. Om du väljer att **Replikera över nätverket automatiskt** rekommenderar vi att du väljer en tid med låg belastning. För stora mängder data eller mindre än optimala nätverks förhållanden bör du överväga att välja **manuellt**, vilket kräver att replikera data offline med hjälp av flyttbara medier.

9. På sidan **Alternativ för konsekvenskontroll** väljer du hur du vill automatisera konsekvenskontroller. Du kan aktivera att en kontroll endast körs när replikdata blir inkonsekvent, eller enligt ett schema. Om du inte vill konfigurera automatiska konsekvenskontroller kan du när som helst köra en manuell kontroll genom att högerklicka på skyddsgruppen och välja **Utför konsekvenskontroll**.

    När du har skapat skyddsgruppen utförs inledande replikering av data i enlighet med den metod som du har valt. Efter den inledande replikeringen utförs varje säkerhetskopiering i enlighet med inställningarna för skyddsgruppen. Tänk på följande om du behöver återställa säkerhetskopierade data:

## <a name="back-up-replica-virtual-machines"></a>Säkerhetskopiera virtuella replikdatorer

Om MABS körs på Windows Server 2012 R2 eller senare kan du säkerhetskopiera virtuella replik datorer. Detta är användbart av flera skäl:

**Det minskar säkerhetskopieringens inverkan på arbetsbelastningen som körs** – När du gör en säkerhetskopia av en virtuell dator krävs vissa extra resurser när en ögonblicksbild skapas. Genom att avlasta säkerhets kopierings processen till en sekundär fjärrplats påverkas inte längre den aktiva arbets belastningen av säkerhets kopierings åtgärden. Detta gäller endast för distributioner där säkerhetskopian lagras på en fjärrplats. Du kan till exempel göra dagliga säkerhetskopior och lagra data lokalt för att säkerställa snabba återställningstider, och samtidigt göra månadsvisa eller kvartalsvisa säkerhetskopior från virtuella replikdatorer som fjärrlagras för långsiktig kvarhållning.

**Sparar bandbredd** – I en typisk distribution för fjärranslutna avdelningskontor/huvudkontor behöver du en lämplig mängd etablerad bandbredd för att överföra säkerhetskopieringsdata mellan olika platser. Om du skapar en replikerings- och redundansstrategi utöver din säkerhetskopieringsstrategi kan du minska mängden redundanta data som skickas över nätverket. Genom att säkerhetskopiera den virtuella replik datorns data i stället för den primära, sparar du omkostnaderna för att skicka säkerhetskopierade data över nätverket.

**Aktiverar värdleverantörens säkerhetskopiering** – Du kan använda ett värdbaserat datacenter som en replikplats utan behov av ett sekundärt datacenter. I det här fallet kräver värd avtalets service avtal konsekvent säkerhets kopiering av virtuella replik datorer.

En virtuell replikdator är inaktiverad till dess att redundansväxling initieras, och VSS kan inte garantera en programkonsekvent säkerhetskopiering för en virtuell replikdator. Det innebär att säkerhets kopieringen av en virtuell replik dator blir krasch-konsekvent. Om kraschkonsekvens inte kan garanteras misslyckas säkerhetskopieringen. Detta kan inträffa i ett antal situationer:

- Den virtuella replikdatorn är inte felfri och är i ett kritiskt tillstånd.

- Den virtuella replikdatorn omsynkroniseras (den är i något av tillstånden Omsynkronisering pågår eller Omsynkronisering krävs).

- Den första replikeringen mellan den primära och den sekundära platsen pågår eller väntar på den virtuella datorn.

- . HRL-loggar tillämpas på den virtuella replik datorn eller en tidigare åtgärd för att tillämpa. HRL-loggarna på den virtuella disken misslyckades eller avbröts eller avbröts.

- Migrering eller redundans av den virtuella replikdatorn pågår

## <a name="recover-backed-up-virtual-machines"></a>Återställa säkerhetskopierade virtuella datorer

När du kan återställa en säkerhetskopierade virtuell dator använder du guiden Återställning för att välja den virtuella datorn och specifik återställningspunkt. Så här öppnar du guiden Återställning och återställer en virtuell dator:

1. I administratörs konsolen för MABS anger du namnet på den virtuella datorn eller expanderar listan över skyddade objekt och väljer den virtuella dator som du vill återställa.

2. I rutan **återställnings punkter för** väljer du ett datum i kalendern för att se tillgängliga återställnings punkter. Välj den återställningspunkt som du vill använda på menyn **Sökväg** i återställningsguiden.

3. Från menyn **åtgärder** väljer du **Återställ** för att öppna återställnings guiden.

    Den virtuella dator och återställningspunkt som du har valt visas på skärmen **Granska val av återställning**. Välj **Nästa**.

4. På skärmen **Välj återställnings typ** väljer du var du vill återställa data och väljer sedan **Nästa**.

    - **Återställ till ursprunglig instans**: när du återställer till den ursprungliga instansen raderas den ursprungliga virtuella hård disken och alla tillhör ande kontroll punkter. MABS återställer den virtuella hård disken och andra konfigurationsfiler till den ursprungliga platsen med hjälp av VSS-skrivaren för Hyper-V. I slutet av återställningsprocessen är de virtuella datorerna fortfarande högtillgängliga.
        Resursgruppen måste vara tillgänglig för återställning. Om den inte är tillgänglig återställer du till en annan plats och gör sedan den virtuella datorn högtillgänglig.

    - **Återställ som virtuell dator till valfri värd**: Mabs stöder alternativ plats återställning (återställning till), vilket ger en sömlös återställning av en skyddad virtuell Hyper-v-dator till en annan Hyper-v-värd, oberoende av processor arkitekturen. Virtuella Hyper-V-datorer som återställs till en klusternod blir inte hög tillgängliga. Om du väljer det här alternativet visas ytterligare en skärm i Återställningsguiden där du kan identifiera mål och målsökväg.
    
        >[!NOTE]
        >Om du väljer den ursprungliga värden är beteendet detsamma som **Återställ till ursprunglig instans**. Den ursprungliga virtuella hård disken och alla tillhör ande kontroll punkter tas bort.

    - **Kopiera till en nätverksmapp**: Mabs har stöd för återställning på objekt nivå (ILR), vilket gör att du kan utföra återställning på objekt nivå av filer, mappar, volymer och virtuella hård diskar (VHD) från en säkerhets kopiering på värdnivå av virtuella Hyper-V-datorer till en nätverks resurs eller en volym på en Mabs-skyddad Server. MABS-skyddsagenten behöver inte vara installerad på gästen för att utföra återställning på objekt nivå. Om du väljer det här alternativet visas ytterligare en skärm i Återställningsguiden där du kan identifiera mål och målsökväg.

5. I **Ange återställnings alternativ** konfigurerar du återställnings alternativen och väljer **Nästa**:

    - Om du återställer en virtuell dator över låg bandbredd väljer du **ändra** för att aktivera **begränsning av nätverks bandbredd**. Du kan ange hur mycket bandbredd som du vill göra tillgänglig och den tidpunkt då bandbredd är tillgänglig när du har aktiverat alternativet för begränsning.
    - Välj **Aktivera San-baserad återställning med hjälp av ögonblicks bilder av maskin vara** om du har konfigurerat nätverket.
    - Välj **Send an e-mail when the recovery complete (Skicka ett e-postmeddelande när återställningen har slutförts)** och ange de e-postadresserna om du vill att e-postmeddelandena ska skickas när återställningen är klar.

6. Kontrollera att alla uppgifter i fönstret Sammanfattning är korrekta. Om informationen inte är korrekt eller om du vill göra en ändring väljer du **bakåt**. Om du är nöjd med inställningarna väljer du **Återställ** för att starta återställnings processen.

7. Skärmen **Återställningsstatus** innehåller information om återställningsjobbet.

## <a name="next-steps"></a>Nästa steg

[Återställa data från Azure Backup Server](./backup-azure-alternate-dpm-server.md)
