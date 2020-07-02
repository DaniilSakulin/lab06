Лабораторная работа №6 по ТиМП

Работу выполнил: Сакулин Даниил

1. Задаем переменные:

```
$ export GITHUB_USERNAME=DaniilSakulin
$ export GITHUB_EMAIL=sakulin_daniil@mail.ru
$ alias edit=nano
$ alias gsed=sed
```

2. Клонирую предыдущий репозиторий:

```
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06

Cloning into 'projects/lab06'...
remote: Enumerating objects: 31, done.
remote: Counting objects: 100% (31/31), done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 31 (delta 7), reused 31 (delta 7), pack-reused 0
Unpacking objects: 100% (31/31), done.
$ cd projects/lab06

$ git remote remove origin

$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06
```
3. Устанавливаю версию:

```
$ gsed -i '/project(print)/a\

set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")
' CMakeLists.txt
$ gsed -i '/project(print)/a\

set(PRINT_VERSION\
  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})
' CMakeLists.txt
$ gsed -i '/project(print)/a\

set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\

set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\

set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
$ gsed -i '/project(print)/a\

set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt
$ git diff

diff --git a/CMakeLists.txt b/CMakeLists.txt
index 89739e7..7497219 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -7,6 +7,13 @@ option(BUILD_EXAMPLES "Build examples" OFF)
 option(BUILD_TESTS "Build tests" OFF)

 project(print)
+set(PRINT_VERSION_MAJOR 0)
+set(PRINT_VERSION_MINOR 1)
+set(PRINT_VERSION_PATCH 0)
+set(PRINT_VERSION_TWEAK 0)
+set(PRINT_VERSION
+  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
+set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

 add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
 ```
 
4. Добавляем информацию в файлы:

```
$ touch DESCRIPTION && edit DESCRIPTION

text for task
$ touch ChangeLog.md

$ export DATE="LANG=en_US date +'%a %b %d %Y'"

$ cat > ChangeLog.md <<EOF

* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```

5. Создание файла для описания информации пакетов:

```
$ cat > CPackConfig.cmake <<EOF

include(InstallRequiredSystemLibraries)
EOF
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
EOF
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF    
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF    
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF    
$ cat >> CPackConfig.cmake <<EOF

include(CPack)
EOF    
$ cat >> CMakeLists.txt <<EOF

include(CPackConfig.cmake)
EOF
```

6. Вносим изменения в README:

```
$ gsed -i 's/lab05/lab06/g' README.md
```

7. Коммит, добавление тега, пуш:

```
$ git add .

$ git commit -m "added cpack config"

$ git tag v0.1.0.0

$ git push origin master --tags

Counting objects: 37, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (28/28), done.
Writing objects: 100% (37/37), 4.35 KiB | 139.00 KiB/s, done.
Total 37 (delta 9), reused 0 (delta 0)
remote: Resolving deltas: 100% (9/9), done.
To https://github.com/DaniilSakulin/lab06
 * [new branch]      master -> master
 * [new tag]         v0.1.0.0 -> v0.1.0.0
 ```
 
8. Сборка:

```
$ travis login --auto

Successfully logged in as DaniilSakulin!
$ travis enable

/var/lib/gems/2.5.0/gems/travis-1.8.13/lib/travis/tools/system.rb:79: warning: Insecure world writable dir /mnt/c in PATH, mode 040777
Detected repository as DaniilSakulin/lab06, is this correct? |yes| yes
DaniilSakulin/lab06: enabled :)
$ cmake -H. -B_build

-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/yrojiobwuha/workspace/projects/projects/lab06/_build
$ cmake --build _build

Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
$ cd _build

$ cpack -G "TGZ"

CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /home/yrojiobwuha/workspace/projects/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
$ cd ..

$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"

-- Configuring done
-- Generating done
-- Build files have been written to: /home/yrojiobwuha/workspace/projects/projects/lab06/_build
$ cmake --build _build --target package

Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /home/yrojiobwuha/workspace/projects/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
$ mkdir artifacts

$ mv _build/*.tar.gz artifacts

    artifacts
└── print-0.1.0.0-Linux.tar.gz

0 directories, 1 file
```

Homework

1. Сначала добавим общие элементы, в конце различные для каждого задания:

dmg

```
$ cat .travis.yml
os: osx
script:
set(CPACK_GENERATOR "TZ")                             # генератор Cpack
set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY "1")             # Cpack может добавлять top-level директорию
set(CPACK_PACKAGE_FILE_NAME "solver")                 # название
set(CPACK_PACKAGE_INSTALL_DIRECTORY "CMake 2.5")      # для инсталяции
set(CPACK_PACKAGE_VENDOR "dimchik")                   # название распространителя пакета
set(CPACK_PACKAGE_CONTACT "d.karpukhin@mail.ru")      # добавление контакта
set(CPACK_PACKAGE_VERSION_MAJOR "1")                  # мажорная версия
set(CPACK_PACKAGE_VERSION_MINOR "0")                  # минорная версия
set(CPACK_PACKAGE_VERSION_PATCH "0")                  # версия патча
set(CPACK_SOURCE_GENERATOR "TGZ")                     # генератор для source пакета
set(CPACK_SYSTEM_NAME "Linux-i686")                   # имя системы
set(CPACK_DMG_VOLUME_NAME "solver")                   # устанавливаем название
set(CPACK_DMG_FORMAT "UDIF")                          # ставим нужное сжатие
set(CPACK_DMG_DISABLE_APPLICATIONS_SYMLINK "ON")      # для избежания добавления simulink
set(CPACK_DMG_SLA_LANGUAGES "English")                # ставим нужный язык
- cpack -G DragNDrop
```

