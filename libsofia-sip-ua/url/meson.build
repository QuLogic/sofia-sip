# meson.build for url module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

url_tag_ref_c = custom_target(
  input: 'url_tag.c',
  output: 'url_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'DLLREF=1', 'REF=@OUTPUT@', '@INPUT@'])

liburl_SOURCES = [
  'url.c',
  'url_tag.c',
  url_tag_ref_c,
]

# ----------------------------------------------------------------------
# Build targets

liburl = static_library(
  'url',
  liburl_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += liburl

# ----------------------------------------------------------------------
# Tests

torture_url = executable(
  'torture_url', 'torture_url.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [liburl, libbnf, libsu])
test('torture_url', torture_url, suite: ['url', 'torture'])
