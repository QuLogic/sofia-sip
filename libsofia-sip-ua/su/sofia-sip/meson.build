configure_file(
  input: 'su_configure.h.in',
  output: 'su_configure.h',
  configuration: conf)
install_headers(
  'su_types.h',
  'su.h',
  'su_errno.h',
  'su_addrinfo.h',
  'su_localinfo.h',
  'su_wait.h',
  'su_alloc.h',
  'su_alloc_stat.h',
  'su_strlst.h',
  'su_vector.h',
  'su_time.h',
  'su_tag.h',
  'su_tag_class.h',
  'su_tagarg.h',
  'su_tag_io.h',
  'su_tag_inline.h',
  'htable.h',
  'htable2.h',
  'rbtree.h',
  'heap.h',
  'su_debug.h',
  'su_log.h',
  'su_config.h',
  'su_md5.h',
  'su_uniqueid.h',
  'su_bm.h',
  'tstdef.h',
  'su_os_nw.h',
  'su_string.h',
  'string0.h',
  subdir: include_sofiadir / 'sofia-sip')

if COREFOUNDATION
  install_headers(
    'su_osx_runloop.h',
    subdir: include_sofiadir / 'sofia-sip')
endif
