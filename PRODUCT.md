# DTV Plugin - Product Overview

## Introduction

The DTV (Digital Television) plugin is a production-ready WPEFramework component that brings comprehensive digital broadcast television capabilities to RDK-powered devices. Built on the proven DTVKit middleware, it enables set-top boxes, smart TVs, and streaming devices to receive, decode, and present live digital TV broadcasts across multiple DVB standards including DVB-T/T2 (terrestrial), DVB-S/S2 (satellite), and DVB-C (cable).

## Key Features

### Broadcast Standards Support
- **DVB-T/T2**: Full support for terrestrial digital TV with configurable bandwidth (7MHz/8MHz) and channel plans
- **DVB-S/S2**: Satellite reception with LNB configuration, DiSEqC control, and multi-satellite support
- **DVB-C**: Cable TV integration with frequency scanning and QAM modulation
- Automatic detection and fallback between DVB-T and DVB-T2 standards

### Service Discovery and Management
- Automated service scanning across multiple frequencies and transponders
- Network Information Table (NIT) parsing for efficient multi-frequency searches
- Service filtering by type (TV, radio, data services)
- Logical Channel Number (LCN) assignment and sorting
- Service list import/export for faster provisioning
- Background service database updates without playback interruption

### Electronic Program Guide (EPG)
- Complete EPG data extraction from EIT present/following and schedule tables
- Rich metadata including event titles, descriptions, genres, parental ratings
- Program start times, duration, and running status
- Extended event information for detailed program descriptions
- Support for multiple event languages
- Real-time EPG updates during service playback

### Playback Control
- Seamless service tuning and playback initiation via DVB URI
- Audio track selection for multi-language broadcasts
- Subtitle stream selection supporting DVB subtitles
- Teletext page rendering for legacy content
- Service component enumeration (video, audio, subtitle, teletext)
- Robust error handling and recovery for signal interruptions

### Signal Quality Monitoring
- Real-time signal strength measurement
- Signal-to-Noise Ratio (SNR) reporting
- Bit Error Rate (BER) monitoring
- Transport stream lock status
- Frequency and modulation parameter reporting

## Use Cases

### Primary Use Case: Linear TV Viewing
A viewer turns on their RDK device and navigates to the live TV interface. The DTV plugin:
1. Loads the previously scanned service database
2. Tunes to the last watched service or default channel
3. Displays current program information from EPG
4. Allows channel surfing with instant tuning
5. Updates EPG data in background for future program planning

**Benefits**: Instant access to live TV with rich program information, familiar linear viewing experience.

### Installation and Setup
During initial device setup or retail mode:
1. User selects their country/region
2. Plugin retrieves appropriate tuning parameters
3. Automatic service search discovers all available channels
4. Services are sorted by LCN for intuitive channel order
5. EPG data is populated for current and upcoming programs

**Benefits**: Zero-configuration TV setup, optimized for local broadcast parameters, professional channel lineup.

### Multi-Satellite Configuration
For satellite installations with multiple LNBs and orbital positions:
1. Installer configures LNB settings (local oscillator frequencies, polarization)
2. Adds satellite configurations (orbital position, transponder lists)
3. Executes multi-satellite search operation
4. All services from multiple satellites appear in unified channel list

**Benefits**: Professional-grade satellite installation support, maximizes service availability, supports complex antenna configurations.

### Program Discovery
Viewer wants to find what's on TV now or later:
1. Application requests now/next events for all services
2. EPG data presented in interactive guide format
3. User browses by time, genre, or channel
4. Selects program to view or schedule recording

**Benefits**: Enhanced content discovery, competitive with linear TV operators, improved viewer engagement.

## API Capabilities

The plugin exposes a comprehensive JSON-RPC API enabling applications to:

### Configuration APIs
- `numberOfCountries`, `countryList`, `country`: Retrieve and set regional configuration
- `addLnb`, `lnbList`: Manage satellite LNB configurations
- `addSatellite`, `satelliteList`: Configure satellite orbital positions

### Search APIs
- `startServiceSearch`: Initiate automatic or manual service scanning with progress callbacks
- `finishServiceSearch`: Commit discovered services to database
- `numberOfServices`, `serviceList`: Query available services with filtering

### Playback APIs
- `startPlaying`, `stopPlaying`: Control service tuning and playback
- `status`: Query current playback state and service information
- `signalInfo`: Monitor signal quality metrics

### EPG APIs
- `nowNextEvents`: Retrieve current and next program for services
- `scheduleEvents`: Query EPG schedule for date/time range
- `extendedEventInfo`: Access detailed program descriptions

### Service Information APIs
- `serviceInfo`: Detailed service metadata (name, LCN, provider, type)
- `serviceComponents`: Enumerate audio/video/subtitle/teletext components
- `transportInfo`: Access transport stream and network parameters

### Event Notifications
- `searchstatus`: Real-time search progress updates
- `serviceevent`: Service availability and EPG update notifications

## Integration Benefits

### For Application Developers
- **Simple Integration**: Clean JSON-RPC API abstracts DVB complexity
- **Event-Driven**: Asynchronous notifications for responsive UIs
- **Type Safety**: Well-defined data structures and error codes
- **Comprehensive**: Single API covers entire TV workflow from search to playback

### For System Integrators
- **Stability**: Out-of-process architecture isolates failures
- **Performance**: Optimized service scanning and EPG parsing
- **Flexibility**: Configurable for various DVB standards and regional requirements
- **Standards Compliant**: Full DVB specification adherence ensures interoperability

### For End Users
- **Fast Channel Switching**: Optimized tuning minimizes zapping time
- **Reliable**: Robust error recovery maintains viewing experience
- **Feature Rich**: Access to all broadcast features including subtitles and multi-audio
- **Future Proof**: Support for latest DVB-T2 and DVB-S2 standards

## Performance Characteristics

- **Service Search Speed**: Full terrestrial scan (Ch 21-69) completes in 3-5 minutes
- **Tuning Latency**: Channel change typically completes in <1 second
- **EPG Load Time**: Present/following data available immediately; 7-day schedule populated in background
- **Memory Footprint**: <50MB typical for database with 200+ services and EPG data
- **CPU Utilization**: <5% during normal playback, 10-15% during service search

## Reliability Features

- **Process Isolation**: Plugin runs out-of-process to prevent system crashes
- **Automatic Recovery**: Signal loss detection and retuning logic
- **Database Integrity**: Service database protected with atomic updates
- **Error Reporting**: Comprehensive error codes and diagnostic logging
- **Graceful Degradation**: Continues operation even with partial EPG or service failures

## Deployment Scenarios

- **Hybrid STBs**: Combines OTT streaming with linear broadcast TV
- **Smart TVs**: Integrated broadcast and connected TV experiences
- **Hospitality**: Professional TV systems for hotels and venues
- **Retail Demo**: Showcase live TV capabilities in retail environments
- **IPTV Gateway**: Bridge DVB services to IP-based distribution
