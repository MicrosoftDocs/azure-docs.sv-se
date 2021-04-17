---
title: Självstudie – Använda OpenSSL för att skapa själv signerade certifikat för Azure IoT Hub | Microsoft Docs
description: Självstudie – Använda OpenSSL för att skapa själv signerade X.509-certifikat för Azure IoT Hub
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
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378201"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Självstudie: Använda OpenSSL för att skapa själv signerade certifikat

Du kan autentisera en enhet till IoT Hub med hjälp av två själv signerade enhetscertifikat. Detta kallas ibland tumavtrycksautentisering eftersom certifikaten innehåller tumavtryck (hash-värden) som du skickar till IoT-hubben. Följande steg visar hur du skapar två själv signerade certifikat.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Steg 1 – Skapa en nyckel för det första certifikatet

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Steg 2 – Skapa en CSR för det första certifikatet

Se till att du anger enhets-ID när du uppmanas att göra det.

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

## <a name="step-3---check-the-csr"></a>Steg 3 – Kontrollera CSR

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Steg 4 – Självcertifikat 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Steg 5 – Skapa en nyckel för certifikat 2

När du uppmanas till det anger du samma enhets-ID som du använde för certifikat 1.

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

## <a name="step-6---self-sign-certificate-2"></a>Steg 6 – Självcertifikat 2

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

## <a name="step-9---create-a-new-iot-device"></a>Steg 9 – Skapa en ny IoT-enhet

Gå till IoT Hub i Azure Portal och skapa en ny IoT-enhetsidentitet med följande egenskaper:

* Ange det **enhets-ID** som matchar ämnesnamnet för dina två certifikat.
* Välj **autentiseringstypen X.509 Med själv** signering.
* Klistra in tumavtrycken för hexsträngen som du kopierade från enhetens primära och sekundära certifikat. Kontrollera att hexadexsträngarna inte har några kolonavgränsare.

## <a name="next-steps"></a>Nästa steg

Gå till [Testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om certifikatet kan autentisera enheten till IoT Hub.
