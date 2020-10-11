---
title: 24Tb FreeBSD NAS budget DIY
date: 2011-07-05T23:53:15+01:00
author: Jake
layout: default
parent: ZFS
grand_parent: Storage
permalink: /make-build-your-freebsd-word/24tb-freebsd-nas-budget-diy.html
categories:
  - Build Your FreeBSD World
  - Storage
tags:
  - DIY NAS
  - storage
  - ZFS
---
# 24Tb FreeBSD NAS budget DIY

I had a bunch of 3Tb disks come in and thought it would be a good opportunity to see how many I could join together.

I used 9 WD Green 3Tb hard disks, 1 WD Blue 500Gb to run FreeBSD 8.2 amd64. The mother board is a Gigabyte Z68-D3-B3, the number of SATA ports are extended using the 2 port RocketRaid PCI express cards the come with the 3Tb disks. Chucked as much RAM in as I could, think it was 8Gb.

  * 9 x WD Green 3Tb hard disks
  * 2 x WD Blue 500Gb (OS mirror).
  * Gigabyte Z68-D3-B3,
  * 3 x 2 port RocketRaid PCI express cards (come with the 3Tb disks)
  * 8Gb RAM

<div>
  The RocketRaid cards (Free with the hard drives!)
</div>

<div>
  <p style="text-align: center;">
    <a href="https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-rocketraid-62x1.jpg" data-rel="lightbox-image-0" data-rl_title="" data-rl_caption="" title=""><img loading="lazy" class="size-large wp-image-373   aligncenter" title="24Tb FreeBSD NAS rocketraid 62x" src="http://www.mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-rocketraid-62x1-1024x768.jpg" alt="24Tb FreeBSD NAS rocketraid 62x" width="502" height="377" srcset="https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-rocketraid-62x1-1024x768.jpg 1024w, https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-rocketraid-62x1-300x224.jpg 300w, https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-rocketraid-62x1.jpg 1792w" sizes="(max-width: 502px) 100vw, 502px" /></a>
  </p>
</div>

<div>
  The mother board Gigabyte Z68-D3-B3
</div>

<div>
  <p style="text-align: center;">
    <a href="https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard.jpg" data-rel="lightbox-image-1" data-rl_title="" data-rl_caption="" title=""><img loading="lazy" class="size-large wp-image-376    aligncenter" title="24Tb FreeBSD NAS Gigabyte Z68-D3-B3" src="http://www.mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard-1024x768.jpg" alt="24Tb FreeBSD NAS Gigabyte Z68-D3-B3" width="498" height="374" srcset="https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard-1024x768.jpg 1024w, https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard-300x224.jpg 300w, https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard.jpg 1792w" sizes="(max-width: 498px) 100vw, 498px" /></a>
  </p>
  
  <p style="text-align: center;">
    <a href="https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard-2.jpg" data-rel="lightbox-image-2" data-rl_title="" data-rl_caption="" title=""><img loading="lazy" class="size-large wp-image-377   aligncenter" title="24Tb FreeBSD NAS motherboard Gigabyte Z68-D3-B3" src="http://www.mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard-2-1024x768.jpg" alt="24Tb FreeBSD NAS motherboard Gigabyte Z68-D3-B3" width="491" height="369" srcset="https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard-2-1024x768.jpg 1024w, https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard-2-300x225.jpg 300w, https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-motherboard-2.jpg 2048w" sizes="(max-width: 491px) 100vw, 491px" /></a>
  </p>
</div>

<div>
  The complete server &#8211; ready for FreeBSD 9.0 install &#8211; ZFS v28!
</div>

<div>
  <p style="text-align: center;">
    <p style="text-align: center;">
      <a href="https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-complete.jpg" data-rel="lightbox-image-3" data-rl_title="" data-rl_caption="" title=""><img loading="lazy" class="size-large wp-image-378   aligncenter" title="24Tb FreeBSD NAS complete" src="http://www.mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-complete-1024x768.jpg" alt="24Tb FreeBSD NAS complete" width="491" height="369" srcset="https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-complete-1024x768.jpg 1024w, https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-complete-300x225.jpg 300w, https://mebsd.com/wp-content/uploads/2011/11/24Tb-FreeBSD-NAS-complete.jpg 2048w" sizes="(max-width: 491px) 100vw, 491px" /></a>
    </p></div> 


This is the result of the setup.

![24Tb FreeBSD NAS setup](/wp-content/uploads/2011/07/3tzfs11.jpg){:class="img-responsive"}
![WD Green 3Tb disks](/wp-content/uploads/2011/07/3tzfs2.jpg){:class="img-responsive"}

I run a basic install of FreeBSD 8.2 RELEASE amd64 with zfs v15 to create a 24Tb striped zpool.

```sh
# zpool create tank /dev/ada0 /dev/ada1 /dev/ada2 /dev/ada3 /dev/ada4 /dev/ada5 /dev/ada6 /dev/ada7 /dev/ada8
# zfs create tank/data
```

![24Tb FreeBSD zfs](/wp-content/uploads/2011/07/3tzfs3.jpg){:class="img-responsive"}