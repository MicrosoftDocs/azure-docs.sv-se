---
title: 'Azure AD Connect synkronisering: ändra standard konfigurationen | Microsoft Docs'
description: Innehåller metod tips för att ändra standard konfigurationen för Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e91ff8fa3666a2dfc5aaad07be7927852b08bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85357706"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect synkronisering: metod tips för att ändra standard konfigurationen
Syftet med det här avsnittet är att beskriva de ändringar som stöds och som inte stöds för Azure AD Connect Sync.

Konfigurationen som skapats av Azure AD Connect fungerar som den är för de flesta miljöer som synkroniserar lokala Active Directory med Azure AD. I vissa fall är det dock nödvändigt att tillämpa vissa ändringar i en konfiguration för att uppfylla ett visst behov eller krav.

## <a name="changes-to-the-service-account"></a>Ändringar i tjänst kontot
Azure AD Connect Sync körs under ett tjänst konto som skapats av installations guiden. Det här tjänst kontot innehåller krypterings nycklarna till den databas som används vid synkronisering. Den skapas med ett lösen ord på 127 tecken och lösen ordet är inställt på att upphöra att gälla.

* Det går **inte** att ändra eller återställa lösen ordet för tjänst kontot. Om du gör det förstörs krypterings nycklarna och tjänsten inte kan komma åt databasen och kan inte starta.

## <a name="changes-to-the-scheduler"></a>Ändringar i Schemaläggaren
Från och med lanseras från build 1,1 (februari 2016) kan du konfigurera [Scheduler](how-to-connect-sync-feature-scheduler.md) att ha en annan synkroniseringsmetod än standard 30 minuter.

## <a name="changes-to-synchronization-rules"></a>Ändringar av regler för synkronisering
Installations guiden innehåller en konfiguration som ska fungera för de vanligaste scenarierna. Om du behöver göra ändringar i konfigurationen måste du följa dessa regler för att fortfarande ha en konfiguration som stöds.

> [!WARNING]
> Om du gör ändringar i reglerna för standardsynkronisering kommer dessa ändringar att skrivas över nästa gång Azure AD Connect uppdateras, vilket resulterar i oväntade och sannolika synkroniseringsresultat.

* Du kan [ändra attribut flöden](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) om standardvärdena för Direct-attribut inte är lämpliga för din organisation.
* Om du inte vill [flöda ett attribut](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) och ta bort eventuella befintliga attributvärden i Azure AD måste du skapa en regel för det här scenariot.
* [Inaktivera en oönskad Synkroniseringsregel](#disable-an-unwanted-sync-rule) i stället för att ta bort den. En borttagen regel återskapas under en uppgradering.
* Om du vill [ändra en regel](#change-an-out-of-box-rule)som inte är i regel bör du skapa en kopia av den ursprungliga regeln och inaktivera regeln som är inaktiv. Redigeraren för Synkroniseringsregel meddelar och hjälper dig.
* Exportera dina anpassade regler för synkronisering med hjälp av redigeraren för formateringsregler. Redigeraren ger dig ett PowerShell-skript som du kan använda för att enkelt återskapa dem i ett haveri beredskaps scenario.

> [!WARNING]
> De färdiga Sync-reglerna har ett tumavtryck. Om du gör en ändring av de här reglerna matchar inte tumavtrycket längre. Du kan ha problem i framtiden när du försöker tillämpa en ny version av Azure AD Connect. Gör bara ändringar på det sätt som beskrivs i den här artikeln.

### <a name="disable-an-unwanted-sync-rule"></a>Inaktivera en oönskade Synkroniseringsregel
Ta inte bort en regel för att synkronisera direkt. Den återskapas under nästa uppgradering.

I vissa fall har installations guiden genererat en konfiguration som inte fungerar för din topologi. Om du till exempel har en topologi för konto-resurs, men har utökat schemat i konto skogen med Exchange-schemat, skapas regler för Exchange för konto skogen och resurs skogen. I så fall måste du inaktivera synkroniseringsregeln för Exchange.

![Inaktive rad Synkroniseringsregel](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

I bilden ovan har installations guiden hittat ett gammalt Exchange 2003-schema i konto skogen. Det här schema tillägget lades till innan resurs skogen introducerades i Fabrikams miljö. Om du inte vill att några attribut från den gamla Exchange-implementeringen ska synkroniseras, ska synkroniseringsregeln vara inaktive rad som visas.

### <a name="change-an-out-of-box-rule"></a>Ändra en regel som inte är i kartongen
Den enda gången du bör ändra en regel som inte ingår i regeln är när du behöver ändra kopplings regeln. Om du behöver ändra ett attributets flöde bör du skapa en Synkroniseringsregel med högre prioritet än de färdiga reglerna. Den enda regel du behöver klona är regeln **i från AD-User Join**. Du kan åsidosätta alla andra regler med en regel med högre prioritet.

Om du behöver göra ändringar i en regel som inte är i regel bör du göra en kopia av regeln som är inaktuell och inaktivera den ursprungliga regeln. Gör sedan ändringarna i den klonade regeln. Med hjälp av regel redigeraren för synkronisering får du hjälp med dessa steg. När du öppnar en regel som är intill en regel visas den här dialog rutan:  
![Varning från Box-regel](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Välj **Ja** om du vill skapa en kopia av regeln. Den klonade regeln öppnas sedan.  
![Klonad regel](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

I den här klonade regeln gör du nödvändiga ändringar av omfång, koppling och transformeringar.

## <a name="next-steps"></a>Nästa steg
**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
