---
title: Detaljerad konfiguration för en värdbaserad testen het på Azure Marketplace
description: Beskrivning av konfigurations steg för en värdbaserad testen het på den kommersiella Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 88779f67a2fa9b18f0177a1459b32c672343bb57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462948"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Detaljerad konfiguration för värdbaserade test enheter

I den här artikeln beskrivs hur du konfigurerar en värdbaserad test enhet för Dynamics 365 för kund engagemang eller Dynamics 365 for Operations.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Konfigurera för Dynamics 365 kund engagemang

1. Logga in på [partner Center](https://partner.microsoft.com/).
2. Om du inte kan komma åt länken ovan måste du skicka en begäran [här](https://appsource.microsoft.com/partners/list-an-app) för att publicera programmet. När vi granskar begäran beviljas du åtkomst för att starta publicerings processen.
3. Hitta en befintlig **dynamics 365 för kund engagemang** eller skapa ett nytt **Dynamics 365-erbjudande för kund engagemang** .
4. Markera kryss rutan **Aktivera en testenhet** och välj en **typ av test enhet** (se punkt nedan) och välj sedan **Spara**.

    [![Markera kryss rutan Aktivera en testen het.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Typ av test enhet** – Välj **Microsoft värdbaserad (Dynamics 365 för kund engagemang & PowerApps)**. Detta anger att Microsoft ska vara värd för och underhålla den tjänst som utför den användar etablering och avetableringen av test enheten.

5. Bevilja Microsoft AppSource behörighet att etablera och avetablera användare av test enheter i din klient med hjälp av [dessa instruktioner](./test-drive-azure-subscription-setup.md). I det här steget ska du generera **Azure AD App-ID: t** och **Azure AD App nyckel** värden som anges nedan.
6. Fyll i fälten på sidan **teknisk konfiguration för test enhet** .

    [![Sidan teknisk konfiguration för test enhet.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximalt antal samtidiga test enheter** – antalet samtidiga användare som kan ha en aktiv testen het på samma gång. Varje användare kommer att använda en Dynamics-licens medan deras test enhet är aktiv, så se till att du har minst följande många Dynamics-licenser tillgängliga för användare av test enheter. Vi rekommenderar 3 till 5.
    - **Test enhetens varaktighet** – antalet timmar som användarens testen het ska vara aktiv. När tiden har gått ut kommer användaren att avetableras från din klient. Vi rekommenderar 2-24 timmar beroende på appens komplexitet. Användaren kan alltid begära en annan testen het om de kör tids gränsen och vill komma åt test enheten igen.
    - **Instans-URL** – URL: en som användaren kommer att skickas till när de startar test enheten. Detta är vanligt vis URL: en för din Dynamics 365-instans där din app och exempel data är installerade. Exempel värde: `https://testdrive.crm.dynamics.com` .
    - **Instans webb-API-URL** – URL för webb-API för din Dynamics 365-instans. Hämta det här värdet genom att logga in på Microsoft Dynamics 365-instansen och navigera för att **ställa in**  >  **anpassnings**  >  **resurser för utvecklare**  >  **-webb-API** och kopiera adressen (URL). Exempelvärde:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Ett exempel på instans-webb-API.":::

    - **Rollnamn** – namnet på den anpassade säkerhets rollen Dynamics 365 som du skapade för test enheten eller så kan du använda en befintlig roll. En ny roll måste ha minst de behörigheter som krävs för att logga in på en kund engagemang instans. Se de [minsta privilegier som krävs för att logga in på Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Detta är den roll som ska tilldelas användare under deras test enhet. Exempel värde: `testdriverole` .
    
        > [!IMPORTANT]
        > Se till att säkerhets grupps kontrollen inte har lagts till. Detta gör att användaren kan synkroniseras med kund åtagande instansen.

    - **Azure Active Directory klient-ID** – ID för Azure-klienten för din Dynamics 365-instans. Om du vill hämta det här värdet loggar du in på Azure Portal och navigerar till **Azure Active Directory**  >  **Egenskaper** och kopierar katalog-ID: t. Exempel värde: 172f988bf-86f1-41AF-91ab-2d7cd01112341.
    - **Azure Active Directory klient namn** – namnet på Azure-klienten för din Dynamics 365-instans. Använd formatet `<tenantname>.onmicrosoft.com`. Exempel värde: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory program-ID** – ID: t för den Azure Active Directory (AD) som du skapade i steg 5. Exempel värde: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory program klient hemlighet** – hemlighet för Azure AD-appen som skapades i steg 5. Exempel värde: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Ange information om Marketplace-listan. Välj **språk** för att se ytterligare obligatoriska fält.

    [![Informations sidan för Marketplace-listan.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beskrivning** – en översikt över test enheten. Den här texten visas för användaren medan test enheten håller på att tillhandahållas. Det här fältet stöder HTML om du vill tillhandahålla formaterat innehåll.
    - **Användar handbok** – en hand bok för PDF-användare som hjälper till att testa användarna förstå hur de använder din app.
    - **Test Drive demo video** – en video som visar din app (valfritt).

## <a name="configure-for-dynamics-365-operations"></a>Konfigurera för Dynamics 365-åtgärder

2. Om du inte kan komma åt länken ovan måste du skicka en begäran [här](https://appsource.microsoft.com/partners/list-an-app) för att publicera programmet. När vi granskar begäran beviljas du åtkomst för att starta publicerings processen.
3. Hitta en befintlig **dynamics 365 för drift** erbjudande eller skapa en ny **Dynamics 365 for Operations** -erbjudande.
4. Markera kryss rutan **Aktivera en testenhet** och välj en **typ av test enhet** (se punkt nedan) och välj sedan **Spara**.

    [![Markera kryss rutan Aktivera en testen het.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Typ av test enhet** – Välj alternativet **Dynamics 365 för åtgärder** . Det innebär att Microsoft kommer att vara värd för och underhålla den tjänst som utför den användar etablering och avetableringen av test enheten.

5. Bevilja Microsoft AppSource behörighet att etablera och avetablera användare av test enheter i din klient med hjälp av [dessa instruktioner](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md). I det här steget ska du generera **Azure AD App-ID: t** och **Azure AD App nyckel** värden som anges nedan.
6. Fyll i fälten på sidan **teknisk konfiguration för test enhet** .

    [![Sidan teknisk konfiguration för Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximalt antal samtidiga test enheter** – antalet samtidiga användare som kan ha en aktiv testen het på samma gång. Varje användare kommer att använda en Dynamics-licens medan deras test enhet är aktiv, så se till att du har minst följande många Dynamics-licenser tillgängliga för användare av test enheter. Vi rekommenderar 3 till 5.
    - **Test enhetens varaktighet** – antalet timmar som användarens testen het ska vara aktiv. När tiden har gått ut kommer användaren att avetableras från din klient. Vi rekommenderar 2-24 timmar beroende på appens komplexitet. Användaren kan alltid begära en annan testen het om de kör tids gränsen och vill komma åt test enheten igen.
    - **Instans-URL** – URL: en som användaren kommer att skickas till när de startar test enheten. Detta är vanligt vis URL: en för din Dynamics 365-instans där din app och exempel data är installerade. Exempel värde: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory klient-ID** – ID för Azure-klienten för din Dynamics 365-instans. Om du vill hämta det här värdet loggar du in på Azure Portal och navigerar till **Azure Active Directory**  >  **Egenskaper** och kopierar katalog-ID: t. Exempel värde: 172f988bf-86f1-41AF-91ab-2d7cd01112341.
    - **Azure Active Directory klient namn** – namnet på Azure-klienten för din Dynamics 365-instans. Använd formatet `<tenantname>.onmicrosoft.com`. Exempel värde: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory program-ID** – ID: t för den Azure Active Directory (AD) som du skapade i steg 5. Exempel värde: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory program klient hemlighet** – hemlighet för Azure AD-appen som skapades i steg 5. Exempel värde: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Juridisk enhet för utvärdering** – ange en juridisk enhet för att tilldela en utvärderings användare. Du kan skapa en ny i [skapa eller ändra en juridisk person](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity).
    - **Roll namn** – AOT-namn (program objekt träd) för den anpassade Dynamics 365-säkerhetsrollen som du skapade för test enheten. Detta är den roll som ska tilldelas användare under deras test enhet.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Sidan säkerhets konfiguration.":::

7. Ange information om Marketplace-listan. Välj **språk** för att se ytterligare obligatoriska fält.

    [![Informations sidan för Marketplace-listan.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beskrivning** – en översikt över test enheten. Den här texten visas för användaren medan test enheten håller på att tillhandahållas. Det här fältet stöder HTML om du vill tillhandahålla formaterat innehåll.
    - **Användar handbok** – en hand bok för PDF-användare som hjälper till att testa användarna förstå hur de använder din app.
    - **Test Drive demo video** – en video som visar din app (valfritt).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->