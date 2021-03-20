---
title: Azure AD Connect-direktautentisering – uppgradera auth agents | Microsoft Docs
description: I den här artikeln beskrivs hur du uppgraderar din Azure Active Directory (Azure AD) genom strömnings konfiguration.
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d0507f7751305af5e626cbd7dd6e0dfd1a63a74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95973056"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory direktautentisering: uppgradera förhands gransknings agenter

## <a name="overview"></a>Översikt

Den här artikeln är för kunder som använder Azure AD-direktautentisering genom för hands version. Vi har nyligen uppgraderat (och ommärkest) Authentication Agent-programvaran. Du måste uppgradera förhands gransknings agenter som är installerade på dina lokala servrar _manuellt_ . Den här manuella uppgraderingen är endast en engångs åtgärd. Alla framtida uppdateringar av autentiseringskrav är automatiska. Anledningen till uppgraderingen är följande:

- För hands versioner av autentiseringsprinciper får inga ytterligare säkerhets-eller fel korrigeringar.
-   För hands versioner av autentiseringsprinciper kan inte installeras på ytterligare servrar för hög tillgänglighet.

## <a name="check-versions-of-your-authentication-agents"></a>Kontrol lera versioner av dina autentiseringspaket

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Steg 1: kontrol lera var dina autentiseringspaket är installerade

Följ de här stegen för att kontrol lera var dina autentiseringsprinciper är installerade:

1. Logga in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) med autentiseringsuppgifterna för global administratör för din klient organisation.
2. Välj **Azure Active Directory** i det vänstra navigerings fältet.
3. Välj **Azure AD Connect**. 
4. Välj **direktautentisering**. Det här bladet visar de servrar där dina autentiseringsprinciper är installerade.

![Azure Active Directory administrations Center – pass-through Authentication-bladet](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Steg 2: kontrol lera versionerna av dina autentiseringspaket

Följ dessa anvisningar för att kontrol lera vilka versioner av dina autentiserings agenter som identifieras i föregående steg:

1. Gå till **kontroll panelen – > program – > program och funktioner** på den lokala servern.
2. Om det finns en post för "**Microsoft Azure AD Connect Authentication agent**", behöver du inte vidta några åtgärder på den här servern.
3. Om det finns en post för "**Microsoft Azure AD Application Proxy Connector**" måste du uppgradera manuellt på den här servern.

![Förhands gransknings version av Authentication agent](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Bästa praxis att följa innan du påbörjar uppgraderingen

Kontrol lera att du har följande objekt på plats innan du uppgraderar:

1. **Skapa endast ett globalt administratörs konto för moln**: uppgradera inte utan att ett globalt administratörs konto för molnet används i nöd situationer där dina direktautentisering inte fungerar som de ska. Lär dig mer om [att lägga till ett globalt administratörs konto för molnet](../fundamentals/add-users-azure-active-directory.md). Det här steget är kritiskt och säkerställer att du inte blir utelåst från din klient.
2.  **Säkerställ hög tillgänglighet**: om du inte har slutfört tidigare installerar du en andra fristående autentiseringstjänst för att tillhandahålla hög tillgänglighet för inloggnings förfrågningar med hjälp av dessa [instruktioner](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Uppgradera Autentiseringstjänsten på din Azure AD Connect-Server

Du måste uppgradera Azure AD Connect innan du uppgraderar Authentication agent på samma server. Följ de här stegen på både din primära och mellanlagrings Azure AD Connect servrar:

1. **Uppgradera Azure AD Connect**: Följ den här [artikeln](how-to-upgrade-previous-version.md) och uppgradera till den senaste Azure AD Connect versionen.
2. **Avinstallera för hands versionen av Autentiseringstjänsten**: Ladda ned [det här PowerShell-skriptet](https://aka.ms/rmpreviewagent) och kör det som administratör på servern.
3. **Hämta den senaste versionen av Autentiseringstjänsten (versioner 1.5.389.0 eller senare)**: Logga in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) med klientens globala administratörs behörighet. Välj **Azure Active Directory-> Azure AD Connect-> direktautentisering – > hämtnings agenten**. Godkänn [villkoren för tjänsten](https://aka.ms/authagenteula) och ladda ned den senaste versionen av Autentiseringstjänsten. Du kan också hämta Authentication agenten [härifrån.](https://aka.ms/getauthagent)
4. **Installera den senaste versionen av Autentiseringstjänsten**: kör den körbara filen som hämtades i steg 3. Ange klient organisationens autentiseringsuppgifter för global administratör när du uppmanas till det.
5. **Kontrol lera att den senaste versionen har installerats**: som visas tidigare går du till **kontroll panelen-> program-> program och funktioner** och kontrollerar att det finns en post för "**Microsoft Azure AD Connect Authentication agent**".

>[!NOTE]
>Om du markerar bladet för direkt autentisering i [Azure Active Directory administrations Center](https://aad.portal.azure.com) när du har slutfört föregående steg, ser du två poster för Autentiseringstjänsten per server – en post som visar Autentiseringstjänsten som **aktiv** och den andra som **inaktiv**. Detta är _förväntat_. Den **inaktiva** posten tas bort automatiskt efter några dagar.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Uppgradera Autentiseringstjänsten på andra servrar

Följ de här stegen för att uppgradera autentiseringsprinciper på andra servrar (där Azure AD Connect inte är installerat):

1. **Avinstallera för hands versionen av Autentiseringstjänsten**: Ladda ned [det här PowerShell-skriptet](https://aka.ms/rmpreviewagent) och kör det som administratör på servern.
2. **Hämta den senaste versionen av Autentiseringstjänsten (versioner 1.5.389.0 eller senare)**: Logga in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) med klientens globala administratörs behörighet. Välj **Azure Active Directory-> Azure AD Connect-> direktautentisering – > hämtnings agenten**. Godkänn villkoren för tjänsten och ladda ned den senaste versionen.
3. **Installera den senaste versionen av Autentiseringstjänsten**: kör den körbara filen som hämtades i steg 2. Ange klient organisationens autentiseringsuppgifter för global administratör när du uppmanas till det.
4. **Kontrol lera att den senaste versionen har installerats**: som visas tidigare går du till **kontroll panelen-> program-> program och funktioner** och kontrollerar att det finns en post med namnet **Microsoft Azure AD Connect Authentication agent**.

>[!NOTE]
>Om du markerar bladet för direkt autentisering i [Azure Active Directory administrations Center](https://aad.portal.azure.com) när du har slutfört föregående steg, ser du två poster för Autentiseringstjänsten per server – en post som visar Autentiseringstjänsten som **aktiv** och den andra som **inaktiv**. Detta är _förväntat_. Den **inaktiva** posten tas bort automatiskt efter några dagar.

## <a name="next-steps"></a>Nästa steg
- [**Felsök**](tshoot-connect-pass-through-authentication.md) – lär dig hur du löser vanliga problem med funktionen.