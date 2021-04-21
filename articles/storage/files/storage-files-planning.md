---
title: Planera för en Azure Files distribution | Microsoft Docs
description: Förstå planering för en Azure Files distribution. Du kan antingen direktmontera en Azure-filresurs eller cachelagra En Azure-filresurs lokalt med Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: be7e5b1f9721cc65c2f9b371becf8b4c82fb37b4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759779"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planera för distribution av Azure Files
[Azure Files](storage-files-introduction.md) kan distribueras på två huvudsakliga sätt: genom att direkt montera de serverlösa Azure-filresurser eller genom att cachelagra Azure-filresurser lokalt med hjälp av Azure File Sync. Vilket distributionsalternativ du väljer ändrar vad du behöver tänka på när du planerar för distributionen. 

- **Direkt montering** av en Azure-filresurs: Eftersom Azure Files antingen ger åtkomst till Server Message Block (SMB) eller NFS (Network File System) kan du montera Azure-filresurser lokalt eller i molnet med hjälp av standardklienterna för SMB eller NFS som är tillgängliga i ditt operativsystem. Eftersom Azure-filresurser är serverlösa kräver distribution för produktionsscenarier inte hantering av en filserver eller NAS-enhet. Det innebär att du inte behöver tillämpa programkorrigeringar eller byta ut fysiska diskar. 

- **Cachelagra En Azure-filresurs** lokalt med Azure File Sync: med Azure File Sync kan du centralisera organisationens filresurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver. Azure File Sync omvandlar en lokal (eller molnbaserad) Windows Server till ett snabbt cacheminne för din Azure SMB-filresurs. 

Den här artikeln tar främst upp distributionsöverväganden för distribution av en Azure-filresurs som ska monteras direkt av en lokal klient eller molnklient. Information om hur du planerar Azure File Sync distribution finns i [Planera för en Azure File Sync distribution.](../file-sync/file-sync-planning.md)

## <a name="available-protocols"></a>Tillgängliga protokoll

Azure Files två protokoll som kan användas när du monterar dina filresurser, SMB och Network File System (NFS). Mer information om dessa protokoll finns i [Azure-filresursprotokoll.](storage-files-compare-protocols.md)

> [!IMPORTANT]
> Merparten av innehållet i den här artikeln gäller endast för SMB-resurser. Allt som gäller för NFS-resurser kommer specifikt att ange att det är tillämpligt.

## <a name="management-concepts"></a>Hanteringsbegrepp
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

När du distribuerar Azure-filresurser till lagringskonton rekommenderar vi:

- Distribuera endast Azure-filresurser till lagringskonton med andra Azure-filresurser. Även om GPv2-konton gör att du kan ha blandade lagringskonton, eftersom lagringsresurser som Azure-filresurser och blobcontainrar delar lagringskontots gränser, kan det vara svårare att felsöka prestandaproblem senare om du blandar resurser tillsammans. 

- Var uppmärksam på IOPS-begränsningarna för ett lagringskonto när du distribuerar Azure-filresurser. Vi rekommenderar att du mappar filresurser 1:1 med lagringskonton, men det kanske inte alltid är möjligt på grund av olika begränsningar, både från din organisation och från Azure. När det inte är möjligt att bara ha en filresurs distribuerad i ett lagringskonto bör du överväga vilka resurser som är mycket aktiva och vilka resurser som är mindre aktiva för att säkerställa att de hetsade filresurser inte placeras i samma lagringskonto tillsammans.

- Distribuera endast GPv2- och FileStorage-konton och uppgradera GPv1- och klassiska lagringskonton när du hittar dem i din miljö. 

