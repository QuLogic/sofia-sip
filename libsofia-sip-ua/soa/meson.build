# meson.build for soa module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

soa_tag_ref_c = custom_target(
  input: 'soa_tag.c',
  output: 'soa_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'LIST=soa_tag_list', 'REF=@OUTPUT@', '@INPUT@'])

libsoa_SOURCES = [
  'soa.c',
  'soa_static.c',
  'soa_tag.c',
  soa_tag_ref_c
]

# ----------------------------------------------------------------------
# Build targets

libsoa = static_library(
  'soa',
  libsoa_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libsoa

# ----------------------------------------------------------------------
# Tests

# Needs s2check, which isn't built yet.
s2check_tests += {
  'test_soa': {
    'sources': files('test_soa.c'),
    'include_directories': toplevel_inc,
  },
}
