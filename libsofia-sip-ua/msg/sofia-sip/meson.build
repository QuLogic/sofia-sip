msg_protos_h = custom_target(
  input: 'msg_protos.h.in',
  output: 'msg_protos.h',
  command: [
    AWK_MSG_AWK, 'module=msg', 'NO_FIRST=1', 'NO_MIDDLE=1',
    'PR=@OUTPUT@', 'TEMPLATE=@INPUT@',
    SS_MIME_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir / 'sofia-sip')

msg_mime_protos_h = custom_target(
  input: 'msg_mime_protos.h.in',
  output: 'msg_mime_protos.h',
  command: [
    AWK_MSG_AWK, 'module=msg', 'NO_FIRST=1', 'NO_LAST=1',
    'PR=@OUTPUT@', 'TEMPLATE=@INPUT@',
    SS_MIME_H],
  env: {'LC_ALL': 'C'},
  install: true,
  install_dir: get_option('includedir') / include_sofiadir / 'sofia-sip')

install_headers(
  'msg.h',
  'msg_header.h',
  'msg_types.h',
  'msg_mclass.h',
  'msg_mclass_hash.h',
  'msg_parser.h',
  'msg_addr.h',
  'msg_date.h',
  'msg_buffer.h',
  'msg_tag_class.h',
  'msg_mime.h',
  subdir: include_sofiadir / 'sofia-sip')
