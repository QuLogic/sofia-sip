http_protos_h = custom_target(
  input: 'http_protos.h.in',
  output: 'http_protos.h',
  command: [AWK_HTTP_AWK, 'PR=@OUTPUT@', 'TEMPLATE=@INPUT@', SS_HTTP_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir / 'sofia-sip')

http_tag_h = custom_target(
  input: 'http_tag.h.in',
  output: 'http_tag.h',
  command: [AWK_HTTP_AWK, 'PR=@OUTPUT@', 'TEMPLATE=@INPUT@', SS_HTTP_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir / 'sofia-sip')

install_headers(
  'http.h',
  'http_header.h',
  'http_parser.h',
  'http_tag_class.h',
  'http_status.h',
  'http_hclasses.h',
  subdir: include_sofiadir / 'sofia-sip')
