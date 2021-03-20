---
title: Felsöka domän-och TLS/SSL-certifikat
description: Hitta lösningar på vanliga problem som du kan stöta på när du konfigurerar en domän eller TLS/SSL-certifikat i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 691cbd79e82432c8e919dcbb51642a76000296dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97607617"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Felsök problem med domän-och TLS/SSL-certifikat i Azure App Service

Den här artikeln innehåller vanliga problem som kan uppstå när du konfigurerar en domän eller TLS/SSL-certifikat för dina webb program i Azure App Service. Det beskriver också möjliga orsaker och lösningar för dessa problem.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Certifikat problem

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Du kan inte lägga till en TLS/SSL-certifikat bindning till en app 

#### <a name="symptom"></a>Symptom

När du lägger till en TLS-bindning visas följande fel meddelande:

"Det gick inte att lägga till SSL-bindning. Det går inte att ange certifikat för befintlig VIP eftersom en annan VIP redan använder certifikatet. "

#### <a name="cause"></a>Orsak

Det här problemet kan uppstå om du har flera IP-baserade SSL-bindningar för samma IP-adress över flera appar. Till exempel har app A en IP-baserad SSL med ett gammalt certifikat. App B har en IP-baserad SSL med ett nytt certifikat för samma IP-adress. När du uppdaterar app TLS-bindningen med det nya certifikatet, Miss lyckas det med det här felet eftersom samma IP-adress används för en annan app. 

#### <a name="solution"></a>Lösning 

Använd någon av följande metoder för att åtgärda problemet:

- Ta bort den IP-baserade SSL-bindningen i appen som använder det gamla certifikatet. 
- Skapa en ny IP-baserad SSL-bindning som använder det nya certifikatet.

### <a name="you-cant-delete-a-certificate"></a>Du kan inte ta bort ett certifikat 

#### <a name="symptom"></a>Symptom

När du försöker ta bort ett certifikat visas följande fel meddelande:

"Det gick inte att ta bort certifikatet eftersom det för närvarande används i en TLS/SSL-bindning. TLS-bindningen måste tas bort innan du kan ta bort certifikatet. "

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om en annan app använder certifikatet.

#### <a name="solution"></a>Lösning

Ta bort TLS-bindningen för certifikatet från apparna. Försök sedan att ta bort certifikatet. Om du fortfarande inte kan ta bort certifikatet rensar du webbläsarens cacheminne och öppnar Azure Portal i ett nytt webbläsarfönster. Försök sedan att ta bort certifikatet.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Du kan inte köpa ett App Service certifikat 

