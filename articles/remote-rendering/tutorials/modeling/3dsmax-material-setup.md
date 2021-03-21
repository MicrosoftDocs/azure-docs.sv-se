---
title: Konfigurera PBR-material i Max 3DS
description: Förklarar hur du ställer in fysiskt baserat åter givnings material i 3ds Max och exporterar dem till FBX-format.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020287"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Självstudie: Konfigurera fysiskt baserat åter givnings material i 3ds Max

## <a name="overview"></a>Översikt
I den här självstudien får du lära dig att:

>[!div class="checklist"]
>
> * Tilldela material med avancerad belysning till objekt i en scen.
> * Hantera instanser av objekt och material.
> * Exportera en scen till FBX-format och välj viktiga alternativ.

> [!Note]
> Proceduren som beskrivs i den här självstudien fungerar i 3ds Max 2019 och 3ds Max 2020.
> En ändring i hur 3ds Max 2021 exporterar ojämnheter innebär att normala kartor inte hittas av konverterings tjänsten om den versionen används.

Att skapa ett [PBR-material (fysiskt baserat åter givning)](../../overview/features/pbr-materials.md) i 3ds Max är en enkel uppgift. Det liknar på många sätt att installera PBR i andra appar för innehålls skapande som Maya. Den här självstudien är en guide till grundläggande PBR Shader-installation och FBX export för Azure-fjärrrendering-projekt.

Exempel scenen i den här självstudien innehåller ett antal polygon-Box-objekt. De har tilldelats olika material, t. ex. trä, metall, målad metall, plast och gummi. Varje material innehåller i stort sett alla eller de flesta av följande texturer:

* **Albedo**, som är materialets färg karta och kallas även för **diffusion** och **BaseColor**.
* **Metall**, som avgör om ett material är metallisk och vilka delar som är metalliska. 
* **Grovhet**, som avgör hur grov eller utjämna en yta är.
Det påverkar också skärpan eller blurriness av reflekterande och högdagrar på en yta.
* **Normal**, som lägger till information på en yta utan att lägga till fler polygoner. Exempel på detaljer är pitting och indrag på en metall yta eller kärna i trä.
* **Omgivande ocklusion**, som används för att lägga till mjuka skuggor och kontakt skuggor i en modell. Det är en grå Skale karta som visar vilka områden i modellen som får fullständig belysning (vit) eller en fullständig nyans (svart).

## <a name="prepare-the-scene"></a>Förbered scenen
I 3ds Max är processen för att ställa in ett PBR-material som följer.

För att starta ska vi skapa ett antal Box-objekt som representerar en annan typ av material.

>[!TIP]
>Det är värt att notera innan du börjar skapa till gångar för fjär rendering som använder mätare för mått.  
>
>Därför är det en bra idé att ställa in din scens systemen het på mätare. Det är också en bra idé att ange **enheter** till mätare i FBX export inställningar när du exporterar en scen.

Följande skärm bild visar stegen för att ställa in system enheter på mätare i max. 

1. På huvud menyn går du till **Anpassa** enheter konfigurera installations programmet för  >    >  **system enheter**. I **system enhets skala** väljer du **mätare**: ![ skärm bild som visar hur du ställer in system enheter.](media/3dsmax/system-units.jpg)

1. Nu kan vi börja skapa modeller. I exempel scenen skapar vi flera Box-objekt, som var och en representerar en annan material typ. Till exempel metall, gummi och plast. 

   >[!TIP]
   >När du skapar till gångar är det en bra idé att namnge dem på rätt sätt. Detta gör dem lättare att hitta senare om scenen innehåller många objekt.

