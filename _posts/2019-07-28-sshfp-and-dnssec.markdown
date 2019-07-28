---
layout: post
title: Validating SSH host keys with DNSSEC
description: Validating SSH host keys with DNSSEC
---

*(Note: this is a [repost of an article](https://www.redpill-linpro.com/techblog/2019/05/06/sshfp-and-dnssec.html) from the [Redpill Linpro techblog](https://www.redpill-linpro.com/techblog/).)*

We have all done it. When SSH asks us this familiar question:

```console
$ ssh redpilllinpro01.ring.nlnog.net
The authenticity of host 'redpilllinpro01.ring.nlnog.net (2a02:c0:200:104::1)' can't be established.
ECDSA key fingerprint is SHA256:IM/o2Qakw4q7vo9dBMLKuKAMioA7UeJSoVhfc5CYsCs.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

…we just answer `yes` - without bothering to verify the fingerprint shown.

Many of us will even automate answering `yes` to this question by adding `StrictHostKeyChecking accept-new` to our `~/.ssh/config` file.

Sometimes, SSH will be more ominous:

```console
$ ssh redpilllinpro01.ring.nlnog.net
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:IM/o2Qakw4q7vo9dBMLKuKAMioA7UeJSoVhfc5CYsCs.
Please contact your system administrator.
Add correct host key in /home/tore/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/tore/.ssh/known_hosts:448
ECDSA host key for redpilllinpro01.ring.nlnog.net has changed and you have requested strict checking.
Host key verification failed.
```

This might make us stop a bit and ask ourselves: *«Has a colleague re-provisioned this node since the last time I logged in to it?»*

Most of the time, the answer will be: *«Yeah, probably»*, followed by something like `sed -i 448d ~/.ssh/known_hosts` to get rid of the old offending key. Problem solved!

These are all very understandable and human ways of dealing with these kinds of repeated questions and warnings. SSH certainly does *«cry wolf»* a lot! Let us not think too much about what happens that one time someone actually *is «DOING SOMETHING NASTY»*, though…

Another challenge occurs when maintaining a large number of servers using automation software like [Ansible](https://www.ansible.com). Manually answering questions about host keys might be impossible, as the automation software likely needs to run entirely without human interaction. The *cop out* way of ensuring it can do so is to disable host key checking altogether, e.g., by adding `StrictHostKeyChecking no` to the `~/.ssh/config` file.

## DNSSEC-validated SSH host key fingerprints in DNS

Fortunately a better way of securely verifying SSH host keys exists - one which does not require lazy and error-prone humans to do all the work.

This is accomplished by combining [DNS Security Extensions](https://en.wikipedia.org/wiki/Domain_Name_System_Security_Extensions) (DNSSEC) with [`SSHFP` resource records](https://tools.ietf.org/html/rfc4255).

To make use of this approach, you will need the following:

1. The SSH host keys published in DNS using `SSHFP` resource records
2. Valid DNSSEC signatures on the `SSHFP` resource records
3. A DNS recursive resolver which supports DNSSEC
4. A stub resolver that is configured to request DNSSEC validation
5. A SSH client that is configured to look for SSH host keys in DNS

I will elaborate on how to implement each of these requirements in the sections below.

### 1. Publishing SSHFP host keys in DNS

The `ssh-keygen` utility provides an easy way to generate the correct `SSHFP` resource records based on contents of the `/etc/ssh/ssh_host_*_key.pub` files. Run it on the server like so:

```console
$ ssh-keygen -r $(hostname --fqdn).
redpilllinpro01.ring.nlnog.net. IN SSHFP 1 1 5fca087a7c3ebebbc89b229a05afd450d08cf9b3
redpilllinpro01.ring.nlnog.net. IN SSHFP 1 2 cdb4cdaf7734df343fd567e0cab92fd6ac5f2754bfef797826dfd4bcf90f0baf
redpilllinpro01.ring.nlnog.net. IN SSHFP 2 1 613f389a36cf33b67d9bd69e381785b275e101cd
redpilllinpro01.ring.nlnog.net. IN SSHFP 2 2 8a07b97b96d826a7d4d403424b97a8ccdb77105b527be7d7be835d02fdb9cd58
redpilllinpro01.ring.nlnog.net. IN SSHFP 3 1 3e46cecd986042e50626575231a4a155cb0ee5ca
redpilllinpro01.ring.nlnog.net. IN SSHFP 3 2 20cfe8d906a4c38abbbe8f5d04c2cab8a00c8a803b51e252a1585f739098b02b
```

These entries can be copied and pasted directly into the zone file in question so that they are visible in DNS:

```console
$ dig +short redpilllinpro01.ring.nlnog.net. IN SSHFP | sort
1 1 5FCA087A7C3EBEBBC89B229A05AFD450D08CF9B3
1 2 CDB4CDAF7734DF343FD567E0CAB92FD6AC5F2754BFEF797826DFD4BC F90F0BAF
2 1 613F389A36CF33B67D9BD69E381785B275E101CD
2 2 8A07B97B96D826A7D4D403424B97A8CCDB77105B527BE7D7BE835D02 FDB9CD58
3 1 3E46CECD986042E50626575231A4A155CB0EE5CA
3 2 20CFE8D906A4C38ABBBE8F5D04C2CAB8A00C8A803B51E252A1585F73 9098B02B
```

How to automatically update the `SSHFP` records in DNS when a node is being provisioned is left as an exercise for the reader, but one nifty little trick is to run something like `ssh-keygen -r "update add $(hostname --fqdn). 3600"`. This produces output that can be piped directly into `nsupdate(1)`.

If you for some reason can not run `ssh-keygen` on the server, you can also use a tool called `sshfp`. This tool will take the entries from `~/.ssh/known_hosts` (i.e., those you have manually accepted earlier) and convert them to `SSHFP` syntax.

### 2. Ensuring the DNS records are signed with DNSSEC

DNSSEC signing of the data in a DNS zone is a task that is usually performed by the DNS hosting provider, so normally you would not need to do this yourself.

There are several web sites that will verify that DNSSEC signatures exist and validate for any given host name. The two best known are:

* [DNSViz](http://dnsviz.net/)
* [Verisign Labs's DNSSEC Debugger](https://dnssec-debugger.verisignlabs.com/)

If DNSViz shows that everything is *«secure»* in the left column ([example](http://dnsviz.net/d/redpilllinpro01.ring.nlnog.net/dnssec/)) and the DNSSEC Debugger only shows green ticks ([example](https://dnssec-debugger.verisignlabs.com/redpilllinpro01.ring.nlnog.net)), your DNS records are correctly signed and the SSH client should consider them secure for the purposes of `SSHFP` validation.


If DNSViz and the DNSSEC Debugger give you a different result, you will most likely have to contact your DNS hosting provider and ask them to sign your zones with DNSSEC.

### 3. A recursive resolver that supports DNSSEC

The recursive resolver used by your system must be capable of validating DNSSEC signatures. This can be verified like so:

```console
$ dig redpilllinpro01.ring.nlnog.net. IN SSHFP +dnssec
[...]
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 7, AUTHORITY: 0, ADDITIONAL: 1
[...]
```

Look for the `ad` flag (*«Authenticated Data»*) in the answer, If present, it means that the DNS server confirms that the supplied answer has a valid DNSSEC signature and is secure.

If the `ad` flag is missing when querying a hostname known to have valid DNSSEC signatures (e.g., `redpilllinpro01.ring.nlnog.net`), your DNS server is probably not DNSSEC capable. You can either ask your ISP or IT department to fix that, or change your system use a public DNS server known to be DNSSEC capable.

[Cloudflare's 1.1.1.1](https://1.1.1.1/dns/) is one well-known example of a public recursive resolver that supports DNSSEC. To change to it, replace any pre-existing `nameserver` lines in `/etc/resolv.conf` with the following:

```
nameserver 1.1.1.1
nameserver 2606:4700:4700::1111
nameserver 1.0.0.1
nameserver 2606:4700:4700::1001
```

### 4. Configuring the system stub resolver to request DNSSEC validation

By default, the system stub resolver (part of the C library) does not set the `DO` (*«DNSSEC OK»*) bit in outgoing queries. This prevents DNSSEC validation.

DNSSEC is enabled in the stub resolver by enabling [EDNS0](https://tools.ietf.org/html/rfc2671). This is done by adding the following line to `/etc/resolv.conf`:

```
options edns0
```

### 5. Configuring the SSH client to look for host keys in DNS

Easy peasy: either you can add the line `VerifyHostKeyDNS yes` to your `~/.ssh/config` file, or you can supply it on the command line using `ssh -o VerifyHostKeyDNS=yes`.

## Verifying that it works

If you have successfully implemented steps 1-5 above, we are ready for a test!

If you have only done step 3-5, you can still test using `redpilllinpro01.ring.nlnog.net` (or any other node in the [NLNOG RING](https://ring.nlnog.net) for that matter). The NLNOG RING nodes will respond to SSH connection attempts from everywhere, and they have all DNSSEC-signed `SSHFP` records registered.


```console
$ ssh -o UserKnownHostsFile=/dev/null -o VerifyHostKeyDNS=yes no-such-user@redpilllinpro01.ring.nlnog.net
no-such-user@redpilllinpro01.ring.nlnog.net: Permission denied (publickey).
```

Ignore the fact that the login attempt failed with *«permission denied»* - this test was a complete success, as the SSH client did not ask to manually verify the SSH host key.

`UserKnownHostsFile=/dev/null` was used to ensure that any host keys manually added to `~/.ssh/known_hosts` at an earlier point in time would be ignored and not skew the test.

It is worth noting that SSH does *not* add host keys verified using `SSHFP` records to the `~/.ssh/known_hosts` file - it will validate the `SSHFP` records every time you connect. This ensures that even if the host keys change, e.g., due to the server being re-provisioned, the ominous *«IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY»* warning will not appear - provided the `SSHFP` records in DNS have been updated, of course.

## Trusting the recursive resolver

The setup discussed in this post places implicit trust in the recursive resolver used by the system. That is, you will be trusting it to diligently validate any DNSSEC signatures on the responses it gives you, and to only set the *«Authenticated Data»* flag if those signatures are truly valid.

You are also placing trust in the network path between the host and the recursive resolver. If the network is under control by a malicious party, the DNS queries sent from your host to the recursive resolver could potentially be hijacked and redirected to a rogue recursive resolver.

This means that an attacker with the capability to hijack or otherwise interfere with both your SSH and DNS traffic could potentially set up a fraudulent SSH server for you to connect to, and make your recursive resolver lie about the SSH host keys being correct and valid according to DNSSEC. The SSH client will not be able to detect this situation on its own.

In order to detect such attacks, it is necessary for your host to double-check the validity of answers received from the recursive resolver by performing local DNSSEC validation. How to set up this will be the subject of a future post here on the [Redpill Lipro techblog](https://www.redpill-linpro.com/techblog/). Stay tuned!
