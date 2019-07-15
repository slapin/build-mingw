node('docker && ubuntu-16.04') {
	stage("init") {
		checkout scm
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
		sh '''#!/bin/sh
			mkdir mingw-build
			cp mingw-w64-build mingw-build
			cd mingw-build
			./mingw-w64-build --help
			set -e
			./mingw-w64-build i686 x86_64
		'''
	}
	stage("artifacts") {
		sh '''#!/bin/sh
			rm -Rf mingw-build/bld mingw-build/src
			tar zcf mingw-build.tar.bz2 mingw-build
		'''
		archiveArtifacts artifacts: "mingw-build.tar.bz2", onlyIfSuccessful: true
		withCredentials([string(credentialsId: 'github-token', variable: 'gh_token')]) {
			withEnv(["TOKEN=$gh_token"]) {
				sh '''#!/bin/sh
					./upload-github-release-asset.sh github_api_token=$TOKEN \
						owner=slapin repo=build-mingw \
						tag=$(date +%Y_%V_%m%d_%H%M) filename=./mingw-build.tar.bz2
				'''
			}
		}

	}
}
