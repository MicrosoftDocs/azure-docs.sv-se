---
title: Ledande hantering i Marketo – Microsofts kommersiella marknads platser
description: Lär dig hur du använder ett Marketo CRM-system för att hantera leads från Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: cbd12481312d310add15bf3d41e21e9c416f1c39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491083"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Använd Marketo för att hantera affärs marknads platser

Den här artikeln beskriver hur du konfigurerar ett Marketo CRM-system för att bearbeta försäljnings leads från dina erbjudanden i Microsoft AppSource och Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Konfigurera ditt Marketo CRM-system

1. Logga in på Marketo.

1. Välj **Design Studio**.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Välj **nytt formulär**.

    ![Marketo-nytt formulär](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Fyll i de obligatoriska fälten i dialog rutan **nytt formulär** och välj sedan **skapa**.

    ![Skapa nytt formulär på marknaden](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  På sidan **fält information** väljer du **Slutför**.

    ![Slutför formulär för Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Godkänn och Stäng.

1. På fliken **MarketplaceLeadBackend** väljer du **bädda in kod**. 

    ![Marketo Inbäddnings kod](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo Inbäddnings kod visar kod som liknar följande exempel.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Kopiera värdena för följande fält som visas i Inbäddnings kod formuläret. Du använder dessa värden för att konfigurera erbjudandet att ta emot leads i nästa steg. Använd nästa exempel som en guide för att hämta de ID: n du behöver från Marketo embed-kod exemplet.

    - Server-ID = **ys12**
    - Munchkin-ID = **123-PQR-789**
    - Formulär-ID = **1179**

    Ett annat sätt att räkna ut dessa värden:

    - Server-ID finns i URL: en för din Marketo-instans, till exempel `serverID.marketo.com` .
    - Hämta din prenumerations Munchkin-ID genom att gå till din **Administratörs**  >  **Munchkin** -meny i fältet **Munchkin konto-ID** eller från den första delen av Marketo REST API värd under domän: `https://{Munchkin ID}.mktorest.com` .
    - Formulär-ID är ID för Inbäddnings kod formuläret som du skapade i steg 7 för att dirigera leads från Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurera ditt erbjudande för att skicka leads till Marketo

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du de här stegen. 

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).

1. Välj ditt erbjudande och gå till fliken **erbjudande konfiguration** .

1. Under avsnittet **kund leads** väljer du **Anslut**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Kund ledare":::

1. Välj **Marketo** för **lead-målet** i popup-fönstret **anslutnings information** .

    ![Välj ett mål för lead](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Ange **Server-ID**, **Munchkin-konto-ID** och **formulär-ID**.

    > [!NOTE]
    > Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet. 

1. Under **Kontakta e-postadress** anger du e-postadresser för personer i företaget som ska få e-postaviseringar när ett nytt lead tas emot. Du kan ange flera e-postadresser genom att avgränsa dem med semikolon.

1. Välj **OK**.

   Om du vill kontrol lera att du har anslutit till ett lead-mål väljer du **Verifiera**. Om det lyckas har du ett test lead i lead-målet.

   ![Anslutnings information, popup-fönster](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
