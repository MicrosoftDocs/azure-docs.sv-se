---
title: Planera en Azure Active Directory-programproxy distribution
description: En komplett guide för att planera distributionen av programproxyn i din organisation
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: c08ce3bc8b722f2ea417c854a606f1060aa174e3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580055"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planera en distribution av Azure AD-programproxy

Azure Active Directory (Azure AD) Application Proxy är en säker och kostnads effektiv lösning för fjärråtkomst för lokala program. Det ger en omedelbar över gångs Sök väg för organisationer med "molnets första" för att hantera åtkomst till äldre lokala program som ännu inte kan använda moderna protokoll. Mer inledande information finns i [Vad är Application Proxy](./application-proxy.md).

Application Proxy rekommenderas för att ge fjärran vändare åtkomst till interna resurser. Programproxyn ersätter behovet av en VPN-eller omvänd proxy för dessa användnings fall för fjärråtkomst. Den är inte avsedd för användare som finns i företags nätverket. Dessa användare som använder programproxy för åtkomst till intranät kan drabbas av oönskade prestanda problem.

Den här artikeln innehåller de resurser som du behöver för att planera, hantera och hantera Azure-AD-programproxy.

## <a name="plan-your-implementation"></a>Planera implementeringen

I följande avsnitt får du en översikt över de viktigaste planerings elementen som du kan använda för att få en effektiv distribution.

### <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du påbörjar implementeringen. Du kan se mer information om hur du konfigurerar din miljö, inklusive dessa krav, i den här [självstudien](application-proxy-add-on-premises-application.md).

* **Kopplingar**: anslutningar är lättare som du kan distribuera till:
   * Fysisk maskin vara lokalt
   * En virtuell dator som finns i en hypervisor-lösning
   * En virtuell dator som finns i Azure för att aktivera utgående anslutning till Application Proxy-tjänsten.

