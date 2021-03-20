---
title: Få support för programmet för kommersiella marknads platser i Partner Center
description: Lär dig mer om dina support alternativ för programmet för kommersiella marknads platser i Partner Center, inklusive hur du kan skicka en support förfrågan.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: a1726b29c153bf680d29fe821ac34aa958064335
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879249"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Stöd för programmet för kommersiella marknads platser i Partner Center

Microsoft tillhandahåller stöd för en mängd olika produkter och tjänster. Att hitta rätt support team är viktigt för att säkerställa en lämplig och korrekt svars tid. Överväg följande scenarier, som kan hjälpa dig att dirigera din fråga till lämpligt team:

- Om du är utgivare och har en fråga från en kund ber du kunden att be om support med hjälp av support länkarna i [Azure Portal](https://portal.azure.com/).
- Om du är utgivare och har identifierat ett säkerhets problem med ett program som körs på Azure, se [så här loggar du in ett support ärende för en säkerhets händelse](../security/fundamentals/event-support-ticket.md). Utgivare måste rapportera misstänkta säkerhets händelser, inklusive säkerhets incidenter och säkerhets risker för sina Azure Marketplace-program och tjänst erbjudanden, så snart som möjligt.
- Om du är utgivare och har en fråga som rör din app eller tjänst kan du läsa följande support alternativ.

## <a name="get-help-or-open-a-support-ticket"></a>Få hjälp eller öppna ett support ärende

1. Logga in med ditt arbets konto. Om du ännu inte har gjort det måste du [skapa ett partner Center-konto](partner-center-portal/create-account.md).

1. Välj ikonen **support** på menyn längst upp till höger på sidan. Rutan **Hjälp och support** visas till höger på sidan.

1. Om du behöver hjälp med den kommersiella Marketplace väljer du **kommersiell marknads** plats.

   ![List menyn stöd](./media/support/commercial-marketplace-support-pane.png)

1. I rutan **problem Sammanfattning** anger du en kort beskrivning av problemet.

1. Gör något av följande i rutan **typ av problem** :

    - **Alternativ 1**: Ange nyckelord som till exempel: Marketplace, Azure app, SaaS-erbjudande, konto hantering, lead-hantering, distributions problem, utbetalning eller medförsäljning för erbjudandet. Välj sedan en problem typ från den rekommenderade listan som visas.

    - **Alternativ 2**: Välj **Bläddra bland ämnen** i listan **kategori** och välj sedan **kommersiell marknads** plats. Välj sedan lämpligt **ämne** och **underavsnitt**.

1. När du har hittat det avsnitt som du väljer väljer du **Granska lösningar**.

    ![Nästa steg](./media/support/next-step.png)

Följande alternativ visas:

- Klicka på **Välj ett annat ärende** om du vill välja ett annat ämne.
- Du kan lösa problemet genom att granska de rekommenderade stegen och dokumenten, om det är tillgängligt.

    ![Rekommenderade lösningar](./media/support/recommended-solutions.png)

Om du inte hittar ditt svar i hjälp avsnittet väljer du **Ange information om utfärdare**. Fyll i alla obligatoriska fält för att påskynda upplösnings processen och välj sedan **Skicka**.

>[!Note]
>Om du inte har loggat in på Partner Center kan du behöva logga in innan du kan skapa en biljett.

## <a name="track-your-existing-support-requests"></a>Spåra dina befintliga support förfrågningar

Om du vill granska dina öppna och stängda biljetter väljer du stöd för **kommersiell Marketplace** på den vänstra navigerings menyn  >  .

## <a name="record-issue-details-with-a-har-file"></a>Registrera ärende information med en-fil

För att hjälpa Support för agenter att felsöka ditt problem bör du överväga att bifoga en fil med HTTP-arkiv (har fil format) till ditt support ärende. Har filer är loggar med nätverks begär anden i en webbläsare.

> [!WARNING]
> Har filer kan registrera känsliga data om ditt partner Center-konto.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge och Google Chrome

Så här genererar du en-fil med **Microsoft Edge** eller **Google Chrome**:

1. Gå till webb sidan där du har drabbats av problemet.
2. I det övre högra hörnet i fönstret väljer du ikonen med tre punkter och sedan **fler verktyg** för  >  **utvecklarverktyg**. Du kan trycka på F12 som en genväg.
3. I fönstret utvecklarverktyg väljer du fliken **nätverk** .
4. Välj **stoppa registrering av nätverks logg** och **Rensa** för att ta bort befintliga loggar. Inspelnings ikonen blir grå.

    ![Ta bort befintliga loggar i Microsoft Edge eller Google Chrome](media/support/chromium-stop-clear-session.png)

5. Välj **Registrera nätverks logg** för att starta inspelningen. När du startar inspelningen blir inspelnings ikonen röd.

    ![Starta inspelningen i Microsoft Edge eller Google Chrome](media/support/chromium-start-session.png)

6. Återskapa problemet som du vill felsöka.
7. När du har återskapat problemet väljer du **stoppa registrering av nätverks logg**.
8. Välj **Exportera** har, markerat med en nedåtpil-ikon och spara filen.

    ![Så här exporterar du en-fil i Microsoft Edge eller Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Så här genererar du en-fil med **Mozilla Firefox**:

1. Gå till webb sidan där du har drabbats av problemet.
1. I det övre högra hörnet i fönstret väljer du ikonen med tre punkter och sedan **webb utvecklarens**  >  **Växla verktyg**. Du kan trycka på F12 som en genväg.
1. Välj fliken **nätverk** och välj sedan **Rensa** för att ta bort befintliga loggar.

    ![Så här tar du bort befintliga loggar i Mozilla Firefox](media/support/firefox-clear-session.png)

1. Återskapa problemet som du vill felsöka.
1. När du har återskapat problemet väljer du har **export/import**  >  **Spara alla som** har.

    ![Så här exporterar du en-fil i Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Så här genererar du en-fil med **Safari**:

1. Aktivera utvecklarverktyg i Safari: Välj inställningar för **Safari**  >  . Gå till fliken **Avancerat** och välj sedan **Visa utveckla meny i meny raden**.
1. Gå till webb sidan där du har drabbats av problemet.
1. Välj **utveckla** och välj sedan **Visa webb kontroll**.
1. Välj fliken **nätverk** och välj sedan **Rensa nätverks objekt** för att ta bort befintliga loggar.

    ![Ta bort befintliga loggar i Safari](media/support/safari-clear-session.png)

1. Återskapa problemet som du vill felsöka.
1. När du har återskapat problemet väljer du **Exportera** och sparar filen.

    ![Så här exporterar du en-fil i Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](partner-center-portal/update-existing-offer.md)