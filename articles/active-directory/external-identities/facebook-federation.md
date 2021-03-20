---
title: Lägg till Facebook som en identitets leverantör – Azure AD
description: Federera med Facebook och aktivera externa användare (gäster) för att logga in på dina Azure AD-appar med sina egna Facebook-konton.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d68f83bd042af6612b91807f2adeed54d24bfe01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648629"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Lägg till Facebook som identitets leverantör för externa identiteter

Du kan lägga till Facebook i dina användar flöden för självbetjänings registrering så att användarna kan logga in på dina program med sina egna Facebook-konton. För att användarna ska kunna logga in med Facebook måste du först aktivera självbetjänings [registrering](self-service-sign-up-user-flow.md) för din klient. När du har lagt till Facebook som identitets leverantör konfigurerar du ett användar flöde för programmet och väljer Facebook som ett av inloggnings alternativen.

När du har lagt till Facebook som ett av ditt programs inloggnings alternativ kan användaren på **inloggnings** sidan bara ange det e-postmeddelande som de använder för att logga in på Facebook eller välja **inloggnings alternativ** och välja **Logga in med Facebook**. I båda fallen omdirigeras de till Facebook-inloggnings sidan för autentisering.

![Inloggnings alternativ för Facebook-användare](media/facebook-federation/sign-in-with-facebook-overview.png)

> [!NOTE]
> Användare kan bara använda sina Facebook-konton för att registrera sig via appar med hjälp av självbetjänings registrering och användar flöden. Användare kan inte bjudas in och lösa in sina inbjudningar med ett Facebook-konto.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Skapa en app i Facebook-utvecklarens konsol

Om du vill använda ett Facebook-konto som [identitets leverantör](identity-providers.md)måste du skapa ett program i Facebook-utvecklarens konsol. Om du inte redan har ett Facebook-konto kan du registrera dig på [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Använd följande URL: er i steg 9 och 16 nedan.
> - För **webbplats-URL** anger du adressen till programmet, till exempel `https://contoso.com` .
> - Ange för **giltiga OAuth-omdirigerings-URI: er** `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . Du hittar ditt `<tenant-ID>` i bladet Azure Active Directory översikt.


1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina Facebook-kontoautentiseringsuppgifter.
2. Om du inte redan har gjort det måste du registrera dig som en Facebook-utvecklare. Om du vill göra det väljer du **Kom igång** i det övre högra hörnet på sidan, accepterar Facebook-principer och slutför registrerings stegen.
3. Välj **Mina appar** och sedan **skapa app**.
4. Ange ett **visnings namn** och en giltig **kontakt-e-postadress**.
5. Välj **skapa app-ID**. Detta kan kräva att du accepterar Facebook-plattforms principer och slutför en säkerhets kontroll online.
6. Välj **Inställningar**  >  **Basic**.
7. Välj en **kategori**, till exempel företag och sidor. Det här värdet krävs av Facebook, men används inte för Azure AD.
8. Längst ned på sidan väljer du **Lägg till plattform** och väljer sedan **webbplats**.
9. I **webbplats-URL** anger du lämplig URL (anges ovan).
10. I **Sekretess policyns URL** anger du webb adressen till sidan där du behåller sekretess information för programmet, till exempel `http://www.contoso.com` .
11. Välj **Spara ändringar**.
12. Kopiera värdet för **app-ID** överst på sidan.
13. Välj **Visa** och kopiera värdet för **appens hemlighet**. Du använder båda alternativen för att konfigurera Facebook som en identitets leverantör i din klient organisation. **App Secret** är en viktig säkerhets autentiseringsuppgift.
14. Välj plus tecknet bredvid **produkter** och välj sedan **Konfigurera** under **Facebook-inloggning**.
15. Under **Facebook-inloggning** väljer du **Inställningar**.
16. I **giltiga OAuth-omdirigerings-URI: er** anger du lämplig URL (anges ovan).
17. Välj **Spara ändringar** längst ned på sidan.
18. Om du vill göra ditt Facebook-program tillgängligt för Azure AD väljer du status väljaren längst upp till höger på sidan och **aktiverar det för att göra** programmet offentligt och väljer sedan **Växla läge**. I det här läget bör statusen ändras från **utveckling** till **Live**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurera ett Facebook-konto som en identitets leverantör
Nu ska du ange Facebook-klient-ID och klient hemlighet, antingen genom att ange den i Azure AD-portalen eller med hjälp av PowerShell. Du kan testa Facebook-konfigurationen genom att registrera dig via ett användar flöde i en app som är aktive rad för självbetjänings registrering.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Så här konfigurerar du Facebook Federation i Azure AD-portalen
1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klient.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **alla identitets leverantörer** och välj sedan **Facebook**.
5. För **klient-ID** anger du **app-ID: t** för det Facebook-program som du skapade tidigare.
6. För **klient hemligheten** anger du **appens hemlighet** som du har spelat in.

   ![Skärm bild som visar sidan Lägg till social identitetsprovider](media/facebook-federation/add-social-identity-provider-page.png)

7. Välj **Spara**.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>Konfigurera Facebook Federation med hjälp av PowerShell
1. Installera den senaste versionen av Azure AD PowerShell för Graph module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör följande kommando: `Connect-AzureAD` .
3. Logga in med det hanterade globala administratörs kontot vid inloggnings meddelandet.  
4. Kör följande kommando: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Använd klient-ID och klient hemlighet från den app som du skapade ovan i Facebook Developer Console. Mer information finns i artikeln [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) . 

## <a name="how-do-i-remove-facebook-federation"></a>Hur gör jag för att ta bort Facebook-federationen?
Du kan ta bort installationen av Facebook-federationen. Om du gör det kommer alla användare som har registrerat sig genom användar flöden med sina Facebook-konton inte längre att kunna logga in. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Ta bort Facebook-federationen i Azure AD-portalen: 
1. Gå till [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **externa identiteter**.
3. Välj **alla identitets leverantörer**.
4. Välj snabb menyn (**...**) på **Facebook** -raden och välj sedan **ta bort**. 
5. Bekräfta borttagningen genom att välja **Ja** .

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Ta bort Facebook-federationen med hjälp av PowerShell: 
1. Installera den senaste versionen av Azure AD PowerShell för Graph module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör `Connect-AzureAD`.  
4. Logga in med det hanterade globala administratörs kontot i inloggnings meddelandet.  
5. Ange följande kommando:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Mer information finns i [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 

## <a name="next-steps"></a>Nästa steg

- [Lägga till självbetjänings registrering i en app](self-service-sign-up-user-flow.md)