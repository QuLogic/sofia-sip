# meson.build for nea module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

nea_tag_ref_c = custom_target(
  input: 'nea_tag.c',
  output: 'nea_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'REF=@OUTPUT@', '@INPUT@'])

libnea_SOURCES = [
  'nea.c',
  'nea_event.c',
  'nea_server.c',
  'nea_debug.h',
  'nea_debug.c',
  'nea_tag.c',
  nea_tag_ref_c
]

# ----------------------------------------------------------------------
# Build targets

libnea = static_library(
  'nea',
  libnea_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libnea
