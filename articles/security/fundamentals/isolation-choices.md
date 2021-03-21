---
title: Isolering i det offentliga Azure-molnet | Microsoft Docs
description: Lär dig hur Azure ger isolering för både skadliga och icke-skadliga användare och erbjuder olika isolerings alternativ till arkitekter.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c06fb0830ae709918b668ed60efbaaf47a63ce84
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94842846"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolering i det offentliga Azure-molnet

Med Azure kan du köra program och virtuella datorer (VM) på en delad fysisk infrastruktur. Ett av de viktigaste ekonomiska motivationen att köra program i en moln miljö är möjligheten att distribuera kostnaden för delade resurser mellan flera kunder. Den här övningen av flera innehavare förbättrar effektiviteten genom att Multiplexing-resurser delas mellan olika kunder med låga kostnader. Tyvärr introduceras risken för att dela fysiska servrar och andra infrastruktur resurser för att köra känsliga program och virtuella datorer som tillhör en godtycklig och potentiellt skadlig användare.

Den här artikeln beskriver hur Azure tillhandahåller isolering för både skadliga och icke-skadliga användare och fungerar som en vägledning för att skapa moln lösningar genom att erbjuda olika isolerings alternativ till arkitekter.

## <a name="tenant-level-isolation"></a>Isolering av klient nivå

En av de främsta fördelarna med molnbaserad data behandling är begreppet en delad, gemensam infrastruktur över flera kunder samtidigt, vilket leder till stor drifts skala. Det här konceptet kallas för flera innehavare. Microsoft arbetar kontinuerligt för att säkerställa att arkitekturen för flera klienter i Microsoft Cloud Azure stöder säkerhet, sekretess, sekretess, integritet och tillgänglighets standarder.

På en arbetsplats i molnet kan en klientorganisation definieras som en klient eller organisation som äger och hanterar en specifik instans av molntjänsten. Med den identitets plattform som tillhandahålls av Microsoft Azure är en klient helt enkelt en dedikerad instans av Azure Active Directory (Azure AD) som din organisation tar emot och äger när den registrerar sig för en moln tjänst från Microsoft.

Varje Azure AD-katalog är separat och åtskild från andra Azure AD-kataloger. Precis som ett företags kontorsbyggnad är en säker resurs som är specifik för din organisation har även Azure AD-katalogen utformats för att vara en säker tillgång för exklusiv användning av din organisation. Azure AD-arkitekturen håller isär kunddata och identitetsinformation. Det innebär att användare och administratörer av en Azure AD-katalog inte oavsiktligt eller illvilligt kan komma åt data i en annan katalog.

### <a name="azure-tenancy"></a>Azure-innehavare

