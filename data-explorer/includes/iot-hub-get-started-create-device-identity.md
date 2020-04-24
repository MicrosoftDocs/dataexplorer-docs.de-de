---
title: include file
description: include file
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493882"
---
In diesem Abschnitt wird die Azure-Befehlszeilenschnittstelle verwendet, um eine Geräteidentität für diesen Artikel zu erstellen. Bei Geräte-IDs wird die Groß-/Kleinschreibung beachtet.

1. Öffnen Sie [Azure Cloud Shell](https://shell.azure.com/).

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle zu installieren:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Erstellen Sie mithilfe der folgenden Befehle eine neue Geräteidentität mit dem Namen `myDeviceId`, und rufen Sie die Verbindungszeichenfolge für das Gerät ab:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Notieren Sie sich die Verbindungszeichenfolge für das Gerät aus dem Ergebnis. Diese Geräteverbindungszeichenfolge wird von der Geräte-App verwendet, um die Verbindung mit Ihrem IoT Hub als Gerät herzustellen.

<!-- images and links -->
