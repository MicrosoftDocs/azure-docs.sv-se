---
title: Förstå azure AD Programproxy anslutningsappar | Microsoft Docs
description: Lär dig mer om Azure AD Programproxy anslutningsappar.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b54e3cb3b4ce7eb8f541755df75e8d6a22eb7c2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575317"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Förstå Azure AD-anslutningsprogram för programproxy

Anslutningsappar är det som gör Azure AD Programproxy möjligt. De är enkla, enkla att distribuera och underhålla och mycket kraftfulla. Den här artikeln beskriver vad anslutningsappar är, hur de fungerar och några förslag på hur du kan optimera distributionen.

## <a name="what-is-an-application-proxy-connector"></a>Vad är en Programproxy anslutningsapp?

Anslutningsappar är lätta agenter som finns lokalt och underlättar den utgående anslutningen till Programproxy tjänsten. Anslutningsprogram måste installeras på en Windows Server som har åtkomst till serverprogrammet. Du kan ordna anslutningsappar i anslutningsgrupper, där varje grupp hanterar trafik till specifika program. Mer information om programproxy och en diagrammatisk representation av programproxyarkitekturen finns i Använda Azure AD Programproxy för att publicera lokala [appar för fjärranvändare](what-is-application-proxy.md#application-proxy-connectors)

## <a name="requirements-and-deployment"></a>Krav och distribution

För att Programproxy korrekt behöver du minst en anslutningsapp, men vi rekommenderar två eller fler för bättre återhämtning. Installera anslutningsappen på en dator som kör Windows Server 2012 R2 eller senare. Anslutningsappen måste kommunicera med Programproxy tjänsten och de lokala program som du publicerar.

### <a name="windows-server"></a>Windows-server
Du behöver en server som kör Windows Server 2012 R2 eller senare där du kan installera Programproxy anslutning. Servern måste ansluta till Programproxy i Azure och de lokala program som du publicerar.

Windows-servern måste ha TLS 1.2 aktiverat innan du installerar Programproxy anslutningsappen. Så här aktiverar du TLS 1.2 på servern:

1. Ange följande registernycklar:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starta om servern

Mer information om nätverkskraven för anslutningsservern finns i Kom igång med att [Programproxy och installera en anslutningsapp.](application-proxy-add-on-premises-application.md)

## <a name="maintenance"></a>Underhåll

Anslutningsapparna och tjänsten tar hand om alla uppgifter för hög tillgänglighet. De kan läggas till eller tas bort dynamiskt. Varje gång en ny begäran tas emot dirigeras den till en av de anslutningsappar som för närvarande är tillgängliga. Om en anslutningsapp tillfälligt inte är tillgänglig svarar den inte på den här trafiken.

Anslutningarna är tillståndslösa och har inga konfigurationsdata på datorn. De enda data som lagras är inställningarna för att ansluta tjänsten och dess autentiseringscertifikat. När de ansluter till tjänsten hämtar de alla konfigurationsdata som krävs och uppdaterar dem varannan minut.

Anslutningsappar avsöker också servern för att ta reda på om det finns en nyare version av anslutningsappen. Om en sådan hittas uppdateras anslutningsapparna själva.

Du kan övervaka dina anslutningsappar från den dator som de körs på med hjälp av händelseloggen och prestandaräknarna. Eller så kan du visa deras status Programproxy på Azure Portal:

![Exempel: Azure AD Programproxy anslutningsappar](./media/application-proxy-connectors/app-proxy-connectors.png)

Du behöver inte manuellt ta bort anslutningsappar som inte används. När en anslutningsapp körs förblir den aktiv när den ansluter till tjänsten. Oanvända anslutningsappar taggas _som inaktiva_ och tas bort efter 10 dagars inaktivitet. Om du vill avinstallera en anslutning avinstallerar du dock både Connector-tjänsten och Updater-tjänsten från servern. Ta bort tjänsten helt och hållet genom att starta om datorn.

## <a name="automatic-updates"></a>Automatiska uppdateringar

Azure AD tillhandahåller automatiska uppdateringar för alla anslutningsappar som du distribuerar. Så länge tjänsten Programproxy Connector Updater körs uppdateras anslutningsapparna automatiskt [med den senaste större versionen av](application-proxy-faq.yml#why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version-) anslutningsappen. Om du inte ser Connector Updater på servern måste du installera om anslutningstjänsten för [att](application-proxy-add-on-premises-application.md) få uppdateringar.

Om du inte vill vänta på att en automatisk uppdatering ska komma till anslutningsappen kan du göra en manuell uppgradering. Gå till [nedladdningssidan för anslutningsappen](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) på den server där anslutningsappen finns och välj **Ladda ned**. Den här processen startar en uppgradering för den lokala anslutningsappen.

För klienter med flera anslutningsappar riktar automatiska uppdateringar in sig på en anslutningsapp i taget i varje grupp för att förhindra driftstopp i din miljö.

Det kan uppstå avbrott när anslutningsappen uppdateras om:
  
- Du har bara en anslutningsapp som vi rekommenderar att du installerar en andra anslutningsapp och [skapar en anslutningsgrupp.](application-proxy-connector-groups.md) På så sätt undviker du driftstopp och ger högre tillgänglighet.  
- En anslutningsapp var mitt i en transaktion när uppdateringen påbörjades. Även om den inledande transaktionen går förlorad bör webbläsaren automatiskt försöka utföra åtgärden igen, eller så kan du uppdatera sidan. När begäran skickas igen dirigeras trafiken till en anslutningsapp för säkerhetskopiering.

Information om tidigare utgivna versioner och vilka ändringar de innehåller finns i [Programproxy versionversionshistorik](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Skapa anslutningsgrupper

Med anslutningsgrupper kan du tilldela specifika anslutningsappar för specifika program. Du kan gruppera ett antal anslutningsappar och sedan tilldela varje program till en grupp.

Anslutningsgrupper gör det enklare att hantera stora distributioner. De förbättrar också svarstiden för klienter som har program som finns i olika regioner, eftersom du kan skapa platsbaserade anslutningsgrupper för att endast betjäna lokala program.

Mer information om anslutningsgrupper finns i [Publicera program på separata nätverk och platser med hjälp av anslutningsgrupper.](application-proxy-connector-groups.md)

## <a name="capacity-planning"></a>Kapacitetsplanering

Det är viktigt att se till att du har planerat tillräckligt med kapacitet mellan anslutningsapparna för att hantera den förväntade trafikvolymen. Vi rekommenderar att varje anslutningsgrupp har minst två anslutningsappar för hög tillgänglighet och skalning. Det är optimalt att ha tre anslutningsappar om du behöver bearbeta en dator när som helst.

I allmänhet gäller att ju fler användare du har, desto större dator behöver du. Nedan visas en tabell med en översikt över volymen och förväntad svarstid som olika datorer kan hantera. Observera att allt baseras på förväntade transaktioner per sekund (TPS) i stället för av användare eftersom användningsmönstren varierar och inte kan användas för att förutsäga belastningen. Det finns även vissa skillnader beroende på storleken på svaren och svarstiden för backend-programmet – större svarsstorlekar och långsammare svarstider resulterar i en lägre maximal TPS. Vi rekommenderar också att du har ytterligare datorer så att den distribuerade belastningen över datorerna alltid ger gott om buffert. Den extra kapaciteten säkerställer att du har hög tillgänglighet och återhämtning.

|Kärnor|RAM|Förväntad svarstid (MS)-P99|Maximalt antal TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Den här datorn använde en anpassad inställning för att höja några av standardanslutningsgränserna utöver de rekommenderade inställningarna för .NET. Vi rekommenderar att du kör ett test med standardinställningarna innan du kontaktar supporten för att ändra den här gränsen för din klientorganisation.

> [!NOTE]
> Det finns ingen större skillnad i maximal TPS mellan 4, 8 och 16 kärniga datorer. Den största skillnaden mellan dem är den förväntade svarstiden.
>
> Den här tabellen fokuserar också på förväntade prestanda för en anslutning baserat på vilken typ av dator den är installerad på. Detta skiljer sig från Programproxy tjänstbegränsningar, se [Tjänstbegränsningar och begränsningar.](../enterprise-users/directory-service-limits-restrictions.md)

## <a name="security-and-networking"></a>Säkerhet och nätverk

Anslutningsappar kan installeras var som helst i nätverket så att de kan skicka begäranden till Programproxy tjänsten. Det viktiga är att datorn som kör anslutningsappen också har åtkomst till dina appar. Du kan installera anslutningsappar i företagsnätverket eller på en virtuell dator som körs i molnet. Anslutningsappar kan köras i ett perimeternätverk, även kallat en demilitariserad zon (DMZ), men det är inte nödvändigt eftersom all trafik är utgående så att nätverket förblir skyddat.

Anslutningsappar skickar endast utgående begäranden. Den utgående trafiken skickas till Programproxy och till de publicerade programmen. Du behöver inte öppna inkommande portar eftersom trafiken flödar åt båda hållen när en session har upprättats. Du behöver inte heller konfigurera inkommande åtkomst via brandväggarna.

Mer information om hur du konfigurerar utgående brandväggsregler [finns i Arbeta med befintliga lokala proxyservrar.](application-proxy-configure-connectors-with-proxy-servers.md)

## <a name="performance-and-scalability"></a>Prestanda och skalbarhet

Skalning för Programproxy-tjänsten är transparent, men skalning är en faktor för anslutningsappar. Du måste ha tillräckligt med anslutningsappar för att hantera trafiktoppar. Eftersom anslutningsappar är tillståndslösa påverkas de inte av antalet användare eller sessioner. I stället svarar de på antalet begäranden och deras nyttolaststorlek. Med standardwebbtrafik kan en genomsnittlig dator hantera några tusen begäranden per sekund. Den specifika kapaciteten beror på de exakta datoregenskaperna.

Anslutningsprestandan är bunden av CPU och nätverk. Processorprestanda krävs för TLS-kryptering och -dekryptering, medan nätverk är viktigt för att få snabba anslutningar till programmen och onlinetjänsten i Azure.

Minnet är däremot mindre av ett problem för anslutningsappar. Onlinetjänsten tar hand om mycket av bearbetningen och all oauticerad trafik. Allt som kan göras i molnet görs i molnet.

Om anslutningen eller datorn av någon anledning blir otillgänglig börjar trafiken gå till en annan anslutningsapp i gruppen. Den här återhämtningen är också anledningen till att vi rekommenderar att du har flera anslutningsappar.

En annan faktor som påverkar prestandan är kvaliteten på nätverket mellan anslutningsapparna, inklusive:

- **Onlinetjänsten :** Anslutningar med långsam eller lång svarstid till Programproxy i Azure påverkar anslutningsappens prestanda. För bästa prestanda kan du ansluta din organisation till Azure med Express Route. Annars bör nätverksteamet se till att anslutningarna till Azure hanteras så effektivt som möjligt.
- **Backend-programmen:** I vissa fall finns det ytterligare proxys mellan anslutningsprogram och backend-program som kan sakta ned eller förhindra anslutningar. Om du vill felsöka det här scenariot öppnar du en webbläsare från anslutningsservern och försöker komma åt programmet. Om du kör anslutningsapparna i Azure men programmen är lokala kanske upplevelsen inte är vad användarna förväntar sig.
- **Domänkontrollanterna:** Om anslutningarna utför enkel inloggning (SSO) med Kerberos-begränsad delegering, kontaktar de domänkontrollanterna innan begäran skickas till backend-enheten. Anslutningsapparna har ett cacheminne med Kerberos-biljetter, men i en upptagen miljö kan domänkontrollanterna påverka prestandan. Det här problemet är vanligare för anslutningsappar som körs i Azure men som kommunicerar med lokala domänkontrollanter.

Mer information om hur du optimerar nätverket finns i Överväganden [för nätverkstopologi när du använder Azure Active Directory Programproxy](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Domän-anslutning

Anslutningsappar kan köras på en dator som inte är domän-ansluten. Men om du vill använda enkel inloggning (SSO) för program som använder Integrerad Windows-autentisering (IWA) behöver du en domänkopplad dator. I det här fallet måste anslutningsdatorerna vara ansluten till en domän som kan utföra [Kerberos-begränsad](https://web.mit.edu/kerberos) delegering åt användarna för de publicerade programmen.

Anslutningsappar kan också anslutas till domäner i skogar som har ett partiellt förtroende eller till skrivskyddade domänkontrollanter.

## <a name="connector-deployments-on-hardened-environments"></a>Distributioner av anslutningsappar i härdade miljöer

Distributionen av anslutningarna är vanligtvis enkel och kräver ingen särskild konfiguration. Det finns dock vissa unika villkor som bör övervägas:

- Organisationer som begränsar den utgående trafiken måste [öppna de portar som krävs.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- FIPS-kompatibla datorer kan behöva ändra sin konfiguration så att anslutningsprocesserna kan generera och lagra ett certifikat.
- Organisationer som låser sin miljö baserat på de processer som utfärdar nätverksbegäranden måste se till att båda anslutningstjänster är aktiverade för att få åtkomst till alla nödvändiga portar och IP-adresser.
- I vissa fall kan utgående vidarebefordrande proxyer bryta den tvåvägscertifikatautentisering och orsaka att kommunikationen misslyckas.

## <a name="connector-authentication"></a>Autentisering av anslutningsapp

För att tillhandahålla en säker tjänst måste anslutningsappar autentisera mot tjänsten och tjänsten måste autentisera mot anslutningstjänsten. Den här autentiseringen görs med klient- och servercertifikat när anslutningarna initierar anslutningen. På så sätt lagras inte administratörens användarnamn och lösenord på anslutningsdatorn.

De certifikat som används är specifika för Programproxy tjänsten. De skapas under den första registreringen och förnyas automatiskt av anslutningsapparna varannan månad.

Efter den första lyckade certifikatförnyelsen har Azure AD Programproxy Connector-tjänsten (nätverkstjänsten) inte behörighet att ta bort det gamla certifikatet från det lokala datorarkivet. Om certifikatet har upphört att gälla eller inte kommer att användas av tjänsten längre kan du ta bort det på ett säkert sätt.

Se till att nätverkskommunikationen från anslutningsappen till [](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) de dokumenterade destinationerna är aktiverad för att undvika problem med certifikatförnyelsen.

Om en anslutningsapp inte är ansluten till tjänsten på flera månader kan dess certifikat vara inaktuella. I det här fallet avinstallerar och installerar du om anslutningsappen för att utlösa registrering. Du kan köra följande PowerShell-kommandon:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

För myndigheter använder du `-EnvironmentName "AzureUSGovernment"` . Mer information finns i [Installera agent för Azure Government Cloud](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).

Mer information om hur du verifierar certifikatet och felsöker problem finns i Verify [Machine and backend components support for Programproxy trust certificate](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Under huven

Anslutningsappar baseras på Windows Server-Programproxy, så de har de flesta av samma hanteringsverktyg, inklusive Windows-händelseloggar

![Hantera händelseloggar med Loggboken](./media/application-proxy-connectors/event-view-window.png)

och Windows-prestandaräknare.

![Lägga till räknare i anslutningsappen med prestandaövervakaren](./media/application-proxy-connectors/performance-monitor.png)

Anslutningsapparna har både **administratörs-** och **sessionsloggar.** **Administratörsloggen** innehåller viktiga händelser och deras fel. **Sessionsloggen** innehåller alla transaktioner och deras bearbetningsinformation.

Om du vill se loggarna öppnar **Loggboken** går du till **Program- och tjänstloggar**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector.** Om du vill **göra sessionsloggen** synlig går du **till** menyn Visa och väljer **Visa analytiska loggar och felsökningsloggar.** **Sessionsloggen** används vanligtvis för felsökning och är inaktiverad som standard. Aktivera den för att börja samla in händelser och inaktivera den när den inte längre behövs.

Du kan undersöka tjänstens tillstånd i fönstret Tjänster. Anslutningsappen består av två Windows-tjänster: den faktiska anslutningsappen och uppdateringsappen. Båda måste köras hela tiden.

 ![Exempel: Fönstret Tjänster som visar lokala Azure AD-tjänster](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Nästa steg

- [Publicera program på separata nätverk och platser med hjälp av anslutningsgrupper](application-proxy-connector-groups.md)
- [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
- [Felsöka Programproxy och anslutningsfel](application-proxy-troubleshoot.md)
- [Så här installerar du Azure AD Programproxy Connector tyst](application-proxy-register-connector-powershell.md)
