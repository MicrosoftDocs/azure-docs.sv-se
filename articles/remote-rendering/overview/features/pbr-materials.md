---
title: PBR-material
description: Beskriver PBR-materialets typ.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e9908c106e57801cb1b7def8b3353a983cc97de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99591947"
---
# <a name="pbr-materials"></a>PBR-material

*PBR-material* är en av de [material typer](../../concepts/materials.md) som stöds i Azure Remote rendering. De används för [nät](../../concepts/meshes.md) som ska få realistiska belysning.

PBR står för **P** hysically **B** ased **R** endering och innebär att materialet beskriver de visuella egenskaperna för en yta på ett fysiskt plausible sätt, så att realistiska resultat kan utföras under alla ljus förhållanden. De flesta moderna spel motorer och innehålls skapande verktyg stöder PBR-material eftersom de anses vara den bästa uppskattningen av verkliga scenarier för real tids åter givning.

![Exempel modellen för hjälm-glTF återges av ARR](media/helmet.png)

PBR-material är inte en universell lösning, men. Det finns material som återspeglar färger på olika sätt beroende på visnings vinkeln. Till exempel vissa infrastrukturer eller bil lacker. Dessa typer av material hanteras inte av standarden PBR-modellen och stöds för närvarande inte av Azure Remote rendering. Detta inkluderar PBR-tillägg, till exempel *tunna filmer* (ytor med flera lager) och *tydliga beläggningar* (för bil färger).

## <a name="common-material-properties"></a>Vanliga material egenskaper

Dessa egenskaper är gemensamma för allt material:

* **albedoColor:** Den här färgen multipliceras med andra färger, till exempel *albedoMap* eller *:::no-loc text="vertex "::: färger*. Om *transparens* är aktiverat på ett material används alfa kanalen för att justera opaciteten, med en `1` helt ogenomskinlig och innebörd som är `0` helt transparent. Standardvärdet är White.

  > [!NOTE]
  > När ett PBR-material är helt transparent, som en helt ren ren glas, visar det fortfarande miljön. Ljusa fläckar som solen är fortfarande synliga i reflektionen. Detta skiljer sig för [färg material](color-materials.md).

* **albedoMap:** En [2D-struktur](../../concepts/textures.md) för albedo-värden per pixel.

* **alphaClipEnabled** och **AlphaClipThreshold:** om *alphaClipEnabled* är true, kommer alla pixlar där albedo alpha-värdet är lägre än *alphaClipThreshold* att ritas. Alfa Urklipp kan användas även utan att aktivera genomskinlighet och är mycket snabbare att återge. Alfa urklippta material är fortfarande långsammare att rendera än helt ogenomskinligt material, även om. Som standard är alpha-Urklipp inaktiverat.

* **textureCoordinateScale** och **textureCoordinateOffset:** skalan multipliceras med UV-texturens koordinater. förskjutningen läggs till i den. Kan användas för att sträcka ut och byta texturer. Standard skalan är (1, 1) och förskjutningen är (0, 0).

* **useVertexColor:** Om nätet innehåller :::no-loc text="vertex"::: färger och det här alternativet är aktiverat :::no-loc text="vertex"::: multipliceras maskens färg med *albedoColor* och *albedoMap*. Som standard är *useVertexColor* inaktive rad.

* **isDoubleSided:** Om Double-sidedness är inställt på Sant återges trianglar med det här materialet även om kameran tittar på bak sidorna. För Materials belysning i PBR-material beräknas också korrekt för bak ansikten. Som standard är det här alternativet inaktiverat. Se även [ :::no-loc text="Single-sided"::: åter givning](single-sided-rendering.md).

* **TransparencyWritesDepth:** Om TransparencyWritesDepth-flaggan är inställd på materialet och materialet är transparent, kommer objekt som använder det här materialet också att bidra till den slutliga djup bufferten. Se flaggan PBR material *transparent* i nästa avsnitt. Aktivering av den här funktionen rekommenderas om ditt användnings fall behöver en mer plausible av [försenad fas omprojektion](late-stage-reprojection.md) av helt transparenta scener. För blandade ogenomskinliga/transparenta scener kan den här inställningen leda till implausible eller omprojektions-artefakter. Därför är standardinställningen och den rekommenderade inställningen för allmänt användnings fall att inaktivera den här flaggan. De skrivna djupen tas från djup skiktet per bild punkt i objektet som är närmast kameran.

* **FresnelEffect:** Med den här material flaggan kan additiva [Fresnel påverka](../../overview/features/fresnel-effect.md) respektive material. Utseendet på effekten styrs av de andra Fresnel-parametrarna som beskrivs i följande avsnitt. 

* **FresnelEffectColor:** Fresnel färg som används för det här materialet. Endast viktigt när Fresnel Effect-biten har angetts för det här materialet (se ovan). Den här egenskapen styr bas färgen för Fresnel Skin (se [Fresnel-effekter](../../overview/features/fresnel-effect.md) för en fullständig förklaring). För närvarande är RGB-kanalens värden viktiga och alfa värdet ignoreras.

* **FresnelEffectExponent:** Fresnel-exponenten som används för det här materialet. Endast viktigt när Fresnel Effect-biten har angetts för det här materialet (se ovan). Den här egenskapen styr spridningen av Fresnel Skin. Det minsta värdet 0,01 orsakar ett uppslag över hela objektet. Det maximala värdet 10,0 begränsar Skin till de mest gracing kanterna som visas.

## <a name="pbr-material-properties"></a>Egenskaper för PBR-material

