# NetStatus Module

A RISC OS module that provides visual feedback during Econet network activity by controlling the hourglass display.

## Overview

The NetStatus module monitors Econet network activity by intercepting the EconetV software vector. It displays an hourglass with LED indicators to show the current network status:

| LED State | Meaning |
|-----------|---------|
| Top LED only | Station is trying to receive |
| Bottom LED only | Station is trying to transmit |
| Both LEDs on | Station is waiting for a broadcast reply |
| Both LEDs off | Transmission/reception complete |

The module also displays percentage figures showing transfer progress when meaningful.

## Building

Build the module using:

```bash
riscos-amu
```

This produces a 32-bit module in `rm32/NetStatus`.

To build a 64-bit version:

```bash
riscos-amu BUILD64=1
```

## Installation

Load the module using:

```basic
RMLOAD NetStatus
```

The module will automatically start monitoring Econet activity.

## Testing

A test program `TestProgram,fd1` is provided which exercises all supported EconetV reason codes:

```bash
riscos-build-run rm32/NetStatus,ffa TestProgram,fd1 --command "rmload NetStatus" --command "TestProgram"
```

The test program calls each supported reason code and you should observe the hourglass changing state as expected.

## Supported EconetV Reason Codes

The module handles the following EconetV reason codes:

### File Operations (Load/Save/Create/GetBytes/PutBytes)

| Reason Code | Name | Description | Hourglass Action |
|-------------|------|-------------|------------------|
| &10/&20/&30/&40/&50 | NetFS_Start* | Start of operation | Hourglass_On |
| &11/&21/&31/&41/&51 | NetFS_Part* | Operation in progress | Hourglass_Percentage |
| &12/&22/&32/&42/&52 | NetFS_Finish* | Operation complete | Hourglass_Off |

### Wait Operations

| Reason Code | Name | Description | Hourglass Action |
|-------------|------|-------------|------------------|
| &60 | NetFS_StartWait | Start of Broadcast_Wait | Hourglass_LEDs (both on) |
| &62 | NetFS_FinishWait | End of Broadcast_Wait | Hourglass_LEDs (both off) |

### Broadcast Operations

| Reason Code | Name | Description | Hourglass Action |
|-------------|------|-------------|------------------|
| &70 | NetFS_StartBroadcastLoad | Start broadcast load | Hourglass_On (green/blue) |
| &71 | NetFS_PartBroadcastLoad | Broadcast load progress | Hourglass_Percentage |
| &72 | NetFS_FinishBroadcastLoad | Broadcast load complete | Hourglass_Off |
| &80 | NetFS_StartBroadcastSave | Start broadcast save | Hourglass_On (red/blue) |
| &81 | NetFS_PartBroadcastSave | Broadcast save progress | Hourglass_Percentage |
| &82 | NetFS_FinishBroadcastSave | Broadcast save complete | Hourglass_Off |

### Econet Transmission/Reception

| Reason Code | Name | Description | Hourglass Action |
|-------------|------|-------------|------------------|
| &C0 | Econet_StartTransmission | Start waiting for transmission | Hourglass_LEDs (bottom only) |
| &C2 | Econet_FinishTransmission | Transmission complete | Hourglass_LEDs (both off) |
| &D0 | Econet_StartReception | Start waiting for reception | Hourglass_LEDs (top only) |
| &D2 | Econet_FinishReception | Reception complete | Hourglass_LEDs (both off) |

## Broadcast Colours

For broadcast operations handled by the Broadcast Loader:

- **Broadcast Load**: Green/blue colours
- **Broadcast Save**: Red/blue colours

## Architecture

The module is implemented as a passive monitoring module that:

1. Claims the EconetV software vector (&21)
2. Examines the reason code for each call
3. Makes appropriate calls to the Hourglass module
4. Passes the vector call on to other handlers

## Files

| File | Description |
|------|-------------|
| `c/module` | Main module implementation |
| `cmhg/modhead` | Module definition and vector handler configuration |
| `Makefile,fe1` | Build configuration |
| `VersionNum` | Version number definitions |
| `TestProgram,fd1` | BASIC test program |

## Version History

- **0.01** (23 Mar 2026) - Initial release

## License

MIT License

## References

- [RISC OS PRM - NetStatus](http://www.riscos.com/support/developers/prm/netstatus.html)
- [RISC OS PRM - EconetV Vector](http://www.riscos.com/support/developers/prm/softvecs.html#94076)
- [RISC OS PRM - Hourglass Module](http://www.riscos.com/support/developers/prm/hourglass.html)
