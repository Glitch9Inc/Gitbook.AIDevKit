# hash\_key

## Values used to calculate the hash in this folder name.

## Should not depend on the absolute path of the project itself.

## - AGP: 8.7.2.

## - $NDK is the path to NDK 27.2.12479018.

## - $PROJECT is the path to the parent folder of the root Gradle build file.

## - $ABI is the ABI to be built with. The specific value doesn't contribute to the value of the hash.

## - $HASH is the hash value computed from this text.

## - $CMAKE is the path to CMake 3.22.1.

## - $NINJA is the path to Ninja.

-H$PROJECT/unityLibrary/src/main/cpp -DCMAKE\_SYSTEM\_NAME=Android -DCMAKE\_EXPORT\_COMPILE\_COMMANDS=ON -DCMAKE\_SYSTEM\_VERSION=23 -DANDROID\_PLATFORM=android-23 -DANDROID\_ABI=$ABI -DCMAKE\_ANDROID\_ARCH\_ABI=$ABI -DANDROID\_NDK=$NDK -DCMAKE\_ANDROID\_NDK=$NDK -DCMAKE\_TOOLCHAIN\_FILE=$NDK/build/cmake/android.toolchain.cmake -DCMAKE\_MAKE\_PROGRAM=$NINJA -DCMAKE\_LIBRARY\_OUTPUT\_DIRECTORY=$PROJECT/unityLibrary/build/intermediates/cxx/RelWithDebInfo/$HASH/obj/$ABI -DCMAKE\_RUNTIME\_OUTPUT\_DIRECTORY=$PROJECT/unityLibrary/build/intermediates/cxx/RelWithDebInfo/$HASH/obj/$ABI -DCMAKE\_BUILD\_TYPE=RelWithDebInfo -DCMAKE\_FIND\_ROOT\_PATH=E:/UnityProjects/AIDevKit-Unity6-1/.utmp/RelWithDebInfo/$HASH/prefab/$ABI/prefab -BE:/UnityProjects/AIDevKit-Unity6-1/.utmp/RelWithDebInfo/$HASH/$ABI -GNinja -DANDROID\_STL=c++\_shared -DANDROID\_SUPPORT\_FLEXIBLE\_PAGE\_SIZES=ON
