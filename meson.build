# Copyright 2023 Google LLC
# SPDX-License-Identifier: MIT

project(
  'sktest',
  'cpp',
  version: '0.1',
  license: 'MIT',
  meson_version: '>=1.1',
  default_options: ['cpp_std=c++17', 'warning_level=3'],
)

fs = import('fs')
cpp = meson.get_compiler('cpp')

warning_args = cpp.get_supported_arguments([
  '-Wno-pedantic',
  '-Wno-missing-field-initializers',
  '-Wno-unused-parameter',
])
add_project_arguments(['-D_GNU_SOURCE', warning_args], language: 'cpp')

dep_dl = cpp.find_library('dl')
dep_m = cpp.find_library('m', required: false)

skia_path = get_option('skia-path')
skia_path = fs.expanduser(skia_path)
if not (fs.is_absolute(skia_path) and fs.is_dir(skia_path))
  error('skia-path ' + skia_path + ' is not an absolute directory')
endif

dep_libskia = cpp.find_library(
  'skia',
  dirs: [skia_path / 'out'],
  has_headers: ['include/core/SkGraphics.h'],
  header_include_directories: include_directories(skia_path),
)
dep_skia = declare_dependency(
  compile_args: ['-L' + skia_path / 'out', '-fno-rtti',
                 '-DSK_DEBUG', '-DSK_GANESH', '-DSK_GL', '-DSK_VULKAN'],
  dependencies: dep_libskia,
  include_directories: [skia_path],
)

idep_skutil = declare_dependency(
  sources: ['skutil.h'],
  dependencies: [dep_dl, dep_m, dep_skia],
)

tests = [
  'canvas-ganesh-gl',
  'canvas-ganesh-vk',
  'canvas-null',
  'canvas-pdf',
  'canvas-picture',
  'canvas-raster',
  'canvas-svg',
  'drawable',
]

foreach t : tests
  test_deps = [idep_skutil]

  executable(
    t,
    sources: t + '.cpp',
    dependencies: test_deps,
  )
endforeach
