---
title: Kamera
description: Beskriver kamera inställningar och användnings fall
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594137"
---
# <a name="camera"></a>Kamera

För att säkerställa att lokalt och externt innehåll kan kombineras sömlöst, måste olika kamera egenskaper vara synkroniserade mellan server och klient. I synnerhet kamera omvandling och projektion. Till exempel måste det lokalt återgivna innehållet använda samma Camera-transformering och projektion som den senaste fjärran sluten bildades med.

![Lokal och fjärran sluten kamera](./media/camera.png)

I bilden ovan har den lokala kameran flyttats jämfört med den fjärran sluten ram som skickats av servern. Det innebär att lokalt innehåll måste återges från samma perspektiv som den fjärranslutna ramen och slutligen projiceras den resulterande bilden i det lokala kamera utrymmet, vilket förklaras i detalj i den [försenade skedet omprojektionen](late-stage-reprojection.md).

Fördröjningen mellan fjär och lokal åter givning innebär att alla ändringar av inställningarna tillämpas med en fördröjning. Ett nytt värde kan därför inte användas direkt i samma ram.

De nära och långt planet avstånden kan ställas in på kamera inställningarna. På HoloLens 2 definieras de återstående transformerings-och projektions data av maskin varan. När du använder [Skriv bords simuleringen](../../concepts/graphics-bindings.md)anges de via inmatad `Update` funktion.

De ändrade värdena kan användas lokalt så snart den första fjärran sluten ram anländer till dem. På HoloLens 2 tillhandahålls de data som ska användas för åter givning av *HolographicFrame* precis som i andra Holographic-program. Vid en [simulering av Skriv bordet](../../concepts/graphics-bindings.md)hämtas de via resultatet av `Update` funktionen.

## <a name="camera-settings"></a>Kamera inställningar

Följande egenskaper kan ändras i kamera inställningarna:

**Nära och långt plan:**

För att se till att inga ogiltiga intervall kan anges är **NearPlane** -och **FarPlane** -egenskaperna skrivskyddade och en separat funktion **SetNearAndFarPlane** finns för att ändra intervallet. Dessa data kommer att skickas till servern i slutet av ramen. När du anger dessa värden måste **NearPlane** vara mindre än **FarPlane**. Annars uppstår ett fel.

> [!IMPORTANT]
> I Union hanteras detta automatiskt när du ändrar huvud kamerans nära och ett långt plan.

**EnableDepth**:

Ibland är det bra att inaktivera bufferten för djupet i fjärravbildningen för fel sökning. Genom att inaktivera djupet stoppas även servern från att skicka djupgående data, vilket minskar bandbredden.

> [!TIP]
> I Union anges en fel söknings komponent som heter **EnableDepthComponent** som kan användas för att växla den här funktionen i redigerings gränssnittet.

**InverseDepth**:

> [!NOTE]
> Den här inställningen är endast viktig om `EnableDepth` är inställd på `true` . Annars har den här inställningen ingen effekt.

Djup buffertar registrerar vanligt vis z-värden i ett flytt ALS intervall på [0; 1], med 0 som anger djupet och 1 som anger djupet i det nära planet. Det är också möjligt att invertera det här intervallet och registrera djupet i intervallet [1; 0], det vill säga att djup planet blir 1 och djupet på planet blir 0. I allmänhet förbättrar den senare fördelningen av flytt ALS precisionen i det icke-linjära z-intervallet.

> [!WARNING]
> En vanlig metod är att invertera de nära planet-och långt plan-värdena i Camera-objekten. Det går inte att göra en Azure-fjärrrendering med ett fel när du försöker igen på `CameraSettings` .

API: t för Azure-fjärrrendering måste veta om den lokala åter givnings profilens djup konvention för att skapa fjärrdjup i den lokala djup bufferten. Om intervallet för djupet är [0; 1] lämnar du denna flagga som `false` . Om du använder en inverterad djup buffert med intervallet [1; 0] anger du `InverseDepth` flaggan till `true` .

> [!NOTE]
> För enhets enhet tillämpas rätt inställning redan av `RenderingConnection` så att du inte behöver göra några manuella åtgärder.

Du kan ändra kamera inställningarna på följande sätt:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API-dokumentation

* [C#-CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++-CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. Update-funktion](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: uppdaterings funktion](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Nästa steg

* [Grafik bindning](../../concepts/graphics-bindings.md)
* [Omprojektion av sena steg](late-stage-reprojection.md)