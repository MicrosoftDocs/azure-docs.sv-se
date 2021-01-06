---
title: Referens guide för Azure Active Directory styrnings åtgärder
description: Den här åtgärds hand boken beskriver de kontroller och åtgärder som du bör vidta för att skydda styrnings hanteringen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: ba3ca140abe36a31ffa03422420ea537bbe4f39a
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935758"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Referens guide för Azure Active Directory styrnings åtgärder

Det här avsnittet i [hand boken för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskriver de kontroller och åtgärder som du bör vidta för att utvärdera och intyga åtkomsten för icke-privilegierade och privilegierade identiteter, granskning och kontroll av miljö ändringar.

> [!NOTE]
> Dessa rekommendationer är aktuella vid publicerings datumet, men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina styrnings metoder när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till viktiga uppgifter

Hantering av Azure Active Directory kräver kontinuerlig körning av viktiga operativa uppgifter och processer, som kanske inte ingår i ett lanserings projekt. Det är fortfarande viktigt att du konfigurerar dessa uppgifter för att optimera din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Aktivitet | Ägare |
| :- | :- |
| Arkivera Azure AD audit-loggar i SIEM system | Åtgärds team för informations säkerhet |
| Identifiera program som hanteras av efterlevnad | IAM-åtgärds team |
| Regelbundet granska åtkomst till program | Arkitektur team för informations säkerhet |
| Granska regelbundet åtkomst till externa identiteter | Arkitektur team för informations säkerhet |
| Granska regelbundet vilka som har privilegierade roller | Arkitektur team för informations säkerhet |
| Definiera säkerhets portar för att aktivera privilegierade roller | Arkitektur team för informations säkerhet |
| Granska godkännande bidrag regelbundet | Arkitektur team för informations säkerhet |
| Utforma kataloger och åtkomst paket för program och resurser som är baserade på anställda i organisationen | App-ägare |
| Definiera säkerhets principer för att tilldela användare åtkomst till paket | Informations informations team + program ägare |
| Om principerna omfattar godkännande arbets flöden, granskar du regelbundet arbets flödes godkännanden | App-ägare |
| Granska undantag i säkerhets principer, till exempel principer för villkorlig åtkomst, med åtkomst granskningar | Åtgärds team för informations säkerhet |

När du granskar listan kanske du måste tilldela en ägare för aktiviteter som saknar ägare eller justera ägarskapet för aktiviteter med ägare som inte är justerade enligt rekommendationerna ovan.

#### <a name="owner-recommended-reading"></a>Rekommenderad läsning av ägare

- [Tilldela administratörsroller i Azure Active Directory](../roles/permissions-reference.md)
- [Styrning i Azure](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>Testning av konfigurations ändringar

Det finns ändringar som kräver särskilda överväganden vid testning, från enkla metoder som att distribuera en mål del uppsättning av användare för att distribuera en ändring i en parallell test klient. Om du inte har implementerat en test strategi bör du definiera en test metod baserat på rikt linjerna i tabellen nedan:

| Scenario| Rekommendation |
|-|-|
|Ändra autentiseringstypen från federerad till PHS/PTA eller vice versa| Använd [stegvis](../hybrid/how-to-connect-staged-rollout.md) distribution för att testa effekten av att ändra autentiseringstypen.|
|Distribuera en ny princip för villkorlig åtkomst (CA) eller identitets skydds princip|Skapa en ny princip för villkorlig åtkomst och tilldela till test användare.|
|Registrera en test miljö för ett program|Lägg till programmet i en produktions miljö, Dölj det från panelen för appar och tilldela det för att testa användare under fasen kvalitets säkring (frågor och svar).|
|Ändra regler för synkronisering|Genomför ändringarna i ett test Azure AD Connect med samma konfiguration som för närvarande i produktion, även kallat mellanlagrings läge och analysera CSExport resultat. Om det är uppfyllt byter du till produktion när du är klar.|
|Ändring av anpassning|Testa i en separat test klient.|
|Distribuera en ny funktion|Om funktionen stöder distribution till en mål uppsättning användare identifierar du pilot användare och bygger ut. Till exempel kan självbetjäning för återställning av lösen ord och Multi-Factor Authentication rikta specifika användare eller grupper.|
|Start punkt ett program från en lokal identitets leverantör (IdP), till exempel Active Directory, till Azure AD|Om programmet stöder flera IdP-konfigurationer, till exempel Salesforce, konfigurerar du både och testar Azure AD under ett ändrings fönster (om programmet introducerar HRD-sidan). Om programmet inte stöder flera IDP: er kan du schemalägga testningen under ett fönster för ändrings kontroll och avbrotts tid för programmet.|
|Uppdatera dynamiska grupp regler|Skapa en parallell dynamisk grupp med den nya regeln. Jämför med det beräknade resultatet, till exempel, kör PowerShell med samma villkor.<br>Om test pass kan du byta plats där den gamla gruppen användes (om möjligt).|
|Migrera produkt licenser|Se [ändra licensen för en enskild användare i en licensierad grupp i Azure Active Directory](../enterprise-users/licensing-groups-change-licenses.md).|
|Ändra AD FS regler, till exempel auktorisering, utfärdande, MFA|Använd grupp anspråk för att ange en delmängd av användarna.|
|Ändra AD FS autentisering eller liknande ändringar i Server gruppen|Skapa en parallell Server grupp med samma värdnamn, implementera konfigurations ändringar, testa från klienter som använder HOSTs-filer, regler för NLB-routning eller liknande routning.<br>Om mål plattformen inte stöder HOSTs-filer (t. ex. mobila enheter) styr du ändringen.|

## <a name="access-reviews"></a>Åtkomstgranskningar

### <a name="access-reviews-to-applications"></a>Åtkomst granskningar till program

Med tiden kan användare ackumulera åtkomst till resurser när de flyttas i olika team och på olika platser. Det är viktigt att resurs ägare granskar åtkomsten till program regelbundet och tar bort privilegier som inte längre behövs under användarens livs cykel. Med Azure AD [Access-granskningar](../governance/access-reviews-overview.md) kan organisationer effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Resurs ägare bör granska användarnas åtkomst regelbundet för att se till att endast rätt personer har fortsatt åtkomst. Vi rekommenderar att du använder Azure AD åtkomst granskningar för den här aktiviteten.

![Start sida för åtkomst granskningar](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Varje användare som interagerar med åtkomst granskningar måste ha en betald Azure AD Premium P2-licens.

### <a name="access-reviews-to-external-identities"></a>Åtkomst granskningar för externa identiteter

Det är viktigt att behålla åtkomst till externa identiteter som endast är begränsade till resurser som behövs, under den tid som krävs. Upprätta en normal automatiserad åtkomst gransknings process för alla externa identiteter och program åtkomst med hjälp av [åtkomst granskningar](../governance/access-reviews-overview.md)i Azure AD. Om det redan finns en process lokalt bör du överväga att använda Azure AD Access-granskningar. När ett program har dragits tillbaka eller inte längre används tar du bort alla externa identiteter som hade åtkomst till programmet.

> [!NOTE]
> Varje användare som interagerar med åtkomst granskningar måste ha en betald Azure AD Premium P2-licens.

## <a name="privileged-account-management"></a>Hantering av privilegierade konton

### <a name="privileged-account-usage"></a>Användning av privilegierade konton

Hackare kan ofta rikta in sig på administratörs konton och andra delar av privilegie rad åtkomst för att snabbt få åtkomst till känsliga data och system. Eftersom användare med privilegierade roller tenderar att ackumuleras över tid, är det viktigt att granska och hantera administratörs åtkomst regelbundet och ge just-in-Time-åtkomst till Azure AD och Azure-resurser.

Om det inte finns någon process i organisationen för att hantera privilegierade konton, eller om du för närvarande har administratörer som använder sina vanliga användar konton för att hantera tjänster och resurser, bör du omedelbart börja använda separata konton, till exempel en för vanliga dagliga aktiviteter. den andra för privilegie rad åtkomst och konfigurerad med MFA. Om din organisation har en Azure AD Premium P2-prenumeration ännu bättre, bör du omedelbart distribuera [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md#license-requirements) (PIM). I samma token bör du även granska de privilegierade kontona och [tilldela mindre privilegierade roller](../roles/security-planning.md) om tillämpligt.

En annan aspekt av Privileged Account Management som bör implementeras är att definiera [åtkomst granskningar](../governance/access-reviews-overview.md) för dessa konton, antingen manuellt eller [automatiserat via PIM](../privileged-identity-management/pim-how-to-perform-security-review.md).

#### <a name="privileged-account-management-recommended-reading"></a>Privilegie rad konto hantering rekommenderat läsning

- [Roller i Azure AD Privileged Identity Management](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>Konton för nöd åtkomst

Organisationer måste skapa [nöd konton](../roles/security-emergency-access.md) för att kunna hantera Azure AD för fall som autentiserings avbrott som:

- Avbrotts komponenter för infrastruktur för autentisering (AD FS, lokal AD, MFA-tjänst)
- Omsättning för administrativ personal

För att förhindra oavsiktligt låst av klienten, eftersom du inte kan logga in eller aktivera en befintlig enskild användares konto som administratör, bör du skapa två eller flera nödfalls konton och se till att de är implementerade och justerade med [Microsofts bästa praxis](../roles/security-planning.md) och [avbrotts glas procedurer](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Privilegie rad åtkomst till Azure EA-portalen

Med [azure Enterprise-avtal-portalen (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) kan du skapa Azure-prenumerationer mot en huvud Enterprise-avtal, vilket är en kraftfull roll i företaget. Det är vanligt att du startar skapandet av den här portalen innan du kan komma igång med Azure AD, så det är nödvändigt att använda Azure AD-identiteter för att låsa den, ta bort personliga konton från portalen, se till att rätt delegering är på plats och minimera risken för utelåsning.

Om du vill att nivån för EA-portalen för närvarande är "blandat läge" måste du ta bort alla Microsoft- [konton](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) från all privilegie rad åtkomst i EA-portalen och konfigurera EA-portalen så att endast Azure AD-konton används. Om de delegerade rollerna för EA-portalen inte har kon figurer ATS, bör du även hitta och implementera delegerade roller för avdelningar och konton.

#### <a name="privileged-access-recommended-reading"></a>Privilegie rad åtkomst Rekommenderad läsning

- [Behörigheter för administratörsrollen i Azure Active Directory](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>Berättigandehantering

[Hantering av rättigheter (EM)](../governance/entitlement-management-overview.md) gör det möjligt för appar att paketera resurser och tilldela dem till vissa personer i organisationen (både interna och externa). EM tillåter självbetjänings registrering och delegering till företags ägare samtidigt som styrnings principer används för att ge åtkomst, ange åtkomst varaktighet och tillåta arbets flöden för godkännande. 

> [!NOTE]
> Hantering av Azure AD-anspråk kräver Azure AD Premium P2-licenser.

## <a name="summary"></a>Sammanfattning

Det finns åtta aspekter av en säker identitets styrning. I den här listan får du hjälp att identifiera de åtgärder som du bör vidta för att utvärdera och intyga åtkomsten för icke privilegierade och privilegierade identiteter, gransknings-och kontroll ändringar i miljön.

- Tilldela ägare till viktiga uppgifter.
- Implementera en test strategi.
- Använd åtkomst granskningar i Azure AD för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar.
- Upprätta en regelbunden, automatiserad åtkomst gransknings process för alla typer av externa identiteter och program åtkomst.
- Upprätta en gransknings process för åtkomst för att granska och hantera administratörs åtkomst regelbundet och ge just-in-Time-åtkomst till Azure AD och Azure-resurser.
- Etablera nöd konton som förbereds för att hantera Azure AD för oväntade avbrott.
- Lås åtkomst till Azure-EA-portalen.
- Implementera rättighets hantering för att ge reglerad åtkomst till en samling resurser.

## <a name="next-steps"></a>Nästa steg

Kom igång med [drift kontroller och åtgärder för Azure AD](active-directory-ops-guide-ops.md).