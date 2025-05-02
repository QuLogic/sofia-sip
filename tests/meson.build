# meson.build for Sofia SIP tests
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.


libtestnua_SOURCES = [
  'test_nua.h',
  'test_ops.c',
  'test_init.c',
  'test_nua_api.c',
  'test_nua_params.c',
  'test_register.c',
  'test_basic_call.c',
  'test_offer_answer.c',
  'test_call_reject.c',
  'test_cancel_bye.c',
  'test_call_hold.c',
  'test_session_timer.c',
  'test_refer.c',
  'test_100rel.c',
  'test_simple.c',
  'test_sip_events.c',
  'test_extension.c',
]

libtestproxy_SOURCES = [
  'test_proxy.h',
  'test_proxy.c',
]

libtestnat_SOURCES = [
  'test_nat.h',
  'test_nat.c',
  'test_nat_tags.c',
]

libtestnua = static_library(
  'testnua', libtestnua_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc])
libtestproxy = static_library(
  'testproxy', libtestproxy_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc])
libtestnat = static_library(
  'testnat', libtestnat_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc])

test_nua = executable(
  'test_nua',
  'test_nua.c',
  include_directories: toplevel_inc,
  link_with: [libtestnua, libtestproxy, libtestnat],
  dependencies: [libsofia_sip_ua_dep, check_dep, dl_dep])
test('test_nua', test_nua)

if check_dep.found()
  check_sofia = executable(
    'check_sofia',
    'check_sofia.c',
    'check_sofia.h',
    'suite_for_nua.c',
    include_directories: toplevel_inc,
    link_with: [libtestnua, libtestproxy, libtestnat],
    dependencies: [libsofia_sip_ua_dep, check_dep])
  test('check_sofia', check_sofia)

  # Needs s2check, which isn't built yet.
  s2check_tests += {
    'check_dlopen_sofia': {
      'sources': files('check_dlopen_sofia.c'),
      'include_directories': toplevel_inc,
      'link_with': [libtestnua, libtestproxy, libtestnat],
      'dependencies': [check_dep, dl_dep],
    },
  }
endif
