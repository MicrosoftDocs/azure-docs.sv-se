---
title: Distributions överväganden för Azure Active Directory återställning av lösen ord för självbetjäning
description: Lär dig mer om distributions överväganden och strategin för lyckad implementering av lösen ords återställning via självbetjäning i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55fc4c0b5f3e2bbf57c972ad4a7fd2c9bdd3810e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579203"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Planera en Azure Active Directory distribution av lösen ords återställning via självbetjäning

> [!IMPORTANT]
> Den här distributions planen ger vägledning och bästa praxis för att distribuera Azure AD självbetjäning för återställning av lösen ord (SSPR).
>
> **Om du är en slutanvändare och behöver gå tillbaka till ditt konto går du till [https://aka.ms/sspr](https://aka.ms/sspr)**.

[Lösen ords återställning via självbetjäning (SSPR)](https://www.youtube.com/watch?v=pS3XwfxJrMo) är en Azure Active Directory (AD)-funktion som gör det möjligt för användare att återställa sina lösen ord utan att kontakta IT-avdelningen för hjälp. Användarna kan snabbt avblockera sig själva och fortsätta att arbeta oavsett var de befinner sig eller tid på dagen. Genom att tillåta att medarbetarna avblockerar sig själva kan din organisation minska den icke produktiva tiden och höga support kostnader för de flesta vanliga problem som rör lösen ord.

SSPR har följande viktiga funktioner:

* Med självbetjäning kan slutanvändare återställa sina utgångna eller inaktuella lösen ord utan att kontakta administratören eller supportavdelningen för support.
* Med [tillbakaskrivning av lösen ord](./concept-sspr-writeback.md) kan du hantera lokala lösen ord och lösa konto utelåsning även i molnet.
* Med aktivitets rapporter för lösen ords hantering får administratörer insikter om lösen ords återställning och registrerings aktivitet som inträffar i organisationen.

Den här distributions guiden visar hur du planerar och testar en SSPR-lansering.

För att snabbt se SSPR i praktiken och återgå sedan till att förstå ytterligare distributions överväganden:

> [!div class="nextstepaction"]
> [Aktivera självbetjäning för återställning av lösen ord (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Lär dig mer om SSPR

Läs mer om SSPR. Se [hur det fungerar: lösen ords återställning](./concept-sspr-howitworks.md)via självbetjäning i Azure AD.

### <a name="key-benefits"></a>Viktiga fördelar

De främsta fördelarna med att aktivera SSPR är:

* **Hantera kostnad**. SSPR minskar kostnaderna för IT-supporten genom att göra det möjligt för användare att återställa lösen ord på egen hand. Det minskar också tiden som förlorades på grund av förlorade lösen ord och utelåsningar. 

* **Intuitiv användar upplevelse**. Det ger en intuitiv användar registrerings process med en gång som gör det möjligt för användare att återställa lösen ord och avblockera konton på begäran från valfri enhet eller plats. SSPR gör det möjligt för användare att gå tillbaka till arbetet snabbare och bli mer produktiva.

* **Flexibilitet och säkerhet**. SSPR gör det möjligt för företag att komma åt den säkerhet och flexibilitet som en moln plattform erbjuder. Administratörer kan ändra inställningarna så att de uppfyller nya säkerhets krav och göra ändringarna ut för användarna utan att störa deras inloggning.

* **Robust gransknings-och användnings spårning**. En organisation kan se till att affärs systemen förblir skyddade medan användarna återställer sina egna lösen ord. Robusta gransknings loggar innehåller information om varje steg i processen för lösen ords återställning. Dessa loggar är tillgängliga från ett API och gör det möjligt för användaren att importera data till ett SIEM-system (security incident and Event Monitoring Event Monitoring).

### <a name="licensing"></a>Licensiering

Azure Active Directory är licensierad per användare, vilket innebär att varje användare kräver en lämplig licens för de funktioner som de använder. Vi rekommenderar gruppbaserad licensiering för SSPR. 

Information om hur du jämför versioner och funktioner och aktiverar grupp-eller användarbaserad licensiering finns i [licens krav för återställning av lösen ord för Azure AD Self-Service](./concept-sspr-licensing.md).

Mer information om priser finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Förutsättningar

* En aktiv Azure AD-klientorganisation med minst en aktiverad utvärderingslicens. Om det behövs kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ett konto med behörigheter som global administratör.


### <a name="training-resources"></a>Utbildnings resurser

| Resurser| Länk och beskrivning |
| - | - |
| Video| [Ge dina användare bättre IT-skalbarhet](https://youtu.be/g9RpRnylxS8) 
| |[Vad är självbetjäning av lösenordsåterställning?](https://youtu.be/hc97Yx5PJiM)|
| |[Distribuera återställning av lösen ord för självbetjäning](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Så här aktiverar och konfigurerar du SSPR i Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ)|
| |[Hur konfigurerar jag lösen ords återställning via självbetjäning för användare i Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Så här [förbereder du användare att] registrera [deras] säkerhets information för Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Onlinekurser|[Hantera identiteter i Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Använd SSPR för att ge användarna en modern, skyddad upplevelse. Se särskilt modulen "[hantera Azure Active Directory användare och grupper](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Pluralsight betalda kurser |[Problem med identitets-och åtkomst hantering](https://www.pluralsight.com/courses/identity-access-management-issues) Lär dig mer om IAM och säkerhets problem som är medvetna om i din organisation. Se i synnerhet modulen "andra autentiseringsmetoder".|
| |[Komma igång med Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Lär dig metod tips för att utöka lokala till gångar till molnet på ett sätt som möjliggör autentisering, auktorisering, kryptering och en säker mobil miljö. Se särskilt modulen "Konfigurera avancerade funktioner i Microsoft Azure Active Directory Premium".
|Självstudier |[Utföra en pilotlansering av självbetjäning av lösenordsåterställning för Azure AD](./tutorial-enable-sspr.md) |
| |[Aktivera tillbakaskrivning av lösenord](./tutorial-enable-sspr-writeback.md) |
| |[Återställning av Azure AD-lösenord från inloggnings skärmen för Windows 10](./howto-sspr-windows.md) |
| Vanliga frågor|[Vanliga frågor och svar om lösen ords hantering](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>Lösningsarkitektur

I följande exempel beskrivs lösnings arkitekturen för lösen ords återställning för vanliga hybrid miljöer.

![diagram över lösnings arkitektur](./media/howto-sspr-deployment//solutions-architecture.png)

Beskrivning av arbets flöde

För att återställa lösen ordet går användarna till [portalen för återställning av lösen ord](https://aka.ms/sspr). De måste verifiera den tidigare registrerade autentiseringsmetoden eller metoderna för att bevisa sin identitet. Om lösen ordet har återställts påbörjas återställnings processen.

* För endast molnbaserade användare lagrar SSPR det nya lösen ordet i Azure AD. 

* För Hybrid användare skriver SSPR tillbaka lösen ordet till lokal-Active Directory via tjänsten Azure AD Connect. 

Obs! för användare som har [hash-synkronisering för lösen ord (PHS)](../hybrid/whatis-phs.md) inaktive rad lagrar SSPR lösen orden endast i lokal-Active Directory.

### <a name="best-practices"></a>Bästa praxis

Du kan hjälpa användarna att registrera sig snabbt genom att distribuera SSPR tillsammans med en annan populär applikation eller tjänst i organisationen. Den här åtgärden genererar en stor volym av inloggningar och kommer att driva registrering.

Innan du distribuerar SSPR kan du välja att fastställa antalet och den genomsnittliga kostnaden för varje samtal för återställning av lösen ord. Du kan använda den här data publicerings distributionen för att visa värdet som SSPR ansluter till organisationen.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Aktivera kombinerad registrering för SSPR och MFA

Microsoft rekommenderar att organisationer aktiverar den kombinerade registrerings upplevelsen för SSPR och Multi-Factor Authentication. När du aktiverar den här kombinerade registrerings upplevelsen behöver användarna bara välja sin registrerings information en gång för att aktivera båda funktionerna.

Den kombinerade registrerings upplevelsen kräver inte att organisationer aktiverar både SSPR och Azure AD Multi-Factor Authentication. Kombinerad registrering ger organisationer en bättre användar upplevelse. Mer information finns i [kombinera säkerhets informations registrering](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planera distributions projektet

Överväg organisationens behov medan du fastställer strategin för den här distributionen i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du engagerar rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att från intressenter-rollerna i projektet är väl förstå genom att dokumentera intressenterna och deras ingångs-och Accountabilities i projektet.

#### <a name="required-administrator-roles"></a>Nödvändiga administratörs roller


| Affärs roll/persona| Azure AD-roll (om det behövs) |
| - | - |
| Nivå 1-helpdesk| Lösenordsadministratör |
| Nivå 2-supportavdelningen| Användaradministratör |
| SSPR-administratör| Global administratör |


### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Du bör proaktivt kommunicera med dina användare hur deras upplevelse kommer att ändras, när den kommer att ändras och hur du får support om de drabbas av problem. Granska distributions [materialet för lösen ords återställning via självbetjäning på Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56768) för att få tips om hur du planerar din slut användar kommunikations strategi.

### <a name="plan-a-pilot"></a>Planera en pilot

Vi rekommenderar att den inledande konfigurationen av SSPR är i en test miljö. Börja med en pilot grupp genom att aktivera SSPR för en delmängd av användare i din organisation. Se [metod tips för en pilot](../fundamentals/active-directory-deployment-plans.md).

Information om hur du skapar en grupp finns i så här [skapar du en grupp och lägger till medlemmar i Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md). 

## <a name="plan-configuration"></a>Planera konfiguration

Följande inställningar krävs för att aktivera SSPR tillsammans med rekommenderade värden.

| Område | Inställning | Värde |
| --- | --- | --- |
| **Egenskaper för SSPR** | Återställning av lösen ord för självbetjäning har Aktiver ATS | **Vald** grupp för pilot/ **alla** för produktion |
| **Autentiseringsmetoder** | Autentiseringsmetoder som krävs för registrering | Alltid 1 mer än vad som krävs för återställning |
|   | Autentiseringsmetoder som krävs för att återställa | En eller två |
| **Registrering** | Kräv att användare registrerar sig vid inloggning | Yes |
|   | Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation | 90 – 180 dagar |
| **Aviseringar** | Meddela användare om lösenordsåterställning | Yes |
|   | Meddela alla administratörer när andra administratörer återställer sina lösenord | Yes |
| **Anpassning** | Anpassa supportavdelningen-länk | Yes |
|   | E-post eller URL för anpassad helpdesk | Support webbplats eller e-postadress |
| **Lokal integration** | Skriv tillbaka lösen ord till lokal AD | Yes |
|   | Tillåt att användare låser upp kontot utan att återställa lösen ordet | Yes |

### <a name="sspr-properties"></a>Egenskaper för SSPR

När du aktiverar SSPR väljer du en lämplig säkerhets grupp i pilot miljön.

* Om du vill framtvinga SSPR-registrering för alla rekommenderar vi att du använder alternativet **alla** .
* Annars väljer du en lämplig Azure AD-eller AD-säkerhetsgrupp.

### <a name="authentication-methods"></a>Autentiseringsmetoder

När SSPR är aktive rad kan användarna bara återställa sina lösen ord om de har data som finns i de autentiseringsmetoder som administratören har aktiverat. Metoderna är telefon, meddelandeautentisering, säkerhets frågor osv. Mer information finns i [Vad är autentiseringsmetoder?](./concept-authentication-methods.md).

Vi rekommenderar följande inställningar för autentiseringsmetoder:

* Ange de **autentiseringsmetoder som krävs för att registrera** minst en över det antal som krävs för att återställa. Att tillåta flera autentiseringar ger användarna flexibilitet när de behöver återställa.

* Ange det **antal metoder som krävs för att återställa** till en nivå som är lämplig för din organisation. En kräver minst friktion, medan två kan öka din säkerhets position. 

Obs! användaren måste ha de autentiseringsmetoder som kon figurer ATS i [lösen ords principer och begränsningar i Azure Active Directory](./concept-sspr-policy.md).

### <a name="registration-settings"></a>Registrerings inställningar

Ange **Kräv att användare registrerar sig när de loggar in** på **Ja**. Den här inställningen kräver att användare registrerar sig vid inloggning, vilket säkerställer att alla användare är skyddade.

Ange **antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** till mellan **90** och **180** dagar, om inte din organisation har ett affärs behov för kortare tidsram.

### <a name="notifications-settings"></a>Aviserings inställningar

Konfigurera både **meddela användare om lösen ords** återställning och **meddela alla administratörer när andra administratörer återställer sina lösen ord** till **Ja**. Att välja **Ja** på båda ökar säkerheten genom att se till att användarna är medvetna när deras lösen ord återställs. Det säkerställer också att alla administratörer är medvetna när en administratör ändrar ett lösen ord. Om användare eller administratörer får ett meddelande och de inte har initierat ändringen kan de omedelbart rapportera ett eventuellt säkerhets problem.

### <a name="customization-settings"></a>Anpassnings inställningar

Det är viktigt att anpassa e-postadressen eller URL: en för supportavdelningen för att säkerställa att användare som upplever problem kan få hjälp direkt. Ange det här alternativet till en vanlig e-postadress för supportavdelningen eller en webb sida som användarna är bekanta med. 

Mer information finns i [Anpassa Azure AD-funktionen för återställning av lösen ord via självbetjäning](./howto-sspr-customization.md).

### <a name="password-writeback"></a>Tillbakaskrivning av lösen ord

**Tillbakaskrivning av lösen ord** aktive ras med [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) och skriver lösen ords återställning i molnet till en befintlig lokal katalog i real tid. Mer information finns i [Vad är tillbakaskrivning av lösen ord?](./concept-sspr-writeback.md)

Vi rekommenderar följande inställningar:

* Se till att **Skriv tillbaka lösen ord till lokal AD** har angetts till **Ja**. 
* Ange **Tillåt att användare låser upp kontot utan att återställa lösen ordet** till **Ja**.

Som standard låser Azure AD upp konton när en lösen ords återställning utförs.

### <a name="administrator-password-setting"></a>Inställning av administratörs lösen ord

Administratörs konton har förhöjd behörighet. Lokala företags administratörer eller domän administratörer kan inte återställa sina lösen ord via SSPR. Lokala administratörs konton har följande begränsningar:

* Det går bara att ändra lösen ordet i sin lokal-miljö.
* kan aldrig använda de hemliga frågorna och svaren som en metod för att återställa lösen ordet.

Vi rekommenderar att du inte synkroniserar dina lokal Active Directory administratörs konton med Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Miljöer med flera identitets hanterings system

Vissa miljöer har flera identitets hanterings system. Lokala identitets hanterare som Oracle AM och SiteMinder, kräver synkronisering med AD för lösen ord. Du kan göra detta med hjälp av ett verktyg som PCNS (Password Change Notification Service) med Microsoft Identity Manager (MIM). Om du vill ha mer information om det här komplexa scenariot kan du läsa artikeln [distribuera MIM-tjänsten för meddelanden om lösen ords ändring på en](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)domänkontrollant.

## <a name="plan-testing-and-support"></a>Planera testning och support

I varje steg i distributionen från initiala pilot grupper genom hela organisationen, se till att resultaten är som förväntat.

### <a name="plan-testing"></a>Planera testning

Du kan se till att distributionen fungerar som förväntat genom att planera en uppsättning test fall för att verifiera implementeringen. För att utvärdera test fallen behöver du en icke-administratörs test användare med ett lösen ord. Om du behöver skapa en användare, se [lägga till nya användare till Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).

Följande tabell innehåller användbara test scenarier som du kan använda för att dokumentera dina organisationers förväntade resultat baserat på dina principer.
<br>


| Affärs ärende| Förväntat resultat |
| - | - |
| SSPR-portalen är tillgänglig i företags nätverket| Bestäms av din organisation |
| SSPR-portalen kan nås utanför företags nätverket| Bestäms av din organisation |
| Återställ användar lösen ord från webbläsare när användaren inte har Aktiver ATS för lösen ords återställning| Användaren kan inte komma åt flödet för lösen ords återställning |
| Återställ användar lösen ord från webbläsare när användaren inte har registrerats för lösen ords återställning| Användaren kan inte komma åt flödet för lösen ords återställning |
| Användaren loggar in när den tvingas att göra en registrering av lösen ords återställning| Användaren ombeds att registrera säkerhets information |
| Användaren loggar in när registreringen av lösen ords återställning har slutförts| Användaren ombeds att registrera säkerhets information |
| SSPR-portalen är tillgänglig när användaren inte har någon licens| Är tillgänglig |
| Återställ användar lösen ord från Windows 10 Azure AD-ansluten eller hybrid Azure AD-ansluten enhets Lås skärm| Användare kan återställa lösen ord |
| SSPR-registrering och användnings data är tillgängliga för administratörer i nära real tid| Är tillgänglig via gransknings loggar |

Du kan också läsa om hur du [Slutför Azure AD självbetjänings-pilot för återställning av lösen ord](./tutorial-enable-sspr.md). I den här självstudien får du aktivera en pilot lansering av SSPR i din organisation och testa med ett konto som inte är administratörs konto.

### <a name="plan-support"></a>Planera support

Medan SSPR vanligt vis inte skapar användar problem är det viktigt att förbereda support personal för att hantera problem som kan uppstå. Även om en administratör kan återställa lösen ordet för slutanvändare via Azure AD-portalen, är det bättre att hjälpa till att lösa problemet via en självbetjänings support process.

Om du vill göra det möjligt för support teamet att lyckas kan du skapa vanliga frågor och svar baserat på frågor som du får från användarna. Några exempel:

| Scenarier| Description |
| - | - |
| Användaren har inga registrerade autentiseringsmetoder tillgängliga| En användare försöker återställa sitt lösen ord men har inte någon av de autentiseringsmetoder som de registrerade är tillgängliga (exempel: de lämnade sin mobil telefon hemma och har inte åtkomst till e-post) |
| Användaren får inte någon text eller ett anrop på deras telefonnummer till kontoret eller mobil telefonen| En användare försöker verifiera sin identitet via text eller anrop, men får ingen text/ett anrop. |
| Användaren har inte åtkomst till portalen för återställning av lösen ord| En användare vill återställa sitt lösen ord men har inte Aktiver ATS för lösen ords återställning och kan inte komma åt sidan för att uppdatera lösen ord. |
| Användaren kan inte ange ett nytt lösen ord| En användare har slutfört verifieringen under flödet för lösen ords återställning, men kan inte ange ett nytt lösen ord. |
| Användaren ser ingen länk för återställning av lösen ord på en Windows 10-enhet| En användare försöker återställa lösen ordet från skärmen för Windows 10-lås, men enheten är inte ansluten till Azure AD eller också är inte Intune-enhets principen aktive rad |

### <a name="plan-rollback"></a>Planera återställning

Återställa distributionen:

* ta bort användaren från säkerhets gruppen för en enskild användare 

* för en grupp tar du bort gruppen från SSPR-konfigurationen

* Inaktivera SSPR för Azure AD-klienten för alla.

## <a name="deploy-sspr"></a>Distribuera SSPR

Innan du distribuerar bör du kontrol lera att du har gjort följande:

1. Skapade och började köra [kommunikations planen](#plan-communications).

1. Fastställde lämpliga [konfigurations inställningar](#plan-configuration).

1. Identifierat användare och grupper för [pilot](#plan-a-pilot) -och produktions miljöerna.

1. [Konfigurations inställningarna](#plan-configuration) för registrering och självbetjäning har fastställts.

1. [Konfigurerad tillbakaskrivning av lösen ord](#password-writeback) om du har en hybrid miljö.


**Nu är du redo att distribuera SSPR!**

Se [Aktivera självbetjäning för återställning av lösen ord](./tutorial-enable-sspr.md#enable-self-service-password-reset) för fullständiga steg-för-steg-instruktioner om hur du konfigurerar följande områden.

1. [Autentiseringsmetoder](./concept-authentication-methods.md)

1. [Registrerings inställningar](./concept-registration-mfa-sspr-combined.md)

1. [Aviserings inställningar](#notifications-settings)

1. [Anpassnings inställningar](./howto-sspr-customization.md)

1. [Lokal integration](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>Aktivera SSPR i Windows
För datorer som kör Windows 7, 8, 8,1 och 10 kan du [göra det möjligt för användare att återställa sina lösen ord på inloggnings skärmen för Windows](./howto-sspr-windows.md)

## <a name="manage-sspr"></a>Hantera SSPR

Azure AD kan ge ytterligare information om SSPR-prestanda via granskningar och rapporter.

### <a name="password-management-activity-reports"></a>Aktivitets rapporter för lösen ords hantering 

Du kan använda färdiga rapporter på Azure Portal för att mäta SSPR prestanda. Om du är korrekt licensierad kan du också skapa anpassade frågor. Mer information finns i [rapporterings alternativ för lösen ords hantering i Azure AD](./howto-sspr-reporting.md)

> [!NOTE]
>  Du måste vara [Global administratör](../roles/permissions-reference.md)och du måste säga att dessa data ska samlas in för din organisation. Om du vill välja måste du gå till fliken rapportering eller gransknings loggarna på Azure Portal minst en gång. Till dess kommer data inte att samlas in för din organisation.

Gransknings loggar för registrering och återställning av lösen ord är tillgängliga i 30 dagar. Om säkerhets granskning i företaget kräver längre kvarhållning måste loggarna exporteras och förbrukas i ett SIEM-verktyg, till exempel [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk eller ArcSight.

![Skärm bild för SSPR repor ting](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Autentiseringsmetoder – användning och insikter

Med [användning och insikter](./howto-authentication-methods-activity.md) kan du förstå hur autentiseringsmetoder för funktioner som Azure AD MFA och SSPR arbetar i din organisation. Den här rapporterings funktionen ger din organisation möjlighet att förstå vilka metoder som registreras och hur de används.

### <a name="troubleshoot"></a>Felsöka

* Se [fel sökning av lösen ords återställning via självbetjäning](./troubleshoot-sspr.md) 

* Följ [vanliga frågor och svar om lösen ords hantering](./active-directory-passwords-faq.md) 

### <a name="helpful-documentation"></a>Användbar dokumentation

* [Vad är autentiseringsmetoder?](./concept-authentication-methods.md)

* [Så här fungerar det: återställning av lösen ord för självbetjäning i Azure AD?](./concept-sspr-howitworks.md)

* [Anpassa Azure AD-funktionen för lösen ords återställning via självbetjäning](./howto-sspr-customization.md)

* [Lösenordsprinciper och begränsningar i Azure Active Directory](./concept-sspr-policy.md)

* [Vad är tillbakaskrivning av lösen ord?](./concept-sspr-writeback.md)

## <a name="next-steps"></a>Nästa steg

* För att komma igång med att distribuera SSPR, se [Aktivera återställning av lösen ord för självbetjäning i Azure AD](tutorial-enable-sspr.md)

* [Överväg att implementera lösen ords skydd i Azure AD](./concept-password-ban-bad.md)

* [Överväg att implementera Azure AD Smart utelåsning](./howto-password-smart-lockout.md)