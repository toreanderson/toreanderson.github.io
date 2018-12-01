---
published: true
title: "Enabling IPv6 on the Huawei ME906s-158 / HP lt4132"
layout: post
---

Last year I wrote a [post](/2017/07/31/huawei-me906s-hp-lt4132-linux-ipv6.html)
about my difficulties getting IPv6 to work with the [Huawei ME906s-158 WWAN
module](http://consumer.huawei.com/solutions/m2m-solutions/en/products/tech-specs/me906s-158-en.htm).
I eventually gave up and had it replaced with a module from another
manufacturer.

Not long ago, though, I received an e-mail from another ME906s-158 owner who
told me that for him, IPv6 worked just fine. That motivated me to brush the
dust off my module and try again. This time, I figured it out! Read on for the
details.

## The Carrier PLMN List

The ME906s-158 comes with a built-in list of nine different
[PLMN](https://en.wikipedia.org/wiki/Public_land_mobile_network) profiles. This
list can be managed with proprietary AT command `AT^PLMNLIST`, which is
documented on page 209 of the module's [*AT Command Interface
Specification*](http://download-c.huawei.com/download/downloadCenter?downloadId=51047&version=120450&siteCode=).

To interact with the AT command interface, use the `option` driver. More
details on that
[here](/2017/07/31/huawei-me906s-hp-lt4132-linux-ipv6.html#lack-of-ipv6-support).

This is the complete factory default list:

```
AT^PLMNLIST?

^PLMNLIST: "00000",00000,23106,26207,23802,23806
^PLMNLIST: "20205",26801,20205,26202,26209,27201,27402,50503,54201,53001,40401,40405,40411,40413,40415,40420,40427,40430,40443,40446,40460,40484,40486,40488,40566,40567,405750,405751,405752,405753,405754,405755,405756,20404,20601,20810,21401,21670,22210,22601,23003,23415,24405,24802,27602,27801,28001,28602,28802,29340,42702,60202,62002,63001,63902,64004,64304,65101,65501,90128,23201,28401,64710,46601,42602,22005,41302,29403,50213,50219,21910,25001,27077,52505,23801,40004,42403,46692,52503,73001,24602,24705
^PLMNLIST: "26201",26201,23001,20416,23203,23207,21901,21630,23102,29702,29401,26002,20201,23431,23432
^PLMNLIST: "21403",20610,20801,20802,21403,22610,23101,23430,23433,26803,26003
^PLMNLIST: "50501",50501,50571,50572
^PLMNLIST: "22801",22801,29501
^PLMNLIST: "21407",21405,21407,23402
^PLMNLIST: "99999",24491,24001,23820
^PLMNLIST: "50502",50502 

OK
```

Each `^PLMNLIST:` line represents a single pre-defined PLMN profile, identified
by the first MCCMNC number (in double quotes). The `"26201"` profile is for
Deutsche Telekom, the `"50501"` profile is for Telstra Mobile, and so on.

The rest of the numbers on each line is a list of MCCMNCs that will use that
particular profile. For example, if you have a SIM card issued by T-Mobile
Netherlands (MCCMNC 20416), then the ME906s-158 will apply the Deutsche Telekom
profile (`"26201"`).

Unfortunately, the documentation offers no information on how the different
PLMN profiles differ and how they change the way the module work.

My provider (Telenor Norway, MCCMNC 24201) is not present in the factory
default list. In that case, the module appears to use the `"00000"` PLMN
profile (*«Generic»*) as the default, and that one disables IPv6! Clearly,
Huawei haven't read [RFC 6540](https://tools.ietf.org/html/rfc6540)...

In any case, this explains why I failed to make IPv6 work last year, and why it
worked fine for the guy who mailed me - his provider was among those that used
the Deutsche Telekom PLMN profile by default.

## Modifying the Carrier PLMN List

The solution seems clear: I need to add my provider's MCCMNC to an IPv6-capable
PLMN profile. The Deutsche Telekom one would probably work, but `"99999"`
(*«Generic(IPV4V6)»*) seems like an even more appropriate choice.

Adding MCCMNCs is done with `AT^PLMNLIST=1,"$PLMNProfile","$MCC"`, like so:

```
AT^PLMNLIST=1,"99999","24201"

OK
```

(To remove an MCCMNC, use `AT^PLMNLIST=0,"$PLMNProfile","$MCCMNC"` instead.)

I can now double check that the `"99999"` PLMN profile includes `24201` for
Telenor Norway:

```
AT^PLMNLIST="99999"

^PLMNLIST: "99999",24491,24001,23820,24201

OK
```

To make the new configuration take effect, it appears to be necessary to reset
the module. This can be done with the `AT^RESET` command.

## Confirming that IPv6 works

It is possible to query the module directly about IPv6 support in at least two
different ways:

```
AT^IPV6CAP?

^IPV6CAP: 7

OK

AT+CGDCONT=?

+CGDCONT: (0-11),"IP",,,(0-2),(0-3),(0,1),(0,1),(0-2),(0,1)
+CGDCONT: (0-11),"IPV6",,,(0-2),(0-3),(0,1),(0,1),(0-2),(0,1)
+CGDCONT: (0-11),"IPV4V6",,,(0-2),(0-3),(0,1),(0,1),(0-2),(0,1)

OK
```

The `^IPV6CAP: 7` response means *«IPv4 only, IPv6 only and IPv4v6»* (cf. page
336 of the [*AT Command Interface
Specification*](http://download-c.huawei.com/download/downloadCenter?downloadId=51047&version=120450&siteCode=)),
and the `+CDGCONT:` responses reveal that the modem is ready to configure PDP
contexts using the IPv6-capable IP types. Looking good!

Of course, the only test that really matters is to connect it:

```
$ mmcli --modem 0 --simple-connect=apn=telenor.smart,ip-type=ipv4v6
successfully connected the modem
$ mmcli --bearer 0                                                                                            
Bearer '/org/freedesktop/ModemManager1/Bearer/0'
  -------------------------
  Status             |   connected: 'yes'
                     |   suspended: 'no'
                     |   interface: 'wwp0s20f0u3c3'
                     |  IP timeout: '20'
  -------------------------
  Properties         |         apn: 'telenor.smart'
                     |     roaming: 'allowed'
                     |     IP type: 'ipv4v6'
                     |        user: 'none'
                     |    password: 'none'
                     |      number: 'none'
                     | Rm protocol: 'unknown'
  -------------------------
  IPv4 configuration |   method: 'static'
                     |  address: '10.169.198.77'
                     |   prefix: '30'
                     |  gateway: '10.169.198.78'
                     |      DNS: '193.213.112.4', '130.67.15.198'
                     |      MTU: '1500'
  -------------------------
  IPv6 configuration |   method: 'static'
                     |  address: '2a02:2121:2c4:7105:5a2c:80ff:fe13:9208'
                     |   prefix: '64'
                     |  gateway: '::'
                     |      DNS: '2001:4600:4:fff::52', '2001:4600:4:1fff::52'
                     |      MTU: '1500'
  -------------------------
  Stats              |          Duration: '59'
                     |    Bytes received: 'N/A'
                     | Bytes transmitted: 'N/A'
```

Success!
