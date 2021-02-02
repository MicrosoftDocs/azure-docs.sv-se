---
title: Ta reda på när en speciell användare kan komma åt en app
description: Så här tar du reda på när en viktig användare kan komma åt ett program som du har konfigurerat för användar etablering med Azure AD
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
ms.openlocfilehash: 890c4a8a5aec3b15b150908c64bb114bd85a61a1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256770"
---
# <a name="check-the-status-of-user-provisioning"></a>Kontrol lera status för användar etablering

Azure AD Provisioning-tjänsten kör en första etablerings cykel mot käll systemet och mål systemet, följt av periodiska stegvisa cykler. När du konfigurerar etablering för en app kan du kontrol lera den aktuella statusen för etablerings tjänsten och se när en användare kommer att ha åtkomst till en app.

## <a name="view-the-provisioning-progress-bar"></a>Visa förlopps indikatorn för etablering

 På **etablerings** sidan för en app kan du visa statusen för Azure AD Provisioning-tjänsten. Avsnittet **aktuell status** längst ned på sidan visar om en etablerings cykel har börjat att etablering av användar konton. Du kan se förloppet för cykeln, se hur många användare och grupper som har etablerats och se hur många roller som skapas.

När du först konfigurerar automatisk etablering visas statusen för den inledande etablerings cykeln i avsnittet **aktuell status** längst ned på sidan. Det här avsnittet uppdateras varje gången en stegvis cykel körs. Följande information visas:
- Typ av etablerings cykel (initial eller stegvis) som körs för tillfället eller som senast slutfördes.
- En **förlopps indikator** som visar procent andelen av etablerings cykeln som har slutförts. Procent andelen visar antalet sidor som har allokerats. Observera att varje sida kan innehålla flera användare eller grupper, så att procent andelen inte direkt korreleras med antalet användare, grupper eller roller som tillhandahålls.
- En **uppdaterings** knapp som du kan använda för att hålla vyn uppdaterad.
- Antalet **användare** och **grupper** i data lagret för anslutningen. Antalet ökar när som helst ett objekt läggs till i omfånget för etableringen. Antalet går inte nedåt om en användare är mjuk raderad eller hårt borttagen eftersom detta inte tar bort objektet från anslutnings data lagret. Antalet beräknas om den första synkroniseringen när CD-skivorna har [återställts](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta) 
- En länk för **gransknings loggar** som öppnar Azure AD-etablerings loggar för information om alla åtgärder som körs av användar etablerings tjänsten, inklusive etablerings status för enskilda användare (se avsnittet [använda etablerings loggar](#use-provisioning-logs-to-check-a-users-provisioning-status) nedan).

När en etablerings cykel är klar visar **statistik till datum** avsnittet det ackumulerade antalet användare och grupper som har etablerats till datum, tillsammans med slutdatum och varaktighet för den senaste cykeln. **Aktivitets-ID: t** identifierar unikt den senaste etablerings cykeln. **Jobb-ID: t** är en unik identifierare för etablerings jobbet och är särskilt för appen i din klient organisation.

Etablerings förloppet kan visas i Azure Portal på fliken **Azure Active Directory &gt; företags &gt; \[ program namn \] &gt; etablering** .

![Förlopps indikator för etablerings Sidan](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Använd etablerings loggar för att kontrol lera en användares etablerings status

Om du vill se etablerings statusen för en vald användare, se [etablerings loggarna (för hands version)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) i Azure AD. Alla åtgärder som körs av användar etablerings tjänsten registreras i Azure AD-etablerings loggarna. Detta omfattar alla Läs-och skriv åtgärder som gjorts för käll-och mål systemen och de användar data som lästs eller skrevs under varje åtgärd.

Du kan komma åt etablerings loggarna i Azure Portal genom att välja **Azure Active Directory** &gt; etablerings loggar för **företags appar** &gt; **(för hands version)** i avsnittet **aktivitet** . Du kan söka i etablerings data baserat på användarens namn eller identifieraren i antingen käll systemet eller mål systemet. Mer information finns i [etablerings loggar (för hands version)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Etablerings loggarna registrerar alla åtgärder som utförs av etablerings tjänsten, inklusive:

* Fråga Azure AD efter tilldelade användare som är inom omfånget för etablering
* Fråga mål appen efter dessa användare
* Jämför användar objekt mellan systemet
* Lägga till, uppdatera eller inaktivera användar kontot i mål systemet baserat på jämförelsen

Mer information om hur du läser etablerings loggar i Azure Portal finns i [rapport guiden för etablering](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Hur lång tid tar det att etablera användare?
När du använder automatisk användar etablering med ett program, etablerar Azure AD automatiskt och uppdaterar användar konton i en app baserat på saker som [användar-och grupp tilldelning](../manage-apps/assign-user-or-group-access-portal.md) enligt ett regelbundet schemalagt tidsintervall, vanligt vis var 40: e minut.

Hur lång tid det tar för en specifik användare att tillhandahållas beror huvudsakligen på om ditt etablerings jobb kör en inledande cykel eller en stegvis cykel.

- För den **första cykeln** beror jobb tiden på många faktorer, inklusive antalet användare och grupper inom omfånget för etablering och det totala antalet användare och grupper i käll systemet. Den första synkroniseringen mellan Azure AD och en app kan ta var som helst från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. En omfattande lista över faktorer som påverkar inledande cykel prestanda sammanfattas senare i det här avsnittet.

- För **stegvisa cykler** efter den första cykeln, tenderar jobb tiderna att bli snabbare (till exempel inom 10 minuter), eftersom etablerings tjänsten lagrar vattenstämplar som representerar båda systemens tillstånd efter den första cykeln, vilket förbättrar prestandan för efterföljande synkroniseringar. Jobb tiden beror på antalet ändringar som har upptäckts i den etablerings cykeln. Om det finns mindre än 5 000 ändringar av användare eller grupp medlemskap kan jobbet avslutas inom en enda stegvis etablerings cykel. 

I följande tabell sammanfattas synkroniserings tider för vanliga etablerings scenarier. I dessa scenarier är käll systemet Azure AD och mål systemet är ett SaaS-program. Sync-tiderna härleds från en statistisk analys av synkroniseringsjobb för SaaS-programmen ServiceNow, Workplace, Salesforce och G Suite.


| Omfattnings konfiguration | Användare, grupper och medlemmar i omfattningen | Första cykel tid | Stegvis cykel tid |
| -------- | -------- | -------- | -------- |
| Synkronisera endast tilldelade användare och grupper |  < 1 000 |  < 30 minuter | < 30 minuter |
| Synkronisera endast tilldelade användare och grupper |  1 000 – 10 000 | 142 – 708 minuter | < 30 minuter |
| Synkronisera endast tilldelade användare och grupper |   10 000 – 100 000 | 1 170 – 2 340 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  < 1 000 | < 30 minuter  | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  1 000 – 10 000 | < 30-120 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  10 000 – 100 000  | 713 – 1 425 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD|  < 1 000  | < 30 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD | 1 000 – 10 000  | 43 – 86 minuter | < 30 minuter |

För den konfigurations **synkronisering som tilldelas användare och grupper** kan du använda följande formler för att fastställa den ungefärliga minsta och högsta förväntade **första cykel** tiden:

- Minsta antal minuter = 0,01 x [antal tilldelade användare, grupper och grupp medlemmar]
- Maximalt antal minuter = 0,08 x [antal tilldelade användare, grupper och grupp medlemmar]

Sammanfattning av faktorer som påverkar hur lång tid det tar att slutföra en **första cykel**:

- Det totala antalet användare och grupper i omfånget för etablering.

- Det totala antalet användare, grupper och grupp medlemmar som finns i käll systemet (Azure AD).

- Om användare i omfång för etablering matchas med befintliga användare i mål programmet eller behöver skapas för första gången. Synkroniseringsjobb för vilka alla användare skapas för första gången tar ungefär *två gånger så länge* som synkroniseringsjobb för vilka alla användare matchas till befintliga användare.

- Antal fel i [etablerings loggarna](check-status-user-account-provisioning.md). Prestanda är långsammare om det finns många fel och etablerings tjänsten har gått in i ett karantäns tillstånd. 

- Hastighets begränsningar och begränsning för begäran som implementeras av mål systemet. Vissa mål system implementerar begränsningar för begär ande hastighet och begränsning, vilket kan påverka prestanda under stora synkroniseringar. Under dessa villkor kan en app som tar emot för många begär Anden för snabbt få en långsam svars frekvens eller stänga anslutningen. För att förbättra prestanda måste anslutningen justeras genom att inte skicka app-begärandena snabbare än appen kan bearbeta dem. Etablerings anslutningar som skapats av Microsoft gör den här ändringen. 

- Antalet och storlekarna för tilldelade grupper. Synkronisering av tilldelade grupper tar längre tid än att synkronisera användare. Både antalet och storlekarna i de tilldelade grupperna påverkar prestanda. Om ett program har [mappningar aktiverade för synkronisering av grupp objekt](customize-application-attributes.md#editing-group-attribute-mappings)synkroniseras grupp egenskaper, till exempel grupp namn och medlemskap, förutom användare. Dessa ytterligare synkroniseringar tar längre tid än att synkronisera användar objekt.

- Om prestanda blir ett problem och du försöker etablera de flesta användare och grupper i din klient organisation använder du områdes filter. Med omfångs filter kan du finjustera de data som etablerings tjänsten extraherar från Azure AD genom att filtrera ut användare baserat på särskilda attributvärden. Mer information om omfångs filter finns i [attribut-baserad program etablering med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)