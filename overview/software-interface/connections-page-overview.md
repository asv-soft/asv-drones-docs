### Connections Page

#### Overview

The Connections page in Asv.Drones serves as a central hub for managing and configuring connections with drones and devices. This page allows users to specify essential settings for addressing and routing in the MAVLink network, add new connection ports, and monitor the status of connected devices.

#### Settings

1. **System ID and Component ID:**
   - These settings are required for addressing and routing in the MAVLink network. Users must specify a unique System ID and Component ID to ensure proper communication between Asv.Drones and connected devices.

2. **Heartbeat Rate:**
   - Users can set the rate at which the HEARTBEAT message is transmitted to announce the existence of the system on the MAVLink network. This helps establish and maintain connections with other devices.

3. **Heartbeat Timeout:**
   - After the specified timeout period, devices that have not sent a heartbeat message will be removed from the device list, helping to manage and update the list of connected devices.

#### Connection Ports

- **Add New Connection Ports:**
  - Users can add new connection ports to establish communication with devices. Supported connection types include serial ports, TCP ports, and UDP ports.

#### Connected Devices List

- **Device Status and Description:**
  - At the bottom of the Connections page, users can view a list of all connected devices. Each device is accompanied by its status and a basic description, providing insights into the current state of the connections.

#### Example Scenario

1. **Configuring System and Component IDs:**
   - Users access the Connections page and configure unique System ID and Component ID settings for Asv.Drones.

2. **Setting Heartbeat Rate and Timeout:**
   - Users specify the heartbeat rate and timeout settings to optimize communication and device management.

3. **Adding Serial Connection Port:**
   - Users add a new TCP connection port to establish communication with a connected drone.

4. **Viewing Connected Devices:**
   - The list of connected devices displays the status and description of each connected device, providing a comprehensive overview of the current connections.

The Connections page is a central hub for managing communication settings, adding connection ports, and monitoring the status of connected devices, ensuring a robust and efficient network for drone management.