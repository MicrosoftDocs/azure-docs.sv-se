---
title: Detaljerad konfiguration för en värdindelade testenhet i Azure Marketplace
description: Förklaring av konfigurationsstegen för en värdindelade testenhet på den kommersiella marknadsplatsen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 04/20/2021
ms.openlocfilehash: f11f1d5601a61bbd9b8729b478c278db8d3e73dc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812432"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Detaljerad konfiguration för värdindelade testenheter

I den här artikeln beskrivs hur du konfigurerar en värdindelade testenhet för Dynamics 365 for Customer Engagement eller Dynamics 365 for Operations.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Konfigurera för Dynamics 365 Customer Engagement

1. Logga in på [Partner Center](https://partner.microsoft.com/).
2. Om du inte kan komma åt länken ovan måste du skicka en begäran här [för](https://appsource.microsoft.com/partners/list-an-app) att publicera ditt program. När vi har granskat begäran beviljas du åtkomst för att starta publiceringsprocessen.
3. Hitta ett befintligt **erbjudande för Dynamics 365 for Customer Engagement** eller skapa ett nytt erbjudande för Dynamics **365 for Customer Engagement.**
4. Markera kryssrutan **Aktivera en provkörning** och välj en typ av **testenhet** (se punkt nedan) och välj sedan **Spara.**

    [![Markera kryssrutan "Aktivera en provkörning".](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Typ av provkörning** – Välj **Microsoft Hosted (Dynamics 365 for Customer Engagement & PowerApps).** Detta anger att Microsoft kommer att vara värd för och underhålla tjänsten som utför etableringen och avetablering av testkörningsanvändaren.

5. Ge Microsoft AppSource behörighet att etablera och avetablera testköra användare i din klientorganisation med hjälp av [dessa instruktioner.](./test-drive-azure-subscription-setup.md) I det här steget ska du generera **Azure AD App-ID** **och Azure AD App nyckelvärden** som anges nedan.
6. Fyll i de här fälten på **sidan Teknisk konfiguration för testenhet.**

    [![Sidan teknisk konfiguration för testenheten.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximalt antal samtidiga** testenheter – Antalet samtidiga användare som kan ha en aktiv testenhet som körs samtidigt. Varje användare kommer att använda en Dynamics-licens när testkörningen är aktiv, så se till att du har minst så många Dynamics-licenser tillgängliga för testkörare. Vi rekommenderar 3 till 5.
    - **Varaktighet för testenhet** – Antalet timmar som användarens provkörning ska vara aktiv. När tiden har gått ut avetablerats användaren från din klientorganisation. Vi rekommenderar 2–24 timmar beroende på appens komplexitet. Användaren kan alltid begära en till provkörning om tiden tar slut och vill komma åt testenheten igen.
    - **Instans-URL** – DEN URL som testkörningsanvändaren skickas till när de startar provkörningen. Detta är vanligtvis URL:en för din Dynamics 365-instans där din app och exempeldata är installerade. Exempelvärde: `https://testdrive.crm.dynamics.com` .
    - **Instanswebb-API-URL** – Webb-API-URL:en för din Dynamics 365-instans. Hämta det här värdet genom att logga in på din Microsoft Dynamics 365-instans och gå till **Ange** webb-API för  >    >  **anpassningsresurserinstans** och  >   kopiera adressen (URL). Exempelvärde:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Ett exempel på instanswebb-API.":::

    - **Rollnamn** – Namnet på den anpassade Dynamics 365-säkerhetsroll som du skapade för testkörningen eller så kan du använda en befintlig roll. En ny roll bör ha minsta nödvändiga behörigheter tillagt i rollen för att logga in på en Customer Engagement-instans. Se Minsta [behörigheter som krävs för att logga in på Microsoft Dynamics 365.](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365) Det här är den roll som kommer att tilldelas till användare under testkörningen. Exempelvärde: `testdriverole` .
    
        > [!IMPORTANT]
        > Kontrollera att säkerhetsgruppskontrollen inte har lagts till. Detta gör att användaren kan synkroniseras med Customer Engagement-instansen.

    - **Azure Active Directory klientorganisations-ID** – ID för Azure-klientorganisationen för din Dynamics 365-instans. Om du vill hämta det här värdet loggar du Azure Portal och går **till Azure Active Directory**  >  **egenskaper** och kopierar katalog-ID:t. Exempelvärde: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory klientnamn** – Namnet på Azure-klientorganisationen för din Dynamics 365-instans. Använd formatet `<tenantname>.onmicrosoft.com`. Exempelvärde: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory program-ID** – ID:t för den Azure Active Directory (AD)-app som du skapade i steg 5. Exempelvärde: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory programklienthemlighet** – Hemligt för Azure AD-appen som skapades i steg 5. Exempelvärde: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Ange information om marketplace-listan. Välj **Språk** för att se ytterligare obligatoriska fält.

    [![Sidan med Marketplace-listinformation.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beskrivning** – En översikt över din provkörning. Den här texten visas för användaren medan testenheten etableras. Det här fältet stöder HTML om du vill ange formaterat innehåll.
    - **Användarhandbok** – En PDF-användarhandbok som hjälper testkör användarna att förstå hur de ska använda din app.
    - **Demovideo på provkörning** – En video som visar din app (valfritt).

## <a name="configure-for-dynamics-365-operations"></a>Konfigurera för Dynamics 365-åtgärder

2. Om du inte kan komma åt länken ovan måste du skicka en begäran här [för](https://appsource.microsoft.com/partners/list-an-app) att publicera ditt program. När vi har granskat begäran beviljas du åtkomst för att starta publiceringsprocessen.
3. Hitta ett befintligt **erbjudande för Dynamics 365 for Operations** eller skapa ett nytt erbjudande för Dynamics **365 for Operations.**
4. Markera kryssrutan **Aktivera en provkörning** och välj en typ av **testenhet** (se punkt nedan) och välj sedan **Spara.**

    [![Markera kryssrutan "Aktivera en provkörning".](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Typ av testenhet** – Välj **alternativet Dynamics 365 for Operations.** Det innebär att Microsoft kommer att vara värd för och underhålla den tjänst som utför etableringen och avetablering av testkörningen.

5. Ge Microsoft AppSource behörighet att etablera och avetablera testköra användare i din klientorganisation med hjälp [av dessa instruktioner.](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) I det här steget ska du generera **Azure AD App-ID** och **Azure AD App nyckelvärden** som anges nedan.
6. Fyll i de här fälten på **sidan Teknisk konfiguration för testenheten.**

    [![Sidan teknisk konfiguration för Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximalt antal samtidiga** testenheter – Antalet samtidiga användare som kan ha en aktiv testenhet som körs samtidigt. Varje användare kommer att använda en Dynamics-licens när deras testenhet är aktiv, så se till att du har minst så många Dynamics-licenser tillgängliga för testkörare. Vi rekommenderar 3 till 5.
    - **Varaktighet för testenhet** – Antalet timmar som användarens provkörning ska vara aktiv. När tiden har gått ut avetablerats användaren från din klientorganisation. Vi rekommenderar 2–24 timmar beroende på appens komplexitet. Användaren kan alltid begära en till provkörning om tiden tar slut och vill komma åt testenheten igen.
    - **Instans-URL** – DEN URL som testkörningsanvändaren skickas till när de startar provkörningen. Detta är vanligtvis URL:en för din Dynamics 365-instans där din app och exempeldata är installerade. Exempelvärde: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory klientorganisations-ID** – ID för Azure-klienten för din Dynamics 365-instans. Om du vill hämta det här värdet loggar du Azure Portal och går **till Azure Active Directory**  >  **egenskaper** och kopierar katalog-ID:t. Exempelvärde: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory klientnamn** – Namnet på Azure-klientorganisationen för din Dynamics 365-instans. Använd formatet `<tenantname>.onmicrosoft.com`. Exempelvärde: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory program-ID** – ID:t för den Azure Active Directory (AD)-app som du skapade i steg 5. Exempelvärde: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory programklienthemlighet** – Hemligt för Azure AD-appen som skapades i steg 5. Exempelvärde: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Juridisk enhet för utvärdering** – Ange en juridisk enhet för att tilldela en utvärderingsanvändare. Du kan skapa en ny i Skapa [eller ändra en juridisk enhet](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity).
    - **Rollnamn** – AOT-namnet (programobjektträdet) för den anpassade Dynamics 365-säkerhetsroll som du skapade för testenheten. Det här är den roll som kommer att tilldelas till användare under testkörningen.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Sidan säkerhetskonfiguration.":::

7. Ange information om marketplace-listan. Välj **Språk** för att se ytterligare obligatoriska fält.

    [![Sidan med Marketplace-listinformation.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beskrivning** – En översikt över din provkörning. Den här texten visas för användaren medan testenheten etableras. Det här fältet stöder HTML om du vill ange formaterat innehåll.
    - **Användarhandbok** – En PDF-användarhandbok som hjälper testkör användarna att förstå hur de ska använda din app.
    - **Demovideo på provkörning** – En video som visar din app (valfritt).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->