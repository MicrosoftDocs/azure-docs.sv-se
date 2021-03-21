---
title: Förbättra säkerheten vid fjärrhantering i Azure | Microsoft Docs
description: Den här artikeln beskriver steg för att förbättra säkerheten för fjärrhantering när du administrerar Microsoft Azure miljöer, inklusive moln tjänster, virtuella datorer och anpassade program.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2020
ms.author: terrylan
ms.openlocfilehash: e9eabc73c244526f0ea15b9c72b5377545f662b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94844871"
---
# <a name="security-management-in-azure"></a>Säkerhetshantering i Azure
Azure-prenumeranter kan hantera sina molnmiljöer från flera enheter, inklusive hantering av arbetsstationer, utvecklardatorer och även privilegierade slutanvändarens enheter som har uppgiftsspecifika behörigheter. I vissa fall kan administrativa funktioner utförs via webbaserade konsoler som [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). I andra fall kan det finnas direkta anslutningar till Azure från lokala system över virtuella privata nätverk (VPN), Terminal Services, klientprotokoll för program eller (programmässigt) Azure Service Management API (SMAPI). Dessutom kan klientslutpunkter vara antingen domänanslutna eller isolerade och ohanterade, till exempel surfplattor eller smartphones.

Även om de många funktionerna för åtkomst och hantering tillhandahåller en omfattande uppsättning alternativ, kan den här variationen medföra betydande risker för en molndistribution. Det kan vara svårt att hantera, spåra och granska administrativa åtgärder. Den här variationen kan också innebära säkerhetshot via oreglerad åtkomst till klientslutpunkter som används för att hantera molntjänster. Med hjälp av allmänna eller personliga arbetsstationer för att utveckla och hantera infrastrukturen öppnas oförutsägbara hotvektorer, till exempel webbsurfning (t.ex. vattenhålsattacker) eller mejl (t.ex. social manipulation och nätfiskewebbplatser).

![Ett diagram som visar olika sätt som ett hot kan montera ett angrepp på.](./media/management/typical-management-network-topology.png)

Risken för angrepp ökar i den här typen av miljöer. Det beror på att det är svårt att konstruera säkerhetsprinciper och mekanismer för att hantera åtkomst till Azure-gränssnitt på korrekt sätt (till exempel SMAPI) från många olika slutpunkter.

### <a name="remote-management-threats"></a>Hot med fjärrhantering
Angripare försöka ofta få privilegierad åtkomst genom att kompromettera autentiseringsuppgifter (till exempel med råstyrkeattacker för lösenord, nätfiske och insamling av autentiseringsuppgifter). De kan också försöka lura användare till att köra skadlig kod (till exempel från skadliga webbplatser med hämtningar eller från skadliga mejlbilagor). I en fjärradministrerade molnmiljö kan kontoöverträdelser leda till ökad risk på grund av att användarna har åtkomst var som helst, när som helst.

Även om de primära administratörskontona innehåller strikta kontroller kan användarkonton på lägre nivå användas för att utnyttja de svaga sidorna i säkerhetsstrategin. Brist på lämplig säkerhetsutbildning kan också leda till överträdelser genom att kontoinformation avslöjas eller exponeras oavsiktligt.

När en användares arbetsstation också används för administrativa uppgifter, kan den vara hotad på många olika punkter. Det kan vara att en användare surfar på Internet, använder ett verktyg från en annan utvecklare eller med öppen källkod eller att man öppnar en skadlig dokumentfil som innehåller en trojan.

Vanligtvis kan de flesta riktade attacker som leder till dataöverträdelser spåras till webbläsartrojaner, plugin-program (till exempel Flash, PDF, Java) och nätfiske (e-post) på stationära datorer. När dessa datorer används för utveckling och hantering av andra tillgångar kan de ha behörigheter på administrativ nivå eller tjänstnivå för att komma åt aktiva servrar eller nätverksenheter för åtgärder.

