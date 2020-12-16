---
title: 'Azure AD Connect: Använd en SAML 2,0 identitets leverantör för enkel inloggning – Azure'
description: Det här dokumentet beskriver hur du använder en SAML 2,0-kompatibel IDP för enkel inloggning.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfe3d995cef888d2f0e973a6a6b2a06e0dd6cb54
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563219"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Använda en SAML 2.0-identitetsprovider (IdP) för enkel inloggning

Det här dokumentet innehåller information om hur du använder en SAML 2,0-kompatibel SP-Lite profilbaserade identitets leverantör som prioriterad säkerhetstokentjänst/identitetsprovider. Det här scenariot är användbart när du redan har en användar katalog och ett lösen ord lokalt som kan nås via SAML 2,0. Den befintliga användar katalogen kan användas för inloggning till Microsoft 365 och andra Azure AD-skyddade resurser. SAML 2,0 SP-Lite-profilen baseras på den vanligaste Security Assertion Markup Language (SAML) federerad identitets standard för att tillhandahålla ett utbytes ramverk för inloggning och attribut.

>[!NOTE]
>En lista över IDP: er från tredje part som har testats för användning med Azure AD finns i [kompatibilitetslista för Azure AD-Federation](how-to-connect-fed-compatibility.md)

Microsoft har stöd för den här inloggnings upplevelsen som integrering av en moln tjänst från Microsoft, till exempel Microsoft 365, med korrekt konfigurerad SAML 2,0-profilbaserade IdP. SAML 2,0-identitets leverantörer är produkter från tredje part och därför ger Microsoft inte stöd för distribution, konfiguration, fel sökning av metod tips för dem. När konfigurationen är korrekt konfigurerad kan integrering med SAML 2,0 Identity Provider testas för korrekt konfiguration med hjälp av Microsoft Connectivity Analyzer-verktyget, som beskrivs i detalj nedan. Om du vill ha mer information om SAML 2,0 SP-Lite profilbaserade identitets leverantören ber du den organisation som tillhandahöll den.

> [!IMPORTANT]
> Endast en begränsad uppsättning klienter är tillgängliga i det här inloggnings scenariot med SAML 2,0 Identity providers, däribland:
> 
> - Webbaserade klienter som Outlook Web Access och SharePoint Online
> - E-postomfattande klienter som använder grundläggande autentisering och en Exchange-åtkomst metod som stöds som IMAP, POP, Active Sync, MAPI osv. (den förbättrade klient protokoll slut punkten måste distribueras), inklusive:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (olika iOS-versioner)
>     - Olika Google Android-enheter
>     - Windows Phone 7, Windows Phone 7,8 och Windows Phone 8,0
>     - Windows 8 Mail-klient och Windows 8,1-e-postklient
>     - Windows 10 Mail-klient

Alla andra klienter är inte tillgängliga i det här inloggnings scenariot med din SAML 2,0 Identity Provider. Till exempel kan inte Lync 2010 Desktop-klienten logga in på tjänsten med SAML 2,0-identitetsprovider som kon figurer ATS för enkel inloggning.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Krav för Azure AD SAML 2,0-protokoll
Det här dokumentet innehåller detaljerade krav på protokoll och meddelande format som din SAML 2,0-identitetsprovider måste implementera för att federera med Azure AD för att aktivera inloggning till en eller flera Microsofts moln tjänster (till exempel Microsoft 365). SAML 2,0-förlitande part (SP-STS) för en moln tjänst från Microsoft som används i det här scenariot är Azure AD.

Vi rekommenderar att du ser till att dina e-postmeddelanden med SAML 2,0-identitetsprovider är så likt de tillhandahållna exempel spåren som möjligt. Använd också särskilda attributvärden från de angivna Azure AD-metadata där det är möjligt. När du är nöjd med dina utmatnings meddelanden kan du testa med Microsoft anslutnings analys enligt beskrivningen nedan.

Azure AD-metadata kan hämtas från den här URL: en: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml) .
För kunder i Kina som använder den Kina-angivna instansen av Microsoft 365, ska följande Federations slut punkt användas: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml) .

