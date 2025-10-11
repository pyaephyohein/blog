---
layout: post
title: "How to Setup Time Zone in  Linux"
date: 2022-03-06 09:00:00 +0700
tags: [Tech]
reading_time: 3
image: /assets/images/default-banner.png
---

### Configure, disable ssh root login.

``` bash
sudo tzselect
```
#### Type number for your region


```bash
Please identify a location so that time zone rules can be set correctly.
Please select a continent, ocean, "coord", or "TZ".
 1) Africa
 2) Americas
 3) Antarctica
 4) Asia
 5) Atlantic Ocean
 6) Australia
 7) Europe
 8) Indian Ocean
 9) Pacific Ocean
10) coord - I want to use geographical coordinates.
11) TZ - I want to specify the timezone using the Posix TZ format.
#? 
```
#### Type number for your country


```bash
Please select a country whose clocks agree with yours.
 1) Afghanistan		  18) Iraq		    35) Pakistan
 2) Antarctica		  19) Israel		    36) Palestine
 3) Armenia		  20) Japan		    37) Philippines
 4) Azerbaijan		  21) Jordan		    38) Qatar
 5) Bahrain		  22) Kazakhstan	    39) Russia
 6) Bangladesh		  23) Korea (North)	    40) Saudi Arabia
 7) Bhutan		  24) Korea (South)	    41) Singapore
 8) Brunei		  25) Kuwait		    42) Sri Lanka
 9) Cambodia		  26) Kyrgyzstan	    43) Syria
10) China		  27) Laos		    44) Taiwan
11) Cyprus		  28) Lebanon		    45) Tajikistan
12) East Timor		  29) Macau		    46) Thailand
13) Georgia		  30) Malaysia		    47) Turkmenistan
14) Hong Kong		  31) Mongolia		    48) United Arab Emirates
15) India		  32) Myanmar (Burma)	    49) Uzbekistan
16) Indonesia		  33) Nepal		    50) Vietnam
17) Iran		  34) Oman		    51) Yemen
#? 
```
#### Type 1 or 2
```bash
The following information has been given:

	Myanmar (Burma)

Therefore TZ='Asia/Yangon' will be used.
Selected time is now:	Sun Mar  6 04:37:48 PM +0630 2022.
Universal Time is now:	Sun Mar  6 10:07:48 AM UTC 2022.
Is the above information OK?
1) Yes
2) No
```
#### After Select Yes

```bash
You can make this change permanent for yourself by appending the line
	TZ='Asia/Yangon'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /usr/bin/tzselect command in shell scripts:
Asia/Yangon
```
