---
title: Förstå Azure AD-programproxy-kopplingar | Microsoft Docs
description: Lär dig mer om Azure AD-programproxy-kopplingar.
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
ms.openlocfilehash: f57d390ed71cb4e0e76972e02170afde7b13e4ae
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253467"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Förstå Azure AD-programproxy-kopplingar

Anslutningar är vad som gör Azure AD-programproxy möjligt. De är enkla, enkla att distribuera och underhålla och super kraftfulla. I den här artikeln beskrivs vilka kopplingar som är, hur de fungerar och några förslag på hur du kan optimera distributionen.

## <a name="what-is-an-application-proxy-connector"></a>Vad är en Application Proxy Connector?

Anslutningar är lätta för enkla agenter som är lokala och underlättar den utgående anslutningen till Application Proxy-tjänsten. Anslutningarna måste installeras på en Windows-Server som har åtkomst till backend-programmet. Du kan organisera kopplingar i anslutnings grupper med varje grupp som hanterar trafik till vissa program. Mer information om programproxy och en diagramvy-representation av Application Proxy-arkitektur finns i [använda Azure AD-programproxy för att publicera lokala appar för fjärran vändare](what-is-application-proxy.md#application-proxy-connectors)

## <a name="requirements-and-deployment"></a>Krav och distribution

Om du ska kunna distribuera programproxyn måste du ha minst en anslutning, men vi rekommenderar två eller fler för större återhämtning. Installera anslutningen på en dator som kör Windows Server 2012 R2 eller senare. Anslutningen måste kommunicera med Application Proxy-tjänsten och de lokala program som du publicerar.

### <a name="windows-server"></a>Windows-server
Du behöver en server som kör Windows Server 2012 R2 eller senare och som du kan installera Application Proxy Connector på. Servern måste ansluta till Application Proxy-tjänsterna i Azure och de lokala program som du publicerar.

Windows Server måste ha TLS 1,2 aktiverat innan du installerar Application Proxy-anslutningsprogrammet. Så här aktiverar du TLS 1,2 på servern:

1. Ange följande registernycklar:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starta om servern

Mer information om nätverks kraven för anslutnings servern finns i [Kom igång med Application Proxy och installera en anslutning](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Underhåll

Anslutningarna och tjänsten tar hand om alla uppgifter för hög tillgänglighet. De kan läggas till eller tas bort dynamiskt. Varje gång en ny begäran tas emot dirigeras den till en av de anslutningar som för närvarande är tillgänglig. Om en anslutning tillfälligt inte är tillgänglig svarar den inte på den här trafiken.

Anslutningarna är tillstånds lösa och saknar konfigurations data på datorn. De enda data som lagras är inställningarna för att ansluta tjänsten och dess autentiseringscertifikat. När de ansluter till tjänsten hämtar de alla nödvändiga konfigurations data och uppdaterar dem varje par minuter.

Anslutningar avsöker också servern för att ta reda på om det finns en nyare version av anslutningen. Om en hittas uppdateras kopplingarna själva.

Du kan övervaka dina anslutningar från den dator som de körs på, antingen med hjälp av händelse loggen och prestanda räknarna. Du kan också visa deras status från sidan Application Proxy i Azure Portal:

![Exempel: Azure AD-programproxy-kopplingar](./media/application-proxy-connectors/app-proxy-connectors.png)

Du behöver inte ta bort kopplingar som inte används manuellt. När en anslutning körs förblir den aktiv när den ansluter till tjänsten. Oanvända kopplingar är taggade som _inaktiva_ och tas bort efter 10 dagars inaktivitet. Om du vill avinstallera en anslutning avinstallerar du både kopplings tjänsten och uppdaterings tjänsten från servern. Ta bort tjänsten helt och hållet genom att starta om datorn.

## <a name="automatic-updates"></a>Automatiska uppdateringar

Azure AD tillhandahåller automatiska uppdateringar för alla anslutningar som du distribuerar. Så länge Connector Updater-tjänsten för programproxy körs, uppdateras anslutnings programmet automatiskt. Om du inte ser Connector Updater-tjänsten på servern måste du [installera om anslutningen](application-proxy-add-on-premises-application.md) för att få några uppdateringar.

Om du inte vill vänta på att en automatisk uppdatering ska komma till din anslutning kan du göra en manuell uppgradering. Gå till [hämtnings sidan för Connector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) på den server där din anslutning finns och välj **Hämta**. Den här processen inaktiverar en uppgradering för den lokala anslutningen.

För klienter med flera anslutningar är de automatiska uppdateringarna riktade till en koppling i taget i varje grupp för att förhindra drift stopp i din miljö.

Du kan uppleva stillestånds tid när dina anslutningar uppdateras om:
  
- Du har bara en koppling vi rekommenderar att du installerar en andra koppling och [skapar en kopplings grupp](application-proxy-connector-groups.md). På så sätt undviker du avbrott och ger högre tillgänglighet.  
- En koppling var i mitten av en transaktion när uppdateringen började. Även om den första transaktionen förloras bör webbläsaren försöka utföra åtgärden igen automatiskt, eller så kan du uppdatera sidan. När begäran skickas dirigeras trafiken till en säkerhets kopierings anslutning.

Information om tidigare utgivna versioner och vilka ändringar de innehåller finns i [Application Proxy-versions historik](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Skapa anslutnings grupper

Med kopplings grupper kan du tilldela vissa anslutningar för att betjäna vissa program. Du kan gruppera ett antal kopplingar tillsammans och sedan tilldela varje program till en grupp.

Anslutnings grupper gör det enklare att hantera stora distributioner. De kan också förbättra svars tiden för klienter som har program som finns i olika regioner, eftersom du kan skapa platsbaserade kopplings grupper så att de endast hanterar lokala program.

Läs mer om anslutnings grupper i [Publicera program i separata nätverk och platser med anslutnings grupper](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapacitetsplanering

Det är viktigt att se till att du har planerat tillräckligt med kapacitet mellan kopplingarna för att hantera den förväntade trafik volymen. Vi rekommenderar att varje kopplings grupp har minst två kopplingar för att ge hög tillgänglighet och skalning. Att ha tre anslutningar är optimalt om du kan behöva underhålla en dator när som helst.

I allmänhet är ju fler användare som du har, desto större dator behöver du. Nedan visas en tabell som ger en översikt av volymen och förväntad fördröjning som olika datorer kan hantera. Observera att det är baserat på förväntade transaktioner per sekund (TPS) i stället för användare sedan användnings mönster varierar och inte kan användas för att förutse belastningen. Det finns också vissa skillnader beroende på storleken på svaren och Server del programmets svars tid – större svars storlekar och långsamma svars tider resulterar i en lägre Max TPS. Vi rekommenderar också att du har ytterligare datorer så att den distribuerade belastningen på datorerna alltid ger en gott buffert. Den extra kapaciteten ser till att du har hög tillgänglighet och återhämtnings kapacitet.

|Kärnor|RAM|Förväntad svars tid (MS)-P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200 *|

\* Den här datorn använde en anpassad inställning för att öka några av standard anslutnings gränserna utöver de rekommenderade .NET-inställningarna. Vi rekommenderar att du kör ett test med standardinställningarna innan du kontaktar supporten för att få denna gräns ändrats för din klient.

> [!NOTE]
> Det finns inte mycket skillnad på den maximala TPS mellan 4, 8 och 16 kärn datorer. Den största skillnaden mellan dem är i den förväntade svars tiden.
>
> Den här tabellen fokuserar också på förväntade prestanda för en anslutning baserat på vilken typ av dator den är installerad på. Detta är skilt från Application Proxy-tjänstens begränsnings gränser, se begränsningar [och begränsningar för tjänsten](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="security-and-networking"></a>Säkerhet och nätverk

Anslutningar kan installeras var som helst i nätverket så att de kan skicka begär anden till Application Proxy-tjänsten. Det är viktigt att datorn som kör anslutningen också har åtkomst till dina appar. Du kan installera anslutningar i företags nätverket eller på en virtuell dator som körs i molnet. Kopplingar kan köras i ett perimeternätverk, även kallat demilitariserad Zone (DMZ), men det är inte nödvändigt eftersom all trafik är utgående så att nätverket förblir säkert.

Kopplingar skickar endast utgående begär Anden. Utgående trafik skickas till Application Proxy-tjänsten och till de publicerade programmen. Du behöver inte öppna inkommande portar eftersom trafiken flödar båda sätten när en session har upprättats. Du behöver inte heller konfigurera inkommande åtkomst genom brand väggarna.

Mer information om hur du konfigurerar utgående brand Väggs regler finns i [arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Prestanda och skalbarhet

Skalningen för Application Proxy-tjänsten är transparent, men skalan är en faktor för kopplingar. Du måste ha tillräckligt många kopplingar för att kunna hantera högsta trafik. Eftersom kopplingar är tillstånds lösa, påverkas de inte av antalet användare eller sessioner. De svarar i stället på antalet begär Anden och deras nytto Last storlek. Med standard webb trafik kan en medels dator hantera ett par 000 begär Anden per sekund. Den specifika kapaciteten beror på de exakta dator egenskaperna.

Anslutnings prestandan är kopplad till processor och nätverk. CPU-prestanda krävs för TLS-kryptering och dekryptering, medan nätverk är viktigt för att få snabb anslutning till programmen och online tjänsten i Azure.

Minnet är däremot mindre av ett problem för anslutningarna. Online tjänsten tar hand om mycket av bearbetningen och all oautentiserad trafik. Allt som kan göras i molnet görs i molnet.

Om anslutningen eller datorn blir otillgänglig kommer trafiken att gå vidare till en annan koppling i gruppen. Den här återhämtnings orsaken är också orsaken till att vi rekommenderar att du har flera kopplingar.

En annan faktor som påverkar prestanda är kvaliteten på nätverket mellan kopplingarna, inklusive:

- **Online tjänsten**: långsam eller hög latens anslutningar till Application Proxy-tjänsten i Azure påverkar anslutnings prestandan. För bästa prestanda bör du ansluta din organisation till Azure med Express Route. Annars kan nätverks teamet se till att anslutningar till Azure hanteras så effektivt som möjligt.
- **Backend-programmen**: i vissa fall finns det ytterligare proxyservrar mellan anslutningen och Server dels programmen som kan sakta ned eller förhindra anslutningar. Om du vill felsöka det här scenariot öppnar du en webbläsare från anslutnings servern och försöker komma åt programmet. Om du kör anslutningarna i Azure men programmen är lokala, är det inte säkert att upplevelsen förväntas vara vad användarna förväntar sig.
- **Domän kontrol Lanterna**: om anslutningarna utför enkel inloggning (SSO) med Kerberos-begränsad delegering, kontaktar de domän kontrol Lanterna innan begäran skickas till Server delen. Anslutningarna har en cache med Kerberos-biljetter, men i en upptagen miljö kan domän kontrollernas svars tider påverka prestandan. Det här problemet är vanligare för anslutningar som körs i Azure men som kommunicerar med domänkontrollanter som är lokala.

Mer information om hur du optimerar nätverket finns i avsnittet [om nätverks sto pol faktorer när du använder Azure Active Directory-programproxy](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Domän anslutning

Kopplingar kan köras på en dator som inte är domänansluten. Men om du vill använda enkel inloggning (SSO) till program som använder integrerad Windows-autentisering (IWA) behöver du en domänansluten dator. I det här fallet måste anslutnings datorerna vara anslutna till en domän som kan utföra [Kerberos](https://web.mit.edu/kerberos) -begränsad delegering för användarens räkning för de publicerade programmen.

Anslutningar kan också anslutas till domäner i skogar som har partiellt förtroende eller skrivskyddade domänkontrollanter.

## <a name="connector-deployments-on-hardened-environments"></a>Anslutnings distributioner i härdade miljöer

Normalt är anslutnings distributionen enkel och kräver ingen särskild konfiguration. Det finns dock vissa unika villkor som bör övervägas:

- Organisationer som begränsar utgående trafik måste [öppna nödvändiga portar](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- FIPS-kompatibla datorer kan behövas för att ändra konfigurationen så att anslutnings processerna kan generera och lagra ett certifikat.
- Organisationer som låser sin miljö baserat på de processer som utfärdar nätverks begär Anden måste se till att båda kopplings tjänsterna är aktiverade för åtkomst till alla portar och IP-adresser som krävs.
- I vissa fall kan utgående vidarebefordrade proxyservrar bryta den dubbelriktade certifikatautentisering och orsaka att kommunikationen Miss lyckas.

## <a name="connector-authentication"></a>Anslutnings-autentisering

För att tillhandahålla en säker tjänst måste anslutningarna autentiseras mot tjänsten och tjänsten måste autentisera mot anslutningen. Den här autentiseringen görs med hjälp av klient-och Server certifikat när anslutningarna initieras. På så sätt är administratörens användar namn och lösen ord inte lagrade på anslutnings datorn.

De certifikat som används är specifika för Application Proxy-tjänsten. De skapas under den inledande registreringen och förnyas automatiskt av kopplingarna varannan månad.

När det första lyckade certifikatet har förnyats har Azure AD-programproxy Connector-tjänsten (nätverks tjänsten) inte behörighet att ta bort det gamla certifikatet från den lokala datorns Arkiv. Om certifikatet har upphört att gälla eller inte används av tjänsten längre, kan du ta bort det på ett säkert sätt.

Undvik problem med certifikat förnyelsen genom att kontrol lera att nätverkskommunikation från anslutningen till de [dokumenterade målen](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) är aktiverade.

Om en anslutning inte är ansluten till tjänsten under flera månader kan dess certifikat vara inaktuella. I det här fallet avinstallerar och installerar du om anslutningen för att utlösa registreringen. Du kan köra följande PowerShell-kommandon:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

För myndigheter, Använd `-EnvironmentName "AzureUSGovernment"` . Mer information finns i [Installera agent för Azure Government molnet](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).

Om du vill veta mer om hur du verifierar certifikatet och felsöker problem, se [Verifiera maskin-och Server dels komponenters stöd för certifikat för programproxy-förtroende](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Under huven

Anslutningarna baseras på Windows Server Web Application Proxy, så de har de flesta av samma hanterings verktyg, inklusive Windows händelse loggar

![Hantera händelse loggar med Loggboken](./media/application-proxy-connectors/event-view-window.png)

och Windows-prestandaräknare.

![Lägg till räknare i anslutningen med prestanda övervakaren](./media/application-proxy-connectors/performance-monitor.png)

Anslutningarna har både **Administratörs** -och **sessions** loggar. I **Administratörs** loggen ingår nyckel händelser och deras fel. I **sessionsnyckeln** ingår alla transaktioner och deras bearbetnings information.

Öppna **Loggboken** och gå till **program-och tjänst loggar**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector** för att se loggarna. Välj **Visa analytiska loggar och fel söknings loggar** på **Visa** -menyn om du vill visa loggen för **sessionen** . Loggen för **sessionen** används vanligt vis för fel sökning och är inaktive rad som standard. Aktivera det för att börja samla in händelser och inaktivera det när det inte längre behövs.

Du kan kontrol lera tjänstens status i fönstret tjänster. Anslutningen består av två Windows-tjänster: den faktiska anslutningen och uppdateraren. Båda måste köras hela tiden.

 ![Exempel: fönstret tjänster visar lokala Azure AD-tjänster](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Nästa steg

- [Publicera program i separata nätverk och platser med anslutnings grupper](application-proxy-connector-groups.md)
- [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
- [Felsöka Application Proxy och anslutnings fel](application-proxy-troubleshoot.md)
- [Tyst installation av Azure AD-programproxy Connector](application-proxy-register-connector-powershell.md)
