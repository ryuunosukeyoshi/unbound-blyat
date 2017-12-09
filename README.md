# unbound-unblock
Unbound.conf include file to unblock domains

# Why

NoU.


# Installation

The Installation is very manual at the moment, but not hard.

Procedure consist of
* Clone this repo, I suggest you clone into your home dir
* Move this repo/files into some specific place, move into /etc
* Add the file with domain records into Unbound configuration

# Steps

First get the repo and files

```
$ git clone https://github.com/ryuunosukeyoshi/unbound-unblock.git
Cloning into 'unbound-unblock'...
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (4/4), done.
Checking connectivity... done.
```

Then move into a specific place
```
$ sudo mv unbound-unblock /etc
```

then add the following statement to */etc/unbound/unbound.conf*
```
include: "/etc/unbound-unblock/unblocked.conf"
```

# Updating

Just do a git pull,
```
$ cd /etc/unbound-unblock/
$ git pull
Already up-to-date.
$ unbound-checkconf
unbound-checkconf: no errors in /etc/unbound/unbound.conf
```

If there are no errors, you can ask Dnssec-Trigger to quit with the menu
and it will restart automatically.

# Checking

Try lookup of a "blocked name", like:

Mac
```
$ host popcorntime.dk 127.0.0.1
Using domain server:
Name: 127.0.0.1
Address: 127.0.0.1#53
Aliases:

popcorntime.dk has address 77.66.80.49
```

Windows
```
nslookup something something
```

also make sure to look up a name from the same zone, which is not blocked like:
```
$ host www.dr.dk 127.0.0.1
Using domain server:
Name: 127.0.0.1
Address: 127.0.0.1#53
Aliases:

www.dr.dk is an alias for www.gss.dr.dk.
www.gss.dr.dk has address 159.20.6.6
```
If you have problems you can check the configuration like this:
```
kunoichi:unbound-unblock root# unbound-checkconf
unbound-checkconf: no errors in /etc/unbound/unbound.conf
```

# How does it work

Unbound is a validating recursive caching resolver, it can take a question
like www.icann.org and return an IP address like 192.0.32.7.

Unbound can also add some records, creating a transparent zone, which
has records for things that have been removed from a zone/domain like dk.

The first record to be added to this was,

```
# Danish blocked domains
    local-zone: "dk" transparent
    local-data: "popcorntime.dk.                  IN A 77.66.80.49"
```

This makes Unbound work for all existing records - names - but for the specific
domain/name popcorntime.dk it will respond with the IP.
