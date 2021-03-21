---
title: Så här lägger du till en förhands gransknings mål grupp för ditt Azure Application erbjudande
description: Lär dig hur du lägger till en förhands gransknings grupp för ditt Azure-program i Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370460"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Så här lägger du till en förhands gransknings mål grupp för ditt Azure Application erbjudande

Den här artikeln beskriver hur du konfigurerar en förhands gransknings grupp för ett Azure Application erbjudande i den kommersiella marknads platsen. Du måste definiera en förhands gransknings grupp som kan granska din erbjudande lista innan den går live.

## <a name="define-a-preview-audience"></a>Definiera en förhands gransknings publik

På sidan för **hands versions mål** kan du definiera en begränsad mål grupp som kan granska ditt Azure Application erbjudande innan du publicerar det Live till den bredare Marketplace-publiken. Du definierar förhands gransknings publiken med ID: n för Azure-prenumerationer, tillsammans med en valfri beskrivning av varje. Inget av dessa fält kan ses av kunder. Du hittar ditt prenumerations-ID för Azure på sidan **prenumerationer** i Azure Portal.

Lägg till minst ett och upp till 10 ID för Azure-prenumeration, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100) för att definiera vem som kan förhandsgranska ditt erbjudande innan det publiceras Live. Om ditt erbjudande redan är Live kan du fortfarande definiera en förhands gransknings grupp för testning av erbjudande ändringar eller uppdateringar av erbjudandet.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat publik. En förhands gransknings mål grupp har åtkomst till ditt erbjudande innan den publiceras Live i onlinebutiker. De kan se och validera alla planer, inklusive de som endast är tillgängliga för en privat mål grupp när ditt erbjudande har publicerats fullständigt till Marketplace. Du kan bara göra en plan tillgänglig för en privat mål grupp. En privat publik (som definieras i en plans **tillgänglighets** flik) har exklusiv åtkomst till en viss plan.

### <a name="add-email-addresses-manually"></a>Lägg till e-postadresser manuellt

1. På sidan för **hands versions mål** lägger du till ett enda Azure-prenumerations-ID och en valfri beskrivning i rutorna.
1. Om du vill lägga till en annan e-postadress väljer du länken **Lägg till ID (max 10)** .
1. Välj **Spara utkast** innan du fortsätter till nästa flik: teknisk konfiguration.
1. Gå till [Nästa steg](#next-steps).

### <a name="add-email-addresses-using-the-csv-file"></a>Lägg till e-postadresser med CSV-filen

1. På sidan för **hands versions mål** väljer du länken **Exportera mål grupp (CSV)** .
1. Öppna. CSV-fil i ett program, till exempel Microsoft Excel.
1. I. CSV-fil i kolumnen **ID** anger du de ID: n för Azure-prenumerationer som du vill lägga till i förhands gransknings gruppen.
1. I kolumnen **Beskrivning** kan du välja att lägga till en beskrivning för varje e-postadress.
1. I kolumnen **typ** lägger du till **SubscriptionID** till varje rad som har en e-postadress.
1. Spara filen som en. CSV-fil.
1. På sidan för **hands versions mål** väljer du länken **Importera mål grupp (CSV)** .
1. Välj **Ja** i dialog rutan **Bekräfta** .
1. Välj. CSV-fil och välj sedan **Öppna**.
1. Välj **Spara utkast** innan du fortsätter till nästa flik: teknisk konfiguration.

## <a name="next-steps"></a>Nästa steg

- [Så här lägger du till teknisk information för Azure Application erbjudandet](create-new-azure-apps-offer-technical.md)
