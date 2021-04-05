---
title: Azure Data Encryption – at-rest – Azure-säkerhet
description: Den här artikeln innehåller en översikt över Azure Data Encryption, de övergripande funktionerna och allmänna överväganden.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 1318aaf50d4f05b8a999e5661318dc83997a2f02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920787"
---
# <a name="azure-data-encryption-at-rest"></a>Azure Data Encryption i vila

Microsoft Azure innehåller verktyg för att skydda data enligt företagets krav på säkerhet och efterlevnad. Det här dokumentet fokuserar på:

- Hur data skyddas i vila mellan Microsoft Azure
- Beskriver de olika komponenterna som ingår i implementeringen av data skydd.
- Granskar-och nack delar med olika metoder för nyckel hanterings skydd.

Kryptering i vila är ett vanligt säkerhets krav. I Azure kan organisationer kryptera data i vila utan risk eller kostnad för en anpassad nyckel hanterings lösning. Organisationer kan välja att låta Azure helt hantera kryptering i vila. Organisationer har dessutom olika alternativ för att noga hantera krypterings-eller krypterings nycklar.

## <a name="what-is-encryption-at-rest"></a>Vad är kryptering i vila?

Kryptering är säker kodning av data som används för att skydda konfidentialiteten för data. Kryptering i rest-modeller i Azure använder symmetrisk kryptering för att kryptera och dekryptera stora mängder data snabbt enligt en enkel konceptuell modell:

- En symmetrisk krypterings nyckel används för att kryptera data när de skrivs till lagringen.
- Samma krypterings nyckel används för att dekryptera dessa data som de är lätta att använda i minnet.
- Data kan vara partitionerade och olika nycklar kan användas för varje partition.
- Nycklar måste lagras på en säker plats med identitetsbaserade åtkomst kontroll och gransknings principer. Data krypterings nycklar krypteras ofta med en nyckel krypterings nyckel i Azure Key Vault för att ytterligare begränsa åtkomsten.

I praktiken kräver nyckel hanterings-och kontroll scenarier, samt skalnings-och tillgänglighets garantier, ytterligare konstruktioner. Microsoft Azure kryptering i rest-koncept och-komponenter beskrivs nedan.

## <a name="the-purpose-of-encryption-at-rest"></a>Syftet med kryptering i vila

Kryptering i vila ger data skydd för lagrade data (i vila). Attacker mot data i vila inkluderar försök att få fysisk åtkomst till den maskin vara som data lagras på och sedan kompromettera de inneslutna data. I ett sådant angrepp kan en servers hård disk vara felhanterad under underhållet och göra det möjligt för en angripare att ta bort hård disken. Senare skulle angriparen lagra hård disken i en dator under sin kontroll för att försöka komma åt data.

Kryptering i vila är utformad för att förhindra att angriparen får åtkomst till okrypterade data genom att se till att data krypteras på disk. Om en angripare får en hård disk med krypterade data men inte krypterings nycklarna måste angriparen manipulera krypteringen för att läsa data. Det här angreppet är mycket mer komplicerat och Resursanvändning än att komma åt okrypterade data på en hård disk. Av den anledningen rekommenderas kryptering i vila starkt och är ett högt prioriterat krav för många organisationer.

Kryptering i vila kan också krävas av en organisations behov av data styrning och efterlevnad. Bransch-och myndighets bestämmelser som HIPAA, PCI och FedRAMP, utformar särskilda skydds nivåer för data skydd och krypterings krav. Kryptering i vila är ett obligatoriskt mått som krävs för att följa vissa av dessa regler. Mer information om Microsofts metod för FIPS 140-2-verifiering finns i [Federal Information Processing standard (FIPS) publikation 140-2](/microsoft-365/compliance/offering-fips-140-2).

Förutom att uppfylla kraven för efterlevnad och regler ger kryptering i rest skydd mot djupgående skydd. Microsoft Azure tillhandahåller en kompatibel plattform för tjänster, program och data. Den tillhandahåller även omfattande anläggningar och fysisk säkerhet, data åtkomst kontroll och granskning. Det är dock viktigt att tillhandahålla ytterligare "överlappande" säkerhets åtgärder om någon av de andra säkerhets åtgärderna Miss lyckas och kryptering i vila ger en sådan säkerhets åtgärd.

