# meson.build for sofia-sip/utils
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Build targets

foreach util : ['sip-options', 'sip-date', 'sip-dig']
  executable(
    util, f'@util@.c',
    include_directories: toplevel_inc,
    dependencies: libsofia_sip_ua_dep,
    install: true)
endforeach
