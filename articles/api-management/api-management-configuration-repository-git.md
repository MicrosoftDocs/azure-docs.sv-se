---
title: Konfigurera API Management tjänsten med git-Azure | Microsoft Docs
description: Lär dig hur du sparar och konfigurerar API Management tjänst konfigurationen med git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 18cc42c3447de733447c27db52a9a6d664539464
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89400371"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Så gör du för att spara och konfigurera din API Management-tjänstkonfiguration med Git

Varje API Management tjänst instans upprätthåller en konfigurations databas som innehåller information om konfiguration och metadata för tjänst instansen. Du kan göra ändringar i tjänst instansen genom att ändra en inställning i Azure Portal, använda en PowerShell-cmdlet eller göra ett REST API anrop. Förutom dessa metoder kan du även hantera din tjänst instans konfiguration med git, vilket möjliggör Service Management-scenarier som:

* Konfigurations version – Ladda ned och lagra olika versioner av tjänst konfigurationen
* Ändringar i Mass konfiguration – gör ändringar i flera delar av tjänst konfigurationen på din lokala lagrings plats och integrera ändringarna till servern med en enda åtgärd
* Välbekant git-verktygskedjan och arbets flöde – Använd git-verktyg och arbets flöden som du redan är van vid

Följande diagram visar en översikt över olika sätt att konfigurera API Management tjänst instansen.

![Git-konfiguration][api-management-git-configure]

När du gör ändringar i tjänsten med hjälp av Azure Portal, PowerShell-cmdletar eller REST API, hanterar du tjänst konfigurations databasen med `https://{name}.management.azure-api.net` slut punkten, som visas på höger sida av diagrammet. Den vänstra sidan i diagrammet illustrerar hur du kan hantera tjänst konfigurationen med git och git-lagringsplatsen för din tjänst som finns på `https://{name}.scm.azure-api.net` .

Följande steg ger en översikt över hur du hanterar din API Management tjänst instans med hjälp av git.

1. Få åtkomst till git-konfiguration i din tjänst
2. Spara konfigurations databasen för tjänsten på git-lagringsplatsen
3. Klona git-lagrings platsen till din lokala dator
4. Hämta den senaste lagrings platsenen till din lokala dator och genomför och skicka tillbaka ändringar till din lagrings platsen
5. Distribuera ändringarna från din lagrings platsen till tjänst konfigurations databasen

I den här artikeln beskrivs hur du aktiverar och använder Git för att hantera tjänst konfigurationen och innehåller en referens till filerna och mapparna på git-lagringsplatsen.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Få åtkomst till git-konfiguration i din tjänst

Om du vill visa och konfigurera inställningar för git-konfigurationen kan du klicka på menyn **distribution och infrastruktur** och gå till fliken **lagrings plats** .

![Aktivera GIT][api-management-enable-git]

> [!IMPORTANT]
> Alla hemligheter som inte definieras som namngivna värden lagras på lagrings platsen och behålls i historiken tills du inaktiverar och återaktiverar git-åtkomst. Namngivna värden ger en säker plats för att hantera konstanta sträng värden, inklusive hemligheter, i alla API-konfigurationer och-principer, så att du inte behöver lagra dem direkt i dina princip instruktioner. Mer information finns i [använda namngivna värden i Azure API Management-principer](api-management-howto-properties.md).
>
>

