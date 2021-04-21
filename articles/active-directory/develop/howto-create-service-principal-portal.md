---
title: Skapa en Azure AD-app & tjänstens huvudnamn i portalen
titleSuffix: Microsoft identity platform
description: Skapa en ny Azure Active Directory-app & tjänstens huvudnamn för att hantera åtkomst till resurser med rollbaserad åtkomstkontroll i Azure Resource Manager.
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
ms.openlocfilehash: 621bd392c12bb6ef1269eaed4731063490664f7e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750796"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser

Den här artikeln visar hur du skapar ett nytt Azure Active Directory-program (Azure AD) och tjänstens huvudnamn som kan användas med rollbaserad åtkomstkontroll. När du har program, värdtjänster eller automatiserade verktyg som behöver åtkomst till eller ändrar resurser kan du skapa en identitet för appen. Den här identiteten kallas tjänstens huvudnamn. Åtkomsten till resurser begränsas av de roller som tilldelats tjänstens huvudnamn, vilket ger dig kontroll över vilka resurser som kan nås och på vilken nivå. Av säkerhetsskäl rekommenderar vi att du alltid använder tjänstens huvudnamn med automatiserade verktyg i stället för att tillåta inloggning med en användaridentitet.

Den här artikeln visar hur du använder portalen för att skapa tjänstens huvudnamn i Azure Portal. Den fokuserar på ett program med en enda klientorganisation där programmet är avsett att köras inom endast en organisation. Du använder vanligtvis program med en enda klientorganisation för verksamhetsapplikationer som körs i din organisation.  Du kan också [använda Azure PowerShell för att skapa ett huvudnamn för tjänsten.](howto-authenticate-service-principal-powershell.md)

