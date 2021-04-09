---
title: Uppdatera dina grupp information på portalen för Mina appar – Azure AD
description: Lär dig hur du visar och uppdaterar grupprelaterad information, inklusive hur du visar de grupper du äger, skapar nya grupper, visar de grupper som du redan är medlem i och hur du kopplar ihop grupper som du inte redan ingår i.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: fc8d211160e757c78a8e2adafad703118628e6dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100095052"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Uppdatera dina grupp information på portalen Mina appar

Du kan använda ditt arbets-eller skol konto med den webbaserade **min Apps** -portalen för att visa och starta många av organisationens molnbaserade appar för att uppdatera viss profil-och konto information, för att se din **grupp** information och för att utföra **åtkomst granskningar** för dina appar och grupper. Om du inte har åtkomst till portalen för **Mina appar** måste du kontakta supportavdelningen för att få behörighet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Innehållet är avsett för användare. Om du är administratör kan du hitta mer information om hur du konfigurerar och hanterar dina molnbaserade appar i [program hanterings dokumentationen](../manage-apps/index.yml).
>
> Om du ser ett fel när du loggar in med en personlig Microsoft-konto kan du fortfarande logga in med domän namnet för din organisation (t. ex. contoso.com) eller **klient organisations-ID: t** för din organisation från administratören i någon av följande URL: er:
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-groups-information"></a>Visa information om dina grupper

Om administratören har gett dig behörighet att visa panelen **grupper** kan du:

- **Som grupp medlem.** Visa information eller lämna någon grupp.

- **Som grupp ägare.** Visa information, skapa en ny grupp, lägga till eller ta bort medlemmar eller ta bort gruppen.

### <a name="to-view-your-groups-information"></a>Visa information om din grupp

1. Logga in på ditt arbets-eller skol konto.

2. Öppna webbläsaren och gå till https://myapps.microsoft.com eller Använd den länk som tillhandahålls av din organisation. Du kan till exempel dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com . Om du loggar in med en personlig Microsoft-konto kan du fortfarande logga in med domän namnet för din organisation (t. ex. contoso.com) eller klient organisations **-ID: t** för din organisation från administratören i någon av följande URL: er:

   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*


    Sidan **appar** visas och visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

    ![Sidan appar i portalen Mina appar](media/my-apps-portal-end-user-groups/my-apps-home-large.png)

3. Dra ned menyn **Mina appar** och välj **Mina grupper** för att se din grupprelaterad information.

