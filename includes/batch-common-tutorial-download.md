---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187693"
---
### <a name="retrieve-output-files"></a>hämta utdatafilerna.

Du kan använda Azure-portalen till att hämta de utdatafiler i MP3-format som genereras av ffmpeg-uppgifter. 

1. Klicka på **alla tjänster**  >  **lagrings konton** och klicka sedan på namnet på ditt lagrings konto.
2. Klicka på **blobbar**  >  *utdata*.
3. Högerklicka på en av MP3-utdatafilerna och klicka sedan på **Ladda ned**. Följ anvisningarna i webbläsaren för att öppna eller spara filen.

![Ladda ned utdatafil](./media/batch-common-tutorial-download/download.png)

Även om det inte visas i det här exemplet kan du också ladda ned filerna programmatiskt från beräkningsnoderna eller från lagringscontainern.