> [!IMPORTANT]
> Överväg att använda hanterade identiteter för Azure-resurser som programidentitet i stället för att skapa ett huvudnamn för tjänsten. Om din kod körs på en tjänst som stöder hanterade identiteter och har åtkomst till resurser som stöder Azure AD-autentisering är hanterade identiteter ett bättre alternativ för dig. Mer information om hanterade identiteter för Azure-resurser, inklusive vilka tjänster som för närvarande stöder det, finns i Vad är hanterade [identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Appregistrering, appobjekt och tjänstens huvudnamn
Det går inte att skapa ett huvudnamn för tjänsten direkt med hjälp av Azure Portal.  När du registrerar ett program via Azure Portal skapas automatiskt ett programobjekt och tjänstens huvudnamn i din hemkatalog eller klientorganisation.  Mer information om relationen mellan appregistrering, programobjekt och tjänstens huvudnamn finns i [Program- och tjänsthuvudnamnsobjekt i Azure Active Directory](app-objects-and-service-principals.md).

## <a name="permissions-required-for-registering-an-app"></a>Behörigheter som krävs för att registrera en app

Du måste ha tillräcklig behörighet för att registrera ett program med din Azure AD-klientorganisation och tilldela programmet en roll i din Azure-prenumeration.

### <a name="check-azure-ad-permissions"></a>Kontrollera Azure AD-behörigheter

1. Välj **Azure Active Directory**.
1. Observera din roll. Om du har **rollen Användare** måste du se till att icke-administratörer kan registrera program.

   ![Hitta din roll. Om du är användare bör du se till att icke-administratörer kan registrera appar](./media/howto-create-service-principal-portal/view-user-info.png)

1. I den vänstra rutan väljer du **Användarinställningar.**
1. Kontrollera **Appregistreringar** inställningen. Det här värdet kan bara anges av en administratör. Om det är **inställt** på Ja kan alla användare i Azure AD-klientorganisationen registrera en app.

Om inställningen för appregistreringar är inställd på **Nej** kan endast användare med en administratörsroll registrera dessa typer av program. Se [inbyggda Roller i Azure AD för att](../roles/permissions-reference.md#all-roles) lära dig mer om tillgängliga administratörsroller och de specifika behörigheter i Azure AD som ges till varje roll. Om ditt konto har tilldelats användarrollen, men appregistreringsinställningen är begränsad till administratörsanvändare, ber du administratören att antingen tilldela dig en av de administratörsroller som kan skapa och hantera alla aspekter av appregistreringar eller att låta användarna registrera appar.

### <a name="check-azure-subscription-permissions"></a>Kontrollera Azure-prenumerationsbehörigheter

I din Azure-prenumeration måste ditt konto ha `Microsoft.Authorization/*/Write` åtkomst till att tilldela en roll till en AD-app. Den här åtgärden beviljas genom rollen [Ägare](../../role-based-access-control/built-in-roles.md#owner) eller [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator). Om ditt konto har **tilldelats rollen** Deltagare har du inte tillräcklig behörighet. Du får ett felmeddelande när du försöker tilldela tjänstens huvudnamn en roll.

Så här kontrollerar du dina prenumerationsbehörigheter:

1. Sök efter och **välj Prenumerationer** eller **välj Prenumerationer** på **startsidan.**

   ![Sök](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den prenumeration som du vill skapa tjänstens huvudnamn i.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration som du letar efter väljer du **globala prenumerationsfilter.** Kontrollera att den prenumeration som du vill använda är vald för portalen.

1. Välj **Mina behörigheter.** Välj sedan Klicka **här för att visa fullständig åtkomstinformation för den här prenumerationen.**

   ![Välj den prenumeration som du vill skapa tjänstens huvudnamn i](./media/howto-create-service-principal-portal/view-details.png)

1. Välj **Visa** i **Rolltilldelningar för** att visa dina tilldelade roller och avgör om du har tillräcklig behörighet att tilldela en roll till en AD-app. Om inte ber du prenumerationsadministratören att lägga till dig i rollen Administratör för användaråtkomst. I följande bild tilldelas användaren rollen Ägare, vilket innebär att användaren har tillräcklig behörighet.

   ![Det här exemplet visar att användaren har tilldelats rollen Ägare](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Registrera ett program med Azure AD och skapa ett huvudnamn för tjänsten

Nu ska vi gå direkt till att skapa identiteten. Om du får problem kontrollerar du [behörigheterna som krävs för](#permissions-required-for-registering-an-app) att kontrollera att ditt konto kan skapa identiteten.

1. Logga in på ditt Azure-konto via <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Välj **Azure Active Directory**.
1. Välj **Appregistreringar**.
1. Välj **Ny registrering.**
1. Ge programmet ett namn. Välj en kontotyp som stöds, som avgör vem som kan använda programmet. Under **Omdirigerings-URI** **väljer du** Webb för den typ av program som du vill skapa. Ange den URI som åtkomsttoken skickas till. Du kan inte skapa autentiseringsuppgifter för ett [inbyggt program](../manage-apps/application-proxy-configure-native-client-application.md). Du kan inte använda den typen för ett automatiserat program. När du har valt värdena väljer du **Registrera**.

   ![Ange ett namn för ditt program](./media/howto-create-service-principal-portal/create-app.png)

Du har skapat ditt Azure AD-program och tjänstens huvudnamn.

> [!NOTE]
> Du kan registrera flera program med samma namn i Azure AD, men programmen måste ha olika program-ID:n (klient).

## <a name="assign-a-role-to-the-application"></a>Tilldela en roll till programmet

Om du vill komma åt resurser i din prenumeration måste du tilldela en roll till programmet. Bestäm vilken roll som ger rätt behörigheter för programmet. Mer information om tillgängliga roller finns i [Inbyggda Roller i Azure.](../../role-based-access-control/built-in-roles.md)

Du kan ange omfånget på nivån för prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre omfångsnivåer. Om du till exempel lägger till ett program *i rollen* Läsare för en resursgrupp kan den läsa resursgruppen och alla resurser som den innehåller.

1. I Azure Portal väljer du den omfångsnivå som du vill tilldela programmet till. Om du till exempel vill tilldela en roll i prenumerationsomfånget söker du efter och väljer Prenumerationer **eller** **väljer** Prenumerationer på **sidan** Start.

   ![Du kan till exempel tilldela en roll i prenumerationsomfånget](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den prenumeration som programmet ska tilldelas till.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration som du letar efter väljer du **globala prenumerationsfilter.** Kontrollera att den prenumeration som du vill använda är vald för portalen.

1. Välj **Åtkomstkontroll (IAM)** .
1. Välj **Lägg till rolltilldelning**.
1. Välj den roll som du vill tilldela till programmet. Om du till exempel vill tillåta att programmet kör åtgärder som **omstart**, **starta** **och** stoppa instanser väljer du **rollen** Deltagare.  Läs mer om [de tillgängliga rollerna](../../role-based-access-control/built-in-roles.md) Som standard visas inte Azure AD-program i de tillgängliga alternativen. Du hittar programmet genom att söka efter namnet och välja det.

   ![Välj den roll som ska tilldelas till programmet](./media/howto-create-service-principal-portal/select-role.png)

1. Välj **Spara** för att slutföra tilldelning av rollen. Du ser ditt program i listan över användare med en roll för det omfånget.

Tjänstens huvudnamn har ställts in. Du kan börja använda den för att köra skript eller appar. Om du vill hantera tjänstens huvudnamn (behörigheter, behörigheter som användaren har samtyckt till, se vilka användare som har samtyckt till, granska behörigheter, se inloggningsinformation med mera) går du till **Företagsprogram**.

Nästa avsnitt visar hur du hämtar värden som behövs vid inloggning programmatiskt.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Hämta värden för klientorganisation och app-ID för inloggning

När du loggar in programmässigt skickar du klientorganisations-ID:t med din autentiseringsbegäran och program-ID:t.  Du behöver också ett certifikat eller en autentiseringsnyckel (beskrivs i följande avsnitt). Hämta dessa värden med följande steg:

1. Välj **Azure Active Directory**.
1. Från **Appregistreringar** i Azure AD väljer du ditt program.
1. Kopiera katalog-ID:t (klientorganisationen) och lagra det i programkoden.

    ![Kopiera katalogen (klient-ID) och lagra den i din appkod](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    Katalog-ID:t (klient) finns också på översiktssidan för standardkatalogen.

1. Kopiera **Program-ID:t** och lagra det i din programkod.

   ![Kopiera program-ID:t (klienten)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Autentisering: Två alternativ

Det finns två typer av autentisering för tjänstens huvudnamn: lösenordsbaserad autentisering (programhemlighet) och certifikatbaserad autentisering. *Vi rekommenderar att du använder ett* certifikat , men du kan också skapa en programhemlighet.

### <a name="option-1-upload-a-certificate"></a>Alternativ 1: Ladda upp ett certifikat

Du kan använda ett befintligt certifikat om du har ett.  Du kan också skapa ett själv signerat certifikat endast *i testsyfte.* Om du vill skapa ett självsignerat certifikat öppnar du PowerShell och kör [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) med följande parametrar för att skapa certifikatet i användarcertifikatarkivet på datorn:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportera det här certifikatet till en fil med mmc-snapin-modulen [Hantera](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) användarcertifikat som är tillgänglig från Windows Kontrollpanelen.

1. Välj **Kör** på **Start-menyn** och ange **sedan certmgr.msc.**

   Certificate Manager-verktyget för den aktuella användaren visas.

1. Om du vill visa dina certifikat går **du till Certifikat – Aktuell** användare i den vänstra rutan och **expanderar katalogen Personlig.**
1. Högerklicka på det certifikat som du skapade och välj **Alla uppgifter som >Exportera.**
1. Följ guiden Exportera certifikat.  Exportera inte den privata nyckeln och exportera till en . CER-fil.

Så här laddar du upp certifikatet:

1. Välj **Azure Active Directory**.
1. Från **Appregistreringar** i Azure AD väljer du ditt program.
1. Välj **Certifikat & hemligheter**.
1. Välj **Ladda upp** certifikat och välj certifikatet (ett befintligt eller det själv signerade certifikat som du exporterade).

    ![Välj Ladda upp certifikat och välj det som du vill lägga till](./media/howto-create-service-principal-portal/upload-cert.png)

1. Välj **Lägg till**.

När du har registrerat certifikatet med ditt program i programregistreringsportalen aktiverar du klientprogramkoden för att använda certifikatet.

### <a name="option-2-create-a-new-application-secret"></a>Alternativ 2: Skapa en ny programhemlighet

Om du väljer att inte använda ett certifikat kan du skapa en ny programhemlighet.

1. Välj **Azure Active Directory**.
1. Från **Appregistreringar** i Azure AD väljer du ditt program.
1. Välj **Certifikat & hemligheter**.
1. Välj **Klienthemligheter - > Ny klienthemlighet**.
1. Ange en beskrivning av hemligheten och en varaktighet. När du är klar väljer du **Lägg till**.

   När du har sparat klienthemligheten visas värdet för klienthemligheten. Kopiera det här värdet eftersom du inte kan hämta nyckeln senare. Du anger nyckelvärdet med program-ID:t för att logga in som programmet. Lagra nyckelvärdet där programmet kan hämta det.

   ![Kopiera det hemliga värdet eftersom du inte kan hämta det senare](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurera åtkomstprinciper för resurser
Tänk på att du kan behöva konfigurera ytterligare behörigheter för resurser som programmet behöver åtkomst till. Du måste till exempel också uppdatera [åtkomstprinciperna för ett](../../key-vault/general/security-overview.md#privileged-access) nyckelvalv så att ditt program får åtkomst till nycklar, hemligheter eller certifikat.

1. I Azure Portal <a href="https://portal.azure.com/" target="_blank">du</a>till nyckelvalvet och väljer **Åtkomstprinciper.**
1. Välj **Lägg till åtkomstprincip** och välj sedan de nyckel-, hemlighets- och certifikatbehörigheter som du vill bevilja programmet.  Välj tjänstens huvudnamn som du skapade tidigare.
1. Välj **Lägg till** för att lägga till åtkomstprincipen och sedan **Spara** för att genomföra ändringarna.
    ![Lägga till åtkomstprincip](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder Azure PowerShell för att skapa ett huvudnamn för tjänsten.](howto-authenticate-service-principal-powershell.md)
* Mer information om hur du anger säkerhetsprinciper finns [i Rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../../role-based-access-control/role-assignments-portal.md)
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns i Azure Resource Manager [åtgärder för resursprovidern.](../../role-based-access-control/resource-provider-operations.md)
* Information om hur du arbetar med appregistreringar med **hjälp Microsoft Graph** finns i referensen för [Api för](/graph/api/resources/application) program.
