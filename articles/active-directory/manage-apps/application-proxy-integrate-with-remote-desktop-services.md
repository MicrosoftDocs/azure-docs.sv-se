---
title: Publicera fjärr skrivbord med Azure Active Directory-programproxy
description: Beskriver hur du konfigurerar app proxy med Fjärrskrivbordstjänster (RDS)
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 5e5d5370057449d1877c31b249d3fe47fd60bf2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687676"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicera Fjärrskrivbord med Azure AD-programproxy

Fjärr skrivbords tjänsten och Azure AD-programproxy samar beta för att förbättra produktiviteten hos de anställda som är borta från företags nätverket. 

Den avsedda mål gruppen för den här artikeln är:
- Aktuella programproxy-kunder som vill erbjuda fler program till sina slutanvändare genom att publicera lokala program via Fjärrskrivbordstjänster.
- Nuvarande Fjärrskrivbordstjänster kunder som vill minska angrepps ytan för distributionen med hjälp av Azure AD-programproxy. Det här scenariot innehåller en uppsättning tvåstegsverifiering och villkorliga åtkomst kontroller till fjärr skrivbords tjänster.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Så här passar programproxyn i standard-RDS-distributionen

En standard-RDS-distribution innehåller olika fjärr skrivbords roll tjänster som körs på Windows Server. Titta på [Fjärrskrivbordstjänster arkitekturen](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture), det finns flera distributions alternativ. Till skillnad från andra distributions alternativ för fjärr skrivbords tjänster har [RDS-distributionen med Azure AD-programproxy](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) (visas i följande diagram) en permanent utgående anslutning från servern som kör anslutnings tjänsten. Andra distributioner lämnar öppna inkommande anslutningar via en belastningsutjämnare.