* Se [förstå Azure AD App proxy-anslutningar](application-proxy-connectors.md) för en mer detaljerad översikt.

     * Anslutnings datorer måste [vara aktiverade för TLS 1,2](application-proxy-add-on-premises-application.md) innan anslutningarna installeras.

     * Om möjligt kan du distribuera kopplingar i [samma nätverk](application-proxy-network-topology.md) och segment som backend-webbprogram-servrarna. Det är bäst att distribuera kopplingar när du har slutfört en identifiering av program.
     * Vi rekommenderar att varje kopplings grupp har minst två kopplingar för att ge hög tillgänglighet och skalning. Att ha tre anslutningar är optimalt om du kan behöva underhålla en dator när som helst. Granska [tabellen med kopplings kapacitet](./application-proxy-connectors.md#capacity-planning) för att avgöra vilken typ av dator som anslutningar ska installeras på. Ju större datorn desto mer buffert och att utföra anslutnings tjänsten är.

* **Nätverks åtkomst inställningar**: Azure AD-programproxy-anslutningar [ansluter till Azure via https (TCP-port 443) och http (tcp-port 80)](application-proxy-add-on-premises-application.md).

   * Det finns inte stöd för att avsluta TLS-trafik för anslutning och förhindrar att anslutningar upprättar en säker kanal till sina respektive Azure App proxy-slutpunkter.

   * Undvik alla former av infogad kontroll på utgående TLS-kommunikation mellan anslutningar och Azure. Intern inspektion mellan en anslutnings-och backend-program är möjlig, men det kan försämra användar upplevelsen och därför rekommenderas det inte.

   * Belastnings utjämning för själva anslutningarna stöds inte heller eller ens nödvändigt.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Viktiga överväganden innan du konfigurerar Azure-AD-programproxy

Följande grund krav måste uppfyllas för att du ska kunna konfigurera och implementera Azure-AD-programproxy.

*  **Azure onboarding**: innan du distribuerar programproxyn måste användar identiteter synkroniseras från en lokal katalog eller skapas direkt i dina Azure AD-klienter. Identitetssynkronisering gör det möjligt för Azure AD att förautentisera användare innan de beviljar åtkomst till App proxy-publicerade program och att ha nödvändig information om användar-ID för att utföra enkel inloggning (SSO).

* **Krav för villkorlig åtkomst**: Vi rekommenderar inte att du använder programproxy för intranäts åtkomst eftersom detta lägger till latens som påverkar användarna. Vi rekommenderar att du använder Application Proxy med förautentisering och principer för villkorlig åtkomst för fjärråtkomst från Internet.  En metod för att ge villkorlig åtkomst för intranät användning är att modernisera program så att de kan autentiseras direkt med AAD. Mer information hittar du i [resurser för att migrera program till AAD](./migration-resources.md) .

* **Tjänst begränsningar**: för att skydda mot överförbrukning av resurser av enskilda klienter finns det begränsade gränser som anges per program och klient. För att se de här begränsningarna refererar du till [Azure AD-tjänstens gränser och begränsningar](../enterprise-users/directory-service-limits-restrictions.md). Dessa begränsnings gränser baseras på ett riktmärke som ligger över normal användnings volym och ger en stor buffert för en majoritet av distributionerna.

* **Offentligt certifikat**: om du använder anpassade domän namn måste du skaffa ett TLS/SSL-certifikat. Det kan ta en stund att hämta ett certifikat, beroende på organisationens krav, och vi rekommenderar att du startar processen så tidigt som möjligt. Azure Application Proxy stöder standardattribut, [jokertecken](application-proxy-wildcard.md)eller San-baserade certifikat. Mer information finns i [Konfigurera anpassade domäner med Azure AD-programproxy](application-proxy-configure-custom-domain.md).

* **Domän krav**: enkel inloggning till dina publicerade program med hjälp av Kerberos-begränsad delegering (KCD) kräver att servern som kör anslutningen och servern som kör appen är domänanslutna och delar av samma domän eller domäner med förtroende.
Detaljerad information om avsnittet finns i [KCD för enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md) med programproxy. Kopplings tjänsten körs i kontexten för det lokala systemet och ska inte konfigureras för användning av en anpassad identitet.

* **DNS-poster för URL: er**

   * Innan du använder anpassade domäner i Application Proxy måste du skapa en CNAME-post i offentlig DNS, så att klienterna kan matcha den anpassade, definierade externa webb adressen till den fördefinierade programproxy-adressen. Om du inte skapar en CNAME-post för ett program som använder en anpassad domän kan fjärran vändare inte ansluta till programmet. De steg som krävs för att lägga till CNAME-poster kan variera från DNS-leverantör till provider, så lär dig hur du [hanterar DNS-poster och post uppsättningar med hjälp av Azure Portal](../../dns/dns-operations-recordsets-portal.md).

   * På samma sätt måste anslutnings värdar kunna matcha den interna URL: en för program som publiceras.

* **Administrativa rättigheter och roller**

   * **Anslutnings installationen** kräver lokal administratörs behörighet till den Windows Server som den är installerad på. Det krävs också minst en *program administratörs* roll för att autentisera och registrera anslutnings instansen till din Azure AD-klient.

   * Program **publicering och administration** kräver rollen *program administratör* . Program administratörer kan hantera alla program i katalogen, inklusive registreringar, SSO-inställningar, användar-och grupp tilldelningar och licensiering, inställningar för programproxy och medgivande. Det ger inte möjlighet att hantera villkorlig åtkomst. Rollen som *moln program administratör* har alla funktioner i program administratören, förutom att den inte tillåter hantering av programproxy-inställningar.

* **Licensiering**: Application Proxy är tillgängligt via en Azure AD Premium-prenumeration. På sidan med [Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/) finns en fullständig lista över licensierings alternativ och-funktioner.

### <a name="application-discovery"></a>Program identifiering

Kompilera en inventering av alla omfångs program som publiceras via programproxyn genom att samla in följande information:

| Informations typ| Information som ska samlas in |
|---|---|
| Typ av tjänst| Exempel: SharePoint, SAP, CRM, anpassat webb program, API |
| Program plattform | Exempel: Windows IIS, Apache på Linux, Tomcat, NGINX |
| Domänmedlemskap| Webb serverns fullständigt kvalificerade domän namn (FQDN) |
| Program plats | Där webb servern eller Server gruppen finns i din infrastruktur |
| Intern åtkomst | Den exakta URL: en som används vid åtkomst till programmet internt. <br> Om en Server grupp, vilken typ av belastnings utjämning används? <br> Om programmet ritar innehåll från andra källor än sig själv.<br> Ta reda på om programmet fungerar över WebSockets. |
| Extern åtkomst | Leverantörs lösningen som programmet redan exponeras för externt. <br> Den URL som du vill använda för extern åtkomst. Om SharePoint, se till att alternativa åtkomst mappningar konfigureras enligt [den här vägledningen](/SharePoint/administration/configure-alternate-access-mappings). Om inte, måste du definiera externa URL: er. |
| Offentligt certifikat | Om du använder en anpassad domän kan du skaffa ett certifikat med ett motsvarande ämnes namn. om ett certifikat finns noterar du serie numret och platsen varifrån det kan hämtas. |
| Autentiseringstyp| Den typ av autentisering som stöds av program stödet, till exempel Basic, Windows integration-autentisering, formulärbaserade, huvudbaserade och anspråk. <br>Om programmet är konfigurerat för att köras under ett speciellt domän konto noterar du det fullständigt kvalificerade domän namnet (FQDN) för tjänst kontot.<br> Om SAML-baserade URL-adresser och svars-URL: er. <br> Om det är ett sidhuvud baserat, leverantörs lösning och särskilt krav för hantering av autentiseringstyp. |
| Anslutnings gruppens namn | Det logiska namnet för den grupp med anslutningar som ska utses för att tillhandahålla överföring och SSO till detta Server dels program. |
| Åtkomst till användare/grupper | De användare eller användar grupper som ska beviljas extern åtkomst till programmet. |
| Ytterligare krav | Observera eventuella ytterligare fjärråtkomst-eller säkerhets krav som bör delas av programmet. |

Du kan ladda ned [kalkyl bladet för program inventering](https://aka.ms/appdiscovery) för att inventera dina appar.

### <a name="define-organizational-requirements"></a>Definiera organisations krav

Följande är områden som du bör definiera organisationens affärs krav för. Varje-arean innehåller exempel på krav

 **Åtkomst**

* Fjärran vändare med domänanslutna eller Azure AD-anslutna enheter användare kan komma åt publicerade program säkert med sömlös enkel inloggning (SSO).

* Fjärranslutna användare med godkända personliga enheter kan komma åt publicerade program på ett säkert sätt förutsatt att de har registrerats i MFA och har registrerat Microsoft Authenticator-appen på sin mobil telefon som autentiseringsmetod.

**Styrning**

* Administratörer kan definiera och övervaka livs cykeln för användar tilldelningar till program som publicerats via Application Proxy.

**Säkerhet**

* Endast användare som är tilldelade till program via grupp medlemskap eller individuellt kan komma åt dessa program.

**Prestanda**

* Det finns ingen försämring av program prestandan jämfört med åtkomst till programmet från det interna nätverket.

**Användar upplevelse**

* Användarna är medvetna om hur de kommer åt sina program med hjälp av välbekanta företags-URL: er på valfri enhets plattform.

**Granskning**
* Administratörer kan granska användar åtkomst aktiviteten.


### <a name="best-practices-for-a-pilot"></a>Metod tips för en pilot

Ta reda på hur lång tid och arbete som krävs för att fullständigt få ett enda program för fjärråtkomst med enkel inloggning (SSO). Gör detta genom att köra en pilot som beaktar den inledande identifieringen, publiceringen och den allmänna testningen. Genom att använda ett enkelt IIS-baserat webb program som redan är förkonfigurerat för integrerad Windows-autentisering (IWA) kan du skapa en bas linje, eftersom den här installationen kräver minimala ansträngningar för att kunna leda till pilot för fjärråtkomst och SSO.

Följande design element bör öka framgången av pilot implementeringen direkt i en produktions klient.

**Anslutnings hantering**:

* Kopplingarna spelar en viktig roll när det gäller att tillhandahålla dina program lokalt. Att använda **standard** anslutnings gruppen är tillräckligt för inledande pilot testning av publicerade program innan du informerar dem i produktion. Program som har testats kan sedan flyttas till produktions kopplings grupper.

**Program hantering**:

* Din personal är mest sannolik att komma ihåg att en extern URL är välbekant och relevant. Undvik att publicera ditt program med hjälp av våra fördefinierade msappproxy.net-eller onmicrosoft.com-suffix. Ange i stället en välbekant domän som verifieras på den översta nivån, som föregås av ett logiskt värdnamn, till exempel *intranät. <customers_domain>. com*.

* Begränsa visningen av pilot programmets ikon till en pilot grupp genom att dölja dess start ikon formulär portalen för Azure-appar. När du är redo för produktion kan du begränsa appen till dess respektive mål grupp, antingen i samma för produktions innehavare, eller genom att även publicera programmet i produktions klient organisationen.

**Inställningar för enkel inloggning**: vissa SSO-inställningar har speciella beroenden som kan ta tid att konfigurera, så undvik ändrings kontrollens fördröjning genom att se till att beroenden behandlas i förväg. Detta inkluderar domän anslutning till anslutnings värdar för att utföra SSO med Kerberos-begränsad delegering (KCD) och ta hand om andra tids krävande aktiviteter. Du kan till exempel ställa in en PING-serverinstans om du behöver ett sidhuvud baserat SSO.

**TLS mellan kopplings värd och mål program**: säkerheten är ytterst viktigt, så TLS mellan kopplings värden och mål program bör alltid användas. I synnerhet om webb programmet har kon figurer ATS för formulärbaserad autentisering (FBA) överförs användarens autentiseringsuppgifter i klartext.

**Implementera stegvis och testa varje steg**.
Utför grundläggande funktions testning när du har publicerat ett program för att säkerställa att alla krav på användare och företag uppfylls genom att följa anvisningarna nedan:

1. Testa och verifiera allmän åtkomst till webb programmet med förautentisering inaktive rad.
2. Om det lyckas aktiverar du förautentisering och tilldelar användare och grupper. Testa och verifiera åtkomst.
3. Lägg sedan till SSO-metoden för ditt program och testa igen för att verifiera åtkomst.
4. Använd villkorlig åtkomst och MFA-principer efter behov. Testa och verifiera åtkomst.

**Fel söknings verktyg**: när du felsöker kan du alltid starta genom att verifiera åtkomst till det publicerade programmet från webbläsaren på kopplings värden och bekräfta att programmet fungerar som förväntat. Det enklare att konfigurera, vilket är enklare att ta reda på rotor saken, så du bör försöka återskapa problem med en minimal konfiguration, till exempel bara använda en enda koppling och ingen SSO. I vissa fall kan webb fel söknings verktyg, till exempel Teleriks Fiddler, bevisa att de kan felsöka åtkomst-eller innehålls problem i program som nås via en proxy. Fiddler kan också fungera som en proxy för att spåra och felsöka trafik för mobila plattformar som iOS och Android, och nästan allt som kan konfigureras för att dirigera via en proxy. Mer information finns i [fel söknings guiden](application-proxy-troubleshoot.md) .

## <a name="implement-your-solution"></a>Implementera din lösning

### <a name="deploy-application-proxy"></a>Distribuera programproxy

Stegen för att distribuera programproxyn beskrivs i den här [självstudien för att lägga till ett lokalt program för fjärråtkomst](application-proxy-add-on-premises-application.md). Om installationen inte lyckas väljer du  **Felsök tillämpningsproxy**  i portalen eller använder fel söknings guiden [för problem med att installera Application Proxy agent-anslutningsprogrammet](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publicera program via Application Proxy

Publicerings program förutsätter att du har uppfyllt alla krav och att du har flera kopplingar som visas som registrerade och aktiva på sidan Application Proxy.

Du kan också publicera program med hjälp av [PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview).

Nedan följer några metod tips som du bör följa när du publicerar ett program:

* **Använd anslutnings grupper**: tilldela en anslutnings grupp som har angetts för publicering av varje program. Vi rekommenderar att varje kopplings grupp har minst två kopplingar för att ge hög tillgänglighet och skalning. Att ha tre anslutningar är optimalt om du kan behöva underhålla en dator när som helst. Se även [Publicera program i separata nätverk och platser med anslutnings grupper](application-proxy-connector-groups.md) för att se hur du kan använda anslutnings grupper för att segmentera dina anslutningar via nätverk eller plats.

* **Ange tids gräns för backend-program**: den här inställningen är användbar i scenarier där programmet kan kräva mer än 75 sekunder att bearbeta en klient transaktion. Till exempel när en klient skickar en fråga till ett webb program som fungerar som en klient del till en databas. Klient delen skickar den här frågan till backend-databasservern och väntar på ett svar, men när den tar emot ett svar är klient sidan av konversationen tids gräns. Om du anger tids gränsen till Long får 180 sekunder för längre transaktioner att slutföras.

* **Använd lämpliga cookie-typer**

   * **Http-endast cookie**: ger ytterligare säkerhet genom att låta programproxyn inkludera flaggan HTTPONLY i HTTP-svarshuvuden för set-cookie. Den här inställningen hjälper till att minimera utnyttjandet, till exempel Cross-Site Scripting (XSS). Lämna den här inställningen till Nej för klienter/användar agenter som behöver åtkomst till sessions-cookien. Till exempel RDP/MTSC-klienten som ansluter till en Fjärrskrivbordsgateway som publicerats via App proxy.

   * **Säker cookie**: när en cookie anges med attributet Secure, kommer användar agenten (app på klient sidan) bara att inkludera cookien i HTTP-begäranden om begäran överförs via en TLS-skyddad kanal. På så sätt kan du minska risken för att en cookie komprometteras över rensade text kanaler, så bör vara aktive rad.

   * **Beständig cookie**: tillåter att programproxy-sessionens cookie behålls mellan webbläsarens stängningar genom att det blir kvar, tills den antingen upphör att gälla eller tas bort. Används för scenarier där ett omfattande program, till exempel Office, använder ett dokument i ett publicerat webb program, utan att användaren uppmanas att ange autentisering igen. Gör det dock möjligt att, eftersom beständiga cookies, i slut ändan lämna en tjänst till risk för obehörig åtkomst, om de inte används tillsammans med andra kompensations kontroller. Den här inställningen bör endast användas för äldre program som inte kan dela cookies mellan processer. Det är bättre att uppdatera ditt program för att hantera delning av cookies mellan processer i stället för att använda den här inställningen.

* **Översätt URL: er i huvuden**: du aktiverar detta för scenarier där intern DNS inte kan konfigureras för att matcha organisationens offentliga namn område (a. k. en delad DNS). Om programmet inte kräver den ursprungliga värd rubriken i klient förfrågan, lämna värdet inställt på Ja. Alternativet är att använda det fullständiga domän namnet i den interna URL: en för routning av den faktiska trafiken och FQDN i den externa URL: en som värd huvud. I de flesta fall bör det här alternativet tillåta att programmet fungerar som normalt, vid fjärråtkomst via fjärr anslutning, men användarna förlorar fördelarna med att ha en matchande i & utanför URL: en.

* **Översätt URL: er i program text**: Aktivera länk översättning för program brödtext för en app när du vill att länkarna från appen ska översättas i svar tillbaka till klienten. Om den här funktionen är aktive rad tillhandahåller den här funktionen ett bästa försök att översätta alla interna länkar som App proxy hittar i HTML-och CSS-svar som returneras till klienter. Det är användbart när du publicerar appar som innehåller antingen hårdkodade absoluta eller NetBIOS-ShortName-länkar i innehållet, eller appar med innehåll som länkar till andra lokala program.

För scenarier där en publicerad app länkar till andra publicerade appar, aktivera länk översättning för varje program så att du har kontroll över användar upplevelsen på nivån per app.

Anta till exempel att du har tre program som publicerats via programproxyn som alla länkar till varandra: förmåner, utgifter och resor, plus en fjärde app, feedback, som inte har publicerats via programproxyn.

![Bild 1](media/App-proxy-deployment-plan/link-translation.png)

När du aktiverar länk översättning för Benefits-appen omdirigeras länkarna till utgifter och resor till de externa URL: erna för dessa appar, så att användare som kommer åt programmen utanför företags nätverket har åtkomst till dem. Länkar från kostnader och tillbaka till förmåner fungerar inte eftersom länk översättning inte har Aktiver ATS för de två apparna. Länken till feedback omdirigeras inte eftersom det inte finns någon extern URL, så användare som använder appen förmåner kommer inte att kunna komma åt appen för feedback från utanför företags nätverket. Se detaljerad information om [länk översättning och andra alternativ för omdirigering](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Få åtkomst till ditt program

Det finns flera alternativ för att hantera åtkomst till publicerade resurser för App-proxy, så välj den lämpligaste för ditt scenario och skalbarhets behov. Vanliga metoder är: använda lokala grupper som synkroniseras via Azure AD Connect, skapa dynamiska grupper i Azure AD baserat på användarattribut, med hjälp av självbetjänings grupper som hanteras av en resurs ägare eller en kombination av dessa. Se de länkade resurserna för fördelarna med var och en.

Det vanligaste sättet att tilldela användare åtkomst till ett program finns i alternativen **användare och grupper** i den vänstra rutan i det publicerade programmet och tilldelar grupper eller individer direkt.

![Bild 24](media/App-proxy-deployment-plan/add-user.png)

Du kan också ge användare självbetjänings åtkomst till ditt program genom att tilldela en grupp som de inte är medlem i och konfigurera de fristående alternativen.

![Bild 25](media/App-proxy-deployment-plan/allow-access.png)

Om aktive rad kan användarna logga in på portalen för Mina appar och begära åtkomst, och antingen automatiskt godkännas och läggas till i den redan tillåtna gruppen för självbetjäning eller kräva godkännande från en viss god kännare.

Gäst användare kan också [uppmanas att komma åt interna program som publicerats via programproxyn via Azure AD B2B](../external-identities/add-users-information-worker.md).

För lokala program som normalt är tillgängliga anonymt, vilket kräver ingen autentisering, kanske du föredrar att inaktivera alternativet som finns i programmets **Egenskaper**.

![Bild 26](media/App-proxy-deployment-plan/assignment-required.png)


Om du lämnar det här alternativet till Nej kan användarna få åtkomst till det lokala programmet via Azure AD App proxy utan behörigheter, så Använd med försiktighet.

När ditt program har publicerats bör det vara tillgängligt genom att skriva dess externa URL i en webbläsare eller med dess ikon på [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Aktivera förautentisering

Kontrol lera att programmet kan nås via programproxyn via den externa URL: en.

1. Gå till **Azure Active Directory**  >  **företags program**  >  **alla program** och välj den app som du vill hantera.

2. Välj **programproxy**.

3. I fältet **före autentisering** använder du List rutan för att välja **Azure Active Directory** och väljer **Spara**.

När förautentisering har Aktiver ATS kommer Azure AD att utmana användare först för autentisering och om enkel inloggning har kon figurer ATS kommer Server dels programmet även att verifiera användaren innan åtkomst till programmet beviljas. Om du ändrar Förautentiserings läget från genom strömning till Azure AD konfigureras även den externa URL: en med HTTPS, så alla program som ursprungligen konfigureras för HTTP kommer nu att skyddas med HTTPS.

### <a name="enable-single-sign-on"></a>Aktivera enstaka Sign-On

SSO ger bästa möjliga användar upplevelse och säkerhet eftersom användarna bara behöver logga in en gång vid åtkomst till Azure AD. När en användare har förautentiserats utförs SSO av den Application Proxy-koppling som autentiserar sig för det lokala programmet för användarens räkning. Server dels programmet bearbetar inloggningen som om den var själva användaren.

Genom att välja alternativet **genom strömning** kan användarna få åtkomst till det publicerade programmet utan att behöva autentisera till Azure AD.

Att utföra SSO är bara möjligt om Azure AD kan identifiera den användare som begär åtkomst till en resurs, så att ditt program måste konfigureras för att förautentisera användare med Azure AD vid åtkomst för SSO till funktionen, annars inaktive ras alternativen för enkel inloggning.

Läs [enkel inloggning till program i Azure AD](what-is-single-sign-on.md) för att hjälpa dig att välja den LÄMPLIGaste SSO-metoden när du konfigurerar dina program.

###  <a name="working-with-other-types-of-applications"></a>Arbeta med andra typer av program

Azure AD-programproxy kan också stödja program som har utvecklats för att använda [Microsoft Authentication Library (MSAL)](../develop/v2-overview.md). Den har stöd för interna klient program genom att använda Azure AD-utfärdade token som tagits emot i huvud informationen för klientbegäran för att utföra förautentisering för användarens räkning.

Läs om [interna publicerings-och mobilappar och klient](./application-proxy-configure-native-client-application.md) [program och anspråksbaserad program](./application-proxy-configure-for-claims-aware-applications.md) för att lära dig mer om tillgängliga konfigurationer av programproxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Använd villkorlig åtkomst för att förstärka säkerheten

Program säkerhet kräver en avancerad uppsättning säkerhetsfunktioner som kan skydda från och svara på komplexa hot lokalt och i molnet. Angripare får oftast företags nätverket åtkomst genom svaga, standardiserade eller stulna användarautentiseringsuppgifter.  Microsofts identitets driven säkerhet minskar användningen av stulna autentiseringsuppgifter genom att hantera och skydda både privilegierade och icke-privilegierade identiteter.

Följande funktioner kan användas för att stödja Azure-AD-programproxy:

* Användar-och plats-baserad villkorlig åtkomst: skydda känsliga data genom att begränsa användar åtkomst baserat på Geo-eller IP-adresser med [platsbaserade principer för villkorlig åtkomst](../conditional-access/location-condition.md).

* Enhets-baserad villkorlig åtkomst: se till att endast registrerade, godkända och kompatibla enheter kan komma åt företags data med [enhets-baserad villkorlig åtkomst](../conditional-access/require-managed-devices.md).

* Programbaserad villkorlig åtkomst: arbetet behöver inte stoppas när en användare inte finns i företags nätverket. [Säker åtkomst till företags molnet och lokala appar](../conditional-access/app-based-conditional-access.md) och behåll kontrollen med villkorlig åtkomst.

* Riskfylld villkorlig åtkomst: skydda dina data från skadliga hackare med en [riskfylld princip för villkorlig åtkomst](https://www.microsoft.com/cloud-platform/conditional-access) som kan tillämpas på alla appar och alla användare, oavsett om de finns lokalt eller i molnet.

* Azure AD Mina appar: med tjänsten Application Proxy distribuerad och program som publicerats på ett säkert sätt kan du erbjuda användarna en enkel hubb för att upptäcka och komma åt alla sina program. Öka produktiviteten med självbetjänings funktioner, till exempel möjligheten att begära åtkomst till nya appar och grupper eller hantera åtkomst till dessa resurser å andras vägnar via [Mina appar](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Hantera din implementering

### <a name="required-roles"></a>Nödvändiga roller

Microsoft rekommenderar att du beviljar den minst möjliga behörigheten att utföra nödvändiga uppgifter med Azure AD. [Granska de olika Azure-rollerna som är tillgängliga](../roles/permissions-reference.md) och välj rätt en för att åtgärda behoven hos varje person. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Affärs roll| Affärs uppgifter| Azure AD-roller |
|---|---|---|
| Support administratör | Normalt begränsad till kvalificering av slutanvändare som har rapporterat problem och utför begränsade uppgifter, till exempel ändring av användarnas lösen ord, ogiltig verifiering av uppdateringstoken och övervakning av tjänstens hälsa. | Support administratör |
| Identitets administratör| Läs om inloggnings rapporter och gransknings loggar i Azure AD för att felsöka problem med App proxy.| Säkerhetsläsare |
| Program ägare| Skapa och hantera alla aspekter av företags program, program registreringar och programproxy-inställningar.| Program administratör |
| Infrastruktur administratör | Certifikat förnyelse ägare | Program administratör |

Att minimera antalet personer som har åtkomst till säker information eller resurser bidrar till att minska risken för att en obehörig aktör får åtkomst till obehörig åtkomst, eller att en behörig användare oavsiktligt påverkar en känslig resurs.

Användare behöver dock fortfarande utföra dagliga privilegierade åtgärder, så att du tvingas använda JIT (just-in-Time)-baserade [Privileged Identity Management](../privileged-identity-management/pim-configure.md) principer för att ge privilegie rad åtkomst på begäran till Azure-resurser och Azure AD är vår rekommenderade metod för att effektivt hantera administrativ åtkomst och granskning.

### <a name="reporting-and-monitoring"></a>Rapportering och övervakning

Azure AD ger ytterligare insikter om din organisations program användning och drifts hälsa genom [gransknings loggar och-rapporter](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Programproxyn gör det också mycket enkelt att övervaka anslutningar från Azure AD-portalen och Windows-händelseloggen.

#### <a name="application-audit-logs"></a>Granskningsloggar för program

Dessa loggar innehåller detaljerad information om inloggningar till program som kon figurer ATS med programproxy och enheten och användaren som har åtkomst till programmet. [Gransknings loggar](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) finns i Azure Portal och i [gransknings-API](/graph/api/resources/directoryaudit?view=graph-rest-beta) för export. Dessutom är [användnings-och insikts rapporter](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) också tillgängliga för ditt program.

#### <a name="application-proxy-connector-monitoring"></a>Övervakning av Application Proxy Connector

Anslutningarna och tjänsten tar hand om alla uppgifter för hög tillgänglighet. Du kan övervaka statusen för dina anslutningar från sidan Application Proxy i Azure AD-portalen. Mer information om Connector-Maintainence finns i [förstå Azure AD-programproxy-kopplingar](./application-proxy-connectors.md#maintenance).

![Exempel: Azure AD-programproxy-kopplingar](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Händelse loggar och prestanda räknare i Windows

Anslutningarna har både administratörs-och sessions loggar. Administratörs loggarna innehåller nyckel händelser och deras fel. Sessionens loggar innehåller alla transaktioner och deras bearbetnings information. Loggar och räknare finns i Windows-händelseloggen mer information finns i [förstå Azure AD-programproxy-kopplingar](./application-proxy-connectors.md#under-the-hood). Följ den här [självstudien för att konfigurera händelse logg data källor i Azure Monitor](../../azure-monitor/agents/data-sources-windows-events.md).

### <a name="troubleshooting-guide-and-steps"></a>Fel söknings guide och steg

Lär dig mer om vanliga problem och hur du löser dem med vår guide för [fel sökning av](application-proxy-troubleshoot.md) fel meddelanden.

I följande artiklar beskrivs vanliga scenarier som också kan användas för att skapa fel söknings guider för din support organisation.

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
