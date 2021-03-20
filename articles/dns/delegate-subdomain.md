---
title: Delegera en under domän – Azure DNS
description: Med den här utbildnings vägen börjar du delegera en Azure DNS under domän.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011548"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegera en Azure DNS under domän

Du kan använda Azure Portal för att delegera en DNS-under domän. Om du till exempel äger contoso.com-domänen kan du delegera en under domän som heter *teknik* till en annan, separat zon som du kan administrera separat från zonen contoso.com.

Om du vill kan du delegera en under domän med hjälp av [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill delegera en Azure DNS under domän måste du först delegera din offentliga domän till Azure DNS. Mer information om hur du konfigurerar namnservrar för delegering finns i [delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) . När din domän har delegerats till din Azure DNS zon kan du delegera din under domän.

> [!NOTE]
> Contoso.com används som ett exempel i den här artikeln. Använd ditt eget domännamn i stället för contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Skapa en zon för under domänen

Skapa först zonen för under domänen **teknik** .

1. Välj **skapa en resurs** från Azure Portal.
2. Skriv **DNS** i rutan Sök och välj **DNS-zon**.
3. Välj **Skapa**.
4. I fönstret **Skapa DNS-zon** skriver du **Engineering.contoso.com** i text rutan **namn** .
5. Välj resurs grupp för din zon. Du kanske vill använda samma resurs grupp som den överordnade zonen för att hålla likartade resurser sammantaget.
6. Klicka på **Skapa**.
7. När distributionen har slutförts går du till den nya zonen.

## <a name="note-the-name-servers"></a>Anteckna namnservrarna

Notera sedan de fyra namnservrarna för under domänen teknik.

Notera de fyra namnservrarna för zonen i fönstret **ingenjörs** zon. Du kommer att använda dessa namnservrar senare.

## <a name="create-a-test-record"></a>Skapa en test post

Skapa en **A** -post som ska användas för testning. Du kan till exempel skapa en **www** a-post och konfigurera den med en **10.10.10.10** -IP-adress.

## <a name="create-an-ns-record"></a>Skapa en NS-post

Skapa sedan en namnserver post (NS) för **teknik** zonen.

1. Navigera till zonen för den överordnade domänen.
2. Välj **+ Postuppsättning**.
3. I rutan **Lägg till uppsättning av poster** skriver du **tekniker** i text rutan **namn** .
4. I **typ** väljer du **ns**.
5. Under **namnserver** anger du de fyra namnservrar som du spelat in tidigare från **teknik** zonen.
6. Klicka på **OK**.

## <a name="test-the-delegation"></a>Testa delegeringen

Använd nslookup för att testa delegeringen.

1. Öppna ett PowerShell-fönster.
2. Skriv i kommando tolken `nslookup www.engineering.contoso.com.`
3. Du bör få ett icke-auktoritativt svar som visar adressen **10.10.10.10**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar omvänd DNS för tjänster som finns i Azure](dns-reverse-dns-for-azure-services.md).