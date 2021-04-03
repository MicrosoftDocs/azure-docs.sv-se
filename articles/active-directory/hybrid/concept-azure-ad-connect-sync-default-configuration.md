---
title: 'Azure AD Connect synkronisering: förstå standard konfigurationen | Microsoft Docs'
description: I den här artikeln beskrivs standard konfigurationen i Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e55526e0a63a0c603e2b62ccb3ac0efed911cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996635"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect-synkronisering: Förstå standardkonfigurationen
I den här artikeln beskrivs de inbyggda konfigurations reglerna. Den dokumenterar reglerna och hur dessa regler påverkar konfigurationen. Den vägleder dig också genom standard konfigurationen av Azure AD Connect Sync. Målet är att läsaren förstår hur konfigurations modellen, med namnet deklarativ etablering, fungerar i ett verkligt exempel. Den här artikeln förutsätter att du redan har installerat och konfigurerat Azure AD Connect Sync med hjälp av installations guiden.

Mer information om konfigurations modellen finns i [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Välkomst regler från lokala datorer till Azure AD
Följande uttryck finns i konfigurations rutan.

### <a name="user-out-of-box-rules"></a>Direkt regler för användare
Dessa regler gäller även för objekt typen iNetOrgPerson.

Ett användar objekt måste uppfylla följande för att kunna synkroniseras:

* Måste ha en sourceAnchor.
* När objektet har skapats i Azure AD kan sourceAnchor inte ändras. Om värdet ändras lokalt stoppas synkroniseringen tills sourceAnchor ändras tillbaka till det tidigare värdet.
* Attributet accountEnabled (userAccountControl) måste fyllas i. Med en lokal Active Directory är det här attributet alltid tillgängligt och ifyllt.

Följande användar objekt synkroniseras **inte** med Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att många färdiga objekt i Active Directory, till exempel det inbyggda administratörs kontot, inte är synkroniserade.
* `IsPresent([sAMAccountName]) = False`. Se till att användar objekt utan sAMAccountName-attribut inte är synkroniserade. Det här fallet är bara praktiskt taget i en domän som uppgraderats från NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Synkronisera inte det tjänst konto som används av Azure AD Connect Sync och dess tidigare versioner.
* Synkronisera inte Exchange-konton som inte fungerar i Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Synkronisera inte objekt som inte fungerar i Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Denna bitmask (&H21C07000) filtrerar bort följande objekt:
  * E-postaktiverad offentlig mapp (i för hands version från och med version 1.1.524.0)
  * Systemets närvaro post låda
  * Post lådans databas post låda (system post låda)
  * Universell säkerhets grupp (gäller inte för en användare, men finns för äldre skäl)
  * Icke-universell grupp (gäller inte för en användare, men finns för äldre skäl)
  * Plan för post låda
  * Identifierings post låda
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte några drabbade objekt för replikering.

Följande regler för attribut gäller:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. SourceAnchor-attributet har inte bidragit från en länkad post låda. Det förutsätts att om en länkad post låda har hittats, kopplas det faktiska kontot senare.
* Exchange-relaterade attribut synkroniseras bara om attributets **smek namn** har ett värde.
* Om det finns flera skogar används attribut i följande ordning:
  1. Attribut som är relaterade till inloggning (till exempel userPrincipalName) har bidragit från skogen med ett aktiverat konto.
  2. Attribut som kan hittas i en Exchange GAL-lista (global adress lista) har bidragit från skogen med en Exchange-postlåda.
  3. Om det inte går att hitta någon post låda kan dessa attribut komma från vilken skog som helst.
  4. Exchange-relaterade attribut (tekniska attribut som inte visas i GAL) har bidragit till den skog där `mailNickname ISNOTNULL` .
  5. Om det finns flera skogar som uppfyller någon av dessa regler, används skapande ordningen (datum/tid) för kopplingarna (skogar) för att avgöra vilken skog som bidrar med attributen. Den första skogen som är ansluten är den första skogen som ska synkroniseras. 

### <a name="contact-out-of-box-rules"></a>Kontakta färdiga regler
Ett kontakt objekt måste uppfylla följande för att kunna synkroniseras:

* Kontakten måste vara e-postaktiverad. Den verifieras med följande regler:
  * `IsPresent([proxyAddresses]) = True)`. Attributet proxyAddresses måste fyllas i.
  * Du kan hitta en primär e-postadress antingen i proxyAddresses-attributet eller e-postattributet. Förekomsten av \@ används för att kontrol lera att innehållet är en e-postadress. En av dessa två regler måste utvärderas till true.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Finns det en post med "SMTP:" och om det finns en sådan kan du \@ hitta i strängen?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Är e-postattributet ifyllt och om det finns kan det \@ finnas i strängen?

Följande kontakt objekt synkroniseras **inte** till Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att inga kontakt objekt har marker ATS som kritiska synkroniserade. Får inte vara någon med en standard konfiguration.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Dessa objekt fungerade inte i Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte några drabbade objekt för replikering.

### <a name="group-out-of-box-rules"></a>Grupp färdiga regler
Ett grupp objekt måste uppfylla följande för att kunna synkroniseras:

* Måste ha färre än 50 000 medlemmar. Det här antalet är antalet medlemmar i den lokala gruppen.
  * Om den har fler medlemmar innan synkroniseringen startar första gången, synkroniseras inte gruppen.
  * Om antalet medlemmar växer från början när det ursprungligen skapades, och när det når 50 000-medlemmar, stoppas synkroniseringen tills antalet medlemskap är lägre än 50 000.
  * Obs! antalet 50 000-medlemskap tillämpas också av Azure AD. Du kan inte synkronisera grupper med fler medlemmar även om du ändrar eller tar bort den här regeln.
* Om gruppen är en **distributions grupp** måste den också vara e-postaktiverad. Se [kontakter färdiga regler](#contact-out-of-box-rules) för den här regeln tillämpas.

Följande grupp objekt synkroniseras **inte** till Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att många färdiga objekt i Active Directory, till exempel den inbyggda gruppen Administratörer, inte är synkroniserade.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Äldre grupp som används av DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Roll grupp.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte några drabbade objekt för replikering.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal regler
FSPs är anslutna till "any" ( \* )-objektet i metaversum. I verkligheten sker denna koppling bara för användare och säkerhets grupper. Den här konfigurationen säkerställer att medlemskap i flera skogar löses och visas korrekt i Azure AD.

### <a name="computer-out-of-box-rules"></a>Direkt regler för dator
Ett dator objekt måste uppfylla följande för att kunna synkroniseras:

* `userCertificate ISNOTNULL`. Endast Windows 10-datorer fyller i det här attributet. Alla dator objekt med ett värde i det här attributet synkroniseras.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Förstå scenariot med direkt regler
I det här exemplet använder vi en distribution med en konto skog (A), en resurs skog (R) och en Azure AD-katalog.

![Bild med scenario beskrivning](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

I den här konfigurationen antas det att det finns ett aktiverat konto i konto skogen och ett inaktiverat konto i resurs skogen med en länkad post låda.

Vårt mål med standard konfigurationen är:

* Attribut som är relaterade till inloggningen synkroniseras från skogen med det aktiverade kontot.
* Attribut som finns i GAL-filen (den globala adress listan) synkroniseras från skogen med post lådan. Om det inte går att hitta någon post låda används alla andra skogar.
* Om en länkad post låda hittas måste det länkade, aktiverade kontot hittas för det objekt som ska exporteras till Azure AD.

### <a name="synchronization-rule-editor"></a>Redigerare för Synkroniseringsregel
Konfigurationen kan visas och ändras med redigeraren för SRE (Tool) och en genväg till den finns på Start menyn.

![Redigeraren för regler för synkronisering](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE är ett Resource Kit-verktyg som installeras med Azure AD Connect Sync. Du måste vara medlem i gruppen ADSyncAdmins för att kunna starta den. När den startas ser du något som liknar detta:

![Inkommande regler för synkronisering](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

I det här fönstret visas alla regler för synkronisering som skapats för din konfiguration. Varje rad i tabellen är en Synkroniseringsregel. Till vänster under regel typer visas de två olika typerna: inkommande och utgående. Inkommande och utgående är från metaversum. Du kommer huvudsakligen att fokusera på reglerna för inkommande trafik i den här översikten. Den faktiska listan över regler för synkronisering beror på det identifierade schemat i AD. I bilden ovan har konto skogen (fabrikamonline.com) inga tjänster, till exempel Exchange och Lync, och inga regler för synkronisering har skapats för dessa tjänster. Men i resurs skogen (res.fabrikamonline.com) hittar du regler för synkronisering av dessa tjänster. Innehållet i reglerna skiljer sig beroende på vilken version som har identifierats. I en distribution med Exchange 2013 finns det till exempel fler attribut som är konfigurerade än i Exchange 2010/2007.

### <a name="synchronization-rule"></a>Synkroniseringsregel
En Synkroniseringsregel är ett konfigurations objekt med en uppsättning attribut som flödar när ett villkor är uppfyllt. Den används också för att beskriva hur ett objekt i ett kopplings utrymme är relaterat till ett objekt i metaversum, som kallas **Join** eller **match**. Reglerna för synkronisering har prioritets värde som anger hur de relaterar till varandra. En Synkroniseringsregel med ett lägre numeriskt värde har en högre prioritet och en konflikt i ett attributarkiv, och högre prioritets konflikt i WINS.

Du kan till exempel titta på synkroniseringsregeln **i från AD – User AccountEnabled**. Markera den här raden i SRE och välj **Redigera**.

Eftersom den här regeln är en regel som är inaktuell visas en varning när du öppnar regeln. Du bör inte göra några [ändringar i välkomst regler](how-to-connect-sync-best-practices-changing-default-configuration.md), så du uppmanas att ange vad dina avsikter är. I det här fallet vill du bara Visa regeln. Välj **Nej**.

![Varning om regler för synkronisering](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

En Synkroniseringsregel har fyra konfigurations avsnitt: Beskrivning, omfångs filter, kopplings regler och transformeringar.

#### <a name="description"></a>Beskrivning
Det första avsnittet innehåller grundläggande information, till exempel ett namn och en beskrivning.

![Fliken Beskrivning i regel redigeraren för synkronisering](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Du hittar också information om vilka anslutna system som den här regeln är relaterad till, vilken objekt typ i det anslutna system den gäller för och metaversum objekt typ. Objekt typen metaversum är alltid person oavsett om käll objekt typen är användare, iNetOrgPerson eller kontakt. Objekt typen metaversum bör aldrig ändras så att den skapas som en generisk typ. Länk typen kan anges till Join, StickyJoin eller provision. Den här inställningen fungerar tillsammans med avsnittet Join rules och beskrivs senare.

Du kan också se att den här synkroniseringsregeln används för Lösenordssynkronisering. Om en användare finns inom omfånget för den här synkroniseringsregeln, synkroniseras lösen ordet från lokalt till molnet (förutsatt att du har aktiverat funktionen för Lösenordssynkronisering).

#### <a name="scoping-filter"></a>Omfångs filter
Avsnittet omfångs filter används för att konfigurera när en Synkroniseringsregel ska gälla. Eftersom namnet på synkroniseringsregeln som du tittar på visar att det bara ska användas för aktiverade användare, är omfånget konfigurerat så att AD-attributet **UserAccountControl** inte får ha bit 2-uppsättningen. När Synkroniseringsmotorn hittar en användare i AD, tillämpas den här synkroniseringsregeln när värdet för **UserAccountControl** anges till decimal värdet 512 (aktive rad normal användare). Regeln tillämpas inte när användaren har **UserAccountControl** inställt på 514 (inaktive rad normal användare).

![Skärm bild som visar avsnittet "omfångs filter" i fönstret "redigera regler för inkommande synkronisering".](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Omfångs filtret har grupper och satser som kan kapslas. Alla satser i en grupp måste vara uppfyllda för att en Synkroniseringsregel ska tillämpas. När flera grupper definieras måste minst en grupp vara uppfylld för att regeln ska gälla. Det vill säga ett logiskt eller utvärderas mellan grupper och ett logiskt och utvärderas i en grupp. Ett exempel på den här konfigurationen finns i regeln för utgående synkronisering **ut till AAD – grupp anslutning**. Det finns flera filter för synkronisering, till exempel en för säkerhets grupper ( `securityEnabled EQUAL True` ) och en för distributions grupper ( `securityEnabled EQUAL False` ).

![Fliken omfattning i regel redigeraren för synkronisering](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Den här regeln används för att definiera vilka grupper som ska tillhandahållas till Azure AD. Distributions grupper måste vara e-postaktiverade för att kunna synkroniseras med Azure AD, men för säkerhets grupper krävs inget e-postmeddelande.

#### <a name="join-rules"></a>Anslut till regler
Det tredje avsnittet används för att konfigurera hur objekt i kopplings utrymmet relaterar till objekt i metaversum. Regeln du har tittat på tidigare har ingen konfiguration för att ansluta till regler, så i stället ska du titta på **i från AD – användar anslutning**.

![Fliken Anslut till regler i regel redigeraren för synkronisering](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Innehållet i kopplings regeln beror på vilket matchande alternativ som valts i installations guiden. För en regel för inkommande trafik börjar utvärderingen med ett objekt i käll kopplings utrymmet och varje grupp i kopplings reglerna utvärderas i följd. Om ett käll objekt utvärderas för att matcha exakt ett objekt i metaversum med någon av kopplings reglerna, är objekten anslutna. Om alla regler har utvärderats och det inte finns någon matchning, används länk typen på sidan beskrivning. Om den här konfigurationen är inställd på att **etableras** skapas ett nytt objekt i målet, metaversum, om minst ett attribut i kopplings villkoret finns (har ett värde). Att etablera ett nytt objekt i metaversum kallas även för att **projicera** ett objekt till metaversum.

Kopplings reglerna utvärderas bara en gång. När ett kopplings utrymmes objekt och ett metaversum-objekt kopplas, förblir de anslutna så länge omfånget för synkroniseringsregeln fortfarande är uppfyllt.

När du utvärderar regler för synkronisering måste endast en Synkroniseringsregel med definierade kopplings regler finnas i omfånget. Om flera regler för synkronisering med kopplings regler hittas för ett objekt, genereras ett fel. Av den anledningen är det bästa sättet att ha bara en Synkroniseringsregel med koppling definierad när flera regler för synkronisering är inom omfånget för ett objekt. I den inbyggda konfigurationen för Azure AD Connect Sync kan du hitta dessa regler genom att titta på namnet och hitta dem med ordet **Join** i slutet av namnet. En Synkroniseringsregel utan definierade kopplings regler tillämpar attributet flöden när en annan Synkroniseringsregel sammanfogade objekten eller etablerade ett nytt objekt i målet.

Om du tittar på bilden ovan kan du se att regeln försöker ansluta till **objectSID** med **msExchMasterAccountSid** (Exchange) och **msRTCSIP-OriginatorSid** (Lync), vilket är vad vi förväntar dig i en skog för konto-resurs. Du hittar samma regel på alla skogar. Antagandet är att varje skog kan vara antingen ett konto eller en resurs skog. Den här konfigurationen fungerar även om du har konton som är aktiva i en enda skog och inte behöver anslutas.

#### <a name="transformations"></a>Transformeringar
Transformerings avsnittet definierar alla attributarkiv som gäller för målobjektet när objekten är anslutna och omfångs filtret är uppfyllt. Om du går tillbaka till **AccountEnabled för AD – User-** , hittar du följande omvandlingar:

![Fliken omvandlingar i regel redigeraren för synkronisering](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

För att kunna använda den här konfigurationen i en Account-Resource skogs distribution förväntas du hitta ett aktiverat konto i konto skogen och ett inaktiverat konto i resurs skogen med inställningarna för Exchange och Lync. Synkroniseringsregeln som du tittar på innehåller de attribut som krävs för inloggning och dessa attribut ska flöda från skogen där det finns ett aktiverat konto. Alla dessa attribut flöden placeras tillsammans i en Synkroniseringsregel.

En omvandling kan ha olika typer: konstant, direkt och uttryck.

* Ett konstant flöde flödar alltid ett hårdkodad-värde. I fallet ovan ställer det alltid in värdet **True** i attributet metaversum med namnet **accountEnabled**.
* Ett direkt flöde flödar alltid värdet för attributet i källan till målattributet som-är.
* Den tredje flödes typen är ett uttryck och det tillåter mer avancerade konfigurationer.

Uttrycks språket är VBA (Visual Basic for Applications), så att personer med erfarenhet av Microsoft Office eller VBScript kommer att känna igen formatet. Attribut omges av hakparenteser, [attributeName]. Attributnamn och funktions namn är Skift läges känsliga, men redigeraren för synkroniseringsfunktionen utvärderar uttryck och ger en varning om uttrycket inte är giltigt. Alla uttryck uttrycks på en enda rad med kapslade funktioner. Om du vill visa kraften i konfigurations språket, så är flödet för pwdLastSet, men med ytterligare kommentarer infogade:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Mer information om uttrycks språket för attribut flöden finns i förstå definitions [uttryck för deklarativ insikter](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Prioritet
Du har nu tittat på några enskilda regler för synkronisering, men reglerna fungerar tillsammans i konfigurationen. I vissa fall bidrogs ett attributvärde från flera regler för synkronisering till samma Target-attribut. I det här fallet används attributets prioritet för att avgöra vilket attribut WINS. Du kan till exempel titta på attributet sourceAnchor. Det här attributet är ett viktigt attribut som kan användas för att logga in på Azure AD. Du kan hitta ett attributarkiv för det här attributet i två olika regler för synkronisering **i från AD – User AccountEnabled** och **i från AD – användare common**. På grund av regel prioriteten för synkroniseringen bidrogs sourceAnchor-attributet från skogen med ett aktiverat konto först när flera objekt är anslutna till metaversum-objektet. Om det inte finns några aktiverade konton använder Synkroniseringsmotorn den catch-all-Synkroniseringsregel **i från AD – användare common**. Den här konfigurationen ser till att även om det finns inaktiverade konton finns det fortfarande en sourceAnchor.

![Inkommande regler för synkronisering](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Prioriteten för regler för synkronisering anges i grupper av installations guiden. Alla regler i en grupp har samma namn, men de är anslutna till olika anslutna kataloger. Installations guiden ger regeln **i från AD – användare ansluter** högst prioritet och upprepas över alla anslutna AD-kataloger. Den fortsätter sedan med nästa grupper av regler i en fördefinierad ordning. I en grupp läggs reglerna till i den ordning som kopplingarna lades till i guiden. Om en annan anslutning läggs till via guiden omorganiseras reglerna för synkronisering och den nya kopplingens regler infogas sist i varje grupp.

### <a name="putting-it-all-together"></a>Färdigställa allt
Vi vet nu nog om regler för synkronisering för att kunna förstå hur konfigurationen fungerar med de olika reglerna för synkronisering. Om du tittar på en användare och de attribut som har bidragit till metaversum tillämpas reglerna i följande ordning:

| Name | Kommentar |
|:--- |:--- |
| I från AD – användar anslutning |Regel för anslutning av anslutnings utrymmes objekt med metaversum. |
| I från AD – UserAccount aktiverat |Attribut krävs för inloggning till Azure AD och Microsoft 365. Vi vill ha dessa attribut från det aktiverade kontot. |
| I från AD – användare common från Exchange |Attribut hittades i den globala adress listan. Vi förutsätter att data kvaliteten är bäst i skogen där vi har hittat användarens post låda. |
| I från AD – vanlig användare |Attribut hittades i den globala adress listan. Om vi inte hittar någon post låda kan andra anslutna objekt bidra till attributvärdet. |
| I från AD – användar utbyte |Finns bara om Exchange har identifierats. Den flödar alla Exchange-attribut för infrastrukturen. |
| I från AD – användare Lync |Finns bara om Lync har identifierats. Alla infrastruktur-Lync-attribut. |

## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurations modellen i att [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Läs mer om uttrycks språket i [förstå deklarativ etablerings uttryck](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Fortsätt att läsa hur den inbyggda konfigurationen fungerar i [förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md)
* Se hur du gör en praktisk ändring med deklarativ etablering i [hur du ändrar standard konfigurationen](how-to-connect-sync-change-the-configuration.md).

**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

