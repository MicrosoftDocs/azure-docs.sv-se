---
title: 'API: er för villkorlig åtkomst och PowerShell – Azure Active Directory'
description: 'Använda API: er för villkorlig åtkomst i Azure AD och PowerShell för att hantera principer som kod'
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860221"
---
# <a name="conditional-access-programmatic-access"></a>Villkorlig åtkomst: program mässig åtkomst

Många organisationer har uttryckt att de behöver hantera så mycket som möjligt av sina miljöer, till exempel kod. Med hjälp av Microsoft Graph kan du behandla principer för villkorlig åtkomst som vilken annan kod som helst i din miljö.

Microsoft Graph tillhandahåller en enhetlig programmerings modell som organisationer kan använda för att interagera med data i Microsoft 365, Windows 10 och Enterprise Mobility + Security. Mer information om Microsoft Graph finns i artikeln [Översikt över Microsoft Graph](/graph/overview).

![En bild som visar de primära resurser och relationer som ingår i grafen](./media/howto-conditional-access-apis/microsoft-graph.png)

Följande exempel tillhandahålls i befintligt skick utan support. Du kan använda dessa exempel som grund för verktyg i din organisation. 

Många av följande exempel använder verktyg som [Managed Identities](../managed-identities-azure-resources/overview.md), [Logic Apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)och [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="configure"></a>Konfigurera

### <a name="powershell"></a>PowerShell

För många administratörer är PowerShell redan ett tolkat skript verktyg. I följande exempel visas hur du använder [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD) för att hantera principer för villkorlig åtkomst.

- [Konfigurera principer för villkorlig åtkomst med Azure AD PowerShell-kommandon](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph API

I det här exemplet visas de grundläggande alternativen skapa, läsa, uppdatera och ta bort (CRUD) som är tillgängliga i API: erna för villkorlig åtkomst. Exemplet innehåller också några JSON-mallar som du kan använda för att skapa några exempel principer.

- [Konfigurera principer för villkorlig åtkomst med Microsoft Graph API-anrop](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Konfigurera med mallar

Använd API: er för villkorlig åtkomst för att distribuera principer för villkorlig åtkomst i för produktions miljön med hjälp av en mall.

- [Konfigurera principer för villkorlig åtkomst med Microsoft Graph-API-mallar](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Test

Det här exemplet modellerar säkrare distributions metoder med arbets flöden för godkännande som kan kopiera principer för villkorlig åtkomst från en miljö, t. ex. för produktion, till en annan, till exempel din produktions miljö.

- [Befordra principer för villkorlig åtkomst från test miljöer](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Distribuera

I det här exemplet finns en mekanism för att utföra en stegvis distribution av principer för villkorlig åtkomst gradvis till din användar population, så att du kan hantera support påverkan och upptäcka problem tidigt.

- [Distribuera principer för villkorlig åtkomst till produktions miljöer med arbets flöden för godkännande](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitor

Det här exemplet innehåller en mekanism för att övervaka ändringar i princip för villkorlig åtkomst över tid och kan utlösa aviseringar när nyckel principer ändras.

- [Övervaka distribuerade principer för villkorlig åtkomst för ändringar och utlösa aviseringar](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Hantera

### <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Automatisera säkerhets kopiering och återställning av principer för villkorlig åtkomst med godkännanden i teamen med hjälp av det här exemplet.

- [Hantera säkerhets kopierings-och återställnings processen för principer för villkorlig åtkomst med Microsoft Graph API-anrop](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Konton för nöd åtkomst

Flera administratörer kan skapa principer för villkorlig åtkomst och kan glömma att lägga till [konton för nöd åtkomst](../roles/security-emergency-access.md) som ett undantag till dessa principer. I det här exemplet ser du till att alla principer uppdateras för att inkludera de utsedda åtkomst kontona.

- [Hantera tilldelningen av nödfalls åtkomst konton till principer för villkorlig åtkomst med hjälp av Microsoft Graph API-anrop](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Planering för oförutsedda händelser

Saker fungerar inte alltid som du vill, när det händer behöver du ett sätt att komma tillbaka till ett tillstånd där arbetet kan fortsätta. I följande exempel får du ett sätt att återställa dina principer till en känd katastrof plan och inaktivera andra principer för villkorlig åtkomst.

- [Hantera aktivering av katastrof principer för villkorlig åtkomst med hjälp av Microsoft Graph API-anrop](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Bidrag från gemenskapen

Dessa exempel är tillgängliga i vår [GitHub-lagringsplats](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). Vi är glada att kunna ge support för community-bidrag för att GitHub problem och pull-begäranden.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Microsoft Graph](/graph/overview)

- [API för villkorlig åtkomst](/graph/api/resources/conditionalaccesspolicy)

- [API för namngiven plats](/graph/api/resources/namedlocation)
