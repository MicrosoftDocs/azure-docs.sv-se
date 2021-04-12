---
title: Översikt över Azure Active Directory Domain Services | Microsoft Docs
description: I den här översikten får du veta vad Azure Active Directory Domain Services tillhandahåller och hur du använder det i din organisation för att tillhandahålla identitets tjänster till program och tjänster i molnet.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 194a5420f38e99d45f74241f67ae8cfbbaaee8f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661005"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Vad är Azure Active Directory Domain Services?

Azure Active Directory Domain Services (AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP (Lightweight Directory Access Protocol) och Kerberos/NTLM-autentisering. Du använder dessa domän tjänster utan att behöva distribuera, hantera och korrigera domänkontrollanter (DCs) i molnet.

Med en Azure AD DS-hanterad domän kan du köra äldre program i molnet som inte kan använda moderna autentiseringsmetoder eller där du inte vill att katalogs ökningar alltid ska gå tillbaka till en lokal AD DS-miljö. Du kan lyfta och flytta de äldre programmen från din lokala miljö till en hanterad domän utan att behöva hantera AD DS-miljön i molnet.

Azure AD DS integreras med din befintliga Azure AD-klient. Med den här integreringen kan användarna logga in på tjänster och program som är anslutna till den hanterade domänen med sina befintliga autentiseringsuppgifter. Du kan också använda befintliga grupper och användar konton för att skydda åtkomsten till resurser. Dessa funktioner ger en smidigare ökning och växling av lokala resurser till Azure.

> [!div class="nextstepaction"]
> [Kom igång genom att skapa en Azure AD DS-hanterad domän med hjälp av Azure Portal][tutorial-create]

Ta en titt på vår korta video och lär dig mer om Azure AD DS.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LblD]

## <a name="how-does-azure-ad-ds-work"></a>Hur fungerar Azure AD DS?

När du skapar en Azure AD DS-hanterad domän definierar du ett unikt namn område. Det här namn området är domän namnet, till exempel *aaddscontoso.com*. Två Windows Server-domänkontrollanter (DCs) distribueras sedan till den valda Azure-regionen. Den här distributionen av DCs kallas för en replik uppsättning.

Du behöver inte hantera, konfigurera eller uppdatera dessa domänkontrollanter. Azure-plattformen hanterar DCs som en del av den hanterade domänen, inklusive säkerhets kopiering och kryptering i vila med hjälp av Azure Disk Encryption.

En hanterad domän har kon figurer ATS för att utföra en enkelriktad synkronisering från Azure AD för att ge åtkomst till en central uppsättning användare, grupper och autentiseringsuppgifter. Du kan skapa resurser direkt i den hanterade domänen, men de synkroniseras inte tillbaka till Azure AD. Program, tjänster och virtuella datorer i Azure som ansluter till den hanterade domänen kan sedan använda vanliga AD DS-funktioner som domän anslutning, grup princip, LDAP och Kerberos/NTLM-autentisering.

I en hybrid miljö med en lokal AD DS-miljö synkroniserar [Azure AD Connect][azure-ad-connect] identitets information med Azure AD, som sedan synkroniseras till den hanterade domänen.

