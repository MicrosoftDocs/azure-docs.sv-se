---
title: Administratörs övertag ande av en ohanterad katalog – Azure AD | Microsoft Docs
description: Hur du tar över ett DNS-domännamn i en ohanterad Azure AD-organisation (skugg klient).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af74967e2de47afeb357e2ac31b1a0ee849ef36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547108"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Ta över en ohanterad katalog som administratör i Azure Active Directory

I den här artikeln beskrivs två sätt att ta över ett DNS-domännamn i en ohanterad katalog i Azure Active Directory (Azure AD). När en självbetjäningsanvändare registrerar sig för en molntjänst som använder Azure AD läggs de till i en ohanterad Azure AD-katalog baserat på e-postdomän. Mer information om självbetjäning eller "viral"-registrering för en tjänst finns i [Vad är självbetjänings registrering för Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Bestäm hur du vill ta över en ohanterad katalog
Under processen för adminövertagande kan du bevisa ägarskapet enligt instruktionerna i [Add a custom domain name to Azure AD](../fundamentals/add-custom-domain.md) (Lägga till ett anpassat domännamn i Azure AD). I nästa avsnitt beskrivs administratörsupplevelsen mer detaljerat, men här följer en sammanfattning:

* När du utför en ["intern" administratörs övertag](#internal-admin-takeover) ande av en ohanterad Azure-katalog läggs du till som global administratör för den ohanterade katalogen. Inga användare, domäner eller tjänstplaner migreras till någon annan katalog som du administrerar.

* När du utför en ["extern" administratörs övertag](#external-admin-takeover) ande av en ohanterad Azure-katalog lägger du till DNS-domännamnet för den ohanterade katalogen i din hanterade Azure-katalog. När du lägger till domännamnet skapas en mappning av användare till resurser i din hanterade Azure-katalog så att användare kan fortsätta att använda tjänsterna utan avbrott. 

## <a name="internal-admin-takeover"></a>Internt övertagande av administratörsrollen

Vissa produkter som innehåller SharePoint och OneDrive, till exempel Microsoft 365, har inte stöd för extern övertag Ande. Om det är ditt scenario, eller om du är en administratör och vill ta över en ohanterad Azure AD-organisation skapa av användare som använde självbetjänings registrering, kan du göra detta med ett internt administratörs övertag Ande.

1. Skapa en användar kontext i den ohanterade organisationen genom att registrera dig för Power BI. För enkelhetens skull antar de här stegen den sökvägen.

2. Öppna [Power BI-webbplatsen](https://powerbi.com) och välj **Starta kostnads fritt**. Ange ett användar konto som använder organisationens domän namn. till exempel `admin@fourthcoffee.xyz` . När du har angett i verifierings koden kontrollerar du din e-postadress för bekräftelse koden.

3. I bekräftelse meddelandet från Power BI väljer du **Ja, det är jag**.

4. Logga in på [Microsoft 365 administrations Center](https://portal.office.com/admintakeover) med Power BI användar kontot. Du får ett meddelande som uppmanar dig att **bli administratör** för domän namnet som redan har verifierats i den ohanterade organisationen. Välj **Ja, jag vill vara administratör**.
  
   ![första skärm bilden för att bli administratör](./media/domains-admin-takeover/become-admin-first.png)
  
5. Lägg till TXT-posten för att bevisa att du äger domän namnet **fourthcoffee.xyz** på din domän namns registrator. I det här exemplet är det GoDaddy.com.
  
   ![Lägg till en TXT-post för domän namnet](./media/domains-admin-takeover/become-admin-txt-record.png)

När DNS-TXT-posterna verifieras hos domän namns registratorn kan du hantera Azure AD-organisationen.

När du har slutfört föregående steg är du nu den globala administratören för den fjärde kaffe organisationen i Microsoft 365. Om du vill integrera domän namnet med dina andra Azure-tjänster kan du ta bort det från Microsoft 365 och lägga till det i en annan hanterad organisation i Azure.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Lägga till domän namnet i en hanterad organisation i Azure AD

1. Öppna [Microsoft 365 administrations Center](https://admin.microsoft.com).
2. Välj fliken **användare** och skapa ett nytt användar konto med ett namn som *användar- \@ fourthcoffeexyz.onmicrosoft.com* som inte använder det anpassade domän namnet. 
3. Se till att det nya användar kontot har globala administratörs behörigheter för Azure AD-organisationen.
4. Öppna fliken **domäner** i Microsoft 365 administrations Center väljer du domän namnet och väljer **ta bort**. 
  
   ![Ta bort domän namnet från Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Om du har några användare eller grupper i Microsoft 365 som refererar till det borttagna domän namnet, måste de byta namn till. onmicrosoft.com-domänen. Om du tvingar bort domän namnet, kommer alla användare att byta namn automatiskt, i det här exemplet till *user \@ fourthcoffeexyz.onmicrosoft.com*.
  
6. Logga in på [administrations centret för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är global administratör för Azure AD-organisationen.
  
7. Välj **anpassade domän namn** och Lägg sedan till domän namnet. Du måste ange DNS-TXT-posterna för att verifiera ägande av domän namnet. 
  
   ![domän verifierad som tillagd i Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Alla användare av Power BI-eller Azure Rights Management-tjänster som har licenser som tilldelats i Microsoft 365 organisation måste spara sina instrument paneler om domän namnet tas bort. De måste logga in med ett användar namn som *användarens \@ fourthcoffeexyz.onmicrosoft.com* i stället för *User \@ fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Externt övertagande av administratörsrollen

Om du redan hanterar en organisation med Azure-tjänster eller Microsoft 365 kan du inte lägga till ett anpassat domän namn om det redan har verifierats i en annan Azure AD-organisation. Från din hanterade organisation i Azure AD kan du dock ta över en ohanterad organisation som en extern administratörs övertag Ande. Den allmänna proceduren följer artikeln [lägga till en anpassad domän i Azure AD](../fundamentals/add-custom-domain.md).

När du verifierar ägarskapet för domän namnet tar Azure AD bort domän namnet från den ohanterade organisationen och flyttar den till din befintliga organisation. Extern administratörs övertag ande av en ohanterad katalog kräver samma validerings process för DNS-TXT som intern administratörs övertag Ande. Skillnaden är att följande även flyttas över med domän namnet:

- Användare
- Prenumerationer
- Licenstilldelningar

### <a name="support-for-external-admin-takeover"></a>Stöd för extern administratörs övertag Ande
Extern administratörs övertag ande stöds av följande onlinetjänster:

- Azure Rights Management
- Exchange Online

Service planerna som stöds är:

- PowerApps kostnads fritt
- PowerFlow kostnads fritt
- RMS för personer
- Microsoft Stream
- Dynamics 365 kostnads fri utvärdering

Extern administratörs övertag ande stöds inte för tjänster som har tjänst planer som omfattar SharePoint, OneDrive eller Skype för företag. till exempel genom en kostnads fri prenumeration på Office. 

Du kan också använda [alternativet **ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) för att ta bort domän namnet från den ohanterade organisationen och verifiera det i den önskade organisationen. 

#### <a name="more-information-about-rms-for-individuals"></a>Mer information om RMS för enskilda användare

För [RMS för enskilda användare](/azure/information-protection/rms-for-individuals), när den ohanterade organisationen finns i samma region som den organisation som du äger, flyttas automatiskt de automatiskt skapade [Azure information Protection organisations nyckeln](/azure/information-protection/plan-implement-tenant-key) och [standardmallarna för skydd](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) över med domän namnet.

Nyckeln och mallarna flyttas inte över när den ohanterade organisationen är i en annan region. Till exempel om den ohanterade organisationen finns i Europa och den organisation som du äger är i Nordamerika.

Även om RMS för enskilda användare har utformats för att ge stöd för Azure AD-autentisering för att öppna skyddat innehåll, hindras inte användare från att också skydda innehåll. Om användarna har skyddat innehåll med prenumerationen RMS för enskilda användare och nyckeln och mallarna inte flyttats över, är det inte tillgängligt efter att domänen har övertagits.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-cmdletar för alternativet ForceTakeover
Du kan se dessa cmdletar som används i [PowerShell-exemplet](#powershell-example).

cmdlet | Användning
------- | -------
`connect-msolservice` | Logga in på din hanterade organisation när du uppmanas till det.
`get-msoldomain` | Visar dina domän namn som är kopplade till den aktuella organisationen.
`new-msoldomain –name <domainname>` | Lägger till domän namnet i organisationen som overifierad (ingen DNS-verifiering har utförts ännu).
`get-msoldomain` | Domän namnet ingår nu i listan över domän namn som är kopplade till din hanterade organisation, men visas som **overifierat**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Innehåller informationen som ska läggas till i nya DNS TXT-poster för domänen (MS = xxxxx). Verifieringen kanske inte sker direkt eftersom det tar lite tid för TXT-posten att spridas, så vänta några minuter innan du överväger alternativet **-ForceTakeover** . 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Om ditt domän namn fortfarande inte verifieras kan du fortsätta med alternativet **-ForceTakeover** . Den verifierar att TXT-posten har skapats och att den avslutar insamlings processen.<li>Alternativet **-ForceTakeover** ska bara läggas till i cmdleten vid Tvingad extern administratörs övertag Ande, till exempel när den ohanterade organisationen har Microsoft 365 Services som blockerar övertagningen.
`get-msoldomain` | I listan domän visas nu domän namnet som **verifierat**.

> [!NOTE]
> Den ohanterade Azure AD-organisationen tas bort 10 dagar efter det att du har utnyttjat alternativet för extern överköps kraft.

### <a name="powershell-example"></a>PowerShell-exempel

1. Anslut till Azure AD med hjälp av de autentiseringsuppgifter som användes för att svara på självbetjänings erbjudandet:
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
5. I ditt offentliga DNS-namnområde skapar du en DNS-TXT-post som innehåller värdet som du kopierade i föregående steg. Namnet på den här posten är namnet på den överordnade domänen, så om du skapar den här resurs posten med hjälp av DNS-rollen från Windows Server lämnar du post namnet tomt och klistrar in värdet i text rutan.
6. Kör cmdleten Confirm-MsolDomain för att verifiera utmaningen:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Exempel:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

En lyckad utmaning går tillbaka till prompten utan ett fel.

## <a name="next-steps"></a>Nästa steg

* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
