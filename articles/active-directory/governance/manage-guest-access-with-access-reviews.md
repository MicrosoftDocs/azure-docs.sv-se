---
title: Hantera gäst åtkomst med åtkomst granskningar – Azure AD
description: Hantera gäst användare som medlemmar i en grupp eller tilldelas till ett program med Azure Active Directory åtkomst granskningar
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76aeb63feb006e6962efeb3687363f18acb59904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746702"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Hantera gäståtkomst med Azure AD-åtkomstgranskningar


Med Azure Active Directory (Azure AD) kan du enkelt aktivera samarbete mellan organisations gränser med hjälp av [funktionen Azure AD B2B](../external-identities/what-is-b2b.md). Gäst användare från andra klienter kan [bjudas in av administratörer](../external-identities/add-users-administrator.md) eller av [andra användare](../external-identities/what-is-b2b.md). Den här funktionen gäller även sociala identiteter som Microsoft-konton.

Du kan också enkelt se till att gäst användare har lämplig åtkomst. Du kan be gästerna själva eller en besluts fattare att delta i en åtkomst granskning och omcertifiera (eller intyga) till gästernas åtkomst. Granskarna kan ge sin syn på varje användares behov för kontinuerlig åtkomst, baserat på förslag från Azure AD. När en åtkomst granskning är färdig kan du göra ändringar och ta bort åtkomst för gäster som inte längre behöver den.

