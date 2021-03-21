---
title: Vanliga frågor och Azure-dedikerad HSM | Microsoft Docs
description: Få svar på vanliga frågor om Azures särskilda maskinvarubaserade säkerhetsmodul, till exempel grundläggande information, interoperabilitet, hög tillgänglighet och support.
services: dedicated-hsm
author: keithp
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: keithp
ms.openlocfilehash: 1f349931fb479be3b3ba936b3d0a665427357f29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369046"
---
# <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Få svar på vanliga frågor om Microsoft Azure dedikerad HSM.

## <a name="the-basics"></a>Grunderna

### <a name="q-what-is-a-hardware-security-module-hsm"></a>F: Vad är en modul för maskin varu säkerhet (HSM)?

En modul för maskin varu säkerhet (HSM) är en fysisk dator enhet som används för att skydda och hantera kryptografiska nycklar. Nycklar som lagras i HSM: er kan användas för kryptografiska åtgärder. Huvud materialet förblir säkert i Manipulerings motstånds kraftigt, manipulerade maskinvarubaserade moduler. HSM tillåter endast autentiserade och auktoriserade program att använda nycklarna. Nyckel materialet lämnar aldrig HSM-skyddets gränser.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>F: Vad är Azure Dedicated HSM-erbjudandet?

Azure Dedicated HSM är en molnbaserad tjänst som tillhandahåller HSM: er som finns i Azure-datacenter som är direkt anslutna till kundens virtuella nätverk. Dessa HSM: er är dedikerade nätverks enheter (Thales Network Luna HSM 7). De distribueras direkt till kunders privata IP-adressutrymme och Microsoft har ingen åtkomst till de kryptografiska funktionerna i HSM: er. Endast kunden har fullständig administrativ och kryptografisk kontroll över dessa enheter. Kunderna ansvarar för hanteringen av enheten och de kan få fullständiga aktivitets loggar direkt från sina enheter. Dedikerade HSM: er hjälper kunder att uppfylla krav på efterlevnad/bestämmelser som FIPS 140-2 nivå 3, HIPAA, PCI-DSS och eIDAS och många andra.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>F: vilken maskin vara används för dedikerad HSM?

