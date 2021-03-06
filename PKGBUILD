# Maintainer: Ross Clark <archiv8@artisteducator.com>
# Contributor: Ross Clark <archiv8@artisteducator.com>

_langname="nodejs"
_relname="lerna"
_repo="https://registry.npmjs.org"


# pkgbase=
pkgname="${_langname}-${_relname}"
pkgver=4.0.0
pkgrel=1
#epoch=
pkgdesc="A tool that assists in managing JavaScript projects with multiple packages."
arch=("any")
url="https://lerna.org"
license=("MIT")
# groups
depends=("nodejs")
# optdepends=()
makedepends=("npm" "jq")
# checkdepends=()
# provides=()
# conflicts=()
# replaces=()
# backup=()
# options=()
# install=
changelog="CHANGELOG.md"
source=(
"$_repo/$_relname/-/$_relname-$pkgver.tgz"
"CHANGELOG.md"
"README.md"
)
noextract=("$_relname-$pkgver.tgz")
# validpgpkeys=()
sha512sums=('0c3fe2d739eeadf3a63496f4381c3ae8d98daa233c905eae22bceb274c0613754d77378e873f738962d88916990c61b081b7233a8e9e21f731f4ee50ca7cfe92'
            '0514fafb7b8147c56186ebb471f0d2f40ea134ffa60b0450cd425ddb75ac8ca4cc94b71e6f67000164510a83ddbeaa002a73e6d6d61a5db677dcfcb6100cc1cd'
            '4250132299cc238781342d30d9b8658809b6935b49f689a8de772908a5c985de240aaaba6281d9e3c8fb0f02047d676d0f8276836bb1e4f7b5bf91db480b78dd')

package() {
  # Ensure cache is set when install is run in order to avoid littering user's home directory
printf "\e[1;32m==>\e[0m \e[38;5;248mBuilding nodejs package... \e[0m\n"
printf "    This may take some time depending on the size of the package and number of dependencies to download... \e[0m\n"
npm install --cache "$srcdir/npm-cache" -g --prefix "$pkgdir"/usr "$srcdir"/$_relname-$pkgver.tgz

# Fix incorrect user / group as highlighted by namcap
printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible ownership issues\e[0m\n"
while IFS= read -r fsitem; do
  chown -h root:root "$fsitem"
done <   <(find "$pkgdir" -not -group root -not -user root)

# Non-deterministic race in npm gives 777 permissions to random directories.
# See https://github.com/npm/npm/issues/9359 for details.
printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible permission issues on directories\e[0m\n"
find "$pkgdir/usr" -type d -exec chmod 755 '{}' +

# Remove references to $pkgdir
printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$pkgdir\e[0m\n"
find "$pkgdir" -type f -name package.json -print0 | xargs -0 sed -i "/_where/d"

# Remove references to $srcdir
printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$srcdir \e[0m\n"
local tmppackage="$(mktemp)"
local pkgjson="$pkgdir/usr/lib/node_modules/$_relname/package.json"
jq '.|=with_entries(select(.key|test("_.+")|not))' "$pkgjson" > "$tmppackage"
mv "$tmppackage" "$pkgjson"
chmod 644 "$pkgjson"

# Install license
install -dm755 "${pkgdir}/usr/share/licenses/${pkgname}"
ln -s ../../../lib/node_modules/eslint/LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

# Create Archiv8 documentation folder
install -dvm 755 "$pkgdir/usr/share/doc/$pkgname/packaging/"

# Install Archiv8 Documentation
install -Dm 644 "CHANGELOG.md" "$pkgdir/usr/share/doc/$pkgname/packaging/CHANGELOG.md"
install -Dm 644 "README.md" "$pkgdir/usr/share/doc/$pkgname/packaging/README.md"
}
