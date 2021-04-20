---
title: 'Självstudie: Skapa en underordnad DNS-zon i Azure'
titleSuffix: Azure DNS
description: Självstudie om hur du skapar underordnade DNS-zoner i Azure Portal.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 04/19/2021
ms.author: jonbeck
ms.openlocfilehash: 283ff2786a0b63c6263c62a13e27cce92c2368dd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737396"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Självstudie: Skapa en ny underordnad DNS-zon

I den här guiden får du lära dig att: 

> [!div class="checklist"]
> * Logga in på Azure-portalen.
> * Skapa en underordnad DNS-zon via en ny DNS-zon.
> * Skapa en underordnad DNS-zon via en överordnad DNS-zon.
> * Verifiera NS-delegering för ny underordnad DNS-zon.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration.  Om du inte har något konto kan du [skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Befintlig överordnad Azure DNS zon.  

I den här självstudien använder vi contoso.com som överordnad zon och subdomain.contoso.com som underdomännamn.  Ersätt *contoso.com* med det överordnade domännamnet *och underdomänen* med din underordnade domän.  Om du inte har skapat den överordnade DNS-zonen kan du gå till stegen för att [skapa DNS-zon med Azure Portal](./dns-getstarted-portal.md#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.
Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar.

Det finns två sätt att skapa den underordnade DNS-zonen.
1.  Via portalsidan "Skapa DNS-zon".
1.  Via konfigurationssidan för den överordnade DNS-zonen.

## <a name="create-child-dns-zone-via-create-dns-zone"></a>Skapa underordnad DNS-zon via skapa DNS-zon

I det här steget skapar vi en ny underordnad DNS-zon med **namnet subdomain.contoso.com** och delegerar den till den befintliga överordnade DNS-zonen **contoso.com**. Du skapar DNS-zonen med hjälp av flikarna på sidan **Skapa DNS-zon.**
1.  Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Fönstret **Nytt** visas.
1.  Välj **Nätverk** och sedan **DNS-zon och** sedan knappen Lägg **till.**

1.  På **fliken Grundläggande** skriver eller väljer du följande värden:
    * **Prenumeration:** Välj en prenumeration som du vill skapa zonen i.
    * **Resursgrupp:** Ange din befintliga resursgrupp eller skapa en ny genom att välja **Skapa ny.** Ange *MyResourceGroup* och välj **OK.** Resursgruppens namn måste vara unikt inom Azure-prenumerationen.
    * Markera den här kryssrutan: **Den här zonen är underordnad en befintlig zon som redan finns i Azure DNS**
    * **Överordnad zonprenumeration:** I den här listrutan söker eller väljer du det prenumerationsnamn under vilket *den överordnade DNS-contoso.com* skapades.
    * **Överordnad zon:** I sökfältet skriver du *contoso.com* att läsa in den i listrutan. När du har *läst contoso.com* väljer du alternativet från listrutan.
    * **Namn:** Skriv *underdomän för det här* självstudieexempel. Observera att det överordnade *DNS-contoso.com* läggs automatiskt till som suffix som namn när vi väljer överordnad zon i steget ovan.

1. Välj **Nästa: Granska + skapa**.
1. På fliken **Granska + skapa** granskar du sammanfattningen, korrigerar eventuella verifieringsfel och väljer sedan **Skapa.**
Det kan ta några minuter att skapa zonen.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Skärmbild av sidan skapa DNS-zon." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Översiktssida för att skapa underordnad DNS-zon via överordnad DNS-zon
Du kan också skapa en ny underordnad DNS-zon och delegera den till den överordnade DNS-zonen med hjälp av knappen **Underordnad** zon från översiktssidan för den överordnade zonen. Med den här knappen fylls de överordnade parametrarna automatiskt i i förväg för den underordnade zonen. 

1.  I den Azure Portal under **Alla resurser** öppnar du *contoso.com* DNS-zon i **resursgruppen MyResourceGroup.** Du kan ange *contoso.com* i **rutan Filtrera efter namn** för att hitta den enklare.
1.  På översiktssidan för DNS-zonen väljer du **knappen +Underordnad** zon.

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Skärmbild av knappen underordnad zon." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Sidan skapa DNS-zon öppnas sedan. Alternativet Underordnad zon är redan markerat och överordnad zonprenumeration och överordnad zon fylls i åt dig på den här sidan.
1.  Skriv namnet som underordnad för *det här* självstudieexempel. Observera att du överordnade DNS-zonnamn contoso.com läggs automatiskt till som prefix till namn.
1.  Välj **Nästa: Taggar** och sedan **Nästa: Granska + skapa**.
1.  På fliken **Granska + skapa** granskar du sammanfattningen, korrigerar eventuella verifieringsfel och väljer sedan **Skapa.**

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Skärmbild av vald underordnad zon" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::

## <a name="verify-child-dns-zone"></a>Verifiera underordnad DNS-zon
Nu när du har en ny underordnad *DNS-zon subdomain.contoso.com* skapat. För att kontrollera att delegeringen har skett korrekt bör du kontrollera att nameserver(NS)-posterna för den underordnade zonen finns i den överordnade zonen enligt beskrivningen nedan.  

**Hämta namnservrar för den underordnade DNS-zonen:**

1.  I den Azure Portal under **Alla resurser** öppnar du *subdomain.contoso.com* DNS-zonen i **resursgruppen MyResourceGroup.** Du kan ange *subdomain.contoso.com* i **rutan Filtrera efter namn** för att hitta den enklare.
1.  Hämta namnservrarna från översiktssidan för DNS-zonen. I det här exemplet har zonen contoso.com tilldelats *namnservrar ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org* och *ns4-08.azure-dns.info*:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Skärmbild av underordnade zonnamnservrar" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Kontrollera NS-posten i den överordnade DNS-zonen:**

I det här steget går vi nu till den överordnade *DNS-contoso.com* och kontrollerar att dess NS-postuppsättningspost för de underordnade zonnamnservrarna har skapats.

1. I den Azure Portal under **Alla resurser** öppnar du contoso.com DNS-zon i **resursgruppen MyResourceGroup.** Du kan ange contoso.com i **rutan Filtrera efter namn** för att hitta den enklare.
1.  På *översiktssidan contoso.com* DNS-zoner söker du efter postuppsättningarna.
1.  Du kommer att se att postuppsättningen av typen NS och namnunderdomänen redan har skapats i den överordnade DNS-zonen. Kontrollera värdena för den här postuppsättningen. Den liknar den namnserverlista som vi hämtade från den underordnade DNS-zonen i ovanstående steg.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Skärmbild av verifiering av namnservrar för underordnad zon" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Rensa resurser
När du inte längre behöver de resurser som du skapade i den här självstudien tar du bort dem genom att ta **bort resursgruppen MyResourceGroup.** Öppna **resursgruppen MyResourceGroup** och välj Ta **bort resursgrupp.**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Scenarier för Azure DNS Private Zones](private-dns-scenarios.md)
