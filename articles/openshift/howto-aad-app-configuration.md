---
title: Azure Active Directory-integrering för Azure Red Hat OpenShift
description: Lär dig hur du skapar en Azure AD-säkerhetsgrupp och användare för att testa appar i ditt Microsoft Azure Red Hat OpenShift-kluster.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f0bf28d61d4c9ad95a485fb4b60e370c16ace16c
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633333"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory-integrering för Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 kommer att dras tillbaka 30 juni 2022. Stöd för att skapa nya Azure Red Hat OpenShift 3,11-kluster fortsätter till och med 30 november 2020. Efter pensionering kommer de återstående Azure Red Hat OpenShift 3,11-klustren att stängas av för att förhindra säkerhets problem.
> 
> Följ den här guiden för att [skapa ett Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md).
> Om du har frågor kan du [kontakta oss](mailto:arofeedback@microsoft.com).

Om du inte redan har skapat en Azure Active Directory-klient (Azure AD) följer du anvisningarna i [skapa en Azure AD-klient för Azure Red Hat OpenShift](howto-create-tenant.md) innan du fortsätter med de här anvisningarna.

Microsoft Azure Red Hat OpenShift måste ha behörighet att utföra åtgärder på uppdrag av klustret. Om din organisation inte redan har en Azure AD-användare, Azure AD-säkerhetsgrupp eller en Azure AD-App-registrering som ska användas som tjänstens huvud namn, följer du dessa anvisningar för att skapa dem.

## <a name="create-a-new-azure-active-directory-user"></a>Skapa en ny Azure Active Directory-användare

