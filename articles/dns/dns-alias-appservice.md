---
title: Värd belastnings utjämning av Azure-Webbappar i zon Apex
description: Använd en Azure DNS Ali Aset för att vara värd för belastningsutjämnade webbappar i zonen Apex
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 72adb2732eb0832589cbc25fb7e4288eb1899214
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954519"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Värd belastnings utjämning av Azure-Webbappar i zon Apex

DNS-protokollet förhindrar tilldelning av något annat än en A-eller AAAA-post i zonens Apex. Ett exempel på en zons Apex är contoso.com. Den här begränsningen utgör ett problem för program ägare som har belastningsutjämnade program bakom Traffic Manager. Det går inte att peka på Traffic Manager profilen från zonens Apex-post. Därför måste program ägare använda en lösning. En omdirigering på program nivån måste omdirigeras från zonens Apex till en annan domän. Ett exempel är en omdirigering från contoso.com till www- \. contoso.com. Den här ordningen visar en enskild felpunkt för omdirigerings funktionen.

Det här problemet finns inte längre med Ali Aset poster. Nu kan program ägare peka sin zon Apex-post till en Traffic Manager profil som har externa slut punkter. Program ägare kan peka på samma Traffic Manager profil som används för alla andra domäner i DNS-zonen.

Till exempel kan contoso.com och www- \. contoso.com peka på samma Traffic Manager profil. Detta är fallet så länge den Traffic Manager profilen bara har konfigurerat externa slut punkter.

I den här artikeln får du lära dig hur du skapar en aliasresurspost för din domän Apex och konfigurerar Traffic Manager profil slut punkter för dina webb program.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

