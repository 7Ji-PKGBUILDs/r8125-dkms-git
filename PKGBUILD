# Maintainer: boogiepop <boogiepop@gmx.com>

_pkgname=r8125
_srcname=$_pkgname-dkms
pkgname=$_pkgname-dkms-git
pkgver=9.013.0213
pkgrel=1
pkgdesc="Kernel 5.10 modules for RealTek r8125 ethernet module"
arch=('any')
url="https://github.com/radxa-pkg/${_srcname}.git"
license=('GPL')
provides=(${pkgname}=${pkgver} ${_pkgname}-dkms=${pkgver})
source=(git+$url dkms.conf)
sha256sums=('SKIP'
            'SKIP')
depends=('dkms' 'python' 'bc')
_headers=('linux-aarch64-rockchip-bsp5.10-radxa-git-headers')
mekdepends=$_headers

_switchtag(){
  _tag=$(git tag -l --sort=creatordate | tail -1)
  git checkout $_tag --quiet
  git reset --hard --quiet
  printf $_tag
}

pkgver() {
  cd $_srcname
  _tag=$(_switchtag)
  printf $(echo $_tag | grep -o "[0-9.]*" | tr -d "\n")
}


build(){
  cd $_srcname
  _tag=$(_switchtag)

  cp $srcdir/dkms.conf _dkms.conf
  # Set name and version
  sed -e "s/@PKGNAME@/${_pkgname}/" \
      -e "s/@PKGVER@/${pkgver}/" \
      -i _dkms.conf
  ln -sf $(pwd)/src $srcdir/$_pkgname-$pkgver
}

check(){
  cd $_srcname
  _tag=$(_switchtag)

  for _kernel in ${_headers[@]}; do
    mkdir -p $srcdir/test_dkms
    rm -rf $srcdir/test_dkms/*
  
    echo "Test Building for $_kernel"
    _kernel_dir=$(pacman -Ql $_kernel | grep -o "/usr/lib/modules/.*/build" | head -1)
    _kernel_ver=$(basename $(pacman -Ql $_kernel | grep -o "/usr/lib/modules/.*/" | head -1))
    dkms build -c _dkms.conf --kernelsourcedir $_kernel_dir --dkmstree=$srcdir/test_dkms --sourcetree=$srcdir -k $_kernel_ver $_pkgname/$pkgver
  done
}

package() {
  cd $_srcname

  # Copy dkms.conf
  install -Dm644 _dkms.conf "${pkgdir}"/usr/src/${_pkgname}-${pkgver}/dkms.conf
  
  _tag=$(_switchtag)
  # Copy sources (including Makefile)
  cp -r src/* "${pkgdir}"/usr/src/${_pkgname}-${pkgver}/
}
