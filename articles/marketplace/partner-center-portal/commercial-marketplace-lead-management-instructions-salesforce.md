---
title: Hantering av lead i Salesforce – Microsofts kommersiella marknads platser
description: Lär dig hur du använder Salesforce för att konfigurera leads för Microsoft AppSource och Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489326"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurera ledar hantering för Salesforce

Den här artikeln beskriver hur du konfigurerar ditt Salesforce-system för att bearbeta försäljnings leads från dina erbjudanden i Microsoft AppSource och Azure Marketplace.

> [!NOTE]
> Azure Marketplace har inte stöd för förifyllda listor, till exempel en lista över värden för fältet **land** . Se till att inga listor har kon figurer ATS innan du fortsätter. Alternativt kan du konfigurera en [https-slutpunkt](./commercial-marketplace-lead-management-instructions-https.md) eller en [Azure-tabell](./commercial-marketplace-lead-management-instructions-azure-table.md) för att ta emot leads.

## <a name="set-up-your-salesforce-system"></a>Konfigurera ditt Salesforce-system

1. Logga in på Salesforce.
1. Navigera till inställningarna för **webb-till-lead** . 
    
    Om du använder Salesforce-belysningen
    1. Välj **Inställningar** på Start sidan för Salesforce.

       ![Salesforce-installation](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. På sidan **installation** går du till **plattforms verktyg**  >  **funktions inställningar**  >  **marknadsföring**  >  **webb-till-lead**.

        ![Salesforce webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Om du använder den klassiska Salesforce-miljön:

    1. Välj **Inställningar** på Start sidan för Salesforce.

       ![Klassisk Salesforce-installation](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. På sidan **installation** väljer du **skapa**  >  **Anpassa**  >  **leads**  >  **webb-till-lead**.

        ![Salesforce-klassiskt webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   De återstående stegen är desamma för båda Salesforce-upplevelserna.

1. På sidan **konfiguration av webb-till-lead** väljer du knappen **skapa webb-till-lead-formulär** .
1. Välj **skapa ett webb-till-lead-formulär** på **webb-till-plats-konfiguration**.

    ![Salesforce-konfiguration för webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. När du **skapar ett webb-till-lead-formulär** kontrollerar du att `Include reCAPTCHA in HTML` inställningen är avmarkerad och väljer **generera**.

    ![Salesforce skapa ett webb-till-lead-formulär](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. HTML-text visas. Sök efter texten "OID" och kopiera **värdet "OID"** från HTML-texten (endast texten mellan citat tecken) och spara den. Du kommer att klistra in det här värdet i fältet **organisations-ID** på publicerings portalen.

    ![Salesforce skapa ett webb-till-lead-formulär som visar HTML-värdet "OID"](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Välj **avslutad**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurera ditt erbjudande för att skicka leads till Salesforce

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du de här stegen.

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).

1. Välj ditt erbjudande och gå till fliken **erbjudande konfiguration** .

1. Under avsnittet **kund leads** väljer du **Anslut**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Kund ledare":::

1. I popup-fönstret **anslutnings information** väljer du **Salesforce** som **lead-mål** och klistrar in `oid` värdet från det webb-till-lead-formulär som du skapade i fältet **organisations-ID** .

    ![Popup-fönster för anslutnings information verifiera kontaktens e-ruta](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. Under **Kontakta e-postadress** anger du e-postadresser för personer i företaget som ska få e-postaviseringar när ett nytt lead tas emot. Du kan ange flera e-postmeddelanden genom att avgränsa dem med semikolon.

1. Välj **OK**.

Om du vill kontrol lera att du har anslutit till ett lead-mål väljer du **Verifiera**. Om det lyckas har du ett test lead i lead-målet.

>[!NOTE]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.
