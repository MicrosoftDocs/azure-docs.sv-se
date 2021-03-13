---
title: Azure Lighthouse i företagsscenarier
description: Funktionerna i Azure Lighthouse kan användas för att förenkla hanteringen av flera innehavare i ett företag som använder flera Azure AD-klienter.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 97b44f71750bdb533e889546f370a9b36ea5d3b4
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419362"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse i företagsscenarier

Ett vanligt scenario för [Azure-Lighthouse](../overview.md) är när en tjänst leverantör hanterar resurser i Azure Active Directory (Azure AD)-klienter som tillhör kunder. Funktionerna i Azure Lighthouse kan också användas för att förenkla hanteringen av flera innehavare i ett företag som använder flera Azure AD-klienter.

## <a name="single-vs-multiple-tenants"></a>En eller flera klienter

För de flesta organisationer är hanteringen enklare med en enda Azure AD-klient. Med alla resurser inom en klient organisation kan centralisering hanterings uppgifter hanteras av användare, användar grupper eller tjänstens huvud namn inom den klient organisationen. Vi rekommenderar att du använder en klient organisation för din organisation närhelst det är möjligt.

Vissa organisationer kan behöva använda flera Azure AD-klienter. Detta kan vara en tillfällig situation, precis som när förvärv har ägt rum och en långsiktig företags konsoliderings strategi inte har definierats ännu. Andra tider kan organisationer behöva upprätthålla flera klienter med jämna mellanrum på grund av helt oberoende dotter bolag, geografiska eller juridiska krav eller andra överväganden.

I de fall där en arkitektur för flera innehavare krävs kan Azure Lighthouse hjälpa till att centralisera och effektivisera hanterings åtgärder. Genom att använda [Azure-delegerad resurs hantering](azure-delegated-resource-management.md)kan användare i en hanterings klient utföra [hanterings funktioner för flera innehavare](cross-tenant-management-experience.md) på ett centraliserat och skalbart sätt.

## <a name="tenant-management-architecture"></a>Arkitektur för klient hantering

Om du vill använda Azure Lighthouse i ett företag måste du bestämma vilken klient som ska innehålla de användare som utför hanterings åtgärder på de andra klient organisationerna. Med andra ord måste du ange en klient organisation som hanterings klient för andra klienter.

Anta till exempel att din organisation har en enda klient som vi ska kalla *klient a*. Din organisation kommer sedan att förvärva *klient B* och *klient C* och du har affärs skäl som kräver att du underhåller dem som separata klienter. Men du vill använda samma princip definitioner, säkerhets kopierings metoder och säkerhets processer för alla dessa, med hanterings uppgifter som utförs av samma uppsättning användare.

Eftersom klient organisationen redan innehåller användare i din organisation som har utfört dessa uppgifter för klient A, kan du publicera prenumerationer i klient B och klient C, vilket gör att samma användare i klient organisationen kan utföra dessa uppgifter över alla klienter.

![Diagram som visar användare i klient organisation A hantera resurser i klient B och klient organisation C.](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Säkerhets-och åtkomst överväganden

I de flesta företags scenarier vill du delegera en fullständig prenumeration till Azure-Lighthouse. Du kan också välja att endast delegera vissa resurs grupper i en prenumeration.

Något av följande sätt måste du [följa principen om minsta behörighet när du definierar vilka användare som ska ha åtkomst till delegerade resurser](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). På så sätt kan du se till att användarna bara har de behörigheter som krävs för att utföra de uppgifter som krävs och minskar risken för oavsiktliga fel.

Azure Lighthouse tillhandahåller endast logiska länkar mellan en hanterande klient organisation och hanterade klienter i stället för att fysiskt flytta data eller resurser. Dessutom går åtkomsten alltid i en riktning, från hanterings klienten till de hanterade klient organisationerna.  Användare och grupper i hanterings klienten bör fortsätta att använda Multi-Factor Authentication när de utför hanterings åtgärder på hanterade klient resurser.

Företag med interna eller externa styrnings-och guardrails kan använda [Azures aktivitets loggar](../../azure-monitor/essentials/platform-logs-overview.md) för att uppfylla sina genomskinlighets krav. När företags klienter har upprättat hantera och hanterade klient relationer, kan användare i varje klient Visa loggad aktivitet för att se åtgärder som vidtagits av användarna i hanterings klienten.

## <a name="onboarding-considerations"></a>Onboarding-överväganden

Prenumerationer (eller resurs grupper inom en prenumeration) kan läggas till i Azure-Lighthouse, antingen genom att distribuera Azure Resource Manager mallar eller genom hanterings tjänster som har publicerats på Azure Marketplace.

Eftersom företags användare vanligt vis har direkt åtkomst till företagets klienter, och det inte finns något behov av att marknadsföra eller marknadsföra ett hanterings erbjudande, är det vanligt vis snabbare och mer enkelt att distribuera Azure Resource Manager mallar. Även om [onboarding-vägledningen](../how-to/onboard-customer.md) avser tjänst leverantörer och kunder kan företag använda samma processer för att publicera sina klienter.

Om du vill kan klient organisationer inom ett företag registreras genom [att publicera ett erbjudande för hanterade tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md). För att säkerställa att erbjudandet endast är tillgängligt för lämpliga klienter, se till att dina planer är markerade som privata. Med en privat plan anger du prenumerations-ID: n för varje klient som du planerar att publicera, och ingen annan kan få ditt erbjudande.

## <a name="terminology-notes"></a>Terminologi-anteckningar

För hantering av flera innehavare i företaget kan referenser till tjänst leverantörer i Azure Lighthouse-dokumentationen förstås för hantering av klient organisationer i ett företag, det vill säga den klient som innehåller de användare som ska hantera resurser i andra klienter via Azure Lighthouse. På samma sätt kan alla referenser till kunder förstås för de klienter som delegerar resurser som ska hanteras via användare i hanterings klienten.

I det exempel som beskrivs ovan kan klient A betraktas som tjänst leverantörens klient organisation (hanterings klienten) och klient B och klient C kan betraktas som kund innehavare.

Om du fortsätter med det exemplet kan klienten som använder rätt behörighet [Visa och hantera delegerade resurser](../how-to/view-manage-customers.md) på sidan **mina kunder** i Azure Portal. På samma sätt kan klient B-och klient organisation C-användare med lämpliga behörigheter [Visa och hantera de resurser som har delegerats](../how-to/view-manage-service-providers.md) till klient organisation A på sidan **tjänst leverantörer** i Azure Portal.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](cross-tenant-management-experience.md).
- Lär dig mer om [Azure-delegerad resurshantering](azure-delegated-resource-management.md).
