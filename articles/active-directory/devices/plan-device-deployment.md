---
title: Planera distribution av Azure Active Directory enhet
description: Välj de integrerings strategier för Azure AD-enheter som uppfyller organisationens behov.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc631e4329c1df2cdcbfe57c2b43ccccf14afa85
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936455"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Planera distribution av Azure Active Directory enhet

Den här artikeln hjälper dig att utvärdera metoder för att integrera din enhet med Azure AD, välja implementerings plan och innehåller viktiga länkar till enhets hanterings verktyg som stöds.

Landskapet för de enheter som användarna loggar in på expanderar. Organisationer kan tillhandahålla Station ära datorer, bärbara datorer, telefoner, surfplattor och andra enheter. Användarna kan använda sin egen matris med enheter och komma åt information från olika platser. I den här miljön är ditt jobb som administratör att skydda dina organisations resurser på alla enheter.

Azure Active Directory (Azure AD) gör det möjligt för din organisation att uppfylla dessa mål med hantering av enhetens identitet. Nu kan du hämta dina enheter i Azure AD och kontrol lera dem från en central plats i [Azure Portal](https://portal.azure.com/). Detta ger dig en enhetlig upplevelse, förbättrad säkerhet och minskar tiden som krävs för att konfigurera en ny enhet.

Det finns flera metoder för att integrera dina enheter i Azure AD:

* Du kan [registrera enheter](concept-azure-ad-register.md) med Azure AD

* [Anslut enheter](concept-azure-ad-join.md) till Azure AD (endast moln) eller

* [Skapa en hybrid Azure AD-anslutning](concept-azure-ad-join-hybrid.md) mellan enheter i din lokala Active Directory och Azure AD. 

## <a name="learn"></a>Learn

Innan du börjar ska du kontrol lera att du är bekant med [Översikt över enhets identitets hantering](overview.md).

### <a name="benefits"></a>Fördelar

Viktiga fördelar med att ge dina enheter en Azure AD-identitet:

* Öka produktiviteten – med Azure AD kan användarna göra [sömlös inloggning (SSO)](./azuread-join-sso.md) till dina lokala och molnbaserade resurser, vilket gör att de kan vara produktiva var de än är.

* Öka säkerheten – med Azure AD-enheter kan du tillämpa [principer för villkorlig åtkomst](../conditional-access/require-managed-devices.md) till resurser baserat på enhetens eller användarens identitet. Principer för villkorlig åtkomst kan ge extra skydd med hjälp av [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md). Att ansluta en enhet till Azure AD är ett krav för att öka säkerheten med en strategi för [lösen ords lös autentisering](../authentication/concept-authentication-passwordless.md) .

* Förbättra användar upplevelsen – med enhets identiteter i Azure AD kan du ge dina användare enkel åtkomst till din organisations molnbaserade resurser från både personliga och företagets enheter. Administratörer kan aktivera [Enterprise State roaming](enterprise-state-roaming-overview.md) för en enhetlig upplevelse på alla Windows-enheter.

* Förenkla distribution och hantering – enhets identitets hantering fören klar processen att sätta enheter till Azure AD med [Windows autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot), [Mass etablering](/mem/intune/enrollment/windows-bulk-enroll)och [självbetjäning: OOBE (out of Box Experience)](../user-help/user-help-join-device-on-network.md). Du kan hantera dessa enheter med verktyg för hantering av mobila enheter (MDM) som [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)och deras identiteter i [Azure Portal](https://portal.azure.com/).

### <a name="training-resources"></a>Utbildnings resurser

Video:  [villkorlig åtkomst med enhets kontroller](https://youtu.be/NcONUf-jeS4)

Vanliga frågor och svar om [Azure AD-enhets hantering, vanliga](faq.md)  frågor och [svar om inställningar och centrala data](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>Planera distributions projektet

Överväg organisationens behov medan du fastställer strategin för den här distributionen i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du engagerar rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att från intressenter-rollerna i projektet är väl förstå. 

I den här planen lägger du till följande intressenter i listan:

| Roll| Beskrivning |
| - | - |
| Enhets administratör| En representant från enhets teamet som kan verifiera att planen uppfyller enhets kraven för din organisation. |
| Nätverks administratör| En representant från nätverks teamet som kan se till att de uppfyller nätverks kraven. |
| Enhets hanterings grupp| Team som hanterar inventering av enheter. |
| OS-/regionsspecifika administratörs team| Team som stöder och hanterar vissa OS-versioner. Det kan till exempel finnas en Mac-eller iOS-inriktad grupp. |

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Kommunicera proaktivt med dina användare hur deras upplevelse kommer att ändras, när den kommer att ändras och hur du får support om de drabbas av problem.

### <a name="plan-a-pilot"></a>Planera en pilot

Vi rekommenderar att den inledande konfigurationen av integrerings metoden är i en test miljö eller med en liten grupp test enheter. Se [metod tips för en pilot](../fundamentals/active-directory-deployment-plans.md).

Hybrid Azure AD Join-distribution är enkelt och det är 100% en administratörs uppgift utan att slutanvändaren behöver utföra åtgärder. Du kanske vill göra en [kontrollerad verifiering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md) innan du aktiverar den i hela organisationen samtidigt.

## <a name="choose-your-integration-methods"></a>Välj integrerings metoder

Din organisation kan använda flera enhets integrerings metoder i en enda Azure AD-klient. Målet är att välja den eller de metoder som är lämpliga för att hantera dina enheter på ett säkert sätt i Azure AD. Det finns många parametrar som styr det här beslutet, inklusive ägarskap, enhets typer, primär publik och organisationens infrastruktur.

Följande information kan hjälpa dig att bestämma vilka integrerings metoder som ska användas.

### <a name="decision-tree-for-devices-integration"></a>Besluts träd för enhets integrering

Använd det här trädet för att bestämma alternativ för enheter som ägs av organisationen. 

> [!NOTE]
> Personliga eller BYOD-scenarier (med egen enhet) är inte avbildade i det här diagrammet. De leder alltid till registrering av Azure AD.

 ![Beslutsträd](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Jämförelse mat ris

iOS-och Android-enheter kanske bara är registrerade i Azure AD. I följande tabell visas övergripande överväganden för Windows-klient enheter. Använd den som en översikt och utforska sedan de olika integrations metoderna i detalj.

| Att tänka på | Azure AD-registrerad| Azure Active Directory-anslutning| Hybrid Azure Active Directory-anslutning |
| - | - | - | - |
| **Klient operativ system**| | |  |
| Windows 10-enheter| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
| Windows-enheter på äldre nivå (Windows 8,1 eller Windows 7)| | | ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
|**Inloggnings alternativ**| | |  |
| Lokala autentiseringsuppgifter för slutanvändare| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| |  |
| Lösenord| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
| PIN-kod för enhet| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello för företag| | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
| Säkerhets nycklar för FIDO 2,0| | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator app (lösen ords avinstallation)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
|**De viktigaste funktionerna**| | |  |
| SSO till moln resurser| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
| Enkel inloggning till lokala resurser| | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
| Villkorlig åtkomst <br> (Kräv att enheter markeras som kompatibla) <br> (Måste hanteras av MDM)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)|![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
Villkorlig åtkomst <br>(Kräv hybrid Azure AD-anslutna enheter)| | | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)
| Lösen ords återställning via självbetjäning från Windows inloggnings skärm| | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
| PIN-återställning för Windows Hello| | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |
| Företags tillstånd för nätverks växling mellan enheter| | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Registrering av Azure AD 

Registrerade enheter hanteras ofta med [Microsoft Intune](/mem/intune/enrollment/device-enrollment). Enheter registreras i Intune på flera olika sätt beroende på operativ systemet. 

Registrerade Azure AD-enheter har stöd för att ta dina egna enheter (BYOD) och företagsägda enheter till enkel inloggning till moln resurser. Åtkomst till resurser baseras på de [principer för villkorlig åtkomst](../conditional-access/require-managed-devices.md) för Azure AD som tillämpas på enheten och användaren.

### <a name="registering-devices"></a>Registrera enheter

Registrerade enheter hanteras ofta med [Microsoft Intune](/mem/intune/enrollment/device-enrollment). Enheter registreras i Intune på flera olika sätt beroende på operativ systemet. 

BYOD och företagsägda mobila enheter registreras av användare som installerar appen företags Portal.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Om du registrerar dina enheter är det bästa alternativet för din organisation, se följande resurser:

* Den här översikten över [registrerade Azure AD-enheter](concept-azure-ad-register.md).

* Den här dokumentationen för slutanvändaren om [att registrera din personliga enhet i din organisations nätverk](../user-help/user-help-register-device-on-network.md).

## <a name="azure-ad-join"></a>Azure Active Directory-anslutning

Med Azure AD Join kan du övergå till en moln-och första modell med Windows. Det ger en bra grund om du planerar att modernisera din enhets hantering och minska enhetens relaterade IT-kostnader. Azure AD Join fungerar endast med Windows 10-enheter. Betrakta det som det första valet för nya enheter.

[Azure AD-anslutna enheter kan dock använda SSO för lokala resurser](azuread-join-sso.md) när de är på organisationens nätverk, kan autentisera till lokala servrar som fil, utskrift och andra program.

Om det här är det bästa alternativet för din organisation, se följande resurser:

* Den här översikten över [Azure AD-anslutna enheter](concept-azure-ad-join.md).

* Bekanta dig med [implementerings planen för Azure AD Join](azureadjoin-plan.md).

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Tillhandahålla Azure AD-anslutning till dina enheter

För att etablera Azure AD Join har du följande metoder:

* Självbetjäning: [Windows 10 första körnings upplevelse](azuread-joined-devices-frx.md)

Om du har Windows 10 Professional eller Windows 10 Enterprise installerat på en enhet är standardinställningen konfigurationsprocessen för företagsägda enheter.

* [Windows out of Box Experience (OOBE) eller från Windows-inställningar](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Mass registrering](/mem/intune/enrollment/windows-bulk-enroll)

Välj distributions proceduren efter noggrann [jämförelse av dessa metoder](azureadjoin-plan.md).

Du kan se att Azure AD Join är den bästa lösningen för en enhet och att enheten kanske redan är i ett annat tillstånd. Här är några uppgraderings överväganden.

| Aktuellt enhets tillstånd| Önskat enhets tillstånd| Så här gör du |
| - | - | - |
| Lokal domänansluten| Azure AD-anslutning| Ta en anslutning till enheten från den lokala domänen innan du ansluter till Azure AD |
| Hybrid Azure AD-anslutning| Azure AD-anslutning| Ta en anslutning till enheten från den lokala domänen och från Azure AD innan du ansluter till Azure AD |
| Azure AD-registrerad| Azure AD-anslutning| Avregistrera enheten innan du ansluter till Azure AD |


## <a name="hybrid-azure-ad-join"></a>Hybrid Azure Active Directory-anslutning

Om du har en lokal Active Directory miljö och vill ansluta till dina Active Directory-domänanslutna datorer till Azure AD kan du göra detta med hybrid Azure AD-anslutning. Det har stöd [för ett brett utbud av Windows-enheter](hybrid-azuread-join-plan.md), inklusive både Windows-aktuella och Windows-enheter på andra nivån.

De flesta organisationer har redan domänanslutna enheter och hanterar dem via grupprincip eller System Center Configuration Manager (SCCM). I så fall rekommenderar vi att du konfigurerar hybrid Azure AD Join för att börja få förmåner samtidigt som du utnyttjar befintliga investeringar.

Om hybrid Azure AD Join är det bästa alternativet för din organisation, se följande resurser:

* Den här översikten över [anslutna Azure AD-enheter](concept-azure-ad-join-hybrid.md).

* Bekanta dig med [implementerings planen för Azure AD Join](hybrid-azuread-join-plan.md) .

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Etablering av hybrid Azure AD-anslutning till dina enheter

[Granska din identitets infrastruktur](hybrid-azuread-join-plan.md). Azure AD Connect ger dig en guide för att konfigurera hybrid Azure AD-anslutning för:

* [Federerade domäner](hybrid-azuread-join-federated-domains.md)

* [Hanterade domäner](hybrid-azuread-join-managed-domains.md)

Om du installerar den nödvändiga versionen av Azure AD Connect inte är ett alternativ kan du läsa mer i [Konfigurera hybrid Azure AD-anslutning manuellt](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> Den lokala domänanslutna Windows 10-enheten försöker ansluta automatiskt till Azure AD för att bli hybrid Azure AD-ansluten som standard. Detta kan bara utföras om du har konfigurerat rätt miljö. 

Du kan fastställa att hybrid Azure AD Join är den bästa lösningen för en enhet och att enheten redan har ett annat tillstånd. Här är några uppgraderings överväganden.

| Aktuellt enhets tillstånd| Önskat enhets tillstånd| Så här gör du |
| - | - | - |
| Lokal domän anslutning| Hybrid Azure AD-anslutning| Använd Azure AD Connect eller AD FS för att ansluta till Azure |
| Lokal arbets grupp som är ansluten eller ny| Hybrid Azure AD-anslutning| Stöds med [Windows autopilot](/windows/deployment/windows-autopilot/windows-autopilot). Enheten måste vara ansluten till en lokal domän före hybrid Azure AD-anslutning |
| Azure AD-ansluten| Hybrid Azure AD-anslutning| Ta en anslutning från Azure AD, som placerar den i den lokala arbets gruppen eller det nya läget. |
| Azure AD-registerad| Hybrid Azure AD-anslutning| Är beroende av Windows-version. [Se dessa överväganden](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>Hantera dina enheter

När du har registrerat eller anslutit dina enheter till Azure AD kan du använda [Azure Portal](https://portal.azure.com/) som en central plats för att hantera enhets identiteter. På sidan Azure Active Directory enheter kan du:

* [Konfigurera enhets inställningar](device-management-azure-portal.md#configure-device-settings)
* Du måste vara lokal administratör för att hantera Windows-enheter. [Azure AD uppdaterar detta medlemskap för Azure AD-anslutna enheter](assign-local-admin.md)och lägger automatiskt till dem med rollen enhets hanterare som administratörer till alla anslutna enheter.

Se till att hålla miljön ren genom att [Hantera inaktuella enheter](manage-stale-devices.md)och fokusera på resurserna på hantering av aktuella enheter.

* [Granska enhets relaterade gransknings loggar](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Enhets hanterings verktyg som stöds

Administratörer kan skydda och hantera dessa registrerade och anslutna enheter ytterligare genom att använda ytterligare enhets hanterings verktyg. Dessa verktyg ger ett sätt att tvinga fram organisations konfiguration, t. ex. att lagringen ska vara krypterad, lösen ords komplexitet, program varu installationer och program uppdateringar. 

Granska stödda plattformar och plattformar som inte stöds för integrerade enheter:

| Verktyg för enhetshantering| Azure AD-registrerad| Azure Active Directory-anslutning| Hybrid Azure Active Directory-anslutning|
| - | - | - | - |
| [Hantering av mobila enheter (MDM) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Exempel: Microsoft Intune| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)|  |
| [Samhantering med Microsoft Intune och Microsoft Endpoint Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 och senare)| | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)| ![Markering för dessa värden.](./media/plan-device-deployment/check.png)|  |
| [Grupprincip](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Endast Windows)| | | ![Markering för dessa värden.](./media/plan-device-deployment/check.png)|  |



 Vi rekommenderar att du funderar [Microsoft Intune hantering av mobil program (MAM)](/mem/intune/apps/app-management) med eller utan enhets hantering för registrerade iOS-eller Android-enheter.

 Administratörer kan även [distribuera VDI-plattformar (Virtual Desktop Infrastructure)](howto-device-identity-virtual-desktop-infrastructure.md) som är värd för Windows-operativsystem i sina organisationer för att effektivisera hanteringen och minska kostnaderna genom konsolidering och centralisering av resurser. 

### <a name="troubleshoot-device-identities"></a>Felsöka enhetsidentiteter

* [Felsöka enheter med kommandot dsregcmd](troubleshoot-device-dsregcmd.md)

* [Felsöka Enterprise State Roaming inställningar i Azure Active Directory](enterprise-state-roaming-troubleshooting.md)

Om du får problem med att slutföra hybrid Azure AD-anslutning för domänanslutna Windows-enheter, se:

* [Felsöka hybrid Azure AD-anslutning för aktuella Windows-enheter](troubleshoot-hybrid-join-windows-current.md)

* [Felsöka hybrid Azure AD-anslutning för Windows-enheter på nivån](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

* [Planera din Azure AD Join-implementering](azureadjoin-plan.md)
* [Planera din hybrid Azure AD Join-implementering](hybrid-azuread-join-plan.md)
* [Hantera enhetsidentiteter](device-management-azure-portal.md)