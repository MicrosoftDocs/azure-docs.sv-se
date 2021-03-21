---
title: Skapa Datadog – Azure-partner lösningar
description: Den här artikeln beskriver hur du använder Azure Portal för att skapa en instans av Datadog.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748893"
---
# <a name="quickstart-get-started-with-datadog"></a>Snabb start: kom igång med Datadog

I den här snabb starten skapar du en instans av Datadog. Du kan antingen skapa en ny Datadog-organisation eller länka till en befintlig Datadog-organisation.

## <a name="pre-requisites"></a>Förutsättningar

Om du vill konfigurera Azure Datadog-integrering måste du ha **ägar** åtkomst till Azure-prenumerationen. Se till att du har rätt åtkomst innan du startar installationen.

Om du vill använda funktionen Security Assertion Markup Language (SAML) Single Sign-On (SSO) i Datadog-resursen måste du konfigurera ett företags program. Om du vill lägga till ett företags program behöver du någon av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.

Använd följande steg för att konfigurera företags programmet:

1. Gå till [Azure Portal](https://portal.azure.com). Välj **Azure Active Directory**.
1. I den vänstra rutan väljer du **Företagsprogram**.
1. Välj **nytt program**.
1. I **Lägg till från galleriet söker du** efter *Datadog*. Välj Sök resultatet och välj sedan **Lägg till**.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="Datadog-programmet i AAD Enterprise-galleriet." border="true":::

1. När appen har skapats går du till egenskaper på sido panelen. Ange **användar tilldelning krävs?** till **Nej** och välj **Spara**.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Ange egenskaper för Datadog-programmet" border="true":::

1. Gå till **enkel inloggning** på sido panelen. Välj sedan **SAML**.

   :::image type="content" source="media/create/saml-sso.png" alt-text="SAML-autentisering." border="true":::

1. Välj **Ja** när du uppmanas att **Spara inställningar för enkel inloggning**.

   :::image type="content" source="media/create/save-sso.png" alt-text="Spara enkel inloggning för Datadog-appen" border="true":::

1. Installationen av enkel inloggning är nu klar.

## <a name="find-offer"></a>Sök erbjudande

Använd Azure Portal för att hitta Datadog.

1. Gå till [Azure Portal](https://portal.azure.com/) och logga in.

1. Om du har besökt **Marketplace** i en senaste session väljer du ikonen från de tillgängliga alternativen. Annars söker du efter _Marketplace_.

    :::image type="content" source="media/create/marketplace.png" alt-text="Marketplace-ikon.":::

1. I Marketplace söker du efter **Datadog**.

1. På sidan plan översikt väljer du **Konfigurera + prenumerera**.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Datadog-program på Azure Marketplace.":::

## <a name="create-a-datadog-resource-in-azure"></a>Skapa en Datadog-resurs i Azure

Portalen visar ett formulär för att skapa Datadog-resursen.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Skapa Datadog-resurs" border="true":::

Ange följande värden.

|Egenskap | Beskrivning
|:-----------|:-------- |
| Prenumeration | Välj den Azure-prenumeration som du vill använda för att skapa Datadog-resursen. Du måste ha ägar åtkomst. |
| Resursgrupp | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En [resursgrupp](../../azure-resource-manager/management/overview.md#resource-groups) är en container som innehåller relaterade resurser för en Azure-lösning. |
| Resursnamn | Ange ett namn för Datadog-resursen. Det här namnet är namnet på den nya Datadog-organisationen när du skapar en ny Datadog-organisation. |
| Location | Välj USA, västra 2. Västra USA 2 är för närvarande den enda region som stöds. |
| Datadog-organisation | Välj **nytt** om du vill skapa en ny Datadog-organisation. Om du vill länka till en befintlig Datadog-organisation väljer du **befintlig**. |
| Pris plan | När du skapar en ny organisation väljer du i listan över tillgängliga Datadog-planer. |
| Fakturerings period | Hyres. |

Om du länkar till en befintlig Datadog-organisation, se nästa avsnitt. Annars väljer du **Nästa: mått och loggar** och hoppar över nästa avsnitt.

## <a name="link-to-existing-datadog-organization"></a>Länk till befintlig Datadog-organisation

Du kan länka din nya Datadog-resurs i Azure till en befintlig Datadog-organisation.

Välj **befintlig** för data organisation och välj sedan **länk till Datadog org**.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Länk till befintlig Datadog-organisation." border="true":::

Länken öppnar ett Datadog Authentication-fönster. Logga in på Datadog.

Som standard länkar Azure din aktuella Datadog-organisation till din Datadog-resurs. Om du vill länka till en annan organisation väljer du lämplig organisation i fönstret autentisering, som du ser nedan.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Välj lämplig Datadog-organisation som ska länkas" border="true":::

## <a name="configure-metrics-and-logs"></a>Konfigurera mått och loggar

Använd Azure Resource Tags för att konfigurera vilka mått och loggar som skickas till Datadog. Du kan inkludera eller exkludera mått och loggar för vissa resurser.

Tag-regler för att skicka **mått** är:

- Som standard samlas måtten in för alla resurser, förutom virtuella datorer, skalnings uppsättningar för virtuella datorer och App Service-planer.
- Virtuella datorer, skalnings uppsättningar för virtuella datorer och App Service-planer med *include* -Taggar skicka mått till Datadog.
- Virtuella datorer, skalnings uppsättningar för virtuella datorer och App Service-planer med *utelämna* Taggar skickar inte mått till Datadog.
- Om det uppstår en konflikt mellan inkluderings-och exkluderings regler, prioriteras uteslutningen

Tag-regler för att skicka **loggar** är:

- Som standard samlas loggar in för alla resurser.
- Azure-resurser med *include* -Taggar skicka loggar till Datadog.
- Azure-resurser med  *exclude* -Taggar skickar inte loggar till Datadog.
- Om det uppstår en konflikt mellan inkluderings-och exkluderings regler, prioriteras undantag.

Skärm bilden nedan visar t. ex. en tag-regel där endast de virtuella datorerna, skalnings uppsättningar för virtuella datorer och App Service-planer som taggats som *Datadog = True* skicka mått till Datadog.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Konfigurera loggar och mått." border="true":::

Det finns två typer av loggar som kan genereras från Azure till Datadog.

1. **Loggar på prenumerations nivå** – ger inblick i driften av dina resurser på [kontroll planet](../../azure-resource-manager/management/control-plane-and-data-plane.md). Uppdateringar av service Health-händelser ingår också. Använd aktivitets loggen för att fastställa vad, vem och när för Skriv åtgärder (skicka, skicka och ta bort). Det finns en enda aktivitets logg för varje Azure-prenumeration.

1. **Azure-resurs loggar** – ger inblick i åtgärder som vidtagits på en Azure-resurs på [data planet](../../azure-resource-manager/management/control-plane-and-data-plane.md). Att hämta en hemlighet från en Key Vault är till exempel en data Plans åtgärd. Eller så är det också en data Plans åtgärd att göra en begäran till en databas. Innehållet i resurs loggar varierar beroende på Azure-tjänsten och resurs typen.

Om du vill skicka prenumerations nivå loggar till Datadog väljer du **Skicka prenumerations aktivitets loggar**. Om det här alternativet lämnas omarkerat skickas inga prenumerations nivå loggar till Datadog.

Om du vill skicka Azure-Datadog till väljer du **Skicka Azures resurs loggar för alla definierade resurser**. Typerna av Azure-resurs loggar visas i [Azure Monitor resurs logg kategorier](../../azure-monitor/essentials/resource-logs-categories.md).  Om du vill filtrera uppsättningen Azure-resurser som skickar loggar till Datadog, använder du Azure Resource-taggar.  

Loggarna som skickas till Datadog kommer att debiteras av Azure. Mer information finns i [prissättningen på plattforms loggar](https://azure.microsoft.com/pricing/details/monitor/) som skickas till Azure Marketplace-partner.

När du har slutfört konfigurationen av mått och loggar väljer du **Nästa: enkel inloggning**.

## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning

Om din organisation använder Azure Active Directory som identitets leverantör kan du upprätta enkel inloggning från Azure Portal till Datadog. Om din organisation använder en annan identitets leverantör eller om du inte vill upprätta enkel inloggning just nu kan du hoppa över det här avsnittet.

Om du länkar Datadog-resursen till en befintlig Datadog-organisation kan du inte konfigurera enkel inloggning i det här steget. I stället ställer du in enkel inloggning när du har skapat Datadog-resursen. Mer information finns i [Konfigurera om enkel inloggning](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/create/linking-sso.png" alt-text="Enkel inloggning för att länka till befintlig Datadog-organisation." border="true":::

Om du vill upprätta enkel inloggning via Azure Active Directory markerar du kryss rutan för **aktivera enkel inloggning via Azure Active Directory**.

Azure Portal hämtar lämpligt Datadog-program från Azure Active Directory. Appen matchar den företags app som du angav i ett tidigare steg.

Välj Datadog-appens namn.

:::image type="content" source="media/create/sso.png" alt-text="Aktivera enkel inloggning till Datadog." border="true":::

Välj **Nästa: Taggar**.

## <a name="add-custom-tags"></a>Lägg till anpassade taggar

Du kan ange anpassade taggar för den nya Datadog-resursen. Ange namn-och värdepar för de taggar som ska användas för Datadog-resursen.

:::image type="content" source="media/create/tags.png" alt-text="Lägg till anpassade taggar för Datadog-resursen." border="true":::

När du är klar med att lägga till taggar väljer du **Nästa: granska + skapa**.

## <a name="review--create-datadog-resource"></a>Granska + skapa Datadog-resurs

Granska dina val och användnings villkoren. När verifieringen är klar väljer du **skapa**.

:::image type="content" source="media/create/review-create.png" alt-text="Granska och skapa Datadog-resurs." border="true":::

Azure distribuerar Datadog-resursen.

När processen har slutförts väljer du **gå till resurs** för att se Datadog-resursen.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Datadog resurs distribution." border="true":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera Datadog-resursen](manage.md)
