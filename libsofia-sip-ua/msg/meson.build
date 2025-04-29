# meson.build for msg module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

MSG_PARSER_AWK = files('msg_parser.awk')
AWK_MSG_AWK = [AWK, '-f', MSG_PARSER_AWK]
SS_MIME_H = files('sofia-sip/msg_mime.h')

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

msg_mime_table_c = custom_target(
  input: 'msg_mime_table.c.in',
  output: 'msg_mime_table.c',
  command: [
    AWK_MSG_AWK, 'module=msg_multipart',
    'tprefix=msg', 'prefix=mp', 'MC_HASH_SIZE=127', 'MC_SHORT_SIZE=26',
    'PT=@OUTPUT@', 'TEMPLATE=@INPUT@',
    SS_MIME_H],
  env: {'LC_ALL': 'C'})

libmsg_SOURCES = [
  'msg.c',
  'msg_tag.c',
  'msg_inlined.c',
  'msg_mime.c',
  msg_mime_table_c,
  'msg_header_copy.c',
  'msg_header_make.c',
  'msg_parser.c',
  'msg_mclass.c',
  'msg_parser_util.c',
  'msg_basic.c',
  'msg_generic.c',
  'msg_date.c',
  'msg_auth.c',
  msg_protos_h,
  msg_mime_protos_h,
]

TEST_CLASS_H = files('test_class.h')

test_protos_h = custom_target(
  input: 'test_protos.h.in',
  output: 'test_protos.h',
  command: [
    AWK_MSG_AWK, 'module=msg_test', 'NO_MIDDLE=1', 'NO_LAST=1',
    'PR=@OUTPUT@', 'TEMPLATE=@INPUT@',
    TEST_CLASS_H],
  env: {'LC_ALL': 'C'})

test_table_c = custom_target(
  input: 'test_table.c.in',
  output: 'test_table.c',
  command: [
    AWK_MSG_AWK, 'module=msg_test', 'prefix=msg',
    'MC_HASH_SIZE=127', 'multipart=msg_multipart',
    'PT=@OUTPUT@', 'TEMPLATE=@INPUT@',
    TEST_CLASS_H],
  env: {'LC_ALL': 'C'})

libtest_msg_SOURCES = [
  'test_class.c',
  'test_class.h',
  test_table_c,
  'test_inlined.c',
  test_protos_h,
]

# ----------------------------------------------------------------------
# Build targets

libmsg = static_library(
  'msg',
  libmsg_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libtest_msg = static_library(
  'test_msg',
  libtest_msg_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += [libmsg, libtest_msg]

# ----------------------------------------------------------------------
# Tests

test_msg = executable(
  'test_msg',
  'test_msg.c',
  'test_class.c',
  'test_class.h',
  test_table_c,
  'test_inlined.c',
  test_protos_h,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libmsg, libbnf, liburl, libipt, libsu],
  dependencies: [check_dep])
test('test_msg', test_msg, suite: 'msg')
