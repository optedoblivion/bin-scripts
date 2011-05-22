#!/usr/bin/perl
######################################################################
#   Original notice
#   File          : split_bootimg.pl
#   Author(s)     : William Enck <enck@cse.psu.edu>
#   Copyright (c) 2008 William Enck
#
######################################################################

use strict;
use warnings;

# Turn on print flushing
$|++;

######################################################################
## Global Variables and Constants

my $SCRIPT = __FILE__;
my $IMAGE_FN = undef;

# Constants (from bootimg.h)
use constant BOOT_MAGIC => 'ANDROID!';
use constant BOOT_MAGIC_SIZE => 8;
use constant BOOT_NAME_SIZE => 16;
use constant BOOT_ARGS_SIZE => 512;

# Unsigned integers are 4 bytes
use constant UNSIGNED_SIZE => 4;

######################################################################
## Supporting Subroutines

=header_format (from bootimg.h)
struct boot_img_hdr
{
    unsigned char magic[BOOT_MAGIC_SIZE];

    unsigned kernel_size;  /* size in bytes */
    unsigned kernel_addr;  /* physical load addr */

    unsigned ramdisk_size; /* size in bytes */
    unsigned ramdisk_addr; /* physical load addr */

    unsigned second_size;  /* size in bytes */
    unsigned second_addr;  /* physical load addr */

    unsigned tags_addr;    /* physical addr for kernel tags */
    unsigned page_size;    /* flash page size we assume */
    unsigned unused[2];    /* future expansion: should be 0 */

    unsigned char name[BOOT_NAME_SIZE]; /* asciiz product name */

    unsigned char cmdline[BOOT_ARGS_SIZE];

    unsigned id[8]; /* timestamp / checksum / sha1 / etc */
};
=cut
sub parse_header {
    my ($fn) = @_;
    my $buf = undef;

    open INF, $fn or die "Could not open $fn: $!\n";
    binmode INF;

    # Read the Magic
    read(INF, $buf, BOOT_MAGIC_SIZE);
    unless ($buf eq BOOT_MAGIC) {
	die "Android Magic not found in $fn. Giving up.\n";
    }

    # Read kernel size and address (assume little-endian)
    read(INF, $buf, UNSIGNED_SIZE * 2);
    my ($k_size, $k_addr) = unpack("VV", $buf);

    # Read ramdisk size and address (assume little-endian)
    read(INF, $buf, UNSIGNED_SIZE * 2);
    my ($r_size, $r_addr) = unpack("VV", $buf);

    # Read second size and address (assume little-endian)
    read(INF, $buf, UNSIGNED_SIZE * 2);
    my ($s_size, $s_addr) = unpack("VV", $buf);

    # Ignore tags_addr
    read(INF, $buf, UNSIGNED_SIZE);

    # get the page size (assume little-endian)
    read(INF, $buf, UNSIGNED_SIZE);
    my ($p_size) = unpack("V", $buf);

    # Ignore unused
    read(INF, $buf, UNSIGNED_SIZE * 2);

    # Read the name (board name)
    read(INF, $buf, BOOT_NAME_SIZE);
    my $name = $buf;

    # Read the command line
    read(INF, $buf, BOOT_ARGS_SIZE);
    my $cmdline = $buf;

    # Ignore the id
    read(INF, $buf, UNSIGNED_SIZE * 8);

    # Close the file
    close INF;

    # Print important values
    printf "Page size: %d (0x%08x)\n", $p_size, $p_size;
    printf "Kernel size: %d (0x%08x)\n", $k_size, $k_size;
    printf "Kernel address: %d (0x%08x)\n", $k_addr, $k_addr;
    printf "Ramdisk size: %d (0x%08x)\n", $r_size, $r_size;
    printf "Second size: %d (0x%08x)\n", $s_size, $s_size;
    printf "Board name: $name\n";
    printf "Command line: $cmdline\n";

}

######################################################################
## Configuration Subroutines

sub parse_cmdline {
    unless ($#ARGV == 0) {
	die "Usage: $SCRIPT boot.img\n";
    }
    $IMAGE_FN = $ARGV[0];
}

parse_cmdline();
parse_header($IMAGE_FN);
