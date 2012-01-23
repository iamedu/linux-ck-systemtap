# Contributor: iamedu <iamedu at gmail dot com>
# Contributor: graysky <graysky AT archlinux DOT us>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

### PATCH AND BUILD OPTIONS
_makenconfig="n"	# tweak kernel options prior to a build via nconfig
_localmodcfg="n"	# compile ONLY probed modules
_use_current="n"	# use the current kernel's .config file
_BFQ_enable_="n"	# enable BFQ as the default I/O scheduler

### DOCS
# READ THIS BEFORE INSTALLING!!!!
# To build the kernel with debuginfo you need quite a bit of space, my build directory is about 3.6 GB
# the kernel also ends up as quite a big kernel so I recommend doing two things:
# 
# Enabling localmodcfg and if you use yaourt use the --tmp flag or you could end up with
# your /tmp partition full ...
# That's it if you find any bug email it to me (iamedu)
#
# DETAILS FOR _localmodcfg="y"
# As of mainline 2.6.32, running with this option will only build the modules that you currently have
# probed in your system VASTLY reducing the number of modules built and the build time to do it.
#
# WARNING - make CERTAIN that all modules are modprobed BEFORE you begin making the pkg!
#
# To keep track of which modules are needed for your specific system/hardware, give my module_db script
# a try: http://aur.archlinux.org/packages.php?ID=41689  Note that if you use my script, this PKGBUILD 
# will auto run the 'sudo modprobed_db reload' for you to probe all the modules you have logged!
#
# More at this wiki page ---> https://wiki.archlinux.org/index.php/Modprobed_db

# DETAILS FOR _use_current="y"
# Enabling this option will use the .config of the RUNNING kernel rather than the ARCH defaults.
# Useful when the package gets updated and you already went through the trouble of customizing your
# config options.  NOT recommended when a new kernel is released, but again, convenient for package bumps.

# DETAILS FOR _BFQ_enable="y"
# Alternative I/O scheduler by Paolo.  For more, see: http://algo.ing.unimo.it/people/paolo/disk_sched/

pkgname=linux-ck-systemtap
true && pkgname=(linux-ck-systemtap linux-ck-systemtap-headers)
_kernelname=-ck
_basekernel=3.2
pkgver=${_basekernel}.1
pkgrel=3
arch=('i686' 'x86_64')
url="http://iamedu.wordpress.com/2012/01/23/arch-linux-and-systemtap/"
license=('GPL2')
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_bfqpath="http://algo.ing.unimo.it/people/paolo/disk_sched/patches/3.2.0-v3r2"
source=("http://www.kernel.org/pub/linux/kernel/v3.x/linux-3.2.tar.xz"
"http://www.kernel.org/pub/linux/kernel/v3.x/patch-${pkgver}.gz"
"http://ck.kolivas.org/patches/3.0/3.2/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.bz2"
'config' 'config.x86_64' 'change-default-console-loglevel.patch' 'i915-fix-ghost-tv-output.patch'
'i915-gpu-finish.patch' 'linux-ck.install' 'linux-ck.preset' 'utrace-3.2.patch.bz2'
"${_bfqpath}/0001-block-prepare-I-O-context-code-for-BFQ-v3r2-for-3.2.patch"
"${_bfqpath}/0002-block-cgroups-kconfig-build-bits-for-BFQ-v3r2-3.2.patch"
"${_bfqpath}/0003-block-introduce-the-BFQ-v3r2-I-O-sched-for-3.2.patch")
sha256sums=('dd96ed02b53fb5d57762e4b1f573460909de472ca588f81ec6660e4a172e7ba7'
            '20f633517dc186157618762338a05927f539dd7eba85a6c0a02635d961637ec0'
            '81aa6ee7b19b70a01f751bd26b79252d43457e3fda57bd35e125b2a20a7115cd'
            '50c2a787acaeccb48d84e4325b325b24942b5d525fa64b266752910cbb197865'
            'a33f764cc88f9f6e97e8e4fea2f5b3776e5c546a575292d1e911fde21183291b'
            'b9d79ca33b0b51ff4f6976b7cd6dbb0b624ebf4fbf440222217f8ffc50445de4'
            '9ccadbe3eb30bb283af3eb869c3a4bdb764628854811cc616a2e02e9ef398705'
            '177f1d6a03a1d5e7a78e8687f223d0a675c43430b9e5dd09e3a038a8b7d2da30'
            '0ad48d4f77ed70bef3227c7293fe6513cf1840f710020bfabbac3d4bd0cb2589'
            'c2cf8cc2600502de348f3dc3aae9a3bde5486759db15cb8a93df7aa35bd6e7da'
            '7da2b684fd3d9ebcb9a2523c652b2ae89f46e5904301fb9baf43caf3a1225633'
            '4167d857d2a6348eac9828a177bbb0e597d49fa4e4a1bf61c5933eb5e6b94d9a'
            'ced0347b5b49cac8ff9165f79bda27d0cf74e5f902381de1de32288c07524037'
            '2a1aa22286a49617ff722cc5af81af206cdaf4de86a120f11c12c1e9cada71cd')

