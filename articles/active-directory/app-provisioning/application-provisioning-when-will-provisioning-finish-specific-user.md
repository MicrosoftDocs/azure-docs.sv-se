---
title: Ta reda på när en viss användare kommer att kunna komma åt en app
description: Så här tar du reda på när en kritiskt viktig användare kan komma åt ett program som du har konfigurerat för användareablering med Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9835ba2b6db2d71d0ff5825f2eb1996133e75537
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530824"
---
# <a name="check-the-status-of-user-provisioning"></a>Kontrollera status för användareablering

Azure AD-etableringstjänsten kör en inledande etableringscykel mot källsystemet och målsystemet, följt av periodiska inkrementella cykler. När du konfigurerar etablering för en app kan du kontrollera etableringstjänstens aktuella status och se när en användare kommer att kunna komma åt en app.

## <a name="view-the-provisioning-progress-bar"></a>Visa förloppsfältet för etablering

 På sidan **Etablering för** en app kan du visa status för Azure AD-etableringstjänsten. Avsnittet **Aktuell status** längst ned på sidan visar om en etableringscykel har börjat etablera användarkonton. Du kan se förloppet för cykeln, se hur många användare och grupper som har etablerats och se hur många roller som skapas.

Första gången du konfigurerar automatisk etablering visar avsnittet **Aktuell status** längst ned på sidan statusen för den inledande etableringscykeln. Det här avsnittet uppdateras varje gång en inkrementell cykel körs. Följande information visas:
- Den typ av etableringscykel (inledande eller inkrementell) som körs eller senast slutfördes.
- En **förloppsstapel** som visar procentandelen av etableringscykeln som har slutförts. Procentandelen visar antalet etablerade sidor. Observera att varje sida kan innehålla flera användare eller grupper, så procentandelen korrelerar inte direkt med antalet användare, grupper eller roller som etablerats.
- En **uppdateringsknapp** som du kan använda för att hålla vyn uppdaterad.
- Antalet användare **och grupper** **i** anslutningsdatalagret. Antalet ökar varje gång ett objekt läggs till i etableringsomfånget. Antalet går inte ned om en användare är mjukt borttagna eller hårdborttagna eftersom detta inte tar bort objektet från anslutningsappens datalager. Antalet räknas om den första synkroniseringen när CDS har [återställts](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) 
- Länken **Visa granskningsloggar** öppnar Azure AD-etableringsloggarna för information om alla åtgärder som körs av användaretableringstjänsten, inklusive etableringsstatus för enskilda användare (se avsnittet Använd etableringsloggar nedan). [](#use-provisioning-logs-to-check-a-users-provisioning-status)

När en etableringscykel har  slutförts visar avsnittet Statistik hittills det ackumulerade antalet användare och grupper som har etablerats hittills, tillsammans med slutförandedatum och varaktighet för den senaste cykeln. **Aktivitets-ID:t** identifierar unikt den senaste etableringscykeln. **Jobb-ID:t** är en unik identifierare för etableringsjobbet och är specifik för appen i din klientorganisation.

Etableringsförloppet kan visas i Azure Portal på fliken Azure Active Directory Enterprise Apps-programnamn **&gt; &gt; \[ \] &gt; Etablering.**

![Förloppsfält för etableringssidan](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Använd etableringsloggar för att kontrollera en användares etableringsstatus

Om du vill se etableringsstatus för en vald användare kan du läsa [Etableringsloggar (förhandsversion)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) i Azure AD. Alla åtgärder som körs av tjänsten för användareablering registreras i Azure AD-etableringsloggarna. Detta omfattar alla läs- och skrivåtgärder som görs till käll- och målsystemen, samt användardata som har lästs eller skrivits under varje åtgärd.

Du kan komma åt etableringsloggarna i Azure Portal genom **att Azure Active Directory** etableringsloggar för Enterprise &gt; **Apps** &gt; **(förhandsversion)** i **avsnittet** Aktivitet. Du kan söka i etableringsdata baserat på namnet på användaren eller identifieraren i antingen källsystemet eller målsystemet. Mer information finns i [Etableringsloggar (förhandsversion).](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

Etableringsloggarna registrerar alla åtgärder som utförs av etableringstjänsten, inklusive:

* Fråga Azure AD efter tilldelade användare som omfattas av etablering
* Fråga målappen om förekomsten av dessa användare
* Jämföra användarobjekten mellan systemet
* Lägga till, uppdatera eller inaktivera användarkontot i målsystemet baserat på jämförelsen

Mer information om hur du läser etableringsloggarna i Azure Portal finns i guiden [för etableringsrapportering.](check-status-user-account-provisioning.md)

## <a name="how-long-will-it-take-to-provision-users"></a>Hur lång tid tar det att etablera användare?
När du använder automatisk användareablering med ett program etablerar och uppdaterar [](../manage-apps/assign-user-or-group-access-portal.md) Azure AD automatiskt användarkonton i en app baserat på saker som användar- och grupptilldelning enligt ett regelbundet schemalagt tidsintervall, vanligtvis var 40:e minut.

Hur lång tid det tar för en viss användare att etableras beror huvudsakligen på om etableringsjobbet kör en inledande cykel eller en inkrementell cykel.

- För **den inledande** cykeln beror jobbtiden på många faktorer, inklusive antalet användare och grupper i etableringsomfånget och det totala antalet användare och grupper i källsystemet. Den första synkroniseringen mellan Azure AD och en app kan ta allt från 20 minuter till flera timmar, beroende på Azure AD-katalogens storlek och antalet användare i etableringsomfånget. En omfattande lista över faktorer som påverkar den inledande cykelns prestanda sammanfattas senare i det här avsnittet.

- För  inkrementella cykler efter den första cykeln brukar jobbtiderna vara snabbare (t.ex. inom 10 minuter), eftersom etableringstjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande cykeln, vilket förbättrar prestandan för efterföljande synkroniseringar. Jobbtiden beror på antalet ändringar som identifierats i etableringscykeln. Om det finns färre än 5 000 ändringar av användar- eller gruppmedlemskap kan jobbet slutföras inom en enda inkrementell etableringscykel. 

I följande tabell sammanfattas synkroniseringstider för vanliga etableringsscenarier. I dessa scenarier är källsystemet Azure AD och målsystemet är ett SaaS-program. Synkroniseringstiderna härleds från en statistisk analys av synkroniseringsjobb för SaaS-programmen ServiceNow, Workplace, Salesforce och G Suite.


| Omfångskonfiguration | Användare, grupper och medlemmar i omfånget | Inledande cykeltid | Inkrementell cykeltid |
| -------- | -------- | -------- | -------- |
| Synkronisera endast tilldelade användare och grupper |  < 1 000 |  < 30 minuter | < 30 minuter |
| Synkronisera endast tilldelade användare och grupper |  1,000 - 10,000 | 142–708 minuter | < 30 minuter |
| Synkronisera endast tilldelade användare och grupper |   10,000 - 100,000 | 1 170–2 340 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  < 1 000 | < 30 minuter  | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  1,000 - 10,000 | < 30–120 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  10,000 - 100,000  | 713–1 425 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD|  < 1 000  | < 30 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD | 1,000 - 10,000  | 43–86 minuter | < 30 minuter |

För konfigurationen Synkronisera **endast tilldelad användare och** grupper kan du använda följande formler för att fastställa ungefärliga minsta och högsta förväntade inledande **cykeltider:**

- Minsta minuter = 0,01 x [antal tilldelade användare, grupper och gruppmedlemmar]
- Maximalt antal minuter = 0,08 x [antal tilldelade användare, grupper och gruppmedlemmar]

Sammanfattning av faktorer som påverkar den tid det tar att slutföra en **inledande cykel:**

- Det totala antalet användare och grupper i etableringsomfånget.

- Det totala antalet användare, grupper och gruppmedlemmar som finns i källsystemet (Azure AD).

- Om användare i etableringsomfånget matchas mot befintliga användare i målprogrammet eller måste skapas för första gången. Synkroniseringsjobb som alla användare skapas för  första gången tar ungefär dubbelt så lång tid som synkroniseringsjobb som alla användare matchas mot befintliga användare för.

- Antal fel i [etableringsloggarna](check-status-user-account-provisioning.md). Prestandan går långsammare om det finns många fel och etableringstjänsten har förts in i ett karantäntillstånd. 

- Hastighetsbegränsningar för förfrågningar och begränsningar som implementeras av målsystemet. Vissa målsystem implementerar begränsningar för begärandefrekvens och begränsning, vilket kan påverka prestanda under stora synkroniseringsåtgärder. Under dessa förhållanden kan en app som tar emot för många begäranden för snabbt sakta ned svarsfrekvensen eller stänga anslutningen. För att förbättra prestandan måste anslutningsappen justeras genom att inte skicka appbegäranden snabbare än appen kan bearbeta dem. Etableringsanslutningsappar som skapats av Microsoft gör den här justeringen. 

- Antal och storlekar för tilldelade grupper. Det tar längre tid att synkronisera tilldelade grupper än att synkronisera användare. Både antalet och storlekarna för de tilldelade grupperna påverkar prestandan. Om ett program har [mappningar aktiverade](customize-application-attributes.md#editing-group-attribute-mappings)för gruppobjektsynkronisering synkroniseras gruppegenskaper som gruppnamn och medlemskap utöver användare. Dessa ytterligare synkroniseringar tar längre tid än att bara synkronisera användarobjekt.

- Om prestandan blir ett problem och du försöker etablera de flesta användare och grupper i din klientorganisation använder du omfångsfilter. Med omfångsfilter kan du finjustera de data som etableringstjänsten extraherar från Azure AD genom att filtrera bort användare baserat på specifika attributvärden. Mer information om omfångsfilter finns i [Attributbaserad programetablering med omfångsfilter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)