Azure-innehavaren (Azure-prenumeration) refererar till en kund/fakturerings relation och en unik [klient](../../active-directory/develop/quickstart-create-new-tenant.md) i [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Isolering av klient nivå i Microsoft Azure uppnås med Azure Active Directory och [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/overview.md) som erbjuds av den. Varje Azure-prenumeration är associerad med en Azure Active Directory-katalog (AD).

Användare, grupper och program från den katalogen kan hantera resurser i Azure-prenumerationen. Du kan tilldela dessa behörigheter med hjälp av Azure Portal, Azures kommando rads verktyg och Azure Management-API: er. En Azure AD-klient isoleras logiskt med hjälp av säkerhets gränser så att ingen kund kan komma åt eller kompromettera samklienter, antingen på ett skadligt eller av misstag. Azure AD körs på "bare metal"-servrar som är isolerade i ett åtskiljt nätverks segment, där paket filtrering på värdnivå och Windows-brandväggen blockerar oönskade anslutningar och trafik.

- Åtkomst till data i Azure AD kräver användarautentisering via en säkerhetstokentjänst. Information om användarens existens, aktiverade tillstånd och roll används av auktoriserings systemet för att avgöra om den begärda åtkomsten till mål klienten har behörighet för den här användaren i den här sessionen.

![Azure-innehavare](./media/isolation-choices/azure-isolation-fig1.png)

- Klienter är diskreta behållare och det finns ingen relation mellan dessa.

- Ingen åtkomst över klient organisationer om inte klient organisations administratören beviljar det via Federation eller etablering av användar konton från andra klienter.

- Fysisk åtkomst till servrar som utgör Azure AD-tjänsten och direkt åtkomst till Azure ADs backend-system är begränsad.

- Azure AD-användare har ingen åtkomst till fysiska till gångar eller platser och det är därför inte möjligt för dem att kringgå de logiska principerna för den logiska Azure RBAC-kontrollen som anges nedan.

För diagnostik-och underhålls behov krävs och används en drifts modell som använder sig av en just-in-Time-höjning. Azure AD Privileged Identity Management (PIM) introducerar begreppet en berättigad administratör. [berättigade administratörer](../../active-directory/privileged-identity-management/pim-configure.md) bör vara användare som behöver privilegie rad åtkomst nu och sedan, men inte varje dag. Rollen är inaktiv tills användaren behöver åtkomst. Därefter slutför användaren en aktiveringsprocess och blir aktiv administratör under en förinställd tidsperiod.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory värdar för varje klient i sin egen skyddade behållare, med principer och behörigheter till och inom behållaren som ägs och hanteras av klienten.

Begreppet klient behållare är djupt inkornigt i katalog tjänsten på alla lager, från portaler till ett beständigt lagrings utrymme.

Även om metadata från flera Azure Active Directory klienter lagras på samma fysiska disk, finns det ingen relation mellan andra behållare än vad som definieras av katalog tjänsten, vilket i sin tur styrs av klient organisations administratören.

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (rollbaserad åtkomstkontroll)

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) hjälper dig att dela olika komponenter som är tillgängliga i en Azure-prenumeration genom att tillhandahålla detaljerad åtkomst hantering för Azure. Med Azure RBAC kan du åtskilja uppgifter i organisationen och bevilja åtkomst baserat på vad användarna behöver för att utföra sina jobb. I stället för att ge alla obegränsade behörigheter i Azure-prenumeration eller-resurser kan du bara tillåta vissa åtgärder.

Azure RBAC har tre grundläggande roller som gäller för alla resurs typer:

- **Ägaren** har fullständig åtkomst till alla resurser, inklusive rätten att delegera åtkomst till andra.

- **Deltagare** kan skapa och hantera alla typer av Azure-resurser, men kan inte bevilja åtkomst till andra.

- **Läsaren** kan visa befintliga Azure-resurser.

![Azure RBAC (rollbaserad åtkomstkontroll)](./media/isolation-choices/azure-isolation-fig3.png)

Resten av Azure-rollerna i Azure möjliggör hantering av vissa Azure-resurser. Till exempel tillåter rollen Virtuell datordeltagare att en användare skapar och hanterar virtuella datorer. Den ger dem inte åtkomst till Azure-Virtual Network eller det undernät som den virtuella datorn ansluter till.

De [inbyggda Azure-rollerna](../../role-based-access-control/built-in-roles.md) visar de roller som är tillgängliga i Azure. Den anger de åtgärder och den omfattning som varje inbyggd roll ger användare. Om du vill definiera egna roller för ännu mer kontroll, se så här skapar du [anpassade roller i Azure RBAC](../../role-based-access-control/custom-roles.md).

Några andra funktioner för Azure Active Directory inkluderar:

