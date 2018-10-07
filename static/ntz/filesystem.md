# File System

- A executable file(non binary) has the following format:
    - header
        - magic number
        - sizes and starting points of the next sections of the file and flags
    - text
    - data
    - relocation bits
        - for relocating into memory
    - symbol table
        - used for debugging

- File Access
    - sequential access (old)
    - random access

### File system layout
- FS are stored in disks
    - disks can have diff partition and independent fs on each
    - `Sector 0` of the disk in called the MBR(used to boot the computer)
        - end of the MBR contains the partition table
            - one partition is marked active(boot block)
                - actually every partition has a boot block even if does not has a bootable OS in it
            - boot block loads the OS

- Implementing files
Problem: Which blocks belong to which file?
Solution:
    - Continious allocation
        - store each file as contiguous run of disk blocks
        - only makes sense in CD ROM
        - DVD uses UDF(Universal Disk Format instead) - extents
    - Linked list allocation
    - Linked list allocation using a table in memory
        - Uses FAT(File allocation table)
        - does not scale well for large disks as table has to be in memory
    - Inodes
        - associate each file with a DS called the inode
        - inode needs to be in memory only when the corresponding file is open

- Implementing directories
???

- Shared Files
hardlinks and softlinks

### Types of filesystem
- Log structured fs (not widely used)
- Journaling file system (ext family, NTFS etc)
- Virtual file system (VFS)
    - In linux, or Windows you can have multiple fs on the disk but they appear to be one FS to the user : VFS
    - all sys calls related to files are directed to the vfs
    - Key idea is to build a separate abstract layer that calls the underlying concrete FS and exposes a common interface for the userspace
    - This also means that, To create a new fs you must supply all the methods that the VFS requires.
    - FS under the VFS can be: local on the disk, NFS 

Understanding VFS
- system is booted
    - root fs is booted with vfs
    - other fs register to the vfs by providing all the methods that vfs requires for that fs
- when a file is required by a sys call
    - eg. `open('/use/myfile.txt')`
    - vfs sees that the fs is mounted at `/usr`
    - then looks for the superblock 
    - find the root directory and the path
    - VFS created a `v-node` and makes a call to the actual fs to fetch all the info in the file's `inode`
    - `vnode` lives in ram with other info about the file like calls like read, write, close etc
    - make an entry in the FD table and point it to the new `vnode`
    - return the FD to the caller where the syscall was made
