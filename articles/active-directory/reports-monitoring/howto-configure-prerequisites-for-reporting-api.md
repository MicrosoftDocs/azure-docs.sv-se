---
title: Krav för Azure Active Directory rapporterings-API | Microsoft Docs
description: Läs om förutsättningarna för att få åtkomst till Azure AD repor ting API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ca618dc234ff4383083ff5eb21299fb1e3e7128
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348746"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Krav för att få åtkomst till API: et för Azure Active Directory rapportering

[Azure Active Directory reporting API: er](./concept-reporting-api.md) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa dessa API: er från programmeringsspråk och verktyg.

Rapporterings-API: et använder [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) för att ge åtkomst till webb-API: er.

För att förbereda din åtkomst till rapporterings-API: et måste du:

1. [Tilldela roller](#assign-roles)
2. [Licens krav](#license-requirements)
3. [Registrera ett program](#register-an-application)
4. [Bevilja behörigheter](#grant-permissions)
5. [Samla in konfigurations inställningar](#gather-configuration-settings)

## <a name="assign-roles"></a>Tilldela roller

Du måste ha någon av följande roller tilldelade för att få åtkomst till rapporterings data via API: t:

- Säkerhetsläsare

- Säkerhetsadministratör

- Global administratör

## <a name="license-requirements"></a>Licens krav

För att få åtkomst till inloggnings rapporterna för en klient organisation måste en Azure AD-klient ha kopplat Azure AD Premium-licens. Azure AD Premium P1-licens (eller senare) krävs för att få åtkomst till inloggnings rapporter för alla Azure AD-klienter. Om katalog typen är Azure AD B2C, kan inloggnings rapporterna nås via API: et utan ytterligare licens krav. 


## <a name="register-an-application"></a>Registrera ett program

Registreringen behövs även om du ansluter till rapporterings-API: et med hjälp av ett skript. Registreringen ger dig ett **program-ID**, vilket krävs för auktoriseringen och gör att din kod kan ta emot tokens.

Om du vill konfigurera din katalog för att få åtkomst till Azure AD repor ting API måste du logga in på [Azure Portal](https://portal.azure.com) med ett Azure-administratörskonto som också är medlem i katalog rollen **Global administratör** i din Azure AD-klient.

> [!IMPORTANT]
> Program som körs under autentiseringsuppgifter med administratörs behörighet kan vara mycket kraftfulla, så se till att behålla programmets ID och hemliga autentiseringsuppgifter på en säker plats.
> 

**Registrera ett Azure AD-program:**

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory** i det vänstra navigerings fönstret.
   
    ![Skärm bild som visar Azure Active Directory valt från Azure Portal-menyn.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På sidan **Azure Active Directory** väljer du **Appregistreringar**.

    ![Skärm bild som visar Appregistreringar valt från menyn hantera.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. På sidan **Appregistreringar** väljer du **ny registrering**.

    ![Skärm bild som visar ny registrering vald.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Sidan **Registrera en app** :

    ![Skärm bild som visar sidan Registrera ett program där du kan ange värden i det här steget.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. I text rutan **namn** skriver du `Reporting API application` .

    b. För **konto typer som stöds** väljer du **konton endast i den här organisationen**.

    c. I **URL: en för omdirigering**  väljer du **webb** text rutan, skriver `https://localhost` .

    d. Välj **Register** (Registrera). 


## <a name="grant-permissions"></a>Bevilja behörigheter 

Beroende på vilket API du vill ha åtkomst till måste du ge appen följande behörigheter:  

| API | Behörighet |
| --- | --- |
| Windows Azure Active Directory | Läs katalogdata |
| Microsoft Graph | Läs alla Gransknings logg data |

![Skärm bild som visar var du kan välja Lägg till en behörighet i behörighets rutan för t. ex.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

I följande avsnitt visas stegen för båda API: erna. Om du inte vill använda något av API: erna kan du hoppa över de relaterade stegen.

**Så här beviljar du dina program behörigheter att använda API: erna:**


1. Välj **API-behörigheter** och **Lägg sedan till en behörighet**. 

    ![Skärm bild som visar sidan för P-I-behörigheter där du kan välja Lägg till en behörighet.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. På **sidan begär API-behörigheter** letar du upp **stöd för äldre API** **Azure Active Directory Graph**. 

    ![Skärm bild som visar sidan begär en P-behörighet där du kan välja Azure Active Directory graf.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. På sidan **nödvändiga behörigheter** väljer du **program behörigheter**, expandera **katalog** kryss rutan **katalog. ReadAll**.  Välj **Lägg till behörigheter**.

    ![Skärm bild som visar sidan begär en P-behörighet där du kan välja program behörigheter.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. På sidan **rapporterings-API application-API-behörigheter** väljer du **bevilja administratörs medgivande**. 

    ![Skärm bild som visar rapporten A P I program A P I-behörighet där du kan välja bevilja administrativt medgivande.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Obs: **Microsoft Graph** läggs till som standard under API-registrering.

    ![Skärm bild som visar sidan för P-I-behörigheter där du kan välja Lägg till en behörighet.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Samla in konfigurations inställningar 

I det här avsnittet visas hur du hämtar följande inställningar från din katalog:

- Domännamn
- Klient-ID
- Klienthemlighet

Du behöver dessa värden när du konfigurerar anrop till rapporterings-API: et. 

### <a name="get-your-domain-name"></a>Hämta ditt domän namn

**Så här hämtar du ditt domän namn:**

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret väljer du **Azure Active Directory**.
   
    ![Skärm bild som visar Azure Active Directory valt från Azure Portal-menyn.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På sidan **Azure Active Directory** väljer du **anpassade domän namn**.

    ![Skärm bild som visar anpassade domän namn som valts från Azure Active Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopiera domän namnet från listan över domäner.


### <a name="get-your-applications-client-id"></a>Hämta klient-ID för ditt program

**Hämta klient-ID: t för ditt program:**

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret klickar du på **Azure Active Directory**.
   
    ![Skärm bild som visar Azure Active Directory valt från Azure Portal-menyn.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Välj ditt program på sidan **registreringar för appen** .

3. Från program sidan, navigerar du till **program-ID** och väljer **Klicka för att kopiera**.

    ![Skärm bild som visar sidan rapportera en P I-program där du kan kopiera programmet I D.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Hämta programmets klient hemlighet
 Undvik fel vid försök att få åtkomst till gransknings loggar eller inloggning med hjälp av API: et.

**Så här hämtar du programmets klient hemlighet:**

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret klickar du på **Azure Active Directory**.
   
    ![Skärm bild som visar Azure Active Directory valt från Azure Portal-menyn.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Välj ditt program på sidan **registreringar för appen** .

3.  Välj **certifikat och hemligheter** på sidan **API-program** , i avsnittet **klient hemligheter** , klickar du på **+ ny klient hemlighet**. 

    ![Skärm bild som visar sidan certifikat & hemligheter där du kan lägga till en klient hemlighet.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. På sidan **Lägg till en klient hemlighet** lägger du till:

    a. I text rutan **Beskrivning** skriver du `Reporting API` .

    b. Välj **i två år** som **förfaller**.

    c. Klicka på **Spara**.

    d. Kopiera nyckelvärdet.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Felsöka fel i rapporterings-API: et

I det här avsnittet visas de vanliga fel meddelanden som du kan köra i när du får åtkomst till aktivitets rapporter med hjälp av Microsoft Graph API och steg för deras lösning.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Fel: det gick inte att hämta användar roller från Microsoft Graph

 Logga in på ditt konto med hjälp av båda inloggnings knapparna i graphs användar gränssnitt för att undvika att få ett fel när du försöker logga in med Graph Explorer. 

![Graph-testaren](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Fel: det gick inte att checka beta licens från Microsoft Graph 

Om du stöter på det här fel meddelandet när du försöker få åtkomst till inloggningar med Graph Explorer väljer du **ändra behörigheter** under ditt konto i det vänstra navigerings fältet och väljer **Tasks. readwrite** och **Directory. Read. all**. 

![Ändra behörighets gränssnitt](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: klienten är inte B2C eller så har klienten ingen Premium-licens

Åtkomst till inloggnings rapporter kräver en licens för Azure Active Directory Premium 1 (P1). Om du ser det här fel meddelandet vid åtkomst till inloggningar kontrollerar du att klienten är licensierad med en Azure AD P1-licens.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Fel: de tillåtna rollerna omfattar inte användare. 

 Undvik fel vid försök att få åtkomst till gransknings loggar eller inloggning med hjälp av API: et. Kontrol lera att ditt konto är en del av rollen **säkerhets läsare** eller **rapport läsare** i Azure Active Directory klient organisationen.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: appen saknar AAD-behörighet för Läs katalog data 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fel: programmet saknar Microsoft Graph API: et Läs alla Gransknings logg data behörighet

Följ stegen i [kraven för att få åtkomst till Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md) för att se till att programmet körs med rätt behörighets uppsättning. 

## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
* [Granska API-referens](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Rapport-API-referens för inloggnings aktivitet](/graph/api/resources/signin?view=graph-rest-beta)