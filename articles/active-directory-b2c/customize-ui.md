---
title: Anpassa användargränssnittet
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användar gränssnittet för dina program som använder Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99056119"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Anpassa användar gränssnittet i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Anpassning och anpassning av användar gränssnittet som Azure Active Directory B2C (Azure AD B2C) visas för dina kunder, vilket ger en smidig användar upplevelse i ditt program. Dessa upplevelser omfattar registrering, inloggning, profil redigering och återställning av lösen ord. I den här artikeln kan du anpassa dina Azure AD B2C sidor med hjälp av sidmall och företags anpassning. 

> [!TIP]
> Information om hur du anpassar andra aspekter av användar flödes sidorna utöver sid mal len, banderoll, bakgrunds bild eller bakgrunds färg finns i så här [anpassar du användar gränssnittet med HTML-mall](customize-ui-with-html.md).

## <a name="prerequisites"></a>Krav

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Översikt

Azure AD B2C innehåller flera inbyggda mallar som du kan välja mellan för att ge dina användar upplevelse sidor ett professionellt utseende. Dessa sidmallar kan också fungera som utgångs punkt för din egen anpassning, med hjälp av funktionen för [företags anpassning](#company-branding) .

### <a name="ocean-blue"></a>Havet, blå

Exempel på den blåa mallen i Atlanten som återges på inloggnings sidan för registrering:

![Skärm bild för blå mall-Atlanten](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Platta, grå

Exempel på den bakgrunds grå mall som återges på inloggnings sidan för registrering:

![Skärm bild för grå bakgrunds mall](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Klassisk

Exempel på den klassiska mallen som återges vid registrering av inloggnings sida:

![Skärm bild av klassisk mall](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Företagsanpassning

Du kan anpassa dina Azure AD B2C sidor med en banderoll, bakgrunds bild och bakgrunds färg med hjälp av Azure Active Directory [företags anpassning](../active-directory/fundamentals/customize-branding.md). Företags anpassningen omfattar registrering, inloggning, profil redigering och återställning av lösen ord. 

I följande exempel visas en *registrerings-och inloggnings* sida med en anpassad logo typ, bakgrunds bild, med den blåa, blåa mallen:

![Anpassad registrerings-och inloggnings sida som hanteras av Azure AD B2C](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Välj en sidmall

::: zone pivot="b2c-user-flow"

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **användar flöden**.
1. Välj ett användar flöde som du vill anpassa.
1. Välj **sidlayouter** under **Anpassa** på den vänstra menyn och välj sedan en **mall**.
    ![Listruta för val av mall på sidan användar flöde i Azure Portal](./media/customize-ui/select-page-template.png)

När du väljer en mall tillämpas den valda mallen på alla sidor i ditt användar flöde. URI: n för varje sida visas i fältet **URI för anpassad sida** .

::: zone-end

::: zone pivot="b2c-custom-policy"

Om du vill välja en sidmall ställer du in `LoadUri` elementet i [innehålls definitionerna](contentdefinitions.md). I följande exempel visas innehålls definitions identifierarna och motsvarande `LoadUri` . 

Havet, blå:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Bakgrunds grått:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Form 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Konfigurera varumärkesexponering

Om du vill anpassa dina användar flödes sidor konfigurerar du först företags anpassning i Azure Active Directory. sedan aktiverar du det i dina användar flöden i Azure AD B2C.

Börja med att ange banderoll, bakgrunds bild och bakgrunds färg inom **företags anpassning**.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **Hantera** väljer du **företags anpassning**.
1. Följ stegen i [lägga till anpassning till din organisations Azure Active Directory inloggnings sida](../active-directory/fundamentals/customize-branding.md).

Tänk på följande när du konfigurerar företags anpassning i Azure AD B2C:

* Företags anpassning i Azure AD B2C är för närvarande begränsad till **bakgrunds bild**, **banderoll-logotyp** och **bakgrunds färg** anpassning. De andra egenskaperna i rutan för företags anpassning, till exempel **Avancerade inställningar**, *stöds inte*.
* Bakgrunds färgen visas i dina användar flödes sidor innan bakgrunds bilden läses in. Vi rekommenderar att du väljer en bakgrunds färg som nära matchar färgerna i bakgrunds bilden för en smidig inläsnings upplevelse.
* Banderoll-logotypen visas i bekräftelse-e-postmeddelanden som skickas till användarna när de initierar ett registrerings användar flöde.



## <a name="enable-company-branding-in-user-flow-pages"></a>Aktivera företags anpassning på användar flödes sidor

::: zone pivot="b2c-user-flow"

När du har konfigurerat företags anpassning aktiverar du det i dina användar flöden.

1. På den vänstra menyn i Azure Portal väljer du **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden (principer)**.
1. Välj det användar flöde som du vill aktivera företags anpassning för. Företags anpassning **stöds inte** för standard *inloggnings* -och standard *profil redigering* av användar flödes typer.
1. Under **Anpassa** **väljer du sidlayouter och** väljer sedan den sida som du vill anpassa. Välj till exempel **enhetlig registrering eller inloggnings sida**.
1. För sidlayouten **(för hands version)** väljer du version **1.2.0** eller senare.
1. Välj **Spara**.

Om du vill anpassa alla sidor i användar flödet anger du sidlayouten för varje sidlayout i användar flödet.

![Val av sidlayout i Azure AD B2C i Azure Portal](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

När du har konfigurerat företags anpassning aktiverar du den i din anpassade princip. Konfigurera [versionen](contentdefinitions.md#migrating-to-page-layout) av sidlayouten med sid `contract` version för *alla* innehålls definitioner i den anpassade principen. Formatet för värdet måste innehålla ordet `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**kontrakt**:p ålder-Name: version_. Om du vill ange en sidlayout i dina anpassade principer som använder ett gammalt **DataUri** -värde. Mer information finns i så här [migrerar du till](contentdefinitions.md#migrating-to-page-layout) sidlayouten med sid version.

I följande exempel visas innehålls definitionerna med deras motsvarande sid kontrakt och den blå sid mal len *havs* : 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan anpassa användar gränssnittet för dina program finns i [Anpassa användar gränssnittet för ditt program i Azure Active Directory B2C](customize-ui-with-html.md).
