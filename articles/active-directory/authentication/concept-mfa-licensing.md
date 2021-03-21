---
title: Azure AD Multi-Factor Authentication-versioner och förbruknings planer
description: Lär dig mer om Azure AD Multi-Factor Authentication-klienten och olika metoder och versioner som är tillgängliga.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d880146a0b068a5d097c452c14b28db4907098
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743963"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Funktioner och licenser för Azure AD Multi-Factor Authentication

Använd Multi-Factor Authentication för att skydda användar konton i din organisation. Den här funktionen är särskilt viktig för konton som har privilegie rad åtkomst till resurser. Grundläggande Multi-Factor Authentication-funktioner är tillgängliga för Microsoft 365 och Azure Active Directory (Azure AD)-administratörer utan extra kostnad. Om du vill uppgradera funktionerna för dina administratörer eller utöka Multi-Factor Authentication till resten av användarna kan du köpa Azure AD-Multi-Factor Authentication på flera sätt.

> [!IMPORTANT]
> Den här artikeln beskriver de olika sätt som Azure AD Multi-Factor Authentication kan licensiera och använda. Mer detaljerad information om priser och fakturering finns på sidan med [priser för Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Tillgängliga versioner av Azure AD Multi-Factor Authentication

Azure AD Multi-Factor Authentication kan användas och licensieras på ett par olika sätt beroende på organisationens behov. Du kanske redan har rätt att använda Azure AD Multi-Factor Authentication beroende på Azure AD-, EMS-eller Microsoft 365-licensen som du för närvarande har. Följande tabell innehåller information om de olika sätten att hämta Azure AD Multi-Factor Authentication och några av funktionerna och användnings fallen för var och en.

| Om du är en användare av | Funktioner och användnings fall |
| --- | --- |
| Microsoft 365 Business Premium och EMS eller Microsoft 365 E3 och E5 | EMS E3, Microsoft 365 E3 och Microsoft 365 Business Premium inkluderar Azure AD Premium P1. EMS E5 eller Microsoft 365 E5 innehåller Azure AD Premium P2. Du kan använda samma funktioner för villkorlig åtkomst som anges i följande avsnitt för att ge Multi-Factor Authentication till användare. |
| Azure AD Premium P1 | Du kan använda [villkorlig åtkomst i Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) för att uppmana användarna att använda Multi-Factor Authentication under vissa scenarier eller händelser så att de passar dina affärs behov. |
| Azure AD Premium P2 | Ger den starkaste säkerhets positionen och förbättrad användar upplevelse. Lägger till [riskfylld villkorlig åtkomst](../conditional-access/howto-conditional-access-policy-risk.md) till de Azure AD Premium P1-funktioner som anpassas efter användares mönster och minimerar Multi-Factor Authentication-prompter. |
| Alla Microsoft 365s planer | Azure AD Multi-Factor Authentication kan [aktive ras per användare](howto-mfa-userstates.md)eller aktive ras eller inaktive ras för alla användare med hjälp av [säkerhets inställningar](../fundamentals/concept-fundamentals-security-defaults.md). Hantering av Azure AD-Multi-Factor Authentication är via Microsoft 365 portalen. Uppgradera till Azure AD Premium P1 eller P2 och Använd villkorlig åtkomst för att få en bättre användar upplevelse. Mer information finns i [skydda Microsoft 365 resurser med Multi-Factor Authentication](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). |
| Azure AD Kostnadsfri | Du kan använda [säkerhets inställningar](../fundamentals/concept-fundamentals-security-defaults.md) för att aktivera Multi-Factor Authentication för alla användare. Du har inte detaljerad kontroll över aktiverade användare eller scenarier, men det ger ytterligare säkerhets steg.<br /> Även om säkerhets standarder inte används för att aktivera Multi-Factor Authentication för alla, kan användare som har tilldelats rollen *Global administratör för Azure AD* konfigureras att använda Multi-Factor Authentication. Den här funktionen i den kostnads fria nivån gör att de kritiska administratörs kontona skyddas av Multi-Factor Authentication. |

## <a name="feature-comparison-of-versions"></a>Funktions jämförelse av versioner

I följande tabell visas en lista över de funktioner som är tillgängliga i olika versioner av Azure AD Multi-Factor Authentication. Planera dina behov för att skydda användarautentisering och ta sedan reda på vilken metod som uppfyller dessa krav. Även om Azure AD Free ger till gång till säkerhets standardvärden som tillhandahåller Azure AD Multi-Factor Authentication, kan endast appen mobilappar användas för autentisering, inte ett telefonsamtal eller SMS. Den här metoden kan vara en begränsning om du inte kan se till att appen för mobil autentisering är installerad på en användares personliga enhet.

| Funktion | Azure AD Free-säkerhets inställningar | Azure AD Free – globala Azure AD-administratörer | Microsoft 365-appar | Azure AD Premium P1 eller P2 |
| --- |:---:|:---:|:---:|:---:|
| Skydda Azure AD-klientens administratörs konton med MFA | ● | ● (Endast *Azure AD global administratörs* konton) | ● | ● |
| Mobilapp som en andra faktor | ● | ● | ● | ● |
| Telefonsamtal som en andra faktor | | ● | ● | ● |
| SMS som en andra faktor | | ● | ● | ● |
| Administratörs kontroll över verifierings metoder | | ● | ● | ● |
| Bedrägerivarning | | | | ● |
| MFA-rapporter | | | | ● |
| Anpassade hälsningar för telefonsamtal | | | | ● |
| Anpassat samtals-ID för telefonsamtal | | | | ● |
| Tillförlitliga IP-adresser | | | | ● |
| MFA sparas för betrodda enheter | | ● | ● | ● |
| MFA för lokala program | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Köp och aktivera Azure AD Multi-Factor Authentication

Om du vill använda Azure AD Multi-Factor Authentication registrerar du eller köper en berättigad Azure AD-nivå. Azure AD ingår i fyra versioner – kostnads fria Microsoft 365 appar, Premium P1 och Premium P2.

Den kostnads fria versionen ingår i en Azure-prenumeration. Se [avsnittet nedan](#azure-ad-free-tier) för information om hur du använder säkerhets standarder eller skyddar konton med rollen *Global administratör för Azure AD* .

Azure AD Premium-versionerna är tillgängliga via din Microsoft-representant, [Open Volume License-programmet](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)och [Cloud solution providers-programmet](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Azure och Microsoft 365 prenumeranter kan också köpa Azure Active Directory Premium P1 och P2 online. [Logga in](https://portal.office.com/Commerce/Catalog.aspx) för att köpa.

När du har köpt den nödvändiga Azure AD-nivån, [planera och Distribuera Azure ad Multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Azure AD Free nivå

Alla användare i en Azure AD Free klient organisation kan använda Azure AD Multi-Factor Authentication genom att använda säkerhets inställningar. Mobilappen är den enda metoden som kan användas för Azure AD-Multi-Factor Authentication när du använder Azure AD Free säkerhets inställningar.

* [Läs mer om säkerhets inställningar i Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Aktivera säkerhets inställningar för användare i Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Om du inte vill aktivera Azure AD Multi-Factor Authentication för alla användare kan du istället välja att endast skydda användar konton med rollen *Global administratör för Azure AD* . Den här metoden ger ytterligare autentiserings-prompter för kritiska administratörs konton. Du aktiverar Azure AD Multi-Factor Authentication på något av följande sätt, beroende på vilken typ av konto du använder:

* Om du använder ett Microsoft-konto kan du [Registrera dig för Multi-Factor Authentication](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Om du inte använder ett Microsoft-konto [aktiverar du Multi-Factor Authentication för en användare eller grupp i Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om kostnader finns i [priser för Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [Vad är villkorsstyrd åtkomst](../conditional-access/overview.md)

