---
title: 'Azure AD Connect: när du redan har Azure AD | Microsoft Docs'
description: I det här avsnittet beskrivs hur du använder Connect när du har en befintlig Azure AD-klient.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420537"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: när du har en befintlig klient
De flesta av avsnitten om hur du använder Azure AD Connect förutsätter att du börjar med en ny Azure AD-klient och att det inte finns några användare eller andra objekt där. Men om du har börjat med en Azure AD-klient, fyllt i den med användare och andra objekt, och nu vill använda Connect, är det här avsnittet för dig.

## <a name="the-basics"></a>Grunderna
Ett objekt i Azure AD är antingen hanterat i molnet (Azure AD) eller lokalt. För ett enda objekt kan du inte hantera vissa attribut lokalt och andra attribut i Azure AD. Varje objekt har en flagga som visar var objektet hanteras.

Du kan hantera vissa användare lokalt och andra i molnet. Ett vanligt scenario för den här konfigurationen är en organisation med en blandning av räkenskaps arbetare och försäljnings arbetare. Bokförings arbetare har ett lokalt AD-konto, men försäljnings arbetarna har inte något konto i Azure AD. Du skulle hantera vissa användare lokalt och några i Azure AD.

Om du började hantera användare i Azure AD som också finns i lokala AD och senare vill använda Connect, finns det några ytterligare problem som du behöver tänka på.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synkronisera med befintliga användare i Azure AD
När du installerar Azure AD Connect och startar synkronisering görs en kontroll av Azure AD Sync-tjänsten (i Azure AD) för varje nytt objekt och försöker hitta ett befintligt objekt som ska matchas. Det finns tre attribut som används för den här processen: **userPrincipalName**, **proxyAddresses** och **sourceAnchor** / **immutableID**. En matchning på **userPrincipalName** och **proxyAddresses** kallas för en **mjuk matchning**. En matchning på **sourceAnchor** kallas för **hård matchning**. För attributet **proxyAddresses** används endast värdet med **SMTP:**, det vill säga den primära e-postadressen, för utvärderingen.

Matchningen utvärderas endast för nya objekt som kommer från Connect. Om du ändrar ett befintligt objekt så att det matchar något av dessa attribut visas ett fel i stället.

Om Azure AD hittar ett objekt där attributvärdena är samma för ett objekt som kommer från Connect och som redan finns i Azure AD, tas objektet i Azure AD över av Connect. Det tidigare moln hanterade objektet är flaggat som lokalt hanterat. Alla attribut i Azure AD med ett värde i den lokala AD skrivs över med det lokala värdet.

> [!WARNING]
> Eftersom alla attribut i Azure AD ska skrivas över av det lokala värdet, se till att du har data lokalt. Om du till exempel bara har en hanterad e-postadress i Microsoft 365 och inte sparade den på en lokal AD DS, förlorar du alla värden i Azure AD/Microsoft 365 som inte finns i AD DS.

> [!IMPORTANT]
> Om du använder Lösenordssynkronisering, som alltid används av Express inställningar, skrivs lösen ordet i Azure AD över med lösen ordet i lokalt AD. Om användarna används för att hantera olika lösen ord måste du meddela dem att de ska använda det lokala lösen ordet när du har installerat Connect.

Föregående avsnitt och varning måste beaktas i planeringen. Om du har gjort många ändringar i Azure AD som inte återspeglas i den lokala AD DS måste du planera för hur du ska fylla i AD DS med de uppdaterade värdena innan du synkroniserar dina objekt med Azure AD Connect.

Om du har matchat dina objekt med en mjuk matchning, läggs **sourceAnchor** till i objektet i Azure AD så att en hård matchning kan användas senare.

>[!IMPORTANT]
> Microsoft rekommenderar starkt att du synkroniserar lokala konton med redan befintliga administrativa konton i Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Hård matchning vs mjuk matchning
För en ny installation av Connect finns det ingen praktisk skillnad mellan en mjuk och en hård matchning. Skillnaden är att en katastrof återställning inträffar. Om du har tappat bort servern med Azure AD Connect kan du installera om en ny instans utan att förlora några data. Ett objekt med en sourceAnchor skickas för att ansluta under den första installationen. Matchningen kan sedan utvärderas av klienten (Azure AD Connect), vilket är mycket snabbare än att göra samma sak i Azure AD. En hård matchning utvärderas både av Connect och av Azure AD. En mjuk matchning utvärderas bara av Azure AD.

### <a name="other-objects-than-users"></a>Andra objekt än användare
För e-postaktiverade grupper och kontakter kan du använda mjuk matchning utifrån proxyAddresses. Hård matchning är inte tillämpligt eftersom du bara kan uppdatera sourceAnchor/immutableID (med PowerShell) enbart för användare. För grupper som inte är e-postaktiverade finns det för närvarande inget stöd för mjuk matchning eller hård matchning.

### <a name="admin-role-considerations"></a>Överväganden för administratörs roll
För att förhindra att ej betrodda lokala användare kommer att matcha med en moln användare som har någon administratörs roll, matchar Azure AD Connect inte lokala användar objekt med objekt som har en administratörs roll. Detta är som standard. För att lösa det här problemet kan du göra följande:

1.  Ta bort katalog rollerna från användarobjektet endast till molnet.
2.  Om det gick inte att synkronisera med användare, tar du bort objektet i karantän i molnet.
3.  Utlös en synkronisering.
4.  Du kan också lägga tillbaka katalog rollerna till objektet användare i molnet när det har skett.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Skapa en ny lokal Active Directory från data i Azure AD
Vissa kunder börjar med en moln lösning med Azure AD och de har inte någon lokal AD. Senare vill de använda lokala resurser och vill bygga en lokal AD baserat på Azure AD-data. Azure AD Connect kan inte hjälpa dig för det här scenariot. Den skapar inte användare lokalt och har inte möjlighet att ange lösen ordet lokalt till samma som i Azure AD.

Om den enda anledningen till varför du planerar att lägga till en lokal AD är att stödja LOBs (verksamhetsspecifika appar) kanske du vill överväga att använda [Azure AD Domain Services](../../active-directory-domain-services/index.yml) i stället.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
