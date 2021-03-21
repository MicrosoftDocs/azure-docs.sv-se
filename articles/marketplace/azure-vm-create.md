---
title: Skapa ett erbjudande för virtuell dator på Azure Marketplace.
description: Lär dig hur du skapar ett erbjudande för virtuella datorer på Microsofts kommersiella marknads plats.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ffc09daa15e742ca2b5b8a2fa9323e33fe317c60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200396"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Så här skapar du ett erbjudande för virtuella datorer på Azure Marketplace

Den här artikeln beskriver hur du skapar ett erbjudande för virtuella Azure-datorer för [Azure Marketplace](https://azuremarketplace.microsoft.com/). Den behandlar både Windows-baserade och Linux-baserade virtuella datorer som innehåller ett operativ system, en virtuell hård disk (VHD) och upp till 16 data diskar.

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](partner-center-portal/create-account.md). Se till att ditt konto har registrerats i programmet för kommersiella marknads platser.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har gjort det ännu kan du gå igenom [planera ett erbjudande för virtuella datorer](marketplace-virtual-machines.md). Den förklarar de tekniska kraven för den virtuella datorn och listar den information och de till gångar du behöver när du skapar ditt erbjudande.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. I det vänstra fönstret väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan **Översikt** väljer du **+ nytt erbjud**  >  **Azure Virtual Machine**.

    ![Skärm bild som visar meny alternativ för det vänstra fönstret och knappen "nytt erbjudande".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> När ditt erbjudande har publicerats visas alla ändringar som du gör i Partner Center på Azure Marketplace först när du har publicerat om erbjudandet. Se till att alltid publicera om ett erbjudande när du har gjort ändringar i det.

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Azure Marketplace-erbjudandet och i Azure PowerShell och Azure CLI, om tillämpligt.
- Använd bara gemena bokstäver och siffror. ID: t kan innehålla bindestreck och under streck, men inte blank steg och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudande-1**, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Erbjudandets alias är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Azure Marketplace. Det skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.

Välj **skapa** för att generera erbjudandet och fortsätt. Partner Center öppnar sidan **erbjudande konfiguration** .

## <a name="enable-a-test-drive-optional"></a>Aktivera en testen het (valfritt)

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem till gång till en förkonfigurerad miljö för ett fast antal timmar. Att erbjuda en testen het resulterar i en ökad konverterings takt och genererar hög kvalificerade leads. Mer information om test enheter finns i [Vad är en test enhet?](./what-is-test-drive.md).

> [!TIP]
> En testen het skiljer sig från en kostnads fri utvärderings version. Du kan erbjuda antingen en testad enhet, en kostnads fri utvärderings version eller både och. De förser båda kunderna med din lösning för en fast tids period. Men en testenhet innehåller även en praktisk guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementerings scenario.

Om du vill aktivera en testen het markerar du kryss rutan **Aktivera en testenhet** . Du kommer att konfigurera test enheten senare. Med Test Drive krävs det att du konfigurerar en CRM (se nästa avsnitt).

## <a name="configure-customer-leads-management"></a>Konfigurera hantering av kund ledare

När du publicerar ett erbjudande till den kommersiella Marketplace med partner Center ansluter du det till ditt CRM-system (Customer Relations hip Management). På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten. Att ansluta till en CRM krävs om du vill aktivera en testen het (se föregående avsnitt). Annars är det valfritt att ansluta till en CRM.

1. Under **kund ledare** väljer du länken **Anslut** .
1. I dialog rutan **anslutnings information** väljer du ett mål för lead.
1. Fyll i fälten som visas. Detaljerade anvisningar finns i följande artiklar:

   - [Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurera ditt erbjudande för att skicka leads till Dynamics 365 kund engagemang](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (tidigare Dynamics CRM Online)
   - [Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurera ditt erbjudande för att skicka leads till Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurera ditt erbjudande för att skicka leads till Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Om du vill verifiera konfigurationen som du har angett väljer du länken **Verifiera** .
1. Välj **Anslut**.

Välj **Spara utkast** innan du fortsätter till nästa flik på vänster-navigerings-menyn och **Egenskaper**.

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du egenskaper för erbjudande för virtuell dator](azure-vm-create-properties.md)
- [Metodtips för erbjudandelistor](gtm-offer-listing-best-practices.md)