build() {
	cd "${srcdir}/linux-${_basekernel}"

	# add upstream patch
	patch -p1 -i "${srcdir}/patch-${pkgver}"

	# add latest fixes from stable queue, if needed
	# http://git.kernel.org/?p=linux/kernel/git/stable/stable-queue.git

	# fix FS#27883
  # drm/i915: Only clear the GPU domains upon a successful finish
  patch -Np1 -i "${srcdir}/i915-gpu-finish.patch"

  # Some chips detect a ghost TV output
	# mailing list discussion: http://lists.freedesktop.org/archives/intel-gfx/2011-April/010371.html
	# Arch Linux bug report: FS#19234
	#
	# It is unclear why this patch wasn't merged upstream, it was accepted,
	# then dropped because the reasoning was unclear. However, it is clearly
	# needed.
	patch -Np1 -i "${srcdir}/i915-fix-ghost-tv-output.patch"

	# set DEFAULT_CONSOLE_LOGLEVEL to 4 (same value as the 'quiet' kernel param)
	# remove this when a Kconfig knob is made available by upstream
	# (relevant patch sent upstream: https://lkml.org/lkml/2011/7/26/227)
	patch -Np1 -i "${srcdir}/change-default-console-loglevel.patch"

	### Patch source with ck patchset with BFS
	# Fix double name in EXTRAVERSION
	sed -i -re "s/^(.EXTRAVERSION).*$/\1 = /" "${srcdir}/${_ckpatchname}"
	msg "Patching source with ck1 including bfs v0.416"
	patch -Np1 -i "${srcdir}/${_ckpatchname}"

	### Patch with BFQ IO Scheduler
	msg "Patching source with BFQ patches"
	for p in $(ls ${srcdir}/000*.patch); do
		patch -Np1 -i $p
	done

        ### Patch source with utrace
        msg "Patching source with utrace"
        patch -Np1 -i "${srcdir}/utrace-3.2.patch"

	### Clean tree and copy ARCH config over
	msg "Running make mrproper to clean source tree"
	make mrproper

	if [ "${CARCH}" = "x86_64" ]; then
		cat "${srcdir}/config.x86_64" > ./.config
	else
		cat "${srcdir}/config" > ./.config
	fi

	### Optionally use running kernel's config
	# code originally by nous; http://aur.archlinux.org/packages.php?ID=40191
	if [ $_use_current = "y" ]; then
		if [[ -s /proc/config.gz ]]; then
			msg "Extracting config from /proc/config.gz..."
			# modprobe configs
			zcat /proc/config.gz > ./.config
		else
			warning "You kernel was not compiled with IKCONFIG_PROC!"
			warning "You cannot read the current config!"
			warning "Aborting!"
			exit
		fi
	fi

	if [ "${_kernelname}" != "" ]; then
		sed -i "s|CONFIG_LOCALVERSION=.*|CONFIG_LOCALVERSION=\"${_kernelname}\"|g" ./.config
	fi

	### BFQ to be compiled in but not enabled
	sed -i -e s'/CONFIG_CFQ_GROUP_IOSCHED=y/CONFIG_CFQ_GROUP_IOSCHED=y\nCONFIG_IOSCHED_BFQ=y\nCONFIG_CGROUP_BFQIO=y/' \
		-i -e s'/CONFIG_DEFAULT_CFQ=y/CONFIG_DEFAULT_CFQ=y\n# CONFIG_DEFAULT_BFQ is not set/' ./.config

	### Optionally enable BFQ as the default io scheduler
	if [ $_BFQ_enable_ = "y" ]; then
		sed -i -e '/CONFIG_DEFAULT_IOSCHED/ s,cfq,bfq,' \
	 -i -e s'/CONFIG_DEFAULT_CFQ=y/# CONFIG_DEFAULT_CFQ is not set\nCONFIG_DEFAULT_BFQ=y/' ./.config
	fi

	# set extraversion to pkgrel
	sed -ri "s|^(EXTRAVERSION =).*|\1 -${pkgrel}|" Makefile

	# get kernel version
	msg "Running make prepare for you to enable patched options of your choosing"
	make prepare

	### Optionally load needed modules for the make localmodconfig
	# See http://aur.archlinux.org/packages.php?ID=41689
	if [ $_localmodcfg = "y" ]; then
		msg "If you have modprobe_db installed, running it in recall mode now"
		if [ -e /usr/bin/modprobed_db ]; then
			[[ ! -x /usr/bin/sudo ]] && echo "Cannot call modprobe with sudo.  Install via pacman -S sudo and configure to work with this user." && exit 1
			sudo /usr/bin/modprobed_db recall
		fi
		msg "Running Steven Rostedt's make localmodconfig now"
		make localmodconfig
	fi

	if [ $_makenconfig = "y" ]; then
		msg "Running make nconfig"
		make nconfig
	fi

	msg "Running make bzImage and modules"
	make ${MAKEFLAGS} bzImage modules
}

package_linux-ck-systemtap() {
_Kpkgdesc='Linux Kernel and modules with the ck1 patchset featuring Brain Fuck Scheduler v0.416.'
pkgdesc="${_Kpkgdesc}"
depends=('coreutils' 'linux-firmware' 'module-init-tools>=3.16' 'mkinitcpio>=0.7')
optdepends=('crda: to set the correct wireless channels of your country' 'lirc-ck: Linux Infrared Remote Control kernel modules for linux-ck' 'nvidia-ck: nVidia drivers for linux-ck' 'nvidia-beta-ck: nVidia beta drivers for linux-ck' 'modprobed_db: Keeps track of EVERY kernel module that has ever been probed - useful for those of us who make localmodconfig')
provides=("linux-ck-systemtap=${pkgver}" "linux-ck=${pkgver}")
conflicts=('kernel26-ck' 'linux-ck')
replaces=('kernel26-ck' 'linux-ck')
backup=("etc/mkinitcpio.d/linux-ck.preset")
install=linux-ck.install
#groups=('ck-generic')

cd "${srcdir}/linux-${_basekernel}"

KARCH=x86

# get kernel version
_kernver="$(make kernelrelease)"

mkdir -p "${pkgdir}"/{lib/modules,lib/firmware,boot}
make INSTALL_MOD_PATH="${pkgdir}" modules_install
cp arch/$KARCH/boot/bzImage "${pkgdir}/boot/vmlinuz-linux-ck"

# add vmlinux
install -D -m644 vmlinux "${pkgdir}/usr/src/linux-${_kernver}/vmlinux"

# install fallback mkinitcpio.conf file and preset file for kernel
install -D -m644 "${srcdir}/linux-ck.preset" "${pkgdir}/etc/mkinitcpio.d/linux-ck.preset"

# set correct depmod command for install
sed \
	-e  "s/KERNEL_NAME=.*/KERNEL_NAME=-ck/g" \
	-e  "s/KERNEL_VERSION=.*/KERNEL_VERSION=${_kernver}/g" \
	-i "${startdir}/linux-ck.install"
sed \
	-e "s|ALL_kver=.*|ALL_kver=\"/boot/vmlinuz-linux-ck\"|g" \
	-e "s|default_image=.*|default_image=\"/boot/initramfs-linux-ck.img\"|g" \
	-e "s|fallback_image=.*|fallback_image=\"/boot/initramfs-linux-ck-fallback.img\"|g" \
	-i "${pkgdir}/etc/mkinitcpio.d/linux-ck.preset"

# remove build and source links
rm -f "${pkgdir}"/lib/modules/${_kernver}/{source,build}
# remove the firmware
rm -rf "${pkgdir}/lib/firmware"
# gzip -9 all modules to save 100MB of space
find "${pkgdir}" -name '*.ko' -exec gzip -9 {} \;
# make room for external modules
ln -s "../extramodules-${_basekernel}${_kernelname:ck}" "${pkgdir}/lib/modules/${_kernver}/extramodules"
# add real version for building modules and running depmod from post_install/upgrade
mkdir -p "${pkgdir}/lib/modules/extramodules-${_basekernel}${_kernelname:ck}"
echo "${_kernver}" > "${pkgdir}/lib/modules/extramodules-${_basekernel}${_kernelname:ck}/version"
}