#### <a name="symptom"></a>Symptom
Du kan inte köpa ett [Azure App Service certifikat](./configure-ssl-certificate.md#import-an-app-service-certificate) från Azure Portal.

#### <a name="cause-and-solution"></a>Orsak och lösning
Det här problemet kan bero på någon av följande orsaker:

- App Service plan är kostnads fri eller delad. Dessa pris nivåer stöder inte TLS. 

    **Lösning**: uppgradera App Service plan för appen till standard.

- Prenumerationen har inte något giltigt kredit kort.

    **Lösning**: Lägg till ett giltigt kredit kort i prenumerationen. 

- Prenumerations erbjudandet stöder inte köp av ett App Service-certifikat, till exempel Microsoft student.  

    **Lösning**: uppgradera din prenumeration. 

- Prenumerationen har uppnått gränsen för inköp som tillåts för en prenumeration.

    **Lösning**: app service certifikat har en gräns på 10 certifikat inköp för prenumerations typerna betala per användning och EA. För andra prenumerations typer är gränsen 3. Kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om du vill öka gränsen.
- App Service certifikatet har marker ATS som bedrägerier. Följande fel meddelande visades: "ditt certifikat har flaggats för möjlig bedrägerier. Begäran är för närvarande en granskning. Kontakta Azure-supporten om certifikatet inte kan användas inom 24 timmar. "

    **Lösning**: om certifikatet har marker ATS som bedrägeri och inte har lösts efter 24 timmar följer du dessa steg:

    1. Logga in på [Azure-portalen](https://portal.azure.com).
    2. Gå till **app service certifikat** och välj certifikat.
    3. Välj **certifikat konfiguration**  >  **steg 2: verifiera**  >  **domän verifieringen**. Det här steget skickar ett meddelande till Azure-certifikat leverantören för att lösa problemet.

## <a name="custom-domain-problems"></a>Anpassade domän problem

### <a name="a-custom-domain-returns-a-404-error"></a>En anpassad domän returnerar ett 404-fel 

#### <a name="symptom"></a>Symptom

När du bläddrar till platsen med hjälp av det anpassade domän namnet visas följande fel meddelande:

"Fel 404 – det gick inte att hitta webb programmet."

#### <a name="cause-and-solution"></a>Orsak och lösning

**Orsak 1** 

Den anpassade domän som du har konfigurerat saknar en CNAME-post eller en post. 

**Lösning för orsak 1**

- Om du har lagt till en A-post ska du se till att även en TXT-post har lagts till. Mer information finns i [skapa en post](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Om du inte behöver använda rot domänen för din app rekommenderar vi att du använder en CNAME-post i stället för en A-post.
- Använd inte både en CNAME-post och en A-post för samma domän. Det här problemet kan orsaka en konflikt och förhindra att domänen löses. 

**Orsak 2** 

Webbläsaren kanske fortfarande cachelagrar den gamla IP-adressen för din domän. 

**Lösning för orsak 2**

Rensa webbläsaren. För Windows-enheter kan du köra kommandot `ipconfig /flushdns` . Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) för att kontrol lera att din domän pekar på APPENs IP-adress.

### <a name="you-cant-add-a-subdomain"></a>Det går inte att lägga till en under domän 

#### <a name="symptom"></a>Symptom

Du kan inte lägga till ett nytt värdnamn i en app för att tilldela en under domän.

#### <a name="solution"></a>Lösning

- Kontakta prenumerations administratören för att kontrol lera att du har behörighet att lägga till ett värdnamn i appen.
- Om du behöver fler under domäner rekommenderar vi att du ändrar domän värd till Azure Domain Name Service (DNS). Med hjälp av Azure DNS kan du lägga till 500-värdnamn i din app. Mer information finns i [lägga till en under domän](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website).

### <a name="dns-cant-be-resolved"></a>DNS kan inte matchas

#### <a name="symptom"></a>Symptom

Du har fått följande fel meddelande:

"Det gick inte att hitta DNS-posten."

#### <a name="cause"></a>Orsak
Det här problemet uppstår av någon av följande orsaker:

- TTL-perioden (Time to Live) har inte gått ut. Kontrol lera DNS-konfigurationen för din domän för att fastställa TTL-värdet och vänta sedan tills perioden upphör att gälla.
- DNS-konfigurationen är felaktig.

#### <a name="solution"></a>Lösning
- Vänta i 48 timmar innan det här problemet kan lösas.
- Om du kan ändra TTL-inställningen i DNS-konfigurationen ändrar du värdet till 5 minuter för att se om det löser problemet.
- Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) för att kontrol lera att din domän pekar på APPENs IP-adress. Om den inte gör det konfigurerar du en post till rätt IP-adress för appen.

### <a name="you-need-to-restore-a-deleted-domain"></a>Du måste återställa en borttagen domän 

#### <a name="symptom"></a>Symptom
Din domän visas inte längre i Azure Portal.

#### <a name="cause"></a>Orsak 
Ägaren till prenumerationen kan ha tagit bort domänen av misstag.

#### <a name="solution"></a>Lösning
Om din domän har tagits bort mindre än sju dagar sedan har domänen inte startat om borttagnings processen än. I det här fallet kan du köpa samma domän igen på Azure Portal under samma prenumeration. (Var noga med att ange det exakta domän namnet i sökrutan.) Du debiteras inte igen för den här domänen. Om domänen har tagits bort mer än sju dagar sedan, kan du kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill ha hjälp med att återställa domänen.

## <a name="domain-problems"></a>Domän problem

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Du har köpt ett TLS/SSL-certifikat för fel domän

#### <a name="symptom"></a>Symptom

Du har köpt ett App Service-certifikat för fel domän. Du kan inte uppdatera certifikatet så att det använder rätt domän.

#### <a name="solution"></a>Lösning

Ta bort certifikatet och Köp ett nytt certifikat.

Om det aktuella certifikatet som använder fel domän är i "utfärdat" tillstånd debiteras du även för det certifikatet. App Service certifikat kan inte återbetalas, men du kan kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att se om det finns andra alternativ. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Ett App Service certifikat har förnyats, men appen visar det gamla certifikatet 

#### <a name="symptom"></a>Symptom

App Service certifikatet förnyades, men den app som använder App Service certifikatet använder fortfarande det gamla certifikatet. Dessutom har du fått en varning om att HTTPS-protokollet krävs.

#### <a name="cause"></a>Orsak 
App Service synkroniserar ditt certifikat automatiskt inom 48 timmar. När du roterar eller uppdaterar ett certifikat, kan det hända att programmet fortfarande hämtar det gamla certifikatet och inte det nyligen uppdaterade certifikatet. Orsaken är att jobbet att synkronisera certifikat resursen inte har körts än. Klicka på synkronisera. Synkroniseringsåtgärden uppdaterar automatiskt värd namns bindningarna för certifikatet i App Service utan att orsaka avbrott i dina appar.

#### <a name="solution"></a>Lösning

Du kan tvinga fram synkronisering av certifikatet:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **app service certifikat** och välj sedan certifikatet.
2. Välj **nyckel förnyelse och synkronisera** och välj sedan **Synkronisera**. Det tar lite tid att slutföra synkroniseringen. 
3. När synkroniseringen är klar visas följande meddelande: "alla resurser har uppdaterats med det senaste certifikatet".

### <a name="domain-verification-is-not-working"></a>Domän verifiering fungerar inte 

#### <a name="symptom"></a>Symptom 
Det App Service certifikatet kräver domän verifiering innan certifikatet kan användas. Processen Miss lyckas när du väljer **Verifiera**.

#### <a name="solution"></a>Lösning
Verifiera din domän manuellt genom att lägga till en TXT-post:

1. Gå till den DNS-provider (Domain Name Service) som är värd för ditt domännamn.
1. Lägg till en TXT-post för din domän med värdet för den domäntoken som visas i Azure-portalen. 

Vänta några minuter på att DNS-spridningen ska köras och välj sedan knappen **Uppdatera** för att utlösa verifieringen. 

Alternativt kan du använda HTML-webbsidans metod för att verifiera din domän manuellt. Med den här metoden kan certifikat utfärdaren bekräfta domän ägarskapet för den domän som certifikatet har utfärdats för.

1. Skapa en HTML-fil med namnet {Domain Verification token}. html. Innehållet i den här filen ska vara värdet för verifierings-token för domänen.
1. Ladda upp den här filen i roten på webb servern som är värd för din domän.
1. Välj **Uppdatera** för att kontrol lera certifikatets status. Det kan ta några minuter innan verifieringen har slutförts.

Om du t. ex. köper ett standard certifikat för azure.com med verifierings-token 1234ABCD, ska en webbegäran som görs till https://azure.com/1234abcd.html returnera 1234ABCD. 

> [!IMPORTANT]
> En certifikat beställning har bara 15 dagar på sig att slutföra domän verifierings åtgärden. Efter 15 dagar nekar certifikat utfärdaren certifikatet och du debiteras inte för certifikatet. I så fall tar du bort certifikatet och försöker igen.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Du kan inte köpa en domän

#### <a name="symptom"></a>Symptom
Du kan inte köpa en App Service domän i Azure Portal.

#### <a name="cause-and-solution"></a>Orsak och lösning

Det här problemet uppstår av någon av följande orsaker:

- Det finns inget kreditkort på Azure-prenumerationen, eller så är kreditkortet ogiltigt.

    **Lösning**: Lägg till ett giltigt kredit kort i prenumerationen.

- Du är inte prenumerationsägare, så du har inte behörighet att köpa en domän.

    **Lösning**: [tilldela ägar rollen](../role-based-access-control/role-assignments-portal.md) till ditt konto. Eller kontakta prenumerations administratören för att få behörighet att köpa en domän.
- Du har nått gränsen för att köpa domäner i din prenumeration. Den nuvarande gränsen är 20.

    **Lösning**: kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om du vill begära en ökning av gränsen.
- Din Azure-prenumerationstyp stöder inte köpet av en App Service-domän.

    **Lösning**: uppgradera din Azure-prenumeration till en annan prenumerations typ, till exempel en prenumeration där du betalar per användning.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Du kan inte lägga till ett värdnamn i en app 

#### <a name="symptom"></a>Symptom

När du lägger till ett värdnamn kan processen inte verifiera och verifiera domänen.

#### <a name="cause"></a>Orsak 

Det här problemet uppstår av någon av följande orsaker:

- Du har inte behörighet att lägga till ett värdnamn.

    **Lösning**: be prenumerations administratören att ge dig behörighet att lägga till ett värdnamn.
- Det gick inte att verifiera domän ägarskapet.

    **Lösning**: kontrol lera att din CNAME-eller A-post har kon figurer ATS korrekt. Om du vill mappa en anpassad domän till en app skapar du antingen en CNAME-post eller en A-post. Om du vill använda en rot domän måste du använda A-och TXT-poster:

    |Posttyp|Värd|Peka på|
    |------|------|-----|
    |A|@|IP-adress för en app|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Vanliga frågor

**Måste jag konfigurera min anpassade domän för min webbplats när jag köper den?**

När du köper en domän från Azure Portal konfigureras App Services programmet automatiskt för att använda den anpassade domänen. Du behöver inte vidta några ytterligare åtgärder. Mer information finns [Azure App Service själv hjälp: Lägg till ett anpassat domän namn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) på channel9.

**Kan jag använda en domän som köpts i Azure Portal för att peka på en virtuell Azure-dator i stället?**

Ja, du kan peka domänen till en virtuell dator. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](../dns/dns-custom-domain.md).

