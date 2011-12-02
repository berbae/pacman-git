# Contributor: Bernard Baeyens (berbae) <berbae52 at sfr dot fr>

pkgname=('pacman-git' 'pacman-contrib-git')
pkgdesc="Build chain for package manager git version."
pkgver=20111202
pkgrel=1
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
makedepends=('git' 'asciidoc' 'curl>=7.19.4' 'gpgme' 'libarchive>=2.8.4')
checkdepends=('python2')

options=(!libtool)
source=(pacman.conf
        pacman.conf.x86_64
        makepkg.conf)
md5sums=('4ebee5e1da37a6afdf06c1bc55efbf92'
         '3e3342bd4abda20bb758595c1cbfb014'
         'db051afbd12993b7743ccd4d58668499')

# keep an upgrade path for older installations
PKGEXT='.pkg.tar.gz'

_gitroot="git://projects.archlinux.org/pacman.git"
_gitname="pacman"

build() {
  cd $srcdir
  msg "Connecting to the GIT repository..."

  if [[ -d $srcdir/$_gitname ]] ; then
      cd $_gitname
      git clean -qxf
      git pull origin
      msg "The local files are updated."
  else
      git clone $_gitroot
  fi

  msg "GIT checkout done"

  cd $srcdir/$_gitname

  ./autogen.sh
  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc
  make
}

#check() {
#  cd $srcdir/$_gitname
#  make check
#}

package_pacman-git() {
  pkgdesc="A library-based package manager with dependency support. git version."
  groups=('base')
  depends=('bash' 'glibc>=2.14' 'curl>=7.19.4' 'gpgme' 'libarchive>=2.8.4' 'pacman-mirrorlist')
  optdepends=('fakeroot: for makepkg usage as normal user')
  backup=(etc/pacman.conf etc/makepkg.conf)
  conflicts=('pacman')
  provides=('pacman')
  install=pacman.install

  cd $srcdir/$_gitname
  make DESTDIR=$pkgdir install

  # install Arch specific stuff
  mkdir -p $pkgdir/etc
  case "$CARCH" in
    i686)
      install -m644 $srcdir/pacman.conf $pkgdir/etc/pacman.conf
      mycarch="i686"
      mychost="i686-pc-linux-gnu"
      myflags="-march=i686 "
      ;;
    x86_64)
      install -m644 $srcdir/pacman.conf.x86_64 $pkgdir/etc/pacman.conf
      mycarch="x86_64"
      mychost="x86_64-unknown-linux-gnu"
      myflags="-march=x86-64 "
      ;;
  esac
  install -m644 $srcdir/makepkg.conf $pkgdir/etc/
  # set things correctly in the default conf file
  sed -i $pkgdir/etc/makepkg.conf \
    -e "s|@CARCH[@]|$mycarch|g" \
    -e "s|@CHOST[@]|$mychost|g" \
    -e "s|@CARCHFLAGS[@]|$myflags|g"

  # install completion files
  mkdir -p $pkgdir/etc/bash_completion.d/
  install -m644 contrib/bash_completion $pkgdir/etc/bash_completion.d/pacman
  mkdir -p $pkgdir/usr/share/zsh/site-functions/
  install -m644 contrib/zsh_completion $pkgdir/usr/share/zsh/site-functions/_pacman
}

package_pacman-contrib-git() {
  pkgdesc="Utilities for use with the pacman package manager"
  depends=('pacman')
  provides=('pacman-contrib')
  conflicts=('pacman-contrib')

  _scripts=(
    bacman
    paccache
    pacdiff
    paclist
    paclog-pkglist
    pacscripts
    pacsearch
  )

  cd $srcdir/$_gitname/contrib

  install -d $pkgdir/usr/bin $pkgdir/usr/share/vim/vimfiles/ftdetect

  install -t $pkgdir/usr/bin "${_scripts[@]}"

  install -Dm644 PKGBUILD.vim $pkgdir/usr/share/vim/vimfiles/syntax/PKGBUILD.vim
  echo "au BufNewFile,BufRead PKGBUILD set filetype=PKGBUILD" \
    > $pkgdir/usr/share/vim/vimfiles/ftdetect/PKGBUILD.vim
}


# vim: set ts=2 sw=2 et:
