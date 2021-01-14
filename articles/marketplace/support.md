---
title: Få support för den kommersiella Marketplace-portalen i Partner Center
description: Läs om dina support alternativ i Partner Center, inklusive hur du kan skicka en support förfrågan.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/14/2020
ms.openlocfilehash: 8979a94ee63ef50c0a3abacd5ab3515226200131
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183446"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Stöd för programmet för kommersiella marknads platser i Partner Center

Microsoft tillhandahåller stöd för en mängd olika produkter och tjänster. Att hitta rätt support team är viktigt för att säkerställa en lämplig och korrekt svars tid. Överväg följande scenarier, som kan hjälpa dig att dirigera din fråga till lämpligt team:

- Om du är utgivare och har en fråga från en kund ber du kunden att be om support med hjälp av support länkarna i [Azure Portal](https://portal.azure.com/).
- Om du är utgivare och har identifierat ett säkerhets problem med ett program som körs på Azure, se [så här loggar du in ett support ärende för en säkerhets händelse](/azure/security/fundamentals/event-support-ticket). Utgivare måste rapportera misstänkta säkerhets händelser, inklusive säkerhets incidenter och säkerhets risker för sina Azure Marketplace-program och tjänst erbjudanden, så snart som möjligt.
- Om du är utgivare och har en fråga som rör din app eller tjänst kan du läsa följande support alternativ.

## <a name="support-options-for-publishers"></a>Support alternativ för utgivare

1. Logga in på det [kommersiella Marketplace-programmet på Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) med ditt arbets konto (om du inte har gjort det ännu måste du [skapa ett partner Center-konto](partner-center-portal/create-account.md).

2. I den övre menyn på höger sida av sidan väljer du **support** ikonen.

3. Rutan **Hjälp och support** visas på sidans högra sida.

   ![List menyn stöd](./media/support/commercial-marketplace-support-pane.png)

    Eller gå till fönstret **Start sida** och välj **Hjälp och support**.

   ![Hjälp och support från start sidan](./media/support/homepage-help-support.png)

4. Välj **[dokumentation](../index.yml)** för att granska omfattande svar på frågor och resurser.

5. Välj **[Marketplace partner community-forum](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/bd-p/2222)** för att besvara dina frågor genom att använda kunskap om andra Microsoft-utgivare.

6. Välj **[ytterligare hjälp](https://aka.ms/marketplacepublishersupport)** för att öppna ett **nytt support ärende** .  

## <a name="how-to-open-a-support-ticket"></a>Så här öppnar du ett support ärende

Nu är du redo att öppna ett support ärende på skärmen **Hjälp och support** .

![Hjälp och support](./media/support/help-and-support.png)

>[!Note]
>Om du är inloggad i Partner Center får du bättre upplevelse med support.

**Alternativ 1:** Ange nyckelord som Marketplace, Azure app, SaaS-erbjudande, konto hantering, lead management, distributions problem, utbetalning och så vidare.

**Alternativ 2:** Bläddra bland ämnen – > väljer du **kategori** = kommersiell marketplace – > väljer lämpligt **ämne** och **underavsnitt**.

När du har hittat det avsnitt som du väljer väljer du **Granska lösningar**.

![Nästa steg](./media/support/next-step.png)

Följande alternativ blir tillgängliga:

- Om du vill välja ett annat ämne väljer du en annan ämnes länk under det **valda problemet**.
- Granska beskrivningen av det här problemet, om det är tillgängligt.  Det är den text som visas ovanför de **rekommenderade stegen**.
- Granska **rekommenderade steg**, om det är tillgängligt.
- Granska **rekommenderade dokument**, om det är tillgängligt.

![Rekommenderade lösningar](./media/support/recommended-solutions.png)

Om du inte kan hitta ditt svar i **rekommenderade lösningar** väljer du **Ange information om utfärdare**. Fyll i alla obligatoriska fält för att påskynda upplösnings processen och välj sedan **Skicka**.

>[!Note]
>Om du inte har loggat in på Partner Center och ämnet kräver autentisering, uppmanas du att logga in innan du kan fortsätta.  För offentliga ämnen krävs ingen autentisering.

## <a name="track-your-existing-support-requests"></a>Spåra dina befintliga support förfrågningar

Om du vill granska alla öppna och stängda biljetter går du till **handels Marketplace** i det vänstra navigerings fältet och väljer sedan **support**.

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
