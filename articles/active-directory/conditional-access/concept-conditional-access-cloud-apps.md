---
title: Molnappar eller åtgärder i princip för villkorlig åtkomst – Azure Active Directory
description: Vad är molnappar eller åtgärder i en princip för villkorlig åtkomst för Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff9fe969c90a2bb4aa9f954b984f511fb490ba3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579152"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Villkorlig åtkomst: molnappar eller åtgärder

Molnappar eller åtgärder är en nyckel signal i en princip för villkorlig åtkomst. Med principer för villkorlig åtkomst kan administratörer tilldela kontroller till vissa program eller åtgärder.

- Administratörer kan välja i listan över program som innehåller inbyggda Microsoft-program och alla [Azure AD-integrerade program](../manage-apps/what-is-application-management.md) , inklusive Galleri, icke-galleri och program som publicerats via [Application Proxy](../manage-apps/what-is-application-proxy.md).
- Administratörer kan välja att definiera principer som inte baseras på ett moln program, men på en användar åtgärd. Den enda åtgärd som stöds är att registrera säkerhets information (förhands granskning), så att villkorlig åtkomst kan användas för att genomdriva kontroller kring den [kombinerade säkerhets informations registreringen](../authentication/howto-registration-mfa-sspr-combined.md).

![Definiera en princip för villkorlig åtkomst och ange molnappar](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft Cloud-program

Många av de befintliga Microsoft Cloud-programmen ingår i listan över program som du kan välja bland. 

Administratörer kan tilldela en princip för villkorlig åtkomst till följande molnappar från Microsoft. Vissa appar som Office 365 och Microsoft Azure hantering inkluderar flera relaterade underordnade appar eller tjänster. Följande lista är inte fullständig och kan komma att ändras.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database-och Azure Synapse-analys](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure hantering](#microsoft-azure-management)
- Microsoft Azure prenumerations hantering
- Microsoft Cloud App Security
- Microsoft Commerce tools Access Control Portal
- Microsoft Commerce tools-autentiseringstjänst
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune registrering](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Search i Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office-Sway
- Outlook Groups
- Power BI-tjänsten
- Project Online
- Skype för företag – Online
- Virtuellt privat nätverk (VPN)
- Windows Defender ATP

### <a name="office-365"></a>Office 365

Microsoft 365 tillhandahåller molnbaserade produktivitets-och samarbets tjänster som Exchange, SharePoint och Microsoft Teams. Microsoft 365 Cloud Services är djupt integrerade för att säkerställa smidiga och samarbets upplevelser. Den här integrationen kan orsaka förvirring när du skapar principer eftersom vissa appar, till exempel Microsoft Teams, har beroenden för andra som SharePoint eller Exchange.

Office 365-appen gör det möjligt att rikta dessa tjänster på samma gång. Vi rekommenderar att du använder den nya Office 365-appen, i stället för att rikta in enskilda molnappar för att undvika problem med [tjänst beroenden](service-dependencies.md). Att rikta in sig på den här gruppen med program bidrar till att undvika problem som kan uppstå på grund av inkonsekventa principer och beroenden.

Administratörer kan välja att undanta vissa appar från en princip om de vill genom att inkludera Office 365-appen och utesluta de enskilda appar som de väljer i principen.

Viktiga program som ingår i Office 365-klient programmet:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - sharepoint online
   - Microsoft 365 Search Service
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype för företag – Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure hantering

Microsoft Azure hanterings programmet innehåller flera underliggande tjänster. 

   - Azure Portal
   - Azure Resource Manager Provider
   - API: er för klassisk distributions modell
   - Azure PowerShell
   - Azure CLI
   - Administratörs Portal för Visual Studio-prenumerationer
   - Azure DevOps
   - Azure Data Factory Portal

> [!NOTE]
> Microsoft Azure hanterings programmet gäller Azure PowerShell, som anropar Azure Resource Manager-API: et. Den gäller inte för Azure AD PowerShell, som anropar Microsoft Graph.

## <a name="other-applications"></a>Andra program

Förutom Microsoft-appar kan administratörer lägga till alla registrerade Azure AD-program i principer för villkorlig åtkomst. Programmen kan innehålla: 

- Program som publicerats via [Azure AD-programproxy](../manage-apps/what-is-application-proxy.md)
- [Program som har lagts till från galleriet](../manage-apps/add-application-portal.md)
- [Anpassade program som inte är i galleriet](../manage-apps/view-applications-portal.md)
- [Äldre program som publicerats via app Delivery controllers och Networks](../manage-apps/secure-hybrid-access.md)
- Program som använder lösenordsbaserad [enkel inloggning](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

> [!NOTE]
> Eftersom principen för villkorlig åtkomst anger kraven för åtkomst till en tjänst kan du inte använda den för ett klient program (offentligt/inbyggt). Andra ord som principen inte ställs in direkt på ett klient program (offentligt/inbyggt), men tillämpas när en klient anropar en tjänst. Till exempel gäller en princip uppsättning i SharePoint-tjänsten för klienter som anropar SharePoint. En princip som angetts för Exchange gäller försöket att komma åt e-postmeddelandet med Outlook-klienten. Det är anledningen till varför klient (offentliga/egna) program inte är tillgängliga för val i apparna för molnappar och alternativet för villkorlig åtkomst inte är tillgänglig i program inställningarna för klientens (offentliga/egna) program som är registrerade i din klient organisation. 

## <a name="user-actions"></a>Användaråtgärder

Användar åtgärder är uppgifter som kan utföras av en användare. Villkorlig åtkomst har för närvarande stöd för två användar åtgärder: 

- **Registrera säkerhets information**: den här användar åtgärden gör att principen för villkorlig åtkomst kan tillämpas när användare som är aktiverade för sammanställd registrering försöker registrera sin säkerhets information. Mer information finns i artikeln [kombinerad säkerhets informations registrering](../authentication/concept-registration-mfa-sspr-combined.md).

- **Registrera eller ansluta enheter (förhands granskning)**: med den här användar åtgärden kan administratörer tillämpa principer för villkorlig åtkomst när användare [registrerar](../devices/concept-azure-ad-register.md) eller [ansluter](../devices/concept-azure-ad-join.md) enheter till Azure AD. Det finns två viktiga överväganden med den här användar åtgärden: 
   - `Require multi-factor authentication` är den enda åtkomst kontrollen som är tillgänglig för den här användar åtgärden och alla andra har inaktiverats. Den här begränsningen förhindrar konflikter med åtkomst kontroller som antingen är beroende av Azure AD-enhets registrering eller inte tillämplig för registrering av Azure AD-enheter. 
   - När en princip för villkorlig åtkomst har Aktiver ATS med den här användar åtgärden måste du ange  >    >  **enhets inställningarna** för Azure Active Directory enheter  -  `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` till **Nej**. Annars tillämpas inte principen för villkorlig åtkomst med den här användar åtgärden korrekt. Mer information om den här enhets inställningen finns i [Konfigurera enhets inställningar](../devices/device-management-azure-portal.md#configure-device-settings). Den här användar åtgärden ger flexibilitet att kräva Multi-Factor Authentication för att registrera eller ansluta enheter för vissa användare och grupper eller villkor i stället för att ha en princip för hela klienten i enhets inställningarna. 
   
## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: villkor](concept-conditional-access-conditions.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
- [Klient program beroenden](service-dependencies.md)
