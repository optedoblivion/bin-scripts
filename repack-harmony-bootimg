#!/usr/bin/perl -W

use strict;
use Cwd;


my $dir = getcwd;

my $usage = "repack-bootimg.pl <kernel> <ramdisk-directory> <outfile>\n";

die $usage unless $ARGV[0] && $ARGV[1] && $ARGV[2];

chdir $ARGV[1] or die "$ARGV[1] $!";

system ("find . | cpio -o -H newc | gzip > $dir/ramdisk-repack.cpio.gz");

chdir $dir or die "$ARGV[1] $!";;

system ("mkbootimg --cmdline \"mem=448M\@0M nvmem=64M\@448M vmalloc=192M video=tegrafb console=ttyS0,115200n8 usbcore.old_scheme_first=1 cpuid=200102 devicetype=1002 btmac=9c5ed6131a00 tegraboot=nand mtdparts=tegra_nand:16384K\@12032K(misc),16384K\@62208K(recovery),16384K\@79104K(boot),204800K\@96000K(system),222464K\@301312K(cache),4096K\@7424K(bootbmp),32768K\@28928K(logodata) androidboot.hardware=harmony\" --kernel $ARGV[0] --ramdisk ramdisk-repack.cpio.gz -o $ARGV[2]");

unlink("ramdisk-repack.cpio.gz") or die $!;

print "\nrepacked boot image written at $ARGV[1]-repack.img\n";