deb

```
$ cat .travis.yml
os: linux
script:                     
set(CPACK_GENERATOR "TZ")                             # генератор Cpack
set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY "1")             # Cpack может добавлять top-level директорию
set(CPACK_PACKAGE_FILE_NAME "solver")                 # название
set(CPACK_PACKAGE_INSTALL_DIRECTORY "CMake 2.5")      # для инсталяции
set(CPACK_PACKAGE_VENDOR "dimchik")                   # название распространителя пакета
set(CPACK_PACKAGE_CONTACT "d.karpukhin@mail.ru")      # добавление контакта
set(CPACK_PACKAGE_VERSION_MAJOR "1")                  # мажорная версия
set(CPACK_PACKAGE_VERSION_MINOR "0")                  # минорная версия
set(CPACK_PACKAGE_VERSION_PATCH "0")                  # версия патча
set(CPACK_SOURCE_GENERATOR "TGZ")                     # генератор для source пакета
set(CPACK_SYSTEM_NAME "Linux-i686")                   # имя системы
set(CPACK_DEB_COMPONENT_INSTALL "ON")                 # разрешение пакетирования
set(CPACK_DEBIAN_PACKAGE_NAME "solver")               # Имя пакета
set(CPACK_DEBIAN_FILE_NAME "solver_f")                # Имя файла
set(CPACK_DEBIAN_PACKAGE_EPOCH "NO")                  # epoch пакета
set(CPACK_DEBIAN_PACKAGE_RELEASE "YES")               # релиз
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "YES")            # maintainer пакета
set(CPACK_DEBIAN_PACKAGE_DEBUG "NO")                  # отключение дебага
- cpack -G DEB
```

rpm

```
$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
set(CPACK_GENERATOR "TZ")                             # генератор Cpack
set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY "1")             # Cpack может добавлять top-level директорию
set(CPACK_PACKAGE_FILE_NAME "solver")                 # название
set(CPACK_PACKAGE_INSTALL_DIRECTORY "CMake 2.5")      # для инсталяции
set(CPACK_PACKAGE_VENDOR "dimchik")                   # название распространителя пакета
set(CPACK_PACKAGE_CONTACT "d.karpukhin@mail.ru")      # добавление контакта
set(CPACK_PACKAGE_VERSION_MAJOR "1")                  # мажорная версия
set(CPACK_PACKAGE_VERSION_MINOR "0")                  # минорная версия
set(CPACK_PACKAGE_VERSION_PATCH "0")                  # версия патча
set(CPACK_SOURCE_GENERATOR "TGZ")                     # генератор для source пакета
set(CPACK_SYSTEM_NAME "Linux-i686")                   # имя системы
set(CPACK_RPM_COMPONENT_INSTALL "YES")                #Enable component packaging for CPack RPM generator
set(CPACK_RPM_PACKAGE_NAME "solver")                  #The RPM package name
set(CPACK_RPM_FILE_NAME "solver_f")                   #Package file name
set(CPACK_RPM_MAIN_COMPONENT "NO")                    #Main component that is packaged without component suffix
set(CPACK_RPM_PACKAGE_EPOCH "NO")                     #The RPM package epoch
set(CPACK_RPM_PACKAGE_VERSION "YES)                   #The RPM package version.
set(CPACK_RPM_PACKAGE_RELEASE "YES")                  #The RPM package release.
set(CPACK_RPM_PACKAGE_RELEASE_DIST "NO")              #The dist tag that is added RPM
set(CPACK_RPM_PACKAGE_LICENSE "NO")                   #The RPM package license policy
- cpack -G RPM
```

nsis

```
$ cat appveyor.yml
platform:
- x86
- x64
build_script:
set(CPACK_GENERATOR "TZ")                             # генератор Cpack
set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY "1")             # Cpack может добавлять top-level директорию
set(CPACK_PACKAGE_FILE_NAME "solver")                 # название
set(CPACK_PACKAGE_INSTALL_DIRECTORY "CMake 2.5")      # для инсталяции
set(CPACK_PACKAGE_VENDOR "dimchik")                   # название распространителя пакета
set(CPACK_PACKAGE_CONTACT "d.karpukhin@mail.ru")      # добавление контакта
set(CPACK_PACKAGE_VERSION_MAJOR "1")                  # мажорная версия
set(CPACK_PACKAGE_VERSION_MINOR "0")                  # минорная версия
set(CPACK_PACKAGE_VERSION_PATCH "0")                  # версия патча
set(CPACK_SOURCE_GENERATOR "TGZ")                     # генератор для source пакета
set(CPACK_SYSTEM_NAME "Linux-i686")                   # имя системы
setCPACK_NSIS_MUI_ICON "ICO")                         #An icon filename
setCPACK_NSIS_DISPLAY_NAME "solver")                  #The display name string that appears
setCPACK_NSIS_PACKAGE_NAME "solver")                  #The title displayed at the top of the installer
setCPACK_NSIS_UNINSTALL_NAME "unin")                  #Specify the name of the program to uninstall the version.
- cpack -G WIX
```