package_linux-ck-systemtap-headers() {
_Hpkgdesc='Header files and scripts to build modules for linux-ck-systemtap.'
pkgdesc="${_Hpkgdesc}"
provides=("linux-ck-systemtap-headers=${pkgver}" "linux-ck-headers=${pkgver}")
conflicts=('kernel26-ck-headers' 'linux-ck-headers')
replaces=('kernel26-ck-headers' 'linux-ck-headers')
#groups=('ck-generic')

mkdir -p "${pkgdir}/lib/modules/${_kernver}"

cd "${pkgdir}/lib/modules/${_kernver}"
ln -sf ../../../usr/src/linux-${_kernver} build

cd "${srcdir}/linux-${_basekernel}"
install -D -m644 Makefile \
	"${pkgdir}/usr/src/linux-${_kernver}/Makefile"
install -D -m644 kernel/Makefile \
	"${pkgdir}/usr/src/linux-${_kernver}/kernel/Makefile"
install -D -m644 .config \
	"${pkgdir}/usr/src/linux-${_kernver}/.config"

mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/include"

for i in acpi asm-generic config crypto drm generated linux math-emu \
	media net pcmcia scsi sound trace video xen; do
cp -a include/${i} "${pkgdir}/usr/src/linux-${_kernver}/include/"
	done

	# copy arch includes for external modules
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/arch/x86"
	cp -a arch/x86/include "${pkgdir}/usr/src/linux-${_kernver}/arch/x86/"

	# copy files necessary for later builds, like nvidia and vmware
	cp Module.symvers "${pkgdir}/usr/src/linux-${_kernver}"
	cp -a scripts "${pkgdir}/usr/src/linux-${_kernver}"

	# fix permissions on scripts dir
	chmod og-w -R "${pkgdir}/usr/src/linux-${_kernver}/scripts"
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/.tmp_versions"

	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/arch/${KARCH}/kernel"

	cp arch/${KARCH}/Makefile "${pkgdir}/usr/src/linux-${_kernver}/arch/${KARCH}/"

	if [ "${CARCH}" = "i686" ]; then
		cp arch/${KARCH}/Makefile_32.cpu "${pkgdir}/usr/src/linux-${_kernver}/arch/${KARCH}/"
	fi

	cp arch/${KARCH}/kernel/asm-offsets.s "${pkgdir}/usr/src/linux-${_kernver}/arch/${KARCH}/kernel/"

	# add headers for lirc package
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/video"

	cp drivers/media/video/*.h  "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/video/"

	for i in bt8xx cpia2 cx25840 cx88 em28xx et61x251 pwc saa7134 sn9c102; do
		mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/video/${i}"
		cp -a drivers/media/video/${i}/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/video/${i}"
	done

	# add docbook makefile
	install -D -m644 Documentation/DocBook/Makefile \
		"${pkgdir}/usr/src/linux-${_kernver}/Documentation/DocBook/Makefile"

	# add dm headers
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/md"
	cp drivers/md/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/md"

	# add inotify.h
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/include/linux"
	cp include/linux/inotify.h "${pkgdir}/usr/src/linux-${_kernver}/include/linux/"

	# add wireless headers
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/net/mac80211/"
	cp net/mac80211/*.h "${pkgdir}/usr/src/linux-${_kernver}/net/mac80211/"

	# add dvb headers for external modules
	# in reference to:
	# http://bugs.archlinux.org/task/9912
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/dvb-core"
	cp drivers/media/dvb/dvb-core/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/dvb-core/"
	# and...
	# http://bugs.archlinux.org/task/11194
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/include/config/dvb/"
	[[ -e include/config/dvb/ ]] && cp include/config/dvb/*.h "${pkgdir}/usr/src/linux-${_kernver}/include/config/dvb/" 

	# add dvb headers for http://mcentral.de/hg/~mrec/em28xx-new
	# in reference to:
	# http://bugs.archlinux.org/task/13146
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/frontends/"
	cp drivers/media/dvb/frontends/lgdt330x.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/frontends/"
	cp drivers/media/video/msp3400-driver.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/frontends/"

	# add dvb headers
	# in reference to:
	# http://bugs.archlinux.org/task/20402
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/dvb-usb"
	cp drivers/media/dvb/dvb-usb/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/dvb-usb/"
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/frontends"
	cp drivers/media/dvb/frontends/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb/frontends/"
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/common/tuners"
	cp drivers/media/common/tuners/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/common/tuners/"

	# add xfs and shmem for aufs building
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/fs/xfs"
	mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/mm"
	cp fs/xfs/xfs_sb.h "${pkgdir}/usr/src/linux-${_kernver}/fs/xfs/xfs_sb.h"

	# copy in Kconfig files
	for i in `find . -name "Kconfig*"`; do
		mkdir -p "${pkgdir}"/usr/src/linux-${_kernver}/`echo ${i} | sed 's|/Kconfig.*||'`
		cp ${i} "${pkgdir}/usr/src/linux-${_kernver}/${i}"
	done

	chown -R root.root "${pkgdir}/usr/src/linux-${_kernver}"
	find "${pkgdir}/usr/src/linux-${_kernver}" -type d -exec chmod 755 {} \;

	# strip scripts directory
	find "${pkgdir}/usr/src/linux-${_kernver}/scripts" -type f -perm -u+w 2>/dev/null | while read binary ; do
	case "$(file -bi "${binary}")" in
		*application/x-sharedlib*) # Libraries (.so)
			/usr/bin/strip ${STRIP_SHARED} "${binary}";;
		*application/x-archive*) # Libraries (.a)
			/usr/bin/strip ${STRIP_STATIC} "${binary}";;
		*application/x-executable*) # Binaries
			/usr/bin/strip ${STRIP_BINARIES} "${binary}";;
	esac
done

# remove unneeded architectures
rm -rf "${pkgdir}"/usr/src/linux-${_kernver}/arch/{alpha,arm,arm26,avr32,blackfin,cris,frv,h8300,ia64,m32r,m68k,m68knommu,mips,microblaze,mn10300,parisc,powerpc,ppc,s390,sh,sh64,sparc,sparc64,um,v850,xtensa}
}
# Global pkgdesc and depends are here so that they will be picked up by AUR
pkgdesc='Linux Kernel and modules with the ck1 patchset featuring Brain Fuck Scheduler v0.416, kernel debug info is enabled, and UTRACE is baked in for userland support so we have a systemtap friendly kernel :D. THE 32 BIT KERNEL HAS NOT BEEN TESTED, IF YOU WANT TO TEST BE MY GUEST, JUST REMEMBER I WARNED YOU, AND PLEASE TELL ME IF SOMETHING GOES WRONG'
