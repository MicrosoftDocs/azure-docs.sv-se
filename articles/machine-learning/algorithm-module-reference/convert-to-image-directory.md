---
title: Konvertera till bildkatalog
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen konvertera till avbildnings katalog för att konvertera data uppsättningen till avbildnings katalog format.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: 1489ce74da2ecff5212feb5a1a2e3c9151b73424
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94555591"
---
# <a name="convert-to-image-directory"></a>Konvertera till bildkatalog

Den här artikeln beskriver hur du använder modulen konvertera till avbildnings katalog för att konvertera avbildnings data uppsättning till data typen *image Directory* , som är standardiserat data format i bildrelaterade uppgifter som bild klassificering i Azure Machine Learning designer.

## <a name="how-to-use-convert-to-image-directory"></a>Så här använder du konvertera till avbildnings katalog  

1. Förbered din avbildnings data uppsättning först. 

    För övervakad inlärning måste du ange etiketten för träning-datauppsättningen. Avbildningens data uppsättnings fil bör vara i följande struktur:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    I mappen avbildnings data uppsättning finns det flera undermappar. Varje undermapp innehåller bilder av en kategori. Namnen på undermapparna betraktas som etiketter för uppgifter som bild klassificering. Mer information hittar du i [torchvision-datauppsättningar](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .

    > [!WARNING]
    > Etiketterade data uppsättningar som exporteras från data etiketter stöds inte i designern.

    Bilder med dessa fil namns tillägg (i gemener) stöds:. jpg,. jpeg,. png,. ppm,. bmp,. PGM,. tif,. TIFF,. webp. Du kan också ha flera olika typer av avbildningar i en mapp. Det är inte nödvändigt att innehålla samma antal avbildningar i varje Category-mapp.

    Du kan antingen använda mappen eller den komprimerade filen med fil namns tillägget ". zip", ". tar", ". gz" och ". bz2". **Komprimerade filer rekommenderas för bättre prestanda.** 
    
    ![Exempel data uppsättning för bild](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > För att kunna bli mer nödvändigt behöver mappen för avbildnings data uppsättning bara innehålla oklassificerade avbildningar.

1. [Registrera avbildnings data uppsättningen som en fil data uppsättning](../how-to-create-register-datasets.md) i din arbets yta, eftersom indata från Convert to image Directory-modulen måste vara en **fil data uppsättning**.

1. Lägg till den registrerade avbildnings data uppsättningen på arbets ytan. Du kan hitta din registrerade data uppsättning i kategorin **data uppsättningar** i listan modul i rutan till vänster om arbets ytan. För närvarande har designern inte stöd för visualisering av bild data uppsättning.

    > [!WARNING]
    > Du **kan inte** använda modulen **Importera data** för att importera avbildnings data uppsättning eftersom utdatatypen för modulen **Importera data** är DataFrame Directory, som endast innehåller en sträng med fil Sök vägar.

1. Lägg till modulen **konvertera till avbildnings katalog** på arbets ytan. Du hittar den här modulen i kategorin "Visuellt innehåll/image data transformation" i listan modul. Anslut den till avbildnings data uppsättningen.
    
3.  Skicka pipelinen. Den här modulen kan köras på antingen GPU eller CPU.

## <a name="results"></a>Resultat

Utdata från **Convert to image Directory** -modulen är i **bild katalog** format och kan anslutas till andra bildrelaterade moduler där Indataporten också är en avbildnings katalog.

![Konvertera till avbildnings katalogens utdata](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>Tekniska anteckningar 

###  <a name="expected-inputs"></a>Förväntade indata  

| Namn          | Typ                  | Beskrivning   |
| ------------- | --------------------- | ------------- |
| Indata-datauppsättning | AnyDirectory, ZipFile | Indata-datauppsättning |

###  <a name="output"></a>Utdata  

| Namn                   | Typ           | Beskrivning            |
| ---------------------- | -------------- | ---------------------- |
| Avbildnings katalog för utdata | ImageDirect | Avbildnings katalog för utdata |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.