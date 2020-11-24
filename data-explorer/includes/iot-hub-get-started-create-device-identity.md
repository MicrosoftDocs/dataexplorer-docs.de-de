---
title: include file
description: include file
services: iot-hub
author: orspod
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: orspodek
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 035435d535a2b67b0de8c4fb9e2af9b1ea1dc5ff
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95390270"
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

