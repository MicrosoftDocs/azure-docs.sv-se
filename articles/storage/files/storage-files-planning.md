---
title: Planera för distribution av Azure Files | Microsoft Docs
description: Att planera för en Azure Files distribution. Du kan antingen direkt montera en Azure-filresurs eller cachelagra Azure-filresurs lokalt med Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a96b44a280e0aea15a6d0843f02f4ed16f8fcf4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879855"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planera för distribution av Azure Files
[Azure Files](storage-files-introduction.md) kan distribueras på två huvudsakliga sätt: genom att montera Server lös Azure-filresurser direkt eller genom att cachelagra Azure-filresurser lokalt med hjälp av Azure File Sync. Vilket distributions alternativ du väljer ändrar de saker du behöver tänka på när du planerar för distributionen. 

- **Direkt montering av en Azure-fil resurs**: eftersom Azure Files tillhandahåller SMB-(Server Message Block) eller NFS-åtkomst (Network File System) kan du montera Azure-filresurser lokalt eller i molnet med hjälp av de standard-SMB-eller NFS-klienter som är tillgängliga i ditt operativ system. Eftersom Azure-filresurser är utan server, behöver distributionen för produktions scenarier inte hantera en fil server eller NAS-enhet. Det innebär att du inte behöver tillämpa program varu korrigeringar eller byta ut fysiska diskar. 

- **Cachelagra Azure-filresurser lokalt med Azure File Sync**: Azure File Sync gör det möjligt att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten för en lokal fil server. Azure File Sync transformerar en lokal (eller moln) Windows Server till ett snabbt cacheminne för din Azure SMB-filresurs. 

Den här artikeln beskriver främst distributions överväganden för att distribuera en Azure-filresurs som ska monteras direkt av en lokal eller moln klient. För att planera för en Azure File Sync distribution, se [Planera för en Azure File Sync distribution](storage-sync-files-planning.md).

## <a name="available-protocols"></a>Tillgängliga protokoll

Azure Files erbjuder två protokoll som kan användas när du monterar dina fil resurser, SMB och Network File System (NFS). Mer information om dessa protokoll finns i [Azure File Share-protokoll](storage-files-compare-protocols.md).

> [!IMPORTANT]
> Merparten av innehållet i den här artikeln gäller endast SMB-resurser. Allt som gäller för NFS-resurser kommer att uttryckligen ange att det är tillämpligt.

## <a name="management-concepts"></a>Hanterings begrepp
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Vi rekommenderar att du distribuerar Azure-filresurser till lagrings konton:

- Distribuera bara Azure-filresurser i lagrings konton med andra Azure-filresurser. Även om GPv2 lagrings konton tillåter att du har blandade lagrings konton, eftersom lagrings resurser som Azure-filresurser och blob-behållare delar lagrings kontots gränser, kan det vara svårare att felsöka prestanda problem senare. 

- Betala till ett lagrings kontos IOPS-begränsningar när du distribuerar Azure-filresurser. Vi rekommenderar att du mappar fil resurser 1:1 med lagrings konton, men det kanske inte alltid är möjligt på grund av olika begränsningar och begränsningar, både från din organisation och från Azure. Om det inte går att ha en enda fil resurs som har distribuerats i ett lagrings konto bör du överväga vilka resurser som ska vara hög aktiva och vilka resurser som är mindre aktiva för att säkerställa att de hetaste fil resurserna inte placeras i samma lagrings konto tillsammans.

- Distribuera endast GPv2-och FileStorage-konton och uppgradera GPv1 och klassiska lagrings konton när du hittar dem i din miljö. 

