def git_clone(url, branch, dirname)
{
	checkout([$class: 'GitSCM',
	branches: [[name: "*/${branch}"]],
	doGenerateSubmoduleConfigurations: false,
	extensions: [[$class: 'LocalBranch', localBranch: branch],
		    [$class: 'RelativeTargetDirectory',
	relativeTargetDir: dirname]],
	submoduleCfg: [],
	userRemoteConfigs: [[url: url]]])
}
node('docker && ubuntu-16.04') {
	stage("init") {
		sh '''#!/bin/sh
			sudo apt-get update
			sudo apt-get -y install build-essential scons pkg-config \
				libx11-dev libxcursor-dev libxinerama-dev libgl1-mesa-dev libglu-dev libasound2-dev \
				libpulse-dev libudev-dev libxi-dev libxrandr-dev yasm libfreetype6-dev texinfo \
				texi2html subversion
			cd godot-updated
			misc/travis/android-tools-linux.sh
			cd ..
			mkdir butler
			cd butler
			curl -L -o butler.zip https://broth.itch.ovh/butler/linux-amd64/LATEST/archive/default
			unzip butler.zip
			chmod +x butler
			./butler -V
			cd ..
			git clone https://github.com/emscripten-core/emsdk.git
			cd emsdk
			./emsdk install latest
			./emsdk activate latest
			cd ..
			# wget https://netix.dl.sourceforge.net/project/mingw-w64/mingw-w64/mingw-w64-release/mingw-w64-v5.0.4.tar.bz2
			# tar xf mingw-w64-v5.0.4.tar.bz2
			# wget -Ointsafe.h https://raw.githubusercontent.com/Alexpux/mingw-w64/master/mingw-w64-headers/include/intsafe.h
			# cp intsafe.h godot-updated/thirdparty/mbedtls/include/

		'''
	}
	stage("build-mingw-toolchain") {
		git_clone('git://github.com/Zeranoe/mingw-w64-build', 'master', 'mingw-build')
		sh '''#!/bin/sh
			cd mingw-build
			./mingw-w64-build --help
			set -e
			./mingw-w64-build i686 x86_64
		'''
	}
	stage("artifacts") {
		sh '''#!/bin/sh
			tar zcf mingw-build.tar.bz2 mingw-build
		'''
		archiveArtifacts artifacts: "mingw-build.tar.bz2", onlyIfSuccessful: true
	}
}
