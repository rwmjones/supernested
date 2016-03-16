dnl supernested configure.ac
dnl (C) Copyright 2014 Red Hat Inc.
dnl
dnl This program is free software; you can redistribute it and/or modify
dnl it under the terms of the GNU General Public License as published by
dnl the Free Software Foundation; either version 2 of the License, or
dnl (at your option) any later version.
dnl
dnl This program is distributed in the hope that it will be useful,
dnl but WITHOUT ANY WARRANTY; without even the implied warranty of
dnl MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
dnl GNU General Public License for more details.
dnl
dnl You should have received a copy of the GNU General Public License
dnl along with this program; if not, write to the Free Software
dnl Foundation, Inc., 675 Mass Ave, Cambridge, MA 02139, USA.
dnl
dnl Written by Richard W.M. Jones <rjones@redhat.com>

AC_INIT([supermin],[0.1])
AM_INIT_AUTOMAKE(foreign)

AC_PROG_INSTALL

AC_CANONICAL_HOST

# Define $(SED).
m4_ifdef([AC_PROG_SED],[
    AC_PROG_SED
],[
    dnl ... else hope for the best
    AC_SUBST([SED], "sed")
])

# Define $(AWK).
AC_PROG_AWK

dnl Check for supermin 5.
AC_PATH_PROG(SUPERMIN,[supermin],[no])

if test "x$SUPERMIN" = "xno" ; then
    AC_MSG_ERROR([supermin not found])
fi

if ! $SUPERMIN --version | grep -q 'supermin 5'; then
    AC_MSG_ERROR([incorrect supermin version, must be supermin 5])
fi

dnl Get the distro that supermin thinks it is using.
AC_MSG_CHECKING([for the distro])
DISTRO="$(
    $SUPERMIN --list-drivers |
    $AWK '$2 == "detected" { gsub ("/", "-", $1); print $1; exit }'
    )"
AC_MSG_RESULT([$DISTRO])
AC_SUBST([DISTRO])
if test -z "$DISTRO"; then
    AC_MSG_ERROR([cannot get supermin distro from '$SUPERMIN --list-drivers' output])
fi

dnl Check for a usable qemu KVM binary.
qemu_system="$(
    echo qemu-system-$host_cpu |
    $SED -e 's/-i@<:@456@:>@86/-i386/g' \
         -e 's/-arm.*/-arm/g'
    )"
qemu_choices="$qemu_system qemu-kvm kvm"
AC_PATH_PROGS([QEMU],[$qemu_choices],[no])

if test "x$QEMU" = "xno" ; then
    AC_MSG_ERROR([no qemu KVM binary found.  I looked for one of: $qemu_choices])
fi

dnl Check for guestfish.
AC_PATH_PROG(GUESTFISH,[guestfish],[no])

if test "x$GUESTFISH" = "xno" ; then
    AC_MSG_ERROR([guestfish not found])
fi

AC_CONFIG_FILES([build-supernested.sh], [chmod +x,-w build-supernested.sh])
AC_CONFIG_FILES([init], [chmod +x,-w init])
AC_CONFIG_FILES([run-supernested.sh], [chmod +x,-w run-supernested.sh])
AC_CONFIG_FILES([Makefile])
AC_OUTPUT
