---
title: Självstudie för att konfigurera Saviynt med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Själv studie kurs som hjälper dig att konfigurera Azure Active Directory B2C med Saviynt för integrering av över program för att effektivisera IT-modernisering och främja bättre säkerhet, styrning och efterlevnad. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b1889a693c08ce78a8bd06b6fe348c0c67bf3812
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465682"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera Saviynt med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar Azure Active Directory (AD) B2C med [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/). Saviynt Security Manager Platform ger insyn, säkerhet och styrning som dagens företag behöver, i en enda enhetlig plattform. Saviynt införlivar program risk och styrning, infrastruktur hantering, privilegie rad konto hantering och kund risk analys.

I den här självstudien får du ställa in Saviynt för att tillhandahålla detaljerad åtkomst kontroll baserad på delegerad administration för Azure AD B2C användare. Saviynt utför följande kontroller för att avgöra om en användare har behörighet att hantera Azure AD B2C användare.

- Säkerhet på funktions nivå för att avgöra om en användare kan utföra en speciell åtgärd. Du kan till exempel skapa användare, uppdatera användare, återställa användar lösen ord och så vidare.

- Säkerhet på fält nivå för att avgöra om en användare kan läsa/skriva ett speciellt attribut för en annan användare under användar hanterings åtgärder. Till exempel kan support agenten bara uppdatera telefonnumret och alla andra attribut är skrivskyddade.

- Säkerhet på data nivå för att avgöra om en användare kan utföra en viss åtgärd på en viss användare. Till exempel kan support administratören för en Storbritannien-region endast hantera Storbritannien-användare.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](./tutorial-create-tenant.md). Klienten är länkad till din Azure-prenumeration.

- En Saviynt- [prenumeration](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>Scenariobeskrivning

Saviynt-integreringen innehåller följande komponenter:

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) – affärs-till-kund-identiteten som en tjänst som möjliggör anpassad kontroll av hur dina kunder registrerar sig, loggar in och hanterar sina profiler.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) – identitets styrnings plattformen som ger en detaljerad delegerad administration för användar livs cykel hantering och åtkomst styrning av Azure AD B2C användare.  

- [Microsoft Graph API](/graph/use-the-api) – det här API: et tillhandahåller gränssnitten för Saviynt för att hantera Azure AD B2C-användare och deras åtkomst i Azure AD B2C.

I följande arkitektur diagram visas implementeringen.

![Bild som visar saviynt-arkitektur diagram](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | En delegerad administratör startar en Hantera Azure AD B2C användar åtgärd via Saviynt.
| 2. | Saviynt verifieras med dess Authorization-motor om den delegerade administratören kan utföra den aktuella åtgärden.
| 3. | Saviynt ' s Authorization Engine skickar ett svar om lyckade/misslyckade autentiseringar.
| 4. | Saviynt gör det möjligt för den delegerade administratören att utföra den begärda åtgärden.
| 5. | Saviynt anropar Microsoft Graph API tillsammans med användarattribut för att hantera användaren i Azure AD B2C
| 6. | Microsoft Graph-API kommer att aktivera skapa/uppdatera/ta bort användaren i Azure AD B2C.
| 7. | Azure AD B2C skickar ett lyckat/misslyckat svar.
| 8. | Microsoft Graph API returnerar sedan svaret till Saviynt.

## <a name="onboard-with-saviynt"></a>Publicera med Saviynt

1. Kontakta [Saviynt](https://saviynt.com/contact-us/) om du vill skapa ett Saviynt-konto

2. Skapa delegerade administrations principer och tilldela användare som delegerade administratörer med olika roller.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Konfigurera Azure AD B2C med Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Skapa ett Azure AD-program för Saviynt

1. Logga in på [Azure-portalen](https://portal.azure.com/#home).

2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

3. I Azure Portal söker du och väljer **Azure AD B2C**.

4. Välj **Appregistreringar**  >  **ny registrering**.

5. Ange ett namn för programmet. Till exempel Saviynt och välj **skapa**.

6. Gå till **API-behörigheter** och välj **+ Lägg till en behörighet.**

7. Sidan begär API-behörigheter visas. Välj fliken **Microsoft API: er** och välj **Microsoft Graph** som vanliga API: er för Microsoft.

8. Gå till nästa sida och välj **program behörigheter**.

9. Välj **katalog** och välj **katalog. Read. all** och **Directory. readwrite. all** kryss rutor.

10. Välj **Lägg till behörigheter**. Granska de behörigheter som lagts till.

11. Välj **bevilja administrativt medgivande för standard katalogen**  >  **Spara**.

12. Gå till **certifikat och hemligheter** och välj **+ Lägg till klient hemlighet**. Ange en beskrivning av klient hemligheten, Välj alternativet förfallo datum och välj **Lägg till**.

13. Den hemliga nyckeln genereras och visas i avsnittet klient hemlighet.

    >[!NOTE]
    > Du behöver klient hemligheten senare.

14. Gå till **Översikt** och hämta **klient-ID och klient** **-ID**.

15. Klient organisations-ID, klient-ID och klient hemlighet krävs för att slutföra installationen i Saviynt.

### <a name="enable-saviynt-to-delete-users"></a>Aktivera Saviynt för att ta bort användare

I stegen nedan förklaras hur du aktiverar Saviynt för att utföra användar borttagnings åtgärder i Azure AD B2C.

>[!NOTE]
>[Utvärdera risken innan du beviljar administratörs roller åtkomst till ett huvud namn för tjänsten.](../active-directory/develop/app-objects-and-service-principals.md)

1. Installera den senaste versionen av MSOnline PowerShell-modulen på en Windows-arbetsstation/-Server.

2. Anslut till AzureAD PowerShell-modulen och kör följande kommandon:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>Testa lösningen

Bläddra till ditt Saviynt-program och testa hantering av användar livs cykel och åtkomst styrnings användnings fall.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

- [Skapa ett webb-API-program](./add-web-api-application.md)