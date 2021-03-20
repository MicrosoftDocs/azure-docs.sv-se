---
title: Så här kör du OPC Vault Certificate Management-tjänsten på ett säkert sätt – Azure | Microsoft Docs
description: Beskriver hur du kör OPC-tjänsten för certifikat hantering på ett säkert sätt i Azure och granskar andra säkerhets rikt linjer som du bör tänka på.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f54467c03a67797d52ac499ca9ba455c0f75e240
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92071463"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Kör OPC Vault Certificate Management-tjänsten på ett säkert sätt

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den här artikeln förklarar hur du kör OPC-valvet för certifikat hantering på ett säkert sätt i Azure och granskar andra säkerhets rikt linjer för att tänka på.

## <a name="roles"></a>Roller

### <a name="trusted-and-authorized-roles"></a>Betrodda och auktoriserade roller

Mikrotjänsten OPC Vault gör det möjligt för olika roller att komma åt olika delar av tjänsten.

> [!IMPORTANT]
> Under distributionen lägger skriptet bara till den användare som kör distributions skriptet som en användare för alla roller. För en produktions distribution bör du granska roll tilldelningen och konfigurera på lämpligt sätt genom att följa rikt linjerna nedan. Den här uppgiften kräver manuell tilldelning av roller och tjänster i Azure Active Directory-portalen (Azure AD) Enterprise-program.

### <a name="certificate-management-service-roles"></a>Tjänst roller för certifikat hantering

OPC Vault-mikrotjänsten definierar följande roller:

- **Läsare**: som standard har alla autentiserade användare i klienten Läs behörighet. 
  - Läs åtkomst till program och certifikat begär Anden. Kan lista och fråga efter program-och certifikat begär Anden. Dessutom är enhets identifierings information och offentliga certifikat tillgängliga med Läs behörighet.
- **Skribent**: skrivar rollen tilldelas till en användare för att lägga till Skriv behörighet för vissa uppgifter. 
  - Läs-/Skriv behörighet till program och certifikat begär Anden. Kan registrera, uppdatera och avregistrera program. Kan skapa certifikat begär Anden och hämta godkända privata nycklar och certifikat. Det går också att ta bort privata nycklar.
- **God kännare**: god kännare-rollen tilldelas en användare för att godkänna eller avvisa certifikat begär Anden. Rollen omfattar inte någon annan roll.
  - Förutom rollen god kännare för att få åtkomst till API: et för OPC Vault mikrotjänst måste användaren också ha behörigheten nyckel signering i Azure Key Vault för att kunna signera certifikaten.
  - Rollen skrivare och god kännare bör tilldelas till olika användare.
  - Den huvudsakliga rollen hos god kännaren är godkännande av generering och avvisande av certifikat begär Anden.
- **Administratör**: administratörs rollen tilldelas till en användare för att hantera certifikat grupperna. Rollen har inte stöd för rollen god kännare, men innehåller rollen skrivare.
  - Administratören kan hantera certifikat grupper, ändra konfigurationen och återkalla program certifikat genom att utfärda en ny lista över återkallade certifikat (CRL).
  - Vi rekommenderar att rollerna skrivare, god kännare och administratör tilldelas olika användare. För ytterligare säkerhet måste en användare med rollen god kännare eller administratör också ha behörighet för nyckel signering i Key Vault, för att utfärda certifikat eller förnya certifikat utfärdarens CA-certifikat.
  - Förutom rollen som mikrotjänst administration inkluderar rollen, men är inte begränsad till:
    - Ansvar för att administrera implementeringen av CA: ns säkerhets rutiner.
    - Hantering av generering, åter kallelse och upphävande av certifikat. 
    - Livs cykel hantering av kryptografisk nyckel (till exempel förnyelse av utfärdares CA-nycklar).
    - Installation, konfiguration och underhåll av tjänster som använder certifikat utfärdaren.
    - Daglig drift av tjänsterna. 
    - Säkerhets kopiering och återställning av CA och databasen.

### <a name="other-role-assignments"></a>Andra roll tilldelningar

