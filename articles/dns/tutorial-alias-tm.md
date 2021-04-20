---
title: 'Självstudie: Skapa en aliaspost för att stödja namn på toppdomäner – Traffic Manager'
titleSuffix: Azure DNS
description: Den här självstudien visar hur du konfigurerar en Azure DNS-aliaspost för att stödja användning av ditt apex-domännamn med Traffic Manager.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: e0101133c68142845a8ada50d9921d341cf10ad0
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738800"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Självstudie: Konfigurera en aliaspost för att hantera apex-domännamn med Traffic Manager 

Du kan skapa en aliaspost för ditt domännamnsapex för att referera till en Azure Traffic Manager-profil. Ett exempel kan vara contoso.com. I stället för att använda en omdirigeringstjänst konfigurerar du Azure DNS till att referera till en Traffic Manager-profil direkt från din zon. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en virtuell värddator och nätverksinfrastruktur.
> * Skapa en Traffic Manager-profil.
> * Skapa en aliaspost.
> * Testa aliasposten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

Anvisningar om att hantera din domän i Azure DNS finns i [Självstudie: Hantera din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

Den exempeldomän som används i den här självstudien är contoso.com, men du använder ditt eget domännamn.

## <a name="create-the-network-infrastructure"></a>Skapa nätverksinfrastrukturen

Skapa först ett virtuellt nätverk och ett undernät för att placera dina webbservrar i.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Längst upp till vänster i portalen väljer du **Skapa en resurs**. Ange *resursgrupp* i sökrutan och skapa en resursgrupp med namnet **RG-DNS-Alias-TM**.
3. Välj **Skapa en resurs**  >    >  **Virtuellt nätverk för nätverk.**
4. Skapa ett virtuellt nätverk med namnet **VNet-Servers**. Placera det i resursgruppen **RG-DNS-Alias-TM** och ge undernätet namnet **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Skapa två virtuella webbserverdatorer

1. Välj **Skapa en resurs för** den virtuella Windows Server  >  **2016-datorn.**
2. Ange **Web-01** som namn och placera den virtuella datorn i resursgruppen **RG-DNS-Alias-TM**. Ange ett användarnamn och ett lösenord, och välj **OK**.
3. För **Storlek** väljer du en SKU med 8 GB RAM.
4. Som **Inställningar** väljer du det virtuella nätverket **VNet-Servers** och undernätet **SN-Web**.
5. Välj **Offentlig IP-adress**. Under **Tilldelning** väljer du **Statisk** och sedan **OK**.
6. För offentliga inkommande portar väljer du **HTTP (80)**  >  **HTTPS (443)**  >  **RDP (3389)** och sedan **OK.**
7. På sidan **Sammanfattning** väljer du **Skapa**. Den här proceduren tar några minuter att slutföra.

Upprepa proceduren för att en annan virtuell dator med namnet **Web-02**.

### <a name="add-a-dns-label"></a>Ange en DNS-etikett

De offentliga IP-adresserna behöver en DNS-etikett för att fungera med Traffic Manager.
1. I resursgruppen **RG-DNS-Alias-TM** väljer du den offentliga IP-adressen **Web-01-ip**.
2. Under **Inställningar** väljer du **Konfiguration**.
3. I textrutan för DNS-namnetikett anger du **web01pip**.
4. Välj **Spara**.

Upprepa den här proceduren för den offentliga IP-adressen **Web-02-ip** med **web02pip** som DNS-namnetikett.

### <a name="install-iis"></a>Installera IIS

Installera IIS på både **Web-01** och **Web-02**.

1. Anslut till **Web-01** och logga in.
2. På instrumentpanelen **Serverhanteraren** väljer du **Lägg till roller och funktioner**.
3. Välj **Nästa** tre gånger. Välj **Webbserver (IIS)** på sidan **Serverroller**.
4. Välj **Lägg till funktioner** och välj **Nästa**.
5. Välj **Nästa** fyra gånger. Välj sedan **Installera**. Den här proceduren tar några minuter att slutföra.
6. När installationen är klar väljer du **Stäng**.
7. Öppna en webbläsare. Gå till **localhost** för att kontrollera att IIS-standardwebbsidan visas.

Upprepa den här proceduren för att installera IIS på **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. Öppna resursgruppen **RG-DNS-Alias-TM** och välj den offentliga IP-adressen **Web-01-ip**. Anteckna IP-adressen för senare användning. Upprepa det här steget för den offentliga IP-adressen **Web-02-ip**.
1. Välj **Skapa en resurs**  >    >  **nätverksprofil Traffic Manager .**
2. För namnet anger du **TM-alias-test**. Placera det i resursgruppen **RG-DNS-Alias-TM**.
3. Välj **Skapa**.
4. När distributionen är klar väljer du **Gå till resurs**.
5. På Traffic Manager-profilsidan går du till **Inställningar** och väljer **Slutpunkter**.
6. Välj **Lägg till**.
7. Som **Typ** väljer du **Extern slutpunkt**, och för **Namn** anger du **EP-Web01**.
8. I textrutan **Fullständigt kvalificerat domännamn (FQDN) eller IP** anger du den IP-adress för **Web-01-IP** som du antecknade tidigare.
9. Välj samma **Plats** som dina andra resurser och välj sedan **OK**.

Upprepa den här proceduren för att lägga till slutpunkten **Web-02** med hjälp av den IP-adress som du antecknade tidigare för **Web-02-ip**.

## <a name="create-an-alias-record"></a>Skapa en aliaspost

Skapa en aliaspost som pekar på Traffic Manager-profilen.

1. Välj din Azure DNS-zon för att öppna zonen.
2. Välj **Postuppsättning**.
3. Lämna textrutan **Namn** tom för att representera domännamnsapex. Ett exempel kan vara contoso.com.
4. Lämna **Typ** som en **A**-post.
5. Välj kryssrutan **Aliaspostuppsättning**.
6. Välj **Välj Azure-tjänst** och sedan Traffic Manager-profilen **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testa aliasposten

1. Från en webbläsare går du till ditt domännamnsapex. Ett exempel kan vara contoso.com. Du ser då IIS-standardwebbsidan. Stäng webbläsaren.
2. Stäng av den virtuella datorn **Web-01**. Vänta några minuter tills den stängs av helt.
3. Öppna ett nytt webbläsarfönster och gå till ditt domännamnsapex igen.
4. Du ser återigen IIS-standardsidan, eftersom Traffic Manager hanterade situationen och dirigerade trafiken till **Web-02**.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som skapades för den här självstudien tar du bort resursgruppen **RG-DNS-Alias-TM**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en aliaspost för att använda ditt domännamnsapex för att referera till en Traffic Manager-profil. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Värdbasera belastningsutjämnade webbappar vid basdomänen](./dns-alias-appservice.md)