Kärn idén med fysiskt baserad åter givning är att använda *BaseColor*, *Egenskaper* för maskin vara och *ojämnheter* för att emulera ett brett utbud av verkliga material. En detaljerad beskrivning av PBR är utöver den här artikelns omfattning. Mer information om PBR finns i [andra källor](http://www.pbr-book.org). Följande egenskaper är speciella för PBR-material:

* **baseColor:** I PBR-material kallas *albedo-färgen* som *bas färgen*. I Azure Remote rendering finns *albedo färg* -egenskapen redan i de gemensamma material egenskaperna, så det finns ingen ytterligare bas färgs egenskap.

* **grovhet** och **roughnessMap:** grovhet definierar hur grov eller utjämna ytan är. Hårda ytor belyser ljuset i fler riktningar än släta ytor, vilket gör att reflektioner suddigare snarare än skarpa. Värde intervallet är från `0.0` till `1.0` . När `roughness` det är lika med är utjämningen `0.0` skarp. När `roughness` likheter `0.5` blir det suddigt att reflekterar.

  Om både ett ojämnhets värde och en ojämnhets karta tillhandahålls, blir det slutliga värdet produkten av de två.

* **metallhet** och **metalnessMap:** i fysik motsvarar den här egenskapen om en yta är ledande eller dielectric. Ledande material har olika reflekterande egenskaper och de tenderar att reflekteras utan albedo-färg. I PBR-material påverkar den här egenskapen hur mycket en yta visar omgivande miljö. Värden sträcker `0.0` sig från till `1.0` . När `0.0` det är en albedo är färgen helt synlig och materialet ser ut som plast eller keramiskt material. När det är så är `0.5` det att det ser ut som målad metall. När `1.0` det är en yta är ytan nästan helt förlorat sin albedo-färg och visar bara omgivningen. Om t. ex. `metalness` är `1.0` och så `roughness` `0.0` ser en yta ut som verklig spegel.

  Om både ett värde för värde och en metall karta anges, är det slutliga värdet produkten av de två.

  ![Sfärer som återges med olika värden för metall och ojämnheter](./media/metalness-roughness.png)

  I bilden ovan ser klotet ut i det nedre högra hörnet ut som ett verkligt metall material, den nedre vänstra delen ser ut som keramisk eller plast. Albedo-färgen ändras också enligt fysiska egenskaper. Med ökande ojämnheter förlorar materialet reflektions skärpa.

* **normalMap:** En [Normal Karta](https://en.wikipedia.org/wiki/Normal_mapping) kan tillhandahållas för att simulera detaljerad information.

* **occlusionMap** och **aoScale:** [omgivande ocklusion](https://en.wikipedia.org/wiki/Ambient_occlusion) gör objekt med crevices att titta mer realistiskt genom att lägga till skuggor i Occluded områden. Ocklusion värde sträcker `0.0` sig från till `1.0` , där `0.0` betyder mörkhet (Occluded) och `1.0` innebär ingen Occlusions. Om en 2D-struktur anges som en ocklusion-karta, aktive ras den och *aoScale* fungerar som en multiplikator.

  ![Ett objekt som återges med och utan omgivande ocklusion](./media/boom-box-ao2.gif)

* **transparent:** För PBR-material finns det bara en genomskinlighets inställning: den är aktive rad eller inte. Opaciteten definieras av albedo-färgens alfa kanal. När den är aktive rad anropas en mer komplex åter givnings pipeline för att rita halv genomskinliga ytor. Azure-fjärrrendering implementerar OIT (True [order Dependent Transparency](https://en.wikipedia.org/wiki/Order-independent_transparency) ).

  Genomskinliga geometrier är dyra att återge. Om du bara behöver hål i en yta, till exempel för löv till ett träd, är det bättre att använda alfa Urklipp i stället.

  ![Kloter som återges med noll till fullständiga genomskinlighets ](./media/transparency.png) meddelanden i bilden ovan, hur den högra sfären är helt transparent, men reflektionen är fortfarande synlig.

  > [!IMPORTANT]
  > Om något material ska växlas från ogenomskinligt till transparent vid körning måste åter givningen använda *TileBasedComposition* [åter givnings läge](../../concepts/rendering-modes.md). Den här begränsningen gäller inte för material som konverteras som genomskinligt material att börja med.

## <a name="technical-details"></a>Teknisk information

Azure fjärrrendering använder Cook-Torrance mikroaspekt BRDF med GGX NDF, Schlick Fresnel och en GGX Smith-korrelerad Synlighets period med en Lambert diffusions period. Den här modellen är den som är i praktiken bransch standard för tillfället. Mer detaljerad information finns i den här artikeln: [fysiskt baserad åter givning-Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Ett alternativ till den *metall-och tuffa PBR-* modell som används i Azure Remote rendering är en *spegel-Glossiness PBR-* modell. Den här modellen kan representera en bredare mängd material. Men det är dyrare och fungerar vanligt vis inte bra för real tids fall.
Det är inte alltid möjligt att konvertera från *spegel-Glossiness* till *Metaly-grovhet* eftersom det finns *(diffusion, spegel)* värdepar som inte kan konverteras till *(BaseColor, metaly)*. Konverteringen i den andra riktningen är enklare och mer exakt eftersom alla *(BaseColor, metall)* par motsvarar väldefinierade *(diffusa, spegel)* par.

## <a name="api-documentation"></a>API-dokumentation

* [C# PbrMaterial-klass](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection. CreateMaterial ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ PbrMaterial-klass](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection:: CreateMaterial ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Nästa steg

* [Färgmaterial](color-materials.md)
* [Bakgrunder](../../concepts/textures.md)
* [Maskor](../../concepts/meshes.md)