- Azure AD aktiverar SSO för SaaS-program, oavsett var de finns. Vissa program federeras med Azure AD och andra använder enkel inloggning med lösenord. Federerade program kan också ha stöd för användar etablering och [lösen ords valv](https://www.techopedia.com/definition/31415/password-vault).

- Åtkomst till data i [Azure Storage](https://azure.microsoft.com/services/storage/) styrs via autentisering. Varje lagrings konto har en primär nyckel ([lagrings konto nyckel](../../storage/common/storage-account-create.md)eller sak) och en sekundär hemlig nyckel (signatur för delad åtkomst eller SAS).

- Azure AD tillhandahåller identitet som en tjänst via Federation med hjälp av [Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs), synkronisering och replikering med lokala kataloger.

- [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) är Multi-Factor Authentication-tjänsten som kräver att användare verifierar inloggningar med hjälp av en mobilapp, ett telefonsamtal eller ett textmeddelande. Den kan användas med Azure AD för att skydda lokala resurser med Azure Multi-Factor Authentication-servern och även med anpassade program och kataloger med hjälp av SDK.

- Med [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kan du ansluta virtuella Azure-datorer till en Active Directory domän utan att distribuera domänkontrollanter. Du kan logga in på dessa virtuella datorer med företagets Active Directory autentiseringsuppgifter och administrera domänanslutna virtuella datorer genom att använda grupprincip för att tvinga säkerhets bas linjer på alla virtuella Azure-datorer.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) tillhandahåller en hög tillgänglig tjänst för hantering av globala identiteter för klient program som kan skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Dina kunder kan logga in på alla dina program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa autentiseringsuppgifter.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolering från Microsoft-administratörer & data borttagning

Microsoft tar starka åtgärder för att skydda dina data mot olämplig åtkomst eller användning av obehöriga personer. Dessa operativa processer och kontroller backas upp av [Online Services-villkoren](https://aka.ms/Online-Services-Terms), som erbjuder avtals enliga åtaganden som styr åtkomsten till dina data.

- Microsoft-tekniker har inte standard åtkomst till dina data i molnet. De beviljas i stället åtkomst, under hanterings övervakning, endast när det behövs. Åtkomsten kontrol leras och loggas noga och återkallas när den inte längre behövs.
- Microsoft kan anlita andra företag för att tillhandahålla begränsade tjänster för dess räkning. Underleverantörer kan komma åt kund information endast för att leverera de tjänster som vi har anlitat dem för att tillhandahålla, och de är förbjudna att använda den i något annat syfte. Dessutom är de avtals enliga för att bibehålla sekretessen hos våra kunders information.

Företags tjänster med granskade certifieringar som ISO/IEC 27001 kontrol leras regelbundet av Microsoft och ackrediterade gransknings företag, som utför exempel granskningar för att intyga att åtkomst sker, endast för legitima affärs skäl. Du kan alltid komma åt dina egna Kunddata när som helst och av vilken anledning som helst.

Om du tar bort data tar Microsoft Azure bort data, inklusive cachelagrade eller säkerhets kopior. För tjänster i omfång sker borttagningen inom 90 dagar efter att kvarhållningsperioden är slut. (I-scope-tjänster definieras i avsnittet data bearbetnings villkor i våra [Online Services-villkor](https://aka.ms/Online-Services-Terms).)

Om en disk enhet som används för lagring drabbas av ett maskin varu haveri raderas den på ett säkert sätt [eller förstörs](https://microsoft.com/trustcenter/privacy/you-own-your-data) innan Microsoft returnerar den till tillverkaren för utbyte eller reparation. Data på enheten skrivs över för att säkerställa att data inte kan återställas på något sätt.

## <a name="compute-isolation"></a>Beräknings isolering

Microsoft Azure tillhandahåller olika molnbaserade data behandlings tjänster som innehåller ett brett urval av beräknings instanser & tjänster som kan skalas upp och ned automatiskt för att möta behoven hos ditt program eller företag. Dessa beräknings instanser och tjänst erbjudande isoleras på flera nivåer för att skydda data utan att offra flexibiliteten i konfigurationen som kunderna kräver.

### <a name="isolated-virtual-machine-sizes"></a>Isolerade storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Dedikerade värdar

Förutom de isolerade värdar som beskrivs i föregående avsnitt, erbjuder Azure även dedikerade värdar. Dedikerade värdar i Azure är en tjänst som tillhandahåller fysiska servrar som kan vara värdar för en eller flera virtuella datorer och som är dedikerade till en enda Azure-prenumeration. Dedikerade värdar tillhandahåller maskin varu isolering på den fysiska server nivån. Inga andra virtuella datorer kommer att placeras på värdarna. Dedikerade värdar distribueras i samma data Center och delar samma nätverk och underliggande lagrings infrastruktur som andra icke-isolerade värdar. Mer information finns i den detaljerade översikten över [Azure-dedikerade värdar](../../virtual-machines/dedicated-hosts.md).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V & rot operativ system isolering mellan virtuella rot-VM & virtuella gäst datorer

Azures beräknings plattform baseras på VM-virtualisering, vilket innebär att all kund kod körs i en virtuell Hyper-V-dator. På varje Azure-nod (eller nätverks slut punkt) finns en hypervisor som körs direkt över maskin varan och delar upp en nod i ett variabel antal gäst Virtual Machines (VM).

![Hyper-V & rot operativ system isolering mellan virtuella rot-VM & virtuella gäst datorer](./media/isolation-choices/azure-isolation-fig4.jpg)

Varje nod har också en speciell virtuell rot-VM, som kör värd operativ systemet. En kritisk avgränsning är isoleringen av den virtuella rot datorn från de virtuella gäst datorerna och de virtuella gäst datorerna från varandra, som hanteras av hypervisorn och rot operativ systemet. Hypervisor-/root OS-ihopparningen utnyttjar Microsofts årtionden för operativ systemets säkerhets upplevelse och senare inlärning från Microsofts Hyper-V för att tillhandahålla en stark isolering av virtuella gäst datorer.

Azure-plattformen använder en virtualiserad miljö. Användar instanser fungerar som fristående virtuella datorer som inte har åtkomst till en fysisk värd Server.

Azure-hypervisorn fungerar som en mikrokernel och skickar alla förfrågningar om maskin varu åtkomst från virtuella gäst datorer till värden för bearbetning med hjälp av ett delat minnes gränssnitt som kallas VMBus. Detta förhindrar att användare erhåller rååtkomsten läs/skriv/kör till systemet och minskar risken med att dela systemresurser.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Avancerad placering av placering av virtuella datorer & skydd från kanal attacker på sidan

Alla angrepp mellan virtuella datorer omfattar två steg: att placera en angripare virtuell dator på samma värd som en av de skadelidande virtuella datorerna och sedan bryta isolerings gränsen för att stjäla känslig information om skadelidande eller påverka dess prestanda för Greed eller vandalism. Microsoft Azure ger skydd i båda stegen genom att använda en avancerad algoritm för VM-placering och skydd från alla kända kanal attacker, inklusive störningar av intilliggande virtuella datorer.

### <a name="the-azure-fabric-controller"></a>Azure Fabric-styrenheten

Azure Fabric Controller ansvarar för att allokera infrastruktur resurser till klient arbets belastningar och hanterar enkelriktad kommunikation från värden till virtuella datorer. Den virtuella datorns placerings algoritm för Azure Fabric Controller är mycket sofistikerad och nästan omöjlig att förutsäga som fysisk värd nivå.

![Azure Fabric-styrenheten](./media/isolation-choices/azure-isolation-fig5.png)

Azure-hypervisorn använder minnes-och process separationer mellan virtuella datorer och dirigerar nätverks trafiken säkert till gäst operativ systemets klienter. Detta eliminerar risken för angrepp på och sidans kanal på VM-nivå.

I Azure är den virtuella rot datorn speciell: den kör ett härdat operativ system som kallas för det rot-OS som är värd för en infrastruktur agent (FA). FAs används i sin tur för att hantera gäst agenter (GA) i gäst operativ system på kundens virtuella datorer. I FAs hanterar du även lagringsnoder.

Samlingen av Azure-hypervisorer, rot-OS/FA och kundens virtuella datorer/GAs omfattar en Compute-nod. FAs hanteras av en Fabric Controller (FC) som finns utanför beräknings-och lagringsnoder (beräknings-och lagrings kluster hanteras av separata FCs). Om en kund uppdaterar programmets konfigurations fil medan den körs kommunicerar FC med FA, som sedan kontaktar gasen, som meddelar programmet om konfigurations ändringen. Om ett maskin varu problem uppstår hittar FC automatiskt tillgänglig maskin vara och startar om den virtuella datorn där.

![Azure Fabric-styrenhet](./media/isolation-choices/azure-isolation-fig6.jpg)

Kommunikation från en infrastruktur styrenhet till en agent är enkelriktad. Agenten implementerar en SSL-skyddad tjänst som endast svarar på begär Anden från kontrollanten. Det går inte att initiera anslutningar till kontrollanten eller andra privilegierade interna noder. FC behandlar alla svar som om de var icke-betrodda.

![Infrastruktur styrenhet](./media/isolation-choices/azure-isolation-fig7.png)

Isoleringen sträcker sig från den virtuella rot datorn från virtuella gäst datorer och de virtuella gäst datorerna från varandra. Compute-noder isoleras också från lagringsnoder för ökat skydd.

Hypervisorn och värd operativ systemet tillhandahåller nätverks paket-filter för att säkerställa att obetrodda virtuella datorer inte kan generera förfalsknings trafik eller ta emot trafik som inte är adresserad till dem, dirigera trafik till skyddade infrastruktur slut punkter eller skicka/ta emot olämplig sändnings trafik.

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Ytterligare regler som kon figurer ATS av Fabric Controller agent för att isolera virtuell dator

All trafik blockeras som standard när en virtuell dator skapas och sedan konfigurerar Fabric Controller-agenten paket filtret för att lägga till regler och undantag för att tillåta auktoriserad trafik.

Det finns två typer av regler som är programmerade:

- **Dator konfiguration eller infrastrukturs regler:** All kommunikation blockeras som standard. Det finns undantag för att tillåta att en virtuell dator skickar och tar emot DHCP-och DNS-trafik. Virtuella datorer kan också skicka trafik till "offentlig" Internet och skicka trafik till andra virtuella datorer i samma Azure-Virtual Network och aktiverings servern för operativ systemet. Listan med virtuella datorer med tillåtna utgående destinationer omfattar inte Azure router-undernät, Azure-hantering och andra Microsoft-egenskaper.
- **Roll konfigurations fil:** Detta definierar de inkommande Access Control listorna (ACL) som baseras på klient organisationens tjänste modell.

### <a name="vlan-isolation"></a>VLAN-isolering

Det finns tre VLAN i varje kluster:

![VLAN-isolering](./media/isolation-choices/azure-isolation-fig8.jpg)

- Huvud-VLAN – kopplar från ej betrodda kundnoder
- FC VLAN – innehåller betrodda FCs-och support system
- Enhetens VLAN – innehåller betrodda nätverk och andra infrastruktur enheter

Kommunikation tillåts från FC VLAN till huvud-VLAN, men det går inte att initiera från huvud-VLAN till FC VLAN. Kommunikation blockeras också från huvud-VLAN till enhets-VLAN. Detta säkerställer att även om en nod som kör kund kod komprometteras, kan den inte attackera noder på antingen FC-eller Device-VLAN.

## <a name="storage-isolation"></a>Lagrings isolering

### <a name="logical-isolation-between-compute-and-storage"></a>Logisk isolering mellan beräkning och lagring

Som en del av den grundläggande designen Microsoft Azure separerar VM-baserad beräkning från Storage. Den här separationen gör att beräkning och lagring kan skalas oberoende av varandra, vilket gör det enklare att tillhandahålla flera innehavare och isolering.

Därför körs Azure Storage på separat maskin vara utan nätverks anslutning till Azure Compute, förutom logiskt. Det innebär att när en virtuell disk skapas, allokeras inte disk utrymme för hela kapaciteten. I stället skapas en tabell som mappar adresser på den virtuella disken till områden på den fysiska disken och tabellen är tom från början. **Första gången en kund skriver data på den virtuella disken allokeras utrymmet på den fysiska disken och en pekare till den placeras i tabellen.**

### <a name="isolation-using-storage-access-control"></a>Isolering med åtkomst kontroll för lagring

**Access Control i Azure Storage** har en enkel åtkomst kontroll modell. Varje Azure-prenumeration kan skapa ett eller flera lagrings konton. Varje lagrings konto har en enda hemlig nyckel som används för att kontrol lera åtkomsten till alla data i det lagrings kontot.

![Isolering med åtkomst kontroll för lagring](./media/isolation-choices/azure-isolation-fig9.png)

**Åtkomst till Azure Storage data (inklusive tabeller)** kan styras via en SAS-token [(signatur för delad åtkomst)](../../storage/common/storage-sas-overview.md) som ger begränsad åtkomst. SAS skapas via en frågegrupp (URL) som är signerad med [sak (lagrings konto nyckel)](/previous-versions/azure/reference/ee460785(v=azure.100)). Den [signerade URL:](../../storage/common/storage-sas-overview.md) en kan ges till en annan process (det vill säga delegerat), som sedan kan fylla i information om frågan och göra en begäran om lagrings tjänsten. Med SAS kan du ge tidsbaserad åtkomst till klienter utan att avslöja lagrings kontots hemliga nyckel.

SAS innebär att vi kan bevilja en klient begränsad behörighet, till objekt i vårt lagrings konto under en angiven tids period och med en angiven uppsättning behörigheter. Vi kan ge dessa begränsade behörigheter utan att behöva dela dina konto åtkomst nycklar.

### <a name="ip-level-storage-isolation"></a>Lagrings isolering på IP-nivå

Du kan upprätta brand väggar och definiera ett IP-adressintervall för dina betrodda klienter. Med ett IP-adressintervall kan endast klienter som har en IP-adress inom det definierade intervallet ansluta till [Azure Storage](../../storage/blobs/security-recommendations.md).

IP Storage-data kan skyddas från obehöriga användare via en nätverks mekanism som används för att allokera en dedikerad eller dedikerad trafik trafik till IP-lagring.

### <a name="encryption"></a>Kryptering

Azure erbjuder följande typer av kryptering för att skydda data:

- Kryptering under överföring
- Kryptering i vila

#### <a name="encryption-in-transit"></a>Kryptering under överföring

Kryptering under överföring är en mekanism för att skydda data när de överförs mellan nätverk. Med Azure Storage kan du skydda data med:

- [Kryptering på transport nivå](../../storage/blobs/security-recommendations.md), till exempel https när du överför data till eller från Azure Storage.
- [Kabel kryptering](../../storage/blobs/security-recommendations.md), till exempel SMB 3,0-kryptering för Azure-filresurser.
- [Kryptering på klient sidan](../../storage/blobs/security-recommendations.md), för att kryptera data innan de överförs till lagrings utrymmet och för att dekryptera data när de har överförts från lagrings utrymmet.

#### <a name="encryption-at-rest"></a>Kryptering i vila

För många organisationer är [data kryptering i vila](isolation-choices.md) ett obligatoriskt steg mot data sekretess, efterlevnad och data suveränitet. Det finns tre Azure-funktioner som ger kryptering av data som är "i vila":

- Med [kryptering för lagringstjänst](../../storage/blobs/security-recommendations.md) kan du begära att lagrings tjänsten automatiskt krypterar data när de skrivs till Azure Storage.
- [Kryptering på klient sidan](../../storage/blobs/security-recommendations.md) tillhandahåller även funktionen för kryptering i vila.
- Med [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) kan du kryptera de OS-diskar och data diskar som används av en virtuell IaaS-dator.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) för virtuella datorer (VM) hjälper dig att lösa organisationens krav på säkerhet och efterlevnad genom att kryptera dina virtuella dator diskar (inklusive start-och data diskar) med nycklar och principer som du styr i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Disk krypterings lösningen för Windows är baserad på [Microsoft BitLocker-diskkryptering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11))och Linux-lösningen baseras på [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Lösningen har stöd för följande scenarier för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:

- Integrering med Azure Key Vault
- Virtuella datorer på standard nivå: A, D, DS, G, GS och så vidare, serien IaaS VM: ar
- Aktivera kryptering för virtuella Windows-och Linux IaaS-datorer
- Inaktivera kryptering på operativ system och data enheter för virtuella Windows IaaS-datorer
- Inaktivera kryptering på data enheter för virtuella Linux IaaS-datorer
- Aktivera kryptering på virtuella IaaS-datorer som kör Windows Client OS
- Aktivera kryptering på volymer med monterings Sök vägar
- Aktivera kryptering på virtuella Linux-datorer som är konfigurerade med disk striping (RAID) med hjälp av [mdadm](https://en.wikipedia.org/wiki/Mdadm)
- Aktivera kryptering på virtuella Linux-datorer med hjälp av [LVM (Logical Volume Manager)](/windows/win32/fileio/about-volume-management) för data diskar
- Aktivera kryptering på virtuella Windows-datorer som har kon figurer ATS med hjälp av lagrings utrymmen
- Alla offentliga Azure-regioner stöds

Lösningen har inte stöd för följande scenarier, funktioner och teknik i versionen:

- Basic-nivå IaaS VM
- Inaktivera kryptering på en OS-enhet för virtuella Linux IaaS-datorer
- Virtuella IaaS-datorer som skapas med hjälp av den klassiska skapande metoden för virtuella datorer
- Integrering med din lokala nyckel hanterings tjänst
- Azure Files (delat fil system), virtuella nätverks fil system (NFS), dynamiska volymer och virtuella Windows-datorer som är konfigurerade med programvarubaserade RAID-system

## <a name="sql-database-isolation"></a>SQL Database isolering

SQL Database är en relationsdatabastjänst i Microsoft Cloud som är baserad på den marknadsledande Microsoft SQL Server-motorn och som kan hantera verksamhetskritiska arbetsbelastningar. SQL Database erbjuder förutsägbar data isolering på konto nivå, geografi/region baserat på nätverk – allt med nästan noll administration.

### <a name="sql-database-application-model"></a>SQL Database program modell

[Microsoft SQL Database](../../azure-sql/database/single-database-create-quickstart.md) är en molnbaserad Relations databas tjänst som bygger på SQL Server tekniker. Det ger en skalbar databas tjänst med hög tillgänglighet som Microsoft i molnet har hög tillgänglighet.

I ett program perspektiv tillhandahåller SQL Database följande hierarki: varje nivå har en-till-många-inne slutning av nivåer nedan.

![SQL Database program modell](./media/isolation-choices/azure-isolation-fig10.png)

Kontot och prenumerationen är Microsoft Azure plattforms koncept för att koppla fakturering och hantering.

Logiska SQL-servrar och databaser är SQL Database-/regionsspecifika koncept och hanteras med hjälp av SQL Database, tillhandahöll OData-och TSQL-gränssnitt eller via Azure Portal.

Servrar i SQL Database är inte fysiska eller virtuella dator instanser, och de är i stället samlingar av databaser, delning av hanterings-och säkerhets principer, som lagras i så kallade "logisk Master"-databas.

![SQL Database](./media/isolation-choices/azure-isolation-fig11.png)

Logiska huvud databaser omfattar:

- SQL-inloggningar som används för att ansluta till servern
- Brandväggsregler

Information om fakturering och användning som är relaterad till databaser från samma server är inte garanterat på samma fysiska instans i klustret. i stället måste programmen ange mål databasens namn vid anslutning.

Från ett kund perspektiv skapas en server i en geo-grafisk region medan den faktiska skapandet av servern sker i ett av klustren i regionen.

### <a name="isolation-through-network-topology"></a>Isolering genom nätverkstopologi

När en server skapas och dess DNS-namn är registrerat pekar DNS-namnet på den så kallade "Gateway VIP"-adressen i det angivna data centret där servern placerades.

Vi har en samling med tillstånds lösa gateway-tjänster bakom VIP (virtuell IP-adress). I allmänhet är gatewayer inblandade när det krävs en samordning mellan flera data källor (Master-databasen, användar databasen osv.). Gateway-tjänster implementerar följande:

- **Proxy för TDS-anslutning.** Detta omfattar att hitta användar databasen i Server dels klustret, implementera inloggningssekvensen och sedan vidarebefordra TDS-paketen till Server delen och tillbaka.
- **Databas hantering.** Detta inkluderar implementering av en samling arbets flöden för att skapa/ändra/släppa databas åtgärder. Databas åtgärderna kan anropas genom att antingen initiera TDS-paket eller explicita OData-API: er.
- Åtgärder för att skapa/ändra/släppa inloggning/användare
- Server hanterings åtgärder via OData API

![Isolering genom nätverkstopologi](./media/isolation-choices/azure-isolation-fig12.png)

Nivån bakom gatewayerna kallas "backend". Det är här som alla data lagras på ett mycket tillgängligt sätt. Varje data del sägs tillhöra en "partition" eller "redundansrelation", där var och en av dem har minst tre repliker. Repliker lagras och replikeras av SQL Server motor och hanteras av ett failover-system kallas ofta för "infrastruktur resurser".

I allmänhet kommunicerar inte Server dels systemet utgående till andra system som en säkerhets åtgärd. Detta är reserverat för systemen i front-end-nivån (Gateway). Datorerna på Gateway-nivå har begränsade behörigheter på backend-datorerna för att minimera angrepps ytan som en skydds intensiv mekanism.

### <a name="isolation-by-machine-function-and-access"></a>Isolering efter dator funktion och åtkomst

SQL Database (består av tjänster som körs på olika dator funktioner. SQL Database är indelat i moln databasen "backend" och "front-end" (Gateway/hantering), med den allmänna principen för trafik som bara kommer in på Server sidan och inte ut. Klient delen kan kommunicera med andra tjänster utanför världen och har i allmänhet endast begränsade behörigheter i Server delen (tillräckligt för att anropa de start punkter som den behöver anropa).

## <a name="networking-isolation"></a>Nätverks isolering

Azure-distributionen har flera lager av nätverks isolering. Följande diagram visar olika lager av nätverks isolering som Azure ger kunderna. Dessa lager är både inbyggda i själva Azure-plattformen och kunddefinierade funktioner. Azure DDoS har en inkommande trafik från Internet och ger en isolering mot storskaliga attacker mot Azure. Nästa isolerings lager är kunddefinierade offentliga IP-adresser (slut punkter) som används för att avgöra vilken trafik som kan passera moln tjänsten till det virtuella nätverket. Inbyggd Azure Virtual Network-isolering säkerställer fullständig isolering från alla andra nätverk och att trafiken bara flödar genom användar konfigurerade sökvägar och metoder. Dessa sökvägar och metoder är nästa lager, där NSG: er, UDR och virtuella nätverks enheter kan användas för att skapa isolerings gränser för att skydda program distributionerna i det skyddade nätverket.

![Nätverks isolering](./media/isolation-choices/azure-isolation-fig13.png)

**Trafik isolering:** Ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) är den trafik isolerings gränser som finns på Azure-plattformen. Virtual Machines (VM) i ett virtuellt nätverk kan inte kommunicera direkt med virtuella datorer i ett annat virtuellt nätverk, även om båda virtuella nätverken har skapats av samma kund. Isolering är en kritisk egenskap som garanterar att kunders virtuella datorer och kommunikation förblir privata i ett virtuellt nätverk.

[Undernät](../../virtual-network/virtual-networks-overview.md) erbjuder ett extra isolerings lager med i virtuellt nätverk baserat på IP-intervall. IP-adresser i det virtuella nätverket kan du dela upp ett virtuellt nätverk i flera undernät för organisation och säkerhet. VM:ar och PaaS-rollinstanser som distribuerats till undernät (samma eller olika) inom ett VNet, kan kommunicera med varandra utan övrig konfiguration. Du kan också konfigurera [nätverks säkerhets gruppen (NSG: er)](../../virtual-network/virtual-networks-overview.md) för att tillåta eller neka nätverks trafik till en virtuell dator instans baserat på regler som kon figurer ATS i åtkomst kontrol listan (ACL) för NSG. NSG:er kan associeras med antingen undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät tillämpas ACL-reglerna på alla VM-instanser i det undernätet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [alternativ för nätverks isolering för datorer i virtuella Windows Azure-nätverk](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Detta omfattar det klassiska klient-och Server dels scenariot där datorer i ett visst Server dels nätverk eller under nätverk bara kan tillåta att vissa klienter eller andra datorer ansluter till en viss slut punkt baserat på en lista över tillåtna IP-adresser.

- Lär dig mer om [isolering av virtuella datorer i Azure](../../virtual-machines/isolation.md). Azure Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskin varu typ och som är dedikerad till en enda kund.