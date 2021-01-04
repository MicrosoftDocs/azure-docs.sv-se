---
title: Övervaka och spåra användning av kostnadsfria Azure-tjänster
description: Lär dig hur du kontrollerar användningen av kostnadsfria tjänster i Azure-portalen. Det tillkommer ingen kostnad för tjänsterna i ett kostnadsfritt konto, såvida du inte överskrider gränserna för tjänsterna.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: banders
ms.openlocfilehash: c7c28e64822a6aefa17e8baa4ef42a3b3fea8adb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589784"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Kontrollera användningen av kostnadsfria tjänster som ingår i ditt kostnadsfria Azure-konto

Du debiteras inte för tjänster som ingår utan kostnad i ditt kostnadsfria Azure-konto, såvida du inte överskrider tjänsternas användningsgränser. För att hålla dig inom gränserna kan du använda Azure-portalen till att spåra användningen av kostnadsfria tjänster.

## <a name="check-usage-in-the-azure-portal"></a>Kontrollera användning i Azure-portalen

1.  Logga in på [Azure-portalen](https://portal.azure.com).
1.  Sök efter **Prenumerationer**.  
    ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  Välj den prenumeration som skapades när du registrerade det kostnadsfria Azure-kontot.
1.  Rulla nedåt så att du ser tabellen med användning av kostnadsfria tjänster.  
    ![Skärmbild som visar användning av kostnadsfria tjänster](./media/check-free-service-usage/subscription-usage-free-services.png)

Tabellen innehåller följande kolumner:

* **Mätare:** Anger måttenheten för den tjänsten som förbrukas.
* **Användning/gräns:** Aktuell månads användning och gräns för mätaren.
* **Status:** Användningsstatus för tjänsten. Baserat på din användning kan du ha en av följande statusar:
  * **Används inte:** Du har inte använt mätaren, eller så har användningen för mätaren inte nått faktureringssystemet.
  * **Överskrids den \<Date>:** Du överskred gränsen för mätaren \<Date>.
  * **Överskrider sannolikt inte:** Du överskrider sannolikt inte mätarens gräns.
  * **Överskrids den \<Date>:** Du överskrider sannolikt mätarens gräns den \<Date>.

> [!IMPORTANT]
>
> Kostnadsfria tjänster är bara tillgängliga för prenumerationen som skapades när du registrerade det kostnadsfria Azure-kontot. Om du inte ser tabellen med kostnadsfria tjänster i prenumerationsöversikten är de inte tillgängliga för prenumerationen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Uppgradera ditt kostnadsfria Azure-konto](upgrade-azure-subscription.md)
