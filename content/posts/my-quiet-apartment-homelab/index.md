---
author: Andrew Cole
date: 2015-09-14
category:
- geek
title: My (Quiet) Apartment Homelab
draft: True
---

_Posted from the future (30/01/2020). Backdated to the date of the original [Reddit](\"https://www.reddit.com/r/homelab/comments/3kwa35/my_quiet_apartment_homelab/\") and [Imgur](\"https://imgur.com/a/GO43m\") posts._

\n\n\n\n

A small homelab built for apartment living. Hardware as of September 2015.

\n\n\n\n

<figure class="\&quot;wp-block-image" size-large\"="">![\"\"](\"https://i2.wp.com/illallangi.com/wp-content/uploads/2020/01/rNBkFmn.jpg?resize=1024%2C768&ssl=1\")

<figcaption>Homelab in all it’s glory</figcaption>

</figure>

\n\n\n\n

All of my gear on an IKEA shelving unit. Everything lives on this rack at the foot of my bed, so it has to be reasonably quiet.

\n\n\n\n

<figure class="\&quot;wp-block-image" size-large\"="">![\"\"](\"https://i2.wp.com/illallangi.com/wp-content/uploads/2020/01/W1Wc747.jpg?resize=1024%2C768&ssl=1\")

<figcaption>Netgear DGN2200 all in one modem/router/AP</figcaption>

</figure>

\n\n\n\n

This doesn't belong to me and was in the house when I moved in, and before I stood up my network was running everything. Its still in place here and configured, ready to be used in case of a failure of the rest of my network. As I travel constantly for work my housemate is trained in plugging this in in case of a failure while I'm away.

\n\n\n\n

<figure class="\&quot;wp-block-image" size-large\"="">![\"\"](\"https://i0.wp.com/illallangi.com/wp-content/uploads/2020/01/ndEC74H.jpg?resize=1024%2C768&ssl=1\")

<figcaption>Netgear GSM7224 Gigabit Switch</figcaption>

</figure>

\n\n\n\n

Next to the standby router sits my Netgear switch with a Ubiquiti AP on top. The Netgear switch provides 24 gigabit ports, supports VLans and LACP. All of the ports are configured identically. Ignore the name label - it is from several moves ago and needs to be updated.

\n\n\n\n

<figure class="\&quot;wp-block-image" size-large\"="">![\"\"](\"https://i1.wp.com/illallangi.com/wp-content/uploads/2020/01/m0oLJGq.jpg?resize=1024%2C768&ssl=1\")

<figcaption>Ubiquiti UniFi AP</figcaption>

</figure>

\n\n\n\n

Sits on top of the Netgear switch. Provides two WLAN SSIDs to the house - one for use by my housemate that only relies on the AP, switch and router being up, and another for my use which uses my virtual environment for DHCP and DNS. The Ubiquiti controller application runs on the Synology NAS. I bought this as part of a three pack, having had signal issues in this apartment with other hardware, but have not needed to use more than one.

\n\n\n\n

<figure class="\&quot;wp-block-image" size-large\"="">![\"\"](\"https://i1.wp.com/illallangi.com/wp-content/uploads/2020/01/9N0dSzw.jpg?resize=1024%2C768&ssl=1\")

<figcaption>Synology DS1813+ NAS</figcaption>

</figure>

\n\n\n\n

My NAS lives on the left of the middle shelf. Hosting 4x4TB WD Red (WD40EFRX) and 4x3TB WD Green (WD30EZRX) HDDs in SHR-2, this NAS has 18TB of usable space. Supplies SMB/CIFS to my Windows machines, iSCSI and NFS to the ESXi hosts, and Plex services to my media center.

\n\n\n\n

<figure class="\&quot;wp-block-image" size-large\"="">![\"\"](\"https://i1.wp.com/illallangi.com/wp-content/uploads/2020/01/6JJQW5i.jpg?resize=1024%2C768&ssl=1\")

<figcaption>Cisco 877</figcaption>

</figure>

\n\n\n\n

My router lives on the right of the middle shelf. Handles the ADSL connection and 3 of my VLANs. Will eventually be replaced by one or my routers that can handle more than 4 VLANs, as currently I rely on several virtual routers for my lab VLANs.

\n\n\n\n

<figure class="\&quot;wp-block-image" size-large="" is-resized\"="">![\"\"](\"https://i1.wp.com/illallangi.com/wp-content/uploads/2020/01/qINxMte.jpg?resize=656%2C492&ssl=1\")

<figcaption>Intel NUCs 1 and 2</figcaption>

</figure>

\n\n\n\n

<figure class="\&quot;wp-block-image" size-large="" is-resized\"="">![\"\"](\"https://i0.wp.com/illallangi.com/wp-content/uploads/2020/01/J2j04cW.jpg?resize=656%2C492&ssl=1\")

<figcaption>Intel NUCs 3 and 4</figcaption>

</figure>

\n\n\n\n

Finally, the bottom shelf hosts 4 Intel NUC ESXi hosts. Each NUC is configured identically, with WD 2.5\u201d HDDs and Intel SSDs. 3 of the hosts are in the \u201ccompute\u201d cluster and configured to use VSAN, forming one large datastore from all the available storage. The final host makes up the \u201cmanagement\u201d cluster, running some AD servers and VCenter servers to manage all the clusters.