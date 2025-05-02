# meson.build for "Sofia-SIP 2 Check", module and unit testing library
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Rules for building the targets

libs2_SOURCES = [
  's2check.h',
  's2tcase.c',
  's2base.h',
  's2base.c',
  's2sip.c',
  's2sip.h',
  's2_localinfo.h',
  's2_localinfo.c',
  's2dns.h',
  's2dns.c',
  's2util.h',
  's2time.c',
]

# ----------------------------------------------------------------------
# Build targets

libs2 = static_library(
  's2',
  libs2_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc])

libs2_dep = declare_dependency(
  include_directories: include_directories('.'),
  link_with: libs2,
)