![Programproxyn är placerad mellan den virtuella datorn för fjärr skrivbords tjänster och det offentliga Internet](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

I en RDS-distribution körs webb rollen för fjärr skrivbord och RD Gateway-rollen på datorer som är riktade mot Internet. Dessa slut punkter visas av följande anledningar:
- RD Web tillhandahåller användaren en offentlig slut punkt för att logga in och Visa de olika lokala program och skriv bord som de kan komma åt. När du väljer en resurs skapas en RDP-anslutning med hjälp av den inbyggda appen på operativ systemet.
- RD Gateway kommer till bilden när en användare startar RDP-anslutningen. RD Gateway hanterar krypterad RDP-trafik som kommer via Internet och översätter den till den lokala server som användaren ansluter till. I det här scenariot kommer den trafik som gatewayen tar emot från Azure-AD-programproxy.

>[!TIP]
>Om du inte har distribuerat fjärr skrivbords tjänster tidigare eller vill ha mer information innan du börjar, lär du dig hur du [sömlöst distribuerar fjärr skrivbords tjänster med Azure Resource Manager och Azure Marketplace](/windows-server/remote/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Krav

- Både webb-och RD Gateway-slutpunkterna måste finnas på samma dator och med en gemensam rot. Webb server för fjärr skrivbord och Fjärrskrivbordsgateway publiceras som ett enda program med programproxy så att du kan använda enkel inloggning mellan de två programmen.
- Du bör redan ha [distribuerat RDS](/windows-server/remote/remote-desktop-services/rds-in-azure)och [aktiverat Application Proxy](application-proxy-add-on-premises-application.md). Se till att du har uppfyllt kraven för att aktivera programproxy, till exempel att installera anslutnings programmet, öppna nödvändiga portar och URL: er och Aktivera TLS 1,2 på servern.
- Dina slutanvändare måste använda en kompatibel webbläsare för att ansluta till webb servern för fjärr skrivbord eller webb klienten för fjärr skrivbord. Mer information finns i [Support för klient konfigurationerna](#support-for-other-client-configurations).
- När du publicerar en webb server för fjärr skrivbord rekommenderar vi att du använder samma interna och externa fullständiga domän namn. Om de interna och externa FQDN-namnen är olika bör du inaktivera översättning av begär ande huvud för att undvika att klienten tar emot ogiltiga länkar.
- Om du använder webb sidan för fjärr skrivbord i Internet Explorer måste du aktivera ActiveX-tillägget för fjärr skrivbords tjänster.
- Om du använder webb klienten för fjärr skrivbord måste du använda Application Proxy [Connector version 1.5.1975 eller senare](./application-proxy-release-version-history.md).
- För flödet för Azure AD-förautentisering kan användare bara ansluta till resurser som publicerats till dem i fönstret **RemoteApp och skriv bord** . Användare kan inte ansluta till en stationär dator med hjälp av rutan **Anslut till en fjärran sluten dator** .
- Om du använder Windows Server 2019 kan du behöva inaktivera HTTP2-protokollet. Mer information finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Distribuera det gemensamma scenariot för RDS och programproxy

När du har konfigurerat RDS och Azure AD-programproxy för din miljö följer du stegen för att kombinera de två lösningarna. De här stegen går igenom publiceringen av de två webbanslutna RDS-slutpunkterna (RD Web och RD Gateway) som program och dirigerar sedan trafiken på ditt RDS för att gå via programproxyn.

### <a name="publish-the-rd-host-endpoint"></a>Publicera värd slut punkten för fjärr skrivbord

1. [Publicera ett nytt program för programproxy](application-proxy-add-on-premises-application.md) med följande värden:
   - Intern URL: `https://<rdhost>.com/` , där `<rdhost>` är den gemensamma roten som resursen för webb-och Fjärrskrivbordsgateway-resurs.
   - Extern URL: det här fältet fylls i automatiskt baserat på namnet på programmet, men du kan ändra det. Användarna kommer att gå till denna URL när de ansluter till RDS.
   - Förautentiserings metod: Azure Active Directory
   - Översätt URL-huvuden: Nej
2. Tilldela användare till det publicerade RD-programmet. Se till att alla har åtkomst till fjärr skrivbords tjänster.
3. Lämna metoden för enkel inloggning för programmet när **enkel inloggning med Azure AD har inaktiverats**.

   >[!Note]
   >Användarna uppmanas att autentisera sig en gång till Azure AD och en gång till webb servern, men de har enkel inloggning till RD Gateway.

4. Välj **Azure Active Directory** och sedan **app-registreringar**. Välj din app i listan.
5. Under **Hantera** väljer du **märkes anpassning**.
6. Uppdatera **Start sidans URL** -fält så att de pekar på din webb slut punkt för fjärr skrivbord (till exempel `https://<rdhost>.com/RDWeb` ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Dirigera fjärr skrivbords trafik till Application Proxy

Anslut till RDS-distributionen som administratör och ändra Server namnet för RD Gateway-servern för distributionen. Den här konfigurationen säkerställer att anslutningarna går via Azure AD-programproxy-tjänsten.

1. Anslut till RDS-servern som kör rollen anslutnings utjämning för fjärr skrivbord.
2. Starta **Serverhanteraren**.
3. Välj **Fjärrskrivbordstjänster** i rutan till vänster.
4. Välj **Översikt**.
5. I avsnittet distributions översikt väljer du den nedrullningsbara menyn och väljer **Redigera distributions egenskaper**.
6. På fliken RD Gateway ändrar du fältet **Server namn** till den externa URL som du har angett för värd slut punkten för fjärr skrivbord i Application Proxy.
7. Ändra fältet **inloggnings metod** till **lösenordsautentisering**.

   ![Sidan distributions egenskaper på RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Kör det här kommandot för varje samling. Ersätt *\<yourcollectionname\>* och *\<proxyfrontendurl\>* med din egen information. Det här kommandot möjliggör enkel inloggning mellan RD Web och RD Gateway och optimerar prestandan:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Exempel:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Kommandot ovan använder ett baktick i "' nrequire '.

9. Kör följande kommando för att kontrol lera ändringen av de anpassade RDP-egenskaperna samt Visa RDP-filinnehållet som ska laddas ned från RDWeb för den här samlingen:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Nu när du har konfigurerat fjärr skrivbord har Azure AD-programproxy tagit över som den Internet-riktade komponenten i RDS. Du kan ta bort de andra offentliga slut punkter som är riktade mot Internet på din webb-och RD Gateway-dator.

### <a name="enable-the-rd-web-client"></a>Aktivera webb klienten för fjärr skrivbord
Om du även vill att användarna ska kunna använda webb klienten för fjärr skrivbord följer du stegen i [Konfigurera fjärr skrivbords webb klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin) så att användarna kan aktivera detta.

Med webb klienten för fjärr skrivbord kan användarna komma åt din organisations fjärr skrivbords infrastruktur via en HTML5-kompatibel webbläsare, till exempel Microsoft Edge, Internet Explorer 11, Google Chrome, Safari eller Mozilla Firefox (v 55.0 och senare).

## <a name="test-the-scenario"></a>Testa scenariot

Testa scenariot med Internet Explorer på en dator med Windows 7 eller 10.

1. Gå till den externa URL: en som du ställer in eller Sök efter ditt program i panelen för mina [appar](https://myapps.microsoft.com).
2. Du uppmanas att autentisera till Azure Active Directory. Använd ett konto som du har tilldelat till programmet.
3. Du uppmanas att autentisera till en webb server för fjärr skrivbord.
4. När din RDS-autentisering lyckas kan du välja det skriv bord eller det program som du vill använda och börja arbeta.

## <a name="support-for-other-client-configurations"></a>Stöd för andra klientkonfigurationer

Konfigurationen som beskrivs i den här artikeln är till för åtkomst till fjärr skrivbords tjänster via webb servern för fjärr skrivbord eller webb klienten för fjärr skrivbord. Om du behöver kan du dock använda andra operativ system eller webbläsare. Skillnaden är i den autentiseringsmetod som du använder.

| Autentiseringsmetod | Klient konfiguration som stöds |
| --------------------- | ------------------------------ |
| Förautentisering    | Webb server för fjärr skrivbord – Windows 7/10 med Internet Explorer * eller [Edge krom IE-läge](/deployedge/edge-ie-mode) + fjärr skrivbords tillägg för ActiveX |
| Förautentisering    | Webb klient för fjärrskrivbordsgateway – HTML5 kompatibel webbläsare, till exempel Microsoft Edge, Internet Explorer 11, Google Chrome, Safari eller Mozilla Firefox (v 55.0 och senare) |
| Genomströmning | Andra operativ system som stöder Microsoft Fjärrskrivbord programmet |

* Edge krom IE-läge krävs när portalen Mina appar används för att komma åt appen för fjärr skrivbord.  

Flödet för förautentisering ger högre säkerhets fördelar än genom strömnings flödet. Med förautentisering kan du använda funktioner i Azure AD-autentisering, t. ex. enkel inloggning, villkorlig åtkomst och tvåstegsverifiering för dina lokala resurser. Du ser också till att endast autentiserad trafik når nätverket.

Om du vill använda passthrough-autentisering finns det bara två ändringar av stegen som anges i den här artikeln:
1. I [publicera värd slut punkten för värddatorn](#publish-the-rd-host-endpoint) steg 1, anger du metoden för förautentisering för **genom strömning**.
2. I [direkt fjärr skrivbords trafik till Application Proxy hoppar du](#direct-rds-traffic-to-application-proxy)över steg 8 helt.

## <a name="next-steps"></a>Nästa steg
- [Aktivera fjärråtkomst till SharePoint med Azure AD-programproxy](application-proxy-integrate-with-sharepoint-server.md)
- [Säkerhets överväganden för att få åtkomst till appar via en fjärr anslutning med hjälp av Azure AD-programproxy](application-proxy-security.md)
- [Metod tips för belastnings utjämning för flera App-servrar](application-proxy-high-availability-load-balancing.md#best-practices-for-load-balancing-among-multiple-app-servers)