**Är min domän värdbaserad av GoDaddy eller Azure DNS?**

App Service domäner använder GoDaddy för domän registrering och Azure DNS som värd för domänerna. 

**Jag har aktiverat automatisk förnyelse, men ändå fått ett meddelande om förnyelse av min domän via e-post. Vad ska jag göra?**

Om du har aktiverat automatisk förnyelse måste du inte vidta några åtgärder. E-postmeddelandet visar att domänen snart upphör att gälla och förnyas manuellt om automatisk förnyelse inte är aktiverat.

**Debiteras jag för Azure DNS vara värd för min domän?**

Den ursprungliga kostnaden för domän inköp gäller endast för domän registrering. Utöver registrerings kostnaden debiteras du för Azure DNS baserat på din användning. Mer information finns i [Azure DNS prissättning](https://azure.microsoft.com/pricing/details/dns/) för mer information.

**Jag har köpt min domän tidigare från Azure Portal och vill flytta från GoDaddy som är värd för Azure DNS-värd. Hur kan jag göra detta?**

Det är inte obligatoriskt att migrera till Azure DNS-värd. Om du vill migrera till Azure DNS ger domän hanterings upplevelsen i Azure Portal om information om de steg som krävs för att flytta till Azure DNS. Om domänen köptes via App Service är migreringen från GoDaddy som är värd för Azure DNS relativt sömlös.

**Jag vill köpa min domän från App Service domän men kan jag vara värd för min domän på GoDaddy i stället för att Azure DNS?**

Från den 24 juli 2017 finns App Service domäner som köpts i portalen Azure DNS. Om du föredrar att använda en annan värd leverantör måste du gå till webbplatsen för att få en domän värd lösning.

**Måste jag betala för sekretess skydd för min domän?**

När du köper en domän via Azure Portal kan du välja att lägga till sekretess utan extra kostnad. Detta är en av fördelarna med att köpa din domän via Azure App Service.

**Kan jag få pengarna tillbaka om jag väljer att jag inte längre vill ha min domän?**

När du köper en domän debiteras du inte för en period på fem dagar, då du kan bestämma att du inte vill att domänen ska användas. Om du väljer att inte vill att domänen ska vara inom den fem dagars perioden debiteras du inte. (. uk-domäner är ett undantag till detta. Om du köper en. uk-domän debiteras du omedelbart och du kan inte återbetala.)

**Kan jag använda domänen i en annan Azure App Service app i min prenumeration?**

Ja. När du öppnar bladet anpassade domäner och TLS i Azure Portal visas de domäner som du har köpt. Du kan konfigurera appen så att den använder någon av dessa domäner.

**Kan jag överföra en domän från en prenumeration till en annan prenumeration?**

Du kan flytta en domän till en annan prenumeration eller resurs grupp med hjälp av PowerShell-cmdleten [Move-AzResource](/powershell/module/az.Resources/Move-azResource) .

**Hur kan jag hantera min anpassade domän om jag för närvarande inte har en Azure App Service-app?**

Du kan hantera din domän även om du inte har en App Service-webbapp. Domän kan användas för Azure-tjänster som virtuell dator, lagring osv. Om du tänker använda domänen för App Service Web Apps måste du inkludera en webbapp som inte finns i App Service – kostnadsfri-planen för att kunna binda domänen till din webbapp.

**Kan jag flytta en webbapp med en anpassad domän till en annan prenumeration eller från App Service-miljön v1 till v2?**

Ja, du kan flytta din webbapp över prenumerationer. Följ rikt linjerna i [hur du flyttar resurser i Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Det finns några begränsningar när du flyttar webbappen. Mer information finns i [begränsningar för att flytta App Service-resurser](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

När du har flyttat webbappen bör värd namns bindningarna för domänerna i inställningen för anpassade domäner vara desamma. Inga ytterligare steg krävs för att konfigurera värd namn bindningarna.