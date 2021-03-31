---
title: Skapa ett tillgängligt kart program med Azure Maps | Microsoft Azure Maps
description: Lär dig mer om tillgänglighets aspekter i Azure Maps. Se vilka funktioner som är tillgängliga för att göra kart program tillgängliga och Visa hjälpmedels tips.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: ec88437a0fad3a6bd94a67a5ef5c75b3e506f9e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88006221"
---
# <a name="building-an-accessible-application"></a>Skapa ett tillgängligt program

Om du har fler än 20% av Internet-användare har du behov av tillgängliga webb program. Det är därför viktigt att se till att ditt program är utformat så att alla användare enkelt kan använda det. I stället för att tänka på tillgänglighet som en uppsättning uppgifter som ska slutföras, kan du se den som en del av din övergripande användar upplevelse. Den mer lättillgängliga appen, desto fler personer som kan använda det. 

När det kommer till omfattande interaktivt innehåll som en karta är några vanliga tillgänglighets aspekter:
- Stöd för skärm läsaren för användare som har svårt att se webb programmet.
- Det finns flera metoder för att interagera med och navigera i webb programmet, till exempel mus, touch och tangent bord.
- Se till att färg kontrasten är sådan att färgerna inte smälter samman och blir hårda för att skilja sig från varandra. 

Azure Maps Web SDK levereras med många hjälpmedels funktioner, till exempel:
- Skärm läsar beskrivningar när kartan flyttas och när användaren fokuserar på en kontroll eller popup.
- Stöd för musen, touch och keyboard.
- Stöd för tillgänglig färg kontrast på väg kartans format.
- Stöd för hög kontrast.

