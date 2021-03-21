---
title: Dela konton och autentiseringsuppgifter – Azure Active Directory | Microsoft Docs
description: Beskriver hur Azure Active Directory gör det möjligt för organisationer att på ett säkert sätt dela konton för lokala appar och konsument moln tjänster.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb088d56879ebdf5d439c913ac47a701db5c4a60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576255"
---
# <a name="sharing-accounts-with-azure-ad"></a>Dela konton med Azure AD

## <a name="overview"></a>Översikt

Ibland behöver organisationer använda ett enda användar namn och lösen ord för flera personer, vilket vanligt vis sker i två fall:

* Vid åtkomst till program som kräver ett unikt inloggnings-och lösen ord för varje användare, oavsett om det gäller lokala appar eller konsument moln tjänster (t. ex. företags konton för sociala medier).
* När du skapar miljöer med flera användare. Du kan ha ett enda, lokalt konto med utökade privilegier och används för att utföra grundläggande konfigurations-, administrations-och återställnings aktiviteter. Till exempel det lokala kontot "global administratör" för Microsoft 365 eller rot kontot i Salesforce.

Traditionellt är dessa konton delade genom att distribuera autentiseringsuppgifterna (användar namn och lösen ord) till rätt personer eller lagra dem på en delad plats där flera betrodda agenter kan komma åt dem.

Den traditionella delnings modellen har flera nack delar:

* Om du aktiverar åtkomst till nya program måste du distribuera autentiseringsuppgifter till alla som behöver åtkomst.
* Varje delat program kan kräva sin egen unika uppsättning delade autentiseringsuppgifter, vilket kräver att användarna kommer ihåg flera uppsättningar autentiseringsuppgifter. När användarna måste komma ihåg flera autentiseringsuppgifter, ökar risken att de använder riskfyllda metoder. (Skriv till exempel lösen ord).
* Du kan inte se vem som har åtkomst till ett program.
* Du kan inte se vem som har *åtkomst till* ett program.
* När du vill ta bort åtkomsten till ett program måste du uppdatera autentiseringsuppgifterna och distribuera dem till alla som behöver åtkomst till programmet.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory konto delning

Azure AD ger en ny metod för att använda delade konton som eliminerar dessa nack delar.

Azure AD-administratören konfigurerar vilka program som en användare har åtkomst till via åtkomst panelen och väljer den typ av enkel inloggning som passar bäst för det programmet. En av dessa typer, *Password-baserad enkel inloggning*, låter Azure AD agera som en typ av "Broker" under inloggnings processen för den appen.

Användare loggar in en gång med sitt organisations konto. Det här kontot är detsamma som de använder ofta för att få åtkomst till Skriv bordet eller e-postmeddelandet. De kan bara identifiera och komma åt de program som de är tilldelade till. Med delade konton kan den här listan över program innehålla valfritt antal delade autentiseringsuppgifter. Slutanvändaren behöver inte komma ihåg eller skriva ned de olika konton som de kan använda.

Delade konton ökar inte bara överblicken och förbättrar användbarheten, men de förbättrar också säkerheten. Användare med behörighet att använda autentiseringsuppgifterna ser inte det delade lösen ordet, utan du får hellre behörighet att använda lösen ordet som en del av ett dirigerat autentiseringsschema. Dessutom ger vissa Password SSO-program möjlighet att använda Azure AD för att regelbundet förnya lösen ord (uppdatera). Systemet använder stora, komplexa lösen ord, vilket ökar konto säkerheten. Administratören kan enkelt bevilja eller återkalla åtkomst till ett program, vet vem som har åtkomst till kontot och vem som har åtkomst till det tidigare.

Azure AD stöder delade konton för alla tjänster för Enterprise Mobility Suite (EMS) eller Azure AD Premium licens plan i alla typer av lösen ord för enkel inloggning. Du kan dela konton för tusentals förintegrerade program i program galleriet och kan lägga till ditt eget program för autentisering av lösen ord med [anpassade SSO-appar](../manage-apps/what-is-single-sign-on.md).

Azure AD-funktioner som möjliggör konto delning är:

* [Enkel inloggning för lösen ord](../manage-apps/sso-options.md#password-based-sso)
* Agent för enkel inloggning med lösen ord
* [Grupp tilldelning](groups-self-service-management.md)
* Anpassade Password-appar
* [Instrument panelen för användning av appar/rapporter](../authentication/howto-sspr-reporting.md)
* Åtkomst portaler för slutanvändare
* [App-proxy](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)

## <a name="sharing-an-account"></a>Dela ett konto

Om du vill använda Azure AD för att dela ett konto måste du:

* Lägg till ett program [Galleri](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) eller [anpassat program](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurera programmet för lösen ord enkel Sign-On (SSO)
* Använd [gruppbaserad tilldelning](groups-saasapps.md) och välj alternativet för att ange en delad autentiseringsuppgift

Du kan också göra ditt delade konto säkrare med Multi-Factor Authentication (MFA) (Lär dig mer om att [skydda program med Azure AD](../authentication/concept-mfa-howitworks.md)) och du kan delegera möjligheten att hantera vem som har åtkomst till programmet med hjälp av [Azure AD](groups-self-service-management.md) självbetjänings grupp hantering.

## <a name="next-steps"></a>Nästa steg

* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Skydda appar med villkorlig åtkomst](../../active-directory-b2c/overview.md)
* [Grupp hantering för självbetjäning/SSAA](groups-self-service-management.md)