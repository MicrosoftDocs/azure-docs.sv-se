---
title: 'Gör så här: ändra de konto typer som stöds av ett program | Azure'
titleSuffix: Microsoft identity platform
description: I den här instruktionen kan du konfigurera ett program som är registrerat med Microsoft Identity Platform för att ändra vem eller vilka konton som har åtkomst till programmet.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 3ae6616263de605d5910f244423b9e7ffc036c5d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080351"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>Ändra vilka konton som stöds av ett program

När du registrerade ditt program med Microsoft Identity Platform angav du vem--vilka konto typer--har åtkomst till den. Du kan till exempel ha specificerat enbart konton i din organisation, som är en app med en *enda klient* . Eller så kanske du har angett konton i en organisation (inklusive ditt), som är en app för *flera klienter* .

I följande avsnitt får du lära dig hur du ändrar appens registrering i Azure Portal för att ändra vem eller vilka typer av konton som har åtkomst till programmet.

## <a name="prerequisites"></a>Förutsättningar

* Ett [program som är registrerat i din Azure AD-klient](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Ändra appregistreringen för att stödja olika konton

Ange en annan inställning för de konto typer som stöds av en befintlig app-registrering:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar** och väljer sedan ditt program.
1. Ange nu vem som kan använda programmet, ibland kallat *inloggnings mål gruppen*.

    | Kontotyper som stöds | Beskrivning |
    |-------------------------|-------------|
    | **Endast konton i den här organisationskatalogen** | Välj det här alternativet om du vill skapa ett program för användning endast av användare (eller gäster) i *din* klient organisation.<br><br>Detta är ett program med en **enda klient organisation** i Microsoft Identity Platform, som ofta kallas LOB-program ( *line-of-Business* ). |
    | **Konton i valfri organisationskatalog** | Välj det här alternativet om du vill att användare i *en* Azure AD-klient ska kunna använda ditt program. Det här alternativet är lämpligt om du till exempel skapar ett SaaS-program (program vara som en tjänst) som du vill ge till flera organisationer.<br><br>Detta kallas för ett program **med flera klienter** i Microsoft Identity Platform. |
1. Välj **Spara**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Varför ändringar av flera klienter kan gå sönder

Det går ibland inte att växla en app-registrering från en Single-to-klient till flera klienter på grund av namn konflikter i program-ID URI (app-ID). Ett exempel på en app-ID-URI är `https://contoso.onmicrosoft.com/myapp` .

App-ID-URI är en av de sätt som ett program identifieras i protokollmeddelanden. App-ID-URI behöver bara vara unikt inom den klienten för ett program med en enda klient. För ett program med flera innehavare måste det vara globalt unikt så att Azure AD kan hitta appen över alla klienter. Global unikhet upprätthålls genom att kräva att app-ID-URI: s värdnamn matchar en av Azure AD-klientens [verifierade publicerings domäner](howto-configure-publisher-domain.md).

Om namnet på din klient till exempel är *contoso.onmicrosoft.com*, `https://contoso.onmicrosoft.com/myapp` är en giltig app-ID-URI. Om din klient organisation har en verifierad domän *contoso.com*, är en giltig app-ID-URI också `https://contoso.com/myapp` . Om app-ID-URI inte följer det andra mönstret, så `https://contoso.com/myapp` Miss lyckas konverteringen av registrerings programmet till flera klienter.

Mer information om hur du konfigurerar en verifierad Publisher-domän finns i [Konfigurera en verifierad domän](howto-configure-publisher-domain.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om kraven för att [konvertera en app från en-till-flera-klient](howto-convert-app-to-be-multi-tenant.md).
