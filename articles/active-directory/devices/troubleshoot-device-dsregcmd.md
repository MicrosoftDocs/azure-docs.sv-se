---
title: Felsöka med hjälp av dsregcmd-kommandot – Azure Active Directory
description: Använda utdata från dsregcmd för att förstå enhetens status i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 614b3f927dc22bc534168f8fe95cc2f97031b621
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725531"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Felsöka enheter med kommandot dsregcmd

Dsregcmd/status-verktyget måste köras som ett domän användar konto.

## <a name="device-state"></a>Enhets tillstånd

I det här avsnittet visas status parametrar för enhets anslutning. I tabellen nedan visas villkoren för enheten i olika anslutnings tillstånd.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Enhets tillstånd |
| ---   | ---   | ---   | ---   |
| JA | NO | NO | Azure AD-ansluten |
| NO | NO | JA | kopplad till en domän |
| JA | NO | JA | Hybrid AD-ansluten |
| NO | JA | JA | Lokala DRS-anslutna |

> [!NOTE]
> Workplace Join (Azure AD-registrerad) visas i avsnittet "användar tillstånd"

- **AzureAdJoined:** -anges till "Ja" om enheten är ansluten till Azure AD. "Nej" annars.
- **EnterpriseJoined:** -anges till "Ja" om enheten är ansluten till en lokal DRS. En enhet kan inte vara både EnterpriseJoined och AzureAdJoined.
- **DomainJoined:** -anges till "Ja" om enheten är ansluten till en domän (AD).
- **Domän namn:** – ange namnet på domänen om enheten är ansluten till en domän.

### <a name="sample-device-state-output"></a>Exempel på enhets tillstånds utdata

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Information om enhet

Visas bara när enheten är Azure AD-ansluten eller en hybrid Azure AD-anslutning (inte Azure AD registrerad). I det här avsnittet visas enhets identifierings information som lagras i molnet.

- **DeviceID:** -unikt ID för enheten i Azure AD-klienten
- **Tumavtryck:** -tumavtryck för enhetens certifikat 
- **DeviceCertificateValidity:** -giltigheten för enhets certifikatet
- **KeyContainerId:** -ContainerId för enhetens privata nyckel som är kopplad till enhetens certifikat
- Nyckel **utfärdare:** -nyckel tillhandahållare (maskin vara/program vara) som används för att lagra enhetens privata nyckel.
- **TpmProtected:** -"Yes" om enhetens privata nyckel lagras i en maskinvaru-TPM.

### <a name="sample-device-details-output"></a>Exempel på enhets information

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Information om klientorganisation

Visas bara när enheten är Azure AD-ansluten eller en hybrid Azure AD-anslutning (inte Azure AD registrerad). I det här avsnittet visas vanliga klient uppgifter när en enhet är ansluten till Azure AD.

> [!NOTE]
> Om MDM-URL: er i det här avsnittet är tomma, betyder det att MDM antingen inte har kon figurer ATS eller att den aktuella användaren inte omfattas av MDM-registreringen. Kontrol lera inställningarna för mobilitet i Azure AD för att granska MDM-konfigurationen.

> [!NOTE]
> Även om du ser MDM-URL: er betyder det inte att enheten hanteras av en MDM. Informationen visas om klienten har MDM-konfiguration för automatisk registrering även om själva enheten inte hanteras. 

### <a name="sample-tenant-details-output"></a>Exempel på klient information

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Användar tillstånd

Det här avsnittet innehåller status för olika attribut för den användare som för närvarande är inloggad på enheten.

> [!NOTE]
> Kommandot måste köras i en användar kontext för att det ska gå att hämta giltig status.

- **NgcSet:** -Ställ in på Ja om en Windows Hello-nyckel har angetts för den aktuella inloggade användaren.
- **NgcKeyId:** -ID för Windows Hello-nyckeln om en har angetts för den aktuella inloggade användaren.
- **CanReset:** -anger om Windows Hello-nyckeln kan återställas av användaren. 
- **Möjliga värden:** -DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive eller Unknown IF error. 
- **WorkplaceJoined:** -anges till "Ja" om registrerade Azure AD-konton har lagts till i enheten i den aktuella ntuser-kontexten.
- **WamDefaultSet:** -Ställ in på Ja om ett standard-webbkonto för WAM skapas för den inloggade användaren. Det här fältet kan visa ett fel om dsreg/status körs från en upphöjd kommando tolk. 
- **WamDefaultAuthority:** -inställt på "organisationer" för Azure AD.
- **WamDefaultId:** -Always " https://login.microsoft.com " för Azure AD.
- **WamDefaultGUID:** -WAM-providerns (Azure AD/Microsoft-konto) GUID för standard-WAM-webbkontot. 

