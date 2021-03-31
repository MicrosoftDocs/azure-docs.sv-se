---
title: 'Azure AD Connect: utforma koncept | Microsoft Docs'
description: I det här avsnittet beskrivs vissa design områden för implementering
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: baa03499cc11bda24ead986dd64621572484cbb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279660"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: utforma begrepp
Syftet med det här dokumentet är att beskriva områden som måste ses över under implementeringen av Azure AD Connect. Det här dokumentet är ett djupgående att ta del av vissa områden och dessa begrepp beskrivs kortfattat i andra dokument.

## <a name="sourceanchor"></a>sourceAnchor
Attributet sourceAnchor definieras som ett attribut som inte kan ändras *under ett objekts livs längd*. Den identifierar ett unikt objekt som samma objekt lokalt och i Azure AD. Attributet kallas även **immutableId** och de två namnen används utbytbara.

Ordet "kan inte ändras" är viktigt för det här dokumentet. Eftersom det här attributets värde inte kan ändras efter att det har angetts är det viktigt att välja en design som har stöd för ditt scenario.

Attributet används i följande scenarier:

* När en ny Sync-databasmotor skapas, eller återskapas efter ett haveri beredskaps scenario, länkar det här attributet befintliga objekt i Azure AD med objekt lokalt.
* Om du flyttar från en molnbaserad identitet till en synkroniserad identitets modell tillåter detta attribut objekt till "hård matchning" befintliga objekt i Azure AD med lokala objekt.
* Om du använder Federation används det här attributet tillsammans med **userPrincipalName** i anspråket för att unikt identifiera en användare.

I den här artikeln pratar bara om sourceAnchor när de relateras till användare. Samma regler gäller för alla objekt typer, men det är bara för användare som det här problemet vanligt vis är ett problem.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Välja ett lämpligt sourceAnchor-attribut
Attributvärdet måste följa följande regler:

* Färre än 60 tecken
  * Tecken som inte är a-z, A-Z eller 0-9 kodas och räknas som tre tecken
* Innehåller inget specialtecken: &#92;! # $% & * +/=? ^ &#96; {} | ~ < >  () '; : , [ ] " \@ _
* Måste vara globalt unikt
* Måste vara antingen en sträng, ett heltal eller en binär
* Ska inte baseras på användarens namn eftersom de kan ändra
* Bör inte vara Skift läges känslig och undvika värden som kan variera beroende på fall
* Ska tilldelas när objektet skapas

Om den valda sourceAnchor inte är av typen sträng Azure AD Connect Base64Encode attributvärdet så att inga specialtecken visas. Om du använder en annan Federations server än ADFS kontrollerar du att servern också kan Base64Encode attributet.

Attributet sourceAnchor är Skift läges känsligt. Värdet "JohnDoe" är inte detsamma som "JohnDoe". Men du bör inte ha två olika objekt med en skillnad i fallet.

Om du har en lokal skog lokalt, är attributet du ska använda **objectGUID**. Detta är även det attribut som används när du använder Express inställningar i Azure AD Connect och även attributet som används av DirSync.

Om du har flera skogar och inte flyttar användare mellan skogar och domäner, är **objectGUID** ett lämpligt attribut att använda även i det här fallet.

Om du flyttar användare mellan skogar och domäner måste du hitta ett attribut som inte ändras eller som kan flyttas till användarna under flytten. En rekommenderad metod är att införa ett syntetiskt attribut. Ett attribut som kan innehålla något som liknar ett GUID är lämpligt. När ett objekt skapas skapas och stämplas ett nytt GUID för användaren. En anpassad Synkroniseringsregel kan skapas i Synkroniseringsmotorn för att skapa det här värdet baserat på **objectGUID** och uppdatera det valda attributet i lägger till. När du flyttar objektet ser du till att även kopiera innehållet för det här värdet.

En annan lösning är att välja ett befintligt attribut som du vet inte ändras. Ofta använda attribut är **Anställningsnr**. Om du funderar på ett attribut som innehåller bokstäver ser du till att det inte går att ändra i skift läget (versaler eller gemener). Felaktiga attribut som inte ska användas inkluderar attributen med namnet på användaren. I en äktenskaps-eller äktenskaps skillnad förväntas namnet ändras, vilket inte är tillåtet för det här attributet. Detta är också en orsak till varför attribut som **userPrincipalName**, **mail** och **targetAddress** inte är lika möjliga att välja i installations guiden för Azure AD Connect. Dessa attribut innehåller också " \@ "-symbolen, som inte tillåts i sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Ändra attributet sourceAnchor
Attributvärdet sourceAnchor kan inte ändras efter att objektet har skapats i Azure AD och identiteten har synkroniserats.

