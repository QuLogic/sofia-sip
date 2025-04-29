# meson.build for http module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

AWK_HTTP_AWK = [AWK, '-f', MSG_PARSER_AWK, 'module=http']
SS_HTTP_H = files('sofia-sip/http.h')

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

http_tag_c = custom_target(
  input: 'http_tag.c.in',
  output: 'http_tag.c',
  command: [AWK_HTTP_AWK, 'PR=@OUTPUT@', 'TEMPLATE=@INPUT@', SS_HTTP_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir)

http_parser_table_c = custom_target(
  input: 'http_parser_table.c.in',
  output: 'http_parser_table.c',
  command: [
    AWK_HTTP_AWK, 'PT=@OUTPUT@', 'TEMPLATE=@INPUT@',
    'MC_HASH_SIZE=127', SS_HTTP_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir)

http_tag_ref_c = custom_target(
  input: http_tag_c,
  output: 'http_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'DLLREF=1', 'REF=@OUTPUT@', '@INPUT@'])

libhttp_SOURCES = [
  'http_parser.c',
  'http_header.c',
  'http_basic.c',
  'http_extra.c',
  'http_inlined.c',
  'http_status.c',
  'http_tag_class.c',
  http_protos_h,
  http_tag_h,
  http_tag_c,
  http_parser_table_c,
  http_tag_ref_c,
]

# ----------------------------------------------------------------------
# Build targets

libhttp = static_library(
  'http',
  libhttp_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libhttp

# ----------------------------------------------------------------------
# Tests

test_http = executable(
  'test_http',
  'test_http.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libhttp, libbnf, libmsg, liburl, libipt, libsu])
test('test_http', test_http, suite: 'http')
