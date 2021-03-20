---
title: Översikt över Azure-kryptering | Microsoft Docs
description: Lär dig mer om krypterings alternativ i Azure. Se information om kryptering i vila, kryptering i flygning och nyckel hantering med Azure Key Vault.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879722"
---
# <a name="azure-encryption-overview"></a>Översikt över Azure-kryptering

Den här artikeln innehåller en översikt över hur kryptering används i Microsoft Azure. Den täcker huvud områdena kryptering, inklusive kryptering i vila, kryptering i flygning och nyckel hantering med Azure Key Vault. I varje avsnitt finns länkar till mer detaljerad information.

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila

Data i vila innehåller information som finns i beständig lagring på fysiska media, i valfritt digitalt format. Mediet kan innehålla filer på magnetiska eller optiska medier, arkiverade data och säkerhets kopior av data. Microsoft Azure erbjuder en mängd data lagrings lösningar som uppfyller olika behov, inklusive fil-, disk-, blob-och tabell lagring. Microsoft tillhandahåller även kryptering för att skydda [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md)och Azure Data Lake.

Data kryptering i vila är tillgängligt för tjänster i SaaS (program vara som en tjänst), PaaS (Platform as a Service) och IaaS-moln (Infrastructure as a Service). Den här artikeln sammanfattar och tillhandahåller resurser som hjälper dig att använda Azures krypterings alternativ.

En mer detaljerad beskrivning av hur data i vila krypteras i Azure finns i [Azure Data Encryption-at-rest](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Azure Encryption-modeller

Azure har stöd för olika krypterings modeller, inklusive kryptering på Server sidan som använder tjänst hanterade nycklar, Kundhanterade nycklar i Key Vault eller Kundhanterade nycklar på kundkontrollerad maskin vara. Med kryptering på klientsidan kan du hantera och lagra nycklar lokalt eller på en annan säker plats.

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Kryptering på klient sidan utförs utanför Azure. Den innehåller:

- Data som krypterats av ett program som körs i kundens Data Center eller av ett tjänst program.
- Data som redan är krypterad när de tas emot av Azure.

Med kryptering på klient sidan har moln tjänst leverantörer inte åtkomst till krypterings nycklarna och kan inte dekryptera dessa data. Du behåller fullständig kontroll över nycklarna.

### <a name="server-side-encryption"></a>Kryptering på serversidan

De tre krypterings modellerna på Server sidan ger olika egenskaper för nyckel hantering, som du kan välja enligt dina krav:

- **Tjänst nycklar som hanteras**: ger en kombination av kontroll och bekvämlighet med låg belastning.

- **Kundhanterade nycklar**: ger dig kontroll över nycklar, inklusive att ta med egna nycklar (BYOK) support, eller så kan du skapa nya.

- **Hanterade nycklar i kundkontrollerad maskin vara**: gör att du kan hantera nycklar i din egen lagrings plats, utanför Microsoft-kontroll. Den här egenskapen kallas värd för din egen nyckel (HYOK). Konfigurationen är dock komplicerad och de flesta Azure-tjänster har inte stöd för den här modellen.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Du kan skydda virtuella Windows-och Linux-datorer med hjälp av [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md), som använder [Windows BitLocker](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) -teknik och Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) för att skydda både operativ system diskar och data diskar med fullständig volym kryptering.

Krypterings nycklar och hemligheter skyddas i [Azure Key Vault prenumerationen](../../key-vault/general/overview.md). Genom att använda Azure Backup tjänsten kan du säkerhetskopiera och återställa krypterade virtuella datorer som använder KEK-konfiguration (Key Encryption Key).

### <a name="azure-storage-service-encryption"></a>Azure Storage tjänst kryptering

Data i vila i Azure Blob Storage och Azure-filresurser kan krypteras både i scenarier på Server sidan och på klient sidan.

[Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) kan automatiskt kryptera data innan de lagras och dekrypterar data automatiskt när du hämtar dem. Processen är helt transparent för användare. Kryptering för lagringstjänst använder [AES-kryptering (](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)256-bitars Advanced Encryption Standard), vilket är en av de starkaste block chiffer som är tillgängliga. AES hanterar kryptering, dekryptering och nyckel hantering transparent.

### <a name="client-side-encryption-of-azure-blobs"></a>Kryptering på klient sidan av Azure-blobbar

Du kan utföra kryptering på klient sidan av Azure-blobbar på olika sätt.

Du kan använda Azure Storage klient bibliotek för .NET NuGet-paket för att kryptera data i dina klient program innan du överför dem till Azure Storage.