## <a name="saml-protocol-requirements"></a>Krav för SAML-protokoll
I det här avsnittet beskrivs hur begär ande-och svars meddelande par placeras tillsammans för att hjälpa dig att formatera dina meddelanden på rätt sätt.

Azure AD kan konfigureras för att fungera med identitets leverantörer som använder SAML 2,0 SP lite-profilen med vissa särskilda krav som anges nedan. Med hjälp av exempel på SAML-begäranden och svarsmeddelanden tillsammans med automatiserad och manuell testning kan du arbeta för att uppnå samverkan med Azure AD.

## <a name="signature-block-requirements"></a>Krav på signatur block
I SAML-svarsmeddelandet innehåller noden signatur information om den digitala signaturen för själva meddelandet. Signatur blocket har följande krav:

1. Assertion-noden måste vara signerad
2.  RSA-SHA1-algoritmen måste användas som DigestMethod. Andra algoritmer för digitala signaturer godkänns inte.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Du kan också signera XML-dokumentet. 
4.  Transformation-algoritmen måste matcha värdena i följande exempel:    `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  SignatureMethod-algoritmen måste matcha följande exempel:   `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Bindningar som stöds
Bindningar är de transporter-relaterade kommunikations parametrarna som krävs. Följande krav gäller för bindningarna

1. HTTPS är den obligatoriska transporten.
2.  Azure AD kräver HTTP POST för att skicka token under inloggningen.
3.  Azure AD kommer att använda HTTP POST för autentiseringsbegäran till identitets leverantören och omdirigeringen för meddelandet om utloggning till identitets leverantören.

## <a name="required-attributes"></a>Obligatoriska attribut
I den här tabellen visas krav för särskilda attribut i SAML 2,0-meddelandet.
 
|Attribut|Beskrivning|
| ----- | ----- |
|NameID|Värdet för denna kontroll måste vara samma som Azure AD-användarens ImmutableID. Det kan vara upp till 64 alfanumeriska tecken. Alla icke-HTML-säkra tecken måste kodas, t. ex. visas "+"-tecken som ". 2B".|
|IDPEmail|Användarens huvud namn (UPN) visas i SAML-svaret som ett-element med namnet IDPEmail användarens UserPrincipalName (UPN) i Azure AD/Microsoft 365. UPN är i e-postadressens format. UPN-värde i Windows Microsoft 365 (Azure Active Directory).|
|Utfärdare|Måste vara en URI för identitets leverantören. Återanvänd inte utfärdaren från exempel meddelandena. Om du har flera domäner på den översta nivån i dina Azure AD-klienter måste utfärdaren matcha den angivna URI-inställningen per domän.|

>[!IMPORTANT]
>Azure AD har för närvarande stöd för följande NameID format-URI för SAML 2.0: urn: Oasis: Names: TC: SAML: 2.0: NameID-format: persistent.

## <a name="sample-saml-request-and-response-messages"></a>Exempel på SAML-begäranden och svarsmeddelanden
Meddelande-och svars meddelande paret visas för inloggnings meddelandets utbyte.
Följande är ett exempel på ett begär ande meddelande som skickas från Azure AD till ett exempel på SAML 2,0 Identity Provider. Exempel på SAML 2,0 Identity Provider är Active Directory Federation Services (AD FS) (AD FS) som kon figurer ATS för att använda SAML-P-protokollet. Samverkans testning har också slutförts med andra SAML 2,0-identitets leverantörer.

```xml
    <samlp:AuthnRequest 
        xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
        xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
        ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
        IssueInstant="2014-01-30T16:18:35Z" 
        Version="2.0" 
        AssertionConsumerServiceIndex="0" >
            <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
            <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>
```

Följande är ett exempel svars meddelande som skickas från den exempel på SAML 2,0-kompatibla identitets leverantören till Azure AD/Microsoft 365.

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurera din SAML 2,0-kompatibla identitets leverantör
Det här avsnittet innehåller rikt linjer för hur du konfigurerar din SAML 2,0 Identity-Provider att federera med Azure AD för att aktivera enkel inloggning till en eller flera Microsoft-molntjänster (till exempel Microsoft 365) med hjälp av SAML 2,0-protokollet. SAML 2,0-förlitande part för en moln tjänst från Microsoft som används i det här scenariot är Azure AD.

