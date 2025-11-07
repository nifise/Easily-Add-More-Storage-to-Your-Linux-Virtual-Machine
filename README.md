# Easily-Add-More-Storage-to-Your-Linux-Virtual-Machine
## A concise guide on expanding disk space in a Linux VM when unallocated space is blocked by extended/swap partitions
#### NOTE: Use this only if newly unallocated disk space is not adjacent to main storage, i.e. the space is under the second partition ,/dev/sda2. 
#### Issue: Linux couldn’t expand /dev/sda1 because the unallocated space wasn’t adjacent, the extended and swap partitions were blocking it.

Make sure you have at least a few GB of free space on your host machine before increasing the VM’s disk.

Here is a video which guides you though on most of what to do and how. https://youtu.be/_26H1few2yI?si=wQBQ3kPe_JugC3Zd 

Steps:

- Turn of the machine.
- Then go to the virtual box manager. Then File on the top left of the window -> Tools -> Virtual Media Manager. Choose the machine then go down to “Attribute”. Increase the space as needed, then apply.
- Turn back on the machine.
- Before doing anything, CREATE A SNAPSHOT to save machine state before configuration. Snapshots don’t undo anything automatically, they’re just restore points. Reverting to a snapshot will roll back all changes made after it.
- Install Gparted.
Sudo apt-get install gparted
- Once gparted is open, you should see 2 main partitions. The main partition (which was /dev/sda1 for me), and /dev/sda2 where the file systems under it are “linux-swap” and “unallocated”.
- Turn off the “linux-swap” on the terminal using:
  Sudo swapoff -a
  Removing these partitions merged all the free space into one continuous block after /dev/sda1, allowing it to be expanded.
  Confirm by using
  free -h
  Swap line should show 0B
- In GParted, delete: the swap partition and /dev/sda2 (the extended container) by right clicking then remove. Don’t forget to apply by clicking on the check. Now you should see only  the unallocated space and the main partition. This will now allow you to expand the main partition. Right click on the main partition then Resize/Move.
- Apply the changes then it’s done. 
- Confirm storage by using the “df -h” command.

#### Optional, if you want the swipe partition back, you could create a swipefile instead by using these commands:
- sudo fallocate -l 1G /swapfile
- sudo chmod 600 /swapfile
- sudo mkswap /swapfile
- sudo swapon /swapfile
- “1G” is the storage you are giving to the swipefile. You can adjust 1G to any size you prefer.
- Then, free -h, to confirm.
