# threatinfo
A BASH script to automate threat intel lookups for single IPs.

threatinfo pulls data about an IP address from a number of sources, and formats it as a report suitable for someone looking to assess whether or not the IP should be blocked.

It requires jq and GNU awk (gawk, or just awk on Linux) to be installed.

threatinfo downloads and checks against a number of different IP blocklists. The blocklists are refreshed when required. Refresh rate varies per blocklist, between hourly and daily depending on how often they're updated.

**Syntax:** `bash threatinfo <IPv4 address>`

**Sample output:** (anonymised)


```
xxx.xx.xx.xxx
=============

    Hostname:                 Unknown
    Org:                      AS209605 UAB Host Baltic
    ISP:                      UAB Host Baltic
    Connection type:          Data Center/Web Hosting/Transit

    Geographic Info
    ===============

    Country:                  Lithuania
    Region:                   Vilnius
    City:                     Vilnius

    Timezone Info
    =============

    Timezone:                 Europe/Vilnius
    UTC offset:               +0300
    Current time:             Wed 16 Jun 2021 04:52:23 EEST

    IPData assessment
    =================
    
    Tor node?                 No
    Proxy node?               No
    Known attacker?           No
    Known threat?             No
    Known spambot?            No
    
    GreyNoise assessment
    ====================
    
    Known scanner?            Yes
    Known good actor?         No
    Category:                 malicious
    Name (if known):          unknown
    Last observed:            2021-06-15
    Link:                     https://viz.greynoise.io/ip/xxx.xx.xx.xxx
    
    AbuseIPDB assessment
    ====================
    
    Reports past 90d:         11937
    No. of reporters:         471
    Confidence of abuse:      100%
    Link:                     https://abuseipdb.com/check/xxx.xx.xx.xxx

    IP Blocklist Feeds
    ==================

    AlienVault:               Not found
    Blocklist.de:             Found
    Brute Force Blocklist:    Found
    CINS Bad IPs:             Not found
    Cleantalk Blocklist:      Found
    ET Compromised IPs:       Found
    3CoreSec Blocklist:       Not found
    Darklist:                 Found
    Greensnow Blocklist:      Found
    Talos Blocklist:          Not found
```

## Installation

### Compatibility

threatinfo is tested on Ubuntu Linux and Mac OS X. It may run on other systems, your mileage may vary.

### Prerequisites

theatinfo requires BASH, jq and GNU awk.

### First run

When you first run threatinfo it will complain that you don't have any keys, and will create a key file in `~/.threatinfo.conf`.

You'll need to populate the key file with API keys for the following services:

**ipdata.co**      - Provides useful intel about known (to IPDATA at least) scanners
**ipinfo.io**      - Excellent location data
**abuseipdb.com**  - Community-based threat intelligence data

All of these services provide a free tier that is sufficient for this tool. Signing up is painless and only takes a couple of minutes. If you're doing threat intel more broadly, I recommend you look at what these services provide more broadly.

Note that threatinfo will never require a paid subscription for any services in order to work.

In addition to those three services, you can provide a shodan.io key. If you do, data will be retrieved from Shodan and stored in `~/.threatinfo/<IPv4 address>/` for manual analysis.

As you can see from the sample output, threatinfo also retrieves an assessment from the community API of GreyNoise (also a really good service if you're doing threat intel).

### Subsequent runs

If you provide threatinfo with an IP, it will freshen the IP blocklist feeds as required (stored in `~/.threatinfo/<feedname>.data`), then pull data from IPData, IPInfo, AbuseIPDB, GreyNoise, RWhois, and potentially Shodan (if you provided a key) and format a report.

The retrieved data and a timestamped copy of the report will also be stored in `~/.threatinfo/<IPv4 address/`. The reason the report is timestamped is because feeds change over time. If you analyse the same IP in a few hours or days, the results may be different. Having a timestamped copy can be useful for explaining actions taken during incident reviews.