Tänk också på följande roller när du kör tjänsten:

- Företags ägare till avtal för certifikat inköp med den externa rot certifikat utfärdaren (till exempel när ägaren köper certifikat från en extern certifikat utfärdare eller använder en certifikat utfärdare som är underordnad en extern certifikat utfärdare).
- Utveckling och validering av certifikat utfärdaren.
- Granskning av gransknings poster.
- Personal som hjälper till att stödja CA: n eller hantera de fysiska och molnbaserade funktionerna, men som inte är direkt betrodda att utföra CA-åtgärder, finns i den *auktoriserade* rollen. Uppsättningen med uppgifter som personer i den auktoriserade rollen tillåts utföra måste också dokumenteras.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Granska medlemskap i betrodda och auktoriserade roller kvartals vis

Granska medlemskap i betrodda och auktoriserade roller minst kvartals vis. Se till att en uppsättning av personer (för manuella processer) eller tjänst identiteter (för automatiserade processer) i varje roll hålls till ett minimum.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Rollseparering mellan en certifikat beställare och god kännare

Processen för utfärdande av certifikat måste tillämpa roll åtskillnad mellan certifikat begär Anden och certifikat god kännare roller (personer eller automatiserade system). Utfärdande av certifikat måste godkännas av en certifikats god kännare roll som verifierar att den begär ande certifikat utfärdaren har behörighet att skaffa certifikat. De personer som innehar rollen som god kännare måste vara en formellt auktoriserad person.

### <a name="restrict-assignment-of-privileged-roles"></a>Begränsa tilldelning av privilegierade roller

Du bör begränsa tilldelningen av privilegierade roller, till exempel att auktorisera medlemskap i gruppen Administratörer och god kännare, till en begränsad uppsättning behörig personal. Ändringar av privilegierade roller måste ha åtkomst återkallade inom 24 timmar. Slutligen granskar du privilegierade roll tilldelningar varje kvartal och tar bort alla onödiga eller förfallna tilldelningar.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Privilegierade roller bör använda tvåfaktorautentisering

Använd Multi-Factor Authentication (kallas även tvåfaktorautentisering) för interaktiva inloggningar av god kännare och administratörer till tjänsten.

## <a name="certificate-service-operation-guidelines"></a>Rikt linjer för certifikat tjänst åtgärder

### <a name="operational-contacts"></a>Drift kontakter

Certifikat tjänsten måste ha en uppdaterad säkerhets svars plan för en fil som innehåller detaljerade rapporter om drift incident svar.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Alla system måste övervakas kontinuerligt och uppdateras med de senaste säkerhets uppdateringarna.

> [!IMPORTANT]
> GitHub-lagringsplatsen i OPC Vault-tjänsten uppdateras kontinuerligt med säkerhets korrigeringar. Övervaka uppdateringarna och tillämpa dem på tjänsten med jämna mellanrum.

### <a name="security-monitoring"></a>Säkerhetsövervakning

Prenumerera på eller implementera lämplig säkerhets övervakning. Du kan till exempel prenumerera på en central övervaknings lösning (till exempel Azure Security Center eller Microsoft 365 övervaknings lösning) och konfigurera den på lämpligt sätt för att säkerställa att säkerhets händelser överförs till övervaknings lösningen.

