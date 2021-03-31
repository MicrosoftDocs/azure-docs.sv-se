---
title: Skapa en klient i Windows Virtual Desktop (klassisk) – Azure
description: Beskriver hur du konfigurerar Windows Virtual Desktop (klassiska) klienter i Azure Active Directory.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f41584194f8f5e8afde630405116b8b169cb5656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91542044"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Självstudie: skapa en klient i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows.

Att skapa en klient i Windows Virtual Desktop är det första steget för att skapa din lösning för Skriv bords virtualisering. En klient organisation är en grupp med en eller flera värdar. Varje adresspool består av flera sessionsbaserade värdar, som körs som virtuella datorer i Azure och som är registrerade på Windows Virtual Desktop-tjänsten. Varje adresspool består också av en eller flera grupp grupper som används för att publicera fjärr skrivbord och fjärranslutna program resurser till användare. Med en klient kan du skapa värdar för pooler, skapa program grupper, tilldela användare och upprätta anslutningar via tjänsten.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Bevilja Azure Active Directory behörigheter till Windows Virtual Desktop-tjänsten.
> * Tilldela program rollen TenantCreator till en användare i din Azure Active Directory klient.
> * Skapa en Windows-klient för virtuella skriv bord.

## <a name="what-you-need-to-set-up-a-tenant"></a>Vad du behöver för att konfigurera en klient

Innan du börjar konfigurera din Windows-klient för virtuella datorer måste du se till att du har följande saker:

* [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) klient-ID för användare av virtuella Windows-datorer.
* Ett globalt administratörs konto inom Azure Active Directory klient organisationen.
   * Detta gäller även för leverantörer av moln lösningar (CSP) som skapar en Windows Virtual Desktop-klient för sina kunder. Om du befinner dig i en CSP-organisation måste du kunna logga in som global administratör för kundens Azure Active Directory instans.
   * Administratörs kontot måste ha en källa från den Azure Active Directory klient som du försöker skapa den virtuella Windows-klienten för fjärr skrivbord i. Den här processen stöder inte Azure Active Directory B2B-konton (gäst).
   * Administratörs kontot måste vara ett arbets-eller skol konto.
* En Azure-prenumeration.

Du måste ha klient-ID, globalt administratörs konto och Azure-prenumerationen klar så att processen som beskrivs i den här självstudien fungerar som den ska.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Bevilja behörigheter till Windows Virtual Desktop

Hoppa över det här avsnittet om du redan har beviljat behörigheter till Windows Virtual Desktop för den här Azure Active Directory-instansen.

När du beviljar behörigheter till Windows Virtual Desktop-tjänsten kan den fråga Azure Active Directory för administrativa och slutanvändare-uppgifter.

Så här beviljar du tjänst behörigheter:

1. Öppna en webbläsare och påbörja det administrativa godkännande flödet till [Windows Server-appen för virtuella skriv bord](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Om du hanterar en kund och behöver bevilja administrativt godkännande för kundens katalog, anger du följande URL i webbläsaren och ersätter {Tenant} med kundens namn på Azure AD-domänen. Om kundens organisation till exempel har registrerat Azure AD-domännamnet contoso.onmicrosoft.com ersätter du {Tenant} med contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Logga in på sidan Windows-godkännande för virtuella datorer med ett globalt administratörs konto. Om du till exempel har Contoso-organisationen kan ditt konto vara admin@contoso.com eller admin@contoso.onmicrosoft.com .
3. Välj **Acceptera**.
4. Vänta en minut så att Azure AD kan registrera medgivande.
5. Öppna en webbläsare och påbörja det administrativa godkännande flödet till [Windows-klienten för virtuella skriv bord](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Om du hanterar en kund och behöver bevilja administrativt godkännande för kundens katalog, anger du följande URL i webbläsaren och ersätter {Tenant} med kundens namn på Azure AD-domänen. Om kundens organisation till exempel har registrerat Azure AD-domännamnet contoso.onmicrosoft.com ersätter du {Tenant} med contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Logga in på sidan Windows-godkännande för virtuella skriv bord som global administratör, precis som du gjorde i steg 2.
7. Välj **Acceptera**.

## <a name="assign-the-tenantcreator-application-role"></a>Tilldela program rollen TenantCreator

Genom att tilldela en Azure Active Directory användare rollen TenantCreator program kan användaren skapa en Windows-klient för virtuella skriv bord som är associerad med Azure Active Directory instansen. Du måste använda ditt globala administratörs konto för att tilldela TenantCreator-rollen.

Så här tilldelar du program rollen TenantCreator:

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera program rollen TenantCreator. Sök efter och välj **företags program**. Om du arbetar med flera Azure Active Directory klienter, är det en bra idé att öppna en privat webbläsarsession och kopiera och klistra in URL: erna i adress fältet.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av sökning efter företags program i Azure Portal](../media/azure-portal-enterprise-applications.png)

2. Sök efter **Windows Virtual Desktop** i **företags program**. Du ser de två program som du tillhandahöll medgivande för i föregående avsnitt. Av dessa två appar väljer du **virtuellt Windows-skrivbord**.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av Sök resultaten vid sökning efter "Windows Virtual Desktop" i "företags program". Appen med namnet "Windows Virtual Desktop" är markerad.](../media/tenant-enterprise-app.png)

