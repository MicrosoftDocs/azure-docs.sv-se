---
title: Vanliga frågor och svar
description: Vanliga frågor och svar om tjänsten Azure Object ankare.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 6de027f317072512bd9893303d3a79d0f34f3e3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749084"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Vanliga frågor och svar om objekt ankare i Azure

Objekt ankare i Azure gör det möjligt för ett program att identifiera ett objekt i den fysiska världen med en 3D-modell och uppskatta dess 6-DoF attityd.

Mer information finns i [Översikt över Azure Object ankare](overview.md).

## <a name="product-faq"></a>Vanliga frågor och svar om produkten
**F: vilka rekommendationer har du för objekt som ska användas?**

**A:** Vi rekommenderar följande egenskaper för objekt:

* 1-10 meter för varje dimension
* Icke-symmetrisk, med tillräckligt många variationer i geometrin
* Låga reflectivity (Matt ytor) med ljusa färger
* Station ära objekt
* Inga eller små mängder av återledande
* Rensa bakgrunder utan eller minimalt rörigt
* Det skannade objektet ska ha 1:1 matchning med den modell som du har tränat med

**F: vilka är de maximala objekt dimensionerna som kan bearbetas för modell inmatning?**

**A:** Varje dimension i en CAD-modell ska vara mindre än 10 meter.

**F: Vad är den maximala storlek som kan bearbetas för CAD-modellen?**

**A:** Modell filens storlek måste vara mindre än 150 MB.

**F: vilka CAD-format som stöds?**

**A:** Vi stöder för närvarande,,, `fbx` `ply` `obj` `glb` och `gltf` filtyper.

**F: Vad är gravitations riktningen och enheten som krävs av modell inmatnings tjänsten? Hur kan vi räkna ut dem?**

**A:** Gravitations riktningen är den nedåtriktade vektorn som pekar mot jorden. För CAD-modeller är gravitations riktningen vanligt vis motsatsen till en uppåt-riktning. I många fall är t. ex. + Z representerad, i vilket fall-Z eller `Vector3(0.0, 0.0, -1.0)` skulle representera gravitations riktningen. När du bestämmer vikten bör du inte bara överväga modellen, utan även i vilken orientering modellen kommer att visas under körningen. Om du försöker identifiera en stol i verkligheten på en plan yta kan det vara en vikt `Vector3(0.0, 0.0, -1.0)` . Men om stolen är i en 45-grad-lutning kan det vara en vikt `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` .