> [!NOTE]
> Det här dokumentet fokuserar på att granska gäst användares åtkomst. Om du vill granska alla användares åtkomst, inte bara gäster, se [hantera användar åtkomst med åtkomst granskningar](manage-user-access-with-access-reviews.md). Om du vill granska användarens medlemskap i administrativa roller, t. ex. global administratör, se [starta en åtkomst granskning i Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2

För mer information, [licens krav](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Skapa och utför en åtkomst granskning för gäster

Först måste du ha tilldelats någon av följande roller:
- global administratör
- Användaradministratör
- Förhandsgranskningsvyn M365 eller AAD-säkerhetsgruppens ägare av gruppen som ska granskas

Gå sedan till [sidan identitets styrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) för att säkerställa att åtkomst granskningar är klara för din organisation.

Azure AD möjliggör flera scenarier för att granska gäst användare.

Du kan granska antingen:

 - En grupp i Azure AD som har en eller flera gäster som medlemmar.
 - Ett program som är anslutet till Azure AD som har en eller flera gäst användare tilldelade till sig. 

Du kan sedan bestämma om du vill be varje gäst att granska sin egen åtkomst eller be en eller flera användare att granska varje gästs åtkomst.

 Dessa scenarier beskrivs i följande avsnitt.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Be gästerna att granska sitt eget medlemskap i en grupp

Du kan använda åtkomst granskningar för att se till att användare som har bjudits in och lagts till i en grupp fortfarande behöver åtkomst. Du kan enkelt be gästerna att granska sitt eget medlemskap i gruppen.

1. Om du vill skapa en åtkomst granskning för gruppen väljer du granskningen för att inkludera enbart gäst användar medlemmar och att medlemmarna granskar sig själva. Mer information finns i [skapa en åtkomst granskning av grupper eller program](create-access-review.md).

2. Be varje gäst att granska sitt eget medlemskap. Varje gäst som har godkänt en inbjudan får som standard ett e-postmeddelande från Azure AD med en länk till åtkomst granskningen. Azure AD innehåller instruktioner för gäster för hur du [granskar åtkomst till grupper eller program](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [fullständig åtkomst granskning av grupper eller program](complete-access-review.md).

4. Förutom de användare som nekade sitt eget behov av fortsatt åtkomst kan du också ta bort användare som inte svarade. Icke-svarande användare som eventuellt inte längre får e-post.

5. Om gruppen inte används för åtkomst hantering kan du också ta bort användare som inte har valt att delta i granskningen eftersom de inte accepterade sin inbjudan. Om du inte accepterar kan det bero på att den inbjudna användarens e-postadress hade ett skrivfel. Om en grupp används som en distributions lista kanske vissa gäst användare inte har valts för att delta eftersom de är kontakt objekt.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Be en sponsor att granska ett gäst medlemskap i en grupp

Du kan be en sponsor, till exempel ägare av en grupp, att granska en gästs behov för fortsatt medlemskap i en grupp.

1. Om du vill skapa en åtkomst granskning för gruppen väljer du bara granskningen för att inkludera gäst användar medlemmar. Ange sedan en eller flera granskare. Mer information finns i [skapa en åtkomst granskning av grupper eller program](create-access-review.md).

2. Be granskarna att komma med reflektioner. Som standard får de ett e-postmeddelande från Azure AD med en länk till åtkomst panelen där de [granskar åtkomst till grupper eller program](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [fullständig åtkomst granskning av grupper eller program](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Be gästerna att granska sin egen åtkomst till ett program

Du kan använda åtkomst granskningar för att se till att användare som bjudits in till ett visst program fortfarande behöver åtkomst. Du kan enkelt be gästerna själva att granska sina egna behov av åtkomst.

1. Om du vill skapa en åtkomst granskning för programmet väljer du granskningen för att endast inkludera gäster och att användarna granskar sin egen åtkomst. Mer information finns i [skapa en åtkomst granskning av grupper eller program](create-access-review.md).

2. Be varje gäst att granska sin egen åtkomst till programmet. Varje gäst som har godkänt en inbjudan får som standard ett e-postmeddelande från Azure AD. E-postmeddelandet innehåller en länk till åtkomst granskningen i organisationens åtkomst panel. Azure AD innehåller instruktioner för gäster för hur du [granskar åtkomst till grupper eller program](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [fullständig åtkomst granskning av grupper eller program](complete-access-review.md).

4. Förutom användare som nekade sitt eget behov av fortsatt åtkomst kan du också ta bort gäst användare som inte svarade. Icke-svarande användare som eventuellt inte längre får e-post. Du kan också ta bort gäst användare som inte valde att delta, särskilt om de inte nyligen bjudits in. Användarna accepterade inte sin inbjudan och har därför inte åtkomst till programmet. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Be en sponsor att granska en gästs åtkomst till ett program

Du kan be en sponsor, till exempel ägare av ett program, att granska gästens behov av fortsatt åtkomst till programmet.

1. Om du vill skapa en åtkomst granskning för programmet väljer du granska som endast innehåller gäster. Ange sedan en eller flera användare som granskare. Mer information finns i [skapa en åtkomst granskning av grupper eller program](create-access-review.md).

2. Be granskarna att komma med reflektioner. Som standard får de ett e-postmeddelande från Azure AD med en länk till åtkomst panelen där de [granskar åtkomst till grupper eller program](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [fullständig åtkomst granskning av grupper eller program](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Be gästerna granska deras behov av åtkomst, i allmänhet

I vissa organisationer kanske gästerna inte känner till deras grupp medlemskap.

> [!NOTE]
> Tidigare versioner av Azure Portal tillåter inte administrativ åtkomst av användare med UserType av gäst. I vissa fall kan en administratör i katalogen ha ändrat ett gästs UserType-värde till medlem med hjälp av PowerShell. Om den här ändringen tidigare har skett i din katalog kanske den föregående frågan inte innehåller alla gäst användare som tidigare hade administratörs behörighet. I så fall måste du antingen ändra gästens UserType eller manuellt inkludera gästen i grupp medlemskapet.

1. Skapa en säkerhets grupp i Azure AD med gäster som medlemmar, om en lämplig grupp inte redan finns. Du kan till exempel skapa en grupp med en manuellt underhålls medlem av gäster. Du kan också skapa en dynamisk grupp med ett namn som "gäster för Contoso" för användare i Contoso-klienten som har UserType-attributvärdet gäst.  För att få effektivitet bör du se till att gruppen är dominerat för gäster – Välj inte en grupp som har medlems användare, eftersom medlems användare inte behöver granskas.  Tänk också på att en gäst användare som är medlem i gruppen kan se andra medlemmar i gruppen.

2. Om du vill skapa en åtkomst granskning för gruppen väljer du granskarna som själva medlemmarna. Mer information finns i [skapa en åtkomst granskning av grupper eller program](create-access-review.md).

3. Be varje gäst att granska sitt eget medlemskap. Varje gäst som har godkänt en inbjudan får som standard ett e-postmeddelande från Azure AD med en länk till åtkomst granskningen i organisationens åtkomst panel. Azure AD innehåller instruktioner för gäster för hur du [granskar åtkomst till grupper eller program](perform-access-review.md).  De gäster som inte accepterade sin inbjudan visas i gransknings resultaten som "inte meddelas".

4. När granskarna har angett ininformation, stoppar du åtkomst granskningen. Mer information finns i [fullständig åtkomst granskning av grupper eller program](complete-access-review.md).

5. Ta bort gäst åtkomst för gäster som nekats, slutförde inte granskningen eller inte tidigare godkänt sin inbjudan. Om några av gästerna är kontakter som valde att delta i granskningen eller inte tidigare har accepterat en inbjudan kan du inaktivera deras konton med hjälp av Azure Portal eller PowerShell. Om gästen inte längre behöver åtkomst och inte är en kontakt kan du ta bort användar objekt från katalogen med hjälp av Azure Portal eller PowerShell för att ta bort gäst användar objektet.

## <a name="next-steps"></a>Nästa steg

[Skapa en åtkomst granskning av grupper eller program](create-access-review.md)