Microsoft värnar om kryptering på rest-alternativ över moln tjänster och ger kunderna kontroll över krypterings nycklar och loggar av nyckel användning. Dessutom arbetar Microsoft för att kryptera all kund information på rest som standard.

## <a name="azure-encryption-at-rest-components"></a>Azure-kryptering i rest-komponenter

Som tidigare nämnts är målet för kryptering i vila att data som finns kvar på disken krypteras med en hemlig krypterings nyckel. För att uppnå detta måste du tillhandahålla ett mål för att skapa säkra nycklar, lagring, åtkomst kontroll och hantering av krypterings nycklarna. Även om informationen kan variera kan Azure Services-kryptering i rest-implementeringar beskrivas i enlighet med följande diagram.

![Komponenter](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Lagrings platsen för krypterings nycklarna och åtkomst kontrollen till dessa nycklar är central för en kryptering i rest-modellen. Nycklarna måste vara hög säkra men hanterbara av angivna användare och tillgängliga för specifika tjänster. För Azure-tjänster är Azure Key Vault den rekommenderade nyckel lagrings lösningen och ger en gemensam hanterings upplevelse över tjänster. Nycklar lagras och hanteras i nyckel valv, och åtkomst till ett nyckel valv kan ges till användare eller tjänster. Azure Key Vault stöder kund skapande av nycklar eller import av kund nycklar för användning i scenarier med Kundhanterade krypterings nycklar.

### <a name="azure-active-directory"></a>Azure Active Directory

Behörigheter för att använda nycklarna som lagras i Azure Key Vault, antingen för att hantera eller komma åt dem för kryptering i rest-kryptering och dekryptering, kan ges till Azure Active Directory-konton.

### <a name="key-hierarchy"></a>Nyckel-hierarki

Mer än en krypterings nyckel används i en kryptering vid rest-implementering. Lagring av en krypterings nyckel i Azure Key Vault garanterar säker nyckel åtkomst och central hantering av nycklar. Tjänst lokal åtkomst till krypterings nycklar är dock mer effektiv för Mass kryptering och dekryptering än att interagera med Key Vault för varje data åtgärd, vilket ger bättre kryptering och bättre prestanda. Att begränsa användningen av en enskild krypterings nyckel minskar risken för att nyckeln komprometteras och kostnaden för omkryptering när en nyckel måste bytas ut. Azure-kryptering i rest-modeller använder en nyckel-hierarki som består av följande typer av nycklar för att hantera alla dessa behov:

- **Data krypterings nyckel (DEK)** – en symmetrisk AES256-nyckel som används för att kryptera en partition eller data block.  En enskild resurs kan ha många partitioner och många data krypterings nycklar. Kryptering av varje data block med en annan nyckel gör det svårare att analysera krypteringen. Åtkomst till DEKs krävs av resurs leverantören eller program instansen som krypterar och dekrypterar ett särskilt block. När en DEK ersätts med en ny nyckel måste endast data i det associerade blocket krypteras igen med den nya nyckeln.
- **Nyckel krypterings nyckel (KEK)** – en krypterings nyckel som används för att kryptera data krypterings nycklarna. Användning av en nyckel krypterings nyckel som aldrig lämnar Key Vault tillåter att data krypterings nycklarna krypteras och kontrol leras. Entiteten som har åtkomst till KEK kan skilja sig från den entitet som kräver DEK. En entitet kan Broker-åtkomst till DEK för att begränsa åtkomsten för varje DEK till en speciell partition. Eftersom KEK krävs för att dekryptera DEKs är KEK en enda punkt med vilken DEKs kan tas bort effektivt genom borttagning av KEK.

Data krypterings nycklarna som krypteras med nyckel krypterings nycklarna lagras separat och endast en entitet med åtkomst till nyckel krypterings nyckeln kan dekryptera dessa data krypterings nycklar. Olika modeller av nyckel lagring stöds. Mer information finns i [data krypterings modeller](encryption-models.md) .

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Kryptering i vila i Microsofts moln tjänster

Microsoft Cloud tjänster används i alla tre moln modeller: IaaS, PaaS, SaaS. Nedan visas några exempel på hur de passar för varje modell:

- Program varu tjänster, som kallas program vara som en server eller SaaS, som har program som tillhandahålls av molnet, till exempel Microsoft 365.
- Plattforms tjänster som kunder utnyttjar molnet i sina program, med hjälp av molnet för saker som lagring, analys och Service Bus-funktioner.
- Infrastruktur tjänster eller infrastruktur som en tjänst (IaaS) i vilken kunden distribuerar operativ system och program som finns i molnet och eventuellt använder andra moln tjänster.

### <a name="encryption-at-rest-for-saas-customers"></a>Kryptering i vila för SaaS-kunder

SaaS-kunder (Software as a Service) har vanligt vis kryptering på rest eller tillgänglig i varje tjänst. Microsoft 365 har flera alternativ för kunderna att verifiera eller aktivera kryptering i vila. Information om Microsoft 365 tjänster finns [i kryptering i Microsoft 365](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Kryptering i vila för PaaS-kunder

PaaS (Platform as a Service)-kunder finns vanligt vis i en lagrings tjänst som Blob Storage men kan också cachelagras eller lagras i program körnings miljön, till exempel en virtuell dator. Om du vill se vilka krypterings alternativ som finns tillgängliga för dig, undersöker du tabellen nedan för de lagrings-och programplattformar som du använder.

### <a name="encryption-at-rest-for-iaas-customers"></a>Kryptering i vila för IaaS-kunder

IaaS-kunder (Infrastructure as a Service) kan ha en mängd olika tjänster och program som används. IaaS Services kan aktivera kryptering i vila på sina virtuella Azure-datorer och virtuella hård diskar med hjälp av Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Krypterad lagring

Som PaaS kan IaaS-lösningar utnyttja andra Azure-tjänster som lagrar data som är krypterade i vila. I dessa fall kan du aktivera kryptering vid rest-stöd som tillhandahålls av varje förbrukad Azure-tjänst. Tabellen nedan räknar upp de viktigaste lagrings enheterna, tjänsterna och programplattformarna och vilken modell av kryptering som stöds i rest.

#### <a name="encrypted-compute"></a>Krypterad beräkning

Alla Managed Disks, ögonblicks bilder och avbildningar krypteras med hjälp av Kryptering för lagringstjänst med hjälp av en tjänst hanterad nyckel. En mer fullständig kryptering i rest-lösning säkerställer att data aldrig sparas i okrypterad form. När data bearbetas på en virtuell dator kan data vara kvar på Windows-växlingsfilen eller Linux-swap-filen, en kraschdump eller en program logg. För att säkerställa att dessa data är krypterade i vila kan IaaS-program använda Azure Disk Encryption på en virtuell Azure IaaS-dator (Windows eller Linux) och virtuell disk.

#### <a name="custom-encryption-at-rest"></a>Anpassad kryptering i vila

Vi rekommenderar att när det är möjligt utnyttjar IaaS-program Azure Disk Encryption och kryptering i rest-alternativ som tillhandahålls av alla förbrukade Azure-tjänster. I vissa fall, till exempel oregelbundet krypterings krav eller icke-Azure-baserad lagring, kan en utvecklare av ett IaaS-program behöva implementera kryptering i vila. Utvecklare av IaaS-lösningar kan bättre integrera med Azure-hantering och kund förväntningar genom att använda vissa Azure-komponenter. Mer specifikt bör utvecklare använda tjänsten Azure Key Vault för att tillhandahålla säker nyckel lagring samt förse sina kunder med enhetliga alternativ för nyckel hantering med det i de flesta Azure Platform-tjänster. Dessutom bör anpassade lösningar använda Azure-Managed tjänst identiteter för att aktivera tjänst konton för åtkomst till krypterings nycklar. Information om utvecklare om Azure Key Vault och hanterade tjänst identiteter finns i respektive SDK: er.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure Resource providers stöd för krypterings modell

Microsoft Azure Services har stöd för en eller flera av krypteringen i rest-modeller. För vissa tjänster är det dock inte säkert att en eller flera av krypterings modellerna är tillämpliga. För tjänster som stöder Kundhanterade nyckel scenarier kan de bara ha stöd för en delmängd av de nyckel typer som Azure Key Vault har stöd för nyckel krypterings nycklar. Dessutom kan tjänster släppa support för dessa scenarier och nyckel typer vid olika scheman. I det här avsnittet beskrivs kryptering vid rest-support när detta skrivs för var och en av de stora Azure Data Storage-tjänsterna.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Alla kunder som använder Azure Infrastructure as a Service (IaaS)-funktioner kan uppnå kryptering i vila för sina IaaS-VM: ar och diskar via Azure Disk Encryption. Mer information om Azure Disk Encryption finns i [Azure Disk Encryption-dokumentationen](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Azure-lagring

Alla Azure Storage tjänster (Blob Storage, Queue Storage, Table Storage och Azure Files) stöder kryptering på Server sidan i vila. vissa tjänster stöder även Kundhanterade nycklar och kryptering på klient sidan.

- Server sidan: alla Azure Storage-tjänster aktiverar kryptering på Server sidan som standard med hjälp av tjänst nycklar, som är transparent för programmet. Mer information finns i [Azure Storage tjänst kryptering för vilande data](../../storage/common/storage-service-encryption.md). Azure Blob Storage och Azure Files stöder även RSA 2048-bitars Kundhanterade nycklar i Azure Key Vault. Mer information finns i [kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).
- Klient sidan: Azure-blobar, tabeller och köer stöder kryptering på klient sidan. När du använder kryptering på klient sidan, krypterar kunder data och laddar upp data som en krypterad blob. Nyckel hanteringen görs av kunden. Mer information finns i [kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database stöder för närvarande kryptering i vila för Microsoft-hanterad tjänst sida och krypterings scenarier på klient sidan.

Stöd för server kryptering tillhandahålls för närvarande genom SQL-funktionen som kallas transparent datakryptering. När en Azure SQL Database-kund aktiverar TDE-nyckeln skapas och hanteras automatiskt. Kryptering i vila kan aktive ras på databas-och server nivå. Från och med juni 2017 är [Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) aktiverat som standard på nyligen skapade databaser. Azure SQL Database stöder RSA 2048-bitars Kundhanterade nycklar i Azure Key Vault. Mer information finns i [Transparent datakryptering med Bring Your Own Key stöd för Azure SQL Database och informations lager](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

Kryptering på klient sidan av Azure SQL Database data stöds via funktionen [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) . Always Encrypted använder en nyckel som skapas och lagras av klienten. Kunder kan lagra huvud nyckeln i ett Windows-certifikat Arkiv, Azure Key Vault eller en lokal säkerhetsmodul för maskin vara. Med hjälp av SQL Server Management Studio väljer SQL-användare vilken nyckel de vill använda för att kryptera vilken kolumn som helst.

## <a name="conclusion"></a>Slutsats

Skydd av kund information som lagras i Azure-tjänster är av största vikt för Microsoft. Alla Azure-värdbaserade tjänster allokeras för att tillhandahålla kryptering vid rest-alternativ. Azure-tjänster har stöd för antingen service-hanterade nycklar, Kundhanterade nycklar eller kryptering på klient sidan. Azure-tjänster ökar i stor mängd kryptering vid vila och nya alternativ planeras för för hands version och allmän tillgänglighet under kommande månader.

## <a name="next-steps"></a>Nästa steg

- Se [data krypterings modeller](encryption-models.md) för att lära dig mer om tjänst hanterade nycklar och Kundhanterade nycklar.
- Lär dig hur Azure använder [Double Encryption](double-encryption.md) för att minimera hot som kommer att kryptera data.
- Lär dig vad Microsoft gör för att säkerställa [plattforms integriteten och säkerheten](platform.md) för värdar som går igenom maskin varan och bygga upp, integrera, driftsättning och reparera pipeliner.
