# meson.build for nua module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

nua_tag_ref_c = custom_target(
  input: 'nua_tag.c',
  output: 'nua_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'LIST=nua_tag_list', 'REF=@OUTPUT@', '@INPUT@'])

libnua_SOURCES = [
  'nua.c',
  'nua_stack.h',
  'nua_common.c',
  'nua_stack.c',
  'nua_server.h',
  'nua_server.c',
  'nua_client.h',
  'nua_client.c',
  'nua_extension.c',
  'nua_types.h',
  'nua_dialog.c',
  'nua_dialog.h',
  'outbound.c',
  'outbound.h',
  'nua_params.c',
  'nua_params.h',
  'nua_register.c',
  'nua_registrar.c',
  'nua_session.c',
  'nua_options.c',
  'nua_message.c',
  'nua_publish.c',
  'nua_subnotref.c',
  'nua_notifier.c',
  'nua_event_server.c',
  'nua_tag.c',
  nua_tag_ref_c,
]

# ----------------------------------------------------------------------
# Build targets

libnua = static_library(
  'nua',
  libnua_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libnua

# ----------------------------------------------------------------------
# Tests

# Needs s2check, which isn't built yet.
s2check_tests += {
  'check_nua': {
    'sources': files(
      'check_etsi.c',
      'check_nua.c',
      'check_nua.h',
      'check_register.c',
      'check_session.c',
      'check_simple.c',
    ),
    'include_directories': toplevel_inc,
    'dependencies': [openssl_dep, zlib_dep, check_dep],
  },
}
