sip_hclasses_h = custom_target(
  input: 'sip_hclasses.h.in',
  output: 'sip_hclasses.h',
  command: [
    AWK_SIP_AWK, 'PR=@OUTPUT@', 'TEMPLATE=@INPUT@',
    SS_SIP_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir / 'sofia-sip')

sip_protos_h = custom_target(
  input: 'sip_protos.h.in',
  output: 'sip_protos.h',
  command: [
    AWK_SIP_AWK, 'PR=@OUTPUT@', 'TEMPLATE=@INPUT@',
    SS_SIP_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir / 'sofia-sip')

sip_tag_h = custom_target(
  input: 'sip_tag.h.in',
  output: 'sip_tag.h',
  command: [
    AWK_SIP_AWK, 'PR=@OUTPUT@', 'TEMPLATE=@INPUT@',
    SS_SIP_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir / 'sofia-sip')

sip_extra_h = custom_target(
  input: ['sip_extra.h.in', 'sip_hclasses.h.in', 'sip_protos.h.in', 'sip_tag.h.in'],
  output: 'sip_extra.h',
  command: [
    AWK_SIP_AWK, 'PR=@OUTPUT@', 'NO_FIRST=1', 'NO_LAST=1',
    'PACKAGE_NAME=' + meson.project_name(),
    'PACKAGE_VERSION=' + meson.project_version(),
    'TEMPLATE=@INPUT0@',
    'TEMPLATE1=@INPUT1@',
    'TEMPLATE2=@INPUT2@',
    'TEMPLATE3=@INPUT3@',
    EXTRA],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir / 'sofia-sip')

install_headers(
  'sip.h',
  'sip_util.h',
  'sip_header.h',
  'sip_parser.h',
  'sip_tag_class.h',
  'sip_status.h',
  subdir: include_sofiadir / 'sofia-sip')
