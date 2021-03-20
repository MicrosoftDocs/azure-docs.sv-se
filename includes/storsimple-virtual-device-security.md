---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187387"
---
<!--v-sharos 10/13/2105 virtual device security-->

Tänk på följande säkerhets aspekter när du använder den virtuella StorSimple-enheten:

* Den virtuella enheten skyddas via din Microsoft Azure prenumeration. Det innebär att om du använder den virtuella enheten och din Azure-prenumeration komprometteras, är data som lagras på den virtuella enheten också sårbara.
* Den offentliga nyckeln för certifikatet som används för att kryptera data som lagras i Azure StorSimple görs säkert tillgängligt för den klassiska Azure-portalen och den privata nyckeln behålls med StorSimple-enheten. Både offentliga och privata nycklar lagras i Azure på den virtuella StorSimple-enheten.
* Den virtuella enheten finns i Microsoft Azure Data centret.