Microsoft samarbetar med Thales för att leverera Azures dedikerade HSM-tjänst. Den speciella enhet som används är [Thales Network Luna HSM 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Den här enheten tillhandahåller inte bara FIPS 140-2 nivå 3-verifierad inbyggd program vara, men erbjuder även låg latens, höga prestanda och hög kapacitet via 10 partitioner. 

### <a name="q-what-is-an-hsm-used-for"></a>F: Vad används en HSM för?

HSM: er används för att lagra kryptografiska nycklar som används för krypterings funktioner som TLS (Transport Layer Security), kryptering av data, PKI (Public Key Infrastructure), DRM (Digital Rights Management) och signerings dokument.

### <a name="q-how-does-dedicated-hsm-work"></a>F: Hur fungerar dedikerad HSM?

Kunder kan etablera HSM: er i vissa regioner med PowerShell eller kommando rads gränssnittet. Kunden anger vilka virtuella nätverk som HSM: er ska anslutas till och när HSM: er har allokerats blir de tillgängliga i det angivna under nätet vid tilldelade IP-adresser i kundens privata IP-adressutrymme. Sedan kan kunderna ansluta till HSM: er med SSH för hantering och administration av HSM, konfigurera HSM-klientanslutningar, initiera HSM: er, skapa partitioner, definiera och tilldela roller som partitions chef, kryptografi befäl och kryptografi användare. Sedan använder kunden Thales-klient verktyg/SDK/program vara för att utföra kryptografiska åtgärder från sina program.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>F: vilken program vara medföljer den dedikerade HSM-tjänsten?

Thales tillhandahåller all program vara för HSM-enheten när den har tillhandahållits av Microsoft. Program varan är tillgänglig på [Thales kund support Portal](https://supportportal.gemalto.com/csm/). Kunder som använder dedikerad HSM-tjänst måste registreras för Thales-support och ha ett kund-ID som ger åtkomst till och nedladdning av relevant program vara. Den klient program vara som stöds är version 7,2, som är kompatibel med den validerade 7.0.3-versionen av FIPS 140-2 nivå 3. 

### <a name="q-what-extra-costs-may-be-incurred-with-dedicated-hsm-service"></a>F: vilka extra kostnader kan uppstå med dedikerad HSM-tjänst?

Följande objekt medför extra kostnad när den dedikerade HSM-tjänsten används. 
* Användning av dedikerad lokal säkerhets kopierings enhet är möjlig att använda med dedikerad HSM-tjänst, men detta kommer att medföra en extra kostnad och bör startas direkt från Thales.
* Dedikerad HSM ingår i en 10-partitions licens. Om en kund behöver fler partitioner, kommer detta att innebära en extra kostnad för ytterligare licenser som direkt debiteras från Thales.
* Dedikerad HSM kräver nätverks infrastruktur (VNET, VPN Gateway osv.) och resurser, till exempel virtuella datorer för enhets konfiguration. Dessa ytterligare resurser debiteras extra kostnader och ingår inte i priset för dedikerad HSM-tjänst.

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>F: erbjuder Azure Dedicated HSM lösen ords-och INMATNINGSENHET-baserad autentisering?

För närvarande ger Azure Dedicated HSM endast HSM: er med lösenordsbaserad autentisering.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>F: är Azures dedikerade HSM-värd mitt HSM: er åt mig?

Microsoft erbjuder endast Thales Network Luna HSM 7 via den dedikerade HSM-tjänsten och kan inte vara värd för alla enheter som tillhandahålls av kunden.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>F: fungerar Azure-funktioner för dedikerad HSM-support (PIN/EFT)?

Azure Dedicated HSM-tjänsten använder Thales Network Luna HSM 7-enheter. Dessa enheter har inte stöd för funktionen för att betala HSM-speciella funktioner (till exempel PIN-kod eller EFT) eller certifieringar. Om du vill att Azures dedikerade HSM-tjänst ska stödja betalnings HSM: er i framtiden, kan du skicka feedback till din Microsoft-konto representant.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>F: vilka Azure-regioner är dedikerade HSM tillgängliga i?

Från och med sent mars 2019 är dedikerad HSM tillgänglig i de 14 regionerna som anges nedan. Ytterligare regioner planeras och kan diskuteras via din Microsoft-konto representant.

* East US
* USA, östra 2
* USA, västra
* USA, västra 2
* USA, södra centrala
* Sydostasien
* Asien, östra
* Indien, centrala
* Södra Indien
* Japan, östra
* Japan, västra
* Europa, norra
* Europa, västra
* Storbritannien, södra
* Storbritannien, västra
* Kanada, centrala
* Kanada, östra
* Australien, östra
* Australien, sydöstra
* Schweiz, norra
* Schweiz, västra
* US Gov, Virginia
* US Gov, Texas

## <a name="interoperability"></a>Samverkan

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>F: Hur ansluter mitt program till en dedikerad HSM?

Du använder Thales som tillhandahålls HSM-klient verktyg/SDK/program vara för att utföra kryptografiska åtgärder från dina program. Program varan är tillgänglig på [Thales kund support Portal](https://supportportal.gemalto.com/csm/). Kunder som använder dedikerad HSM-tjänst måste registreras för Thales-support och ha ett kund-ID som ger åtkomst till och nedladdning av relevant program vara.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>F: kan ett program ansluta till dedikerad HSM från ett annat VNET i eller mellan regioner?

Ja, du måste använda VNet- [peering](../virtual-network/virtual-network-peering-overview.md) inom en region för att upprätta anslutningar mellan virtuella nätverk. För anslutning mellan regioner måste du använda [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>F: kan jag synkronisera dedikerad HSM med lokal HSM: er?

Ja, du kan synkronisera lokala HSM: er med dedikerad HSM. [Punkt-till-plats-VPN eller punkt-till-plats](../vpn-gateway/vpn-gateway-about-vpngateways.md) -anslutning kan användas för att upprätta anslutningar till ditt lokala nätverk.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>F: kan jag kryptera data som används av andra Azure-tjänster med hjälp av nycklar som lagras i dedikerad HSM?

Nej. De dedikerade Azure-HSM: er är bara tillgängliga i det virtuella nätverket.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>F: kan jag importera nycklar från en befintlig lokal HSM till dedikerad HSM?

Ja, om du har lokalt Thales Network Luna HSM 7 HSM: er. Det finns flera metoder. Se [Thales HSM-dokumentationen](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>F: vilka operativ system stöds av dedikerade klient program vara för HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuell: VMware, Hyper-V, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>F: Hur gör jag för att konfigurera mitt klient program för att skapa en konfiguration med hög tillgänglighet med flera partitioner från flera HSM: er?

Om du vill ha hög tillgänglighet måste du konfigurera din HSM-klients program konfiguration för att använda partitioner från varje HSM. Läs dokumentationen om Thales HSM-klientprogramvaran.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>F: vilka autentiseringsmekanismer stöds av dedikerad HSM?

Azure Dedicated HSM använder SafeNet Network HSM 7-enheter (modell A790) och de stöder lösenordsbaserad autentisering.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>F: vilka SDK: er, API: er, klient program vara kan användas med dedikerad HSM?

PKCS # 11, Java (JCA/gemensamt), Microsoft CAPI och CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>F: kan jag importera/migrera nycklar från Luna 5/6 HSM: er till Azure Dedicated HSM: er?

Ja. Kontakta din Thales-representant för lämplig Thales migration-guide. 

## <a name="using-your-hsm"></a>Använda din HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>F: Hur gör jag för att bestämma om du vill använda Azure Key Vault eller Azure Dedicated HSM?

Azure Dedicated HSM är det bästa valet för företag som migrerar till lokala Azure-program som använder HSM: er. Dedikerad HSM: er visar ett alternativ för att migrera ett program med minimala ändringar. Om kryptografiska åtgärder utförs i programmets kod som körs i en virtuell Azure-dator eller webbapp kan de använda dedikerad HSM. I allmänhet är det att komprimera program vara som körs i IaaS-modeller (infrastruktur som en tjänst), som stöder HSM: er som ett nyckel lager kan använda dedicerad HSM, till exempel Application Gateway eller Traffic Manager för Keyless TLS, ADCS (Active Directory Certificate Services) eller liknande PKI-verktyg, verktyg/program som används för dokument signering, kod signering eller en SQL Server (IaaS) som kon figurer ATS med TDE (transparent databas kryptering) med huvud nyckeln i en HSM med hjälp av en EKM-Provider Azure Key Vault lämpar sig för program som är födda i molnet eller för kryptering i vilo läge där kund information bearbetas av PaaS (plattform som en tjänst) eller SaaS (program vara som en tjänst), t. ex. Office 365 kund nyckel, Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store kryptering med kundhanterad nyckel, Azure Storage kryptering med kundhanterad nyckel och Azure SQL med kundhanterad nyckel.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>F: vilka användnings scenarier passar bäst i Azure Dedicated HSM?

Azure Dedicated HSM är mest lämplig för migrerings scenarier. Det innebär att om du migrerar lokala program till Azure som redan använder HSM: er. Detta ger ett alternativ för låg friktion att migrera till Azure med minimala ändringar i programmet. Om kryptografiska åtgärder utförs i programmets kod som körs i en virtuell Azure-dator eller i en webbapp, kan dedikerad HSM användas. I allmänhet är det möjligt att komprimera program vara som körs i IaaS (infrastruktur som en tjänst) som har stöd för HSM: er som ett nyckel lager kan använda dedicerad HSM, till exempel:

* Application Gateway eller Traffic Manager för Keyless TLS
* ADCS (Active Directory certifikat tjänster)
* Liknande PKI-verktyg
* Verktyg/program som används för dokument signering
* Kod signering
* SQL Server (IaaS) som kon figurer ATS med TDE (transparent databas kryptering) med huvud nyckeln i en HSM med en EKM-Provider (Extensible Key Management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>F: kan dedikerad HSM användas med Office 365 kund nyckel, Azure Information Protection, Azure Data Lake Store, disk kryptering, Azure Storage kryptering, Azure SQL-TDE?

Nej. Dedikerad HSM tillhandahålls direkt till kundens privata IP-adressutrymme så att den inte kan nås av andra Azure-eller Microsoft-tjänster.

## <a name="administration-access-and-control"></a>Administration, åtkomst och kontroll

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-"></a>F: får kunden fullständig kontroll över HSM: er med dedikerat?

Ja. Varje HSM-installation är fullständigt dedikerad till en enda kund och ingen annan har administrativ kontroll när den har kon figurer ATS och administratörs lösen ordet har ändrats.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>F: vilken åtkomst nivå har Microsoft till gång till min HSM?

Microsoft har ingen administrativ eller kryptografisk kontroll över HSM. Microsoft har åtkomst till övervaknings nivå via seriell port anslutning för att hämta grundläggande telemetri, till exempel temperatur och komponent hälsa. Detta gör att Microsoft kan tillhandahålla proaktiv avisering om hälso problem. Vid behov kan kunden inaktivera det här kontot.

### <a name="q-what-is-the-tenant-admin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-thales-luna-hsms"></a>F: Vad är kontot "innehavaradministratör" som Microsoft använder, jag används för administratörs användare som "admin" på Thales Luna HSM: er?

HSM-enheten levereras med en standard användare av administratör med det vanliga standard lösen ordet. Microsoft vill inte ha standard lösen ord som används när en enhet är i en pool som väntar på att tillhandahållas av kunderna. Detta uppfyller inte våra strikta säkerhets krav. Därför ställer vi in ett starkt lösen ord som tas bort vid etablerings tiden. Vid etablerings tiden skapar vi också en ny användare i administratörs rollen som kallas "innehavaradministratör". Den här användaren har standard lösen ordet och kunderna ändrar detta som första åtgärd vid första inloggningen till den nyligen etablerade enheten. Den här processen säkerställer höga säkerhets nivåer och upprätthåller vårt löfte om ensam administratörs kontroll för våra kunder. Det bör noteras att användaren "innehavaradministratör" kan användas för att återställa administratörs lösen ordet om en kund föredrar att använda det kontot. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>F: kan Microsoft eller någon på Microsoft Access-nycklar i min dedikerad HSM?

Nej. Microsoft har ingen åtkomst till nycklarna som lagras i en dedikerad HSM-kund.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>F: kan jag uppgradera program vara/inbyggd program vara på HSM: er som tilldelats mig?

Kunden har fullständig administrativ kontroll, inklusive uppgradering av program vara/inbyggd program vara om specifika funktioner krävs från olika versioner av den inbyggda program varan. Innan du gör några ändringar bör du kontakta Microsoft om uppgraderingen genom att kontakta HSMRequest@microsoft.com  

### <a name="q-how-do-i-manage-dedicated-hsm"></a>F: Hur gör jag för att hantera dedikerad HSM?

Du kan hantera dedikerade HSM: er genom att använda SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>F: Hur gör jag för att hantera partitioner på dedikerad HSM?

Thales HSM-klientprogramvaran används för att hantera HSM: er och partitioner.

### <a name="q-how-do-i-monitor-my-hsm"></a>F: Hur gör jag för att övervaka min HSM?

En kund har fullständig åtkomst till HSM-aktivitets loggar via syslog och SNMP. En kund måste konfigurera en Syslog-server eller SNMP-Server för att ta emot loggarna eller händelserna från HSM: er.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>F: kan jag få fullständig åtkomst logg över alla HSM-åtgärder från dedikerad HSM?

Ja. Du kan skicka loggar från HSM-enheten till en Syslog-server

## <a name="high-availability"></a>Hög tillgänglighet

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>F: är det möjligt att konfigurera hög tillgänglighet i samma region eller i flera regioner?

Ja. Konfiguration och inställningar för hög tillgänglighet utförs i HSM-klientprogramvaran som tillhandahålls av Thales. HSM: er från samma VNET eller andra virtuella nätverk i samma region eller i flera regioner, eller lokalt HSM: er som är anslutna till ett VNET med hjälp av plats-till-plats-eller punkt-till-plats-VPN kan läggas till i samma konfiguration för hög tillgänglighet. Det bör noteras att detta bara synkroniserar nyckel material och inte vissa konfigurations objekt, till exempel roller.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>F: kan jag lägga till HSM: er från mitt lokala nätverk till en grupp med hög tillgänglighet med Azure Dedicated HSM?

Ja. De måste uppfylla kraven på hög tillgänglighet för SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>F: kan jag lägga till Luna 5/6-HSM: er från lokala nätverk till en grupp med hög tillgänglighet med Azure Dedicated HSM?

Nej.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>F: hur många HSM: er kan jag lägga till i samma konfiguration för hög tillgänglighet från ett enda program?

16 medlemmar i en HA-grupp har genomgått en full begränsnings testning med utmärkta resultat.

## <a name="support"></a>Support

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>F: Vad är service avtalet för dedikerad HSM-tjänst?

Ingen särskild garanti för drift tid har angetts för dedikerad HSM-tjänst. Microsoft garanterar åtkomst till enheten på nätverks nivå och därför använder Azure-service avtal standard.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>F: Hur används HSM: er i Azure Dedicated HSM?

Azure-datacenter har omfattande fysiska och stegvisa säkerhets kontroller. Förutom att dedikerade HSM: er finns i ett ytterligare begränsat åtkomst utrymme i data centret. Dessa områden har ytterligare fysiska åtkomst kontroller och övervakning av video kamera för ökad säkerhet.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>F: Vad händer om det uppstår ett säkerhets intrång eller en maskin varu manipulations händelse?

Dedikerad HSM-tjänst använder Thales Network Luna HSM 7-enheter. Dessa apparater stöder fysisk och logisk manipulation. Om det finns en ändrings händelse av HSM: er nollställs automatiskt.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>F: Hur gör jag för att kontrol lera att nycklarna i mina dedikerade HSM: er inte går förlorade på grund av ett fel eller ett skadligt Insider angrepp?

Vi rekommenderar starkt att du använder en lokal HSM backup-enhet för att utföra regelbundna regelbundna säkerhets kopieringar av HSM: er för katastrof återställning. Du måste använda en peer-till-peer-eller plats-till-plats-VPN-anslutning till en lokal arbets station som är ansluten till en HSM-enhet för säkerhets kopiering.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>F: Hur gör jag för att få stöd för dedikerad HSM?

Support tillhandahålls av både Microsoft och Thales.  Om du har problem med maskin varan eller nätverks åtkomsten kan du generera en supportbegäran med Microsoft och om du har problem med HSM-konfiguration, program vara och program utveckling får du en supportbegäran med Thales. Om du har ett avidentifierat problem, kan du generera en supportbegäran med Microsoft och sedan kan Thales användas som obligatorisk. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-thales-network-luna-hsm-7"></a>F: Hur gör jag för att hämta klient program vara, dokumentation och åtkomst till integrerings vägledning för Thales Network Luna HSM 7?

När du har registrerat dig för tjänsten får du ett Thales kund-ID som möjliggör registrering i Thales kund support Portal. Detta ger åtkomst till all program vara och dokumentation samt att aktivera support förfrågningar direkt med Thales.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-thales-who-is-responsible-for-upgradingpatching-osfirmware"></a>F: om det finns ett säkerhets problem och en korrigering släpps av Thales, som ansvarar för uppgradering/korrigering av OS/inbyggd program vara?

Microsoft har inte möjlighet att ansluta till HSM: er som har tilldelats till kunder. Kunderna måste uppgradera och korrigera sina HSM: er.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>F: Vad händer om jag behöver starta om min HSM?

HSM har ett alternativ för omstart av kommando rad, men vi har problem där omstart slutar att svara, och därför rekommenderas det för den säkraste starten att du höjer en supportbegäran med Microsoft för att enheten ska starta om fysiskt. 

## <a name="cryptography-and-standards"></a>Kryptografi och standarder

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>F: är det säkert att lagra krypterings nycklar för mina viktigaste data i dedikerad HSM?

Ja, dedikerade HSM-bestämmelser Thales Network Luna HSM 7-enheter som använder FIPS 140-2 nivå 3-verifierade HSM: er. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>F: vilka kryptografiska nycklar och algoritmer stöds av dedikerad HSM?

Dedikerad HSM-tjänst tillhandahåller Thales Network Luna HSM 7-enheter. De har stöd för ett brett utbud av kryptografiska nyckel typer och algoritmer, inklusive: fullständig Suite B stöd

* Asymmetric
  * RSA
  * DSA
  * Diffie-Hellman
  * Elliptic kurva
  * Kryptografi (ECDSA, ECDH, Ed25519, ECIES) med namngivna, användardefinierade och Brainpoola kurvor, KCDSA
* Symmetriska
  * AES-GCM
  * Tredubbel DES
  * DES
  * ARIA, SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/meddelande sammandrag/HMAC: SHA-1, SHA-2, SM3
  * Nyckel härledning: SP 800-108-räknar läge
  * Nyckel brytning: SP 800 – 38F
  * Slumptalsgenerering: FIPS 140-2-godkänd DRBG (SP 800-90-läge), uppfyller BSI DRG. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>F: är dedikerad HSM FIPS 140-2 Level 3 verifierad?

Ja. Dedikerad HSM-tjänst etablerar Thales Network Luna HSM 7-enheter som använder FIPS 140-2 nivå 3-verifierade HSM: er.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>F: Vad behöver jag göra för att se till att jag använder dedikerad HSM i FIPS 140-2 nivå 3-validerat läge?

Dedikerad HSM-tjänst etablerar Thales Network Luna HSM 7-enheter. Dessa apparater använder FIPS 140-2 nivå 3-verifierade HSM: er. Den distribuerade standard konfigurationen, operativ systemet och den inbyggda program varan är också FIPS-verifierad. Du behöver inte vidta några åtgärder för kompatibilitet på FIPS 140-2 nivå 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>F: Hur ser en kund till att när en HSM avetableras rensas alla viktiga material?

Innan du begär avetablering måste kunden ha nollställt HSM med hjälp av Thales-klient verktyg som tillhandahålls av HSM.

## <a name="performance-and-scale"></a>Prestanda och skalning

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>F: hur många kryptografiska åtgärder som stöds per sekund med dedikerad HSM?

Dedikerade HSM-bestämmelser Thales Network Luna HSM 7 HSM: er. Här är en sammanfattning av högsta prestanda för vissa åtgärder: 

* RSA-2048:10 000 transaktioner per sekund
* ECC-P256:20 000 transaktioner per sekund
* AES-GCM: 17 000 transaktioner per sekund

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>F: hur många partitioner kan skapas i dedikerad HSM?

SafeNet Luna HSM 7-A790 används innehåller en licens för 10 partitioner i tjänstens kostnad. Enheten har en gräns på 100 partitioner och att lägga till partitioner upp till den här gränsen skulle medföra extra licensierings kostnader och kräva installation av en ny licens fil på enheten.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>F: hur många nycklar kan stödjas i dedikerad HSM?

Det maximala antalet nycklar är en funktion i tillgängligt minne. SafeNet Luna 7-modellen A790 som används har 32 MB minne. Följande siffror gäller även för nyckel par om du använder asymmetriska nycklar.

* RSA-2048-19 000
* ECC-P256 – 91 000

Kapaciteten varierar beroende på vilka nyckelattribut som angetts i mallen för nyckel skapande och antalet partitioner.
