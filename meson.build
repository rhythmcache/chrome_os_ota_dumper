project('chrome_os_ota_dumper', 'c',
  version : '1.0.0',
  meson_version : '>= 1.1.0',
  default_options : [
    'c_std=c11',
    'warning_level=3',
    'werror=false'
  ]
)

enable_http = get_option('enable_http')

# Find required dependencies
lzma_dep = dependency('liblzma')
zstd_dep = dependency('libzstd')
bz2_dep = dependency('bzip2')
brotli_dep = dependency('libbrotlidec')
protobuf_c_dep = dependency('libprotobuf-c')


deps = [
  lzma_dep,
  zstd_dep,
  bz2_dep,
  brotli_dep,
  protobuf_c_dep
]

if host_machine.system() != 'android'
  threads_dep = dependency('threads')
  deps += threads_dep
endif

curl_dep = dependency('libcurl', required: enable_http)

if enable_http and curl_dep.found()
  deps += curl_dep
endif

protoc_c = find_program('protoc-c', required: false)

fs = import('fs')
proto_exists = fs.exists('proto/update_metadata.proto')

if protoc_c.found() and proto_exists
  update_metadata_pb = custom_target('update_metadata_pb',
    output: ['update_metadata.pb-c.c', 'update_metadata.pb-c.h'],
    input: 'proto/update_metadata.proto',
    command: [protoc_c, '--proto_path=@SOURCE_ROOT@/proto', '--c_out=@OUTDIR@', 'update_metadata.proto'],
    depend_files: 'proto/update_metadata.proto'
  )
  pb_sources = [update_metadata_pb[0]]  # Get the .c file
  pb_inc = include_directories('.', 'proto')  # Include both build dir and proto dir
  message('Generating fresh protobuf files from proto/update_metadata.proto')
else
  # Use pre-compiled files
  if not protoc_c.found()
    message('protoc-c not found, using pre-compiled protobuf files')
  elif not proto_exists
    message('proto/update_metadata.proto not found, using pre-compiled protobuf files')
  endif
  pb_sources = files('proto/update_metadata.pb-c.c')
  pb_inc = include_directories('proto')
endif

sources = [
  'src/payload_dumper.c',
  'src/zip/zip_parser.c',
  'src/zip/zip_parser.h'
] + pb_sources

if enable_http and curl_dep.found()
  sources += ['src/http/http_reader.c', 'src/http/http_reader.h']
endif

compile_args = []
if enable_http and curl_dep.found()
  compile_args += '-DENABLE_HTTP_SUPPORT'
endif

executable('payload_dumper',
  sources,
  dependencies: deps,
  c_args: compile_args,
  include_directories: [
    include_directories('src'),
    include_directories('src/zip'),
    include_directories('src/http'),
    pb_inc  # Use the protobuf include directory
  ],
  install: true,
  install_dir: get_option('bindir')
)