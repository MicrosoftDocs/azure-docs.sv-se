---
title: Versioner av Java Script och sidlayout
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar Java Script och använder versioner av sidlayouten i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d234e57f7c11b0d9f2cd212bde93a8b8e478ef41
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781372"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Java Script och versioner av sidlayouten i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C innehåller en uppsättning paketerat innehåll som innehåller HTML, CSS och Java Script för användar gränssnitts elementen i dina användar flöden och anpassade principer. Så här aktiverar du Java Script för dina program:

::: zone pivot="b2c-user-flow"

* Välj en [Sidlayout](page-layout.md)
* Aktivera det i användar flödet med hjälp av Azure Portal
* Använd [b2clogin.com](b2clogin.md) i dina begär Anden

::: zone-end

::: zone pivot="b2c-custom-policy"

* Välj en [Sidlayout](page-layout.md)
* Lägg till ett element i din [anpassade princip](custom-policy-overview.md)
* Använd [b2clogin.com](b2clogin.md) i dina begär Anden

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Välj en version av sidlayouten

Om du tänker aktivera Java Script på klient sidan måste de element som du baserar Java Script på vara oföränderliga. Om de inte är oföränderliga kan eventuella ändringar orsaka oväntade beteenden på dina användar sidor. Du kan förhindra dessa problem genom att använda en sidlayout och ange en version för sidlayouten för att se till att de innehålls definitioner som du har baserat java script är oföränderliga. Även om du inte planerar att aktivera Java Script kan du ange en version för sidlayouten för dina sidor.

::: zone pivot="b2c-user-flow"

Så här anger du en version för sidlayouten för dina användar flödes sidor: 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Välj **sidlayouter**. Välj ett **namn för layout** och välj sedan sidlayouten **(för hands version)**.

Information om olika versioner av sidlayouten finns i [versions ändrings loggen för sidlayouten](page-layout.md).

![Inställningar för sidlayout i portalen med listruta för sidlayout](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Välj en [Sidlayout](contentdefinitions.md#select-a-page-layout) för användar gränssnitts elementen i programmet.

Definiera en [version](contentdefinitions.md#migrating-to-page-layout) av sidlayouten med sid `contract` version för *alla* innehålls definitioner i den anpassade principen. Formatet för värdet måste innehålla ordet `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**kontrakt**:p ålder-Name: version_. Lär dig hur du [migrerar till](contentdefinitions.md#migrating-to-page-layout) sidlayouten med Page version.

I följande exempel visas innehålls definitions identifierarna och motsvarande **DataUri** med sid kontrakt: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>Aktivera JavaScript

::: zone pivot="b2c-user-flow"

I **egenskaperna** för användar flödet kan du aktivera Java Script. Genom att aktivera Java Script används även en sidlayout. Du kan sedan ange versionen för sid layouten för användar flödet enligt beskrivningen i nästa avsnitt.

![Sidan Egenskaper för användar flöde med alternativet Aktivera Java Script markerat](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Du aktiverar skript körning genom att lägga till **ScriptExecution** -elementet i [RelyingParty](relyingparty.md) -elementet.

1. Öppna din anpassade princip fil. Till exempel *SignUpOrSignin.xml*.
1. Lägg till **ScriptExecution** -elementet i **RelyingParty** -elementet:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Spara och ladda upp filen.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Rikt linjer för att använda Java Script

Följ dessa rikt linjer när du anpassar gränssnittet för ditt program med hjälp av Java Script:

- Bind inte en klick händelse på `<a>` HTML-element.
- Ta inte ett beroende på Azure AD B2C kod eller kommentarer.
- Ändra inte ordningen eller hierarkin för Azure AD B2C HTML-element. Använd en Azure AD B2C-princip för att kontrol lera ordningen på GRÄNSSNITTs elementen.
- Du kan anropa en RESTful-tjänst med följande överväganden:
    - Du kan behöva ange din RESTful-tjänst-CORS för att tillåta HTTP-anrop på klient sidan.
    - Kontrol lera att din RESTful-tjänst är säker och använder endast HTTPS-protokollet.
    - Använd inte Java Script direkt för att anropa Azure AD B2C slut punkter.
- Du kan bädda in Java Script eller länka till externa JavaScript-filer. När du använder en extern JavaScript-fil ska du se till att använda den absoluta URL: en och inte en relativ URL.
- JavaScript-ramverk:
    - Azure AD B2C använder en [angiven version av jQuery](page-layout.md#jquery-version). Ta inte med en annan version av jQuery. Om du använder mer än en version på samma sida uppstår problem.
    - Användning av RequireJS stöds inte.
    - De flesta JavaScript-ramverk stöds inte av Azure AD B2C.
- Azure AD B2C inställningar kan läsas genom att anropa `window.SETTINGS` , `window.CONTENT` objekt, till exempel det aktuella gränssnitts språket. Ändra inte värdet för dessa objekt.
- Om du vill anpassa Azure AD B2C fel meddelandet använder du lokalisering i en princip.
- Om något kan uppnås med hjälp av en princip är det vanligt vis det rekommenderade sättet.

## <a name="javascript-samples"></a>JavaScript-exempel

### <a name="show-or-hide-a-password"></a>Visa eller Dölj ett lösen ord

Ett vanligt sätt att hjälpa dina kunder med att registrera sig, är att låta dem se vad de har angett som lösen ord. Med det här alternativet kan användarna registrera sig genom att låta dem enkelt se och göra ändringar i lösen ordet om det behövs. Alla fält av typen lösen ord har en kryss ruta med etiketten **Visa lösen ord** .  Detta gör det möjligt för användaren att se lösen ordet som oformaterad text. Ta med det här kodfragmentet i din registrerings-eller inloggnings mall för en egen kontrollerad sida:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Lägg till användnings villkor

Inkludera följande kod på sidan där du vill inkludera en **användnings villkors** kryss ruta. Den här kryss rutan behövs vanligt vis på registrerings sidorna för ditt lokala konto och sociala konton.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

I koden ersätter du `termsOfUseUrl` med länken till dina användnings villkor. Skapa ett nytt användarattribut med namnet **termsofuse** i din katalog och ta sedan med **termsofuse** som användar-attribut.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan anpassa användar gränssnittet för dina program finns i [Anpassa användar gränssnittet för ditt program i Azure Active Directory B2C](customize-ui-with-html.md).