I [Azure Portal](https://portal.azure.com)kontrollerar du att klienten visas under ditt användar namn längst upp till höger i portalen:

![Skärm bild av portalen med klient organisation listad överst till höger ](./media/howto-create-tenant/tenant-callout.png) om fel klient visas, klicka på ditt användar namn längst upp till höger och klicka sedan på **Växla katalog** och välj rätt klient i listan **alla kataloger** .

Skapa en ny Azure Active Directory ägarens användare för att logga in i ditt Azure Red Hat OpenShift-kluster.

1. Gå till bladet [användare – alla användare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) .
2. Klicka på **+ ny användare** för att öppna fönstret **användare** .
3. Ange ett **namn** för den här användaren.
4. Skapa ett **användar namn** baserat på namnet på den klient som du skapade, med  `.onmicrosoft.com` tillägg i slutet. Till exempel `yourUserName@yourTenantName.onmicrosoft.com`. Skriv ned det här användar namnet. Du behöver den för att logga in på klustret.
5. Klicka på **katalog roll** för att öppna fönstret katalog roll och välj **ägare** och klicka sedan på **OK** längst ned i fönstret.
6. I fönstret **användare** klickar du på **Visa lösen ord** och registrerar det tillfälliga lösen ordet. När du har loggat in första gången uppmanas du att återställa den.
7. Klicka på **skapa** längst ned i fönstret för att skapa användaren.

## <a name="create-an-azure-ad-security-group"></a>Skapa en Azure AD-säkerhetsgrupp

Medlemskap i en Azure AD-säkerhetsgrupp synkroniseras i OpenShift-gruppen "OSA-Customer-admins" för att bevilja åtkomst till kluster administratören. Om inget anges beviljas ingen kluster administratörs åtkomst.

1. Öppna bladet [Azure Active Directory grupper](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) .
2. Klicka på **+ ny grupp**.
3. Ange ett grupp namn och en beskrivning.
4. Ange **grupp typ** till **säkerhet**.
5. Ange **medlemskaps typ** som **tilldelad**.

    Lägg till den Azure AD-användare som du skapade i det tidigare steget i den här säkerhets gruppen.

6. Klicka på **medlemmar** för att öppna fönstret **Välj medlemmar** .
7. I listan Medlemmar väljer du den Azure AD-användare som du skapade ovan.
8. Längst ned i portalen klickar du på **Välj** och sedan **skapa** för att skapa säkerhets gruppen.

    Skriv ned värdet för grupp-ID.

9. När gruppen har skapats visas den i listan över alla grupper. Klicka på den nya gruppen.
10. På sidan som visas kopierar du **objekt-ID: t**. Vi kommer att referera till det här värdet som `GROUPID` i själv studie kursen [skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

> [!IMPORTANT]
> Om du vill synkronisera den här gruppen med OpenShift-gruppen OSA-Customer-admins skapar du klustret med hjälp av Azure CLI. Azure Portal för närvarande saknar ett fält för att ange den här gruppen.

## <a name="create-an-azure-ad-app-registration"></a>Skapa en Azure AD-App-registrering

Du kan automatiskt skapa en Azure Active Directory-klient (Azure AD) för program registrering som en del av att skapa klustret genom `--aad-client-app-id` att utelämna flaggan till `az openshift create` kommandot. I den här självstudien lär du dig hur du skapar Azure AD-appens registrering för att bli klar.

Om din organisation inte redan har en Azure Active Directory-app (Azure AD) som ska användas som tjänstens huvud namn, följer du dessa instruktioner för att skapa en.

1. Öppna [bladet Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) och klicka på **+ ny registrering**.
2. Ange ett namn för program registreringen i fönstret **Registrera ett program** .
3. Se till att under **konto typer som stöds** som **konton i den här organisations katalogen endast** är markerad. Det här är det säkraste alternativet.
4. Vi kommer att lägga till en omdirigerings-URI senare när vi känner till klustrets URI. Klicka på knappen **Registrera** för att skapa Azure AD-programregistrering.
5. På sidan som visas kopierar du **program-ID: t (klient)**. Vi kommer att referera till det här värdet som `APPID` i själv studie kursen [skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

![Skärm bild av sidan app-objekt](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Skapa en klient hemlighet

Generera en klient hemlighet för autentisering av appen till Azure Active Directory.

1. I avsnittet **Hantera** på sidan registrerings program klickar du på **certifikat & hemligheter**.
2. Klicka på **+ ny klient hemlighet** i fönstret **certifikat & hemligheter** .  Fönstret **Lägg till en klient hemlighet** visas.
3. Ange en **Beskrivning**.
4. Ange **förfallo datum** för den varaktighet du föredrar, till exempel **på två år**.
5. Klicka på **Lägg till** och nyckelvärdet visas i avsnittet **klient hemligheter** på sidan.
6. Kopiera värdet för nyckel. Vi kommer att referera till det här värdet som `SECRET` i själv studie kursen [skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

![Skärm bild av fönstret certifikat och hemligheter](./media/howto-create-tenant/create-key.png)

Mer information om Azure Application objekt finns [i program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Mer information om hur du skapar ett nytt Azure AD-program finns i [Registrera en app med slut punkten för Azure Active Directory v 1.0](../active-directory/develop/quickstart-register-app.md).

## <a name="add-api-permissions"></a>Lägga till API-behörigheter

[//]: # (Ändra inte till Microsoft Graph. Den fungerar inte med Microsoft Graph.)
1. I avsnittet **Hantera** klickar du på **API-behörigheter**
2. Klicka på **Lägg till behörighet** och välj **Azure Active Directory graf** sedan **delegerade behörigheter**.
> [!NOTE]
> Se till att du har valt "Azure Active Directory Graf" och inte "Microsoft Graph"-panelen.

3. Expandera **användare** i listan nedan och aktivera **användaren. Läs** behörighet. Om **User. Read** är aktiverat som standard, se till att det är **Azure Active Directory Graph** behörighet **User. Read**.
4. Rulla upp och välj **program behörigheter**.
5. Expandera **katalogen** i listan nedan och aktivera **Directory. ReadAll**.
6. Klicka på **Lägg till behörigheter** för att acceptera ändringarna.
7. Panelen API-behörigheter bör nu visa både *User. Read* och *Directory. ReadAll*. Observera varningen i kolumnen **admin medgivande som krävs** bredvid *Directory. ReadAll*.
8. Om du är *administratör för Azure-prenumeration* klickar du på **bevilja administratörs medgivande för *prenumerations namn*** nedan. Om du inte är *administratör för Azure-prenumerationen* ber du ditt medgivande från administratören.

![Skärm bild av panelen API-behörigheter. User. Read och Directory. ReadAll-behörigheter lades till, administratörs medgivande krävs för Directory. ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Synkronisering av gruppen kluster administratörer fungerar bara när medgivande har beviljats. En grön cirkel visas med en bock markering och ett meddelande "beviljat *prenumerations namn*" i kolumnen *admin medgivande krävs* .

Mer information om hur du hanterar administratörer och andra roller finns i [lägga till eller ändra Azure-prenumerations administratörer](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="resources"></a>Resurser

* [Program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)
* [Snabbstart: Registrera en app med Azure Active Directory v1.0-slutpunkten](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>Nästa steg

Om du har uppfyllt alla de [nödvändiga förutsättningarna för att skapa Azure Red Hat OpenShift](howto-setup-environment.md)är du redo att skapa ditt första kluster!

Prova själv studie kursen:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)