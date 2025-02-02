---
title: Troubleshooting Azure IoT Hub error 403004 DeviceMaximumQueueDepthExceeded
description: Understand how to fix error 403004 DeviceMaximumQueueDepthExceeded 
author: kgremban
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: kgremban
ms.custom: [amqp, mqtt]
#Customer intent: As a developer or operator for Azure IoT Hub, I want to resolve 403004 DeviceMaximumQueueDepthExceeded errors.
---

# 403004 DeviceMaximumQueueDepthExceeded

This article describes the causes and solutions for **403004 DeviceMaximumQueueDepthExceeded** errors.

## Symptoms

When trying to send a cloud-to-device message, the request fails with the error **403004** or **DeviceMaximumQueueDepthExceeded**.

## Cause

The underlying cause is that the number of messages enqueued for the device exceeds the [queue limit (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

The most likely reason that you're running into this limit is because you're using HTTPS to receive the message, which leads to continuous polling using `ReceiveAsync`, resulting in IoT Hub throttling the request.

## Solution

The supported pattern for cloud-to-device messages with HTTPS is intermittently connected devices that check for messages infrequently (less than every 25 minutes). To reduce the likelihood of running into the queue limit, switch to AMQP or MQTT for cloud-to-device messages.

Alternatively, enhance device side logic to complete, reject, or abandon queued messages quickly, shorten the time to live, or consider sending fewer messages. See [C2D message time to live](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Lastly, consider using the [Purge Queue API](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) to periodically clean up pending messages before the limit is reached.