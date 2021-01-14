---
title: Trafikvy i Azure Traffic Manager
description: I den här introduktionen lär du dig hur Traffic Manager trafikvyer fungerar.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: b20357413c62460aba55a2d354b90995a2aa4815
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183701"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Trafikvy

Traffic Manager tillhandahåller routning av DNS-nivå så att slutanvändarna dirigeras till felfria slut punkter baserat på den routningsmetod som angavs när du skapade profilen. Trafikvy ger Traffic Manager en vy över dina användar baser (på en detaljerad nivå för DNS-matchning) och deras trafik mönster. När du aktiverar Trafikvy bearbetas den här informationen för att ge dig till gång till åtgärds bara insikter. 

Med hjälp av Trafikvy kan du:
- förstå var dina användar baser finns (upp till en lokal DNS-matchare på nivån).
- Visa trafik volymen (observeras som DNS-frågor som hanteras av Azure Traffic Manager) från dessa regioner.
- få insikter om vad som är den representativa svars tiden som dessa användare upplever.
- djupgående att ta sig in i de speciella trafik mönstren från var och en av dessa användar baser till Azure-regioner där du har slut punkter. 

Du kan till exempel använda Trafikvy för att förstå vilka regioner som har ett stort antal trafik, men som är lidande från högre fördröjningar. Sedan kan du använda den här informationen för att planera expansions utrymmet till nya Azure-regioner så att dessa användare kan ha en lägre svars tid.

## <a name="how-traffic-view-works"></a>Så här fungerar Trafikvy

Trafikvy fungerar genom att Traffic Manager titta på inkommande frågor som tagits emot under de senaste sju dagarna mot en profil som har den här funktionen aktive rad. Från informationen om inkommande frågor, Trafikvy extraherar käll-IP: en för den DNS-matchare som används som en representation av användarnas platser. De grupperas sedan tillsammans på en precisions nivå för DNS-matchning för att skapa användar bas regioner genom att använda den geografiska informationen för IP-adresser som underhålls av Traffic Manager. Traffic Manager tittar sedan på de Azure-regioner som frågan dirigerades till och skapar en trafik flödes karta för användare från dessa regioner.  
I nästa steg korrelerar Traffic Manager användarens bas region till Azures region mappning med de tabeller för nätverks informations latenter som den upprätthåller för olika slut användar nätverk för att förstå genomsnittlig svars tid för användare från dessa regioner vid anslutning till Azure-regioner. Alla dessa beräkningar kombineras sedan till en IP-nivå för en lokal DNS-matchare innan den presenteras för dig. Du kan använda informationen på olika sätt.

Frekvensen för data uppdatering av trafikvyer beror på flera interna tjänst-variabler. Data uppdateras dock vanligt vis var 24: e timme.

>[!NOTE]
>Svars tiden som beskrivs i Trafikvy är en representativ fördröjning mellan slutanvändaren och de Azure-regioner som de var anslutna till och som inte är svars tiden för DNS-sökning. Trafikvy gör en uppskattning av svars tiden mellan den lokala DNS-matcharen och den Azure-region som frågan dirigerades till, om det inte finns tillräckligt med data tillgängliga, kommer svars tiden som returneras vara null. 

## <a name="visual-overview"></a>Visuell översikt

När du navigerar till avsnittet **trafikvy** på Traffic Manager sidan, visas en geografisk karta med en överlagring av trafikvy insikter. Kartan ger information om användar basen och slut punkterna för din Traffic Manager profil.

![Traffic Manager geografisk vy för Trafikvy][1]

### <a name="user-base-information"></a>Användar bas information

För de lokala DNS-matchare som plats information är tillgänglig för visas de i kartan. Färgen på DNS-matcharen anger den genomsnittliga svars tiden som inträffat av slutanvändare som använde den här DNS-matcharen för sina Traffic Manager-frågor.

Om du hovrar över en DNS-matchare på kartan visas följande:
- IP-adressen för DNS-matcharen
- volymen DNS-fråga trafik som visas av Traffic Manager från den
- slut punkterna som trafik från DNS-matcharen dirigerades till, som en linje mellan slut punkten och DNS-matcharen 
- den genomsnittliga svars tiden från den platsen till slut punkten, som visas som färgen på linjen som ansluter dem

### <a name="endpoint-information"></a>Slut punkts information

De Azure-regioner där slut punkterna finns visas som blå punkter i kartan. Om slut punkten är extern och inte har någon mappad Azure-region visas den överst i kartan. Klicka på en slut punkt om du vill se olika platser (baserat på den DNS-matchare som används) från vilken trafik dirigerades till slut punkten. Anslutningarna visas som en linje mellan slut punkten och platsen för DNS-matchning och är färgade enligt den representativa svars tiden mellan det paret. Dessutom kan du se namnet på slut punkten, den Azure-region där den körs och den totala mängden begär Anden som har dirigerats till den av den här Traffic Manager profilen.


## <a name="tabular-listing-and-raw-data-download"></a>Hämtning av Tabellista och rå data

Du kan visa Trafikvy data i tabell format i Azure Portal. Det finns en post för varje IP/Endpoint-par i DNS-matcharen som visar IP-adressen för DNS-matcharen, namnet på och den geografiska platsen för den Azure-region där slut punkten finns (om den är tillgänglig), antalet begär Anden som associeras med den här DNS-matcharen till den slut punkten och den representativa svars tid som är kopplad till slutanvändarna Du kan också hämta Trafikvy data som en CSV-fil som kan användas som en del av ett analys arbets flöde som du väljer.

## <a name="billing"></a>Fakturering

När du använder Trafikvy faktureras du baserat på antalet data punkter som används för att skapa de insikter som presenteras. För närvarande är den enda typ av data punkt som används de frågor som tagits emot mot din Traffic Manager-profil. Mer information om prissättningen finns på [sidan Traffic Manager priser](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Vanliga frågor och svar

* [Vad gör Trafikvy?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Hur kan jag dra nytta av Trafikvy?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [Hur skiljer sig Trafikvy från Traffic Manager mått som är tillgängliga via Azure Monitor?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Använder Trafikvy EDNS-klientens under näts information?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Hur många dagars data Trafikvy använda?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Hur hanterar Trafikvy externa slut punkter?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [Måste jag aktivera Trafikvy för varje profil i min prenumeration?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Hur kan jag stänga av Trafikvy?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Hur fungerar Trafikvy fakturering?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Läs mer om [metoderna för trafik-routning](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Lär dig hur du [skapar en Traffic Manager-profil](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png