3. Välj **Användare och grupper**. Du kan se att administratören som beviljade medgivande till programmet redan visas i listan med **standard åtkomst** rollen som tilldelats. Detta är inte tillräckligt för att skapa en Windows-klient för virtuella skriv bord. Fortsätt följa dessa instruktioner för att lägga till **TenantCreator** -rollen till en användare.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av de användare och grupper som har tilldelats för att hantera företags programmet "Windows Virtual Desktop". Skärm bilden visar bara en tilldelning, vilket är för "standard åtkomst".](../media/tenant-default-access.png)

4. Välj **Lägg till användare** och välj sedan **användare och grupper** på fliken **Lägg till tilldelning** .
5. Sök efter ett användar konto som ska skapa din Windows-klient för virtuella skriv bord. För enkelhetens skull kan detta vara det globala administratörs kontot.
   - Om du använder en Microsoft Identity-Provider som contosoadmin@live.com eller contosoadmin@outlook.com , kanske du inte kan logga in på det virtuella Windows-skrivbordet. Vi rekommenderar att du använder ett domänbaserat konto som admin@contoso.com eller admin@contoso.onmicrosoft.com i stället.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av att välja en användare att lägga till som "TenantCreator".](../media/tenant-assign-user.png)

   > [!NOTE]
   > Du måste välja en användare (eller en grupp som innehåller en användare) som har sitt ursprung i Azure Active Directory-instansen. Du kan inte välja en gäst (B2B) användare eller ett huvud namn för tjänsten.

6. Välj användar kontot, klicka på knappen **Välj** och välj sedan **tilldela**.
7. På sidan **Windows Virtual Desktop – användare och grupper** kontrollerar du att du ser en ny post med rollen **TenantCreator** tilldelad till den användare som ska skapa Windows-klienten för virtuella skriv bord.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av de användare och grupper som har tilldelats för att hantera företags programmet "Windows Virtual Desktop". Skärm bilden innehåller nu en andra post för en användare som tilldelats rollen "TenantCreator".](../media/tenant-tenant-creator-added.png)

Innan du fortsätter med att skapa en Windows-klient för virtuella skriv bord behöver du två uppgifter:

   - Ditt Azure Active Directory klient-ID (eller **katalog-ID**)
   - Ditt Azure-prenumerations-ID

Så här hittar du Azure Active Directory klient-ID (eller **katalog-ID**):
1. I samma [Azure Portal](https://portal.azure.com) -session söker du efter och väljer **Azure Active Directory**.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av Sök resultaten för "Azure Active Directory" i Azure Portal. Sök resultatet under "tjänster" är markerat.](../media/tenant-search-azure-active-directory.png)

2. Rulla nedåt tills du hittar **Egenskaper** och markera den.
3. Leta efter **katalog-ID** och välj sedan ikonen Urklipp. Klistra in det på en praktisk plats så att du kan använda det senare som **AadTenantId** -värde.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av Azure Active Directory egenskaper. Musen hovrar över urklipps ikonen för "katalog-ID" för att kopiera och klistra in.](../media/tenant-directory-id.png)

Så här hittar du ditt Azure-prenumerations-ID:
1. I samma [Azure Portal](https://portal.azure.com) -session söker du efter och väljer **prenumerationer**.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av Sök resultaten för "Azure Active Directory" i Azure Portal. Sök resultatet för "tjänster" är markerat.](../media/tenant-search-subscription.png)

2. Välj den Azure-prenumeration som du vill använda för att ta emot Windows-aviseringar för virtuella skriv bord.
3. Leta efter **prenumerations-ID** och hovra sedan över värdet tills en urklipps ikon visas. Välj urklipps ikonen och klistra in den på en praktisk plats så att du kan använda den senare som **AzureSubscriptionId** -värde.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av egenskaperna för Azure-prenumerationen. Musen hovrar över urklipps ikonen för "prenumerations-ID" för att kopiera och klistra in.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Skapa en Windows-klient för virtuella skriv bord

Nu när du har beviljat Windows Virtual Desktop-tjänsten behörighet att fråga Azure Active Directory och tilldelat TenantCreator-rollen till ett användar konto, kan du skapa en Windows-klient för virtuella skriv bord.

Börja med att [Hämta och importera Windows-modulen för virtuella skriv bord](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det.

Logga in på Windows Virtual Desktop genom att använda TenantCreator-användarkontot med denna cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Därefter skapar du en ny Windows-klient för virtuella skriv bord som är associerad med Azure Active Directory klient organisationen:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Ersätt värdena för hakparenteser med värden som är relevanta för din organisation och klient organisation. Det namn du väljer för den nya virtuella Windows-klienten för virtuella datorer ska vara globalt unikt. Anta till exempel att du är Windows Virtual Desktop-TenantCreator för Contoso-organisationen. Cmdleten som du kör skulle se ut så här:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Det är en bra idé att tilldela administrativ åtkomst till en andra användare om du någonsin vet att du har låst ditt konto eller om du går på semester och behöver någon för att agera som innehavaradministratör i din frånvaro. Om du vill tilldela administratörs åtkomst till en andra användare kör du följande cmdlet med `<TenantName>` och `<Upn>` ersatt med klient namnet och den andra användarens UPN.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Nästa steg
När du har skapat din klient organisation måste du skapa ett huvud namn för tjänsten i Azure Active Directory och tilldela den en roll i det virtuella Windows-skrivbordet. Tjänstens huvud namn gör att du kan distribuera Windows Virtual Desktop Azure Marketplace-erbjudandet för att skapa en adresspool. Om du vill veta mer om värdar för pooler fortsätter du till självstudien för att skapa en adresspool i Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Skapa tjänstens huvudnamn och rolltilldelningar med PowerShell](create-service-principal-role-powershell.md)
