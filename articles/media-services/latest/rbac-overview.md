---
title: Rollbaserad åtkomst kontroll för Media Services-konton
description: Den här artikeln beskriver rollbaserad åtkomst kontroll i Azure (Azure RBAC) för Azure Media Services-konton.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 7c9edce71e0d0565de80153bfd5b3d5f1edb2e8f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955164"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Rollbaserad åtkomst kontroll i Azure (Azure RBAC) för Media Services-konton

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

För närvarande definierar Azure Media Services inte några anpassade roller som är speciella för tjänsten. För att få fullständig åtkomst till Media Services kontot kan kunder använda de inbyggda rollerna för **ägare** eller **deltagare**. Den största skillnaden mellan dessa roller är: **ägaren** kan styra vem som har åtkomst till en resurs och **deltagaren** inte kan. Den inbyggda **läsar** rollen kan också användas, men användaren eller programmet kommer bara att ha Läs behörighet till Media Services API: er. 

## <a name="design-principles"></a>Designprinciper

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3-API: er returnerar inte hemligheter eller autentiseringsuppgifter för **Get** -eller **list** åtgärder. Nycklarna är alltid null, tomma eller oberoende av svaret. Användaren måste anropa en separat åtgärds metod för att få hemligheter eller autentiseringsuppgifter. Rollen **läsare** kan inte anropa åtgärder som till gång. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Med separata åtgärder kan du ställa in mer detaljerade Azure RBAC-säkerhetsbehörigheter i en anpassad roll om du vill.

Gör så här om du vill visa vilka åtgärder som Media Services stöder:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Den [inbyggda roll definitions](../../role-based-access-control/built-in-roles.md) artikeln visar exakt vad rollen beviljar. 

Mer information finns i följande artiklar:

- [Klassiska administratörs roller för prenumeration, Azure-roller och Azure AD-roller](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av REST API](../../role-based-access-control/role-assignments-rest.md)
- [Åtgärder för Media Services Resource Provider](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Nästa steg

- [Utveckla med Media Services v3-API: er](media-services-apis-overview.md)
- [Hämta princip för innehålls nyckel med Media Services .NET](get-content-key-policy-dotnet-howto.md)
