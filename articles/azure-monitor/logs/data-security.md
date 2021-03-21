---
title: Log Analytics data säkerhet | Microsoft Docs
description: Lär dig mer om hur Log Analytics skyddar din integritet och skyddar dina data.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/11/2020
ms.openlocfilehash: 8f193cface177e6be6343f28ef4dc27c2d6c6a7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030910"
---
# <a name="log-analytics-data-security"></a>Log Analytics data säkerhet
Det här dokumentet är avsett att ge information som är speciell för Log Analytics, som är en funktion i Azure Monitor, för att komplettera informationen på [Azure Säkerhetscenter](https://www.microsoft.com/en-us/trust-center?rtc=1).  

Den här artikeln förklarar hur data samlas in, bearbetas och skyddas av Log Analytics. Du kan använda agenter för att ansluta till webb tjänsten, använda System Center Operations Manager för att samla in användnings data eller hämta data från Azure Diagnostics för användning av Log Analytics. 

Tjänsten Log Analytics hanterar dina molnbaserade data på ett säkert sätt med hjälp av följande metoder:

* Dataavgränsning
* Datakvarhållning
* Fysisk säkerhet
* Incidenthantering
* Efterlevnad
* Säkerhets standard certifieringar

Du kan också använda ytterligare säkerhetsfunktioner som är inbyggda i Azure Monitor och Log Analytics. Dessa funktioner kräver mer administratörs hantering. 
* Kundhanterade nycklar (Security)
* Privat Azure-lagring
* Private Link-nätverk 
* Åtkomst gränser för Azure-support som anges av Azure-databasen

Kontakta oss med frågor, förslag eller frågor om någon av följande uppgifter, inklusive våra säkerhets principer på [Support alternativ för Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Skicka data på ett säkert sätt med TLS 1,2 

För att säkerställa säkerheten för data som överförs till Log Analytics rekommenderar vi starkt att du konfigurerar agenten att använda minst Transport Layer Security (TLS) 1,2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte** och branschen flyttas snabbt till överge support för dessa äldre protokoll. 

[PCI Security Standards-rådet](https://www.pcisecuritystandards.org/) har ställt in en [tids gräns på den 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) för att inaktivera äldre versioner av TLS/SSL och uppgradera till säkrare protokoll. Om dina agenter inte kan kommunicera via minst TLS 1,2 kan du inte skicka data till Log Analytics när Azure släpper äldre support. 

Vi rekommenderar att du inte uttryckligen anger att agenten ska använda TLS 1,2 om det inte är absolut nödvändigt, eftersom det kan bryta säkerhets funktioner på plattforms nivå som gör att du automatiskt kan identifiera och dra nytta av nyare säkra protokoll när de blir tillgängliga, t. ex. TLS 1,3. 

### <a name="platform-specific-guidance"></a>Plattformsspecifik vägledning

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner tenderar att förlita sig på [openssl](https://www.openssl.org) för TLS 1,2-stöd.  | Kontrol lera [openssl-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows 8,0-10 | Stöds och är aktiverat som standard. | För att bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012-2016 | Stöds och är aktiverat som standard. | Bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men är inte aktiverat som standard. | På sidan [Transport Layer Security (TLS) register inställningar](/windows-server/security/tls/tls-registry-settings) finns mer information om hur du aktiverar.  |

## <a name="data-segregation"></a>Dataavgränsning
När dina data har matats in av Log Analyticss tjänsten, lagras data logiskt åtskilda på varje komponent i tjänsten. Alla data är taggade per arbets yta. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten. Dina data lagras i en dedikerad databas i lagrings klustret i den region som du har valt.

## <a name="data-retention"></a>Datakvarhållning
Indexerade logg Sök data lagras och bevaras enligt pris planen. Mer information finns i [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/log-analytics/).

Som en del av ditt [prenumerations avtal](https://azure.microsoft.com/support/legal/subscription-agreement/)behåller Microsoft dina data enligt villkoren i avtalet.  När kund information tas bort förstörs inga fysiska enheter.  

I följande tabell visas några av de tillgängliga lösningarna och innehåller exempel på den typ av data som samlas in.

| **Lösning** | **Datatyper** |
| --- | --- |
| Kapacitet och prestanda |Prestanda data och metadata |
| Uppdateringshantering |Metadata och tillstånds data |
| Logg hantering |Användardefinierade händelse loggar, Windows händelse loggar och/eller IIS-loggar |
| Spårning av ändringar |Program varu inventering, metadata för Windows-tjänst och Linux-daemon och Windows/Linux-filmetadata |
| SQL och Active Directory-utvärdering |WMI-data, register data, prestanda data och SQL Server dynamisk hantering Visa resultat för vyn dynamisk hantering |

I följande tabell visas exempel på data typer:

| **Datatyp** | **Fält** |
| --- | --- |
| Varning |Aviserings namn, aviserings beskrivning, BaseManagedEntityId, problem-ID, IsMonitorAlert, RuleId, ResolutionState, prioritet, allvarlighets grad, kategori, ägare, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguration |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Händelse |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Obs:** När du skriver händelser med anpassade fält i till händelse loggen i Windows samlar Log Analytics in dem. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP-adress, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Prestanda |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Tillstånd |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId,, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysisk säkerhet
Tjänsten Log Analytics hanteras av Microsoft-personal och alla aktiviteter loggas och kan granskas. Log Analytics körs som en Azure-tjänst och uppfyller alla krav för Azure-efterlevnad och säkerhet. Du kan visa information om den fysiska säkerheten för Azure-tillgångar på sidan 18 i [Microsoft Azure säkerhets översikt](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysiska åtkomst rättigheter till säkra områden har ändrats inom en arbets dag för alla som inte längre har ansvar för tjänsten Log Analytics, inklusive överföring och uppsägning. Du kan läsa om den globala fysiska infrastrukturen som vi använder på [Microsoft Data Center](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Incidenthantering
Log Analytics har en incident hanterings process som alla Microsoft-tjänster följer. För att sammanfatta ska vi:

* Använd en delad ansvars modell där en del av ett säkerhets ansvar tillhör Microsoft och en del tillhör kunden
* Hantera säkerhets incidenter i Azure:
  * Starta en undersökning vid identifiering av en incident
  * Utvärdera konsekvenserna och allvarlighets graden för en incident av en jour incident svars grupp medlem. Utvärderingen kan eller inte resultera i ytterligare eskalering till Security Response-teamet, baserat på bevis.
  * Diagnostisera en incident genom säkerhetssvars experter för att genomföra den tekniska eller kriminal tekniska undersökningen, identifiera inne slutnings-, lösnings-och lösnings strategier. Om säkerhets teamet anser att kund information kan ha blivit utsatt för en olagligt eller obehörig person, är parallell körningen av meddelande processen för kund incident inlett parallellt.  
  * Stabilisera och Återställ från incidenten. Incident hanterings teamet skapar en återställnings plan för att minimera problemet. Hanterings steg för kris, till exempel sätta system som påverkas, kan uppstå omedelbart och parallellt med diagnos. Längre långsiktiga åtgärder kan planeras som inträffar efter det att den omedelbara risken har passerat.  
  * Stäng incidenten och genomför en efter slakt. Incident hanterings teamet skapar en efter slakt som visar information om incidenten, med avsikt att ändra principer, procedurer och processer för att förhindra att händelsen upprepas.
* Meddela kunder om säkerhets incidenter:
  * Fastställ omfattningen av påverkade kunder och för att ge vem som påverkas som ett detaljerat meddelande som möjligt
  * Skapa ett meddelande om att förse kunderna med detaljerad information så att de kan utföra en undersökning på deras sida och uppfylla alla åtaganden som de har gjort till sina slutanvändare, samtidigt som aviserings processen inte är korrekt försenad.
  * Bekräfta och deklarera incidenten vid behov.
  * Meddela kunder med en incident avisering utan orimlig fördröjning och i enlighet med ett juridiskt eller avtalat åtagande. Meddelanden om säkerhets incidenter levereras till en eller flera av kundens administratörer på något av de sätt som Microsoft väljer, inklusive via e-post.
* Genomför team beredskap och utbildning:
  * Microsoft-personal krävs för att slutföra utbildning av säkerhet och medvetenhet, vilket hjälper dem att identifiera och rapportera misstänkta säkerhets problem.  
  * Operatörer som arbetar med Microsoft Azure tjänsten har ytterligare utbildnings skyldigheter som omger deras åtkomst till känsliga system som är värdar för kund information.
  * Microsoft Security Response personal får specialiserad utbildning för sina roller

Om förlust av kund information inträffar meddelar vi varje kund inom en dag. Men Kundens data förlust har aldrig skett med tjänsten. 

Mer information om hur Microsoft reagerar på säkerhets incidenter finns i [Microsoft Azure säkerhets svar i molnet](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Efterlevnad
Den Log Analytics program utvecklings-och tjänst teamets informations säkerhet och styrnings program stöder sina affärs behov och följer lagar och förordningar enligt beskrivningen i [Microsoft Azure säkerhets Center](https://azure.microsoft.com/support/trust-center/) och [Microsoft Trust Center-efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Hur Log Analytics skapar säkerhets krav, identifierar säkerhets kontroller, hanterar och övervakar risker. Varje år går vi igenom principer, standarder, procedurer och rikt linjer.

Varje utvecklings grupp medlem tar emot formell program säkerhets utbildning. Internt använder vi ett versions kontroll system för program utveckling. Varje program varu projekt skyddas av versions kontroll systemet.

Microsoft har ett team för säkerhet och efterlevnad som överser och utvärderar alla tjänster i Microsoft. Informations säkerhets ansvariga utgör teamet och de är inte kopplade till de teknik team som utvecklar Log Analytics. Säkerhets tjänstemännen har sin egen hanterings kedja och utför oberoende utvärdering av produkter och tjänster för att säkerställa säkerhet och efterlevnad.

Microsofts styrelse styrelse meddelas av en års rapport om alla informations säkerhets program på Microsoft.

Log Analytics program utvecklings-och tjänst teamet arbetar aktivt med Microsofts juridiska och efterlevnads team och andra bransch partner för att förvärva olika certifieringar.

## <a name="certifications-and-attestations"></a>Certifieringar och attesteringar
Azure Log Analytics uppfyller följande krav:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Payment Card Industry (PCI-kompatibel) data säkerhets standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) av PCI Security Standards-rådet.
* [Service Organization Controls (SOC) 1 typ 1 och SOC 2 typ 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) är kompatibel
* [HIPAA och HITECH](/compliance/regulatory/offering-hipaa-hitech) för företag som har ett HIPAA affärs associerat avtal
* Kriterier för Windows common Engineering
* Microsoft-säker dator användning
* Som Azure-tjänst uppfyller de komponenter som Log Analytics använder sig av för att uppfylla Azures krav på efterlevnad. Du kan läsa mer på [Microsoft Trust Center-kompatibilitet](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> I vissa certifieringar/attesteringar visas Log Analytics under det tidigare namnet på *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Säkerhets data flöde för molnbaserad data behandling
I följande diagram visas en moln säkerhets arkitektur som informations flöde från ditt företag och hur det skyddas som flyttas till den Log Analytics tjänsten, som slutligen setts av dig i Azure Portal. Mer information om varje steg följer diagrammet.

![Bild av Log Analytics data insamling och säkerhet](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. registrera dig för Log Analytics och samla in data
För att din organisation ska kunna skicka data till Log Analytics konfigurerar du en Windows-eller Linux-Agent som körs på virtuella Azure-datorer eller på virtuella eller fysiska datorer i din miljö eller annan moln leverantör.  Om du använder Operations Manager konfigurerar du Operations Manager agenten från hanterings gruppen. Användare (som kan vara du, andra enskilda användare eller en grupp av människor) skapar en eller flera Log Analytics arbets ytor och registrerar agenter genom att använda något av följande konton:

* [Organisations-ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-konto – Outlook, Office Live, MSN](https://account.microsoft.com/account)

En Log Analytics arbets yta är där data samlas in, aggregeras, analyseras och presenteras. En arbets yta används främst som ett sätt att partitionera data och varje arbets yta är unik. Du kanske till exempel vill ha dina produktions data som hanteras med en arbets yta och dina test data som hanteras med en annan arbets yta. Arbets ytor hjälper också en administratör att styra användar åtkomsten till data. Varje arbets yta kan ha flera associerade användar konton, och varje användar konto har åtkomst till flera Log Analytics arbets ytor. Du skapar arbets ytor baserat på Data Center region.

För Operations Manager upprättar Operations Manager hanterings gruppen en anslutning till Log Analyticss tjänsten. Du kan sedan konfigurera vilka agent-hanterade system i hanterings gruppen som tillåts samla in och skicka data till tjänsten. Beroende på vilken lösning du har aktiverat skickas data från de här lösningarna direkt från en Operations Manager hanterings server till Log Analyticss tjänsten, eller på grund av data mängden som samlas in av det agentbaserade systemet, skickas direkt från agenten till tjänsten. För system som inte övervakas av Operations Manager ansluter varje säkert till Log Analyticss tjänsten direkt.

All kommunikation mellan anslutna system och tjänsten Log Analytics är krypterad. TLS-protokollet (HTTPS) används för kryptering.  Microsoft SDL-processen följs för att säkerställa att Log Analytics är uppdaterad med de senaste förbättringarna i kryptografi protokollen.

Varje typ av agent samlar in data för Log Analytics. Vilken typ av data som samlas in beror på vilka typer av lösningar som används. Du kan se en sammanfattning av data insamling i [Lägg till Log Analytics lösningar från Lösningsgalleriet](../insights/solutions.md). Dessutom är mer detaljerad samlings information tillgänglig för de flesta lösningar. En lösning är ett paket med fördefinierade vyer, loggs öknings frågor, data insamlings regler och bearbetnings logik. Endast administratörer kan använda Log Analytics för att importera en lösning. När lösningen har importer ATS flyttas den till Operations Manager hanterings servrar (om den används) och sedan till alla agenter som du har valt. Därefter samlar agenterna in data.

## <a name="2-send-data-from-agents"></a>2. skicka data från agenter
Du registrerar alla agent typer med en registrerings nyckel och en säker anslutning upprättas mellan agenten och den Log Analytics tjänsten med certifikatbaserad autentisering och TLS med port 443. Log Analytics använder ett hemligt Arkiv för att generera och underhålla nycklar. Privata nycklar roteras var 90 dag och lagras i Azure och hanteras av Azure-åtgärder som följer strikta regler och efterlevnad.

Med Operations Manager upprättar hanterings gruppen som är registrerad i en Log Analytics arbets yta en säker HTTPS-anslutning med en Operations Manager-hanteringsserver.

För Windows-eller Linux-agenter som körs på virtuella Azure-datorer används en skrivskyddad lagrings nyckel för att läsa diagnostiska händelser i Azure-tabeller.  

Med alla agenter som rapporterar till en Operations Manager hanterings grupp som är integrerad med Log Analytics, om hanterings servern inte kan kommunicera med tjänsten av någon anledning, lagras insamlade data lokalt i en tillfällig cache på hanterings servern.   De försöker skicka om data var åttonde minut i två timmar.  För data som kringgår hanterings servern och skickas direkt till Log Analytics, är beteendet konsekvent med Windows-agenten.  

Cachelagrade data för Windows-eller Management Server-agenten skyddas av operativ systemets Credential Store. Om tjänsten inte kan bearbeta data efter två timmar kommer agenterna att köa data. Om kön blir full börjar agenten att släppa data typer, från och med prestanda data. Gränsen för agent kön är en register nyckel så att du kan ändra den, om det behövs. Insamlade data komprimeras och skickas till tjänsten, vilket kringgår databaserna för Operations Manager hanterings grupp, så att den inte lägger till någon belastning till dem. När insamlade data har skickats tas de bort från cachen.

Som det beskrivs ovan skickas data från hanterings servern eller direkt anslutna agenter via TLS till Microsoft Azure Data Center. Du kan också använda ExpressRoute för att ge ytterligare säkerhet för data. ExpressRoute är ett sätt att ansluta direkt till Azure från ditt befintliga WAN-nätverk, till exempel en MPLS-VPN (Multi-Protocol Label Switching) som tillhandahålls av en nätverks tjänst leverantör. Mer information finns i [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Log Analytics tjänsten tar emot och bearbetar data
Log Analytics tjänsten säkerställer att inkommande data kommer från en betrodd källa genom att verifiera certifikat och data integritet med Azure-autentisering. Obearbetade rå data lagras sedan i en Azure Event Hub i den region som data kommer att lagras i vila. Vilken typ av data som lagras beror på vilka typer av lösningar som har importer ATS och använts för att samla in data. Sedan bearbetar Log Analyticss tjänsten rå data och matar in dem i databasen.

Kvarhållningsperioden för insamlade data som lagras i databasen beror på den valda pris planen. För den *kostnads fria* nivån finns insamlade data tillgängliga i sju dagar. För den *betalda* nivån är insamlade data tillgängliga i 31 dagar som standard, men kan utökas till 730 dagar. Data lagras krypterade i vila i Azure Storage, för att säkerställa data sekretessen och data replikeras i den lokala regionen med hjälp av lokalt redundant lagring (LRS). De senaste två veckorna med data lagras också i SSD-baserad cache och denna cache är krypterad.

Data i databas lagringen kan inte ändras när de har matats in men kan tas bort via [ *rensnings* -API-sökväg](personal-data-mgmt.md#delete). Även om det inte går att ändra data, kräver vissa certifieringar att data förblir oföränderliga och inte kan ändras eller tas bort i lagrings utrymmet. Data oföränderlighets kan uppnås med hjälp av [data export](logs-data-export.md) till ett lagrings konto som har kon figurer ATS som [oföränderligt lagrings utrymme](../../storage/blobs/storage-blob-immutability-policies-manage.md).

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Använd Log Analytics för att komma åt data
För att komma åt din Log Analytics-arbetsyta loggar du in på Azure Portal med hjälp av organisations kontot eller Microsoft-konto som du har skapat tidigare. All trafik mellan portalen och Log Analytics tjänsten skickas via en säker HTTPS-kanal. När du använder portalen genereras ett sessions-ID på användar klienten (webbläsaren) och data lagras i en lokal cache tills sessionen avslutas. När den avbröts tas cacheminnet bort. Cookies på klient sidan, som inte innehåller personligt identifierbar information, tas inte bort automatiskt. Sessionscookies är markerade som HTTPOnly och skyddas. Azure Portal sessionen avslutas efter en fördefinierad inaktiv period.


## <a name="additional-security-features"></a>Ytterligare säkerhetsfunktioner
Du kan använda dessa ytterligare säkerhetsfunktioner för att skydda din Azure Monitor/Log Analytics-miljö ytterligare. Dessa funktioner kräver mer administratörs hantering. 
- [Kundhanterade nycklar (Security)](../logs/customer-managed-keys.md) – du kan använda Kundhanterade nycklar för att kryptera data som skickas till dina Log Analytics-arbetsytor. Det kräver att Azure Key Vault används. 
- [Privat/kundhanterad lagring](./private-storage.md) – hantera ditt personligt krypterade lagrings konto och be Log Analytics att använda det för att lagra övervaknings data 
- [Privat länk nätverk](./private-link-security.md) – med Azure Private Link kan du på ett säkert sätt länka Azure PaaS Services (inklusive Azure Monitor) till ditt virtuella nätverk med hjälp av privata slut punkter. 
- [Azure Customer lock](../../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-preview) -Customer Lockbox för Microsoft Azure ger ett gränssnitt för kunder att granska och godkänna eller avvisa förfrågningar om kund data åtkomst. Den används i fall där en Microsoft-tekniker behöver åtkomst till kundinformation under en supportförfrågan.


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du samlar in data med Log Analytics för dina virtuella Azure-datorer enligt snabb starten för [Azure VM](../vm/quick-collect-azurevm.md).  

*  Om du vill samla in data från fysiska eller virtuella Windows-eller Linux-datorer i din miljö kan du läsa [snabb starten för Linux-datorer](../vm/quick-collect-linux-computer.md) eller [snabb start för Windows-datorer](../vm/quick-collect-windows-computer.md)