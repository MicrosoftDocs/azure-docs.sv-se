---
title: Felsöka import-och export problem i Azure import/export | Microsoft Docs
description: Lär dig hur du hanterar vanliga problem när du använder Azure import/export.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706684"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Felsöka problem i Azure import/export
Den här artikeln beskriver hur du felsöker vanliga problem när du importerar och exporterar data i Azure import/export.

## <a name="a-copy-session-failed-what-i-should-do"></a>Det gick inte att kopiera sessionen. Vad ska jag göra?  

När en kopierings session Miss lyckas har du två alternativ:  
* Om felet kan göras om, till exempel om nätverks resursen var offline under en kort period, och nu är online igen, kan du återuppta kopierings sessionen.
* Om felet inte kan göras igen, till exempel om du har angett fel käll fils katalog i kommando rads parametrarna, måste du avbryta kopierings sessionen.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Jag kan inte återuppta eller avbryta en kopierings session.

Om kopierings sessionen är den första redigeringssessionen för en enhet bör fel meddelandet tillstånd: "den första kopierings sessionen kan inte återupptas eller avbrytas." I så fall kan du ta bort den gamla Journal filen och köra kommandot igen.  

Om en Copy-session inte är den första för en enhet, kan sessionen alltid återupptas eller avbrytas.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Jag förlorade Journal filen. Kan jag fortfarande skapa jobbet?

Journal filen för en enhet innehåller information om fullständiga sessioner från data kopieringen. När du lägger till filer till enheten används Journal filen för att skapa ett import jobb. Om du förlorar Journal filen måste du göra om alla kopierings sessioner för enheten.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget Azure import/export](storage-import-export-tool-setup-v1.md)
* [Förbereda hård diskar för ett import jobb](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Granska jobb status med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparera ett import jobb](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparera ett export jobb](storage-import-export-tool-repairing-an-export-job-v1.md)