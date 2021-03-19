---
title: Jämföra anpassade bilder och formler i DevTest Labs | Microsoft Docs
description: Lär dig mer om skillnaderna mellan anpassade bilder och formler som virtuella dator baser, så att du kan bestämma vilka som passar din miljö bäst.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: df7a8e6209f8033eb5a29c65079e9c2f4cbbe544
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87287535"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Jämföra anpassade bilder och formler i DevTest Labs
Både [anpassade bilder](devtest-lab-create-template.md) och [formler](devtest-lab-manage-formulas.md) kan användas som baser för [skapade nya virtuella datorer](devtest-lab-add-vm.md). Skillnaden mellan anpassade bilder och formler är dock att en anpassad avbildning bara är en bild som baseras på en virtuell hård disk, medan en formel är en avbildning baserad på en virtuell hård disk *förutom* förkonfigurerade inställningar – till exempel VM-storlek, virtuellt nätverk, undernät och artefakter. Dessa förkonfigurerade inställningar ställs in med standardvärden som kan åsidosättas när den virtuella datorn skapas. Den här artikeln förklarar några av fördelarna (-och nack delarna) med att använda anpassade bilder jämfört med formler.

## <a name="custom-image-pros-and-cons"></a>Anpassade image-tekniker och nack delar
Anpassade avbildningar ger ett statiskt och oföränderligt sätt att skapa virtuella datorer från en önskad miljö. 

**Fördelar**

* Etablering av virtuella datorer från en anpassad avbildning är snabb, precis som inga ändringar efter att den virtuella datorn har anpassats från avbildningen. Det finns alltså inga inställningar att tillämpa eftersom den anpassade avbildningen bara är en bild utan inställningar. 
* Virtuella datorer som har skapats från en enda anpassad avbildning är identiska.

**Nackdelar**

* Om du behöver uppdatera någon aspekt av den anpassade avbildningen måste avbildningen återskapas.  

## <a name="formula-pros-and-cons"></a>Formel för-och nack delar
Formler ger ett dynamiskt sätt att skapa virtuella datorer från önskad konfiguration/inställningar.

**Fördelar**

* Ändringar i miljön kan samlas in direkt via artefakter. Om du till exempel vill att en virtuell dator ska installeras med de senaste bitarna från din versions pipeline eller om du anger den senaste koden från din lagrings plats, kan du bara ange en artefakt som distribuerar de senaste bitarna eller registrerar den senaste koden i formeln tillsammans med en mål bas avbildning. När den här formeln används för att skapa virtuella datorer distribueras/registreras de senaste bitarna/kodarna till den virtuella datorn. 
* Formler kan definiera standardinställningar som anpassade avbildningar inte kan tillhandahålla – till exempel VM-storlekar och virtuella nätverks inställningar. 
* Inställningarna som sparas i en formel visas som standardvärden, men kan ändras när den virtuella datorn skapas. 

**Nackdelar**

* Det kan ta längre tid att skapa en virtuell dator från en formel än att skapa en virtuell dator från en anpassad avbildning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogg inlägg
* [Anpassade bilder eller formler?](./devtest-lab-faq.md#blog-post)

## <a name="next-steps"></a>Nästa steg
- [Vanliga frågor och svar om DevTest Labs](devtest-lab-faq.md)
