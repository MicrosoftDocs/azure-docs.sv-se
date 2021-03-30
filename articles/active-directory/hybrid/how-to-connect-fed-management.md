---
title: Azure AD Connect-AD FS hantering och anpassning | Microsoft Docs
description: AD FS hantering med Azure AD Connect och anpassning av användar AD FS inloggnings upplevelse med Azure AD Connect och PowerShell.
keywords: AD FS, ADFS, AD FS hantering, AAD Connect, Connect, inloggning, AD FS anpassning, reparera förtroende, M365, Federation, förlitande part
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0c8c40e370579100c562e0289c97e3f5ce4236
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91274120"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Hantera och anpassa Active Directory Federation Services (AD FS) med Azure AD Connect
Den här artikeln beskriver hur du hanterar och anpassar Active Directory Federation Services (AD FS) (AD FS) med hjälp av Azure Active Directory (Azure AD) Connect. Den innehåller också andra vanliga AD FS uppgifter som du kan behöva utföra för en fullständig konfiguration av en AD FS server grupp.

| Avsnitt | Vad det täcker |
|:--- |:--- |
| **Hantera AD FS** | |
| [Reparera förtroendet](#repairthetrust) |Så här reparerar du Federations förtroendet med Microsoft 365. |
| [Federera med Azure AD med hjälp av alternativt inloggnings-ID](#alternateid) | Konfigurera Federation med hjälp av alternativt inloggnings-ID  |
| [Lägg till en AD FS-server](#addadfsserver) |Expandera en AD FS server grupp med ytterligare en AD FS server. |
| [Lägg till en AD FS Webbprogramproxy](#addwapserver) |Hur du expanderar en AD FS server grupp med en ytterligare Server för webbprogramproxy (WAP). |
| [Lägg till en federerad domän](#addfeddomain) |Så här lägger du till en federerad domän. |
| [Uppdatera TLS/SSL-certifikatet](how-to-connect-fed-ssl-update.md)| Så här uppdaterar du TLS/SSL-certifikatet för en AD FS server grupp. |
| **Anpassa AD FS** | |
| [Lägg till en anpassad företags logo typ eller bild](#customlogo) |Anpassa en AD FS inloggnings sida med en företags logo typ och en bild. |
| [Lägg till en inloggnings Beskrivning](#addsignindescription) |Så här lägger du till en beskrivning av inloggnings sidan. |
| [Ändra AD FS anspråks regler](#modclaims) |Ändra AD FS-anspråk för olika Federations scenarier. |

## <a name="manage-ad-fs"></a>Hantera AD FS
Du kan utföra olika AD FS-relaterade uppgifter i Azure AD Connect med minimal användar åtgärd med hjälp av guiden Azure AD Connect. När du har installerat Azure AD Connect genom att köra guiden kan du köra guiden igen för att utföra ytterligare åtgärder.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Reparera förtroendet 
Du kan använda Azure AD Connect för att kontrol lera den aktuella hälsan för AD FS och Azure AD-förtroende och vidta lämpliga åtgärder för att reparera förtroendet. Följ dessa steg om du vill reparera Azure AD och AD FS förtroende.

1. Välj **Reparera AAD och ADFS Trust** i listan över ytterligare aktiviteter.
   ![Reparera AAD-och ADFS-förtroende](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. På sidan **Anslut till Azure AD** anger du dina autentiseringsuppgifter som global administratör för Azure AD och klickar på **Nästa**.
   ![Skärm bild som visar sidan "Anslut till Azure AD" med exempel uppgifter som angetts.](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Ange autentiseringsuppgifterna för domän administratören på sidan **autentiseringsuppgifter för fjärråtkomst** .

   ![Skärm bild som visar sidan "autentiseringsuppgifter för fjärråtkomst" med exempel uppgifter som angetts.](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    När du har klickat på **nästa** Azure AD Connect kontrollerar om det finns certifikat hälsa och eventuella problem visas.

    ![Tillstånd för certifikat](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    Sidan **klar att konfigurera** visar en lista över åtgärder som ska utföras för att reparera förtroendet.

    ![Skärm bild som visar sidan "klar att konfigurera" med en lista över åtgärder.](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Klicka på **Installera** för att reparera förtroendet.

> [!NOTE]
> Azure AD Connect kan bara reparera eller agera på certifikat som är självsignerade. Azure AD Connect kan inte reparera certifikat från tredje part.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Federera med Azure AD med hjälp av AlternateID 
Vi rekommenderar att du använder det lokala UPN-namnet (User Principal Name) och moln användarens huvud namn. Om lokala UPN använder en icke-dirigerbara domän (ex. Contoso. local) eller kan inte ändras på grund av lokala program beroenden, rekommenderar vi att du ställer in alternativt inloggnings-ID. Alternativt inloggnings-ID kan du konfigurera en inloggnings upplevelse där användare kan logga in med ett annat attribut än deras UPN, t. ex. e-post. Alternativet för användarens huvud namn i Azure AD Connect standardvärdet för attributet userPrincipalName i Active Directory. Om du väljer något annat attribut för användarens huvud namn och federerar med hjälp av AD FS konfigureras Azure AD Connect AD FS för alternativt inloggnings-ID. Ett exempel på att välja ett annat attribut för användarens huvud namn visas nedan:

![Val av attribut för alternativ-ID](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurering av alternativt inloggnings-ID för AD FS består av två huvud steg:
1. **Konfigurera rätt uppsättning utfärdande anspråk**: reglerna för utfärdande-anspråk i Azure AD-förtroendet för förlitande part ändras till att använda det valda userPrincipalName-attributet som användarens alternativa ID.
2. **Aktivera alternativt inloggnings-ID i AD FS konfigurationen**: AD FS konfigurationen uppdateras så att AD FS kan söka efter användare i relevanta skogar med hjälp av det alternativa ID: t. Den här konfigurationen stöds för AD FS på Windows Server 2012 R2 (med KB2919355) eller senare. Om AD FS-servrarna är 2012 R2 kontrollerar Azure AD Connect om det finns tillräckligt med KB. Om KB inte identifieras visas en varning efter att konfigurationen är klar, enligt nedan:

    ![Varning för saknade KB på 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Om du vill åtgärda konfigurationen om det saknas KB installerar du den nödvändiga [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) och reparerar sedan förtroendet med hjälp av [repair AAD och AD FS Trust](#repairthetrust).

> [!NOTE]
> Mer information om alternateID och steg för manuell konfiguration finns i [Konfigurera alternativt inloggnings-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Lägg till en AD FS-server 

> [!NOTE]
> För att lägga till en AD FS-server kräver Azure AD Connect PFX-certifikatet. Därför kan du bara utföra den här åtgärden om du har konfigurerat AD FS-servergruppen med hjälp av Azure AD Connect.

1. Välj **distribuera ytterligare en Federations Server** och klicka på **Nästa**.

   ![Ytterligare Federations Server](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. På sidan **Anslut till Azure AD** anger du dina autentiseringsuppgifter som global administratör för Azure AD och klickar på **Nästa**.

   ![Skärm bild som visar sidan "Anslut till Azure AD" med exempel uppgifter som angetts.](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Ange autentiseringsuppgifter för domän administratören.

   ![Autentiseringsuppgifter för domän administratör](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect frågar efter lösen ordet för den PFX-fil som du angav när du konfigurerade den nya AD FS-gruppen med Azure AD Connect. Klicka på **Ange lösen ord** för att ange lösen ordet för PFX-filen.

   ![Skärm bild som visar sidan "ange SSL-certifikat" där fönstret "certifikat lösen ord" är öppet.](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Skärm bild som visar sidan "ange SSL-certifikat" efter att ett lösen ord har angetts för PFX-filen.](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. På sidan **AD FS servrar** anger du det Server namn eller den IP-adress som ska läggas till i AD FS-gruppen.

   ![AD FS servrar](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klicka på **Nästa** och gå igenom den sista **konfigurations** sidan. När Azure AD Connect har lagt till servrarna i AD FS-gruppen får du alternativet att verifiera anslutningen.

   ![Skärm bild som visar sidan "klar att konfigurera" med en lista över åtgärder som ska utföras när du klickar på "installera".](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Skärm bild som visar sidan "installationen har slutförts" och meddelandet "intranäts konfigurationen har verifierats" visas. ](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Lägg till en AD FS WAP-server 

> [!NOTE]
> För att lägga till en WAP-server kräver Azure AD Connect PFX-certifikatet. Därför kan du bara utföra den här åtgärden om du har konfigurerat AD FS-servergruppen med hjälp av Azure AD Connect.

1. Välj **distribuera** Webbprogramproxy i listan över tillgängliga uppgifter.

   ![Distribuera Webbprogramproxy](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Ange Azures globala administratörs behörighet.

   ![Skärm bild som visar sidan "Anslut till Azure AD" med ett exempel på användar namn och lösen ord angivet.](./media/how-to-connect-fed-management/wapserver2.PNG)

3. På sidan **ange SSL-certifikat** anger du lösen ordet för den PFX-fil som du angav när du konfigurerade AD FS-servergruppen med Azure AD Connect.
   ![Certifikat lösen ord](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Ange TLS/SSL-certifikat](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Lägg till den server som ska läggas till som en WAP-server. Eftersom WAP-servern kanske inte är ansluten till domänen ber guiden om administrativa autentiseringsuppgifter till servern som läggs till.

   ![Autentiseringsuppgifter för administrativ Server](./media/how-to-connect-fed-management/WapServer5.PNG)

5. På sidan **autentiseringsuppgifter för proxyserver** anger du administrativa autentiseringsuppgifter för att konfigurera proxy-förtroendet och få åtkomst till den primära servern i AD FS server gruppen.

   ![Autentiseringsuppgifter för proxy förtroende](./media/how-to-connect-fed-management/WapServer6.PNG)

6. På sidan **klar att konfigurera** visar guiden en lista över åtgärder som ska utföras.

   ![Skärm bild som visar sidan "klar att konfigurera" med en lista över åtgärder som ska utföras.](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Slutför konfigurationen genom att klicka på **Installera** . När konfigurationen är klar ger guiden dig möjlighet att verifiera anslutningen till servrarna. Klicka på **Verifiera** för att kontrol lera anslutningen.

   ![Installationen är klar](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Lägg till en federerad domän 

Det är enkelt att lägga till en domän som ska federerades med Azure AD med hjälp av Azure AD Connect. Azure AD Connect lägger till domänen för Federation och ändrar anspråks reglerna så att de återspeglar utfärdaren korrekt om du har flera domäner som är federerade med Azure AD.

1. Om du vill lägga till en federerad domän väljer du aktiviteten **Lägg till ytterligare en Azure AD-domän**.

   ![Ytterligare Azure AD-domän](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. På nästa sida i guiden anger du den globala administratörens autentiseringsuppgifter för Azure AD.

   ![Anslut till Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Ange autentiseringsuppgifterna för domän administratören på sidan **autentiseringsuppgifter för fjärråtkomst** .

   ![Autentiseringsuppgifter för fjärråtkomst](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. På nästa sida visar guiden en lista över Azure AD-domäner som du kan federera din lokala katalog med. Välj domän i listan.

   ![Azure AD-domän](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    När du har valt domän ger guiden lämplig information om ytterligare åtgärder som ska vidtas och effekten av konfigurationen. I vissa fall, om du väljer en domän som inte har verifierats i Azure AD, innehåller guiden information som hjälper dig att verifiera domänen. Mer information finns i [lägga till ett anpassat domän namn i Azure Active Directory](../fundamentals/add-custom-domain.md) .

5. Klicka på **Nästa**. Sidan **klar att konfigurera** visar en lista över åtgärder som Azure AD Connect utföra. Slutför konfigurationen genom att klicka på **Installera** .

   ![Klart att konfigurera](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Användare från den tillagda federerade domänen måste synkroniseras innan de kan logga in på Azure AD.

## <a name="ad-fs-customization"></a>AD FS-anpassning
I följande avsnitt finns information om några vanliga uppgifter som du kan behöva utföra när du anpassar AD FS inloggnings sida.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Lägg till en anpassad företags logo typ eller bild 
Om du vill ändra logo typen för företaget som visas på **inloggnings** sidan använder du följande Windows PowerShell-cmdlet och syntax.

> [!NOTE]
> De rekommenderade måtten för logo typen är 260 x 35 \@ 96 dpi med en fil storlek som inte är större än 10 kB.

```azurepowershell-interactive
Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}
```

> [!NOTE]
> Parametern *TargetName* måste anges. Standard temat som släpps med AD FS heter default.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Lägg till en inloggnings Beskrivning 
Om du vill lägga till en beskrivning av inloggnings sidan på **inloggnings sidan** använder du följande Windows PowerShell-cmdlet och syntax.

```azurepowershell-interactive
Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"
```

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Ändra AD FS anspråks regler 
AD FS har stöd för ett omfattande anspråks språk som du kan använda för att skapa anpassade anspråks regler. Mer information finns i [rollen för anspråks regel språket](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807118(v=ws.11)).

I följande avsnitt beskrivs hur du kan skriva anpassade regler för vissa scenarier som relaterar till Azure AD och AD FS Federation.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Oåterkalleligt ID villkorligt för ett värde som finns i attributet
Med Azure AD Connect kan du ange ett attribut som ska användas som käll ankare när objekt synkroniseras till Azure AD. Om värdet i det anpassade attributet inte är tomt kanske du vill utfärda ett oåterkalleligt ID-anspråk.

Du kan till exempel välja **MS-DS-consistencyguid** som attribut för käll ankaret och utfärda **ImmutableID** som **MS-DS-consistencyguid** om attributet har ett värde mot det. Om det inte finns något värde gentemot attributet ska du utfärda **objectGUID** som det oföränderliga ID: t. Du kan skapa en uppsättning anpassade anspråks regler enligt beskrivningen i följande avsnitt.

**Regel 1: fråga attribut**

```claim-rule-language
c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
=> add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);
```

I den här regeln ska du fråga värdena för **MS-DS-consistencyguid** och **objectGUID** för användaren från Active Directory. Ändra lagrings namnet till ett lämpligt Arkiv namn i din AD FS-distribution. Ändra även anspråks typen till en lämplig anspråks typ för din Federation, enligt definitionen för **objectGUID** och **MS-DS-consistencyguid**.

Genom att använda **Lägg till** och inte **problem** undviker du att lägga till ett utgående problem för entiteten och kan använda värdena som mellanliggande värden. Du kommer att utfärda anspråket i en senare regel när du har fastställt vilket värde som ska användas som oföränderligt ID.

**Regel 2: kontrol lera om ms-DS-consistencyguid finns för användaren**

```claim-rule-language
NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
=> add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");
```

Den här regeln definierar en temporär flagga som kallas **idflag** och som är inställd på **useguid** om det inte finns någon **MS-DS-consistencyguid** ifylld för användaren. Logiken bakom detta är det faktum att AD FS inte tillåter tomma anspråk. När du lägger till anspråk `http://contoso.com/ws/2016/02/identity/claims/objectguid` och `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` i regel 1 får du bara ett **msdsconsistencyguid** -anspråk om värdet är ifyllt för användaren. Om den inte har fyllts i AD FS se att den kommer att ha ett tomt värde och släpper det direkt. Alla objekt kommer att ha **objectGUID**, så detta anspråk kommer alltid att finnas där när regel 1 har körts.

**Regel 3: utfärda ms-DS-consistencyguid som oföränderligt ID om det finns**

```claim-rule-language
c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);
```

Det här är en **implicit kontroll** . Om värdet för anspråket finns kan du utfärda det som det oföränderliga ID: t. I föregående exempel används **NameIdentifier** -anspråket. Du måste ändra detta till lämplig anspråks typ för det oåterkalleliga ID: t i din miljö.

**Regel 4: utfärda objectGuid som oföränderligt ID om ms-DS-consistencyGuid inte finns**

```claim-rule-language
c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
&& c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);
```

I den här regeln kontrollerar du bara den temporära flaggan **idflag**. Du bestämmer om du vill utfärda anspråk baserat på dess värde.

> [!NOTE]
> Ordningen för dessa regler är viktig.

### <a name="sso-with-a-subdomain-upn"></a>SSO med ett under domänens UPN

Du kan lägga till fler än en domän som ska sammanfattas med hjälp av Azure AD Connect, enligt beskrivningen i [Lägg till en ny federerad domän](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect version 1.1.553.0 och den senaste skapar rätt anspråks regel för issuerID automatiskt. Om du inte kan använda Azure AD Connect version 1.1.553.0 eller senare, rekommenderar vi att verktyget [Azure AD RPT anspråks regler](https://aka.ms/aadrptclaimrules) används för att generera och ange rätt anspråks regler för det förlitande parts förtroendet för Azure AD.

## <a name="next-steps"></a>Nästa steg
Läs mer om [användar inloggnings alternativ](plan-connect-user-signin.md).