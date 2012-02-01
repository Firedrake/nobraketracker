=Introduction

NoBrakeTracker is a lightweight BitTorrent HTTP tracker written in Perl.
Goals are basic functionality and a higher code quality than some of the
competition.

=Copyright

NoBrakeTracker is Copyright 2011 Roger Burton West.

This program is free software; you can redistribute it and/or modify it
under the terms of the GNU General Public License as published by the
Free Software Foundation; either version 3 of the License, or (at your
option) any later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General
Public License for more details.

You should have received a copy of the GNU General Public License along
with this program; if not, write to the Free Software Foundation, Inc.,
51 Franklin Street, Fifth Floor, Boston, MA 02110-1301, USA.

=Features

Supports all basic BitTorrent functionality (BEP3), scraping (no BEP),
external IP reporting (BEP24), partial seeding (BEP21), IPv6 (BEP7) and
compact peer lists (BEP23).

=Updates

http://firedrake.org/cgi-bin/gitweb.cgi?p=nobraketracker.git
git://firedrake.org/nobraketracker.git/

=Prerequisites

This program has been tested with Perl 5.10.0 and 5.10.1 but should work
with any reasonably modern Perl.

It relies on these non-core modules:

Convert::Bencode_XS
Digest::SHA1
HTTP::Server::Simple::CGI::PreFork
IO::Handle
JSON
Net::IPv6Addr
Net::Server
Regexp::IPv6 qw($IPv6_re)

Debian users: many of these are already in Debian/squeeze (ibjson-perl,
libdigest-sha1-perl, libregexp-ipv6-perl, libnet-ipv6-addr). However,
you will need to install a more recent Net::Server than squeeze provides
(minimum 0.99.6.1) if you want IPv6 support, and Convert::Bencode_XS and
HTTP::Server::Simple::CGI::PreFork are not in Debian at all. On a Debian
system, install dh-make-perl for automatic building.

Non-Debian users: check your OS's packages and use CPAN as appropriate.

=Configuration

The configuration file (tracker.cfg by default) is a JSON file with
configuration data. A minimal configuration might look like this:

{
   "port" : 6969,
   "allowed" : "./allowed_torrents",
   "interval" : 1800,
   "expiry" : 3600
}

Elements are:

ip (optional) - the IP address to bind to (by default, all interfaces)

port (required) - the port number to bind to

family - the address family to bind to ("AF_INET6" will use IPv6; any
other value will use IPv4)

allowed (required) - the directory containing allowed torrents (which
must exist), or '*' to track any torrent

interval (required) - the suggested client reconnection interval, in
seconds

expiry (required) - if a client makes no contact for this many seconds,
it will no longer be reported to other clients

logfile (optional) - log to this file rather than STDERR

noscrape (optional) - set to 1 to disable /scrape reporting.

nostats (optional) - set to 1 to disable /stats reporting.

=Operation

Put torrent files into the "allowed" directory or subdirectories
thereof; filenames should end with ".torrent". To refresh the list of
torrents, restart the tracker.

If a torrent filename ends with ".secret.torrent", it will be excluded
from /scrape and /stats announcements unless its info-hash is given.

Give the configuration file as a command line parameter.

There are several URL paths supported:

/announce - normal tracker functionality
/scrape - standardised scraping
/stats - list stats and peers for torrents (similar to /scrape, but more
detail and in HTML)

By analogy with /scrape, /stats takes an optional info_hash parameter to
restrict reporting to that torrent.

If a peer connects from one IP address but announces another, /stats
will report both.

=Logging

Log lines are space-separated and take the form:

date mode details

mode can be "startup" (the tracker has been started), "client" (a client
has used the tracker), "torrent" (a torrent has been added or rmeoved),
"stats" (a stats request has been made), or "scrape" (a scrape request
has been made).

=Bugs

Running in open mode will probably exhaust resources since torrents
don't expire.

No UDP-tracker support (BEP15).

No SSL support. (Potentially available in this server.)

No failure-retry support (BEP31).

No peer obfuscation (BEP8).

=Version history

0.01 - initial release

0.02 - fixed lack of return for invalid torrent;
       fixed error reporting to clients;
       added /scrape support.

0.03 - added external IP support (BEP 24);
       added partial seed support (BEP 21);
       removed /scan action (use HUP or restart).

0.04 - added noscrape config option;
       added secret torrent;
       added info_hash support to /scrape and /stats.

0.05 - added IPv6 support;
       added timeout for state saving;
       reads statefile only at startup time;
       added display of torrent name in /stats (you will need to delete
         the statefile).

0.06 - switched to preforking server back-end;
       removed statefile;
       split noscrape/nostats.

=Reference

http://www.bittorrent.org/beps/bep_0003.html
http://www.morehawes.co.uk/the-bittorrent-protocol
http://wiki.theory.org/BitTorrentSpecification
