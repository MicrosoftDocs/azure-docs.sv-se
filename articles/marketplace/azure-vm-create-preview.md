---
title: Lägg till en förhands gransknings grupp för ett erbjudande för virtuella datorer på Azure Marketplace
description: Lär dig hur du lägger till en förhands gransknings grupp för ett erbjudande för virtuella datorer på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 854d0fa9b34d495f03a0c3c6203ceb227e4712d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629520"
---
# <a name="how-to-add-a-preview-audience-for-a-virtual-machine-offer"></a>Så här lägger du till en förhands gransknings grupp för ett erbjudande för virtuell dator

På sidan för **hands versionen** (Välj från vänster-navigerings-menyn i Partner Center) väljer du en begränsad **förhands gransknings grupp** för att verifiera ditt erbjudande innan du publicerar det Live till den bredare webbplatsen för kommersiella marknads platser.

> [!IMPORTANT]
> När du har kontrollerat ditt erbjudande i **förhands gransknings** fönstret väljer du **Go Live** för att publicera ditt erbjudande för den publika publike Marketplace.

Din förhands gransknings mål identifieras av ID: n för **Azure Subscription ID** , tillsammans med en valfri **Beskrivning** av varje. Inget av dessa fält kan ses av kunder. Du hittar ditt prenumerations-ID för Azure på sidan **prenumerationer** i Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10 ID) eller genom att ladda upp en CSV-fil (upp till 100 ID). Genom att lägga till dessa prenumerations-ID: n definierar du vem som kan förhandsgranska ditt erbjudande innan det publiceras Live. Om ditt erbjudande redan är Live kan du fortfarande definiera en förhands gransknings grupp för testning av erbjudanden eller uppdateringar av erbjudandet.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat publik. En förhands gransknings grupp kan komma åt ditt erbjudande *innan* det publiceras Live på Azure Marketplace. Förhands gransknings publiken kan se och validera alla planer, inklusive de som endast är tillgängliga för en privat publik när ditt erbjudande har publicerats fullständigt till Azure Marketplace. En privat mål grupp (som definieras i fönstret planera **priser och tillgänglighet** ) har exklusiv åtkomst till en viss plan.

Om du har gjort ändringarna väljer du **Spara utkast** innan du fortsätter till nästa flik i menyn till vänster-navigerings, **planerar översikt**.

## <a name="next-steps"></a>Nästa steg

- [Skapa planer](azure-vm-create-plans.md)
