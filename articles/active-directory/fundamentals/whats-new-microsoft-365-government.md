---
title: Vad är nytt för Azure AD i Microsoft 365 myndigheter? | Microsoft Docs
description: Läs om några ändringar i Azure Active Directory (Azure AD) i Microsoft 365 myndighets moln instans, vilket kan påverka dig.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89318938"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Vad är nytt för Azure Active Directory i Microsoft 365 myndigheter

Vi har gjort några ändringar i Azure Active Directory (Azure AD) i Microsoft 365 myndighets moln instans, som är tillämpliga för kunder som använder följande tjänster:

- Microsoft Azure Government

- Microsoft 365 myndigheter – GCC hög

- Microsoft 365 myndigheter – DoD

Den här artikeln gäller inte för Microsoft 365 myndigheter – GCC kunder.

## <a name="changes-to-the-initial-domain-name"></a>Ändringar i det ursprungliga domän namnet

Under din organisations första registrering för en Microsoft 365 myndighets online-tjänst uppmanades du att välja din organisations domän namn `<your-domain-name>.onmicrosoft.com` . Om du redan har ett domän namn med. com-suffixet ändras ingenting.

Men om du registrerar dig för en ny Microsoft 365 myndighets tjänst uppmanas du att välja ett domän namn med hjälp av `.us` suffixet. Därför är det `<your-domain-name>.onmicrosoft.us` .

>[!Note]
>Den här ändringen gäller inte för kunder som hanteras av moln tjänst leverantörer (CSP).

## <a name="changes-to-portal-access"></a>Ändringar av Portal åtkomst

Vi har uppdaterat portalens slut punkter för Microsoft Azure Government, Microsoft 365 myndigheter – GCC hög och Microsoft 365 myndigheter – DoD, som visas i [tabellen slut punkts mappning](#endpoint-mapping).

Tidigare kunde kunder logga in med de internationella Azure-portal.azure.com-och Office 365-portarna (portal.office.com). Med den här uppdateringen måste kunderna nu logga in med hjälp av den speciella Microsoft Azure Government, Microsoft 365 myndighets-GCC hög, och Microsoft 365 myndighets-DoD portaler.

## <a name="endpoint-mapping"></a>Slut punkts mappning

I följande tabell visas slut punkterna för alla kunder:

| Name | Slut punkts information |
|------|------------------|
| Portaler |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 myndigheter – GCC hög: https://portal.office365.us<p>Microsoft 365 myndigheter – DoD: https://portal.apps.mil |
| Slut punkt för Azure Active Directory utfärdare | https://login.microsoftonline.us |
| Microsoft Graph-API för Microsoft 365 myndighets GCC hög | https://graph.microsoft.us |
| Microsoft Graph-API för Microsoft 365 myndigheter – DoD | https://dod-graph.microsoft.us |
| Azure Government Services-slutpunkter | Mer information finns i [Azure Government Developer Guide](../../azure-government/documentation-government-developer-guide.md) |
| Microsoft 365 myndigheter – GCC med höga slut punkter | Mer information finns i [Office 365 amerikanska myndigheter gcc High-slutpunkter](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 myndigheter – DoD | Mer information finns i [Office 365 amerikanska myndigheter DoD-slutpunkter](/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Nästa steg

Mer information finns i de här artiklarna:

- [Vad är Azure Government?](../../azure-government/documentation-government-welcome.md)

- [Uppdatering av Azure Government AAD-auktoritets slut punkt](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph slut punkter i det amerikanska myndighets molnet](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 amerikanska myndigheter GCC hög och DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)