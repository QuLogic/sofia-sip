# meson.build for sip module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

AWK_SIP_AWK = [AWK, '-f', MSG_PARSER_AWK, 'module=sip']
SS_SIP_H = files('sofia-sip/sip.h')
EXTRA = files('sip_extra_headers.txt')

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

sip_tag_c = custom_target(
  input: 'sip_tag.c.in',
  output: 'sip_tag.c',
  command: [
    AWK_SIP_AWK, 'PR=@OUTPUT@', 'TEMPLATE=@INPUT@',
    SS_SIP_H, EXTRA],
  env: {'LC_ALL': 'C'})

# Note: sip_bad_mask is used by nta to weed out bad messages
sip_parser_table_c = custom_target(
  input: ['sip_parser_table.c.in', 'sip_bad_mask'],
  output: 'sip_parser_table.c',
  command: [
    AWK_SIP_AWK, 'PT=@OUTPUT@', 'TEMPLATE=@INPUT0@',
    'FLAGFILE=@INPUT1@',
    'MC_HASH_SIZE=127', 'MC_SHORT_SIZE=26',
    SS_SIP_H, EXTRA],
  env: {'LC_ALL': 'C'})

sip_tag_ref_c = custom_target(
  input: sip_tag_c,
  output: 'sip_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'REF=@OUTPUT@', '@INPUT@'])

libsip_SOURCES = [
  'sip_parser.c',
  'sip_header.c',
  'sip_util.c',
  'sip_pref_util.c',
  'sip_basic.c',
  'sip_extra.c',
  'sip_feature.c',
  'sip_mime.c',
  'sip_security.c',
  'sip_event.c',
  'sip_prack.c',
  'sip_refer.c',
  'sip_session.c',
  'sip_caller_prefs.c',
  'sip_reason.c',
  'sip_status.c',
  'sip_time.c',
  'sip_tag_class.c',
  'sip_inlined.c',
  sip_hclasses_h,
  sip_protos_h,
  sip_tag_h,
  sip_extra_h,
  sip_tag_c,
  sip_parser_table_c,
  sip_tag_ref_c,
]

# ----------------------------------------------------------------------
# Build targets

libsip = static_library(
  'sip',
  libsip_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libsip

validator = executable(
  'validator',
  'validator.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libsip, libmsg, libbnf, liburl, libipt, libsu])

# ----------------------------------------------------------------------
# Tests

torture_sip = executable(
  'torture_sip',
  'torture_sip.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libsip, libmsg, libbnf, liburl, libipt, libsu])
test('torture_sip', torture_sip, suite: ['sip', 'torture'])

test_sip_msg = executable(
  'test_sip_msg',
  'test_sip_msg.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libsip, libmsg, libbnf, liburl, libipt, libsu])
test('test_sip_msg', find_program('run_test_sip_msg'),
     args: test_sip_msg,
     env: {'srcdir': meson.current_source_dir()},
     suite: 'sip')

test_date = executable(
  'test_date',
  'test_date.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libsip, libmsg, libbnf, liburl, libipt, libsu])
test('test_date', test_date, args: 'Sun, 18 Mar 2001 23:01:00 GMT',
     suite: 'sip')
