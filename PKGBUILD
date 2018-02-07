# Maintainer: Eric Vidal <eric@obarun.org>
# Contributor: Danial Spruce <bit@teknik.io>
 
 
pkgname=openntpd
_pkgbase=openntpd-portable
pkgver=6.2p3
pkgrel=5
pkgdesc='Free, easy to use implementation of the Network Time Protocol.'
url='http://www.openntpd.org/'
arch=(x86_64)
license=(BSD)
depends=('openssl')
makedepends=('git' 'libtool' 'bison')
conflicts=('ntp')
backup=('etc/ntpd.conf')
#source=("ftp://ftp.openbsd.org/pub/OpenBSD/OpenNTPD/${pkgname}-$pkgver.tar.gz")
source=("openntpd-portable::git+https://github.com/openntpd-portable/openntpd-portable#commit=$_commit"
		"openntpd.sysusers")
_commit=19c9c6ff003bd91c9133997f310d1ccbbbc69aaa # tag 6.2p3
sha256sums=('SKIP'
            'bec9a1009d6c63a3959708a1a7e159411b698c3dcff0bc7e53949e1843a25005')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

prepare(){
  cd "$srcdir/${_pkgbase}"
	
  ./autogen.sh
  
  # fix /run path, the directory /run/openntpd must be created by the runit service
  sed -i 's:/var/run/ntpd:/run/openntpd/openntpd:g' src/ntpctl.8 src/ntpd.8
  sed -i 's:LOCALSTATEDIR "/run/ntpd:"/run/openntpd/openntpd:' src/ntpd.h
  
  # fix ntpd.drift path
  sed -i 's:/var/db/ntpd.drift:/var/lib/openntpd/db/ntpd.drift:g' src/ntpd.8
  #sed -i 's:"/db/ntpd.drift":"/db/ntpd.drift":' src/ntpd.h
  
  # fix ntp name
  # changed this variable or not seems to change nothing
  sed -i 's:"_ntp":"ntp":' src/ntpd.h
  


}

build() {
  cd "$srcdir/${_pkgbase}"
   
  ./configure \
    --prefix=/usr \
    --sysconfdir=/etc \
    --localstatedir=/var/lib/openntpd \
    --sbindir=/usr/bin \
    --with-privsep-user=ntp \
    --with-privsep-path=/var/empty/openntpd/ \
    --with-adjtimex
    
  make
}
 
package() {
  cd "$srcdir/${_pkgbase}"
 
  make DESTDIR="$pkgdir" install
  install -Dm644 "$srcdir/${_pkgbase}/COPYING" \
    "$pkgdir/usr/share/licenses/${pkgname}/COPYING"
  sed -i 's/\*/0.0.0.0/' "$pkgdir/etc/ntpd.conf"
  
  #create privsep directory
  install -dm 0755 "$pkgdir/var/empty/openntpd" 
  
  # this directory is define directly on /run, we don't need anymore.
  rm -r "$pkgdir/var/lib/openntpd/run"  

  install -Dm 0644 "${srcdir}/openntpd.sysusers" "${pkgdir}/usr/lib/sysusers.d/openntpd.conf"
}
 
# vim:set ts=2 sw=2 et:
