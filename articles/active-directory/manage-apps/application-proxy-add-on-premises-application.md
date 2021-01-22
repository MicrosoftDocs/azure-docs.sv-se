---
title: Självstudie – Lägg till en lokal app-Application Proxy i Azure AD
description: Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien visar hur du förbereder din miljö för användning med Application Proxy. Sedan använder den Azure Portal för att lägga till ett lokalt program till Azure AD-klienten.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q3
ms.openlocfilehash: 6b46a5ea71bf8c9705ffc3bc51ea48f4b0c28502
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660771"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Självstudie: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory

Azure Active Directory (Azure AD) innehåller en programproxytjänst som gör att användarna kan komma åt lokala program genom att logga in med sitt Azure AD-konto. Den här självstudien förbereder din miljö för användning med programproxy. När din miljö är redo använder du Azure-portalen för att lägga till ett lokalt program till Azure AD-klientorganisationen.

:::image type="content" source="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png" alt-text="Översikts diagram över Application Proxy" lightbox="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png":::

Kopplingar är en viktig del av programproxyn. Mer information om anslutningar finns i [förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md).

I den här självstudien:

> [!div class="checklist"]
> * Öppnar portar för utgående trafik och ger åtkomst till specifika webbadresser
> * Installerar anslutningsprogrammet på din Windows-server och registrerar den med programproxy
> * Verifierar att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lägger till ett lokalt program till Azure AD-klientorganisationen
> * Verifierar att en test användare kan logga in på programmet med hjälp av ett Azure AD-konto

## <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till ett lokalt program i Azure AD behöver du:

* En [Microsoft Azure AD Premium-prenumeration](https://azure.microsoft.com/pricing/details/active-directory)
* Ett program administratörs konto
* Användar identiteter måste synkroniseras från en lokal katalog eller skapas direkt i dina Azure AD-klienter. Identitetssynkronisering gör det möjligt för Azure AD att förautentisera användare innan de beviljar åtkomst till App proxy-publicerade program och att ha nödvändig information om användar-ID för att utföra enkel inloggning (SSO).

### <a name="windows-server"></a>Windows-server

Om du vill använda programproxy behöver du en Windows-server som kör Windows Server 2012 R2 eller senare. Du installerar anslutningsappen för programproxy på servern. Anslutningsservern måste ansluta till programproxytjänsten i Azure och de lokala programmen som du har för avsikt att publicera.

Vi rekommenderar att du har mer än en Windows-server för att säkra hög tillgänglighet i produktionsmiljön. I den här självstudien räcker det med en Windows-server.

> [!IMPORTANT]
> Om du installerar anslutningen på Windows Server 2019 måste du inaktivera stöd för HTTP2-protokoll i WinHttp-komponenten för att Kerberos-begränsad delegering ska fungera korrekt. Detta är inaktiverat som standard i tidigare versioner av operativ system som stöds. Om du lägger till följande register nyckel och startar om servern inaktive ras den på Windows Server 2019. Observera att detta är en hel dators register nyckel.
>
> ```
> Windows Registry Editor Version 5.00
> 
> HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 Value: 0
> ```
>
> Nyckeln kan ställas in via PowerShell med följande kommando.
> ```
> Set-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\' -Name EnableDefaultHTTP2 -Value 0
>

#### <a name="recommendations-for-the-connector-server"></a>Rekommendationer för anslutningsservern

1. Placera fysiskt anslutningsservern nära programservrarna för att optimera prestandan mellan anslutningsprogrammet och programmet. Mer information finns i [Överväganden för nätverkstopologi](application-proxy-network-topology.md).
1. Kopplings servern och webb program servrarna bör tillhöra samma Active Directory domän eller intervall för domäner som är betrodda. Att ha servrar i samma domän eller betrodda domäner är ett krav för att använda enkel inloggning (SSO) med integrerad Windows-autentisering (IWA) och Kerberos-begränsad delegering (KCD). Om anslutningsservern och webbprogramservrarna är i olika Active Directory-domäner så måste du använda resursbaserad delegering för enkel inloggning. Mer information finns i [KCD för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Om du har distribuerat Azure AD Password Protection-proxy ska du inte installera Azure-AD-programproxy och Azure AD-proxy för lösen ords skydd tillsammans på samma dator. Azure AD-programproxy och Azure AD Password Protection-proxy installera olika versioner av tjänsten Azure AD Connect agent Updateer. Dessa olika versioner är inkompatibla när de installeras tillsammans på samma dator.

#### <a name="tls-requirements"></a>TLS-krav

Windows-anslutningsservern måste ha TLS 1.2 aktiverat innan du installerar anslutningsprogrammet för programproxyn.

Aktivera TLS 1.2:

1. Ange följande registernycklar:

   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Starta om servern.

> [!Note]
> Microsoft uppdaterar Azure-tjänster för att använda TLS-certifikat från en annan uppsättning rot certifikat utfärdare (ca: er). Den här ändringen görs eftersom de aktuella CA-certifikaten inte uppfyller något av kraven för CA/webbläsarens forum bas linje. Se [ändringar i Azure TLS-certifikat](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes) för mer information.

## <a name="prepare-your-on-premises-environment"></a>Förbered din lokala miljö

Börja med att aktivera kommunikation till Azure-datacenter för att förbereda din miljö för Azure AD-programproxy. Om det finns en brand vägg i sökvägen ser du till att den är öppen. En öppen brand vägg gör det möjligt för anslutningen att göra HTTPS-förfrågningar (TCP) till programproxyn.

> [!IMPORTANT]
> Om du installerar anslutningen för Azure Government Cloud följer du [krav](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) och [installations anvisningar](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Detta kräver att du aktiverar åtkomst till en annan uppsättning URL: er och ytterligare en parameter för att köra installationen.

### <a name="open-ports"></a>Öppna portar

Öppna följande portar för **utgående** trafik.

   | Portnummer | Hur den används |
   | --- | --- |
   | 80 | Laddar ned listor över återkallade certifikat (CRL) När TLS/SSL-certifikatet verifieras |
   | 443 | All utgående kommunikation med programproxytjänsten |

Om brandväggstrafiken hanteras baserat på användarna som genererar den ska du även öppna portarna 80 och 443 för trafik som kommer från Windows-tjänster som körs som en nätverkstjänst.

### <a name="allow-access-to-urls"></a>Tillåt åtkomst till webbadresser

Tillåt åtkomst till följande webbadresser:

| URL | Port | Hur den används |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP |Anslutnings tjänsten använder dessa URL: er för att verifiera certifikat. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;. microsoftonline-p.com<br>&ast;. msauth.net<br>&ast;. msauthimages.net<br>&ast;. msecnd.net<br>&ast;. msftauth.net<br>&ast;. msftauthimages.net<br>&ast;. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com<br>www.microsoft.com/pkiops | 443/HTTPS |Anslutningsprogrammet använder dessa webbadresser under registreringen. |
| ctldl.windowsupdate.com | 80/HTTP |Anslutningen använder denna URL under registrerings processen. |

Du kan tillåta anslutningar till &ast; . msappproxy.net, &ast; . ServiceBus.Windows.net och andra URL: er ovan om din brand vägg eller proxy låter dig konfigurera åtkomst regler baserade på domänsuffix. Om inte, måste du tillåta åtkomst till [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519). IP-adressintervallen uppdateras varje vecka.

### <a name="dns-name-resolution-for-azure-ad-application-proxy-endpoints"></a>DNS-namnmatchning för Azure AD-programproxy-slutpunkter

Offentliga DNS-poster för Azure AD-programproxy-slutpunkter är länkade CNAME-poster som pekar på en A-post. Detta säkerställer fel tolerans och flexibilitet. Det garanterar att Azure AD-programproxy Connector alltid har åtkomst till värdnamn med domänsuffix _*. msappproxy.net_ eller _*. ServiceBus.Windows.net_. Men under namn matchningen kan CNAME-posterna innehålla DNS-poster med olika värdnamn och suffix.  På grund av detta måste du se till att enheten (beroende på din installation-Connector-Server, brand vägg, utgående proxy) kan matcha alla poster i kedjan och tillåter anslutning till de matchade IP-adresserna. Eftersom DNS-posterna i kedjan kan komma att ändras från tid till och med kan vi inte tillhandahålla någon lista över DNS-poster.

## <a name="install-and-register-a-connector"></a>Installera och registrera ett anslutningsprogram

Om du vill använda programproxyn installerar du en anslutning på varje Windows Server som du använder med Application Proxy-tjänsten. Anslutningsprogrammet är en agent som hanterar den utgående anslutningen från lokala programservrar till programproxy i Azure Active Directory. Du kan installera ett anslutningsprogram på servrar som även har andra autentiseringsagenter installerade, till exempel Azure Active Directory Connect.


Så här installerar du anslutningsprogrammet:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som programadministratör för den katalog som använder programproxy. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat administratörsalias på den domänen.
1. Välj ditt användar namn i det övre högra hörnet. Kontrol lera att du är inloggad på en katalog som använder programproxy. Om du behöver ändra kataloger väljer du **Växla katalog** och väljer en katalog som använder Application Proxy.
1. I den vänstra navigerings panelen väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **programproxy**.
1. Välj **Hämta anslutnings tjänst**.

    ![Hämta kopplings tjänsten om du vill se villkoren för tjänsten](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Läs användningsvillkoren. När du är klar väljer du **Godkänn villkoren & Ladda ned**.
1. Längst ned i fönstret väljer du **Kör** för att installera anslutningen. Installationsguiden öppnas.
1. Följ anvisningarna i guiden för att installera tjänsten. När du uppmanas att registrera anslutningsprogrammet med programproxyn för Azure AD-klientorganisationen anger du autentiseringsuppgifterna för programadministratören.
    - Om **IE Förbättrad säkerhetskonfiguration** är satt till **På** för Internet Explorer (IE) kanske inte registreringsskärmen visas. Följ instruktionerna i felmeddelandet för att få åtkomst. Se till att **förbättrad säkerhets konfiguration i Internet Explorer** är inställt på **av**.

### <a name="general-remarks"></a>Allmänna anmärkningar

Om du tidigare har installerat ett anslutningsprogram måste du installera om för att hämta den senaste versionen. Information om tidigare utgivna versioner och vilka ändringar de innehåller finns i [Application Proxy: versions historik](application-proxy-release-version-history.md).

Väljer du att ha mer än en Windows-server för dina lokala program måste du installera och registrera anslutningsprogrammet på varje server. Du kan ordna anslutningsprogrammen i anslutningsgrupper. Mer information finns i [anslutningsgrupper](application-proxy-connector-groups.md).

Om din organisation använder proxyservrar för att ansluta till internet måste du konfigurera dem för programproxy.  Mer information finns i [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). 

Information om anslutningsprogram, kapacitetsplanering och hur de håller sig uppdaterade finns i avsnittet [Förstå anslutningsprogram för Azure AD programproxy](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verifiera att anslutningsprogrammet installerats och registrerats på rätt sätt

Du kan använda Azure-portalen eller din Windows-server för att bekräfta att ett nytt anslutningsprogram har installerats.

### <a name="verify-the-installation-through-azure-portal"></a>Verifiera installationen via Azure Portal

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Logga in på din klientkatalog i [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory** i den vänstra navigerings panelen och välj sedan **programproxy** under avsnittet **Hantera** . Alla dina anslutningsprogram och anslutningsgrupper visas på den här sidan.
1. Visa en koppling för att verifiera dess information. Kopplingarna bör expanderas som standard. Om den koppling som du vill visa inte är expanderad expanderar du kopplingen så att du kan se informationen. En aktiv grön etikett innebär att ditt anslutningsprogram kan ansluta till tjänsten. Även om etiketten är grön kan dock ett nätverksproblem fortfarande blockera anslutningsprogrammet från att ta emot meddelanden.

    ![Azure AD-programproxy-kopplingar](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Mer hjälp om hur du installerar en anslutning finns i [problem med att installera Application Proxy Connector](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verifiera installationen via Windows Server

Så här bekräftar du att anslutningsprogrammet installerats och registrerats på rätt sätt:

1. Öppna Hanteraren för Windows-tjänster genom att klicka på nyckeln **Windows** och ange *services.msc*.
1. Kontrollera om statusen för följande två tjänster är **Körs**.
   - **Microsoft AAD Application Proxy Connector** möjliggör anslutning.
   - **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar. Uppdateringsverktyget söker efter nya versioner av anslutningsprogrammet och uppdaterar det efter behov.

     ![Application Proxy Connector-tjänster – skärmbild](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Om statusen för tjänsterna inte **körs**, högerklickar du på varje tjänst och väljer **Starta**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Lägg till ett lokalt program till Azure Active Directory

Nu när du har förberett din miljö och installerat ett anslutningsprogram är du redo att lägga till lokala program till Azure Active Directory.  

1. Logga in som administratör i [Azure-portalen](https://portal.azure.com/).
2. I den vänstra navigerings panelen väljer du **Azure Active Directory**.
3. Välj **företags program** och välj sedan **nytt program**.
4. Välj **Lägg till en lokal program** knapp som visas om halva sidan i avsnittet **lokala program** . Alternativt kan du välja **skapa ett eget program** överst på sidan och sedan välja **Konfigurera programproxy för säker fjärråtkomst till ett lokalt program**.
5. I avsnittet **Lägg till ett eget lokalt program** anger du följande information om ditt program:

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Namn** | Namnet på programmet som ska visas i Mina appar och i Azure Portal. |
    | **Intern webbadress** | Det här är webbadressen för att komma åt programmet från inuti ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika webbplatser på samma server som olika program och ge varje webbplats sitt eget namn och sina egna åtkomstregler.<br><br>Om du publicerar en sökväg, så se till att den innehåller alla bilder, skript och formatmallar som krävs för ditt program. Om din app till exempel är på https: \/ /yourapp/app och använder avbildningar som finns på https: \/ /yourapp/media, ska du publicera https: \/ /yourapp/som sökväg. Den interna webbadressen måste inte vara landningssidan som användarna ser. Mer information finns i [Ange en anpassad startsida för publicerade program](application-proxy-configure-custom-home-page.md). |
    | **Extern webbadress** | Adressen som ger användare åtkomst till programmet från utanför ditt nätverk. Om du inte vill använda standarddomänen för programproxy kan du läsa om [anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Förautentisering** | Hur programproxyn verifierar användare innan de ges åtkomst till ditt program.<br><br>**Azure Active Directory** – Programproxyn omdirigerar användarna till att logga in med Azure AD, som autentiserar deras katalog- och programbehörigheter. Vi rekommenderar att du behåller det här alternativet som standard så att du kan utnyttja Azure AD-säkerhetsfunktioner som villkorlig åtkomst och Multi-Factor Authentication. **Azure Active Directory** krävs för övervakning av program med Microsoft Cloud Application Security.<br><br>**Genom strömning** – användarna behöver inte autentisera sig mot Azure AD för att få åtkomst till programmet. Du kan fortfarande konfigurera autentiseringskrav från serverdelen. |
    | **Anslutningsgrupp** | Anslutningsprogram bearbetar fjärråtkomsten till programmet och anslutningsgrupper hjälper dig att organisera anslutningsprogram och program efter region, nätverk eller syfte. Om du inte har skapat några anslutningsgrupper än kommer programmet att tilldelas **Standard**.<br><br>Om ditt program använder WebSockets för att ansluta måste alla anslutningsprogram i gruppen vara version 1.5.612.0 eller senare.|

6. Om det behövs konfigurerar du **ytterligare inställningar**. De flesta programmen bör behålla dessa inställningarna i standardtillstånden. 

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Tidsgränsen för serverdels-programmet** | Ställ endast in värdet på **Lång** om programmet autentiserar och ansluter långsamt. Som standard har backend-programmets tids gräns en längd på 85 sekunder. När värdet är Long ökas Server dels tids gränsen till 180 sekunder. |
    | **Använd endast HTTP-cookie** | Ställ in värdet på **Ja** för att programproxycookies ska inkluderas i HTTPOnly-flaggan i HTTP-svarsrubriken. Ställ in värdet på **Nej** om du använder fjärrskrivbordstjänster.|
    | **Använd säker cookie**| Ställ in värdet på **Ja** för att skicka cookies via en säker kanal, som en krypterad HTTPS-begäran.
    | **Använd beständig cookie**| Behåll det här värdet inställt på **Nej**. Använd endast den här inställningen för program som inte kan dela cookies mellan processer. Mer information om cookie-inställningar finns i [cookie-inställningar för att komma åt lokala program i Azure Active Directory](./application-proxy-configure-cookie-settings.md).
    | **Översätt webbadresser i rubriker** | Behåll det här värdet som **Ja** såvida inte programmets ursprungliga värdrubrik krävs i autentiseringsbegäran. |
    | **Översätt webbadresser i brödtext för program** | Behåll detta värde som **Nej** om du inte har hårdkodad HTML-länkar till andra lokala program och inte använder anpassade domäner. Mer information finns i [Länka översättning med programproxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ställ in det här värdet på **Ja** om du planerar att övervaka programmet med Microsoft Cloud App Security (MCAS). Mer information finns i [Konfigurera övervakning av program åtkomst i real tid med Microsoft Cloud App Security och Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Välj **Lägg till**.

## <a name="test-the-application"></a>Testa programmet

Du är redo att testa om programmet har lagts till korrekt. I följande steg kan du lägga till ett användarkonto i programmet och försöka logga in.

### <a name="add-a-user-for-testing"></a>Lägg till en användare för testning

Verifiera att användarkontot redan har behörighet att komma åt programmet inifrån företagsnätverket innan du lägger till en användare till programmet.

Lägga till en testanvändare:

1. Välj **företags program** och välj sedan det program som du vill testa.
2. Välj **Kom igång** och välj sedan **tilldela en användare för testning**.
3. Under **användare och grupper** väljer du **Lägg till användare**.
4. Under **Lägg till tilldelning** väljer **du användare och grupper**. Avsnittet **användare och grupper** visas.
5. Välj det konto som du vill lägga till.
6. Välj **Välj** och välj sedan **tilldela**.

### <a name="test-the-sign-on"></a>Testa inloggningen

Så här testar du inloggningen i programmet:

1. Välj **programproxy** från det program som du vill testa.
2. Längst upp på sidan väljer du **testa program** för att köra ett test på programmet och kontrol lera eventuella konfigurations problem.
3. Se till att först starta programmet för att testa att logga in på programmet och ladda ned den diagnostiska rapporten för att granska lösnings vägledningen för eventuella identifierade problem.

Mer information om felsökning finns i [Felsöka problem med programproxyn och felmeddelanden](application-proxy-troubleshoot.md).

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser du skapade i den här självstudien när de inte längre behövs.

## <a name="next-steps"></a>Nästa steg

I den här självstudien förberedde du din lokala miljö för arbete med programproxy och installerade och registrerade sedan anslutningsprogrammet för programproxy. Sedan la du till ett program till din Azure AD-klientorganisation. Du verifierade att en användare kan logga in på programmet med hjälp av ett Azure AD-konto.

Du gjorde detta:
> [!div class="checklist"]
> * Öppnade portar för utgående trafik och gav åtkomst till specifika webbadresser
> * Installerade anslutningsprogrammet på din Windows-server och registrerade den med programproxy
> * Verifierade att anslutningsprogrammet installerats och registrerats på rätt sätt
> * Lade till ett lokalt program till Azure AD-klientorganisationen
> * Verifierat att en test användare kan logga in på programmet med ett Azure AD-konto

Nu är du redo att konfigurera programmet för enkel inloggning. Använd följande länk för att välja en enkel inloggnings metod och för att hitta självstudier för enkel inloggning.

> [!div class="nextstepaction"]
> [Konfigurera enkel inloggning](sso-options.md#choosing-a-single-sign-on-method)
