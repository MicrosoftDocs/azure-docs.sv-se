---
title: Skapa en Azure AD-App & tjänstens huvud namn i portalen
titleSuffix: Microsoft identity platform
description: Skapa en ny Azure Active Directory app & tjänstens huvud namn för att hantera åtkomst till resurser med rollbaserad åtkomst kontroll i Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 6adc3a8af90f6f05f640de97b8fa74c8d40e0329
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175557"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser

Den här artikeln visar hur du skapar ett nytt Azure Active Directory (Azure AD)-program och tjänstens huvud namn som kan användas med rollbaserad åtkomst kontroll. När du har program, värdbaserade tjänster eller automatiserade verktyg som behöver åtkomst till eller ändra resurser, kan du skapa en identitet för appen. Den här identiteten kallas tjänstens huvudnamn. Åtkomst till resurser begränsas av de roller som är tilldelade till tjänstens huvud namn, vilket ger dig kontroll över vilka resurser som kan nås och på vilka nivåer. Av säkerhetsskäl rekommenderar vi att du alltid använder tjänstens huvudnamn med automatiserade verktyg i stället för att tillåta inloggning med en användaridentitet.

Den här artikeln visar hur du använder portalen för att skapa tjänstens huvud namn i Azure Portal. Den fokuserar på ett program med en enda klient där programmet är avsett att köras endast inom en organisation. Du använder vanligt vis program med en enda klient för branschspecifika program som körs i din organisation.  Du kan också [använda Azure PowerShell för att skapa ett huvud namn för tjänsten](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> I stället för att skapa ett huvud namn för tjänsten bör du överväga att använda hanterade identiteter för Azure-resurser för din program identitet. Om din kod körs på en tjänst som har stöd för hanterade identiteter och åtkomst till resurser som stöder Azure AD-autentisering, är hanterade identiteter ett bättre alternativ. Mer information om hanterade identiteter för Azure-resurser, inklusive vilka tjänster som för närvarande stöder det, finns i [Vad är Managed identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Registrera appar, app-objekt och tjänstens huvud namn
Det finns inget sätt att direkt skapa ett huvud namn för tjänsten med hjälp av Azure Portal.  När du registrerar ett program via Azure Portal skapas automatiskt ett program objekt och tjänstens huvud namn i din hem katalog eller klient organisation.  För ytterligare information om relationen mellan app-registrering, program objekt och tjänst huvud namn, Läs [program-och tjänst huvud objekt i Azure Active Directory](app-objects-and-service-principals.md).

## <a name="permissions-required-for-registering-an-app"></a>Behörigheter som krävs för att registrera en app

Du måste ha behörighet att registrera ett program med din Azure AD-klient och tilldela till programmet en roll i din Azure-prenumeration.

### <a name="check-azure-ad-permissions"></a>Kontrol lera Azure AD-behörigheter

1. Välj **Azure Active Directory**.
1. Anteckna din roll. Om du har **användar** rollen måste du se till att icke-administratörer kan registrera program.

   ![Hitta din roll. Om du är en användare ser du till att icke-administratörer kan registrera appar](./media/howto-create-service-principal-portal/view-user-info.png)

1. Välj **användar inställningar** i det vänstra fönstret.
1. Kontrol lera inställningen för **Appregistreringar** . Det här värdet kan bara anges av en administratör. Om det är inställt på **Ja** kan alla användare i Azure AD-klienten registrera en app.

Om inställningen för appens registrering är inställd på **Nej** kan endast användare med en administratörs roll registrera dessa typer av program. Se [inbyggda Azure AD-roller](../roles/permissions-reference.md#all-roles) för att lära dig om tillgängliga administratörs roller och de särskilda behörigheter i Azure AD som ges till varje roll. Om ditt konto har tilldelats användar rollen, men appens registrerings inställning är begränsad till administratörs användare, be administratören att antingen tilldela dig en av administratörs rollerna som kan skapa och hantera alla aspekter av registrerade appar, eller för att låta användare registrera appar.

### <a name="check-azure-subscription-permissions"></a>Kontrol lera behörigheter för Azure-prenumeration

I din Azure-prenumeration måste ditt konto ha `Microsoft.Authorization/*/Write` åtkomst för att tilldela en roll till en AD-App. Den här åtgärden beviljas genom rollen [Ägare](../../role-based-access-control/built-in-roles.md#owner) eller [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator). Om ditt konto har tilldelats **deltagar** rollen, har du inte tillräcklig behörighet. Ett fel meddelande visas när du försöker tilldela tjänstens huvud namn en roll.

Så här kontrollerar du dina prenumerations behörigheter:

1. Sök efter och välj **prenumerationer** eller Välj **prenumerationer** på **Start** sidan.

   ![Sök](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den prenumeration som du vill skapa tjänstens huvud namn i.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration du letar efter väljer du **globala prenumerations filter**. Kontrol lera att den prenumeration du vill använda är vald för portalen.

1. Välj **mina behörigheter**. Välj sedan **Klicka här om du vill visa fullständig åtkomst information för den här prenumerationen**.

   ![Välj den prenumeration som du vill skapa tjänstens huvud namn i](./media/howto-create-service-principal-portal/view-details.png)

1. Välj **Visa** i **roll tilldelningar** för att visa dina tilldelade roller och kontrol lera om du har tillräcklig behörighet för att tilldela en roll till en AD-App. Annars ber du prenumerations administratören att lägga till dig i rollen som administratör för användar åtkomst. I följande bild tilldelas användaren rollen ägare, vilket innebär att användaren har tillräcklig behörighet.

   ![Det här exemplet visar att användaren har tilldelats ägar rollen](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Registrera ett program med Azure AD och skapa ett huvud namn för tjänsten

Nu ska vi gå direkt till att skapa identiteten. Om du stöter på ett problem kontrollerar du de [behörigheter som krävs](#permissions-required-for-registering-an-app) för att se till att ditt konto kan skapa identiteten.

1. Logga in på ditt Azure-konto via <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Välj **Azure Active Directory**.
1. Välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ge programmet ett namn. Välj en kontotyp som stöds, vilket avgör vem som kan använda programmet. Under **omdirigerings-URI** väljer du **webb** för den typ av program som du vill skapa. Ange URI: n där åtkomsttoken skickas till. Du kan inte skapa autentiseringsuppgifter för ett [internt program](../manage-apps/application-proxy-configure-native-client-application.md). Du kan inte använda den typen för ett automatiskt program. När du har angett värdena väljer du **Registrera**.

   ![Ange ett namn för ditt program](./media/howto-create-service-principal-portal/create-app.png)

Du har skapat ditt Azure AD-program och tjänstens huvud namn.

> [!NOTE]
> Du kan registrera flera program med samma namn i Azure AD, men programmen måste ha olika program-ID: n (klient).

## <a name="assign-a-role-to-the-application"></a>Tilldela en roll till programmet

Du måste tilldela en roll till programmet för att få åtkomst till resurser i din prenumeration. Bestäm vilken roll som har rätt behörigheter för programmet. Mer information om tillgängliga roller finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Du kan ange omfång på nivån för prenumerationen, resurs gruppen eller resursen. Behörigheter ärvs till lägre omfattnings nivåer. Om du till exempel lägger till ett program till rollen *läsare* för en resurs grupp innebär det att den kan läsa resurs gruppen och alla resurser som den innehåller.

1. I Azure Portal väljer du den omfattnings nivå som du vill tilldela programmet till. Om du till exempel vill tilldela en roll i prenumerations omfånget söker du efter och väljer **prenumerationer** eller väljer **prenumerationer** på **Start** sidan.

   ![Tilldela till exempel en roll i prenumerations omfånget](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den specifika prenumeration som programmet ska tilldelas.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration du letar efter väljer du **globala prenumerations filter**. Kontrol lera att den prenumeration du vill använda är vald för portalen.

1. Välj **Åtkomstkontroll (IAM)** .
1. Välj **Lägg till rolltilldelning**.
1. Välj den roll som du vill tilldela till programmet. Om du till exempel vill tillåta att programmet kör åtgärder som **starta om**, **Starta** och **stoppa** instanser väljer du rollen **deltagare** .  Läs mer om [tillgängliga roller](../../role-based-access-control/built-in-roles.md) som standard visas inte Azure AD-program i de tillgängliga alternativen. Du hittar ditt program genom att söka efter namnet och välja det.

   ![Välj den roll som ska tilldelas programmet](./media/howto-create-service-principal-portal/select-role.png)

1. Välj **Spara** för att slutföra tilldelningen av rollen. Du ser ditt program i listan med användare med en roll för det omfånget.

Tjänstens huvud namn har kon figurer ATS. Du kan börja använda den för att köra skript eller appar. Om du vill hantera tjänstens huvud namn (behörigheter, användare som har skickat behörigheter, se vilka användare som har samtyckt, granska behörigheter, se inloggnings information och mer) går du till **företags program**.

Nästa avsnitt visar hur du hämtar värden som behövs när du loggar in program mässigt.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Hämta klient-och app-ID-värden för inloggning

När program mässigt loggar in skickar du klient-ID: t med din autentiseringsbegäran och program-ID: t.  Du måste också ha ett certifikat eller en autentiseringsnyckel (beskrivs i följande avsnitt). Hämta dessa värden med följande steg:

1. Välj **Azure Active Directory**.
1. Välj ditt program från **Appregistreringar** i Azure AD.
1. Kopiera katalog-ID och lagra det i program koden.

    ![Kopiera katalogen (klient-ID) och lagra den i din app Code](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    Du kan också hitta katalog-ID: t på sidan standard katalog översikt.

1. Kopiera **Program-ID:t** och lagra det i din programkod.

   ![Kopiera program-ID: t (klient)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Autentisering: två alternativ

Det finns två typer av autentisering för tjänstens huvud namn: lösenordsbaserad autentisering (program hemlighet) och certifikatbaserad autentisering. *Vi rekommenderar att du använder ett certifikat*, men du kan också skapa en program hemlighet.

### <a name="option-1-upload-a-certificate"></a>Alternativ 1: Ladda upp ett certifikat

Du kan använda ett befintligt certifikat om du har ett.  Alternativt kan du bara skapa ett självsignerat certifikat för *testning*. Om du vill skapa ett självsignerat certifikat öppnar du PowerShell och kör [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) med följande parametrar för att skapa certifikatet i användar certifikat arkivet på datorn:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportera det här certifikatet till en fil med hjälp av MMC-snapin-modulen [hantera användar certifikat](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) som är tillgänglig från kontroll panelen i Windows.

1. Välj **Kör** på **Start** -menyn och ange sedan **certmgr. msc**.

   Verktyget Certificate Manager för den aktuella användaren visas.

1. Om du vill visa dina certifikat, under **certifikat – aktuell användare** i det vänstra fönstret, expanderar du den **personliga** katalogen.
1. Högerklicka på det certifikat som du har skapat, Välj **alla aktiviteter->exportera**.
1. Följ guiden Exportera certifikat.  Exportera inte den privata nyckeln och exportera till en. CER-fil.

För att ladda upp certifikatet:

1. Välj **Azure Active Directory**.
1. Välj ditt program från **Appregistreringar** i Azure AD.
1. Välj **certifikat & hemligheter**.
1. Välj **överför certifikat** och välj certifikatet (ett befintligt certifikat eller det självsignerade certifikat som du exporterade).

    ![Välj överför certifikat och välj det som du vill lägga till](./media/howto-create-service-principal-portal/upload-cert.png)

1. Välj **Lägg till**.

När du har registrerat certifikatet med ditt program på program registrerings portalen ska du aktivera klient program koden för att använda certifikatet.

### <a name="option-2-create-a-new-application-secret"></a>Alternativ 2: skapa en ny program hemlighet

Om du väljer att inte använda ett certifikat kan du skapa en ny program hemlighet.

1. Välj **Azure Active Directory**.
1. Välj ditt program från **Appregistreringar** i Azure AD.
1. Välj **certifikat & hemligheter**.
1. Välj **klient hemligheter – > ny klient hemlighet**.
1. Ange en beskrivning av hemligheten och en varaktighet. När du är färdig väljer du **Lägg till**.

   När klient hemligheten har sparats visas värdet för klient hemligheten. Kopiera det här värdet eftersom du inte kommer att kunna hämta nyckeln senare. Du får nyckel värdet med program-ID för att logga in som programmet. Lagra nyckelvärdet där programmet kan hämta det.

   ![Kopiera det hemliga värdet eftersom du inte kan hämta det senare](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurera åtkomst principer för resurser
Kom ihåg att du kan behöva konfigurera ytterligare behörigheter för resurser som ditt program behöver ha åtkomst till. Till exempel måste du också [Uppdatera ett nyckel valvs åtkomst principer](../../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) för att ge programmet åtkomst till nycklar, hemligheter eller certifikat.

1. I <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>navigerar du till ditt nyckel valv och väljer **åtkomst principer**.
1. Välj **Lägg till åtkomst princip** och välj sedan de nyckel-, hemlighet-och certifikat behörigheter som du vill ge ditt program.  Välj tjänstens huvud namn som du skapade tidigare.
1. Välj **Lägg** till för att lägga till åtkomst principen och spara för att **Spara** ändringarna.
    ![Lägg till åtkomst princip](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder Azure PowerShell för att skapa ett huvud namn för tjänsten](howto-authenticate-service-principal-powershell.md).
* Information om hur du anger säkerhets principer finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns i [Azure Resource Manager Resource Provider-åtgärder](../../role-based-access-control/resource-provider-operations.md).
* Information om hur du arbetar med app-registreringar med hjälp av **Microsoft Graph** finns i API-referens för [program](/graph/api/resources/application) .
