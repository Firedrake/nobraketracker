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
external IP reporting (BEP24), partial seeding (BEP21), and compact peer
lists (BEP23).

=Updates

http://firedrake.org/cgi-bin/gitweb.cgi?p=nobraketracker.git
git clone -v git://firedrake.org/nobraketracker.git/ nobraketracker

=Prerequisites

This program has been tested with Perl 5.10.0 and 5.10.1 but should work
with any reasonably modern Perl.

It relies on these non-core modules:

HTTP::Server::Simple
JSON
Digest::SHA1
Regexp::IPv6
Net::IPv6Addr
LockFile::Simple
Convert::Bencode_XS

All but the last are in Debian/squeeze (libhttp-server-simple-perl,
libjson-perl, libdigest-sha1-perl, libregexp-ipv6-perl,
libnet-ipv6-addr, liblockfile-simple-perl). Convert::Bencode_XS can be
installed via CPAN, or for greater ease use dh-make-perl to create a
local deb that can be installed via dpkg.

If you wish to run with IPv6 support, you will need to patch
HTTP::Server::Simple. On a Debian/squeeze system, the patch at
http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=596176 is suitable; if
you are downloading directly from CPAN, try the patch at
https://rt.cpan.org/Ticket/Display.html?id=61200 instead.

=Configuration

The configuration file (tracker.cfg by default) is a JSON file with
configuration data. A minimal configuration might look like this:

{
   "port" : 6969,
   "allowed" : "./allowed_torrents",
   "interval" : 1800,
   "statefile" : "./statefile",
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

statefile (required) - the file that will be used to save ongoing
information

expiry (required) - if a client makes no contact for this many seconds,
it will no longer be reported to other clients

pidfile (optional) - rather than running in the foreground, daemonise
the tracker and store its PID in this file

logfile (optional) - log to this file rather than STDERR

noscrape (optional) - set to 1 to disable /scrape and /stats reporting.

=Operation

Put torrent files into the "allowed" directory; filenames should end
with ".torrent". To refresh the list of torrents, send a HUP to the
tracker process (or just stop and restart it - state is saved after each
client connection and restored on startup).

If you wish to bind to multiple (but not all) interfaces, multiple
ports, or multiple address families (IPv4/IPv6), run multiple instances
of the tracker with different configuration files, all of which specify
the same statefile.

If a torrent filename ends with ".secret.torrent", it will be excluded
from /scrape and /stats announcements unless its info-hash is given.

Give the configuration file as a command line parameter.

There are several URL paths supported:

/announce - normal tracker functionality
/scrape - standardised scraping
/stats - list stats and peers for torrents (similar to /scrape, but in
HTML)

By analogy with /scrape, /stats takes an optional info_hash parameter to
restrict reporting to that torrent.

If a peer connects from one IP address but announces another, /stats
will report the claimed address followed by the connection address.

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

No IPv6 support (BEP7).

No SSL support.

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

=Reference

http://www.bittorrent.org/beps/bep_0003.html
http://www.morehawes.co.uk/the-bittorrent-protocol
http://wiki.theory.org/BitTorrentSpecification
