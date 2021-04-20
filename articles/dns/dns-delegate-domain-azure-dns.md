---
title: 'Självstudie: Vara värd för din domän och underdomän – Azure DNS'
description: I den här självstudien får du lära dig hur du konfigurerar Azure DNS att vara värd för dina DNS-zoner.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: c9c0568eb4d8a7403fc29f34a4c4e9f6e0fadecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738872"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Självstudie: Använda Azure DNS som värd för din domän

Du kan använda Azure DNS för att vara värd för din DNS-domän och hantera dina DNS-poster. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

Anta att du köper domänen `contoso.net` från en domännamnsregistrator och sedan skapar en zon med namnet `contoso.net` i Azure DNS. Eftersom du är ägare till domänen erbjuder registratorn alternativet att konfigurera namnserverposter (NS) för din domän. Registratorn lagrar NS-posterna i den överordnade .NET-zonen. Internetanvändare över hela världen omdirigeras sedan till din Azure DNS-zon när de försöker matcha DNS-poster i contoso.net.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en DNS-zon.
> * Hämta en lista över namnservrar.
> * Delegera domänen.
> * Kontrollera att delegeringen fungerar.


Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett domännamn som du kan testa med och som du kan använda i Azure DNS. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

I det här exemplet refererar vi till den överordnade domänen en `contoso.net` .

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Gå till den [Azure Portal för](https://portal.azure.com/) att skapa en DNS-zon. Sök efter och välj **DNS-zoner**.

   ![DNS-zon](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Välj **Skapa DNS-zon.**

1. På sidan **Skapa DNS-zon** anger du följande värden och väljer sedan **Skapa.** Till exempel `contoso.net`.

   > [!NOTE] 
   > Om den nya zonen som du skapar är en underordnad zon (t.ex. överordnad zon = underordnad zon = ), kan du gå till självstudien Skapa en ny `contoso.net` `child.contoso.net` underordnad [DNS-zon](./tutorial-public-dns-zones-child.md)

    | **Inställning** | **Värde** | **Information** |
    |--|--|--|
    | **Resursgrupp**    | ContosoRG | Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration som du har valt. Platsen för resursgruppen har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte. |
    | **Underordnad zon**        | lämna avmarkerat | Eftersom den här zonen **inte är** en [underordnad zon](./tutorial-public-dns-zones-child.md) bör du lämna den avmarkerad |
    | **Namn**              | `contoso.net` | Fält för det överordnade zonnamnet      |
    | **Plats**          | East US | Det här fältet baseras på den plats som valts när resursgruppen skapas  |
    

## <a name="retrieve-name-servers"></a>Hämta namnservrar

Innan du kan delegera din DNS-zon till Azure DNS måste du känna till namnservrarna för din zon. Azure DNS namnservrar från en pool varje gång en zon skapas.

1. I Azure Portal klickar du på **Alla resurser** i rutan **Favoriter** för den DNS-zon du skapade. På sidan **Alla resurser** väljer du din DNS-zon. Om den prenumeration som du har valt redan har flera resurser kan du ange ditt domännamn i rutan **Filtrera** efter namn för att enkelt komma åt programgatewayen. 

1. Hämta namnservrarna på sidan DNS-zon. I det här exemplet har `contoso.net` zonen tilldelats namnservrarna `ns1-01.azure-dns.com` , , * och `ns2-01.azure-dns.net` `ns3-01.azure-dns.org` `ns4-01.azure-dns.info` :

   ![Lista över namnservrar](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS skapar automatiskt auktoritativa NS-poster i din zon för de tilldelade namnservrarna.

## <a name="delegate-the-domain"></a>Delegera domänen

När DNS-zonen har skapats och du har namnservrarna måste du uppdatera den överordnade domänen med de Azure DNS namnservrarna. Varje registrator har sina egna DNS-hanteringsverktyg för att ändra namnserverposterna för en domän. 

1. På registratorns DNS-hanteringssida redigerar du NS-posterna och ersätter NS-posterna med Azure DNS-namnservrarna.

1. När du delegerar en domän till Azure DNS måste du använda namnservernamnen som tillhandahålls av Azure DNS. Använd alla fyra namnservernamn, oavsett vilket namn din domän har. Domändelegering kräver inte att namnservern använder samma toppnivådomän som din domän.

> [!NOTE]
> När du kopierar varje namnserveradress, bör du kontrollera att du kopierar den efterföljande punkten i slutet av adressen. En efterföljande punkt anger slutet av ett fullständigt domännamn. Vissa registratorer lägger till punkten om NS-namnet inte har någon i slutet. För att vara kompatibel med DNS-RFC ska du ta med avslutande punkt.

Delegering med hjälp av namnservrar i din egen zon, även kallat *vanity name servers*, stöds inte i Azure DNS.

## <a name="verify-the-delegation"></a>Kontrollera delegeringen

När du har slutfört delegeringen kan du kontrollera att den fungerar med hjälp av ett verktyg som till exempel *nslookup* för att köra en fråga mot SOA-posten (Start of Authority) för din zon. SOA-posten skapas automatiskt när zonen har skapats. Du kan behöva vänta minst 10 minuter efter att du har slutfört delegeringen innan du kan kontrollera att den fungerar. Det kan ta en stund innan ändringarna sprids genom DNS-systemet.

Du måste inte ange Azure DNS-namnservrarna. Om delegeringen är korrekt konfigurerad hittar den normala DNS-matchningsprocessen namnservrarna automatiskt.

1. Från en kommandotolk anger du ett nslookup-kommando som liknar följande exempel:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Kontrollera att ditt svar liknar följande nslookup:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla resursgruppen **contosoRG** om du planerar att genomföra nästa självstudie. Annars tar du bort resursgrupp **contosoRG** för att ta bort de resurser som skapades i den här självstudien.

Markera resursgruppen **contosoRG** och välj sedan **Ta bort resursgrupp**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en DNS-zon för din domän och delegerat den till Azure DNS. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
