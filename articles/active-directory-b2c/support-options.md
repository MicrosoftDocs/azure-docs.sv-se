---
title: Stöd för Azure Active Directory B2C | Microsoft Docs
description: Så här File support-begäranden för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5195241003b1ce4ea505002e2cc3c10410e6cde1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "78183728"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: begär Anden om fil support
Du kan File support-begäranden för Azure Active Directory B2C (Azure AD B2C) på Azure Portal med hjälp av följande steg:

1. Växla från din B2C-klient till en annan klient som har en Azure-prenumeration kopplad till sig. Det sistnämnda är vanligt vis din medarbetare eller standard klient organisation som skapats åt dig när du registrerade dig för en Azure-prenumeration. Mer information finns i [hur en Azure-prenumeration är relaterad till Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Azure Portal med val av innehavare markerat](./media/support-options/support-switch-dir.png)

1. När du har växlat klienter klickar du på **Hjälp + Support**.

    ![Hjälp + support panel markerad i Azure Portal](./media/support-options/support-support.png)

1. Klicka på **ny supportbegäran**.

    ![Ny panel för support förfrågan är markerad i Azure Portal](./media/support-options/support-new.png)

1. Använd den här informationen i bladet **grundläggande** och klicka på **Nästa**.

    * **Typ av problem** är **teknisk**.
    * Välj lämplig **prenumeration**.
    * **Tjänsten** är **Active Directory**.
    * Välj lämplig **Support plan**. Om du inte har någon kan du registrera dig för det [här](https://azure.microsoft.com/support/plans/).

     ![Sidan grunder med nästa knapp markerad i Azure Portal](./media/support-options/support-basics.png)

1. Använd den här informationen i bladet **problem** och klicka på **Nästa**.

    * Välj lämplig **allvarlighets** grad.
    * **Problem typen** är **B2C**.
    * Välj lämplig **kategori**.
    * Beskriv problemet i fältet **information** . Ange information som B2C-klientens namn, beskrivning av problemet, fel meddelanden, korrelations-ID: n (om tillgängligt) och så vidare.
    * Ange datum och tid (inklusive tidszon) som problemet inträffade i fältet **tidsram** .
    * Under **fil uppladdning** laddar du upp alla skärm dum par och filer som du tycker hjälper till att lösa problemet.

     ![Sidan problem med nästa knapp markerad i Azure Portal](./media/support-options/support-problem.png)

1. Lägg till din kontakt information på bladet **kontakt information** . Klicka på **Skapa**.

    ![Sidan kontakt information med knappen Skapa markerad i portalen](./media/support-options/support-contact.png)

1. När du har skickat in ditt support ärende kan du övervaka det genom att klicka på **Hjälp + Support** på Start sidan och sedan **Hantera support förfrågningar**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Känt problem: en supportbegäran arkiveras i kontexten för en B2C-klient

Om du missade steg 2 som beskrivs ovan och försöker skapa en supportbegäran i kontexten för din B2C-klient visas följande fel.

> [!IMPORTANT]
> Försök inte registrera dig för en ny Azure-prenumeration i B2C-klienten.

![Du har inget prenumerations fel som visas i Azure Portal](./media/support-options/support-no-sub.png)
