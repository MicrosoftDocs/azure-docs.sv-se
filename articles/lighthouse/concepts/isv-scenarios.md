---
title: Azure Lighthouse i ISV-scenarier
description: 'Funktionerna i Azure Lighthouse kan användas av ISV: er för mer flexibilitet med kund erbjudanden.'
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696388"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Azure Lighthouse i ISV-scenarier

Ett vanligt scenario för [Azure Lighthouse](../overview.md) är en tjänst leverantör som hanterar resurser i sina kunders Azure Active Directory (Azure AD)-klient organisationer. Funktionerna i Azure Lighthouse kan också användas av oberoende program varu leverantörer (ISV: er) med SaaS-baserade erbjudanden med sina kunder. Azure Lighthouse kan vara särskilt användbart för ISV: er som erbjuder hanterade tjänster eller stöd som kräver åtkomst till prenumerations omfånget.

## <a name="managed-service-offers-in-azure-marketplace"></a>Erbjudanden om hanterade tjänster på Azure Marketplace

Som en ISV kanske du redan har publicerat lösningar på Azure Marketplace. Om du erbjuder hanterade tjänster till dina kunder kan du göra det genom att publicera ett hanterat tjänst erbjudande. Dessa erbjudanden effektiviserar onboarding-processen och gör dina tjänster mer skalbara till så många kunder som behövs. Azure Lighthouse stöder en mängd olika [hanterings uppgifter och scenarier](cross-tenant-management-experience.md#enhanced-services-and-scenarios) som kan användas för att ge kunderna mervärde.

Mer information finns i [publicera ett hanterat tjänst erbjudande på Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Använda Azure-Lighthouse med Azure-hanterade program

[Azure-hanterade program](../../azure-resource-manager/managed-applications/overview.md) är ett annat sätt som ISV: er kan tillhandahålla tjänster till sina kunder. Du kan använda Azure-Lighthouse tillsammans med dina Azure-hanterade program för att aktivera förbättrade scenarier.

Mer information finns i [Azure-Lighthouse och Azure-hanterade program](managed-applications.md).

## <a name="saas-based-multi-tenant-offerings"></a>SaaS-baserade erbjudanden för flera innehavare

Ett annat scenario är att ISV är värd för resurserna i en prenumeration i sin egen klient och använder sedan Azure-Lighthouse för att låta kunderna komma åt resurserna. Kunden kan sedan logga in på sin egen klient och få åtkomst till dessa resurser efter behov. ISV: er underhåller sin IP-adress i sin egen klient organisation och kan använda sina egna support avtal för att öka biljetter som är relaterade till den lösning som finns i klient organisationen, i stället för att använda kundens plan. Eftersom resurserna finns i ISV-klienten kan alla åtgärder utföras direkt av ISV, till exempel Logga in på virtuella datorer, installera appar och utföra underhålls aktiviteter.

I det här scenariot beviljas användare i kundens klient organisation i princip åtkomst som "hantering av klient", även om kunden inte hanterar ISV-resurserna. Eftersom de ansluter till ISV: s klient direkt, är det viktigt att endast bevilja de lägsta behörigheter som krävs, så att kunderna oavsiktligt inte kan göra ändringar i lösningen eller andra ISV-resurser.

För att aktivera den här arkitekturen måste ISV: n hämta objekt-ID: t för en användar grupp i kundens Azure AD-klient, tillsammans med klient-ID. ISV: en skapar sedan en ARM-mall som beviljar den här användar gruppen rätt behörigheter och [distribuerar den på ISV: s prenumeration](../how-to/onboard-customer.md) som innehåller de resurser som kunden kommer att få åtkomst till.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](cross-tenant-management-experience.md).
- Lär dig mer om [Azure-delegerad resurshantering](azure-delegated-resource-management.md).