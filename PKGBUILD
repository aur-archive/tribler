# Maintainer: RunningDroid <runningdroid AT zoho.com>
# Contributor: Alessio Bolognino <themolok at gmail dot com>
# Contributor: Simone Esposito <webmaster at freebnc dot net>
# Contributor: Jeremiah Dodds <jeremiah dot dodds at gmail dot com>

pkgname=tribler
pkgver=6.4.3
_mootools_ver=1.5.1
pkgrel=1
pkgdesc="A 4th generation file sharing system bittorrent client"
arch=('any')
url="http://www.tribler.org"
license=('LGPL2.1')
install=install
depends=('python2' 'wxpython2.8'
        'python2-m2crypto' 'python2-apsw'
        'python2-netifaces' 'libswift'
        'gconf' 'libtorrent-rasterbar'
        'python2-feedparser' 'python2-twisted'
        'python2-pyasn1' 'python2-requests'
        'python2-pillow' 'python2-gmpy2'
        'libsodium' 'python2-cryptography')
optdepends=('vlc: watch videos in tribler'
            'python2-cherrypy: use the tribler webUI'
            'desktop-file-utils')
source=("https://github.com/Tribler/tribler/releases/download/v${pkgver}/tribler_${pkgver}_all.deb"
        "mootools.js::http://mootools.net/download/get/mootools-core-${_mootools_ver}-full-nocompat.js"
        "https://explorercanvas.googlecode.com/files/excanvas_r3.zip"
        "imgs.tar.gz")
sha256sums=('80dc3e44adb0edc7a77ce85475a6bddfd693666afcaefa2b1dfbf9d6721be814'
            'ec7bc2fc49068bc2d77ea24345021caa224faf08dcd524afcb9e856f249c5b9f'
            '3e5a3d5a94b6703aedbf8502e9d0935c082f1a6785377139ad2fe2bc4d166c87'
            '9925e574f09593b02fab023ac2b51850fac26c8ed7f706b61e73475001ca0032')

build() {
  cd "$srcdir"

  msg2 "Extracting tribler"
  tar -xJf data.tar.xz
}

package() {
  cp -r "$srcdir/usr" "$pkgdir/"
  cd "$pkgdir"

  # replace pngs that have incorrect sRGB profiles
  # upstream issue #180
  for f in "$srcdir"/imgs/*; do
    install -m 644 "$f" "$pkgdir"/usr/share/tribler/Tribler/Main/vwxGUI/images/
  done

  msg2 "Changing python to python2"
  sed -i -e "s|^exec\ python|exec\ python2|g" -e "s/Ubuntu/Arch/" usr/bin/$pkgname
  # get everything in usr/share/tribler as well, just to be sure
  find . -iname '*.py' -exec sed -i -e "s|^#!/usr/bin/python|#!/usr/bin/python2|" -e "s|^#!/usr/bin/env\ python|#!/usr/bin/python2|" '{}' +

  # this is a (minor) bugfix
  sed -i -e "s|^#!\ /usr/bin/env\ python|#!/usr/bin/python2|" -e "s|^#!\ /usr/bin/python|#!/usr/bin/python2|" "usr/share/tribler/Tribler/Core/DecentralizedTracking/pymdht/run_pymdht_node.py" usr/share/tribler/Tribler/vlc.py

  # add pull request #1109
  sed -i -e 's/ | grep -v pgrep//' usr/share/tribler/Tribler/Utilities/SingleInstanceChecker.py

  msg2 "Making tribler work with wxpython2.8"
  # make Tribler use wxpython2.8
  find . -iname '*.py' -exec sed -i -e "s|^import wx|import wxversion\nwxversion.select('2.8')\nimport wx|" '{}' +

  # replace the symlink to the file (which doesn't exist on Arch) with the actual file
  install -m 644 "$srcdir"/mootools.js usr/share/tribler/Tribler/Main/webUI/static/mootools.js
  install -m 644 "$srcdir"/excanvas.compiled.js usr/share/tribler/Tribler/Main/webUI/static/excanvas.js

  # this file appears to be run by apt when python is updated, but we don't do that
  rm -rf usr/share/python

  # delete files for Debian/Ubuntu
  rm -rf usr/share/doc

  sed -i -re 's/^import Image/import PIL.Image/' usr/share/tribler/Tribler/Core/Video/VideoUtility.py
  sed -i -re 's/gmpy/gmpy2/' usr/share/tribler/Tribler/community/tunnel/crypto/cryptowrapper.py
}