Gravitations riktningen kan vara en följd av ett 3D-återgivnings verktyg, till exempel [MeshLab](http://www.meshlab.net/).

Enheten representerar modellens mått enhet. Enheter som stöds kan hittas med hjälp av **Microsoft. Azure. ObjectAnchors.** inmatningen. Unit-uppräkning.

**F: hur lång tid tar det att mata in en CAD-modell?**

**A:** För en `ply` modell, vanligt vis 3-15 minuter. Om du skickar modeller i andra format måste du vänta 15-60 minuter beroende på fil storlek.

**F: vilka enheter stöder objekt ankare?**

**A:** HoloLens 2 

**F: vilken OS-version ska användas för att köra min HoloLens?**

**A:** OS Build 18363,720 eller senare, lanserat efter 12 mars 2020.

  Mer information finns i [Windows 10 mars 2020 Update](https://support.microsoft.com/help/4551762).

**F: hur lång tid tar det att identifiera ett objekt på HoloLens?**

**A:** Det beror på objektets storlek och skannings processen. Om du vill få snabbare identifiering kan du prova att följa metod tipsen för en grundlig genomsökning. För mindre objekt inom 2 meter i varje dimension kan identifieringen ske inom några sekunder. För större objekt, t. ex. en bil, bör användaren gå en full slinga runt objektet för att få en tillförlitlig identifiering, vilket innebär att identifieringen kan ta flera sekunder.

**F: Vad är bästa praxis vid användning av objekt ankare i ett HoloLens-program?**

**En**

 1. Utför en ögon kalibrering för att få bättre åter givning.
 2. Se till att rummet har omfattande visuell struktur och bättre belysning.
 3. Behåll objektet stationärt, utan att vara prydligt om möjligt.
 4. Du kan också rensa cacheminnet för [spatial mappning](https://docs.microsoft.com/windows/mixed-reality/spatial-mapping) på din HoloLens-enhet.
 5. Skanna objektet genom att gå runt det. Se till att det mesta av objektet observeras.
 6. Ange ett sökområde som är tillräckligt stort för att kunna behandla objektet.
 7. Objektet bör vara stillastående under identifieringen.
 8. Starta objekt identifiering och visualisera åter givningen utifrån uppskattad attityd.
 9. Lås identifierat objekt eller stoppa spårning när attityden är stabil och korrekt för att bevara batteri tiden.

**F: hur exakt är en uppskattad attityd?**

**A:** Det beror på objekt storlek, material, miljö osv. För små objekt kan den beräknade attityden vara inom 2 cm fel. För stora objekt, som en bil, kan felet vara upp till 2-8 cm.

**F: kan objekt ankar handtag flytta objekt?**

**A:** Vi stöder inte **kontinuerlig flytt** eller **dynamiska** objekt.

**F: kan objekts ankare hantera deformerade eller ledare?**

**A:** Delvis, beroende på hur mycket objekt form eller geometri ändringar som ändras på grund av deformeringen eller ledandet. Om objektets geometri ändrar ett parti, kan användaren skapa en annan modell för den konfigurationen och använda den för identifiering.

**F: hur många olika objekt kan objekts ankare identifiera samtidigt?**

**A:** Vi har för närvarande stöd för att identifiera en enda objekt modell i taget. 

**F: kan objekt ankare identifiera flera instanser av samma objekt modell?**

**A:** Ja, du kan identifiera upp till tre objekt av samma modell typ. Programmet kan anropa `ObjectObserver.DetectAsync` flera gånger med olika frågor för att identifiera flera instanser av samma modell.

**F: Vad ska jag göra om objekt ankaren runtime inte kan identifiera mitt objekt?**

**En**

* Se till att rummet har tillräckligt med texturer genom att lägga till några affischer.
* Skanna objektet mer fullständigt.
* Justera modell parametrarna enligt beskrivningen ovan.
* Ange en tätt avgränsnings ruta som sökområde som innehåller alla eller flest objekt.
* Rensa cacheminnet för spatial mappning och Genomsök objektet igen.
* Avbilda diagnostik och skicka data till oss.

**F: så här väljer du parametrar för objekt frågor?**

**En**

* Ange ett tätt Sök område för att helst ge det fullständiga objektet för att förbättra identifierings hastigheten och precisionen.
* Standardvärdet `ObjectQuery.MinSurfaceCoverage` från objekt modellen är vanligt vis lämpligt, annars använder ett mindre värde för att få en snabbare identifiering.
* Använd ett litet värde för `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` om objektet förväntas vara upp till höger.
* En app bör alltid använda en `1:1` objekt modell för identifiering. Den uppskattade skalningen bör vara nära 1% fel. En app kan ställa in `ObjectQuery.MaxScaleChange` på `0` eller `0.1` inaktivera eller aktivera skalnings uppskattning och utvärdera instansen på ett kvalitativt sätt.

**F: Hur gör jag för att hämta data ankare för objekt från HoloLens?**

**A:** Programmet kan ange platsen för diagnostiska arkiv. Exempel appen objekt ankare skriver diagnostik till mappen **TempState** .

**F: Varför justeras inte käll modellen med det fysiska objektet när den attityd som returnerades av objekt Fästarna Unity SDK används?**

**A:** Unity kan ändra koordinatsystemet när en objekt modell importeras. Till exempel inverterar Object-koordinaterna Z-axeln när du konverterar från en högerhänt till ett överordnat koordinatsystem, men Unit kan använda ytterligare en rotation av X-eller Y-axeln. En utvecklare kan bestämma denna ytterligare rotation genom att visualisera och jämföra koordinatsystemet.

**F: har du stöd för 2D?**

**A:** Eftersom vi är Geometry-baserade stöder vi bara 3D.

**F: kan du skilja mellan samma modell i olika färger?**

**A:** Eftersom våra algoritmer är geometribaserade fungerar inte olika färger i samma modell annorlunda under identifieringen.

**F: kan jag använda objekt ankare utan Internet anslutning?**

**En** 
* För modell inmatning och utbildning krävs anslutning som detta inträffar i molnet.
* Runtime-sessioner är helt på enhet och kräver inte anslutning eftersom alla beräkningar sker på HoloLens 2.

## <a name="privacy-faq"></a>Vanliga frågor och svar om sekretess
**F: Hur lagrar Azure Object ankare data?**

**A:** Vi lagrar bara systemmetadata, vilket är krypterat i vila med en Microsoft-hanterad data krypterings nyckel.
