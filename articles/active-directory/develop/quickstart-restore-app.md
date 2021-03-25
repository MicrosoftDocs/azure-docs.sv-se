---
title: 'Gör så här: återställa eller ta bort ett nyligen borttaget program med Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: I den här instruktionen får du lära dig hur du återställer eller permanent tar bort ett nyligen borttaget program som är registrerat hos Microsoft Identity Platform.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: a5cf8f42bb15d3ce566506d85fac192027a1c69f
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105039033"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Återställa eller ta bort ett nyligen borttaget program med Microsoft Identity Platform
När du har tagit bort en app-registrering förblir appen i ett pausat tillstånd i 30 dagar. Under den 30-dagars perioden kan registreringen av appen återställas, tillsammans med alla dess egenskaper. När 30-dagars perioden har passerat går det inte att återställa App-registreringar och den permanenta borttagnings processen kan starta automatiskt.  Den här funktionen gäller endast för program som är kopplade till en katalog.  Den är inte tillgänglig för program från en personlig Microsoft-konto som inte kan återställas.

Du kan visa dina borttagna program, återställa ett borttaget program eller permanent ta bort ett program med hjälp av Appregistreringar-upplevelsen under Azure Active Directory (Azure AD) i Azure Portal.

Observera att varken du eller Microsofts kund support kan återställa ett permanent borttaget program eller ett program som har tagits bort mer än 30 dagar sedan.

## <a name="required-permissions"></a>Behörigheter som krävs
Du måste ha någon av följande roller för att ta bort program permanent.

- Global administratör

- Program administratör

- Moln program administratör

- Hybrid identitets administratör

- Program ägare

Du måste ha någon av följande roller för att återställa program.

- Global administratör

- Program ägare

## <a name="deleted-applications-ui--preview"></a>Användar gränssnitt för borttagna program | Förhandsgranskningsvyn

> [!IMPORTANT]
> Den borttagna program portalens GRÄNSSNITTs funktion [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

### <a name="view-your-deleted-applications"></a>Visa dina borttagna program
Du kan se alla program i läget Soft Deleted.  Endast program som har tagits bort under 30 dagar sedan kan återställas.

#### <a name="to-view-your-restorable-applications"></a>Så här visar du dina återställas-program
1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Sök och välj **Azure Active Directory**, Välj **Appregistreringar** och välj fliken **borttagna program (för hands version)** .

    Granska listan med program. Endast program som har tagits bort under de senaste 30 dagarna är tillgängliga för återställning. Om du använder Appregistreringar Sök förhands granskning kan du filtrera efter kolumnen borttaget datum om du bara vill se dessa program.

### <a name="restore-a-recently-deleted-application"></a>Återställa ett nyligen borttaget program

När en app-registrering tas bort från organisationen är appen i ett inaktiverat tillstånd och dess konfigurationer bevaras. När du återställer en registrerad app återställs även konfigurationerna.  Men om det fanns några företagsspecifika inställningar i **företags program** för programmets hem klient kommer de inte att återställas.  

Detta beror på att företagsspecifika inställningar lagras på ett separat objekt, vilket kallas för tjänstens huvud namn.  Inställningar som lagras i tjänstens huvud namn inkluderar behörighets medgivande och användar-och grupp tilldelningar för en viss organisation. de här konfigurationerna kommer inte att återställas när appen återställs. Mer information finns i [objekt för program-och tjänst objekt](app-objects-and-service-principals.md). 


#### <a name="to-restore-an-application"></a>Återställa ett program
1. På fliken **borttagna program (för hands version)** söker du efter och väljer ett av de program som har tagits bort under 30 dagar sedan.

2. Välj **Återställ registrering av appar**.

### <a name="permanently-delete-an-application"></a>Ta bort ett program permanent
Du kan manuellt ta bort ett program från din organisation Permanent. Ett permanent borttaget program kan inte återställas av dig, en annan administratör eller av Microsofts kund support.

#### <a name="to-permanently-delete-an-application"></a>Ta bort ett program permanent

1. På fliken **borttagna program (för hands version)** söker du efter och väljer något av de tillgängliga programmen.

2. Välj **ta bort permanent**.

3. Läs varnings texten och välj **Ja**.

## <a name="next-steps"></a>Nästa steg
När du har återställt eller tagit bort appen permanent kan du:

- [Lägga till ett program](quickstart-register-app.md)

- Läs mer om [program-och tjänst huvud objekt](app-objects-and-service-principals.md) i Microsoft Identity Platform.
