---
title: Azure Active Directory SMART på FHIR-proxy
description: I den här självstudien beskrivs hur du använder en proxy för att aktivera SMART on FHIR-program med Azure API för FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020729"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Självstudie: Azure Active Directory SMART on FHIR proxy

[Smart på FHIR](https://docs.smarthealthit.org/) är en uppsättning öppna specifikationer för att integrera partner program med FHIR-servrar och elektroniska medicinska register system som har FHIR-gränssnitt. Ett av de främsta syftena med specifikationerna är att beskriva hur ett program ska identifiera autentiserings slut punkter för en FHIR-Server och starta en autentiseringsprovider. 

Autentiseringen baseras på OAuth2. Men eftersom SMART på FHIR använder parameter namn konventioner som inte är direkt kompatibla med Azure Active Directory (Azure AD), har Azure API för FHIR en inbyggd Azure AD SMART på FHIR-proxy som möjliggör en delmängd av SMART on FHIR-startsekvenser. Mer specifikt aktiverar proxyn [EHR-startsekvensen](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence).

I den här självstudien beskrivs hur du använder proxyn för att aktivera SMART on FHIR-program med Azure-API: t för FHIR.

## <a name="prerequisites"></a>Förutsättningar

- En instans av Azure API för FHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Konfigurera Azure AD-registreringar

SMART on FHIR kräver att `Audience` har en ID-URI som är lika med URI: n för FHIR-tjänsten. Standard konfigurationen av Azure API för FHIR använder `Audience` värdet `https://azurehealthcareapis.com` . Du kan dock också ange ett värde som matchar den speciella URL: en för din FHIR-tjänst (till exempel `https://MYFHIRAPI.azurehealthcareapis.com` ). Detta krävs när du arbetar med SMART on FHIR-proxyn.

Du behöver också en registrering av klient program. De flesta smarta FHIR-program är ett JavaScript-program med en enda sida. Följ anvisningarna för att konfigurera ett [offentligt Azure AD-klientcertifikat](register-public-azure-ad-client-app.md).

När du har slutfört de här stegen bör du ha:

- En FHIR-server med RGE-målgruppen inställd på `https://MYFHIRAPI.azurehealthcareapis.com` , där `MYFHIRAPI` är namnet på din Azure API för FHIR-instans.
- En offentlig klient program registrering. Anteckna program-ID för det här klient programmet.

## <a name="enable-the-smart-on-fhir-proxy"></a>Aktivera SMART on FHIR-proxy

Aktivera SMART on FHIR-proxy i **autentiseringsinställningarna** för ditt Azure API för FHIR-instansen genom att markera kryss rutan **Smart on FHIR proxy** :

![Alternativ för att aktivera SMART on FHIR-proxy](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>Aktivera CORS

Eftersom de flesta smarta FHIR-program är enkla JavaScript-appar måste du [Aktivera resurs delning mellan ursprung (CORS)](configure-cross-origin-resource-sharing.md) för Azure API för FHIR:

![Val för att aktivera CORS](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>Konfigurera svars-URL

SMART on FHIR-proxy fungerar som en mellanhand mellan SMART on FHIR-appen och Azure AD. Svaret (autentiseringsmetoden) måste gå till SMART on FHIR-proxy i stället för själva appen. Proxyn vidarebefordrar sedan svaret till appen. 

Därför måste du ange svars-URL (motringning) för ditt Azure AD-klientprogram till en URL som är en kombination av svars-URL: en för SMART on FHIR-proxy och svars-URL: en för SMART on FHIR-appen. Den kombinerade svars-URL: en tar det här formuläret:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

I det svaret `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` är en URL-säker Base64-kodad version av svars-URL: en för den smarta FHIR-appen. När appen körs lokalt är svars-URL: en för SMART on FHIR-app starta när appen körs lokalt `https://localhost:5001/sampleapp/index.html` . 

Du kan skapa den kombinerade svars-URL: en med hjälp av ett skript som detta:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Lägg till svars-URL: en till det offentliga klient program som du skapade tidigare för Azure AD:

![Svars-URL konfigurerad för den offentliga klienten](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Skaffa en test-patient

Om du vill testa Azure API för FHIR och SMART on FHIR-proxyn måste du ha minst en patient i databasen. Om du inte har interagerat med API: et och du inte har data i databasen följer du [själv studie kursen om FHIR API Postman](access-fhir-postman-tutorial.md) för att läsa in en patient. Anteckna ID: t för en speciell patient.

## <a name="download-the-smart-on-fhir-app-launcher"></a>Hämta SMART on FHIR app starter

FHIR-servern med öppen källkod [för Azure-lagringsplatsen](https://github.com/Microsoft/fhir-server) innehåller en enkel Smart on FHIR app-start och ett exempel Smart på FHIR-appen. I den här självstudien använder du den här SMART-FHIR Launcher lokalt för att testa installationen.

Du kan klona GitHub-lagringsplatsen och gå till programmet med hjälp av följande kommandon:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

Programmet behöver några konfigurations inställningar, som du kan ange i `appsettings.json` :

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

Vi rekommenderar att du använder `dotnet user-secrets` funktionen:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Använd det här kommandot för att köra programmet:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>Testa SMART on FHIR-proxyn

När du har startat programmet SMART on FHIR app (starta) kan du peka din webbläsare på `https://localhost:5001` , där du bör se följande skärm:

![SMART on FHIR app starter](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

När du anger **patient**, **stötande** eller **praktiserande** information ser du att **Start kontexten** har uppdaterats. När du använder Azure API för FHIR är start kontexten bara ett JSON-dokument som innehåller information om patient, läkare och annat. Den här start kontexten är Base64-kodad och skickas till SMART on FHIR-appen som `launch` Frågeparametern. Enligt SMART on FHIR-specifikationen är den här variabeln ogenomskinlig för SMART on FHIR-appen och skickas till identitets leverantören. 

SMART on FHIR-proxyn använder den här informationen för att fylla i fält i svar på token. Den smarta FHIR-appen *kan* använda dessa fält för att styra vilken patient som begär data för och hur den återger programmet på skärmen. SMART on FHIR-proxyn har stöd för följande fält:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Dessa fält är avsedda att ge vägledning till appen, men de förmedlar ingen säkerhets information. Ett SMART on FHIR-program kan ignorera dem.

Observera att **start-URL** -informationen uppdateras längst ned på sidan i FHIR app start. Välj **Starta** för att starta exempel appen och se något som liknar det här exemplet:

![SMART on FHIR-app](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Kontrol lera svaret på token för att se hur fälten för att starta kontexten skickas till appen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat Azure Active Directory SMART på FHIR-proxy. Om du vill utforska användningen av SMART on FHIR-program med Azure API för FHIR och FHIR-servern med öppen källkod för Azure går du till lagrings platsen för FHIR-Server exempel på GitHub:

>[!div class="nextstepaction"]
>[FHIR Server-exempel](https://github.com/Microsoft/fhir-server-samples)
