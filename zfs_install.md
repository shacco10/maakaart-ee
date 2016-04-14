# ZFS install #
  * vajalikud pakid:
> urpmi fuse fuse-devel lib64aio lib64aio-devel zlib-devel scons
  * viimane zfs-fuse source http://zfs-fuse.net/
  * tar xvfj fail.tar.bz2; cd src
  * scons; scons install
  * Et zfs-fuse tuleks bootimise ajal üles:
> cp ../contrib/zfs-fuse.initd /etc/init.d/zfs-fuse
> chkconfig --add zfs-fuse
  * käivita zfs-fuse 'zfs-fuse'
  * poolide loomine

# Poolide loomine #
2 pooli 1 baasi jaoks 2 tilede jaoks (storage)

  * zpool create baas -m /storage /dev/sdc /dev/sdd
  * zfs set mountpoint=/storage/baas baas
  * zfs set dedup=off baas
  * zpool create media -m /storage raidz /dev/sde /dev/sdf /dev/sdg /dev/sdh -f
  * zfs set dedup=on media
  * zfs create media/tiles
  * zfs create media/mod\_tile
  * zfs create media/test
  * zfs create media/osmdata

Kasulikud käsud
  * zpool list
  * zfs get all
  * zfs list