---
title: Verifiera Azure Traffic Manager-inställningar
description: I den här artikeln lär du dig hur du verifierar Traffic Manager inställningar och testar routningsmetoden för trafik.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 4bb7458d4258dffb074d663e5cf712c7a0245cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022334"
---
# <a name="verify-traffic-manager-settings"></a>Verifiera Traffic Manager-inställningar

Om du vill testa dina Traffic Manager inställningar måste du ha flera klienter på olika platser, från vilka du kan köra dina tester. Ta sedan slut punkterna i din Traffic Manager-profil en i taget.

* Ange ett lågt DNS-värde för TTL så att ändringar sprids snabbt (till exempel 30 sekunder).
* Ta reda på IP-adresserna för dina Azure-Molntjänster och webbplatser i den profil som du testar.
* Använd verktyg som du kan använda för att matcha ett DNS-namn med en IP-adress och Visa adressen.

Du kontrollerar att DNS-namnen matchar IP-adresserna för slut punkterna i din profil. Namnen bör matcha på ett sätt som stämmer överens med routningsmetoden för trafik som definierats i Traffic Manager profilen. Du kan använda verktygen som **nslookup** eller **gräva** för att lösa DNS-namn.

I följande exempel kan du testa din Traffic Manager-profil.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Kontrol lera Traffic Manager profil med nslookup och ipconfig i Windows

1. Öppna ett kommando eller en Windows PowerShell-kommandotolk som administratör.
2. Typ `ipconfig /flushdns` för att tömma DNS-matcharens cacheminne.
3. Skriv `nslookup <your Traffic Manager domain name>`. Följande kommando kontrollerar till exempel domän namnet med prefixet *MyApp. contoso*

    ```powershell
    nslookup myapp.contoso.trafficmanager.net
    ```

    Ett typiskt resultat visar följande information:

    + DNS-namnet och IP-adressen för den DNS-server som används för att matcha det här Traffic Manager domän namnet.
    + Det Traffic Manager domän namn som du angav på kommando raden efter "nslookup" och IP-adressen som Traffic Managers domän matchar. Den andra IP-adressen är den som är viktig för att kontrol lera. Den ska matcha en offentlig virtuell IP-adress (VIP) för en av de moln tjänster eller webbplatser i Traffic Manager profilen som du testar.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Så här testar du routningsmetoden för växling vid fel

1. Lämna alla slut punkter uppåt.
2. Med en enda klient begär du DNS-matchning för ditt företags domän namn med hjälp av nslookup eller liknande verktyg.
3. Se till att den matchade IP-adressen matchar den primära slut punkten.
4. Ta bort den primära slut punkten eller ta bort övervaknings filen så att Traffic Manager anser att programmet inte är igång.
5. Vänta tills DNS-TTL (Time-to-Live) för den Traffic Manager profilen plus ytterligare två minuter. Om ditt DNS-TTL till exempel är 300 sekunder (5 minuter) måste du vänta i sju minuter.
6. Rensa DNS-klientcachen och begär DNS-matchning med hjälp av nslookup. I Windows kan du tömma DNS-cachen med kommandot ipconfig/flushdns.
7. Se till att den matchade IP-adressen matchar den sekundära slut punkten.
8. Upprepa processen och Stäng av varje slut punkt i tur och ett. Kontrol lera att DNS returnerar IP-adressen för nästa slut punkt i listan. När alla slut punkter är nere bör du hämta IP-adressen för den primära slut punkten igen.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Så här testar du metoden för att routa viktad trafik

1. Lämna alla slut punkter uppåt.
2. Med en enda klient begär du DNS-matchning för ditt företags domän namn med hjälp av nslookup eller liknande verktyg.
3. Se till att den matchade IP-adressen matchar en av dina slut punkter.
4. Töm cacheminnet för DNS-klienten och upprepa steg 2 och 3 för varje slut punkt. Du bör se olika IP-adresser som returneras för varje slut punkt.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Så här testar du routningsmetoden för prestanda trafik

För att effektivt testa en routningsmetod för prestanda trafik måste du ha klienter som finns i olika delar av världen. Du kan skapa klienter i olika Azure-regioner som du kan använda för att testa dina tjänster. Om du har ett globalt nätverk kan du logga in på klienter i andra delar av världen och köra dina tester därifrån.

Det finns också kostnads fri webbaserad DNS-sökning och tjänster som är tillgängliga. Några av dessa verktyg ger dig möjlighet att kontrol lera DNS-namnmatchning från olika platser runtom i världen. Gör en sökning efter "DNS-sökning" för exempel. Tjänster från tredje part, t. ex. Gomez eller, kan användas för att bekräfta att profilerna distribuerar trafik som förväntat.

## <a name="next-steps"></a>Nästa steg

* [Om Traffic Manager Traffic routing-metoder](traffic-manager-routing-methods.md)
* [Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
* [Felsöka degraderat Traffic Manager-tillstånd](traffic-manager-troubleshooting-degraded.md)
