project('test', 'c', 'cpp')

cpp = meson.get_compiler('cpp')

x = disabler()

if ['darwin', 'ios'].contains(host_machine.system())
	if cpp.get_id() == 'clang'
		x = dependency('appleframeworks', modules: 'openal', required: true, method: 'extraframework')
	else
		# GCC cannot find frameworks properly
		x = dependency('openal-soft', required: false)
	endif
else
	x = dependency('openal', required: host_machine.system() != 'windows')
endif

python = import('python').find_installation()

if not x.found()
	cmake = import('cmake')
	min_osx_version = cpp.get_define('__MAC_OS_X_VERSION_MIN_REQUIRED', prefix: '#include <Availability.h>')
	if min_osx_version != ''
		min_osx_version = run_command(python, '-c', 'from sys import argv; x = argv[1]; s = "0"; min = x[2:4].lstrip(s); min = min if min else s; print(f"{x[0:2]}.{min}")', min_osx_version, check: true).stdout().strip()
	endif

	openALOptions = cmake.subproject_options()
	# OpenAL can NOT be built with c++17 -- see almalloc.cpp
	openALOptions.set_override_option('cpp_std', 'c++14')
	openALOptions.set_override_option('strip', 'false')
	# Disable Git because Meson somehow gets confused with the
	# custom version generator
	openALOptions.add_cmake_defines({
		'CMAKE_BUILD_TYPE': 'RelWithDebInfo',
		'ALSOFT_UTILS': false,
		'ALSOFT_NO_CONFIG_UTIL': true,
		'ALSOFT_EXAMPLES': false,
		'ALSOFT_INSTALL_CONFIG': false,
		'ALSOFT_INSTALL_HRTF_DATA': false,
		'ALSOFT_INSTALL_AMBDEC_PRESETS': false,
		'ALSOFT_BACKEND_ALSA': false,
		'ALSOFT_BACKEND_OSS': false,
		'ALSOFT_BACKEND_SOLARIS': false,
		'ALSOFT_BACKEND_SNDIO': false,
		'ALSOFT_BACKEND_PORTAUDIO': false,
		'ALSOFT_BACKEND_PULSEAUDIO': false,
		'ALSOFT_BACKEND_COREAUDIO': true,
		'ALSOFT_BACKEND_JACK': false,
		'ALSOFT_BACKEND_OPENSL': false,
		'ALSOFT_BACKEND_WAVE': true,
		'ALSOFT_BACKEND_WINMM': false,
		'ALSOFT_BACKEND_DSOUND': true,
		'ALSOFT_BACKEND_WASAPI': false,
		'ALSOFT_CPUEXT_NEON': false,
		'CMAKE_DISABLE_FIND_PACKAGE_WindowsSDK': true,
		'CMAKE_DISABLE_FIND_PACKAGE_Git': true,
        'CMAKE_OSX_DEPLOYMENT_TARGET': min_osx_version
	})

	x = cmake.subproject('openal-soft', options: openALOptions, required: true).dependency('OpenAL')
endif

y = executable('tt', files('main.cpp'), dependencies: x)