4. Baserat på dina behörigheter kan du använda sidan **Mina grupper** för att:

    - **Granska de grupper som du äger.** Visa information om alla grupper som du äger i organisationen från **grupper i eget** områden. Om du väljer ett särskilt grupp namn får du mer information om gruppen, inklusive grupp typen, antalet medlemmar, kopplings principen och listan över aktiva medlemmar.

    - **Skapa en ny grupp.** Skapa en ny grupp med dig som ägare från **grupperna i det egna** avsnittet. Detaljerade anvisningar finns i avsnittet [skapa en ny grupp](#create-a-new-group) i den här artikeln.

    - **Redigera en befintlig grupp.** Redigera informationen för någon av dina egna grupper. Detaljerade anvisningar finns i avsnittet [Redigera en befintlig grupp](#edit-an-existing-group) i den här artikeln.

    - **Lägg till eller ta bort medlemmar.** Lägg till eller ta bort medlemmar för grupper som du äger. Detaljerade anvisningar finns i avsnittet [lägga till eller ta bort en medlem](#add-or-remove-a-member) i den här artikeln.

    - **Förnya en Office 365-grupp.** Om din organisation tillåter det kan du förnya Office 365-grupperna. Detaljerade anvisningar finns i avsnittet [förnya en Office 365-grupp](#renew-an-office-365-group) i den här artikeln. 

    - **Ta bort en grupp.** Ta bort alla grupper som du äger. Detaljerade anvisningar finns i avsnittet [ta bort en grupp](#delete-a-group) i den här artikeln.

    - **Granska de grupper som du är en del av.** Visa namnen på alla grupper som du är medlem i från de **grupper jag bor i** . Om du väljer ett särskilt grupp namn får du mer information om gruppen, inklusive grupp typen, antalet medlemmar, kopplings principen och listan över aktiva medlemmar.

    - **Gå med i en grupp.** Anslut till en befintlig grupp, som du inte redan är medlem i, från **grupper** i ett ställe. Mer information om de olika stegen finns i [koppla en befintlig grupp](#join-an-existing-group).

## <a name="create-a-new-group"></a>Skapa en ny grupp

1. På sidan **grupper** väljer du **skapa en grupp** från **grupper i eget** områden.

    Rutan **Skapa grupp** visas.

    ![Skapa grupp ruta](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Ange den information som krävs:

    - **Grupptyp:**

        - **Bullet.** Används för att hantera medlems- och datoråtkomst till delade resurser för en grupp användare. Du kan till exempel skapa en säkerhetsgrupp för en specifik säkerhetsprincip. På det här sättet kan du ge en uppsättning behörigheter till alla medlemmar på samma gång utan att behöva lägga till behörigheterna för varje medlem individuellt.

        - **Office 365.** Ger samarbetsmöjligheter genom att ge medlemmar tillgång till en delad postlåda, kalender, filer, SharePoint-webbplats och mer. Med det här alternativet kan du även ge personer utanför organisationen tillgång till gruppen.

    - **Grupp namn.** Lägg till ett namn för gruppen, något som du kommer ihåg och som låter logiskt.

    - **Grupp Beskrivning (valfritt).** Lägg till en valfri beskrivning till din grupp.

    - **Grup princip.** Välj antingen om du vill att alla ska kunna ansluta till gruppen eller bara tillåta ägare av gruppen att lägga till medlemmar.

3. Välj **Skapa**.

    Den nya gruppen skapas med dig som ägare och visas i **grupper** i listan. Eftersom du är ägare visas den här gruppen även i **grupperna** i listan.

## <a name="edit-an-existing-group"></a>Redigera en befintlig grupp

När du har skapat en grupp kan du redigera dess information, inklusive uppdatera all befintlig information.

1. Välj den grupp som du vill redigera på sidan **grupper** och välj sedan **Redigera information** på sidan *&lt; GROUP_NAME &gt;* .

    Rutan **Redigera information** visas och du kan uppdatera den information som du lade till när du skapade gruppen första gången.

2. Gör alla ändringar och välj sedan **Uppdatera**.

## <a name="add-or-remove-a-member"></a>Lägga till eller ta bort en medlem

Du kan lägga till eller ta bort medlemmar för alla grupper som du äger.

1. Välj den grupp som du vill lägga till medlemmar i och välj sedan **+** på sidan *&lt; GROUP_NAME &gt;* .

    ![Lägg till en grupp medlem med plus tecken markerat](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Sök efter den medlem som du vill lägga till från rutan **Lägg till medlemmar** och välj sedan **Lägg till**.

    ![Rutan Lägg till medlemmar med ny medlem att lägga till](media/my-apps-portal/my-apps-portal-add-member-page.png)

    En inbjudan skickas till den nya medlemmen för att komma igång med att komma åt organisationens appar.

3. Om du har lagt till en medlem av misstag, eller om en medlem har lämnat din organisation, kan du ta bort medlemmen genom att välja **ta bort medlem** bredvid medlemmens namn på sidan *&lt; GROUP_NAME &gt;* .

    ![Ta bort en medlem med länken borttagnings länk markerad](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Förnya en Office 365-grupp

Om din organisation tillåter det kan du förnya en Office 365-grupp och förlänga ditt förfallo datum.

1. Välj den Office 365-grupp som du vill förnya och välj sedan **förnya grupp**.

    ![Förnya en Office 365-grupp och utöka förfallo datumet](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Klicka på **OK** för att stänga bekräftelse meddelandet.

    När du har uppdaterat sidan ser du ditt uppdaterade datum för **senaste förnyade** och **gruppens förfallo** datum.

## <a name="delete-a-group"></a>Ta bort en grupp

Du kan när som helst ta bort alla egna grupper. Men om du tar bort en grupp av misstag måste du skapa den och lägga till medlemmar igen.

1. Välj den grupp som du vill ta bort permanent och välj sedan **ta bort grupp** på sidan *&lt; &gt; GROUP_NAME* .

    ![Sidan <Group_name> med länken Ta bort grupp markerad](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Välj **Ja** i bekräftelse meddelandet.

    Gruppen tas bort permanent.

## <a name="join-an-existing-group"></a>Anslut till en befintlig grupp

Du kan koppla eller lämna en befintlig grupp från sidan **grupper** .

1. På sidan **grupper** väljer du **Anslut grupp** från **grupper som jag bor i** .

    Sidan **Anslut grupper** visas.

    ![Sidan Anslut till grupper, med knappen ansluts grupp markerad](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. På sidan **Anslut grupper** väljer du namnet på den grupp som du vill ansluta till, visar de associerade grupp detaljerna och väljer sedan **Anslut grupp** om gruppen är tillgänglig.

    Om gruppen kräver att grupp ägaren godkänner medlemskap, uppmanas du att ange en affärs motivering för varför du behöver delta i gruppen och väljer sedan **begäran**. Om gruppen inte kräver godkännande läggs du omedelbart till som medlem och gruppen visas i **grupper** i listan.

3. Om du har anslutit en grupp av misstag eller om du inte längre behöver vara en del av den, kan du välja grupp namnet på sidan **Anslut grupper** och sedan välja **lämna grupp**.

    ![Sidan Anslut till grupper med knappen lämna grupp markerad](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Nästa steg

- [Få åtkomst till och använda appar på Mina appar-portalen](my-apps-portal-end-user-access.md).

- [Ändra din profil information](./my-account-portal-settings.md).

- [Utför dina egna åtkomst granskningar](my-apps-portal-end-user-access-reviews.md).