---
title: Planera för distribution av Azure Files | Microsoft Docs
description: Att planera för en Azure Files distribution. Du kan antingen direkt montera en Azure-filresurs eller cachelagra Azure-filresurs lokalt med Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: aab06b4870efd88893b4a14c1127de7ffcd2ba68
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520539"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planera för distribution av Azure Files
[Azure Files](storage-files-introduction.md) kan distribueras på två huvudsakliga sätt: genom att montera Server lös Azure-filresurser direkt eller genom att cachelagra Azure-filresurser lokalt med hjälp av Azure File Sync. Vilket distributions alternativ du väljer ändrar de saker du behöver tänka på när du planerar för distributionen. 

- **Direkt montering av en Azure-fil resurs**: eftersom Azure Files ger SMB-åtkomst kan du montera Azure-filresurser lokalt eller i molnet med hjälp av standard-SMB-klienten som är tillgänglig i Windows, MacOS och Linux. Eftersom Azure-filresurser är utan server, behöver distributionen för produktions scenarier inte hantera en fil server eller NAS-enhet. Det innebär att du inte behöver tillämpa program varu korrigeringar eller byta ut fysiska diskar. 

- **Cachelagra Azure-filresurser lokalt med Azure File Sync**: Azure File Sync gör det möjligt att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten för en lokal fil server. Azure File Sync transformerar en lokal (eller moln) Windows Server till ett snabbt cacheminne för Azure-filresursen. 

