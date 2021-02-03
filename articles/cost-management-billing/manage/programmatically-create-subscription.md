---
title: Skapa Azure-prenumerationer programmatiskt
description: I den här artikeln beskrivs de alternativ som finns tillgängliga för att skapa Azure-prenumerationer programmatiskt.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 546ed24b5f9e7892f40c9d425b668f60ad705f8f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493896"
---
# <a name="create-azure-subscriptions-programmatically"></a>Skapa Azure-prenumerationer program mässigt

I den här artikeln beskrivs de alternativ som finns tillgängliga för att skapa Azure-prenumerationer programmatiskt.

Med hjälp av olika REST-API: er kan du skapa en prenumeration för följande typer av Azure-avtal:

- Enterprise-avtal (EA)
- Microsoft-kundavtal (MCA)
- Microsoft-partneravtal (MPA)

Du kan inte programmässigt skapa ytterligare prenumerationer för andra avtalstyper med REST-API:er.

Krav och information för att skapa prenumerationer skiljer sig mellan olika avtal och API-versioner. Se följande artiklar som gäller din situation:

Senaste API:er:

- [Skapa EA-prenumerationer](programmatically-create-subscription-enterprise-agreement.md)
- [Skapa MCA-prenumerationer](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Skapa MPA-prenumerationer](programmatically-create-subscription-microsoft-partner-agreement.md)

Om du fortfarande använder [förhandsversioner av API:er](programmatically-create-subscription-preview.md) kan du fortsätta skapa prenumerationer med dem. 

Du kan också [skapa prenumerationer med en ARM-mall](create-subscription-template.md). En ARM-mall bidrar till att automatisera processen för att skapa prenumerationer med REST-API:er. 

## <a name="next-steps"></a>Nästa steg

* När du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänsthuvudnamn. Mer information finns i [Bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hanteringsgrupper finns i [Ordna resurser med hanteringsgrupper i Azure](../../governance/management-groups/overview.md).
