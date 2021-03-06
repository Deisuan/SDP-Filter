A lightweight SDP Filter
=================================================

© by Moritz Blanke 


Description:
------------

SDP-Filter provies an easy way to filter SDP (Session Description Protocol) packets


License:
-------

GPLv3, see LICENSE File.


Dependencies:
------------

* libnetfilter-queue
* linux-kernel >= 2.6.14


Usage:
------

SDP-Filter needs to be fed the packets via the NFQUEUE of iptables, for example:

    iptables -t mangle -A POSTROUTING -p udp --dport 9875 -j NFQUEUE --queue-num 42

Kernel module nfnetlink_queue needs to be loaded.
Since UDP:9875 is the standard port for SDP, any packets which may be fed into the queue
(e.g. through misconfiguration of iptables) and are NOT SDP packets on UDP:9875 are
being forwarded as further specified by iptables.


Configuration:
--------------

SDP-Filter provides harcoded configuration through the file config.h

    [1] #define QUEUE 42
    [2] #define BLACKLIST true
    [3] #define RULESC 2
    [4] #define RULES\
    [5]	{<verdict>,<attribute>,<filter>,<string>}, \
    [6]	{<verdict>,<attribute>,<filter>,<string>}, \
    [7]

[1] QUEUE defines the netfilter-queue, as specified above by iptables

[2] BLACKLIST defines the default behaviour of SDP-Filter:
* true: blacklist - all packets, except for those denied by the rules are forwarded 
* false: whitelist - all packets, except for those allowed by the rules are dropped

[3] RULESC defines the number of rules specified in 4-6]...

[4-6] RULES defines the rules by which SDP-Filter filters

* verdict: ALLOW || DISALLOW specifies which action has to be taken if a rule matches
* attribute: "string" specifies the attribute which is to be tested. e.g. "a=x-plgroup", "s=" or "m=video". See RFC4566 (or use Wireshark) for a list of viable attributes
* filter: IS || BEGINS_WITH || ENDS_WITH || CONTAINS string operator which is used to compare string to the value of the attribute.
* string: "string" specifies the value which is compared by above operators against the specified attribute

[7] The C-Preprocessor dictates: if the last rule 6] ends with \ an empty line has to follow

	
Example:
--------

    {ALLOW,"s=",BEGINS_WITH,"[Radio] "}, \

