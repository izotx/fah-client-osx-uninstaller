# Configure system boilerplate
import os, sys
sys.path.append(os.environ.get('CONFIG_SCRIPTS_HOME',
                               '../../../control/config-scripts'))
import config

#version = open('version/version.txt', 'r').read().strip()
version = '0.1.0'

# Setup
# these vars should probably all be in distpkg.add_vars()
vars = [
    # desire everything built flat True, target 10.5
    # to get old build, use flat False, target 10.6 in scons-options.py
    BoolVariable('distpkg_flat', 'Build a flat OSX installer pkg', True),
    ('distpkg_target', 'Min OSX version required by installer pkg', '10.5'),
    # non-root pkg installs are very buggy, so this should stay True
    ('distpkg_root_volume_only', 'Require root auth to install', True),
    # put sign_* in scons-options.py
    # if not sign_keychain, the default (login) keychain will be used
    # if not sign_id_installer, productsign will be skipped
    # sign_id_app is required for sign_apps and sign_tools
    # sign_prefix is required for sign_tools
    # global; cannot currently be overridden per-component
    ('sign_keychain', 'Keychain that has signatures'),
    ('sign_id_installer', 'Installer signature name'),
    ('sign_id_app', 'Application/Tool signature name'),
    ('sign_prefix', 'codesign identifier prefix'),
    ]
env = config.make_env(['packager'], vars)

# Configure
conf = Configure(env)

# Packaging
config.configure('packager', conf)

# this should be in packager.configure
env['package_ignores'] += ['.DS_Store']

sys.path.append('./src')
import flatdistpkg, flatdistpackager
flatdistpkg.configure(conf)
flatdistpackager.configure(conf)

# Flat Dist Components
distpkg_components = [
    {'name': 'Uninstaller',
        'home': '.',
        'pkg_id': 'edu.stanford.folding.uninstaller.pkg',
        'must_close_apps': [
            'edu.stanford.folding.fahviewer',
            'edu.stanford.folding.fahcontrol',
            ],
        'pkg_nopayload': True,
        'pkg_scripts': 'Scripts',
        },
    ]

# Package
# Note: a flat dist pkg does not need a pkg_id
name = 'fah-uninstaller'
parameters = {
    'name' : name,
    'version' : version,
    'maintainer' : 'Joseph Coffland <joseph@cauldrondevelopment.com>',
    'vendor' : 'Folding@home',
    'summary' : 'Folding@home Removal',
    'description' : 'Folding@home uninstaller package',
    'short_description' : 'Folding@home uninstaller package',
    'pkg_type' : 'dist',
    'distpkg_resources' : [['Resources', '.']],
    'distpkg_welcome' : 'Welcome.rtf',
    'distpkg_conclusion' : 'Conclusion.rtf',
    #'distpkg_license' : 'License.rtf',
    'distpkg_background' : 'fah-light.png',
    'distpkg_target' : '10.5',
    'distpkg_arch' : 'i386 ppc',
    'package_arch' : 'all',
    'distpkg_flat' : True,
    'distpkg_components' : distpkg_components,
    'distpkg_customize' : 'never', # only one component
    }
pkg = env.FlatDistPackager(**parameters)

AlwaysBuild(pkg)
env.Alias('package', pkg)

# Clean
Clean(pkg, ['build', 'config.log'])
# ensure *.zip not cleaned unless distclean
NoClean(pkg, Glob('*.zip'))
if 'distclean' in COMMAND_LINE_TARGETS:
    Clean('distclean', [
        '.sconsign.dblite', '.sconf_temp', 'config.log',
        'build', 'package.txt', 'package-description.txt',
        Glob(name + '*.pkg'),
        Glob(name + '*.mpkg'),
        Glob(name + '*.zip'),
        Glob('*.pyc'),
        Glob('src/*.pyc'),
        ])