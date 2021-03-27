---
title: Självstudie – Använd OpenSSL för att skapa självsignerade certifikat för Azure IoT Hub | Microsoft Docs
description: Självstudie – Använd OpenSSL för att skapa självsignerade X. 509-certifikat för Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 82ef2e39d5d04914e1086e0b25ccbc8e5c7c762e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630772"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Självstudie: använda OpenSSL för att skapa självsignerade certifikat

Du kan autentisera en enhet till din IoT Hub att använda två självsignerade enhets certifikat. Det kallas ibland för tumavtryck-autentisering eftersom certifikaten innehåller tumavtrycken (hash-värden) som du skickar till IoT-hubben. Följande steg beskriver hur du skapar två självsignerade certifikat.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Steg 1 – Skapa en nyckel för det första certifikatet

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Steg 2 – Skapa en CSR för det första certifikatet

Se till att du anger enhets-ID när du uppmanas till det.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Steg 3 – kontrol lera CSR

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Steg 4 – självsignerat certifikat 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Steg 5 – skapa en nyckel för certifikat 2

När du uppmanas anger du samma enhets-ID som du använde för certifikat 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>Steg 6 – självsignera certifikat 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Steg 7 – Hämta tumavtrycket för certifikat 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Steg 8 – Hämta tumavtrycket för certifikat 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Steg 9 – skapa en ny IoT-enhet

Navigera till IoT Hub i Azure Portal och skapa en ny IoT-enhets identitet med följande egenskaper:

* Ange **enhets-ID: t** som matchar ämnes namnet för dina två certifikat.
* Välj den **självsignerade X. 509-** autentiseringstypen.
* Klistra in den hex-sträng tumavtrycken som du kopierade från enhetens primära och sekundära certifikat. Se till att hex-strängarna inte har några kolon-avgränsare.

## <a name="next-steps"></a>Nästa steg

Gå till [testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om ditt certifikat kan autentisera din enhet till din IoT Hub.
