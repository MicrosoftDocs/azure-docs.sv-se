---
title: VANLIGA fel söknings roller tilldelade till moln grupp – Azure Active Directory | Microsoft Docs
description: Lär dig några vanliga frågor och fel söknings tips för att tilldela roller till grupper i Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ff906654dc91c7b1980292efd2a737503e684e9
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742921"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Felsökningsroller tilldelade till molngrupper

Här följer några vanliga frågor och fel söknings tips för att tilldela roller till grupper i Azure Active Directory (Azure AD).

**F:** Jag är en grupp administratör men jag kan inte se **Azure AD-rollerna kan tilldelas till grupp** växeln.

**A:** Endast privilegierade roll administratörer eller globala administratörer kan skapa en grupp som är berättigad till roll tilldelning. Endast användare i dessa roller ser den här kontrollen.

**F:** Vem kan ändra medlemskap i grupper som är tilldelade till Azure AD-roller?

**A:** Som standard hanterar endast privilegie rad roll administratör och global administratör medlemskap i en grupp som kan tilldelas av roller, men du kan delegera hanteringen av roll tilldelnings bara grupper genom att lägga till grupp ägare.

**F**: Jag är administratör för supportavdelningen i min organisation, men jag kan inte uppdatera lösen ordet för en användare som är katalog läsare. Varför sker detta?

**A**: användaren kan ha blivit katalog läsare via en grupp som kan tilldelas av roller. Alla medlemmar och ägare av en roll tilldelnings bara grupper är skyddade. Endast användare med rollerna Privileged Authentication Administrator eller global administratör kan återställa autentiseringsuppgifter för en skyddad användare.

**F:** Jag kan inte uppdatera användarens lösen ord. De har inte tilldelats någon högre privilegie rad roll. Varför händer det?

**A:** Användaren kan vara ägare till en grupp som kan tilldelas av roller. Vi skyddar ägare till Roll tilldelnings bara grupper för att undvika höjning av privilegier. Ett exempel kan vara om en grupp Contoso_Security_Admins tilldelas rollen som säkerhets administratör, där Bob är grupp ägaren och Alice är lösen ords administratör i organisationen. Om det här skyddet inte finns kan Alice återställa Bobs autentiseringsuppgifter och ta över sin identitet. Sedan kan Alice lägga till sig själv eller någon i gruppen Contoso_Security_Admins gruppen för att bli en säkerhets administratör i organisationen. Ta reda på om en användare är grupp ägare genom att hämta listan över ägda objekt för den användaren och se om någon av grupperna har isAssignableToRole angetts till true. Om ja, är den användaren skyddad och beteendet är avsiktligt. Referera till dessa dokument för att få ägda objekt:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject)  
- [Visa lista ownedObjects](/graph/api/user-list-ownedobjects?tabs=http)

**F:** Kan jag skapa en åtkomst granskning för grupper som kan tilldelas till Azure AD-roller (särskilt grupper med egenskapen isAssignableToRole inställd på true)?  

**A:** Ja, du kan. Om du använder den senaste versionen av åtkomst granskning dirigeras granskarna till min åtkomst som standard och endast globala administratörer kan skapa åtkomst granskningar för roll tilldelnings bara grupper. Men om du använder den äldre versionen av Access granskning dirigeras granskarna till åtkomst panelen som standard, och både globala administratörer och användar administratörer kan skapa åtkomst granskningar för roll tilldelnings bara grupper. Den nya upplevelsen kommer att distribueras till alla kunder den 28 juli 2020, men om du vill uppgradera tidigare kan du göra en begäran till [Azure AD Access-granskningar – uppdaterad gransknings upplevelse i registreringen av min åtkomst](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

**F:** Kan jag skapa ett Access-paket och lägga till grupper som kan tilldelas till Azure AD-roller i det?

**A:** Ja, du kan. Global administratör och användar administratör har möjlighet att publicera en grupp i ett Access-paket. Inga ändringar för global administratör, men det finns en liten ändring av användar administratörens roll behörigheter. Om du vill ange en grupp som kan tilldelas roller i ett Access-paket måste du vara en användar administratör och även ägare till den roll tilldelnings bara gruppen. Här är en fullständig tabell som visar vem som kan skapa åtkomst paket i Enterprise License Management:

Azure AD-katalog roll | Rättighets hanterings roll | Kan lägga till säkerhets grupp\* | Kan lägga till Microsoft 365 grupp\* | Kan lägga till app | Kan lägga till SharePoint Online-webbplats
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Global administratör | saknas | ✔️ | ✔️ | ✔️  | ✔️
Användaradministratör  | saknas  | ✔️  | ✔️  | ✔️
Intune-administratör | Katalog ägare | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange-administratör  | Katalog ägare  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Team tjänst administratör | Katalog ägare  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint-administratör | Katalog ägare | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Program administratör | Katalog ägare  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Moln program administratör | Katalog ägare  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
User | Katalog ägare | Endast om grupp ägare | Endast om grupp ägare | Endast om app-ägare  | &nbsp;

\*Gruppen är inte roll tilldelnings bara. det vill säga isAssignableToRole = false. Om en grupp är tilldelnings bara för roll måste personen som skapar åtkomst paketet också vara ägare till den roll tilldelnings bara gruppen.

**F:** Jag kan inte hitta alternativet "ta bort tilldelning" i "tilldelade roller". Hur gör jag för att bort roll tilldelningen till en användare?

**A:** Det här svaret gäller endast för Azure AD Premium P1-organisationer.

1. Logga in på [Azure Portal](https://portal.azure.com) och öppna **Azure Active Directory**.
1. Välj användare och öppna en användar profil.
1. Välj **tilldelade roller**.
1. Välj kugg hjuls ikonen. Ett fönster som kan ge den här informationen visas. Det finns en "ta bort"-knapp bredvid direkta tilldelningar. Ta bort den indirekta roll tilldelningen genom att ta bort användaren från gruppen som har tilldelats rollen.

**F:** Hur gör jag för att se alla grupper som kan tilldelas roll?

**A:** Följ dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com) och öppna **Azure Active Directory**.
1. Välj **grupper**  >  **alla grupper**.
1. Välj **Lägg till filter**.
1. Filter till **Roll tilldelnings** bara.

**F:** Hur gör jag för att vet du vilken roll som tilldelas ett huvud konto direkt och indirekt?

**A:** Följ dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com) och öppna **Azure Active Directory**.
1. Välj användare och öppna en användar profil.
1. Välj **tilldelade roller** och sedan:

    - I Azure AD Premium P1-licensierade organisationer: Välj kugg hjuls ikonen. Ett fönster som kan ge den här informationen visas.
    - I Azure AD Premium P2 licensierade organisationer hittar du direkt och ärvd licens information i kolumnen **medlemskap** .

**F:** Varför tvingar vi fram att skapa en ny moln grupp för att tilldela den rollen?  

**A:** Om du tilldelar en befintlig grupp till en roll kan den befintliga grupp ägaren lägga till andra medlemmar i gruppen utan att de nya medlemmarna verkligen har rollen. Eftersom grupper som kan tilldelas roll är kraftfulla, har vi en mängd begränsningar för att skydda dem. Du vill inte att ändringar i gruppen som ska överraskandes till den person som hanterar gruppen.

## <a name="next-steps"></a>Nästa steg

- [Använda molngrupper för att hantera rolltilldelningar](groups-concept.md)
- [Skapa en rolltilldelningsbar grupp](groups-create-eligible.md)
