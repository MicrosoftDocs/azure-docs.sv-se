---
title: 'Självstudie: värd för din domän och under domän – Azure DNS'
description: I den här självstudien får du lära dig hur du konfigurerar Azure DNS att vara värd för dina DNS-zoner.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: a8f64ab3141459142def12a1758b0fe0a94ca432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92282167"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Självstudie: Använda Azure DNS som värd för din domän

Du kan använda Azure DNS för att vara värd för din DNS-domän och hantera dina DNS-poster. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

Anta exempelvis att du köper domänen contoso.net från en domännamnsregistrator och sedan skapar en zon med namnet contoso.net i Azure DNS. Eftersom du är ägare till domänen erbjuder sig registratorn att konfigurera namnserverposterna (NS) för din domän. Registratorn lagrar NS-poster i den överordnade .NET-zonen. Internetanvändare över hela världen omdirigeras sedan till din Azure DNS-zon när de försöker matcha DNS-poster i contoso.net.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en DNS-zon.
> * Hämta en lista över namnservrar.
> * Delegera domänen.
> * Kontrollera att delegeringen fungerar.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett domän namn tillgängligt för att kunna testa med att du kan vara värd för Azure DNS. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

I det här exemplet kommer vi att referera till den överordnade domänen som **contoso.net**

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Gå till [Azure Portal](https://portal.azure.com/) för att skapa en DNS-zon. Sök efter och välj **DNS-zoner**.

   ![DNS-zon](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Välj **Skapa DNS-zon**.
1. På sidan **Skapa DNS-zon** anger du följande värden och väljer sedan **skapa**: till exempel **contoso.net**
      > [!NOTE] 
      > Om den nya zonen som du skapar är en underordnad zon (t. ex. överordnad zon = contoso.net underordnad zon = child.contoso.net), se vår [själv studie kurs om att skapa en ny underordnad DNS-zon](./tutorial-public-dns-zones-child.md)

    | **Inställning** | **Värde** | **Information** |
    |--|--|--|
    | **Projekt information:**  |  |  |
    | **Resursgrupp**    | ContosoRG | Skapa en resursgrupp. Resurs gruppens namn måste vara unikt inom den prenumeration som du har valt. Platsen för resurs gruppen har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid "global" och visas inte. |
    | **Instans information:** |  |  |
    | **Underordnad zon**        | lämna omarkerad | Eftersom den här zonen **inte** är en [underordnad zon](./tutorial-public-dns-zones-child.md) bör du lämna denna omarkerad |
    | **Namn**              | contoso.net | Fält för namn på överordnad zon      |
    | **Plats**          | East US | Det här fältet baseras på den plats som valts som del av resurs gruppen skapas  |
    

## <a name="retrieve-name-servers"></a>Hämta namnservrar

Innan du kan delegera din DNS-zon till Azure DNS måste du känna till namnservrarna för din zon. Azure DNS allokerar namnservrar från en pool varje gång en zon skapas.

1. I Azure Portal klickar du på **Alla resurser** i rutan **Favoriter** för den DNS-zon du skapade. På sidan **Alla resurser** väljer du din DNS-zon. Om den prenumeration som du valde redan har flera resurser kan du ange ditt domännamn i rutan **Filtrera efter namn** för att enkelt få åtkomst till programgatewayen. 

1. Hämta namnservrarna på sidan DNS-zon. I det här exemplet har zonen contoso.net tilldelats namnservrarna *ns1-01.Azure-DNS.com*, *NS2-01.Azure-DNS.net*, *NS3-01.Azure-DNS.org* och *NS4-01.Azure-DNS.info*:

   ![Lista över namnservrar](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS skapar automatiskt auktoritativa NS-poster i din zon för de tilldelade namnservrarna.

## <a name="delegate-the-domain"></a>Delegera domänen

Nu när du har namnservrar och DNS-zonen är skapad måste den överordnade domänen uppdateras med Azure DNS-namnservrarna. Varje registrator har sina egna DNS-hanteringsverktyg för att ändra namnserverposterna för en domän. 

1. På registratorns DNS-hanteringssida redigerar du NS-posterna och ersätter NS-posterna med Azure DNS-namnservrarna.

1. När du delegerar en domän till Azure DNS måste du använda namnservernamnen som tillhandahålls av Azure DNS. Använd alla fyra namnservernamn, oavsett vilket namn din domän har. Domändelegering kräver inte att namnservern använder samma toppnivådomän som din domän.

> [!NOTE]
> När du kopierar varje namnserveradress, bör du kontrollera att du kopierar den efterföljande punkten i slutet av adressen. En efterföljande punkt anger slutet av ett fullständigt domännamn. Vissa registratorer lägger till punkten om NS-namnet inte har någon i slutet. För att vara kompatibel med DNS-RFC ska du ta med avslutande punkt.

Delegering med hjälp av namnservrar i din egen zon, även kallat *vanity name servers*, stöds inte i Azure DNS.

## <a name="verify-the-delegation"></a>Kontrollera delegeringen

När du har slutfört delegeringen kan du kontrollera att den fungerar med hjälp av ett verktyg som till exempel *nslookup* för att köra en fråga mot SOA-posten (Start of Authority) för din zon. SOA-posten skapas automatiskt när zonen har skapats. Du kan behöva vänta i 10 minuter eller mer när du har slutfört delegeringen innan du kan bekräfta att den fungerar. Det kan ta en stund innan ändringarna sprids genom DNS-systemet.

Du måste inte ange Azure DNS-namnservrarna. Om delegeringen är korrekt konfigurerad hittar den normala DNS-matchningsprocessen namnservrarna automatiskt.

1. I en kommando tolk anger du ett nslookup-kommando som liknar följande exempel:

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

- Markera resursgruppen **contosoRG** och välj sedan **Ta bort resursgrupp**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en DNS-zon för din domän och delegerat den till Azure DNS. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
