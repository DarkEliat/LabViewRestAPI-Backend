# LabVIEW REST API Backend

A LabVIEW-based REST API backend service for controlling servo motors via HTTP endpoints. The system provides bidirectional communication between LabVIEW, ESP32 devices, and web clients, supporting both manual and automatic operation modes.

## Overview

This project implements a RESTful web service in LabVIEW that acts as a middleware between web clients and servo motor controllers (ESP32 devices). It provides endpoints for querying servo status, sending control commands, and managing automatic operation modes.

## Features

- **RESTful Web Service**: HTTP endpoints for servo control and status monitoring
- **Dual Operation Modes**: Manual and automatic servo control
- **ESP32 Integration**: Communication with ESP32-based servo controllers via HTTP
- **Connection Status Tracking**: Real-time monitoring of servo connection states
- **File-based Configuration**: Network settings managed through INI configuration files
- **Functional Global Variables**: State management pattern for shared data
- **Postman Collection**: Pre-configured API testing collection

## Architecture

The project follows a modular architecture with clear separation of concerns:

### Core Components

1. **WebService Module** (`WebService/`)

   - REST API endpoints for client communication
   - Web service initialization and configuration
   - Endpoints:
     - `GET /ServoControlService/status` - Retrieve aggregated servo data
     - `POST /ServoControlService/actuator` - Send servo control commands
2. **ServoCommunication Module** (`ServoCommunication/`)

   - HTTP communication with ESP32 servo controllers
   - Connection management and status tracking
   - Endpoints:
     - `GET_receive_ServoStatus_fromServo.vi` - Fetch status from servo
     - `POST_sendNew_ServoStatus_toServo.vi` - Send commands to servo
3. **DataTypes** (`DataTypes/`)

   - Type definitions for servo status, connection parameters, and operation modes
   - Custom controls (`.ctl` files) for structured data
4. **FunctionalGlobalVariables** (`FunctionalGlobalVariables/`)

   - State management using Functional Global Variable pattern
   - Shared state for servo data, connection status, and operation parameters
5. **Configuration** (`config/`)

   - Network settings and connection parameters
   - Template-based configuration management

## Project Structure

```
LabViewRestAPI-Backend/
├── Main.vi                          # Main application entry point
├── DataTypes/                       # Type definitions
│   ├── AllServoData.ctl            # Aggregated servo data structure
│   ├── AutoModeParameters.ctl     # Automatic mode configuration
│   ├── OperationTypeForFunctionalGlobalVariable.ctl
│   ├── ServoConnectionParameters.ctl  # Servo connection settings
│   ├── ServoConnectionStatus.ctl   # Connection state tracking
│   ├── ServoStatus.ctl             # Servo status structure
│   └── WebServiceStatus.ctl       # Web service state
├── FunctionalGlobalVariables/      # State management
│   ├── CurrentAllServoData.vi
│   ├── CurrentAutoModeParameters.vi
│   ├── CurrentServoConnectionParameters.vi
│   ├── CurrentServoConnectionStatus.vi
│   ├── CurrentServoStatus.vi
│   └── CurrentWebServiceStatus.vi
├── WebService/                     # REST API endpoints
│   ├── Endpoints/
│   │   ├── GET_send_AllServoData_toClient.vi
│   │   └── POST_receiveNew_ServoStatus_fromClient.vi
│   └── Startup/
│       └── WebService_Initialization.vi
├── ServoCommunication/            # ESP32 communication
│   ├── Endpoints/
│   │   ├── GET_receive_ServoStatus_fromServo.vi
│   │   └── POST_sendNew_ServoStatus_toServo.vi
│   └── Startup/
│       └── ServoCommunication_Initialization.vi
├── config/                        # Configuration files
│   ├── app.local.ini.example      # Configuration template
│   └── app.local.ini              # Local configuration (gitignored)
├── postman_collection.json        # API testing collection
└── LabViewRestAPI-Backend.lvproj  # LabVIEW project file
```

## Configuration

### Network Settings

Create a local configuration file `config/app.local.ini` based on the template `config/app.local.ini.example`:

```ini
[network]
host = 192.168.x.y
port = 80
use_https = false
timeout_ms = 5000
base_url = http://192.168.x.y:80
```

**Note**: The `config/app.local.ini` file is gitignored to prevent committing local network settings.

## API Endpoints

### LabVIEW Web Service

The LabVIEW service runs on `http://localhost:9090` by default.

#### GET /ServoControlService/status

Retrieves aggregated servo data including status and connection information.

**Response**: JSON object with servo status and connection state

#### POST /ServoControlService/actuator

Sends a servo control command.

**Request Body**:

```json
{
    "status": {
        "mode": "manual",
        "position": 144
    }
}
```

**Parameters**:

- `mode`: Operation mode (`"manual"` or `"automatic"`)
- `position`: Servo position value (integer)

### ESP32 Servo Endpoints

The ESP32 devices expose endpoints for direct communication:

#### GET http://192.168.0.13/status

Retrieves current servo status from ESP32 device.

#### POST http://192.168.0.13/actuator

Sends control command to ESP32 servo controller.

**Request Body**:

```json
{
    "status": {
        "mode": "manual",
        "position": 144
    }
}
```

## Operation Modes

### Manual Mode

Direct control of servo position through API commands. Each request immediately updates the servo position.

### Automatic Mode

Automated operation with configurable parameters. The system manages servo movements based on predefined parameters stored in `AutoModeParameters.ctl`.

## Dependencies

The project requires the following LabVIEW libraries and VIs:

- `LabVIEWHTTPClient.lvlib` - HTTP client functionality
- `NI_LVConfig.lvlib` - Configuration file handling
- `Application Directory.vi` - Application path utilities
- `Check if File or Folder Exists.vi` - File system utilities
- `Path To Command Line String.vi` - Path conversion utilities
- `PathToUNIXPathString.vi` - Path format conversion

## Testing

A Postman collection (`postman_collection.json`) is included for API testing. The collection contains:

- **Server_ESP32**: Direct ESP32 endpoint tests
- **Server_LabView**: LabVIEW web service endpoint tests

Import the collection into Postman to test both server configurations.

## Development History

### Recent Updates

- **v0.1.0** (Latest)

  - Added aggregated servo data structure (`AllServoData.ctl`)
  - Implemented connection status tracking
  - Updated web endpoints to return comprehensive servo information
  - Introduced configuration template system
- **v0.0.0**

  - Initial project structure
  - Basic REST API endpoints
  - Manual and automatic mode support
  - ESP32 communication module
  - Functional Global Variables pattern implementation

### Key Milestones

1. **Servo Communication Module**: Introduced dedicated module for ESP32 communication with HTTP endpoints
2. **File-based Configuration**: Added INI-based configuration system for network settings
3. **Automatic Mode**: Implemented automatic operation mode with parameter management
4. **Project Reorganization**: Restructured project with clear module separation
5. **Enhanced Data Types**: Extended type system for comprehensive servo data management

## Requirements

- LabVIEW 2024 or later
- Network connectivity to ESP32 servo controllers
- HTTP client libraries (included in LabVIEW)

## License

[Specify license if applicable]

## Author

DarkEliat (Jan Kostka)
