---
title: Bevilja B2B-användare åtkomst till dina lokala appar – Azure AD
description: Visar hur du ger Cloud B2B-användare åtkomst till lokala appar med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd91d1d2c9f5a4a413f9ea64cfdef649823d0f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131028"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Bevilja B2B-användare i Azure AD åtkomst till dina lokala program

Som organisation som använder Azure Active Directory (Azure AD) B2B-samarbets funktioner för att bjuda in gäst användare från partner organisationer till din Azure AD, kan du nu ge dessa B2B-användare åtkomst till lokala appar. Dessa lokala appar kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

## <a name="access-to-saml-apps"></a>Åtkomst till SAML-appar

Om den lokala appen använder SAML-baserad autentisering kan du enkelt göra dessa appar tillgängliga för dina Azure AD B2B Collaboration-användare via Azure Portal.

Du måste göra något av följande:

- Integrera appen med SAML enligt beskrivningen i [Konfigurera SAML-baserad enkel inloggning](../manage-apps/configure-saml-single-sign-on.md). Kom ihåg vad du använder för **inloggnings-URL** -värdet.
-  Använd Azure AD-programproxy för att publicera den lokala appen med **Azure Active Directory** konfigurerad som källa för autentisering. Instruktioner finns i [Publicera program med hjälp av Azure AD-programproxy](../manage-apps/application-proxy-add-on-premises-application.md). 

   När du konfigurerar den **interna URL** -inställningen använder du den inloggnings-URL som du angav i program mal len ej Galleri. På så sätt kan användare komma åt appen utanför organisationens gränser. Programproxyn utför SAML enkel inloggning för den lokala appen.
 
   ![Visar en intern URL och autentisering för lokala program inställningar](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Åtkomst till IWA-och KCD-appar

För att ge B2B-användare åtkomst till lokala program som skyddas med integrerad Windows-autentisering och Kerberos-begränsad delegering, behöver du följande komponenter:

- **Autentisering via Azure-AD-programproxy**. B2B-användare måste kunna autentisera till det lokala programmet. Om du vill göra det måste du publicera den lokala appen via Azure-AD-programproxy. Mer information finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via Application Proxy](../manage-apps/application-proxy-add-on-premises-application.md).
- **Auktorisering via ett B2B-användar objekt i den lokala katalogen**. Programmet måste kunna utföra kontroller för användar åtkomst och ge åtkomst till rätt resurser. IWA och KCD kräver ett användar objekt i den lokala Windows Server-Active Directory för att slutföra den här auktoriseringen. Som beskrivs i [hur enkel inloggning med KCD fungerar](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)måste programproxyn ha det här användarobjektet för att personifiera användaren och hämta en Kerberos-token till appen. 

   > [!NOTE]
   > När du konfigurerar Azure-AD-programproxy bör du se till att den **delegerade inloggnings identiteten** är inställd på **användarens huvud namn** (standard) i konfigurationen för enkel inloggning för integrerad Windows-autentisering (IWA).

   För B2B-användar scenariot finns det två metoder som du kan använda för att skapa gäst användar objekt som krävs för auktorisering i den lokala katalogen:

   - Microsoft Identity Manager (MIM) och MIM Management Agent för Microsoft Graph. 
   - [Ett PowerShell-skript](#create-b2b-guest-user-objects-through-a-script-preview). Att använda skriptet är en mer lätt lösning som inte kräver MIM. 

Följande diagram innehåller en översikt över hur Azure AD-programproxy och generationen B2B-användarobjektet i den lokala katalogen fungerar tillsammans för att ge B2B-användare åtkomst till dina lokala IWA-och KCD-appar. De numrerade stegen beskrivs i detalj under diagrammet.

![Diagram över MIM-och B2B-skript lösningar](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  En användare från en partner organisation (Fabrikam-klienten) bjuds in till contoso-klienten.
2.  Ett gäst användar objekt skapas i Contoso-klienten (till exempel ett användar objekt med ett UPN för guest_fabrikam. com # EXT # @contoso.onmicrosoft.com ).
3.  Fabrikam-gästen importeras från Contoso till MIM eller via B2B-PowerShell-skriptet.
4.  En representation eller "plats" för gäst användar objekt (Guest # EXT #) skapas i den lokala katalogen, Contoso.com, via MIM eller via B2B-PowerShell-skriptet.
5.  Gäst användaren får åtkomst till det lokala programmet, app.contoso.com.
6.  Autentiseringsbegäran är auktoriserad via programproxyn med Kerberos-begränsad delegering. 
7.  Eftersom gäst användar objektet finns lokalt, lyckas autentiseringen.

### <a name="lifecycle-management-policies"></a>Principer för livs cykel hantering

Du kan hantera lokala B2B-användar objekt via principer för livs cykel hantering. Exempel:

- Du kan ställa in Multi-Factor Authentication-principer (MFA) för gäst användaren så att MFA används vid autentisering i Application Proxy. Mer information finns i [villkorlig åtkomst för B2B-samarbets användare](conditional-access.md).
- Alla sponsring, åtkomst granskningar, konto verifieringar osv. som utförs i Cloud B2B-användaren gäller för lokala användare. Om till exempel moln användaren tas bort via livs cykel hanterings principerna, tas den lokala användaren också bort av MIM Sync eller genom Azure AD Connect Sync. Mer information finns i [Hantera gäst åtkomst med åtkomst granskningar för Azure AD](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Skapa B2B-gäst användar objekt via MIM

Information om hur du använder MIM 2016 Service Pack 1 och MIM Management Agent för Microsoft Graph att skapa gäst användar objekt i den lokala katalogen finns i [Azure AD Business-to-Business (B2B)-samarbete med Microsoft Identity Manager (MIM) 2016 SP1 med Azure Application Proxy](/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Skapa B2B-gäst användar objekt via ett skript (förhands granskning)

Det finns ett PowerShell-exempel skript som du kan använda som utgångs punkt för att skapa gäst användar objekt i din lokala Active Directory.

Du kan ladda ned skriptet och Readme-filen från [kopplingar för Microsoft Identity Manager 2016 och Forefront Identity Manager 2010 R2](https://www.microsoft.com/download/details.aspx?id=51495). I hämtnings paketet väljer du **skriptet och viktigt för att hämta Azure AD B2B-användare on-prem.zips** fil.

Innan du använder skriptet måste du kontrol lera att du har granskat nödvändiga komponenter och viktiga överväganden i den tillhör ande Readme-filen. Förstå också att skriptet endast är tillgängligt som ett exempel. Utvecklings teamet eller en partner måste anpassa och granska skriptet innan du kör det.

## <a name="license-considerations"></a>Licens överväganden

Kontrol lera att du har rätt klient åtkomst licenser (CAL) för externa gäst användare som har åtkomst till lokala appar. Mer information finns i avsnittet "externa anslutningar" i [klient åtkomst licenser och hanterings licenser](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Kontakta din Microsoft-representant eller lokala åter försäljare angående dina speciella licensierings behov.

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för Hybrid organisationer](hybrid-organizations.md)

- En översikt över Azure AD Connect finns i [integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).