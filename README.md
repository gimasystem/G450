# Avaya G450 by SNMP

Zabbix 7.0 template for monitoring Avaya Branch Gateway devices (G430/G450) via SNMP.

- **Template name:** `Avaya G450 by SNMP`
- **Template group:** `Templates/Telephony`
- **Tested device:** Avaya G450
- **Tested firmware:** 36.26.0
- **Template source:** [G450template.xml](./G450template.xml)

## Overview

This template collects hardware health, CPU and memory utilization, H.248 connectivity, ISDN clock source, media gateway status, module inventory, DSP status, cooling fan data, temperature sensor data, and VoIP engine capacity/status.

The template uses SNMP polling and low-level discovery (LLD) to create items for detected modules, DSP cores, fans, temperature sensors, and VoIP engine slots.

### Scope

Included:

- Device and component inventory
- Hardware fault and controller status
- CPU and memory utilization
- H.248 link and registration status
- ISDN clock source and media gateway fault status
- Module, DSP, cooling fan, temperature sensor, and VoIP engine discovery
- Temperature, CPU, memory, and DSP Admin State triggers

Not included:

- ICMP availability monitoring
- Ethernet interface discovery or traffic monitoring

These functions can be added separately by linking an appropriate Zabbix template to the host.

## Requirements and configuration

### Requirements

- Zabbix 7.0
- SNMP access from the Zabbix server or proxy to the Avaya gateway
- An Avaya G430 or G450 supported by the relevant MIB/OID implementation

### Configure SNMP on the gateway

Configure an SNMP read-only community on the gateway and restrict access to the Zabbix server/proxy addresses.

Example CLI syntax (replace the example community and access restrictions with values approved for your environment):

```text
snmp-server community read-only <READ_ONLY_COMMUNITY>
```

Save the configuration using the gateway's supported configuration procedure. Do not use the example community strings `public` or `private` in production.