Den här artikeln beskriver främst distributions överväganden för att distribuera en Azure-filresurs som ska monteras direkt av en lokal eller moln klient. För att planera för en Azure File Sync distribution, se [Planera för en Azure File Sync distribution](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Hanterings begrepp
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Vi rekommenderar att du distribuerar Azure-filresurser till lagrings konton:

- Distribuera bara Azure-filresurser i lagrings konton med andra Azure-filresurser. Även om GPv2 lagrings konton tillåter att du har blandade lagrings konton, eftersom lagrings resurser som Azure-filresurser och blob-behållare delar lagrings kontots gränser, kan det vara svårare att felsöka prestanda problem senare. 

- Betala till ett lagrings kontos IOPS-begränsningar när du distribuerar Azure-filresurser. Vi rekommenderar att du mappar fil resurser 1:1 med lagrings konton, men det kanske inte alltid är möjligt på grund av olika begränsningar och begränsningar, både från din organisation och från Azure. Om det inte går att ha en enda fil resurs som har distribuerats i ett lagrings konto bör du överväga vilka resurser som ska vara hög aktiva och vilka resurser som är mindre aktiva för att säkerställa att de hetaste fil resurserna inte placeras i samma lagrings konto tillsammans.

- Distribuera endast GPv2-och FileStorage-konton och uppgradera GPv1 och klassiska lagrings konton när du hittar dem i din miljö. 

## <a name="identity"></a>Identitet
För att få åtkomst till en Azure-filresurs måste fil resursens användare autentiseras och ha behörighet att komma åt resursen. Detta görs baserat på identiteten för den användare som har åtkomst till fil resursen. Azure Files integreras med tre huvudsakliga identitets leverantörer:
- **Lokala Active Directory Domain Services (AD DS eller lokala AD DS) (för** hands version): Azure Storage-konton kan vara domänanslutna till kundägda Active Directory Domain Services, precis som en Windows Server-fil server eller NAS-enhet. Du kan distribuera en domänkontrollant lokalt, i en virtuell Azure-dator eller till och med som en virtuell dator i en annan moln leverantör. Azure Files är oberoende till platsen där din domänkontrollant finns. När ett lagrings konto är domänanslutna kan slutanvändaren montera en fil resurs med det användar konto som de har loggat in på sina datorer med. AD-baserad autentisering använder Kerberos-autentiseringsprotokollet.
- **Azure Active Directory Domain Services (Azure AD DS)**: Azure AD DS tillhandahåller en Microsoft-hanterad domänkontrollant som kan användas för Azure-resurser. En domän som ansluter till ditt lagrings konto till Azure AD DS ger liknande förmåner för domän som du ansluter till till ett kundägda Active Directory. Det här distributions alternativet är mest användbart för scenarier med program ökning och-SKIFT som kräver AD-baserade behörigheter. Eftersom Azure AD DS tillhandahåller AD-baserad autentisering använder det här alternativet även Kerberos-autentiseringsprotokollet.
- **Azure Storage-konto nyckel**: Azure-filresurser kan också monteras med en nyckel för Azure Storage-konto. För att montera en fil resurs på det här sättet används lagrings kontots namn som användar namn och lagrings konto nyckel används som ett lösen ord. Att använda lagrings konto nyckeln för att montera Azure-filresursen är en administratörs åtgärd, eftersom den monterade fil resursen kommer att ha fullständig behörighet till alla filer och mappar på resursen, även om de har ACL: er. När du använder lagrings konto nyckeln för att montera via SMB används NTLMv2-autentiseringsprotokollet.

För kunder som migrerar från lokala fil servrar, eller om du skapar nya fil resurser i Azure Files som är avsedda att fungera som Windows-filservrar eller NAS-enheter, är det rekommenderade alternativet att ansluta till ditt lagrings konto till **kundägda Active Directory** . Mer information om domän anslutning till ditt lagrings konto till ett kundägda Active Directory finns i [Azure Files Active Directory översikt](storage-files-active-directory-overview.md).

Om du tänker använda lagrings konto nyckeln för att få åtkomst till dina Azure-filresurser rekommenderar vi att du använder tjänstens slut punkter enligt beskrivningen i avsnittet [nätverk](#networking) .

## <a name="networking"></a>Nätverk
Azure-filresurser är tillgängliga överallt via lagrings kontots offentliga slut punkt. Det innebär att autentiserade begär Anden, till exempel begär Anden som har godkänts av en användares inloggnings identitet, kan komma från eller utanför Azure. I många kund miljöer kommer en första montering av Azure-filresursen på din lokala arbets Station att Miss lyckas, även om monteringar från virtuella Azure-datorer lyckas. Orsaken till detta är att många organisationer och Internet leverantörer (ISP) blockerar porten som SMB använder för att kommunicera, Port 445. En översikt över Internetleverantörer som tillåter och inte tillåter åtkomst från port 445 finns på [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Om du vill avblockera åtkomst till Azure-filresursen har du två huvud alternativ:

- Avblockera port 445 för organisationens lokala nätverk. Azure-filresurser kan bara nås externt via den offentliga slut punkten med hjälp av säkra protokoll för Internet, till exempel SMB 3,0 och det fileraste API: et. Detta är det enklaste sättet att komma åt Azure-filresursen lokalt eftersom den inte kräver avancerad nätverks konfiguration än att ändra organisationens regler för utgående port, men vi rekommenderar dock att du tar bort äldre och föråldrade versioner av SMB-protokollet, nämligen SMB 1,0. Information om hur du gör detta finns i [skydda Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) och [säkra Linux](storage-how-to-use-files-linux.md#securing-linux).

- Få åtkomst till Azure-filresurser via en ExpressRoute eller VPN-anslutning. När du kommer åt Azure-filresursen via en nätverks tunnel kan du montera Azure-filresursen som en lokal fil resurs eftersom SMB-trafik inte korsar din organisations gränser.   

Även om det är mycket enklare att montera dina Azure-filresurser via den offentliga slut punkten i ett tekniskt perspektiv förväntar vi de flesta kunder att montera sina Azure-filresurser via en ExpressRoute eller VPN-anslutning. För att göra detta måste du konfigurera följande för din miljö:  

- **Nätverks tunnel med ExpressRoute, plats-till-plats eller punkt-till-plats-VPN**: tunnlar till ett virtuellt nätverk tillåter åtkomst till Azure-filresurser från lokalt, även om Port 445 är blockerad.
- **Privata slut punkter**: privata slut punkter ger ditt lagrings konto en dedikerad IP-adress i det virtuella nätverkets adress utrymme. Detta möjliggör nätverks tunnel utan att behöva öppna lokala nätverk upp till alla IP-adressintervall som ägs av Azure Storage-klustren. 
- **DNS-vidarebefordran**: Konfigurera din lokala DNS för att matcha namnet på ditt lagrings konto (dvs. `storageaccount.file.core.windows.net` för de offentliga moln regionerna) för att matcha IP-adressen för dina privata slut punkter.

Information om hur du planerar för nätverk som är kopplade till att distribuera en Azure-filresurs finns [Azure Files nätverks överväganden](storage-files-networking-overview.md).

## <a name="encryption"></a>Kryptering
Azure Files stöder två olika typer av kryptering: kryptering under överföring, som relaterar till den kryptering som används vid montering/åtkomst till Azure-filresursen och kryptering i vila, som relaterar till hur data krypteras när de lagras på disk. 

### <a name="encryption-in-transit"></a>Kryptering under överföring
Som standard har alla Azure Storage-konton kryptering under överföring aktiverat. Det innebär att när du monterar en fil resurs över SMB eller åtkomst till den via det fileraste protokollet (till exempel via Azure Portal, PowerShell/CLI eller Azure SDK: er), tillåter Azure Files bara anslutningen om den görs med SMB 3.0 + med kryptering eller HTTPS. Klienter som inte har stöd för SMB 3,0 eller klienter som stöder SMB 3,0 men inte SMB-kryptering kommer inte att kunna montera Azure-filresursen om kryptering i överföring är aktiverat. Mer information om vilka operativ system som stöder SMB 3,0 med kryptering finns i vår detaljerade dokumentation för [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)och [Linux](storage-how-to-use-files-linux.md). Alla aktuella versioner av PowerShell, CLI och SDK: er stöder HTTPS.  

Du kan inaktivera kryptering under överföring för ett Azure Storage-konto. När krypteringen är inaktive rad kommer Azure Files också tillåta SMB 2,1, SMB 3,0 utan kryptering och okrypterade filer för API-anrop via HTTP. Den främsta anledningen till att inaktivera kryptering vid överföring är att stödja ett äldre program som måste köras på ett äldre operativ system, till exempel Windows Server 2008 R2 eller äldre Linux-distribution. Azure Files tillåter endast SMB 2,1-anslutningar inom samma Azure-region som Azure-filresursen. en SMB 2,1-klient utanför Azure-filresursens region, till exempel lokalt eller i en annan Azure-region, kommer inte att kunna komma åt fil resursen.

Vi rekommenderar starkt att du ser till att kryptering av data överförs är aktiverat.

Mer information om kryptering i överföring finns i [krav på säker överföring i Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Vilande kryptering
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Dataskydd
Azure Files har en metod med flera lager för att säkerställa att dina data säkerhets kopie ras, kan återställas och skyddas från säkerhetshot.

### <a name="soft-delete"></a>Mjuk borttagning
Mjuk borttagning för fil resurser (förhands granskning) är en inställning för lagrings konto nivå som gör att du kan återställa fil resursen när den tas bort av misstag. När en fil resurs tas bort övergår den till ett mjukt borttaget tillstånd i stället för att raderas permanent. Du kan konfigurera hur lång tid det tar för mjuka borttagna data att återställas innan den tas bort permanent och ta bort resursen när den kvarhålls. 

Vi rekommenderar att du aktiverar mjuk borttagning för de flesta fil resurser. Om du har ett arbets flöde där delning av delning är vanligt och förväntat, kan du välja att ha en mycket kort kvarhållningsperiod eller inte har mjuk borttagning aktive rad.

Mer information om mjuk borttagning finns i [förhindra oavsiktlig data borttagning](https://docs.microsoft.com/azure/storage/files/storage-files-prevent-file-share-deletion).

### <a name="backup"></a>Backup
Du kan säkerhetskopiera Azure-filresursen via [resurs ögonblicks bilder](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files), som är skrivskyddade, tidpunkts kopior av din resurs. Ögonblicks bilder är stegvisa, vilket innebär att de bara innehåller lika mycket data som har ändrats sedan föregående ögonblicks bild. Du kan ha upp till 200 ögonblicks bilder per fil resurs och spara dem i upp till 10 år. Du kan antingen manuellt ta dessa ögonblicks bilder i Azure Portal, via PowerShell eller kommando rads gränssnitt (CLI), eller så kan du använda [Azure Backup](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json). Ögonblicks bilder lagras i fil resursen, vilket innebär att om du tar bort fil resursen, tas ögonblicks bilderna också bort. För att skydda säkerhets kopior av ögonblicks bilder från oavsiktlig borttagning, se till att mjuk borttagning är aktiverat för din resurs.

[Azure Backup för Azure-filresurser](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) hanterar schemaläggning och kvarhållning av ögonblicks bilder. Farfar-funktionerna (-fader-son) innebär att du kan ta dagliga, veckovis, månatliga och årliga ögonblicks bilder, var och en med sin egen lagrings period. Azure Backup dirigerar också aktivering av mjuk borttagning och tar ett borttagnings lås på ett lagrings konto så snart alla fil resurser i det har kon figurer ATS för säkerhets kopiering. Till sist tillhandahåller Azure Backup vissa viktiga övervaknings-och aviserings funktioner som gör det möjligt för kunder att ha en samlad vy över sina reserv delar.

Du kan utföra både återställning på objekt-och delnings nivå i Azure Portal med Azure Backup. Allt du behöver göra är att välja återställnings punkt (en viss ögonblicks bild), en viss fil eller katalog, om det är relevant, och sedan den plats (original eller alternativa) som du vill återställa till. Säkerhets kopierings tjänsten hanterar kopiering av ögonblicks bild data över och visar återställnings förloppet i portalen.

Mer information om säkerhets kopiering finns i [om Azure File Share-säkerhetskopiering](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json).

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Avancerat skydd för Azure Files (för hands version)
Avancerat skydd (ATP) för Azure Storage ger ytterligare ett lager med säkerhets information som ger aviseringar när avvikande aktivitet på ditt lagrings konto upptäcks, till exempel ovanliga försök att komma åt lagrings kontot. ATP kör även skadlig kod för hash-rykte och meddelar om känd skadlig kod. Du kan konfigurera ATP på en prenumeration eller lagrings konto nivå via Azure Security Center. 

Mer information finns i [Avancerat skydd mot Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection).

## <a name="storage-tiers"></a>Lagringsnivåer
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

I allmänhet är Azure Files funktioner och samverkan med andra tjänster identiska mellan Premium-filresurser och standard fil resurser (inklusive transaktions optimerings-, frekventa och häftiga fil resurser), men det finns några viktiga skillnader:
- **Faktureringsmodell**
    - Premium-filresurser faktureras med hjälp av en etablerad fakturerings modell, vilket innebär att du betalar för hur mycket lagrings utrymme du tillhandahåller i stället för hur mycket lagrings utrymme du verkligen ber om. 
    - Standard fil resurser faktureras med hjälp av en modell där du betalar per användning, vilket omfattar en bas kostnad för lagring för hur mycket lagrings utrymme du faktiskt använder och sedan ytterligare en transaktions kostnad baserat på hur du använder resursen. Med standard fil resurser kommer din faktura att öka om du använder (Läs/skriv/montera) Azure-filresursen mer.
- **Alternativ för redundans**
    - Premium-filresurser är bara tillgängliga för lokalt redundant (LRS) och zon redundant lagring (ZRS).
    - Standard fil resurser är tillgängliga för lokalt redundant, zon redundant, Geo-redundant (GRS) och GZRS-lagring (geo-Zone redundant).
- **Maximal storlek på fil resurs**
    - Premium-filresurser kan tillhandahållas för upp till 100 TiB utan ytterligare arbete.
    - Som standard kan standard fil resurser bara omfatta upp till 5 TiB, även om resurs gränsen kan ökas till 100 TiB av väljer till den *stora fil resursens* lagrings konto flagga. Standard fil resurser kan bara omfatta upp till 100 TiB för lokalt redundanta eller zon redundanta lagrings konton. Mer information om hur du ökar fil resurs storlekarna finns i [Aktivera och skapa stora fil resurser](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share).
- **Regional tillgänglighet**
    - Premium-filresurser är inte tillgängliga i varje region, och Zone-redundant support är tillgängligt i en mindre delmängd av regionerna. För att ta reda på om Premium-filresurser är tillgängliga i din region, se sidan [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) för Azure. För att ta reda på vilka regioner som stöder ZRS, se [Support för Azures tillgänglighets zon efter region](../../availability-zones/az-region.md). För att hjälpa oss att prioritera nya regioner och funktioner på Premium-nivå kan du fylla i den här [undersökningen](https://aka.ms/pfsfeedback).
    - Standard fil resurser är tillgängliga i alla Azure-regioner.
- Azure Kubernetes service (AKS) stöder Premium-filresurser i version 1,13 och senare.

När en fil resurs har skapats som antingen en Premium-eller standard fil resurs kan du inte automatiskt konvertera den till den andra nivån. Om du vill växla till den andra nivån måste du skapa en ny fil resurs på den nivån och manuellt kopiera data från den ursprungliga resursen till den nya resurs som du har skapat. Vi rekommenderar att `robocopy` du använder för Windows eller `rsync` MacOS och Linux för att utföra den kopian.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Förstå etablering för Premium-filresurser
Premium-filresurser tillhandahålls baserat på en fast GiB/IOPS/data flödes kvot. För varje GiB tilldelas resursen en IOPS och 0,1 MiB/s-genomflöde upp till de maximala gränserna per resurs. Den minsta tillåtna etableringen är 100 GiB med lägsta IOPS/data flöde.

På bästa möjliga sätt kan alla resurser överföra upp till tre IOPS per GiB lagrings utrymme i 60 minuter eller längre beroende på resursens storlek. Nya resurser börjar med full burst-kredit baserat på den tillhandahållna kapaciteten.

Resurser måste tillhandahållas i steg om 1 GiB. Minimi storleken är 100 GiB, nästa storlek är 101 GiB och så vidare.

> [!TIP]
> Bas linje IOPS = 1 * etablerad GiB. (Upp till högst 100 000 IOPS).
>
> Burst-gräns = 3 * bas linje IOPS. (Upp till högst 100 000 IOPS).
>
> utgående taxa = 60 MiB/s + 0,06 * etablerad GiB
>
> ingress-taxa = 40 MiB/s + 0,04 * etablerad GiB

En etablerad resurs storlek anges av resurs kvoten. Du kan öka resurs kvoten när som helst, men den kan bara minskas efter 24 timmar sedan den senaste ökningen. Efter att ha väntat 24 timmar utan en kvot ökning kan du minska delnings kvoten så många gånger du vill, tills du ökar den igen. Ändringar av IOPS/data flödes skalning börjar gälla inom några minuter efter att storleken ändrats.

Det går att minska storleken på den allokerade resursen under den använda GiB. Om du gör detta kommer du inte att förlora data, men du kommer fortfarande att faktureras för den storlek som används och ta emot prestanda (bas linje, data flöde och burst-IOPS) för den etablerade resursen, inte den storlek som används.

I följande tabell visas några exempel på dessa formler för de allokerade resurs storlekarna:

|Kapacitet (GiB) | Bas linje IOPS | Burst IOPS | Utgående (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Upp till 300     | 66   | 44   |
|500         | 500     | Upp till 1 500   | 90   | 60   |
|1 024       | 1 024   | Upp till 3 072   | 122   | 81   |
|5 120       | 5 120   | Upp till 15 360  | 368   | 245   |
|10 240      | 10 240  | Upp till 30 720  | 675 | 450   |
|33 792      | 33 792  | Upp till 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Upp till 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Upp till 100 000 | 6 204 | 4 136   |

> [!NOTE]
> Fil resursernas prestanda är beroende av dator nätverks begränsningar, tillgänglig nätverks bandbredd, i/o-storlekar, parallellitet, bland många andra faktorer. Till exempel, baserat på intern testning med 8 KiB i/o-storlek, kan en virtuell Windows-dator, *Standard F16s_v2*som är anslutna till Premium-filresurs via SMB uppnå 20 000 Read IOPS och 15 000 Skriv-IOPS. Med 512 MiB Läs-/skriv-i/o-storlekar kan samma virtuella dator uppnå 1,1 GiB/s utgående och 370 MiB/s ingress-genomflöde. För att uppnå maximal prestanda skalning sprider du belastningen över flera virtuella datorer. Se [fel söknings guiden](storage-troubleshooting-files-performance.md) för några vanliga prestanda problem och lösningar.

#### <a name="bursting"></a>Bursting "
Premium-filresurser kan överföra sina IOPS upp till en faktor på tre. Burst-överföring automatiseras och fungerar baserat på ett kredit system. Burst-överföring fungerar på bästa möjliga sätt och burst-gränsen är inte en garanti, fil resurser kan överföras *till* gränsen.

Krediterna ackumuleras i en burst-Bucket när trafiken för fil resursen är under bas linje IOPS. Till exempel har en 100 GiB-resurs 100 bas linje IOPS. Om den faktiska trafiken på resursen var 40 IOPS för ett angivet intervall på 1 sekund, krediteras 60 oanvända IOPS till en burst-Bucket. Dessa krediter kommer sedan att användas senare när åtgärder skulle överskrida bas linjens IOPs.

> [!TIP]
> Storlek på burst-Bucket = bas linje för IOPS * 2 * 3600.

När en resurs överskrider bas linjens IOPS och har krediter i en burst-Bucket, överförs den till burst. Resurser kan fortsätta att överföra så länge krediterna är kvar, men resurser som är mindre än 50 TiB hålls bara vid burst-gränsen i upp till en timme. Resurser som är större än 50 TiB får tekniskt ut en Tim gräns på upp till två timmar, men detta baseras på antalet överförda burst-krediter. Varje i/o utöver bas linje IOPS förbrukar en kredit och när alla krediter förbrukas kommer resursen att återgå till bas linje IOPS.

Dela krediter har tre tillstånd:

- Påförs när fil resursen använder mindre än bas linjens IOPS.
- Avböjande, när fil resursen är burst-överföring.
- Återstående konstant, när det inte finns några krediter eller bas linje IOPS som används.

Nya fil resurser börjar med det fullständiga antalet krediter i sin burst-Bucket. Burst-krediter kommer inte att periodiseras om resursens IOPS sjunker under bas linje IOPS på grund av begränsning av servern.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Aktivera standard fil resurser för att täcka upp till 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Begränsningar
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundans
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrering
I många fall kommer du inte att upprätta en ny net-ny fil resurs för din organisation, utan i stället migrera en befintlig fil resurs från en lokal fil server eller NAS-enhet till Azure Files. Det är viktigt att du väljer strategi och verktyg för rätt migrering för ditt scenario för att migreringen ska lyckas. 

I [översikts artikeln om migreringen](storage-files-migration-overview.md) beskrivs kortfattat grunderna och innehåller en tabell som leder dig till migrerings guider som ofta täcker ditt scenario.

## <a name="next-steps"></a>Nästa steg
* [Planera för en Azure File Sync distribution](storage-sync-files-planning.md)
* [Distribuera Azure Files](storage-files-deployment-guide.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
* [Titta närmare på översikts artikeln om migrering för att hitta migreringsguiden för ditt scenario](storage-files-migration-overview.md)
