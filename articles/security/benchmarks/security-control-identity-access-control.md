---
title: Azures säkerhets kontroll – identitet och Access Control
description: Azures säkerhets kontroll identitet och Access Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 08ba75b5160acb20cb5e2d172203ae48b546f17d
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202240"
---
# <a name="security-control-identity-and-access-control"></a>Säkerhets kontroll: identitet och Access Control

Rekommendationer för identitets-och åtkomst hantering fokuserar på att hantera problem som rör identitetsbaserade åtkomst kontroller, låsning av administrativ åtkomst, varningar om identitetsbaserade händelser, onormalt konto beteende och rollbaserad åtkomst kontroll.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3.1 | 4.1 | Kund |

Azure AD har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

## <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3.2 | 4.2 | Kund |

Azure AD har inte begreppet standard lösen ord. Andra Azure-resurser som kräver ett lösen ord tvingar ett lösen ord att skapas med komplexitets krav och minsta längd på lösen ord, vilket varierar beroende på tjänsten. Du ansvarar för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

## <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3.3 | 4.3 | Kund |

Skapa standard operativa procedurer kring användningen av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

Du kan också aktivera en just-in-Time/bara-tillräcklig-åtkomst genom att använda Azure AD Privileged Identity Management privilegierade roller för Microsoft-tjänster och Azure Resource Manager. 

- [Läs mer om Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3.4 | 4.4 | Kund |

När det är möjligt ska du använda Azure Active Directory SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Förstå SSO med Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3.5 | 4,5, 11,5, 12,11, 16,3 | Kund |

Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering.

- [Aktivera MFA i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3,6 | 4,6, 11,6, 12,12 | Kund |

Använd Paw (Privileged Access Workstation) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera MFA i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3.7 | 4,8, 4,9 | Kund |

Använd Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3,8 | 11,7 | Kund |

Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Kund |

Använd Azure Active Directory som den centrala autentiserings-och auktoriserings systemet. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3,10 | 16,9, 16,10 | Kund |

Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

- [Förstå Azure AD repor ting](../../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3,11 | 16,12 | Kund |

Du har åtkomst till Azure AD-inloggning, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla SIEM/övervaknings verktyg.

Du kan effektivisera processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3,12 | 16,13 | Kund |

Använd funktioner för Azure AD-risk och identitets skydd för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 3,13 | 16 | Kund |

I support scenarier där Microsoft behöver åtkomst till kund information, ger Customer Lockbox ett gränssnitt som du kan använda för att granska och godkänna eller avvisa förfrågningar om kund data åtkomst.

- [Förstå Customer Lockbox](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll: [data skydd](security-control-data-protection.md)