# meson.build for sofia-sip/libsofia-sip-ua
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# note: order does matter in the subdir list
dirs = ['su', 'features', 'bnf', 'sresolv', 'ipt', 'sdp', 'url', 'msg', 'sip']
if HAVE_NTH
  dirs += ['http']
endif
if HAVE_STUN
  dirs += ['stun']
endif
dirs += ['soa', 'tport', 'nta']
if HAVE_NTH
  dirs += ['nth']
endif
dirs += ['nea', 'iptsec', 'nua']

# Every directory needs to see each other as they might have shared headers.
libsofia_sip_ua_inc = []
foreach dir : dirs
  libsofia_sip_ua_inc += include_directories(dir)
endforeach
libsofia_sip_ua_lib_deps = []
foreach dir : dirs
  subdir(dir)
endforeach

libsofia_sip_ua_lib = library('sofia-sip-ua',
  link_whole: libsofia_sip_ua_lib_deps,
  # set the libtool version info version:revision:age for libsofia-sip-ua
  # - soname to 'libsofia-sip-ua.so.(CUR-AGE)'
  version: f'@LIBVER_SOFIA_SIP_UA_SOVER@.@LIBVER_SOFIA_SIP_UA_AGE@.@LIBVER_SOFIA_SIP_UA_REV@',
  dependencies: [openssl_dep, zlib_dep],
  install: true,
)

libsofia_sip_ua_dep = declare_dependency(
  include_directories: libsofia_sip_ua_inc,
  link_with: libsofia_sip_ua_lib,
)

meson.override_dependency('sofia-sip-ua', libsofia_sip_ua_dep)
