---
title: Konfigurera en plan för lösnings mal len
description: Lär dig hur du konfigurerar en plan för lösnings mal len för ditt Azure Application-erbjudande i Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744626"
---
# <a name="configure-a-solution-template-plan"></a>Konfigurera en plan för lösnings mal len

Den här artikeln gäller endast för lösningar för lösnings mal len för ett erbjudande för Azure-program. Om du konfigurerar en hanterad program plan går du till [Konfigurera en hanterad program plan](create-new-azure-apps-offer-managed.md).

## <a name="choose-who-can-see-your-plan"></a>Välj vem som kan se din plan

Du kan konfigurera varje plan så att den blir synlig för alla eller bara för en speciell mål grupp. Du beviljar åtkomst till en privat publik med hjälp av Azures prenumerations-ID med alternativet att inkludera en beskrivning av varje prenumerations-ID som du tilldelar. Du kan lägga till högst 10 prenumerations-ID: n manuellt eller upp till 10 000 prenumerations-ID med hjälp av en. CSV-fil. ID: n för Azure-prenumerationen visas som GUID och bokstäver måste vara gemena.

> [!NOTE]
> Om du publicerar en privat plan kan du ändra dess synlighet till offentlig senare. Men när du har publicerat ett offentligt abonnemang kan du inte ändra dess synlighet till privat.

Gör något av följande under **plan synlighet** på fliken **tillgänglighet** :

- Om du vill göra planen offentlig väljer du knappen **offentlig** alternativ (kallas även för en _alternativ knapp_).
- Om du vill göra planen privat väljer du alternativet **privat** alternativ och lägger sedan till Azure-prenumerations-ID: n manuellt eller med en CSV-fil.

    > [!NOTE]
    > En privat eller begränsad mål grupp skiljer sig från den förhands visnings mål som du definierade på fliken **förhands granskning** . En förhands gransknings grupp kan komma åt ditt erbjudande innan det publiceras Live på Marketplace. Även om valet för privat mål bara gäller för en speciell plan, kan förhands gransknings gruppen Visa alla planer (privata eller inte) för validerings syfte.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Lägg till Azure-prenumerations-ID: n manuellt för en privat plan

1. Under **plan synlighet** väljer du knappen **privat** alternativ.
1. I rutan **Azure-prenumerations-ID** som visas anger du ID för Azure-prenumeration för den mål grupp som du vill bevilja åtkomst till den privata planen. Minst ett prenumerations-ID krävs.
1. Valfritt Ange en beskrivning av den här mål gruppen i rutan **Beskrivning** .
1. Om du vill lägga till ett annat prenumerations-ID väljer du länken **Lägg till ID (max 10)** och upprepar steg 2 och 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Använd en. CSV-fil för att lägga till ID för Azure-prenumeration för en privat plan

1. Under **plan synlighet** väljer du knappen **privat** alternativ.
1. Välj länken **Exportera mål grupp (CSV)** .
1. Öppna. CSV-fil och Lägg till de ID: n för Azure-prenumerationen som du vill bevilja åtkomst till det privata erbjudandet i kolumnen **ID** .
1. Alternativt kan du ange en beskrivning för varje mål grupp i kolumnen **Beskrivning** .
1. Lägg till "SubscriptionId" i kolumnen **typ** för varje rad med ett PRENUMERATIONS-ID.
1. Spara. CSV-fil.
1. På fliken **tillgänglighet** under **plan synlighet** väljer du länken **Importera mål grupp (CSV)** .
1. I dialog rutan som visas väljer du **Ja**.
1. Välj. CSV-fil och välj sedan **Öppna**. Ett meddelande visas som anger att. CSV-filen har importer ATS.

### <a name="hide-your-plan"></a>Dölj din plan

Om din lösnings mall är avsedd att distribueras endast indirekt när den refereras till, även om en annan lösning eller ett hanterat program, markerar du kryss rutan under **Dölj plan** för att publicera din lösnings mall, men döljer den från kunder som söker och bläddrar efter den direkt.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt: definiera den tekniska konfigurationen.

## <a name="define-the-technical-configuration"></a>Definiera den tekniska konfigurationen

På fliken **teknisk konfiguration** laddar du upp distributions paketet som låter kunderna distribuera din plan och tillhandahålla ett versions nummer för paketet.

> [!NOTE]
> Den här fliken visas inte om du väljer att återanvända paket från en annan plan på fliken **Planera konfiguration** . I så fall kan du gå till [Visa dina planer](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Tilldela ett versions nummer för paketet

I rutan **version** anger du den aktuella versionen av den tekniska konfigurationen. Öka den här versionen varje gången du publicerar en ändring på den här sidan. Versions numret måste vara i formatet: Integer. Integer. Integer. Till exempel `1.0.2`.

### <a name="upload-a-package-file"></a>Ladda upp en paketfil

Under **paketfil (. zip)** drar du paket filen till den grå rutan eller väljer länken **Bläddra efter din fil (er)** .

> [!NOTE]
> Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som `https://upload.xboxlive.com` används av Partner Center.

### <a name="previously-published-packages"></a>Tidigare publicerade paket

När du har publicerat erbjudandet Live visas den underordnade fliken **publicerade paket** på sidan **teknisk konfiguration** . På den här fliken visas alla tidigare publicerade versioner av den tekniska konfigurationen.

## <a name="view-your-plans"></a>Visa dina planer

- Välj **Spara utkast**. i det övre vänstra hörnet på sidan väljer du **plan översikt** för att återgå till sidan **plan översikt** .

När du har skapat ett eller flera planer ser du ditt plan namn, plan-ID, plan typ, tillgänglighet (offentlig eller privat), aktuell publicerings status och alla tillgängliga åtgärder på fliken **plan översikt** .

De åtgärder som är tillgängliga i kolumnen **åtgärd** på fliken **plan översikt** varierar beroende på status för din plan och kan innehålla följande:

- Om plan status är **utkast**, kommer länken i kolumnen **åtgärd** att säga **ta bort utkast**.
- Om plan status är **Live** kommer länken i kolumnen **åtgärd** att antingen **sluta säljas planen** eller **synkronisera den privata mål gruppen**. Länken **Synkronisera privat publik** kommer bara att publicera ändringar i dina privata mål grupper utan att publicera några andra uppdateringar som du har gjort i erbjudandet.
- Om du vill skapa ett annat schema för det här erbjudandet väljer du **+ Skapa ny plan** längst upp på fliken **plan översikt** . Upprepa sedan stegen i [så här skapar du planer för ditt erbjudande för Azure-program](create-new-azure-apps-offer-plans.md). Annars, om du är klar med att skapa planer, går du till nästa avsnitt: nästa steg.

## <a name="next-steps"></a>Nästa steg

- [Testa och publicera Azure Application erbjudandet](create-new-azure-apps-offer-test-publish.md).
- Lär dig [hur du säljer ditt Azure Applications erbjudande](create-new-azure-apps-offer-marketing.md) genom medförsäljning med Microsoft och som återförsäljs via CSP-program.
