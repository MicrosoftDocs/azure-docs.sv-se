---
title: Omprojektion av sena steg
description: Information om omprojektion av sena steg och hur du använder den.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: f0951415bba22a226dadb7f2a115cede451399bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205650"
---
# <a name="late-stage-reprojection"></a>Omprojektion av sena steg

LSR ( *sent Stage reprojection* ) är en maskin varu funktion som hjälper till att stabilisera hologram när användaren rör sig.

Statiska modeller förväntas visuellt bevara deras position när du flyttar runt dem. Om de verkar vara instabila kan detta inträffa i LSR-problem. Observera att ytterligare dynamiska transformeringar, t. ex. animationer och explosions visningar, kan maskera detta beteende.

Du kan välja mellan två olika LSR-lägen, nämligen plan **LSR** eller **djup LSR**. Vilken som är aktiv bestäms av om klient programmet skickar en djup buffert.

Båda LSR-lägena förbättrar hologram stabiliteten, även om de har olika begränsningar. Börja med att testa djupet LSR, eftersom det är utan tvekan ger bättre resultat i de flesta fall.

## <a name="choose-lsr-mode-in-unity"></a>Välj LSR-läge i Unity

I Unity-redigeraren går du till *:::no-loc text="File > Build Settings":::* . Välj *:::no-loc text="Player Settings":::* i det nedre vänstra hörnet och kontrol lera sedan under *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* om **:::no-loc text="Enable Depth Buffer Sharing":::** är markerat:

![Flagga för delning av djup buffert aktive rad](./media/unity-depth-buffer-sharing-enabled.png)

I så fall använder appen djup LSR, annars används Planning LSR.

## <a name="depth-lsr"></a>Djup LSR

För att djupet LSR ska fungera måste klient programmet tillhandahålla en giltig djup buffert som innehåller alla relevanta geometrier att överväga under LSR.

Djup LSR försöker stabilisera video ramen baserat på innehållet i den angivna djup bufferten. Som en följd av detta kan innehåll som inte har renderas till den, till exempel transparenta objekt, inte justeras av LSR och kan visa instabilitets-och omprojektions artefakter. 

Om du vill minska omprojektionen för transparenta objekt, kan du framtvinga Skriv djupet. Se material flaggan *TransparencyWritesDepth* för [färg](color-materials.md) -och [PBR](pbr-materials.md) -material. Observera dock att den visuella kvaliteten för transparent/ogenomskinlig objekt interaktion kan bli lidande när du aktiverar den här flaggan.

## <a name="planar-lsr"></a>Plan-LSR

Plan-LSR har ingen djup information per bild punkt, eftersom djupet LSR gör. I stället projiceras allt innehåll på grund av ett plan som du måste ange varje ram.

Plan LSR reprojicerar objekten på bästa sätt som ligger nära det angivna planet. Det ytterligare bort ett objekt är, desto mer instabilt kommer att se ut. Även om djupet LSR är bättre vid omprojektering av objekt vid olika djup, kan plan LSR fungera bättre för innehåll som justeras väl med ett plan.

### <a name="configure-planar-lsr-in-unity"></a>Konfigurera plan LSR i Unity

Plan parametrarna härleds från en så kallad *fokus punkt*, som du måste ange för varje bild ruta `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` . Se [API för Unity Focus Point](/windows/mixed-reality/focus-point-in-unity) för mer information. Om du inte anger en fokus punkt väljs en återställnings punkt åt dig. Den automatiska återställningen leder ofta till underoptimala resultat.

Du kan beräkna fokus punkten själv, men det kan vara bra att basera det på den som beräknas av värden för fjärrrendering. Anropa `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` för att hämta det. Du uppmanas att ange en koordinat-ram där du vill uttrycka fokus punkten. I de flesta fall vill du bara ge resultatet från `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` här.

Vanligt vis kan både klienten och värden återge innehåll som den andra sidan inte är medveten om, till exempel GRÄNSSNITTs element på klienten. Därför kan det vara klokt att kombinera fjär fokus punkten med en lokalt Beräknad.

Fokus punkterna som beräknas i två efterföljande ramar kan vara helt olika. Genom att använda dem som de är kan det leda till att hologram tycks hoppa runt. För att förhindra det här beteendet är interpolating mellan föregående och aktuella fokus punkter lämpligt.

## <a name="next-steps"></a>Nästa steg

* [Prestandafrågor på serversidan](performance-queries.md)