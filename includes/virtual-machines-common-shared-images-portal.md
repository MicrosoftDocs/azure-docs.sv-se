---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e7a4af2c18bfc2b8fbd6149dab12c0bcfc665341
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878838"
---
## <a name="create-an-image-gallery"></a>Skapa ett bild galleri

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck.  Galleri namn måste vara unika inom din prenumeration. 

I följande exempel skapas ett galleri med namnet min *Galleri* i resurs gruppen *myGalleryRG* .

1. Logga in på Azure Portal på https://portal.azure.com.
1. Använd **galleriet** för typ av delad avbildning i sökrutan och välj **Galleri för delade avbildningar** i resultatet.
1. Klicka på **Lägg till** på sidan **delad avbildnings Galleri** .
1. På sidan **skapa Galleri för delad avbildning** väljer du rätt prenumeration.
1. I **resurs grupp** väljer du **Skapa ny** och skriver *myGalleryRG* som namn.
1. I **namn** skriver du *galleriet* för galleriets namn.
1. Lämna standardvärdet för **region**.
1. Du kan ange en kort beskrivning av galleriet, t *. ex. min bild galleri för testning.* och klicka sedan på **Granska + skapa**.
1. När verifieringen har godkänts väljer du **skapa**.
1. När distributionen är färdig väljer **du gå till resurs**.


## <a name="create-an-image-definition"></a>Skapa en avbildnings definition 

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om de avbildnings versioner som skapas i dem. Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](../articles/virtual-machines/shared-image-galleries.md#image-definitions).

Skapa Galleri avbildnings definitionen i galleriet. I det här exemplet heter Galleri bilden *myImageDefinition*.

1. På sidan för det nya bild galleriet väljer du **Lägg till en ny bild definition** överst på sidan. 
1. I området **Lägg till ny avbildnings definition till delad avbildning** för **region** väljer du *östra USA*.
1. För **bild definitions namn** skriver du *myImageDefinition*.
1. För **operativ system** väljer du rätt alternativ baserat på den virtuella käll datorn.  
1. För **generering av virtuella datorer** väljer du alternativet baserat på den virtuella käll datorn. I de flesta fall kommer detta att vara *gen 1*. Mer information finns i [stöd för virtuella datorer i generation 2](../articles/virtual-machines/generation-2.md).
1. För **operativ systemets tillstånd** väljer du alternativet baserat på den virtuella käll datorn. Mer information finns i [generaliserad och specialiserad](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).
1. För **utgivare** skriver du in *unpublisher*. 
1. För **erbjudande**, skriver du *erbjudandet*.
1. För **SKU** skriver du *mySKU*.
1. När du är färdig väljer du **Granska + skapa**.
1. När du har klarat valideringen av avbildnings definitionen väljer du **skapa**.
1. När distributionen är färdig väljer **du gå till resurs**.


## <a name="create-an-image-version"></a>Skapa en avbildningsversion

Skapa en avbildnings version från en hanterad avbildning. I det här exemplet är avbildnings versionen *1.0.0* och replikeras till både *västra centrala* USA och *södra centrala* Data Center. Kom ihåg att du även måste inkludera *käll* regionen som mål för replikering när du väljer mål regioner för replikering.

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

Stegen för att skapa en avbildnings version skiljer sig något beroende på om källan är en generaliserad avbildning eller en ögonblicks bild av en specialiserad virtuell dator. 

### <a name="option-generalized"></a>Alternativ: generaliserad

1. På sidan för din avbildnings definition väljer du **Lägg till version** överst på sidan.
1. I **region** väljer du den region där den hanterade avbildningen lagras. Avbildnings versioner måste skapas i samma region som den hanterade avbildning som de skapas från.
1. I **namn** skriver du *1.0.0*. Avbildningens versions namn bör följa *huvud* namnet. *mindre*. *korrigerings* format med hjälp av heltal. 
1. I **käll avbildningen** väljer du din hanterade avbildning från List rutan.
1. Lämna standardvärdet *Nej* i **utelämna från senaste**.
1. I **slutet av livs längd** väljer du ett datum i kalendern som är ett par månader i framtiden.
1. Lämna **standardvärdet** för replikering som 1 i **replikering**. Du behöver replikera till käll regionen, så lämna den första repliken som standard och välj sedan en andra replik region som ska vara *östra USA*.
1. När du är färdig väljer du **Granska + skapa**. Azure kommer att verifiera konfigurationen.
1. När bild versionen klarar valideringen väljer du **skapa**.
1. När distributionen är färdig väljer **du gå till resurs**.

Det kan ta en stund att replikera avbildningen till alla mål regioner.

### <a name="option-specialized"></a>Alternativ: specialiserad

1. På sidan för din avbildnings definition väljer du **Lägg till version** överst på sidan.
1. I **region** väljer du den region där din ögonblicks bild lagras. Avbildnings versioner måste skapas i samma region som den källa de skapas från.
1. I **namn** skriver du *1.0.0*. Avbildningens versions namn bör följa *huvud* namnet. *mindre*. *korrigerings* format med hjälp av heltal. 
1. I **ögonblicks bild av OS-disk** väljer du ögonblicks bilden från den virtuella käll datorn från List rutan. Om den virtuella käll datorn hade en datadisk som du vill inkludera väljer du korrekt **LUN** -nummer i list rutan och väljer sedan ögonblicks bilden av data disken för **ögonblicks bild av data disk**. 
1. Lämna standardvärdet *Nej* i **utelämna från senaste**.
1. I **slutet av livs längd** väljer du ett datum i kalendern som är ett par månader i framtiden.
1. Lämna **standardvärdet** för replikering som 1 i **replikering**. Du behöver replikera till käll regionen, så lämna den första repliken som standard och välj sedan en andra replik region som ska vara *östra USA*.
1. När du är färdig väljer du **Granska + skapa**. Azure kommer att verifiera konfigurationen.
1. När bild versionen klarar valideringen väljer du **skapa**.
1. När distributionen är färdig väljer **du gå till resurs**.

## <a name="share-the-gallery"></a>Dela galleriet

Vi rekommenderar att du delar åtkomst på avbildnings Galleri nivån. Följande vägleder dig genom att dela galleriet som du nyss skapade.

1. Öppna [Azure-portalen](https://portal.azure.com).
1. I menyn till vänster väljer du **resurs grupper**. 
1. I listan över resurs grupper väljer du **myGalleryRG**. Bladet för resurs gruppen öppnas.
1. I menyn till vänster på sidan **myGalleryRG** väljer du **åtkomst kontroll (IAM)**. 
1. Under **Lägg till en roll tilldelning** väljer du **Lägg till**. Fönstret **Lägg till en roll tilldelning** öppnas. 
1. Välj **läsare** under **roll**.
1. Lämna standardvärdet för **Azure AD-användare,-grupp eller tjänst objekt** under **tilldela åtkomst till**.
1. Under **Välj** skriver du in e-postadressen till den person som du vill bjuda in.
1. Om användaren är utanför organisationen visas meddelandet **den här användaren kommer att skicka ett e-postmeddelande som gör det möjligt för dem att samar beta med Microsoft.** Välj användaren med e-postadressen och klicka sedan på **Spara**.

Om användaren är utanför organisationen får han eller hon en e-postinbjudan om att delta i organisationen. Användaren måste godkänna inbjudan. sedan kommer de att kunna se galleriet och alla avbildnings definitioner och versioner i listan över resurser.