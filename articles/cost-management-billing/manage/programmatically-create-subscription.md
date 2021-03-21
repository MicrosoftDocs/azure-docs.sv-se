---
title: Skapa Azure-prenumerationer program mässigt
description: I den här artikeln beskrivs de alternativ som finns tillgängliga för att skapa Azure-prenumerationer programmatiskt.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9ec0ffeb930fd9285f34ad9ba9e6aa606b15b5a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593896"
---
# <a name="create-azure-subscriptions-programmatically"></a>Skapa Azure-prenumerationer program mässigt

I den här artikeln beskrivs de alternativ som finns tillgängliga för att skapa Azure-prenumerationer programmatiskt.

Med hjälp av olika REST-API: er kan du skapa en prenumeration för följande typer av Azure-avtal:

- Enterprise-avtal (EA)
- Microsoft-kundavtal (MCA)
- Microsoft-partneravtal (MPA)

Du kan inte program mässigt skapa ytterligare prenumerationer för andra avtals typer med REST API: er.

Krav och information för att skapa prenumerationer skiljer sig mellan olika avtal och API-versioner. Se följande artiklar som gäller din situation:

Senaste API:er:

- [Skapa EA-prenumerationer](programmatically-create-subscription-enterprise-agreement.md)
- [Skapa MCA-prenumerationer](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Skapa MPA-prenumerationer](programmatically-create-subscription-microsoft-partner-agreement.md)

De här artiklarna visar också hur du skapar prenumerationer med en Azure Resource Manager-mall (ARM-mall). En ARM-mall hjälper till att automatisera processen med att skapa prenumerationer.

Om du fortfarande använder för [hands versions-API: er](programmatically-create-subscription-preview.md)kan du fortsätta att skapa prenumerationer med dem. 

## <a name="next-steps"></a>Nästa steg

* När du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänsthuvudnamn. Mer information finns i [Bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hanteringsgrupper finns i [Ordna resurser med hanteringsgrupper i Azure](../../governance/management-groups/overview.md).
