---
title: Inaktiverade domäner i Azure AD Domain Services | Microsoft Docs
description: Läs om de olika hälso tillstånden för en Azure AD DS-hanterad domän och hur du återställer en pausad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: b0b48e7ad494386052e6d94c32d7215b3f4f0202
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618832"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Förstå hälso tillstånden och lösa inaktiverade domäner i Azure Active Directory Domain Services

När Azure Active Directory Domain Services (Azure AD DS) inte kan betjäna en hanterad domän under en lång tids period, placerar den hanterade domänen i ett pausat tillstånd. Om en hanterad domän förblir i pausat läge tas den bort automatiskt. Om du vill att din Azure AD DS-hanterade domän ska vara felförhindrad kan du lösa eventuella aviseringar så snabbt som möjligt.

I den här artikeln förklaras varför hanterade domäner har pausats och hur du återställer en pausad domän.

## <a name="overview-of-managed-domain-states"></a>Översikt över hanterade domän tillstånd

Genom livs cykeln för en hanterad domän finns det olika tillstånd som indikerar dess hälsa. Om den hanterade domänen rapporterar ett problem kan du snabbt lösa den underliggande orsaken för att stoppa statusen från att fortsätta försämras.

![Förloppet för tillstånd som en hanterad domän tar mot SUS Pension](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

En hanterad domän kan vara i något av följande tillstånd:

* [Körs](#running-state)
* [Kräver åtgärd](#needs-attention-state)
* [Inaktiverad](#suspended-state)
* [Borttaget](#deleted-state)

## <a name="running-state"></a>Kör tillstånd

En hanterad domän som är korrekt konfigurerad och utan problem är i *körnings* tillstånd. Detta är det önskade läget för en hanterad domän.

### <a name="what-to-expect"></a>Vad du kan förvänta dig

* Azure-plattformen kan regelbundet övervaka hälso tillståndet för den hanterade domänen.
* Domänkontrollanter för den hanterade domänen korrigeras och uppdateras regelbundet.
* Ändringar från Azure Active Directory synkroniseras regelbundet till den hanterade domänen.
* Vanliga säkerhets kopieringar utförs för den hanterade domänen.

## <a name="needs-attention-state"></a>Kräver Attention-tillstånd

En hanterad domän med ett eller flera problem som måste åtgärdas är i *Åtgärds läget krav* . Sidan hälso tillstånd för den hanterade domänen visar aviseringarna och visar var det finns ett problem.

Vissa aviseringar är tillfälliga och löses automatiskt av Azure-plattformen. För andra aviseringar kan du åtgärda problemet genom att följa de lösnings steg som anges. Det finns en kritisk varning. [öppna en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

Ett exempel på en avisering är när det finns en begränsande nätverks säkerhets grupp. I den här konfigurationen kanske inte Azure-plattformen kan uppdatera och övervaka den hanterade domänen. En avisering genereras och statusen ändras till *kräver en åtgärd*.

Mer information finns i [fel sökning av aviseringar för en hanterad domän][resolve-alerts].

### <a name="what-to-expect"></a>Vad du kan förvänta dig

När en hanterad domän är i *behovs* känslig status kan Azure-plattformen kanske inte övervaka, korrigera, uppdatera eller säkerhetskopiera data regelbundet. I vissa fall, till exempel en ogiltig nätverks konfiguration, kan det hända att domän kontrol Lanterna för den hanterade domänen inte kan kontaktas.

* Den hanterade domänen är i ett ohälsosamt tillstånd och pågående hälso övervakning kan sluta tills aviseringen har åtgärd ATS.
* Domänkontrollanter för den hanterade domänen kan inte korrigeras eller uppdateras.
* Ändringar från Azure Active Directory får inte synkroniseras till den hanterade domänen.
* Det går inte att säkerhetskopiera den hanterade domänen.
* Om du löser icke-kritiska varningar som påverkar den hanterade domänen bör hälso tillståndet återgå till *körnings* tillståndet.
* Kritiska aviseringar utlöses för konfigurations problem där Azure-plattformen inte kan komma åt domän kontrol Lanterna. Om dessa kritiska aviseringar inte löses inom 15 dagar, kommer den hanterade domänen att övergå i tillståndet *Suspended* .

## <a name="suspended-state"></a>Inaktiverat tillstånd

En hanterad domän övergår i **pausat** tillstånd av någon av följande anledningar:

* En eller flera kritiska aviseringar har inte lösts på 15 dagar.
    * Kritiska aviseringar kan orsakas av en felaktig konfiguration som blockerar åtkomst till resurser som krävs av Azure AD DS. Till exempel är aviseringen [AADDS104: nätverks felet][alert-nsg] har olösts på över 15 dagar i den hanterade domänen.
* Det har uppstått ett fakturerings problem med Azure-prenumerationen eller så har Azure-prenumerationen gått ut.

Hanterade domäner pausas när Azure-plattformen inte kan hantera, övervaka, korrigera eller säkerhetskopiera domänen. En hanterad domän förblir i ett *inaktiverat* tillstånd i 15 dagar. För att upprätthålla åtkomsten till den hanterade domänen löser du viktiga aviseringar direkt.

### <a name="what-to-expect"></a>Vad du kan förvänta dig

Följande beteende uppstår när en hanterad domän är i tillståndet *Suspended* :

* Domän kontrol Lanterna för den hanterade domänen är avetablerade och går inte att komma åt i det virtuella nätverket.
* Säkert LDAP åtkomst till den hanterade domänen via Internet, om aktive rad, slutar fungera.
* Det finns ett problem med att autentisera till den hanterade domänen, logga in på domänanslutna virtuella datorer eller ansluta via LDAP/LDAPs.
* Säkerhets kopieringar för den hanterade domänen tas inte längre.
* Synkronisering med Azure AD stoppas.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Hur vet du om din hanterade domän har pausats?

Du ser en [avisering][resolve-alerts] på sidan Azure AD DS Health i den Azure Portal som antecknar domänen är inaktive rad. Domänens tillstånd visas även *inaktive* rad.

### <a name="restore-a-suspended-domain"></a>Återställa en pausad domän

Slutför följande steg för att återställa hälsan för en hanterad domän som har tillståndet *Suspended* :

1. Sök efter och välj **domän tjänster** i Azure Portal.
1. Välj din hanterade domän i listan, t. ex. *aaddscontoso.com*, och välj sedan **hälsa**.
1. Välj aviseringen, till exempel *AADDS503* eller *AADDS504*, beroende på orsaken till SUS pensionen.
1. Välj den upplösnings länk som anges i aviseringen och följ stegen för att lösa problemet.

En hanterad domän kan bara återställas till datumet för den senaste säkerhets kopieringen. Datumet för den senaste säkerhets kopieringen visas på sidan **hälsa** i den hanterade domänen. Eventuella ändringar som inträffat efter den senaste säkerhets kopieringen återställs inte. Säkerhets kopior för en hanterad domän lagras i upp till 30 dagar. Säkerhets kopieringar som är äldre än 30 dagar tas bort.

När du har löst aviseringar när den hanterade domänen är i *paus* läge [öppnar du en support förfrågan för Azure][azure-support] för att återgå till felfritt tillstånd. Om det finns en säkerhets kopia som är mindre än 30 dagar gammal kan Azure-supporten återställa den hanterade domänen.

## <a name="deleted-state"></a>Borttaget läge

Om en hanterad domän förblir i *pausat* tillstånd i 15 dagar tas den bort. Den här processen kan inte återställas.

### <a name="what-to-expect"></a>Vad du kan förvänta dig

När en hanterad domän går in i det *borttagna* läget visas följande:

* Alla resurser och säkerhets kopior för den hanterade domänen tas bort.
* Du kan inte återställa den hanterade domänen. Du måste skapa en ersättande hanterad domän för åter användning av Azure AD DS.
* När den har tagits bort debiteras du inte för den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

Om du vill hålla din hanterade domän felfri och minimera risken för att den blir pausad, lär du dig hur du [löser aviseringar för din hanterade domän][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
