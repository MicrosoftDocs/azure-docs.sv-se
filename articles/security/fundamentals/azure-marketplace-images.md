---
title: Säkerhets rekommendationer för Azure Marketplace-avbildningar | Microsoft Docs
description: Den här artikeln innehåller rekommendationer för avbildningar som ingår på marknads platsen
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 7c317a0b4fea0c981b227bace00c1b8924fd582c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89536390"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Säkerhets rekommendationer för Azure Marketplace-avbildningar

Din avbildning måste uppfylla dessa rekommendationer för säkerhets konfiguration. Detta hjälper till att upprätthålla en hög säkerhets nivå för partner lösnings avbildningar på Azure Marketplace.

Kör alltid en säkerhets sårbarhets identifiering på avbildningen innan du skickar. Om du upptäcker en säkerhets risk i en egen publicerad avbildning måste du informera dina kunder i rätt tid av både säkerhets problemet och hur du korrigerar det.

## <a name="open-source-based-images"></a>Öppna källbaserade avbildningar

| Kategori | Markera |
| -------- | ----- |
| Säkerhet                                                     | Installera alla de senaste säkerhets korrigeringarna för Linux-distributionen.                                                                                                                                                                                                              |
| Säkerhet                                                     | Följ rikt linjerna för att skydda den virtuella dator avbildningen för den aktuella Linux-distributionen.                                                                                                                                                                                     |
| Säkerhet                                                     | Begränsa angrepps ytan genom att behålla minimalt utrymme med endast nödvändiga Windows Server-roller, funktioner, tjänster och nätverks portar.                                                                                                                                               |
| Säkerhet                                                     | Skanna käll koden och den resulterande VM-avbildningen för skadlig kod.                                                                                                                                                                                                                                   |
| Säkerhet                                                     | VHD-avbildningen innehåller bara nödvändiga låsta konton som inte har standard lösen ord som tillåter interaktiv inloggning. inga back dörrar.                                                                                                                                           |
| Säkerhet                                                     | Inaktivera brand Väggs regler om inte programmet använder dem i funktion, till exempel en brand Väggs enhet.                                                                                                                                                                             |
| Säkerhet                                                     | Ta bort all känslig information från VHD-avbildningen, till exempel testa SSH-nycklar, kända värdar-fil, loggfiler och onödiga certifikat.                                                                                                                                       |
| Säkerhet                                                     | Undvik att använda LVM.                                                                                                                                                                                                                                            |
| Säkerhet                                                     | Ta med de senaste versionerna av de nödvändiga biblioteken: </br> -OpenSSL v 1.0 eller senare </br> – Python 2,5 eller senare (python 2.6 + är starkt rekommenderat) </br> – Python pyasn1-paket om det inte redan har installerats </br> -d. OpenSSL v 1,0 eller senare                                                                |
| Säkerhet                                                     | Rensa poster i bash/shell-historik.                                                                                                                                                                                                                                             |
| Nätverk                                                   | Inkludera SSH-servern som standard. Ange SSH Keep Alive till sshd-konfigurationen med följande alternativ: ClientAliveInterval 180.                                                                                                                                                        |
| Nätverk                                                   | Ta bort eventuell anpassad nätverks konfiguration från avbildningen. Ta bort kommandot matcha. conf: `rm /etc/resolv.conf` .                                                                                                                                                                                |
| Distribution                                                   | Installera den senaste Azure Linux-agenten.</br> – Installera med hjälp av RPM-eller deb-paketet.  </br> -Du kan också använda processen för manuell installation, men installations paketen rekommenderas och föredras. </br> – Om du installerar agenten manuellt från GitHub-lagringsplatsen kopierar du först `waagent` filen till `/usr/sbin` och kör (som rot): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Agent konfigurations filen placeras på `/etc/waagent.conf` . |
| Distribution                                                   | Se till att Azure-supporten kan ge våra partners till gång till serie konsolens utdata vid behov och ange lämplig tids gräns för montering av OS-diskar från moln lagring Lägg till följande parametrar till avbildnings start raden för kernel: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` . |
| Distribution                                                   | Ingen växlings-partition på OS-disken. Du kan begära växling för att skapa en lokal resurs disk av Linux-agenten.         |
| Distribution                                                   | Skapa en enda rotnod för OS-disken.      |
| Distribution                                                   | endast 64-bitars operativ system.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-baserade avbildningar

| Kategori | Markera |
|--------- | ----- |
| Säkerhet                                                         | Använd en säker OS-avbildning. Den virtuella hård disk som används för källan till alla avbildningar som baseras på Windows Server måste vara från Windows Server OS-avbildningarna som tillhandahålls via Microsoft Azure. |
| Säkerhet                                                         | Installera alla de senaste säkerhets uppdateringarna.                                                                                                                                     |
| Säkerhet                                                         | Program bör inte vara beroende av begränsade användar namn som administratör, rot eller administratör.                                                                |
| Säkerhet                                                         | Aktivera BitLocker-diskkryptering för både OS-hårddiskar och data hård diskar.                                                             |
| Säkerhet                                                         | Begränsa angrepps ytan genom att behålla minimalt utrymme med endast nödvändiga Windows Server roller, funktioner, tjänster och nätverks portar aktiverade.                         |
| Säkerhet                                                         | Skanna käll koden och den resulterande VM-avbildningen för skadlig kod.                                                                                                                     |
| Säkerhet                                                         | Ange Windows Server-avbildningar säkerhets uppdatering för automatisk uppdatering.                                                                                                                |
| Säkerhet                                                         | VHD-avbildningen innehåller bara nödvändiga låsta konton som inte har standard lösen ord som tillåter interaktiv inloggning. inga back dörrar.                             |
| Säkerhet                                                         | Inaktivera brand Väggs regler om inte programmet använder dem i funktion, till exempel en brand Väggs enhet.                                                               |
| Säkerhet                                                         | Ta bort all känslig information från VHD-avbildningen, inklusive HOSTs-filer, loggfiler och onödiga certifikat.                                              |
| Distribution                                                       | endast 64-bitars operativ system.                            |

Även om din organisation inte har avbildningar på Azure Marketplace bör du överväga att kontrol lera dina Windows-och Linux-bildkonfigurationer mot dessa rekommendationer.

