---
title: Administratörsupptagande av en ohanterad katalog – Azure AD | Microsoft Docs
description: Så här tar du över ett DNS-domännamn i en ohanterad Azure AD-organisation (skuggklientorganisation).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f4645626675ae19a462ac8707e995c3b4045e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739379"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Ta över en ohanterad katalog som administratör i Azure Active Directory

I den här artikeln beskrivs två sätt att ta över ett DNS-domännamn i en ohanterad katalog i Azure Active Directory (Azure AD). När en självbetjäningsanvändare registrerar sig för en molntjänst som använder Azure AD läggs de till i en ohanterad Azure AD-katalog baserat på e-postdomän. Mer information om självbetjäning eller "viral" registrering för en tjänst finns i Vad är registrering med [självbetjäning för Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Bestäm hur du vill ta över en ohanterad katalog
Under processen för adminövertagande kan du bevisa ägarskapet enligt instruktionerna i [Add a custom domain name to Azure AD](../fundamentals/add-custom-domain.md) (Lägga till ett anpassat domännamn i Azure AD). I nästa avsnitt beskrivs administratörsupplevelsen mer detaljerat, men här följer en sammanfattning:

* När du utför ett [”internt” administratörsövertagande](#internal-admin-takeover) av en ohanterad Azure-katalog läggs du till som global administratör för den ohanterade katalogen. Inga användare, domäner eller tjänstplaner migreras till någon annan katalog som du administrerar.

* När du utför ett [”externt” administratörsövertagande](#external-admin-takeover) av en ohanterad Azure-katalog lägger du till DNS-domännamnet på den ohanterade katalogen till din hanterade Azure-katalog. När du lägger till domännamnet skapas en mappning av användare till resurser i din hanterade Azure-katalog så att användare kan fortsätta att använda tjänsterna utan avbrott. 

## <a name="internal-admin-takeover"></a>Internt övertagande av administratörsrollen

Vissa produkter som innehåller SharePoint och OneDrive, till exempel Microsoft 365, stöder inte externt övertagande. Om det är ditt scenario, eller om du är administratör och vill ta över en ohanterad eller "skuggig" Azure AD-organisation som skapas av användare som har använt självbetjäning, kan du göra detta med ett internt administratörsupptagande.

1. Skapa en användarkontext i den ohanterade organisationen genom att registrera dig för Power BI. För att underlätta för exempel förutsätter de här stegen att sökvägen.

2. Öppna Power BI [och](https://powerbi.com) välj **Starta kostnadsfritt.** Ange ett användarkonto som använder domännamnet för organisationen. till exempel `admin@fourthcoffee.xyz` . När du har anger verifieringskoden kontrollerar du din e-postadress för bekräftelsekoden.

3. I bekräftelsemeddelandet från Power BI väljer **du Ja, det är jag**.

4. Logga in på [Administrationscenter för Microsoft 365](https://portal.office.com/admintakeover) med Power BI användarkontot. Du får ett meddelande som  uppmanar dig att bli administratör för domännamnet som redan har verifierats i den ohanterade organisationen. välj **Ja, jag vill vara administratör.**
  
   ![första skärmbilden för Bli administratör](./media/domains-admin-takeover/become-admin-first.png)
  
5. Lägg till TXT-posten för att bevisa att du äger **fourthcoffee.xyz** hos domännamnsregistratorn. I det här exemplet är det GoDaddy.com.
  
   ![Lägga till en txt-post för domännamnet](./media/domains-admin-takeover/become-admin-txt-record.png)

När DNS TXT-posterna har verifierats hos din domännamnsregistrator kan du hantera Azure AD-organisationen.

När du har slutfört föregående steg är du nu global administratör för organisationen Fjärde kaffe i Microsoft 365. Om du vill integrera domännamnet med dina andra Azure-tjänster kan du ta bort det från Microsoft 365 lägga till det i en annan hanterad organisation i Azure.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Lägga till domännamnet i en hanterad organisation i Azure AD

1. Öppna [Administrationscenter för Microsoft 365](https://admin.microsoft.com).
2. Välj **fliken** Användare och skapa ett nytt användarkonto med ett namn *som \@ fourthcoffeexyz.onmicrosoft.com* användare som inte använder det anpassade domännamnet. 
3. Kontrollera att det nya användarkontot har globala administratörsbehörigheter för Azure AD-organisationen.
4. Öppna **fliken** Domäner i Administrationscenter för Microsoft 365, välj domännamnet och välj Ta **bort.** 
  
   ![Ta bort domännamnet från Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Om du har några användare eller grupper i Microsoft 365 som refererar till det borttagna domännamnet måste de byta namn till .onmicrosoft.com domänen. Om du tvingar bort domännamnet får alla användare automatiskt ett nytt namn, i det här exemplet *till fourthcoffeexyz.onmicrosoft.com \@*.
  
6. Logga in på [Azure AD-administrationscentret](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är global administratör för Azure AD-organisationen.
  
7. Välj **Anpassade domännamn och** lägg sedan till domännamnet. Du måste ange DNS TXT-posterna för att verifiera ägarskapet för domännamnet. 
  
   ![domän verifierad som tillagd i Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Alla användare av Power BI eller Azure Rights Management-tjänsten som har licenser tilldelade i Microsoft 365-organisationen måste spara sina instrumentpaneler om domännamnet tas bort. Användaren måste logga in med ett användarnamn som user *\@ fourthcoffeexyz.onmicrosoft.com* istället *för användaren \@ fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Externt övertagande av administratörsrollen

Om du redan hanterar en organisation med Azure-tjänster eller Microsoft 365 kan du inte lägga till ett anpassat domännamn om det redan har verifierats i en annan Azure AD-organisation. Men från din hanterade organisation i Azure AD kan du ta över en ohanterad organisation som ett externt administratörsupptagande. Den allmänna proceduren följer artikeln Lägga [till en anpassad domän i Azure AD.](../fundamentals/add-custom-domain.md)

När du verifierar ägarskapet för domännamnet tar Azure AD bort domännamnet från den ohanterade organisationen och flyttar det till din befintliga organisation. Externt administratörsupptagande av en ohanterad katalog kräver samma DNS TXT-verifieringsprocess som internt administratörsupptagande. Skillnaden är att följande också flyttas över med domännamnet:

- Användare
- Prenumerationer
- Licenstilldelningar

### <a name="support-for-external-admin-takeover"></a>Stöd för externt administratörsupptagande
Externt administratörsupptagande stöds av följande onlinetjänster:

- Azure Rights Management
- Exchange Online

De tjänstplaner som stöds är:

- PowerApps Free
- PowerFlow Free
- RMS för personer
- Microsoft Stream
- Kostnadsfri utvärderingsversion av Dynamics 365

Externt administratörsupptagande stöds inte för tjänster som har tjänstplaner som omfattar SharePoint, OneDrive eller Skype för företag; till exempel via en kostnadsfri Office-prenumeration. 

Om du vill kan du använda [ **alternativet ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) för att ta bort domännamnet från den ohanterade organisationen och verifiera det i den önskade organisationen. 

#### <a name="more-information-about-rms-for-individuals"></a>Mer information om RMS för enskilda användare

För [RMS](/azure/information-protection/rms-for-individuals)för enskilda användare flyttas de automatiskt skapade [Azure Information Protection-organisationsnyckeln](/azure/information-protection/plan-implement-tenant-key) och standardskyddsmallarna dessutom över [](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) med domännamnet när den ohanterade organisationen finns i samma region som den organisation som du äger.

Nyckeln och mallarna flyttas inte över när den ohanterade organisationen finns i en annan region. Till exempel om den ohanterade organisationen finns i Europa och den organisation som du äger finns i Nordamerika.

Även om RMS för enskilda användare har utformats för att stödja Azure AD-autentisering för att öppna skyddat innehåll, hindrar det inte användare från att även skydda innehåll. Om användarna har skyddat innehåll med prenumerationen RMS för enskilda användare och nyckeln och mallarna inte har flyttats över, är innehållet inte tillgängligt efter domänupptagandet.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-cmdlets för ForceTakeover-alternativet
Du kan se dessa cmdlets som används i [PowerShell-exemplet](#powershell-example).

cmdlet | Användning
------- | -------
`connect-msolservice` | När du uppmanas till det loggar du in på din hanterade organisation.
`get-msoldomain` | Visar dina domännamn som är associerade med den aktuella organisationen.
`new-msoldomain –name <domainname>` | Lägger till domännamnet i organisationen som Overifierat (ingen DNS-verifiering har utförts ännu).
`get-msoldomain` | Domännamnet ingår nu i listan över domännamn som är associerade med din hanterade organisation, men visas som **Overifierade.**
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Innehåller information om att placera i en ny DNS TXT-post för domänen (MS=xxxxx). Verifieringen kanske inte sker omedelbart eftersom det tar lite tid för TXT-posten att spridas, så vänta några minuter innan du överväger **alternativet -ForceTakeover.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Om domännamnet fortfarande inte är verifierat kan du fortsätta med alternativet **-ForceTakeover.** Den verifierar att TXT-posten har skapats och startar övertagandeprocessen.<li>Alternativet **-ForceTakeover** bör endast läggas till i cmdleten när ett externt administratörsupptagande framtvingas, till exempel när den ohanterade organisationen har Microsoft 365 tjänster som blockerar övertagandet.
`get-msoldomain` | Domänlistan visar nu domännamnet som **Verifierad.**

> [!NOTE]
> Den ohanterade Azure AD-organisationen tas bort 10 dagar efter att du har tränat alternativet för externt övertagande.

### <a name="powershell-example"></a>PowerShell-exempel

1. Anslut till Azure AD med de autentiseringsuppgifter som användes för att svara på självbetjäningserbjudandet:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Hämta en lista över domäner:
  
   ```powershell
   Get-MsolDomain
   ```
3. Kör cmdleten Get-MsolDomainVerificationDns för att skapa en utmaning:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Exempel:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Kopiera värdet (utmaningen) som returneras från det här kommandot. Exempel:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. I ditt offentliga DNS-namnområde skapar du en DNS txt-post som innehåller värdet som du kopierade i föregående steg. Namnet på den här posten är namnet på den överordnade domänen, så om du skapar den här resursposten med dns-rollen från Windows Server lämnar du postnamnet tomt och klistrar bara in värdet i textrutan.
6. Kör cmdleten Confirm-MsolDomain för att verifiera utmaningen:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Exempel:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

En lyckad utmaning returnerar dig till prompten utan fel.

## <a name="next-steps"></a>Nästa steg

* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
