---
title: Definiera en teknisk profil för en SAML-utfärdare i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för en Security Assertion Markup Language token-utfärdare (SAML) i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bb5383ee7930cb3d54593f71a709c033d3850889
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521220"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för en SAML-token utfärdare i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) genererar flera typer av säkerhetstoken när de bearbetar varje autentiseringspaket. En teknisk profil för en SAML-token utfärdare genererar en SAML-token som returneras tillbaka till den förlitande part appen (tjänst leverantören). Vanligt vis är den här tekniska profilen det sista Orchestration-steget i användar resan.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `None` . Ange **OutputTokenFormat** -elementet till `SAML2` .

I följande exempel visas en teknisk profil för `Saml2AssertionIssuer` :

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Indata, utdata och beständiga anspråk

**InputClaims**-, **OutputClaims**-och **PersistClaims** -elementen är tomma eller saknas. **InutputClaimsTransformations** -och **OutputClaimsTransformations** -element saknas också.

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| IssuerUri | No | Utfärdarens namn som visas i SAML-svaret. Värdet ska vara samma namn som det som kon figurer ATS i programmet för förlitande part. |
| XmlSignatureAlgorithm | No | Metoden som Azure AD B2C använder för att signera SAML-kontrollen. Möjliga värden: `Sha256` , `Sha384` , `Sha512` eller `Sha1` . Se till att du konfigurerar signeringsalgoritmen på båda sidor med samma värde. Använd bara den algoritm som ditt certifikat stöder. Om du vill konfigurera SAML-svaret, se [SAML-metadata för förlitande part](relyingparty.md#metadata)|

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

CryptographicKeys-elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| MetadataSigning | Yes | X509-certifikatet (RSA-nyckel uppsättning) som används för att signera SAML-metadata. Azure AD B2C använder den här nyckeln för att signera metadata. |
| SamlMessageSigning| Yes| Ange det X509-certifikat (RSA-nyckel uppsättning) som ska användas för att signera SAML-meddelanden. Azure AD B2C använder den här nyckeln för att signera svaret `<samlp:Response>` som skickas till den förlitande parten.|

## <a name="session-management"></a>Sessionshantering

För att konfigurera Azure AD B2C SAML-sessioner mellan ett förlitande part program, attribut för `UseTechnicalProfileForSessionManagement` -elementet, referens till [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO-session.

## <a name="next-steps"></a>Nästa steg

Se följande artikel till exempel om du använder en teknisk profil för SAML Issuer:

- [Registrera ett SAML-program i Azure AD B2C](connect-with-saml-service-providers.md)

