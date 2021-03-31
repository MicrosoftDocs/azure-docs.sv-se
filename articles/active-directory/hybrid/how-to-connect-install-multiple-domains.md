---
title: Azure AD Connect flera domäner
description: I det här dokumentet beskrivs hur du konfigurerar och konfigurerar flera domäner på toppnivå med Microsoft 365 och Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53a0da5b5db21c9a543d39d1b252b0b4c64e2a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306369"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Stöd för flera domäner för federering med Azure AD
Följande dokumentation ger vägledning om hur du använder flera toppnivå domäner och under domäner när du federerar med Microsoft 365-eller Azure AD-domäner.

## <a name="multiple-top-level-domain-support"></a>Stöd för flera domäner på översta nivån
Att federera flera domäner på toppnivå med Azure AD kräver ytterligare konfiguration som inte krävs vid federering med en toppnivå domän.

När en domän är federerad med Azure AD, anges flera egenskaper för domänen i Azure.  En viktig sådan är IssuerUri.  Den här egenskapen är en URI som används av Azure AD för att identifiera den domän som token är associerad med.  URI: n behöver inte matcha något, men den måste vara en giltig URI.  Som standard ställer Azure AD in URI till värdet för Federations tjänst identifieraren i din lokala AD FS konfiguration.

> [!NOTE]
> Federations tjänstens identifierare är en URI som unikt identifierar en Federations tjänst.  Federations tjänsten är en instans av AD FS som fungerar som säkerhetstokentjänst.
>
>

Du kan visa IssuerUri med hjälp av PowerShell-kommandot `Get-MsolDomainFederationSettings -DomainName <your domain>` .

