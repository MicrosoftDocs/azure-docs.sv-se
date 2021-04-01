---
title: Köp ett anpassat domän namn
description: Lär dig hur du köper en App Service domän och använder den som en anpassad domän för din app Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704862"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Köpa ett anpassat domännamn för Azure App Service

App Service domäner är anpassade domäner som hanteras direkt i Azure. De gör det enkelt att hantera anpassade domäner för [Azure App Service](overview.md). Den här självstudien visar hur du köper en App Service domän och tilldelar DNS-namn till Azure App Service.

Information om virtuella Azure-datorer eller Azure Storage finns i [tilldela App Service domän till Azure VM eller Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Mer Cloud Services finns i [Konfigurera ett anpassat domän namn för en Azure-moln tjänst](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Skapa en App Service-app](./index.yml), eller använd en app som du har skapat för en annan kurs. Appen bör vara i en offentlig Azure-region. För närvarande stöds inte de nationella Azure-molnen.
* [Ta bort utgifts gränsen för din prenumeration](../cost-management-billing/manage/spending-limit.md#remove). Du kan inte köpa App Service domäner med kostnads fria prenumerations krediter.

## <a name="buy-an-app-service-domain"></a>Köp en App Service-domän

Information om priser för App Service domäner finns på [sidan med App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/windows/) och rulla ned till App Service domän.

1. Öppna [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. I Sök fältet söker du efter och väljer **App Service domäner**.

    ![Portal navigering till Azure App Service domäner](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. I vyn **App Service domäner** klickar du på **Lägg till**.

    ![Klicka på Lägg till i App Service domäner](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Välj **Klicka för att testa den nyare versionen av App Service domäner skapa upplevelse**.

    ![Skapa App Service domän med ny upplevelse](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **grundläggande** inställningar konfigurerar du inställningarna med följande tabell:  

   | Inställning  | Beskrivning |
   | -------- | ----------- |
   | **Prenumeration** | Den prenumeration som ska användas för att köpa domänen. |
   | **Resursgrupp** | Resurs gruppen där domänen ska beställas. Till exempel är resurs gruppen som din app är i. |
   | **Domän** | Ange den domän som du vill använda. Till exempel **contoso.com**. Om den domän som du vill använda inte är tillgänglig kan du välja från en lista över tillgängliga domäner eller testa en annan domän. |

    > [!NOTE]
    > Följande [toppnivå domäner](https://wikipedia.org/wiki/Top-level_domain) stöds av App Service domäner: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _BIZ_, _org.uk_ och _co.in_.
    >
    >
    
2. När du är färdig klickar du på **Nästa: kontakt information**.

### <a name="contact-information-tab"></a>Fliken kontakt information

1. Ange din information som krävs av [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) för domän registreringen. 

    Det är viktigt att du fyller i alla obligatoriska fält med så mycket precision som möjligt. Felaktiga data för kontakt information kan leda till att domänen inte kan köpas.

1. När du är färdig klickar du på **Nästa: Avancerat**.

### <a name="advanced-tab"></a>Fliken Avancerat

1. Konfigurera de valfria inställningarna på fliken **Avancerat** :  

   | Inställning  | Beskrivning |
   | -------- | ----------- |
   | **Automatisk förnyelse** | Aktiverat som standard. Din App Service domän har registrerats till dig vid ett års öknings steg. Automatisk förnyelse säkerställer att domän registreringen inte upphör att gälla och att du behåller domänens ägarskap. Din Azure-prenumeration debiteras automatiskt den årliga domän registrerings avgiften vid tidpunkten för förnyelsen. Välj Inaktivera om du vill välja **inaktivera**. Om automatisk förnyelse har inaktiverats kan du [förnya det manuellt](#renew-the-domain). |
   | **Sekretesskydd** | Aktiverat som standard. Sekretess skydd döljer din domän registrerings kontakt information från WHOIS-databasen. Sekretess skydd ingår redan i den årliga domän registrerings avgiften. Välj Inaktivera om du vill välja **inaktivera**. |

2. När du är färdig klickar du på **Nästa: Taggar**.

### <a name="finish"></a>Slutför

1. På fliken **taggar** anger du de taggar som du vill använda för din app service-domän. Taggning krävs inte för att använda App Service domäner, men är en [funktion i Azure som hjälper dig att hantera dina resurser](../azure-resource-manager/management/tag-resources.md).

1. Klicka på **Nästa: granska + skapa**.

1. På fliken **Granska + skapa** granskar du din domän ordning. Klicka på **Skapa** när du är klar.

    > [!NOTE]
    > App Service domäner använder GoDaddy för domän registrering och Azure DNS som värd för domänerna. Utöver den årliga domän registrerings avgiften gäller användnings kostnader för Azure DNS. Mer information finns i [Azure DNS prissättning](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. När domän registreringen är klar visas knappen **gå till resurs** . Välj den för att se hanterings sidan.

    ![App Service domän har skapats. Gå till resurs](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Nu är du redo att tilldela en App Service-app till den här anpassade domänen.

## <a name="prepare-the-app"></a>Förbereda appen

Om du vill mappa ett anpassat DNS-namn till en webbapp måste webbappens [App Service plan](https://azure.microsoft.com/pricing/details/app-service/) vara en betald nivå (delad, Basic, standard, Premium eller konsumtion för Azure Functions). I det här steget ser du till att App Service-appen har en prisnivå som stöds.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigera till appen i Azure Portal

1. Sök efter och välj **app Services** i det övre Sök fältet.

    ![Sök efter App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Välj namnet på appen.

    ![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Du ser hanteringssidan för App Service-appen.  

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

1. I det vänstra navigeringsfältet på appsidan bläddrar du till avsnittet **Inställningar** och väljer **Skala upp (App Service-plan)**.

    ![Skala upp-menyn](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen inte är på nivån **F1**. Anpassad DNS stöds inte på nivån **F1**. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Skärm bild av den vänstra navigerings menyn på sidan app med skala upp (App Service plan) vald.":::

1. Om App Service plan inte finns på **F1** -nivån stänger du sidan **skala upp** och fortsätter med att [köpa domänen](#buy-an-app-service-domain).

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

1. Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

1. Klicka på **Applicera**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Skärm bild av pris nivåerna för anpassade domäner i produktions kategorin med fliken produktion, B1 plan och knappen Använd markerat.":::

    När du ser följande meddelande har skalningsåtgärden slutförts.

    ![Bekräftelse av skalningsåtgärd](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Mappa App Service domän till din app

Det är enkelt att mappa ett värdnamn i din App Service-domän till en App Service-app, så länge den är i samma prenumeration. Du mappar App Service domän eller någon av dess under domän direkt i din app, och Azure skapar nödvändiga DNS-poster åt dig.

> [!NOTE]
> Om domänen och appen finns i olika prenumerationer mappar du App Service-domänen till appen precis som att [Mappa en externt köpt domän](app-service-web-tutorial-custom-domain.md). I det här fallet är Azure DNS den externa domän leverantören och du måste [lägga till nödvändiga DNS-poster manuellt](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Mappa domänen

1. I det vänstra navigerings fönstret på App-sidan, bläddrar du till avsnittet **Inställningar** och väljer **anpassade domäner**.

    ![Skärm bild som visar menyn anpassade domäner.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Välj **Lägg till anpassad domän**.

    ![Skärm bild som visar objektet Lägg till värddator namn.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Skriv App Service domän (till exempel **contoso.com**) eller en under domän (till exempel **www.contoso.com**) och klicka på **Verifiera**.

    > [!NOTE]
    > Om du har skapat ett stavfel i App Service domän namnet visas ett verifierings fel längst ned på sidan för att se att du saknar några DNS-poster. Du behöver inte lägga till dessa poster manuellt för en App Service domän. Se bara till att du anger domän namnet korrekt och klicka på **Verifiera** igen.
    >
    > ![Skärm bild som visar ett verifierings fel.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Godkänn **post typen hostname** och klicka på **Lägg till anpassad domän**.

    ![Skärm bild som visar knappen Lägg till anpassad domän.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Uppdatera webbläsaren för att uppdatera data.

    ![Skärm bild som visar hur du lägger till CNAME-posten.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > En **osäker etikett för** din anpassade domän innebär att den inte har bundits till ett TLS/SSL-certifikat än. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se [skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Testa den anpassade domänen

Om du vill testa den anpassade domänen navigerar du till den i webbläsaren.

## <a name="renew-the-domain"></a>Förnya domänen

Den App Service domän som du köpte är giltig i ett år från tidpunkten för köpet. Som standard är domänen konfigurerad att förnya automatiskt genom att debitera din betalnings metod för nästa år. Du kan förnya ditt domän namn manuellt.

Om du vill inaktivera automatisk förnyelse, eller om du vill förnya din domän manuellt, följer du stegen här.

1. I Sök fältet söker du efter och väljer **App Service domäner**.

    ![Portal navigering till Azure App Service domäner](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. I avsnittet **App Service domäner** väljer du den domän som du vill konfigurera.

1. Välj **domän förnyelse** i den vänstra navigeringen i domänen. Välj **av** om du vill sluta förnya din domän automatiskt. Inställningen utförs direkt.

    ![Skärm bild som visar alternativet att automatiskt förnya din domän.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > När du navigerar bort från sidan ignoreras meddelandet "dina osparade ändringar kommer att ignoreras" genom att klicka på **OK**.
    >

Om du vill förnya din domän manuellt väljer du **förnya domän**. Den här knappen är dock inte aktiv förrän [90 dagar innan domänen upphör att gälla](#when-domain-expires).

Om din domän förnyelse lyckas får du ett e-postmeddelande inom 24 timmar.

## <a name="when-domain-expires"></a>När domänen upphör att gälla

Azure följer förfaller eller förfaller App Service domäner på följande sätt:

* Om automatisk förnyelse är inaktive rad: 90 dagar innan domänen upphör skickas ett e-postmeddelande med en förnyad avisering till dig och knappen **förnya domän** aktive ras i portalen.
* Om automatisk förnyelse har Aktiver ATS: efter det att din domäns giltighets tid har gått ut försöker Azure debitera dig för förnyelsen av domän namn.
* Om ett fel inträffar under den automatiska förnyelsen (till exempel om ditt kort på filen har upphört att gälla), eller om automatisk förnyelse har inaktiverats och du tillåter att domänen upphör att gälla, meddelar Azure dig om domänen upphör att gälla och parkerar ditt domän namn. Du kan [förnya](#renew-the-domain) din domän manuellt.
* På dygnet runt och tolfte dagar efter förfallo datum skickar Azure ytterligare e-postmeddelanden. Du kan [förnya](#renew-the-domain) din domän manuellt. På den femte dagen efter förfallo datum stoppas DNS-matchningen för den utgångna domänen.
* På den 19: a dagen efter förfallo datum förblir din domän stoppad men blir föremål för en avgift för inlösen. Du kan ringa kund support för att förnya ditt domän namn, beroende på eventuella tillämpliga förnyelse-och inlösen avgifter.
* På den 25: a dagen efter förfallo datum placerar Azure din domän för auktion med en auktions tjänst för domän namns branschen. Du kan ringa kund support för att förnya ditt domän namn, beroende på eventuella tillämpliga förnyelse-och inlösen avgifter.
* Den 30: e dagen efter det att du har gått ut kan du inte längre lösa in din domän.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Hantera anpassade DNS-poster

I Azure hanteras DNS-poster för en App Service domän med hjälp av [Azure DNS](https://azure.microsoft.com/services/dns/). Du kan lägga till, ta bort och uppdatera DNS-poster, precis som för en externt köpt domän. Så här hanterar du anpassade DNS-poster:

1. I Sök fältet söker du efter och väljer **App Service domäner**.

    ![Portal navigering till Azure App Service domäner](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. I avsnittet **App Service domäner** väljer du den domän som du vill konfigurera.

1. På sidan **Översikt** väljer du **hantera DNS-poster**.

    ![Skärm bild som visar var du kan få åtkomst till DNS-posterna.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Information om hur du redigerar DNS-poster finns i [hantera DNS-zoner i Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Avbryt köp (ta bort domän)

När du har köpt App Service-domänen har du fem dagar på dig att avbryta köpet för en fullständig åter betalning. Efter fem dagar kan du ta bort den App Service-domänen, men får ingen åter betalning.

1. I Sök fältet söker du efter och väljer **App Service domäner**.

    ![Portal navigering till Azure App Service domäner](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. I avsnittet **App Service domäner** väljer du den domän som du vill konfigurera.

1. I domänens vänstra navigering väljer du **hostname-bindningar**. Värd namns bindningarna från alla Azure-tjänster visas här.

    ![Skärm bild som visar sidan hostname-bindningar.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Ta bort varje hostname-bindning genom att välja **...**  >  **Ta bort**. När alla bindningar har tagits bort väljer du **Spara**.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. I domänens vänstra navigering väljer du **Översikt**. 

1. Om uppsägnings perioden på den köpta domänen inte har förflutit väljer du **Avbryt köp**. Annars visas knappen **ta bort** i stället. Om du vill ta bort domänen utan åter betalning väljer du **ta bort**.

    ![Skärm bild som visar var du kan ta bort eller avbryta en köpt domän.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Bekräfta åtgärden genom att välja **Ja**.

    När åtgärden har slutförts släpps domänen från din prenumeration och är tillgänglig för alla som köper igen. 

## <a name="direct-default-url-to-a-custom-directory"></a>Dirigera standard-URL:en till en anpassad katalog

Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. För att dirigera dem till en under katalog, t. ex `public` ., se [omdirigera till en anpassad katalog](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du binder ett anpassat TLS/SSL-certifikat till App Service.

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en TLS-bindning i Azure App Service](configure-ssl-bindings.md)
