---
title: Länka ditt partner-ID för att spåra påverkan på delegerade resurser
description: Lär dig hur du associerar ditt partner-ID för att få partner intjänad kredit (PEC) för kund resurser som du hanterar via Azure Lighthouse.
ms.date: 01/28/2021
ms.topic: how-to
ms.openlocfilehash: 46338b925b1ad44019c9cc95e4b7f8c0963c07c4
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985979"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Länka ditt partner-ID för att spåra påverkan på delegerade resurser 

Om du är medlem i [Microsoft Partner Network](https://partner.microsoft.com/)kan du länka ditt partner-ID med de autentiseringsuppgifter som används för att hantera delegerade kund resurser, så att Microsoft kan identifiera och identifiera partner som driver Azure-kund. Med den här länken kan du också använda CSP-partner [(Cloud Solution Provider)](/partner-center/csp-overview) för att få [partner intjänade kredit för hanterade tjänster (PEC)](/partner-center/partner-earned-credit) för kunder som har [undertecknat Microsofts kund avtal (MCA)](/partner-center/confirm-customer-agreement) och som följer [Azure-prenumerationen](/partner-center/azure-plan-get-started).

Om du registrerar [kunder med hanterade tjänst erbjudanden i Azure Marketplace](publish-managed-services-offers.md)sker länkning automatiskt med MPN-ID: t som är kopplat till det Partner Center-konto som används för att publicera erbjudandena. Ingen ytterligare åtgärd krävs för att kunna spåra dina konsekvenser för de här kunderna.

Om du registrerar [kunder med hjälp av Azure Resource Management-mallar](onboard-customer.md)måste du vidta åtgärder för att skapa den här länken. Detta görs genom att [du länkar ditt MPN-ID](../../cost-management-billing/manage/link-partner-id.md) med minst ett användar konto i din hanterings klient som har åtkomst till var och en av dina inbyggda prenumerationer.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associera ditt partner-ID när du har publicerat nya kunder

När du registrerar kunder genom Azure Resource Manager mallar (ARM-mallar) använder du följande process för att länka ditt partner-ID (och aktiverar partner intjänad kredit, om tillämpligt). Du måste känna till ditt [MPN-partner-ID](/partner-center/partner-center-account-setup#locate-your-mpn-id) för att kunna utföra dessa steg. Se till att använda **associerat MPN-ID** som visas i din partnerprofil.

För enkelhetens skull rekommenderar vi att du skapar ett tjänst huvud namns konto i din klient organisation, länkar det till ditt **associerade MPN-ID** och sedan beviljar åtkomst till varje kund som du har registrerat med en [inbyggd Azure-roll som är berättigad till PEC](/partner-center/azure-roles-perms-pec).

1. [Skapa ett tjänst huvud konto](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) i hanterings klienten. I det här exemplet använder vi *Automation-kontot namn Provider* för detta tjänst objekt.
1. Med hjälp av kontot för tjänstens huvud namn [länkar du till ditt associerade MPN-ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) i hanterings klienten. Du behöver bara göra detta en gången.
1. När du registrerar [en kund som använder arm-mallar](onboard-customer.md)måste du ta med en auktorisering som innehåller ett Automation-konto för providern som en användare med en [inbyggd Azure-roll som är berättigad till PEC](/partner-center/azure-roles-perms-pec).

Genom att följa dessa steg associeras alla kund innehavare som du hanterar med ditt partner-ID. Providerns Automation-konto behöver inte autentisera eller utföra några åtgärder i kundens klient organisation.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagram som visar länk processen partner-ID med Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Lägg till ditt partner-ID till tidigare registrerade kunder

Om du redan har registrerat en kund kanske du inte vill utföra en annan distribution för att lägga till providerns Automation-kontots tjänst huvud namn. I stället kan du länka ditt **associerade MPN-ID** till ett användar konto som redan har åtkomst till arbete i den kundens klient organisation. Se till att kontot har fått en [inbyggd Azure-roll som är berättigad till PEC](/partner-center/azure-roles-perms-pec).

När kontot har [länkats till ditt associerade MPN-ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) i hanterings klienten kan du spåra igenkänningen av din kunds påverkan.

## <a name="confirm-partner-earned-credit"></a>Bekräfta den intjänade partner krediten

Du kan [Visa PEC-information i Azure Portal](/partner-center/partner-earned-credit-explanation#azure-cost-management) och bekräfta vilka kostnader som har fått fördelen med PEC. Kom ihåg att PEC endast gäller för CSP-kunder som har signerat MCA och som finns i Azure-planen.

Om du har följt stegen ovan och inte ser den förväntade associationen öppnar du en supportbegäran i Azure Portal.

Du kan också använda [partner Center SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) och filtrera på `rateOfPartnerEarnedCredit` för att automatisera PEC-verifiering för en prenumeration.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Microsoft Partner Network](/partner-center/mpn-overview).
- Lär dig [hur PEC beräknas och betalas](/partner-center/partner-earned-credit-explanation).
- [Publicera kunder](onboard-customer.md) till Azure-Lighthouse.