---
title: Azure Data Explorer Python SDK - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das Azure Data Explorer Python SDK in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5bee1fce1ca76069c34602872b2d4dedeb762ec2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502855"
---
# <a name="azure-data-explorer-python-sdk"></a>Azure Data Explorer Python SDK

Die Clientbibliothek von Azure Data Explorer *Kusto Python* bietet die Möglichkeit, Azure Data Explorer-Cluster mithilfe von Python abzufragen. Es ist Python 2.x/3.x kompatibel und unterstützt alle Datentypen über die vertraute Python DB API-Schnittstelle.

Es ist möglich, die Bibliothek beispielsweise von [Jupyter-Notebooks](https://jupyter.org/) zu verwenden, die an Spark-Cluster angeschlossen sind, einschließlich, aber nicht ausschließlich, [Azure Databricks-Instanzen.](https://azure.microsoft.com/services/databricks/)

*Kusto Python Ingest Client* ist eine Python-Bibliothek, die das Senden von Daten an den Azure Data Explorer-Dienst ermöglicht, d. h. Daten zu erfassen. 

* [So installieren Sie das Paket](https://github.com/Azure/azure-kusto-python#install)

* [Kusto-Abfragebeispiel](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py)

* [Datenaufnahmeprobe](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-ingest/tests/sample.py)

* [GitHub-Repository](https://github.com/Azure/azure-kusto-python)

    [![alt text](https://travis-ci.org/Azure/azure-kusto-python.svg?branch=master "azure-kusto-python")](https://travis-ci.org/Azure/azure-kusto-python)

* Pypi-Pakete:

    * [azure-kusto-data](https://pypi.org/project/azure-kusto-data/)
    [![PyPI-Version](https://badge.fury.io/py/azure-kusto-data.svg)](https://badge.fury.io/py/azure-kusto-data)
    * [azure-kusto-ingest](https://pypi.org/project/azure-kusto-ingest/)
    [![PyPI-Version](https://badge.fury.io/py/azure-kusto-ingest.svg)](https://badge.fury.io/py/azure-kusto-ingest)
    * [azure-mgmt-kusto](https://pypi.org/project/azure-mgmt-kusto/)
    [![PyPI-Version](https://badge.fury.io/py/azure-mgmt-kusto.svg)](https://badge.fury.io/py/azure-mgmt-kusto)