## <a name="add-azure-ad-metadata"></a>Lägg till Azure AD-metadata
Din SAML 2,0-identitetsprovider måste följa information om den förlitande parten i Azure AD. Azure AD publicerar metadata på https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml .

Vi rekommenderar att du alltid importerar de senaste Azure AD-metadata när du konfigurerar din SAML 2,0 Identity Provider.

>[!NOTE]
>Azure AD läser inte metadata från identitets leverantören.

## <a name="add-azure-ad-as-a-relying-party"></a>Lägg till Azure AD som en förlitande part
Du måste aktivera kommunikation mellan din SAML 2,0 Identity Provider och Azure AD. Den här konfigurationen kommer att vara beroende av din identitets leverantör och du bör läsa dokumentationen för den. Normalt anger du det förlitande part-ID: t till samma som entityID från Azure AD-metadata.

>[!NOTE]
>Kontrol lera att klockan på din SAML 2,0-identitetsprovider är synkroniserad med en korrekt tids källa. En felaktig instämplingstid kan orsaka att federerade inloggningar inte fungerar.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Installera Windows PowerShell för inloggning med SAML 2,0 Identity Provider
När du har konfigurerat din SAML 2,0 Identity Provider för användning med Azure AD-inloggning, är nästa steg att ladda ned och installera Azure Active Directory-modulen för Windows PowerShell. När du har installerat använder du dessa cmdlets för att konfigurera Azure AD-domänerna som federerade domäner.

Azure Active Directory-modulen för Windows PowerShell är en nedladdning för hantering av organisations data i Azure AD. Den här modulen installerar en uppsättning cmdletar till Windows PowerShell. du kör dessa cmdletar för att konfigurera enkel inloggning till Azure AD och i sin tur till alla moln tjänster som du prenumererar på. Instruktioner för hur du hämtar och installerar-cmdletarna finns i [/previous-versions/Azure/jj151815 (v = Azure. 100)](/previous-versions/azure/jj151815(v=azure.100))

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Konfigurera ett förtroende mellan SAML Identity Provider och Azure AD
Innan du konfigurerar federationen på en Azure AD-domän måste den ha en anpassad domän konfigurerad. Du kan inte federera standard domänen som tillhandahålls av Microsoft. Standard domänen från Microsoft slutar med "onmicrosoft.com".
Du kommer att köra en serie cmdlets i Windows PowerShell kommando rads gränssnitt för att lägga till eller konvertera domäner för enkel inloggning.

Varje Azure Active Directory domän som du vill federera med hjälp av din SAML 2,0-identitetsprovider måste antingen läggas till som en enkel inloggnings domän eller konverteras till en enkel inloggnings domän från en standard domän. Genom att lägga till eller konvertera en domän konfigurerar du ett förtroende mellan din SAML 2,0-identitetsprovider och Azure AD.

Följande procedur vägleder dig genom konvertering av en befintlig standard domän till en federerad domän med SAML 2,0 SP-lite. 

>[!NOTE]
>Din domän kan drabbas av ett avbrott som påverkar användare upp till två timmar efter det här steget.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurera en domän i Azure AD-katalogen för Federation


1. Anslut till din Azure AD-katalog som klient organisations administratör:

    ```powershell
    Connect-MsolService
    ```
    