![Synkronisering i Azure AD Domain Services med Azure AD och lokal AD DS med AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure AD DS replikerar identitets information från Azure AD, så att den fungerar med Azure AD-klienter som endast är enbart i molnet eller som synkroniseras med en lokal AD DS-miljö. Samma uppsättning Azure AD DS-funktioner finns i båda miljöerna.

* Om du har en befintlig lokal AD DS-miljö kan du synkronisera användar konto information för att ge användarna en enhetlig identitet. Mer information finns i [så här synkroniseras objekt och autentiseringsuppgifter i en hanterad domän][synchronization].
* I miljöer med enbart moln behöver du inte en traditionell lokal AD DS-miljö för att använda de centraliserade identitets tjänsterna för Azure AD DS.

Du kan expandera en hanterad domän så att den har fler än en replik uppsättning per Azure AD-klient. Replik uppsättningar kan läggas till i alla peer-kopplat virtuella nätverk i alla Azure-regioner som har stöd för Azure AD DS. Ytterligare replik uppsättningar i olika Azure-regioner ger geografisk haveri beredskap för äldre program om en Azure-region försätts i offlineläge. Replik uppsättningar är för närvarande en för hands version. Mer information finns i [repliker anger koncept och funktioner för hanterade domäner][concepts-replica-sets].

Följande videoklipp ger en översikt över hur Azure AD DS integreras med dina program och arbets belastningar för att tillhandahålla identitets tjänster i molnet:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Om du vill se distributions scenarier för Azure AD DS i praktiken kan du utforska följande exempel:

* [Azure AD DS för Hybrid organisationer](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS för endast moln organisationer](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Funktioner och fördelar i Azure AD DS

För att tillhandahålla identitets tjänster för program och virtuella datorer i molnet är Azure AD DS helt kompatibelt med en traditionell AD DS-miljö för åtgärder som domän anslutning, säker LDAP (LDAPs), grupprincip, DNS-hantering och LDAP-bindning och Läs stöd. Stöd för LDAP-skrivning är tillgängligt för objekt som skapats i den hanterade domänen, men inte resurser som synkroniseras från Azure AD.

Om du vill veta mer om dina identitets alternativ kan du [jämföra Azure AD DS med Azure AD, AD DS på virtuella Azure-datorer och AD DS lokalt][compare].

Följande funktioner i Azure AD DS fören klar distributions-och hanterings åtgärder:

* **Förenklad distributions upplevelse:** Azure AD DS har Aktiver ATS för din Azure AD-klient med hjälp av en enda guide i Azure Portal.
* **Integrerat med Azure AD:** Användar konton, grupp medlemskap och autentiseringsuppgifter är automatiskt tillgängliga från Azure AD-klienten. Nya användare, grupper eller ändringar av attribut från din Azure AD-klient eller din lokala AD DS-miljö synkroniseras automatiskt till Azure AD DS.
    * Konton i externa kataloger som är länkade till Azure AD är inte tillgängliga i Azure AD DS. Autentiseringsuppgifterna är inte tillgängliga för dessa externa kataloger, så det går inte att synkronisera till en hanterad domän.
* **Använd företagets autentiseringsuppgifter/lösen ord:** Lösen ord för användare i Azure AD DS är desamma som i din Azure AD-klient. Användarna kan använda sina företags uppgifter för att ansluta till domän datorer, logga in interaktivt eller via fjärr skrivbord och autentisera mot den hanterade domänen.
* **NTLM-och Kerberos-autentisering:** Med stöd för NTLM-och Kerberos-autentisering kan du distribuera program som förlitar sig på Windows-integrerad autentisering.
* **Hög tillgänglighet:** Azure AD DS innehåller flera domänkontrollanter som ger hög tillgänglighet för din hanterade domän. Den här hög tillgängligheten garanterar tjänste drift tid och återhämtning till problem.
    * I regioner som stöder [Azure-tillgänglighetszoner][availability-zones]distribueras även dessa domänkontrollanter i zoner för ytterligare återhämtning.
    * [Replik uppsättningar][concepts-replica-sets] kan också användas för att tillhandahålla geografisk haveri beredskap för äldre program om en Azure-region kopplas från.

Några viktiga aspekter av en hanterad domän är följande:

* Den hanterade domänen är en fristående domän. Den är inte en utökning av en lokal domän.
    * Om det behövs kan du skapa envägs utgående skogs förtroenden från Azure AD DS till en lokal AD DS-miljö. Mer information finns i avsnittet [om begrepp och funktioner för resurs skogar för Azure AD DS][ forest-trusts].
* Ditt IT-team behöver inte hantera, korrigera eller övervaka domänkontrollanter för den här hanterade domänen.

För Hybrid miljöer som kör AD DS lokalt behöver du inte hantera AD-replikering till den hanterade domänen. Användar konton, grupp medlemskap och autentiseringsuppgifter från din lokala katalog synkroniseras med Azure AD via [Azure AD Connect][azure-ad-connect]. Dessa användar konton, grupp medlemskap och autentiseringsuppgifter är automatiskt tillgängliga i den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD DS jämför med andra identitets lösningar och hur synkronisering fungerar finns i följande artiklar:

* [Jämför Azure AD DS med Azure AD, Active Directory Domain Services på virtuella Azure-datorer och Active Directory Domain Services lokalt][compare]
* [Lär dig hur Azure AD Domain Services synkroniseras med din Azure AD-katalog][synchronization]
* Information om hur du administrerar en hanterad domän finns i [hanterings begrepp för användar konton, lösen ord och administration i Azure AD DS][administration-concepts].

Kom igång genom att [skapa en hanterad domän med hjälp av Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
