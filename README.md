# DOCSIS Channel Parser

A utility to read channel diagnostics information from DOCSIS modems and output
it in the InfluxDB line protocol.

This package is intended to be used within a Telegraf instance.

This package has been written in Go in an attempt to allow it to run on low end
hardware (such as a Raspberry Pi Zero) with no issues.


## Usage

In its simplest form, you can run this repository directly.
The only dependencies being Go.

### Configuration

The scripts need to know the modem type (`ROUTER_TYPE`).
Additional information depends on the model.

**Virgin Superhub 3:**
 * `ROUTER_TYPE=superhub3`
 * `IP_ADDRESS` (defaults to `192.168.100.1`)

**Virgin Superhub 4:**
 * `ROUTER_TYPE=superhub4`
 * `IP_ADDRESS` (defaults to `192.168.100.1`)

**Com Hem WiFi Hub C2:**
(This is likely to work on any Sagemcom DOCSIS modem)
 * `ROUTER_TYPE=comhemc2`
 * `IP_ADDRESS` (defaults to `192.168.10.1`)
 * `ROUTER_USER` (defaults to `admin`)
 * `ROUTER_PASS` (defaults to `admin`)

### Example

```
$ ROUTER_TYPE=superhub3 IP_ADDRESS=192.168.100.1 go run sh-stats/*.go
downstream,channel=3,id=10,modulation=QAM256,scheme=SC-QAM frequency=211000000,snr=403,power=71,prerserr=300,postrserr=0
downstream,channel=9,id=16,modulation=QAM256,scheme=SC-QAM frequency=259000000,snr=409,power=68,prerserr=72,postrserr=0
...
```

### Within Telegraf

To run within Telegraf, you should build a binary for your architecture, then
mount that executable to the container.

```
go build -o docsis-stats sh-stats/*.go
```

`docsis-stats` should be mounted to the container.

The Telegraf configuration should then use the `exec` input to call it.

```
[[inputs.exec]]
  commands = ["bash /docsis-stats"]
  data_format = "influx"
```
