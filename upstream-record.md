---
layout: page
title: Upstream Record
permalink: /upstream-record/
---

A record of my public open-source contributions — pull requests, direct
commits, own projects, mailing-list patches, security advisory credits, and
IETF standards work — gathered from GitHub, GitLab, project AUTHORS files,
OpenStack security advisories, and the IETF Datatracker. It spans 2006 to
2026, is sourced from public records only, and is not necessarily exhaustive.

79 entries so far: 61 pull/merge requests and Gerrit changes, 5 direct
commits, 6 IETF RFCs, 4 own projects, and 2 CVEs credited, across 35
third-party projects.

| Date | Project | Contribution | Language |
|------|---------|--------------|----------|
| 2006-01-07 | [Linux kernel](https://github.com/torvalds/linux/commit/50aa88e2877f) | kbuild: ensure mrproper removes .old_version — merged by Sam Ravnborg | Makefile |
| 2006-01-11 | [Linux kernel](https://github.com/torvalds/linux/commit/e56d5ae305b9) | ext3: fix documentation of online resizing (undocument non-working resize= option) — merged by Linus Torvalds | Documentation |
| c. 2007 (Openbox 3.4.0) | [Openbox](https://github.com/danakj/openbox/blob/main/AUTHORS) | Directional focus, edge moving/growing window actions (DirectionalFocus, MoveToEdge, GrowToEdge) — shipped in Openbox 3.4.0 (2007-08-06). Predates the project's move to git; original mailing-list submission not independently linkable. | C |
| 2011-08-28 | [Linux kernel](https://github.com/torvalds/linux/commit/026359bc6edd) | ipv6: send ICMPv6 Router Solicitations only when accept_ra allows accepting Router Advertisements — merged by David S. Miller | C |
| 2014-03-10 | [clatd](https://github.com/toreanderson/clatd) | 464XLAT CLAT implementation for Linux (own project) | Perl |
| 2014-04-15 | [rancid-git](https://github.com/dotwaffle/rancid-git/pull/29) | Merge handling of rancid-fe from upstream branch — merged | Perl |
| 2014-04-22 | [rancid-git](https://github.com/dotwaffle/rancid-git/pull/30) | Add ucsrancid for Cisco UCS devices — merged | Perl |
| 2014-04-30 | [keepalived](https://github.com/acassen/keepalived/pull/82) | Revert "Honor preempt_delay setting on startup" — merged | C |
| 2014-07-09 | [rpki-validator](https://github.com/RIPE-NCC/rpki-validator/pull/4) | rpki-validator.sh: add a run in foreground mode — merged | Shell |
| 2014-07-29 | [keepalived](https://github.com/acassen/keepalived/pull/102) | vrrp: fix preempt and state BACKUP when prio 255 — merged | C |
| 2014-07-31 | [bgpq3](https://github.com/snar/bgpq3/pull/9) | Avoid producing empty Juniper route-filter sets — not merged | C |
| 2014-08-11 | [rpki-validator](https://github.com/RIPE-NCC/rpki-validator/pull/5) | Bugfix: ensure "start" actually daemonises — merged | Shell |
| 2014-08-23 | [ipv6-deprecate-atomfrag](https://github.com/fgont/ipv6-deprecate-atomfrag/pull/1) | "Some updates" to the draft text — merged | IETF Draft |
| 2014-08-24 | [ipv6-deprecate-atomfrag](https://github.com/fgont/ipv6-deprecate-atomfrag/pull/2) | As requested in e-mail discussion — merged | IETF Draft |
| 2014-11-26 | [netconf-perl](https://github.com/Juniper/netconf-perl/pull/20) | Rewrite Net::Netconf::Access::ssh using Net::SSH2 — merged | Perl |
| 2014-12-03 | [ipv6-deprecate-atomfrag](https://github.com/fgont/ipv6-deprecate-atomfrag/pull/3) | Note that protocols built on SIIT are impacted — merged | IETF Draft |
| 2015-01-08 | [Jool](https://github.com/NICMx/Jool/pull/124) | Fix test of EAM 192.0.2.128/26 ↔ 2001:db8:dddd::/64 — merged | C |
| 2015-03-06 | [Jool](https://github.com/NICMx/Jool/pull/129) | Debug log level for drops due to UDP w/ checksum 0 — merged | C |
| 2015-03-08 | [Jool](https://github.com/NICMx/Jool/pull/131) | Use /96 translation prefix instead of /64 — merged | C |
| 2015-04-13 | [NIPAP](https://github.com/SpriteLink/NIPAP/pull/739) | Make init script "status" action LSB compliant — merged | Shell |
| 2015-04-17 | [mobile-broadband-provider-info](https://github.com/mer-packages/mobile-broadband-provider-info/pull/49) | no: New default APN for Telenor Norway — merged | XML |
| 2015-04-22 | [NIPAP](https://github.com/SpriteLink/NIPAP/pull/748) | Fix confirmation message when editing prefixes — merged | JavaScript |
| 2015-08-08 | [Jool](https://github.com/NICMx/Jool/pull/165) | Add support for the DKMS framework — merged | Shell |
| 2015-10-19 | [libvmod-rfc6052](https://github.com/toreanderson/libvmod-rfc6052) | Varnish Cache vmod for RFC6052 IPv4-embedded IPv6 addresses (own project) | C |
| 2015-11-27 | [keepalived](https://github.com/acassen/keepalived/pull/200) | vrrp: set router flag in neighbour advertisements — merged | C |
| 2015-12-07 | [netconf-perl](https://github.com/Juniper/netconf-perl/pull/25) | Correct usage of Net::SSH2->error() — open | Perl |
| 2016-02 | [RFC 7755](https://datatracker.ietf.org/doc/rfc7755/) | SIIT-DC: Stateless IP/ICMP Translation for IPv6 Data Centers | IETF RFC |
| 2016-02 | [RFC 7756](https://datatracker.ietf.org/doc/rfc7756/) | SIIT-DC: Dual Translation Mode | IETF RFC |
| 2016-02 | [RFC 7757](https://datatracker.ietf.org/doc/rfc7757/) | Explicit Address Mappings for Stateless IP/ICMP Translation | IETF RFC |
| 2016-04-10 | [openwrt routing](https://github.com/openwrt/routing/pull/168) | hnetd: support the ip4mode parameter — merged | C |
| 2016-04-10 | [openwrt routing](https://github.com/openwrt/routing/pull/169) | hnetd: ensure ULA prefix persists across reboots — not merged | C |
| 2016-06 | [RFC 7915](https://datatracker.ietf.org/doc/rfc7915/) | IP/ICMP Translation Algorithm | IETF RFC |
| 2017-01 | [RFC 8021](https://datatracker.ietf.org/doc/rfc8021/) | Generation of IPv6 Atomic Fragments Considered Harmful | IETF RFC |
| 2017-03-17 | [bgp-session-culling](https://github.com/bgp/draft-grow-bgp-session-culling/pull/14) | Rewrite Section 1 (Introduction) — merged | IETF Draft |
| 2017-07-01 | [Linux kernel](https://github.com/torvalds/linux/commit/a68491f895a9) | net: cdc_mbim: apply "NDP to end" quirk to HP lt4132 modem — merged by David S. Miller | C |
| 2017-08 | [RFC 8215](https://datatracker.ietf.org/doc/rfc8215/) | Local-Use IPv4/IPv6 Translation Prefix | IETF RFC |
| 2017-10-22 | [lede-project source](https://github.com/lede-project/source/pull/1445) | Make network.*.ip6assign default to 64 — not merged | OpenWrt UCI |
| 2018-10-04 | [FRRouting](https://github.com/FRRouting/frr/pull/3132) | doc: correct route map match for prefix lists — merged | Documentation |
| 2018-11-17 | [NLNOG ring-ansible](https://github.com/NLNOG/ring-ansible/pull/37) | Install Paris Traceroute — merged | YAML/Ansible |
| 2018-11-18 | [ipxe](https://github.com/ipxe/ipxe/pull/84) | [efi] Enable NET_PROTO_IPV6 by default — not merged | C |
| 2018-12-08 | [Linux kernel](https://github.com/torvalds/linux/commit/d57ec3c83b51) | USB: serial: option: add HP lt4132 modem — merged by Johan Hovold | C |
| 2018-12-17 | [systemd](https://github.com/systemd/systemd/pull/11181) | resolve: enable EDNS0 towards the 127.0.0.53 stub resolver — merged | C |
| 2019-05-04 | [NLNOG ring-ansible](https://github.com/NLNOG/ring-ansible/pull/53) | etcfiles/hosts.j2: fix ordering of fqdn/alias in /etc/hosts — merged | YAML/Ansible |
| 2019-05-04 | [NLNOG ring-ansible](https://github.com/NLNOG/ring-ansible/pull/54) | ring-ping: only use nodes with working dual-stack connectivity — merged | YAML/Ansible |
| 2019-05-16 | [openshift-netbox](https://github.com/leoluk/openshift-netbox/pull/3) | netbox-base.yml: use GIT_REMOTE instead of GIT_REPO — not merged | YAML |
| 2019-05-24 | [openshift-netbox](https://github.com/leoluk/openshift-netbox/pull/10) | Use a static database username ("netbox") by default — merged | OpenShift Template |
| 2019-05-24 | [openshift-netbox](https://github.com/leoluk/openshift-netbox/pull/11) | Add database backup/restore instructions to README — merged | Markdown |
| 2019-05-24 | [openshift-netbox](https://github.com/leoluk/openshift-netbox/pull/12) | Add media backup/restore instructions to README — merged | Markdown |
| 2019-07-18 | [openshift-netbox](https://github.com/leoluk/openshift-netbox/pull/13) | WIP: update to NetBox v2.5.13 — not merged | OpenShift Template |
| 2019-07-30 | [nextcloud-snap](https://github.com/nextcloud-snap/nextcloud-snap/pull/1078) | mysql: only write root password to root.ini if successfully set — merged | Shell |
| 2019-07-31 | [nextcloud-snap](https://github.com/nextcloud-snap/nextcloud-snap/pull/1080) | mysql: explicitly configure error log file name — merged | Shell |
| 2019-08-17 | [dnssec-trigger](https://github.com/NLnetLabs/dnssec-trigger/pull/2) | Enable EDNS0 in generated /etc/resolv.conf — not merged | C |
| 2020-04-13 | [3gpptest](https://github.com/toreanderson/3gpptest) | IPv6 testing tool for mobile networks while roaming (own project) | Python |
| 2020-06-07 | [liquidprompt](https://github.com/liquidprompt/liquidprompt/pull/604) | load: do not infer CPU utilisation from load average — not merged | Shell |
| 2020-07-22 | [FRRouting](https://github.com/FRRouting/frr/pull/6787) | tools: do not silently ignore config load errors at startup — merged | C |
| 2020-09-26 | [pop-os shell](https://github.com/pop-os/shell/pull/569) | rebuild.sh: offer to install TypeScript via npm if missing — merged | Shell |
| 2020-10-02 | [pop-os shell](https://github.com/pop-os/shell/pull/589) | rebuild.sh: offer to install TypeScript via npm if needed — not merged | Shell |
| 2021-02-12 | [podman (southalc)](https://github.com/southalc/podman/pull/10) | Remove creation of pointless temp file — merged | Puppet |
| 2022-06-02 | [sonic-buildimage](https://github.com/sonic-net/sonic-buildimage/pull/11009) | [Accton] fix inconsistent tabs/spaces in fanutil.py — open | Python |
| 2022-11-16 | [firezone](https://github.com/firezone/firezone/pull/1114) | Allow pgcrypto extension to preexist — merged | Elixir |
| 2022-11-17 | [firezone](https://github.com/firezone/firezone/pull/1122) | Allow btree_gist extension to preexist — merged | Elixir |
| 2023-03-06 | [sonic-builds](https://github.com/bluecmd/sonic-builds/pull/3) | Only deploy builds.json if it is non-empty — merged | Shell |
| 2023-03-09 | [sonic-buildimage](https://github.com/sonic-net/sonic-buildimage/pull/14183) | frrcfgd: fix incorrect rendering of route-map references — open | Python |
| 2024-12-03 | [OpenStack Neutron](https://security.openstack.org/ossa/OSSA-2024-005.html) | OSSA-2024-005 / CVE-2024-53916: authorization bypass let unprivileged tenants add or clear tags on Neutron networks they don't own — discovered and credited as sole reporter (CVE discovery) | Python |
| 2024-12-06 | [evpn_agent](https://github.com/toreanderson/evpn_agent) | OpenStack EVPN Agent (own project) | Python |
| 2026-05-13 | [rclone](https://github.com/rclone/rclone/pull/9436) | jottacloud: support whitelabel service Phonero Sky — merged | Go |
| 2026-07-10 | [OpenStack Designate](https://review.opendev.org/c/openstack/designate/+/996747) | Fix catalog zone AXFR lookup for non-default pools — open | Python |
| 2026-07-10 | [OpenStack Designate](https://review.opendev.org/c/openstack/designate/+/996761) | Fix catalog zone TSIG key name to not include a trailing dot — open | Python |
| 2026-07-21 | [OpenStack Puppet-Designate](https://review.opendev.org/c/openstack/puppet-designate/+/998124) | Add remaining default SOA parameters — open | Puppet |
| 2026-07-25 | [nwipe](https://github.com/martijnvanbrummelen/nwipe/pull/780) | create_pdf: use smartctl -x instead of -a — merged | C |
| 2026-07-27 | [Knot DNS](https://gitlab.nic.cz/knot/knot-dns/-/merge_requests/1898/commits) | remote: bind outgoing connections to a device via 'via' address, plus keying the TCP connection pool and unreachable-remote cache on the outgoing device too, with tests — reported as issue #977, authored on a fork; merged by maintainer Daniel Salzman in MR !1898 (2026-08-04), shipped in Knot DNS 3.5.7 | C |
| 2026-08-11 | [OpenStack Designate](https://security.openstack.org/ossa/OSSA-2026-034.html) | OSSA-2026-034 / CVE-2026-71193, CVE-2026-71194: cross-tenant DNS zone overlap and mDNS denial-of-service via pool scheduling — co-discovered with Omer Schwartz (Red Hat) (CVE discovery) | Python |
| 2026-09-08 | [OpenStack Designate](https://review.opendev.org/c/openstack/designate/+/996760) | Fix pool nameserver update when pool has a catalog zone — merged | Python |
| 2026-09-08 | [OpenStack Designate](https://review.opendev.org/c/openstack/designate/+/997376) | Fix pool_move_zone not bumping catalog zone serials — merged | Python |
| 2026-09-08 | [OpenStack Designate](https://review.opendev.org/c/openstack/designate/+/998103) | Use lowest priority ns_record as SOA MNAME — merged | Python |
| 2026-09-11 | [Knot DNS](https://gitlab.nic.cz/knot/knot-dns/-/merge_requests/1911) | policy: opportunistically reuse trashed DNSSEC keys for recreated zones — open | C |
| 2026-09-12 | [betterleaks](https://github.com/betterleaks/betterleaks/pull/351) | report: never print a raw secret when its exact location can't be verified — open | Go |
| 2026-09-21 | [OpenStack Designate](https://review.opendev.org/c/openstack/designate/+/996748) | Fix pool delete when it contains a catalog zone — merged | Python |
| 2026-09-21 | [OSISM openstack-image-manager](https://github.com/osism/openstack-image-manager/pull/1279) | Stop hiding the newest version of an image — merged | Python |