1. Byt namn på objekten så som visas på följande skärm bild: 

   ![Skärm bild som visar hur du byter namn på objekt.](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Tilldela material

Nu när vi har några objekt i vår scen, i det här fallet ett antal kuber, kan vi starta PBR-installationen:

1. I huvud verktygsfältet väljer du ikonen **material redigerare** , som du ser i följande skärm bild. Du kan också välja **M** på tangent bordet för att öppna redigeraren. Material redigeraren har två lägen som du kan välja i listan **lägen** : **kompakt material redigerings** läge och redigerings läge för Skriv **material** . Eftersom den här scenen är relativt enkel använder vi kompakt läge.

1. I material redigeraren ser du ett antal sfärer. Dessa sfärer är materialet. Vi tilldelar ett sådant material till varje objekt (varje ruta) i scenen. Om du vill tilldela materialet väljer du först ett av objekten i huvud visnings området. Välj sedan den första sfären i material redigeraren. När det har tilldelats ett objekt markeras det valda materialet, som du ser i nästa bild.

1. Välj **Tilldela material att välja** enligt vad som visas. Materialet tilldelas nu det valda objektet.

   ![Skärm bild som visar hur du tilldelar material.](media/3dsmax/assign-material.jpg)

    I material redigeraren kan du välja bland ett brett urval av material typer, beroende på dina behov. Normalt är material typen **standard** inställd som standard. Det här materialet är ett grundläggande material som inte passar för PBR-installationen. Vi måste ändra material typen till ett PBR-material. Fysiskt material är det föredragna 3ds Max-materialet för Azure-fjärråtergivnings projekt.

1. Välj fliken **standard** i material redigeraren. I **webbläsaren material/karta** väljer du **fysiskt material**. Den här åtgärden konverterar det tilldelade **standard** materialet till ett fysiskt PBR-material.

   ![Skärm bild som visar hur du ändrar materialet.](media/3dsmax/physical-material.jpg)

    I material redigeraren ser du nu egenskaperna för det fysiska materialet, som du ser i följande skärm bild. Nu kan du börja tilldela till gången strukturer.

   ![Skärm bild som visar listan över texturer.](media/3dsmax/textures-list.jpg)

Som du ser finns det en mängd olika kartor och texturer som du kan lägga till i materialet. I den här självstudien använder vi bara fem textur fack i materialet.

>[!TIP]
>Det är en bra idé att namnge ditt material på lämpligt sätt, som du ser i föregående skärm bild.

Hur du genererar strukturer kan variera beroende på preferenser eller användning. Du kanske till exempel vill använda strukturerade texturer som kan användas för alla till gångar. Eller så kanske du behöver specifika delar av ett projekt eller till gång för att få egna anpassade uppsättningar med strukturer. Du kanske vill använda allmänna strukturer som du kan ansluta online. Du kan också skapa dem själv i appar som Photoshop, Quixel Suite och substans Suite.

Innan vi börjar tilldela strukturer måste du tänka på till gångens textur koordinater (UVW). Det är en bra idé när du använder en textur i en modell för att se till att modellen är unwrap. (Texturer visas inte korrekt utan korrekt UV-avfigurning.) Det är särskilt viktigt för våra syfte eftersom vi vill använda en ocklusion-karta (AO) i vår modell. Till skillnad från Stingray Shader i Maya har det fysiska materialet i 3ds Max ingen dedikerad AO textur plats. Vi använder därför AO-kartan på en annan plats. Om du vill att den ska kunna användas separat från andra texturer (till exempel för att lägga till texturer) ska vi tilldela den en egen UVW-kart kanal. 

Vi börjar med att tilldela en UVW-modifierare till modellen, som du ser i följande skärm bild. 

- I redigeraren för valda objekt egenskaper väljer du listan med ändringar. Rulla nedåt i list rutan som visas och välj **unwrap UVW**. Den här åtgärden tillämpar en UVW-modifierare för till gången.
![Skärm bild som visar hur du väljer unwrap UVW.](media/3dsmax/unwrap-modifier.jpg)

  Kart kanalen har angetts till 1. Du använder vanligt vis huvud nedbrytningen i kart kanal 1. I det här fallet har objektet inte omslutits utan överlappande textur koordinater (UV).
![Skärm bild som visar UVW (Unwrapped textur koordinater).](media/3dsmax/unwrapped-uvw.jpg)

Nästa steg är att skapa en andra UV-kart kanal.

1. Stäng UV-redigeraren om den är öppen. I avsnittet **kanal** på menyn **Redigera UVs** ändrar du kanal numret till **2**. Kart kanal 2 är den förväntade kanalen för AO Maps. 

1. I dialog rutan **kanal ändrings varning** kan du antingen **Flytta** befintliga UVs i kanal 1 till den nya kanalen 2 eller **överge** den befintliga UVs som skapar en ny UV-omslutning automatiskt. Välj endast **överge** om du planerar att skapa en ny UV-brytning för Ao-kartan som skiljer sig från UVs i kartans kanal 1. (Om du till exempel vill använda textur strukturer i kanal 1.) I den här självstudien flyttar vi UVs från kanal en till kanal 2 eftersom vi inte behöver redigera den nya UV-kanalen.

   >[!NOTE]
   >Även om du har kopierat (flyttat) UV-omslutning från kart kanal 1 till kart kanal 2, kan du göra nödvändiga ändringar i den nya kanal UVs utan att påverka den ursprungliga kart kanalen.

   ![Skärm bild som visar varningen för kanal ändring.](media/3dsmax/channel-change.jpg)

Nu när vi har skapat den nya kart kanalen kan vi gå tillbaka till det fysiska materialet i material redigeraren och börja lägga till våra texturer till den. Först ska vi lägga till AO-kartan eftersom det finns ett annat steg att tillåta att den fungerar korrekt. När AO-kartan är ansluten till vårt material måste vi konfigurera den för att använda kart kanal 2.

Som tidigare nämnts finns det ingen dedikerad plats för AO Maps i det fysiska det högsta fysiska materialet för 3DS. I stället tillämpar vi AO-kartan på den **diffusa tuffa** platsen.

1. I det fysiska materialets **allmänna Maps** -lista väljer du **ingen kart** plats bredvid **diffus grovhet** och läser in din Ao-karta.

1. I egenskaperna för AO-texturer anges kart kanalen till **1** som standard. Ändra värdet till **2**. Den här åtgärden slutför de steg som krävs för att lägga till AO-kartan.

   >[!IMPORTANT]
   >Detta är ett viktigt steg, särskilt om din UVs i kanal 2 skiljer sig från de i kanal 1, eftersom AO inte mappas korrekt om fel kanal är markerad.

   ![Skärm bild som visar hur du tilldelar en AO-karta.](media/3dsmax/assign-ao-map.jpg)

Vi kommer nu att tilldela den normala kartan till PBR-materialet. Den här åtgärden skiljer sig något från processen i Maya. Den normala kartan används inte direkt på ojämnhets kartans plats. (Det finns inget normalt kart fack i det högsta fysiska materialet för 3DS.) I stället lägger du till den normala kartan i en normal kart modifierare, som i sin tur är kopplad till normal facket.

1. I avsnittet **särskilda kartor** i egenskaperna för fysiskt material (i material redigeraren) väljer du **ingen kart** plats bredvid **ojämnhets karta**. 

1. Leta upp och välj **Normal ojämnhet** i **webbläsaren material/karta**. Den här åtgärden lägger till en **Normal ojämnhets** modifierare för materialet.

1. I den **normala ojämnhets** modifieraren väljer du **ingen karta** bredvid **Normal**. Leta upp och Läs in din normala karta.

1. Kontrol lera att metoden är inställd på **tangens**. (Det bör vara som standard.) Om det behövs växlar du till **vänd grönt (Y)**.

   ![Skärm bild som visar hur du väljer normal ojämnhet. ](media/3dsmax/normal-bump.jpg)
    ![ Skärm bild som visar inläsning av den normala kartan.](media/3dsmax/load-normal-map.jpg)

När den normala kartan har tilldelats korrekt kan vi tilldela de återstående texturerna för att slutföra installationen av det fysiska materialet. Den här processen är enkel. Det finns inga särskilda inställningar att tänka på. Följande skärm bild visar en fullständig uppsättning texturer som tilldelats materialet: 

![Skärm bild som visar en fullständig uppsättning texturer som tilldelats materialet.](media/3dsmax/all-textures.jpg)

Nu när PBR-materialen har skapats och kon figurer ATS är det värt att tänka på indelnings objekt i scenen. Instansen liknar objekt i scenen, t. ex. nötter, bultar, skruvar och spolare. Alla objekt som är desamma kan ge betydande besparingar när det gäller fil storlek. Instanser av ett huvud objekt kan ha sin egen skalning, rotation och transformering, så att du kan placera dem efter behov i din scen. I 3ds Max är indelnings processen enkel.

1. I huvud visnings området väljer du det eller de objekt som du vill exportera.

1. Håll **ned SKIFT** -tangenten och dra till gångarna uppåt med hjälp av verktyget Omforma (flytta). 

1. I dialog rutan **klonings alternativ** anger du **objekt** till **instans** och väljer sedan **OK**:

   ![Skärm bild av dialog rutan klonings alternativ.](media/3dsmax/instance-object.jpg)

Den här åtgärden skapar en instans av objektet som du kan flytta, rotera eller skala oberoende av dess överordnade och andra instanser av den överordnade.

>[!IMPORTANT]
>Alla ändringar du gör i en instans när du befinner dig i under objekts läge överförs till alla instanser av objektet. Så om du arbetar med ett instans objekts komponenter, t. ex. hörn och polygoner, måste du se till att du vill ha alla ändringar som du gör för att påverka alla instanser. Kom ihåg att alla instanser av objekt kan göras till ett unikt objekt när som helst. 

>[!TIP]
>När instans instansen i din scen är det en bra idé att skapa instanser när du går vidare. Det är svårt att ersätta kopior med instans objekt senare. 

En slutlig sak att tänka på innan vi går vidare med export processen är hur du kanske vill paketera din scen/till gång för delning. Vi rekommenderar att om du skickar till gången till klienter eller grupp medlemmar vill du att de ska kunna öppna och Visa till gången eftersom den bör ses med en minimal mängd du behöver. Det är därför viktigt att hålla din till gångs textur Sök vägar i förhållande till scen filen. Om textur Sök vägarna för din till gångs plats till en lokal enhet eller absolut sökväg/plats, läses de inte in i scenen om de öppnas på en annan dator, även om. Max-filen finns i samma mapp som strukturerna. Att göra textur Sök vägarna relativa i 3ds Max löser problemet och är ganska enkelt.

1. I huvud verktygsfältet går du till **fil**  >  **referens** för  >  **till gångs spårning växla**. 

1. I fönstret till gångs spårning ser du alla eller de flesta texturer som du har tillämpat på ditt PBR-material som anges i kolumnen **Maps/shaders** .

1. Bredvid dem, i kolumnen **fullständig sökväg** , ser du sökvägen till texturens placering, förmodligen sökvägen till platsen på den lokala datorn.

1. Slutligen visas en kolumn med namnet **status**. Den här kolumnen anger om en specifik textur har hittats och använts på din scen. Den flaggar texturen med någon av följande villkor: **OK**, **hittades** eller **filen saknas**. De två första anger att filen har hittats och lästs in. Det sista uppenbart innebär att spåraren inte kunde hitta filen.
 
   ![Skärm bild som visar fönstret till gångs spårning.](media/3dsmax/texture-paths.jpg)

Du kanske märker att inte alla dina texturer visas i fönstret till gångs spårning när du öppnar det första gången. Detta är inget som är bekymrat över. Att köra genom Sök vägs processen en gång eller två gånger, hittar du vanligt vis alla en scens texturer. Processen för att söka efter Sök vägar är följande: 

1. I fönstret till gångs spårning håller du ned **SKIFT** -tangenten och väljer den översta strukturen i listan **Maps/shaders** och fortsätter att hålla **SKIFT** och väljer den sista texturen i listan. Den här åtgärden markerar alla texturer i listan. De markerade texturerna är markerade i blått. (Se föregående skärm bild.)

1. Högerklicka på markeringen och välj **Ange sökväg**.

1. I rutan **Ange till gångs Sök väg** väljer du den lokala sökvägen till dina strukturer och ersätter den med `.\` .  Välj **OK**. 

    Fönstret till gångs spårning uppdateras så som visas i följande skärm bild. Den här uppdateringen kan ta en stund, beroende på hur många strukturer som finns i din scen och på hur stor din scen är.
![Screensthot som visar den uppdaterade till gångs spårnings fönstret.](media/3dsmax/resolve-textures.jpg)

Observera att kolumnen **fullständig sökväg** nu är tom. Det innebär att scenen inte längre söker efter relevanta texturer på en angiven (absolut) plats. Den hittar alltid dem så länge filen. Max eller relaterad FBX finns i samma mapp som strukturerna. 

>[!NOTE]
>Du kanske måste upprepa den här processen ett par gånger för att hitta och lösa alla texturer och sökvägar. Detta är inget som är bekymrat över. Upprepa bara processen tills alla relevanta till gångar redovisas. I vissa fall hittas inte vissa filer. I så fall väljer du bara alla till gångar i listan och väljer sedan **ta bort saknade sökvägar**. (Se föregående bild.)

## <a name="fbx-export"></a>FBX-export

Nu när vi har gjort textur Sök vägarna relativa kan vi gå vidare till FBX-exporten. Processen är återigen enkel och du kan göra det på ett par olika sätt. 

>[!TIP]
>Om du inte vill exportera hela scenen är det en bra idé att välja endast för att exportera de till gångar som du behöver. Vid resurs krävande scener kan exporten ta lång tid.
>
>Om du har använt modifierare som Turbosmooth eller Open SubDiv, är det en bra idé att komprimera dem innan du exporterar eftersom de kan orsaka problem under exporten. Se till att spara din scen innan du döljer dem. 

1. I scenen väljer du de resurser som du vill exportera. I huvud verktygsfältet går du till **fil**  >  **export**  >  **export vald**.

1. I dialog rutan **Välj fil som ska exporteras** skriver eller väljer du ett namn på utdatafilen. I listan **fil format** väljer du **Autodesk (*. FBX)**. Den här åtgärden öppnar export fönstret för FBX.

  >[!IMPORTANT] 
  >Om du har skapat instanser i din scen är det viktigt att välja **bevara instanser** i export inställningarna för FBX. 

  ![Skärm bild som visar hur du exporterar till FBX.](media/3dsmax/fbx-export.jpg)

  Kom ihåg att det finns ett par olika sätt att exportera filen. Om avsikten är att dela FBX tillsammans med dess textur-filer i en mapp/katalog, bör inställningarna som visas i följande skärm bild fungera bra. 

   Om du inte vill dela stora mappar/kataloger med texturer tillsammans med FBX kan du välja att bädda in texturerna i FBX. Om du bäddar in texturerna läggs hela till gången, inklusive texturer, till i en enda FBX. Detta kombinerar din export till en enda till gång, men FBX-filen kommer att bli betydligt större.

   >[!IMPORTANT]
   >Om den resulterande FBX-filen är större än 2,4 GB ska den lägsta version som anges i export inställningarna för FBX vara 2016 eller senare. (Se föregående skärm bild.) Nyare versioner har stöd för 64 bitar, så de har stöd för större filer.

1. Om du vill exportera scenen med texturer som ingår väljer du **bädda in media** i fönstret * FBX export. 

1. Välj resten av inställningarna och välj sedan **OK**:

    ![Skärm bild som visar export inställningarna för FBX.](media/3dsmax/fbx-settings.jpg)


   När du exporterar till FBX när du använder ett fysiskt material ser du förmodligen följande varning när du har valt **OK** i fönstret FBX export: 

   ![Skärm bild som visar att det inte gick att exportera material](media/3dsmax/export-warnings.jpg)

   Den här varningen anger att det exporterade materialet kanske inte är kompatibelt med andra program varu paket. Eftersom det fysiska materialet är kompatibelt med Azure-fjärrrendering behöver du inte bekymra dig om den här varningen. 

1. Klicka på **OK** för att slutföra processen och Stäng fönstret.

## <a name="conclusion"></a>Slutsats

I allmänhet ser den här typen av material mer realistisk eftersom den baseras på den verkliga fysiken av ljus. Den skapar en extra intensiv påverkan så att scenen verkar finnas i verkligheten.

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du konfigurerar material med avancerad belysning för objekt i en scen. Du vet också hur du exporterar objekt till FBX-format, som stöds av Azure Remote rendering. Nästa steg är att konvertera FBX-filen och visualisera den i Azure Remote rendering.

>[!div class="nextstepaction"]
>[Snabbstart: Konvertera en modell för rendering](../../quickstarts\convert-model.md)
