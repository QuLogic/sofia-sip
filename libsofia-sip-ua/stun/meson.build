# meson.build for stun module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

stun_tag_ref_c = custom_target(
  input: 'stun_tag.c',
  output: 'stun_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'LIST=stun_tag_list', 'REF=@OUTPUT@', '@INPUT@'])

libstun_SOURCES = [
  'stun.c',
  'stun_common.c',
  'stun_dns.c',
  'stun_mini.c',
  'stun_tag.c',
  stun_tag_ref_c,
  'stun_internal.h',
]

# ----------------------------------------------------------------------
# Build targets

libstun = static_library(
  'stun',
  libstun_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libstun

stunc = executable(
  'stunc',
  'stunc.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libstun, libsresolv, libsu],
  dependencies: [openssl_dep],
  install: true)

lookup_stun_server = executable(
  'lookup_stun_server',
  'lookup_stun_server.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libstun, libsresolv, libsu],
  dependencies: [openssl_dep])
