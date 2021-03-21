---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 05/06/2020
ms.author: larryfr
ms.openlocfilehash: 83898744610cd27989e6fa0d1c6b1cca6286cb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103021212"
---
Du kan förhindra att onödiga filer tas med i ögonblicks bilden genom att göra en IGNORE-fil ( `.gitignore` eller `.amlignore` ) i katalogen. Lägg till de filer och kataloger som ska undantas i den här filen. Mer information om vilken syntax som ska användas i den här filen finns i [syntax och mönster](https://git-scm.com/docs/gitignore) för `.gitignore` . `.amlignore`Filen använder samma syntax. _Om båda filerna finns `.amlignore` används filen och `.gitignore` filen är inte används._
