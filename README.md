# Azure-IoT-Smart-Device-Lab-IoT-Hub-IoT-Central-and-a-Simulated-Raspberry-Pi-
Hands-on lab provisioning an Azure IoT Hub and IoT Central application, then connecting a simulated Raspberry Pi device to send live temperature and humidity telemetry into the cloud.

# Azure IoT Smart Device Lab (IoT Hub, IoT Central and a Simulated Raspberry Pi)

`Azure IoT Hub` · `Azure IoT Central` · `Raspberry Pi Azure IoT Online Simulator` · `IoT Plug and Play` · `Device Provisioning`

## Overview
This lab was hands on practice with setting up an end to end IoT pipeline in Azure, from provisioning the backend services to getting a simulated device actually sending telemetry into the cloud. I built an IoT Hub and an IoT Central application in the same resource group, registered a device in each, and used the Raspberry Pi Azure IoT Online Simulator to send live temperature and humidity readings from a virtual BME280 sensor into IoT Hub.

## Objective
Get comfortable standing up the core pieces of an Azure IoT solution (a resource group, an IoT Hub, and an IoT Central application), registering devices against each, and confirming that telemetry actually flows from a client into the hub rather than just trusting that the configuration is correct.

## Environment
- **Cloud platform:** Microsoft Azure
- **Resource group:** `iot-group24`, region East US
- **IoT Hub:** `iot-smart24`, Standard tier, hostname `iot-smart24.azure-devices.net`, daily message limit 400000, minimum TLS version 1.2
- **IoT Central application:** `mysupersmartdevice24`
- **Device simulator:** Raspberry Pi Azure IoT Online Simulator (Fritzing based virtual wiring of a BME280 sensor and an LED to a simulated Raspberry Pi 3 Model B)

## Tools I Used

| Tool | What It Does | Why I Used It |
|------|--------------|----------------|
| **Azure IoT Hub** | Managed cloud gateway for bidirectional device communication | Central endpoint that the simulated device sent telemetry to |
| **Azure IoT Central** | SaaS style IoT application platform built on top of IoT Hub | Used to manage devices through prebuilt templates instead of writing a custom backend |
| **Raspberry Pi Azure IoT Online Simulator** | Browser based device simulator | Sent simulated temperature and humidity readings without needing physical hardware |
| **IoT Plug and Play mobile app** | Turns a phone into an IoT Central device | Registered a phone as a device under IoT Central using a Device Provisioning Service model |

## What I Did

### Setting Up the Resource Group
1. Created the resource group `iot-group24` in East US to hold both IoT services together.
2. Confirmed both resources landed in the same group: the `iot-smart24` IoT Hub and the `mysupersmartdevice24` IoT Central Application.

### Provisioning the IoT Hub
1. Created `iot-smart24` on the Standard tier so it would support the full range of IoT Hub features rather than the more limited Free or Basic tiers.
2. Checked the Overview and Usage tabs to confirm the hub was active, with a daily message quota of 400000 and zero messages used and zero devices connected at that point.
3. Registered a device named `samuel` under Device Management, Devices, using Shared Access Signature authentication.

### Connecting a Simulated Device
1. Opened the Raspberry Pi Azure IoT Online Simulator, which comes preloaded with a Fritzing diagram wiring a BME280 temperature and pressure sensor and an LED to a simulated Raspberry Pi.
2. Set the device connection string in the code, pointing it at the `iot-smart24` hub with the `samuel` device ID and its shared access key. I am intentionally not including that connection string here, since it contains a live key. In a real repo this line should always be replaced with a placeholder or pulled from an environment variable rather than committed as is.
3. Ran the simulator and watched the console log each message as it was sent, for example a simulated temperature and humidity reading tagged with an incrementing message ID, followed by a confirmation line reading "Message sent to Azure IoT Hub" for each one.

