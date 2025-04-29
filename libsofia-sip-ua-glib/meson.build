# meson.build for sofia-sip/libsofia-sip-ua-glib
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

dirs = ['su-glib']

# Every directory needs to see each other as they might have shared headers.
libsofia_sip_ua_glib_inc = []
foreach dir : dirs
  libsofia_sip_ua_glib_inc += include_directories(dir)
endforeach
libsofia_sip_ua_glib_lib_deps = []
foreach dir : dirs
  subdir(dir)
endforeach

libsofia_sip_ua_glib_lib = library(
  'sofia-sip-ua-glib',
  link_whole: libsofia_sip_ua_glib_lib_deps,
  # set the libtool version info version:revision:age for libsofia-sip-ua-glib
  # - soname to 'libsofia-sip-ua-glib.so.(CUR-AGE)'
  version: f'@LIBVER_SOFIA_SIP_UA_GLIB_SOVER@.@LIBVER_SOFIA_SIP_UA_GLIB_AGE@.@LIBVER_SOFIA_SIP_UA_GLIB_REV@',
  dependencies: [openssl_dep, zlib_dep],
  install: true,
)

libsofia_sip_ua_glib_dep = declare_dependency(
  include_directories: libsofia_sip_ua_glib_inc,
  link_with: libsofia_sip_ua_glib_lib,
)

meson.override_dependency('sofia-sip-ua-glib', libsofia_sip_ua_glib_dep)
