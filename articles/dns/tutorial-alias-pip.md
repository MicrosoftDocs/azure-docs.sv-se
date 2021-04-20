---
title: 'Självstudie: Skapa en Azure DNS aliaspost för att referera till en offentlig IP-adress i Azure'
description: Den här självstudien visar hur du konfigurerar en Azure DNS-aliaspost för att referera till en offentlig IP-adress i Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: 28e37ad0b404b5275a224c8debab5c11c07948b4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738818"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Självstudie: Konfigurera en aliaspost för att referera till en offentlig IP-adress i Azure 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en nätverksinfrastruktur.
> * Skapa en virtuell webbserverdator med en offentlig IP-adress.
> * Skapa en aliaspost som pekar på den offentliga IP-adressen.
> * Testa aliasposten.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

Anvisningar för att vara värd för din domän i Azure DNS finns i [Självstudie: var värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

Den exempeldomän som används i den här självstudien är contoso.com, men du använder ditt eget domännamn.

## <a name="create-the-network-infrastructure"></a>Skapa nätverksinfrastrukturen
Skapa först ett virtuellt nätverk och ett undernät för att placera dina webbservrar i.
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Skapa en resurs** på den vänstra panelen i Azure Portal. Ange *resursgrupp* i sökrutan och skapa en resursgrupp med namnet **RG-DNS-Alias-pip**.
3. Välj **Skapa en resurs**  >    >  **Virtuellt nätverk för nätverk.**
4. Skapa ett virtuellt nätverk med namnet **VNet-Server**. Placera det i resursgruppen **RG-DNS-Alias-pip** och ge undernätet namnet **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Skapa en virtuell webbserverdator
1. Välj **Skapa en resurs för** den virtuella Windows Server  >  **2016-datorn.**
2. Ange **Web-01** som namn och placera den virtuella datorn i resursgruppen **RG-DNS-Alias-TM**. Ange ett användarnamn och ett lösenord, och välj **OK**.
3. För **Storlek** väljer du en SKU med 8 GB RAM.
4. Som **Inställningar** väljer du det virtuella nätverket **VNet-Servers** och undernätet **SN-Web**. För offentliga inkommande portar väljer du **HTTP (80)**  >  **HTTPS (443)**  >  **RDP (3389)** och sedan **OK.**
5. På sidan **Sammanfattning** väljer du **Skapa**.

Den här distributionen tar några minuter att slutföra. Den virtuella datorn har ett anslutet nätverkskort med en grundläggande dynamisk offentlig IP-adress som kallas Web-01-ip. Den offentliga IP-adressen ändras varje gång den virtuella datorn startas om.

### <a name="install-iis"></a>Installera IIS

Installera IIS på **Web-01**.

1. Anslut till **Web-01** och logga in.
2. På instrumentpanelen **Serverhanteraren** väljer du **Lägg till roller och funktioner**.
3. Välj **Nästa** tre gånger. Välj **Webbserver (IIS)** på sidan **Serverroller**.
4. Välj **Lägg till funktioner** och sedan **Nästa**.
5. Välj **Nästa** fyra gånger och sedan **Installera**. Den här proceduren tar några minuter att slutföra.
6. När installationen är klar väljer du **Stäng**.
7. Öppna en webbläsare. Gå till **localhost** för att kontrollera att IIS-standardwebbsidan visas.

## <a name="create-an-alias-record"></a>Skapa en aliaspost

Skapa en aliaspost som pekar på den offentliga IP-adressen.

1. Välj din Azure DNS-zon för att öppna zonen.
2. Välj **Postuppsättning**.
3. I textrutan **Namn** väljer du **web01**.
4. Lämna **Typ** som en **A**-post.
5. Välj kryssrutan **Aliaspostuppsättning**.
6. Välj **Välj Azure-tjänst** och välj sedan den offentliga IP-adressen **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testa aliasposten

1. I resursgruppen **RG-DNS-Alias-pip** väljer du den virtuella datorn **Web-01**. Anteckna den offentliga IP-adressen.
1. Från en webbläsare bläddrar du till det fullständigt kvalificerade domännamnet för den virtuella datorn Web01-01. Ett exempel är **web01.contoso.com**. Du ser nu IIS-standardwebbsidan.
2. Stäng webbläsaren.
3. Stoppa den virtuella datorn **Web-01** och starta sedan om den.
4. När den virtuella datorn startar om antecknar du den nya offentliga IP-adressen för den virtuella datorn.
5. Öppna en ny webbläsare. Bläddra åter till det fullständigt kvalificerade domännamnet för den virtuella datorn Web01-01. Ett exempel är **web01.contoso.com**.

Den här proceduren lyckas eftersom du använde en aliaspost för att peka mot den offentliga IP-adressresursen i stället för en standardmässig A-post.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som skapades för den här självstudien tar du bort resursgruppen **RG-DNS-Alias-pip**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du en aliaspost för att referera till en offentlig IP-adress i Azure. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
