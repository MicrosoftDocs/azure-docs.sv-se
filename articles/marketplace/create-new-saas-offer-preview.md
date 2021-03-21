---
title: Så här lägger du till en förhands gransknings grupp för ditt SaaS-erbjudande på Microsofts kommersiella marknads plats
description: Så här lägger du till en förhands gransknings mål grupp för SaaS-erbjudandet (Software as a Service) i Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 041df9b77e85f09d2cf680773edc995c6288acaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96746428"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Så här lägger du till en förhands gransknings grupp för ditt SaaS-erbjudande

När du skapar ett SaaS-erbjudande (Software as a Service) i Partner Center måste du definiera en förhands gransknings grupp som kan granska din erbjudande lista innan den går live. Den här artikeln förklarar hur du konfigurerar en förhands gransknings grupp.

> [!NOTE]
> Om du väljer att bearbeta transaktioner oberoende av visas inte det här alternativet. Gå istället till [hur du säljer ditt SaaS-erbjudande](create-new-saas-offer-marketing.md).

## <a name="define-a-preview-audience"></a>Definiera en förhands gransknings publik

På fliken **förhands visning** kan du definiera en begränsad mål grupp som kan granska ditt SaaS-erbjudande innan du publicerar det Live till den bredare Marketplace-publiken. Du kan skicka inbjudningar till Microsoft-konto (MSA) eller Azure Active Directory (Azure AD) e-postadresser. Lägg till minst en och upp till 10 e-postadresser manuellt eller importera upp till 20 med en. csv-fil. Du kan när som helst uppdatera listan för förhands granskning.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat publik. En förhands gransknings mål grupp har åtkomst till ditt erbjudande innan den publiceras Live i onlinebutiker. Du kan också välja att skapa en plan och bara göra den tillgänglig för en privat publik. Privata planer beskrivs i [skapa planer för ditt SaaS-erbjudande](create-new-saas-offer-plans.md).

### <a name="add-email-addresses-manually"></a>Lägg till e-postadresser manuellt

1. På sidan för **hands versions mål** lägger du till en enskild Azure AD-eller MSA-e-postadress och en valfri beskrivning i rutorna som tillhandahålls.
1. Om du vill lägga till en annan e-postadress väljer du länken **Lägg till en annan e-** postadress
1. Välj **Spara utkast** innan du fortsätter till nästa flik: **teknisk konfiguration**.
1. Fortsätt till [hur du lägger till teknisk information för ditt SaaS-erbjudande](create-new-saas-offer-technical.md).

### <a name="add-email-addresses-using-the-csv-file"></a>Lägg till e-postadresser med CSV-filen

1. På sidan för **hands versions mål** väljer du länken **Exportera mål grupp (CSV)** .
1. Öppna. CSV-fil i ett program, till exempel Microsoft Excel.
1. I. CSV-fil i kolumnen **ID** anger du de e-postadresser som du vill lägga till i förhands gransknings mål gruppen.
1. I kolumnen **Beskrivning** kan du välja att lägga till en beskrivning för varje e-postadress.
1. I kolumnen **typ** lägger du till **MixedAadMsaId** till varje rad som har en e-postadress.
1. Spara filen som en. CSV-fil.
1. På sidan för **hands versions mål** väljer du länken **Importera mål grupp (CSV)** .
1. Välj **Ja** i dialog rutan **Bekräfta** .
1. Välj. CSV-fil och välj sedan **Öppna**.
1. Välj **Spara utkast** innan du fortsätter till nästa flik: **teknisk konfiguration**.

## <a name="next-steps"></a>Nästa steg

- [Så här lägger du till teknisk information för ditt SaaS-erbjudande](create-new-saas-offer-technical.md)
