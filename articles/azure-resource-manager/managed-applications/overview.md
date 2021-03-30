---
title: Översikt över hanterade program
description: Beskriver koncepten för Azure Managed Applications, som innehåller moln lösningar som är lätta för konsumenter att distribuera och använda.
author: tfitzmac
ms.topic: overview
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 65b8c21251f092311150a50480f5abecb1ad7c17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434574"
---
# <a name="azure-managed-applications-overview"></a>Översikt över Azure Managed Applications

Med Azure Managed Applications kan du erbjuda molnlösningar som är enkla för användare att distribuera och använda. Du implementerar infrastrukturen och erbjuder fortlöpande support. Om du vill göra ett hanterat program tillgängligt för alla kunder publicerar du det på Azure Marketplace. Publicera det till en intern katalog för att endast göra det tillgängligt för användare i din organisation. 

Ett hanterat program liknar en lösningsmall i Marketplace med en viktig skillnad. I ett hanterat program distribueras resurserna till en resursgrupp som hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. Som utgivare anger du kostnaden för fortlöpande support för lösningen.

> [!NOTE]
> Tidigare fanns dokumentationen för Azure Custom providers i dokumentationen för hanterade program. Den dokumentationen har flyttats. Nu kan du se [Azure-anpassade providers](../custom-providers/overview.md).

## <a name="advantages-of-managed-applications"></a>Fördelar med hanterade program

Hanterade program minskar hindren för användare som använder dina lösningar. De behöver inga kunskaper om molninfrastruktur för att använda din lösning. Konsumenter har begränsad åtkomst till viktiga resurser och behöver inte bekymra sig om att göra fel vid hanteringen. 

Med hanterade program kan du etablera en fortlöpande relation med dina användare. Du definierar villkoren för hanteringen av programmet och alla kostnader hanteras via Azure-fakturering.

Trots att kunder distribuerar hanterade program i sina prenumerationer måste de inte underhålla, uppdatera eller utföra service på dem. Du kan se till att alla kunder använder godkända versioner. Kunder måste inte utveckla programspecifika domänkunskaper för att hantera programmen. Kunder hämtar automatiskt programuppdateringar utan att behöva tänka på felsökning och att diagnostisera problem med programmen. 

För IT-team gör hanterade program att de kan erbjuda förhandsgodkända lösningar till användare i organisationen. Du vet att lösningarna uppfyller organisationens standarder.

Hanterade program har stöd [för hanterade identiteter för Azure-resurser](./publish-managed-identity.md).

## <a name="types-of-managed-applications"></a>Typer av hanterade program

Du kan publicera dina hanterade program externt eller internt.

![Publicera internt eller externt](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Tjänstkatalog

Tjänstkatalogen är en intern katalog med godkända lösningar för användare i en organisation. Du använder katalogen för att uppfylla organisationsstandarder samtidigt som de tillhandahåller lösningar för organisationer. De anställda kan använder katalogen till att enkelt hitta program som rekommenderas och godkänts av IT-avdelningen. De ser de hanterade program som andra personer i organisationen delar med dem.

Information om att publicera en tjänstkatalog för hanterade program finns i [Skapa tjänstkatalogprogram](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Leverantörer som vill fakturera för sina tjänster kan göra ett hanterat program tillgängligt via Azure Marketplace. När en leverantör publicerar ett program är det tillgängligt för användare utanför organisationen. Med den här metoden kan leverantörer av hanterade tjänster (MSP:er), oberoende programvaruleverantörer (ISV:er) och systemintegratörer (SI:er) erbjuda sina lösningar till alla Azure-kunder.

Mer information om hur du publicerar program på Marketplace finns i [Använda marknadsplatsprogram](../../marketplace/create-new-azure-apps-offer.md).

## <a name="resource-groups-for-managed-applications"></a>Resursgrupper för hanterade program

Normalt är resurserna för ett hanterat program i två resurs grupper. Användaren hanterar en resursgrupp och utgivaren hanterar den andra resursgruppen. När utgivaren definierar det hanterade programmet anger han/hon åtkomstnivå. Utgivaren kan begära antingen en permanent roll tilldelning eller [just-in-Time-åtkomst](request-just-in-time-access.md) för en tilldelning som är begränsad till en tids period.

Möjligheten att begränsa åtkomsten för [dataåtgärder](../../role-based-access-control/role-definitions.md) stöds inte för närvarande för alla datalprovidrar i Azure.

Följande avbildning visar ett scenario där utgivaren begär ägarrollen för den hanterade resursgruppen. Utgivaren placerade ett skrivskyddat lås för den här resursgruppen för användaren. Utgivaridentiteter som beviljas åtkomst till den hanterade resursgruppen är undantagna från låset.

![Åtkomst till resursgrupp](./media/overview/access.png)

### <a name="application-resource-group"></a>Programresursgrupp

I den här resursgruppen finns instansen för det hanterade programmet. Den här resursgruppen får bara innehålla en resurs. Resurstypen för det hanterade programmet är **Microsoft.Solutions/applications**.

Användaren har fullständig åtkomst till resursgruppen och använder den för att hantera det hanterade programmets livscykel.

### <a name="managed-resource-group"></a>Hanterad resursgrupp

Den här resursgruppen innehåller alla resurser som krävs av det hanterade programmet. Den här resursgruppen innehåller exempelvis lösningens virtuella datorer, lagringskonton och virtuella nätverk. Användaren har begränsad åtkomst till den här resursgruppen eftersom användaren inte hanterar de enskilda resurserna för det hanterade programmet. Utgivarens åtkomst till den här resursgruppen motsvarar rollen som anges i definitionen av hanterade program. Utgivaren kan till exempel begära ägar- eller deltagarroll för den här resursgruppen. Åtkomsten är antingen permanent eller begränsad till en viss tidpunkt.

När du publicerar det [hanterade programmet på Marketplace](../../marketplace/create-new-azure-apps-offer.md)kan utgivaren ge konsumenterna möjlighet att utföra specifika åtgärder på resurser i den hanterade resurs gruppen. Utgivaren kan till exempel ange att konsumenter kan starta om virtuella datorer. Alla andra åtgärder utöver Läs åtgärder nekas fortfarande. Ändringar av resurser i en hanterad resurs grupp av en konsument med beviljade åtgärder är föremål för [Azure policy](../../governance/policy/overview.md) tilldelningar i klient organisationen som omfattas för att inkludera den hanterade resurs gruppen.

När användaren tar bort det hanterade programmet tas även den hanterade resursgruppen bort.

## <a name="azure-policy"></a>Azure Policy

Du kan använda en [Azure policy](../../governance/policy/overview.md) för att granska det hanterade programmet. Du tillämpar princip definitioner för att se till att distribuerade instanser av ditt hanterade program uppfyller data-och säkerhets kraven. Om programmet interagerar med känsliga data måste du ha utvärderat hur dessa data ska skyddas. Om ditt program till exempel interagerar med data från Microsoft 365 använder du en princip definition för att kontrol lera att data kryptering är aktiverat.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om fördelarna med att använda hanterade program. Gå till nästa artikel för att skapa en definition för det hanterade programmet.

> [!div class="nextstepaction"]
> [Snabbstart: Publicera en definition av Azure-hanterat program](publish-service-catalog-app.md)