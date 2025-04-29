# meson.build for features module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

libfeatures_SOURCES = [
  'features.c',
]

# ----------------------------------------------------------------------
# Build targets

libfeatures = static_library(
  'features',
  libfeatures_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libfeatures
