---
published: true
title: 'IPv6 roaming in Romania'
layout: post
---

I am currently in Romania, Corona-stranded and rather bored. To kill some time, I decided to [hack together a script](https://github.com/toreanderson/3gpptest) that performs automated testing of IPv6 capability in all available [PLMNs](https://en.wikipedia.org/wiki/Public_land_mobile_network) and use it with SIM cards from three different IPv6-capable PLMNs ([🇵🇱 Orange](https://www.orange.pl), [🇸🇪 Tele2](https://www.tele2.se) and [🇳🇴 Telenor](https://www.telenor.no)).

## tl;dr

🤷‍♂️ [Digi](https://www.digiromania.ro)<br/>
✅ [Orange](https://www.orange.ro)<br/>
✅ [Telekom](https://www.telekom.ro)<br/>
❌ [Vodafone](https://www.vodafone.ro)

Requesting a dual-stack `IPv4v6` bearer always succeeds, but the resulting connectivity is sometimes IPv4-only.

## Test results

Click the links in the **Tech** column to be taken directly to the corresponding output from my test script, and make sure to check out the footnotes for more information and explanations.

### Digi (MCCMNC 22605)

| SIM card   | Tech                                                                                                                    | IPv4v6                   | IPv6                   |
|------------|-------------------------------------------------------------------------------------------------------------------------|--------------------------|------------------------|
| 🇵🇱 Orange  | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L35-L37)     | 📵 [^noreg]             | 📵 [^noreg]             |
| 🇵🇱 Orange  | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L84-L86)    | 📵 [^noreg]             | 📵 [^noreg]             |
| 🇸🇪 Tele2   | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L35-L37)      | 📵 [^noreg]             | 📵 [^noreg]             |
| 🇸🇪 Tele2   | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L84-L86)     | 📵 [^noreg] [^modembug] | 📵 [^noreg] [^modembug] |
| 🇳🇴 Telenor | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L54-L56)    | 📵 [^noreg]             | 📵 [^noreg]             |
| 🇳🇴 Telenor | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L103-L105) | 📵 [^noreg]             | 📵 [^noreg]             |

### Orange (MCCMNC 22610)

| SIM card   | Tech                                                                                                                    | IPv4v6           | IPv6          |
|------------|-------------------------------------------------------------------------------------------------------------------------|------------------|---------------|
| 🇵🇱 Orange  | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L39-L60)     | ✅ [^2bearers]   | ✅            |
| 🇵🇱 Orange  | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L111-L132)  | ✅ [^2bearers]   | ✅            |
| 🇵🇱 Orange  | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L179-L200)   | ✅ [^2bearers]   | ✅            |
| 🇸🇪 Tele2   | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L39-L60)      | ✅               | ✅            |
| 🇸🇪 Tele2   | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L92-L109)    | 4️⃣ [^v4fallback] | ❌ [^cause33] |
| 🇸🇪 Tele2   | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L137-L154)    | 4️⃣ [^v4fallback] | ❌ [^cause33] |
| 🇳🇴 Telenor | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L58-L79)    | ✅               | ✅            |
| 🇳🇴 Telenor | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L126-L147) | ✅               | ✅            |
| 🇳🇴 Telenor | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L190-L211)  | ✅               | ✅            |

### Telekom (MCCMNCs 22603 and 22606)