## <a name="identity"></a>Identitet
För att få åtkomst till en Azure-filresurs måste fil resursens användare autentiseras och ha behörighet att komma åt resursen. Detta görs baserat på identiteten för den användare som har åtkomst till fil resursen. Azure Files integreras med tre huvudsakliga identitets leverantörer:
- **Lokala Active Directory Domain Services (AD DS eller lokala AD DS)**: Azure Storage-konton kan vara domänanslutna till kundägda Active Directory Domain Services, precis som en Windows Server-fil server eller NAS-enhet. Du kan distribuera en domänkontrollant lokalt, i en virtuell Azure-dator eller till och med som en virtuell dator i en annan moln leverantör. Azure Files är oberoende till platsen där din domänkontrollant finns. När ett lagrings konto är domänanslutna kan slutanvändaren montera en fil resurs med det användar konto som de har loggat in på sina datorer med. AD-baserad autentisering använder Kerberos-autentiseringsprotokollet.
- **Azure Active Directory Domain Services (Azure AD DS)**: Azure AD DS tillhandahåller en Microsoft-hanterad domänkontrollant som kan användas för Azure-resurser. En domän som ansluter till ditt lagrings konto till Azure AD DS ger liknande förmåner för domän som du ansluter till till ett kundägda Active Directory. Det här distributions alternativet är mest användbart för scenarier med program ökning och-SKIFT som kräver AD-baserade behörigheter. Eftersom Azure AD DS tillhandahåller AD-baserad autentisering använder det här alternativet även Kerberos-autentiseringsprotokollet.
- **Azure Storage-konto nyckel**: Azure-filresurser kan också monteras med en nyckel för Azure Storage-konto. För att montera en fil resurs på det här sättet används lagrings kontots namn som användar namn och lagrings konto nyckel används som ett lösen ord. Att använda lagrings konto nyckeln för att montera Azure-filresursen är en administratörs åtgärd, eftersom den monterade fil resursen kommer att ha fullständig behörighet till alla filer och mappar på resursen, även om de har ACL: er. När du använder lagrings konto nyckeln för att montera via SMB används NTLMv2-autentiseringsprotokollet.

För kunder som migrerar från lokala fil servrar, eller om du skapar nya fil resurser i Azure Files som är avsedda att fungera som Windows-filservrar eller NAS-enheter, är det rekommenderade alternativet att ansluta till ditt lagrings konto till **kundägda Active Directory** . Mer information om domän anslutning till ditt lagrings konto till ett kundägda Active Directory finns i [Azure Files Active Directory översikt](storage-files-active-directory-overview.md).

