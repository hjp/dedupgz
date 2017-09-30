Deduplicate gzip-compressed files.

Gzipped files normally contain the original filename and a time stamp,
so when two files with identical content are gzipped, the gzipped files
are not identical. Also the output depends on the compression level and
other options (e.g., --rsyncable).

The upshot is that normal deduplication techniques don't work for
gzipped files.

To deduplicate gzipped files, proceed as follows:

    Get a list of all files ending in '.gz', ordered by date.

    For each filename in the list:
        Stat the file.
        Check if we've seen the device/inode combination before.
        If so, it is already a hard-link to a file we have processed, so
        we don't need to do it again.
        Compute the SHA256 hash of the uncompressed contents of the
        file.
        Check if we've seen that hash before. If so (and if the device
        number is the same), replace the file with a hardlink to the
        previous file.
        Record the hash, filename, device and inode number.
