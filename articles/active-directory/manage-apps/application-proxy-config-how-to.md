---
title: Så här konfigurerar du ett program för programproxy | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar ett APplication proxy-program med några enkla steg
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/18/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f1488986dc38bba89a5b9a109a444b5f0d81f2
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255211"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Så här konfigurerar du ett program för programproxy

Den här artikeln hjälper dig att förstå hur du konfigurerar ett program för programproxy i Azure AD för att exponera dina lokala program i molnet.

## <a name="recommended-documents"></a>Rekommenderade dokument

Om du vill veta mer om de inledande konfigurationerna och skapandet av ett programproxy-program via administrations portalen följer du [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md).

Mer information om hur du konfigurerar anslutningar finns [i Aktivera Application Proxy i Azure Portal](application-proxy-add-on-premises-application.md).

Information om hur du laddar upp certifikat och använder anpassade domäner finns i [arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Skapa programmet/ange URL: erna

Om du följer stegen i artikeln [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md) -dokumentationen och får ett fel när du skapar programmet, se fel information och förslag på hur du kan åtgärda programmet. De flesta fel meddelanden innehåller en föreslagen korrigering. För att undvika vanliga fel, verifiera:

- Du är administratör med behörighet att skapa ett program för programproxy
- Den interna URL: en är unik
- Den externa URL: en är unik
- URL: erna börjar med http eller https och slutar med "/"
- URL: en måste vara ett domän namn, inte en IP-adress

Fel meddelandet bör visas i det övre högra hörnet när du skapar programmet. Du kan också välja meddelande ikonen för att se fel meddelandena.

![Visar var du hittar meddelande tolken i Azure Portal](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurera anslutningar/anslutnings grupper

Om du har problem med att konfigurera programmet på grund av en varning om anslutningarna och anslutnings grupperna, se anvisningar om hur du aktiverar Application Proxy för information om hur du hämtar anslutningar. Om du vill lära dig mer om kopplingar, se [dokumentationen för Connector](application-proxy-connectors.md).

Om dina anslutningar är inaktiva innebär det att de inte kan komma åt tjänsten. Detta beror ofta på att alla portar som krävs inte är öppna. Om du vill se en lista över de portar som krävs läser du avsnittet krav i dokumentationen om att aktivera programproxy.

## <a name="upload-certificates-for-custom-domains"></a>Ladda upp certifikat för anpassade domäner

Med anpassade domäner kan du ange domänen för dina externa URL: er. Om du vill använda anpassade domäner måste du ladda upp certifikatet för domänen. Information om hur du använder anpassade domäner och certifikat finns i [arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).

Om du stöter på problem med att ladda upp certifikatet kan du leta efter fel meddelanden i portalen för ytterligare information om problemet med certifikatet. Vanliga certifikat problem är:

- Utgånget certifikat
- Certifikatet är självsignerat
- Certifikatet saknar den privata nyckeln

Fel meddelandet visas i det övre högra hörnet när du försöker ladda upp certifikatet. Du kan också välja meddelande ikonen för att se fel meddelandena.

## <a name="next-steps"></a>Nästa steg

[Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md)