Av den anledningen gäller följande begränsningar för Azure AD Connect:

* SourceAnchor-attributet kan bara anges under den första installationen. Om du kör installations guiden igen, är det här alternativet skrivskyddat. Om du behöver ändra den här inställningen måste du avinstallera och installera om.
* Om du installerar en annan Azure AD Connect-Server måste du välja samma sourceAnchor-attribut som tidigare använt. Om du tidigare har använt DirSync och flyttat till Azure AD Connect måste du använda **objectGUID** eftersom det är det attribut som används av DirSync.
* Om värdet för sourceAnchor ändras efter att objektet har exporter ATS till Azure AD genererar Azure AD Connect Sync ett fel och tillåter inte några fler ändringar av objektet innan problemet har åtgärd ATS och sourceAnchor ändras i käll katalogen igen.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Använda MS-DS-ConsistencyGuid som sourceAnchor
Som standard använder Azure AD Connect (version 1.1.486.0 och äldre) objectGUID som sourceAnchor-attribut. ObjectGUID har genererats av systemet. Du kan inte ange dess värde när du skapar lokala AD-objekt. Som det beskrivs i avsnitt [sourceAnchor](#sourceanchor)finns det scenarier där du måste ange sourceAnchor-värdet. Om scenarierna gäller för dig måste du använda ett konfigurerbart AD-attribut (till exempel ms-DS-ConsistencyGuid) som sourceAnchor-attribut.

Azure AD Connect (version 1.1.524.0 och senare) gör nu det lättare att använda MS-DS-ConsistencyGuid som sourceAnchor-attribut. När du använder den här funktionen konfigurerar Azure AD Connect automatiskt reglerna för synkronisering till:

1. Använd ms-DS-ConsistencyGuid som sourceAnchor-attribut för användar objekt. ObjectGUID används för andra objekt typer.

2. För alla lokala AD-användarkonton vars ms-DS-ConsistencyGuid-attribut inte har fyllts i, skriver Azure AD Connect det objectGUID-värdet tillbaka till MS-DS-ConsistencyGuid-attributet i den lokala Active Directory. När attributet ms-DS-ConsistencyGuid har fyllts i, Azure AD Connect sedan att objektet exporteras till Azure AD.

>[!NOTE]
> När ett lokalt AD-objekt har importer ATS till Azure AD Connect (d.v.s. importer ATS till AD Connector-utrymmet och projicerat i metaversum) kan du inte ändra dess sourceAnchor-värde längre. Om du vill ange värdet sourceAnchor för ett specifikt AD-objekt konfigurerar du dess ms-DS-ConsistencyGuid-attribut innan det importeras till Azure AD Connect.

### <a name="permission-required"></a>Behörighet krävs
För att den här funktionen ska fungera måste det AD DS-konto som används för att synkronisera med lokala Active Directory beviljas Skriv behörighet till attributet ms-DS-ConsistencyGuid i den lokala Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Aktivera funktionen ConsistencyGuid – ny installation
Du kan aktivera användningen av ConsistencyGuid som sourceAnchor under den nya installationen. I det här avsnittet beskrivs både Express och anpassad installation i detalj.

  >[!NOTE]
  > Endast nyare versioner av Azure AD Connect (1.1.524.0 och efter) stöder användningen av ConsistencyGuid som sourceAnchor vid en ny installation.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Aktivera funktionen ConsistencyGuid

#### <a name="express-installation"></a>Snabb installation
När du installerar Azure AD Connect med Express läget avgör Azure AD Connect guiden automatiskt det mest lämpliga AD-attributet som ska användas som sourceAnchor-attribut med följande logik:

* Först frågar guiden Azure AD Connect din Azure AD-klient för att hämta det AD-attribut som används som attributet sourceAnchor i föregående Azure AD Connect-installation (om det finns några). Om den här informationen är tillgänglig använder Azure AD Connect samma AD-attribut.

  >[!NOTE]
  > Endast nyare versioner av Azure AD Connect (1.1.524.0 och efter) lagrar information i Azure AD-klienten om attributet sourceAnchor som används under installationen. Äldre versioner av Azure AD Connect inte.

* Om information om sourceAnchor-attributet som används inte är tillgängligt kontrollerar guiden status för attributet ms-DS-ConsistencyGuid i din lokala Active Directory. Om attributet inte har kon figurer ATS för något objekt i katalogen använder guiden ms-DS-ConsistencyGuid som sourceAnchor-attribut. Om attributet har kon figurer ATS på ett eller flera objekt i katalogen, avslutar guiden attributet som används av andra program och är inte lämpligt som sourceAnchor-attribut...

* I så fall går guiden tillbaka till att använda objectGUID som sourceAnchor-attribut.

* När sourceAnchor-attributet har beslut ATS lagrar guiden informationen i din Azure AD-klient. Informationen kommer att användas av framtida installation av Azure AD Connect.

När Express installationen är klar informerar guiden vilket attribut som har valts som käll-Anchor-attribut.

![Guiden informerar AD-attribut som har plockats för sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Anpassad installation
När du installerar Azure AD Connect med anpassat läge tillhandahåller guiden Azure AD Connect två alternativ när du konfigurerar sourceAnchor-attribut:

![Anpassad installation – konfiguration av sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Inställning | Beskrivning |
| --- | --- |
| Låt Azure hantera källfästpunkten | Välj det här alternativet om du vill att Azure AD ska hämta attributet. Om du väljer det här alternativet använder Azure AD Connect guiden samma [logik för val av sourceAnchor som används under snabb installationen](#express-installation). Precis som Express installation informerar guiden om vilket attribut som har plockats som käll-Anchor-attribut efter att den anpassade installationen har slutförts. |
| Ett specifikt attribut | Välj det här alternativet om du vill ange ett befintligt AD-attribut som sourceAnchor-attribut. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Aktivera funktionen ConsistencyGuid-befintlig distribution
Om du har en befintlig Azure AD Connect distribution som använder objectGUID som käll-Anchor-attribut kan du växla det till att använda ConsistencyGuid i stället.

>[!NOTE]
> Endast nyare versioner av Azure AD Connect (1.1.552.0 och efter) stöder växling från ObjectGuid till ConsistencyGuid som käll-Anchor-attribut.

Växla från objectGUID till ConsistencyGuid som käll-Anchor-attribut:

1. Starta guiden Azure AD Connect och klicka på **Konfigurera** för att gå till skärmen uppgifter.

2. Välj alternativet **Konfigurera käll fäst punkts** aktivitet och klicka på **Nästa**.

   ![Aktivera ConsistencyGuid för befintlig distribution – steg 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Ange dina autentiseringsuppgifter för Azure AD-administratören och klicka på **Nästa**.

4. Azure AD Connects guiden analyserar statusen för attributet ms-DS-ConsistencyGuid i din lokala Active Directory. Om attributet inte har kon figurer ATS för något objekt i katalogen, Azure AD Connect att det inte finns något annat program som för närvarande använder attributet och är säkert att använda det som käll-Anchor-attribut. Klicka på **Nästa** för att fortsätta.

   ![Aktivera ConsistencyGuid för befintlig distribution – steg 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. På skärmen **redo att konfigurera** klickar du på **Konfigurera** för att göra konfigurations ändringen.

   ![Aktivera ConsistencyGuid för befintlig distribution – steg 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. När konfigurationen är klar anger guiden att ms-DS-ConsistencyGuid nu används som käll-Anchor-attribut.

   ![Aktivera ConsistencyGuid för befintlig distribution – steg 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Under analysen (steg 4), om attributet har kon figurer ATS på ett eller flera objekt i katalogen, avslutar guiden attributet som används av ett annat program och returnerar ett fel som illustreras i diagrammet nedan. Det här felet kan också inträffa om du tidigare har aktiverat funktionen ConsistencyGuid på den primära Azure AD Connect-servern och du försöker göra samma sak på din mellanlagrings Server.

![Aktivera ConsistencyGuid för befintlig distribution – fel](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Om du är säker på att attributet inte används av andra befintliga program kan du ignorera felet genom att starta om guiden Azure AD Connect med växeln **/SkipLdapSearch** angiven. Det gör du genom att köra följande kommando i kommando tolken:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Inverkan på AD FS eller Federations konfiguration från tredje part
Om du använder Azure AD Connect för att hantera distribution lokalt AD FS, uppdaterar Azure AD Connect automatiskt anspråks reglerna så att de använder samma AD-attribut som sourceAnchor. Detta säkerställer att ImmutableID-anspråket som genereras av ADFS överensstämmer med sourceAnchor-värdena som exporteras till Azure AD.

Om du hanterar AD FS utanför Azure AD Connect eller om du använder Federations servrar från tredje part för autentisering måste du manuellt uppdatera anspråks reglerna för ImmutableID-anspråk så att de överensstämmer med sourceAnchor-värdena som exporteras till Azure AD enligt beskrivningen i artikel avsnittet [ändra AD FS anspråks regler](./how-to-connect-fed-management.md#modclaims). Guiden returnerar följande varning när installationen är klar:

![Federations konfiguration från tredje part](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Lägga till nya kataloger i en befintlig distribution
Anta att du har distribuerat Azure AD Connect med ConsistencyGuid-funktionen aktive rad, och nu vill du lägga till en annan katalog i distributionen. När du försöker lägga till katalogen kontrollerar Azure AD Connect-guiden status för ms-DS-ConsistencyGuid-attributet i katalogen. Om attributet har kon figurer ATS på ett eller flera objekt i katalogen, avslutar guiden attributet som används av andra program och returnerar ett fel enligt diagrammet nedan. Om du är säker på att attributet inte används av befintliga program kan du ignorera felet genom att starta om guiden Azure AD Connect med **/SkipLdapSearch** -växeln som anges ovan eller så behöver du kontakta supporten för mer information.

![Lägga till nya kataloger i en befintlig distribution](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD-inloggning
När du integrerar din lokala katalog med Azure AD är det viktigt att förstå hur synkroniseringsinställningarna kan påverka hur användaren autentiserar sig. I Azure AD används userPrincipalName (UPN) för att autentisera användaren. När du synkroniserar dina användare måste du dock välja det attribut som ska användas för värdet userPrincipalName noggrant.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Välja attributet för userPrincipalName
När du väljer attributet för att ange värdet för UPN som ska användas i Azure One bör det säkerställas att

* Attributvärdena motsvarar UPN-syntaxen (RFC 822), vilket är det ska vara av formatet användar namn \@ domän
* Suffixet i värdena matchar en av de verifierade anpassade domänerna i Azure AD

I Express inställningar är det förmodade valet för attributet userPrincipalName. Om attributet userPrincipalName inte innehåller värdet som du vill att användarna ska logga in på Azure måste du välja **anpassad installation**.

### <a name="custom-domain-state-and-upn"></a>Anpassad domän status och UPN
Det är viktigt att se till att det finns en verifierad domän för UPN-suffixet.

John är en användare i contoso.com. Du vill att John ska använda det lokala UPN John \@ contoso.com för att logga in på Azure när du har synkroniserat användare till Azure AD-katalogen contoso.onmicrosoft.com. För att göra det måste du lägga till och verifiera contoso.com som en anpassad domän i Azure AD innan du kan börja synkronisera användarna. Om UPN-suffixet John, till exempel contoso.com, inte matchar en verifierad domän i Azure AD, ersätter Azure AD UPN-suffixet med contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Icke-dirigerbart lokala domäner och UPN för Azure AD
Vissa organisationer har icke-flyttbara domäner, t. ex. contoso. local eller enkla enkla domän etiketter som contoso. Du kan inte verifiera en icke-dirigerbart domän i Azure AD. Azure AD Connect kan bara synkronisera till en verifierad domän i Azure AD. När du skapar en Azure AD-katalog skapar den en dirigerbart domän som blir standard domän för din Azure AD, till exempel contoso.onmicrosoft.com. Därför är det nödvändigt att verifiera en annan dirigerbart domän i ett sådant scenario om du inte vill synkronisera till standard domänen onmicrosoft.com.

Läs [Lägg till ditt anpassade domän namn för att Azure Active Directory](../fundamentals/add-custom-domain.md) för mer information om att lägga till och verifiera domäner.

Azure AD Connect identifierar om du kör i en icke-dirigerbart domän miljö och korrekt varnar dig när du går vidare med Express inställningar. Om du arbetar i en icke-dirigerbart domän är det troligt att användarens UPN-namn också har icke-dirigerbart suffix. Om du till exempel kör under contoso. local föreslår Azure AD Connect att du använder anpassade inställningar i stället för att använda Express inställningar. Med anpassade inställningar kan du ange det attribut som ska användas som UPN för att logga in på Azure när användarna har synkroniserats till Azure AD.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).