Fullständig tillgänglighets information för alla Microsoft-produkter hittar du [här](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Sök efter "Azure Maps webb" för att hitta det dokument som är specifikt för Azure Maps Web SDK. 

## <a name="navigating-the-map"></a>Navigera i kartan

Det finns flera olika sätt på vilka kartan kan zoomas, panoreras, roteras och visas. Följande information innehåller alla olika sätt att navigera i kartan.

**Zooma kartan**

- Använd en mus, dubbelklicka på kartan för att zooma in en nivå.
- Använd en mus, Rulla hjulet för att zooma kartan.
- Använd en touch-skärm och vidrör kartan med två fingrar och dra ihop för att zooma ut eller sprida fingrarna från varandra för att zooma in.
- Använd en pekskärm och dubbelknacka på kartan för att zooma på en nivå.
- Med kartan fokuserad använder du plus tecknet ( `+` ) eller likhets tecknet ( `=` ) för att zooma in en nivå.
- Med kartan fokuserad använder du minus tecknet, bindestrecket ( `-` ) eller under streck ( `_` ) för att zooma ut en nivå.
- Använd zoomnings kontrollen med musen, touch eller tangent bord/ange nycklar.
- Tryck på och håll ned `Shift` knappen och tryck på vänster musknapp ned på kartan och dra för att rita ut ett område för att zooma kartan till.
- Använd lite multi-touch-pad, dra två fingrar upp för att zooma ut eller ned för att zooma in.

**Panorera kartan**

- Använd en mus, tryck ned med vänster musknapp på kartan och dra i valfri riktning.
- Använd en touch-skärm och vidrör kartan och dra i valfri riktning.
- Med kartan fokuserad använder du piltangenterna för att flytta kartan.

**Rotera kartan**

- Använd en mus, tryck ned med höger musknapp på kartan och dra åt vänster eller höger. 
- Använd en touch-skärm och vidrör kartan med två fingrar och rotera.
- Med kartan fokuserad använder du Shift-tangenten och vänster-eller högerpilarna.
- Använd rotations kontrollen med musen, touch eller tangent bord/ange nycklar.

**Bredd på kartan**

- Med musen trycker du ned med höger musknapp på kartan och drar upp eller ned. 
- Använd en touch-skärm och vidrör kartan med två fingrar och dra dem uppåt eller nedåt.
- Med den fokuserade kartan använder du Shift-tangenten och tangenterna Uppil eller NEDPIL. 
- Använd kontrollen mus, touch eller tangent bord/ange nycklar.

## <a name="change-the-map-style"></a>Ändra kart stil

Alla utvecklare vill inte att alla möjliga kart format ska vara tillgängliga i sitt program. Om utvecklaren visar kontrollen format väljare för kartan kan användaren ändra kopplings formatet med musen, en touch eller tangent bordet med tabben eller ange nyckel. Utvecklaren kan ange vilka kart format som ska vara tillgängliga i kontrollen kartans stil väljare. Dessutom kan utvecklaren program mässigt ange och ändra kart formatet.

**Använd högkontrast**

- När kart kontrollen läses in, kontrollerar den om hög kontrast är aktiverat och webbläsaren stöder det.
- Kart kontrollen övervakar inte enhetens höga kontrast läge. Om enhets läget ändras, kommer kartan inte att vara det. Det innebär att användaren måste läsa in kartan igen genom att uppdatera sidan.
- När hög kontrast identifieras växlar kart formatet automatiskt till hög kontrast, och alla inbyggda kontroller använder en hög kontrast stil. Till exempel, ZoomControl, PitchControl, CompassControl, StyleControl och andra inbyggda kontroller, kommer att använda ett högt kontrast format.
- Det finns två typer av hög kontrast, ljus och mörk. Om typen av hög kontrast kan identifieras av kart kontrollerna, kommer beteendet för kartan att justeras efter behov. Om ljuset, kommer den grayscale_light kart stilen att läsas in. Om typen inte kan identifieras eller är mörk, kommer high_contrast_dark formatet att läsas in.
- Om du skapar anpassade kontroller är det bra att veta om de inbyggda kontrollerna använder en hög kontrast stil. Utvecklare kan lägga till en CSS-klass på kartan container div för att kontrol lera. De CSS-klasser som ska läggas till är `high-contrast-dark` och `high-contrast-light` . Om du vill kontrol lera med hjälp av Java Script använder du:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

eller Använd:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Kortkommandon

Kartan har ett antal kortkommandon inbyggda som gör det enklare att använda kartan. Dessa kortkommandon fungerar när kartan är i fokus.

| Nyckel      | Action                            |
|----------|-----------------------------------|
| `Tab` | Navigera bland kontrollerna och popup-fönster i kartan. |
| `ESC` | Flytta fokus från valfritt element i kartan till kart elementet på översta nivån. |
| `Ctrl` + `Shift` + `D` | Växla skärm läsar detalj nivå.  |
| Vänsterpil | Panorera kartan till vänster 100 bild punkter |
| Högerpil | Panorera kartan höger 100 bild punkter |
| NEDPIL-tangenten | Panorera kartan nedåt 100 bild punkter |
| UPPIL-nyckel | Panorera kartan upp 100 bild punkter |
| `Shift` + UPPIL | Öka kart bredden med 10 grader |
| `Shift` + NEDPIL | Minska kart bredden med 10 grader |
| `Shift` + HÖGERPIL | Rotera kartan 15 grader medsols |
| `Shift` + VÄNSTERPIL | Rotera kartan 15 grader moturs |
| Plus tecken ( `+` ) eller <sup>*</sup> likhets tecken ( `=` ) | Zooma in |
| Minus tecken, bindestreck ( `-` ) eller <sup>*</sup> under streck ( `_` ) | Zooma ut | 
| `Shift` + Dra på kartan för att rita område | Zooma in i yta |

<sup>*</sup> Dessa kortkommandon delar vanligt vis samma nyckel på ett tangent bord. Dessa genvägar har lagts till för att förbättra användar upplevelsen. Det spelar ingen roll om användaren använder Shift-tangenten eller inte för dessa kortkommandon.

## <a name="screen-reader-support"></a>Stöd för skärm läsare

Användare kan navigera i kartan med tangent bordet. Om en skärm läsare körs meddelar kartan användaren om ändringar i sitt tillstånd. Användare meddelas till exempel om kart ändringar när kartan är panorerad eller zoomad. Som standard tillhandahåller kartan förenklade beskrivningar som utesluter zoomnings nivån och koordinaterna i mitten av kartan. Användaren kan växla detalj nivån för dessa beskrivningar genom att använda tangent bordet kort klipp `Ctrl`  +  `Shift`  +  `D` .

All ytterligare information som placeras på bas kartan bör ha motsvarande text information för skärm läsar användare. Se till att du lägger till [HJÄLPMEDELSANPASSADE Aria-](https://www.w3.org/WAI/standards-guidelines/aria/), Alt-och title-attribut där det är lämpligt. 

## <a name="make-popups-keyboard-accessible"></a>Gör tangent bordet tillgängligt för popup-fönster

En markör eller symbol används ofta för att representera en plats på kartan. Ytterligare information om platsen visas vanligt vis i ett popup-fönster när användaren interagerar med markören. I de flesta program visas popup-fönster när en användare klickar eller trycker på en markör. Men om du klickar och trycker måste användaren använda en mus och en pekskärm. En bra idé är att göra popup-fönster tillgängliga när du använder ett tangent bord. Den här funktionen kan uppnås genom att skapa en popup för varje data punkt och lägga till den på kartan. 

I följande exempel läses intressanta punkter på kartan med ett symbol lager och ett popup-fönster läggs till i kartan för varje orienterings punkt. En referens till varje popup-fönster lagras i egenskaperna för varje data punkt. Den kan också hämtas för en markör, t. ex. När en markör klickas. När du fokuserar på kartan kan du trycka på TABB-tangenten för att gå igenom alla popup-fönster på kartan.

<br/>

<iframe height='500' scrolling='no' title='Gör ett tillgängligt program' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>gör ett tillgängligt program</a> av Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) i <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Ytterligare tillgänglighets tips

Här följer några ytterligare tips för att göra ditt webb mappnings program mer tillgängligt.

- Om du visar många interaktiva punkt data på kartan bör du överväga att minska oredan och använda klustring. 
- Se till att färg kontrast förhållandet mellan text/symboler och bakgrunds färger är 4,5:1 eller mer.
- Behåll skärm läsaren (ARIA, Alt och title) meddelanden kort, beskrivande och meningsfulla meddelanden. Undvik onödiga jargong och akronymer.
- Försök att optimera meddelanden som skickas till skärm läsaren för att ge kortfattad meningsfull information som är enkel för användaren att sammanställa. Om du till exempel vill uppdatera skärm läsaren med hög frekvens, till exempel när kartan flyttas, bör du överväga följande punkter:
    - Vänta tills kartan har flyttat för att uppdatera skärm läsaren.
    - Begränsa uppdateringarna till en gång om några sekunder. 
    - Kombinera meddelanden tillsammans på ett logiskt sätt. 
- Undvik att endast förmedla information med hjälp av färger. Använd text, ikoner eller mönster för att komplettera eller ersätta färgen. Några saker att tänka på:
    - Om du använder ett bubbel-lager för att visa det relativa värdet mellan data punkter, kan du överväga att skala radien för varje bubbla, färga bubblan eller bådadera. 
    - Överväg att använda ett symbol lager med olika ikoner för olika mått kategorier, till exempel trianglar, stjärnor och fyr kanter. Symbol lagret stöder också skalning av ikonens storlek. En text etikett kan också visas.
    - Om du visar rad data kan bredden användas för att representera vikt eller storlek. Ett mönster för streck mat ris kan användas för att representera olika kategorier av linjer. Ett symbol lager kan användas tillsammans med en linje för att täcka över ikoner längs linjen. Det är praktiskt att använda en pil-ikon för att Visa linjens flöde eller riktning.
    - Om du visar polygon-data, kan ett mönster, till exempel ränder, användas som ett alternativ till färg. 
- Vissa visualiseringar som termiska kartor, panel lager och bild lager är inte tillgängliga för användare med nedsatt syn. Några saker att tänka på:
    - Låt skärm läsaren beskriva vad lagret visar när det läggs till i kartan. Om t. ex. ett väderleks panels lager visas, har skärm läsaren säga "väder polär data överlappar kartan".
- Begränsa mängden funktioner som kräver mus hovring. Dessa funktioner kommer inte att vara tillgängliga för användare som använder ett tangent bord eller en touch-enhet för att interagera med ditt program. Obs! det är fortfarande en bra idé att ha ett hov rings format för interaktivt innehåll, till exempel klicka bara ikoner, länkar och knappar.
- Försök att navigera i programmet med tangent bordet. Kontrol lera att Tab-ordningen är logisk.
- Om du skapar kortkommandon kan du försöka begränsa det till två nycklar eller färre. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hjälpmedel i Web SDK-modulerna.

> [!div class="nextstepaction"]
> [Hjälpmedel för rit verktyg](drawing-tools-interactions-keyboard-shortcuts.md)

Lär dig mer om att utveckla hjälpmedelsanpassade appar med Microsoft Learn:

> [!div class="nextstepaction"]
> [Hjälpmedel i praktiken utbildnings väg för digital Badge](https://ready.azurewebsites.net/learning/track/2940)

Ta en titt på dessa användbara hjälpmedels verktyg:
> [!div class="nextstepaction"]
> [Utveckla hjälpmedelsanpassade appar](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI – ARIA-översikt](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Verktyg för webb hjälpmedels utvärdering (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim färg kontrast kontroll](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Ingen kaffe formulerings Simulator](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
