---
title: Etablering på begäran i Azure AD Connect molnbaserad synkronisering
description: Den här artikeln beskriver hur du använder funktionen Cloud Sync i Azure AD Connect för att testa konfigurations ändringar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554283"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Etablering på begäran i Azure AD Connect molnbaserad synkronisering

Du kan använda funktionen Cloud Sync i Azure Active Directory (Azure AD) Anslut för att testa konfigurations ändringar genom att tillämpa dessa ändringar på en enskild användare. Med den här etableringen av den här inställningen kan du verifiera och kontrol lera att ändringarna som har gjorts i konfigurationen har tillämpats korrekt och synkroniseras korrekt med Azure AD.  

> [!IMPORTANT] 
> När du använder etablering på begäran tillämpas inte omfångs filtren på den användare som du har valt. Du kan använda etablering på begäran för användare som är utanför de organisations enheter som du har angett.

## <a name="validate-a-user"></a>Verifiera en användare
Följ dessa steg om du vill använda etablering på begäran:

1.  Välj **Azure Active Directory** i Azure Portal.
2.  Välj **Azure AD Connect**.
3.  Välj **hantera synkronisering av moln**.

    ![Skärm bild som visar länken för att hantera synkronisering av molnet.](media/how-to-install/install-6.png)
4. Under **konfiguration** väljer du din konfiguration.
5. Under **Verifiera** väljer du knappen **Tillhandahåll en användare** . 

   ![Skärm bild som visar knappen för att konfigurera en användare.](media/how-to-on-demand-provision/on-demand-2.png)

6. På skärmen **etablera på begäran** anger du det unika namnet för en användare och klickar på knappen **Tillhandahåll** .  
 
   ![Skärm bild som visar knappen användar namn och en provision.](media/how-to-on-demand-provision/on-demand-3.png)
7. När etableringen har slutförts visas skärmen lyckades med fyra gröna kryss rutor. Eventuella fel visas till vänster.

   ![Skärm bild som visar lyckad etablering.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Hämta information om etablering
Nu kan du titta på användar informationen och avgöra om de ändringar som du har gjort i konfigurationen har tillämpats. Resten av den här artikeln beskriver de enskilda avsnitten som visas i informationen om en korrekt synkroniserad användare.

### <a name="import-user"></a>Importera användare
Avsnittet **Importera användare** innehåller information om den användare som har importer ats från Active Directory. Så här ser det ut som om användaren ser ut innan den allokeras till Azure AD. Välj länken **Visa information** om du vill visa den här informationen.

![Skärm bild av knappen för att visa information om en importerad användare.](media/how-to-on-demand-provision/on-demand-5.png)

Med hjälp av den här informationen kan du se de olika attributen (och deras värden) som har importer ATS. Om du har skapat en anpassad attributmappning kan du se värdet här.

![Skärm bild som visar användar information.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Avgöra om användaren är inom omfånget
Avsnittet **avgöra om användaren finns i omfånget** innehåller information om huruvida användaren som har importerat till Azure AD finns i omfånget. Välj länken **Visa information** om du vill visa den här informationen.

![Skärm bild av knappen för att visa information om användar omfång.](media/how-to-on-demand-provision/on-demand-7.png)

Med hjälp av den här informationen kan du se om användaren är i omfånget.

![Skärm bild som visar användar omfattnings information.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Matcha användare mellan käll-och mål system
Avsnittet **matcha användare mellan käll-och mål system** innehåller information om huruvida användaren redan finns i Azure AD och om en anslutning ska ske i stället för att en ny användare ska skapas. Välj länken **Visa information** om du vill visa den här informationen.

![Skärm bild av knappen för att visa information om en matchad användare.](media/how-to-on-demand-provision/on-demand-8.png)

Med hjälp av den här informationen kan du se om en matchning hittades eller om en ny användare ska skapas.

![Skärm bild som visar användar information.](media/how-to-on-demand-provision/on-demand-11.png)

Matchnings informationen visar ett meddelande med någon av följande tre åtgärder:
- **Skapa**: en användare skapas i Azure AD.
- **Uppdatera**: en användare uppdateras baserat på en ändring som gjorts i konfigurationen.
- **Ta bort**: en användare tas bort från Azure AD.

Beroende på vilken typ av åtgärd som du har utfört varierar meddelandet.

### <a name="perform-action"></a>Utför åtgärd
Avsnittet **utföra åtgärd** innehåller information om användaren som etablerades eller exporterades till Azure AD efter att konfigurationen tillämpades. Detta är vad användaren ser ut efter etableringen i Azure AD. Välj länken **Visa information** om du vill visa den här informationen.

![Skärm bild av knappen för att visa information om en utförd åtgärd.](media/how-to-on-demand-provision/on-demand-9.png)

Med hjälp av den här informationen kan du se värdena för attributen när konfigurationen har tillämpats. Ser de ut ungefär som importerade eller är de annorlunda? Har konfigurationen tillämpats?  

Med den här processen kan du spåra attributet Transformation när den flyttas genom molnet och till din Azure AD-klient.

![Skärm bild som visar information om spårat attribut.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Nästa steg 

- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [Installera Azure AD Connect Cloud Sync](how-to-install.md)
 