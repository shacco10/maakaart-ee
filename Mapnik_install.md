# Intall #
> _Mandriva 2010.1 -e jaoks. 2011.0 -s urpmi mapnik mapnik-devel_
  * Mapniku sorts
```
 svn co http://svn.mapnik.org/tags/release-0.7.1 mapnik
```
  * Vajalikud pakid
```
 urpmi lib64boost_system1.44.0 lib64boost_thread1.44.0 lib64boost_filesystem1.44.0
 lib64boost_regex1.44.0 lib64boost_iostreams1.44.0 lib64boost_python1.44.0 
lib64proj-devel lib64icu-devel lib64png-devel lib64jpeg-devel lib64tiff-devel 
lib64zlib-devel lib64freetype2-devel lib64xml2-devel lib64cairomm1.0-devel 
python-cairo-devel lib64pq++-devel lib64gdal-devel lib64curl-devel
```
  * Build
```
 python scons/scons.py -Q PREFIX=/usr
```
  * Install
```
 python scons/scons.py install
```