Information om hur du aktiverar eller inaktiverar git-åtkomst med hjälp av REST API finns i [Aktivera eller inaktivera git-åtkomst med hjälp av REST API](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Spara tjänst konfigurationen till git-lagringsplatsen

Det första steget innan du klonar lagrings platsen är att spara det aktuella läget för tjänst konfigurationen till lagrings platsen. Klicka på **Spara till lagrings plats**.

Gör önskade ändringar på bekräftelse skärmen och klicka på **Spara** för att spara.

Efter en liten stund sparas konfigurationen och databasens konfigurations status visas, inklusive datum och tid för den senaste konfigurations ändringen och den senaste synkroniseringen mellan tjänst konfigurationen och lagrings platsen.

När konfigurationen har sparats i lagrings platsen kan den klonas.

För information om hur du utför den här åtgärden med hjälp av REST API, se [Bekräfta konfiguration av ögonblicks bild med hjälp av REST API](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>För att klona lagrings platsen till din lokala dator

Om du vill klona en lagrings plats behöver du URL: en till din lagrings plats, ett användar namn och ett lösen ord. Om du vill hämta användar namn och andra autentiseringsuppgifter klickar du på behörigheter för **åtkomst** längst upp på sidan.

Om du vill generera ett lösen ord måste du först se till att förfallo datum och- **tid har angetts** till önskad giltighets tid och klicka sedan på **generera**.

> [!IMPORTANT]
> Anteckna det här lösen ordet. När du lämnar den här sidan visas inte lösen ordet igen.
>

I följande exempel används git bash-verktyget från [git för Windows](https://www.git-scm.com/downloads) , men du kan använda ett git-verktyg som du känner till.

Öppna git-verktyget i önskad mapp och kör följande kommando för att klona git-lagringsplatsen till den lokala datorn med hjälp av kommandot som tillhandahålls av Azure Portal.

```
git clone https://{name}.scm.azure-api.net/
```

Ange användar namn och lösen ord när du uppmanas till det.

Om du får fel meddelanden kan du försöka `git clone` att ändra kommandot så att det innehåller användar namn och lösen ord, som du ser i följande exempel.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Om detta är ett fel kan du prova URL: en och ange lösen ordets del av kommandot. Ett snabbt sätt att göra detta är att öppna Visual Studio och utfärda följande kommando i kommando **tolken**. Öppna **direkt fönstret** genom att öppna en lösning eller ett projekt i Visual Studio (eller skapa ett nytt tomt konsol program) och välj **Windows**, **omedelbart** från **fel söknings** menyn.

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Använd det kodade lösen ordet tillsammans med ditt användar namn och din lagrings plats för att skapa git-kommandot.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

När lagrings platsen är klonad kan du Visa och arbeta med den i det lokala fil systemet. Mer information finns i [referens för fil-och mappstruktur för den lokala git-lagringsplatsen](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Uppdatera din lokala lagrings plats med den mest aktuella tjänst instans konfigurationen

Om du gör ändringar i API Management tjänst instansen i Azure Portal eller använder REST API måste du spara ändringarna i lagrings platsen innan du kan uppdatera din lokala lagrings plats med de senaste ändringarna. Det gör du genom att klicka på **Spara till lagrings plats** på fliken **databas** i Azure Portal och sedan utfärda följande kommando i din lokala lagrings plats.

```
git pull
```

Innan `git pull` du börjar bör du se till att du är i mappen för din lokala lagrings plats. Om du precis har slutfört `git clone` kommandot måste du ändra katalogen till din lagrings platsen genom att köra ett kommando som liknar följande.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Skicka ändringar från din lokala lagrings platsen till servern lagrings platsen
Om du vill skicka ändringar från den lokala lagrings platsen till Server databasen måste du spara ändringarna och sedan skicka dem till Server databasen. Om du vill genomföra ändringarna öppnar du git-kommando verktyget, växlar till katalogen för din lokala lagrings plats och utfärdar följande kommandon.

```
git add --all
git commit -m "Description of your changes"
```

Kör följande kommando för att skicka alla incheckningar till servern.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Distribuera ändringar i tjänst konfigurationen till API Management tjänst instansen

När dina lokala ändringar har genomförts och skickas till Server databasen kan du distribuera dem till din API Management tjänst instans.

Information om hur du utför den här åtgärden med hjälp av REST API finns i [distribuera git-ändringar till konfigurations databasen med hjälp av REST API](/rest/api/apimanagement/2019-12-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Fil-och mappstrukturs referens för lokal git-lagringsplats

Filerna och mapparna i den lokala git-lagringsplatsen innehåller konfigurations information om tjänst instansen.

| Objekt | Beskrivning |
| --- | --- |
| rotmapps-API – hanterings mapp |Innehåller konfiguration på översta nivån för tjänst instansen |
| API-mapp |Innehåller konfigurationen för API: erna i tjänst instansen |
| mapp för grupper |Innehåller konfigurationen för grupperna i tjänst instansen |
| mappen principer |Innehåller principerna i tjänst instansen |
| portalStyles-mapp |Innehåller konfigurationen för anpassningar av utvecklings portalen i tjänst instansen |
| mappen produkter |Innehåller konfigurationen för produkterna i tjänst instansen |
| mappen Mallar |Innehåller konfigurationen för e-postmallarna i tjänst instansen |

Varje mapp kan innehålla en eller flera filer, och i vissa fall en eller flera mappar, till exempel en mapp för varje API, produkt eller grupp. Filerna i varje mapp är speciella för enhets typen som beskrivs av mappnamnet.

| Filtyp | Syfte |
| --- | --- |
| json |Konfigurations information om respektive entitet |
| html |Beskrivningar av entiteten, som ofta visas i Developer-portalen |
| xml |Principrapporter |
| css |Formatmallar för anpassning av utvecklings Portal |

De här filerna kan skapas, tas bort, redige ras och hanteras i det lokala fil systemet och ändringarna distribueras tillbaka till API Management tjänst instansen.

> [!NOTE]
> Följande entiteter ingår inte i git-lagringsplatsen och kan inte konfigureras med git.
>
> * [Användare](/rest/api/apimanagement/2019-12-01/user)
> * [Prenumerationer](/rest/api/apimanagement/2019-12-01/subscription)
> * Namngivna värden
> * Andra utvecklare Portal-enheter än format
>

### <a name="root-api-management-folder"></a>Rotmapps-API – hanterings mapp
Rotmappen `api-management` innehåller en `configuration.json` fil som innehåller information på översta nivån för tjänst instansen i följande format.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

De första fyra inställningarna ( `RegistrationEnabled` , `UserRegistrationTerms` , `UserRegistrationTermsEnabled` och `UserRegistrationTermsConsentRequired` ) mappas till följande inställningar på fliken **identiteter** i avsnittet **Developer-portalen** .

| Identitets inställning | Mappar till |
| --- | --- |
| RegistrationEnabled |Förekomst av providern **användar namn och lösen ord** identitet |
| UserRegistrationTerms |Text ruta **för användningsvillkor på användar registrering** |
| UserRegistrationTermsEnabled |Kryss rutan **Visa användnings villkoren på registrerings sidan** |
| UserRegistrationTermsConsentRequired |Kryss rutan **Kräv medgivande** |
| RequireUserSigninEnabled |Kryss rutan **omdirigera anonyma användare till inloggnings sidan** |

De följande fyra inställningarna ( `DelegationEnabled` , `DelegationUrl` , `DelegatedSubscriptionEnabled` och `DelegationValidationKey` ) mappas till följande inställningar på fliken **delegering** i avsnittet **Developer-portalen** .

| Delegerings inställning | Mappar till |
| --- | --- |
| DelegationEnabled |Kryss ruta **för att delegera inloggning &** |
| DelegationUrl |URL-text för **Delegerings slut punkt** |
| DelegatedSubscriptionEnabled |Kryss rutan **delegera produkt prenumeration** |
| DelegationValidationKey |Text ruta för att **delegera validerings nyckel** |

Den sista inställningen, `$ref-policy` mappas till den globala princip deklarations filen för tjänst instansen.

### <a name="apis-folder"></a>API-mapp
`apis`Mappen innehåller en mapp för varje API i tjänst instansen, som innehåller följande objekt.

* `apis\<api name>\configuration.json` – Det här är konfigurationen för API: et och innehåller information om Server dels tjänstens URL och åtgärder. Detta är samma information som skulle returneras om du anropar [Hämta ett särskilt API](/rest/api/apimanagement/2019-12-01/apis/get) med `export=true` i `application/json` formatet.
* `apis\<api name>\api.description.html` – Det här är en beskrivning av API: et och motsvarar `description` egenskapen för API- [entiteten](/java/api/com.microsoft.azure.storage.table.entityproperty).
* `apis\<api name>\operations\` -den här mappen innehåller `<operation name>.description.html` filer som mappar till åtgärderna i API: et. Varje fil innehåller en beskrivning av en enskild åtgärd i API: et, som mappar till `description` egenskapen för [entiteten åtgärd](/rest/api/visualstudio/operations/list#operationproperties) i REST API.

### <a name="groups-folder"></a>mapp för grupper
`groups`Mappen innehåller en mapp för varje grupp som definierats i tjänst instansen.

* `groups\<group name>\configuration.json` – Det här är konfigurationen för gruppen. Detta är samma information som skulle returneras om du anropar åtgärden [Hämta en enskild grupp](/rest/api/apimanagement/2019-12-01/group/get) .
* `groups\<group name>\description.html` – Det här är en beskrivning av gruppen och motsvarar `description` egenskapen för [grupp entiteten](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>mappen principer
`policies`Mappen innehåller princip instruktionerna för din tjänst instans.

* `policies\global.xml` – innehåller principer som definierats i det globala omfånget för din tjänst instans.
* `policies\apis\<api name>\` -Om du har definierat några principer i API-omfattning finns de i den här mappen.
* `policies\apis\<api name>\<operation name>\` mapp – om du har definierat några principer i åtgärds omfattningen finns de i den här mappen i `<operation name>.xml` filer som mappar till princip instruktionerna för varje åtgärd.
* `policies\products\` -Om du har några principer definierade i produktens omfattning finns de i den här mappen, som innehåller `<product name>.xml` filer som mappar till princip instruktionerna för varje produkt.

### <a name="portalstyles-folder"></a>portalStyles-mapp
`portalStyles`Mappen innehåller konfigurations-och formatmallar för anpassningar av utvecklings portalen för tjänst instansen.

* `portalStyles\configuration.json` – innehåller namnen på formatmallarna som används av Developer-portalen
* `portalStyles\<style name>.css` – varje `<style name>.css` fil innehåller format för Developer-portalen ( `Preview.css` och `Production.css` som standard).

### <a name="products-folder"></a>mappen produkter
`products`Mappen innehåller en mapp för varje produkt som definierats i tjänst instansen.

* `products\<product name>\configuration.json` – Det här är konfigurationen för produkten. Detta är samma information som skulle returneras om du anropar åtgärden [Hämta en enskild produkt](/rest/api/apimanagement/2019-12-01/product/get) .
* `products\<product name>\product.description.html` – Det här är en beskrivning av produkten och motsvarar `description` egenskapen för [entiteten produkt](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) i REST API.

### <a name="templates"></a>mallar
`templates`Mappen innehåller konfiguration för [e-postmallarna](api-management-howto-configure-notifications.md) för tjänst instansen.

* `<template name>\configuration.json` – Det här är konfigurationen för e-postmallen.
* `<template name>\body.html` – Det här är bröd texten i e-postmallen.

## <a name="next-steps"></a>Nästa steg
Information om andra sätt att hantera din tjänst instans finns i:

* Hantera din tjänst instans med följande PowerShell-cmdletar
  * [Referens för PowerShell-cmdleten för tjänstdistribution](/powershell/module/wds)
  * [Referens för PowerShell-cmdleten för tjänsthantering](/powershell/azure/servicemanagement/overview)
* Hantera din tjänst instans med hjälp av REST API
  * [API Management REST API referens](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png