## <a name="identity"></a>Identitet
För att få åtkomst till en Azure-filresurs måste användaren av filresursen autentiseras och ha behörighet att komma åt resursen. Detta görs baserat på identiteten för den användare som har åtkomst till filresursen. Azure Files integreras med tre huvudsakliga identitetsproviders:
- **Lokal Active Directory Domain Services (AD DS eller lokal AD DS):** Azure-lagringskonton kan vara domänägda till en kundägd Active Directory Domain Services, precis som en Windows Server-filserver eller NAS-enhet. Du kan distribuera en domänkontrollant lokalt, på en virtuell Azure-dator eller till och med som en virtuell dator i en annan molnleverantör. Azure Files är agnostisk för den plats där domänkontrollanten finns. När ett lagringskonto är domän-ansluten kan slutanvändaren montera en filresurs med det användarkonto som användaren är inloggad på datorn med. AD-baserad autentisering använder Kerberos-autentiseringsprotokollet.
- **Azure Active Directory Domain Services (Azure AD DS)**: Azure AD DS tillhandahåller en Microsoft-hanterad domänkontrollant som kan användas för Azure-resurser. Domän som ansluter ditt lagringskonto till Azure AD DS ger liknande fördelar som att ansluta det till en kundägd Active Directory. Det här distributionsalternativet är mest användbart för lift and shift-scenarier för program som kräver AD-baserade behörigheter. Eftersom Azure AD DS ad-baserad autentisering används även Kerberos-autentiseringsprotokollet i det här alternativet.
- **Azure Storage-kontonyckel:** Azure-filresurser kan också monteras med en Azure Storage-kontonyckel. För att montera en filresurs på det här sättet används lagringskontonamnet som användarnamn och lagringskontonyckeln används som lösenord. Att använda lagringskontonyckeln för att montera Azure-filresursen är i praktiken en administratörsåtgärd, eftersom den monterade filresursen har fullständig behörighet till alla filer och mappar på resursen, även om de har åtkomstkontrollistor. När du använder lagringskontonyckeln för att montera över SMB används NTLMv2-autentiseringsprotokollet.

För kunder som migrerar från lokala filservrar eller skapar nya filresurser i Azure Files som är avsedda att bete sig som **Windows-filservrar** eller NAS-enheter är domän anslutning av ditt lagringskonto till kundägd Active Directory det rekommenderade alternativet. Mer information om hur du ansluter ditt lagringskonto till en kundägd Active Directory finns i [Azure Files Översikt över Active Directory.](storage-files-active-directory-overview.md)