| SIM card   | Tech                                                                                                                   | IPv4v6           | IPv6          |
|------------|------------------------------------------------------------------------------------------------------------------------|------------------|---------------|
| 🇵🇱 Orange  | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L31-L33)    | 📵 [^noreg]      | 📵 [^noreg]   |
| 🇵🇱 Orange  | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L88-L109)  | ✅ [^2bearers]   | ✅            |
| 🇵🇱 Orange  | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L156-L177)  | ✅ [^2bearers]   | ✅            |
| 🇸🇪 Tele2   | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L31-L33)     | 📵 [^noreg]      | 📵 [^noreg]   |
| 🇸🇪 Tele2   | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L88-L90)    | 📵 [^noreg]      | 📵 [^noreg]   |
| 🇸🇪 Tele2   | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L133-L135)   | 📵 [^noreg]      | 📵 [^noreg]   |
| 🇳🇴 Telenor | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L31-L52)   | ✅ [^2bearers]   | ✅            |
| 🇳🇴 Telenor | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L107-L124)| 4️⃣ [^v4fallback] | ❌ [^cause33] |
| 🇳🇴 Telenor | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L171-L188) | 4️⃣ [^v4fallback] | ❌ [^cause33] |

### Vodafone (MCCMNC 22601)

| SIM card   | Tech                                                                                                                   | IPv4v6           | IPv6          |
|------------|------------------------------------------------------------------------------------------------------------------------|------------------|---------------|
| 🇵🇱 Orange  | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L12-L29)    | 4️⃣ [^v4fallback] | ❌ [^cause32] |
| 🇵🇱 Orange  | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L65-L82)   | 4️⃣ [^v4fallback] | ❌ [^cause32] |
| 🇵🇱 Orange  | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_26003-Orange-Poland.txt#L137-L154)  | 4️⃣ [^v4fallback] | ❌ [^cause32] |
| 🇸🇪 Tele2   | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L12-L29)     | 4️⃣ [^v4fallback] | ❌ [^cause32] |
| 🇸🇪 Tele2   | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L65-L82)    | 4️⃣ [^v4fallback] | ❌ [^cause33] |
| 🇸🇪 Tele2   | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24007-Tele2-Sweden.txt#L114-L131)   | 4️⃣ [^v4fallback] | ❌ [^cause33] |  
| 🇳🇴 Telenor | [LTE](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L12-L29)   | 4️⃣ [^v4fallback] | ❌ [^cause32] |
| 🇳🇴 Telenor | [UMTS](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L84-L101) | 4️⃣ [^v4fallback] | ❌ [^cause32] |
| 🇳🇴 Telenor | [GSM](https://github.com/toreanderson/3gpptest/blob/master/results/romania/2020-04_24201-Telenor-Norway.txt#L152-L169) | 4️⃣ [^v4fallback] | ❌ [^cause32] |

## Footnotes

[^noreg]: I was unable to register in this PLMN (probably because there is no roaming agreement with the SIM card operator), so it was impossible to test.
[^modembug]: Trying to register in the UMTS Digi PLMN with my 🇸🇪 Tele2 SIM card apears to trigger a bug in my modem. After such an attempt (which timed out in any case), it would be unable to register in *any* LTE PLMN and be unable to attach packet service in *any* UMTS or GSM PLMN (even though registration went fine). The modem had to be rebooted with `AT+MSO=0` in order to start working again. I therefore had to manually make my script skip the Digi PLMN while testing with 🇸🇪 Tele2.
[^2bearers]: Automatic fallback from a single dual-stack `IPv4v6` bearer to dual single-stack `IP`(v4)+`IPv6` bearers appears to take place. I can spot this happening because the modem only reports IP config for a single stack even though both actually work. 🇵🇱 Orange is known to not support the dual-stack `IPv4v6` bearer, so this is the expected behaviour. As for 🇳🇴 Telenor, I do not know if they perform [IPv4v6 subscriber capability filtering](https://tools.ietf.org/html/rfc7445#section-3) or if Telekom do not support/block `IPv4v6` bearers.
[^v4fallback]: Successful automatic fallback from `IPv4v6` to `IPv4`, likely due to `IPv4v6` and `IPv6` not working.
[^cause33]: Failed with 3GPP cause code 33: *service not activated*. Likely due to [IPv6 subscriber capability filtering performed by the SIM card ](https://tools.ietf.org/html/rfc7445#section-3).
[^cause32]: Failed with 3GPP cause code 32: *service option not supported*. Likely an intentional block by Vodafone. 😠
