install_headers(
  'auth_common.h',
  'auth_client.h',
  'auth_digest.h',
  'auth_module.h',
  'auth_plugin.h',
  'auth_client_plugin.h',
  subdir: include_sofiadir / 'sofia-sip')
if HAVE_NTLM
  install_headers(
    'auth_ntlm.h',
    subdir: include_sofiadir / 'sofia-sip')
endif
