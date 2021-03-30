---
title: Princip guide för support och upphör Ande för Azure gäst operativ system | Microsoft Docs
description: Ger information om vad Microsoft kommer att ha stöd för i det Azure gäst operativ system som används av Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "68945456"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Support och policy för Azure gäst operativ systemets support
Informationen på den här sidan gäller Azure gäst operativ system ([gäst](cloud-services-guestos-update-matrix.md)operativ system) för Cloud Services Worker och webb roller (PaaS). Den gäller inte för Virtual Machines (IaaS).

Microsoft har en publicerad [support princip för gäst operativ systemet](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Sidan som du läser beskriver nu hur principen implementeras.

Principen är

1. Microsoft kommer att stödja **minst de senaste två familjerna av gäst operativ systemet**. När en familj dras tillbaka, har kunder 12 månader från det officiella datumet för indragningen för att uppdatera till en nyare gäst operativ system familj som stöds.
2. Microsoft stöder **minst de senaste två versionerna av de gäst operativ system familjer som stöds**.
3. Microsoft stöder **minst de senaste två versionerna av Azure SDK**. När en version av SDK: n har dragits tillbaka kommer kunderna att ha 12 månader från det officiella datumet för indragningen för att uppdatera till en nyare version.

Ibland kan fler än två familjer eller versioner stödjas. Officiella gäst operativ systemets support information visas på [Azures OS-utgåvor och SDK-kompatibilitetens matris](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>När en gäst operativ system version dras tillbaka
Nya gäst operativ system **versioner** introduceras varannan månad för att införliva de senaste MSRC-uppdateringarna. På grund av de regelbundna månads uppdateringarna inaktive ras vanligt vis en version av gäst operativ systemet runt 60 dagar efter lanseringen. Den här aktiviteten behåller minst två gäst operativ system versioner för varje familj som är tillgänglig för användning.

### <a name="process-during-a-guest-os-family-retirement"></a>Bearbeta under en gäst operativ system familj
När indragningen har meddelats har kunderna en 12-månaders "över gång"-period innan den äldre familjen tas bort från tjänsten officiellt. Den här över gångs tiden kan utökas enligt Microsofts gottfinnande. Uppdateringar publiceras på [Azure-gästens OS-utgåvor och SDK-kompabilitet](cloud-services-guestos-update-matrix.md).

En gradvis indragnings process börjar sex (6) månader i över gångs perioden. Under den här tiden:

1. Microsoft meddelar kunderna om indragningen.
2. Den nyare versionen av Azure SDK stöder inte den återkallade gäst operativ system familjen.
3. Nya distributioner och omdistributioner av Cloud Services är inte tillåtna i den indragna familjen

Microsoft fortsätter att införa en ny gäst operativ system version som innehåller de senaste MSRC-uppdateringarna fram till den sista dagen i över gångs perioden, även kallat "förfallo datum". På förfallo datumet stöds inte Cloud Services fortfarande igång enligt Azure SLA. Microsoft har möjlighet att tvinga fram uppgradering, ta bort eller stoppa dessa tjänster efter det datumet.

### <a name="process-during-a-guest-os-version-retirement"></a>Bearbeta under en gäst operativ system version
Om kunderna anger att gäst operativ systemet ska uppdateras automatiskt behöver de inte bekymra sig om att hantera operativ system versioner. De kommer alltid att använda den senaste versionen av gäst operativ systemet.

Gäst operativ system versioner publiceras varje månad. På grund av frekvensen av regelbundna versioner har varje version en fast livs längd.

Vid 60 dagar till livs längd är en version "*inaktive rad*". "Disabled" innebär att versionen tas bort från portalen. Versionen kan inte längre anges från CSCFG-konfigurationsfilen. Befintliga distributioner körs. Men nya distributioner och kod-och konfigurations uppdateringar för befintliga distributioner kommer inte att tillåtas.

När du har inaktiverat "inaktive rad" upphör gäst operativ systemets version, och alla installationer fortfarande att köra den utgångna versionen är utsatta för säkerhets-och sårbarhets problem. Normalt görs förfallo datum i batchar, så att perioden från inaktivitet kan variera.

Kunder som konfigurerar sina tjänster för att uppdatera gäst operativ systemet manuellt bör se till att deras tjänster körs på ett gäst operativ system som stöds. Om en tjänst har kon figurer ATS för att uppdatera gäst operativ systemet automatiskt, garanterar den underliggande plattformen efterlevnad och uppgraderas till det senaste gäst operativ systemet.

Dessa perioder kan göras längre på Microsofts eget sätt att under lätta kunds över gångar. Eventuella ändringar kommer att meddelas på [Azures OS-utgåvor och SDK-kompatibilitet](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Meddelanden under indragningen
* **Familje Pension** <br>Microsoft kommer att använda blogg inlägg och Portal meddelanden. Kunder som fortfarande använder en kassed gäst operativ system familj meddelas via direkt kommunikation (e-post, Portal meddelanden, telefonsamtal) till tilldelade tjänst administratörer. Alla ändringar kommer att skickas till [Azure-gästens OS-utgåvor och SDK-kompatibilitet](cloud-services-guestos-update-matrix.md).
* **Versions Pension** <br>Alla ändringar och de datum som de inträffar skickas till [Azures OS-utgåvor och SDK-kompatibilitet](cloud-services-guestos-update-matrix.md), inklusive version, inaktive rad och förfallo datum. Tjänste administratörer kommer att få e-postmeddelanden om de har distributioner som körs på en inaktive rad gäst operativ system version eller-serie. Tiden för dessa e-postmeddelanden kan variera. I allmänhet är de minst en månad före inaktivitet, men den här tids perioden är inte ett officiellt service avtal.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Hur kan jag minimera påverkan på migreringen?**

Vi rekommenderar att du använder den senaste gäst operativ system familjen för att utforma din Cloud Services.

1. Börja planera migreringen till en nyare familj tidigt.
2. Konfigurera tillfälliga test distributioner för att testa moln tjänsten som körs på den nya familjen.
3. Ställ in din gäst operativ system version på **automatiskt** (osVersion = * i [. cscfg](cloud-services-model-and-package.md#cscfg) -filen) så att migreringen till nya gäst operativ system versioner sker automatiskt.

**Vad händer om mitt webb program kräver djupare integrering med operativ systemet?**

Om din webb program arkitektur är beroende av underliggande funktioner i operativ systemet, använder du plattforms funktioner som stöds, till exempel [Start åtgärder](cloud-services-startup-tasks.md) eller andra utöknings metoder. Du kan också använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infrastruktur som en tjänst), där du är ansvarig för att underhålla det underliggande operativ systemet.

## <a name="next-steps"></a>Nästa steg
Granska de senaste [versionerna av gäst operativ systemet](cloud-services-guestos-update-matrix.md).