See the Avaya documentation: [SNMP access configuration](https://documentation.avaya.com/ja-JP/bundle/AvayaG450BranchGatewayCLIReference_10.2.x/page/SNMPAccessConfiguration.html).

### Enable CPU utilization monitoring

The G450 CPU utilization monitoring function is disabled by default. When disabled, CPU utilization values may remain at 0%.

To monitor CPU utilization, enable the CPU utilization monitoring function from the gateway CLI before relying on the CPU items and trigger. Confirm that `CPU Utilization Enable Monitoring` reports the enabled state.

## Template configuration

### Template macros

| Macro | Default | Description |
|---|---:|---|
| `{$CPU.UTIL.CRIT}` | `90` | CPU utilization critical threshold (%) |
| `{$MEMORY.UTIL.WARN}` | `80` | Memory utilization warning threshold (%) |

Adjust these values at the template or host level to suit the operating environment.

## Discovery rules

All five discovery rules run every 6 hours.

| Discovery rule | Key | Discovered resources |
|---|---|---|
| VoIP DSP ID discovery in all DSP slots | `DSPCoreID.discoveryAllSlots` | DSP cores on VoIP engine slots. On G450, motherboard DSP slot IDs are 101–104. |
| Cooling FAN discovery | `FAN.discovery` | Cooling fan sensors, readings, status, and fan thresholds. |
| Module discovery | `ModuleSlot.discovery` | Installed modules, slot, module name, serial number, firmware version, fault mask, and status mask. |
| Ambient Temperature Sensor discovery | `Temperature.discovery` | Temperature sensors, sensor status, and temperature thresholds. |
| VoIP Slot discovery | `VoIPSlot.discovery` | VoIP engine slots. G450 supports up to four VoIP engine modules; G430 supports up to two. |

## Monitored items

The following tables describe the items currently defined in `G450template.xml`. Update intervals are shown as configured; an empty interval means the Zabbix default update interval is used.

### Device, hardware, and H.248

| Item name | Key | Update interval | Purpose |
|---|---|---:|---|
| Component Model Description | `ModelDescription` | 1d | Component product description and software version; linked to host inventory. |
| Component Model Number | `ModelNumber` | Default | Component hardware model number. |
| Component Serial Number | `SerialNumber` | 1d | Gateway serial number; linked to host inventory. |
| Component Firmware Version | `FirmwareVersion` | 1d | Gateway firmware version; linked to host inventory. |
| Hardware Vintage | `HardwareVintage` | 1d | Hardware revision/generation. Check firmware compatibility before upgrades. |
| Hardware Sensor Health | `HardwareFaultMask` | 10m | Hardware sensor fault bitmask, including power, fan, and internal temperature faults. |
| Hardware Controller Status | `HardwareStatusMask` | 1h | Controller fault bitmask covering controller, DSP, I/O, and LAN-related faults. |
| Call controller Link Status | `H248LinkStatus` | 10m | H.248 link state between the media gateway and active call controller. |
| Call controller Link Error Code | `H248LinkErrorCode` | 10m | Most recently received H.248 link error code. |
| Call controller Registration Status | `H248RegistrationState` | 3m | Whether the media gateway is registered with a call controller. |

### CPU

| Item name | Key | Update interval | Purpose |
|---|---|---:|---|
| CPU Utilization (1min Average) | `CpuAverageUtilization` | Default | One-minute average CPU utilization. Requires CPU monitoring to be enabled on the gateway. |
| CPU Utilization (current) | `CpuCurrentUtilization` | 5s | Current CPU utilization. Requires CPU monitoring to be enabled. |
| CPU Utilization Enable Monitoring | `CpuUtilizationEnableMonitoring` | 1h | Indicates whether CPU utilization monitoring is enabled on the gateway. |

### Memory

| Item name | Key | Update interval | Purpose |
|---|---|---:|---|
| total count of memory failures | `MemoryUtilizationAllocationFailures` | 5m | Cumulative number of failed memory allocations. |
| available dynamically allocated memory | `MemoryUtilizationDynAllocMemAvailable` | Default | Available dynamically allocated memory (bytes). |
| allocated memory | `MemoryUtilizationDynAllocMemUsed` | Default | Used dynamically allocated memory (bytes). |
| Memory Usage | `MemoryUtilizationPercentUsed` | 3m | Memory utilization (%). |
| total size of physical RAM | `MemoryUtilizationPhyRam` | 1d | Physical RAM size (bytes). |
| total size of RAM | `MemoryUtilizationTotalRAM` | 1d | Total RAM size reported by the device (bytes). |

### ISDN clock and media gateway status

| Item name | Key | Update interval | Purpose |
|---|---|---:|---|
| ISDN Active Clock Source | `ISDNActiveClockSource` | 1h | Clock source currently in use. |
| ISDN Primary Clock Source | `ISDNPrimaryClockSource` | 1h | Configured primary clock source. |
| ISDN Secondary Clock Source | `ISDNSecondaryClockSource` | 1h | Configured secondary clock source. |
| Media gateway fault | `MediagatewayFaultMask` | 3m | Media gateway processor fault flag. A value other than `00 00` may indicate a fault. |

### Discovered item prototypes

#### DSP cores

The DSP discovery rule creates the following items for each discovered DSP core. The update interval is 3 minutes for all six prototypes.

| Item prototype | Key |
|---|---|
| DSP Core Admin State [{#SNMPINDEX}] | `DSPCoreAdminState.[{#SNMPINDEX}]` |
| DSP Core Channels InUse [{#SNMPINDEX}] | `DSPCoreChannelsInUse.[{#SNMPINDEX}]` |
| DSP Core Demand Test [{#SNMPINDEX}] | `DSPCoreDemandTest.[{#SNMPINDEX}]` |
| DSP Core Demand Test Result [{#SNMPINDEX}] | `DSPCoreDemandTestResult.[{#SNMPINDEX}]` |
| DSP Core Status [{#SNMPINDEX}] | `DSPCoreStatus.[{#SNMPINDEX}]` |
| DSP Core Total Channels [{#SNMPINDEX}] | `DSPCoreTotalChannels.[{#SNMPINDEX}]` |

The Admin State value map defines `busy-out` (1), `release` (2), `camp-on` (3), and `unknown` (255). The Demand Test Result value map defines `pass` (255), error codes 1–6, and `notResponding` (7). DSP Core Status maps 1/2/3 to `idle`/`inUse`/`fault`.

#### Cooling fans

| Item prototype | Key | Update interval | Purpose |
|---|---|---:|---|
| {#PHYDESCR} {#SENSORDISP} | `FanSensorRPM[{#SNMPINDEX}]` | 5m | Fan speed reading; unit is supplied by the sensor discovery macro. |
| {#PHYDESCR} Status | `FanSensorStatus[{#SNMPINDEX}]` | 5m | Fan sensor operational status. |
| {#PHYDESCR} Shutdown threshold | `FanSensorHiShutdown[{#SNMPINDEX}]` | 2h | High-speed shutdown threshold reported by the sensor. |
| {#PHYDESCR} HiWarning threshold | `FanSensorHiWarning[{#SNMPINDEX}]` | 2h | High-speed warning threshold reported by the sensor. |
| {#PHYDESCR} LoWarning threshold | `FanSensorLoWarning[{#SNMPINDEX}]` | 2h | Low-speed warning threshold reported by the sensor. |

The sensor status value map is `ok` (1), `unavailable` (2), and `nonoperational` (3). The RPM and threshold items are numeric values and do not use this status value map. Fan threshold items are collected for reference; no Zabbix trigger prototype currently evaluates these fan thresholds.

After replacing a fan, allow at least one minute before checking its status with the gateway CLI command `show platform fans`.

#### Temperature sensors

| Item prototype | Key | Update interval | Purpose |
|---|---|---:|---|
| {#PHYDESCR} | `AmbientTemperatureSensorCelsius[{#SNMPINDEX}]` | 5m | Temperature measured by the sensor (°C). |
| {#PHYDESCR} Status | `AmbientTemperatureSensorStatus[{#SNMPINDEX}]` | 5m | Sensor operational status. |
| {#PHYDESCR} HiShutdown Threshold | `AmbientTemperatureSensorHiShutdown[{#SNMPINDEX}]` | 2h | High-temperature shutdown threshold (°C). |
| {#PHYDESCR} HiWarning Threshold | `AmbientTemperatureSensorHiWarning[{#SNMPINDEX}]` | 2h | High-temperature warning threshold (°C). |
| {#PHYDESCR} LoWarning Threshold | `AmbientTemperatureSensorLoWarning[{#SNMPINDEX}]` | 2h | Low-temperature warning threshold (°C). |

The sensor status value map is `ok` (1), `unavailable` (2), and `nonoperational` (3). The temperature item is compared with the high warning and high shutdown thresholds by Zabbix trigger prototypes. The low warning threshold is collected for reference; no low-temperature trigger prototype is currently defined.

#### Modules

| Item prototype | Key | Update interval | Purpose |
|---|---|---:|---|
| ModuleSlot V{#SLOT} Module Name | `ModuleSlot[{#SNMPINDEX}]` | 2h | Module name/type. |
| ModuleSlot V{#SLOT} SerialNumber | `ModuleSerialNumber[{#SNMPINDEX}]` | 2h | Module serial number. |
| ModuleSlot V{#SLOT} Firmware version | `ModuleFWVersion[{#SNMPINDEX}]` | 2h | Module firmware version. |
| ModuleSlot V{#SLOT} FaultMask | `ModuleFaultMask[{#SNMPINDEX}]` | 5m | Module fault mask. |
| ModuleSlot V{#SLOT} ModuleStatusMask | `ModuleStatusMask[{#SNMPINDEX}]` | 5m | Module status mask. |

#### VoIP engines

| Item prototype | Key | Update interval | Purpose |
|---|---|---:|---|
| VoIP Average Occupancy No{#VOIPSLOT} | `VoipAverageOccupancy_[{#VOIPSLOT}]` | 5m | Five-minute average VoIP engine occupancy. |
| VoIP Channels In Use No{#VOIPSLOT} | `VoipChannelsInUse_[{#VOIPSLOT}]` | Default | Number of channels currently in use. |
| VoIP Fault Mask No{#VOIPSLOT} | `VoipFaultMask_[{#VOIPSLOT}]` | 5m | VoIP engine fault bitmask. |
| VoIP Hyper Activity No{#VOIPSLOT} | `VoipHyperactivity_[{#VOIPSLOT}]` | 5m | Overload state; `normal(1)`, `hyperactive(2)`, or `unknown(255)`. |
| VoIP Total Channels | `VoipTotalChannels_[{#VOIPSLOT}]` | 30m | Total channels managed by Communication Manager (ACM). |

## Triggers

The following triggers are currently defined in the template. Thresholds can be adjusted using the template macros described above.

| Trigger name | Condition | Severity |
|---|---|---|
| High CPU utilization | CPU one-minute average remains above `{$CPU.UTIL.CRIT}` for 5 minutes (`min(...,5m)`). | High |
| High memory utilization | Memory utilization remains above `{$MEMORY.UTIL.WARN}` for 10 minutes (`min(...,10m)`). | Warning |
| DSP Core is not in release state [{#SNMPINDEX}] | DSP Core Admin State is not `release(2)`. | Information |
| Temperature has reached the warning threshold. | Sensor temperature is above its HiWarning threshold. | High |
| Temperature has reached the critical threshold. | Sensor temperature is above its HiShutdown threshold. | Disaster |

The CPU and memory expressions use a time window to reduce alerts caused by short-lived spikes. The DSP Admin State trigger checks the current value; it is intended to indicate a core that is not in the release state, including maintenance busy-out states.

### Trigger validation

Trigger operation has not been fully validated by forcing fault conditions on production hardware. In particular, high-temperature and shutdown conditions have not been deliberately induced on a physical G450. Validate trigger behavior in a controlled test environment before relying on it for operational response.

## Graphs

- **Memory Usage** — displays dynamically allocated memory used.
- **Temperature sensor graph prototypes** — display the sensor temperature together with its low warning, high warning, and high shutdown thresholds.

## Notes and limitations

- SNMP polling intervals shown above are the intervals configured in the template. Items without an explicit interval use the Zabbix default.
- CPU utilization data is not meaningful until CPU utilization monitoring is enabled on the gateway.
- Fan threshold items are informational threshold readings; the current template does not define fan threshold trigger prototypes.
- The low temperature warning threshold is collected, but no low-temperature trigger prototype is defined.
- ICMP and Ethernet interface monitoring are intentionally outside this template's scope.
- The template has been tested with G450 firmware 36.26.0. Behavior on other hardware revisions and firmware versions should be verified.

## SNMP OID reference

The following OIDs are the numeric OIDs configured in the current template export. They are listed here for troubleshooting and implementation reference.

### Static items

| Item name | Key | SNMP OID |
|---|---|---|
| CPU Utilization (1min Average) | `CpuAverageUtilization` | `.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.5.10` |
| CPU Utilization (current) | `CpuCurrentUtilization` | `.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.6.10` |
| CPU Utilization Enable Monitoring | `CpuUtilizationEnableMonitoring` | `.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.2.10` |
| Component Firmware Version | `FirmwareVersion` | `.1.3.6.1.4.1.6889.2.9.1.2.1.3.0` |
| Call controller Link Error Code | `H248LinkErrorCode` | `.1.3.6.1.4.1.6889.2.9.1.3.4.0` |
| Call controller Link Status | `H248LinkStatus` | `.1.3.6.1.4.1.6889.2.9.1.3.3.0` |
| Call controller Registration Status | `H248RegistrationState` | `.1.3.6.1.4.1.6889.2.9.1.3.1.0` |
| Hardware Sensor Health | `HardwareFaultMask` | `.1.3.6.1.4.1.6889.2.9.1.1.10.12.0` |
| Hardware Controller Status | `HardwareStatusMask` | `.1.3.6.1.4.1.6889.2.9.1.1.10.13.0` |
| Hardware Vintage | `HardwareVintage` | `.1.3.6.1.4.1.6889.2.9.1.1.5.0` |
| ISDN Active Clock Source | `ISDNActiveClockSource` | `.1.3.6.1.4.1.6889.2.9.1.2.3.3.0` |
| ISDN Primary Clock Source | `ISDNPrimaryClockSource` | `.1.3.6.1.4.1.6889.2.9.1.2.3.1.0` |
| ISDN Secondary Clock Source | `ISDNSecondaryClockSource` | `.1.3.6.1.4.1.6889.2.9.1.2.3.2.0` |
| Media gateway fault | `MediagatewayFaultMask` | `.1.3.6.1.4.1.6889.2.9.1.2.1.15.0` |
| total count of memory failures | `MemoryUtilizationAllocationFailures` | `.1.3.6.1.4.1.6889.2.1.11.1.2.4.0` |
| available dynamically allocated memory | `MemoryUtilizationDynAllocMemAvailable` | `.1.3.6.1.4.1.6889.2.1.11.1.2.3.3.0` |
| allocated memory | `MemoryUtilizationDynAllocMemUsed` | `.1.3.6.1.4.1.6889.2.1.11.1.2.3.1.0` |
| Memory Usage | `MemoryUtilizationPercentUsed` | `.1.3.6.1.4.1.6889.2.1.11.1.2.6.1.3.10` |
| total size of physical RAM | `MemoryUtilizationPhyRam` | `.1.3.6.1.4.1.6889.2.1.11.1.2.6.1.2.10` |
| total size of RAM | `MemoryUtilizationTotalRAM` | `.1.3.6.1.4.1.6889.2.1.11.1.2.1.0` |
| Component Model Description | `ModelDescription` | `.1.3.6.1.4.1.6889.2.9.1.1.3.0` |
| Component Model Number | `ModelNumber` | `.1.3.6.1.4.1.6889.2.9.1.1.2.0` |
| Component Serial Number | `SerialNumber` | `.1.3.6.1.4.1.6889.2.9.1.1.4.0` |

### Discovery rules

| Discovery rule | Key | SNMP discovery OID |
|---|---|---|
| VoIP DSP ID discovery in all DSP slots | `DSPCoreID.discoveryAllSlots` | `discovery[{#COREID},.1.3.6.1.4.1.6889.2.9.1.4.6.1.1]` |
| Cooling FAN discovery | `FAN.discovery` | `discovery[{#TEMPERSENSORTYPE},1.3.6.1.2.1.99.1.1.1.1,{#SENSORVALUE},1.3.6.1.2.1.99.1.1.1.4,{#SENSORSTATUS},1.3.6.1.2.1.99.1.1.1.5,{#PHYDESCR},1.3.6.1.2.1.47.1.1.1.1.2,{#SENSORDISP},1.3.6.1.2.1.99.1.1.1.6,{#SENSORSHUTDOWN},1.3.6.1.4.1.6889.2.1.99.1.1.1,{#SENSORHIWARNING},1.3.6.1.4.1.6889.2.1.99.1.1.2,{#SENSORLOWWARNING},1.3.6.1.4.1.6889.2.1.99.1.1.5]` |
| Module discovery | `ModuleSlot.discovery` | `discovery[{#SLOT},.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.1,{#MODULENAME},.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.2,{#MODULESN},.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.5,{#MODULEFW},.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.8,{#MODULEFAULTMASK},.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.10,{#MODULESTATUSMASK},.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.11]` |
| Ambient Temperature Sensor discovery | `Temperature.discovery` | `discovery[{#TEMPERSENSORTYPE},1.3.6.1.2.1.99.1.1.1.1,{#SENSORVALUE},1.3.6.1.2.1.99.1.1.1.4,{#SENSORSTATUS},1.3.6.1.2.1.99.1.1.1.5,{#PHYDESCR},1.3.6.1.2.1.47.1.1.1.1.2,{#SENSORDISP},1.3.6.1.2.1.99.1.1.1.6,{#SENSORSHUTDOWN},1.3.6.1.4.1.6889.2.1.99.1.1.1,{#SENSORHIWARNING},1.3.6.1.4.1.6889.2.1.99.1.1.2,{#SENSORLOWWARNING},1.3.6.1.4.1.6889.2.1.99.1.1.5]` |
| VoIP Slot discovery | `VoIPSlot.discovery` | `discovery[{#VOIPSLOT},.1.3.6.1.4.1.6889.2.9.1.4.5.1.1]` |

### Item prototypes

| Item prototype | Key | SNMP OID |
|---|---|---|
| DSP Core Admin State [{#SNMPINDEX}] | `DSPCoreAdminState.[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.4.{#SNMPINDEX}` |
| DSP Core Channels InUse [{#SNMPINDEX}] | `DSPCoreChannelsInUse.[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.3.{#SNMPINDEX}` |
| DSP Core Demand Test [{#SNMPINDEX}] | `DSPCoreDemandTest.[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.6.{#SNMPINDEX}` |
| DSP Core Demand Test Result [{#SNMPINDEX}] | `DSPCoreDemandTestResult.[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.7.{#SNMPINDEX}` |
| DSP Core Status [{#SNMPINDEX}] | `DSPCoreStatus.[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.5.{#SNMPINDEX}` |
| DSP Core Total Channels [{#SNMPINDEX}] | `DSPCoreTotalChannels.[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.2.{#SNMPINDEX}` |
| {#PHYDESCR} Shutdown threshold | `FanSensorHiShutdown[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.1.99.1.1.1.{#SNMPINDEX}` |
| {#PHYDESCR} HiWarning threshold | `FanSensorHiWarning[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.1.99.1.1.2.{#SNMPINDEX}` |
| {#PHYDESCR} LoWarning threshold | `FanSensorLoWarning[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.1.99.1.1.5.{#SNMPINDEX}` |
| {#PHYDESCR} {#SENSORDISP} | `FanSensorRPM[{#SNMPINDEX}]` | `.1.3.6.1.2.1.99.1.1.1.4.{#SNMPINDEX}` |
| {#PHYDESCR} Status | `FanSensorStatus[{#SNMPINDEX}]` | `.1.3.6.1.2.1.99.1.1.1.5.{#SNMPINDEX}` |
| ModuleSlot V{#SLOT} FaultMask | `ModuleFaultMask[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.10.{#SNMPINDEX}` |
| ModuleSlot V{#SLOT} Firmware version | `ModuleFWVersion[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.8.{#SNMPINDEX}` |
| ModuleSlot V{#SLOT} SerialNumber | `ModuleSerialNumber[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.5.{#SNMPINDEX}` |
| ModuleSlot V{#SLOT} Module Name | `ModuleSlot[{#SNMPINDEX}]` | `.1.3.6.1.4.1.81.8.1.1.5.{#SNMPINDEX}` |
| ModuleSlot V{#SLOT} ModuleStatusMask | `ModuleStatusMask[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.11.{#SNMPINDEX}` |
| {#PHYDESCR} | `AmbientTemperatureSensorCelsius[{#SNMPINDEX}]` | `.1.3.6.1.2.1.99.1.1.1.4.{#SNMPINDEX}` |
| {#PHYDESCR} HiShutdown Threshold | `AmbientTemperatureSensorHiShutdown[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.1.99.1.1.1.{#SNMPINDEX}` |
| {#PHYDESCR} HiWarning Threshold | `AmbientTemperatureSensorHiWarning[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.1.99.1.1.2.{#SNMPINDEX}` |
| {#PHYDESCR} LoWarning Threshold | `AmbientTemperatureSensorLoWarning[{#SNMPINDEX}]` | `.1.3.6.1.4.1.6889.2.1.99.1.1.5.{#SNMPINDEX}` |
| {#PHYDESCR} Status | `AmbientTemperatureSensorStatus[{#SNMPINDEX}]` | `.1.3.6.1.2.1.99.1.1.1.5.{#SNMPINDEX}` |
| VoIP Average Occupancy No{#VOIPSLOT} | `VoipAverageOccupancy_[{#VOIPSLOT}]` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.8.{#SNMPINDEX}` |
| VoIP Channels In Use No{#VOIPSLOT} | `VoipChannelsInUse_[{#VOIPSLOT}]` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.7.{#SNMPINDEX}` |
| VoIP Fault Mask No{#VOIPSLOT} | `VoipFaultMask_[{#VOIPSLOT}]` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.14.{#SNMPINDEX}` |
| VoIP Hyper Activity No{#VOIPSLOT} | `VoipHyperactivity_[{#VOIPSLOT}]` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.9.{#SNMPINDEX}` |
| VoIP Total Channels | `VoipTotalChannels_[{#VOIPSLOT}]` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.6.{#SNMPINDEX}` |

## References

- [Avaya G450 Branch Gateway CLI Reference — SNMP access configuration](https://documentation.avaya.com/ja-JP/bundle/AvayaG450BranchGatewayCLIReference_10.2.x/page/SNMPAccessConfiguration.html)
- [Zabbix template guidelines](https://www.zabbix.com/documentation/guidelines/en/template_guidelines)