### Setting Up IoT Central
1. Created the `mysupersmartdevice24` IoT Central application inside the same resource group.
2. Looked through the built in device templates available (IoT Plug and Play mobile, Inventory Gateways) to see how IoT Central organizes devices by template rather than treating them as flat entries.
3. Registered a phone as a device named `smartphone24` under the IoT Plug and Play mobile template, using the phone as a device model rather than a physical sensor kit.
4. Used the Get Started tab for that device, which walks through downloading the IoT Plug and Play mobile app and scanning a QR code to link the phone to IoT Central.
5. Confirmed on the Devices list that `smartphone24`, along with two gateway devices under the Inventory Gateways template, all showed a status of Provisioned.
6. Opened `smartphone24` again after linking it and confirmed it now showed as Connected, with a timestamp for the last data received.

## What's in This Repo

```
azure-iot-smart-device-lab/
├── README.md                          # This file
└── screenshots/
    ├── 01-iot-hub-overview.png        # iot-smart24 IoT Hub overview and usage
    ├── 02-raspberry-pi-simulator.png  # Simulated device sending telemetry
    ├── 03-iot-hub-devices.png         # Registered device "samuel" in IoT Hub
    ├── 04-iot-central-devices.png     # All devices view in IoT Central
    ├── 05-resource-group-overview.png # iot-group24 holding both resources
    └── 06-iot-central-device-detail.png # smartphone24 Get Started and QR code
```

## Skills I Picked Up
- **Standing up an IoT Hub end to end,** from creating the resource, choosing a tier, checking usage limits, to registering a device against it and watching real telemetry arrive.
- **Understanding the difference between IoT Hub and IoT Central,** IoT Hub as the lower level messaging backbone, and IoT Central as a higher level application layer built on top of it with templates and a more guided device management experience.
- **Working with device templates,** seeing how IoT Central groups devices like IoT Plug and Play mobile and Inventory Gateways under templates rather than managing every device as a one off.
- **Turning a phone into an IoT device,** using the IoT Plug and Play mobile app and a QR code to provision a real handheld device against an IoT Central template instead of only simulating hardware.
- **Being careful with connection strings,** noticing that a device connection string carries a live shared access key and treating it the same way I would treat any other credential, meaning it does not belong in a public README or a committed file.

## How This Applies in the Real World
Most real IoT deployments follow this same shape: a device (or a fleet of them) authenticates against IoT Hub or IoT Central, sends telemetry on a schedule, and the platform handles the reliability and scale so the application layer does not have to. Choosing between raw IoT Hub and IoT Central usually comes down to how much you want to build yourself versus how much you want a managed application experience with less custom development.

The connection string handling in this lab is also a small but realistic reminder of a bigger security habit, treating any device credential, API key, or shared access signature as something to store securely and rotate rather than something to leave sitting in a script or a public repository.

## Where I'm Coming From
I'm making the jump into cybersecurity from a background in **healthcare**, and I'm currently studying for **CompTIA Security+** while building labs like this one to get real hands on reps in. This particular lab sits more on the cloud and IoT side than pure security work, but provisioning cloud resources, managing device identities, and being deliberate about credential handling all feed directly into the security mindset I'm trying to build.

## What I Want to Learn Next
- Wiring up IoT Hub message routing so telemetry can be filtered and sent to different downstream endpoints
- Setting up alerts or rules in IoT Central based on telemetry thresholds, for example a temperature spike
- Looking at Device Provisioning Service (DPS) for provisioning many devices at scale instead of registering them one at a time
- Exploring Azure IoT security recommendations, like per device certificates instead of shared access signatures

## Limitations & What I'd Do Differently in Production
- **The simulator is not a physical device.** A production setup would need to account for real world issues like unreliable connectivity and power loss that a browser based simulator does not have to deal with.
- **Shared Access Signature authentication is simpler but less secure than certificate based authentication.** In production I would look at X.509 certificates for device identity instead.
- **Only one device was tested per service.** A real deployment would need to think through provisioning and monitoring at fleet scale, not just a single device.
- **No message routing or alerting was configured.** This lab focused on getting data in, not on what happens to it afterward.

## References
- [Azure IoT Hub Documentation](https://learn.microsoft.com/azure/iot-hub/)
- [Azure IoT Central Documentation](https://learn.microsoft.com/azure/iot-central/)
- [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/)
- [IoT Plug and Play Documentation](https://learn.microsoft.com/azure/iot-develop/overview-iot-plug-and-play)
