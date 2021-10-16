---
layout: post
title: everyone should use aliases
categories: [gnulinux]
tags: [random]
fullview: false
comments: true
---
i did recently a full wipe up of my laptop and one of the very first things i do in such cases is getting my aliases file from github...

```
ip=current_ip_im_working_on
website=current_website_im_working_on
myip=10.10.14.249

# ifconfig... but better
alias ifconfig='ip -s -c -h a'

# ovpnn hackthebox
alias vpn_lab='sudo openvpn lab_death2raz.ovpn'
alias vpn_fortresses='sudo openvpn fortresses_death2raz.ovpn'

# edit hosts quickly
alias vimhosts='vim /etc/hosts'
alias vimalias='vim ~/.bash_aliases'
```

i have aliases for connecting to the hackthebox vpn, editing the host file, the aliases file etc... what are your fav ones?
