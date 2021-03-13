---
title: Visa och hantera kunder och delegerade resurser i Azure Portal
description: Som tjänst leverantör eller företag som använder Azure-Lighthouse kan du Visa alla delegerade resurser och prenumerationer genom att gå till mina kunder i Azure Portal.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419339"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Visa och hantera kunder och delegerade resurser i Azure Portal

Tjänste leverantörer som använder [Azure Lighthouse](../overview.md) kan använda sidan **mina kunder** i [Azure Portal](https://portal.azure.com) för att Visa delegerade kund resurser och prenumerationer.

> [!TIP]
> Vi kommer att hänvisa till tjänst leverantörer och kunder här, men [företag som hanterar flera klienter](../concepts/enterprise.md) kan använda samma process för att konsolidera sin hanterings upplevelse.

Du kommer åt sidan **Mina kunder** på Azure Portal genom att välja **alla tjänster** och sedan söka efter **Mina kunder** och välja den. Du kan också hitta den genom att ange "mina kunder" i sökrutan längst upp i Azure Portal.

Tänk på att avsnittet främsta **kunder** på sidan **Mina kunder** endast visar information om kunder som har delegerade prenumerationer eller resurs grupper till din Azure Active Directory-klient (Azure AD) via Azure Lighthouse. Om du arbetar med andra kunder (t. ex. via [Cloud Solution Provider](/partner-center/csp-overview)) kan du inte se information om de här kunderna i avsnittet **kunder** , om du inte har publicerat [dem på Azure-Lighthouse](onboard-customer.md) (även om du kan se information om vissa CSP-kunder i avsnittet för [hands version av Cloud Solution Provider (för hands version](#cloud-solution-provider-preview) ) på sidan).

> [!NOTE]
> Dina kunder kan visa information om tjänst leverantörer genom att gå till **tjänst leverantörer** i Azure Portal. Mer information finns i [Visa och hantera tjänst leverantörer](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Visa och hantera kund information

Om du vill visa kund information väljer du **kunder** till vänster på sidan **Mina kunder** .

> [!IMPORTANT]
> För att kunna se den här informationen måste användarna ha fått rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsar åtkomst) i onboarding-processen.

För varje kund ser du kundens namn, kund-ID (klient-ID) och **erbjudande-ID** och **erbjudande version** som är kopplad till ärendet. I kolumnen **delegeringar** ser du antalet delegerade prenumerationer och/eller antalet delegerade resurs grupper.

Med alternativen överst på sidan kan du sortera, filtrera och gruppera kund information efter specifika kunder, erbjudanden eller nyckelord.

Du kan visa följande information på den här sidan:

- Om du vill se alla prenumerationer, erbjudanden och delegeringar som är associerade med en kund väljer du kundens namn.
- Om du vill se mer information om ett erbjudande och dess delegeringar väljer du namnet på erbjudandet.
- Om du vill visa mer information om roll tilldelningar för delegerade prenumerationer eller resurs grupper väljer du posten i kolumnen **delegeringar** .

## <a name="view-and-manage-delegations"></a>Visa och hantera delegeringar

Delegeringarna visar den prenumeration eller resurs grupp som har delegerats, tillsammans med de användare och behörigheter som har åtkomst till den. Om du vill visa den här informationen väljer du **delegeringar** på vänster sida av sidan **Mina kunder** .

Med alternativen längst upp på sidan kan du sortera, filtrera och gruppera informationen genom vissa kunder, erbjudanden eller nyckelord.

### <a name="view-role-assignments"></a>Visa rolltilldelningar

Användare och behörigheter som är associerade med varje delegering visas i kolumnen **roll tilldelningar** . Du kan välja varje post om du vill visa en fullständig lista över användare, grupper och tjänstens huvud namn som har beviljats åtkomst till prenumerationen eller resurs gruppen. Därifrån kan du välja en viss användare, grupp eller tjänstens huvud namn för att få mer information.

### <a name="remove-delegations"></a>Ta bort delegeringar

Om du har inkluderat användare med [tilldelnings rollen för hantering av hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) när du registrerar en kund på Azure-Lighthouse kan dessa användare ta bort en delegering genom att välja pappers korgs ikonen som visas i raden för delegeringen. När de gör det kommer inga användare i tjänste leverantörens klient organisation att kunna komma åt de resurser som tidigare har delegerats.

Mer information finns i [ta bort åtkomst till en delegering](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Visa Delegerings ändrings aktivitet

Avsnittet **aktivitets logg** på sidan **Mina kunder** håller koll på varje gång kund prenumerationer eller resurs grupper delegeras till din klient, och varje gång som tidigare delegerade resurser tas bort. Den här informationen kan endast visas av användare som har [tilldelats rollen som övervaknings läsare i rot omfånget](monitor-delegation-changes.md).

Mer information finns i [Visa Delegerings ändringar i Azure Portal](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Arbeta i kontexten för en delegerad prenumeration

Du kan arbeta direkt i kontexten för en delegerad prenumeration i Azure Portal, utan att byta katalog som du är inloggad på. Så här gör du:

1. Välj ikonen **katalog + prenumeration** längst upp i Azure Portal.
2. I **standard prenumerations filtret** kontrollerar du att endast rutan för den delegerade prenumerationen är markerad. Du kan använda List rutan **aktuella och delegerade kataloger** om du bara vill visa prenumerationer i en angiven katalog. (Använd inte alternativet **Växla katalog** eftersom det ändrar den katalog som du är inloggad på.)

Om du sedan ansluter till en tjänst som stöder [hantering av flera innehavare](../concepts/cross-tenant-management-experience.md), kommer tjänsten att standard kontexten för den delegerade prenumeration som du har valt. Du kan ändra detta genom att följa stegen ovan och **Markera kryss rutan Markera alla** (eller välja en eller flera prenumerationer som du vill arbeta i i stället).

> [!NOTE]
> Om du har beviljats åtkomst till en eller flera resurs grupper, i stället för att få åtkomst till en hel prenumeration, väljer du den prenumeration som resurs gruppen tillhör. Sedan arbetar du i samband med den prenumerationen, men kommer bara att kunna komma åt de angivna resurs grupperna.

Du kan också få åtkomst till funktioner relaterade till delegerade prenumerationer eller resurs grupper från tjänster som stöder hantering av flera innehavare genom att välja prenumerationen eller resurs gruppen inifrån den tjänsten.

## <a name="cloud-solution-provider-preview"></a>Cloud Solution Provider (för hands version)

En separat avsnittet om **moln lösnings leverantör (för hands version)** på sidan **Mina kunder** visar fakturerings information och resurser för dina CSP-kunder som har [undertecknat Microsofts kund avtal (MCA)](/partner-center/confirm-customer-agreement) och följer [Azure-planen](/partner-center/azure-plan-get-started). Mer information finns i [Kom igång med ditt fakturerings konto för Microsoft partner avtal](../../cost-management-billing/understand/mpa-overview.md).

Sådana CSP-kunder visas i det här avsnittet oavsett om du också har publicerat dem till Azure-Lighthouse. På samma sätt behöver inte en CSP-kund visas i avsnittet **Cloud Solution Provider (för hands version)** i **Mina kunder** för att du ska kunna publicera dem på Azure Lighthouse.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).
- Lär dig hur dina kunder kan [Visa och hantera tjänst leverantörer](view-manage-service-providers.md) genom att gå till **tjänst leverantörer** i Azure Portal.
