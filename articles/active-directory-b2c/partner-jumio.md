---
title: Självstudie för att konfigurera Azure Active Directory B2C med Jumio
titleSuffix: Azure AD B2C
description: I den här självstudien konfigurerar du Azure Active Directory B2C med Jumio för automatisk ID-verifiering, vilket skyddar kund information.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 66ec0d4b09dc983eb898d63d45b3dd7cab291c4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928672"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera Jumio med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar Azure Active Directory B2C (Azure AD B2C) med [Jumio](https://www.jumio.com/). Jumio är en tjänst för ID-verifiering som möjliggör verifiering av automatiserat ID i real tid för att skydda kund information.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](./tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

Jumio-integreringen innehåller följande komponenter:

- Azure AD B2C: den auktoriserade server som ansvarar för att verifiera användarens autentiseringsuppgifter. Det kallas även för identitets leverantören.

- Jumio: tjänsten som tar de ID-uppgifter som användaren har fått och som verifierar dem.

- Mellanliggande REST API: API: et som implementerar integreringen mellan Azure AD B2C och Jumio-tjänsten.

- Azure Blob Storage: tjänsten som tillhandahåller anpassade UI-filer till Azure AD B2C principer.

I följande arkitektur diagram visas implementeringen.

![Diagram över arkitekturen i en Azure AD B2C-integrering med Jumio.](./media/partner-jumio/jumio-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | Användaren kommer till en sida för att antingen logga in eller registrera dig för att skapa ett konto. Azure AD B2C samlar in användarattribut.
| 2. | Azure AD B2C anropar API: t i mitten och passerar användar attributen.
| 3. | API för mellanlager samlar in användarattribut och omvandlar dem till ett format som Jumio-API: n kan använda. Sedan skickar den attributen till Jumio.
| 4. | När Jumio förbrukar informationen och bearbetar den returneras resultatet till API för mellanlager.
| 5. | API för mellanlager bearbetar informationen och skickar tillbaka relevant information till Azure AD B2C.
| 6. | Azure AD B2C tar emot information tillbaka från API för mellanlager. Om det visar ett felsvar visas ett fel meddelande för användaren. Om det visar sig ett lyckat svar, autentiseras och skrivs användaren till katalogen.

## <a name="sign-up-with-jumio"></a>Registrera dig med Jumio

Kontakta [Jumio](https://www.jumio.com/contact/)om du vill skapa ett Jumio-konto.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Konfigurera Azure AD B2C med Jumio

När du har skapat ett Jumio-konto kan du använda kontot för att konfigurera Azure AD B2C. I följande avsnitt beskrivs processen i tur och ordning.

### <a name="deploy-the-api"></a>Distribuera API: et

Distribuera den tillhandahållna [API-koden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) till en Azure-tjänst. Du kan publicera koden från Visual Studio genom att följa [dessa anvisningar](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Du behöver URL: en för den distribuerade tjänsten för att konfigurera Azure AD med nödvändiga inställningar.

### <a name="deploy-the-client-certificate"></a>Distribuera klient certifikatet

1. Ett klient certifikat skyddar Jumio-API-anropet. Skapa ett självsignerat certifikat med hjälp av följande PowerShell-exempel kod:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   Certifikatet exporteras sedan till den plats som angetts för ``{your-local-path}`` .

3. Importera certifikatet till Azure App Service genom att följa anvisningarna i [den här artikeln](../app-service/configure-ssl-certificate.md#upload-a-private-certificate).

### <a name="create-a-signingencryption-key"></a>Skapa en signerings-/krypterings nyckel

Skapa en slumpmässig sträng med en längd på högst 64 tecken som bara innehåller bokstäver och siffror.

Exempelvis: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Använd följande PowerShell-skript för att skapa strängen:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>Konfigurera API

Du kan [Konfigurera program inställningar i Azure App Service](../app-service/configure-common.md#configure-app-settings). Med den här metoden kan du konfigurera inställningar på ett säkert sätt utan att kontrol lera dem i en lagrings plats. Du måste ange följande inställningar för REST-API: et:

| Programinställningar | Källa | Kommentarer |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Konfiguration av Jumio-konto |     |
|JumioSettings:AuthPassword | Konfiguration av Jumio-konto |     |
|AppSettings: SigningCertThumbprint|Tumavtryck för det skapade självsignerade certifikatet|  |
|AppSettings: IdTokenSigningKey| Signerings nyckel som skapats med PowerShell | |
| AppSettings: IdTokenEncryptionKey |Krypterings nyckel som skapats med PowerShell
| AppSettings: IdTokenIssuer | Utfärdare som ska användas för JWT-token (ett GUID-värde rekommenderas) |
| AppSettings: IdTokenAudience  | Mål grupp som ska användas för JWT-token (ett GUID-värde rekommenderas) |
|AppSettings: BaseRedirectUrl | Bas-URL för den Azure AD B2C principen | https://{din-Tenant-Name}. b2clogin. com/{ditt-Application-ID}|
| WEBSITE_LOAD_CERTIFICATES| Tumavtryck för det skapade självsignerade certifikatet |

### <a name="deploy-the-ui"></a>Distribuera användar gränssnittet

1. Konfigurera en [Blob Storage-behållare i ditt lagrings konto](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

2. Lagra UI-filerna från [mappen UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) i BLOB-behållaren.

#### <a name="update-ui-files"></a>Uppdatera UI-filer

1. I UI-filerna går du till mappen [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Öppna varje HTML-fil.

3. Sök och Ersätt `{your-ui-blob-container-url}` med URL: en för din BLOB-behållare.

4. Sök och Ersätt `{your-intermediate-api-url}` med URL: en för mellanliggande API app-tjänsten.

>[!NOTE]
> Som bästa praxis rekommenderar vi att du lägger till medgivande meddelande på sidan samling av attribut. Meddela användarna att informationen kommer att skickas till tjänster från tredje part för identitets verifiering.

### <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurera principen för Azure AD B2C

1. Gå till [Azure AD B2C principen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) i mappen principer.

2. Följ [den här artikeln](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) för att ladda ned [LocalAccounts start paket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

3. Konfigurera principen för Azure AD B2C klient.

>[!NOTE]
>Uppdatera de angivna principerna så att de relaterar till din angivna klient.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten. Under **principer** väljer du **Identity Experience Framework**.

2. Välj din tidigare skapade **SignUpSignIn**.

3. Välj **Kör användar flöde** och sedan:

   a. För **program** väljer du den registrerade appen (EXEMPLET är JWT).

   b. För **svars-URL** väljer du **omdirigerings-URL**: en.

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto.

5. Jumio-tjänsten kommer att anropas under flödet efter att attributet User har skapats. Om flödet är ofullständigt kontrollerar du att användaren inte har sparats i katalogen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
