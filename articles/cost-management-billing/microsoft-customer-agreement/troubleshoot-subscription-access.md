---
title: Felsöka prenumerationsåtkomst när du har signerat Microsoft-kundavtal – Azure
description: Den här artikeln hjälper dig att felsöka prenumerationsåtkomst när du har signerat en Microsoft-kundavtal.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 04/07/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: cccc43c2133a0c83329c2a519b8e1c88e52eb16a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493100"
---
# <a name="troubleshoot-subscription-access-after-you-sign-a-microsoft-customer-agreement"></a>Felsöka prenumerationsåtkomst när du har signerat en Microsoft-kundavtal

Den här artikeln hjälper dig att felsöka prenumerationsåtkomst när du har signerat en Microsoft-kundavtal. Använd följande information för att felsöka och lösa vanliga problem.

## <a name="troubleshoot-subscription-access"></a>Felsöka prenumerationsåtkomst

Kontrollera att typen av faktureringskonto är Microsoft-kundavtal. Du kan se typen av faktureringskonto i Azure Portal i **Cost Management + Billing**. Mer information finns i [Kontrollera om du har åtkomst till ditt MCA.](../understand/mca-understand-your-usage.md#check-access-to-a-microsoft-customer-agreement)

## <a name="troubleshoot-viewing-your-billing-account"></a>Felsöka visning av ditt faktureringskonto

Om du har problem med att visa ditt faktureringskonto och du har flera klienter kan du prova att byta katalog i Azure Portal.

1. Välj ditt Azure-konto Azure Portal det övre högra hörnet.
1. Välj Växla katalog.  
    :::image type="content" source="./media/troubleshoot-subscription-access/switch-directory.png" alt-text="Skärmbild som visar alternativet Växla katalog." lightbox="./media/troubleshoot-subscription-access/switch-directory.png" :::
1. I fönstret Katalog + prenumeration väljer du den andra katalogen för att växla till den.  
    :::image type="content" source="./media/troubleshoot-subscription-access/select-directory.png" alt-text="Skärmbild som visar var du väljer en annan katalog." lightbox="./media/troubleshoot-subscription-access/select-directory.png" :::

## <a name="troubleshoot-account-access"></a>Felsöka kontoåtkomst

Du kan ha flera konton med Microsoft som använder samma e-postadress. Du kan ha ett personligt konto och ett arbetskonto. Den Microsoft-kundavtal använder ditt arbets- eller skolkonto. Mer information finns i Kontrollera [att du loggar in med ditt arbets- eller skolkonto.](https://support.microsoft.com/office/which-account-do-you-want-to-use-2b5bbd7a-7df6-4283-beff-8015e28eb7b9)

- Logga in med ditt arbets- eller skolkonto.  
    :::image type="content" source="./media/troubleshoot-subscription-access/two-accounts.png" alt-text="Skärmbild som visar val av arbets- eller skolkonto." lightbox="./media/troubleshoot-subscription-access/two-accounts.png" :::

## <a name="next-steps"></a>Nästa steg

- Läs dokumentationen [Microsoft-kundavtal .](https://docs.microsoft.com/azure/cost-management-billing/microsoft-customer-agreement/) 