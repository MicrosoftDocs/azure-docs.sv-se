---
title: 'Microsoft Graph-API: er för PIM (för hands version) – Azure AD | Microsoft Docs'
description: 'Innehåller information om hur du använder Microsoft Graph-API: er för Azure AD Privileged Identity Management (PIM) (för hands version).'
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb102c23d80095b8eb79fc25d1c1fd9d7f374fce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91529702"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Microsoft Graph-API: er för Privileged Identity Management (förhands granskning)

Du kan utföra Privileged Identity Management uppgifter med hjälp av [Microsoft Graph-API: er](/graph/overview) för Azure Active Directory. I den här artikeln beskrivs viktiga begrepp för att använda Microsoft Graph-API: er för Privileged Identity Management.

Mer information om Microsoft Graph-API:er finns i [referensen för Azure AD Privileged Identity Management API](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta).

> [!IMPORTANT]
> API:er under /betaversionen i Microsoft Graph är i förhandsversion och kan komma att ändras. Användning av dessa API:er i produktionsprogram stöds inte.

## <a name="required-permissions"></a>Behörigheter som krävs

Om du vill anropa Microsoft Graph-API: er för Privileged Identity Management måste du ha **en eller flera** av följande behörigheter:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Ställa in behörigheter

För att program ska kunna anropa Microsoft Graph-API: er för Privileged Identity Management måste de ha de behörigheter som krävs. Det enklaste sättet att ange nödvändiga behörigheter är att använda [Azure AD-ramverket för medgivande](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Ställa in behörigheter i Graph Explorer

Om du använder Graph Explorer för att testa anrop kan du ange behörigheterna i verktyget.

1. Logga in på [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) som global administratör.

1. Klicka på **ändra behörigheter**.

    ![Skärm bild som visar sidan "Graph Explorer" med åtgärden "ändra behörigheter" vald.](./media/pim-apis/graph-explorer.png)

1. Markera kryss rutorna bredvid de behörigheter som du vill inkludera. `PrivilegedAccess.ReadWrite.AzureAD` är ännu inte tillgänglig i Graph Explorer.

    ![Graph Explorer – ändra behörigheter](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Klicka på **Ändra behörigheter** för att tillämpa behörighetsändringarna.

## <a name="next-steps"></a>Nästa steg

- [Referens för Azure AD Privileged Identity Management API](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)