---
title: Konfigurera en hanterad program plan
description: Lär dig hur du konfigurerar en hanterad program plan för ditt Azure Application-erbjudande i Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744643"
---
# <a name="configure-a-managed-application-plan"></a>Konfigurera en hanterad program plan

Den här artikeln gäller endast hanterade program planer för ett erbjudande om Azure-program. Om du konfigurerar en plan för lösnings mal len går du till [Konfigurera en plan för lösnings mal len](create-new-azure-apps-offer-solution.md).

## <a name="define-markets-pricing-and-availability"></a>Definiera marknader, priser och tillgänglighet

Varje plan måste vara tillgänglig på minst en marknad. På fliken **priser och tillgänglighet** kan du konfigurera de marknader som planen kommer att vara tillgänglig i, priset och om du vill göra planen synlig för alla eller bara för vissa kunder (även kallat privat plan).

1. Under **marknader** väljer du länken **Redigera marknader** .
1. I dialog rutan som visas väljer du de marknads platser där du vill göra ditt abonnemang tillgängligt. Du måste välja minst en och högst 141 marknader.

    > [!NOTE]
    > Den här dialog rutan innehåller en sökruta och ett alternativ som du kan använda för att filtrera efter "skatte mottagare", där Microsoft betalar och använder moms för din räkning.

1. Stäng dialog rutan genom att välja **Spara**.

## <a name="define-pricing"></a>Definiera priser

I rutan **pris** anger du priset per månad för den här planen. Det här priset gäller utöver alla Azure-infrastrukturer eller användnings kostnader som uppstår i de resurser som distribueras av den här lösningen.

Utöver priset per månad kan du också ange priser för förbrukning av enheter som inte är standard med hjälp av [avgiftsbelagd fakturering](partner-center-portal/azure-app-metered-billing.md). Du kan ställa in priset per månad på noll och debitera exklusivt genom att fakturera faktureringen om du vill.

Priserna anges i USD (USD = USA dollar) konverteras till den lokala valutan för alla valda marknader med de aktuella växelkurserna när de sparas. Validera dessa priser innan du publicerar genom att exportera pris kalkyl bladet och granska priset på varje marknad. Om du vill ange anpassade priser på en enskild marknad ändrar du och importerar pris kalkyl bladet.

### <a name="add-a-custom-meter-dimension-optional"></a>Lägg till en anpassad mätnings dimension (valfritt)

1. Under **Marketplace mått för avläsning av tjänst** väljer du länken **Lägg till en anpassad mätnings dimension (max 18)** .
1. I rutan **ID** anger du referensen för det oåterkalleliga ID: t vid sändning av användnings händelser.
1. I rutan **visnings namn** anger du det visnings namn som är associerat med dimensionen. Till exempel "textmeddelanden har skickats".
1. I rutan mått **enhet** anger du en beskrivning av fakturerings enheten. Till exempel "per textmeddelande" eller "per 100-e-post".
1. I rutan **pris per enhet i USD** anger du priset för en dimensions enhet.
1. I rutan **månatlig kvantitet som ingår i bas** anger du kvantiteten (som ett heltal) för dimensionen som ingår varje månad för kunder som betalar den återkommande månads avgiften. Markera kryss rutan i stället om du vill ange en obegränsad kvantitet.
1. Upprepa steg 1 till 7 för att lägga till en annan anpassad mätnings dimension.

### <a name="set-custom-prices-optional"></a>Ange anpassade priser (valfritt)

Priser som angetts i USD (USD = USA dollar) konverteras till den lokala valutan för alla valda marknader med de aktuella växelkurserna när de sparas. Validera dessa priser innan du publicerar genom att exportera pris kalkyl bladet och granska priset på varje marknad. Om du vill ange anpassade priser på en enskild marknad ändrar du och importerar pris kalkyl bladet.

Granska dina priser noggrant innan du publicerar eftersom det finns vissa begränsningar för vad som kan ändras när en plan har publicerats.

> [!NOTE]
> När du har publicerat ett pris för en marknad i planen kan du inte ändra det senare.