> [!IMPORTANT]
> OPC Vault-tjänsten distribueras som standard med [Azure Application insikter](../azure-monitor/app/devops.md) som en övervaknings lösning. Det rekommenderas starkt att du lägger till en säkerhetslösning som [Azure Security Center](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-the-security-of-open-source-software-components"></a>Utvärdera säkerheten för program varu komponenter med öppen källkod

Alla komponenter med öppen källkod som används i en produkt eller tjänst måste vara fria från medelhög eller större säkerhets sårbarheter.

> [!IMPORTANT]
> Under kontinuerlig integrerings versioner genomsöker GitHub-lagringsplatsen för OPC-valvet alla komponenter för sårbarheter. Övervaka uppdateringarna på GitHub och tillämpa dem på tjänsten med jämna mellanrum.

### <a name="maintain-an-inventory"></a>Underhålla en inventering

Underhåll en till gångs inventering för alla produktions värdar (inklusive beständiga virtuella datorer), enheter, alla interna IP-adressintervall, VIP och offentliga DNS-domännamn. När du lägger till eller tar bort ett system, en IP-adress för enheten, en VIP-eller offentlig DNS-domän måste du uppdatera inventeringen inom 30 dagar.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventering av standard produktions distributionen för Azure OPC Vault-mikrotjänster 

I Azure:
- **App Service plan**: App Service-plan för tjänst värdar. Standard S1.
- **App Service** för mikrotjänster: OPC Vault service-värd.
- **App Service** för exempel program: OPC-valvets exempel program värd.
- **Key Vault standard**: att lagra hemligheter och Azure Cosmos DB nycklar för webb tjänsterna.
- **Key Vault Premium**: för att vara värd för utfärdares ca-nycklar, för signerings tjänsten och för valv konfiguration och lagring av program privata nycklar.
- **Azure Cosmos DB**: databas för program-och certifikat begär Anden. 
- **Application Insights**: (valfritt) övervaknings lösning för webb tjänst och program.
- **Registrering av Azure AD-program**: en registrering för exempel programmet, tjänsten och Edge-modulen.

För moln tjänsterna ska alla värdnamn, resurs grupper, resurs namn, prenumerations-ID och klient-ID: n som används för att distribuera tjänsten dokumenteras. 

I Azure IoT Edge eller en lokal IoT Edge Server:
- **OPC Vault IoT Edge modul**: för att stödja en global identifierings Server för fabriks nätverk för OPC UA. 

För IoT Edge enheter ska värd namnen och IP-adresserna dokumenteras. 

### <a name="document-the-certification-authorities-cas"></a>Dokumentera certifikat utfärdare (ca)

Dokumentationen för CA-hierarkin måste innehålla alla drivna certifikat utfärdare. Detta omfattar alla relaterade underordnade certifikat utfärdare, överordnade certifikat utfärdare och rot certifikat utfärdare, även när de inte hanteras av tjänsten. I stället för formell dokumentation kan du ange en fullständig uppsättning CA-certifikat som inte har upphört att gälla.

> [!NOTE]
> Exempel programmet OPC Vault stöder nedladdning av alla certifikat som används och produceras i tjänsten för dokumentation.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Dokumentera de utfärdade certifikaten från alla certifikat utfärdare

Tillhandahålla en fullständig uppsättning av alla certifikat som utfärdats under de senaste 12 månaderna.

> [!NOTE]
> Exempel programmet OPC Vault stöder nedladdning av alla certifikat som används och produceras i tjänsten för dokumentation.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Dokumentera standard drift proceduren för att säkert ta bort kryptografiska nycklar

Under en CERTIFIKATs livs längd kan nyckel borttagningen bara ske sällan. Detta är anledningen till att ingen användare har Key Vault behörigheten Ta bort certifikat, och varför det inte finns några API: er exponerade för att ta bort ett CA-certifikat. Den manuella standard drift proceduren för att säkert ta bort certifikat utfärdarens kryptografiska nycklar är bara tillgänglig genom direkt åtkomst till Key Vault i Azure Portal. Du kan också ta bort certifikat gruppen i Key Vault. Inaktivera funktionen [Key Vault mjuk borttagning](../key-vault/general/soft-delete-overview.md) för att säkerställa omedelbar borttagning.

## <a name="certificates"></a>Certifikat

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certifikat måste följa minsta certifikat profil

OPC Vault-tjänsten är en online-certifikatutfärdare som utfärdar certifikat för slutentiteter till prenumeranter. Mikrotjänsten OPC Vault följer dessa rikt linjer i standard implementeringen.

- Alla certifikat måste innehålla följande X. 509-fält, som anges nedan:
  - Innehållet i versions fältet måste vara v3. 
  - Innehållet i fältet serialNumber måste innehålla minst 8 byte av entropi som hämtats från en FIPS-baserad (Federal Information Processing standard) 140-godkänd slump nummer generator.<br>
    > [!IMPORTANT]
    > Serie numret för OPC-valvet är som standard 20 byte och hämtas från det kryptografiska slump tals generatorn för operativ systemet. Slump tals generatorn är FIPS 140 som godkänts på Windows-enheter, men inte i Linux. Tänk på detta när du väljer en tjänst distribution som använder virtuella Linux-datorer eller Linux Docker-behållare, där den underliggande teknikens OpenSSL inte är FIPS 140-godkänd.
  - Fälten issuerUniqueID och subjectUniqueID får inte förekomma.
  - Certifikat för slutanvändare måste identifieras med tillägget för grundläggande begränsningar, i enlighet med IETF RFC 5280.
  - PathLenConstraint-fältet måste anges till 0 för det utfärdande CA-certifikatet. 
  - Tillägget för utökad nyckel användning måste finnas och måste innehålla minst en uppsättning objekt identifierare för utökad nyckel användning (OID). AnyExtendedKeyUsage OID (2.5.29.37.0) får inte anges. 
  - CDP-tillägget (CRL Distribution Point) måste finnas i utfärdarens CA-certifikat.<br>
    > [!IMPORTANT]
    > CDP-tillägget finns i OPC Vault CA-certifikat. OPC UA-enheter använder dock anpassade metoder för att distribuera CRL: er.
  - Åtkomst tillägget för auktoritets information måste finnas i prenumerantens certifikat.<br>
    > [!IMPORTANT]
    > Åtkomst tillägget för auktoritets information finns i OPC-valv för prenumeranter. OPC UA-enheter använder dock anpassade metoder för att distribuera information om utfärdare av certifikat utfärdare.
- Godkända asymmetriska algoritmer, nyckel längder, hash-funktioner och utfyllnads lägen måste användas.
  - RSA och SHA-2 är de enda algoritmer som stöds.
  - RSA kan användas för kryptering, nyckel utbyte och signatur.
  - RSA-kryptering får endast använda OAEP-, RSA-KEM-eller RSA-PSS-utfyllnad.
  - Nyckel längder som är större än eller lika med 2048 bitar krävs.
  - Använd SHA-2-familjen för hash-algoritmer (SHA256, SHA384 och SHA512).
  - RSA-rotens CA-nycklar med en typisk livstid som är större än eller lika med 20 år måste vara 4096 bitar eller mer.
  - RSA Issuer-CA-nycklar måste vara minst 2048 bitar. Om certifikat UTFÄRDARens förfallo datum är efter 2030 måste CA-nyckeln vara 4096 bitar eller mer.
- Certifikatets livs längd
  - Certifikat för rot certifikat utfärdare: den maximala giltighets perioden för certifikat för rot certifikat utfärdare får inte överstiga 25 år.
  - Certifikat utfärdare eller certifikat från certifikat utfärdare för certifikat utfärdare: den maximala giltighets perioden för certifikat utfärdare som är online och endast utfärdar prenumerations certifikat får inte överstiga 6 år. För dessa certifikat utfärdare får inte den relaterade privata signatur nyckeln användas längre än tre år för att utfärda nya certifikat.<br>
    > [!IMPORTANT]
    > Utfärdarens certifikat, som det genereras i standard-mikrotjänsten (OPC Vault) utan extern rot certifikat utfärdare, behandlas som en online-underenhet, med respektive krav och livstid. Standard livs längden är inställd på 5 år, med en nyckel längd som är större än eller lika med 2048.
  - Alla asymmetriska nycklar måste ha en högsta 5-årig livs längd och en rekommenderad livs längd på 1 år.<br>
    > [!IMPORTANT]
    > Som standard har livs längden för program certifikat som utfärdas med OPC-valvet en livs längd på två år och bör ersättas varje år. 
  - När ett certifikat förnyas förnyas det med en ny nyckel.
- OPC UA-specifika tillägg i program instans certifikat
  - SubjectAltName-tillägget innehåller programmets URI och värdnamn. De kan även innehålla FQDN-, IPv4-och IPv6-adresser.
  - Användningen innehåller digitalSignature, oavvislig het, keyEncipherment och dataEncipherment.
  - ExtendedKeyUsage innehåller serverAuth och clientAuth.
  - AuthorityKeyIdentifier anges i signerade certifikat.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA-nycklar och certifikat måste uppfylla minimi kraven

- **Privata nycklar**: RSA-nycklar måste vara minst 2048 bitar. Om certifikat UTFÄRDARens förfallo datum är efter 2030 måste CA-nyckeln vara 4096 bitar eller mer.
- **Livs längd**: den maximala giltighets perioden för certifikatet för certifikat utfärdare som är online och endast utfärdar prenumerations certifikat får inte överstiga 6 år. För dessa certifikat utfärdare får inte den relaterade privata signatur nyckeln användas längre än tre år för att utfärda nya certifikat.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>CA-nycklar skyddas med hjälp av säkerhetsmoduler för maskin vara

OpcVault använder Azure Key Vault Premium och nycklar skyddas av FIPS 140-2 nivå 2. 

De kryptografiska moduler som Key Vault använder, om HSM eller program vara, är FIPS-validerade. Nycklar som skapas eller importeras som HSM-skyddade bearbetas i en HSM, verifieras till FIPS 140-2 nivå 2. Nycklar som skapas eller importeras som program varu skyddade bearbetas i kryptografiska moduler validerade till FIPS 140-2 nivå 1.

## <a name="operational-practices"></a>Operativa metoder

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Dokumentera och underhålla vanliga PKI-rutiner för certifikat registrering

Dokumentera och underhålla standard operativa procedurer (SOP) för hur CAs utfärdar certifikat, inklusive: 
- Hur prenumeranten identifieras och autentiseras. 
- Hur certifikatbegäran bearbetas och verifieras (om det är tillämpligt inkluderar även hur certifikat förnyelse och nyckel förnyelse begär Anden bearbetas). 
- Hur utfärdade certifikat distribueras till prenumeranterna. 

OPC Vault mikrotjänst-SOP beskrivs i [OPC Vault-arkitektur](overview-opc-vault-architecture.md) och [hanterar OPC-valvets certifikat tjänst](howto-opc-vault-manage.md). Praxis följer "OPC Unified Architecture Specification del 12: identifiering och globala tjänster".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Dokumentera och underhålla standardiserade PKI-metoder för certifikat åter kallelse

Återkallnings processen för certifikat beskrivs i [OPC Vault arkitektur](overview-opc-vault-architecture.md) och [hanterar OPC-valvets certifikat tjänst](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Dokumentera CA-nyckelns generations ceremoni 

Genereringen av utfärdarens CA-nyckel i OPC Vault-mikrotjänsten är förenklad, på grund av den säkra lagringen i Azure Key Vault. Mer information finns i [Hantera certifikat tjänsten OPC Vault](howto-opc-vault-manage.md).

Men när du använder en extern rot certifikat utfärdare måste en CA-nyckel skapa ceremonin uppfylla följande krav.

CA-nyckeln för att skapa ceremonin måste utföras mot ett dokumenterat skript som innehåller minst följande objekt: 
- Definition av roller och deltagar ansvar.
- Godkännande för att genomföra CA-nyckelns generations ceremoni.
- Kryptografisk maskin vara och aktiverings material som krävs för ceremonin.
- Maskin varu förberedelse (inklusive uppdatering av till gångs-/konfigurations information och utloggning).
- Installation av operativ system.
- Vissa steg som utförs under CA-nyckelns generations ceremoni, till exempel: 
  - Installation och konfiguration av CA-program.
  - Generering av CA-nyckel.
  - Säkerhets kopiering av CA-nyckel.
  - Signering av CA-certifikat.
  - Import av signerade nycklar i den skyddade HSM för tjänsten.
  - Avstängning av CA-systemet.
  - Beredning av material för lagring.


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar OPC-valvet på ett säkert sätt kan du:

> [!div class="nextstepaction"]
> [Skydda OPC UA-enheter med OPC-valvet](howto-opc-vault-secure.md)