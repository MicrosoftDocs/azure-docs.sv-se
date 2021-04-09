---
title: Hanterings guide för Azure Applications-hanterade program – Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera ett hanterat program på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 09badebab86da8e4fd8d35307aa6648a26e91821
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232264"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Publicerings guide för Azure-hanterade program

Ett Azure- *hanterat program* erbjuder ett sätt att publicera ett Azure-program på Azure Marketplace. Hanterade program är Transact-erbjudanden som distribueras och debiteras via Azure Marketplace. Det List alternativ som en användare ser är att *Hämta det nu*.

I den här artikeln beskrivs kraven för typen av erbjudande för hanterade program.

Använd det hanterade programmets erbjudande typ under följande omständigheter:

- Du distribuerar en prenumerations-baserad lösning för kunden genom att antingen använda en virtuell dator (VM) eller en hel infrastruktur som en tjänst (IaaS)-baserad lösning.
- Du eller din kund kräver att lösningen hanteras av en partner.

>[!NOTE]
>En partner kan till exempel vara en System Integrator eller en hanterad tjänst leverantör (MSP).  

## <a name="managed-application-offer-requirements"></a>Krav för hanterade program erbjudanden

|Krav |Information  |
|---------|---------|
|En Azure-prenumeration | Hanterade program måste distribueras till en kunds prenumeration, men de kan hanteras av en tredje part. |
|Fakturering och mätning    |  Resurserna finns i en kunds Azure-prenumeration. Azure-resurser som använder betalnings modellen betala per användning samverkar med kunden via Microsoft och faktureras via kundens Azure-prenumeration. <br><br> För att få Azure-resurser med egen licens debiterar Microsoft alla infrastruktur kostnader som uppstår i kund prenumerationen, men du debiteras licens avgifterna för Transact-programvaran med kunden direkt.        |
|Ett Azure-hanterat programpaket    |   Den konfigurerade Azure Resource Manager-mallen och skapa användar gränssnitts definition som ska användas för att distribuera ditt program till kundens prenumeration.<br><br>Mer information om hur du skapar ett hanterat program finns i [Översikt över hanterade program](../azure-resource-manager/managed-applications/publish-service-catalog-app.md).|

---

> [!NOTE]
> Hanterade program måste kunna distribueras via Azure Marketplace. Om kund kommunikationen är ett problem kan du kontakta intresserade kunder när du har aktiverat delning av lead.  

> [!Note]
> En partner kanal för en moln lösnings leverantör (CSP) är nu tillgänglig. Mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner Channels finns i [moln lösnings leverantörer](./cloud-solution-providers.md).

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, lär du dig hur du kan [Utöka din moln verksamhet med Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrera dig och börja arbeta i Partner Center:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [skapa ett erbjudande för Azure-program](./create-new-azure-apps-offer.md) .
