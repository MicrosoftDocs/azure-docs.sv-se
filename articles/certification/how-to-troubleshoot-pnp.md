---
title: Felsöka din IoT Plug and Play enhet
description: En guide med rekommenderade felsökningssteg för partner som certifierar en IoT Plug and Play enhet.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 9b406e489fb83083d47f01e1483160181601d518
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559313"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>Felsöka ditt IoT Plug and Play certifieringsprojekt

Under Connect &-testfasen i ditt IoT Plug and Play-certifieringsprojekt kan du få problem med vissa scenarier som hindrar dig från att skicka AICS-testning (Azure for IoT Certification Service).

## <a name="prerequisites"></a>Förutsättningar

- Du bör vara inloggad och ha ett projekt för din enhet som skapats på [Azure Certified Device-portalen.](https://certify.azure.com) Mer information finns i [självstudien](tutorial-01-creating-your-project.md).

## <a name="when-aics-tests-arent-passing"></a>När AICS-tester inte passerar

AICS-testet kanske inte klarar sig på grund av flera orsaker. Följ de här stegen för att söka efter vanliga problem och felsöka din enhet.

1. Kontrollera att enhetskoden anger nyttolasten för modell-ID under DPS-etableringen. Detta är ett krav för att AICS ska verifiera din enhet.
1. Du kan visa telemetriloggarna från tidigare testkörningar genom att trycka på knappen för att identifiera vad som `View Logs` orsakar att testet misslyckas. Både testmeddelanden och rådata är tillgängliga för granskning.  

    ![Granska testdata](./media/images/review-logs.png)

1. I vissa fall där loggarna visar `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` kan du prova att starta om enheten och starta om enhetsetableringsprocessen.
1. Om de automatiserade testerna fortsätter att misslyckas kan du `request a manual review` använda resultatet som ersättning. Detta utlöser en begäran om **manuell verifiering hos** Azure Certified Device-teamet.  

    ![Begära manuell granskning](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>När du ser "Skickades med varningar"

Om du får ett resultat av när du kör testerna innebär det att `Passed with warnings` viss telemetri inte togs emot under testperioden. Detta kan bero på ett beroende av telemetrin på längre tidsintervall eller externa utlösare som inte var tillgängliga. Du kan fortsätta med att skicka in din enhet för granskning, där granskningsteamet avgör om **manuell verifiering** krävs i framtiden.

## <a name="when-you-need-help-with-the-model-repository"></a>När du behöver hjälp med modelldatabasen

Information om IoT Plug and Play som rör modelldatabasen finns i [vår Dokumentationsvägledning om lagringsplatsen för enhetsmodell.](https://docs.microsoft.com/azure/iot-pnp/concepts-model-repository)

## <a name="next-steps"></a>Nästa steg

Förhoppningsvis hjälper den här guiden dig att fortsätta med din IoT Plug and Play certifieringsresa! När du har passerat AICS kan du gå vidare med våra självstudier för att skicka och publicera din enhet.

- [Självstudie: Testa din enhet](tutorial-03-testing-your-device.md)
