---
title: "Tracking Peristent PyPI Malware"
date: 2023-07-14T16:22:24-05:00
draft: false
tags: ['python','pypi','malware']
summary: "The Python Packaging Ecosystem remains fairly stable in the broad scope of open source package distribution, but they are not immune to sustained attacks either. One threat actor group has evolved from simple nuissance to a sustained stream of spam and malware utilizing GitHub staging and direct targeting of userbases for the distribution of malicious programs."
---

A persistent nuissance to the Python Packaging Index (PyPI) has been the "Kekw LTD" threat actor group. We've largely been tracking this group since the inception of our organization, and I'd like to take some time to share the details we've found. We initially were aware of these individuals from their consistent targeting of Discord members in programming communities, offering code assistance by way of dropping malicious payloads through PyPI packages. "You need to install this library," they would acclaim. When moderators of these communities began to target this kind of behavior, this threat actor group largely shifted towards targeting individuals in Direct Messages, by way of browsing the user list for new users who would be vulnerable to this type of attack.

These payloads would drop two files, a `dcbot.jar` file which contained a token logger made by user Wiesel, and a Nuitka packed executable called `neu2.exe` which served to provide functions such as C2, token stealing, etc. This NEU2 payload, a spin-off of the common W4SP stealer, would exfiltrate user information such as web browser history, usernames and passwords, payment information, and other sensitive materials from the user's computer to both a Discord webhook and an `anonfiles | transfer.sh` type service. We largely caught onto the presence of these webhooks within the payloads, and were able to automate the detection and removal pipeline for these webhooks by passing DELETE HTTP requests to them.

Eventually, as threat actors do, their TTP's evolved. Instead of hosting obfuscated malware, they relied on productionized publishing. By automating the staging process, they were able to enumerate a weak point in the Python security architecture; the current reviews process.

## A Twofold Attack

Ultimately, this attack is carried out in two parts. GitHub accounts are used to stage payloads and act as a 'burner' account to prevent the suspensions of the threat actors' real accounts. These GitHub accounts are used to increase the attack surface of the packages which otherwise would not receive as much attention.

Utilizing automated removal detection, they enumerate pseudorandom package names such as "pythoncryptpkgsV2", "pipsqlite3liberyV2", etc., to install their own payloads through the GitHub accounts. These packages often contain the aforementioned information stealers. In the second 'prong' of the attack, these individuals have productionized the uploading of malware to the Python Package Index. These two actions together make a difficult to remove nuissance-type attack.

We coordinated with Phylum upon initial realization of this, and worked to inform the supply chain security community with intelligence as it became available to ensure the lateral and layered effort to eradicate this sustained attack. Phylum published [Respawning Malware Persists on PyPI](https://blog.phylum.io/respawning-malware-persists-on-pypi/) on May 16th, 2023 detailing our discoveries and coordination between open source security enthusiasts to effectively detect and defeat this type of attack.

## Shotgunning Information

On the dates of May 19th thru May 21st, Python Packaging administrators [released a statement](https://status.python.org/incidents/qy2t9mjjcc7g) that account creation would be suspended pending a large influx of reports and lack of administrator availability. These were tied to a single Github user, PatrickPogoda. The productionized distribution of malware had reached a critical level whereby triple-digit package counts were being staged and detected, drawing to this singular user. As time carried on, the payload being staged morphed from a plaintext stealer to an obfuscated strain of malware utilizing Fernet encrypted data to attempt to evade detection (unsuccessfully, I might add.) While we were largely able to track and detect these individuals through payload modifications, ultimately attempting to stem the tide was difficult.

This threat actor group has largely been active since late 2022, dabbling in simple information stealers and distributing them through the Hypixel modding community. The shift to staging payloads on the Python Package Index, as well as the targeting of individual members in programming communities represents a substantial increase in threat, and largely has shifted the burden from simple malware to an extreme nuissance for cybersecurity analysts. Owing to a large level of threat intelligence and excellent specialists on the Vipyr Security team, as well as poor personal operational security on the part of the threat actor group, we've largely been able to document and follow these threat actors to the corners of the internet that they operate in.

{{< figure src="kekw-flow.png" title="KEKW Threat Actor Group" >}}

As shown in the chart above, this threat actor group, which we've taken to calling by their original name "Kekw" has undergone several mutations of malware, all of which we currently successfully detect. We've also filed appropriate reports with domain registrars and GitHub to have these accounts suspended (successfully in most cases). While DCBOT and NEU2 present less of an issue holistically (and frankly, are well documented and detected at this point), the desire for these individuals to distribute their payload has dictated that they rely less on obfuscation and quiet signatures, and more on raw volume.

## So What?

While this may seem like small fish in the grand scheme of things; a bunch of script kiddies generating nuissance malware that is often easily evaded by average users, the impact is still abundantly clear. User **_Battleb0t_** maintains a subdomain on his personal blog that contains numerous pictures of victims of the malware with both the names and organizations of Kekw and various threat actors within. Targeting and exploiting children for enjoyment represents a deplorable action that myself and Vipyr Security are dedicated to preventing.

On top of that, these packages often squat common library names in dependancy confusion type attacks, whereby a user intending to install a SQLite or Cryptographic package may inadvertently install these malicious packages instead. The effects are obvious-- from leaked password information to leaked business intelligence. While these may not be the target demographics, the threat against average users is still very much real.

Prompting this blog post was the emergence of a new attack on the Python Package Index by the Kekw threat actor group utilizing the GitHub names [mmatthew130](https://github.com/mmatthew130) and [wieselnuggis](https://github.com/wieselnuggis). Vipyr Security remains in coordination with appropriate adminstrators across the open source security spectrum to detect and prevent these types of attacks.

## Additional Reading

[Inside of the WASP's Nest](https://blog.virustotal.com/2023/06/inside-of-wasps-nest-deep-dive-into.html) - VirusTotal, 19 June 2023

[Respawning Malware Persists on PyPI](https://blog.phylum.io/respawning-malware-persists-on-pypi/) - Phylum, 16 May 2023

[PyPI Temporarily Pauses New Users...](https://www.bleepingcomputer.com/news/security/pypi-temporarily-pauses-new-users-projects-amid-high-volume-of-malware/) - BleepingComputer, 20 May 2023