Om du tänker använda lagrings konto nyckeln för att få åtkomst till dina Azure-filresurser rekommenderar vi att du använder tjänstens slut punkter enligt beskrivningen i avsnittet [nätverk](#networking) .

## <a name="networking"></a>Nätverk
Azure-filresurser är tillgängliga överallt via lagrings kontots offentliga slut punkt. Det innebär att autentiserade begär Anden, till exempel begär Anden som har godkänts av en användares inloggnings identitet, kan komma från eller utanför Azure. I många kund miljöer kommer en första montering av Azure-filresursen på din lokala arbets Station att Miss lyckas, även om monteringar från virtuella Azure-datorer lyckas. Orsaken till detta är att många organisationer och Internet leverantörer (ISP) blockerar porten som SMB använder för att kommunicera, Port 445. En översikt över Internetleverantörer som tillåter och inte tillåter åtkomst från port 445 finns på [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Om du vill avblockera åtkomst till Azure-filresursen har du två huvud alternativ:

- Avblockera port 445 för organisationens lokala nätverk. Azure-filresurser kan bara nås externt via den offentliga slut punkten med hjälp av säkra protokoll för Internet, till exempel SMB 3,0 och det fileraste API: et. Detta är det enklaste sättet att komma åt Azure-filresursen lokalt eftersom den inte kräver avancerad nätverks konfiguration än att ändra organisationens regler för utgående port, men vi rekommenderar dock att du tar bort äldre och föråldrade versioner av SMB-protokollet, nämligen SMB 1,0. Information om hur du gör detta finns i [skydda Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) och [säkra Linux](storage-how-to-use-files-linux.md#securing-linux).

- Få åtkomst till Azure-filresurser via en ExpressRoute eller VPN-anslutning. När du kommer åt Azure-filresursen via en nätverks tunnel kan du montera Azure-filresursen som en lokal fil resurs eftersom SMB-trafik inte korsar din organisations gränser.   

Även om det är mycket enklare att montera dina Azure-filresurser via den offentliga slut punkten i ett tekniskt perspektiv förväntar vi de flesta kunder att montera sina Azure-filresurser via en ExpressRoute eller VPN-anslutning. Montering med dessa alternativ är möjligt med både SMB-och NFS-resurser. För att göra detta måste du konfigurera följande för din miljö:  

- **Nätverks tunnel med ExpressRoute, plats-till-plats eller punkt-till-plats-VPN**: tunnlar till ett virtuellt nätverk tillåter åtkomst till Azure-filresurser från lokalt, även om Port 445 är blockerad.
- **Privata slut punkter**: privata slut punkter ger ditt lagrings konto en dedikerad IP-adress i det virtuella nätverkets adress utrymme. Detta möjliggör nätverks tunnel utan att behöva öppna lokala nätverk upp till alla IP-adressintervall som ägs av Azure Storage-klustren. 
- **DNS-vidarebefordran**: Konfigurera din lokala DNS för att matcha namnet på ditt lagrings konto (dvs. `storageaccount.file.core.windows.net` för de offentliga moln regionerna) för att matcha IP-adressen för dina privata slut punkter.

Information om hur du planerar för nätverk som är kopplade till att distribuera en Azure-filresurs finns [Azure Files nätverks överväganden](storage-files-networking-overview.md).

## <a name="encryption"></a>Kryptering
Azure Files stöder två olika typer av kryptering: kryptering under överföring, som relaterar till den kryptering som används vid montering/åtkomst till Azure-filresursen och kryptering i vila, som relaterar till hur data krypteras när de lagras på disk. 

### <a name="encryption-in-transit"></a>Kryptering under överföring

> [!IMPORTANT]
> Det här avsnittet beskriver kryptering i överförings information för SMB-resurser. Mer information om kryptering i överföring med NFS-resurser finns i [säkerhet](storage-files-compare-protocols.md#security).

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

Mer information om mjuk borttagning finns i [förhindra oavsiktlig data borttagning](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
Du kan säkerhetskopiera Azure-filresursen via [resurs ögonblicks bilder](./storage-snapshots-files.md), som är skrivskyddade, tidpunkts kopior av din resurs. Ögonblicks bilder är stegvisa, vilket innebär att de bara innehåller lika mycket data som har ändrats sedan föregående ögonblicks bild. Du kan ha upp till 200 ögonblicks bilder per fil resurs och spara dem i upp till 10 år. Du kan antingen manuellt ta dessa ögonblicks bilder i Azure Portal, via PowerShell eller kommando rads gränssnitt (CLI), eller så kan du använda [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ögonblicks bilder lagras i fil resursen, vilket innebär att om du tar bort fil resursen, tas ögonblicks bilderna också bort. För att skydda säkerhets kopior av ögonblicks bilder från oavsiktlig borttagning, se till att mjuk borttagning är aktiverat för din resurs.

[Azure Backup för Azure-filresurser](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) hanterar schemaläggning och kvarhållning av ögonblicks bilder. Farfar-funktionerna (-fader-son) innebär att du kan ta dagliga, veckovis, månatliga och årliga ögonblicks bilder, var och en med sin egen lagrings period. Azure Backup dirigerar också aktivering av mjuk borttagning och tar ett borttagnings lås på ett lagrings konto så snart alla fil resurser i det har kon figurer ATS för säkerhets kopiering. Till sist tillhandahåller Azure Backup vissa viktiga övervaknings-och aviserings funktioner som gör det möjligt för kunder att ha en samlad vy över sina reserv delar.

Du kan utföra både återställning på objekt-och delnings nivå i Azure Portal med Azure Backup. Allt du behöver göra är att välja återställnings punkt (en viss ögonblicks bild), en viss fil eller katalog, om det är relevant, och sedan den plats (original eller alternativa) som du vill återställa till. Säkerhets kopierings tjänsten hanterar kopiering av ögonblicks bild data över och visar återställnings förloppet i portalen.

Mer information om säkerhets kopiering finns i [om Azure File Share-säkerhetskopiering](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Avancerat skydd för Azure Files (för hands version)
Avancerat skydd (ATP) för Azure Storage ger ytterligare ett lager med säkerhets information som ger aviseringar när avvikande aktivitet på ditt lagrings konto upptäcks, till exempel ovanliga försök att komma åt lagrings kontot. ATP kör även skadlig kod för hash-rykte och meddelar om känd skadlig kod. Du kan konfigurera ATP på en prenumeration eller lagrings konto nivå via Azure Security Center. 

Mer information finns i [Avancerat skydd mot Azure Storage](../common/azure-defender-storage-configure.md).

## <a name="storage-tiers"></a>Lagringsnivåer
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

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
* [Distribuera Azure Files](./storage-how-to-create-file-share.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
* [Titta närmare på översikts artikeln om migrering för att hitta migreringsguiden för ditt scenario](storage-files-migration-overview.md)