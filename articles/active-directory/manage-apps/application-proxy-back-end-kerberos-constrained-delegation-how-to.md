---
title: Felsöka Kerberos-begränsad delegering – App proxy
description: Felsöka konfigurationer med Kerberos-begränsad delegering för programproxy
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen, japere
ms.openlocfilehash: 65d68924fbc3a777ef7ef59d2532a1ae20e23c48
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258226"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Felsöka konfigurationer med Kerberos-begränsad delegering för programproxy

De metoder som är tillgängliga för att uppnå SSO till publicerade program kan variera från ett program till ett annat. Ett alternativ som Azure Active Directory (Azure AD) Application Proxy erbjuder som standard Kerberos-begränsad delegering (KCD). Du kan konfigurera en anslutning, för användarna, för att köra begränsad Kerberos-autentisering till backend-program.

Proceduren för att aktivera KCD är enkel. Det kräver inte mer än en allmän förståelse för de olika komponenter och autentiseringsscheman som stöder SSO. Men ibland fungerar inte KCD SSO som förväntat. Du behöver användbara informations källor för att felsöka dessa scenarier.

Den här artikeln innehåller en enda referens punkt som hjälper dig att felsöka och åtgärda några av de vanligaste problemen. Den omfattar också diagnostisering av mer komplexa implementerings problem.

Den här artikeln gör följande antaganden:

- Distribution av Azure AD-programproxy per [Kom igång med Application Proxy](application-proxy-add-on-premises-application.md) och allmän åtkomst till icke-KCD program fungerar som förväntat.
- Det publicerade mål programmet baseras på Internet Information Services (IIS) och Microsofts implementering av Kerberos.
- Server-och program värdarna finns i en enda Azure Active Directory domän. Detaljerad information om scenarier mellan domäner och skogar finns i [KCD White Paper](https://aka.ms/KCDPaper).
- Ämnes programmet publiceras i en Azure-klient med förautentisering aktive rad. Användare förväntas autentisera sig till Azure via formulärbaserad autentisering. Många scenarier för klientautentisering omfattas inte av den här artikeln. De kan läggas till vid en viss tidpunkt i framtiden.

## <a name="prerequisites"></a>Förutsättningar

Azure AD-programproxy kan distribueras i många typer av infrastrukturer eller miljöer. Arkitekturerna varierar från organisation till organisation. De vanligaste orsakerna till KCD-relaterade problem är inte i miljöerna. Enkla fel konfigurationer eller allmänna misstag orsakar de flesta problem.

Därför är det bäst att se till att du uppfyller alla krav för att [använda KCD SSO med programproxyn](application-proxy-configure-single-sign-on-with-kcd.md) innan du påbörjar fel sökningen. Observera avsnittet om hur du konfigurerar Kerberos-begränsad delegering på 2012R2. Den här processen använder en annan metod för att konfigurera KCD i tidigare versioner av Windows. Tänk också på följande:

- Det är inte ovanligt att en domän medlems Server öppnar en dialog ruta för säker kanal med en speciell domänkontrollant (DC). Sedan kan servern gå över till en annan dialog ruta vid en specifik tidpunkt. Därför är anslutnings värdar inte begränsade till kommunikation med enbart en speciell lokal plats-domänkontrollanter.
- Scenarier mellan domäner förlitar sig på referenser som dirigerar en anslutnings värd till domänkontrollanter som kan vara utanför den lokala nätverks omkretsen. I dessa fall är det lika viktigt att även skicka trafik vidare till domänkontrollanter som representerar andra respektive domäner. Annars Miss lyckas delegeringen.
- Om möjligt bör du undvika att placera aktiva IP-eller ID-enheter mellan kopplings värdar och domänkontrollanter. Dessa enheter är ibland för påträngandea och stör Core RPC-trafik.

Testa delegering i enkla scenarier. De fler variabler du introducerar, desto mer kan du behöva tävla med. Om du vill spara tid begränsar du testningen till en enda koppling. Lägg till ytterligare anslutningar när problemet har lösts.

Vissa miljö faktorer kan också bidra till ett problem. Undvik dessa faktorer genom att minimera arkitekturen så mycket som möjligt under testningen. Till exempel är felkonfigurerade interna brand Väggs-ACL: er vanliga. Skicka om möjligt all trafik från en anslutning direkt till DCs och backend-programmet.

Den bästa platsen för att placera anslutningar är så nära som möjligt för deras mål. En brand vägg som är infogad vid testning lägger till onödig komplexitet och kan förlänga dina undersökningar.

Vad visar ett KCD-problem? Det finns flera vanliga indikationer på att KCD SSO fungerar. De första tecknen i ett problem visas i webbläsaren.

![Skärm bild som visar ett exempel på ett felaktigt K C D D-konfigurations fel, med felet "felaktig Kerberos-begränsad delegering..." Skriv.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Exempel: Auktoriseringen misslyckades på grund av saknade behörigheter](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Båda dessa bilder visar samma symptom: SSO-problem. Användar åtkomst till programmet nekas.

## <a name="troubleshooting"></a>Felsökning

Hur du felsöker beror på problemet och de symtom du ser. Utforska följande artiklar innan du går längre tillbaka. De ger användbar felsöknings information:

- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)
- [Kerberos-fel och symptom](application-proxy-troubleshoot.md#kerberos-errors)
- [Arbeta med SSO när lokala och moln identiteter inte är identiska](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Om du har den här punkten finns ditt huvud problem. För att starta avgränsar du flödet i följande tre steg som du kan felsöka.

### <a name="client-pre-authentication"></a>Förautentisering för klient

Den externa användare som autentiserar sig för Azure via en webbläsare. Möjligheten att förautentisera till Azure krävs för att KCD SSO ska fungera. Testa och åtgärda den här möjligheten om det finns några problem. Tillståndet för autentisering är inte relaterat till KCD eller det publicerade programmet. Det är enkelt att korrigera skillnader genom att Sanity kontrol lera att ämnes kontot finns i Azure. Kontrol lera också att den inte är inaktive rad eller blockerad. Fel svaret i webbläsaren är tillräckligt beskrivande för att förklara orsaken. Om du är osäker kontrollerar du om det finns andra Microsoft-felsöknings artiklar att verifiera.

### <a name="delegation-service"></a>Delegerings tjänst

Azure proxy-anslutningsprogrammet som hämtar en Kerberos-tjänstebiljett för användare från en Kerberos-Key Distribution Center (KCD).

Den externa kommunikationen mellan klienten och Azure-klient delen har ingen betydelse på KCD. Dessa kommunikationer säkerställer bara att KCD fungerar. Azure proxy-tjänsten tillhandahåller ett giltigt användar-ID som används för att hämta en Kerberos-biljett. Utan detta ID är KCD inte möjligt och Miss lyckas.

Som tidigare nämnts innehåller webbläsarens fel meddelanden några användbara tips om varför saker inte fungerar. Se till att du noterar aktivitets-ID och tidsstämpel i svaret. Den här informationen hjälper dig att korrelera beteendet för faktiska händelser i händelse loggen i Azure proxy.

![Exempel: felaktigt konfigurations fel för KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Motsvarande poster som visas i händelse loggen visas som händelser 13019 eller 12027. Sök efter anslutnings händelse loggar i **program och tjänster loggar** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **admin**.

![Händelse 13019 från händelse loggen för programproxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Händelse 12027 från händelse loggen för programproxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Använd en **A** -post i din interna DNS för programmets adress, inte en **CNAME**.
1. Bekräfta att kopplings värden har beviljats behörighet att delegera till det angivna mål kontots SPN. Bekräfta att **Använd valfritt autentiseringsprotokoll** är markerat. Mer information finns i [artikeln SSO Configuration](application-proxy-configure-single-sign-on-with-kcd.md).
1. Kontrol lera att det bara finns en instans av SPN i Azure AD. Problem `setspn -x` från en kommando tolk på en domän medlems värd.
1. Kontrol lera att en domän princip tillämpas som begränsar den [maximala storleken på utfärdade Kerberos-token](/archive/blogs/askds/maxtokensize-and-windows-8-and-windows-server-2012). Den här principen stoppar anslutningen från att hämta en token om den är för hög.

En nätverks spårning som samlar in utbyten mellan kopplings värden och en domän-KDC är nästa bästa steg för att få mer detaljerad information om problemen. Mer information finns i [fel söknings bladet för djupet](https://aka.ms/proxytshootpaper).

Om biljetten ser bra ut visas en händelse i loggarna som anger att autentiseringen misslyckades eftersom programmet returnerade en 401. Den här händelsen anger att mål programmet avvisade biljetten. Gå till nästa steg.

### <a name="target-application"></a>Mål program

Konsumenten av Kerberos-biljetten som tillhandahålls av anslutningen. I det här skedet väntar du på att anslutningen har skickat en Kerberos-tjänstebiljett till Server delen. Den här biljetten är en rubrik i den första program förfrågan.

1. När du använder programmets interna URL som definierats i portalen kontrollerar du att programmet är tillgängligt direkt från webbläsaren på kopplings värden. Sedan kan du logga in korrekt. Information finns på sidan **för fel sökning** av anslutning.
1. Kontrol lera att autentiseringen mellan webbläsaren och programmet använder Kerberos fortfarande på kopplings värden. Välj en av följande åtgärder:
1. Kör DevTools (**F12**) i Internet Explorer eller Använd [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) från anslutnings värden. Gå till programmet genom att använda den interna URL: en. Kontrol lera att de erbjudna WWW Authorization-huvudena som returneras i svaret från programmet för att säkerställa att antingen Negotiate eller Kerberos finns.

   - Nästa Kerberos-blob som returneras i svaret från webbläsaren till programmet börjar med **YII**. Dessa bokstäver anger att Kerberos körs. Microsoft NT LAN Manager (NTLM), å andra sidan, börjar alltid med **TlRMTVNTUAAB**, som läser NTLM Security Support Provider (NTLMSSP) när den avkodas från base64. Om du ser **TlRMTVNTUAAB** i början av blobben är Kerberos inte tillgängligt. Om du inte ser **TlRMTVNTUAAB** är Kerberos troligt vis tillgängligt.

      > [!NOTE]
      > Om du använder Fiddler kräver den här metoden att du tillfälligt inaktiverar utökat skydd för programmets konfiguration i IIS.

      ![Kontroll fönster för webb läsar nätverk](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Blobben i den här bilden startar inte med **TIRMTVNTUAAB**. I det här exemplet är Kerberos tillgängligt och Kerberos-blobben börjar inte med **YII**.

1. Ta tillfälligt bort NTLM från providers-listan på IIS-webbplatsen. Öppna appen direkt från Internet Explorer på anslutnings värden. NTLM finns inte längre i listan med providers. Du kan bara komma åt programmet med hjälp av Kerberos. Om åtkomsten Miss lyckas kan det bero på ett problem med programmets konfiguration. Kerberos-autentisering fungerar inte.

   - Om Kerberos inte är tillgängligt kontrollerar du programmets autentiseringsinställningar i IIS. Se till att **Negotiate** visas överst, med enbart NTLM under det. Om du **inte ser Negotiate**, **Kerberos eller Negotiate** eller **PKU2U** fortsätter du bara om Kerberos fungerar.

     ![Windows-autentiseringsproviders](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Med Kerberos och NTLM på plats inaktiverar du tillfälligt förautentisering för programmet i portalen. Försök att komma åt den från Internet med hjälp av den externa URL: en. Du uppmanas att autentisera. Du kan göra det med samma konto som användes i föregående steg. Om inte, är det ett problem med Server dels programmet, inte KCD.
   - Återaktivera förautentisering på portalen. Autentisera via Azure genom att försöka ansluta till programmet via dess externa URL. Om SSO Miss lyckas visas ett otillåtet fel meddelande i webbläsaren och händelse 13022 i loggen:

     *Microsoft AAD Application Proxy Connector kan inte autentisera användaren eftersom backend-servern svarar på Kerberos-autentiseringsförsök med ett HTTP 401-fel.*

      ![Visar HTTTP 401-otillåtet fel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Kontrol lera IIS-programmet. Kontrol lera att den konfigurerade programpoolen och SPN är konfigurerade för att använda samma konto i Azure AD. Navigera i IIS på det sätt som visas i följande bild:

      ![IIS-programmets konfigurations fönster](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      När du känner till identiteten kontrollerar du att det här kontot har kon figurer ATS med SPN i fråga. Ett exempel är `setspn –q http/spn.wacketywack.com`. Ange följande text i en kommando tolk:

      ![Visar kommando fönstret SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Kontrol lera det SPN som definierats mot programmets inställningar i portalen. Se till att samma SPN som kon figurer ATS mot Azure AD-målets Azure-konto används av programmets programpool.

      ![SPN-konfiguration i Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Gå till IIS och välj alternativet **konfigurations redigerare** för programmet. Gå till **system. webserver/Security/Authentication/windowsAuthentication**. Kontrol lera att värdet **UseAppPoolCredentials** är **Sant**.

      ![Inloggnings alternativ för IIS-konfiguration för programpooler](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Ändra värdet till **Sant**. Ta bort alla cachelagrade Kerberos-biljetter från backend-servern genom att köra följande kommando:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Mer information finns i [Rensa Kerberos-klientens biljett-cache för alla sessioner](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Om du låter kernelläge vara aktiverat förbättrar det prestandan för Kerberos-åtgärder. Men det gör också att biljetten för den begärda tjänsten dekrypteras med hjälp av dator kontot. Det här kontot kallas även för det lokala systemet. Ange värdet **Sant** om du vill bryta KCD när programmet finns på fler än en server i en Server grupp.

- Ytterligare kontroll är att inaktivera **utökat** skydd. I vissa scenarier är **utökat** skydd KCD när det aktiverades i vissa konfigurationer. I dessa fall publicerades ett program som en undermapp till standard webbplatsen. Det här programmet är endast konfigurerat för anonym autentisering. Alla dialog rutor är nedtonade, vilket innebär att underordnade objekt inte ärver några aktiva inställningar. Vi rekommenderar att du testar, men inte glömmer att återställa värdet till **aktive rad**, där det är möjligt.

  Med den här ytterligare kontrollen går du vidare till spåret och använder ditt publicerade program. Du kan sätta upp ytterligare anslutningar som också är konfigurerade att delegera. Mer information finns i mer djupgående teknisk genom gång, [fel sökning av Azure-AD-programproxy](https://aka.ms/proxytshootpaper).

Om du fortfarande inte kan göra något kan Microsoft Support hjälpa dig. Skapa ett support ärende direkt på portalen. En tekniker kommer att kontakta dig.

## <a name="other-scenarios"></a>Andra scenarier

- Azure Application Proxy begär en Kerberos-biljett innan begäran skickas till ett program. Vissa program från tredje part liknar denna metod för autentisering. De här programmen förväntar sig att de mer konventionella förhandlingarna ska äga rum. Den första begäran är Anonym, vilket gör att programmet kan svara med de autentiseringstyper som stöds via en 401. Den här typen av Kerberos-förhandling kan aktive ras med de steg som beskrivs i det här dokumentet: [Kerberos-begränsad delegering för enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md).
- Autentisering med flera hopp används ofta i scenarier där ett program skiktas, med en server del och klient del, där båda kräver autentisering, t. ex. SQL Server Reporting Services. Information om hur du konfigurerar multi-hop-scenariot finns i Support artikeln [Kerberos-begränsad delegering kan kräva protokoll över gång i scenarier med flera hopp](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Nästa steg

[Konfigurera KCD på en hanterad domän](../../active-directory-domain-services/deploy-kcd.md).