---
title: Lösen ords skydd i Azure Active Directory
description: Lär dig att dynamiskt förbjuda svaga lösen ord från din miljö med Azure Active Directory lösen ords skydd
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 740201b84323f5a01ab01bb21ec458a19832e499
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652028"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Eliminera Felaktiga lösen ord med Azure Active Directory lösen ords skydd

Många säkerhets guider rekommenderar att du inte använder samma lösen ord på flera platser, för att göra det komplext och för att undvika enkla lösen ord som *Password123*. Du kan ge dina användare [vägledning om hur du väljer lösen ord](https://www.microsoft.com/research/publication/password-guidance), men svaga eller oskyddade lösen ord används ofta. Lösen ords skyddet i Azure AD identifierar och blockerar kända svaga lösen ord och deras varianter, och kan också blockera ytterligare svaga termer som är speciella för din organisation.

Med Azure AD Password Protection tillämpas standard globala förbjudna lösen ords listor automatiskt på alla användare i en Azure AD-klient. För att stödja dina egna affärs-och säkerhets behov kan du definiera poster i en anpassad lista över blockerade lösen ord. När användare ändrar eller återställer sina lösen ord, kontrol leras dessa förbjudna lösen ords listor för att tvinga användningen av starka lösen ord.

Du bör använda ytterligare funktioner som [Azure ad Multi-Factor Authentication](concept-mfa-howitworks.md), inte bara förlitar dig på starka lösen ord som tillämpas av lösen ords skydd i Azure AD. Mer information om hur du använder flera säkerhets nivåer för dina inloggnings händelser finns i [din pa $ $Word spelar ingen roll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> I den här konceptuella artikeln beskrivs en administratör Hur lösen ords skydd i Azure fungerar. Om du är en slutanvändare som redan är registrerad för lösen ords återställning via självbetjäning och behöver gå tillbaka till ditt konto, går du till [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösen ord kan du kontakta supportavdelningen för ytterligare hjälp.

## <a name="global-banned-password-list"></a>Lista över globala förbjudna lösen ord

I Azure AD Identity Protections teamet analyseras hela Azure AD-säkerhetstelemetri som söker efter svaga eller komprometterade lösen ord. Mer specifikt söker analysen efter bas villkor som ofta används som grund för svaga lösen ord. När svaga termer hittas läggs de till i *listan globalt blockerade lösen ord*. Innehållet i den globala listan över förbjudna lösen ord baseras inte på någon extern data källa, men i resultatet av Azure AD-säkerhetstelemetri och analys.

När ett lösen ord ändras eller återställs för en användare i en Azure AD-klient, används den aktuella versionen av den globala listan över blockerade lösen ord för att verifiera lösen ordets styrka. Den här verifierings kontrollen resulterar i starkare lösen ord för alla Azure AD-kunder.

Den globala listan över blockerade lösen ord tillämpas automatiskt på alla användare i en Azure AD-klient. Det finns inget att aktivera eller konfigurera och kan inte inaktive ras. Den här globala listan över förbjudna lösen ord används för användare när de ändrar eller återställer sina egna lösen ord via Azure AD.

> [!NOTE]
> Cyberhot-cyberbrottslingarna använder också liknande strategier i deras attacker för att identifiera vanliga svaga lösen ord och varianter. För att förbättra säkerheten publicerar Microsoft inte innehållet i den globala listan över blockerade lösen ord.

## <a name="custom-banned-password-list"></a>Lista med anpassade förbjudna lösen ord

Vissa organisationer vill förbättra säkerheten och lägga till egna anpassningar ovanpå den globala listan över blockerade lösen ord. Om du vill lägga till egna poster kan du använda *listan anpassat blockerade lösen ord*. Termer som läggs till i listan med anpassade förbjudna lösen ord bör vara fokuserade på organisatoriska villkor som följande exempel:

- Märkes namn
- Produkt namn
- Platser, till exempel företagets huvud kontor
- Företagsspecifika interna villkor
- Förkortningar som har ett bestämt företags betydelse

När villkoren läggs till i listan anpassat blockerade lösen ord kombineras de med villkoren i listan globalt blockerade lösen ord. Lösen ords ändring eller återställnings händelser verifieras sedan mot den kombinerade uppsättningen av dessa förbjudna lösen ords listor.

> [!NOTE]
> Listan med anpassade förbjudna lösen ord är begränsad till högst 1000 villkor. Den är inte utformad för att blockera extremt stora listor med lösen ord.
>
> För att helt utnyttja fördelarna med den anpassade listan över blockerade lösen ord bör du först förstå [hur är lösen ord som utvärderas](#how-are-passwords-evaluated) innan du lägger till dem i den anpassade listan över blockerade. Med den här metoden kan du effektivt identifiera och blockera stora mängder svaga lösen ord och deras varianter.

![Ändra listan med anpassade förbjudna lösen ord under autentiseringsmetoder](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Vi tänker på en kund som heter *contoso*. Företaget är baserat i London och gör en produkt som heter *widget*. För det här exempel kunden är det onödig och mindre säkert att försöka blockera specifika variationer av dessa villkor, till exempel följande:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso
- "LondonHQ"

I stället är det mycket mer effektivt och säkert att bara blockera nyckel bas villkoren, t. ex. följande exempel:

- ”Contoso”
- London
- Fält

Algoritmen för lösen ords validering blockerar automatiskt svaga varianter och kombinationer.

Följ de här självstudierna för att komma igång med att använda en anpassad lista över blockerade lösen ord:

> [!div class="nextstepaction"]
> [Självstudie: Konfigurera anpassade förbjudna lösen ord](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ordlisteattacker och komprometterade lösen ords listor från tredje part

Med Azure AD Password Protection kan du skydda dig mot angrepp vid lösen ords spridning. De flesta angrepp av lösen ord försöker inte angripa ett visst enskilt konto mer än några gånger. Det här beteendet ökar sannolikheten för identifiering, antingen via konto utelåsning eller på annat sätt.

I stället skickar majoriteten av angreppna av lösen ords sprutning bara ett litet antal kända svaga lösen ord mot var och en av kontona i ett företag. Med den här tekniken kan angriparen snabbt söka efter ett lätt komprometterat konto och undvika potentiella identifierings trösklar.

Med Azure AD Password Protection kan du effektivt blockera alla kända svaga lösen ord som troligen används i angrepp med lösen ord. Det här skyddet baseras på verkliga säkerhets telemetridata från Azure AD för att bygga den globala listan över blockerade lösen ord.

Det finns vissa webbplatser från tredje part som räknar upp miljon tals lösen ord som har komprometterats i tidigare offentligt kända säkerhets överträdelser. Det är vanligt att de produkter som används för att verifiera lösen ord från tredje part baseras på en jämförelse av den här miljonen av lösen ord. Dessa metoder är dock inte det bästa sättet att förbättra den övergripande lösen ords styrkan för att få de typiska strategier som används av lösen ords besprutnings attacker.

> [!NOTE]
> Den globala listan över förbjudna lösen ord baseras inte på någon data Källa från tredje part, inklusive listor över komprometterade lösen ord.

Även om den globala listan över blockerade program är liten jämfört med en rad Mass listor från tredje part, kommer den från verkliga säkerhetstelemetri om faktiska angrepp vid lösen ords attacker. Den här metoden förbättrar den övergripande säkerheten och effektiviteten, och algoritmen för verifiering av lösen ord använder också smarta fuzzy-matchande tekniker. Det innebär att Azure AD Password Protection effektivt identifierar och blockerar miljon tals vanligaste svaga lösen ord från att användas i företaget.

## <a name="on-premises-hybrid-scenarios"></a>Lokala hybrid scenarier

Många organisationer har en hybrid identitets modell som innehåller lokala Active Directory Domain Services-miljöer (AD DS). Om du vill utöka säkerhets fördelarna med Azure AD Password Protection till din AD DS-miljö kan du installera komponenter på dina lokala servrar. Dessa agenter kräver lösen ords ändrings händelser i den lokala AD DS-miljön för att följa samma lösen ords princip som i Azure AD.

Mer information finns i [Framtvinga lösen ords skydd i Azure AD för AD DS](concept-password-ban-bad-on-premises.md).

## <a name="how-are-passwords-evaluated"></a>Hur utvärderas lösen ord

När en användare ändrar eller återställer sitt lösen ord, kontrol leras det nya lösen ordet för att styrka och komplexitet genom att verifiera det mot den kombinerade listan med termer från listorna globala och anpassade lösen ord.

Även om användarens lösen ord innehåller ett blockerat lösen ord, kan lösen ordet godkännas om det övergripande lösen ordet annars är tillräckligt starkt. Ett nyligen konfigurerat lösen ord går igenom följande steg för att utvärdera den övergripande styrkan för att avgöra om den ska godkännas eller avvisas:

### <a name="step-1-normalization"></a>Steg 1: normalisering

Ett nytt lösen ord går först igenom normaliserings processen. Den här tekniken gör det möjligt för en liten uppsättning blockerade lösen ord att mappas till en mycket större uppsättning potentiellt svaga lösen ord.

Normalisering har följande två delar:

* Versaler ändras till gemener.
* Sedan utförs vanliga tecken ersättningar, till exempel i följande exempel:

   | Ursprunglig bokstav | Ersatt bokstav |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

Se följande exempel:

* Lösen ordet "tomt" är förbjudet.
* En användare försöker ändra sitt lösen ord till " Bl@nK ".
* Trots att " Bl@nk " inte blockeras konverterar normaliserings processen detta lösen ord till "tomt".
* Detta lösen ord skulle nekas.

### <a name="step-2-check-if-password-is-considered-banned"></a>Steg 2: kontrol lera om lösen ordet anses vara förbjudet

Ett lösen ord granskas sedan för andra matchnings beteenden och poängen genereras. Slut resultatet avgör om begäran om lösen ords ändring godkänns eller avvisas.

#### <a name="fuzzy-matching-behavior"></a>Partiell matchnings beteende

Fuzzy Matching används på det normaliserade lösen ordet för att identifiera om det innehåller ett lösen ord som finns på antingen den globala eller den anpassade listan över blockerade lösen ord. Den matchande processen baseras på ett redigerings avstånd på en (1) jämförelse.

Se följande exempel:

* Lösen ordet "ABCDEF" är förbjudet.
* En användare försöker ändra sitt lösen ord till något av följande:

   * ' abcdeg '- *sista bokstaven ändrades från ' f ' till ' g '*
   * "abcdefg"- *"g" har lagts till i slutet*
   * ABCD ' – *efterföljande ' f ' har tagits bort från slutet*

* Vart och ett av lösen orden ovan matchar inte exakt det förbjudna lösen ordet "ABCDEF".

    Men eftersom varje exempel ligger inom ett redigerings avstånd på 1 av den förbjudna termen "ABCDEF" betraktas de som en matchning till "ABCDEF".
* Dessa lösen ord skulle nekas.

#### <a name="substring-matching-on-specific-terms"></a>Matchning av del strängar (utifrån vissa villkor)

Del Strängs matchning används på det normaliserade lösen ordet för att kontrol lera användarens för-och efter namn samt klient organisationens namn. Klient namns matchning sker inte när lösen ord verifieras på en AD DS-domänkontrollant för lokala hybrid scenarier.

> [!IMPORTANT]
> Matchning av del strängar gäller bara för namn och andra villkor som är minst fyra tecken långa.

Se följande exempel:

* En användare med namnet Poll som vill återställa sina lösen ord till "p0LL23fb".
* Efter normaliseringen skulle det här lösen ordet bli "poll23fb".
* Del Strängs matchning söker efter att lösen ordet innehåller användarens förnamn "avsökning".
* Även om "poll23fb" inte specifikt befann sig på antingen en blockerad lösen ords lista, påträffade del strängar "pollen" i lösen ordet.
* Detta lösen ord skulle nekas.

#### <a name="score-calculation"></a>Poäng beräkning

Nästa steg är att identifiera alla instanser av förbjudna lösen ord i användarens normaliserade nya lösen ord. Poäng tilldelas baserat på följande kriterier:

1. Alla förbjudna lösen ord som hittas i en användares lösen ord får en punkt.
1. Varje återstående tecken som inte ingår i ett förbjudet lösen ord får en punkt.
1. Ett lösen ord måste vara minst fem (5) punkter som ska godkännas.

I följande två exempel scenarier använder contoso Azure AD Password Protection och har "contoso" i sin egen lista över blockerade lösen ord. Vi antar också att "tomt" finns på den globala listan.

I följande exempel scenario ändrar en användare sitt lösen ord till "C0ntos0Blank12":

* Efter normalisering blir det här lösen ordet "contosoblank12".
* Matchnings processen ser till att det här lösen ordet innehåller två förbjudna lösen ord: "contoso" och "tomt".
* Det här lösen ordet tilldelas sedan följande Poäng:

    *[contoso] + [blank] + [1] + [2] = 4 punkter*

* Eftersom det här lösen ordet är under fem (5) punkter avvisas det.

Låt oss se något annorlunda exempel för att visa hur ytterligare komplexitet i ett lösen ord kan bygga upp det antal punkter som krävs för att godkännas. I följande exempel scenario ändrar en användare sitt lösen ord till " ContoS0Bl@nkf9 !":

* Efter normalisering blir det här lösen ordet "contosoblankf9!".
* Matchnings processen ser till att det här lösen ordet innehåller två förbjudna lösen ord: "contoso" och "tomt".
* Det här lösen ordet tilldelas sedan följande Poäng:

    *[contoso] + [blank] + [f] + [9] + [!] = 5 punkter*

* Eftersom det här lösen ordet är minst fem (5) punkter accepteras det.

> [!IMPORTANT]
> Den förbjudna lösen ords algoritmen, tillsammans med listan globalt blockerade lösen ord, kan och göra ändringar när som helst i Azure baserat på pågående säkerhets analyser och forskning.
>
> För den lokala DC-agenttjänsten i hybrid scenarier börjar uppdaterade algoritmer att gälla när DC-agentens program vara har uppgraderats.

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker återställa ett lösen ord till något som ska blockeras visas följande fel meddelande:

*"Tyvärr, ditt lösen ord innehåller ett ord, en fras eller ett mönster som gör ditt lösen ord lätt att gissa. Försök igen med ett annat lösen ord. "*

## <a name="license-requirements"></a>Licenskrav

| Användare | Lista med lösen ords skydd i Azure AD med global lista över förbjudna lösen ord | Lista med lösen ords skydd i Azure AD med anpassad lista över blockerade lösen ord|
|-------------------------------------------|---------------------------|---------------------------|
| Endast molnbaserade användare                          | Azure AD Kostnadsfri             | Azure AD Premium P1 eller P2 |
| Användare som synkroniseras från lokala AD DS | Azure AD Premium P1 eller P2 | Azure AD Premium P1 eller P2 |

> [!NOTE]
> Lokala AD DS-användare som inte har synkroniserats till Azure AD får också till gång till Azure AD Password Protection baserat på befintliga licenser för synkroniserade användare.

Ytterligare licens information, inklusive kostnader, finns på [Azure Active Directory prissättnings webbplats](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Nästa steg

Följ de här självstudierna för att komma igång med att använda en anpassad lista över blockerade lösen ord:

> [!div class="nextstepaction"]
> [Självstudie: Konfigurera anpassade förbjudna lösen ord](tutorial-configure-custom-password-protection.md)

Du kan också [Aktivera lokal Azure AD-lösenords skydd](howto-password-ban-bad-on-premises-deploy.md).
