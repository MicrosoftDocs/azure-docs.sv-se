---
title: 'Azure AD Connect synkronisering: tekniska begrepp | Microsoft Docs'
description: Förklarar de tekniska begreppen i Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01e53b30a4c27296e30e031ffb771697afa8e1e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87019683"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect-synkronisering: Tekniska begrepp
Den här artikeln är en sammanfattning av avsnittet [förstå arkitekturen](how-to-connect-sync-technical-concepts.md).

Azure AD Connect Sync-versioner på en solid metakatalogdesign-synkroniseringspartner.
I följande avsnitt introduceras begreppen för metakatalogdesign-synkronisering.
När du bygger på MIIS (Microsoft Identity Integration Server), ILM (identitets livs cykel hanterare) och FIM (Forefront Identity Manager) tillhandahåller Azure Active Directory Sync-tjänster nästa plattform för att ansluta till data källor, synkronisera data mellan data källor, samt etablering och avetablering av identiteter.

![Tekniska begrepp](./media/how-to-connect-sync-technical-concepts/scenario.png)

I följande avsnitt finns mer information om följande aspekter av FIM-synkroniseringstjänsten:

* Anslutning
* Attributflöde
* Kopplings utrymme
* Metaversum
* Etablering

## <a name="connector"></a>Anslutning
Kodmoduler som används för att kommunicera med en ansluten katalog kallas för kopplingar (tidigare kallade hanterings agenter (MAs)).

De installeras på datorn som kör Azure AD Connect Sync. Anslutningarna ger agenten möjlighet att omvända med hjälp av fjärrsystemets protokoll i stället för att använda sig av distribution av specialiserade agenter. Detta innebär minskad risk och distributions tider, särskilt när du hanterar kritiska program och system.

I bilden ovan är kopplingen synonym med kopplings utrymmet men omfattar all kommunikation med det externa systemet.

Anslutningen ansvarar för all import-och export funktion till systemet och gör det möjligt för utvecklare att behöva förstå hur de ansluter till varje system internt när de använder deklarativ etablering för att anpassa data transformationer.

Import och export sker endast när det är schemalagt, vilket ger ytterligare isolering från ändringar som sker i systemet, eftersom ändringar inte sprids automatiskt till den anslutna data källan. Dessutom kan utvecklare också skapa egna anslutningar för att ansluta till i princip vilken data källa som helst.

## <a name="attribute-flow"></a>Attributflöde
Metaversum är den konsoliderade vyn över alla kopplade identiteter från intilliggande kopplings utrymmen. I bilden ovan visas ett attributarkiv med linjer med pilspetsar för både inkommande och utgående flöde. Attribute Flow är processen för att kopiera eller transformera data från ett system till ett annat och alla attribut flöden (inkommande eller utgående).

Attribute Flow sker mellan kopplings utrymmet och metaversum i bi-riktning när synkroniseringen (fullständig eller delta) har schemalagts att köras.

Attribute Flow inträffar bara när de här synkroniseringarna körs. Attribut flöden definieras i regler för synkronisering. Dessa kan vara inkommande (ISR i bilden ovan) eller utgående (OSR i bilden ovan).

## <a name="connected-system"></a>Anslutet system
Det anslutna systemet (aka Connected Directory) refererar till fjärrsystemet Azure AD Connect Sync har anslutit till och läst och skrivit identitets data till och från.

## <a name="connector-space"></a>Kopplings utrymme
Varje ansluten data källa representeras som en filtrerad delmängd av objekten och attributen i kopplings utrymmet.
Detta gör att synkroniseringstjänsten fungerar lokalt utan att du behöver kontakta fjärrsystemet när du synkroniserar objekten och begränsar interaktionen till import och export.

När data källan och anslutningen har möjlighet att tillhandahålla en lista över ändringar (en delta import) ökar drifts effektiviteten dramatiskt eftersom endast ändringar sedan den senaste avsöknings cykeln utbyts. Anslutnings utrymmet skyddar den anslutna data källan från ändringar som sprids automatiskt genom att kräva att anslutnings programmet schemalägger import och export. Den här extra försäkringen ger dig trygghet när du testar, för hands Grans kar eller bekräftar nästa uppdatering.

## <a name="metaverse"></a>Metaversum
Metaversum är den konsoliderade vyn över alla kopplade identiteter från intilliggande kopplings utrymmen.

Eftersom identiteter är kopplade till varandra och utfärdare tilldelas för olika attribut via import Flow-mappningar börjar det centrala metaversum-objektet att samla in information från flera system. Från det här objektet Attribute Flow, använder mappningar för att överföra information till utgående system.

Objekt skapas när ett auktoritativt system projekt läggs till i metaversum. Så fort alla anslutningar tas bort tas metaversum-objektet bort.

Objekt i metaversum kan inte redige ras direkt. Alla data i objektet måste bidrogs via Attribute Flow. Metaversum hanterar permanenta anslutningar med varje kopplings utrymme. Dessa anslutningar kräver inte utvärderings versionen för varje synkronisering som körs. Det innebär att Azure AD Connect Sync inte behöver hitta det matchande fjärrobjektet varje tillfälle. På så sätt undviker du att kostsamma agenter behöver förhindra ändringar i attribut som vanligt vis är ansvariga för att korrelera objekten.

När du identifierar nya data källor som kan ha befintliga objekt som måste hanteras, Azure AD Connect Sync använder en process som kallas kopplings regel för att utvärdera potentiella kandidater som en länk ska upprättas till.
När länken har upprättats sker inte utvärderingen och det normala attributet kan ske mellan den fjärranslutna data källan och metaversum.

## <a name="provisioning"></a>Etablering
När en auktoritativ källa projekt ett nytt objekt i metaversum kan ett nytt objekt för kopplings utrymme skapas i en annan koppling som representerar en underordnad data källa.

Detta upprättar en länk och ett attributarkiv kan fortsätta i båda riktningarna.

När en regel avgör att ett nytt kopplings utrymme måste skapas, kallas det för etablering. Men eftersom den här åtgärden bara äger rum inom kopplings utrymmet överförs den inte till den anslutna data källan förrän en export har utförts.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure AD Connect synkronisering: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
