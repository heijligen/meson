project('Meson documentation', version: '1.0')

cur_bdir = meson.current_build_dir()

# Only the script knows which files are being generated
docs_gen = custom_target(
    'gen_docs',
    input: files('markdown/index.md'),
    output: 'gen_docs.stamp',
    command: [
        files('../tools/regenerate_docs.py'),
        '--output-dir', cur_bdir,
        '--dummy-output-file', '@OUTPUT@',
    ],
    build_by_default: true,
    install: false)

refman_gen = custom_target(
    'gen_refman',
    input: files('sitemap.txt'),
    output: ['configured_sitemap.txt', 'refman_links.json'],
    depfile: 'reman_dep.d',
    command: [
        find_program('./genrefman.py'),
        '-g', 'md',
        '-s', '@INPUT@',
        '-o', '@OUTPUT0@',
        '--link-defs', '@OUTPUT1@',
        '--depfile', '@DEPFILE@',
        '--force-color',
        '--no-modules',
    ],
)

hotdoc = import('hotdoc')
documentation = hotdoc.generate_doc(meson.project_name(),
    project_version: meson.project_version(),
    sitemap: refman_gen[0],
    build_by_default: true,
    depends: docs_gen,
    index: 'markdown/index.md',
    install: false,
    extra_assets: ['images/'],
    include_paths: ['markdown', cur_bdir],
    default_license: 'CC-BY-SAv4.0',
    html_extra_theme: join_paths('theme', 'extra'),
    git_upload_repository: 'git@github.com:mesonbuild/mesonbuild.github.io.git',
    edit_on_github_repository: 'https://github.com/mesonbuild/meson',
    syntax_highlighting_activate: true,
    keep_markup_in_code_blocks: true,
    extra_extension: meson.current_source_dir() / 'extensions' / 'refman_links.py',
    refman_data_file: refman_gen[1],
)

run_target('upload',
    command: [find_program('hotdoc'), 'run',
      '--conf-file', documentation.config_path(),
      '--git-upload',
      '-vv',
    ],
)
