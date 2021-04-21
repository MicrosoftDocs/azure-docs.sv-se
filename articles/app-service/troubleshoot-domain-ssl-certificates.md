---
title: Felsöka domän- och TLS/SSL-certifikat
description: Hitta lösningar på vanliga problem som kan uppstå när du konfigurerar en domän eller ett TLS/SSL-certifikat i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: c2c09e1a30c9cef4d65b2d5443481c84ab779af8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833840"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Felsöka problem med domän- och TLS-/SSL-certifikat i Azure App Service

Den här artikeln innehåller vanliga problem som kan uppstå när du konfigurerar en domän eller ett TLS/SSL-certifikat för dina webbappar i Azure App Service. Den beskriver också möjliga orsaker och lösningar för dessa problem.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN](https://azure.microsoft.com/support/forums/)och Stack Overflow forumen . Du kan också skapa en Azure-supportincident. Gå till [Azure Support och](https://azure.microsoft.com/support/options/) välj Få **support.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Certifikatproblem

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Du kan inte lägga till en TLS/SSL-certifikatbindning i en app 

#### <a name="symptom"></a>Symptom

När du lägger till en TLS-bindning visas följande felmeddelande:

"Det gick inte att lägga till SSL-bindning. Det går inte att ange certifikat för befintlig VIP eftersom en annan VIP redan använder det certifikatet."

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du har flera IP-baserade SSL-bindningar för samma IP-adress i flera appar. App A har till exempel en IP-baserad SSL med ett gammalt certifikat. App B har en IP-baserad SSL med ett nytt certifikat för samma IP-adress. När du uppdaterar appens TLS-bindning med det nya certifikatet misslyckas den med det här felet eftersom samma IP-adress används för en annan app. 

#### <a name="solution"></a>Lösning 

Åtgärda problemet med någon av följande metoder:

- Ta bort den IP-baserade SSL-bindningen för den app som använder det gamla certifikatet. 
- Skapa en ny IP-baserad SSL-bindning som använder det nya certifikatet.

### <a name="you-cant-delete-a-certificate"></a>Du kan inte ta bort ett certifikat 

#### <a name="symptom"></a>Symptom

När du försöker ta bort ett certifikat visas följande felmeddelande:

"Det går inte att ta bort certifikatet eftersom det för närvarande används i en TLS/SSL-bindning. TLS-bindningen måste tas bort innan du kan ta bort certifikatet."

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om en annan app använder certifikatet.

#### <a name="solution"></a>Lösning

Ta bort TLS-bindningen för certifikatet från apparna. Försök sedan att ta bort certifikatet. Om du fortfarande inte kan ta bort certifikatet rensar du webbläsarens cacheminne och öppnar Azure Portal i ett nytt webbläsarfönster. Försök sedan att ta bort certifikatet.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Du kan inte köpa ett App Service certifikat 

#### <a name="symptom"></a>Symptom
Du kan inte köpa ett [Azure App Service certifikat](./configure-ssl-certificate.md#import-an-app-service-certificate) från Azure Portal.

#### <a name="cause-and-solution"></a>Orsak och lösning
Det här problemet kan inträffa av någon av följande orsaker:

- Den App Service plan är kostnadsfri eller delad. Dessa prisnivåer stöder inte TLS. 

    **Lösning:** Uppgradera App Service plan för appen till Standard.

- Prenumerationen har inget giltigt kreditkort.

    **Lösning:** Lägg till ett giltigt kreditkort i din prenumeration. 

- Prenumerationserbjudandet stöder inte köp av ett App Service certifikat, till exempel Microsoft Student.  

    **Lösning:** Uppgradera din prenumeration. 

- Prenumerationen har nått gränsen för köp som tillåts för en prenumeration.

    **Lösning:** App Service certifikat har en gräns på 10 certifikatinköp för prenumerationstyperna Betala per prenumeration och EA. För andra prenumerationstyper är gränsen 3. Kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om du vill öka gränsen.
- Det App Service certifikatet har markerats som bedrägeri. Du har fått följande felmeddelande: "Certifikatet har flaggats för eventuellt bedrägeri. Begäran granskas för närvarande. Om certifikatet inte kan användas inom 24 timmar kontaktar du Azure Support."

    **Lösning:** Om certifikatet har markerats som bedrägeri och inte har lösts efter 24 timmar följer du dessa steg:

    1. Logga in på [Azure-portalen](https://portal.azure.com).
    2. Gå till **App Service certifikat** och välj certifikatet.
    3. Välj **Certifikatkonfiguration**  >  **steg 2: Verifiera**  >  **domänverifiering.** Det här steget skickar ett e-postmeddelande till Azure-certifikatprovidern för att lösa problemet.

## <a name="custom-domain-problems"></a>Problem med anpassade domäner

### <a name="a-custom-domain-returns-a-404-error"></a>En anpassad domän returnerar ett 404-fel 

#### <a name="symptom"></a>Symptom

När du bläddrar till webbplatsen med hjälp av det anpassade domännamnet visas följande felmeddelande:

"Fel 404-Webbappen hittades inte."

#### <a name="cause-and-solution"></a>Orsak och lösning

**Orsak 1** 

Den anpassade domän som du konfigurerade saknar en CNAME- eller A-post. 

**Lösning för orsak 1**

- Om du har lagt till en A-post kontrollerar du att en TXT-post också har lagts till. Mer information finns i [Skapa A-posten](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Om du inte behöver använda rotdomänen för din app rekommenderar vi att du använder en CNAME-post i stället för en A-post.
- Använd inte både en CNAME-post och en A-post för samma domän. Det här problemet kan orsaka en konflikt och förhindra att domänen löses. 

**Orsak 2** 

Webbläsaren kanske fortfarande cachelagrar den gamla IP-adressen för din domän. 

**Lösning för orsak 2**

Rensa webbläsaren. För Windows-enheter kan du köra kommandot `ipconfig /flushdns` . Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) för att kontrollera att din domän pekar på appens IP-adress.

### <a name="you-cant-add-a-subdomain"></a>Du kan inte lägga till en underdomän 

#### <a name="symptom"></a>Symptom

Du kan inte lägga till ett nytt värdnamn i en app för att tilldela en underdomän.

#### <a name="solution"></a>Lösning

- Kontrollera med prenumerationsadministratören att du har behörighet att lägga till ett värdnamn i appen.
- Om du behöver fler underdomäner rekommenderar vi att du ändrar domänvärdar till Azure Domain Name Service (DNS). Genom att Azure DNS kan du lägga till 500 värdnamn i din app. Mer information finns i Lägga [till en underdomän.](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website)

### <a name="dns-cant-be-resolved"></a>DNS kan inte matchas

#### <a name="symptom"></a>Symptom

Du har fått följande felmeddelande:

"DET gick inte att hitta DNS-posten."

#### <a name="cause"></a>Orsak
Det här problemet uppstår av någon av följande orsaker:

- TTL-perioden (Time to Live) har inte gått ut. Kontrollera DNS-konfigurationen för din domän för att fastställa TTL-värdet och vänta sedan tills perioden upphör att gälla.
- DNS-konfigurationen är felaktig.

#### <a name="solution"></a>Lösning
- Vänta i 48 timmar på att det här problemet ska lösa sig självt.
- Om du kan ändra TTL-inställningen i DNS-konfigurationen ändrar du värdet till 5 minuter för att se om det löser problemet.
- Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) för att kontrollera att din domän pekar på appens IP-adress. Om den inte gör det konfigurerar du A-posten till rätt IP-adress för appen.

### <a name="you-need-to-restore-a-deleted-domain"></a>Du måste återställa en borttagna domän 

#### <a name="symptom"></a>Symptom
Domänen visas inte längre i Azure Portal.

#### <a name="cause"></a>Orsak 
Prenumerationens ägare kan ha tagit bort domänen av misstag.

#### <a name="solution"></a>Lösning
Om din domän togs bort för mindre än sju dagar sedan har domänen ännu inte startat borttagningsprocessen. I det här fallet kan du köpa samma domän igen på Azure Portal under samma prenumeration. (Se till att ange det exakta domännamnet i sökrutan.) Du debiteras inte igen för den här domänen. Om domänen togs bort för mer än sju dagar sedan kontaktar du [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få hjälp med att återställa domänen.

## <a name="domain-problems"></a>Domänproblem

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Du har köpt ett TLS/SSL-certifikat för fel domän

#### <a name="symptom"></a>Symptom

Du har köpt App Service certifikat för fel domän. Du kan inte uppdatera certifikatet så att det använder rätt domän.

#### <a name="solution"></a>Lösning

Ta bort certifikatet och köp sedan ett nytt certifikat.

Om det aktuella certifikatet som använder fel domän har statusen "Utfärdat" debiteras du också för det certifikatet. App Service certifikat kan inte återbetalas, men du kan kontakta [Azure-supporten för](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att se om det finns andra alternativ. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Ett App Service förnyades, men appen visar det gamla certifikatet 

#### <a name="symptom"></a>Symptom

Certifikatet App Service förnyades, men appen som använder App Service certifikatet använder fortfarande det gamla certifikatet. Dessutom fick du en varning om att HTTPS-protokollet krävs.

#### <a name="cause"></a>Orsak 
App Service synkroniserar certifikatet automatiskt inom 48 timmar. När du roterar eller uppdaterar ett certifikat hämtar programmet ibland fortfarande det gamla certifikatet och inte det nyligen uppdaterade certifikatet. Anledningen är att jobbet för att synkronisera certifikatresursen inte har körts ännu. Klicka på Synkronisera. Synkroniseringsåtgärden uppdaterar automatiskt värdnamnsbindningarna för certifikatet i App Service utan att orsaka några avbrott i dina appar.

#### <a name="solution"></a>Lösning

Du kan tvinga fram en synkronisering av certifikatet:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **App Service certifikat** och välj sedan certifikatet.
2. Välj **Nyckela om och synkronisera** och välj sedan **Synkronisera.** Synkroniseringen tar lite tid att slutföra. 
3. När synkroniseringen är klar visas följande meddelande: "Alla resurser har uppdaterats med det senaste certifikatet".

### <a name="domain-verification-is-not-working"></a>Domänverifiering fungerar inte 

#### <a name="symptom"></a>Symptom 
Certifikatet App Service kräver domänverifiering innan certifikatet är redo att användas. När du väljer **Verifiera** misslyckas processen.

#### <a name="solution"></a>Lösning
Verifiera domänen manuellt genom att lägga till en TXT-post:

1. Gå till den DNS-provider (Domain Name Service) som är värd för ditt domännamn.
1. Lägg till en TXT-post för din domän med värdet för den domäntoken som visas i Azure-portalen. 

Vänta några minuter tills DNS-spridningen har körts och välj sedan knappen **Uppdatera för** att utlösa verifieringen. 

Alternativt kan du använda HTML-webbsidans metod för att manuellt verifiera din domän. Med den här metoden kan certifikatutfärdaren bekräfta domänägarskapet för domänen som certifikatet har utfärdats för.

1. Skapa en HTML-fil med namnet {domänverifieringstoken}.html. Innehållet i den här filen ska vara värdet för domänverifieringstoken.
1. Ladda upp den här filen i roten på den webbserver som är värd för din domän.
1. Välj **Uppdatera** för att kontrollera certifikatstatusen. Det kan ta några minuter för verifieringen att slutföras.

Om du till exempel köper ett standardcertifikat för azure.com med domänverifieringstoken 1234abcd returnerar en webbegäran https://azure.com/1234abcd.html 1234abcd. 

> [!IMPORTANT]
> En certifikatbeställning har bara 15 dagar på sig att slutföra domänverifieringsåtgärden. Efter 15 dagar nekar certifikatutfärdaren certifikatet och du debiteras inte för certifikatet. I så fall tar du bort det här certifikatet och försöker igen.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Du kan inte köpa en domän

#### <a name="symptom"></a>Symptom
Du kan inte köpa en App Service domän i Azure Portal.

#### <a name="cause-and-solution"></a>Orsak och lösning

Det här problemet uppstår av någon av följande orsaker:

- Det finns inget kreditkort på Azure-prenumerationen, eller så är kreditkortet ogiltigt.

    **Lösning:** Lägg till ett giltigt kreditkort i din prenumeration.

- Du är inte prenumerationsägare, så du har inte behörighet att köpa en domän.

    **Lösning:** [Tilldela ägarrollen](../role-based-access-control/role-assignments-portal.md) till ditt konto. Eller kontakta prenumerationsadministratören för att få behörighet att köpa en domän.
- Du har nått gränsen för att köpa domäner i din prenumeration. Den nuvarande gränsen är 20.

    **Lösning:** Kontakta Azure-supporten om du vill begära en ökning [av gränsen.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- Din Azure-prenumerationstyp stöder inte köpet av en App Service-domän.

    **Lösning:** Uppgradera din Azure-prenumeration till en annan prenumerationstyp, till exempel en Betala per användning-prenumeration.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Du kan inte lägga till ett värdnamn i en app 

#### <a name="symptom"></a>Symptom

När du lägger till ett värdnamn kan inte processen validera och verifiera domänen.

#### <a name="cause"></a>Orsak 

Det här problemet uppstår av någon av följande orsaker:

- Du har inte behörighet att lägga till ett värdnamn.

    **Lösning:** Be prenumerationsadministratören att ge dig behörighet att lägga till ett värdnamn.
- Det gick inte att verifiera ditt domänägarskap.

    **Lösning:** Kontrollera att din CNAME- eller A-post är korrekt konfigurerad. Om du vill mappa en anpassad domän till en app skapar du antingen en CNAME-post eller en A-post. Om du vill använda en rotdomän måste du använda A- och TXT-poster:

    |Posttyp|Värd|Peka på|
    |------|------|-----|
    |A|@|IP-adress för en app|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Vanliga frågor

**Måste jag konfigurera min anpassade domän för min webbplats när jag har köpt den?**

När du köper en domän från Azure Portal konfigureras App Service automatiskt för att använda den anpassade domänen. Du behöver inte vidta några ytterligare åtgärder. Mer information finns i Azure App Service [Självhjälp: Lägga till ett Custom Domain namn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) på Channel9.

**Kan jag använda en domän som köpts i Azure Portal peka på en virtuell Azure-dator i stället?**

Ja, du kan peka domänen på en virtuell dator. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](../dns/dns-custom-domain.md).

**Finns goDaddy eller Azure DNS i min domän?**

App Service Domains använder GoDaddy för domänregistrering och Azure DNS som värd för domänerna. 

**Jag har aktiverat automatisk förnyelse men jag har fortfarande fått ett förnyelsemeddelande för min domän via e-post. Vad ska jag göra?**

Om du har aktiverat automatisk förnyelse behöver du inte vidta några åtgärder. Meddelandets e-postmeddelande skickas för att informera dig om att domänen är nära att gå ut och för att förnya manuellt om automatisk förnyelse inte har aktiverats.

**Kommer jag att debiteras för Azure DNS värd för min domän?**

Den initiala kostnaden för domänköp gäller endast domänregistrering. Förutom registreringskostnaden tillkommer avgifter för att Azure DNS din användning. Mer information finns i [Azure DNS för](https://azure.microsoft.com/pricing/details/dns/) mer information.

**Jag har köpt min domän tidigare från Azure Portal och vill flytta från GoDaddy-värd till Azure DNS värd. Hur gör jag detta?**

Det är inte obligatoriskt att migrera till Azure DNS värd. Om du vill migrera till Azure DNS innehåller domänhanteringsupplevelsen i Azure Portal information om de steg som krävs för att gå Azure DNS. Om domänen har köpts via App Service är migrering från GoDaddy till Azure DNS sömlös procedur.

**Jag vill köpa min domän från en App Service domän, men kan jag vara värd för min domän på GoDaddy i stället för Azure DNS?**

Från och med 24 juli 2017 App Service som köpts i portalen på Azure DNS. Om du föredrar att använda en annan värdleverantör måste du gå till deras webbplats för att få en domänvärdlösning.

**Måste jag betala för sekretessskydd för min domän?**

När du köper en domän via Azure Portal kan du välja att lägga till sekretess utan extra kostnad. Detta är en av fördelarna med att köpa din domän via Azure App Service.

**Kan jag få tillbaka mina pengar om jag inte längre vill ha min domän?**

När du köper en domän debiteras du inte för en period på fem dagar, och under den tiden kan du bestämma att du inte vill ha domänen. Om du bestämmer dig för att du inte vill ha domänen inom den femdagarsperioden debiteras du inte. (.uk-domäner är ett undantag till detta. Om du köper en .uk-domän debiteras du omedelbart och du kan inte återbetalas.)

**Kan jag använda domänen i en annan Azure App Service i min prenumeration?**

Ja. När du öppnar bladet Anpassade domäner och TLS i Azure Portal visas de domäner som du har köpt. Du kan konfigurera din app så att den använder någon av dessa domäner.

**Kan jag överföra en domän från en prenumeration till en annan prenumeration?**

Du kan flytta en domän till en annan prenumeration/resursgrupp med hjälp av [PowerShell-cmdleten Move-AzResource.](/powershell/module/az.Resources/Move-azResource)

**Hur kan jag hantera min anpassade domän om jag för närvarande inte har en Azure App Service app?**

Du kan hantera din domän även om du inte har en App Service webbapp. Domänen kan användas för Azure-tjänster som virtuell dator, lagring osv. Om du tänker använda domänen för App Service Web Apps måste du inkludera en webbapp som inte finns på den kostnadsfria App Service plan för att binda domänen till din webbapp.

**Kan jag flytta en webbapp med en anpassad domän till en annan prenumeration eller från App Service-miljön v1 till V2?**

Ja, du kan flytta din webbapp mellan prenumerationer. Följ riktlinjerna i [Så här flyttar du resurser i Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Det finns några begränsningar när du flyttar webbappen. Mer information finns i Begränsningar [för att flytta App Service resurser.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

När du har flyttat webbappen bör värdnamnsbindningarna för domänerna i den anpassade domäninställningen förbli desamma. Inga ytterligare steg krävs för att konfigurera bindningar för värdnamn.
