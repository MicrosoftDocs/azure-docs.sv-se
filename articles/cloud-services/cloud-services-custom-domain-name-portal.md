---
title: Konfigurera ett anpassat domän namn i Cloud Services (klassisk) | Microsoft Docs
description: Lär dig hur du exponerar dina Azure-program eller data på Internet på en anpassad domän genom att konfigurera DNS-inställningar.  I de här exemplen används Azure Portal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: bced2345473dbcbb5b9adf0269de0bef0549e862
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742377"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service-classic"></a>Konfigurera ett anpassat domän namn för en Azure Cloud Service (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

När du skapar en moln tjänst tilldelar Azure den till en under domän till **cloudapp.net**. Om din moln tjänst till exempel heter Contoso, kommer användarna att kunna komma åt ditt program på en URL som `http://contoso.cloudapp.net` . Azure tilldelar också en virtuell IP-adress.

Du kan dock också exponera ditt program på ditt eget domän namn, till exempel **contoso.com**. Den här artikeln förklarar hur du reserverar eller konfigurerar ett anpassat domän namn för webb roller för moln tjänster.

Förstår du redan vad CNAME och en post är? [Hoppa förbi förklaringen](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Procedurerna i den här uppgiften gäller för Azure Cloud Services. För App Services, se [mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). Information om lagrings konton finns i [Konfigurera ett anpassat domän namn för din Azure Blob Storage-slutpunkt](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Kom igång snabbare – Använd den nya Azure [guidade genom gången](https://support.microsoft.com/kb/2990804)!  Det gör att du kan associera ett anpassat domän namn och skydda kommunikation (TLS) med Azure Cloud Services eller Azure Websites a Snap.
> 
> 

## <a name="understand-cname-and-a-records"></a>Förstå CNAME-och A-poster
CNAME (eller Ali Aset poster) och en post gör att du kan associera ett domän namn med en speciell Server (eller tjänst i det här fallet) men de fungerar på olika sätt. Det finns även vissa saker att tänka på när du använder en post med Azure Cloud Services som du bör tänka på innan du bestämmer vilken du vill använda.

### <a name="cname-or-alias-record"></a>CNAME-eller Ali Aset
En CNAME-post mappar en *speciell* domän, till exempel **contoso.com** eller **www- \. contoso.com**, till ett kanoniskt domän namn. I det här fallet är det kanoniska domän namnet **[Mittprog]. cloudapp. net** Domain Name för ditt Azure-värdbaserade program. När det har skapats skapar CNAME ett alias för **[MyApp]. cloudapp. net**. CNAME-posten kommer att matcha IP-adressen för din **[MyApp]. cloudapp. net** -tjänst automatiskt, så om moln TJÄNSTens IP-adress ändras, behöver du inte vidta några åtgärder.

> [!NOTE]
> Vissa domän registratorer tillåter bara att du mappar under domäner när du använder en CNAME-post, t. ex. www- \. contoso.com och inte rot namn, till exempel contoso.com. Mer information om CNAME-poster finns i dokumentationen från din registrator, Wikipedia- [posten på CNAME-posten](https://en.wikipedia.org/wiki/CNAME_record)eller i [IETF-domän namn – implementering och Specifikations](https://tools.ietf.org/html/rfc1035) dokument.

### <a name="a-record"></a>A-post
En *a* -post mappar en domän, till exempel **contoso.com** eller **www- \. contoso.com**, *eller en domän med jokertecken* , till exempel **\* . contoso.com**, till en IP-adress. Om det gäller en Azure-molnbaserad tjänst är tjänstens virtuella IP-adress. Den största fördelen med en post över en CNAME-post är att du kan ha en post som använder ett jokertecken, till exempel \* *_. contoso.com_*, som hanterar begär Anden för flera under domäner som **mail.contoso.com**, **login.contoso.com** eller **www \. contso.com**.

> [!NOTE]
> Eftersom en A-post mappas till en statisk IP-adress, kan den inte automatiskt lösa ändringar i moln tjänstens IP-adress. Den IP-adress som används av moln tjänsten tilldelas första gången du distribuerar till ett tomt fack (produktion eller mellanlagring.) Om du tar bort distributionen för platsen, släpps IP-adressen av Azure och eventuella framtida distributioner till platsen kan tilldelas en ny IP-adress.
> 
> Enkelt är IP-adressen för en specifik distributions plats (produktion eller mellanlagring) Sparad när du växlar mellan mellanlagrings-och produktions distributioner eller utför en uppgradering på plats av en befintlig distribution. Mer information om hur du utför dessa åtgärder finns i [Hantera moln tjänster](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Lägg till en CNAME-post för din anpassade domän
Om du vill skapa en CNAME-post måste du lägga till en ny post i DNS-tabellen för din anpassade domän med hjälp av de verktyg som tillhandahålls av din registrator. Varje registrator har en liknande men något annorlunda metod för att ange en CNAME-post, men begreppen är desamma.

1. Använd någon av dessa metoder för att hitta det **. cloudapp.net** domän namn som tilldelats din moln tjänst.

   * Logga in på [Azure Portal], välj din moln tjänst, titta på **översikts** avsnittet och leta upp URL-posten för **webbplatsen** .

       ![snabb blick-avsnittet som visar webbplatsens URL][csurl]

       **OR**
   * Installera och konfigurera [Azure PowerShell](/powershell/azure/)och Använd sedan följande kommando:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Spara domän namnet som används i URL: en som returneras av någon av metoderna, eftersom du behöver det när du skapar en CNAME-post.
2. Logga in på din DNS-registrators webbplats och gå till sidan för att hantera DNS. Sök efter länkar eller områden på webbplatsen som är märkta som **domän namn**, **DNS** eller **namn server hantering**.
3. Nu kan du se var du kan välja eller ange CNAME. Du kan behöva välja post typen från en listruta eller gå till sidan Avancerade inställningar. Du bör söka efter orden **CNAME**, **alias** eller **under domäner**.
4. Du måste också ange domänen eller under domän Ali Aset för CNAME, till exempel **www** , om du vill skapa ett alias för **www- \. customdomain.com**. Om du vill skapa ett alias för rot domänen kan det visas som **\@** symbolen ' ' i din registrators DNS-verktyg.
5. Sedan måste du ange ett kanoniskt värdnamn, som är programmets **cloudapp.net** -domän i det här fallet.

Till exempel vidarebefordrar följande CNAME-post all trafik från **www- \. contoso.com** till **contoso.cloudapp.net**, det anpassade domän namnet för det distribuerade programmet:

| Alias/värdnamn/under domän | Kanonisk domän |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> En besökare av **www- \. contoso.com** kommer aldrig att se den sanna värden (contoso.cloudapp.net), så vidarebefordrings processen är osynlig för slutanvändaren.
> 
> Exemplet ovan gäller endast för trafik på **www** -underdomänen. Eftersom du inte kan använda jokertecken med CNAME-poster måste du skapa en CNAME-post för varje domän/under domän. Om du vill dirigera trafik från under domäner, till exempel *. contoso.com, till din cloudapp.net-adress kan du konfigurera en post för **URL-omdirigering** eller **URL-vidarebefordran** i dina DNS-inställningar eller skapa en a-post.

## <a name="add-an-a-record-for-your-custom-domain"></a>Lägg till en A-post för din anpassade domän
Om du vill skapa en A-post måste du först hitta den virtuella IP-adressen för din moln tjänst. Lägg sedan till en ny post i DNS-tabellen för din anpassade domän med hjälp av de verktyg som tillhandahålls av din registrator. Varje registrator har liknande men något annat sätt att ange en post, men begreppen är desamma.

1. Använd någon av följande metoder för att hämta IP-adressen för din moln tjänst.

   * Logga in på [Azure Portal], välj din moln tjänst, titta på avsnittet **Översikt** och leta upp posten **offentliga IP-adresser** .

       ![snabb genom delen som visar VIP][vip]

       **OR**
   * Installera och konfigurera [Azure PowerShell](/powershell/azure/)och Använd sedan följande kommando:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Spara IP-adressen eftersom du kommer att behöva den när du skapar en A-post.
2. Logga in på din DNS-registrators webbplats och gå till sidan för att hantera DNS. Sök efter länkar eller områden på webbplatsen som är märkta som **domän namn**, **DNS** eller **namn server hantering**.
3. Nu kan du se var du kan välja eller ange en posts. Du kan behöva välja post typen från en listruta eller gå till sidan Avancerade inställningar.
4. Välj eller ange den domän eller under domän som ska använda den här posten. Välj till exempel **www** om du vill skapa ett alias för www- **\. customdomain.com**. Om du vill skapa en post med jokertecken för alla under domäner anger du ' * * * * * '. Detta kommer att avse alla under domäner som **mail.customdomain.com**, **login.customdomain.com** och **www \. customdomain.com**.

    Om du vill skapa en post för rot domänen kan den visas som **\@** symbolen ' ' i din registrators DNS-verktyg.
5. Ange IP-adressen för din moln tjänst i det angivna fältet. Detta associerar domän posten som används i A-posten med IP-adressen för moln tjänst distributionen.

Följande A-post vidarebefordrar exempelvis all trafik från **contoso.com** till **137.135.70.239**, IP-adressen för det distribuerade programmet:

| Värddator namn/under domän | IP-adress |
| --- | --- |
| \@ |137.135.70.239 |

Det här exemplet visar hur du skapar en A-post för rot domänen. Om du vill skapa en post för jokertecken för alla under domäner anger du "* * * * *" som under domän.

> [!WARNING]
> IP-adresser i Azure är dynamiska som standard. Du bör förmodligen använda en [reserverad IP-adress](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) för att se till att din IP-adress inte ändras.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Så här hanterar du Cloud Services](cloud-services-how-to-manage-portal.md)
* [Mappa CDN-innehåll till en anpassad domän](../cdn/cdn-map-content-to-custom-domain.md)
* [Allmän konfiguration av din moln tjänst](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuerar en moln tjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera [TLS/SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure-portalen]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png