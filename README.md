# [PROTOTYPE] docker-opencyphal-debug-tools

A docker container to use Yakut from OpenCyphal.org and github.com/opencyphal and Tshark from Wireshark.

## Building

I use finch locally to build on my Mac. 

```bash
finch build cyphal-tools cyphal-tools
```

On Linux you can use docker

```bash
docker buildx build cyphal-tools -t cyphal-tools
```

## Running

```bash
finch run -it --network host --cap-add=NET_RAW --cap-add=NET_ADMIN cyphal-tools
```

or

```bash
docker run -it --net host cyphal-tools
```

You can use both the `tshark` and `yakut` tools in the container to observer multicast Cyphal/UDP
traffic.

### Terminal Shark

```bash
$ tshark -i eth0 -f "udp port 9382"
...
Capturing on 'eth0'
 ** (tshark:99) 02:02:02.350754 [Main MESSAGE] -- Capture started.
 ** (tshark:99) 02:02:02.350845 [Main MESSAGE] -- File: "/tmp/wireshark_eth0PHSCO2.pcapng"
    1 0.000000000 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
    2 0.981301818 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
    3 1.995579300 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
    4 2.982021528 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
    5 3.992935682 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
    6 4.981271074 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
    7 5.993050351 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
    8 6.898101521 192.168.5.15 ? 239.0.29.86  CYPHALUDP 221 38117 ? 9382 Len=179
    9 6.978108581 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
   10 7.996077225 192.168.5.15 ? 239.0.29.85  CYPHALUDP 77 33667 ? 9382 Len=35
...
```

### Yakut
See Yakut's readme for more information: [here](https://github.com/opencyphal/yakut)

Be sure to set `UAVCAN__NODE__ID` and `UAVCAN__UDP__IFACE` as appropriate before running `yakut`.

```bash
# These are incidental to my machine! Don't copy!
export UAVCAN__NODE__ID=44
export UAVCAN__UDP__IFACE=192.168.5.15
yakut monitor
```

Which results in:

```
NodID Mode Health VSSC Uptime         VProtcl VHardwr VSoftware(major.minor.vcs.crc)            Unique-ID
   44 oper nomina   13     0d00:07:27   1.0     0.0     0.13                                    8099d400db13bb3
Legend: pub/cln│sub/srv│(pub+sub)/(cln+srv)│activity│uavcan.node.port.List is published/not│
MESSG   44 ∑t/s ∑B/s
 7509   1    1    7  7509
 7510   0    0   15  7510
∑t/s    1    1      ↖ t/s
∑B/s   22        22
RQ+RS   44 ∑t/s ∑B/s
  384   0    0    0   384
  385   0    0    0   385
  430   0    0    0   430
∑t/s    0    0      ↖ t/s
∑B/s    0         0
TOTAL   44 ∑t/s ∑B/s
∑t/s    1    1
∑B/s   22        22
Transport errors:        0         Average over 10.0 sec
```