### <a name="sample-user-state-output"></a>Exempel på utdata från användar tillstånd

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO-tillstånd

Det här avsnittet kan ignoreras för registrerade Azure AD-enheter.

> [!NOTE]
> Kommandot måste köras i en användar kontext för att det ska gå att hämta giltig status för användaren.

- **AzureAdPrt:** -Ställ in på Ja om det finns en PRT på enheten för den inloggade användaren.
- **AzureAdPrtUpdateTime:** -Ställ in på tiden i UTC när PRT senast uppdaterades.
- **AzureAdPrtExpiryTime:** -Ställ in på tid i UTC när PRT upphör att gälla om den inte förnyas.
- **AzureAdPrtAuthority:** -Azure AD-auktoritets-URL
- **EnterprisePrt:** -anges till "Ja" om enheten har PRT från den lokala ADFS-enheten. För Hybrid Azure AD-anslutna enheter kan enheten ha PRT från både Azure AD och lokala AD samtidigt. Lokala enheter som är anslutna till har endast en Enterprise-PRT.
- **EnterprisePrtUpdateTime:** – Ställ in på tiden i UTC när företags-PRT senast uppdaterades.
- **EnterprisePrtExpiryTime:** -Ställ in på tid i UTC när PRT upphör att gälla om den inte förnyas.
- **EnterprisePrtAuthority:** -ADFS-auktoritets-URL

### <a name="sample-sso-state-output"></a>Exempel på SSO State-utdata

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Diagnostikdata

### <a name="pre-join-diagnostics"></a>För koppling av diagnostik

Det här avsnittet visas endast om enheten är domänansluten och det inte går att ansluta till hybrid Azure AD Join.

Det här avsnittet utför olika tester för att hjälpa till att diagnostisera kopplings fel. Det här avsnittet innehåller också information om föregående (?). Den här informationen omfattar fel fasen, felkoden, serverns begäran-ID, http-status för Server svar, fel meddelande för Server svar.

- **Användar kontext:** -kontexten som diagnostiken körs i. Möjliga värden: SYSTEM, icke-förhöjd användare, utökad användare. 

   > [!NOTE]
   > Eftersom den faktiska kopplingen utförs i SYSTEM kontext, är körning av diagnostik i SYSTEM kontext närmast det faktiska kopplings scenariot. Om du vill köra diagnostik i SYSTEM kontexten måste kommandot dsregcmd/status köras från en upphöjd kommando tolk.

- **Klient tid:** -system tiden i UTC.
- **AD-anslutnings test:** -test utför ett anslutnings test till domänkontrollanten. Fel i det här testet kommer troligen att resultera i kopplings fel i för kontroll fasen.
- **AD-konfiguration test:** -test läser och verifierar om SCP-objektet har kon figurer ATS korrekt i den lokala AD-skogen. Fel i det här testet skulle sannolikt leda till kopplings fel i identifierings fasen med felkoden 0x801c001d.
- **DRS identifierings test:** -test hämtar DRS-slutpunkterna från slut punkten för identifiering av metadata och utför en användar sfär förfrågan. Fel i det här testet skulle sannolikt leda till kopplings fel i identifierings fasen.
- **DRS anslutnings test:** -test utför grundläggande anslutnings test till DRS-slutpunkten.
- **Test för token-hämtning:** -test försöker hämta en Azure AD-autentiseringstoken om användar klienten är federerad. Fel i det här testet skulle sannolikt leda till kopplings fel i auth-fasen. Om autentiseringen Miss lyckas försöker synkroniseringen bli reserv, om inte återställningen uttryckligen inaktive ras med register nyckel inställningarna nedan.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Återgå till Sync – Anslut:** -Ställ in på "aktive rad" om register nyckeln ovan, för att förhindra återställningen till synkronisering med auth-haverier, inte finns. Det här alternativet är tillgängligt från Windows 10 1803 och senare.
- **Tidigare registrering:** -tid då föregående anslutnings försök gjordes. Endast misslyckade anslutnings försök loggas.
- **Fel fas:** -fasen för den koppling där den avbröts. Möjliga värden är för kontroll, identifiering, auth, Join.
- **Klient felkod:** -klient fel kod som returnerades (HRESULT).
- **Server felkod:** -Server fel kod om en begäran skickades till servern och servern svarade igen med en felkod. 
- **Server meddelande:** – Server meddelande som returnerades tillsammans med felkoden.
- **Https-status:** -http-status som returneras av servern.
- **Begärande-ID:** -klient-RequestId skickas till servern. Användbart för att korrelera med loggar på Server sidan.

