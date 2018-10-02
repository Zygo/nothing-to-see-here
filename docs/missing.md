Features You Might Expect That bees Doesn't Have
------------------------------------------------

* There's no configuration file (patches welcome!).  There are
some tunables hardcoded in the source that could eventually become
configuration options.  There's also an incomplete option parser
(patches welcome!).

* There's no way to *stop* the bees daemon.  Use SIGKILL, SIGTERM, or
Ctrl-C for now.  Some of the destructors are unreachable and have never
been tested.  bees checkpoints its progress every 15 minutes (also not
configurable, patches welcome) and will repeat some work when restarted.

* The bees process doesn't fork and writes its log to stdout/stderr.
A shell wrapper is required to make it behave more like a daemon.

* There's no facility to exclude any part of a filesystem or focus on
specific files (patches welcome).

* PREALLOC extents and extents containing blocks filled with zeros will
be replaced by holes.  There is no way to turn this off.

* Consecutive runs of duplicate blocks that are less than 12K in length
can take 30% of the processing time while saving only 3% of the disk
space.  There should be an option to just not bother with those, but it's
complicated by the btrfs requirement to always dedupe complete extents.

* There is a lot of duplicate reading of blocks in snapshots.  bees will
scan all snapshots at close to the same time to try to get better
performance by caching, but really fixing this requires rewriting the
crawler to scan the btrfs extent tree directly instead of the subvol
FS trees.

* Block reads are currently more allocation- and CPU-intensive than they
should be, especially for filesystems on SSD where the IO overhead is
much smaller.  This is a problem for CPU-power-constrained environments
(e.g. laptops running from battery, or ARM devices with slow CPU).

* bees can currently fragment extents when required to remove duplicate
blocks, but has no defragmentation capability yet.  When possible, bees
will attempt to work with existing extent boundaries, but it will not
aggregate blocks together from multiple extents to create larger ones.

* When bees fragments an extent, the copied data is compressed.  There
is currently no way (other than by modifying the source) to select a
compression method or not compress the data (patches welcome!).

* It is theoretically possible to resize the hash table without starting
over with a new full-filesystem scan; however, this feature has not been
implemented yet.
