# EtherChannel 

### S1

```
Flags:  D - down        P - in port-channel
		I - stand-alone s - suspended
		H - Hot-standby (LACP only)
		R - Layer3      S - Layer2
		U - in use      f - failed to allocate aggregator
		u - unsuitable for bundling
		w - waiting to be aggregated
		d - default port


Number of channel-groups in use: 2
Number of aggregators:           2

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           PAgP   Fa0/3(P) Fa0/4(P) 
2      Po2(SU)           LACP   Fa0/1(P) Fa0/2(P) 
```

### S2

```
Flags:  D - down        P - in port-channel
		I - stand-alone s - suspended
		H - Hot-standby (LACP only)
		R - Layer3      S - Layer2
		U - in use      f - failed to allocate aggregator
		u - unsuitable for bundling
		w - waiting to be aggregated
		d - default port


Number of channel-groups in use: 2
Number of aggregators:           2

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

2      Po2(SU)           LACP   Fa0/1(P) Fa0/2(P) 
3      Po3(SU)           LACP   Fa0/3(P) Fa0/4(P) 

```

### S3

```
Flags:  D - down        P - in port-channel
		I - stand-alone s - suspended
		H - Hot-standby (LACP only)
		R - Layer3      S - Layer2
		U - in use      f - failed to allocate aggregator
		u - unsuitable for bundling
		w - waiting to be aggregated
		d - default port


Number of channel-groups in use: 2
Number of aggregators:           2

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           PAgP   Fa0/3(P) Fa0/4(P) 
3      Po3(SU)           LACP   Fa0/1(P) Fa0/2(P) 
```