Mer information om och hur du hämtar Azure Storage klient bibliotek för .NET NuGet-paketet finns i [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

När du använder kryptering på klient sidan med Key Vault krypteras dina data med en engångs symmetrisk innehålls krypterings nyckel (CEK) som genereras av Azure Storage-klientens SDK. CEK krypteras med en nyckel krypterings nyckel (KEK), som kan vara antingen en symmetrisk nyckel eller ett asymmetriskt nyckel par. Du kan hantera den lokalt eller lagra den i Key Vault. Krypterade data överförs sedan till Azure Storage.

Om du vill veta mer om kryptering på klient sidan med Key Vault och kom igång med instruktioner, se [Självstudier: kryptera och dekryptera blobbar i Azure Storage med Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Slutligen kan du också använda Azure Storage klient bibliotek för Java för att utföra kryptering på klient sidan innan du överför data till Azure Storage och för att dekryptera data när du laddar ned den till klienten. Det här biblioteket stöder också integrering med [Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av lagrings konto nycklar.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Kryptering av data i vila med Azure SQL Database

[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) är en allmän Relations databas tjänst i Azure som stöder strukturer som Relations data, JSON, spatial och XML. SQL Database stöder både kryptering på Server sidan via funktionen transparent datakryptering (TDE) och kryptering på klient sidan via funktionen Always Encrypted.

#### <a name="transparent-data-encryption"></a>Transparent datakryptering

[TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) används för att kryptera [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) -datafiler i real tid med hjälp av en databas krypterings nyckel (DEK) som lagras i databasens start post för tillgänglighet under återställningen.

TDE skyddar data och loggfiler med hjälp av AES-krypteringsalgoritmer (3DES och Triple Data Encryption Standard). Kryptering av databas filen utförs på sidnivå. Sidorna i en krypterad databas krypteras innan de skrivs till disk och dekrypteras när de läses in i minnet. TDE är nu aktiverat som standard på nyligen skapade Azure SQL-databaser.

#### <a name="always-encrypted-feature"></a>Always Encrypted funktion

Med [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) funktionen i Azure SQL kan du kryptera data i klient program innan du lagrar dem i Azure SQL Database. Du kan också aktivera delegering av lokal databas administration till tredje part och upprätthålla separering mellan de som äger och kan visa data och de som hanterar den, men inte ha åtkomst till den.

#### <a name="cell-level-or-column-level-encryption"></a>Kryptering på cell nivå eller kolumn nivå

Med Azure SQL Database kan du tillämpa symmetrisk kryptering på en kolumn med data med hjälp av Transact-SQL. Den här metoden kallas kryptering på [cell nivå eller kryptering på kolumn nivå (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), eftersom du kan använda den för att kryptera vissa kolumner eller till och med vissa data celler med olika krypterings nycklar. Detta ger dig mer detaljerad krypterings kapacitet än TDE, som krypterar data på sidor.

CLE har inbyggda funktioner som du kan använda för att kryptera data med hjälp av antingen symmetriska eller asymmetriska nycklar, den offentliga nyckeln för ett certifikat eller en lösen fras med 3DES.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB databas kryptering

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) är Microsofts globalt distribuerade databas för flera modeller. Användar data som lagras i Cosmos DB i icke-flyktig lagring (solid-state-enheter) krypteras som standard. Det finns inga kontroller att aktivera eller inaktivera. Kryptering i vila implementeras med hjälp av ett antal säkerhets tekniker, inklusive säkra nyckel lagrings system, krypterade nätverk och kryptografiska API: er. Krypterings nycklar hanteras av Microsoft och roteras enligt Microsofts interna rikt linjer.

### <a name="at-rest-encryption-in-data-lake"></a>Vilande kryptering i Data Lake

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) är en företagsomfattande lagrings plats för alla typer av data som samlas in på en och samma plats innan en formell definition av krav eller schema. Data Lake Store stöder "på som standard" transparent kryptering av data i vila, vilket ställs in när ditt konto skapas. Azure Data Lake Store hanterar nycklar som standard, men du kan välja att hantera dem själv.

Tre typer av nycklar används för att kryptera och dekryptera data: huvud krypterings nyckeln (huvud krypterings nyckeln), data krypterings nyckel (DEK) och block krypterings nyckel (BEK). HUVUD krypterings nyckeln används för att kryptera DEK, som lagras på permanenta medier och BEK härleds från DEK och data blocket. Om du hanterar dina egna nycklar kan du rotera huvud krypterings nyckeln.

## <a name="encryption-of-data-in-transit"></a>Kryptering av data under överföring

Azure erbjuder många mekanismer för att hålla data privata när de flyttas från en plats till en annan.

### <a name="data-link-layer-encryption-in-azure"></a>Kryptering av data länk skikt i Azure

När Azure-kundtrafiken flyttas mellan data Center – utanför fysiska gränser som inte styrs av Microsoft (eller på uppdrag av Microsoft) – en krypterings metod för data länk nivå som använder [IEEE 802.1 AE Mac Security Standards](https://1.ieee802.org/security/802-1ae/) (kallas även MACsec) tillämpas från punkt-till-plats över den underliggande nätverks maskin varan. Paketen krypteras och dekrypteras på enheterna innan de skickas, vilket förhindrar fysiska "man-in-the-Middle"-eller snooping/wiretapping-attacker. Eftersom den här tekniken är integrerad i själva nätverks maskin varan tillhandahåller den en kryptering på nätverks maskin varan utan en mätbar fördröjning för länk fördröjning. Den här MACsec-krypteringen är aktive ras som standard för all Azure-trafik inom en region eller mellan regioner, och ingen åtgärd krävs för kundernas del att aktivera. 

### <a name="tls-encryption-in-azure"></a>TLS-kryptering i Azure

Microsoft ger kunderna möjlighet att använda [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) -protokollet (TLS) för att skydda data när de reser mellan moln tjänster och kunder. Microsoft-datacentren förhandlar en TLS-anslutning med klient system som ansluter till Azure-tjänster. TLS ger stark autentisering, meddelande sekretess och integritet (aktiverar identifiering av meddelande manipulation, avlyssning och förfalskning), samverkan, algoritm flexibilitet och enkel distribution och användning.

Perfect [Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) skyddar anslutningar mellan kunders klient system och Microsoft Cloud Services genom unika nycklar. Anslutningar använder även RSA-baserade 2 048-bitars krypterings nyckel längd. Den här kombinationen gör det svårt för någon att avlyssna och komma åt data som är under överföring.

### <a name="azure-storage-transactions"></a>Azure Storage transaktioner

När du interagerar med Azure Storage via Azure Portal sker alla transaktioner över HTTPS. Du kan också använda lagrings REST API över HTTPS för att interagera med Azure Storage. Du kan använda HTTPS när du anropar REST-API: er för att komma åt objekt i lagrings konton genom att aktivera den säkra överföring som krävs för lagrings kontot.

Signaturer för delad åtkomst ([SAS](../../storage/common/storage-sas-overview.md)), som kan användas för att delegera åtkomst till Azure Storage objekt, innehåller ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst. Den här metoden säkerställer att vem som skickar länkar med SAS-token använder rätt protokoll.

[SMB 3,0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption), som används för att få åtkomst till Azure Files resurser, stöder kryptering och är tillgängligt i windows Server 2012 R2, Windows 8, Windows 8,1 och Windows 10. Det ger åtkomst över flera regioner och till och med åtkomst på Skriv bordet.

Kryptering på klient sidan krypterar data innan de skickas till Azure Storage-instansen, så att den krypteras när den överförs i nätverket.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-kryptering över virtuella Azure-nätverk 

Genom att använda [SMB 3,0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) på virtuella datorer som kör Windows Server 2012 eller senare kan du göra data överföringar säkra genom att kryptera data i överföring över virtuella Azure-nätverk. Genom att kryptera data kan du skydda mot manipulering och avlyssning av attacker. Administratörer kan aktivera SMB-kryptering för hela servern eller bara vissa resurser.

När SMB-kryptering har Aktiver ATS för en resurs eller server har som standard endast SMB 3,0-klienter åtkomst till krypterade resurser.

## <a name="in-transit-encryption-in-vms"></a>Kryptering under överföring i virtuella datorer

Data som överförs till, från och mellan virtuella datorer som kör Windows kan krypteras på flera olika sätt, beroende på anslutningens typ.

### <a name="rdp-sessions"></a>RDP-sessioner

Du kan ansluta och logga in på en virtuell dator med hjälp av [Remote Desktop Protocol (RDP)](/windows/win32/termserv/remote-desktop-protocol) från en Windows-klientdator eller från en Mac med en RDP-klient installerad. Data som överförs via nätverket i RDP-sessioner kan skyddas av TLS.

Du kan också använda fjärr skrivbord för att ansluta till en virtuell Linux-dator i Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Säker åtkomst till virtuella Linux-datorer med SSH

För fjärrhantering kan du använda SSH ( [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) ) för att ansluta till virtuella Linux-datorer som körs i Azure. SSH är ett krypterat anslutnings protokoll som tillåter säkra inloggningar via oskyddade anslutningar. Det är standard anslutnings protokollet för virtuella Linux-datorer som finns i Azure. Genom att använda SSH-nycklar för autentisering eliminerar du behovet av lösen ord för att logga in. SSH använder ett offentligt/privat nyckel par (asymmetrisk kryptering) för autentisering.

## <a name="azure-vpn-encryption"></a>Azure VPN-kryptering

Du kan ansluta till Azure via ett virtuellt privat nätverk som skapar en säker tunnel för att skydda sekretessen för data som skickas över nätverket.

### <a name="azure-vpn-gateways"></a>Azure VPN-gatewayer

Du kan använda en [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) för att skicka krypterad trafik mellan det virtuella nätverket och den lokala platsen via en offentlig anslutning, eller för att skicka trafik mellan virtuella nätverk.

VPN för plats-till-plats använder [IPSec](https://en.wikipedia.org/wiki/IPsec) för transport kryptering. Azure VPN-gatewayer använder en uppsättning standard förslag. Du kan konfigurera Azure VPN-gatewayer så att de använder en anpassad IPsec/IKE-princip med vissa kryptografiska algoritmer och viktiga styrkor, i stället för Azures standard princip uppsättningar.

### <a name="point-to-site-vpns"></a>Punkt-till-plats-VPN

Med punkt-till-plats-VPN får enskilda klient datorer åtkomst till ett virtuellt Azure-nätverk. [SSTP (Secure Socket Tunneling Protocol)](/previous-versions/technet-magazine/cc162322(v=msdn.10)) används för att skapa VPN-tunneln. Den kan passera brand väggar (tunneln visas som en HTTPS-anslutning). Du kan använda en egen intern PKI (Public Key Infrastructure) rot certifikat utfärdare (CA) för punkt-till-plats-anslutning.

Du kan konfigurera en punkt-till-plats-VPN-anslutning till ett virtuellt nätverk med hjälp av Azure Portal med certifikatautentisering eller PowerShell.

Mer information om punkt-till-plats-VPN-anslutningar till virtuella Azure-nätverk finns i:

[Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av certifikatautentisering: Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av certifikatautentisering: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>VPN för plats-till-plats 

Du kan använda en VPN gateway-anslutning från plats till plats för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE-VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som har tilldelats en extern offentlig IP-adress.

Du kan konfigurera en plats-till-plats-VPN-anslutning till ett virtuellt nätverk med hjälp av Azure Portal, PowerShell eller Azure CLI.

Mer information finns i:

[Skapa en plats-till-plats-anslutning i Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)

[Skapa en plats-till-plats-anslutning i PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med hjälp av CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Kryptering under överföring i Data Lake

Data under överföring (även kallat data i rörelse) krypteras också alltid i Data Lake Store. Förutom att kryptera data innan de lagras i beständigt medium, skyddas även data alltid vid överföring med hjälp av HTTPS. HTTPS är det enda protokoll som stöds för Data Lake Store REST-gränssnitt.

Mer information om kryptering av data i överföring i Data Lake finns i [kryptering av data i data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Nyckel hantering med Key Vault

Krypteringen blir oanvändbar om du inte har rätt skydd och hantering av nycklarna. Key Vault är den Microsoft-rekommenderade lösningen för att hantera och kontrol lera åtkomst till krypterings nycklar som används av moln tjänster. Behörigheter för åtkomst till nycklar kan tilldelas till tjänster eller till användare via Azure Active Directory-konton.

Med Key Vault slipper organisationer konfigurera, korrigera och underhålla maskinvarumoduler för säkerhet (HSM, Hardware Security Modules) och programvara för nyckelhantering. När du använder Key Vault behåller du kontrollen. Microsoft ser aldrig dina nycklar och program har inte direkt åtkomst till dem. Du kan också importera eller generera nycklar i HSM: er.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure-säkerhet](./overview.md)
- [Översikt över nätverkssäkerhet i Azure](network-overview.md)
- [Översikt över Azure Database-säkerhet](../../azure-sql/database/security-overview.md)
- [Säkerhets översikt för Azure Virtual Machines](virtual-machines-overview.md)
- [Datakryptering i vila](encryption-atrest.md)
- [Metodtips för datasäkerhet och kryptering](data-encryption-best-practices.md)