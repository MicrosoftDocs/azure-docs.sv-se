---
title: 'Självstudie: skapa en Azure-underordnad DNS-zon'
titleSuffix: Azure DNS
description: Själv studie kurs om hur du skapar underordnade DNS-zoner i Azure Portal.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 1e2eddd821bb7a9d2050913efef3d73b406e32f7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733218"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Självstudie: skapa en ny underordnad DNS-zon

I den här guiden får du lära dig att: 

> [!div class="checklist"]
> * Logga in på Azure-portalen.
> * Skapa en underordnad DNS-zon via den nya DNS-zonen.
> * Skapar en underordnad DNS-zon via överordnad DNS-zon.
> * Verifierar NS-delegering för ny underordnad DNS-zon.



## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration.  Om du inte har något konto kan du [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Befintlig överordnad Azure DNS zon.  

I den här självstudien kommer vi att använda contoso.com som överordnad zon och subdomain.contoso.com som underordnat domän namn.  Ersätt *contoso.com* med ditt överordnade domän namn och under *domän* med din underordnade domän.  Om du inte har skapat din överordnade DNS-zon, se steg för att [skapa en DNS-zon med hjälp av Azure Portal](./dns-getstarted-portal.md#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.
Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar.

Det finns två sätt att skapa en underordnad DNS-zon.
1.  Via portal sidan Skapa DNS-zon.
1.  Via konfigurations sidan för den överordnade DNS-zonen.


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Skapa en underordnad DNS-zon via skapa DNS-zon

I det här steget ska vi skapa en ny underordnad DNS-zon med namnet **subdomain.contoso.com** och delegera den till befintlig överordnad DNS-zon **contoso.com**. Du skapar DNS-zonen med hjälp av flikarna på sidan **Skapa DNS-zon** .
1.  Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Fönstret **Nytt** visas.
1.  Välj **nätverk** och välj sedan **DNS-zon** och välj sedan **Lägg till** knapp.

1.  På fliken **grundläggande** anger eller väljer du följande värden:
    * **Prenumeration**: Välj en prenumeration för att skapa zonen i.
    * **Resurs grupp**: Ange din befintliga resurs grupp eller skapa en ny genom att välja **Skapa ny**, ange *MyResourceGroup* och välj **OK**. Resursgruppens namn måste vara unikt inom Azure-prenumerationen.
    * Markera den här kryss rutan: **zonen är underordnad en befintlig zon som redan finns i Azure DNS**
    * **Prenumeration på överordnad zon**: i den här List rutan söker du efter och/eller väljer det prenumerations namn som överordnad DNS-zon *contoso.com* skapades under.
    * **Överordnad zon**: i Sök fältet skriver du *contoso.com* för att läsa in den i list rutan. När du har läst in Select *contoso.com* från List rutan.
    * **Namn:** Skriv under *domän* för den här självstudien exempel. Observera att den överordnade DNS-zonens namn *contoso.com* automatiskt läggs till som suffix till namn när vi väljer överordnad zon i steget ovan.

1. Välj **Nästa: Granska + skapa**.
1. På fliken **Granska + skapa** granskar du sammanfattningen, korrigera eventuella verifierings fel och väljer sedan **skapa**.
Det kan ta några minuter att skapa zonen.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Skärm bild av sidan Skapa DNS-zon." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Sidan skapa underordnad DNS-zon via översikt över överordnad DNS-zon
Du kan också skapa en ny underordnad DNS-zon och delegera den till den överordnade DNS-zonen med hjälp av knappen för den **underordnade zonen** från översikts sidan för överordnad zon. Genom att använda den här knappen fylls de överordnade parametrarna automatiskt i för den underordnade zonen. 

1.  I Azure Portal, under **alla resurser**, öppnar du DNS-zonen *contoso.com* i resurs gruppen **MyResourceGroup** . Du kan ange *contoso.com* i rutan **Filtrera efter namn** för att hitta den enklare.
1.  På sidan Översikt över DNS-zon väljer du knappen **+ underordnad zon** .

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Knapp för underordnade skärm bilds zon." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Sidan Skapa DNS-zon öppnas sedan. Alternativet för underordnad zon är redan markerat och prenumerationen på den överordnade zonen och den överordnade zonen är redan ifylld för dig på den här sidan.
1.  Skriv namnet som *underordnat* exempel i den här kursen. Observera att du har överordnat DNS-zonens namn contoso.com automatiskt som prefix till namn.
1.  Välj **Nästa: Taggar** och **Nästa: granska + skapa**.
1.  På fliken **Granska + skapa** granskar du sammanfattningen, korrigera eventuella verifierings fel och väljer sedan **skapa**.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Skärm bild av markerad underordnad zon" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::

## <a name="verify-child-dns-zone"></a>Verifiera underordnad DNS-zon
Nu när du har skapat en ny underordnad DNS-zon *subdomain.contoso.com* . Om du vill kontrol lera att delegeringen har genomförts korrekt ska du kontrol lera namnserver (NS) för din underordnade zon i den överordnade zonen enligt beskrivningen nedan.  

**Hämta namnservrar för den underordnade DNS-zonen:**

1.  I Azure Portal, under **alla resurser**, öppnar du DNS-zonen *subdomain.contoso.com* i resurs gruppen **MyResourceGroup** . Du kan ange *subdomain.contoso.com* i rutan **Filtrera efter namn** för att hitta den enklare.
1.  Hämta namnservrarna från översikts sidan för DNS-zonen. I det här exemplet har zonen contoso.com tilldelats namnservrarna *ns1-08.Azure-DNS.com, ns2-08.Azure-DNS.net, NS3-08.Azure-DNS.org* och *NS4-08.Azure-DNS.info*:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Skärm bild av namnservrar för den underordnade zonen" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Verifiera NS-posten i den överordnade DNS-zonen:**

I det här steget går vi till den överordnade DNS-zonen *contoso.com* och kontrollerar att post uppsättningen för de underordnade zonerna namnservrar har skapats.

1. I Azure Portal, under **alla resurser**, öppnar du DNS-zonen contoso.com i resurs gruppen **MyResourceGroup** . Du kan ange contoso.com i rutan **Filtrera efter namn** för att hitta den enklare.
1.  På sidan Översikt över *contoso.com* DNS-zoner söker du efter post uppsättningarna.
1.  Du kommer att se att post uppsättning av typen NS och namn under domän redan har skapats i den överordnade DNS-zonen. Kontrol lera värdena för den här post uppsättningen den liknar listan namnserver som vi hämtade från den underordnade DNS-zonen i ovanstående steg.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Skärm bild av namnservrar-verifiering för underordnade zoner" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Rensa resurser
När du inte längre behöver de resurser som du skapade i den här självstudien tar du bort dem genom att ta bort resurs gruppen **MyResourceGroup** . Öppna resurs gruppen **MyResourceGroup** och välj **ta bort resurs grupp**.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Scenarier för Azure DNS Private Zones](private-dns-scenarios.md)