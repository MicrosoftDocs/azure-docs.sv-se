---
title: Felsöka Azure Active Directory-hybridanslutna enheter
description: Felsöka hybrid Azure Active Directory anslutna Windows 10-och Windows Server 2016-enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 1eb4194354a07d5b580f07cfe5962785a4e100bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578030"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Felsöka Azure Active Directory-hybridanslutna enheter

Innehållet i den här artikeln gäller enheter som kör Windows 10 eller Windows Server 2016.

För andra Windows-klienter kan du läsa artikeln [Felsöka hybrid Azure Active Directory anslutna enheter på den äldre nivån](troubleshoot-hybrid-join-windows-legacy.md).

I den här artikeln förutsätter vi att du har [konfigurerat hybrid Azure Active Directory anslutna enheter](hybrid-azuread-join-plan.md) som stöd för följande scenarier:

- Enhetsbaserad villkorlig åtkomst
- [Företags växling av inställningar](./enterprise-state-roaming-overview.md)
- [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

Det här dokumentet innehåller fel söknings vägledning för att lösa eventuella problem.

För Windows 10 och Windows Server 2016 har hybrid Azure Active Directorys anslutning stöd för Windows 10 november 2015 Update och senare.

## <a name="troubleshoot-join-failures"></a>Felsök anslutnings fel

### <a name="step-1-retrieve-the-join-status"></a>Steg 1: Hämta kopplings status

**Hämta anslutnings status:**

1. Öppna en kommando tolk som administratör
2. Skriv `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Steg 2: utvärdera kopplings status

Granska följande fält och kontrol lera att de har de förväntade värdena:

#### <a name="domainjoined--yes"></a>DomainJoined: Ja

Det här fältet anger om enheten är ansluten till en lokal Active Directory eller inte. Om värdet är **Nej** kan enheten inte utföra en hybrid Azure AD-anslutning.

#### <a name="workplacejoined--no"></a>WorkplaceJoined: Nej

Det här fältet anger om enheten är registrerad med Azure AD som en personlig enhet (markerad som *arbets plats ansluten*). Det här värdet ska vara **Nej** för en domänansluten dator som också är hybrid Azure AD-ansluten. Om värdet är **Ja**, har ett arbets-eller skol konto lagts till innan hybriden av Azure AD-anslutning slutfördes. I det här fallet ignoreras kontot när du använder Windows 10 version 1607 eller senare.

#### <a name="azureadjoined--yes"></a>AzureAdJoined: Ja

Det här fältet anger om enheten är ansluten. Värdet blir **Ja** om enheten antingen är en Azure AD-ansluten enhet eller en hybrid Azure AD-ansluten enhet.
Om värdet är **Nej** har kopplingen till Azure AD inte slutförts ännu.

Fortsätt till nästa steg för ytterligare fel sökning.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Steg 3: hitta fasen där anslutningen misslyckades och felkoden

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 och senare

Leta efter underavsnittet "föregående registrering" i avsnittet "diagnostikdata" i resultatet för att koppla status. Det här avsnittet visas endast om enheten är domänansluten och det inte går att ansluta till hybrid Azure AD Join.
I fältet "fel fas" anges fasen för kopplings felet medan klient ErrorCode anger felkoden för kopplings åtgärden.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Äldre Windows 10-versioner

Använd Loggboken loggar för att hitta fasen och felkoden för kopplings felen.

1. Öppna händelse loggarna för **registrering av användar enheter** i logg boken. Finns under **program och tjänster logga**  >  **Microsoft**  >  **Windows**  >  **användar enhets registrering**
2. Sök efter händelser med följande eventIDs 304, 305, 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Skärm bild av logg boken. En händelse med I D 304 är markerad och dess information visas, med felkoden och den markerade fasen." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Skärm bild av logg boken. En händelse med I D 305 är synlig och dess information visas, och felkoden är markerad." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Steg 4: Sök efter möjliga orsaker och lösningar från listorna nedan

#### <a name="pre-check-phase"></a>Fas för kontroll

Möjliga orsaker till problemet:

- Enheten har ingen detaljerad information om domänkontrollanten.
   - Enheten måste finnas i organisationens interna nätverk eller på VPN med nätverks ledningen till en lokal Active Directory (AD) domänkontrollant.

#### <a name="discover-phase"></a>Identifierings fas

Möjliga orsaker till problemet:

- SCP-objektet (Service Connection Point) är felkonfigurerat/kunde inte läsa SCP-objekt från DOMÄNKONTROLLANTen.
   - Ett giltigt SCP-objekt krävs i AD-skogen, som enheten tillhör, som pekar på ett verifierat domän namn i Azure AD.
   - Information finns i avsnittet [Konfigurera en tjänst anslutnings punkt](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Det gick inte att ansluta och hämta metadata för identifiering från identifierings slut punkten.
   - Enheten ska kunna komma åt `https://enterpriseregistration.windows.net` , i system kontexten, för att identifiera slut punkterna för registrering och auktorisering.
   - Om den lokala miljön kräver en utgående proxy måste IT-administratören se till att enhetens dator konto kan identifiera och tyst autentisera till den utgående proxyn.
- Det gick inte att ansluta till användar sfär slut punkten och utföra sfär identifiering. (Endast Windows 10 version 1809 och senare)
   - Enheten ska kunna komma åt `https://login.microsoftonline.com` , i system kontexten, för att utföra sfär identifiering för den verifierade domänen och fastställa domän typen (hanterad/federerad).
   - Om den lokala miljön kräver en utgående proxy, måste IT-administratören se till att SYSTEM kontexten på enheten kan identifiera och tyst autentisera till den utgående proxyn.

**Vanliga felkoder:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Orsak: det gick inte att läsa SCP-objektet och hämta information om Azure AD-klienten.
   - Lösning: Läs avsnittet [Konfigurera en tjänst anslutnings punkt](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Orsak: generiskt identifierings problem. Det gick inte att hämta metadata för identifiering från DRS.
   - Lösning: hitta underfelet nedan för att undersöka ytterligare.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Orsak: tids gränsen för åtgärden nåddes under identifieringen.
   - Lösning: kontrol lera att `https://enterpriseregistration.windows.net` är tillgängligt i system kontexten. Mer information finns i avsnittet krav på [nätverks anslutning](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Orsak: det gick inte att identifiera allmän sfär. Det gick inte att fastställa domän typ (hanterad/federerad) från STS.
   - Lösning: hitta underfelet nedan för att undersöka ytterligare.

**Vanliga under fel koder:**

Använd någon av följande metoder för att hitta underfelkodna för identifierings fel koden.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 och senare

Sök efter "DRS Discovery test" i avsnittet "diagnostikdata" i sammanfognings statusens utdata. Det här avsnittet visas endast om enheten är domänansluten och det inte går att ansluta till hybrid Azure AD Join.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Äldre Windows 10-versioner

Använd Loggboken loggar för att hitta fasen och ErrorCode för kopplings felen.

1. Öppna händelse loggarna för **registrering av användar enheter** i logg boken. Finns under **program och tjänster logga**  >  **Microsoft**  >  **Windows**  >  **användar enhets registrering**
2. Sök efter händelser med följande eventIDs 201

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Skärm bild av logg boken. En händelse med I D 201 är markerad och dess information visas, och felkoden är markerad." border="false":::

###### <a name="network-errors"></a>Nätverks fel

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Orsak: det gick inte att upprätta en anslutning till servern
   - Lösning: kontrol lera nätverks anslutningen till de Microsoft-resurser som krävs. Mer information finns i [krav för nätverks anslutning](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Orsak: allmän nätverks tids gräns.
   - Lösning: kontrol lera nätverks anslutningen till de Microsoft-resurser som krävs. Mer information finns i [krav för nätverks anslutning](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f beror/-2147012721)
   - Orsak: nätverks stacken kunde inte avkoda svaret från servern.
   - Lösning: kontrol lera att nätverks proxyn inte stör och ändrar Server svaret.

###### <a name="http-errors"></a>HTTP-fel

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Orsak: SCP-objektet har kon figurer ATS med fel klient-ID. Eller inga aktiva prenumerationer hittades i klient organisationen.
   - Lösning: kontrol lera att SCP-objektet har kon figurer ATS med rätt Azure AD-klient-ID och aktiva prenumerationer eller finns i klient organisationen.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Orsak: HTTP 503 från DRS-servern.
   - Lösning: servern är inte tillgänglig för tillfället. framtida anslutnings försök kommer sannolikt att lyckas när servern är online igen.

###### <a name="other-errors"></a>Övriga fel

- **E_INVALIDDATA** (0x8007000D/-2147024883)
   - Orsak: det gick inte att parsa Server svars-JSON. Troligen på grund av proxy som returnerar HTTP 200 med en HTML-auth-sida.
   - Lösning: om den lokala miljön kräver en utgående proxy måste IT-administratören se till att SYSTEM kontexten på enheten kan identifiera och tyst autentisera till den utgående proxyn.

#### <a name="authentication-phase"></a>Autentiserings fas

Gäller endast för federerade domän konton.

Orsaker till problemet:

- Det går inte att hämta en åtkomsttoken tyst för DRS-resursen.
   - Windows 10-enheter hämtar auth-token från Federations tjänsten med integrerad Windows-autentisering till en aktiv WS-Trust-slutpunkt. Information: [federationstjänst konfiguration](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Vanliga felkoder:**

Använd Loggboken loggar för att hitta felkod, underfelkod, Server fel kod och Server fel meddelande.

1. Öppna händelse loggarna för **registrering av användar enheter** i logg boken. Finns under **program och tjänster logga**  >  **Microsoft**  >  **Windows**  >  **användar enhets registrering**
2. Sök efter händelser med följande eventID 305

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Skärm bild av logg boken. En händelse med I D 305 är synlig. Informationen visas, med en D-felkoder och status markerad." border="false":::

##### <a name="configuration-errors"></a>Konfigurationsfel

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Orsak: autentiseringsprotokollet är inte WS-Trust.
   - Lösning: den lokala identitets leverantören måste ha stöd för WS-Trust
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Orsak: den lokala Federations tjänsten returnerade inget XML-svar.
   - Lösning: se till att MEX-slutpunkten returnerar en giltig XML. Kontrol lera att proxyn inte stör och returnerar icke-XML-svar.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Orsak: det gick inte att identifiera slut punkten för autentisering med användar namn/lösen ord.
   - Lösning: kontrol lera inställningarna för lokal identitetsprovider. Se till att WS-Trust-slutpunkterna är aktiverade och se till att MEX-svaret innehåller rätt slut punkter.

##### <a name="network-errors"></a>Nätverks fel

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Orsak: allmän nätverks tids gräns.
   - Lösning: kontrol lera att `https://login.microsoftonline.com` är tillgängligt i system kontexten. Se till att den lokala identitets leverantören är tillgänglig i SYSTEM kontexten. Mer information finns i [krav för nätverks anslutning](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Orsak: anslutningen med auth-slutpunkten avbröts.
   - Lösning: försök igen om en stund eller försök att ansluta från en alternativ stabil nätverks plats.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Orsak: Transport Layer Security (TLS), som tidigare kallades Secure Sockets Layer (SSL), det gick inte att verifiera certifikat som skickats av servern.
   - Lösning: kontrol lera skevningen för klient tiden. Försök igen om en stund eller försök att ansluta från en alternativ stabil nätverks plats.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Orsak: försöket att ansluta till `https://login.microsoftonline.com` misslyckades.
   - Lösning: kontrol lera nätverks anslutningen till `https://login.microsoftonline.com` .

##### <a name="other-errors"></a>Övriga fel

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Orsak: SAML-token från den lokala identitets leverantören godkändes inte av Azure AD.
   - Lösning: kontrol lera inställningarna för Federations servern. Leta efter server fel koden i inloggnings loggarna.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Orsak: Server WS-Trust svaret rapporterade fel undantag och det gick inte att hämta försäkran
   - Lösning: kontrol lera inställningarna för Federations servern. Leta efter server fel koden i inloggnings loggarna.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Orsak: ett fel uppstod vid försök att hämta åtkomsttoken från token-slutpunkten.
   - Lösning: Sök efter det underliggande felet i ADAL-loggen.
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Orsak: allmänt ADAL-problem
   - Lösning: Sök efter felkoden eller server fel koden från inloggnings loggarna.

#### <a name="join-phase"></a>Kopplings fas

Orsaker till problemet:

Sök efter registrerings typen och leta efter felkoden i listan nedan.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 och senare

Leta efter underavsnittet "föregående registrering" i avsnittet "diagnostikdata" i resultatet för att koppla status. Det här avsnittet visas endast om enheten är domänansluten och det inte går att ansluta till hybrid Azure AD Join.
Fältet "registrerings typ" anger vilken typ av koppling som utförs.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Äldre Windows 10-versioner

Använd Loggboken loggar för att hitta fasen och ErrorCode för kopplings felen.

1. Öppna händelse loggarna för **registrering av användar enheter** i logg boken. Finns under **program och tjänster logga**  >  **Microsoft**  >  **Windows**  >  **användar enhets registrering**
2. Sök efter händelser med följande eventIDs 204

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Skärm bild av logg boken. Information om en händelse med I D 204 visas, med felkod, H T T P-status och meddelande markerat." border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>HTTP-fel som returnerats från DRS-servern

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Orsak: ett felsvar togs emot från DRS med felkod: "DirectoryError"
   - Lösning: Mer information om möjliga orsaker och lösningar hittar du i Server fel koden.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Orsak: ett felsvar togs emot från DRS med felkod: "AuthenticationError" och ErrorSubCode är inte "DeviceNotFound".
   - Lösning: Mer information om möjliga orsaker och lösningar hittar du i Server fel koden.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Orsak: ett felsvar togs emot från DRS med felkod: "DirectoryError"
   - Lösning: Mer information om möjliga orsaker och lösningar hittar du i Server fel koden.

##### <a name="tpm-errors"></a>TPM-fel

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Orsak: TPM-åtgärden misslyckades eller var ogiltig
   - Lösning: troligen på grund av en felaktig Sysprep-avbildning. Se till att den dator där Sysprep-avbildningen skapades inte är Azure AD-ansluten, att en hybrid Azure AD är ansluten eller att Azure AD är registrerad.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Orsak: allmänt TPM-fel.
   - Lösning: inaktivera TPM på enheter med det här felet. Windows 10 version 1809 och högre identifierar automatiskt TPM-haverier och slutför Azure AD-anslutning utan att använda TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Orsak: TPM i FIPS-läge stöds inte för närvarande.
   - Lösning: inaktivera TPM på enheter med det här felet. Windows 1809 identifierar automatiskt TPM-haverier och slutför Azure AD-anslutning utan att använda TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Orsak: TPM är låst.
   - Lösning: tillfälligt fel. Vänta på cooldowns perioden. Försök att ansluta efter en stund. Mer information finns i artikeln om TPM- [grunderna](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Nätverks fel

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Orsak: allmän nätverks tids gräns vid försök att registrera enheten på DRS
   - Lösning: kontrol lera nätverks anslutningen till `https://enterpriseregistration.windows.net` .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072EE7/-2147012889)
   - Orsak: Server namnet eller adressen kunde inte matchas.
   - Lösning: kontrol lera nätverks anslutningen till `https://enterpriseregistration.windows.net` . Se till att DNS-matchningen för värd namnet är korrekt i n/w och på enheten.
- **WININET_E_CONNECTION_ABORTED** (0x80072EFE/-2147012866)
   - Orsak: anslutningen till servern avslutades onormalt.
   - Lösning: försök igen om en stund eller försök att ansluta från en alternativ stabil nätverks plats.

##### <a name="federated-join-server-errors"></a>Federerat kopplings Server fel

| Server fel kod | Server fel meddelande | Möjliga orsaker | Lösning |
| --- | --- | --- | --- |
| DirectoryError | Din begäran är tillfälligt begränsad. Försök igen om 300 sekunder. | Förväntat fel. Detta kan bero på att du gör flera registrerings begär anden i snabb följd. | Försök ansluta igen efter cooldown-perioden |

##### <a name="sync-join-server-errors"></a>Fel vid synkronisering av kopplings Server

| Server fel kod | Server fel meddelande | Möjliga orsaker | Lösning |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: klienten <UUID> hittades inte. Det här felet kan inträffa om det inte finns några aktiva prenumerationer för klienten. Kontakta prenumerations administratören. | Klient-ID i SCP-objektet är felaktigt | Se till att SCP-objektet är konfigurerat med rätt Azure AD-klient-ID och aktiva prenumerationer och finns i klient organisationen. |
| DirectoryError | Det gick inte att hitta enhets objektet med angivet ID. | Förväntat fel för sync-anslutning. Enhetsobjektet har inte synkroniserats från AD till Azure AD | Vänta tills den Azure AD Connect synkroniseringen har slutförts och nästa anslutnings försök efter att synkroniseringen har slutförts löser problemet |
| AuthenticationError | Verifieringen av mål datorns SID | Certifikatet på Azure AD-enheten stämmer inte överens med det certifikat som användes för att signera blobben under Sync-anslutningen. Det här felet innebär vanligt vis att synkroniseringen inte har slutförts än. |  Vänta tills den Azure AD Connect synkroniseringen har slutförts och nästa anslutnings försök efter att synkroniseringen har slutförts löser problemet |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Steg 5: samla in loggar och kontakta Microsoft Support

Hämta filen Auth.zip från [https://github.com/CSS-Identity/DRS/tree/main/Auth](https://github.com/CSS-Identity/DRS/tree/main/Auth)

1. Zippa upp filerna och Byt namn på de inkluderade filerna **start-auth.txt** och **stop-auth.txt** till **Start-auth. cmd** och **Stop-auth. cmd**.
1. Kör **Start-auth. cmd** från en upphöjd kommando tolk.
1. Använd switch-konto för att växla till en annan session med problem användaren.
1. Återskapa problemet.
1. Använd switch-konto om du vill växla tillbaka till admin-sessionen som kör spårningen.
1. Kör **Stop-auth. cmd** från en upphöjd kommando tolk.
1. Zip och skicka mappen **Authlogs** från mappen där skripten kördes.

## <a name="troubleshoot-post-join-issues"></a>Felsöka problem efter anslutning

### <a name="retrieve-the-join-status"></a>Hämta kopplings status

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: Ja och AzureADPrt: Ja

Fälten visar om användaren har autentiserats för Azure AD vid inloggning på enheten.
Om värdena är **Nej** kan det bero på att:

- Felaktig lagrings nyckel i den TPM som är kopplad till enheten vid registreringen (kontrol lera KeySignTest vid körning).
- Alternativt inloggnings-ID
- Det gick inte att hitta HTTP-proxy

## <a name="known-issues"></a>Kända problem
- Under Inställningar – > konton – > åtkomst till arbete eller skola kan hybrid Azure AD-anslutna enheter Visa två olika konton, en för Azure AD och en för lokal AD, vid anslutning till mobila hotspots eller externa WiFi-nätverk. Detta är endast ett UI-problem och påverkar inte funktionaliteten.

## <a name="next-steps"></a>Nästa steg

Fortsätt [Felsöka enheter med kommandot dsregcmd](troubleshoot-device-dsregcmd.md)

Frågor finns i [vanliga frågor och svar om enhets hantering](faq.md)
