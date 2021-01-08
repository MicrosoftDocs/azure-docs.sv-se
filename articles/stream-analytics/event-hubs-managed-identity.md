---
title: Använda hanterade identiteter för att komma åt Händelsehubben från ett Azure Stream Analytics jobb (förhands granskning)
description: Den här artikeln beskriver hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics jobb till Azure Event Hubs in-och utdata.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 4dc718f21f2ef3beabc31821bd60b571ac07be05
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018759"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Använda hanterade identiteter för att komma åt Händelsehubben från ett Azure Stream Analytics jobb (förhands granskning)

Azure Stream Analytics stöder autentisering med hanterad identitet för både indata och utdata i Azure Event Hubs. Hanterade identiteter eliminerar begränsningar för användarbaserade autentiseringsmetoder, t. ex. behovet av att autentisera på grund av lösen ords ändringar eller förfallo datum för användar-token som inträffar var 90: e dag. När du tar bort behovet av att autentisera manuellt, kan dina Stream Analytics-distributioner helt automatiseras.  

En hanterad identitet är ett hanterat program registrerat i Azure Active Directory som representerar ett angivet Stream Analytics jobb. Det hanterade programmet används för att autentisera till en mål resurs, inklusive Event Hubs som finns bakom en brand vägg eller ett virtuellt nätverk (VNet). Mer information om hur du kringgår brand väggar finns i [Tillåt åtkomst till Azure Event Hubs-namnområden via privata slut punkter](../event-hubs/private-link-service.md#trusted-microsoft-services).

Den här artikeln visar hur du aktiverar hanterad identitet för Event Hubs indata eller utdata för ett Stream Analytics jobb via Azure Portal.Innan du har aktiverat hanterad identitet måste du först ha en Stream Analytics jobb-och Event Hub-resurs.

### <a name="limitation"></a>Begränsning
Under för hands versionen fungerar inte insamlings indata från Event Hubs på Azure Portal när du använder läget för hanterad identitets autentisering.

## <a name="create-a-managedidentity"></a>Skapa en hanterad identitet  

Först skapar du en hanterad identitet för ditt Azure Stream Analytics jobb.  

1. Öppna Azure Stream Analytics-jobbet i Azure Portal.  

1. I den vänstra navigerings menyn väljer du **hanterad identitet** som   finns under *Konfigurera*. Markera sedan kryss rutan bredvid **Använd tilldelad hanterad identitet**   och välj **Spara**.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Systemtilldelad hanterad identitet":::  

1. Ett tjänst huvud namn för Stream Analytics jobbets identitet skapas i Azure Active Directory. Livs cykeln för den nyligen skapade identiteten hanteras av Azure. När Stream Analytics jobb tas bort, tas den tillhör ande identiteten (dvs. tjänstens huvud namn) automatiskt bort av Azure.  

   När du sparar konfigurationen visas objekt-ID: t (OID) för tjänstens huvud namn som huvud-ID: t enligt nedan:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="Ägar-ID":::

   Tjänstens huvud namn har samma namn som Stream Analyticss jobbet. Om namnet på ditt jobb exempelvis är är  `MyASAJob` namnet på tjänstens huvud namn också  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Ge Stream Analytics jobb behörighet att komma åt Händelsehubben

För att Stream Analytics jobbet ska kunna komma åt Händelsehubben med hanterad identitet måste tjänstens huvud namn som du skapade ha särskilda behörigheter för händelsehubben.

1. Gå till **Access Control (IAM)** i händelsehubben.

1. Välj **+ Lägg till** och **Lägg till roll tilldelning**.

1. På sidan **Lägg till roll tilldelning** anger du följande alternativ:

   |Parameter|Värde|
   |---------|-----|
   |Roll|Azure Event Hubs data ägare|
   |Tilldela åtkomst till|Användare, grupp eller tjänstens huvud namn|
   |Välj|Ange namnet på ditt Stream Analytics jobb|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Lägg till rolltilldelning":::

1. Välj **Spara** och vänta en minut för att ändringarna ska spridas.

Du kan också ge den här rollen på namn området för Händelsehubben, som naturligt kommer att sprida behörigheter till alla Event Hubs som skapas under den. Det vill säga att alla Event Hubs under ett namn område kan användas som en resurs för hanterad identitets autentisering i ditt Stream Analytics jobb.

## <a name="create-anevent-hub-input-or-output"></a>Skapa en indata eller utdata för Event Hub  

Nu när din hanterade identitet har kon figurer ATS är du redo att lägga till Event Hub-resursen som indata eller utdata till ditt Stream Analytics-jobb.  

### <a name="add-the-event-hub-as-an-input"></a>Lägg till Event Hub som inmatad 

1. Gå till ditt Stream Analytics jobb och gå till sidan **indata** under **jobb sto pol Ogin**.

1. Välj **Lägg till Stream-indata > Event Hub**. I fönstret ingångs egenskaper söker du och väljer Händelsehubben och väljer **hanterad identitet** i list rutan *autentiseringsläge* .

1. Fyll i resten av egenskaperna och välj **Spara**.

### <a name="add-the-event-hub-as-an-output"></a>Lägg till Händelsehubben som utdata

1. Gå till ditt Stream Analytics jobb och gå till sidan **utdata** under **jobb sto pol Ogin**.

1. Välj **Lägg till > Event Hub**. I fönstret Egenskaper för utdata söker du och väljer Händelsehubben och väljer **hanterad identitet** i den nedrullningsbara menyn *autentiseringsläge* .

1. Fyll i resten av egenskaperna och välj **Spara**.

## <a name="next-steps"></a>Nästa steg

* [Event Hubs utdata från Azure Stream Analytics](event-hubs-output.md)
* [Strömma data med Event Hubs](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
