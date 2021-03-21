---
title: 'Azure AD Connect: fel sökning av fel vid synkronisering | Microsoft Docs'
description: Förklarar hur du felsöker fel som påträffas under synkronisering med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d976cd924644828f5861e4c54460a8b4e4f81444
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643872"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Fel sökning av fel under synkronisering
Fel kan uppstå när identitets data synkroniseras från Windows Server Active Directory (AD DS) till Azure Active Directory (Azure AD). Den här artikeln innehåller en översikt över olika typer av synkroniseringsfel, några möjliga scenarier som orsakar felen och potentiella sätt att åtgärda felen. Den här artikeln innehåller vanliga fel typer och kan inte omfatta alla möjliga fel.

 Den här artikeln förutsätter att läsaren är bekant med de underliggande [design begreppen i Azure AD och Azure AD Connect](plan-connect-design-concepts.md).

Med den senaste versionen av Azure AD Connect \( augusti 2016 eller högre \) är en rapport med synkroniseringsfel tillgängligt i [Azure Portal](https://aka.ms/aadconnecthealth) som en del av Azure AD Connect Health för synkronisering.

Från den 1 september 2016 [Azure Active Directory duplicerat attribut återhämtnings](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funktion aktive ras som standard för alla *nya* Azure Active Directory klienter. Den här funktionen aktive ras automatiskt för befintliga klienter under kommande månader.

Azure AD Connect utför tre typer av åtgärder från de kataloger som ska synkroniseras: import, synkronisering och export. Fel kan ske i alla åtgärder. Den här artikeln fokuserar främst på fel vid export till Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Fel under export till Azure AD
I följande avsnitt beskrivs olika typer av synkroniseringsfel som kan inträffa under export åtgärden till Azure AD med hjälp av Azure AD-anslutningen. Den här kopplingen kan identifieras av namn formatet "contoso. *onmicrosoft.com*".
Fel under export till Azure AD indikerar att åtgärden \( Lägg till, uppdatera, ta bort osv. \) försökte utföras av Azure AD Connect \( Sync-motorn \) på Azure Active Directory misslyckades.

![Översikt över export fel](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Fel vid data matchning
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Beskrivning
* När Azure AD Connect \( Sync-motorn \) instruerar Azure Active Directory att lägga till eller uppdatera objekt, matchar Azure AD det inkommande objektet med attributet **SourceAnchor** till attributet **IMMUTABLEID** för objekt i Azure AD. Den här matchningen kallas för en **hård matchning**.
* När Azure AD inte **hittar** något objekt som matchar attributet **immutableId** med **sourceAnchor** -attributet för det inkommande objektet, går det tillbaka till att använda proxyAddresses-och userPrincipalName-attributen för att hitta en matchning. Den här matchningen kallas för en **mjuk matchning**. Den mjuka matchningen är utformad för att matcha objekt som redan finns i Azure AD (som har en källa i Azure AD) med de nya objekt som läggs till/uppdateras under synkroniseringen och som representerar samma entitet (användare, grupper) lokalt.
* **InvalidSoftMatch** -fel uppstår när hård matchningen inte hittar något matchande objekt **och** en mjuk matchning söker efter ett matchande objekt, men objektet har ett annat värde än *immutableId* än det inkommande objektets *SourceAnchor*, vilket tyder på att det matchande objektet har synkroniserats med ett annat objekt från lokalt Active Directory.

Med andra ord, för att den mjuka matchningen ska fungera, ska det objekt som ska vara Soft-matchat med inte ha något värde för *immutableId*. Om ett objekt med *immutableId* som har angetts med ett värde inte klarar hård matchning men som uppfyller kriterierna för mjuka matchningar skulle åtgärden resultera i ett InvalidSoftMatch-synkroniseringsfel.

Azure Active Directory schema tillåter inte att två eller flera objekt har samma värde för följande attribut. \(Detta är inte en fullständig lista.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> En återhämtnings funktion för attribut för [duplicerat Azure AD-attribut](how-to-connect-syncservice-duplicate-attribute-resiliency.md) är också distribuerad som standard beteende för Azure Active Directory.  Detta minskar antalet synkroniseringsfel som visas av Azure AD Connect (och andra synkroniseringsanvändare) genom att göra Azure AD mer elastiskt på det sätt som de hanterar duplicerade ProxyAddresses-och UserPrincipalName-attribut som finns i lokala AD-miljöer. Den här funktionen korrigerar inte fel i dupliceringen. Därför behöver data fortfarande åtgärdas. Men det tillåter etablering av nya objekt som annars blockeras från att tillhandahållas på grund av duplicerade värden i Azure AD. Detta minskar också antalet synkroniseringsfel som returneras till synkroniseringstjänsten.
> Om den här funktionen är aktive rad för din klient kan du inte se de InvalidSoftMatch synkroniseringsfel som visas vid etablering av nya objekt.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Exempel scenarier för InvalidSoftMatch
1. Två eller flera objekt med samma värde för attributet ProxyAddresses finns i lokala Active Directory. Endast en har hämtats i Azure AD.
2. Två eller flera objekt med samma värde för attributet userPrincipalName finns i den lokala Active Directory. Endast en har hämtats i Azure AD.
3. Ett objekt har lagts till i den lokala Active Directory med samma värde för attributet ProxyAddresses som för ett befintligt objekt i Azure Active Directory. Objektet som lagts till lokalt kommer inte att tillhandahållas i Azure Active Directory.
4. Ett objekt lades till i lokalt Active Directory med samma värde som userPrincipalName-attributet som ett konto i Azure Active Directory. Objektet har inte allokerats i Azure Active Directory.
5. Ett synkroniserat konto har flyttats från skog A till skog B. Azure AD Connect (Synkroniseringsmotorn) använde attributet ObjectGUID för att beräkna SourceAnchor. När skogen har flyttats är värdet för SourceAnchor detsamma. Det nya objektet (från skog B) kan inte synkroniseras med det befintliga objektet i Azure AD.
6. Ett synkroniserat objekt togs bort av misstag från lokalt Active Directory och ett nytt objekt skapades i Active Directory för samma entitet (till exempel användare) utan att kontot tas bort i Azure Active Directory. Det nya kontot kan inte synkroniseras med det befintliga Azure AD-objektet.
7. Azure AD Connect avinstallerades och installerades om. Under ominstallationen valdes ett annat attribut som SourceAnchor. Alla objekt som tidigare har synkroniserats stoppades vid synkronisering med InvalidSoftMatch-fel.

#### <a name="example-case"></a>Exempel:
1. **Bob Smith** är en synkroniserad användare i Azure Active Directory från lokalt Active Directory av *contoso.com*
2. Robert Svenssons **userPrincipalName** har angetts som **Bobs \@ contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** är **SourceAnchor** som beräknas av Azure AD Connect att använda Bob Smith- **objectGUID** från lokalt Active Directory, vilket är **immutableId** för Bob Smith i Azure Active Directory.
4. Bob har också följande värden för attributet **proxyAddresses** :
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
5. En ny användare, **Bob Taylor**, läggs till i den lokala Active Directory.
6. Robert Taylors **userPrincipalName** anges som **bobt \@ contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** är **sourceAnchor** som beräknas av Azure AD Connect att använda Bob Taylors **objectGUID** från lokalt Active Directory. Bob Taylor-objektet har inte synkroniserats till Azure Active Directory än.
8. Bob Taylor har följande värden för attributet proxyAddresses
   * SMTP bobt@contoso.com
   * SMTP bob.taylor@contoso.com
   * **SMTP: Bob \@ contoso.com**
9. Under synkroniseringen kommer Azure AD Connect att identifiera tillägg av Bob Taylor i lokalt Active Directory och be Azure AD att göra samma ändringar.
10. Azure AD kommer först att utföra en hård matchning. Det vill säga att den söker efter ett objekt med immutableId som är lika med "abcdefghijkl0123456789 = =". Hård matchning fungerar inte eftersom inget annat objekt i Azure AD kommer att ha den immutableId.
11. Azure AD försöker sedan med en mjuk matchning av Bob Taylor. Det vill säga att den söker efter ett objekt med proxyAddresses som motsvarar de tre värdena, inklusive SMTP: bob@contoso.com
12. Azure AD hittar Bob Nilssons objekt så att de matchar kriterierna för mjuka matchningar. Men det här objektet har värdet immutableId = "abcdefghijklmnopqrstuv = =". vilket indikerar att objektet har synkroniserats från ett annat objekt från lokalt Active Directory. Därför kan inte Azure AD mjuka matcha dessa objekt och resultera i ett **InvalidSoftMatch** synkroniseringsfel.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Så här åtgärdar du InvalidSoftMatch-fel
Den vanligaste orsaken till att InvalidSoftMatch-felet är två objekt med olika SourceAnchor \( \) -immutableId har samma värde för attributen proxyAddresses och/eller userPrincipalName, som används vid en mjuk matchning i Azure AD. För att åtgärda den ogiltiga mjuka matchningen

1. Identifiera det duplicerade proxyAddresses-, userPrincipalName-eller annat attributvärde som orsakar felet. Identifiera också vilka två \( eller flera \) objekt som ingår i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](./how-to-connect-health-sync.md) kan hjälpa dig att identifiera de två objekten.
2. Identifiera vilket objekt som ska fortsätta att ha det duplicerade värdet och vilket objekt som inte ska användas.
3. Ta bort det duplicerade värdet från det objekt som inte ska ha det värdet. Du bör göra ändringen i den katalog där objektet har sitt ursprung. I vissa fall kan du behöva ta bort ett av objekten i konflikt.
4. Om du har gjort ändringen i lokal AD kan Azure AD Connect synkronisera ändringen.

Synkrona fel rapporter i Azure AD Connect Health för synkronisering uppdateras var 30: e minut och innehåller fel från det senaste synkroniseringsförsök.

> [!NOTE]
> ImmutableId, som definition, bör inte ändras under objektets livs längd. Om Azure AD Connect inte har kon figurer ATS med några av scenarierna i åtanke från listan ovan kan du få en situation där Azure AD Connect beräknar ett annat värde för SourceAnchor för AD-objektet som representerar samma entitet (användare/grupp/kontakt osv.) som har ett befintligt Azure AD-objekt som du vill fortsätta använda.
>
>

#### <a name="related-articles"></a>Relaterade artiklar
* [Dubbletter eller ogiltiga attribut förhindrar katalog synkronisering i Microsoft 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Beskrivning
När Azure AD försöker använda mjuk matchning för två objekt, är det möjligt att två objekt av olika objekt typer (till exempel användare, grupp, kontakt osv.) har samma värden för attributen som används för att utföra den mjuka matchningen. Eftersom duplicering av dessa attribut inte är tillåtet i Azure AD, kan åtgärden leda till "ObjectTypeMismatch"-synkroniseringsfel.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Exempel scenarier för ObjectTypeMismatch-fel
* En e-postaktiverad säkerhets grupp skapas i Microsoft 365. Admin lägger till en ny användare eller kontakt i lokalt AD (som inte synkroniseras med Azure AD ännu) med samma värde för attributet ProxyAddresses som i gruppen Microsoft 365.

#### <a name="example-case"></a>Exempel fall
1. Admin skapar en ny e-postaktiverad säkerhets grupp i Microsoft 365 för skatte avdelningen och ger en e-postadress som tax@contoso.com . Den här gruppen har tilldelats attributvärdet ProxyAddresses för **SMTP: tax \@ contoso.com**
2. En ny användare ansluter Contoso.com och ett konto skapas för användaren lokalt med proxyAddress som **SMTP: skatt \@ contoso.com**
3. När Azure AD Connect kommer att synkronisera det nya användar kontot får du meddelandet "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Så här åtgärdar du ObjectTypeMismatch-fel
Den vanligaste orsaken till ObjectTypeMismatch-felet är två objekt av annan typ (användare, grupp, kontakt osv.) har samma värde för attributet ProxyAddresses. För att åtgärda ObjectTypeMismatch:

1. Identifiera det duplicerade proxyAddresses-värdet (eller andra attribut) som orsakar felet. Identifiera också vilka två \( eller flera \) objekt som ingår i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](./how-to-connect-health-sync.md) kan hjälpa dig att identifiera de två objekten.
2. Identifiera vilket objekt som ska fortsätta att ha det duplicerade värdet och vilket objekt som inte ska användas.
3. Ta bort det duplicerade värdet från det objekt som inte ska ha det värdet. Observera att du bör göra ändringen i den katalog där objektet har sitt ursprung. I vissa fall kan du behöva ta bort ett av objekten i konflikt.
4. Om du har gjort ändringen i lokal AD kan Azure AD Connect synkronisera ändringen. Synkroniseringsfel i Azure AD Connect Health för synkronisering uppdateras var 30: e minut och innehåller fel från det senaste synkroniseringsförsök.

## <a name="duplicate-attributes"></a>Duplicera attribut
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Beskrivning
Azure Active Directory schema tillåter inte att två eller flera objekt har samma värde för följande attribut. Det innebär att varje objekt i Azure AD tvingas ha ett unikt värde för dessa attribut vid en specifik instans.

* ProxyAddresses
* UserPrincipalName

Om Azure AD Connect försöker lägga till ett nytt objekt eller uppdatera ett befintligt objekt med ett värde för ovanstående attribut som redan har tilldelats till ett annat objekt i Azure Active Directory, resulterar åtgärden i synkroniseringsfel "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Möjliga scenarier:
1. Dubblettvärden är kopplat till ett redan synkroniserat objekt, vilket står i konflikt med ett annat synkroniserat objekt.

#### <a name="example-case"></a>Exempel:
1. **Bob Smith** är en synkroniserad användare i Azure Active Directory från lokalt Active Directory av contoso.com
2. Robert Svenssons **userPrincipalName** lokalt har angetts som **Bobs \@ contoso.com**.
3. Bob har också följande värden för attributet **proxyAddresses** :
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
4. En ny användare, **Bob Taylor**, läggs till i den lokala Active Directory.
5. Robert Taylors **userPrincipalName** anges som **bobt \@ contoso.com**.
6. **Bob Taylor** har följande värden för attributet **proxyAddresses** i. SMTP: bobt@contoso.com II. SMTP bob.taylor@contoso.com
7. Bob Taylor-objektet har synkroniserats med Azure AD.
8. Admin beslutade att uppdatera Bob Taylor-attributet **proxyAddresses** med följande värde: i. **SMTP: Bob \@ contoso.com**
9. Azure AD försöker uppdatera Bob Taylor-objektet i Azure AD med ovanstående värde, men åtgärden Miss lyckas eftersom ProxyAddresses-värdet redan har tilldelats Bob Smith, vilket resulterar i "AttributeValueMustBeUnique"-fel.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Så här åtgärdar du AttributeValueMustBeUnique-fel
Den vanligaste orsaken till att AttributeValueMustBeUnique-felet är två objekt med olika SourceAnchor- \( immutableId \) har samma värde för attributen proxyAddresses och/eller userPrincipalName. För att åtgärda AttributeValueMustBeUnique-fel

1. Identifiera det duplicerade proxyAddresses-, userPrincipalName-eller annat attributvärde som orsakar felet. Identifiera också vilka två \( eller flera \) objekt som ingår i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](./how-to-connect-health-sync.md) kan hjälpa dig att identifiera de två objekten.
2. Identifiera vilket objekt som ska fortsätta att ha det duplicerade värdet och vilket objekt som inte ska användas.
3. Ta bort det duplicerade värdet från det objekt som inte ska ha det värdet. Observera att du bör göra ändringen i den katalog där objektet har sitt ursprung. I vissa fall kan du behöva ta bort ett av objekten i konflikt.
4. Om du har gjort ändringen i lokalt AD kan Azure AD Connect synkronisera ändringen för felet för att åtgärda problemet.

#### <a name="related-articles"></a>Relaterade artiklar
-[Dubbletter eller ogiltiga attribut förhindrar katalog synkronisering i Microsoft 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Data verifierings problem
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Beskrivning
Azure Active Directory tillämpar olika begränsningar för själva data innan de tillåter att data skrivs till katalogen. De här begränsningarna är att se till att slutanvändarna får bästa möjliga upplevelse när du använder de program som är beroende av dessa data.

#### <a name="scenarios"></a>Scenarier
a. Attributvärdet UserPrincipalName innehåller ogiltiga tecken/tecken som inte stöds.
b. Attributet UserPrincipalName följer inte det format som krävs.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Så här åtgärdar du IdentityDataValidationFailed-fel
a. Se till att attributet userPrincipalName innehåller tecken som stöds och det format som krävs.

#### <a name="related-articles"></a>Relaterade artiklar
* [Förbered för att etablera användare via katalog synkronisering till Microsoft 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Beskrivning
Det här fallet resulterar i ett **"FederatedDomainChangeError"** -synkroniseringsfel när suffixet för en användares userPrincipalName ändras från en federerad domän till en annan federerad domän.

#### <a name="scenarios"></a>Scenarier
För en synkroniserad användare ändrades UserPrincipalName-suffixet från en federerad domän till en annan federerad domän lokalt. Exempelvis har *userPrincipalName = bob \@ contoso.com* ändrats till *userPrincipalName = Bob \@ fabrikam.com*.

#### <a name="example"></a>Exempel
1. Bob Smith, ett konto för Contoso.com, läggs till som en ny användare i Active Directory med UserPrincipalName bob@contoso.com
2. Bob flyttas till en annan division av Contoso.com som heter Fabrikam.com och deras UserPrincipalName har ändrats till bob@fabrikam.com
3. Både contoso.com-och fabrikam.com-domäner är federerade domäner med Azure Active Directory.
4. Bobs userPrincipalName uppdateras inte och resulterar i ett "FederatedDomainChangeError"-synkroniseringsfel.

#### <a name="how-to-fix"></a>Så här löser du
Om en användares UserPrincipalName-suffix har uppdaterats från bob@**contoso.com** till Bob \@ **fabrikam.com**, där både **contoso.com** och **fabrikam.com** är **federerade domäner**, följer du dessa steg för att åtgärda synkroniseringsfel

1. Uppdatera användarens UserPrincipalName i Azure AD från bob@contoso.com till bob@contoso.onmicrosoft.com . Du kan använda följande PowerShell-kommando med Azure AD PowerShell-modulen: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Tillåt nästa synkronisering att försöka synkronisera. Den här tidssynkroniseringen kommer att lyckas och den kommer att uppdatera UserPrincipalName till Bob bob@fabrikam.com som förväntat.

#### <a name="related-articles"></a>Relaterade artiklar
* [Ändringarna synkroniseras inte med Azure Active Directory Sync-verktyget när du har ändrat UPN för ett användar konto för att använda en annan federerad domän](./howto-troubleshoot-upn-changes.md)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Beskrivning
När ett attribut överskrider den tillåtna storleks gränsen, längd begränsningen eller antals gränsen som angetts av Azure Active Directory schema, resulterar synkroniseringsåtgärden i **LargeObject** -eller **ExceededAllowedLength** -synkroniseringsfel. Detta fel uppstår vanligt vis för följande attribut

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Möjliga scenarier
1. Bobs userCertificate-attribut lagrar för många certifikat som har tilldelats Bob. Dessa kan innehålla äldre, utgångna certifikat. Hård gränsen är 15 certifikat. Mer information om hur du hanterar LargeObject-fel med userCertificate-attribut finns i artikeln [Hantera LargeObject-fel som orsakas av userCertificate-attribut](tshoot-connect-largeobjecterror-usercertificate.md).
2. Bobs userSMIMECertificate-attribut lagrar för många certifikat som har tilldelats Bob. Dessa kan innehålla äldre, utgångna certifikat. Hård gränsen är 15 certifikat.
3. Bob thumbnailPhoto set i Active Directory är för stor för att kunna synkroniseras i Azure AD.
4. Vid automatisk ifyllning av attributet ProxyAddresses i Active Directory har ett objekt för många ProxyAddresses tilldelats.

### <a name="how-to-fix"></a>Så här löser du
1. Se till att det attribut som orsakar felet ligger inom den tillåtna begränsningen.

## <a name="existing-admin-role-conflict"></a>Konflikt vid befintlig administratörs roll

### <a name="description"></a>Beskrivning
En **befintlig administratörs roll konflikt** inträffar på ett användar objekt under synkroniseringen när det användar objektet har:

- administrativ behörighet och
- samma UserPrincipalName som ett befintligt Azure AD-objekt

Azure AD Connect får inte vara en mjuk matchning av ett användar objekt från en lokal AD med ett användar objekt i Azure AD som har tilldelats en administrativ roll.  Mer information finns i [Azure AD userPrincipalName-population](plan-connect-userprincipalname.md)

![Befintlig administratör](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Så här löser du
Gör så här för att lösa problemet:

1. Ta bort Azure AD-kontot (ägare) från alla administratörs roller. 
2. **Hård borttagning** av det placerade objektet i molnet. 
3. Nästa synkronisering tar hand om mjuk matchning av den lokala användaren till moln kontot (eftersom moln användaren nu inte längre är en global GA). 
4. Återställ roll medlemskap för ägaren. 

>[!NOTE]
>Du kan tilldela den administrativa rollen till det befintliga användarobjektet igen efter att den mjuka matchningen mellan det lokala användarobjektet och objektet Azure AD har slutförts.

## <a name="related-links"></a>Relaterade länkar
* [Hitta Active Directory objekt i Active Directory Administrationscenter](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Så här frågar du Azure Active Directory efter ett objekt med Azure Active Directory PowerShell](/previous-versions/azure/jj151815(v=azure.100))