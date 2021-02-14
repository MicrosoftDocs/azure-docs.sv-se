---
title: 'ML Studio (klassisk): Visa & köra experiment igen – Azure'
description: Hantera experiment körningar i Azure Machine Learning Studio (klassisk). Du kan granska tidigare körningar av dina experiment när som helst för att kunna anropa, gå tillbaka, och i slut ändan antingen bekräfta eller förfina tidigare antaganden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 419a696da1244afab7aa03cd8c4521ea819a5298
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515962"
---
# <a name="manage-experiment-runs-in-azure-machine-learning-studio-classic"></a>Hantera experiment körningar i Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Att utveckla en förutsägelse analys modell är en iterativ process – när du ändrar de olika funktionerna och parametrarna i experimentet konvergerar resultatet tills du är nöjd med att du har en tränad, effektiv modell. Nyckeln till den här processen spårar olika iterationer av dina experiment parametrar och konfigurationer.

Du kan granska tidigare körningar av dina experiment när som helst för att kunna anropa, gå tillbaka, och i slut ändan antingen bekräfta eller förfina tidigare antaganden. När du kör ett experiment behåller Machine Learning Studio (klassisk) en historik över körningen, inklusive data uppsättning, modul och port anslutningar och-parametrar. Den här historiken fångar också resultat, körnings information som start-och stopp tider, logg meddelanden och körnings status. Du kan när som helst se hur som helst av dessa körningar om du vill granska kronologisk ordning av experimentet och mellanliggande resultat. Du kan även använda en tidigare körning av ditt experiment för att starta i en ny fas av förfrågan och identifiering på din sökväg för att skapa enkla, komplexa eller till och med Ensemble-modellerings lösningar.

> [!NOTE]
> När du visar en tidigare körning av ett experiment är den versionen av experimentet låst och kan inte redige ras. Du kan dock spara en kopia av den genom att klicka på **Spara som** och ange ett nytt namn för kopian. Machine Learning Studio (klassisk) öppnar den nya kopian, som du sedan kan redigera och köra. Den här kopian av ditt experiment är tillgänglig i **experiment** listan tillsammans med alla andra experiment.
> 
> 

## <a name="view-the-prior-run"></a>Visa föregående körning
När du har ett experiment öppet som du har kört minst en gång kan du Visa föregående körning av experimentet genom att klicka på **föregående körning** i rutan Egenskaper.

Anta till exempel att du skapar ett experiment och kör versioner av det på 11:23, 11:42 och 11:55. Om du öppnar den senaste körningen av experimentet (11:55) och klickar på **föregående körning** öppnas den version du körde vid 11:42.

## <a name="view-the-run-history"></a>Visa körnings historiken
Du kan visa alla tidigare körningar av ett experiment genom att klicka på **Visa körnings historik** i ett öppet experiment.

Anta till exempel att du skapar ett experiment med modulen [linjär regression][linear-regression] och att du vill se hur du ändrar värdet för **inlärnings frekvensen** för dina experiment resultat. Du kör experimentet flera gånger med olika värden för den här parametern, enligt följande:

| Inlärnings pris värde | Kör Start tid |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 PM |
| 0,2 |9/11/2014 4:24:33 PM |
| 0,4 |9/11/2014 4:28:36 PM |
| 0,5 |9/11/2014 4:33:31 PM |

Om du klickar på **Visa körnings historik** visas en lista över alla dessa körningar:

![Exempel på körnings historik](./media/manage-experiment-iterations/viewrunhistory.jpg)

Klicka på någon av dessa körningar för att visa en ögonblicks bild av experimentet vid den tidpunkt då du körde det. Konfigurationen, parameter värden, kommentarer och resultat bevaras för att ge dig en fullständig post av den körningen av experimentet.

> [!TIP]
> Om du vill dokumentera dina iterationer av experimentet kan du ändra titeln varje gång du kör det, så att du kan uppdatera **sammanfattningen** av experimentet i fönstret Egenskaper och du kan lägga till eller uppdatera kommentarer om enskilda moduler för att registrera dina ändringar. Kommentarerna för rubrik, Sammanfattning och modul sparas med varje körning av experimentet.
> 
> 

I listan över experiment på fliken **experiment** i Machine Learning Studio (klassisk) visas alltid den senaste versionen av ett experiment. Om du öppnar en tidigare körning av experimentet (med tidigare **Kör** eller **Visa körnings historik**) kan du gå tillbaka till utkastet genom att klicka på **Visa körnings historik** och välja den iteration som har **statusen** **redigerbar**.

## <a name="run-a-previous-experiment"></a>Kör ett tidigare experiment
När du klickar på **föregående körning** eller **Visa körnings historik** och öppnar en tidigare körning kan du visa ett klart experiment i skrivskyddat läge.

Om du vill påbörja en upprepning av experimentet som börjar med det sätt som du konfigurerade det för en tidigare körning, kan du göra det genom att öppna körningen och klicka på **Spara som**. Detta skapar ett nytt experiment, med en ny rubrik, en tom körnings historik och alla komponenter och parameter värden för föregående körning. Det nya experimentet visas på fliken **experiment** på start sidan Machine Learning Studio (klassisk), och du kan ändra och köra det och påbörja en ny körnings historik för den här iterationen av experimentet. 

Anta till exempel att du har historiken för experiment körning som visas i föregående avsnitt. Du vill veta vad som händer när du ställer in **Learning Rate** -parametern på 0,4 och provar olika värden för **antalet epoker** -parametrar.

1. Klicka på **Visa körnings historik** och öppna upprepningen av experimentet som du körde vid 4:28:36 PM (där du angav parametervärdet till 0,4).
2. Klicka på **Spara som**.
3. Ange en ny rubrik och klicka på **OK** -bocken. En ny kopia av experimentet skapas.
4. Ändra **antalet epoker** -parametrar.
5. Klicka på **Kör**.

Nu kan du fortsätta att ändra och köra den här versionen av experimentet och skapa en ny körnings historik för att registrera ditt arbete.

<!-- Module References -->
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression