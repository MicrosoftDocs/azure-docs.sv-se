---
title: Lead-hantering för Dynamics 365 kund engagemang – Microsoft Commercial Marketplace
description: Lär dig hur du konfigurerar Dynamics 365 kund engagemang för att hantera leads från Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 067a30d8f16b06b1b29ad494363d180886d1ab8b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560088"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Konfigurera lead-hantering för Dynamics 365 kund engagemang

Den här artikeln beskriver hur du konfigurerar Dynamics 365 kund engagemang (tidigare namngiven Dynamics CRM Online). Läs mer om ändringen i [Konfigurera serverbaserad autentisering med kund engagemang och SharePoint Online](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) för att bearbeta försäljnings leads från ditt kommersiella Marketplace-erbjudande.

>[!NOTE]
>De här anvisningarna är specifika för den Microsoft-värdbaserade moln miljön för Dynamics 365-kund engagemang. Anslutning direkt till en lokal Dynamics-miljö stöds inte för närvarande. Det finns andra alternativ för att ta emot leads, till exempel konfigurera en [https-slutpunkt](./commercial-marketplace-lead-management-instructions-https.md) eller en Azure- [tabell](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Förutsättningar

Följande användar behörigheter krävs för att slutföra stegen i den här artikeln:

* Administratörs behörighet på din Dynamics 365 kund engagemang-instans för att kunna installera en lösning.
* Klient administratörs rättigheter för att skapa ett nytt tjänst konto för den lead-tjänst som används för att skicka leads från kommersiella Marketplace-erbjudanden.
* Åtkomst till administrations portalen.
* Åtkomst till Azure Portal.

## <a name="install-the-solution"></a>Installera lösningen

1. Ladda ned [lösningen för Microsoft Marketplace bly-skrivaren](https://referralsflowpackages.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)och spara den lokalt på datorn.

1. Öppna Dynamics 365 kund engagemang genom att gå till URL: en för din Dynamics-instans, till exempel `https://tenant.crm.dynamics.com` .

1. Välj kugg hjuls ikonen i det översta fältet och välj sedan **Avancerade inställningar**.

    ![Meny alternativ för Dynamics 365 avancerade inställningar](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. På sidan **Inställningar** öppnar du menyn **Inställningar** i det översta fältet och väljer **lösningar**.

    >[!NOTE]
    >Om du inte ser alternativen på följande skärm har du inte de behörigheter som krävs för att fortsätta. Kontakta en administratör på din Dynamics 365 kund engagemang instans.

    ![Alternativ för Dynamics 365-lösningar](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. På sidan **lösningar** väljer du **Importera** och går till den plats där du sparade den Microsoft Marketplace lösningen för **bly skrivare** som du laddade ned i steg 1.

    ![Knappen Importera](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Slutför importen av lösningen genom att följa guiden Importera lösning.

## <a name="configure-user-permissions"></a>Konfigurera användar behörigheter

Om du vill skriva leads till din Dynamics 365 kund engagemang-instans måste du dela ett tjänst konto med Microsoft och konfigurera behörigheter för kontot.

Använd följande steg för att skapa tjänst kontot och tilldela behörigheter. Du kan använda Azure Active Directory eller Office 365.

>[!NOTE]
>Hoppa till motsvarande instruktioner baserat på det autentiseringsalternativ du väljer. Se [Azure Active Directory](#azure-active-directory) eller [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Vi rekommenderar det här alternativet eftersom du aldrig behöver uppdatera ditt användar namn eller lösen ord för att få leads. Om du vill använda alternativet Azure Active Directory anger du app-ID, program nyckel och katalog-ID från ditt Active Directory-program.

Så här konfigurerar du Azure Active Directory för Dynamics 365-kund engagemang:

1. Logga in på [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan.

1. Välj **Egenskaper** och kopiera värdet för **katalog-ID** på sidan **katalog egenskaper** . Spara det här värdet eftersom du måste ange det i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande.

    ![Meny alternativet Azure Active Directory egenskaper](media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Välj **Appregistreringar** i den Azure Active Directory vänstra rutan och välj sedan **ny registrering** på sidan.
1. Ange ett meningsfullt namn på program namnet.
1. Under **konto typer som stöds** väljer du **konton i valfri organisations katalog**.
1. Under **omdirigerings-URI (valfritt)** väljer du **webb** och anger en URI, till exempel `https://contosoapp1/auth` .
1. Välj **Register** (Registrera).

    ![Registrera en program sida](media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Nu när ditt program är registrerat går du till programmets översikts sida. Kopiera **ID-värdet för programmet (klienten)** på sidan. Spara det här värdet eftersom du måste ange det i publicerings portalen och i Dynamics 365 för att ta emot leads för ditt Marketplace-erbjudande.

    ![ID-ruta för program (klient)](media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Välj **certifikat & hemligheter** i appens vänstra ruta och välj knappen **ny klient hemlighet** . Ange en meningsfull beskrivning för klient hemligheten och välj alternativet **aldrig** under **upphör att gälla**. Välj **Lägg till** för att skapa klient hemligheten.

    ![Meny alternativet certifikat & hemligheter](media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Kopiera **klientens hemliga** värde så fort klient hemligheten har skapats. Du kommer inte att kunna hämta värdet när du har lämnat sidan. Spara det här värdet eftersom du måste ange det i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande. 
1. Välj **API-behörigheter** i appens vänstra ruta och välj sedan **+ Lägg till en behörighet**.
1. Välj **Microsoft API: er** och välj sedan **Dynamics CRM** som API.
1. Se till att **delegerade behörigheter** är markerat under **vilken typ av behörigheter som krävs för programmet?**.
1. Under **behörighet** markerar du kryss rutan **user_impersonation** för **åtkomst common data service som organisations användare**. Välj sedan **Lägg till behörigheter**.

    ![Knappen Lägg till behörigheter](media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. När du har slutfört steg 1 till 14 i Azure Portal går du till din Dynamics 365 kund engagemang-instans genom att gå till URL: en, till exempel `https://tenant.crm.dynamics.com` .
1. Välj kugg hjuls ikonen i det översta fältet och välj sedan **Avancerade inställningar**.
1. På sidan **Inställningar** öppnar du menyn **Inställningar** i det översta fältet och väljer **säkerhet**.
1. På sidan **säkerhet** väljer du **användare**. På sidan **användare** väljer du List rutan **aktiverade användare** och väljer sedan **program användare**.
1. Välj **ny** för att skapa en ny användare.

    ![Skapa en ny användare](media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. I fönstret **ny användare** kontrollerar du att **användare: program användare** har marker ATS. Ange ett användar namn, fullständigt namn och e-postadress för den användare som du vill använda med den här anslutningen. Klistra också in **program-ID: t** för den app som du skapade i Azure Portal från steg 8. Välj **spara & Stäng** för att slutföra tillägget av användaren.

    ![Fönstret ny användare](media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Gå till avsnittet "säkerhets inställningar" i den här artikeln om du vill slutföra konfigurationen av anslutningen för den här användaren.

### <a name="office-365"></a>Office 365

Om du inte vill använda Azure Active Directory kan du registrera en ny användare på Microsoft 365 administrations Center. Du måste uppdatera ditt användar namn och lösen ord var 90 dag om du vill fortsätta få leads.

Så här konfigurerar du Office 365 för Dynamics 365 kund engagemang:

1. Logga in på [Administrationscenter för Microsoft 365](https://admin.microsoft.com).

1. Välj **Lägg till en användare**.

    ![Microsoft 365 administrations Center Lägg till ett användar alternativ](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Skapa en ny användare för tjänsten för lead Writer. Konfigurera följande inställningar:

    * Ange ett användar namn.
    * Ange ett lösen ord och avmarkera alternativet **gör att användaren ändrar sitt lösen ord första gången de loggar** in.
    * Välj **användare (ingen administratörs åtkomst)** som roll för användaren.
    * Välj **Dynamics 365 kund engagemang plan** som produkt licens, som du ser på följande skärm bild. Du debiteras för den licens du väljer. 

Spara dessa värden eftersom du måste ange värdena för **användar namn** och **lösen ord** i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande.

![Fönstret ny användare i Microsoft 365 administrations Center](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Säkerhetsinställningar

Det sista steget är att aktivera den användare som du skapade för att skriva leads.

1. Öppna Dynamics 365 kund engagemang genom att gå till URL: en för din Dynamics-instans, till exempel `https://tenant.crm.dynamics.com` .
1. Välj kugg hjuls ikonen i det översta fältet och välj sedan **Avancerade inställningar**.
1. På sidan **Inställningar** öppnar du menyn **Inställningar** i det översta fältet och väljer **säkerhet**.
1. På sidan **säkerhet** väljer du **användare** och väljer den användare som du skapade i avsnittet "konfigurera användar behörigheter" i det här dokumentet. Välj sedan **hantera roller**.

    ![Fliken hantera roller](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Sök efter roll namnet **Microsoft Marketplace lead Writer** och välj den för att tilldela användaren rollen.

    ![Fönstret hantera användar roller](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Den här rollen skapas av den lösning som du har importerat och har bara behörighet att skriva leads och spåra lösnings versionen för att säkerställa kompatibiliteten.

1. Gå tillbaka till sidan **säkerhet** och välj **säkerhets roller**. Sök efter rollen **Microsoft Marketplace lead Writer** och markera den.

    ![Fönstret säkerhets roller](media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. I säkerhets rollen väljer du fliken **kärn poster** . Sök efter objektet **användar enhets gränssnitts inställningar** . Aktivera behörigheterna Skapa, läsa och skriv till användare (1/4 gul cirkel) för den entiteten genom att välja motsvarande alternativ knappar.

    ![Fliken poster i Microsoft Marketplace huvud start skrivare](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. På fliken **anpassning** söker du efter **system jobb** -objektet. Aktivera Läs-, skriv-och AppendTo-behörighet till organisationen (solida gröna alternativ knappar) för den entiteten genom att välja motsvarande alternativ knappar.

    ![Microsoft Marketplace anpassning av lead Writer-fliken](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Välj **Spara och stäng**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Konfigurera ditt erbjudande för att skicka leads till Dynamics 365 kund engagemang

Så här konfigurerar du information om lead-hantering för ditt erbjudande i publicerings portalen:

1. Gå till installations sidan för **erbjudandet** för ditt erbjudande.
1. Under avsnittet **kund leads** väljer du **Anslut**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Kund ledare":::

1. I popup-fönstret anslutnings information väljer du **Dynamics 365 kund engagemang** för lead-målet.

    ![Rutan lead-mål](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Ange **URL: en** för Dynamics 365-instansen, till exempel `https://contoso.crm4.dynamics.com` .

1. Välj **autentiseringsmetod, antingen** Azure Active Directory eller Office 365.
1. Om du har valt **Azure Active Directory** anger du **programmets (klient) ID** (till exempel `23456052-aaaa-bbbb-8662-1234df56788f` ), **katalog-ID** (till exempel `12345678-8af1-4asf-1234-12234d01db47` ) och **klient hemlighet** (till exempel `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=` ).

    ![Autentisering med Azure Active Directory vald](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Om du har valt **Office 365** anger du **användar namnet** (till exempel `contoso@contoso.onmicrosoft.com` ) och **lösen ordet** (till exempel `P@ssw0rd` ).

    ![Office 365-användar namn Box](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Ange e- **postadresser för personer** i företaget som ska få e-postaviseringar när ett nytt lead tas emot. Du kan ange flera e-postadresser genom att avgränsa dem med semikolon.
1. Välj **OK**.

Om du vill kontrol lera att du har anslutit till ett lead-mål väljer du knappen **Verifiera** . Om det lyckas har du ett test lead i lead-målet.

![Rutan kontakta e-post](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.