### <a name="operational-security-fundamentals"></a>Grunderna om operativ säkerhet
För säker hantering och säkrare åtgärder kan du minimera risken för ett klientangrepp genom att minska antalet möjliga startpunkter. Det kan göras med hjälp av säkerhetsprinciper: ”uppdelning av uppgifter” och ”ansvarsfördelning av miljöer”.

Isolera känsliga funktioner från varandra för att minska risken för att ett fel på en nivå leder till en överträdelse på en annan. Exempel:

* Administrativa uppgifter bör inte kombineras med aktiviteter som kan leda till kompromettering (till exempel skadlig kod i en administratörs mejl som sedan infekterar en infrastrukturserver).
* En arbetsstation som används för åtgärder med hög känslighet bör inte vara samma dator som används för aktiviteter med hög risk, till exempel att surfa på Internet.

Minska risken för systemangrepp genom att ta bort onödiga program. Exempel:

* Standarddatorn för administration, support eller utveckling behöver inte ha en e-postklient eller andra produktivitetsprogram installerade om huvudsyftet är att hantera molntjänster.

Klientdatorer som har administratörsåtkomst till infrastrukturkomponenter bör ha så strikta principer som möjligt för att minska säkerhetsrisker. Exempel:

* Säkerhetsprinciperna kan innehålla grupprincipinställningar som nekar öppen Internet-åtkomst från enheten och användning av en restriktiv brandväggskonfiguration.
* Använd IPsec (Internet Protocol security) VPN om direktåtkomst behövs.
* Konfigurera separata Active Directory-domäner för hantering och utveckling.
* Isolera och filtrera nätverkstrafiken på hanteringsdatorn.
* Använd program mot skadlig kod.
* Minska risken för stulna autentiseringsuppgifter genom att implementera multifaktorautentisering.

Du kan också förenkla hanteringsuppgifter genom att konsolidera och ta bort ohanterade slutpunkter.

### <a name="providing-security-for-azure-remote-management"></a>Skapa säker Azure-fjärrhantering
Azure tillhandahåller säkerhetsmekanismer som hjälper administratörer som hanterar Azure-molntjänster och virtuella datorer. Dessa mekanismer är:

* Autentisering och [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Övervakning, loggning och granskning.
* Certifikat och krypterad kommunikation.
* En webbhanteringsportal.
* Filtrering av nätverkspaket.

Med säkerhetskonfiguration på klientsidan och datacenterdistribution av en hanterings-gateway går det att begränsa och övervaka administratörsåtkomsten till molnbaserade program och data.

> [!NOTE]
> Vissa rekommendationerna i den här artikeln kan leda till ökad användning av data, nätverk eller beräkningsresurser och öka kostnaderna för din licens eller prenumeration.
>
>

## <a name="hardened-workstation-for-management"></a>Förstärkt dator för hantering
Målet med att förstärka en dator är att ta bort alla utom de mest kritiska funktioner som krävs för att den ska fungera. Det minimerar risken för potentiella attacker så mycket som möjligt. Systemförstärkning handlar bland annat om att minimera antalet installerade produkter och program, begränsa programkörning, begränsa nätverksåtkomsten bara till vad som behövs och se till att datorn alltid är uppdaterad. Dessutom separeras administrativa verktyg och aktiviteter från andra slutanvändaraktiviteter när man arbetar med en förstärkt dator.

Inom en lokal företagsmiljö kan du begränsa risken för angrepp på den fysiska infrastrukturen via dedikerade hanteringsnätverk, serverrum med kortåtkomst och datorer  som körs på skyddade delar av nätverket. I en moln- eller hybridbaserad IT-modell kan et vara mer komplicerat med säker hantering eftersom det saknas fysisk åtkomst till IT-resurserna. Det krävs noggrann programvarukonfiguration, säkerhetsfokuserade processer och omfattande principer för att implementera skyddslösningar.

Med hjälp av programvara med lägsta behörighet i en låst dator för molnhantering (och för programutveckling) kan du minska risken för säkerhetsincidenter genom att standardisera miljöerna för fjärrhantering och utveckling. En förstärkt datorkonfiguration gör att du kan förhindra att konton som används för hantering av kritiska molnresurser komprometteras. Det gör du genom att stänga många vanliga vägar som används av skadlig kod och trojaner. Mer specifikt kan du med hjälp av [Windows AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759117(v=ws.11)) och Hyper-V-teknik styra och isolera klientsystembeteende och minska hotrisken, inklusive mejl eller surfning.

Administratören kör ett vanligt användarkonto (som blockerar körning på administrativ nivå) på en förstärkt dator och associerade program styrs av en lista över tillåtna. Grundelementen  i en förstärkt dator är följande:

* Aktiv genomsökning och korrigering. Distribuera program mot skadlig kod, utföra regelbundna sårbarhetsgenomsökningar och uppdatera alla datorer med de senaste säkerhetsuppdateringarna inom rimlig tid.
* Begränsad funktionalitet. Avinstallera alla program som inte behövs och inaktivera onödiga (start-) tjänster.
* Förstärka nätverket. Använda regler för Windows-brandväggen och endast tillåta giltiga IP-adresser, portar och URL:er som är relaterade till hantering av Azure. Se till att inkommande fjärranslutningar till datorn också blockeras.
* Begränsning av körning. Tillåt endast en uppsättning fördefinierade körbara filer som behövs för hantering (kallas ”standardneka”). Som standard bör användare nekas behörighet att köra alla program, om det inte definieras uttryckligen i listan över tillåtna.
* Lägsta behörighet. Användare av hanteringsdatorn bör inte ha administrativ behörighet på själva lokala datorn. På så sätt kan de inte ändra systemkonfigurationen eller systemfiler, oavsiktligt eller avsiktligt.

Du kan framtvinga allt detta genom att använda [grupprincipobjekt](../../active-directory-domain-services/manage-group-policy.md) (GPO:er) i AD DS (Active Directory Domain Services) och genom att använda dem i din (lokala) hanteringsdomän för alla hanteringskonton.

### <a name="managing-services-applications-and-data"></a>Hantera tjänster, program och data
Konfiguration av Azure Cloud Services utförs antingen via Azure Portal eller SMAPI, via kommandoradsgränssnittet Windows PowerShell eller ett specialbyggt program som utnyttjar dessa RESTful-gränssnitt. Produkter som använder dessa mekanismer är bland annat Azure AD (Active Directory Azure), Azure Storage, Azure Websites och Azure Virtual Network.

Program som distribueras på virtuell datorer tillhandahåller egna klientverktyg och -gränssnitt efter behov. Det gäller exempelvis Microsoft Management Console (MMC), en konsol för företagshantering (till exempel Microsoft System Center eller Windows Intune) eller ett annat hanteringsprogram, bland annat Microsoft SQL Server Management Studio. Verktygen finns oftast i en företagsmiljö eller i ett klientnätverk. De kan vara beroende av specifika nätverksprotokoll, till exempel Remote Desktop Protocol (RDP), som kräver direkta och tillståndskänsliga anslutningar. Vissa kan ha webbaktiverade gränssnitt som inte ska vara publicerade eller tillgängliga öppet via Internet.

I Azure kan du begränsa åtkomsten till hantering av infrastruktur och plattform med hjälp av [multifaktorautentisering](../../active-directory/authentication/concept-mfa-howitworks.md), [X.509-hanteringscertifikat](/archive/blogs/azuresecurity/certificate-management-in-azure-dos-and-donts) och brandväggsregler. Azure Portal och SMAPI kräver TLS (Transport Layer Security). Men tjänster och program som du distribuerar till Azure måste du vidta skyddsåtgärder som är lämpliga för ditt program. Dessa mekanismer kan ofta aktiveras enklare via en standardiserad förstärkt datorkonfiguration.

### <a name="management-gateway"></a>Management Gateway
Centralisera all administrativ åtkomst och förenkla övervakning och loggning genom att distribuera en dedikerad [ fjärrskrivbordsgateway ](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560672(v=ws.10))-server (RD Gateway) i det lokala nätverket och anslut den till Azure-miljön.

En  fjärrskrivbordsgateway  är en principbaserad RDP-proxytjänst som tillämpar säkerhetskrav. Genom att implementera RD Gateway tillsammans med Windows Server NAP (Network Access Protection) kan du se till att bara klienter som uppfyller de specifika hälsokriterier som upprättats av grupprincipobjekt för AD DS (Active Directory Domain Services) kan ansluta. Dessutom gäller följande:

* Etablera ett [Azure-hanteringscertifikat](/previous-versions/azure/gg551722(v=azure.100)) på RD Gateway så att det är den enda värden som får tillgång till Azure Portal.
* Koppla RD Gateway till samma [hanteringsdomän](/previous-versions/windows/it-pro/windows-2000-server/bb727085(v=technet.10)) som administratörsdatorerna. Detta är nödvändigt när du använder plats-till-plats IPsec VPN eller ExpressRoute i en domän som har ett enkelriktat förtroende till Azure AD, eller om du federerar autentiseringsuppgifter mellan din lokala AD DS-instans och Azure AD.
* Konfigurera en [auktoriseringsprincip för klientanslutning](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753324(v=ws.11)) så att RD Gateway kan verifiera att klientdatorns namn är giltigt (domänanslutet) och har tillgång till Azure Portal.
* Skydda hanteringstrafiken ytterligare från tjuvlyssnande och token-stöld med IPsec för [Azure VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/). Du kan även överväga en isolerad Internet-anslutning via [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Aktivera Multi-Factor Authentication (via [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)) eller Smart-Card-autentisering för administratörer som loggar in via RD Gateway.
* Minimera antalet tillåtna hanteringsslutpunkter genom att konfigurera [begränsningar för IP-adress](https://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) eller [nätverkssäkerhetsgrupper](../../virtual-network/network-security-groups-overview.md) i Azure.

## <a name="security-guidelines"></a>Riktlinjer för säkerhet
Vanligtvis kan du se till att administratörsdatorerna är säkra att använda med molnet på ungefär samma sätt som för övriga lokala datorer. Det handlar till exempel om att minimera byggnadsbehörigheterna och de restriktiva behörigheterna. Vissa unika aspekter av molnhantering liknar mer företagshantering via fjärranslutning eller utanför IP-nätverket. Det gäller bland annat att använda och granska autentiseringsuppgifter, fjärråtkomst med ökad säkerhet och hotidentifiering och -svar.

### <a name="authentication"></a>Autentisering
Du kan använda inloggningsbegränsningar för Azure om du vill begränsa källans IP-adresser från åtkomst till administrativa verktyg och granska förfrågningar. Du kan hjälpa Azure att identifiera hanterings klienter (arbets stationer och/eller program) genom att konfigurera både SMAPI (via kundutvecklade verktyg som Windows PowerShell-cmdlets) och Azure Portal att kräva att hanterings certifikat för klient sidan installeras, förutom TLS/SSL-certifikat. Vi rekommenderar också att administratörsåtkomst kräver multifaktorautentisering.

Vissa program eller tjänster som du distribuerar i Azure kan ha sina egna autentiseringsmekanismer för både slutanvändare och administratörer, medan andra utnyttjar Azure AD fullt ut. Beroende på om du federerar autentiseringsuppgifter via AD FS (Active Directory Federation Services), via katalogsynkronisering eller genom underhåll av användarkonton enbart i molnet kan du med hjälp av [Microsoft Identity Manager](/microsoft-identity-manager/) (del av Azure AD Premium) hantera identitetslivscykler mellan resurserna.

### <a name="connectivity"></a>Anslutning
Du kan säkra klientanslutningar till dina virtuella Azure-nätverk med hjälp av andra tillgängliga mekanismer. Med två av dessa mekanismer, [plats-till-plats-VPN](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) och [punkt-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) (P2S) kan du aktivera användning av branschstandarden IPsec (S2S) eller [Secure Socket Tunneling Protocol](/previous-versions/technet-magazine/cc162322(v=msdn.10)) (SSTP) (P2S) för kryptering och tunnlar. När Azure ansluter till offentliga Azure-tjänster för hantering, till exempel Azure Portal, kräver Azure Hypertext Transfer Protocol Secure (HTTPS).

En fristående förstärkt dator som inte ansluter till Azure via en RD Gateway ska använda det SSTP-baserade punkt-till-plats-VPN-nätverket för den första anslutningen till det virtuella Azure-nätverket. Därefter ska RDP-anslutning upprättas till enskilda virtuella datorer från VPN-tunneln.

### <a name="management-auditing-vs-policy-enforcement"></a>Hantering av granskning jämfört med tillämpning av principer
Vanligtvis finns det två sätt som hjälper dig att säkra hanteringsprocesser: gransknings- och principtillämpning. Om du gör både och får du omfattande kontroller, men det kanske inte är möjligt i alla situationer. Dessutom innebär varje metod olika nivåer av risk, kostnad och arbete som är kopplat till att hantera säkerhet, särskilt eftersom det gäller vilken förtroendenivå man har både för enskilda personer och för systemarkitekturer.

Övervakning, loggning och granskning ger en grund för att spåra och förstå administrativa aktiviteter, men det inte alltid möjligt att granska alla åtgärder i detalj på grund av mängden data som genereras. Men det är alltid bra att utvärdera hur effektiva hanteringsprinciperna är.

Principtillämpning som innehåller strikta åtkomstkontroller skapar programmässiga mekanismer som kan styra administratörsåtgärder, och det säkerställer att alla möjliga skyddsåtgärder används. Loggning ger bevis på efterlevnad. Det ger också ett register över vem som gjorde vad, från var och när. Med loggning kan du också granska och dubbelkontrollera information om hur administratörer följer principerna, och det ger dig aktivitetsbevis

## <a name="client-configuration"></a>Klientkonfiguration
Vi rekommenderar tre primära konfigurationer för en förstärkt dator. De största skillnaderna mellan dem är kostnad, användbarhet och tillgänglighet, samtidigt som du får en liknande säkerhetsprofil för alla alternativ. Följande tabell ger en kort analys av fördelar och riskerna för vart och ett av dem. (Observera att ”företagets dator” hänvisar till en stationär dator med standardkonfiguration som distribueras för alla domänanvändare, oavsett roller.)

| Konfiguration | Fördelar | Nackdelar |
| --- | --- | --- |
| Fristående förstärkt dator |Strikt kontrollerad dator |högre kostnader för dedikerade stationära datorer |
| - | Minskad risk för programtrojaner |Ökat hanteringsarbete |
| - | Tydlig uppdelning av uppgifter | - |
| Företagets dator som virtuell dator |Minskade maskinvarukostnader | - |
| - | Uppdelning av roll och program | - |

Det är viktigt att den förstärkta datorn är värden och inte gäst, utan något mellan värdoperativsystemet och maskinvaran. Att följa ”principen om ren källa” (även kallat ”säkert ursprung”) innebär att värden ska vara den som är mest förstärkt. I annat fall kan den förstärkta datorn (gäst) utsättas för angrepp i det system som är värd.

Du kan särskilja administrativa funktioner ytterligare via dedikerade systemavbildningar för varje strikt dator som bara har de verktyg och behörigheter som krävs för att hantera utvalda Azure- och molnprogram. Det gör du med specifika lokala AD DS-grupprincipobjekt för de nödvändiga uppgifterna.

För IT-miljöer som inte har lokal infrastruktur (till exempel ingen åtkomst till en lokal AD DS-instans för grupprincipobjekt eftersom alla servrar finns i molnet) kan en tjänst som [Microsoft Intune](/mem/intune/) förenkla distribution och underhåll av datorkonfigurationer.

### <a name="stand-alone-hardened-workstation-for-management"></a>Fristående strikt dator för hantering
Med en fristående strikt dator  har administratörer en stationär eller bärbar dator som de använder för administrativa uppgifter och en annan separat stationär eller bärbar dator för icke-administrativa uppgifter. En dator som är dedikerad för att hantera Azure-tjänster behöver inte ha andra program installerade. Dessutom hjälper datorer som stöder en [Trusted Platform Module](/previous-versions/windows/it-pro/windows-vista/cc766159(v=ws.10)) (TPM) eller liknande krypteringsteknik på maskinvarunivå till med att autentisera enheter och skydda från vissa angrepp. TPM kan också använda fullständigt skydd för systemenhet genom att använda [BitLocker-diskkryptering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)).

I situationen med den fristående strikta datorn (visas nedan) har den lokala instansen av Windows-brandväggen (eller en klientbrandvägg från en annan leverantör än Microsoft) konfigurerats för att blockera inkommande anslutningar, till exempel RDP. Administratören kan logga in på strikt arbetsstationen och starta en RDP-session som ansluter till Azure när du har etablerat en VPN-anslutning ansluta med ett virtuellt Azure-nätverk men det går inte att logga in på en dator som företagets och RDP för att ansluta till strikt arbetsstationen själva.

![Ett diagram som visar det fristående strikta arbets Stations scenariot.](./media/management/stand-alone-hardened-workstation-topology.png)

### <a name="corporate-pc-as-virtual-machine"></a>Företagets dator som virtuell dator
I fall där en separat fristående strikt dator är kostnadshindrande eller olämplig kan den vara värd för en virtuell dator för icke-administrativa uppgifter.

![Ett diagram som visar den skärpta arbets station som är värd för en virtuell dator för att utföra icke-administrativa uppgifter.](./media/management/hardened-workstation-enabled-with-hyper-v.png)

Du kan undvika flera säkerhetsrisker som kan uppstå när du använder en dator för systemhantering och andra dagliga arbetsuppgifter genom att distribuera en virtuell Windows Hyper-V-dator till den strikta datorn. Den här virtuella datorn kan användas som företagets dator. Företagsdatorn kan förbli isolerad från värden, vilket minskar dess angreppsyta och tar bort användarens dagliga aktiviteter (till exempel mejl) så att de inte finns tillsammans med känsliga administrativa uppgifter.

Företagets virtuella dator körs i ett skyddat utrymme och tillhandahåller användarprogram. Värden är en ”ren källa” och tillämpar strikta nätverksprinciper i rotoperativsystemet (till exempel att blockera RDP-åtkomst från den virtuella datorn).

## <a name="best-practices"></a>Bästa praxis
Tänk även på följande riktlinjer när du hanterar program och data i Azure.

### <a name="dos-and-donts"></a>Att tänka på
Utgå inte ifrån att andra vanliga säkerhetskrav kan åsidosättas om en dator har låsts. Risken är högre på grund av de upphöjda åtkomstnivåerna som administratörskonton normalt har. Exempel på risker och deras olika säkra rutiner visas i tabellen nedan.

| Gör inte följande | Gör följande |
| --- | --- |
| Skicka inte autentiseringsuppgifter för administratörs åtkomst eller andra hemligheter (till exempel TLS/SSL eller hanterings certifikat) |Upprätthåll sekretessen genom att lämna ut kontonamn och -lösenord via telefon (men lagrar dem inte i röstmeddelanden), utför en fjärrinstallation av klient-/servercertifikat (via en krypterad session), ladda ned från en skyddad nätverksresurs eller distribuera manuellt via flyttbara medier. |
| - | Hantera hanteringscertifikatets livscyklar proaktivt. |
| Lagra inte lösenord okrypterade eller icke-hashformaterade i programlagring (till exempel i kalkylblad, SharePoint-webbplatser eller filresurser). |Fastställa säkerhetsprinciper och principer för systemhärdning och använd dem i din utvecklingsmiljö. |
| - | Använd regler för att fästa certifikat i [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751) för lämplig åtkomst till Azure SSL/TLS-webbplatser. |
| Använd inte samma konton och lösenord för flera administratörer. Återanvänd inte heller lösenord i flera användarkonton eller -tjänster, särskilt de för sociala medier eller andra icke-administrativa aktiviteter. |Skapa ett särskilt Microsoft-konto för hantering av din Azure-prenumeration. Det ska vara ett konto som inte används för privat mejl. |
| Skicka inte konfigurationsfiler via mejl. |Konfigurationsfiler och profiler ska installeras från en betrodd källa (till exempel en krypterad USB-flash-enhet), inte från en mekanism som lätt kan äventyras, till exempel mejl. |
| Använd inte svaga eller enkla lösenord för inloggning. |Tillämpa principer för starka lösenord, förfallotider (ändra vid första användning), tidsgränser för konsolen och automatiska kontolåsningar. Använd ett hanteringssystem för klientlösenord med multifaktorautentisering för valvåtkomst med lösenord. |
| Exponera inte hanteringsportar på Internet. |Begränsa hanteringsåtkomsten genom att låsa Azure-portar och IP-adresser. Mer information finns i faktabladet [Azure Network Security](https://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx). |
| - | Använd brandväggar, VPN:er och NAP för alla hanteringsanslutningar. |

## <a name="azure-operations"></a>Åtgärder i Azure
Inom Microsofts Azure-åtgärder använder operationstekniker och supportpersonal som har åtkomst till Azures produktionssystem  [härdade datorer med virtuella datorer](#stand-alone-hardened-workstation-for-management) etablerade på dem för intern åtkomst till företagsnätverk och -program (till exempel mejl, intranät o.s.v.). Alla hanteringsdatorer har TPM:er, värdens startenheten är krypterad med BitLocker och de är anslutna till en särskild organisationsenhet (OU) i Microsofts primära företagsdomän.

Systemhärdning tillämpas via en grupprincip med centraliserad uppdatering av programvara. När det gäller granskning och analys samlas händelseloggar (till exempel säkerhet och AppLocker) in från hanteringsdatorer och sparas på en central plats.

Dessutom används dedikerade JumpBox-datorer i Microsofts nätverk som kräver tvåfaktorsautentisering för att ansluta till Azures produktionsnätverk.

## <a name="azure-security-checklist"></a>Checklista för Azure-säkerhet
Genom att minimera antalet uppgifter som administratörer kan utföra på en härdad arbetsstation kan du minimera risken för angrepp i utvecklings- och hanteringsmiljön. Använd följande tekniker för att skydda din härdade dator:

* IE-härdning. Webbläsaren Internet Explorer (eller valfri webbläsare) är en viktig ingångspunkt för skadlig kod på grund av dess omfattande interaktioner med externa servrar. Granska dina klientprinciper och se till att webbläsaren körs i skyddat läge genom att inaktivera tillägg, inaktivera filnedladdningar och använda [Microsoft SmartScreen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj618329(v=ws.11))-filtrering. Kontrollera att säkerhetsvarningar visas. Dra nytta av Internetzoner och skapa en lista över betrodda platser som du har konfigurerat rimliga härdning för. Blockera alla andra webbplatser och kod i webbläsaren, till exempel ActiveX och Java.
* Standardanvändare. När du kör som en vanlig användare får du ett antal fördelar. Den största är att det blir svårare att stjäla administratörers autentiseringsuppgifter via skadlig kod. Dessutom har ett vanligt användarkonto inte utökade privilegier på rotoperativsystemet, och många konfigurationsalternativ och API:er är låsta som standard.
* AppLocker. Du kan begränsa vilka program och skript som användarna kan köra med hjälp av [AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619725(v=ws.10)) . Du kan köra AppLocker i granskningsläge eller tvingande läge. Som standard har AppLocker en regel för Tillåt som gör att användare med en admin-token kan köra all kod på klienten. Den här regeln finns för att förhindra att administratörer låser sig ut och den tillämpas bara för upphöjda token. Se även Kodintegritet som en del av Windows Servers [grundläggande säkerhet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd348705(v=ws.10)).
* Kodsignering. Att kodsignera alla verktyg och skript som används av administratörer ger en hanterbar mekanism för att distribuera principer för programlåsning. Hashvärden skalas inte med snabba kodändringar och filsökvägar ger inte en hög säkerhetsnivå. Du bör kombinera AppLocker-regler med en PowerShell [-körningsprincip](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)) som bara tillåter att viss signerad kod och vissa signerade skript [körs](/powershell/module/microsoft.powershell.security/set-executionpolicy).
* Grupprincip. Skapa en global administrativ princip som tillämpas på alla domändatorer som används för hantering (och blockera åtkomst från alla andra) samt för användarkonton som autentiseras på datorerna.
* Säkrare etablering. Skydda dig mot manipulation genom att skydda baslinjens härdade datoravbildning. Använd säkerhetsåtgärder som kryptering och isolering för att lagra avbildningar, virtuella datorer och skript och begränsa åtkomsten (till exempel genom att använda en granskningsbar in-/utcheckningsprocess).
* Korrigering. Underhåll en konsekvent avbildning (eller ha separata avbildningar för utveckling, åtgärder och andra administrativa uppgifter), sök regelbundet efter ändringar och skadlig kod, uppdatera kontinuerligt  och aktivera bara datorer när de behövs.
* Kryptering. Kontrollera att hanteringsdatorerna har en TPM så att du kan aktivera [krypterande filsystem](/previous-versions/tn-archive/cc700811(v=technet.10)) (EFS) och BitLocker på ett säkert sätt.
* Styrning. Styr alla administratörens Windows-gränssnitt (till exempel fildelning) genom att använda AD DS-grupprincipobjekt. Inkludera hanteringsdatorer processerna för granskning, övervakning och loggning. Spåra alla administratörers och utvecklares åtkomst och användning.

## <a name="summary"></a>Sammanfattning
Genom att använda en härdad datorkonfiguration för administrering av dina Azure-molntjänster, virtuella datorer och program kan du undvika flera risker och hot som kan uppstå när du fjärrhanterar kritiska IT-infrastrukturer. Både Azure och Windows erbjuder funktioner som du kan använda för att skydda och styra beteenden för kommunikation, autentisering och klienten.

## <a name="next-steps"></a>Nästa steg
Följande resurser med allmän information om Azure och relaterade Microsoft-tjänster finns tillgängliga, utöver de specifika objekt som det hänvisas till i det här dokumentet:

* [Skydda privilegierad åtkomst](/windows-server/identity/securing-privileged-access/securing-privileged-access) – Hämta teknisk information för att designa och skapa en säker administrativ dator för hantering av Azure
* [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure) – Lär dig mer om de funktioner i Azure-plattformen som skyddar Azure-strukturen och de arbetsbelastningar som körs på Azure
* [Microsoft Security Response Center](https://www.microsoft.com/msrc) – här kan du rapportera säkerhetsproblem i Microsoft, inklusive problem med Azure, eller mejla till [secure@microsoft.com](mailto:secure@microsoft.com)