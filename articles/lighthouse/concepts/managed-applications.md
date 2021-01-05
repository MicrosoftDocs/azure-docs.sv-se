---
title: Azure Lighthouse och Azure Managed Applications
description: Förstå hur Azure-Lighthouse och Azure-hanterade program kan användas tillsammans.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 5c30c3234a57e25ceaa521ad485f58d4d663ebe9
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693964"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse och Azure Managed Applications

Både Azure-hanterade program och Azure Lighthouse fungerar genom att tillhandahålla en tjänst leverantör för att få åtkomst till resurser som finns i kundens klient organisation. Det kan vara bra att förstå skillnaderna på det sätt som de fungerar och de scenarier som de hjälper till att aktivera och hur de kan användas tillsammans.

> [!TIP]
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet kan [företag som hanterar flera klienter](enterprise.md) använda samma processer och verktyg.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Jämför Azure-Lighthouse och Azure-hanterade program

I den här tabellen beskrivs några skillnader på hög nivå som kan påverka om du kan välja att använda Azure-Lighthouse eller Azure-hanterade program. Som anges nedan kan du också utforma en lösning som använder dem tillsammans.

|Att tänka på  |Azure Lighthouse  |Azure-hanterade program  |
|---------|---------|---------|
|Typisk användare     |Tjänste leverantörer eller företag som hanterar flera klienter         |Oberoende program varu leverantörer (ISV: er)         |
|Omfång för åtkomst mellan klienter     |Prenumeration (er) eller resurs grupp (er)         |Resurs grupp (begränsat till ett enda program)         |
|Köpbara på Azure Marketplace     |Nej (erbjudanden kan publiceras på Azure Marketplace, men kunder faktureras separat)        |Ja         |
|IP-skydd     |Ja (IP kan finnas kvar hos tjänst leverantörens klient organisation)        |Ja (enligt design är resurs gruppen låst till kunder)         |
|Avvisa tilldelning     |Nej         |Ja        |

### <a name="azure-lighthouse"></a>Azure Lighthouse

Med [Azure Lighthouse](../overview.md)kan en tjänst leverantör utföra en mängd olika hanterings uppgifter direkt på en kunds prenumeration (eller resurs grupp). Den här åtkomsten uppnås via en logisk projektion som gör det möjligt för tjänst leverantörer att logga in på sin egen klient organisation och få åtkomst till resurser som tillhör kundens klient organisation. Kunden kan avgöra vilka prenumerationer eller resurs grupper som ska delegeras till tjänst leverantören och kunden behåller fullständig åtkomst till dessa resurser. De kan också ta bort tjänst leverantörens åtkomst när som helst.

För att kunna använda Azure-Lighthouse, registreras kunder för [Azure-delegerad resurs hantering](azure-delegated-resource-management.md) , antingen genom att [distribuera arm-mallar](../how-to/onboard-customer.md) eller via ett [hanterat tjänst erbjudande i Azure Marketplace](managed-services-offers.md). Du kan spåra din påverkan på kund engagemang genom [att länka ditt partner-ID](../how-to/partner-earned-credit.md).

Azure-Lighthouse används vanligt vis när en tjänst leverantör utför hanterings uppgifter för en kund kontinuerligt.

### <a name="azure-managed-applications"></a>Azure-hanterade program

Med [Azure Managed Applications](../../azure-resource-manager/managed-applications/overview.md) kan en tjänst leverantör eller ISV erbjuda moln lösningar som är enkla för kunder att distribuera och använda i sina egna prenumerationer.

I ett hanterat program paketeras de resurser som används av programmet tillsammans och distribueras till en resurs grupp som hanteras av utgivaren. Den här resurs gruppen finns i kundens prenumeration, men en identitet i utgivarens klient organisation har åtkomst till den. ISV fortsätter att hantera och underhålla det hanterade programmet, medan kunden inte har direkt åtkomst till arbete i sin resurs grupp eller någon åtkomst till dess resurser.

Hanterade program stöder [anpassade Azure Portal upplevelser](../../azure-resource-manager/managed-applications/concepts-view-definition.md) och [integrering med anpassade providers](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Dessa alternativ kan användas för att tillhandahålla en mer anpassad och integrerad upplevelse, vilket gör det enklare för kunderna att utföra vissa hanterings uppgifter själva.

Hanterade program kan [publiceras på Azure Marketplace](../../marketplace/create-new-azure-apps-offer.md), antingen som ett privat erbjudande för en specifik kunds användning eller som offentliga erbjudanden som flera kunder kan köpa. De kan också levereras till användare i din organisation genom [att publicera hanterade program till tjänst katalogen](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). Du kan distribuera både tjänst katalog-och Marketplace-instanser med ARM-mallar, som kan innehålla en prenumeration på kommersiell Marketplace-partner för att spåra [användnings behörighet för kunder](../../marketplace/azure-partner-customer-usage-attribution.md).

Azure-hanterade program används vanligt vis för specifika kund behov som kan uppnås genom en nyckel färdig lösning som är helt hanterad av tjänste leverantören.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Använda Azure-Lighthouse och Azure-hanterade program tillsammans

Även om Azure-Lighthouse och Azure-hanterade program använder olika åtkomst metoder för att uppnå olika mål, kan det finnas scenarier där det är meningsfullt att en tjänste leverantör använder sig av båda med samma kund.

En kund kan till exempel vilja hantera tjänster som levereras av en tjänst leverantör via Azure Lighthouse, så att de har insyn i partnerns åtgärder tillsammans med fortsatt kontroll över deras delegerade prenumeration. Men tjänste leverantören kanske inte vill att kunden ska komma åt vissa resurser som kommer att lagras i kundens klient organisation, eller tillåta anpassade åtgärder på dessa resurser. För att uppfylla dessa mål kan tjänste leverantören publicera ett privat erbjudande som ett hanterat program. Det hanterade programmet kan innehålla en resurs grupp som har distribuerats i kundens klient, men som inte kan nås direkt av kunden.

Kunder kan också vara intresserade av hanterade program från flera tjänst leverantörer, oavsett om de även använder hanterade tjänster via Azure Lighthouse från någon av dessa leverantörer. Dessutom kan partner i program varan Cloud Solution Provider (CSP) sälja vissa hanterade program som publicerats av andra ISV: er till kunder som de stöder via Azure Lighthouse. Med en mängd olika alternativ kan tjänst leverantörer välja rätt saldo för att uppfylla kundernas behov samtidigt som de begränsar åtkomsten till resurser när det är lämpligt.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-hanterade program](../../azure-resource-manager/managed-applications/overview.md).
- Lär dig att [publicera en prenumeration på Azure Lighthouse](../how-to/onboard-customer.md).
- Lär dig mer om [ISV-scenarier med Azure Lighthouse](isv-scenarios.md).