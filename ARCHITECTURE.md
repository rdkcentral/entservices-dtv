# DTV Plugin Architecture

## Overview

The DTV (Digital Television) plugin is a WPEFramework plugin that provides comprehensive digital TV broadcast functionality for RDK devices. It integrates with the DTVKit middleware to deliver DVB (Digital Video Broadcasting) capabilities including service discovery, tuning, playback, and Electronic Program Guide (EPG) management.

## System Architecture

### Component Layers

```
┌─────────────────────────────────────────────────────┐
│          Application Layer (JSON-RPC)               │
│         (Thunder/WPEFramework Clients)              │
└────────────────┬────────────────────────────────────┘
                 │ JSON-RPC API
┌────────────────▼────────────────────────────────────┐
│              DTV Plugin (In-Process)                │
│  ┌─────────────────────────────────────────────┐   │
│  │  DTV.cpp/h (JSONRPC Handler)               │   │
│  │  - Request marshalling                      │   │
│  │  - Response formatting                      │   │
│  │  - Event notification routing               │   │
│  └──────────────┬──────────────────────────────┘   │
│                 │ C++ Interface (IDTV)              │
│  ┌──────────────▼──────────────────────────────┐   │
│  │  DTVImpl.cpp/h (Out-of-Process)            │   │
│  │  - Service management                       │   │
│  │  - Tuner control                            │   │
│  │  - EPG data handling                        │   │
│  │  - Search operations                        │   │
│  └──────────────┬──────────────────────────────┘   │
└─────────────────┼──────────────────────────────────┘
                  │ DVB API Calls
┌─────────────────▼──────────────────────────────────┐
│           DTVKit Middleware                         │
│  - DVB Stack (DVB-T/T2, DVB-S/S2, DVB-C)          │
│  - SI/PSI Parsing                                  │
│  - Service Database                                 │
│  - Hardware Abstraction                            │
└────────────────┬───────────────────────────────────┘
                 │ Platform HAL
┌────────────────▼───────────────────────────────────┐
│        Hardware Layer (Tuner, Demux)               │
└────────────────────────────────────────────────────┘
```

## Core Components

### 1. DTV Plugin (DTV.cpp/h)
**Responsibility**: JSON-RPC API exposure and Thunder framework integration

- Implements `PluginHost::IPlugin` and `PluginHost::JSONRPC` interfaces
- Manages plugin lifecycle (Initialize, Deinitialize)
- Registers JSON-RPC methods for external API access
- Handles asynchronous event notifications to clients
- Marshalls data between JSON and C++ interfaces
- Maintains connection to out-of-process implementation

**Key Features**:
- Country and configuration management
- LNB and satellite configuration (DVB-S)
- Service search operations
- Service playback control
- EPG data retrieval
- Transport stream information
- Signal quality monitoring

### 2. DTVImpl (DTVImpl.cpp/h)
**Responsibility**: Business logic and DTVKit middleware integration

- Implements `Exchange::IDTV` interface
- Executes in a separate process for isolation and stability
- Direct integration with DTVKit API functions
- Maintains service database and state
- Handles DVB standard-specific operations (DVB-T, DVB-S, DVB-C)
- Processes SI/PSI tables for EPG and service information
- Manages tuning parameters and frequency tables

**Key Classes**:
- `CountryImpl`: Country configuration management
- `LnbImpl`: LNB settings for satellite reception
- `SatelliteImpl`: Satellite configuration data
- `ServiceImpl`: Service metadata and properties
- `ComponentImpl`: Audio/video/subtitle component information
- `EitEventImpl`: EPG event data representation

### 3. Helper Utilities (helpers/)
**Responsibility**: Common utility functions and macros

- `UtilsJsonRpc.h`: JSON-RPC helper macros for request/response handling
- `UtilsLogging.h`: Logging infrastructure with thread safety

## Data Flow

### Service Search Flow
1. Client initiates search via `startServiceSearch` JSON-RPC
2. DTV plugin forwards request to DTVImpl
3. DTVImpl configures DTVKit with tuning parameters
4. DTVKit scans frequencies and parses SI/PSI tables
5. Services discovered are stored in internal database
6. Progress notifications sent via `searchstatus` events
7. Client retrieves service list via `serviceList` API

### Playback Flow
1. Client calls `startPlaying` with service DVB URI
2. DTVImpl resolves service from database
3. Tuning request sent to DTVKit with transport parameters
4. DTVKit configures hardware (tuner, demux, decoders)
5. AV streams routed to platform pipeline
6. Playback status events delivered to client
7. EPG updates pushed via `serviceevent` notifications

## Integration Points

### Thunder/WPEFramework
- Plugin registered with Thunder plugin system
- Uses Thunder IPC for out-of-process communication
- Leverages Thunder COM-RPC for interface marshalling
- Event distribution via Thunder notification framework

### DTVKit Middleware
- Statically linked DTVKit libraries (`DTVKIT_LIBRARIES`)
- C API integration via extern "C" declarations
- Platform-specific tuning tables (DVB-T frequency plans)
- SI/PSI table parsing for service discovery

### Hardware Abstraction
- Optional Broadcom Nexus client integration (`libnxclient`)
- Platform power management (`libpmlib`)
- Hardware-specific tuner and demux control

## Configuration

The plugin supports runtime configuration via `DTV.config`:
- `autostart`: Automatic plugin initialization
- `mode`: Execution mode (Local/Out-of-Process)
- `subtitles`: DVB subtitle decoding enable
- `teletext`: EBU teletext decoding enable

## Threading Model

- Main plugin thread handles JSON-RPC requests
- DTVImpl runs in separate process for stability
- DTVKit internal threads for SI/PSI parsing and decoding
- Event notifications dispatched asynchronously via Thunder callbacks

## Error Handling

- WPEFramework error codes used throughout (`Core::ERROR_*`)
- Graceful degradation on hardware failures
- Plugin isolation prevents system-wide crashes
- Detailed error logging with thread identification

## Performance Considerations

- Out-of-process design prevents UI blocking
- Efficient service database queries
- Asynchronous search operations
- Optimized SI/PSI parsing via DTVKit
- Minimal JSON serialization overhead