Anvisningar för att vara värd för din domän i Azure DNS finns i [Självstudie: var värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

Den exempeldomän som används i den här självstudien är contoso.com, men du använder ditt eget domännamn.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs grupp som innehåller alla resurser som används i den här artikeln.

## <a name="create-app-service-plans"></a>Skapa App Services planer

Skapa två Webb App Services planer i resurs gruppen med hjälp av följande tabell för konfigurations information. Mer information om hur du skapar en App Service plan finns i [hantera ett App Service plan i Azure](../app-service/app-service-plan-manage.md).


|Name  |Operativsystem  |Location  |Prisnivå  |
|---------|---------|---------|---------|
|ASP-01     |Windows|East US|D1-Shared för utveckling/testning|
|ASP-02     |Windows|Central US|D1-Shared för utveckling/testning|

## <a name="create-app-services"></a>Skapa App Services

Skapa två webb program, en i varje App Service plan.

1. I det övre vänstra hörnet på sidan Azure Portal väljer du **skapa en resurs**.
2. Skriv in **webbapp** i Sök fältet och tryck på RETUR.
3. Välj **webbapp**.
4. Välj **Skapa**.
5. Acceptera standardinställningarna och Använd följande tabell för att konfigurera de två webbapparna:

   |Name<br>(måste vara unikt inom. azurewebsites.net)|Resursgrupp |Körningsstack|Region|App Service plan/plats
   |---------|---------|-|-|-------|
   |App-01|Använd befintlig<br>Välj din resursgrupp|.NET Core 2.2|East US|ASP-01 (D1)|
   |App-02|Använd befintlig<br>Välj din resursgrupp|.NET Core 2.2|Central US|ASP-02 (D1)|

### <a name="gather-some-details"></a>Samla in viss information

Nu måste du anteckna IP-adressen och värd namnet för webbapparna.

1. Öppna resurs gruppen och välj din första webbapp (**app-01** i det här exemplet).
2. I den vänstra kolumnen väljer du **Egenskaper**.
3. Anteckna adressen under **URL** och under **utgående IP-adresser** noterar du den första IP-adressen i listan. Du kommer att använda den här informationen senare när du konfigurerar Traffic Manager slut punkter.
4. Upprepa för **app-02**.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil i din resurs grupp. Använd standardinställningarna och ange ett unikt namn i namn området trafficmanager.net.

Information om hur du skapar en Traffic Manager-profil finns i [snabb start: skapa en Traffic Manager profil för ett webb program med hög tillgänglighet](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Skapa slutpunkter

Nu kan du skapa slut punkter för de två webbapparna.

1. Öppna resurs gruppen och välj din Traffic Manager profil.
2. Välj **slut punkter** i den vänstra kolumnen.
3. Välj **Lägg till**.
4. Använd följande tabell för att konfigurera slut punkterna:

   |Typ  |Name  |Mål  |Location  |Anpassade huvud inställningar|
   |---------|---------|---------|---------|---------|
   |Extern slut punkt     |Slut – 01|IP-adress som du har spelat in för app-01|East US|värd\<the URL you recorded for App-01\><br>Exempel: **värd: app-01.azurewebsites.net**|
   |Extern slut punkt     |Slut punkt 02|IP-adress som du har spelat in för app-02|Central US|värd\<the URL you recorded for App-02\><br>Exempel: **värd: app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Skapa DNS-zon

Du kan antingen använda en befintlig DNS-zon för testning, eller så kan du skapa en ny zon. Information om hur du skapar och delegerar en ny DNS-zon i Azure finns i [Självstudier: värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Lägg till en TXT-post för anpassad domän verifiering

När du lägger till ett anpassat värdnamn i dina webbappar söker det efter en angiven TXT-post för att verifiera din domän.

1. Öppna resurs gruppen och välj DNS-zonen.
2. Välj **Postuppsättning**.
3. Lägg till post uppsättningen i följande tabell. För värdet använder du den faktiska webb program-URL som du tidigare har registrerat:

   |Namn  |Typ  |Värde|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Lägga till en anpassad domän

Lägg till en anpassad domän för båda webbapparna.

1. Öppna resurs gruppen och välj din första webbapp.
2. Välj **anpassade domäner** i den vänstra kolumnen.
3. Under **anpassade domäner** väljer du **Lägg till anpassad domän**.
4. Under **anpassad domän** anger du ditt anpassade domän namn. Till exempel contoso.com.
5. Välj **Verifiera**.

   Din domän bör klara verifiering och Visa gröna kryss markeringar bredvid **värd namns tillgänglighet** och **domän ägarskap**.
5. Välj **Lägg till anpassad domän**.
6. Uppdatera webbläsaren om du vill se det nya värd namnet under **värdnamn som har tilldelats till platsen**. Uppdateringen på sidan visar inte alltid ändringar direkt.
7. Upprepa proceduren för den andra webb programmet.

## <a name="add-the-alias-record-set"></a>Lägg till post uppsättningen alias

Lägg nu till en aliasresurspost för zonens Apex.

1. Öppna resurs gruppen och välj DNS-zonen.
2. Välj **Postuppsättning**.
3. Lägg till post uppsättningen i följande tabell:

   |Namn  |Typ  |Aliaspostuppsättning  |Aliastyp  |Azure-resurs|
   |---------|---------|---------|---------|-----|
   |@     |A|Ja|Azure-resurs|Traffic Manager – din profil|


## <a name="test-your-web-apps"></a>Testa dina webb program

Nu kan du testa för att kontrol lera att du kan komma åt din webbapp och att den är belastningsutjämnad.

1. Öppna en webbläsare och bläddra till din domän. Till exempel contoso.com. Du bör se sidan standard webb program.
2. Stoppa din första webbapp.
3. Stäng webbläsaren och vänta några minuter.
4. Starta webbläsaren och bläddra till din domän. Du bör fortfarande se sidan standard webb program.
5. Stoppa den andra webbappen.
6. Stäng webbläsaren och vänta några minuter.
7. Starta webbläsaren och bläddra till din domän. Du bör se fel 403, vilket indikerar att webbappen har stoppats.
8. Starta din andra webbapp.
9. Stäng webbläsaren och vänta några minuter.
10. Starta webbläsaren och bläddra till din domän. Du bör se sidan standard webb program igen.

## <a name="next-steps"></a>Nästa steg

Mer information om aliin poster finns i följande artiklar:

- [Självstudie: Konfigurera en aliasresurspost för att referera till en offentlig Azure-IP-adress](tutorial-alias-pip.md)
- [Självstudie: Konfigurera en aliaspost för att hantera apex-domännamn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](./dns-faq.md#alias-records)

Information om hur du migrerar ett aktivt DNS-namn finns i [Migrera ett aktivt DNS-namn till Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).