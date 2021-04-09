---
title: Använd Azure Storage Resource Provider för att få åtkomst till hanterings resurser
description: Azure Storage Resource Provider är en tjänst som ger till gång till hanterings resurser för Azure Storage. Du kan använda Azure Storage Resource Provider för att skapa, uppdatera, hantera och ta bort resurser, till exempel lagrings konton, privata slut punkter och konto åtkomst nycklar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789683"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Använd Azure Storage Resource Provider för att få åtkomst till hanterings resurser

Azure Resource Manager är Azures tjänst för distribution och hantering. Azure Storage Resource Provider är en tjänst som baseras på Azure Resource Manager och som ger till gång till hanterings resurser för Azure Storage. Du kan använda Azure Storage Resource Provider för att skapa, uppdatera, hantera och ta bort resurser, till exempel lagrings konton, privata slut punkter och konto åtkomst nycklar. Mer information om Azure Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).

Du kan använda Azure Storage Resource Provider för att utföra åtgärder som att skapa eller ta bort ett lagrings konto eller hämta en lista över lagrings konton i en prenumeration. Använd Azure Active Directory (Azure AD) om du vill auktorisera begär Anden mot Azure Storage resurs leverantören. Den här artikeln beskriver hur du tilldelar behörigheter till hanterings resurser och pekar på exempel som visar hur du begär förfrågningar mot Azure Storage resurs leverantören.

## <a name="management-resources-versus-data-resources"></a>Hanterings resurser jämfört med data resurser

Microsoft tillhandahåller två REST-API: er för att arbeta med Azure Storage-resurser. Dessa API: er utgör grunden för alla åtgärder som du kan utföra med Azure Storage. Med Azure Storage REST API kan du arbeta med data i ditt lagrings konto, inklusive BLOB-, kö-, fil-och tabell data. Med Azure Storage Resource Provider REST API kan du arbeta med lagrings kontot och relaterade resurser.

En begäran som läser eller skriver BLOB-data kräver olika behörigheter än en begäran som utför en hanterings åtgärd. Azure RBAC ger detaljerad kontroll över behörigheter för båda typerna av resurser. När du tilldelar en Azure-roll till ett säkerhets objekt ser du till att du förstår vilka behörigheter som ska beviljas för huvud kontot. En detaljerad referens som beskriver vilka åtgärder som är associerade med varje inbyggd Azure-roll finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Azure Storage stöder användning av Azure AD för att auktorisera begär Anden mot blob-och Queue-lagring. Information om Azure-roller för blob-och Queue data-åtgärder finns i [bevilja åtkomst till blobbar och köer med hjälp av Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Tilldela hanterings behörigheter med rollbaserad åtkomst kontroll i Azure (Azure RBAC)

Varje Azure-prenumeration har en associerad Azure Active Directory som hanterar användare, grupper och program. En användare, grupp eller ett program kallas även säkerhets objekt i kontexten för [Microsoft Identity Platform](../../active-directory/develop/index.yml). Du kan bevilja åtkomst till resurser i en prenumeration på ett säkerhets objekt som definieras i Active Directory med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC).

När du tilldelar en Azure-roll till ett säkerhets objekt, anger du även omfattningen som de behörigheter som har beviljats av rollen gäller. För hanterings åtgärder kan du tilldela en roll på nivån för prenumerationen, resurs gruppen eller lagrings kontot. Du kan tilldela en Azure-roll till ett säkerhets objekt med hjälp av [Azure Portal](https://portal.azure.com/), [Azure CLI-verktyg](/cli/azure/install-classic-cli), [PowerShell](/powershell/azure/)eller [Azure Storage Resource Provider REST API](/rest/api/storagerp).

Mer information finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?](../../role-based-access-control/overview.md) och de klassiska administratörs rollerna för [prenumerationer, Azure-roller och Azure AD-administratörer](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Inbyggda roller för hanterings åtgärder

Azure innehåller inbyggda roller som ger behörighet att anropa hanterings åtgärder. Azure Storage innehåller också inbyggda roller som är specifika för användning med Azure Storage resurs leverantör.

Inbyggda roller som ger behörighet att anropa lagrings hanterings åtgärder inkluderar de roller som beskrivs i följande tabell:

|    Azure-roll    |    Beskrivning    |    Har du åtkomst till konto nycklar?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Ägare** | Kan hantera alla lagrings resurser och åtkomst till resurser.  | Ja, ger behörighet att visa och återskapa lagrings konto nycklar. |
| **Deltagare**  | Kan hantera alla lagrings resurser, men kan inte hantera åtkomst till resurser. | Ja, ger behörighet att visa och återskapa lagrings konto nycklar. |
| **Läsare** | Kan visa information om lagrings kontot, men kan inte Visa konto nycklarna. | Nej. |
| **Lagringskontodeltagare** | Kan hantera lagrings kontot, hämta information om prenumerationens resurs grupper och resurser och skapa och hantera distributioner av resurs grupper för prenumerationer. | Ja, ger behörighet att visa och återskapa lagrings konto nycklar. |
| **Administratör för användaråtkomst** | Kan hantera åtkomst till lagrings kontot.   | Ja, tillåter ett säkerhets objekt att tilldela sig själva behörigheter och andra. |
| **Virtuell datordeltagare** | Kan hantera virtuella datorer, men inte lagrings kontot som de är anslutna till.   | Ja, ger behörighet att visa och återskapa lagrings konto nycklar. |

Den tredje kolumnen i tabellen visar om den inbyggda rollen stöder **Microsoft. Storage/storageAccounts/listnycklar/åtgärd**. Den här åtgärden ger behörighet att läsa och återskapa lagrings konto nycklar. Behörigheter för åtkomst till Azure Storage hanterings resurser omfattar inte även behörigheter för åtkomst till data. Men om en användare har åtkomst till konto nycklarna kan de använda konto nycklarna för att få åtkomst till Azure Storage data via autentisering med delad nyckel.

### <a name="custom-roles-for-management-operations"></a>Anpassade roller för hanterings åtgärder

Azure har också stöd för att definiera anpassade Azure-roller för åtkomst till hanterings resurser. Mer information om anpassade roller finns i [Azure-anpassade roller](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Kodexempel

Kod exempel som visar hur du auktoriserar och anropar hanterings åtgärder från Azure Storage hanterings bibliotek finns i följande exempel:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager jämfört med klassiska distributioner

Resource Manager och de klassiska distributionsmodellerna är två olika sätt att distribuera och hantera dina Azure-lösningar. Microsoft rekommenderar att du använder Azure Resource Manager distributions modell när du skapar ett nytt lagrings konto. Om möjligt rekommenderar Microsoft också att du återskapar befintliga klassiska lagrings konton med Resource Manager-modellen. Även om du kan skapa ett lagrings konto med hjälp av den klassiska distributions modellen är den klassiska modellen mindre flexibel och kommer slutligen att bli föråldrad.

Mer information om distributions modeller i Azure finns i [Resource Manager och klassisk distribution](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Skalbarhets mål för Azure Storage Resource Provider](scalability-targets-resource-provider.md)