Om du tänker använda lagringskontonyckeln för att få åtkomst till dina Azure-filresurser rekommenderar vi att du använder tjänstslutpunkter enligt beskrivningen i [avsnittet](#networking) Nätverk.

## <a name="networking"></a>Nätverk
Azure-filresurser är tillgängliga var som helst via lagringskontots offentliga slutpunkt. Det innebär att autentiserade begäranden, till exempel begäranden som auktoriserats av en användares inloggningsidentitet, kan komma inifrån eller utanför Azure på ett säkert sätt. I många kundmiljöer kommer en inledande montering av Azure-filresursen på din lokala arbetsstation att misslyckas, även om monteringar från virtuella Azure-datorer lyckas. Anledningen är att många organisationer och Internetleverantörer blockerar den port som SMB använder för att kommunicera, port 445. En översikt över Internetleverantörer som tillåter och inte tillåter åtkomst från port 445 finns på [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Du har två huvudalternativ för att avblockera åtkomsten till din Azure-filresurs:

- Avblockera port 445 för organisationens lokala nätverk. Azure-filresurser kan endast nås externt via den offentliga slutpunkten med hjälp av internetsäkra protokoll som SMB 3.0 och FileREST API. Det här är det enklaste sättet att komma åt din Azure-filresurs lokalt eftersom det inte krävs avancerad nätverkskonfiguration utöver att ändra organisationens regler för utgående portar. Vi rekommenderar dock att du tar bort äldre och föråldrade versioner av SMB-protokollet, nämligen SMB 1.0. Information om hur du gör detta finns i [Skydda Windows/Windows Server och](storage-how-to-use-files-windows.md#securing-windowswindows-server) Skydda [Linux.](storage-how-to-use-files-linux.md#securing-linux)

- Få åtkomst till Azure-filresurser via en ExpressRoute- eller VPN-anslutning. När du kommer åt din Azure-filresurs via en nätverkstunnel kan du montera Azure-filresursen som en lokal filresurs eftersom SMB-trafiken inte passerar organisationens gränser.   

Även om det ur ett tekniskt perspektiv är betydligt enklare att montera dina Azure-filresurser via den offentliga slutpunkten förväntar vi oss att de flesta kunder väljer att montera sina Azure-filresurser via en ExpressRoute- eller VPN-anslutning. Montering med dessa alternativ är möjligt med både SMB- och NFS-resurser. Om du vill göra detta måste du konfigurera följande för din miljö:  

- **Nätverkstunnlar med ExpressRoute, plats-till-plats eller punkt-till-plats-VPN:** Tunneltrafik till ett virtuellt nätverk tillåter åtkomst till Azure-filresurser från en lokal plats, även om port 445 blockeras.
- **Privata slutpunkter:** Privata slutpunkter ger ditt lagringskonto en dedikerad IP-adress inifrån det virtuella nätverkets adressutrymme. Detta möjliggör nätverkstunnlar utan att behöva öppna lokala nätverk upp till alla IP-adressintervall som ägs av Azure Storage-klustren. 
- **DNS-vidarebefordran:** Konfigurera din lokala DNS för att matcha namnet på ditt lagringskonto ( för de offentliga molnregionerna) för att matcha ip-adressen för dina `storageaccount.file.core.windows.net` privata slutpunkter.

Information om hur du planerar för nätverk som är associerade med att distribuera en Azure-filresurs [finns i Azure Files nätverksöverväganden](storage-files-networking-overview.md).

## <a name="encryption"></a>Kryptering
Azure Files stöder två olika typer av kryptering: kryptering under överföring, som relaterar till den kryptering som används vid montering/åtkomst till Azure-filresursen och kryptering i vila, som relaterar till hur data krypteras när de lagras på disk. 

### <a name="encryption-in-transit"></a>Kryptering under överföring

> [!IMPORTANT]
> Det här avsnittet beskriver kryptering under överföringsinformation för SMB-resurser. Mer information om kryptering under överföring med NFS-resurser finns i [Säkerhet](storage-files-compare-protocols.md#security).

Som standard har alla Azure Storage-konton kryptering under överföring aktiverat. Det innebär att när du monterar en filresurs via SMB eller kommer åt den via FileREST-protokollet (till exempel via Azure Portal, PowerShell/CLI eller Azure-SDK:er) tillåter Azure Files endast anslutningen om den görs med SMB 3.0+ med kryptering eller HTTPS. Klienter som inte stöder SMB 3.0 eller klienter som stöder SMB 3.0 men inte SMB-kryptering kan inte montera Azure-filresursen om kryptering under överföring är aktiverat. Mer information om vilka operativsystem som stöder SMB 3.0 med kryptering finns i vår detaljerade dokumentation för [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)och [Linux.](storage-how-to-use-files-linux.md) Alla aktuella versioner av PowerShell, CLI och SDK:er stöder HTTPS.  

Du kan inaktivera kryptering under överföring för ett Azure Storage-konto. När kryptering har inaktiverats Azure Files även SMB 2.1, SMB 3.0 utan kryptering och okrypterade FileREST API-anrop via HTTP. Det främsta skälet till att inaktivera kryptering under överföring är att stödja ett äldre program som måste köras på ett äldre operativsystem, till exempel Windows Server 2008 R2 eller äldre Linux-distribution. Azure Files endast tillåter SMB 2.1-anslutningar inom samma Azure-region som Azure-filresursen. en SMB 2.1-klient utanför Azure-regionen för Azure-filresursen, till exempel lokalt eller i en annan Azure-region, kan inte komma åt filresursen.

Vi rekommenderar starkt att kryptering av data under överföring är aktiverat.

Mer information om kryptering under överföring finns i [kräva säker överföring i Azure Storage.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="encryption-at-rest"></a>Vilande kryptering
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Dataskydd
Azure Files har en metod med flera lager för att se till att dina data säkerhetskopieras, kan återställas och skyddas mot säkerhetshot.

### <a name="soft-delete"></a>Mjuk borttagning
Mjuk borttagning för filresurser (förhandsversion) är en inställning på lagringskontonivå som gör att du kan återställa filresursen när den tas bort av misstag. När en filresurs tas bort övergår den till ett mjukt borttagna tillstånd i stället för att raderas permanent. Du kan konfigurera hur lång tid mjukt borttagna data ska kunna återställas innan de tas bort permanent och sedan ta bort resursen när som helst under kvarhållningsperioden. 

Vi rekommenderar att du slår på mjuk borttagning för de flesta filresurser. Om du har ett arbetsflöde där borttagning av resurs är vanligt och förväntat kan du välja att ha en kort kvarhållningsperiod eller inte ha mjuk borttagning aktiverat alls.

Mer information om mjuk borttagning finns i Förhindra [oavsiktlig databorttagning.](./storage-files-prevent-file-share-deletion.md)

### <a name="backup"></a>Backup
Du kan säkerhetskopiera [](./storage-snapshots-files.md)Din Azure-filresurs via resursögonblicksbilder , som är skrivskyddade kopior av resursen vid en tidpunkt. Ögonblicksbilder är inkrementella, vilket innebär att de bara innehåller så mycket data som har ändrats sedan den föregående ögonblicksbilden. Du kan ha upp till 200 ögonblicksbilder per filresurs och behålla dem i upp till 10 år. Du kan antingen manuellt ta dessa ögonblicksbilder Azure Portal, via PowerShell eller kommandoradsgränssnittet (CLI), eller så kan du använda [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ögonblicksbilder lagras i filresursen, vilket innebär att dina ögonblicksbilder också tas bort om du tar bort filresursen. Om du vill skydda säkerhetskopior av ögonblicksbilder från oavsiktlig borttagning ser du till att mjuk borttagning är aktiverat för resursen.

[Azure Backup för Azure-filresurser](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) hanterar schemaläggning och kvarhållning av ögonblicksbilder. Dess gfs-son-funktioner innebär att du kan ta ögonblicksbilder varje dag, vecka, månad och år, var och en med sin egen distinkta kvarhållningsperiod. Azure Backup också aktivera mjuk borttagning och tar ett borttagningslås på ett lagringskonto så snart en filresurs i det har konfigurerats för säkerhetskopiering. Slutligen Azure Backup vissa funktioner för nyckelövervakning och avisering som gör att kunderna kan ha en samlad vy över sin säkerhetskopieringse egendom.

Du kan utföra återställningar på både objektnivå och resursnivå i Azure Portal med Azure Backup. Allt du behöver göra är att välja återställningspunkten (en viss ögonblicksbild), den specifika filen eller katalogen om det är relevant, och sedan den plats (ursprunglig eller alternativ) som du vill återställa till. Säkerhetskopieringstjänsten hanterar kopiering av ögonblicksbilddata över och visar återställningsförloppet i portalen.

Mer information om säkerhetskopiering finns i [Om säkerhetskopiering av Azure-filresurs.](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-defender-for-azure-files"></a>Azure Defender för Azure Files 
Azure Defender för Azure Storage (tidigare Advanced Threat Protection för Azure Storage) ger ytterligare ett lager med säkerhetsinformation som ger aviseringar när den identifierar avvikande aktivitet på ditt lagringskonto, till exempel ovanliga åtkomstförsök. Den kör också analys av hash-rykte för skadlig kod och varnar om känd skadlig kod. Du kan konfigurera Azure Defender på prenumerations- eller lagringskontonivå via Azure Security Center. 

Mer information finns i [Introduktion till Azure Defender för Storage](../../security-center/defender-for-storage-introduction.md).

## <a name="storage-tiers"></a>Lagringsnivåer
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Aktivera standardfilresurser för att sträcka sig över upp till 100 TiB
Som standard kan standardfilresurser bara sträcka sig över upp till 5 TiB, men du kan öka resursgränsen till 100 TiB. Information om hur du ökar resursgränsen finns i [Aktivera och skapa stora filresurser.](storage-files-how-to-create-large-file-share.md)


#### <a name="limitations"></a>Begränsningar
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundans
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrering
I många fall kommer du inte att upprätta en ny nettofilresurs för din organisation, utan i stället migrera en befintlig filresurs från en lokal filserver eller NAS-enhet till Azure Files. Att välja rätt migreringsstrategi och verktyg för ditt scenario är viktigt för att migreringen ska lyckas. 

Artikeln [om migreringsöversikt](storage-files-migration-overview.md) beskriver kortfattat grunderna och innehåller en tabell som leder dig till migreringsguider som troligen täcker ditt scenario.

## <a name="next-steps"></a>Nästa steg
* [Planera för en Azure File Sync distribution](../file-sync/file-sync-planning.md)
* [Distribuera Azure Files](./storage-how-to-create-file-share.md)
* [Distribuera Azure File Sync](../file-sync/file-sync-deployment-guide.md)
* [Läs artikeln om migreringsöversikten för att hitta migreringsguiden för ditt scenario](storage-files-migration-overview.md)