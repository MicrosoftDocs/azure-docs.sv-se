---
title: 'Azure AD Connect: Felsöka problem med käll ankare under installationen | Microsoft Docs'
description: Det här avsnittet innehåller anvisningar för hur du felsöker problem med käll ankaret under installationen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4e6460015430850c11fbf70a005d7440ce1b92fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306012"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Felsöka problem med käll ankare under installationen
I den här artikeln förklaras olika käll fäst punkter för problem som kan uppstå under installationen och som kan lösa problemen.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ogiltigt käll ankare i Azure Active Directory

### <a name="custom-installation"></a>Anpassad installation

Vid anpassad installation läser Azure AD Connect käll fäst punkts princip från Azure Active Directory. Om principen finns i Azure Active Directory, Azure AD Connect tillämpar samma princip om den inte åsidosätts av kunden. Guiden informerar dig om vilket attribut som har lästs. Dessutom varnar guiden om du försöker åsidosätta käll fäst punkten.

Under den här Läs åtgärden är det möjligt att käll fäst punkten i Azure Active Directory är oväntad. I det här fallet vet Azure AD Connect inte vad käll ankaret ska använda och kräver manuellt åsidosättning.</br>
![Skärm bild som visar var du kan åsidosätta käll fäst punkten manuellt.](media/tshoot-connect-source-anchor/source1.png)

För att lösa det här problemet kan du manuellt åsidosätta käll fäst punkten genom att välja ett speciellt attribut. Fortsätt med det här alternativet om och bara om du är säker på vilket attribut som ska väljas. Om du inte är säker kontaktar du [Microsoft Support](https://support.microsoft.com/contactus/) om du behöver hjälp. Om du ändrar käll fäst punkts principen kan den bryta kopplingen mellan dina lokala användare och deras associerade Azure-resurser.</br>
![Skärm bild som visar det angivna attributet som åsidosätter käll ankaret.](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Snabb installation
Under snabb installationen läser Azure AD Connect käll fäst punkts princip från Azure Active Directory. Om principen finns i Azure Active Directory, Azure AD Connect tillämpar samma princip. Det finns inget alternativ för manuell åsidosättning.

Under den här Läs åtgärden är det möjligt att käll fäst punkten i Azure Active Directory är oväntad. I det här fallet vet Azure AD Connect inte vad käll fäst punkten ska vara.</br>
![Skärm bild som visar vad som händer när käll ankaret i Azure Active Directory är oväntat.](media/tshoot-connect-source-anchor/source3.png)

För att lösa det här problemet måste du installera på nytt med det anpassade läget och åsidosätta käll ankaret manuellt genom att välja ett särskilt attribut. Fortsätt med det här alternativet om och bara om du är säker på vilket attribut som ska väljas. Om du inte är säker kontaktar du [Microsoft Support](https://support.microsoft.com/contactus/) om du behöver hjälp. Om du ändrar käll fäst punkts principen kan den bryta kopplingen mellan dina lokala användare och deras associerade Azure-resurser.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ogiltigt käll ankare i Synkroniseringsmotorn
Under installationen är det möjligt att Azure AD Connect försöker konfigurera Synkroniseringsmotorn med en ogiltig käll-ankare. Den här åtgärden är förmodligen ett produkt problem och installationen av Azure AD Connect kommer att Miss förväntas. Kontakta [Microsoft-supporten](https://support.microsoft.com/contactus/) om du kör på det här problemet.</br>
![ett](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