Om du vill ange anpassade priser på en enskild marknad, exportera, ändra och importera sedan pris kalkyl bladet. Du ansvarar för att verifiera priset och äga dessa inställningar. Detaljerad information finns i [anpassade priser](plans-pricing.md#custom-prices).

1. Du måste först spara dina pris ändringar för att möjliggöra export av pris information. Nästan längst ned på fliken **priser och tillgänglighet** väljer du **Spara utkast**.
1. Under **prissättning** väljer du länken **Exportera pris data** .
1. Öppna exportedPrice.xlsx-filen i Microsoft Excel.
1. I kalkyl bladet gör du de uppdateringar som du vill ha på din pris information och sparar sedan filen.

   Du kan behöva aktivera redigering i Excel innan du kan uppdatera filen.

1. På fliken **priser och tillgänglighet** under **prissättning** väljer du länken **Importera prissättnings data** .
1. I dialog rutan som visas klickar du på **Ja**.
1. Välj den exportedPrice.xlsx-fil som du har uppdaterat och klicka sedan på **Öppna**.

## <a name="choose-who-can-see-your-plan"></a>Välj vem som kan se din plan

Du kan konfigurera varje plan så att den blir synlig för alla eller bara för en speciell mål grupp. Du beviljar åtkomst till en privat publik med hjälp av Azures prenumerations-ID med alternativet att inkludera en beskrivning av varje prenumerations-ID som du tilldelar. Du kan lägga till högst 10 prenumerations-ID: n manuellt eller upp till 10 000 prenumerations-ID med hjälp av en. CSV-fil. ID: n för Azure-prenumerationen visas som GUID och bokstäver måste vara gemena.

> [!NOTE]
> Om du publicerar en privat plan kan du ändra dess synlighet till offentlig senare. Men när du har publicerat ett offentligt abonnemang kan du inte ändra dess synlighet till privat.

Under **plan synlighet** gör du något av följande:

- Om du vill göra planen offentlig väljer du knappen **offentlig** alternativ (kallas även för en _alternativ knapp_).
- Om du vill göra planen privat väljer du alternativet **privat** alternativ och lägger sedan till Azure-prenumerations-ID: n manuellt eller med en CSV-fil.

> [!NOTE]
> En privat eller begränsad mål grupp skiljer sig från den förhands visnings mål som du definierade på fliken **förhands granskning** . En förhands gransknings grupp kan komma åt ditt erbjudande innan det publiceras Live på Marketplace. Även om valet för privat mål bara gäller för en speciell plan, kan förhands gransknings gruppen Visa alla planer (privata eller inte) för validerings syfte.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Lägg till Azure-prenumerations-ID: n manuellt för en privat plan

1. Under **plan synlighet** väljer du knappen **privat** alternativ.
1. I rutan **Azure-prenumerations-ID** som visas anger du ID för Azure-prenumeration för den mål grupp som du vill bevilja åtkomst till den privata planen. Minst ett prenumerations-ID krävs.
1. Valfritt Ange en beskrivning av den här mål gruppen i rutan **Beskrivning** .
1. Om du vill lägga till ett annat prenumerations-ID väljer du länken **Lägg till ID (max 10)** och upprepar steg 2 och 3.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Använd en. CSV-fil för att lägga till ID för Azure-prenumeration för en privat plan

1. Under **plan synlighet** väljer du knappen **privat** alternativ.
1. Välj länken **Exportera mål grupp (CSV)** .
1. Öppna. CSV-fil och Lägg till de ID: n för Azure-prenumerationen som du vill bevilja åtkomst till det privata erbjudandet i kolumnen **ID** .
1. Alternativt kan du ange en beskrivning för varje mål grupp i kolumnen **Beskrivning** .
1. Lägg till "SubscriptionId" i kolumnen **typ** för varje rad med ett PRENUMERATIONS-ID.
1. Spara. CSV-fil.
1. På fliken **tillgänglighet** under **plan synlighet** väljer du länken **Importera mål grupp (CSV)** .
1. I dialog rutan som visas väljer du **Ja**.
1. Välj. CSV-fil och välj sedan **Öppna**. Ett meddelande visas som anger att. CSV-filen har importer ATS.

## <a name="define-the-technical-configuration"></a>Definiera den tekniska konfigurationen

På fliken **teknisk konfiguration** laddar du upp distributions paketet som låter kunderna distribuera din plan och tillhandahålla ett versions nummer för paketet. Du får även annan teknisk information.

> [!NOTE]
> Den här fliken visas inte om du väljer att återanvända paket från en annan plan på fliken **Planera konfiguration** . I så fall kan du gå till [Visa dina planer](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Tilldela ett versions nummer för paketet

I rutan **version** anger du den aktuella versionen av den tekniska konfigurationen. Öka den här versionen varje gången du publicerar en ändring på den här sidan. Versions numret måste vara i formatet: Integer. Integer. Integer. Till exempel `1.0.2`.

### <a name="upload-a-package-file"></a>Ladda upp en paketfil

Under **paketfil (. zip)** drar du paket filen till den grå rutan eller väljer länken **Bläddra efter din fil (er)** .

> [!NOTE]
> Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som `https://upload.xboxlive.com` används av Partner Center.

#### <a name="previously-published-packages"></a>Tidigare publicerade paket

Under fliken **tidigare publicerade paket** kan du Visa alla publicerade versioner av den tekniska konfigurationen.

### <a name="enable-just-in-time-jit-access-optional"></a>Aktivera JIT-åtkomst (just-in-Time) (valfritt)

Markera kryss rutan **Aktivera JIT-åtkomst (aktivera just-in-Time)** om du vill aktivera JIT-åtkomst för den här planen. Om du vill kräva att konsumenter av ditt hanterade program ger ditt konto permanent åtkomst, lämnar du alternativet omarkerat. Om du vill veta mer om det här alternativet, se [just-in-Time (JIT-åtkomst)](plan-azure-app-managed-app.md#just-in-time-jit-access).

### <a name="select-a-deployment-mode"></a>Välj ett distributions läge

Välj antingen det **fullständiga** eller **stegvisa** distributions läget.

- I **komplett** läge leder en omdistribution av programmet av kunden till att resurser i den hanterade resurs gruppen tas bort om resurserna inte har definierats i [mainTemplate.jspå](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template).
- I **stegvist** läge lämnar en omdistribution av programmet befintliga resurser oförändrade.

Läs mer om distributions lägen i [Azure Resource Manager distributions lägen](../azure-resource-manager/templates/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Ange en URL för meddelande slut punkten

I rutan **URL för meddelande slut punkt** anger du en https webhook-slutpunkt för att ta emot aviseringar om alla CRUD-åtgärder på hanterade program instanser av den här plan versionen.

### <a name="customize-allowed-customer-actions-optional"></a>Anpassa tillåtna kund åtgärder (valfritt)

1. Om du vill ange vilka åtgärder som kunder kan utföra på hanterade resurser utöver de `*/read` åtgärder som är tillgängliga som standard, väljer du rutan **Anpassa tillåtna kund åtgärder** .
1. I rutorna som visas anger du ytterligare kontroll åtgärder och tillåtna data åtgärder som du vill att kunden ska kunna utföra, avgränsade med semikolon. Om du till exempel vill tillåta att användare startar om virtuella datorer, lägger `Microsoft.Compute/virtualMachines/restart/action` du till i rutan **tillåtna kontroll åtgärder** .

### <a name="choose-who-can-manage-the-application"></a>Välj vem som kan hantera programmet

Ange vem som ska ha hanterings åtkomst till det här hanterade programmet i varje vald Azure-region: _Global Azure_ och _Azure Government molnet_. Du kommer att använda Azure AD-identiteter för att identifiera de användare, grupper eller program som du vill ge behörighet till den hanterade resurs gruppen. Mer information finns i [planera ett Azure-hanterat program för ett Azure Application erbjudande](plan-azure-application-offer.md).

Slutför följande steg för Global Azure och Azure Government molnet, efter vad som är tillämpligt.

1. I rutan **Azure Active Directory klient-ID** anger du Azure AD-klient-ID: t (även kallat katalog-ID) som innehåller identiteterna för de användare, grupper eller program som du vill ge behörighet till.
1. I rutan **ägar-ID** anger du Azure AD-objekt-ID: t för den användare, grupp eller det program som du vill bevilja behörighet till den hanterade resurs gruppen. Identifiera användaren med sitt huvud-ID, som finns på [bladet Azure Active Directory användare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) på Azure Portal.
1. I listan **roll definition** väljer du en inbyggd Azure AD-roll. Rollen du väljer beskriver de behörigheter som huvud kontot kommer att ha på resurserna i kund prenumerationen.
1. Om du vill lägga till en annan auktorisering väljer du länken **Lägg till auktorisering (max 100)** och upprepar steg 1 till 3.

### <a name="policy-settings-optional"></a>Princip inställningar (valfritt)

Du kan konfigurera högst fem principer och bara en instans av varje princip alternativ. Vissa principer kräver ytterligare parametrar.

1. Under **princip inställningar** väljer du länken **+ Lägg till princip (max 5)** .
1. I rutan **namn** anger du princip tilldelnings namnet (begränsat till 50 tecken).
1. I list rutan **principer** väljer du den Azure-princip som ska tillämpas på resurser som skapats av det hanterade programmet i kund prenumerationen.
1. I rutan **princip parametrar** anger du den parameter som ska användas för principerna för gransknings-och diagnostiska inställningar.
1. I list rutan **princip-SKU** väljer du princip-SKU-typ.

    > [!NOTE]
    > _Standard princip_ -SKU: n krävs för gransknings principer.

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