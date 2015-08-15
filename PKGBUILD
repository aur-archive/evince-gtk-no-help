# Contributor: Alexander Fehr <pizzapunk gmail com>
# Contributor: Auguste Pop <auguste [at] gmail [dot] com>
# Maintainer: Abby Pan <abbypan [at] gmail [dot] com>

pkgname=evince-gtk-no-help
pkgver=3.6.1
_pkgalias=evince
pkgrel=1
pkgdesc="A document viewer, built without GConf and GNOME keyring support"
arch=('i686' 'x86_64')
url="http://projects.gnome.org/evince/"
license=('GPL')
depends=('dconf' 'gtk3' 'libsm' 'poppler-glib' 'hicolor-icon-theme'
         'desktop-file-utils' 'gsettings-desktop-schemas')
makedepends=('intltool')
optdepends=('libspectre: PostScript support'
            'djvulibre: DJVU support'
            'texlive-bin: DVI support'
            'libgxps: XPS support'
            'gvfs: bookmark and annotation support')
provides=("$_pkgalias")
conflicts=("$_pkgalias")
install=$_pkgalias.install
source=("http://ftp.gnome.org/pub/GNOME/sources/evince/${pkgver%.*}/$_pkgalias-$pkgver.tar.xz")
md5sums=('e03d1158eeba2f5c693e1a1db58ed1ec')

_ods=()
_tmpfile="$pkgdir/.tmp"

add_ods_pc()
{
    pkg-config $1 && echo "$3" >> "$_tmpfile" || _ods=("${_ods[@]}" "$2")
}

add_ods_file()
{
    [[ -e "$1" ]] && echo "$3" >> "$_tmpfile" || _ods=("${_ods[@]}" "$2")
}

unset_optdepend()
{
    for _idx in ${!optdepends[@]}
    do
        if [[ "${optdepends[$_idx]}" =~ ^$1:* ]]
        then
            unset optdepends[$_idx]
            break
        fi
    done
}

build()
{
    cd "$srcdir/$_pkgalias-$pkgver"

    sed -i 's/gnome-icon-theme//' configure.ac configure
    sed -i '/NoDisplay/d' ./data/evince.desktop.in.in

    rm -rf "$_tmpfile"
    add_ods_pc libspectre '--disable-ps' libspectre
    add_ods_pc ddjvuapi '--disable-djvu' djvulibre
    add_ods_file '/usr/lib/libkpathsea.a' '--disable-dvi' texlive-bin
    add_ods_pc libgxps '--disable-xps' libgxps

    autoreconf --force --install
    ./configure \
        --prefix=/usr \
        --libexecdir=/usr/lib/$_pkgalias \
        --sysconfdir=/etc \
        --localstatedir=/var \
        --disable-maintainer-mode \
        --disable-schemas-compile \
        --disable-debug \
        --disable-tests \
        --disable-nautilus \
        --enable-previewer \
        --disable-introspection \
        --enable-t1lib \
        --enable-comics \
        --disable-scrollkeeper \
        --disable-help \
        --disable-gtk-doc \
        --without-keyring \
        --with-smclient=xsmp "${_ods[@]}"
    perl -i -lpe 's/ help / /g' Makefile
    make
}

package()
{
    cd $srcdir/$_pkgalias-$pkgver
    make DESTDIR=$pkgdir install

    while read _ch
    do
        if [[ 1 -gt 0 ]]; then depends=("${depends[@]}" "$_ch"); fi
        unset_optdepend "$_ch"
    done < "$_tmpfile"
    optdepends=("${optdepends[@]}")
    rm -rf "$_tmpfile"
}
