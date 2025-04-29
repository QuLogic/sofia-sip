# meson.build for su-glib module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

libsu_glib_SOURCES = [
  'su_source.c',
]

# ----------------------------------------------------------------------
# Build targets

libsu_glib = static_library(
  'su-glib',
  libsu_glib_SOURCES,
  include_directories: toplevel_inc,
  dependencies: [libsofia_sip_ua_dep, glib_dep])

libsofia_sip_ua_glib_lib_deps += libsu_glib

# ----------------------------------------------------------------------
# Tests

su_source_test = executable(
  'su_source_test',
  'su_source_test.c',
  include_directories: toplevel_inc,
  link_with: libsu_glib,
  dependencies: [libsofia_sip_ua_dep, glib_dep])
test('su_source_test', su_source_test, suite: 'glib')

torture_su_glib_timer = executable(
  'torture_su_glib_timer',
  'torture_su_glib_timer.c',
  include_directories: toplevel_inc,
  link_with: libsu_glib,
  dependencies: [libsofia_sip_ua_dep, glib_dep])
test('torture_su_glib_timer', torture_su_glib_timer,
     suite: ['glib', 'torture'])
