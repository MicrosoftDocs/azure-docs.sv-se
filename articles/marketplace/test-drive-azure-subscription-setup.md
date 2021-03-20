---
title: Konfigurera en Azure Marketplace-prenumeration för värdbaserade test enheter
description: Förklarar hur du konfigurerar en Azure Marketplace-prenumeration för Dynamics 365 för kund engagemang och Dynamics 365 för drift test enheter
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 03/16/2020
ms.openlocfilehash: a7f12891bf394e54ee46c60598536faed1731202
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600891"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Konfigurera en Azure Marketplace-prenumeration för värdbaserade test enheter

Den här artikeln förklarar hur du konfigurerar en Azure Marketplace-prenumeration och **dynamics 365 för kund engagemang** eller **Dynamics 365 för drifts** miljöer för test enheter.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Konfigurera för Dynamics 365 för kund engagemang

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett administratörs konto.
2. Kontrol lera att du är i den klient som är kopplad till din Dynamics 365-test enhets instans genom att hovra över konto ikonen i det övre högra hörnet. Om du inte befinner dig i rätt klient väljer du konto ikonen för att växla till rätt klient organisation.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Välja rätt klient.":::

3. Verifiera att Dynamics 365-prenumerationen för **kund engagemang** är tillgänglig.

    [![Kontrollerar plan licensen.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Skapa en Azure Active Directory-app (AD) i Azure. AppSource kommer att använda den här appen för att etablera och avetablera användare av test enheten i din klient organisation.
    1. I filter fönstret väljer du **Azure Active Directory**.
    2. Välj **Appregistreringar**.

        [![Välja app-registreringar.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Välj **ny registrering**.
    4. Ange ett lämpligt program namn.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrerar ett program.":::

    5. Under konto typer som stöds väljer du **konto i valfri organisations katalog och personliga Microsoft-konton**.
    6. Välj **skapa** och vänta tills appen har skapats.
    7. När appen har skapats noterar du det **program-ID** som visas på översikts skärmen. Du behöver det här värdet senare när du konfigurerar test enheten.
    8. Under **hantera program** väljer du **API-behörigheter**.
    9. Välj **Lägg till en behörighet** och sedan **Microsoft Graph-API**.
    10. Välj kategorin **program** behörighet och sedan **User. readwrite. all**, **Directory. Read. all** och **Directory. readwrite. all** behörighet.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Ange program behörigheter.":::

    11. När du har lagt till behörigheten väljer du **bevilja administrativt godkännande för Microsoft**.
    12. Välj **Ja** i meddelande aviseringen.

        [![Visar program behörigheter som har beviljats.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. Så här skapar du en hemlighet för Azure AD App:
        1. Välj **certifikat och hemligheter** från **hantera program**.
        2. Under klient hemligheter väljer du **ny klient hemlighet**.
        3. Ange en beskrivning, till exempel *Testkör*, och välj lämplig varaktighet. Test enheten avbryts när den här nyckeln går ut, då du måste generera och ange AppSource en ny nyckel.
        4. Välj **Lägg till** för att generera Azure-appens hemlighet. Kopiera det här värdet eftersom det kommer att döljas så snart du lave det här bladet. Du behöver det här värdet senare när du konfigurerar test enheten.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Lägga till en klient hemlighet.":::

5. Lägg till rollen tjänstens huvud namn i programmet så att Azure AD-appen kan ta bort användare från din Azure-klient.
    1. Öppna en PowerShell-kommandotolk på administratörs nivå.
    2. Install-Module MSOnline (kör detta kommando om MSOnline inte är installerat).
    3. Connect-MsolService (ett popup-fönster visas. Logga in med den nya organisations klienten).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Logga in på ditt konto.":::

6. Lägg till den tidigare skapade Azure-appen som en program användare till din test enhet CRM-instans.
    1. Lägg till en ny användare i **Azure Active Directory**. Endast **namn** -och **användar namns** värden (som tillhör samma klient) krävs för att skapa den här användaren, lämna övriga fält som standard. Kopiera värdet username.
    2. Logga in på **CRM-instansen** och välj **Ange**  >  **säkerhets**  >  **användare**.
    3. Ändra vyn till **program användare**.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Ange konto information för en användare.":::

    4. Lägg till en ny användare (se till att formuläret är för program användare).
    5. Ange samma användar namn i fälten **primär e-post** och **användar namn** . Lägg till **Azure ApplicationId** i **program-ID**.
    6. Ge alla **fullständiga namn**.
    7. Välj **Spara**.
    8. Välj **hantera roller**.
    9. Tilldela en anpassad säkerhets roll eller OOB-säkerhetsroll som innehåller behörigheterna läsa, skriva och tilldela roll, till exempel *system administratör*.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Välja roll behörighet.":::

    10. Aktivera också **åtgärden för en annan användares** behörighet.
    11. Tilldela program användaren den anpassade säkerhets roll som du skapade för test enheten.

## <a name="set-up-for-dynamics-365-for-operations"></a>Konfigurera för Dynamics 365 för åtgärder

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett administratörs konto.
2. Kontrol lera att du är i den klient som är kopplad till din Dynamics 365-test enhets instans genom att hovra över konto ikonen i det övre högra hörnet. Om du inte befinner dig i rätt klient väljer du konto ikonen för att växla till rätt klient organisation.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Välja rätt klient.":::

3. Skapa ett Azure AD App i Azure. AppSource kommer att använda den här appen för att etablera och avetablera användare av test enheten i din klient organisation.
    1. I filter fönstret väljer du **Azure Active Directory**.
    2. Välj **Appregistreringar**.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Välja en app-registrering.":::

    3. Välj **ny registrering**.
    4. Ange ett lämpligt program namn.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrerar ett program.":::

    5. Under konto typer som stöds väljer du **konto i valfri organisations katalog och personliga Microsoft-konton**.
    6. Välj **skapa** och vänta tills appen har skapats.
    7. När appen har skapats noterar du det **program-ID** som visas på översikts skärmen. Du behöver det här värdet senare när du konfigurerar test enheten.
    8. Under **hantera program** väljer du **API-behörigheter**.
    9. Välj **Lägg till en behörighet** och sedan **Microsoft Graph-API**.
    10. Välj kategorin **program** behörighet och sedan **katalogen. Read. all** och **Directory. readwrite. all** behörighet.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Anger program behörigheter.":::

    11. Välj **Lägg till behörighet**.
    12. När du har lagt till behörigheten väljer du **bevilja administrativt godkännande för Microsoft**.
    13. Välj **Ja** i meddelande aviseringen.

        [![Visar program behörigheter som har beviljats.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Så här skapar du en hemlighet för Azure AD App:
        1. Välj **certifikat och hemligheter** från **hantera program**.
        2. Under klient hemligheter väljer du **ny klient hemlighet**.
        3. Ange en beskrivning, till exempel *Testkör*, och välj lämplig varaktighet. Test enheten avbryts när den här nyckeln går ut, då du måste generera och ange AppSource en ny nyckel.
        4. Välj **Lägg till** för att generera Azure-appens hemlighet. Kopiera det här värdet eftersom det kommer att döljas så snart du lave det här bladet. Du behöver det här värdet senare när du konfigurerar test enheten.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Lägga till en klient hemlighet.":::

4. Lägg till rollen tjänstens huvud namn i programmet så att Azure AD-appen kan ta bort användare från din Azure-klient.
    1. Öppna en PowerShell-kommandotolk på administratörs nivå.
    2. Install-Module MSOnline (kör detta kommando om MSOnline inte är installerat).
    3. Connect-MsolService (ett popup-fönster visas. Logga in med den nya organisations klienten).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Logga in på ditt konto.":::

5. Lägg nu till appen ovan i **Dynamics 365 för åtgärder** för att göra det möjligt för appen att hantera användare.
    1. Hitta din **Dynamics 365 for Operations** -instans.
    2. Klicka på menyn med tre rader (Hamburger) i det övre vänstra hörnet.
    3. Välj **system administration**.
    4. Välj **Azure Active Directory program**.
    5. Välj **+ Ny**.
    6. Ange **klient-ID för den Azure AD-App** som ska utföra åtgärder på uppdrag.

    > [!NOTE]
    > Det användar-ID för vars räkning åtgärderna utförs (vanligt vis system administratören för instansen eller en användare som har behörighet att lägga till andra användare).

    [![Det användar-ID för vars räkning åtgärderna utförs (vanligt vis system administratören för instansen eller en användare som har behörighet att lägga till andra användare).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