![Skärm bild som visar resultat när du har angett kommandot "Get-MsolDomainFederationSettings" i PowerShell.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Ett problem uppstår när du lägger till fler än en domän på den översta nivån.  Anta till exempel att du har konfigurerat federation mellan Azure AD och din lokala miljö.  Domänen bmcontoso.com används för det här dokumentet.  Nu har en andra toppnivå domän bmfabrikam.com lagts till.

![En skärm bild som visar flera toppnivå domäner](./media/how-to-connect-install-multiple-domains/domains.png)

När du försöker konvertera bmfabrikam.com-domänen till federerad, uppstår ett fel.  Orsaken är att Azure AD har en begränsning som inte tillåter att egenskapen IssuerUri har samma värde för fler än en domän.  

![Skärm bild som visar ett Federations fel i PowerShell.](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain-parameter
För att undvika den här begränsningen måste du lägga till en annan IssuerUri, som kan göras med hjälp av `-SupportMultipleDomain` parametern.  Den här parametern används med följande cmdletar:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Den här parametern gör att Azure AD konfigurerar IssuerUri så att den baseras på domänens namn.  IssuerUri är unika för alla kataloger i Azure AD.  Med parametern kan PowerShell-kommandot slutföras.

![Skärm bild som visar att PowerShell-kommandot har slutförts.](./media/how-to-connect-install-multiple-domains/convert.png)

När du tittar på inställningarna för bmfabrikam.com-domänen kan du se följande:

![Skärm bild som visar inställningarna för domänen "bmfabrikam.com".](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` ändrar inte de andra slut punkterna, som fortfarande är konfigurerade så att de pekar på Federations tjänsten på adfs.bmcontoso.com.

En annan sak som garanterar att det `-SupportMultipleDomain` AD FS systemet innehåller rätt Issuer-värde i token som utfärdats för Azure AD. Det här värdet anges genom att ta domän delen av användarens UPN och ange det som domän i IssuerUri, t. ex. https://{UPN-suffix}/ADFS/Services/Trust.

Därför används IssuerUri-elementet i användarens token för att hitta domänen i Azure AD under autentisering till Azure AD eller Microsoft 365. Om det inte går att hitta en matchning, kommer autentiseringen att Miss Miss det.

Om en användares UPN t. ex. är bsimon@bmcontoso.com , kommer IssuerUri-elementet i token, AD FS problem, att ställas in på `http://bmcontoso.com/adfs/services/trust` . Det här elementet matchar Azure AD-konfigurationen och autentiseringen kommer att lyckas.

Följande är den anpassade anspråks regeln som implementerar den här logiken:

```
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));
```


> [!IMPORTANT]
> För att kunna använda växeln-SupportMultipleDomain när du försöker lägga till nya eller konvertera redan befintliga domäner måste ditt federerade förtroende redan ha kon figurer ATS för att stödja dem.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Så här uppdaterar du förtroendet mellan AD FS och Azure AD
Om du inte konfigurerade det federerade förtroendet mellan AD FS och din instans av Azure AD, kan du behöva återskapa det här förtroendet.  Anledningen är att när den ursprungligen konfigureras utan `-SupportMultipleDomain` parametern, anges IssuerUri med standardvärdet.  I skärm bilden nedan kan du se att IssuerUri är inställt på `https://adfs.bmcontoso.com/adfs/services/trust` .

Om du har lagt till en ny domän i Azure AD-portalen och sedan försöker konvertera den med `Convert-MsolDomaintoFederated -DomainName <your domain>` , får du följande fel meddelande.

![Skärm bild som visar ett Federations fel i PowerShell när du försöker konvertera en ny domän med kommandot "Convert-MsolDomaintoFederated".](./media/how-to-connect-install-multiple-domains/trust1.png)

Om du försöker lägga till `-SupportMultipleDomain` växeln visas följande fel meddelande:

![Skärm bild som visar ett Federations fel när du har lagt till växeln "-SupportMultipleDomain".](./media/how-to-connect-install-multiple-domains/trust2.png)

Att bara försöka köra `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` på den ursprungliga domänen resulterar också i ett fel.

![Federations fel](./media/how-to-connect-install-multiple-domains/trust3.png)

Följ stegen nedan om du vill lägga till ytterligare en domän på den översta nivån.  Om du redan har lagt till en domän och inte använde `-SupportMultipleDomain` parametern börjar du med stegen för att ta bort och uppdatera den ursprungliga domänen.  Om du ännu inte har lagt till en toppnivå domän kan du börja med stegen för att lägga till en domän med PowerShell för Azure AD Connect.

Använd följande steg för att ta bort Microsoft Online-förtroende och uppdatera din ursprungliga domän.

1. Öppna **AD FS hantering** på AD FS Federations servern.
2. Till vänster expanderar du **förtroende relationer** och **förtroenden för förlitande part**
3. Till höger tar du bort posten **Microsoft Office 365 identitets plattform** .
   ![Ta bort Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. På en dator där [Azure Active Directory-modulen för Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) är installerad på den kör du följande: `$cred=Get-Credential` .  
5. Ange användar namn och lösen ord för en global administratör för den Azure AD-domän som du federerar med.
6. I PowerShell anger du `Connect-MsolService -Credential $cred`
7. I PowerShell anger du `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain` .  Den här uppdateringen gäller för den ursprungliga domänen.  Så här använder du ovanstående domäner:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Använd följande steg för att lägga till den nya toppnivå domänen med hjälp av PowerShell

1. På en dator där [Azure Active Directory-modulen för Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) är installerad på den kör du följande: `$cred=Get-Credential` .  
2. Ange användar namn och lösen ord för en global administratör för den Azure AD-domän som du federerar med
3. I PowerShell anger du `Connect-MsolService -Credential $cred`
4. I PowerShell anger du `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Använd följande steg för att lägga till den nya domänen på den översta nivån med hjälp av Azure AD Connect.

1. Starta Azure AD Connect från Skriv bordet eller Start-menyn
2. Välj "Lägg till ytterligare en Azure AD-domän"- ![ skärm bild som visar sidan "ytterligare aktiviteter" med "Lägg till ytterligare en Azure AD-domän" vald.](./media/how-to-connect-install-multiple-domains/add1.png)
3. Ange dina autentiseringsuppgifter för Azure AD och Active Directory
4. Välj den andra domän som du vill konfigurera för federation.
   ![Lägg till ytterligare en Azure AD-domän](./media/how-to-connect-install-multiple-domains/add2.png)
5. Klicka på Installera

### <a name="verify-the-new-top-level-domain"></a>Verifiera den nya domänen på den översta nivån
Med hjälp av PowerShell-kommandot `Get-MsolDomainFederationSettings -DomainName <your domain>` kan du Visa den uppdaterade IssuerUri.  Skärm bilden nedan visar Federations inställningarna som uppdaterades på den ursprungliga domänen `http://bmcontoso.com/adfs/services/trust`

![Skärm bild som visar de Federations inställningar som uppdaterats på den ursprungliga domänen.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Och IssuerUri på den nya domänen har angetts till `https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Stöd för under domäner
När du lägger till en under domän, på grund av hur Azure AD-hanterade domäner, kommer den att ärva inställningarna för den överordnade.  Därför måste IssuerUri matcha de överordnade.

Detta kan till exempel anta att jag har bmcontoso.com och sedan lägger till corp.bmcontoso.com.  IssuerUri för en användare från corp.bmcontoso.com måste vara **`http://bmcontoso.com/adfs/services/trust`** .  Standard regeln som implementeras ovan för Azure AD kommer dock att generera en token med en utfärdare som **`http://corp.bmcontoso.com/adfs/services/trust`** . Det kommer inte att matcha domänens obligatoriska värde och autentiseringen kommer att Miss kommer.

### <a name="how-to-enable-support-for-subdomains"></a>Så här aktiverar du stöd för under domäner
För att undvika detta beteende måste den AD FS förlitande partens förtroende för Microsoft Online uppdateras.  Om du vill göra detta måste du konfigurera en anpassad anspråks regel så att den tar bort eventuella under domäner från användarens UPN-suffix när det anpassade utfärdarens värde skapas.

Följande anspråk gör detta:

```    
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
```

[!NOTE]
Det sista antalet i den reguljära uttrycks uppsättningen är hur många överordnade domäner som finns i rot domänen. Här bmcontoso.com används, så två överordnade domäner krävs. Om tre överordnade domäner skulle behållas (t. ex.: corp.bmcontoso.com) skulle numret ha varit tre. Ett intervall kan anges, men matchningen görs alltid för att matcha det maximala antalet domäner. " {2,3} " kommer att matcha två till tre domäner (t. ex.: bmfabrikam.com och Corp.bmcontoso.com).

Använd följande steg för att lägga till ett anpassat anspråk för att stödja under domäner.

1. Öppna AD FS hantering
2. Högerklicka på Microsoft Online RP-förtroende och välj Redigera anspråks regler
3. Välj regeln för tredje anspråk och Ersätt ![ Redigera anspråk](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Ersätt aktuellt anspråk:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   ```
    med

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
   ```

    ![Ersätt anspråk](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klicka på OK.  Klicka på Applicera.  Klicka på OK.  Stäng AD FS-hantering.

## <a name="next-steps"></a>Nästa steg
Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).

Lär dig mer om dessa funktioner, som aktiverades med installationen: [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md), [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](how-to-connect-sync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).