---
title: Felsöka
description: Felsökningsinformation för Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 8f0fb9ab5c53c3fd1bfb32ac7b112a116301cba7
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575351"
---
# <a name="troubleshoot"></a>Felsöka

På den här sidan visas vanliga problem som stör Azure Remote Rendering och sätt att lösa dem.

## <a name="cant-link-storage-account-to-arr-account"></a>Det går inte att länka lagringskontot till ARR-kontot

Ibland visas [inte Remote Rendering](../how-tos/create-an-account.md#link-storage-accounts) under länkningen av ett lagringskonto. Du kan åtgärda problemet genom att gå till ARR-kontot i Azure Portal och **välja Identitet** under **gruppen** Inställningar till vänster. Kontrollera att **Status** är inställt på **På**.
![Felsökning av Unity-bildruta](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Klienten kan inte ansluta till servern

Kontrollera att dina brandväggar (på enheten, inuti routrar osv.) inte blockerar de portar som anges i [Systemkrav](../overview/system-requirements.md#network-firewall).

## <a name="error-disconnected-videoformatnotavailable"></a>Fel `Disconnected: VideoFormatNotAvailable` ' '

Kontrollera att GPU:n stöder videoavkodning av maskinvara. Se [Utvecklingsdator](../overview/system-requirements.md#development-pc).

Om du arbetar på en bärbar dator med två GPU:er, är det möjligt att GPU:n som du kör på som standard inte tillhandahåller funktioner för videoavkodning av maskinvara. I så fall kan du försöka tvinga appen att använda den andra GPU:n. Detta är ofta möjligt i GPU-drivrutinsinställningarna.

## <a name="retrieve-sessionconversion-status-fails"></a>Det går inte att hämta sessions-/konverteringsstatus

Om REST API kommandona skickas för ofta kommer servern att begränsa och returnera fel så småningom. HTTP-statuskoden i begränsningsfallet är 429 ("för många begäranden"). Som tumregel bör det finnas en fördröjning på **5–10 sekunder mellan efterföljande anrop**.

Observera att den här gränsen inte bara påverkar REST API anropas direkt utan även deras C#/C++-motsvarigheter, till exempel `Session.GetPropertiesAsync` , `Session.RenewAsync` eller `Frontend.GetAssetConversionStatusAsync` .

Om du upplever begränsning på serversidan ändrar du koden för att göra anropen mindre ofta. Servern återställer begränsningstillståndet varje minut, så det är säkert att köra koden igen efter en minut.

## <a name="h265-codec-not-available"></a>H265-codec är inte tillgänglig

Det finns två orsaker till varför servern kan vägra att ansluta med ett `codec not available` fel.

**H265-codec är inte installerad:**

Se först till att installera **HEVC-videotilläggen** som nämns i [avsnittet Programvara](../overview/system-requirements.md#software) i systemkraven.

Om du fortfarande stöter på problem kontrollerar du att ditt grafikkort stöder H265 och att du har den senaste grafikdrivrutinen installerad. Se avsnittet [Utvecklingsdator](../overview/system-requirements.md#development-pc) i systemkraven för leverantörsspecifik information.

**Codec är installerat, men kan inte användas:**

Orsaken till det här problemet är en felaktig säkerhetsinställning på DLL:erna. Det här problemet uppstår inte när du försöker titta på videor som kodats med H265. Att installera om codec löser inte heller problemet. Utför i stället följande steg:

1. Öppna ett **PowerShell med administratörsrättigheter** och kör

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Kommandot bör mata ut `InstallLocation` för codec, ungefär så här:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Öppna mappen i Utforskaren i Windows
1. Det bör finnas en **x86-** och **en x64-undermapp.** Högerklicka på en av mapparna och välj **Egenskaper**
    1. Välj fliken **Säkerhet** och klicka på **knappen Avancerade** inställningar
    1. Klicka **på** Ändra för **ägaren**
    1. Skriv **Administratörer** i textfältet
    1. Klicka **på Kontrollera namn** och **OK**
1. Upprepa stegen ovan för den andra mappen
1. Upprepa också stegen ovan för varje DLL-fil i båda mapparna. Det bör finnas fyra DLL:er helt och hållet.

Kontrollera att inställningarna nu är korrekta genom att göra detta för var och en av de fyra DLL:erna:

1. Välj **Egenskaper > Security > Redigera**
1. Gå igenom listan över alla **grupper/användare** och se till att var och en har  rätt & Read **& Execute** (bockmarkeringen i kolumnen Tillåt måste markeras)

## <a name="low-video-quality"></a>Låg videokvalitet

Videokvaliteten kan komprometteras antingen av nätverkskvalitet eller den saknade H265-video-codecen.

* Se stegen för att [identifiera nätverksproblem.](#unstable-holograms)
* Se [systemkraven för](../overview/system-requirements.md#development-pc) att installera den senaste grafikdrivrutinen.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Video som spelas in med MRC återspeglar inte liveupplevelsens kvalitet

En video kan spelas in på HoloLens via [Inspelning av mixad verklighet (MRC).](/windows/mixed-reality/mixed-reality-capture-for-developers) Den resulterande videon har dock sämre kvalitet än liveupplevelsen av två skäl:
* Videoramfrekvensen är begränsad till 30 Hz i stället för 60 Hz.
* Videobilderna går inte [](../overview/features/late-stage-reprojection.md) igenom bearbetningssteget för senare steg, så videon verkar vara ännu mer oskadlig.

Båda är inbyggda begränsningar i inspelningstekniken.

## <a name="black-screen-after-successful-model-loading"></a>Svart skärm efter lyckad modellläsning

Om du är ansluten till renderingskörningen och har läst in en modell, men bara ser en svart skärm efteråt, kan detta ha några olika orsaker.

Vi rekommenderar att du testar följande innan du gör en mer djupgående analys:

* Är H265-codec installerad? Även om det bör finnas en återställning till H264-codecen har vi sett fall där den här återställningen inte fungerade korrekt. Se [systemkraven för](../overview/system-requirements.md#development-pc) att installera den senaste grafikdrivrutinen.
* När du använder ett Unity-projekt  stänger du Unity, tar bort det tillfälliga biblioteket och *obj-mapparna* i projektkatalogen och läser in/skapar projektet igen. I vissa fall orsakade cachelagrade data att exemplet inte fungerade korrekt av någon uppenbar anledning.

Om de här två stegen inte hjälper måste du ta reda på om bildrutor tas emot av klienten eller inte. Detta kan efterfrågas programmatiskt enligt förklaringen i kapitlet [prestandafrågor på serversidan.](../overview/features/performance-queries.md) `FrameStatistics struct`har en medlem som anger hur många bildrutor som har tagits emot. Om det här antalet är större än 0 och ökar med tiden tar klienten emot faktiska bildrutor från servern. Därför måste det vara ett problem på klientsidan.

### <a name="common-client-side-issues"></a>Vanliga problem på klientsidan

**Modellen överskrider gränserna för den valda virtuella datorn, särskilt det maximala antalet polygoner:**

Se specifika [storleksgränser för servern.](../reference/limits.md#overall-number-of-polygons)

**Modellen är inte inuti kameran frustum:**

I många fall visas modellen korrekt men finns utanför kameran frustum. En vanlig orsak är att modellen har exporterats med en mycket off-center-pivot så att den klipps av kamerans avlägsna cpunktsplan. Det hjälper till att köra frågor mot modellens begränsningsruta programmatiskt och visualisera rutan med Unity som en linjeruta eller skriva ut dess värden i felsökningsloggen.

Konverteringsprocessen genererar dessutom en [json-utdatafil tillsammans](../how-tos/conversion/get-information.md) med den konverterade modellen. Om du vill felsöka problem med modellpositionering är det värt att `boundingBox` titta på posten i avsnittet [outputStatistics:](../how-tos/conversion/get-information.md#the-outputstatistics-section)

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Begränsningsrutan beskrivs som en - och `min` `max` -position i 3D-utrymmet i meter. Så en koordinat på 1000,0 innebär att den är 1 kilometer från ursprunget.

Det kan finnas två problem med den här begränsningsrutan som leder till osynlig geometri:
* **Rutan kan vara långt utanför mitten, så** objektet klipps helt och hållet på grund av ett långt planurklipp. Värdena `boundingBox` i det här fallet skulle se ut så här: , med en stor `min = [-2000, -5,-5], max = [-1990, 5,5]` förskjutning på x-axeln som exempel här. Lös den här typen av problem genom att aktivera `recenterToOrigin` alternativet i [modellkonverteringskonfigurationen](../how-tos/conversion/configure-model-conversion.md).
* **Boxen kan centreras men vara för stor.** Det innebär att även om kameran startar i mitten av modellen klipps dess geometri i alla riktningar. Typiska `boundingBox` värden i det här fallet skulle se ut så här: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Orsaken till den här typen av problem är vanligtvis ett matchningsfel för enhetsskalning. För att kompensera anger du [ett skalningsvärde under konverteringen](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) eller markerar källmodellen med rätt enheter. Skalning kan också tillämpas på rotnoden när modellen läses in vid körning.

**Unity-renderingspipelinen innehåller inte renderings-hookarna:**

Azure Remote Rendering ansluter till Unity-renderingspipelinen för att göra ramsammansättning med videon och för att göra omprojekteringen. Om du vill kontrollera att dessa hookar finns öppnar du menyn *:::no-loc text="Window > Analysis > Frame debugger":::* . Aktivera det och kontrollera att det finns två poster för `HolographicRemotingCallbackPass` i pipelinen:

![Unity-renderingspipeline](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Checkerboard-mönstret återges efter modellläsning

Om den renderade bilden ser ut så här: Skärmbild som visar ett ![ rutnät med svarta och vita rutor med en verktygsmeny.](../reference/media/checkerboard.png)
sedan når renderaren [polygongränserna för standardkonfigurationsstorleken](../reference/vm-sizes.md). Du kan åtgärda detta genom att **antingen växla** till premiumkonfigurationsstorlek eller minska antalet synliga polygoner.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Den renderade bilden i Unity är upp och ned

Se till att följa [Unity-självstudien: Visa fjärrmodeller](../tutorials/unity/view-remote-models/view-remote-models.md) exakt. En upp och ned-bild indikerar att Unity krävs för att skapa ett renderingsmål utanför skärmen. Det här beteendet stöds inte för närvarande och skapar en enorm prestandapåverkan på HoloLens 2.

Orsakerna till det här problemet kan vara MSAA, THEA eller aktivera efterbearbetning. Kontrollera att profilen med låg kvalitet har valts och angetts som standard i Unity. Det gör du genom att *gå till > projektinställningar... > Quality*.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Unity-kod som använder Remote Rendering-API:et kompileras inte

### <a name="use-debug-when-compiling-for-unity-editor"></a>Använda Felsökning vid kompilering för Unity-redigeraren

Växla *versionstyp för* Unity-lösningen till **Felsök.** När du testar ARR i Unity-redigeraren `UNITY_EDITOR` är definiera endast tillgängligt i "felsöknings"-byggen. Observera att detta inte är relaterat till den byggtyp som [används](../quickstarts/deploy-to-hololens.md)för distribuerade program , där du bör föredra versioner av versionerna.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Kompilera fel vid kompilering av Unity-exempel för HoloLens 2

Vi har sett falska fel när vi försöker kompilera Unity-exempel (snabbstart, ShowCaseApp, ..) för HoloLens 2. Visual Studio klagar på att det inte går att kopiera vissa filer trots att de finns där. Om du får det här problemet:
* Ta bort alla temporära Unity-filer från projektet och försök igen. Stäng Unity, ta bort det tillfälliga biblioteket *och* *obj-mapparna* i projektkatalogen och läs in/skapa projektet igen.
* Kontrollera att projekten finns i en katalog på disken med en någorlunda kort sökväg, eftersom kopieringssteget ibland verkar få problem med långa filnamn.
* Om det inte hjälper kan det vara så att MS Sense stör kopieringssteget. Om du vill konfigurera ett undantag kör du det här registerkommandot från kommandoraden (kräver administratörsrättigheter):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Arm64-byggen för Unity-projekt misslyckas eftersom AudioPluginMsHRTF.dll saknas

for `AudioPluginMsHRTF.dll` Arm64 lades till i *Windows Mixed Reality-paketet (com.unity.xr.windowsmr.metro)* i version 3.0.1.  Kontrollera att du har version 3.0.1 eller senare installerat via Unity Package Manager. Från menyraden i Unity går du till *Windows > Package Manager* och letar efter *Windows Mixed Reality* paketet.

## <a name="native-c-based-application-does-not-compile"></a>Det interna C++-baserade programmet kompileras inte

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Felet "Det gick inte att hitta biblioteket" för UWP-programmet eller dll-filen

I C++ NuGet-paketet finns det en fil `microsoft.azure.remoterendering.Cpp.targets` som definierar vilken binär smak som ska användas. För att `UWP` identifiera kontrollerar du villkoren i filkontrollen för `ApplicationType == 'Windows Store'` . Därför måste du se till att den här typen anges i projektet. Det bör vara fallet när du skapar ett UWP-program eller dll-Visual Studio i projektguiden.

## <a name="unstable-holograms"></a>Instabila hologram

Om renderade objekt verkar röra sig tillsammans med huvudförflyttningar kan du stöta på problem med LSR *(Late Stage Reprojection).* Se avsnittet om [reprojection av sent](../overview/features/late-stage-reprojection.md) stadium för vägledning om hur du ska komma tillväga en sådan situation.

En annan orsak till instabila hologram (vingling, snytning, jittering eller hoppande hologram) kan vara dålig nätverksanslutning, i synnerhet otillräcklig nätverksbandbredd eller för hög svarstid. En bra indikator för kvaliteten på nätverksanslutningen är [prestandastatistikvärdet](../overview/features/performance-queries.md) `ServiceStatistics.VideoFramesReused` . Återanvända bildrutor indikerar situationer där en gammal videoram behövde återanvändas på klientsidan eftersom det inte fanns någon ny videoram tillgänglig, till exempel på grund av paketförlust eller variationer i nätverksfördröjningen. Om `ServiceStatistics.VideoFramesReused` är ofta större än noll indikerar detta ett nätverksproblem.

Ett annat värde att titta på är `ServiceStatistics.LatencyPoseToReceiveAvg` . Den bör konsekvent vara under 100 ms. Att se högre värden kan tyda på att du är ansluten till ett datacenter som är för långt bort.

En lista över möjliga åtgärder finns i riktlinjerna [för nätverksanslutning.](../reference/network-requirements.md#guidelines-for-network-connectivity)

## <a name="z-fighting"></a>Z-strider

Arr erbjuder [riskreduceringsfunktioner z-mitigation](../overview/features/z-fighting-mitigation.md), men z-fighting kan fortfarande visas i scenen. Syftet med den här guiden är att felsöka de återstående problemen.

### <a name="recommended-steps"></a>Rekommenderade åtgärder

Använd följande arbetsflöde för att minimera z-fighting:

1. Testa scenen med standardinställningarna för ARR (z-mitigation mitigation on)

1. Inaktivera riskreducering med z-åtgärd via dess [API](../overview/features/z-fighting-mitigation.md) 

1. Ändra kameran nära och långt plan till ett närmre intervall

1. Felsök scenen via nästa avsnitt

### <a name="investigating-remaining-z-fighting"></a>Undersöka återstående z-strider

Om ovanstående steg har uttömts och de återstående z-striderna är oacceptabla måste den underliggande orsaken till z-striderna undersökas. Som vi angav på funktionssidan [z-mitigation](../overview/features/z-fighting-mitigation.md)finns det två huvudsakliga orsaker till z-korsning: förlust av djupprecision längst bort i djupintervallet och ytor som övervinnlar samtidigt som de är coplanar. Förlust av djupprecision är en matematisk eventualitet och kan bara undvikas genom att följa steg 3 ovan. Coplanar-ytor indikerar ett fel i källtillgången och är bättre fasta i källdata.

ARR har en funktion för att avgöra om ytor kan z-match: [Checkerboard som markerar](../overview/features/z-fighting-mitigation.md). Du kan också visuellt avgöra vad som orsakar z-striderna. Följande första animering visar ett exempel på djupprecision i avståndet, och den andra visar ett exempel på nästan coplanar-ytor:

![Animering som visar ett exempel på förlust av djupprecision i avståndet.](./media/depth-precision-z-fighting.gif)  ![Animering som visar ett exempel på nästan coplanära ytor.](./media/coplanar-z-fighting.gif)

Jämför de här exemplen med z-striderna för att fastställa orsaken eller om du vill följa det här stegvisa arbetsflödet:

1. Placera kameran ovanför z-striderna för att titta direkt på ytan.
1. Flytta kameran långsamt bakåt, bort från ytan.
1. Om z-striderna är synliga hela tiden är ytorna perfekt coplanar. 
1. Om z-striderna är synliga för det mesta är ytan nästan coplanär.
1. Om z-striderna bara syns långt bort är orsaken bristande djupprecision.

Coplanar-ytor kan ha ett antal olika orsaker:

* Ett objekt duplicerades av det exporterande programmet på grund av ett fel eller olika arbetsflödesmetoder.

    Kontrollera dessa problem med respektive program- och programsupport.

* Ytor dupliceras och vändas så att de ser dubbelsidig ut i återgivningar som använder ansikts- eller baksidor.

    Import via [modellkonverteringen](../how-tos/conversion/model-conversion.md) avgör modellens huvudsida. Dubbelsidighet antas vara standardinställningen. Ytan renderas som en tunn vägg med fysiskt korrekt belysning från båda sidor. Enkelsidighet kan underförstådas av flaggor i källtillgången eller uttryckligen framtvingas under [modellkonverteringen](../how-tos/conversion/model-conversion.md). Dessutom, men om du vill, [kan det enkelsidig läge](../overview/features/single-sided-rendering.md) anges till "normal".

* Objekten överser källtillgångarna.

     Objekt som transformeras på ett sätt som vissa av deras ytor överlappar skapar också z-strider. Att transformera delar av scenträdet i den importerade scenen i ARR kan också skapa det här problemet.

* Ytor är målmedvetet författade att röra, till exempel deskaler eller text på väggar.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Grafikartefakter med multipass-stereoåtergivning i interna C++-appar

I vissa fall kan anpassade interna C++-appar som använder ett multipass-stereoåtergivningsläge för lokalt innehåll (återgivning till vänster och höger öga i separata pass) efter anrop av [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) utlösa en drivrutinsbugg. Buggen resulterar i icke-deterministiska bugeriseringsfel, vilket gör att enskilda trianglar eller delar av trianglar i det lokala innehållet försvinner slumpmässigt. Av prestandaskäl rekommenderar vi ändå att du återger lokalt innehåll med en modernare stereoåtergivningsteknik med ett enda pass, till exempel genom att **använda SV_RenderTargetArrayIndex**.

## <a name="conversion-file-download-errors"></a>Nedladdningsfel för konverteringsfil

Konverteringstjänsten kan stöta på fel vid nedladdning av filer från Blob Storage på grund av sökvägslängdsbegränsningar som har införts av Windows och tjänsten. Sökvägar och filnamn i bloblagringen får inte överskrida 178 tecken. Till exempel givet `blobPrefix` ett av som är `models/Assets` 13 tecken:

`models/Assets/<any file or folder path greater than 164 characters will fail the conversion>`

Konverteringstjänsten laddar ned alla filer som anges under `blobPrefix` , inte bara de filer som används i konverteringen. Filer/mappar som orsakar problem kan vara mindre uppenbara i dessa fall, så det är viktigt att kontrollera allt som finns i lagringskontot under `blobPrefix` . Se exempelindata nedan för vad som hämtas.
``` json
{
  "settings": {
    "inputLocation": {
      "storageContainerUri": "https://contosostorage01.blob.core.windows.net/arrInput",
      "blobPrefix": "models/Assets",
      "relativeInputAssetPath": "myAsset.fbx"
    ...
  }
}
```

```
models
├───Assets
│   │   myAsset.fbx                 <- Asset
│   │
│   └───Textures
│   |       myTexture.png           <- Used in conversion
│   |
|   └───MyFiles
|          myOtherFile.txt          <- File also downloaded under blobPrefix      
|           
└───OtherFiles
        myReallyLongFileName.txt    <- Ignores files not under blobPrefix             
```
## <a name="next-steps"></a>Nästa steg

* [Systemkrav](../overview/system-requirements.md)
* [Nätverkskrav](../reference/network-requirements.md)