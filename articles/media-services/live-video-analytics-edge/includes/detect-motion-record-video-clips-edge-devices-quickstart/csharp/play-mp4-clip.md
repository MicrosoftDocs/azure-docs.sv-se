---
ms.openlocfilehash: dfb887004cd29b5bd9f1d9886b7dfa5f43c83dbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99532156"
---
MP4-filerna skrivs till en katalog på gräns enheten som du konfigurerade i *. miljö* -filen med hjälp av VIDEO_OUTPUT_FOLDER_ON_DEVICE nyckeln. Om du använde standardvärdet ska resultaten finnas i */var/Media/* -mappen.

För att spela upp MP4-klippet:

1. Gå till din resurs grupp, leta upp den virtuella datorn och Anslut sedan med hjälp av Azure-skydds.

    ![Resursgrupp](../../../media/quickstarts/resource-group.png)
    
    ![Virtuell dator](../../../media/quickstarts/virtual-machine.png)
1. Logga in med de autentiseringsuppgifter som genererades när du konfigurerade [dina Azure-resurser](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. Gå till den relevanta katalogen i kommando tolken. Standard platsen är */var/Media*. Du bör se MP4-filerna i katalogen.

    ![Utdata](../../../media/quickstarts/samples-output.png) 

1. Använd [säker kopia (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) för att kopiera filerna till den lokala datorn. 
1. Spela upp filerna med hjälp av [VLC Media Player](https://www.videolan.org/vlc/) eller någon annan MP4-spelare.
