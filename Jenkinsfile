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
			ls -l
			sudo apt-get update
			sudo apt-get -y install build-essential scons pkg-config \
				libx11-dev libxcursor-dev libxinerama-dev libgl1-mesa-dev libglu-dev libasound2-dev \
				libpulse-dev libudev-dev libxi-dev libxrandr-dev yasm libfreetype6-dev texinfo \
				texi2html subversion
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
