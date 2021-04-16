---
title: Planera en Azure Active Directory Programproxy distribution
description: En guide från slutet till slut för att planera distributionen av programproxy i din organisation
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: b0a3653d2cc840d745b1bb5788406b8d374c76d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533777"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planera en distribution av Azure AD-programproxy

Azure Active Directory (Azure AD) Programproxy är en säker och kostnadseffektiv fjärråtkomstlösning för lokala program. Det ger en omedelbar övergång för "Cloud First"-organisationer att hantera åtkomst till äldre lokala program som ännu inte kan använda moderna protokoll. Mer information finns i Vad [är Programproxy](./application-proxy.md).

Programproxy rekommenderas för att ge fjärranslutna användare åtkomst till interna resurser. Programproxy ersätter behovet av en VPN-anslutning eller omvänd proxy för dessa användningsfall för fjärråtkomst. Det är inte avsett för användare som finns i företagsnätverket. Dessa användare som använder Programproxy för intranätåtkomst kan uppleva oönskade prestandaproblem.

Den här artikeln innehåller de resurser som du behöver för att planera, hantera och hantera Azure AD-Programproxy.

## <a name="plan-your-implementation"></a>Planera implementeringen

Följande avsnitt innehåller en bred vy över de viktigaste planeringselementen som hjälper dig att få en effektiv distributionsupplevelse.

### <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du påbörjar implementeringen. Du kan se mer information om hur du ställer in din miljö, inklusive dessa krav, i den här [självstudien.](application-proxy-add-on-premises-application.md)

* **Anslutningsappar:** Anslutningsappar är lätta agenter som du kan distribuera till:
   * Lokal fysisk maskinvara
   * En virtuell dator som finns i en hypervisor-lösning
   * En virtuell dator som finns i Azure för att aktivera utgående anslutning till Programproxy tjänsten.