### <a name="sample-pre-join-diagnostics-output"></a>Exempel på utdata för att koppla från diagnostik

I följande exempel visas diagnostiskt test med ett identifierings fel.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

I följande exempel visas diagnostiska tester, men registrerings försöket misslyckades med ett katalog fel, vilket förväntas för sync-anslutning. När jobbet Azure AD Connect synkronisering har slutförts kommer enheten att kunna ansluta.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

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

### <a name="post-join-diagnostics"></a>Diagnostik efter koppling

I det här avsnittet visas utdata från Sanity-kontroller som utförs på en enhet som är ansluten till molnet.

- **AadRecoveryEnabled:** -om ja, kan nycklarna som lagras i enheten inte användas och enheten markeras för återställning. Nästa inloggning kommer att utlösa återställnings flödet och registrera enheten på nytt.
- **KeySignTest:** -om "lyckades" enhets nycklarna har felfri hälsa. Om KeySignTest Miss lyckas markeras vanligt vis enheten för återställning. Nästa inloggning kommer att utlösa återställnings flödet och registrera enheten på nytt. För Hybrid Azure AD-anslutna enheter återställningen är tyst. När Azure AD anslöt eller Azure AD är registrerad, kommer enheter att uppmanas att använda användarautentisering för att återställa och omregistrera enheten vid behov. **KeySignTest kräver förhöjd behörighet.**

#### <a name="sample-post-join-diagnostics-output"></a>Exempel på efter koppling av diagnostik

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Krav kontroll för NGC

Det här avsnittet utför de nödvändiga kontrollerna för etablering av Windows Hello för företag (WHFB). 

> [!NOTE]
> Du kanske inte ser NGC-krav kontroll information i dsregcmd/status om användaren redan har konfigurerat WHFB.

- **IsDeviceJoined:** -anges till "Ja" om enheten är ansluten till Azure AD.
- **IsUserAzureAD:** -Ställ in på Ja om den inloggade användaren finns i Azure AD.
- **PolicyEnabled:** -anges till "Ja" om WHFB-principen är aktive rad på enheten.
- **PostLogonEnabled:** -anges till "Ja" om WHFB-registreringen utlöses internt av plattformen. Om det är inställt på "nej" anger det att Windows Hello för företag-registrering utlöses av en anpassad mekanism
- **DeviceEligible:** -Ställ in på Ja om enheten uppfyller maskin varu kraven för registrering med WHFB.
- **SessionIsNotRemote:** -Ställ in på Ja om den aktuella användaren är inloggad direkt på enheten och inte på distans.
- **CertEnrollment:** -bara för WHFB certifikat förtroende distribution, som anger certifikat registrerings utfärdaren för WHFB. Ange till "registrerings utfärdare" om källan för WHFB-principen är grupprincip, "hantering av mobila enheter" om källan är MDM. "ingen", annars
- **AdfsRefreshToken:** -bara för WHFB certifikat förtroende distribution. Förekommer endast om CertEnrollment är "registrerings utfärdare". Anger om enheten har en Enterprise-PRT för användaren.
- **AdfsRaIsReady:** -bara för WHFB certifikat förtroende distribution.  Förekommer endast om CertEnrollment är "registrerings utfärdare". Ange till "Ja" om ADFS anges i identifierings-metadata som den stöder WHFB *och* om mallen för inloggnings certifikat är tillgänglig.
- **LogonCertTemplateReady:** -bara för WHFB certifikat förtroende distribution. Förekommer endast om CertEnrollment är "registrerings utfärdare". Ange till "Ja" om mallens tillstånd för inloggnings certifikat är giltig och hjälper till med att felsöka ADFS RA.
- **PreReqResult:** -ger resultat av all nödvändig utvärdering av WHFB. Ange till "etablera" om WHFB-registrering skulle startas som en uppgift efter inloggning när användaren loggar in nästa gång.

### <a name="sample-ngc-prerequisite-check-output"></a>Exempel på utdata från krav kontroll för NGC

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Nästa steg

Frågor finns i [vanliga frågor och svar om enhets hantering](faq.md)
