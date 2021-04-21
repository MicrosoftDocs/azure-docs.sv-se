---
title: Skapa ett erbjudande för virtuella datorer Azure Marketplace.
description: Lär dig hur du skapar ett erbjudande om virtuella datorer på Microsofts kommersiella marknadsplats.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 04/08/2021
ms.openlocfilehash: f0c1d9d528ed4fbf61786042fb6fb34f05fec5d5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812601"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Så här skapar du ett erbjudande om virtuella datorer på Azure Marketplace

Den här artikeln beskriver hur du skapar ett erbjudande för virtuella Azure-datorer [för Azure Marketplace](https://azuremarketplace.microsoft.com/). Den hanterar både Windows- och Linux-baserade virtuella datorer som innehåller ett operativsystem, en virtuell hårddisk (VHD) och upp till 16 datadiskar.

Innan du börjar skapar [du ett konto för den kommersiella marknadsplatsen i Partnercenter](create-account.md). Kontrollera att ditt konto har registrerats i programmet för den kommersiella marknadsplatsen.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har gjort det ännu kan du läsa [Planera ett erbjudande för virtuell dator.](marketplace-virtual-machines.md) Den förklarar de tekniska kraven för den virtuella datorn och listar den information och de tillgångar som du behöver när du skapar ditt erbjudande.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [Partner Center.](https://partner.microsoft.com/dashboard/home)
2. I den vänstra rutan väljer du **Översikt över Kommersiell**  >  **marknadsplats.**
3. På sidan **Översikt** väljer du **+ Nytt erbjudande För** virtuell  >  **Azure-dator.**

    ![Skärmbild som visar menyalternativen i det vänstra fönstret och knappen "Nytt erbjudande".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> När ditt erbjudande har publicerats visas eventuella ändringar som du gör i Partnercenter den Azure Marketplace du publicerar erbjudandet igen. Se till att alltid publicera om ett erbjudande när du har gjort ändringar i det.

Ange ett **erbjudande-ID.** Det här är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webbadressen för Azure Marketplace-erbjudandet och i Azure PowerShell och Azure CLI, om tillämpligt.
- Använd bara gemena bokstäver och siffror. ID:t kan innehålla bindestreck och understreck, men inga blanksteg och är begränsat till 50 tecken. Om du till exempel anger **test-offer-1** blir erbjudandets webbadress `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID kan inte ändras när du har valt **Skapa.**

Ange ett **erbjudandealias**. Erbjudandets alias är det namn som används för erbjudandet i Partnercenter.

- Det här namnet används inte på Azure Marketplace. Det skiljer sig från erbjudandets namn och andra värden som visas för kunder.

Välj **Skapa** för att generera erbjudandet och fortsätta. Partnercenter öppnar sidan **För erbjudandekonfiguration.**

## <a name="enable-a-test-drive-optional"></a>Aktivera en provkörning (valfritt)

En provkörning är ett bra sätt att demonstrera ditt erbjudande för potentiella kunder genom att ge dem åtkomst till en förkonfigurerad miljö under ett fast antal timmar. Att erbjuda en testkörning resulterar i en ökad konverteringsgrad och genererar högt kvalificerade leads. Mer information om testenheter finns i [Vad är en provkörning?](./what-is-test-drive.md).

> [!TIP]
> En testkörning skiljer sig från en kostnadsfri utvärderingsversion. Du kan erbjuda antingen en provkörning, en kostnadsfri utvärderingsversion eller både och. Båda ger kunderna en lösning under en viss tidsperiod. Men en provkörning innehåller också en praktisk, guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementeringsscenario.

Om du vill aktivera en provkörning markerar **du kryssrutan Aktivera en** provkörning. Du konfigurerar testenheten senare. Med testkörningen krävs konfiguration av en CRM (se nästa avsnitt).

## <a name="configure-customer-leads-management"></a>Konfigurera hantering av kund leads

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

Välj **Spara utkast** innan du fortsätter till nästa flik i den vänstra navigeringsmenyn, **Egenskaper.**

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du egenskaper för erbjudande för virtuella datorer](azure-vm-create-properties.md)
- [Metodtips för erbjudandelistor](gtm-offer-listing-best-practices.md)