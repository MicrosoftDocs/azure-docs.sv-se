---
title: Så här lägger du till en förhands gransknings grupp för ditt hanterade tjänst erbjudande
description: Lär dig hur du lägger till en förhands gransknings grupp för ditt hanterade tjänst erbjudande i Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918417"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Så här lägger du till en förhands gransknings grupp för ditt hanterade tjänst erbjudande

Den här artikeln beskriver hur du konfigurerar en förhands gransknings grupp för ett hanterat tjänst erbjudande på den kommersiella Marketplace med hjälp av Partner Center. Åhörarna i förhands granskningen kan granska ditt erbjudande innan det blir Live.

## <a name="define-a-preview-audience"></a>Definiera en förhands gransknings publik

På sidan för **hands versions mål** kan du definiera en begränsad publik som kan granska ditt hanterade tjänst erbjudande innan du publicerar det Live till den bredare Marketplace-publiken. Du definierar förhands gransknings publiken med ID: n för Azure-prenumerationer, tillsammans med en valfri beskrivning av varje. Inget av dessa fält kan ses av kunder. Du hittar ditt prenumerations-ID för Azure på sidan **prenumerationer** på Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100) för att definiera vem som kan förhandsgranska ditt erbjudande innan det publiceras Live. Om erbjudandet redan är Live kan du fortfarande definiera en förhands gransknings grupp för att testa uppdateringar av ditt erbjudande.

> [!NOTE]
> En *förhands gransknings* mål skiljer sig från en *privat* publik. En förhands gransknings mål grupp har åtkomst till ditt erbjudande innan den publiceras Live i onlinebutiker. De kan se och validera alla planer, inklusive de som endast är tillgängliga för en privat mål grupp när ditt erbjudande är fullständigt publicerat i Marketplace. Du kan bara göra en plan tillgänglig för en privat mål grupp. En privat publik (som definieras i en plans tillgänglighets flik) har exklusiv åtkomst till en viss plan.

## <a name="add-email-addresses-manually"></a>Lägg till e-postadresser manuellt

1. På sidan för **hands versions mål** lägger du till ett enda Azure-prenumerations-ID och en valfri beskrivning i rutorna.
2. Om du vill lägga till en annan e-postadress väljer du länken **Lägg till ID (max 10)** .
3. Välj **Spara utkast** innan du fortsätter till nästa flik.

## <a name="add-email-addresses-using-a-csv-file"></a>Lägg till e-postadresser med en CSV-fil

1. På sidan för hands **Grans kar** åhörarna väljer du länken **Exportera mål grupp (CSV)** .
2. Öppna CSV-filen. I kolumnen **ID** anger du de ID för Azure-prenumerationer som du vill lägga till i förhands gransknings mål gruppen.
3. I kolumnen **Beskrivning** har du möjlighet att lägga till en beskrivning för varje post.
4. I kolumnen **typ** lägger du till **SubscriptionId** till varje rad som har ett ID.
5. Spara filen som en CSV-fil.
6. På sidan för **hands versions mål** väljer du länken **Importera mål grupp (CSV)** .
7. I dialog rutan **Bekräfta** väljer du **Ja** och laddar upp CSV-filen.
8. Välj **Spara utkast** innan du fortsätter till nästa flik.

## <a name="next-steps"></a>Nästa steg

* [Skapa planer](create-managed-service-offer-plans.md)