2. Konfigurera önskad Microsoft 365 domän att använda Federation med SAML 2,0:

    ```powershell
    $dom = "contoso.com" 
    $BrandName - "Sample SAML 2.0 IDP" 
    $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
    $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
    $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
    $MyURI = "urn:uri:MySamlp2IDP" 
    $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
    $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
    $Protocol = "SAMLP" 
    Set-MsolDomainAuthentication `
        -DomainName $dom `
        -FederationBrandName $BrandName `
        -Authentication Federated `
        -PassiveLogOnUri $LogOnUrl `
        -ActiveLogOnUri $ecpUrl `
        -SigningCertificate $MySigningCert `
        -IssuerUri $MyURI `
        -LogOffUri $LogOffUrl `
        -PreferredAuthenticationProtocol $Protocol
    ``` 

3.  Du kan hämta signerings certifikatets base64-kodade sträng från din IDP metadata-fil. Ett exempel på den här platsen har angetts men kan skilja sig något beroende på din implementering.

    ```xml
    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <KeyDescriptor use="signing">
          <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
             <X509Data>
                 <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
              </X509Data>
            </KeyInfo>
        </KeyDescriptor>
    </IDPSSODescriptor>
    ``` 

Mer information om "Set-MsolDomainAuthentication" finns i: [/previous-versions/Azure/dn194112 (v = Azure. 100)](/previous-versions/azure/dn194112(v=azure.100)).

>[!NOTE]
>Du måste `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` bara använda om du konfigurerar ett ECP-tillägg för din identitetsprovider. Exchange Online-klienter, förutom Outlook Web Application (OWA), förlitar sig på en POST baserad aktiv slut punkt. Om SAML 2,0 STS implementerar en aktiv slut punkt som liknar shibboleths ECP-implementering av en aktiv slut punkt kan det vara möjligt för dessa rika klienter att samverka med Exchange Online-tjänsten.

När federationen har kon figurer ATS kan du växla tillbaka till "icke-federerad" (eller "hanterad"), men den här ändringen tar upp till två timmar att slutföra och den kräver att du tilldelar nya slumpmässiga lösen ord för molnbaserad inloggning till varje användare. Att växla tillbaka till "hanterad" kan krävas i vissa situationer för att återställa ett fel i inställningarna. Mer information om domän konvertering finns i: [/previous-versions/Azure/dn194122 (v = Azure. 100)](/previous-versions/azure/dn194122(v=azure.100)).

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>Etablera användar huvud konton till Azure AD/Microsoft 365
Innan du kan autentisera dina användare till Microsoft 365 måste du etablera Azure AD med användarens huvud namn som motsvarar intyget i SAML 2,0-anspråket. Om dessa användares huvud namn inte är kända för Azure AD i förväg, kan de inte användas för federerad inloggning. Antingen Azure AD Connect eller Windows PowerShell kan användas för att etablera användar huvud konton.

Azure AD Connect kan användas för att etablera huvud konton för dina domäner i Azure AD-katalogen från den lokala Active Directory. Mer detaljerad information finns i [integrera dina lokala kataloger med Azure Active Directory](whatis-hybrid-identity.md).

Windows PowerShell kan också användas för att automatisera tillägg av nya användare i Azure AD och för att synkronisera ändringar från den lokala katalogen. Om du vill använda Windows PowerShell-cmdlets måste du ladda ned [Azure Active Directory-modulerna](/powershell/azure/active-directory/install-adv2).

Den här proceduren visar hur du lägger till en enskild användare i Azure AD.


1. Anslut till din Azure AD-katalog som innehavaradministratör: Connect-MsolService.
2. Skapa ett nytt huvud namn för användare:

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

Mer information om utcheckningen "New-MsolUser", [/previous-versions/Azure/dn194096 (v = Azure. 100)](/previous-versions/azure/dn194096(v=azure.100))

>[!NOTE]
>Värdet "UserPrincipalName" måste matcha det värde som du ska skicka för "IDPEmail" i ditt SAML 2,0-anspråk och värdet "ImmutableID" måste matcha det värde som skickas i din "NameID"-kontroll.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Verifiera enkel inloggning med SAML 2,0-IDP
Som administratör innan du verifierar och hanterar enkel inloggning (även kallat identitets Federation) kan du läsa informationen och utföra stegen i följande artiklar för att konfigurera enkel inloggning med din SAML 2,0 SP-Lite-baserade identitets leverantör:


1.  Du har granskat kraven för Azure AD SAML 2,0-protokollet
2.  Du har konfigurerat din SAML 2,0 Identity Provider
3.  Installera Windows PowerShell för enkel inloggning med SAML 2,0 Identity Provider
4.  Konfigurera ett förtroende mellan SAML 2,0 Identity Provider och Azure AD
5.  Etablerade ett känt test användares huvud namn till Azure Active Directory (Microsoft 365) antingen via Windows PowerShell eller Azure AD Connect.
6.  Konfigurera katalog-synkronisering med [Azure AD Connect](whatis-hybrid-identity.md).

När du har konfigurerat enkel inloggning med din SAML 2,0 SP-Lite-baserade identitetsprovider, bör du kontrol lera att den fungerar som den ska.

>[!NOTE]
>Om du har konverterat en domän, i stället för att lägga till en, kan det ta upp till 24 timmar innan du konfigurerar enkel inloggning.
Innan du verifierar enkel inloggning bör du slutföra konfigurationen av Active Directory synkronisering, synkronisera dina kataloger och aktivera dina synkroniserade användare.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Använd verktyget för att kontrol lera att enkel inloggning har kon figurer ATS korrekt
För att kontrol lera att enkel inloggning har ställts in korrekt, kan du utföra följande procedur för att bekräfta att du kan logga in på moln tjänsten med företagets autentiseringsuppgifter.

Microsoft tillhandahåller ett verktyg som du kan använda för att testa din SAML 2,0-baserade identitets leverantör. Innan du kör test verktyget måste du ha konfigurerat en Azure AD-klient för att federera med din identitets leverantör.

>[!NOTE]
>Anslutnings analys kräver Internet Explorer 10 eller senare.



1. Hämta anslutnings analys från, [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client) .
2.  Klicka på Installera nu för att börja ladda ned och installera verktyget.
3.  Välj "Jag kan inte konfigurera Federation med Office 365, Azure eller andra tjänster som använder Azure Active Directory".
4.  När verktyget har laddats ned och körs visas fönstret anslutnings diagnos. Verktyget kommer att gå igenom hur du testar din Federations anslutning.
5.  Anslutnings analysen öppnar din SAML 2,0-IDP så att du kan logga in, ange autentiseringsuppgifterna för det användar huvud som du testar: ![ skärm bild som visar inloggnings fönstret för SAML 2,0-IDP.](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  I fönstret för Federations test inloggning ska du ange ett konto namn och lösen ord för den Azure AD-klient som har kon figurer ATS för federerad med din SAML 2,0-identitetsprovider. Verktyget försöker logga in med de här autentiseringsuppgifterna och detaljerade resultat från tester som utförs under inloggnings försöket visas som utdata.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Det här fönstret visar ett undersöknings resultat som inte kunde utföras. Om du klickar på granska detaljerade resultat visas information om resultatet för varje test som utfördes. Du kan också spara resultatet till disk för att dela dem.
 
>[!NOTE]
>Anslutnings analys testar också en aktiv Federation med hjälp av WS *-och ECP/PAOS-protokollen. Om du inte använder dessa kan du bortse från följande fel: testa det aktiva inloggnings flödet med hjälp av din identitets leverantörs aktiva Federations slut punkt.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verifiera manuellt att enkel inloggning har kon figurer ATS korrekt
Manuell verifiering ger ytterligare åtgärder som du kan vidta för att se till att din SAML 2,0 Identity-Provider fungerar korrekt i många scenarier.
Utför följande steg för att kontrol lera att enkel inloggning har ställts in korrekt:


1. På en domänansluten dator loggar du in på moln tjänsten med samma inloggnings namn som du använder för företagets autentiseringsuppgifter.
2.  Klicka i rutan lösen ord. Om enkel inloggning har kon figurer ATS skuggas rutan lösen ord och följande meddelande visas: "du måste nu logga in på &lt; företaget &gt; ".
3.  Klicka på inloggningen på &lt; företagets &gt; länk. Om du kan logga in har enkel inloggning kon figurer ATS.

## <a name="next-steps"></a>Nästa steg


- [Active Directory Federation Services (AD FS) hantering och anpassning med Azure AD Connect](how-to-connect-fed-management.md)
- [Kompatibilitetslista för Azure AD-federation](how-to-connect-fed-compatibility.md)
- [Azure AD Connect anpassad installation](how-to-connect-install-custom.md)
