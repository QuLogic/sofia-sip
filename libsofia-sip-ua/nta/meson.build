# meson.build for nta module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

nta_tag_ref_c = custom_target(
  input: 'nta_tag.c',
  output: 'nta_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'LIST=nta_tag_list', 'REF=@OUTPUT@', '@INPUT@'])

libnta_SOURCES = [
  'nta.c',
  'nta_check.c',
  'nta_tag.c',
  nta_tag_ref_c,
  'nta_internal.h',
  'sl_utils_print.c',
  'sl_utils_log.c',
  'sl_read_payload.c',
]

# ----------------------------------------------------------------------
# Build targets

libnta = static_library(
  'nta',
  libnta_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libnta

# ----------------------------------------------------------------------
# Tests

if check_dep.found()
  # Needs s2check, which isn't built yet.
  s2check_tests += {
    'check_nta': {
      'sources': files(
        'check_nta.c',
        'check_nta.h',
        'check_nta_api.c',
        'check_nta_client.c',
      ),
      'include_directories': toplevel_inc,
      'dependencies': [openssl_dep, zlib_dep, check_dep],
    },
    'test_nta_api': {
      'sources': files('test_nta_api.c'),
      'include_directories': toplevel_inc,
      'dependencies': [openssl_dep, zlib_dep],
    },
    'test_nta': {
      'sources': files('test_nta.c'),
      'include_directories': toplevel_inc,
      'dependencies': [openssl_dep, zlib_dep],
    },
  }

  portbind = executable(
    'portbind',
    'portbind.c',
    include_directories: [toplevel_inc, libsofia_sip_ua_inc],
    link_with: [libnta, libsu])
endif
