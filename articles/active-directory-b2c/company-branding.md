---
title: Lägg till anpassning till din organisations inloggnings sida
titleSuffix: Azure AD B2C
description: Lär dig hur du lägger till din organisations anpassning till Azure Active Directory B2C sidor.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111499"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Lägg till anpassning till din organisations Azure Active Directory B2C sidor

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Du kan anpassa dina Azure AD B2C sidor med en banderoll, bakgrunds bild och bakgrunds färg med hjälp av Azure Active Directory [företags anpassning](../active-directory/fundamentals/customize-branding.md). Företags anpassningen omfattar registrering, inloggning, profil redigering och återställning av lösen ord. 

> [!TIP]
> Information om hur du anpassar andra aspekter av dina användar flödes sidor utöver banderollens logo typ, bakgrunds bild eller bakgrunds färg finns i [Anpassa användar gränssnittet med HTML-mall](customize-ui-with-html.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Om du vill anpassa dina användar flödes sidor konfigurerar du först företags anpassning i Azure Active Directory. Därefter aktiverar du den i sidlayouten för dina användar flöden i Azure AD B2C.

## <a name="configure-company-branding"></a>Konfigurera varumärkesexponering

Börja med att ange banderoll, bakgrunds bild och bakgrunds färg inom **företags anpassning**.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **Hantera** väljer du **företags anpassning**.
1. Följ stegen i [lägga till anpassning till din organisations Azure Active Directory inloggnings sida](../active-directory/fundamentals/customize-branding.md).

Tänk på följande när du konfigurerar företags anpassning i Azure AD B2C:

* Företags anpassning i Azure AD B2C är för närvarande begränsad till **bakgrunds bild**, **banderoll-logotyp** och **bakgrunds färg** anpassning. De andra egenskaperna i rutan företags anpassning, till exempel de i **Avancerade inställningar**, *stöds inte*.
* Bakgrunds färgen visas i dina användar flödes sidor innan bakgrunds bilden läses in. Vi rekommenderar att du väljer en bakgrunds färg som nära matchar färgerna i bakgrunds bilden för en smidig inläsnings upplevelse.
* Banderoll-logotypen visas i bekräftelse-e-postmeddelanden som skickas till användarna när de initierar ett registrerings användar flöde.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Aktivera anpassning på användar flödes sidor

När du har konfigurerat företags anpassning aktiverar du det i dina användar flöden.

1. På den vänstra menyn i Azure Portal väljer du **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden (principer)**.
1. Välj det användar flöde som du vill aktivera företags anpassning för. Företags anpassning **stöds inte** för standard *inloggnings* -och standard *profil redigering* av användar flödes typer.
1. Under **Anpassa** **väljer du sidlayouter och** väljer sedan den layout som du vill anpassa. Välj till exempel **enhetlig registrering eller inloggnings sida**.
1. För sidlayouten **(för hands version)** väljer du version **1.2.0** eller senare.
1. Välj **Spara**.

Om du vill anpassa alla sidor i användar flödet anger du sidlayouten för varje sidlayout i användar flödet.

![Val av sidlayout i Azure AD B2C i Azure Portal](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Välj en sidlayout

Om du vill aktivera företags anpassning måste du [definiera en version för sidlayouten](contentdefinitions.md#migrating-to-page-layout) med sid `contract` version för *alla* innehålls definitioner i din anpassade princip. Formatet för värdet måste innehålla ordet `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**kontrakt**:p ålder-Name: version_. Om du vill ange en sidlayout i dina anpassade principer som använder ett gammalt **DataUri** -värde.

Lär dig hur du [migrerar till](contentdefinitions.md#migrating-to-page-layout) sidlayouten med Page version.

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

I följande exempel visas en anpassad logo typ och bakgrunds bild på sidan *Registrera dig och logga* in på användar flöde som använder den blåa mallen i Atlanten:

![Anpassad registrerings-och inloggnings sida som hanteras av Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Använda företags anpassnings till gångar i anpassad HTML

Om du vill använda företagets anpassnings till gångar i [anpassad HTML](customize-ui-with-html.md)lägger du till följande Taggar utanför `<div id="api">` taggen:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Bild källan ersätts med bakgrunds bildens och banderollens logo typ.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan anpassa användar gränssnittet för dina program finns i [Anpassa användar gränssnittet för ditt program i Azure Active Directory B2C](customize-ui-with-html.md).