* Se [Förstå Azure AD App Proxy Connectors för](application-proxy-connectors.md) en mer detaljerad översikt.

     * Anslutningsdatorer måste [vara aktiverade för TLS 1.2](application-proxy-add-on-premises-application.md) innan anslutningsapparna installeras.

     * Om möjligt distribuerar du anslutningsappar i [samma nätverk och](application-proxy-network-topology.md) segment som backend-webbprogramservrarna. Det är bäst att distribuera anslutningsappar när du har slutfört en identifiering av program.
     * Vi rekommenderar att varje anslutningsgrupp har minst två anslutningsappar för att tillhandahålla hög tillgänglighet och skalning. Det är optimalt att ha tre anslutningsappar om du behöver ser över en dator när som helst. Granska tabellen [för anslutningskapacitet för](./application-proxy-connectors.md#capacity-planning) att få hjälp med att bestämma vilken typ av dator som anslutningsappar ska installeras på. Ju större dator desto mer buffert och presterande blir anslutningsappen.

* **Inställningar för nätverksåtkomst:** Azure AD Programproxy anslutningsappar ansluter till [Azure via HTTPS (TCP-port 443) och HTTP (TCP-port 80).](application-proxy-add-on-premises-application.md)

   * Det går inte att avsluta TLS-trafik för anslutningsappen och förhindrar att anslutningsappar etablerar en säker kanal med sina respektive Azure App Proxy-slutpunkter.

   * Undvik alla former av infogade kontroller för utgående TLS-kommunikation mellan anslutningsappar och Azure. Intern kontroll mellan ett anslutningsprogram och backend-program är möjligt, men kan försämra användarupplevelsen och därför rekommenderas inte.

   * Belastningsutjämning av själva anslutningsapparna stöds inte heller, eller ens nödvändigt.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Viktiga överväganden innan du konfigurerar Azure AD-Programproxy

Följande grundläggande krav måste uppfyllas för att konfigurera och implementera Azure AD-Programproxy.

*  **Azure-registrering:** Innan du distribuerar programproxy måste användaridentiteter synkroniseras från en lokal katalog eller skapas direkt i dina Azure AD-klienter. Identitetssynkronisering gör att Azure AD kan autentisera användare i förväg innan de beviljas åtkomst till publicerade program i App Proxy och har nödvändig information om användaridentifierare för att utföra enkel inloggning (SSO).

* **Krav för villkorsstyrd** åtkomst: Vi rekommenderar inte Programproxy för intranätåtkomst eftersom detta ökar svarstiden som påverkar användarna. Vi rekommenderar att Programproxy med förautentisering och principer för villkorlig åtkomst för fjärråtkomst från Internet.  En metod för att tillhandahålla villkorlig åtkomst för intranät är att modernisera program så att de kan autentisera direkt med AAD. Mer information [finns i Resurser för att migrera program till AAD.](./migration-resources.md)

* **Tjänstbegränsningar:** För att skydda mot överanvändning av resurser av enskilda klienter finns det begränsningar per program och klientorganisation. Information om dessa begränsningar finns i Begränsningar [och begränsningar för Azure AD-tjänster.](../enterprise-users/directory-service-limits-restrictions.md) Dessa begränsningsgränser baseras på ett prestandatest som ligger långt över den vanliga användningsvolymen och ger omfattande buffert för en majoritet av distributionerna.

* **Offentligt certifikat:** Om du använder anpassade domännamn måste du skaffa ett TLS/SSL-certifikat. Beroende på organisationens krav kan det ta lite tid att skaffa ett certifikat, och vi rekommenderar att du påbörjar processen så tidigt som möjligt. Azure Application Proxy stöder [SAN-baserade](application-proxy-wildcard.md)standardcertifikat, jokerteckencertifikat eller SAN-baserade certifikat. Mer information finns i [Konfigurera anpassade domäner med Azure AD Programproxy](application-proxy-configure-custom-domain.md).

* **Domänkrav:** Enkel inloggning till dina publicerade program med Kerberos-begränsad delegering (KCD) kräver att servern som kör anslutningsappen och servern som kör appen är domän-ansluten och en del av samma domän eller betrodda domäner.
Detaljerad information om ämnet finns i [KCD för enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md) med Programproxy. Anslutningstjänsten körs i kontexten för det lokala systemet och bör inte konfigureras för att använda en anpassad identitet.

* **DNS-poster för URL:er**

   * Innan du använder anpassade domäner i Programproxy måste du skapa en CNAME-post i offentlig DNS, så att klienter kan matcha den anpassade definierade externa URL:en till den fördefinierade Programproxy adressen. Om du inte skapar en CNAME-post för ett program som använder en anpassad domän förhindras fjärranvändare från att ansluta till programmet. Stegen som krävs för att lägga till CNAME-poster kan variera från DNS-provider till provider, så lär dig hur du hanterar DNS-poster och postuppsättningar med hjälp [av Azure Portal](../../dns/dns-operations-recordsets-portal.md).

   * På samma sätt måste anslutningsvärdar kunna matcha den interna URL:en för program som publiceras.

* **Administrativa rättigheter och roller**

   * **Anslutningsprogrammets** installation kräver lokal administratörsbehörighet till den Windows-server som den installeras på. Det krävs även minst en *programadministratörsroll för* att autentisera och registrera anslutningsinstansen till din Azure AD-klientorganisation.

   * **Programpublicering och -administration** kräver *rollen Programadministratör.* Programadministratörer kan hantera alla program i katalogen, inklusive registreringar, inställningar för enkel inloggning, användar- och grupptilldelningar och licensiering, Programproxy inställningar och medgivande. Den ger inte möjlighet att hantera villkorlig åtkomst. Rollen *Molnprogramadministratör* har alla funktioner som programadministratör, förutom att den inte tillåter hantering av Programproxy inställningar.

* **Licensiering:** Programproxy är tillgänglig via en Azure AD Premium prenumeration. Se sidan [Azure Active Directory för en](https://azure.microsoft.com/pricing/details/active-directory/) fullständig lista över licensieringsalternativ och funktioner.

### <a name="application-discovery"></a>Programidentifiering

Kompilera en inventering av alla program inom omfång som publiceras via Programproxy genom att samla in följande information:

| Informationstyp| Information som ska samlas in |
|---|---|
| Typ av tjänst| Till exempel: SharePoint, SAP, CRM, anpassat webbprogram, API |
| Programplattform | Exempel: Windows IIS, Apache på Linux, Tomcat, NGINX |
| Domänmedlemskap| Webbserverns fullständigt kvalificerade domännamn (FQDN) |
| Programplats | Var webbservern eller servergruppen finns i infrastrukturen |
| Intern åtkomst | Den exakta URL:en som används vid åtkomst till programmet internt. <br> Vilken typ av belastningsutjämning används om en servergrupp används? <br> Om programmet hämtar innehåll från andra källor än sig själv.<br> Kontrollera om programmet fungerar via WebSockets. |
| Extern åtkomst | Leverantörslösningen som programmet redan exponeras för externt. <br> Den URL som du vill använda för extern åtkomst. Om du använder SharePoint ska du se till att alternativa åtkomstmappningar har konfigurerats enligt [den här vägledningen.](/SharePoint/administration/configure-alternate-access-mappings) Annars måste du definiera externa URL:er. |
| Offentligt certifikat | Om du använder en anpassad domän ska du skaffa ett certifikat med motsvarande ämnesnamn. Om det finns ett certifikat ska du anteckna serienumret och platsen där det kan hämtas. |
| Autentiseringstyp| Den typ av autentisering som stöds av programstöd, till exempel Basic, Windows-integreringsautentisering, formulärbaserad, rubrikbaserad och anspråk. <br>Om programmet är konfigurerat att köras under ett specifikt domänkonto ska du anteckna det fullständiga domännamnet (FQDN) för tjänstkontot.<br> Om den är SAML-baserad, identifieraren och svars-URL:erna. <br> Om den är huvudbaserad, leverantörslösningen och specifika krav för hantering av autentiseringstyp. |
| Namn på anslutningsgrupp | Det logiska namnet för gruppen med anslutningsappar som ska anges för att tillhandahålla kanalen och enkel inloggning till det här backend-programmet. |
| Åtkomst för användare/grupper | De användare eller användargrupper som ska beviljas extern åtkomst till programmet. |
| Ytterligare krav | Observera eventuella ytterligare fjärråtkomst- eller säkerhetskrav som ska tas med i publiceringen av programmet. |

Du kan ladda ned det här [kalkylbladet för programinventering](https://aka.ms/appdiscovery) för att inventera dina appar.

### <a name="define-organizational-requirements"></a>Definiera organisationens krav

Följande är områden där du bör definiera organisationens affärskrav. Varje område innehåller exempel på krav

 **Åtkomst**

* Fjärranvändare med domän-anslutna eller Azure AD-anslutna enheter kan komma åt publicerade program på ett säkert sätt med sömlös enkel inloggning (SSO).

* Fjärranvändare med godkända personliga enheter kan på ett säkert sätt komma åt publicerade program, förutsatt att de är registrerade i MFA och har registrerat Microsoft Authenticator-appen på sin mobiltelefon som autentiseringsmetod.

**Styrning**

* Administratörer kan definiera och övervaka livscykeln för användartilldelningar till program som publicerats via Programproxy.

**Säkerhet**

* Endast användare som är tilldelade till program via gruppmedlemskap eller individuellt kan komma åt dessa program.

**Prestanda**

* Programprestanda försämras inte jämfört med åtkomst till program från det interna nätverket.

**Användarupplevelse**

* Användarna är medvetna om hur de kommer åt sina program med hjälp av välbekanta företags-URL:er på valfri enhetsplattform.

**Granskning**
* Administratörer kan granska aktiviteter för användaråtkomst.


### <a name="best-practices-for-a-pilot"></a>Metodtips för ett pilotprojekt

Fastställ hur lång tid och arbete som krävs för att helt kunna använda ett enda program för fjärråtkomst med enkel inloggning (SSO). Gör det genom att köra ett pilottest som tar hänsyn till den inledande identifieringen, publiceringen och den allmänna testningen. Att använda en enkel IIS-baserad webbapp som redan är förkonfigurerad för Integrerad Windows-autentisering (IWA) skulle hjälpa till att upprätta en baslinje, eftersom den här konfigurationen kräver minimalt arbete för att pilottesta fjärråtkomst och enkel inloggning.

Följande designelement bör öka framgången för pilotimplementering direkt i en produktionsklientorganisation.

**Anslutningshantering:**

* Anslutningsappar spelar en viktig roll när det gäller att tillhandahålla den lokala kanalen till dina program. Att använda **standardanslutningsgruppen** är lämpligt för inledande pilottestning av publicerade program innan de i uppdrags till produktion. Program som har testats kan sedan flyttas till produktionsanslutningsgrupper.

**Programhantering:**

* Personalen kommer troligen ihåg att en extern URL är bekant och relevant. Undvik att publicera ditt program med våra fördefinierade msappproxy.net eller onmicrosoft.com-suffix. Ange i stället en bekant toppnivåverifierad domän med prefixet ett logiskt värdnamn, till exempel *intranet.<customers_domain>.com*.

* Begränsa synligheten för pilotprogrammets ikon till en pilotgrupp genom att dölja startikonen från Azure MyApps-portalen. När den är klar för produktion kan du begränsa appen till dess respektive målgrupp, antingen i samma förproduktionsklient eller genom att även publicera programmet i din produktionsklientorganisation.

**Inställningar för enkel** inloggning: Vissa inställningar för enkel inloggning har specifika beroenden som kan ta tid att konfigurera, så undvik fördröjningar i ändringskontrollen genom att se till att beroenden åtgärdas i förväg. Detta inkluderar värdar för domänanslutningsanslutning för att utföra enkel inloggning med kerberos-begränsad delegering (KCD) och att ta hand om andra tidskrävande aktiviteter. Du kan till exempel konfigurera en PING Access-instans om du behöver rubrikbaserad enkel inloggning.

**TLS mellan anslutningsvärd och målprogram:** Säkerheten är avgörande, så TLS mellan anslutningsvärden och målprogrammen bör alltid användas. Särskilt om webbappen har konfigurerats för formulärbaserad autentisering (FBA), eftersom användarautentiseringsuppgifter sedan skickas i klartext.

**Implementera stegvis och testa varje steg.**
Utför grundläggande funktionstestning när du har publicerat ett program för att säkerställa att alla användar- och affärskrav uppfylls genom att följa anvisningarna nedan:

1. Testa och validera allmän åtkomst till webbappen med förautentisering inaktiverat.
2. Om det lyckas aktiverar du förautentisering och tilldelar användare och grupper. Testa och verifiera åtkomst.
3. Lägg sedan till SSO-metoden för ditt program och testa igen för att verifiera åtkomsten.
4. Tillämpa principer för villkorsstyrd åtkomst och MFA efter behov. Testa och validera åtkomst.

**Felsökningsverktyg:** När du felsöker ska du alltid börja med att verifiera åtkomsten till det publicerade programmet från webbläsaren på anslutningsappens värd och bekräfta att programmet fungerar som förväntat. Ju enklare konfigurationen är, desto enklare är det att fastställa rotorsaken, så överväg att försöka återskapa problem med en minimal konfiguration, till exempel att bara använda en enda anslutning och ingen enkel inloggning. I vissa fall kan webbfelsökningsverktyg som Teleriks Fiddler vara till hjälp för att felsöka problem med åtkomst eller innehåll i program som nås via en proxyserver. Fiddler kan också fungera som proxy för att spåra och felsöka trafik för mobila plattformar som iOS och Android, och i stort sett allt som kan konfigureras för att dirigera via en proxy. Mer information [finns i](application-proxy-troubleshoot.md) felsökningsguiden.

## <a name="implement-your-solution"></a>Implementera din lösning

### <a name="deploy-application-proxy"></a>Distribuera Programproxy

Stegen för att distribuera Programproxy tas upp i den här [självstudien](application-proxy-add-on-premises-application.md)för att lägga till ett lokalt program för fjärråtkomst. Om installationen inte lyckas väljer du **Felsök Programproxy** portalen eller använder felsökningsguiden för Problem med att installera [Programproxy Agent Connector.](application-proxy-connector-installation-problem.md)

### <a name="publish-applications-via-application-proxy"></a>Publicera program via Programproxy

Publicering av program förutsätter att du har uppfyllt alla krav och att du har flera anslutningsappar som visas som registrerade och aktiva på Programproxy sidan.

Du kan också publicera program med hjälp av [PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true).

Nedan visas några metodtips att följa när du publicerar ett program:

* **Använd anslutningsgrupper:** Tilldela en anslutningsgrupp som har angetts för publicering av respektive program. Vi rekommenderar att varje anslutningsgrupp har minst två anslutningsappar för hög tillgänglighet och skalning. Det är optimalt att ha tre anslutningsappar om du behöver ser över en dator när som helst. Dessutom kan du se [Publicera program](application-proxy-connector-groups.md) på separata nätverk och platser med hjälp av anslutningsgrupper för att se hur du också kan använda anslutningsgrupper för att segmentera dina anslutningsappar efter nätverk eller plats.

* **Ange tidsgräns för program på backend:** Den här inställningen är användbar i scenarier där programmet kan kräva mer än 75 sekunder för att bearbeta en klienttransaktion. Till exempel när en klient skickar en fråga till ett webbprogram som fungerar som en klientdel till en databas. Klientsidan skickar den här frågan till sin backend-databasserver och väntar på ett svar, men när den tar emot ett svar går det ut på klientsidan av konversationen. Om du ställer in tidsgränsen på Lång får du 180 sekunder för längre transaktioner att slutföra.

* **Använda lämpliga cookietyper**

   * **Endast HTTP-cookie:** Ger ytterligare säkerhet genom Programproxy inkluderar HTTPOnly-flaggan i SET-cookie HTTP-svarshuvuden. Den här inställningen hjälper till att minimera kryphål, till exempel skriptning mellan webbplatser (XSS). Lämna det här inställt på Nej för klienter/användaragenter som kräver åtkomst till sessionscookie. Exempel: RDP/MTSC-klient som ansluter till en fjärrskrivbordsgateway som publicerats via App Proxy.

   * **Säker cookie:** När en cookie anges med attributet Säker inkluderar användaragenten (appen på klientsidan) endast cookien i HTTP-begäranden om begäran skickas via en TLS-skyddad kanal. Detta minskar risken för att en cookie komprometteras över kanaler med klartext, så bör aktiveras.

   * **Beständig cookie:** Tillåter Programproxy sessionscookie att sparas mellan webbläsarnedstängningar genom att fortsätta vara giltig tills den antingen upphör att gälla eller tas bort. Används för scenarier där ett omfattande program, till exempel Office, kommer åt ett dokument i ett publicerat webbprogram, utan att användaren uppmanas att autentisera på nytt. Aktivera dock med försiktighet, eftersom beständiga cookies i slutändan kan ge en tjänst risk för obehörig åtkomst, om den inte används tillsammans med andra kompenserande kontroller. Den här inställningen bör endast användas för äldre program som inte kan dela cookies mellan processer. Det är bättre att uppdatera ditt program så att det hanterar delning av cookies mellan processer i stället för att använda den här inställningen.

* **Översätt URL:er** i rubriker: Du aktiverar detta för scenarier där intern DNS inte kan konfigureras för att matcha organisationens offentliga namnrymd (dela DNS). Om inte programmet kräver det ursprungliga värdhuvudet i klientbegäran lämnar du det här värdet inställt på Ja. Alternativet är att anslutningsappen använder FQDN i den interna URL:en för routning av den faktiska trafiken och FQDN i den externa URL:en som värdrubrik. I de flesta fall bör det här alternativet tillåta att programmet fungerar som vanligt vid fjärråtkomst, men användarna förlorar fördelarna med att ha en matchning inuti & utanför URL:en.

* **Översätt URL:er i programtexten:** Aktivera länköversättning av programtext för en app när du vill att länkarna från appen ska översättas i svar tillbaka till klienten. Om den här funktionen är aktiverad görs ett försök att översätta alla interna länkar som App Proxy hittar i HTML- och CSS-svar som returneras till klienter. Det är användbart när du publicerar appar som innehåller hårdkodade absoluta länkar eller NetBIOS-shortname-länkar i innehållet, eller appar med innehåll som länkar till andra lokala program.

För scenarier där en publicerad app länkar till andra publicerade appar aktiverar du länköversättning för varje program så att du har kontroll över användarupplevelsen på appnivå.

Anta till exempel att du har tre program publicerade via Programproxy som alla länkar till varandra: Förmåner, utgifter och resor, plus en fjärde app, Feedback, som inte publiceras via Programproxy.

![Bild 1](media/App-proxy-deployment-plan/link-translation.png)

När du aktiverar länköversättning för appen Förmåner omdirigeras länkarna till Utgifter och Resor till de externa URL:erna för dessa appar, så att användare som kommer åt programmen utanför företagsnätverket kan komma åt dem. Länkar från Utgifter och Resa tillbaka till Förmåner fungerar inte eftersom länköversättning inte har aktiverats för de två apparna. Länken till Feedback omdirigeras inte eftersom det inte finns någon extern URL, så användare som använder appen Förmåner kan inte komma åt feedbackappen utanför företagsnätverket. Se detaljerad information om [länköversättning och andra omdirigeringsalternativ.](application-proxy-configure-hard-coded-link-translation.md)

### <a name="access-your-application"></a>Få åtkomst till ditt program

Det finns flera alternativ för att hantera åtkomst till publicerade resurser i App Proxy, så välj det lämpligaste för ditt scenario och dina skalbarhetsbehov. Vanliga metoder är att använda lokala grupper som synkroniseras via Azure AD Connect, skapa dynamiska grupper i Azure AD baserat på användarattribut, använda självbetjäningsgrupper som hanteras av en resursägare eller en kombination av alla dessa. Se de länkade resurserna för att se fördelarna med var och en.

Det enklaste sättet att tilldela användare åtkomst till  ett program är att gå till alternativen Användare och grupper i den vänstra rutan i det publicerade programmet och direkt tilldela grupper eller enskilda användare.

![Bild 24](media/App-proxy-deployment-plan/add-user.png)

Du kan också ge användare självbetjäningsåtkomst till ditt program genom att tilldela en grupp som de för närvarande inte är medlemmar i och konfigurera självbetjäningsalternativen.

![Bild 25](media/App-proxy-deployment-plan/allow-access.png)

Om aktiverad kan användarna sedan logga in på MyApps-portalen och begära åtkomst, och antingen godkännas automatiskt och läggas till i den redan tillåtna självbetjäningsgruppen eller behöver godkännande från en utsedd godkännare.

Gästanvändare kan också [bjudas in för att få åtkomst till interna program som publicerats via Programproxy via Azure AD B2B.](../external-identities/add-users-information-worker.md)

För lokala program som normalt är tillgängliga anonymt och inte kräver någon autentisering kan du inaktivera alternativet som finns i programmets **Egenskaper.**

![Bild 26](media/App-proxy-deployment-plan/assignment-required.png)


Om det här alternativet är inställt på Nej kan användarna komma åt det lokala programmet via Azure AD App Proxy utan behörighet, så använd med försiktighet.

När ditt program har publicerats ska det vara tillgängligt genom att skriva dess externa URL i en webbläsare eller med dess ikon på [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Aktivera förautentisering

Kontrollera att programmet är tillgängligt via Programproxy via den externa URL:en.

1. Gå till **Azure Active Directory**  >    >  **Företagsprogram Alla program** och välj den app som du vill hantera.

2. Välj **Programproxy**.

3. I fältet **Förautentisering** använder du listrutan för att **välja Azure Active Directory** och väljer **Spara**.

När förautentisering är aktiverat uppmanar Azure AD användarna först att autentisera sig, och om enkel inloggning har konfigurerats verifierar serverprogrammet även användaren innan åtkomst till programmet beviljas. Om du ändrar förautentiseringsläget från Genomströmning till Azure AD konfigureras även den externa URL:en med HTTPS, så alla program som ursprungligen konfigurerats för HTTP kommer nu att skyddas med HTTPS.

### <a name="enable-single-sign-on"></a>Aktivera enkel Sign-On

Enkel inloggning ger bästa möjliga användarupplevelse och säkerhet eftersom användarna bara behöver logga in en gång vid åtkomst till Azure AD. När en användare har autentiserats i förväg utförs enkel inloggning av Programproxy-anslutningsappen som autentiserar till det lokala programmet för användarens räkning. Backend-programmet bearbetar inloggningen som om det vore användaren själv.

Om du **väljer alternativet** Genomströmning får användarna åtkomst till det publicerade programmet utan att behöva autentisera till Azure AD.

Du kan bara utföra enkel inloggning om Azure AD kan identifiera den användare som begär åtkomst till en resurs, så ditt program måste konfigureras för att autentisera användare med Azure AD vid åtkomst för att enkel inloggning ska fungera, annars inaktiveras alternativen för enkel inloggning.

Läs Enkel inloggning till program i Azure AD för att hjälpa dig att välja den lämpligaste [SSO-metoden](what-is-single-sign-on.md) när du konfigurerar dina program.

###  <a name="working-with-other-types-of-applications"></a>Arbeta med andra typer av program

Azure AD Programproxy också stöd för program som har utvecklats för att använda [Microsoft Authentication Library (MSAL).](../develop/v2-overview.md) Den stöder interna klientappar genom att använda Azure AD-utfärdade token som tas emot i huvudinformationen för klientbegäran för att utföra förautentisering för användarnas räkning.

Läs [publicera interna och mobila klientappar och](./application-proxy-configure-native-client-application.md) [anspråksbaserade program om](./application-proxy-configure-for-claims-aware-applications.md) du vill veta mer om tillgängliga konfigurationer av Programproxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Använd villkorsstyrd åtkomst för att stärka säkerheten

Programsäkerhet kräver en avancerad uppsättning säkerhetsfunktioner som kan skydda mot och svara på komplexa hot lokalt och i molnet. Angripare får oftast åtkomst till företagets nätverk via svaga, standard eller stulna användarautentiseringsuppgifter.  Microsofts identitetsdrivna säkerhet minskar användningen av stulna autentiseringsuppgifter genom att hantera och skydda både privilegierade och icke-privilegierade identiteter.

Följande funktioner kan användas för att stödja Azure AD-Programproxy:

* Användar- och platsbaserad villkorlig åtkomst: Skydda känsliga data genom att begränsa användaråtkomsten baserat på geografisk plats eller en IP-adress med platsbaserade principer [för villkorsstyrd åtkomst.](../conditional-access/location-condition.md)

* Enhetsbaserad villkorlig åtkomst: Se till att endast registrerade, godkända och kompatibla enheter kan komma åt företagsdata [med enhetsbaserad villkorlig åtkomst.](../conditional-access/require-managed-devices.md)

* Programbaserad villkorlig åtkomst: Arbetet behöver inte stoppas när en användare inte finns i företagsnätverket. [Skydda åtkomsten till företagets moln och lokala appar och behåll](../conditional-access/app-based-conditional-access.md) kontrollen med villkorlig åtkomst.

* Riskbaserad villkorsstyrd åtkomst: Skydda dina data från hackare med skadligt uppsåt med en [riskbaserad](https://www.microsoft.com/cloud-platform/conditional-access) princip för villkorsstyrd åtkomst som kan tillämpas på alla appar och alla användare, oavsett om de finns lokalt eller i molnet.

* Azure AD Mina appar: När din Programproxy-tjänst har distribuerats och program publiceras på ett säkert sätt kan du erbjuda användarna en enkel hubb för att identifiera och komma åt alla sina program. Öka produktiviteten med självbetjäningsfunktioner, till exempel möjligheten att begära åtkomst till nya appar och grupper eller hantera åtkomst till dessa resurser för andras räkning, [via Mina appar](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Hantera implementeringen

### <a name="required-roles"></a>Nödvändiga roller

Microsoft rekommenderar principen att bevilja minsta möjliga behörighet för att utföra nödvändiga uppgifter med Azure AD. [Granska de olika Azure-roller som är](../roles/permissions-reference.md) tillgängliga och välj rätt för att hantera behoven för varje person. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Affärsroll| Affärsuppgifter| Azure AD-roller |
|---|---|---|
| Supportavdelningsadministratör | Vanligtvis begränsat till kvalificerande slutanvändarrapporterade problem och utförande av begränsade uppgifter som att ändra användarnas lösenord, ogiltigförklara uppdateringstoken och övervaka tjänstens hälsa. | Supportadministratör |
| Identitetsadministratör| Läs Azure AD-inloggningsrapporter och granskningsloggar för att felsöka appproxyrelaterade problem.| Säkerhetsläsare |
| Programägare| Skapa och hantera alla aspekter av företagsprogram, programregistreringar och programproxyinställningar.| Programadministratör |
| Infrastrukturadministratör | Ägare av certifikatsrover | Programadministratör |

Att minimera antalet personer som har åtkomst till säker information eller resurser bidrar till att minska risken för att en obehörig aktör får obehörig åtkomst eller att en behörig användare oavsiktligt påverkar en känslig resurs.

Användare behöver dock fortfarande utföra dagliga privilegierade åtgärder, så att framtvinga JIT-baserade (just-in-time) [Privileged Identity Management-principer](../privileged-identity-management/pim-configure.md) för att ge privilegierad åtkomst på begäran till Azure-resurser och Azure AD är vår rekommenderade metod för att effektivt hantera administrativ åtkomst och granskning.

### <a name="reporting-and-monitoring"></a>Rapportering och övervakning

Azure AD ger ytterligare insikter om din organisations programanvändning och drifthälsa via [granskningsloggar och rapporter.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Programproxy gör det också mycket enkelt att övervaka anslutningsappar från Azure AD-portalen och Windows-händelseloggar.

#### <a name="application-audit-logs"></a>Granskningsloggar för program

Dessa loggar innehåller detaljerad information om inloggningar till program som konfigurerats med Programproxy och enheten och användaren som har åtkomst till programmet. [Granskningsloggar](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) finns i Azure Portal och i [Gransknings-API](/graph/api/resources/directoryaudit) för export. Dessutom är [användnings- och insiktersrapporter](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) också tillgängliga för ditt program.

#### <a name="application-proxy-connector-monitoring"></a>Programproxy connector-övervakning

Anslutningsapparna och tjänsten tar hand om alla uppgifter för hög tillgänglighet. Du kan övervaka statusen för dina anslutningsappar från Programproxy i Azure AD-portalen. Mer information om underhåll av anslutningsappar finns [i Förstå Azure AD Programproxy Connectors](./application-proxy-connectors.md#maintenance).

![Exempel: Azure AD Programproxy anslutningsappar](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-händelseloggar och prestandaräknare

Anslutningsappar har både administratörs- och sessionsloggar. Administratörsloggarna innehåller viktiga händelser och deras fel. Sessionsloggarna innehåller alla transaktioner och deras bearbetningsinformation. Loggar och räknare finns i Windows-händelseloggar för mer information i [Förstå Azure AD Programproxy Connectors](./application-proxy-connectors.md#under-the-hood). Följ den här [självstudien för att konfigurera händelseloggdatakällor i Azure Monitor](../../azure-monitor/agents/data-sources-windows-events.md).

### <a name="troubleshooting-guide-and-steps"></a>Felsökningsguide och steg

Läs mer om vanliga problem och hur du löser dem med vår guide för [felsökning](application-proxy-troubleshoot.md) av felmeddelanden.

Följande artiklar innehåller vanliga scenarier som också kan användas för att skapa felsökningsguider för din supportorganisation.

* [Problem med att visa appsida](application-proxy-page-appearance-broken-problem.md)
* [För lång programinläsning](application-proxy-page-load-speed-problem.md)
* [Länkar på programsidan fungerar inte](application-proxy-page-links-broken-problem.md)
* [Vilka portar ska jag öppna för min app?](application-proxy-add-on-premises-application.md)
* [Ingen fungerande anslutning i en grupp med anslutningar för min app](application-proxy-connectivity-no-working-connector.md)
* [Konfigurera i administratörsportalen](application-proxy-config-how-to.md)
* [Konfigurera enkel inloggning till min app](application-proxy-config-sso-how-to.md)
* [Problem med att skapa en app i administratörsportalen](application-proxy-config-problem.md)
* [Konfigurera begränsad Kerberos-delegering](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurera med PingAccess](./application-proxy-ping-access-publishing-guide.md)
* [Fel: Det går inte att få åtkomst till det här företagsprogrammet](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problem med att installera anslutningsappen för programproxyagenten](application-proxy-connector-installation-problem.md)
* [Inloggningsproblem](application-sign-in-problem-on-premises-application-proxy.md)
