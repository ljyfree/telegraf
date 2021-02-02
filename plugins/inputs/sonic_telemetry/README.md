# SONiC GNMI Telemetry

SONiC GNMI Telemetry is an input plugin that consumes telemetry data based on the [GNMI](https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md) Subscribe method. TLS is supported for authentication and encryption.

It has been optimized to support GNMI telemetry as produced by SONiC.


### Configuration
SONiC---Telegraf---InfluxDB
```toml
[[inputs.sonic_telemetry_gnmi]]
  ## Address and port of the GNMI GRPC server，which should be a SONiC Switch with Telemetry enabled.
  addresses = ["10.10.39.39:8080"]

  ## define credentials
  username = "admin"
  password = "YourPaSsWoRd"

  ## GNMI encoding requested (one of: "proto", "json", "json_ietf")
  encoding = "json_ietf"

  ## redial in case of failures after
  redial = "10s"

  ## enable client-side TLS and define CA to authenticate the device
  enable_tls = true
  # tls_ca = "/etc/telegraf/ca.pem"
  insecure_skip_verify = true

  ## define client-side TLS certificate & key to authenticate to the device
  # tls_cert = "/etc/telegraf/cert.pem"
  # tls_key = "/etc/telegraf/key.pem"

  ## GNMI subscription prefix (optional, can usually be left empty)
  ## See: https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md#222-paths
  # origin = ""
  # prefix = ""
  target = "COUNTERS_DB"

  ## Define additional aliases to map telemetry encoding paths to simple measurement names
  # [inputs.sonic_telemetry_gnmi.aliases]
  #   ifcounters = "openconfig:/interfaces/interface/state/counters"

  [[inputs.sonic_telemetry_gnmi.subscription]]
    ## Name of the measurement that will be emitted
    name = "ifcounters"

    ## Origin and path of the subscription
    ## See: https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md#222-paths
    ##
    ## origin usually refers to a (YANG) data model implemented by the device
    ## and path to a specific substructe inside it that should be subscribed to (similar to an XPath)
    ## YANG models can be found e.g. here: https://sonic_mgmt_ip_address/ui
    origin = ""
    path = "/COUNTERS/Ethernet0"

    # Subscription mode (one of: "target_defined", "sample", "on_change") and interval
    subscription_mode = "sample"
    sample_interval = "5s"

    ## Suppress redundant transmissions when measured values are unchanged
    # suppress_redundant = false

    ## If suppression is enabled, send updates at least every X seconds anyway
    # heartbeat_interval = "60s"
 [[outputs.influxdb]]
    urls = ["http://172.17.0.5:8086"]
    database = "sonic_telemetry"
    timeout = "5s"
    username = "root"
    password = "root"
```
### Validation
You should be able to find measurement "ifcounters" in database "sonic_telemetry",which could be treated as Grafana's data source.

# SONiC FRR Input Plugin for BGP Neighbors
This Input plugin give the following measurements per configured VRF and Address Family
### Measurements & Fields:

- BGPNeighbors 
	"ipv4PrefixRecv"
	"ipv4PrefixSent"
	"localAs"
	"localRouterID"
	"remoteAs"
	"remoteRouterID"
	"state"	
	"totalMsgsRecv"
	"totalMsgsSent"
	"uptime"


- BGPNbrCount 
	"totalNbrs"
	"totalNbrsUp"

### Configuration

```toml
## Define the VRF and Address Families for the BGP Neighbors from FRR on SONiC OS
[[inputs.sonic_frr]]
  #[[inputs.sonic_frr.vrf]]
  #  name = "default"
  ## Address Family one of : "ipv4", "ipv6"
  #  address_family = ["ipv4", "ipv6"]
  #[[inputs.sonic_frr.vrf]]
  #  name = "Vrf_blue"
  #  address_family = ["ipv4", "ipv6"]
```
